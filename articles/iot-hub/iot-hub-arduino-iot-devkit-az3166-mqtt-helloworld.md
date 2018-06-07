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
ms.openlocfilehash: ee8ff1acbaf5d97d62d6811e8e8abc86017b32fe
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632619"
---
# <a name="send-messages-to-an-mqtt-server"></a>Skicka meddelanden till en MQTT-server

Sakernas Internet (IoT) system ofta hantera återkommande, dålig kvalitet eller långsamma internet-anslutningar. MQTT är en dator till dator (M2M) anslutningen protokoll som har utvecklats med sådana problem i åtanke. 

Klientbiblioteket MQTT används här är en del av den [Eclipse Paho](http://www.eclipse.org/paho/) projekt som innehåller API: er för att använda MQTT över flera transportmedel.

## <a name="what-you-learn"></a>Detta får du får lära dig

I det här projektet du lära dig:
- Hur du använder klientbiblioteket MQTT för att skicka meddelanden till en MQTT broker.
- Hur du konfigurerar din MXChip Iot DevKit som en MQTT-klient.

## <a name="what-you-need"></a>Vad du behöver

Slutför den [Getting Started Guide](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) till:

* Har din DevKit ansluten till Wi-Fi
* Förbereda utvecklingsmiljön

## <a name="open-the-project-folder"></a>Öppna projektmappen

1. Koppla från DevKit från datorn, om den redan är ansluten.

2. Starta VS-kod.

3. Anslut DevKit till datorn.

## <a name="open-the-mqttclient-sample"></a>Öppna MQTTClient-exempel

Expandera vänster **ARDUINO exempel** bläddrar du till **exempel MXCHIP AZ3166 > MQTT**, och välj **MQTTClient**. Det öppnas ett nytt fönster i koden jämfört med en projektmapp i den.

> [!NOTE]
> Du kan också öppna exempel kommandot paletten. Använd `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) för att öppna paletten kommando skriver **Arduino**, och sedan söka efter och välj **Arduino: exempel**.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Skapa och ladda upp Arduino ritningarna till DevKit

Typen `Ctrl+P` (macOS: `Cmd+P`) att köra `task device-upload`. När överföringen har slutförts startar om DevKit och kör ritningarna.

![enheten-överföringen](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> Du kan få ett ”fel: AZ3166: Okänd paketet” felmeddelande. Det här felet uppstår när paketindexet board inte uppdateras på rätt sätt. För att lösa det här felet finns i det här [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testa projektet

I VS-kod, följer du proceduren för att öppna och Ställ in seriella övervakaren:

1. Klicka på den `COM[X]` word i statusfältet för att ange rätt COM-port med `STMicroelectronics`: ![set-com-port](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Klicka på ikonen power plugin i statusfältet för att öppna Serial övervakaren: ![serial-Övervakaren](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. Klicka på siffran som representerar den Baud-hastighet i statusfältet, och ange det till `115200`: ![set överföringshastighet](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

Seriell övervakaren visar alla meddelanden som skickas av exemplet ritningarna. Ritningarna ansluter DevKit till Wi-Fi. När Wi-Fi-anslutningen är klar skickar ett meddelande till MQTT broker ritningarna. Efter det skickar exemplet upprepade gånger två ”iot.eclipse.org”-meddelanden med hjälp av QoS-0 och 1 för QoS, respektive.

![Serial-utdata](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Problem och feedback

Om du stöter på problem, läser du [vanliga frågor och svar](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller Anslut via följande kanaler:

* [Gitter.IM](http://gitter.im/Microsoft/azure-iot-developer-kit)
* [StackOverflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Se också

* [Ansluta IoT DevKit AZ3166 till Azure IoT-hubb i molnet]({{"/docs/getting-started/" | absolute_url }})
* [Skaka, skaka i en Tweet]({{"/docs/projects/shake-shake/" | absolute_url }})

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du konfigurerar din MXChip Iot DevKit som en MQTT klient och använda MQTT klientbiblioteket för att skicka meddelanden till en MQTT broker är här de föreslagna nästa steg:

* [Azure IoT fjärråtkomst övervakning solution accelerator-översikt](https://docs.microsoft.com/azure/iot-suite/)
* [Anslut en MXChip IoT DevKit enhet till din Azure IoT centralt program](https://docs.microsoft.com/microsoft-iot-central/howto-connect-devkit)
