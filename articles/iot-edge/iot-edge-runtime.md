---
title: Lär dig hur körningen hanterar enheter – Azure IoT Edge | Microsoft-dokument
description: Lär dig hur IoT Edge-körningen hanterar moduler, säkerhet, kommunikation och rapportering på dina enheter
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: ef31bd74c73aa081c32031b71392f69a1ca14f75
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81730911"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Förstå Azure IoT Edge-körningen och dess arkitektur

IoT Edge-körningen är en samling program som omvandlar en enhet till en IoT Edge-enhet. Tillsammans gör IoT Edge-körningskomponenterna det möjligt för IoT Edge-enheter att ta emot kod för att köras vid kanten och kommunicera resultaten.

IoT Edge-körningen ansvarar för följande funktioner på IoT Edge-enheter:

* Installerar och uppdaterar arbetsbelastningar på enheten.
* Upprätthåller Azure IoT Edge-säkerhetsstandarder på enheten.
* Se till att [IoT Edge-moduler](iot-edge-modules.md) alltid körs.
* Rapporterar modulens hälsa till molnet för fjärrövervakning.
* Hantera kommunikation mellan nedströmsenheter och IoT Edge-enheter.
* Hantera kommunikation mellan moduler på IoT Edge-enheten.
* Hantera kommunikationen mellan IoT Edge-enheten och molnet.

![Runtime kommunicerar insikter och modulhälsa till IoT Hub](./media/iot-edge-runtime/Pipeline.png)

Ansvaret för IoT Edge runtime delas in i två kategorier: kommunikation och modulhantering. Dessa två roller utförs av två komponenter som ingår i IoT Edge-körningen.*IoT Edge-hubben* ansvarar för kommunikationen, medan *IoT Edge-agenten* distribuerar och övervakar modulerna.

Både IoT Edge-hubben och IoT Edge-agenten är moduler, precis som alla andra moduler som körs på en IoT Edge-enhet. De kallas ibland *runtime-moduler.*

## <a name="iot-edge-hub"></a>IoT Edge-nav

IoT Edge-hubben är en av två moduler som utgör Azure IoT Edge-körningen. Den fungerar som en lokal proxy för IoT Hub genom att exponera samma protokollslutpunkter som IoT Hub. Den här konsekvensen innebär att klienter (oavsett om enheter eller moduler) kan ansluta till IoT Edge-körningen på samma sätt som de skulle till IoT Hub.

>[!NOTE]
> IoT Edge-hubben stöder klienter som ansluter med MQTT eller AMQP. Det stöder inte klienter som använder HTTP.

IoT Edge-hubben är inte en fullständig version av IoT Hub som körs lokalt. IoT Edge-hubben delegerar tyst vissa uppgifter till IoT Hub. IoT Edge-hubben vidarebefordrar till exempel autentiseringsbegäranden till IoT Hub när en enhet först försöker ansluta. När den första anslutningen har upprättats cachelagras säkerhetsinformation lokalt av IoT Edge-hubben. Framtida anslutningar från den enheten tillåts utan att behöva autentisera till molnet igen.

För att minska bandbredden som IoT Edge-lösningen använder optimerar IoT Edge-hubben hur många faktiska anslutningar som görs till molnet. IoT Edge-hubben tar logiska anslutningar från moduler eller nedströmsenheter och kombinerar dem för en enda fysisk anslutning till molnet. Detaljerna i denna process är transparenta för resten av lösningen. Klienter tror att de har sin egen anslutning till molnet trots att de alla skickas över samma anslutning.

![IoT Edge-hubb är en gateway mellan fysiska enheter och IoT Hub](./media/iot-edge-runtime/Gateway.png)

IoT Edge-hubben kan avgöra om den är ansluten till IoT Hub. Om anslutningen bryts sparar IoT Edge-hubben meddelanden eller dubbla uppdateringar lokalt. När en anslutning har återupprättats synkroniseras alla data. Platsen som används för den här tillfälliga cachen bestäms av en egenskap för IoT Edge-hubbens modultvilling. Storleken på cacheminnet är inte begränsad och kommer att växa så länge enheten har lagringskapacitet.Mer information finns i [Offlinefunktioner](offline-capabilities.md).

### <a name="module-communication"></a>Modulkommunikation

IoT Edge-hubben underlättar modul till modulkommunikation. Om du använder IoT Edge-hubben som meddelandemäklare håller moduler oberoende av varandra. Moduler behöver bara ange de indata som de accepterar meddelanden och utdata som de skriver meddelanden till. En lösningsutvecklare kan sy ihop dessa in- och utdata så att modulerna bearbetar data i den ordning som är specifik för den lösningen.

![IoT Edge Hub underlättar kommunikation mellan modul och modul](./media/iot-edge-runtime/module-endpoints.png)

Om du vill skicka data till IoT Edge-hubben anropar en modul metoden SendEventAsync. Det första argumentet anger på vilket utdata som ska skickas. Följande pseudokod skickar ett meddelande vid **utdata1:**

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

Om du vill ta emot ett meddelande registrerar du en motringning som bearbetar meddelanden som kommer in på en viss indata. Följande pseudokod registrerar funktionsmeddelandetProcessor som ska användas för bearbetning av alla meddelanden som tas emot **vid indata1:**

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

