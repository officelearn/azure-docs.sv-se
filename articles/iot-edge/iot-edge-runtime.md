---
title: Förstå Azure IoT Edge-körningen | Microsoft Docs
description: Läs mer om Azure IoT Edge-körningen och hur den hjälper dina gränsenheter
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 08/13/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 97a2180aaf236d3541cff30d2151f26ce70b14af
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49393482"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Förstå Azure IoT Edge-körningen och dess arkitektur

IoT Edge-körningen är en uppsättning program som måste installeras på en enhet för att den ska betraktas som en IoT Edge-enhet. Sammantaget, komponenterna i IoT Edge-körningen aktivera IoT Edge-enheter att få kod att köra kant och meddela resultaten. 

IoT Edge-körningen utför följande funktioner på IoT Edge-enheter:

* Installerar och uppdaterar arbetsbelastningar på enheten.
* Underhåller Azure IoT Edge-säkerhetsstandarder på enheten.
* Säkerställer att [IoT Edge-moduler](iot-edge-modules.md) alltid körs.
* Rapporterar modulens hälsa till molnet för fjärrövervakning.
* Underlättar kommunikationen mellan nedströms lövenheter och IoT Edge-enheter.
* Underlättar kommunikationen mellan moduler på IoT Edge-enheten.
* Underlättar kommunikationen mellan IoT Edge-enheten och molnet.

![IoT Edge-körningen kommunicerar insikter och modulens hälsa till IoT Hub](./media/iot-edge-runtime/Pipeline.png)

Ansvaret för IoT Edge-körningen är indelade i två kategorier: kommunikation och modulen. Dessa två roller som utförs av två komponenter som utgör IoT Edge-körningen. IoT Edge hub ansvarar för kommunikation, medan IoT Edge-agenten hanterar distribution och övervakning av moduler. 

Både Edge hub och Edge-agenten är moduler, precis som andra moduler som körs på en IoT Edge-enhet. 

## <a name="iot-edge-hub"></a>IoT Edge hub

Edge hub är en av två moduler som utgör Azure IoT Edge-körningen. Den fungerar som en lokal proxy för IoT-hubb genom att exponera samma protokollslutpunkterna som IoT Hub. Detta innebär som klienter (oavsett om enheter eller moduler) kan ansluta till IoT Edge-körningen precis samma sätt som på IoT Hub. 

>[!NOTE]
>Edge Hub har stöd för klienter som ansluter med hjälp av MQTT eller AMQP. Det har inte stöd för klienter som använder HTTP. 

Edge hub är inte en fullständig version av IoT Hub som körs lokalt. Det finns några saker som Edge hub tyst delegerar till IoT Hub. Edge hub vidarebefordrar begäranden om autentisering till IoT Hub när en enhet först försöker ansluta. När den första anslutningen har upprättats kan cachelagras säkerhetsinformation lokalt Edge hub. Efterföljande anslutningar från den enheten tillåts utan att behöva autentisera till molnet. 

>[!NOTE]
>Vara måste ansluten körningen varje gång den försöker autentisera en enhet.

För att minska bandbredden som din IoT Edge-lösning använder, Edge hub optimerar hur många faktiska anslutningar görs till molnet. Edge hub tar logiska anslutningar från klienter som moduler eller lövenheter och kombinerar dem för en enda fysisk anslutning till molnet. Information om den här processen är transparent för resten av lösningen. Klienter tror att de har sin egen anslutning till molnet, även om de är alla som skickas via samma anslutning. 

![Edge hub fungerar som en gateway mellan flera fysiska enheter och molnet](./media/iot-edge-runtime/Gateway.png)

Edge hub kan avgöra om den är ansluten till IoT Hub. Om anslutningen bryts, sparar Edge hub meddelanden eller twin uppdateringar lokalt. När en anslutningen återupprättas synkroniserar alla data. Platsen som används för den här tillfälliga cachen bestäms av en egenskap för Edge hub modultvilling. Storleken på cacheminnet är inte begränsat och kommer att växa så länge enheten har lagringskapacitet. 

### <a name="module-communication"></a>Modulen kommunikation

Edge Hub underlättar modulen till modulen kommunikationen. Med Edge Hub som en asynkron meddelandekö behåller moduler som är oberoende av varandra. Moduler behöver bara ange indata som de godkänner meddelanden och utdata som de skriva meddelanden. En för lösningsutvecklare sedan häftar samman dessa indata och utdata tillsammans så att modulerna som bearbetar data i ordningen som är specifika för lösningen. 

![Edge Hub underlättar modulen till modulen kommunikationen](./media/iot-edge-runtime/ModuleEndpoints.png)

