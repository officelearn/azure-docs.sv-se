---
title: Lär dig hur körningen hanterar enheter – Azure IoT Edge | Microsoft Docs
description: Lär dig hur moduler, säkerhet, kommunikation och rapportering på dina enheter hanteras av Azure IoT Edge-körningen
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/06/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 423825540c02d2788de7a6148ddcec3c654fd450
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2019
ms.locfileid: "66754800"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Förstå Azure IoT Edge-körningen och dess arkitektur

IoT Edge-körningen är en uppsättning program som göra en enhet till en IoT Edge-enhet. Sammantaget, IoT Edge-körtidskomponenter aktivera IoT Edge-enheter att få kod att köra kant och meddela resultaten. 

IoT Edge-körningen utför följande funktioner på IoT Edge-enheter:

* Installerar och uppdaterar arbetsbelastningar på enheten.
* Upprätthåller Azure IoT Edge-säkerhetsstandarder på enheten.
* Se till att [IoT Edge-moduler](iot-edge-modules.md) alltid körs.
* Rapporterar modulens hälsa till molnet för fjärrövervakning.
* Underlätta kommunikationen mellan nedströms lövenheter och IoT Edge-enheter.
* Underlätta kommunikationen mellan moduler på IoT Edge-enhet.
* Underlätta kommunikationen mellan IoT Edge-enheten och molnet.

![Runtime kommunicerar insikter och modulens hälsa till IoT Hub](./media/iot-edge-runtime/Pipeline.png)

Ansvaret för IoT Edge-körningen är indelade i två kategorier: kommunikation och modulen. Dessa två roller som utförs av två komponenter som ingår i IoT Edge-körningen. Den *IoT Edge hub* ansvarar för kommunikationen, medan den *IoT Edge-agenten* distribuerar och övervakar moduler. 

Både IoT Edge hub och IoT Edge-agenten är moduler, precis som andra moduler som körs på en IoT Edge-enhet. 

## <a name="iot-edge-hub"></a>IoT Edge hub

IoT Edge hub är en av två moduler som utgör Azure IoT Edge-körningen. Den fungerar som en lokal proxy för IoT-hubb genom att exponera samma protokollslutpunkterna som IoT Hub. Detta innebär som klienter (oavsett om enheter eller moduler) kan ansluta till IoT Edge-körningen precis samma sätt som på IoT Hub. 

>[!NOTE]
> IoT Edge hub har stöd för klienter som ansluter med hjälp av MQTT eller AMQP. Det har inte stöd för klienter som använder HTTP. 

IoT Edge hub är inte en fullständig version av IoT Hub som körs lokalt. Det finns några saker som IoT Edge hub tyst delegerar till IoT Hub. IoT Edge hub vidarebefordrar begäranden om autentisering till IoT Hub när en enhet först försöker ansluta. Efter först har anslutningen upprättats säkerhetsinformation cachelagras lokalt av IoT Edge hub. Efterföljande anslutningar från den enheten tillåts utan att behöva autentisera till molnet. 

För att minska bandbredden som din IoT Edge-lösning använder, IoT Edge hub optimerar hur många faktiska anslutningar görs till molnet. IoT Edge hub tar logiska anslutningar från klienter som moduler eller lövenheter och kombinerar dem för en enda fysisk anslutning till molnet. Information om den här processen är transparent för resten av lösningen. Klienter tror att de har sin egen anslutning till molnet, även om de är alla som skickas via samma anslutning. 

![IoT Edge hub är en gateway mellan fysiska enheter och IoT Hub](./media/iot-edge-runtime/Gateway.png)

IoT Edge hub kan avgöra om den är ansluten till IoT Hub. Om anslutningen bryts, sparar IoT Edge hub meddelanden eller twin uppdateringar lokalt. När en anslutningen återupprättas synkroniserar alla data. Platsen som används för den här tillfälliga cachen bestäms av en egenskap för modultvilling för IoT Edge-hubben. Storleken på cacheminnet är inte begränsat och kommer att växa så länge enheten har lagringskapacitet. Mer information finns i [offlinefunktionerna](offline-capabilities.md).

### <a name="module-communication"></a>Modulen kommunikation

IoT Edge hub underlättar modulen till modulen kommunikationen. Med IoT Edge behåller hub som en asynkron meddelandekö moduler oberoende av varandra. Moduler behöver bara ange indata som de godkänner meddelanden och utdata som de skriva meddelanden. En för lösningsutvecklare kan sätta ihop dessa indata och utdata så att modulerna som bearbetar data i ordningen som är specifika för lösningen. 

![IoT Edge Hub underlättar modulen till modulen kommunikationen](./media/iot-edge-runtime/module-endpoints.png)

Om du vill skicka data till IoT Edge hub, anropar metoden SendEventAsync i en modul. Det första argumentet anger på vilka utdata att skicka meddelandet. Följande pseudocode skickar ett meddelande på **output1**:

   ```csharp
   ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync(“output1”, message); 
   ```

