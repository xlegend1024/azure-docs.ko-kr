---
title: "실제 센서에서 Node.js를 사용하여 Azure IoT Suite에 Raspberry Pi 연결 | Microsoft Docs"
description: "Raspberry Pi 3 및 Azure IoT Suite에 Microsoft Azure IoT 스타터 키트를 사용합니다. Node.js를 사용하여 Raspberry Pi를 원격 모니터링 솔루션에 연결하고, 센서의 원격 분석을 클라우드로 전송하고, 솔루션 대시보드에서 호출된 메서드에 응답합니다."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-suite
ms.devlang: nodejs
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 6d8e6ef070c3b9d9623f4b29ab97a5d20f69d499
ms.contentlocale: ko-kr
ms.lasthandoff: 05/03/2017


---
# <a name="connect-your-raspberry-pi-3-to-the-remote-monitoring-solution-and-send-telemetry-from-a-real-sensor-using-nodejs"></a>Raspberry Pi 3를 원격 모니터링 솔루션에 연결하고 Node.js를 사용하여 실제 센서에서 원격 분석 전송

[!INCLUDE [iot-suite-raspberry-pi-kit-selector](../../includes/iot-suite-raspberry-pi-kit-selector.md)]

이 자습서에서는 Raspberry Pi 3에용 Microsoft Azure IoT 스타터 키트를 사용하여 클라우드와 통신할 수 있는 온도 및 습도 판독기를 개발하는 방법을 보여 줍니다. 이 자습서에서는 다음을 사용하여 작업을 수행합니다.

- Raspbian OS, Node.js 프로그래밍 언어 및 Node.js용 Microsoft Azure IoT SDK를 사용하여 샘플 장치를 구현합니다.
- 클라우드 기반 백 엔드로 미리 구성된 IoT Suite 원격 모니터링 솔루션

## <a name="overview"></a>개요

이 자습서에서는 다음 단계를 완료합니다.

- Azure 구독에서 미리 구성된 원격 모니터링 솔루션의 인스턴스를 배포합니다. 이 단계에서는 여러 Azure 서비스를 자동으로 배포하고 구성합니다.
- 사용자 컴퓨터 및 원격 모니터링 솔루션과 통신하도록 장치 및 센서를 설정합니다.
- 샘플 장치 코드를 업데이트하여 원격 모니터링 솔루션에 연결하고 솔루션 대시보드에서 볼 수 있는 원격 분석을 보냅니다.

[!INCLUDE [iot-suite-raspberry-pi-kit-prerequisites](../../includes/iot-suite-raspberry-pi-kit-prerequisites.md)]

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

> [!WARNING]
> 원격 모니터링 솔루션은 Azure 구독에서 Azure 서비스 집합을 프로비전합니다. 배포는 실제 엔터프라이즈 아키텍처를 반영합니다. 불필요한 Azure 사용료가 부과되지 않도록 하려면 배포가 완료된 후 azureiotsuite.com에서 미리 구성된 솔루션 인스턴스를 삭제합니다. 미리 구성된 솔루션이 다시 필요하면 쉽게 다시 만들 수 있습니다. 원격 모니터링 솔루션이 실행되는 동안 소비를 감소하는 방법에 대한 자세한 내용은 [데모 목적으로 미리 구성된 Azure IoT Suite 솔루션 구성][lnk-demo-config]을 참조하세요.

[!INCLUDE [iot-suite-raspberry-pi-kit-view-solution](../../includes/iot-suite-raspberry-pi-kit-view-solution.md)]

[!INCLUDE [iot-suite-raspberry-pi-kit-prepare-pi](../../includes/iot-suite-raspberry-pi-kit-prepare-pi.md)]

## <a name="download-and-configure-the-sample"></a>샘플 다운로드 및 구성

이제 Raspberry Pi에서 원격 모니터링 클라이언트 응용 프로그램을 다운로드하고 구성할 수 있습니다.

### <a name="install-nodejs"></a>Node.js 설치

Raspberry Pi에 Node.js를 설치합니다. Node.js용 IoT SDK에는 Node.js 0.11.5 이상 버전의 Node.js가 필요합니다. 다음 단계에서는 Raspberry Pi에 Node.js v6.10.2를 설치하는 방법을 보여 줍니다.

1. 다음 명령을 사용하여 Raspberry Pi를 업데이트합니다.

    `sudo apt-get update`

1. 다음 명령을 사용하여 Raspberry Pi에 Node.js 이진 파일을 다운로드합니다.

    `wget https://nodejs.org/dist/v6.10.2/node-v6.10.2-linux-armv7l.tar.gz`

1. 다음 명령을 사용하여 이진 파일을 설치합니다.

    `sudo tar -C /usr/local --strip-components 1 -xzf node-v6.10.2-linux-armv7l.tar.gz`

1. 다음 명령을 사용하여 Node.js v6.10.2를 성공적으로 설치했는지 확인합니다.

    `node --version`

### <a name="clone-the-repositories"></a>리포지토리 복제

아직 해당 작업을 수행하지 않은 경우 Pi에서 다음 명령을 실행하여 필요한 리포지토리를 복제합니다.

`cd ~`

`git clone --recursive https://github.com/Azure-Samples/iot-remote-monitoring-node-raspberrypi-getstartedkit.git`

### <a name="update-the-device-connection-string"></a>장치 연결 문자열 업데이트

다음 명령을 사용하여 **nano** 편집기에서 샘플 원본 파일을 엽니다.

`nano ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/basic/remote_monitoring.js`

다음과 같은 줄을 찾습니다.

```javascript
var connectionString = 'HostName=[Your IoT hub name].azure-devices.net;DeviceId=[Your device id];SharedAccessKey=[Your device key]';
```

자리 표시자 값을 이 자습서를 시작할 때 만들어 저장한 장치 및 IoT Hub 정보로 바꿉니다. 변경 내용을 저장하고(**Ctrl-O**, **Enter**) 편집기를 종료합니다(**Ctrl-X**).

## <a name="run-the-sample"></a>샘플 실행

다음 명령을 실행하여 이 샘플에 대한 필수 구성 요소 패키지를 설치합니다.

`cd ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/basic`

`npm install`

이제 Raspberry Pi에서 샘플 프로그램을 실행할 수 있습니다. 다음 명령을 입력합니다.

`sudo node ~/iot-remote-monitoring-node-raspberrypi-getstartedkit/basic/remote_monitoring.js`

다음 샘플 출력은 Raspberry Pi의 명령 프롬프트에 표시되는 출력의 예입니다.

![Raspberry Pi 앱의 출력][img-raspberry-output]

**Ctrl+C**를 눌러 언제든지 프로그램을 종료합니다.

[!INCLUDE [iot-suite-raspberry-pi-kit-view-telemetry](../../includes/iot-suite-raspberry-pi-kit-view-telemetry.md)]

## <a name="next-steps"></a>다음 단계

Azure IoT에 대한 추가 샘플 및 설명서를 보려면 [Azure IoT 개발자 센터](https://azure.microsoft.com/develop/iot/)를 방문하세요.


[img-raspberry-output]: ./media/iot-suite-raspberry-pi-kit-node-get-started-basic/app-output.png

[lnk-demo-config]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/configure-preconfigured-demo.md

