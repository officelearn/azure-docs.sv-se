# Azure och Sakernas Internet

Välkommen till Microsoft Azure och Sakernas Internet (Internet of Things, IoT). Den här artikeln introducerar en lösningsarkitektur för IoT som beskriver de gemensamma egenskaperna för en IoT-lösning som du kan distribuera med Azure-tjänster. IoT-lösningar kräver säker, dubbelriktad kommunikation mellan upp till miljoner enheter. Därmed krävs en serverdelslösning som exempelvis använder sig av automatiserad förutsägelseanalys för att få insikter från din händelseström från enhet till moln.

Azure IoT Hub är en viktig byggsten vid implementering av den här IoT-lösningsarkitekturen med hjälp av Azure-tjänster. IoT Suite ger en fullständig, slutpunkt-till-slutpunkt-implementation av den här arkitekturen för specifika IoT-scenarier. *Fjärrövervaknings*lösningen kan exempelvis låta dig övervaka status på enheter som till exempel försäljningsautomater, och *förutsägande underhåll* hjälper dig att förutse underhållsbehov för enheter som till exempel pumpar på avlägsna pumpstationer och därmed undvika oplanerade avbrott.

## IoT-lösningsarkitektur

I följande diagram visas en typisk IoT-lösningsarkitektur. Observera att den inte innehåller namnen på eventuella specifika Azure-tjänster, men beskriver nyckelelement i en allmän IoT-lösningsarkitektur. I den här lösningen samlar IoT-enheter in data som de sedan skickar till en molngateway. Molngatewayen tillhandahåller data för bearbetning av andra serverdelstjänster som levererar data till andra verksamhetsspecifika program eller till mänskliga operatörer via en instrumentpanel eller annan presentationsenhet.

![IoT-lösningsarkitektur][img-solution-architecture]

> [AZURE.NOTE] En detaljerad beskrivning av IoT-arkitekturen finns i [Referensarkitektur för Microsoft Azure IoT][lnk-refarch].

### Enhetsanslutning

I den här IoT-lösningsarkitekturen skickar enheter telemetri, till exempel sensoravläsningar från en pumpstation, till en molnslutpunkt för lagring och bearbetning. I ett scenario med förutsägande underhåll kan serverdelen använda sig av strömmen med sensordata för att fastställa när en specifik pump kräver underhåll. Enheter kan också ta emot och svara på kommandon från moln till enhet genom att läsa meddelanden från en molnslutpunkt. I det förutsägande underhållsscenariot kan lösningens serverdel exempelvis skicka kommandon till andra pumpar vid pumpstationen för att börja styra om flöden precis innan underhållsstart för att säkerställa att underhållsingenjören kan komma igång så fort hen kommer dit.

En av de största utmaningarna för IoT-projekt är hur man säkert och tillförlitligt ansluter enheterna till lösningens serverdel så att de kan skicka telemetri och ta emot kommandon. IoT-enheter skiljer sig från andra klienter, till exempel webbläsare och mobilappar. IoT-enheter:

- Är ofta inbyggda system utan en mänsklig operatör.
- Kan distribueras på avlägsna platser, där fysisk åtkomst är väldigt dyr.
- Kan i vissa fall enbart nås via lösningens serverdel. Det finns inget annat sätt att interagera med enheten.
- Kan ha begränsade ström- och bearbetningsresurser.
- Kan ha oregelbunden, långsam eller dyr nätverksanslutning.
- Kan behöva använda patentskyddade, anpassade eller branschspecifika programprotokoll.
- Kan skapas med en stor mängd populära maskinvaru- och programvaruplattformar.

Förutom kraven ovan behöver alla IoT-lösningar också erbjuda skalbarhet, säkerhet och tillförlitlighet. Den resulterande uppsättningen anslutningskrav är svår och tidskrävande att implementera med traditionella teknologier, till exempel webbehållare och asynkrona meddelandeköer. Azure IoT Hub och SDK för Azure IoT-enheter gör det lättare att implementera lösningar som uppfyller de här kraven.

En enhet kan kommunicera direkt med en slutpunkt för en molngateway, eller om enheten inte kan använda sig av något av de kommunikationsprotokoll som molgatewayen stöder så kan den ansluta via en mellanliggande gateway, exempelvis [IoT Hub-protokollgatewayen][Ink-protocol-gateway], som kan utföra protokollöversättning. Exempelvis från CIP (Common Industrial Protocol) till AMQPS.

### Databearbetning och analys

Serverdelen för en IoT-lösning är var den största delen av databearbetningen sker i molnet, speciellt vad gäller filtrering och aggregering av telemetri och vidarebefordring till andra tjänster. Serverdelen för IoT-lösningen:

- Tar emot stora mängder telemetri från dina enheter och bestämmer hur telemetridata bearbetas och lagras. 
- Kan låta dig skicka kommandon från molnet till specifika enheter.
- Möjliggör enhetsregistrering, vilket låter dig etablera enheter och styra vilka enheter som får ansluta till din infrastruktur.
- Låter dig kontrollera status för enheter och övervaka deras verksamhet.

I scenariot med förutsägande underhåll lagrar lösningens serverdel historiska telemetridata som används för att identifiera mönster och analysera inkommande telemetri för att upptäcka mönster som tyder på att en specifik pump är i behov av underhåll.

IoT-lösningar kan inkludera automatiska feedback-slingor. En analysmodul i serverdelen kan exempelvis identifiera från telemetrin att temperaturen för en specifik enhet är över normal driftsnivå och skicka ett kommando till enheten att den ska vidta åtgärder.

### Presentation och företagsanslutningar

Lagret med presentation och företagsanslutningar låter slutanvändare interagera med IoT-lösningen och enheterna. Det låter användare se och analysera data som samlats in från deras enheter. Vyerna kan bestå av instrumentpaneler eller BI-rapporter som kan visa både historiska data eller nästan realtidsdata. En operatör kan exempelvis kontrollera statusen för en viss pumpstation och se alla varningar som har utlösts i systemet. Lagret tillåter också integrering av IoT-lösningens serverdel med befintliga branschspecifika program för att knyta samman med företagets verksamhetsprocesser eller arbetsflöden. Den förutsägande underhållslösningen kan till exempel integreras med ett system för schemaläggning som bokar in en ingenjör att besöka en pumpstation när lösningen identifierar en pump i behov av underhåll.

![Instrumentpanel för IoT-lösning][img-dashboard]

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[Ink-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf



<!--HONumber=Jun16_HO2-->


