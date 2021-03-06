## <a name="virtual-network-basics"></a>가상 네트워크 기본 사항
### <a name="what-is-an-azure-virtual-network-vnet"></a>Azure 가상 네트워크(VNet)란?
VNet을 사용하여 Azure에서 VPN(가상 사설망)을 프로비전 및 관리할 수 있으며 필요에 따라 VNet을 Azure에서 다른 VNet 또는 온-프레미스 IT 인프라와 연결하여 하이브리드 또는 크로스-프레미스 솔루션을 만들 수 있습니다. 생성한 각 VNet은 각각의 CIDR 블록을 가지며 CIDR 블록이 충돌하지 않는 한 다른 VNet 및 온-프레미스 네트워크에 연결할 수 있습니다. 또한 VNet에 대한 DNS 서버 설정 및 VNet의 서브넷으로 구분에 대한 제어권을 가집니다.

VNet을 다음에 사용합니다.

* 사설 클라우드만 사용하는 전용 가상 네트워크 만들기
  
    경우에 따라 솔루션에 대한 크로스-프레미스 구성을 필요로 하지 않습니다. VNet을 만들 때 VNet 내의 서비스 및 VM은 클라우드 내에서 안전하게 직접 서로 통신할 수 있습니다. 이는 VNet 내에서 안전하게 트래픽을 유지하면서도 솔루션의 일부로 인터넷 통신을 필요로 하는 VM 및 서비스에 대한 끝점 연결을 구성할 수 있습니다.
* 데이터 센터를 안전하게 확장
  
    VNet을 사용하여 기존의 사이트 간(S2S) VPN을 빌드하여 데이터 센터 용량을 안전하게 확장할 수 있습니다. S2S VPN은 IPSEC를 사용하여 회사 VPN 게이트웨이와 Azure 간의 보안 연결을 제공합니다.
* 하이브리드 클라우드 시나리오 활성화
  
    VNet은 유연성을 제공하여 다양한 하이브리드 클라우드 시나리오를 지원합니다. 메인프레임 및 Unix 시스템과 같은 모든 형식의 온-프레미스 시스템에 클라우드 기반 응용프로그램을 안전하게 연결할 수 있습니다.

### <a name="how-do-i-know-if-i-need-a-virtual-network"></a>가상 네트워크가 필요한 경우를 어떻게 알 수 있을까요?
[가상 네트워크 개요](../articles/virtual-network/virtual-networks-overview.md) 를 방문하여 최상의 네트워크 디자인 옵션을 결정하는 데 도움이 되는 의사 결정 테이블을 참조하세요.

