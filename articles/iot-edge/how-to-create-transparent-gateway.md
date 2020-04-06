---
title: Skapa transparent gateway-enhet – Azure IoT Edge | Microsoft-dokument
description: Använda en Azure IoT Edge-enhet som en transparent gateway som kan bearbeta information från underordnade enheter
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/03/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3b3aeff595671c5f924d01599b572b6b938ef09d
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666671"
---
# <a name="configure-an-iot-edge-device-to-act-as-a-transparent-gateway"></a>Konfigurera en IoT Edge-enhet till att fungera som en transparent gateway

Den här artikeln innehåller detaljerade instruktioner för hur du konfigurerar en IoT Edge-enhet så att den fungerar som en transparent gateway för andra enheter att kommunicera med IoT Hub. I den här artikeln används termen *IoT Edge-gateway* för att referera till en IoT Edge-enhet som konfigurerats som en transparent gateway. Mer information finns i [Hur en IoT Edge-enhet kan användas som en gateway](./iot-edge-as-gateway.md).

>[!NOTE]
>För närvarande:
>
> * Edge-aktiverade enheter kan inte ansluta till IoT Edge-gateways.
> * Nedströmsenheter kan inte använda filöverföring.

Det finns tre allmänna steg för att konfigurera en lyckad transparent gateway-anslutning. Den här artikeln beskriver det första steget:

1. **Gateway-enheten måste kunna ansluta till nedströmsenheter på ett säkert sätt, ta emot kommunikation från nedströmsenheter och dirigera meddelanden till rätt mål.**
2. Nedströmsenheten måste ha en enhetsidentitet för att kunna autentisera med IoT Hub och veta att kommunicera via sin gateway-enhet. Mer information finns i [Autentisera en nedströmsenhet till Azure IoT Hub](how-to-authenticate-downstream-device.md).
3. Nedströmsenheten måste ansluta till gatewayenheten på ett säkert sätt. Mer information finns i [Ansluta en nedströmsenhet till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md).

För att en enhet ska fungera som en gateway måste den kunna ansluta till sina nedströmsenheter på ett säkert sätt. Med Azure IoT Edge kan du använda en infrastruktur för offentliga nycklar (PKI) för att konfigurera säkra anslutningar mellan enheter. I det här fallet tillåter vi en nedströms enhet att ansluta till en IoT Edge-enhet som fungerar som en transparent gateway. För att upprätthålla rimlig säkerhet bör nedströmsenheten bekräfta gateway-enhetens identitet. Den här identitetskontrollen hindrar dina enheter från att ansluta till potentiellt skadliga gateways.

