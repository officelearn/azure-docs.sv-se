
# <a name="azure-and-internet-of-things"></a>Azure och Sakernas Internet

Välkommen till Microsoft Azure och Sakernas Internet (Internet of Things, IoT). Den här artikeln introducerar de vanligast förekommande egenskaperna för en IoT-lösning som du kan distribuera med hjälp av Azure-tjänster. IoT-lösningar kräver säker, dubbelriktad kommunikation mellan stora antal enheter och en serverdelslösning. Serverdelslösningen kan använda automatiserad, förutsägande analys för att få fram insikter från din enhet-till-molnet händelseström.

[Azure IoT Hub][lnk-iot-hub] är en viktig byggsten för alla IoT-lösningar som använder Azure-tjänster. IoT Hub är en helt hanterad tjänst som möjliggör tillförlitlig och säker dubbelriktad kommunikation mellan flera miljoner IoT-enheter och som tillhandahåller serverdelen för lösningar av den här typen. 

[Azure IoT Suite][lnk-iot-suite] tillhandahåller fullständiga, slutpunkt-till-slutpunkts-implementeringar av den här arkitekturen för specifika IoT-scenarier. Exempel:

* *Fjärrövervaknings*-lösningen låter dig övervaka status för enheter som till exempel varuautomater.
* *Förutsägbart underhålls*-lösningen hjälper dig att förutse underhållsbehov av enheter som till exempel pumpar vid avlägsna pumpstationer för att kunna undvika oplanerade avbrott.
* Lösningen *connected factory* hjälper dig att ansluta och övervaka dina industriella enheter.

## <a name="iot-solution-architecture"></a>IoT-lösningsarkitektur

I följande diagram visas en typisk IoT-lösningsarkitektur. Diagrammet innehåller inte namnen på några specifika Azure-tjänster, utan beskriver snarare nyckelelementen för en generisk IoT-lösningsarkitektur. I den här lösningen samlar IoT-enheter in data som de sedan skickar till en molngateway. Molngatewayen tillhandahåller data för bearbetning av andra serverdeltjänster. Lösningens serverdel levererar data till företagsprogram eller till mänskliga operatörer via en instrumentpanel eller rapport.

![IoT-lösningsarkitektur][img-solution-architecture]

> [!NOTE]
> En detaljerad beskrivning av IoT-arkitekturen finns i [Microsoft Azure IoT Reference Architecture][lnk-refarch] (Referensarkitektur för Microsoft Azure IoT).

### <a name="device-connectivity"></a>Enhetsanslutning

I den här IoT-lösningen skickar enheter telemetri, till exempel sensoravläsningar från en pumpstation, till en molnslutpunkt för lagring och bearbetning. I ett scenario med förutsägande underhåll kan lösningen för serverdelen använda sig av strömmen med sensordata för att fastställa när en specifik pump kräver underhåll. Enheter kan också ta emot och svara på meddelanden från moln till enhet genom att läsa meddelanden från en molnslutpunkt. Exempelvis kan serverdelen av lösningen i scenariot med förutsägande underhåll skicka meddelanden till andra pumpar på pumpstationen för att börja dirigera om flöden strax innan underhållet ska påbörjas. Den här proceduren säkerställer att underhållsteknikern kan sätta igång med att lösa problemet direkt.

En av de största utmaningarna med IoT-projekt är att på ett tillförlitligt och säkert sätt kunna ansluta enheter till lösningens serverdel. IoT-enheter skiljer sig från andra klienter, till exempel webbläsare och mobilappar. IoT-enheter:

* Är ofta inbyggda system utan en mänsklig operatör.
* Kan distribueras på avlägsna platser, där fysisk åtkomst är dyr.
* Kan i vissa fall enbart nås via lösningens serverdel. Det finns inget annat sätt att interagera med enheten.
* Kan ha begränsade ström- och bearbetningsresurser.
* Kan ha oregelbunden, långsam eller dyr nätverksanslutning.
* Kan behöva använda patentskyddade, anpassade eller branschspecifika programprotokoll.
* Kan skapas med en stor mängd populära maskinvaru- och programvaruplattformar.

Förutom kraven ovan behöver alla IoT-lösningar också erbjuda skalbarhet, säkerhet och tillförlitlighet. Den resulterande uppsättningen anslutningskrav är svår och tidskrävande att implementera med traditionella teknologier, till exempel webbehållare och asynkrona meddelandeköer. Azure IoT Hub och enhets-SDK:erna för Azure IoT gör det enklare att implementera lösningar som uppfyller dessa krav.

En enhet kan kommunicera direkt med en slutpunkt för en molngateway. Om enheten inte kan använda sig av något av de kommunikationsprotokoll som molgatewayen stöder så kan den ansluta via en mellanliggande gateway. Till exempel kan [Azure IoT-protokollgatewayen][lnk-protocol-gateway] utföra protokollöversättningar om enheter inte kan använda sig av något av de protokoll som stöds av IoT Hub.

### <a name="data-processing-and-analytics"></a>Databearbetning och analys

I molnet sker det mesta av databearbetningen i en IoT-lösning. Serverdelen för IoT-lösningen:

* Tar emot stora mängder telemetri från dina enheter och bestämmer hur telemetridata bearbetas och lagras. 
* Kan låta dig skicka kommandon från molnet till specifika enheter.
* Möjliggör enhetsregistrering, vilket låter dig etablera enheter och styra vilka enheter som kan ansluta till din infrastruktur.
* Låter dig kontrollera status för enheter och övervaka deras verksamhet.

I det förutsägande underhållsscenariot, lagrar lösningens serverdel historisk telemetridata. Lösningens serverdel kan använda sig av dessa data för att identifiera mönster som indikerar att det är dags för underhåll av en viss pump.

IoT-lösningar kan inkludera automatiska feedback-slingor. En analysmodul i lösningens serverdel kan exempelvis utläsa från telemetridata att temperaturen för en specifik enhet är över normal driftsnivå. Lösningen kan sedan skicka ett kommando till enheten och instruera den att vidta åtgärder.

### <a name="presentation-and-business-connectivity"></a>Presentation och företagsanslutningar

Slutanvändarna kan interagera med IoT-lösningen och enheterna via ett presentations- och företagsanslutningsskikt. I det här skiktet kan användare se och analysera data som samlats in från deras enheter. Vyerna kan bestå av instrumentpaneler eller rapporter som kan visa både historiska data eller nästan realtidsdata. En operatör kan exempelvis kontrollera statusen för en viss pumpstation och se alla varningar som har utlösts i systemet. Här kan även IoT-lösningens serverdel integreras med befintliga branschspecifika program för att knyta samman med företagets verksamhetsprocesser eller arbetsflöden. Exempelvis kan den förebyggande underhållslösningen integrera med ett schemaläggningssystem. När lösningen identifierar att en pump är i behov av underhåll bokar systemet för schemaläggning in en ingenjör som ska besöka en pumpstation.

![Instrumentpanel för IoT-lösning][img-dashboard]

[img-solution-architecture]: ./media/iot-azure-and-iot/iot-reference-architecture.png
[img-dashboard]: ./media/iot-azure-and-iot/iot-suite.png

[lnk-iot-hub]: ../articles/iot-hub/iot-hub-what-is-iot-hub.md
[lnk-iot-suite]: ../articles/iot-suite/iot-suite-overview.md
[lnk-machinelearning]: http://azure.microsoft.com/documentation/services/machine-learning/
[Azure IoT Suite]: http://azure.microsoft.com/solutions/iot
[lnk-protocol-gateway]:  ../articles/iot-hub/iot-hub-protocol-gateway.md
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