Om du vill skicka data till Edge hub, anropar metoden SendEventAsync i en modul. Det första argumentet anger på vilka utdata att skicka meddelandet. Följande pseudocode skickar ett meddelande på output1:

   ```csharp
   ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync(“output1”, message); 
   ```

Registrera ett återanrop som bearbetar inkommande meddelanden på en specifik indata för att ta emot ett meddelande. Följande pseudocode registrerar funktionen messageProcessor som ska användas för att bearbeta alla meddelanden som tas emot på indata1:

   ```csharp
   await client.SetEventHandlerAsync(“input1”, messageProcessor, userContext);
   ```

Lösningsutvecklaren är ansvarig för att ange reglerna som bestämmer hur Edge hub skickar meddelanden mellan moduler. Routningsregler definieras i molnet och flyttas fram till Edge hub i dess enhetstvilling. Samma syntax för IoT Hub vägar används för att definiera rutter mellan moduler i Azure IoT Edge. 

<!--- For more info on how to declare routes between modules, see []. --->   

![Rutter mellan moduler](./media/iot-edge-runtime/ModuleEndpointsWithRoutes.png)

## <a name="iot-edge-agent"></a>IoT Edge-agenten

IoT Edge-agenten är den modul som utgör Azure IoT Edge-körningen. Den är ansvarig för kontrollanten moduler, se till att de fortsätter att köras och rapporterar status för moduler tillbaka till IoT Hub. Precis som andra moduler använder dess modultvilling i Edge-agenten för att spara konfigurationsinformationen. 

Den [IoT Edge security daemon](iot-edge-security-manager.md) startar Edge-agenten vid start av enheten. Agenten hämtar dess modultvilling från IoT Hub och inspekterar distribution manifestet. Manifestet distribution är en JSON-fil som deklarerar de moduler som startas. 

Varje objekt i manifestet distribution innehåller specifik information om en modul och används av Edge-agenten för att styra modulens livscykel. Vissa av egenskaperna mer intressant är: 

* **Settings.Image** – den behållaravbildning som Edge-agenten använder för att starta modulen. Edge-agenten måste konfigureras med autentiseringsuppgifterna för behållarregistret om avbildningen skyddas av ett lösenord. Autentiseringsuppgifter för container registry kan konfigureras via en fjärranslutning med hjälp av manifestet distribution eller på själva Edge-enheten genom att uppdatera den `config.yaml` filen i mappen IoT Edge-programmet.
* **settings.createOptions** – en sträng som skickas direkt till Docker-daemon när du startar en modul behållare. Om du lägger till Docker-alternativ i den här egenskapen får för avancerade alternativ som port vidarebefordran eller montering av volymer i en modul behållare.  
* **status för** – tillståndet som Edge-agenten placerar modulen. Det här värdet anges vanligtvis till *kör* de flesta vill Edge-agenten att starta alla moduler direkt på enheten. Du kan dock ange det ursprungliga tillståndet för en modul för att stoppas och vänta tills ett senare tillfälle som talar om att Edge-agent ska starta en modul. Edge-agenten rapporterar status för varje modul tillbaka till molnet i rapporterade egenskaper. Någon skillnad mellan önskad egenskap och rapporterad egenskap är ett tecken på en skadad enhet. Den stödda statusen är:
   * Laddas ned
   * Körs
   * Skadad
   * Misslyckad
   * Stoppad
* **restartPolicy** – hur Edge-agenten startar om en modul. Möjliga värden omfattar:
   * Edge-agenten startar aldrig – aldrig om modulen.
   * onFailure - om modulen kraschar, Edge-agenten startar om den. Om modulen får en ren avstängning, Edge-agenten inte starta om den.
   * Ohälsosamma - om modulen kraschar eller som kan vara felaktiga, Edge-agenten startar om den.
   * Alltid - om modulen kraschar, som anses vara defekt eller stängs av på något sätt, Edge-agenten startar om den. 

IoT Edge-agenten skickar körningssvar till IoT Hub. Här är en lista över möjliga svar:
  * 200 - OK
  * 400 - distributionskonfigurationen är skadad eller ogiltig.
  * 417 - enheten har inte en distributionskonfiguration anger.
  * 412 - schemaversion i distributionskonfigurationen är ogiltig.
  * 406 - edge-enheten är frånkopplad eller skickar inga statusrapporter.
  * 500 – ett fel uppstod i edge-körningen.

### <a name="security"></a>Säkerhet

IoT Edge-agenten spelar en viktig roll i säkerheten för en IoT Edge-enhet. Till exempel utförs åtgärder som att verifiera en moduls avbildning innan du startar den. 

Mer information om Azure IoT Edge security framework Läs mer om den [IoT Edge-säkerhetshanteraren](iot-edge-security-manager.md)

## <a name="next-steps"></a>Nästa steg

[Förstå Azure IoT Edge-certifikat](iot-edge-certs.md)
