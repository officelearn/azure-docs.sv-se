---
title: Skicka meddelanden till en MQTT-server med Azure MQTT-klientbibliotek
description: Lär dig hur du använder MQTT-klientbiblioteket för att skicka meddelanden till en MQTT-mäklare. Lär dig också hur du konfigurerar din mXChip IoT DevKit att vara en MQTT-klient.
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
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733494"
---
# <a name="send-messages-to-an-mqtt-server"></a>Skicka meddelanden till en MQTT-server

IoT-system (Internet of Things) hanterar ofta intermittenta, dåliga kvalitet eller långsamma internetanslutningar. MQTT är ett M2M-anslutningsprotokoll (machine-to-machine), som utvecklades med sådana utmaningar i åtanke. 

MQTT-klientbiblioteket som används här är en del av [Eclipse Paho-projektet,](https://www.eclipse.org/paho/) som tillhandahåller API:er för att använda MQTT över flera transportmedel.

## <a name="what-you-learn"></a>Detta får du får lära dig

I det här projektet lär du dig:
- Så här använder du MQTT-klientbiblioteket för att skicka meddelanden till en MQTT-mäklare.
- Så här konfigurerar du din MXChip Iot DevKit som MQTT-klient.

## <a name="what-you-need"></a>Vad du behöver

Avsluta [komma igång-guiden](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) till:

* Ha din DevKit ansluten till Wi-Fi
* Förbereda utvecklingsmiljön

## <a name="open-the-project-folder"></a>Öppna projektmappen

1. Om DevKit redan är ansluten till datorn kopplar du från den.

2. Starta VS Code.

3. Anslut DevKit till datorn.

## <a name="open-the-mqttclient-sample"></a>Öppna MQTTClient-exemplet

Expandera avsnittet **ARDUINO EXAMPLES på** vänster sida, bläddra till Exempel för **MXCHIP AZ3166 > MQTT**och välj **MQTTClient**. Ett nytt VS-kodfönster öppnas med en projektmapp i det.

> [!NOTE]
> Du kan också öppna exempel från kommandopaletten. Använd `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) för att öppna kommandopaletten, skriv **Arduino**och leta sedan upp och välja **Arduino: Exempel**.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Bygg och ladda upp Arduino-skissen till DevKit

Skriv `Ctrl+P` (macOS: `Cmd+P`) `task device-upload`för att köra . När uppladdningen är klar startar DevKit om och kör skissen.

![ladda upp enheten](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> Felmeddelandet "Fel: AZ3166: Okänt paket" visas. Det här felet uppstår när kartongpaketindexet inte uppdateras korrekt. LÃ¶s problemet genom att [fÃ¶ã¶ ge dig avsnittet utveckling i vanliga frågor och svar om IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development).

## <a name="test-the-project"></a>Testa projektet

I VS-kod följer du den här proceduren för att öppna och konfigurera seriell övervakare:

1. Klicka `COM[X]` på ordet i statusfältet för att `STMicroelectronics` ![ställa in rätt COM-port med: set-com-port](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Klicka på ikonen för stickkontakt i statusfältet ![för att öppna serialövervakaren: serieskärmen](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. Klicka på det tal som representerar Baud-hastigheten `115200`i ![statusfältet och ställ in det på : set-baud-rate](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

Serieförteckningen visar alla meddelanden som skickas av exempelskissen. Skissen ansluter DevKit till Wi-Fi. När Wi-Fi-anslutningen har framgångsrikt skickar skissen ett meddelande till MQTT-mäklaren. Därefter skickar exemplet upprepade gånger två "iot.eclipse.org"-meddelanden med QoS 0 respektive QoS 1.

![seriell utgång](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Problem och feedback

Om du stöter på problem kan du läsa vanliga frågor och svar om [IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) eller ansluta med följande kanaler:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Se även

* [Anslut IoT DevKit AZ3166 till Azure IoT Hub i molnet](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [Skaka, Skaka för en Tweet](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig att konfigurera din MXChip Iot DevKit som en MQTT-klient och använda MQTT-klientbiblioteket för att skicka meddelanden till en MQTT-mäklare, här är de föreslagna nästa steg:

* [Azure IoT Remote Monitoring solution accelerator översikt](https://docs.microsoft.com/azure/iot-suite/)
* [Ansluta en MXChip IoT DevKit-enhet till ditt Azure IoT Central-program](/azure/iot-central/core/howto-connect-devkit)
