---
title: "Raspberry Pi-클라우드(Node.js) - Raspberry Pi를 Azure IoT Hub에 연결 | Microsoft Docs"
description: "Raspberry Pi가 Azure 클라우드에 데이터를 보내도록 Raspberry Pi를 Azure IoT Hub에 연결합니다."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: "azure iot raspberry pi, raspberry pi iot hub, raspberry pi에서 클라우드로 데이터 전송, raspberry pi-클라우드"
ms.assetid: b0e14bfa-8e64-440a-a6ec-e507ca0f76ba
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 4/14/2017
ms.author: xshi
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 6f09d2244d0a1f6dbd7cff164c6d9e35379ee131
ms.lasthandoff: 04/18/2017


---

# <a name="connect-raspberry-pi-to-azure-iot-hub-nodejs"></a>Raspberry Pi를 Azure IoT Hub에 연결(Node.js)

[!INCLUDE [iot-hub-get-started-device-selector](../../includes/iot-hub-get-started-device-selector.md)]

이 자습서에서는 Raspbian을 실행하는 Raspberry Pi 작업의 기초부터 학습합니다. 그런 다음 [Azure IoT Hub](iot-hub-what-is-iot-hub.md)를 사용하여 장치를 클라우드에 원활하게 연결하는 방법을 알아봅니다. Windows 10 IoT Core 샘플이 필요하면 [Windows 개발자 센터](http://www.windowsondevices.com/)로 이동하세요.

아직 키트가 없으세요? [여기](https://azure.microsoft.com/develop/iot/starter-kits)서 새 키트를 구입하세요.


## <a name="what-you-do"></a>수행할 작업

* Raspberry Pi를 설치합니다.
* IoT Hub를 만듭니다.
* IoT Hub에 Pi용 장치를 등록합니다.
* Pi에서 샘플 응용 프로그램을 실행하여 IoT Hub로 센서 데이터를 보냅니다.

앞에서 만든 IoT Hub에 Raspberry Pi를 연결합니다. 그런 다음 Pi에서 샘플 응용 프로그램을 실행하여 BME280에서 온도 및 습도 데이터를 수집합니다. 마지막으로 센서 데이터를 IoT Hub로 보냅니다.

## <a name="what-you-learn"></a>학습 내용

* Azure IoT Hub를 만들고 새 장치 연결 문자열을 가져오는 방법.
* Pi를 BME280 센서와 연결하는 방법.
* Pi에서 샘플 응용 프로그램을 실행하여 센서 데이터를 수집하는 방법.
* IoT Hub로 센서 데이터를 보내는 방법.

## <a name="what-you-need"></a>필요한 항목

![필요한 항목](media/iot-hub-raspberry-pi-kit-node-get-started/0_starter_kit.jpg)

* Raspberry Pi 2 또는 Raspberry Pi 3 보드.
* 활성 Azure 구독. Azure 계정이 없는 경우 몇 분 만에 [Azure 평가판 계정](https://azure.microsoft.com/free/)을 만들 수 있습니다.
* Pi에 연결할 모니터, USB 키보드 및 마우스.
* Windows 또는 Linux를 실행하는 Mac 또는 PC.
* 인터넷 연결.
* 16GB 이상의 microSD 카드.
* 운영 체제 이미지를 microSD 카드에 굽기 위한 USB-SD 어댑터 또는 microSD 카드.
* 6피트 마이크로 USB 케이블과 5볼트 2암페어 전원 공급 장치.

다음 항목은 선택 사항입니다.

* 조립된 Adafruit BME280 온도, 압력 및 습도 센서.
* 실험용 회로판
* F/M 점퍼 와이어 6개.
* 확산형 10mm LED.


> [!NOTE] 
코드 샘플이 시뮬레이션된 센서 데이터를 지원하므로 이러한 항목은 선택 사항입니다.

[!INCLUDE [iot-hub-get-started-create-hub-and-device](../../includes/iot-hub-get-started-create-hub-and-device.md)]

## <a name="setup-raspberry-pi"></a>Raspberry Pi 설치

### <a name="install-the-raspbian-operating-system-for-pi"></a>Pi용 Raspbian 운영 체제 설치

Raspbian 이미지를 설치를 위해 microSD 카드를 준비합니다.

1. Raspbian을 다운로드합니다.
   1. [Raspbian Jessie with Pixel 다운로드](https://www.raspberrypi.org/downloads/raspbian/)(.zip 파일).
   1. 컴퓨터의 폴더에 Raspbian 이미지의 압축을 풉니다.
1. microSD 카드에 Raspbian을 설치합니다.
   1. [Etcher SD 카드 버너 유틸리티를 다운로드하여 설치합니다](https://etcher.io/).
   1. Etcher를 실행하고 1단계에서 압축을 푼 Raspbian 이미지를 선택합니다.
   1. microSD 카드 드라이브를 선택합니다. Etcher가 이미 올바른 드라이브를 선택했을 수 있습니다.
   1. Flash를 클릭하여 microSD 카드에 Raspbian을 설치합니다.
   1. 설치가 완료되면 컴퓨터에서 microSD 카드를 제거합니다. 완료되면 Etcher가 microSD 카드를 자동으로 배출하거나 탑재를 해제하므로 microSD 카드를 바로 제거하는 것이 안전합니다.
   1. Pi에 microSD 카드를 삽입합니다.

### <a name="enable-ssh-and-i2c"></a>SSH 및 I2C를 사용하도록 설정

1. 사용자 이름으로 `pi`, 암호로 `raspberry`를 사용하여 Pi를 모니터, 키보드, 마우스에 연결하고, Pi를 시작한 다음 Raspbian에 로그인합니다.
1. Raspberry 아이콘 > **기본 설정** > **Raspberry Pi 구성**을 클릭합니다.

   ![Raspbian 기본 설정 메뉴](media/iot-hub-raspberry-pi-kit-node-get-started/1_raspbian-preferences-menu.png)

1. **인터페이스** 탭에서 **I2C** 및 **SSH**를 **사용**으로 설정한 다음 **확인**을 클릭합니다.

   ![Raspberry Pi에서 I2C 및 SSH를 사용하도록 설정](media/iot-hub-raspberry-pi-kit-node-get-started/2_enable-i2c-ssh-on-raspberry-pi.png)

> [!NOTE] 
SSH 및 I2C를 사용하려는 경우 [raspberrypi.org](https://www.raspberrypi.org/documentation/remote-access/ssh/) 및 [Adafruit.com](https://learn.adafruit.com/adafruits-raspberry-pi-lesson-4-gpio-setup/configuring-i2)에서 더 많은 참조 문서를 찾을 수 있습니다.

### <a name="connect-the-sensor-to-pi"></a>Pi에 센서 연결

실험용 회로판과 점퍼 와이어를 사용하여 LED 및 BME280 Pi를 다음과 같이 연결합니다. 센서가 없는 경우 이 섹션을 건너뛰세요.

![Raspberry Pi와 센서 연결](media/iot-hub-raspberry-pi-kit-node-get-started/3_raspberry-pi-sensor-connection.png)


센서 핀의 경우 다음 배선을 사용합니다.

| 시작(센서 및 LED)     | 끝(보드)            | 케이블 색   |
| -----------------------  | ---------------------- | ------------: |
| VDD(5G 핀)             | 3.3V PWR(1 핀)       | 흰색 케이블   |
| GND(7G 핀)             | GND(6 핀)            | 갈색 케이블   |
| SCK(8G 핀)             | I2C1 SDA(3 핀)       | 주황색 케이블  |
| SDI(10G 핀)            | I2C1 SCL(5 핀)       | 빨간색 케이블     |
| LED VDD(18F 핀)        | GPIO 24(18 핀)       | 흰색 케이블   |
| LED GND(17F 핀)        | GND(20 핀)           | 검은색 케이블   |

참조용으로 [Raspberry Pi 2 및 3 핀 매핑](https://developer.microsoft.com/windows/iot/docs/pinmappingsrpi)을 보려면 클릭하세요.

BME280이 Raspberry Pi에 성공적으로 연결되면 아래 이미지처럼 보여야 합니다.

![Pi와 BME280 연결](media/iot-hub-raspberry-pi-kit-node-get-started/4_connected-pi.jpg)

마이크로 USB 케이블 및 전원 공급 장치를 사용하여 Pi를 켭니다. 이더넷 케이블을 사용하여 Pi를 유선 네트워크에 연결하거나 [Raspberry Pi Foundation의 지침](https://www.raspberrypi.org/learning/software-guide/wifi/)에 따라 Pi를 무선 네트워크에 연결합니다.

![유선 네트워크에 연결](media/iot-hub-raspberry-pi-kit-node-get-started/5_power-on-pi.jpg)


## <a name="run-a-sample-application-on-pi"></a>Pi에서 샘플 응용 프로그램 실행

### <a name="clone-sample-application-and-install-the-prerequisite-packages"></a>샘플 응용 프로그램을 복제하고 필수 구성 요소 패키지 설치

1. 호스트 컴퓨터에서 다음 SSH 클라이언트 중 하나를 사용하여 Raspberry Pi에 연결합니다.
    - Windows용 [PuTTY](http://www.putty.org/)
    - Ubuntu 또는 macOS에 있는 기본 제공 SSH 클라이언트

1. 다음 명령을 실행하여 샘플 응용 프로그램을 복제합니다.

   ```bash
   git clone https://github.com/Azure-Samples/iot-hub-node-raspberrypi-client-app
   ```

1. 다음 명령을 사용하여 모든 패키지를 설치합니다. Azure IoT 장치 SDK, BME280 센서 라이브러리 및 배선 Pi 라이브러리가 포함되어 있습니다.

   ```bash
   cd iot-hub-node-raspberry-pi-clientapp
   npm install
   ```
   > [!NOTE] 
   네트워크 연결에 따라 이 설치 프로세스를 완료하는 데 몇 분 정도 걸릴 수 있습니다.

### <a name="configure-the-sample-application"></a>샘플 응용 프로그램 구성

1. 다음 명령을 실행하여 config 파일을 엽니다.

   ```bash
   nano config.json
   ```

   ![Config 파일](media/iot-hub-raspberry-pi-kit-node-get-started/6_config-file.png)

   이 파일에는 사용자가 구성할 수 있는 두 개 항목이 있습니다. 첫 번째는 클라우드로 전송되는 두 메시지 사이의 간격을 정의하는 `interval`입니다. 두 번째는 시뮬레이션된 센서 데이터의 사용 여부에 대한 부울 값인 `simulatedData`입니다.

   **센서가 없는 경우** `simulatedData` 값을 `true`로 설정하여 샘플 응용 프로그램에서 시뮬레이션된 센서 데이터를 만들어서 사용하게 합니다.

1. Control-O > Enter > Control-X를 눌러 저장하고 종료합니다.

### <a name="run-the-sample-application"></a>샘플 응용 프로그램 실행

1. 다음 명령을 실행하여 샘플 응용 프로그램을 실행합니다.

   ```bash
   sudo node index.js '<your Azure IoT hub device connection string>'
   ```

   > [!NOTE] 
   장치 연결 문자열을 복사하여 작은따옴표 안에 붙여 넣습니다.


IoT Hub로 전송되는 센서 데이터와 메시지를 보여 주는 다음 출력이 표시됩니다.

![출력 - Raspberry Pi에서 IoT Hub로 전송된 센서 데이터](media/iot-hub-raspberry-pi-kit-node-get-started/8_run-output.png)

## <a name="next-steps"></a>다음 단계

샘플 응용 프로그램을 실행하여 센서 데이터를 수집하고 IoT Hub로 전송했습니다.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
