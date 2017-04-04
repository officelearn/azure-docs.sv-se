
# <a name="azure-and-internet-of-things"></a>Azure och Sakernas Internet

Välkommen till Microsoft Azure och Sakernas Internet (Internet of Things, IoT). Den här artikeln introducerar en lösningsarkitektur för IoT som beskriver de vanligast förekommande egenskaperna för en IoT-lösning som du kan distribuera med hjälp av Azure-tjänster. IoT-lösningar kräver säker, dubbelriktad kommunikation mellan enheter, potentiellt miljontals, samt en serverdelslösning. En serverdelslösning kan exempelvis använda automatiserad, förutsägande analys för att få fram insikter från din enhet-till-molnet händelseström.

Azure IoT Hub är en viktig byggsten vid implementering av den här IoT-lösningsarkitekturen med hjälp av Azure-tjänster. IoT Suite tillhandahåller fullständiga, slutpunkt-till-slutpunkts-implementeringar av den här arkitekturen för specifika IoT-scenarier. Några exempel:

* *Fjärrövervaknings*-lösningen låter dig övervaka status för enheter som till exempel varuautomater.
* *Förutsägbart underhålls*-lösningen hjälper dig att förutse underhållsbehov av enheter som till exempel pumpar vid avlägsna pumpstationer för att kunna undvika oplanerade avbrott.

## <a name="iot-solution-architecture"></a>IoT-lösningsarkitektur

I följande diagram visas en typisk IoT-lösningsarkitektur. Diagrammet innehåller inte namnen på några specifika Azure-tjänster, utan beskriver snarare nyckelelementen för en generisk IoT-lösningsarkitektur. I den här lösningen samlar IoT-enheter in data som de sedan skickar till en molngateway. Molngatewayen tillhandahåller data för bearbetning av andra serverdelstjänster som levererar data till andra verksamhetsspecifika program eller till mänskliga operatörer via en instrumentpanel eller annan presentationsenhet.

![IoT-lösningsarkitektur][img-solution-architecture]

> [!NOTE]
> En detaljerad beskrivning av IoT-arkitekturen finns i [Microsoft Azure IoT Reference Architecture][lnk-refarch] (Referensarkitektur för Microsoft Azure IoT).

### <a name="device-connectivity"></a>Enhetsanslutning

I den här IoT-lösningsarkitekturen skickar enheter telemetri, till exempel sensoravläsningar från en pumpstation, till en molnslutpunkt för lagring och bearbetning. I ett scenario med förutsägande underhåll kan lösningen för serverdelen använda sig av strömmen med sensordata för att fastställa när en specifik pump kräver underhåll. Enheter kan också ta emot och svara på meddelanden från moln till enhet genom att läsa meddelanden från en molnslutpunkt. Exempelvis kan serverdelen av lösningen i scenariot med förutsägande underhåll skicka meddelanden till andra pumpar på pumpstationen för att börja dirigera om flöden strax innan underhållet ska påbörjas. Den här proceduren säkerställer att underhållsteknikern kan sätta igång så fort han anländer.

En av de största utmaningarna med IoT-projekt är att på ett tillförlitligt och säkert sätt kunna ansluta enheter till lösningens serverdel. IoT-enheter skiljer sig från andra klienter, till exempel webbläsare och mobilappar. IoT-enheter:

* Är ofta inbyggda system utan en mänsklig operatör.
* Kan distribueras på avlägsna platser, där fysisk åtkomst är dyr.
* Kan i vissa fall enbart nås via lösningens serverdel. Det finns inget annat sätt att interagera med enheten.
* Kan ha begränsade ström- och bearbetningsresurser.
* Kan ha oregelbunden, långsam eller dyr nätverksanslutning.
* Kan behöva använda patentskyddade, anpassade eller branschspecifika programprotokoll.
* Kan skapas med en stor mängd populära maskinvaru- och programvaruplattformar.

Förutom kraven ovan behöver alla IoT-lösningar också erbjuda skalbarhet, säkerhet och tillförlitlighet. Den resulterande uppsättningen anslutningskrav är svår och tidskrävande att implementera med traditionella teknologier, till exempel webbehållare och asynkrona meddelandeköer. Azure IoT Hub och enhets-SDK:erna för Azure IoT gör det enklare att implementera lösningar som uppfyller dessa krav.

En enhet kan kommunicera direkt med en slutpunkt för en molngateway, eller om enheten inte kan använda sig av något av de kommunikationsprotokoll som molgatewayen stöder så kan den ansluta via en mellanliggande gateway. Till exempel kan [Azure IoT-protokollgatewayen][lnk-protocol-gateway] utföra protokollöversättningar om enheter inte kan använda sig av något av de protokoll som stöds av IoT Hub.

### <a name="data-processing-and-analytics"></a>Databearbetning och analys

Det är i IoT-lösningens serverdel i molnet som den största delen av databearbetningen sker, speciellt vad gäller filtrering och sammanställning av telemetri och vidarebefordran till andra tjänster. Serverdelen för IoT-lösningen:

* Tar emot stora mängder telemetri från dina enheter och bestämmer hur telemetridata bearbetas och lagras. 
* Kan låta dig skicka kommandon från molnet till specifika enheter.
* Möjliggör enhetsregistrering, vilket låter dig etablera enheter och styra vilka enheter som får ansluta till din infrastruktur.
* Låter dig kontrollera status för enheter och övervaka deras verksamhet.

I det förutsägande underhållsscenariot, lagrar lösningens serverdel historisk telemetridata. Lösningens serverdel kan använda sig av dessa data för att identifiera mönster som indikerar att det är dags för underhåll av en viss pump.

IoT-lösningar kan inkludera automatiska feedback-slingor. En analysmodul i lösningens serverdel kan exempelvis utläsa från telemetridata att temperaturen för en specifik enhet är över normal driftsnivå. Lösningen kan sedan skicka ett kommando till enheten och instruera den att vidta åtgärder.

### <a name="presentation-and-business-connectivity"></a>Presentation och företagsanslutningar

Slutanvändarna kan interagera med IoT-lösningen och enheterna via ett presentations- och företagsanslutningsskikt. I det här skiktet kan användare se och analysera data som samlats in från deras enheter. Vyerna kan bestå av instrumentpaneler eller BI-rapporter som kan visa både historiska data eller nästan realtidsdata. En operatör kan exempelvis kontrollera statusen för en viss pumpstation och se alla varningar som har utlösts i systemet. Här kan även IoT-lösningens serverdel integreras med befintliga branschspecifika program för att knyta samman med företagets verksamhetsprocesser eller arbetsflöden. Förutsägande underhåll kan till exempel integreras med ett system för schemaläggning som bokar in en ingenjör att besöka en pumpstation när en pump är i behov av underhåll.

![Instrumentpanel för IoT-lösning][img-dashboard]

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
