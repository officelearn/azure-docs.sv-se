---
title: "Förstå Azure IoT kant runtime | Microsoft Docs"
description: "Lär dig mer om Azure IoT kant-runtime och hur kan dina enheter"
services: iot-edge
keywords: 
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 02/15/2018
ms.topic: article
ms.service: iot-edge
ms.openlocfilehash: 8bd725e2201cb08853f4fb63d156b6359427663b
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2018
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture---preview"></a>Förstå Azure IoT kant-runtime och dess arkitektur - förhandsgranskning

IoT-Edge runtime är en uppsättning program som måste installeras på en enhet att anses vara en IoT-enhet. Gemensamt kallade komponenter av körningsmiljön IoT kant aktivera IoT kant enheter tar emot koden för att köra i utkanten och meddela resultaten. 

IoT-Edge runtime utför följande funktioner på enheter som IoT:

* Installerar och uppdaterar arbetsbelastningar på enheten.
* Underhåller Azure IoT Edge-säkerhetsstandarder på enheten.
* Garanterar att [IoT kant moduler][lnk-moduler] alltid körs.
* Rapporterar modulens hälsa till molnet för fjärrövervakning.
* Underlättar kommunikationen mellan nedströms lövenheter och IoT Edge-enheten.
* Underlättar kommunikationen mellan moduler på IoT Edge-enheten.
* Underlättar kommunikationen mellan IoT Edge-enheten och molnet.

![IoT-Edge runtime kommunicerar insikter och modulen hälsa till IoT-hubb][1]

Ansvaret för IoT kant runtime är indelade i två kategorier: modulen Hantering och kommunikation. Dessa två roller utförs av två komponenter som utgör IoT kant-körningsmiljön. IoT Edge-hubben är ansvarig för kommunikation, medan IoT kant agenten hanterar distribuera och övervaka modulerna. 

Både Edge-agenten och kant-hubben är moduler, precis som en annan modul som körs på en IoT-enhet. Mer information om hur moduler fungerar finns [lnk-moduler]. 

## <a name="iot-edge-hub"></a>Gräns för IoT-hubb

Edge-hubben är en av två moduler som utgör Azure IoT kant-körningsmiljön. Det fungerar som en lokal proxyserver för IoT-hubb genom att exponera samma protokollslutpunkterna som IoT-hubb. Den här konsekvenskontroll innebär att klienter (om enheter eller moduler) kan ansluta till IoT kant runtime precis som de skulle för IoT-hubb. 

>[!NOTE]
> Under förhandsversion stöd Edge hubb endast för klienter som ansluter via MQTT.

Edge-hubben är inte en fullständig version av IoT-hubb som körs lokalt. Det finns vissa saker som Edge hubben tyst delegerar till IoT-hubb. Edge hubb vidarebefordrar autentiseringsbegäranden till IoT-hubb när en enhet försöker ansluta. Efter den första anslutningen har upprättats cachelagras säkerhetsinformation lokalt Edge hubb. Efterföljande anslutningar från den enheten tillåts utan att autentisera till molnet. 

>[!NOTE]
> Under förhandsversion ansluten körningsmiljön varje gång den försöker autentisera en enhet.

För att minska bandbredden som gräns för IoT-lösningen använder, Edge hubben optimerar hur många anslutningar görs till molnet. Edge hubb tar logiska anslutningar från klienter som moduler eller löv enheter och kombineras för en enda fysisk anslutning till molnet. Information om den här processen är transparent för resten av lösningen. Klienter tror att de har sina egna anslutningar till molnet, även om de alla skickas via samma anslutning. 

![Edge-hubb som fungerar som en gateway mellan flera fysiska enheter och molnet][2]

Edge-hubb kan avgöra om den är ansluten till IoT-hubb. Om anslutningen bryts, sparar Edge hubb meddelanden eller dubbla uppdateringar lokalt. När en anslutning har återupprättat synkroniserar alla data. Den plats som används för den här tillfälliga cache bestäms av en egenskap för hubben Edge modulen dubbla. Storleken på cacheminnet begränsas inte och kommer att växa så länge enheten har lagringskapacitet. 

>[!NOTE]
>Lägga till kontroll över ytterligare cachelagring parametrar läggs till produkten innan den försätts allmän tillgänglighet.

### <a name="module-communication"></a>Modulen kommunikation

Edge hubb underlättar modulen modulen kommunikationen. Med hjälp av Edge-hubb som förhandlare meddelandet håller moduler oberoende av varandra. Moduler behöver bara ange indata som de accepterar meddelanden och utdata som de skriva meddelanden. En för lösningsutvecklare sedan häftar samman dessa indata och utdata tillsammans så att modulerna som bearbetar data i ordningen som är specifika för lösningen. 

![Edge hubb underlättar kommunikationen för modul-modul][3]