### <a name="how-do-i-get-started"></a>어떻게 시작하나요?
[가상 네트워크 설명서](https://azure.microsoft.com/documentation/services/virtual-network/) 를 방문하여 시작합니다. 이 페이지에는 가상 네트워크를 디자인할 때 고려해야 할 사항을 이해하는데 도움이 되는 정보뿐만 아니라 일반적인 구성 단계에 대한 링크가 있습니다.

### <a name="what-services-can-i-use-with-vnets"></a>VNet은 어떤 서비스와 함께 사용할 수 있습니까?
VNet은 클라우드 서비스(PaaS), 가상 컴퓨터 및 웹앱과 같은 다양한 다른 Azure 서비스와 함께 사용할 수 있습니다. 그러나 VNet에서 지원하지 않는 몇 가지 서비스가 있습니다. 사용하려는 특정 서비스를 확인하고 호환 가능한지 확인하세요.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>크로스-프레미스 연결 없이 VNet을 사용할 수 있습니까?
예. 사이트 간 연결을 사용하지 않고 VNet을 사용할 수 있습니다. Azure에서 도메인 컨트롤러와 SharePoint 팜을 실행하려는 경우 특히 유용합니다.

## <a name="virtual-network-configuration"></a>가상 네트워크 구성
### <a name="what-tools-do-i-use-to-create-a-vnet"></a>VNet을 만들려면 어떤 도구를 사용합니까?
다음 도구를 사용하여 가상 네트워크를 만들거나 구성할 수 있습니다.

* Azure 포털(클래식 및 리소스 관리자 VNet용)
* 네트워크 구성 파일(netcfg - 클래식 VNet 전용) [네트워크 구성 파일을 사용하여 가상 네트워크 구성](../articles/virtual-network/virtual-networks-using-network-configuration-file.md)을 참조하세요.
* PowerShell(클래식 및 리소스 관리자 VNet용)
* Azure CLI(클래식 및 리소스 관리자 VNet용)

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>VNet 내에서 사용할 수 있는 주소 범위는 무엇입니까?
공용 IP 주소 범위와 [RFC 1918](http://tools.ietf.org/html/rfc1918)에서 정의된 모든 IP 주소 범위를 사용할 수 있습니다.

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>VNet 내에서 공용 IP 주소를 가질 수 있습니까?
예. 공용 IP 주소 범위에 대한 자세한 내용은 [VNet(가상 네트워크)의 공용 IP 주소 공간](../articles/virtual-network/virtual-networks-public-ip-within-vnet.md)을 참조하세요. 공용 IP 사용자는 인터넷에서 직접 액세스할 수 없습니다.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-virtual-network"></a>가상 네트워크 내에서 서브넷 수에 제한이 있습니까?
VNet 내에서 사용하는 서브넷 수에는 제한이 없습니다. 모든 서브넷은 가상 네트워크 주소 공간에 완전히 포함되어야 하며 서로 겹치지 않아야 합니다.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>이러한 서브넷 내에서 IP 주소를 사용하는데 제한 사항이 있습니까?
Azure는 각 서브넷 내의 일부 IP 주소를 예약합니다. 서브넷의 첫 번째 및 마지막 IP 주소는 Azure 서비스에 사용되는 3개 이상의 주소와 함께 프로토콜 적합성을 위해 예약됩니다.

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>VNet 및 서브넷은 얼마나 크고 얼마나 작을 수 있습니까?
지원되는 가장 작은 서브넷은 /29이며 가장 큰 서브넷은 /8(CIDR 서브넷 정의 사용)입니다.

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>VNet을 사용하여 내 VLAN을 Azure에 가져올 수 있습니까?
아니요. VNet은 계층 3 오버레이입니다. Azure는 모든 계층 2 의미 체계를 지원하지 않습니다.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>VNet 및 서브넷에 사용자 지정 라우팅 정책을 지정할 수 있습니까?
예. UDR(사용자 정의 라우팅)을 사용할 수 있습니다. UDR에 대한 자세한 내용은 [사용자 정의 경로 및 IP 전달](../articles/virtual-network/virtual-networks-udr-overview.md)을 참조하세요.

### <a name="do-vnets-support-multicast-or-broadcast"></a>VNet은 멀티 캐스트 또는 브로드캐스트를 지원합니까?
아니요. 멀티 캐스트 또는 브로드캐스트는 지원하지 않습니다.

### <a name="what-protocols-can-i-use-within-vnets"></a>VNet 내에서 사용할 수 있는 프로토콜은 무엇입니까?
VNet 내에서 표준 IP 기반 프로토콜을 사용할 수 있습니다. 그러나 멀티 캐스트, 브로드캐스트, IP-IP 캡슐화 패킷 및 GRE(일반 라우팅 캡슐화) 패킷은 VNet 내에서 차단됩니다. 작동하는 표준 프로토콜은 다음과 같습니다.

* TCP
* UDP
* ICMP

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>내 기본 라우터를 VNet 내에서 ping할 수 있습니까?
아니요.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Tracert를 사용하여 연결을 진단할 수 있습니까?
아니요.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>VNet을 만든 후 서브넷을 추가할 수 있습니까?
예. 서브넷 주소가 VNet에서 다른 서브넷의 일부가 아닌 경우 언제든지 서브넷을 VNet에 추가할 수 있습니다.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>서브넷을 만든 후 크기를 수정할 수 있습니까?
VNet 내에서 PowerShell cmdlet 또는 NETCFG 파일을 사용하여 배포된 VM 또는 서비스가 없는 경우 서브넷을 추가, 제거, 확장 또는 축소할 수 있습니다. 또한 VM을 포함한 서브넷 또는 서비스가 변경에 영향을 받지 않는 경우 모든 접두사를 추가, 제거, 확장 또는 축소할 수 있습니다.

### <a name="can-i-modify-subnets-after-i-created-them"></a>서브넷을 만든 후 수정할 수 있습니까?
예. VNet에서 사용되는 CIDR 블록을 추가, 제거 및 수정할 수 있습니다.

### <a name="can-i-connect-to-the-internet-if-i-am-running-my-services-in-a-vnet"></a>VNet에서 서비스를 실행 중인 경우 인터넷에 연결할 수 있습니까?
예. VNet 내에 배포된 모든 서비스는 인터넷에 연결할 수 있습니다. Azure에서 배포된 모든 클라우드 서비스에는 공개적으로 주소를 지정할 수 있는 할당된 VIP가 있습니다. 해당 서비스를 활성화하여 인터넷의 연결을 허용하려면 PaaS 역할에 대한 입력 끝점 및 가상 컴퓨터에 대한 끝점을 정의해야 합니다.

### <a name="do-vnets-support-ipv6"></a>VNet은 IPv6를 지원합니까?
아니요. VNet과 함께 IPv6를 사용할 수 없습니다.

### <a name="can-a-vnet-span-regions"></a>VNet을 사용하여 지역을 확장할 수 있습니까?
아니요. VNet은 단일 지역으로 제한됩니다.

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Azure에서 다른 VNet에 VNet을 연결할 수 있습니까?
예. REST API 또는 Windows PowerShell을 사용하여 VNet 통신용으로 VNet을 만들 수 있습니다. VNet 피어링을 통해 VNet을 연결할 수도 있습니다. 피어링에대한 자세한 내용은 [여기](../articles/virtual-network/virtual-network-peering-overview.md)에서 참조하세요.

## <a name="name-resolution-dns"></a>이름 확인(DNS)
### <a name="what-are-my-dns-options-for-vnets"></a>VNet에 대한 DNS 옵션은 무엇입니까?
사용 가능한 모든 DNS 옵션을 안내하는 [VM 및 역할 인스턴스에 대한 이름 확인](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) 페이지의 의사 결정 테이블을 사용하세요.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>VNet에 대한 DNS 서버를 지정할 수 있습니까?
예. VNet 설정에서 DNS 서버 IP 주소를 지정할 수 있습니다. VNet에서 모든 VM에 대한 기본 DNS 서버로 적용됩니다.

### <a name="how-many-dns-servers-can-i-specify"></a>얼마나 많은 DNS 서버 수를 지정할 수 있습니까?
최대 12개의 DNS 서버를 지정할 수 있습니다.

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>네트워크를 생성 한 후 DNS 서버를 수정할 수 있습니까?
예. VNet에 대한 DNS 서버 목록을 언제든지 변경할 수 있습니다. DNS 서버 목록을 변경하는 경우 새로운 DNS 서버를 선택하기 위해 VNet에서 각 VM을 다시 시작해야 합니다.

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Azure에서 제공하는 DNS란 무엇이며 VNet으로 작동합니까?
Azure에서 제공하는 DNS는 Microsoft에서 제공하는 다중 테넌트 DNS 서비스입니다. Azure는 이 서비스에 모든 VM 및 역할 인스턴스를 등록합니다. 이 서비스는 동일한 클라우드 서비스 내에 포함된 VM 및 역할 인스턴스에 대한 호스트 이름 및 동일한 VNet에서 VM 및 역할 인스턴스에 대한 FQDN으로 이름 확인을 제공합니다.

> [!NOTE]
> 이번에는 Azure에서 제공하는 DNS를 사용한 테넌트 간 이름 확인에 대한 가상 네트워크의 첫 100개의 클라우드 서비스에 제한이 있습니다. 사용자 고유의 DNS 서버를 사용하는 경우 이러한 제한이 적용되지 않습니다.
> 
> 

### <a name="can-i-override-my-dns-settings-on-a-per-vm--service-basis"></a>VM 당/서비스 단위로 DNS 설정을 재정의할 수 있습니까?
예. 클라우드 서비스 별로 DNS 서버를 설정하여 기본 네트워크 설정을 재정의할 수 있습니다. 그러나 가능한 한 네트워크 차원 DNS를 사용하는 것이 좋습니다.

### <a name="can-i-bring-my-own-dns-suffix"></a>고유한 DNS 접미사를 가져올 수 있습니까?
아니요. VNet에 대해 사용자 지정 DNS 접미사를 지정할 수 없습니다.

## <a name="vnets-and-vms"></a>VNet 및 VM
### <a name="can-i-deploy-vms-to-a-vnet"></a>VNet에 VM을 배포할 수 있습니까?
예.

### <a name="can-i-deploy-linux-vms-to-a-vnet"></a>VNet에 Linux VM을 배포할 수 있습니까?
예. Azure에서 지원되는 Linux의 모든 배포판을 배포할 수 있습니다.

### <a name="what-is-the-difference-between-a-public-vip-and-an-internal-ip-address"></a>공용 VIP와 내부 IP 주소 간의 차이는 무엇입니까?
* 내부 IP 주소는 DHCP에 의해 VNet 내에서 각 VM에 할당된 IP 주소입니다. 공용 주소가 아닙니다. VNet을 만든 경우 VNet의 서브넷 설정에 지정된 범위에서 내부 IP 주소가 할당됩니다. VNet이 없는 경우 내부 IP 주소가 계속 할당됩니다. 내부 IP 주소는 VM이 할당 취소되지 않는 한 VM의 수명 주기 동안 유지됩니다.
* 공용 VIP는 클라우드 서비스 또는 부하 분산 장치에 할당된 공용 IP 주소입니다. VM NIC에 직접 할당되지 않습니다. VIP는 해당 클라우드 서비스의 모든 VM이 할당 취소되거나 삭제될 때까지 할당된 클라우드 서비스와 함께 유지됩니다. 이때 해제됩니다.

### <a name="what-ip-address-will-my-vm-receive"></a>VM은 어떤 IP 주소를 받게 됩니까?
* **내부 IP 주소 -** VNet에 VM을 배포하는 경우 VM은 지정한 내부 IP 주소 풀에서 내부 IP 주소를 받습니다. VM은 VNet 내에서 내부 IP 주소를 사용하여 통신합니다. 하지만 Azure에서 동적 내부 IP 주소를 할당하더라도 VM에 대 한 정적 주소를 요청할 수 있습니다. 고정 내부 IP 주소에 대한 자세한 내용을 보려면 [고정 내부 IP를 설정하는 방법](../articles/virtual-network/virtual-networks-reserved-private-ip.md)을 참조하세요.
* **VIP -** VIP가 VM에 직접 할당되지 않더라도 VM은 VIP에 연결됩니다. VIP는 클라우드 서비스에 할당될 수 있는 공용 IP 주소입니다. 필요에 따라 클라우드 서비스에 대한 VIP를 예약할 수 있습니다.
* **ILPIP -** 인스턴스 수준 공용 IP 주소(ILPIP)를 구성할 수도 있습니다. ILPIP는 클라우드 서비스를 사용하지 않고 VM과 직접 연결됩니다. ILPIP에 대한 자세한 내용을 보려면 [인스턴스 수준 공용 IP(ILPIP) 개요](../articles/virtual-network/virtual-networks-instance-level-public-ip.md)를 참조하세요.

### <a name="can-i-reserve-an-internal-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>나중에 만들 VM에 대한 내부 IP 주소를 예약할 수 있습니까?
아니요. 내부 IP 주소를 예약할 수 없습니다. 내부 IP 주소가 사용 가능한 경우 DHCP 서버에서 VM 또는 역할 인스턴스에 할당됩니다. 해당 VM은 내부 IP 주소를 할당하려는 VM일 수도 아닐 수도 있습니다. 그러나 이미 만든 VM의 내부 IP 주소를 사용 가능한 모든 내부 IP 주소로 변경할 수 있습니다.

### <a name="do-internal-ip-addresses-change-for-vms-in-a-vnet"></a>VNet에서 VM에 대한 내부 IP 주소가 변경됩니까?
예. 내부 IP 주소는 VM이 할당 취소되지 않는 한 VM의 수명 주기 동안 유지됩니다. VM이 할당 취소되는 경우 VM에 대한 고정 내부 IP 주소를 정의하지 않는 한 내부 IP 주소는 해제됩니다. VM이 단순히 중지된 경우(및 상태에 있지 않은 경우 **중지(할당 해제)**) IP 주소는 VM에 할당된 상태로 유지됩니다.

### <a name="can-i-manually-assign-ip-addresses-to-nics-in-vms"></a>VM에서 수동으로 IP 주소를 NIC에 할당할 수 있습니까?
아니요. VM의 인터페이스 속성을 변경하지 말아야 합니다. 변경하면 VM에 대한 연결이 손실될 수 있습니다.

### <a name="what-happens-to-my-ip-addresses-if-i-shut-down-a-vm"></a>VM을 종료하는 경우 IP 주소가 어떻게 되나요?
아무 일도 일어나지 않습니다. IP 주소(공용 VIP 및 내부 IP 주소)는 클라우드 서비스 또는 VM에 유지됩니다.

> [!NOTE]
> 단순히 VM을 종료하려는 경우 관리 포털을 사용하지 마세요. 현재 시스템 종료 단추가 가상 컴퓨터를 할당 취소합니다.
> 
> 

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-re-deploying"></a>VM을 다시 배포하지 않고 VNet에서 하나의 서브넷에서 다른 서브넷으로 이동할 수 있습니까?
예. 자세한 내용은 [여기](../articles/virtual-network/virtual-networks-move-vm-role-to-subnet.md)에서 찾을 수 있습니다.

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>VM에 대해 정적 MAC 주소를 구성할 수 있습니까?
아니요. MAC 주소를 정적으로 구성할 수 없습니다.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-it-has-been-created"></a>MAC 주소가 만들어진 후 VM에 대해 동일하게 유지됩니까?
예, VM이 중지(할당이 취소)되었다가 다시 시작되더라도 MAC 주소는 VM에 대해 동일하게 유지됩니다.

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>VNet의 VM에서 인터넷에 연결할 수 있습니까?
예. VNet 내에 배포된 모든 서비스는 인터넷에 연결할 수 있습니다. 또한 Azure에서 배포된 모든 클라우드 서비스에는 공개적으로 주소를 지정할 수 있는 할당된 VIP가 있습니다. 해당 서비스를 활성화하여 인터넷의 연결을 허용하려면 PaaS 역할에 대한 입력 끝점 및 VM에 대한 끝점을 정의해야 합니다.

## <a name="vnets-and-services"></a>VNet 및 서비스
### <a name="what-services-can-i-use-with-vnets"></a>VNet은 어떤 서비스와 함께 사용할 수 있습니까?
VNet 내에서 계산 서비스만 사용할 수 있습니다. 계산 서비스는 클라우드 서비스(웹 및 작업자 역할) 및 VM으로 제한됩니다.

### <a name="can-i-use-web-apps-with-virtual-network"></a>가상 네트워크와 함께 웹앱을 사용할 수 있습니까?
예. ASE(앱 서비스 환경)를 사용하여 VNet 내부에 웹앱을 배포할 수 있습니다. 또한 VNet에 대해 지점 및 사이트 간 구성된 경우 Azure VNet에서 웹앱을 안전하게 연결하고 리소스에 액세스할 수 있습니다. 자세한 내용은 다음을 참조하세요.

* [앱 서비스 환경에서 웹앱 만들기](../articles/app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md)
* [웹앱 가상 네트워크 통합](https://azure.microsoft.com/blog/2014/09/15/azure-websites-virtual-network-integration/)
* [웹앱을 통해 VNet 통합 및 하이브리드 연결 사용](https://azure.microsoft.com/blog/2014/10/30/using-vnet-or-hybrid-conn-with-websites/)
* [Azure 가상 네트워크에 웹앱 통합](../articles/app-service-web/web-sites-integrate-with-vnet.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>VNet에서 웹 및 작업자 역할(PaaS)을 사용하여 클라우드 서비스를 배포할 수 있습니까?
예. VNet 내에서 PaaS 서비스를 배포할 수 있습니다.

### <a name="how-do-i-deploy-paas-roles-to-a-vnet"></a>PaaS 역할을 VNet에 배포하려면 어떻게 해야 합니까?
서비스 구성의 네트워크 구성 섹션에서 VNet 이름 및 역할/서브넷 매핑을 지정하여 수행할 수 있습니다. 이진 파일을 업데이트할 필요가 없습니다.

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>서비스를 VNet 내부 및 외부로 이동할 수 있습니까?
아니요. 서비스를 VNet 내부 및 외부로 이동할 수 없습니다. 서비스를 삭제하고 다시 배포하여 다른 VNet으로 이동해야 합니다.

## <a name="vnets-and-security"></a>VNet 및 보안
### <a name="what-is-the-security-model-for-vnets"></a>VNet에 대한 보안 모델은 무엇입니까?
VNet은 다른 VNet 및 Azure 인프라에서 호스팅되는 다른 서비스에서 완전히 격리됩니다. VNet은 트러스트 경계입니다.

### <a name="can-i-define-acls-or-nsgs-on-my-vnets"></a>VNet 내에서 ACL 또는 NSG를 정의할 수 있습니까?
아니요. ACL 또는 NSG를 VNet에 연결할 수 없습니다. 그러나 ACL은 VNet에 배포된 VM에 대한 입력 끝점에 정의할 수 있으며 NSG는 서브넷 또는 NIC에 연결할 수 있습니다.

### <a name="is-there-a-vnet-security-whitepaper"></a>VNet 보안 백서가 있습니까?
예. [여기](http://go.microsoft.com/fwlink/?LinkId=386611)에서 다운로드할 수 있습니다.

## <a name="apis-schemas-and-tools"></a>API, 스키마 및 도구
### <a name="can-i-manage-vnets-from-code"></a>코드에서 VNet을 관리할 수 있습니까?
예. REST API를 사용하여 VNet 및 크로스-프레미스 연결을 관리할 수 있습니다. 자세한 내용은 [여기](http://go.microsoft.com/fwlink/?LinkId=296833)를 참조하세요.

### <a name="is-there-tooling-support-for-vnets"></a>VNet에 대한 도구 지원이 있습니까?
예. 다양한 플랫폼에 대해 PowerShell 및 명령줄 도구를 사용할 수 있습니다. 자세한 내용은 [여기](http://go.microsoft.com/fwlink/?LinkId=317721)를 참조하세요.

