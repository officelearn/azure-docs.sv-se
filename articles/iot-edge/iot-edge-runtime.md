---
title: Lär dig hur körningen hanterar enheter – Azure IoT Edge | Microsoft Docs
description: Lär dig hur IoT Edge runtime hanterar moduler, säkerhet, kommunikation och rapportering på dina enheter
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c184972789c412406f264f725f8b94e1f7f162ce
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358669"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Förstå Azure IoT Edge-körningen och dess arkitektur

IoT Edge runtime är en samling program som omvandlar en enhet till en IoT Edge enhet. IoT Edges körnings komponenter gör det möjligt för IoT Edge enheter att ta emot kod som ska köras vid gränsen och förmedla resultatet.

IoT Edge runtime ansvarar för följande funktioner på IoT Edge enheter:

* Installerar och uppdaterar arbetsbelastningar på enheten.
* Upprätthåller Azure IoT Edge-säkerhetsstandarder på enheten.
* Se till att [IoT Edge moduler](iot-edge-modules.md) alltid körs.
* Rapporterar modulens hälsa till molnet för fjärrövervakning.
* Hantera kommunikation mellan underordnade enheter och IoT Edge enheter.
* Hantera kommunikation mellan moduler på den IoT Edge enheten.
* Hantera kommunikationen mellan IoT Edge-enheten och molnet.

![Runtime kommunicerar insikter och modulens hälsa till IoT Hub](./media/iot-edge-runtime/Pipeline.png)

Ansvaret för IoT Edge-körningen är indelade i två kategorier: kommunikation och modulen. Dessa två roller utförs av två komponenter som ingår i IoT Edge Runtime. *IoT Edge Hub* ansvarar för kommunikation, medan *IoT Edge agent* distribuerar och övervakar modulerna.

Både IoT Edge Hub och IoT Edge agent är moduler, precis som andra moduler som körs på en IoT Edge enhet. De kallas ibland för *körnings moduler*.

## <a name="iot-edge-hub"></a>IoT Edge hub

IoT Edge Hub är en av två moduler som utgör Azure IoT Edge Runtime. Den fungerar som en lokal proxy för IoT-hubb genom att exponera samma protokollslutpunkterna som IoT Hub. Detta innebär som klienter (oavsett om enheter eller moduler) kan ansluta till IoT Edge-körningen precis samma sätt som på IoT Hub.

>[!NOTE]
> IoT Edge Hub stöder klienter som ansluter med MQTT eller AMQP. Det har inte stöd för klienter som använder HTTP.

IoT Edge hubben är inte en fullständig version av IoT Hub som körs lokalt. IoT Edge Hub delegerar vissa uppgifter till IoT Hub. IoT Edge hubben vidarebefordrar till exempel autentiseringsbegäranden till IoT Hub när en enhet först försöker ansluta. När den första anslutningen har upprättats cachelagras säkerhets informationen lokalt av IoT Edge Hub. Framtida anslutningar från den enheten tillåts utan att du behöver autentisera till molnet igen.

För att minska bandbredden som IoT Edge-lösningen använder optimerar IoT Edge Hub hur många faktiska anslutningar som görs till molnet. IoT Edge hub tar logiska anslutningar från moduler eller underordnade enheter och kombinerar dem för en enda fysisk anslutning till molnet. Information om den här processen är transparent för resten av lösningen. Klienter tror att de har sin egen anslutning till molnet, även om de är alla som skickas via samma anslutning.

![IoT Edge Hub är en gateway mellan fysiska enheter och IoT Hub](./media/iot-edge-runtime/Gateway.png)

IoT Edge Hub kan avgöra om det är anslutet till IoT Hub. Om anslutningen bryts sparar IoT Edge hubben meddelanden eller dubbla uppdateringar lokalt. När en anslutningen återupprättas synkroniserar alla data. Den plats som används för den här tillfälliga cachen bestäms av en egenskap hos den IoT Edge hubbens modul, dubbla. Storleken på cacheminnet är inte begränsat och kommer att växa så länge enheten har lagringskapacitet. Mer information finns i [offline-funktioner](offline-capabilities.md).

### <a name="module-communication"></a>Modulen kommunikation

IoT Edge hubb underlättar modulen kommunikation. Om du använder IoT Edge hubben som en meddelande utjämning lagras moduler oberoende av varandra. Moduler behöver bara ange indata som de godkänner meddelanden och utdata som de skriva meddelanden. En lösnings utvecklare kan sy ihop dessa indata och utdata, så att modulerna bearbetar data i den ordning som är speciell för lösningen.

![IoT Edge hubb underlättar kommunikation mellan moduler och moduler](./media/iot-edge-runtime/module-endpoints.png)

För att skicka data till IoT Edge Hub anropar en modul metoden SendEventAsync. Det första argumentet anger på vilka utdata att skicka meddelandet. Följande pseudocode skickar ett meddelande på **output1**:

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

Registrera ett återanrop som bearbetar inkommande meddelanden på en specifik indata för att ta emot ett meddelande. Följande pseudocode registrerar funktionen messageProcessor som ska användas för bearbetning av alla meddelanden som tas emot på **INPUT1**:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