Mer information om klassen ModuleClient och dess kommunikationsmetoder finns i API-referensen för önskat SDK-språk: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)eller [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

Lösningsutvecklaren är ansvarig för att ange de regler som avgör hur IoT Edge-hubben skickar meddelanden mellan moduler. Routningsregler definieras i molnet och trycks ned till IoT Edge-hubben i modulens tvilling. Samma syntax för IoT Hub-vägar används för att definiera vägar mellan moduler i Azure IoT Edge. Mer information finns i [Lär dig hur du distribuerar moduler och upprättar vägar i IoT Edge](module-composition.md).

![Rutter mellan moduler går via IoT Edge-hubben](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>IoT Edge-agent

IoT Edge-agenten är den andra modulen som utgör Azure IoT Edge-körningen. Den ansvarar för att instansiera moduler, se till att de fortsätter att köras och rapportera status för modulerna tillbaka till IoT Hub. Dessa konfigurationsdata skrivs som en egenskap för IoT Edge-agentmodulens tvilling.

[Säkerhetsdemonen IoT Edge](iot-edge-security-manager.md) startar IoT Edge-agenten när enheten startas. Agenten hämtar sin modultvilling från IoT Hub och inspekterar distributionsmanifestet. Distributionsmanifestet är en JSON-fil som deklarerar de moduler som måste startas.

Varje objekt i distributionsmanifestet innehåller specifik information om en modul och används av IoT Edge-agenten för att styra modulens livscykel. Några av de mer intressanta egenskaper är:

* **settings.image** – Behållaravbildningen som IoT Edge-agenten använder för att starta modulen. IoT Edge-agenten måste konfigureras med autentiseringsuppgifter för behållarregistret om avbildningen skyddas av ett lösenord. Autentiseringsuppgifter för behållarregistret kan fjärrstyras med distributionsmanifestet eller på `config.yaml` själva IoT Edge-enheten genom att uppdatera filen i programmappen IoT Edge.
* **settings.createOptions** – En sträng som skickas direkt till Moby-containerdemonen när du startar en moduls behållare. Genom att lägga till alternativ i den här egenskapen kan avancerade konfigurationer som portbefordring eller monteringsvolymer i en moduls behållare.  
* **status** – Det tillstånd där IoT Edge-agenten placerar modulen. Vanligtvis är det här värdet inställt på att *köras* eftersom de flesta vill att IoT Edge-agenten omedelbart ska starta alla moduler på enheten. Du kan dock ange det ursprungliga tillståndet för en modul som ska stoppas och vänta på en framtida tid för att tala om för IoT Edge-agenten att starta en modul.IoT Edge-agenten rapporterar status för varje modul tillbaka till molnet i de rapporterade egenskaperna. En skillnad mellan den önskade egenskapen och den rapporterade egenskapen är en indikator på en felaktig enhet. Statusen som stöds är:

  * Ladda ner
  * Körs
  * Inte felfri
  * Misslyckades
  * Stoppad

* **restartPolicy** – Så startar IoT Edge-agenten om en modul. Möjliga värden inkluderar:
  
  * `never`– IoT Edge-agenten startar aldrig om modulen.
  * `on-failure`- Om modulen kraschar startar IoT Edge-agenten om den. Om modulen stängs av på ett rent sätt startar inte IoT Edge-agenten om den.
  * `on-unhealthy`- Om modulen kraschar eller anses vara ohälsosam startar IoT Edge-agenten om den.
  * `always`- Om modulen kraschar, anses vara ohälsosam eller stängs av på något sätt startar IoT Edge-agenten om den.

* **imagePullPolicy** - Om IoT Edge-agenten försöker hämta den senaste avbildningen för en modul automatiskt eller inte. Om du inte anger något värde finns standardvärdet *påSkap*. Möjliga värden inkluderar:

  * `on-create`- När du startar en modul eller uppdaterar en modul baserat på ett nytt distributionsmanifest, försöker IoT Edge-agenten hämta modulavbildningen från behållarregistret.
  * `never`- IoT Edge-agenten kommer aldrig att försöka hämta modulavbildningen från behållarregistret. Med den här konfigurationen är du ansvarig för att få modulavbildningen till enheten och hantera eventuella avbildningsuppdateringar.

IoT Edge-agenten skickar körningssvar till IoT Hub. Här är en lista över möjliga svar:
  
* 200 - OK
* 400 - Distributionskonfigurationen är felaktig eller ogiltig.
* 417 - Enheten har ingen distributionskonfigurationsuppsättning.
* 412 - Schemaversionen i distributionskonfigurationen är ogiltig.
* 406 - IoT Edge-enheten är offline eller skickar inte statusrapporter.
* 500 - Ett fel uppstod i IoT Edge-körningen.

Mer information finns i [Lär dig hur du distribuerar moduler och upprättar vägar i IoT Edge](module-composition.md).

### <a name="security"></a>Säkerhet

IoT Edge-agenten spelar en avgörande roll för säkerheten för en IoT Edge-enhet. Till exempel utför åtgärder som att verifiera en moduls avbildning innan du startar den.

Mer information om säkerhetsramverket för Azure IoT Edge finns i [IoT Edge-säkerhetshanteraren](iot-edge-security-manager.md).

## <a name="next-steps"></a>Nästa steg

[Förstå Azure IoT Edge-moduler](iot-edge-modules.md)
