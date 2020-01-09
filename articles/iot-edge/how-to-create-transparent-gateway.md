---
title: Skapa transparent gateway-enhet – Azure IoT Edge | Microsoft Docs
description: Använda en Azure IoT Edge-enhet som en transparent gateway som kan bearbeta information från underordnade enheter
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2fb552578bf7c1af70b6efb4f2f6f02a2f20f2be
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434347"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Konfigurera en IoT Edge-enhet kan fungera som en transparent gateway

Den här artikeln innehåller detaljerade anvisningar för hur du konfigurerar en IoT Edge-enhet så att den fungerar som en transparent Gateway för andra enheter att kommunicera med IoT Hub. I den här artikeln används termen *IoT Edge Gateway* för att referera till en IoT Edge enhet som kon figurer ATS som en transparent Gateway. Mer information finns i [så här kan en IoT Edge enhet användas som en gateway](./iot-edge-as-gateway.md).

>[!NOTE]
>För närvarande:
> * Edge-aktiverade enheter kan inte ansluta till IoT Edge-gateways. 
> * Efterföljande enheter kan inte använda filuppladdningen.

Det finns tre allmänna steg för att konfigurera en lyckad transparent Gateway-anslutning. Den här artikeln beskriver det första steget:

1. **Gateway-enheten måste kunna ansluta till underordnade enheter på ett säkert sätt, ta emot kommunikation från underordnade enheter och dirigera meddelanden till rätt mål.**
2. Den underordnade enheten måste ha en enhets identitet för att kunna autentisera med IoT Hub och kunna kommunicera via dess gateway-enhet. Mer information finns i [autentisera en underordnad enhet till Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. Den underordnade enheten måste ansluta till sin gateway-enhet på ett säkert sätt. Mer information finns i [en underordnad ansluts till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md).


För att en enhet ska fungera som en gateway måste den kunna ansluta till dess underordnade enheter på ett säkert sätt. Azure IoT Edge kan du använda en public key infrastructure (PKI) för att konfigurera säkra anslutningar mellan enheter. I det här fallet vi så att en underordnad enhet att ansluta till en IoT Edge-enhet som fungerar som en transparent gateway. För att upprätthålla rimlig säkerhet bör den underordnade enheten bekräfta gateway-enhetens identitet. Den här identitets kontrollen förhindrar att enheterna ansluter till potentiellt skadliga gatewayer.

En underordnad enhet i ett scenario med transparent Gateway kan vara vilket program eller vilken plattform som helst som har en identitet som skapats med [Azure IoT Hub](https://docs.microsoft.com/azure/iot-hub) Cloud service. I många fall kan dessa program använda den [Azure IoT-enhetens SDK](../iot-hub/iot-hub-devguide-sdks.md). En underordnad enhet kan även vara ett program som körs på IoT Edge-gatewayenhet själva för alla praktiska syften. En IoT Edge enhet kan dock inte underordnas IoT Edge Gateway. 

Du kan skapa någon infrastruktur för certifikat som gör det förtroendet som krävs för din enhet-gateway-topologi. I den här artikeln förutsätter vi samma certifikat inställningar som du använde för att aktivera [x. 509 ca-säkerhet](../iot-hub/iot-hub-x509ca-overview.md) i IoT Hub, vilket inbegriper ett X. 509 CA-certifikat som är kopplat till en speciell IoT-hubb (IoT Hub rot certifikat utfärdare), en serie med certifikat som har signerats med denna certifikat utfärdare och en certifikat utfärdare för IoT Edges enheten.

![Installationsprogram för gateway-certifikatet](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>Termen "rot certifikat utfärdare" som används i den här artikeln avser det offentliga auktoritets certifikatet för PKI-certifikat kedjan och inte nödvändigt vis certifikat roten för en syndikerad certifikat utfärdare. I många fall är det faktiskt ett offentligt certifikat för certifikat utfärdare. 

Gatewayen visar IoT Edge enhetens CA-certifikat till den underordnade enheten under initieringen av anslutningen. Den underordnade enheten kontrollerar att IoT Edge enhetens CA-certifikat är signerat av rot certifikat utfärdarens certifikat. Den här processen gör att den underordnade enheten kan bekräfta att gatewayen kommer från en betrodd källa.

Följande steg vägleder dig genom processen att skapa certifikaten och installera dem på rätt plats på gatewayen. Du kan använda valfri dator för att generera certifikat och kopiera dem sedan till din IoT Edge-enhet. 

## <a name="prerequisites"></a>Krav

En Azure IoT Edge enhet som kon figurer ATS med [produktions certifikat](how-to-install-production-certificates.md).

## <a name="deploy-edgehub-to-the-gateway"></a>Distribuera edgeHub till gatewayen

Första gången du installerar IoT Edge på en enhet startar bara en systemmodul automatiskt: IoT Edge agenten. När du har skapat den första distributionen av mer än en enhet, startas den andra systemmodulen, IoT Edge hubben, också. 

IoT Edge Hub ansvarar för att ta emot inkommande meddelanden från underordnade enheter och dirigera dem till nästa mål. Om modulen **edgeHub** inte körs på enheten skapar du en första distribution för enheten. Distributionen kommer att se Tom ut eftersom du inte lägger till några moduler, men det ser till att båda systemmodulerna körs. 

Du kan kontrol lera vilka moduler som körs på en enhet genom att kontrol lera enhets informationen i Azure Portal, Visa enhets status i Visual Studio eller Visual Studio Code eller genom att köra kommandot `iotedge list` på själva enheten. 

Om modulen **edgeAgent** körs utan modulen **edgeHub** använder du följande steg:

1. Gå till din IoT-hubb på Azure Portal.

2. Gå till **IoT Edge** och välj din IoT Edge-enhet som du vill använda som en gateway.

3. Välj **Ange moduler**.

4. Välj **Nästa**.

5. I den **ange vägar** sidan bör du ha en standardväg som skickar alla meddelanden från alla moduler till IoT Hub. Om du inte har det lägger du till följande kod och väljer sedan **Nästa**.

   ```JSON
   {
       "routes": {
           "route": "FROM /* INTO $upstream"
       }
   }
   ```

6. I den **granska mallen** väljer **skicka**.

## <a name="open-ports-on-gateway-device"></a>Öppna portar på gateway-enhet

Standard IoT Edge enheter behöver ingen inkommande anslutning för att fungera eftersom all kommunikation med IoT Hub görs via utgående anslutningar. Gateway-enheter skiljer sig åt eftersom de måste ta emot meddelanden från deras underordnade enheter. Om en brand vägg är mellan de efterföljande enheterna och gateway-enheten, behöver kommunikationen också vara möjlig genom brand väggen.

För att ett Gateway-scenario ska fungera måste minst ett av de protokoll som stöds av IoT Edge Hub vara öppet för inkommande trafik från efterföljande enheter. De protokoll som stöds är MQTT, AMQP, HTTPS, MQTT över WebSockets och AMQP för WebSockets. 

| Port | Protokoll |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT + WS <br> AMQP+WS | 

## <a name="route-messages-from-downstream-devices"></a>Skicka meddelanden från underordnade enheter
IoT Edge-körningen kan dirigera meddelanden som skickas från underordnade enheter precis som meddelanden som skickas av moduler. Med den här funktionen kan du utföra analyser i en modul som körs på gatewayen innan du skickar data till molnet. 

För närvarande är det sätt som du vidarebefordra meddelanden som skickas av efterföljande enheter genom att skilja dem från meddelanden som skickas av moduler. Meddelanden som skickas av moduler som alla innehåller en systemegenskap som kallas **connectionModuleId** men inte meddelanden som skickas av efterföljande enheter. Du kan använda WHERE-satsen i vägen för att exkludera alla meddelanden som innehåller den Systemegenskapen. 

Nedanstående väg är ett exempel som skickar meddelanden från en underordnad enhet till en modul med namnet `ai_insights`och sedan från `ai_insights` till IoT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")", 
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream" 
    } 
}
```

Läs mer om meddelanderoutning [distribuerar moduler och upprätta vägar](./module-composition.md#declare-routes).


## <a name="enable-extended-offline-operation"></a>Aktivera utökad offline-åtgärd

Från och med [v 1.0.4-versionen](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) av IoT Edge runtime kan gateway-enheten och underordnade enheter som ansluter till den konfigureras för utökad offline-åtgärd. 

Med den här funktionen kan lokala moduler eller underordnade enheter återautentiseras med den IoT Edge enheten vid behov och kommunicera med varandra med hjälp av meddelanden och metoder, även när de är frånkopplade från IoT-hubben. Mer information finns i [förstå utökat offlinefunktionerna för IoT Edge-enheter, moduler och underordnade enheter](offline-capabilities.md).

Om du vill aktivera utökade offline-funktioner upprättar du en överordnad-underordnad relation mellan en IoT Edge gateway-enhet och underordnade enheter som ska ansluta till den. Dessa steg beskrivs i detalj i [autentisera en underordnad enhet till Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har en IoT Edge-enhet som fungerar som en transparent gateway, måste du konfigurera dina underordnade enheter för att lita på gatewayen och skicka meddelanden till den. Fortsätt med att [autentisera en underordnad enhet till Azure IoT Hub](how-to-authenticate-downstream-device.md) för nästa steg i konfigurationen av scenariot för transparent Gateway. 
