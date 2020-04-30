---
title: Skicka meddelanden till en MQTT-server med hjälp av klient biblioteket för Azure MQTT
description: Lär dig hur du använder klient biblioteket för MQTT för att skicka meddelanden till en MQTT-Broker. Lär dig också hur du konfigurerar din mXChip IoT-DevKit som en MQTT-klient.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.custom: mqtt
ms.openlocfilehash: 600e64ef5bc3329f0116359066bdcdaf42c13e2e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733494"
---
# <a name="send-messages-to-an-mqtt-server"></a>Skicka meddelanden till en MQTT-Server

Sakernas Internet-system (IoT) hanterar ofta återkommande, dålig kvalitet eller långsamma Internet anslutningar. MQTT är ett M2M-protokoll (Machine-to-Machine) som har utvecklats med dessa utmaningar i åtanke. 

Det MQTT-klient bibliotek som används här är en del av [PAHO](https://www.eclipse.org/paho/) -projektet, som innehåller API: er för att använda MQTT över flera transport medel.

## <a name="what-you-learn"></a>Detta får du får lära dig

I det här projektet får du lära dig:
- Så här använder du klient biblioteket MQTT för att skicka meddelanden till en MQTT-Broker.
- Så här konfigurerar du din MXChip IoT-DevKit som en MQTT-klient.

## <a name="what-you-need"></a>Vad du behöver

Slutför [komma igång guiden](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) för att:

* Låt dina DevKit vara anslutna till Wi-Fi
* Förbereda utvecklingsmiljön

## <a name="open-the-project-folder"></a>Öppna projektmappen

1. Om DevKit redan är ansluten till datorn kopplar du från den.

2. Starta VS Code.

3. Anslut DevKit till din dator.

## <a name="open-the-mqttclient-sample"></a>Öppna exemplet MQTTClient

Expandera exempel avsnittet för vänster **ARDUINO** , bläddra till **exempel för MXCHIP AZ3166 > MQTT**och välj **MQTTClient**. Ett nytt VS Code-fönster öppnas med en projektmapp i den.

> [!NOTE]
> Du kan också öppna exempel från kommando paletten. Använd `Ctrl+Shift+P` (MacOS: `Cmd+Shift+P`) för att öppna kommando-paletten, Skriv **Arduino**och leta upp och välj **Arduino: exempel**.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Bygg och överför Arduino-skissen till DevKit

Typ `Ctrl+P` (MacOS: `Cmd+P`) som ska `task device-upload`köras. När uppladdningen är klar startar DevKit om och kör skissen.

![enhet – Ladda upp](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> Du kan få fel meddelandet "Error: AZ3166: unknowed Package". Det här felet uppstår när tavla-paketets index inte har uppdaterats på rätt sätt. För att lösa det här felet, se [avsnittet utveckling i vanliga frågor och svar om IoT-DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testa projektet

I VS Code följer du stegen nedan för att öppna och konfigurera den seriella övervakaren:

1. Klicka på `COM[X]` ordet i statusfältet för att ange rätt com-port med `STMicroelectronics`: ![set-com-port](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Klicka på Power plugg-ikonen i statusfältet för att öppna den seriella övervakaren ![: serie övervakaren](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. I statusfältet klickar du på det tal som representerar överföringshastigheten och ställer in det på `115200`: ![set-baud-rate](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

I den seriella övervakaren visas alla meddelanden som skickas av exempel ritningen. Skissen ansluter DevKit till Wi-Fi. När Wi-Fi-anslutningen har slutförts skickar skissen ett meddelande till MQTT-koordinatorn. Efter det skickar exemplet upprepade gånger två "iot.eclipse.org"-meddelanden med QoS 0 och QoS 1.

![serie utdata](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Problem och feedback

Om det uppstår problem kan du läsa [vanliga frågor och svar om IoT-DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller ansluta med följande kanaler:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Se även

* [Ansluta IoT DevKit-AZ3166 till Azure IoT Hub i molnet](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [Skaka, skaka om för en tweet](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du konfigurerar din MXChip IoT-DevKit som en MQTT-klient och använder MQTT-klient biblioteket för att skicka meddelanden till en MQTT-Broker, så är det förslag på nästa steg:

* [Översikt över Azure IoT Remote Monitoring Solution Accelerator](https://docs.microsoft.com/azure/iot-suite/)
* [Ansluta en MXChip IoT DevKit-enhet till ditt Azure IoT Central-program](/azure/iot-central/core/howto-connect-devkit)
