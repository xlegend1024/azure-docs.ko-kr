## <a name="meaning-of-migration-of-iaas-resources-from-classic-to-resource-manager"></a>클래식에서 Resource Manager로 IaaS 리소스를 마이그레이션하는 작업의 의미
자세한 내용을 살펴보기 전에 IaaS 리소스에서 데이터 평면과 관리 평면 작업 간 차이를 살펴보겠습니다.

* *관리/제어 평면* 은 리소스 수정을 위해 관리/제어 평면 또는 API로 들어오는 호출을 설명합니다. 예를 들어 VM을 만들고, VM을 다시 시작하고, 새 서브넷을 사용하여 가상 네트워크를 업데이트하는 등의 작업은 실행 중인 리소스를 관리합니다. 인스턴스 연결에는 직접적으로 영향을 주지 않습니다.
* *데이터 평면* (응용 프로그램)은 응용 프로그램 자체의 런타임을 설명하며 Azure API를 통하지 않는 인스턴스와의 상호 작용이 포함됩니다. 웹 사이트에 액세스하거나 실행 중인 SQL Server 인스턴스 또는 mongoDB 서버에서 데이터를 가져오는 경우 데이터 평면 또는 응용 프로그램 상호 작용으로 간주됩니다. 저장소 계정에서 Blob을 복사하고 공용 IP 주소에 액세스하여 가상 컴퓨터에 RDP 또는 SSH 연결을 하는 작업도 데이터 평면에 해당합니다. 이러한 작업은 계산, 네트워킹, 저장소에서 응용 프로그램을 계속 실행하는 상태에서 수행합니다.

![관리/제어 평면 및 데이터 평면 간의 차이를 보여주는 스크린샷](../articles/virtual-machines/media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)

> [!NOTE]
> 일부 마이그레이션 시나리오에서 Azure 플랫폼은 가상 컴퓨터를 중지하고, 할당 취소하고, 다시 시작합니다. 이 경우 데이터 평면 가동이 잠시 중지됩니다.
>
>

## <a name="the-migration-experience"></a>마이그레이션 환경
마이그레이션 환경을 시작하기 전에 다음을 수행하는 것이 좋습니다.

* 마이그레이션하려는 리소스가 지원되지 않는 기능이나 구성을 사용하지 않도록 확인합니다. 일반적으로 플랫폼이 이러한 문제를 감지하고 오류를 생성합니다.
* 가상 네트워크에 없는 VM이 있는 경우에는 준비 작업 중에 중지 및 할당 취소됩니다. 공용 IP 주소를 유지하려는 경우에는 준비 작업을 트리거하기 전에 IP 주소 예약을 참조하세요. 하지만 VM이 가상 네트워크에 있는 경우에는 중지 및 할당 취소되지 않습니다.
* 업무 외 시간 동안 마이그레이션을 계획하여 마이그레이션 중 발생할 수 있는 예기치 않은 오류를 해결할 수 있도록 계획하십시오.
* 준비 단계가 완료된 후 쉽게 검증할 수 있도록 PowerShell, CLI(명령줄 인터페이스) 명령 또는 REST API를 사용하여 VM의 현재 구성을 다운로드합니다.
* 마이그레이션을 시작하기 전에 Resource Manager 배포를 처리할 수 있도록 자동화/운영 스크립트를 업데이트합니다. 경우에 따라 리소스가 준비됨 상태일 때 GET 작업을 실행할 수도 있습니다.
* 클래식 IaaS 리소스에 구성된 RBAC 정책을 평가하고 마이그레이션이 완료되면 계획을 세웁니다.

마이그레이션 워크플로는 다음과 같습니다.