Registrera ett återanrop som bearbetar inkommande meddelanden på en specifik indata för att ta emot ett meddelande. Följande pseudocode registrerar funktionen messageProcessor som ska användas för att bearbeta alla meddelanden som tas emot på **indata1**:

   ```csharp
   await client.SetInputMessageHandlerAsync(“input1”, messageProcessor, userContext);
   ```

Mer information om klassen ModuleClient och dess kommunikationsmetoder finns i API-referens för SDK det språk du föredrar: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C och Python](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable), eller [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

Lösningsutvecklaren är ansvarig för att ange reglerna som bestämmer hur IoT Edge hub skickar meddelanden mellan moduler. Routningsregler definieras i molnet och flyttas fram till IoT Edge-hubben i dess enhetstvilling. Samma syntax för IoT Hub vägar används för att definiera rutter mellan moduler i Azure IoT Edge. Mer information finns i [Lär dig hur du distribuerar moduler och upprätta vägar i IoT Edge](module-composition.md).   

![Rutter mellan moduler som går igenom IoT Edge hub](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>IoT Edge-agenten

IoT Edge-agenten är den modul som utgör Azure IoT Edge-körningen. Den är ansvarig för kontrollanten moduler, se till att de fortsätter att köras och rapporterar status för moduler tillbaka till IoT Hub. Precis som andra moduler använder dess modultvilling i IoT Edge-agenten för att spara konfigurationsinformationen. 

Den [IoT Edge security daemon](iot-edge-security-manager.md) startar IoT Edge-agenten vid start av enheten. Agenten hämtar dess modultvilling från IoT Hub och inspekterar distribution manifestet. Manifestet distribution är en JSON-fil som deklarerar de moduler som startas. 

Varje objekt i manifestet distribution innehåller specifik information om en modul och används av IoT Edge-agenten för att styra modulens livscykel. Vissa av egenskaperna mer intressant är: 

* **Settings.Image** – den behållaravbildning som IoT Edge-agenten använder för att starta modulen. IoT Edge-agenten måste konfigureras med autentiseringsuppgifterna för behållarregistret om avbildningen skyddas av ett lösenord. Autentiseringsuppgifter för container registry kan konfigureras via en fjärranslutning med hjälp av manifestet distribution eller på själva IoT Edge-enheten genom att uppdatera den `config.yaml` filen i mappen IoT Edge-programmet.
* **settings.createOptions** – en sträng som skickas direkt till daemonen Moby behållaren när du startar en modul behållare. Om du lägger till alternativ i den här egenskapen får för de avancerade konfigurationer som port vidarebefordran eller montering av volymer i en modul behållare.  
* **status för** – status som IoT Edge-agenten placerar modulen. Vanligtvis är det här värdet anges till *kör* de flesta vill IoT Edge-agenten att starta alla moduler direkt på enheten. Du kan dock ange det ursprungliga tillståndet för en modul för att stoppas och vänta tills en framtida tid att berätta för IoT Edge-agent ska starta en modul. IoT Edge-agenten rapporterar status för varje modul tillbaka till molnet i rapporterade egenskaper. Någon skillnad mellan önskad egenskap och rapporterad egenskap är ett tecken på en skadad enhet. Den stödda statusen är:
   * Laddas ned
   * Körs
   * Skadad
   * Misslyckad
   * Stoppad
* **restartPolicy** – hur IoT Edge-agenten startar om en modul. Möjliga värden omfattar:
   * `never` – IoT Edge-agenten startar aldrig om modulen.
   * `on-failure` -Om modulen kraschar, IoT Edge-agenten startar om den. Om modulen får en ren avstängning, startar inte IoT Edge-agenten om den.
   * `on-unhealthy` – Om modulen kraschar eller är anses vara felaktigt, IoT Edge-agenten startar om den.
   * `always` -Om modulen kraschar kan betraktas som defekt eller stängs av på något sätt, IoT Edge-agenten startar om den. 

IoT Edge-agenten skickar körningssvar till IoT Hub. Här är en lista över möjliga svar:
  * 200 - OK
  * 400 - distributionskonfigurationen är skadad eller ogiltig.
  * 417 - enheten har inte en distribution konfigurationsuppsättning.
  * 412 - schemaversion i distributionskonfigurationen är ogiltig.
  * 406 - IoT Edge-enheten är frånkopplad eller skickar inga statusrapporter.
  * 500 – ett fel inträffade i IoT Edge-körningen.

Mer information finns i [Lär dig hur du distribuerar moduler och upprätta vägar i IoT Edge](module-composition.md).   

### <a name="security"></a>Säkerhet

IoT Edge-agenten spelar en viktig roll i säkerheten för en IoT Edge-enhet. Till exempel utförs åtgärder som att verifiera en moduls avbildning innan du startar den. 

Mer information om Azure IoT Edge security framework Läs mer om den [IoT Edge-säkerhetshanteraren](iot-edge-security-manager.md).

## <a name="next-steps"></a>Nästa steg

[Förstå Azure IoT Edge-moduler](iot-edge-modules.md)