En nedströmsenhet i ett transparent gateway-scenario kan vara alla program eller plattformar som har en identitet som skapats med [Azure IoT](https://docs.microsoft.com/azure/iot-hub) Hub-molntjänsten. I många fall använder dessa program [Azure IoT-enheten SDK](../iot-hub/iot-hub-devguide-sdks.md). För alla praktiska ändamål kan en nedströmsenhet till och med vara ett program som körs på själva IoT Edge-gatewayenheten. En IoT Edge-enhet kan dock inte vara nedströms en IoT Edge-gateway.

Du kan skapa alla certifikatinfrastrukturer som aktiverar det förtroende som krävs för din enhetsgatewaytop. I den här artikeln antar vi samma certifikatinställningar som du använder för att aktivera [X.509 CA-säkerhet](../iot-hub/iot-hub-x509ca-overview.md) i IoT Hub, vilket innebär ett X.509 CA-certifikat som är associerat till en specifik IoT-hubb (IoT-hubbrotscertifikatutfärdaren), en serie certifikat som har signerats med den här certifikatutfärdaren och en certifikatutfärdare för IoT Edge-enheten.

![Konfigurera gatewaycertifikat](./media/how-to-create-transparent-gateway/gateway-setup.png)

>[!NOTE]
>Termen "root CA" som används i hela den här artikeln refererar till det högsta myndighetsutfärdarcertifikatet för PKI-certifikatkedjan och inte nödvändigtvis certifikatroten för en syndikerad certifikatutfärdare. I många fall är det faktiskt ett offentligt certifikat för den mellanliggande certifikatutfärdaren.

Säkerhetsdemonen IoT Edge använder certifikatet för IoT Edge-enhetscertifikatutfärdare för att signera ett certifikat för arbetsbelastningscertifikat för certifikatutfärdare, vilket i sin tur signerar ett servercertifikat för IoT Edge-hubb. Gatewayen visar sitt servercertifikat för nedströmsenheten under anslutningens initiering. Nedströmsenheten kontrollerar att servercertifikatet är en del av en certifikatkedja som summerar rotcertifikatutfärdarcertifikatet. Den här processen gör det möjligt för nedströmsenheten att bekräfta att gatewayen kommer från en betrodd källa. Mer information finns i [Förstå hur Azure IoT Edge använder certifikat](iot-edge-certs.md).

Följande steg går igenom processen att skapa certifikaten och installera dem på rätt ställen i gatewayen. Du kan använda vilken dator som helst för att generera certifikaten och sedan kopiera dem till din IoT Edge-enhet.

## <a name="prerequisites"></a>Krav

En Azure IoT Edge-enhet som konfigurerats med [produktionscertifikat](how-to-manage-device-certificates.md).

## <a name="deploy-edgehub-to-the-gateway"></a>Distribuera edgeHub till gatewayen

När du först installerar IoT Edge på en enhet startar endast en systemmodul automatiskt: IoT Edge-agenten. När du har skapat den första distributionen mer startas även den andra systemmodulen, IoT Edge-hubben.

IoT Edge-hubben ansvarar för att ta emot inkommande meddelanden från nedströmsenheter och dirigera dem till nästa mål. Om **edgeHub-modulen** inte körs på enheten skapar du en första distribution för enheten. Distributionen ser tom ut eftersom du inte lägger till några moduler, men det kommer att se till att båda systemmodulerna körs.

Du kan kontrollera vilka moduler som körs på en enhet genom att kontrollera dess enhetsinformation i Azure-portalen, `iotedge list` visa enhetsstatus i Visual Studio eller Visual Studio-kod eller genom att köra kommandot på själva enheten.

Om **edgeAgent-modulen** körs utan **edgeHub-modulen** gör du följande:

1. Gå till din IoT-hubb på Azure Portal.

2. Gå till **IoT Edge** och välj din IoT Edge-enhet som du vill använda som en gateway.

3. Välj **Ange moduler**.

4. Välj **Nästa**.

5. På sidan **Ange vägar** bör du ha en standardväg som skickar alla meddelanden från alla moduler till IoT Hub. Om du inte har det lägger du till följande kod och väljer sedan **Nästa**.

   ```JSON
   {
       "routes": {
           "route": "FROM /messages/* INTO $upstream"
       }
   }
   ```

6. På **sidan Granska mall** väljer du **Skicka**.

## <a name="open-ports-on-gateway-device"></a>Öppna portar på gateway-enhet

Standard-IoT Edge-enheter behöver ingen inkommande anslutning för att fungera, eftersom all kommunikation med IoT Hub sker via utgående anslutningar. Gateway-enheter är olika eftersom de måste ta emot meddelanden från sina underordnade enheter. Om en brandvägg är mellan nedströmsenheterna och gateway-enheten måste kommunikationen också vara möjlig via brandväggen.

För att ett gateway-scenario ska fungera måste minst ett av IoT Edge-hubbens protokoll som stöds vara öppet för inkommande trafik från nedströmsenheter. Protokoll som stöds är MQTT, AMQP, HTTPS, MQTT över WebSockets och AMQP över WebSockets.

| Port | Protokoll |
| ---- | -------- |
| 8883 | MQTT |
| 5671 | AMQP |
| 443 | HTTPS <br> MQTT+WS <br> AMQP+WS |

## <a name="route-messages-from-downstream-devices"></a>Dirigera meddelanden från nedströmsenheter

IoT Edge-körningen kan dirigera meddelanden som skickas från nedströmsenheter precis som meddelanden som skickas av moduler. Med den här funktionen kan du utföra analyser i en modul som körs på gatewayen innan du skickar data till molnet.

För närvarande är det sätt som du dirigerar meddelanden som skickas av nedströms enheter genom att skilja dem från meddelanden som skickas av moduler. Meddelanden som skickas av moduler innehåller alla en systemegenskap som kallas **connectionModuleId,** men meddelanden som skickas av underordnade enheter inte. Du kan använda WHERE-satsen i flödet för att utesluta meddelanden som innehåller den systemegenskapen.

Nedanstående väg är ett exempel som skulle skicka meddelanden från `ai_insights`alla nedströmsenheter till en modul med namnet och sedan från `ai_insights` till IoT Hub.

```json
{
    "routes":{
        "sensorToAIInsightsInput1":"FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO BrokeredEndpoint(\"/modules/ai_insights/inputs/input1\")",
        "AIInsightsToIoTHub":"FROM /messages/modules/ai_insights/outputs/output1 INTO $upstream"
    }
}
```

Mer information om meddelanderoutning finns i [Distribuera moduler och upprätta vägar](./module-composition.md#declare-routes).

## <a name="enable-extended-offline-operation"></a>Aktivera utökad offlineåtgärd

Från och med [v1.0.4-versionen](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) av IoT Edge-körningen kan gateway-enheten och nedströmsenheter som ansluter till den konfigureras för utökad offlinedrift.

Med den här funktionen kan lokala moduler eller nedströmsenheter omauktisera med IoT Edge-enheten efter behov och kommunicera med varandra med meddelanden och metoder även när de kopplas från IoT-hubben. Mer information finns i [Förstå utökade offlinefunktioner för IoT Edge-enheter, moduler och underordnade enheter](offline-capabilities.md).

Om du vill aktivera utökade offlinefunktioner upprättar du en överordnad och underordnad relation mellan en IoT Edge-gatewayenhet och underordnade enheter som ansluter till den. Dessa steg förklaras mer i detalj i [Autentisera en nedströmsenhet till Azure IoT Hub](how-to-authenticate-downstream-device.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har en IoT Edge-enhet som fungerar som en transparent gateway måste du konfigurera dina nedströmsenheter för att lita på gatewayen och skicka meddelanden till den. Fortsätt att [autentisera en nedströmsenhet till Azure IoT Hub](how-to-authenticate-downstream-device.md) för nästa steg i att konfigurera ditt transparenta gatewayscenario.