![마이그레이션 워크플로를 보여주는 스크린 샷](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> 다음 섹션에서 설명하는 모든 작업은 idempotent 상태입니다. 지원되지 않는 기능 또는 구성 오류 이외의 문제가 발생하는 경우 준비, 중단 또는 커밋 작업을 다시 시도하는 것이 좋습니다. Azure 플랫폼은 이 작업을 다시 시도합니다.
>
>

### <a name="validate"></a>유효성 검사
유효성 검사 작업은 마이그레이션 프로세스의 첫 번째 단계입니다. 이 단계의 목표는 마이그레이션 대상 리소스에 대해 백그라운드에서 데이터 분석을 수행하고 리소스 마이그레이션이 가능할 경우 성공/실패를 반환합니다.

마이그레이션을 유효성 검사하려는 가상 네트워크 또는 호스티드 서비스(가상 네트워크가 아닌 경우)를 선택합니다.

* 리소스를 마이그레이션할 수 없을 경우 Azure 플랫폼에 마이그레이션이 지원되지 않는 모든 이유가 나열됩니다.

저장소 서비스의 유효성을 검사할 때 저장소 계정과 동일한 이름에 "-Migrated"가 추가된 리소스 그룹에서 마이그레이션된 계정을 찾습니다.  예를 들어 저장소 계정 이름이 "mystorage"인 경우 "mystorage-Migrated"라는 리소스 그룹에서 Azure Resource Manager 지원 리소스를 찾을 수 있습니다. 여기에는 "mystorage"라는 저장소 계정이 포함됩니다.

### <a name="prepare"></a>준비
준비 작업은 마이그레이션 프로세스의 두 번째 단계입니다. 이 단계의 목표는 클래식에서 Resource Manager 리소스로의 IaaS 리소스 전환을 시뮬레이션하고 사용자가 볼 수 있도록 나란히 표시하는 것입니다.

마이그레이션을 준비하려는 가상 네트워크 또는 호스티드 서비스(가상 네트워크가 아닌 경우)를 선택합니다.

* 리소스를 마이그레이션할 수 없을 경우 Azure 플랫폼이 마이그레이션 프로세스를 중지하고 준비 작업이 실패한 이유를 나열합니다.
* 리소스 마이그레이션이 가능할 경우에는 Azure 플랫폼이 가장 먼저 마이그레이션 리소스에 대한 관리 평면 작업을 잠급니다. 예: 마이그레이션 중인 VM에 데이터 디스크를 추가할 수 없습니다.

그런 다음 Azure 플랫폼이 마이그레션하는 리소스에 대해 클래식에서Resource Manager로 메타데이터 마이그레이션을 시작합니다.

준비 작업이 완료되면 리소스를 클래식 및 Resource Manager에서 볼 수 있는 옵션이 표시됩니다. 클래식 배포 모델의 모든 클라우드 서비스에 대해 Azure Platform에서 `cloud-service-name>-Migrated`패턴의 리소스 그룹 이름을 만듭니다.

> [!NOTE]
> 마이그레이션된 리소스에 대해 만든 리소스 그룹의 이름(예: "-Migrated")을 선택할 수 없지만 마이그레이션이 완료된 후 Azure Resource Manager 이동 기능을 사용하여 원하는 리소스 그룹으로 리소스를 이동할 수는 있습니다. 자세한 내용을 보려면 [새 리소스 그룹 또는 구독으로 리소스 이동](../articles/resource-group-move-resources.md)을 참조하세요.

다음은 성공적인 준비 작업 후의 결과를 보여 주는 두 화면입니다. 첫 번째 화면에서는 원래 클라우드 서비스를 포함하는 리소스 그룹을 보여 줍니다. 두 번째 화면에서는 동일한 Azure Resource Manager 리소스를 포함하는 새 "-Migrated" 리소스 그룹을 보여 줍니다.

![포털 클래식 클라우드 서비스를 보여 주는 스크린샷](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-classic.png)

![준비 중인 포털 Azure Resource Manager 리소스를 보여주는 스크린샷](../articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-arm.png)

> [!NOTE]
> 기존 가상 네트워크에 없는 가상 컴퓨터는 이 마이그레이션 단계에서 할당 취소된 상태로 중지됩니다.
>
>

### <a name="check-manual-or-scripted"></a>검사(수동 또는 스크립트)
이 검사 단계에서는 앞에서 다운로드한 구성을 사용하여 마이그레이션이 정상적으로 진행되는지 확인할 수 있습니다(선택 사항). 또는 포털에 로그인하고 속성과 리소스에 대해 주요 점검을 수행하여 메타데이터 마이그레이션이 정상적으로 진행되는지 확인합니다.

가상 네트워크를 마이그레이션하는 경우 가상 컴퓨터의 구성이 대부분 다시 시작됩니다. 이러한 VM의 응용 프로그램에 대해 응용 프로그램이 아직 실행 중인지 확인할 수 있습니다.

모니터링/자동화와 작업 스크립트를 테스트하여 VM이 정상적으로 작동하며 업데이트된 스크립트가 올바르게 작동하는지 확인합니다. 리소스가 준비됨 상태인 경우에는 GET 작업만 지원됩니다.

마이그레이션을 언제까지 커밋해야 하는가에 대해 정해진 시간은 없습니다. 이 상태를 원하는 시간 동안 유지할 수 있습니다. 하지만 중단 또는 커밋할 때까지 이러한 리소스에 대한 관리 평면이 잠깁니다.

문제가 발생할 경우 언제든지 마이그레이션을 중단하고 클래식 배포 모델로 돌아갈 수 있습니다. 그럴 경우 클래식 배포 모델에서 해당 VM에서 정상 작업을 재개할 수 있도록 Azure 플랫폼에서 리소스에 대한 관리 평면 작업을 시작합니다.

### <a name="abort"></a>중단
중단 단계에서는 변경 사항을 클래식 배포 모델로 되돌리고 마이그레이션을 중지할 수 있습니다(선택 사항).

> [!NOTE]
> 커밋 작업을 트리거하면 이 작업을 실행할 수 없습니다.     
>
>

### <a name="commit"></a>커밋
유효성 검사를 마친 후 마이그레이션을 커밋할 수 있습니다. 리소스는 클래식에 더 이상 표시되지 않으며 Resource Manager 배포 모델에서만 사용할 수 있습니다. 새 포털에서는 마이그레이션된 리소스만 관리할 수 있습니다.

> [!NOTE]
> 이 작업은 멱등원 작업입니다. 이 작업이 실패하면 작업을 다시 시도하는 것이 좋습니다. 계속 실패할 경우 지원 티켓을 만들거나 [VM 포럼](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows)에서 ClassicIaaSMigration 태그를 사용하여 포럼 게시물을 작성할 수 있습니다.
>
>
<br>
다음은 마이그레이션 프로세스가 진행되는 동안의 단계 순서도입니다.

![마이그레이션 단계를 보여 주는 스크린샷](../articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-classic-to-azure-resource-manager-resources"></a>클래식을 Azure Resource Manager 리소스로 변환
다음 표에서 클래식 및 Resource Manager에서 리소스를 표현하는 방식을 확인할 수 있습니다. 다른 기능 및 리소스는 현재 지원되지 않습니다.

| 클래식 표현 | Resource Manager 표현 | 자세한 정보 |
| --- | --- | --- |
| 클라우드 서비스 이름 |DNS 이름 |마이그레이션하는 동안 명명 패턴 `<cloudservicename>-migrated`를 사용하여 모든 클라우드 서비스에 대한 새 리소스 그룹을 만듭니다. 이 리소스 그룹에는 모든 리소스가 포함됩니다. 클라우드 서비스 이름은 공용 IP 주소와 연결된 DNS 이름이 됩니다. |
| 가상 컴퓨터 |가상 컴퓨터 |VM 관련 속성은 변경되지 않고 마이그레이션됩니다. 클래식 배포 모델에서는 컴퓨터 이름 등의 특정 osProfile 정보가 저장되지 않으므로 마이그레이션 후 빈 상태로 유지됩니다. |
| VM에 연결된 디스크 리소스 |VM에 연결된 암시적 디스크 |Resource Manager 배포 모델에서는 디스크가 최상위 리소스로 모델링되지 않습니다. VM에서 암시적 디스크로 마이그레이션됩니다. 현재 VM에 연결되어 있는 디스크만 지원됩니다. Resource Manager VM은 이제 클래식 저장소 계정을 사용할 수 있습니다. 이를 통해 업데이트하지 않고도 디스크를 쉽게 마이그레이션할 수 있습니다. |
| VM 확장 |VM 확장 |클래식 배포 모델에서는 XML 확장을 제외한 모든 리소스 확장이 마이그레이션됩니다. |
| 가상 컴퓨터 인증서 |Azure 키 자격 증명 모음의 인증서 |클라우드 서비스에 서비스 인증서가 포함된 경우 클라우드 서비스당 새 Azure 주요 자격 증명 모음이 생성되고 주요 자격 증명 모음으로 인증서가 이동됩니다. VM이 업데이트되면서 주요 자격 증명 모음의 인증서를 참조합니다. <br><br> **참고:** VM이 실패한 상태로 전환될 수 있으므로 keyvault를 삭제하지 않습니다. Key Vaults을 안전하게 삭제하거나 VM과 함께 새 구독으로 이동할 수 있도록 백 엔드의 기능 향상에 최선을 다하고 있습니다. |
| WinRM 구성 |osProfile 하의 WinRM 구성 |Windows 원격 관리 구성은 마이그레이션의 일부로 변경되지 않은 상태로 이동됩니다. |
| 가용성 집합 속성 |가용성 집합 리소스 | 클래식 배포 모델에서 가용성 집합 사양은 VM의 속성이었습니다. 마이그레이션에서는 가용성 집합이 최상위 리소스가 됩니다. 클라우드 서비스당 둘 이상의 가용성 집합 또는 클라우드 서비스의 가용성 집합에 없는 하나 이상의 가용성 집합 및 VM 구성은 지원되지 않습니다. |
| VM의 네트워크 구성 |기본 네트워크 인터페이스 |VM의 네트워크 구성은 마이그레이션 후 기본 네트워크 인터페이스 리소스로 표현됩니다. 가상 네트워크에 없는 VM의 경우 마이그레이션 중 내부 IP 주소가 변경됩니다. |
| VM의 여러 네트워크 인터페이스 |네트워크 인터페이스 |VM에 여러 네트워크 인터페이스가 연결된 경우 각 네트워크 인터페이스는 Resource Manager 배포 모델의 마이그레이션 중 모든 속성과 마찬가지로 최상위 리소스가 됩니다. |
| 부하 분산된 끝점 집합 |부하 분산 장치 |클래식 배포 모델에서 플랫폼은 모든 클라우드 서비스에 대한 암시적 부하 분산 장치를 할당받습니다. 마이그레이션 중에는 새로운 부하 분산 장치 리소스를 만들며 부하 분산 끝점 집합이 부하 분산 장치 규칙이 됩니다. |
| 인바운드 NAT 규칙 |인바운드 NAT 규칙 |VM에 정의된 입력 끝점은 마이그레이션 중에 부하 분산 장치의 인바운드 NAT(Network Address Translation) 규칙으로 변환됩니다. |
| VIP 주소 |DNS 이름이 포함된 공용 IP 주소 |가상 IP 주소는 공용 IP 주소가 되며, 부하 분산 장치와 연결됩니다. |
| 가상 네트워크 |가상 네트워크 |가상 네트워크는 모든 속성과 함께 Resource Manager 배포 모델로 마이그레이션됩니다. `-migrated`이름을 사용하여 새 리소스 그룹이 생성됩니다. |
| 예약된 IP |정적 할당 방법의 공용 IP 주소 |부하 분산 장치와 연결되어 있고 예약된 IP는 클라우드 서비스 또는 가상 컴퓨터의 마이그레이션과 함께 마이그레이션됩니다. 연결되지 않고 예약된 IP 마이그레이션은 현재 지원되지 않습니다. |
| VM당 공용 IP 주소 |동적 할당 방법의 공용 IP 주소 |VM에 연결된 공용 IP 주소는 할당 방법이 정적으로 설정된 공용 IP 주소 리소스로 변환됩니다. |
| NSG |NSG |서브넷과 연결된 네트워크 보안 그룹은 마이그레이션 중 Resource Manager 배포 모델로 복제됩니다. 마이그레이션 중 클래식 배포 모델의 NSG는 제거되지 않습니다. 하지만 마이그레이션이 진행 중인 동안에는 NSG의 관리 평면 작업이 차단됩니다. |
| DNS 서버 |DNS 서버 |가상 네트워크 또는 VM과 연결된 DNS 서버는 해당 리소스 마이그레이션 중 모든 속성과 함께 마이그레이션됩니다. |
| UDR |UDR |서브넷과 연결된 사용자 정의 경로는 마이그레이션 중 Resource Manager 배포 모델로 복제됩니다. 마이그레이션 중 클래식 배포 모델의 UDR는 제거되지 않습니다. 마이그레이션이 진행 중인 동안에는 UDR의 관리 평면 작업이 차단됩니다. |
| VM 네트워크 구성의 IP 전달 속성 |NIC의 IP 전달 속성 |마이그레이션 중 VM의 IP 전달 속성이 네트워크 인터페이스의 속성으로 변환됩니다. |
| 여러 IP가 포함된 부하 분산 장치 |여러 공용 IP 리소스가 포함된 부하 분산 장치 |부하 분산 장치와 연결된 모든 공용 IP는 공용 IP 리소스로 변환되며 마이그레이션 후 부하 분산 장치와 연결됩니다. |
| VM의 내부 DNS 이름 |NIC의 내부 DNS 이름 |마이그레이션하는 동안 VM의 내부 DNS 접미사는 NIC의 "InternalDomainNameSuffix"라는 읽기 전용 속성으로 마이그레이션됩니다. 마이그레이션 후 접미사는 그대로 유지되며 VM 확인은 이전처럼 계속 작동해야 합니다. |
| 가상 네트워크 게이트웨이 |가상 네트워크 게이트웨이 |가상 네트워크 게이트웨이 속성은 변경되지 않고 마이그레이션됩니다. 게이트웨이와 연결된 VIP는 변경하지 않습니다. |
| 로컬 네트워크 사이트 |로컬 네트워크 게이트웨이 |로컬 네트워크 사이트 속성은 로컬 네트워크 게이트웨이라는 새 리소스로 변경되지 않고 마이그레이션됩니다. 이는 온-프레미스 주소 접두사 및 원격 게이트웨이 IP를 나타냅니다. |
| 연결 참조 |연결 |네트워크 구성에서 게이트웨이 및 로컬 네트워크 사이트 간 연결 참조는 마이그레이션 후에 리소스 관리자에서 [Connection]이라는 새로 만든 리소스에 의해 표시됩니다. 네트워크 구성 파일에서 연결 참조의 모든 속성은 변경되지 않은 상태로 새로 만든 [Connection] 리소스에 복사됩니다. VNet을 나타내는 로컬 네트워크 사이트에 두 개의 IPsec 터널을 만들어 클래식의 VNet 간을 연결합니다. 로컬 네트워크 게이트웨이 없이 Resource Manager 모델에서 Vnet2Vnet 연결 형식으로 변환됩니다. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>마이그레이션 후 자동화 및 도구 변경
클래식 배포 모델에서 Resource Manager 배포 모델로 리소스를 마이그레이션하는 과정의 일부로, 기존 자동화 또는 도구를 업데이트하여 마이그레이션 후에도 계속 작동되도록 해야 합니다.
