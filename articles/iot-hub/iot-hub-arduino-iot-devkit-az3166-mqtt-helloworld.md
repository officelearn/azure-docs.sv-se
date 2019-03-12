---
title: Skicka meddelanden till en MQTT-server med hjälp av klientbiblioteket för Azure MQTT | Microsoft Docs
description: Använd DevKit som en klient för att skicka meddelanden till en MQTT-server
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.openlocfilehash: 60520f5a72fd7e27d4ea64ac76511a00a727426e
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57552018"
---
# <a name="send-messages-to-an-mqtt-server"></a>Skicka meddelanden till en MQTT-server

Sakernas Internet (IoT) system ofta hantera tillfälliga, dålig kvalitet eller långsamma internet-anslutningar. MQTT är en dator till dator (M2M) anslutning protokoll som har utvecklats med sådana utmaningar i åtanke. 

MQTT-klientbibliotek som används här är en del av den [Eclipse Paho](https://www.eclipse.org/paho/) projektet som innehåller API: er för att använda MQTT över flera transportmedel.

## <a name="what-you-learn"></a>Detta får du får lära dig

I det här projektet lär du dig:
- Hur du använder MQTT-klientbiblioteket för att skicka meddelanden till en MQTT asynkron meddelandekö.
- Så här konfigurerar du MXChip Iot DevKit som en MQTT-klient.

## <a name="what-you-need"></a>Vad du behöver

Slutför den [Kom igång med](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) till:

* Har din DevKit som är ansluten till Wi-Fi
* Förbereda utvecklingsmiljön

## <a name="open-the-project-folder"></a>Öppna projektmappen

1. Om DevKit redan ansluter till din dator måste du koppla från den.

2. Starta VS Code.

3. Anslut DevKit till din dator.

## <a name="open-the-mqttclient-sample"></a>Öppna exemplet MQTTClient

Expandera vänster **ARDUINO exempel** bläddrar du till **exempel för MXCHIP AZ3166 > MQTT**, och välj **MQTTClient**. Ett nytt VS Code-fönster öppnas med en projektmapp, till exempel i den.

> [!NOTE]
> Du kan också öppna exemplet från kommandopaletten. Använd `Ctrl+Shift+P` (Mac OS: `Cmd+Shift+P`) Skriv för att öppna kommandopaletten **Arduino**, och leta upp och välj **Arduino: Exempel**.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Skapa och ladda upp Arduino skissen till DevKit

Typ `Ctrl+P` (Mac OS: `Cmd+P`) att köra `task device-upload`. När uppladdningen är klar, startar om DevKit och kör skissen.

![enhet-överföringen](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> Du kan få en ”fel: AZ3166: Okänd package ”felmeddelande. Det här felet uppstår när paketindexet tavla inte uppdateras korrekt. Lös felet genom att referera till den [utveckling delen av vanliga IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testa projektet

I VS Code, följer du proceduren för att öppna och Ställ in seriell övervakaren:

1. Klicka på den `COM[X]` word på statusfältet för att ange rätt COM-porten med `STMicroelectronics`: ![set-com-port](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Klicka på ikonen power plugin i statusfältet för att öppna den seriella Monitor: ![seriell – övervaka](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. Klicka på siffran som representerar det överföringshastigheten i statusfältet, och väljer `115200`: ![set överföringshastighet](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

Seriell övervakaren visar alla meddelanden som skickas av exemplet skissen. Skissen ansluter DevKit till Wi-Fi. När Wi-Fi-anslutningen är klar skickar ett meddelande till meddelandekön MQTT i skissen. Därefter skickar exemplet upprepade gånger två ”iot.eclipse.org”-meddelanden med hjälp av QoS 0 och 1 för QoS, respektive.

![serial-output](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Problem och feedback

Om du får problem kan se den [IoT DevKit vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller Anslut via följande kanaler:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Se också

* [Ansluta IoT DevKit AZ3166 till Azure IoT Hub i molnet](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [Skaka, skaka efter en Tweet](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du konfigurerar MXChip Iot DevKit som en MQTT-klient och använda MQTT-klientbiblioteket för att skicka meddelanden till en asynkron meddelandekö för MQTT, är här nästa föreslagna steg:

* [IoT-Remote Monitoring solution accelerator översikt över Azure](https://docs.microsoft.com/azure/iot-suite/)
* [Anslut en enhet för MXChip IoT DevKit till programmet Azure IoT Central](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