Om du vill skicka data till hubben Edge anropar metoden SendEventAsync i en modul. Det första argumentet anger på vilka utdata att skicka meddelandet. Följande pseudocode skickar ett meddelande på output1:

   ```csharp
   DeviceClient client = new DeviceClient.CreateFromConnectionString(moduleConnectionString, settings); 
   await client.OpenAsync(); 
   await client.SendEventAsync(“output1”, message); 
   ```

Registrera ett återanrop som bearbetar inkommande meddelanden på specifika indata för att ta emot ett meddelande. Följande pseudocode registrerar funktionen messageProcessor som ska användas för bearbetning av alla meddelanden som tas emot på input1:

   ```csharp
   await client.SetEventHandlerAsync(“input1”, messageProcessor, userContext);
   ```

Lösningsutvecklaren ansvarar för att ange regler som bestämmer hur Edge hubb skickar meddelanden mellan moduler. Regler för routning definieras i molnet och flyttas fram till Edge hubb i sin enhet dubbla. Samma syntax för IoT-hubb vägar används för att definiera vägar mellan moduler i Azure IoT kant. 

<!--- For more info on how to declare routes between modules, see []. --->   

![Vägar mellan moduler][4]

## <a name="iot-edge-agent"></a>IoT-Edge-agent

IoT kant-agenten är den modul som utgör Azure IoT kant-körningsmiljön. Den är ansvarig för en instans skapades av moduler, säkerställer att de fortsätter att köras och rapportera status för modulerna som tillbaka till IoT-hubb. Precis som alla andra moduler använder Edge-agenten dess modulen dubbla för att spara konfigurationsinformationen. 

Om du vill starta körningen av Edge-agent, kör du startkommandot azure iot-edge-runtime-ctl.py. Agenten hämtar dess modulen dubbla från IoT-hubb och kontrollerar moduler ordlistan. Moduler ordlistan är en samling av moduler som behöver startas. 

Varje objekt i moduler ordlistan innehåller specifik information om en modul och används av Edge-agenten för att styra modulens livscykel. Vissa av egenskaperna mer intressant är: 

* **Settings.Image** – behållaren avbildningen Edge-agenten använder för att starta modulen. Edge-agent måste konfigureras med autentiseringsuppgifter för behållaren registernyckeln om bilden skyddas av ett lösenord. Om du vill konfigurera Edge-agenten använder du följande kommando: `azure-iot-edge-runtime-ctl.py –configure`
* **settings.createOptions** – en sträng som skickas direkt till Docker-daemon när du startar en modul behållare. Lägger till Docker-alternativ i den här egenskapen kan avancerade alternativ som port vidarebefordran eller montering av volymer i en modul behållare.  
* **status för** – tillstånd där agenten Edge placerar modulen. Det här värdet anges vanligtvis *kör* som de flesta vill Edge-agent för att starta alla moduler direkt på enheten. Du kan dock ange inledningsvis i en modul som ska stoppas och vänta en framtida tid för att ange Edge-agenten för att starta en modul. Edge agenten rapporterar status för varje modul tillbaka till molnet i rapporterade egenskaper. Skillnad mellan önskade egenskaperna och rapporterade är en indikator eller fel enhet. Stöds statusar:
   * Hämtar
   * Körs
   * Skadad
   * Misslyckad
   * Stoppad
* **restartPolicy** – hur Edge agenten startar om en modul. Möjliga värden omfattar:
   * Edge-agenten startar aldrig – aldrig om modulen.
   * onFailure - om modulen kraschar Edge agenten startar om den. Om modulen avslutas korrekt Edge-agenten inte starta om den.
   * Ohälsosamma - om modulen kraschar eller bedöms ohälsosamt Edge agenten startar om den.
   * Alltid - om modulen kraschar, anses feltillstånd eller stängs av på något sätt, startar Edge agenten den. 

IoT-Edge agenten skickar runtime svar till IoT-hubb. Här är en lista över möjliga svar:
  * 200 - OK
  * 400 - distributionskonfigurationen är skadad eller ogiltig.
  * 417 - enheten har inte en distributionskonfiguration anger.
  * 412 - schemaversionen i distributionskonfigurationen är ogiltig.
  * 406 - gränsenheten är offline eller inte skicka statusrapporter.
  * 500 - ett fel inträffade i edge-körningsmiljön.

### <a name="security"></a>Säkerhet

IoT-Edge agenten spelar en viktig roll i säkerheten för en IoT-enhet. Till exempel utför den åtgärder som verifierar en modul bilden innan du startar den. Dessa funktioner läggs vid allmän tillgänglighet för V2-funktioner. 

<!-- For more information about the Azure IoT Edge security framework, see []. -->

## <a name="next-steps"></a>Nästa steg

- [Förstå Azure IoT kant moduler][lnk-moduler]

<!-- Images -->
[1]: ./media/iot-edge-runtime/Pipeline.png
[2]: ./media/iot-edge-runtime/Gateway.png
[3]: ./media/iot-edge-runtime/ModuleEndpoints.png
[4]: ./media/iot-edge-runtime/ModuleEndpointsWithRoutes.png

<!-- Links -->
[lnk-moduler]: iot-edge-modules.md