Mer information om klassen ModuleClient och dess kommunikations metoder finns i API-referensen för ditt prioriterade SDK-språk: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)eller [Node. js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

Lösnings utvecklaren ansvarar för att ange regler som avgör hur IoT Edge Hub skickar meddelanden mellan moduler. Routningsregler definieras i molnet och flyttas ned till IoT Edge Hub i sin modul. Samma syntax för IoT Hub vägar används för att definiera rutter mellan moduler i Azure IoT Edge. Mer information finns i [Lär dig hur du distribuerar moduler och etablerar vägar i IoT Edge](module-composition.md).

![Vägar mellan moduler går till IoT Edge hubb](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>IoT Edge-agenten

IoT Edge-agenten är den modul som utgör Azure IoT Edge-körningen. Den är ansvarig för kontrollanten moduler, se till att de fortsätter att köras och rapporterar status för moduler tillbaka till IoT Hub. Den här konfigurations informationen skrivs som en egenskap för den IoT Edge agent-modulen.

[IoT Edge Security daemon](iot-edge-security-manager.md) startar IoT Edge agent vid enhets start. Agenten hämtar dess modultvilling från IoT Hub och inspekterar distribution manifestet. Manifestet distribution är en JSON-fil som deklarerar de moduler som startas.

Varje objekt i distributions manifestet innehåller detaljerad information om en modul och används av IoT Edge-agenten för att kontrol lera modulens livs cykel. Vissa av egenskaperna mer intressant är:

* **Settings. image** – den behållar avbildning som IoT Edge-agenten använder för att starta modulen. IoT Edge agenten måste konfigureras med autentiseringsuppgifter för behållar registret om avbildningen skyddas av ett lösen ord. Autentiseringsuppgifter för behållar registret kan konfigureras via en fjärr anslutning med hjälp av distributions manifestet eller på den IoT Edge själva enheten genom att uppdatera `config.yaml`-filen i mappen IoT Edge program.
* **Settings. createOptions** – en sträng som skickas direkt till Moby container daemon när du startar en moduls behållare. Genom att lägga till alternativ i den här egenskapen kan du använda avancerade konfigurationer som port vidarebefordran eller montera volymer i en moduls behållare.  
* **status** – den stat där IoT Edge agenten placerar modulen. Vanligt vis är det här värdet inställt på att *köras* som de flesta personer vill att den IoT Edge agenten omedelbart ska starta alla moduler på enheten. Du kan dock ange start status för en modul som ska stoppas och vänta en stund tills den IoT Edge agenten starta en modul. IoT Edge agenten rapporterar status för varje modul tillbaka till molnet i de rapporterade egenskaperna. Någon skillnad mellan önskad egenskap och rapporterad egenskap är ett tecken på en skadad enhet. Den stödda statusen är:

  * Laddas ned
  * Körs
  * Ohälsosamt
  * Misslyckades
  * Stoppad

* **restartPolicy** – hur den IoT Edge agenten startar om en modul. Möjliga värden omfattar:
  
  * `never` – IoT Edge agenten startar aldrig om modulen.
  * `on-failure`-om modulen kraschar startar IoT Edge Agent om den. Om modulen stängs av korrekt startar IoT Edge-agenten inte om den.
  * `on-unhealthy`-om modulen kraschar eller om den betraktas som ohälsosam, startas IoT Edge Agent om den.
  * `always`-om modulen kraschar, om den är ohälsosam eller om den stängs av på något sätt, startas IoT Edge Agent om den.

* **imagePullPolicy** – om IoT Edge-agenten försöker hämta den senaste avbildningen för en modul automatiskt eller inte. Om du inte anger något värde är standardvärdet *onCreate*. Möjliga värden omfattar:

  * `on-create` – när du startar en modul eller uppdaterar en modul baserat på ett nytt distributions manifest försöker IoT Edge agent Hämta modulens avbildning från behållar registret.
  * `never` – IoT Edges agenten försöker aldrig hämta modulens avbildning från behållar registret. Med den här konfigurationen ansvarar du för att hämta modul avbildningen till enheten och hantera eventuella avbildnings uppdateringar.

IoT Edge-agenten skickar körningssvar till IoT Hub. Här är en lista över möjliga svar:
  
* 200 - OK
* 400 - distributionskonfigurationen är skadad eller ogiltig.
* 417 – enheten har ingen distributions konfigurations uppsättning.
* 412 - schemaversion i distributionskonfigurationen är ogiltig.
* 406 – den IoT Edge enheten är offline eller skickar inte status rapporter.
* 500 – ett fel uppstod i IoT Edge Runtime.

Mer information finns i [Lär dig hur du distribuerar moduler och etablerar vägar i IoT Edge](module-composition.md).

### <a name="security"></a>Säkerhet

IoT Edge-agenten spelar en viktig roll i säkerheten för en IoT Edge-enhet. Till exempel utförs åtgärder som att verifiera en moduls avbildning innan du startar den.

Mer information om säkerhets ramverket Azure IoT Edge finns i [IoT Edge Security Manager](iot-edge-security-manager.md).

## <a name="next-steps"></a>Nästa steg

[Förstå Azure IoT Edge moduler](iot-edge-modules.md)
