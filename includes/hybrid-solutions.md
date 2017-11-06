# <a name="azure-service-bus"></a>Azure Service Bus
Oavsett om en app eller tjänst körs i molnet eller lokalt, måste den ofta samverka med andra program eller tjänster. För att ge ett fritt och praktiskt sätt att göra detta, erbjuder Azure Service Bus. I den här artikeln tar vi en titt på denna teknik och beskriver vad den är och varför du kanske vill använda den.

## <a name="service-bus-fundamentals"></a>Grunderna i Service Bus
Olika situationer kräver olika kommunikationsstilar. Ibland är den bästa lösningen att låta appar skicka och ta emot meddelanden via en enkel kö. I vissa situationer räcker det inte med en vanlig kö, utan det är bättre att använda en kö med en mekanism av typen publicera och prenumerera. Och i vissa fall kan allt som behövs är en anslutning mellan program & #151, köer är överflödiga. Service Bus innehåller alla tre alternativ att låta dina program interagera på flera olika sätt.

Service Bus är en molntjänst med flera klienter, vilket innebär att tjänsten delas av flera användare. Varje användare, till exempel en programutvecklare, skapar ett *namnområde*, och sedan definierar utvecklaren de kommunikationsmekanismer som han eller hon vill använda inom det här namnområdet. [Bild 1](#Fig1) visar hur det ser ut.

<a name="Fig1"></a>![Diagram över Azure Service Bus][svc-bus]

**Bild 1: Service Bus erbjuder en tjänst för flera innehavare för att ansluta program via molnet.**

Du kan använda en eller flera instanser av fyra olika kommunikationsmekanismer inom namnområdet. Dessa ansluter till appar på olika sätt. Alternativen är:

* *Köer*, som tillåter en enkelriktad kommunikation. Varje kö fungerar som en mellanhand (kallas ibland för en *koordinator*) som sparar skickade meddelanden tills de tas emot. Varje meddelande tas emot av en enda mottagare.
* *Ämnen*, som ger enkelriktad kommunikation med hjälp av *prenumerationer*. Ett ämne kan ha flera prenumerationer. Precis som med köerna fungerar ämnena som en koordinator (broker), men varje prenumeration kan sedan använda ett filter för att endast ta emot meddelanden som uppfyller specifika villkor.
* *Reläer* (relays), som ger dubbelriktad kommunikation. Till skillnad från köer och ämne lagrar inte ett relä meddelanden som redan startats. Det är alltså inte en koordinator. I stället skickar reläet bara meddelandena vidare till målprogrammet.
* *Händelsehubbar* ger händelse- och telemetritillträde till molnet i massiv skala, med kort svarstid och hög tillförlitlighet.

När du skapar en kö, ett ämne, ett relä eller en händelsehubb, ger du dem ett namn. Det här namnet, tillsammans med det namn som du gav namnområdet, skapar en unik identifierare för objektet. Appen kan överföra detta namn till Service Bus och sedan använda den kön, det ämnet, det reläet eller den händelsehubben i sin kommunikation med varandra. 

Windows-program kan använda Windows Communication Foundation (WCF) för att använda något av dessa objekt. För köer, ämnen och händelsehubbar kan Windows-program även använda API:er för Service Bus-definierade meddelandefunktioner. Om du vill att det ska vara enklare att använda objekten från andra program som inte är från Windows, tillhandahåller Microsoft SDK:er för Java, Node.js och andra programmeringsspråk. Du kan även få åtkomst till köer, ämnen och händelsehubbar med hjälp av REST-API:er via HTTP. 

Det är viktigt att förstå att även om själva Service Bus körs i molnet (det vill säga i datacenter för Microsoft Azure) så kan apparna som använder den köras var som helst. Du kan använda Service Bus för att ansluta appar som körs på Azure, till exempel, eller program som körs i ditt eget datacenter. Du kan också använda Service Bus för att ansluta en app som körs på Azure eller på en annan molnplattform med ett lokalt program, eller med surfplattor och smartmobiler. Det går till och med att ansluta hushållsapparater, sensorer och andra enheter till ett centralt program eller till varandra. Service Bus är en allmän kommunikationsmekanism i molnet och den är tillgänglig från nästan var som helst. Hur du använder den beror på vad dina appar behöver göra.

## <a name="queues"></a>Köer
Anta att du bestämmer dig för att ansluta två appar med hjälp av en Service Bus-kö. [Bild 2](#Fig2) visar den här situationen.

<a name="Fig2"></a>![Diagram över Service Bus-köer][queues]

**Bild 2: Service Bus-köer erbjuder enkelriktade asynkrona köer.**

Processen är enkel: En avsändare skickar ett meddelande till en Service Bus-kö och en mottagare hämtar meddelandet vid ett senare tillfälle. En kö kan ha bara en enda mottagare som [bild 2](#Fig2) visar eller flera program kan läsa från samma kö. I det senare fallet läses varje meddelande av enbart en mottagare. För en tjänst med multi-cast bör du istället använda ett ämne.

Varje meddelande består av två delar: en uppsättning av egenskaper, som alla är ett nyckel-/värdepar, och en binär meddelandetext. Hur de används beror på vad en app försöker utföra. Ett exempel: Ett program som skickar ett meddelande om en nyligen genomförd försäljning kan innehålla egenskaperna *Säljare="Ava"* och *Belopp= 10000*. Meddelandetexten (brödtexten) kan innehålla en skannad bild av försäljningsavtalet, eller om det inte finns något sådant, bara vara tomt.

En mottagare kan läsa ett meddelande från en Service Bus-kö på två olika sätt. Det första alternativet, som kallas *ReceiveAndDelete*, tar bort meddelandet från kön och raderar det direkt. Detta är ett enkelt alternativ men om mottagaren kraschar innan den har slutfört behandlingen av meddelandet så kommer det att gå förlorat. Eftersom det har tagits bort från kön kan inga andra mottagare komma åt det. 

Det andra alternativet, *PeekLock*, är utformat för att lösa detta problem. Som ReceiveAndDelete bort en PeekLock meddelandet från kön. Men det raderar inte meddelandet. I stället låser det meddelandet, vilket gör det osynligt för andra mottagare, och sedan inväntar alternativet en av följande tre händelser:

* Om mottagaren bearbetar meddelandet på ett framgångsrikt sätt, anropar det *Slutför* och kön raderar meddelandet. 
* Om mottagaren beslutar att den inte kan bearbeta meddelandet på rätt sätt, anropar det *Avbryt*. Kön tar bort spärren från meddelandet och gör det tillgängligt för andra mottagare.
* Om mottagaren inte anropar något av dessa alternativ inom en konfigurerbar tidsperiod (standardvärdet är 60 sekunder), förutsätter kön att mottagaren har misslyckats. I det här fallet fungerar som om mottagaren hade anropat avbryta, gör meddelandet tillgängligt för andra mottagare.

Observera vad som kan inträffa här: Samma meddelande kan levereras två gånger, kanske till två olika mottagare. Appar som använder Service Bus-köer måste förberedas för detta. Om du vill förenkla identifiering av dubbletter, varje meddelande har en unik MessageID-egenskap som standard alltid densamma, oavsett hur många gånger har meddelandet lästs från en kö. 

Köer är användbara i ett ganska stort antal situationer. De låter appar kommunicera även när båda inte körs på samma gång, något som är särskilt praktiskt med batch- och mobilappar. En kö med flera mottagare ger också automatisk belastningsbalansering eftersom skickade meddelanden sprids ut bland dessa mottagare.

## <a name="topics"></a>Ämnen
Även om köer ofta är väldigt användbara, är de inte alltid den rätta lösningen. Ibland är det bättre att använda Service Bus-ämnen. [Bild 3](#Fig3) visar den här idén.

<a name="Fig3"></a>![Diagram över Service Bus-ämnen och prenumerationer][topics-subs]

**Bild 3: Baserat på de filter ett prenumererande program specificerar så kan det ta emot en del eller alla meddelanden som skickats till ett Service Bus-ämne.**

Ett ämne liknar på många sätt en kö. Avsändare skickar meddelanden till ett ämne på samma sätt som de skickar meddelanden till en kö. Och dessa meddelanden ser likadana ut som när de används i köer. Den stora skillnaden är att avsnitt om att varje mottagande programmet skapa sin egen prenumeration genom att definiera en *filter*. Prenumeranten kommer därefter endast att se de meddelanden som matchar filtret. Till exempel [bild 3](#Fig3) visar en avsändare och ett ämne med tre prenumeranter, var och en med sitt eget filter:

* Prenumerant 1 får endast meddelanden som innehåller egenskapen *Säljare="Ava"*.
* Prenumerant 2 tar emot meddelanden som innehåller egenskapen *Säljare="Ruth"* och/eller innehåller en egenskap för *belopp* vars värde är större än 100 000. Ruth kanske är försäljningschef och så hon vill se både sina egna försäljningar och alla stora försäljningar, oavsett vem som gör dem.
* Prenumerant 3 har ställt in sitt filter på *Sant*, vilket innebär att han eller hon får alla meddelanden. Den här appen kan till exempel ansvara för att bibehålla ett revisionsspår och därför behöver den visa alla meddelanden.

Precis som med köer, läsa prenumeranter till ett ämne meddelanden med ReceiveAndDelete och PeekLock. Men till skillnad från köer kan ett enskilt meddelande som skickas till ett ämne tas emot av flera prenumeranter. Den här metoden anropas ofta *publicera och prenumerera*, är användbart när flera program kan ha intresse av samma meddelanden. Genom att definiera rätt typ av filter kan varje prenumerant ta del av enbart den del av meddelandeströmmen som de behöver se.

## <a name="relays"></a>Reläer
Både köer och ämnen ger dig enkelriktad, asynkron kommunikation via en koordinator. Trafiken flödar endast i en riktning och det finns ingen direkt anslutning mellan avsändarna och mottagarna. Men vad händer om du inte vill använda denna lösning? Anta att dina appar både måste kunna skicka och ta emot meddelanden. Eller så kanske du vill ha en direktlänk mellan dem och du har inget behov av en koordinator för att lagra meddelanden. Att hantera scenarier som detta tillhandahåller Service Bus reläer (relays), som [bild 4](#Fig4) visar.

<a name="Fig4"></a>![Diagram över Service Bus Relay][relay]

**Bild 4: Service Bus Relay erbjuder synkron, dubbelriktad kommunikation mellan program.**

Den naturliga frågan när det gäller reläer är följande: Varför bör jag använda ett sådant? Även om jag inte behöver köer, varför göra så att appar kommunicerar via en molnbaserad tjänst i stället för att helt enkelt interagera direkt med varandra? Svaret är att det här med att prata direkt med varandra kan vara svårare än du tror.

Anta att du vill ansluta två lokala appar. Båda körs på företagets eget datacenter. Var och en av dessa appar ligger bakom en brandvägg och varje datacenter använder antagligen nätadressöversättning (NAT). Brandväggen blockerar inkommande data på i princip alla portar och NAT-användningen antyder att datorerna där apparna körs inte har en fast IP-adress som gör att du kan nå dem direkt från en plats utanför datacentret. Utan extra hjälp är det problematiskt att ansluta dessa appar till varandra med hjälp av en offentlig Internetanslutning.

Ett Service Bus-relä ger dig denna hjälp. För att kommunicera i båda riktningarna via ett relä, upprättar varje app en utgående TCP-anslutning med Service Bus och håller den sedan öppen. All kommunikation mellan de två apparna kommer att överföras via dessa anslutningar. Eftersom varje anslutning har upprättats inifrån datacentret, kommer brandväggen att tillåta inkommande trafik till varje app utan att öppna nya portar. Med hjälp av den här metoden kringgår du också problemet med NAT eftersom varje app har en konsistent slutpunkt i molnet under hela kommunikationen. Genom att utföra utbyte av data via reläet kan apparna undvika problem som annars skulle ha försvårat kommunikationen. 

Om du vill använda Service Bus reläer bygger program på Windows Communication Foundation (WCF). Service Bus ger dig WCF-bindningar som gör det enkelt för Windows-program att interagera via reläer. Program som redan använder WCF behöver normalt bara ange en av dessa bindningar och sedan kan de kommunicera med varandra via ett relä. Men till skillnad från köer och ämnen kräver användningen av reläer från andra program än Windows-program en hel del programmeringsarbete, även om det går att genomföra. Det medföljer inga standardbibliotek.

Till skillnad från köer och ämnen skapar apparna inga reläer rent uttryckligen. När en app som du vill ta emot meddelanden med upprättar en TCP-anslutning med Service Bus, skapas istället ett relä automatiskt. När anslutningen avbryts tas reläet bort. Om du vill att ett program att hitta det relä som skapats av en viss lyssnare, innehåller Service Bus ett register som gör det möjligt att söka efter en specifik vidarebefordran efter namn.

Reläer är den rätta lösningen när du behöver upprätta direkt kommunikation mellan program. Föreställ dig till exempel ett bokningssystem från ett flygbolag som körs i ett lokalt datacenter. Detta måste kunna nås från incheckningskiosker, mobila enheter och andra datorer. Program som körs på alla dessa system kan förlita sig på Service Bus Relays i molnet för att kommunicera med varandra, oavsett var de körs.

## <a name="event-hubs"></a>Händelsehubbar
Händelsehubbar i Azure är ett mycket skalbart system som kan behandla miljontals händelser per sekund. Tack vare detta kan din app behandla och analysera de enorma mängder data som produceras av dina anslutna enheter och program. Du kan till exempel använda en händelsehubb för att samla in prestandadata live från motorer i en bilpark. När uppgifterna väl samlats i Event Hubs, kan du omvandla och lagra data med hjälp av en leverantör av realtidsanalyser eller lagringskluster. Mer information om Händelsehubbar finns i [översikt av Händelsehubbar][Event Hubs overview].

## <a name="summary"></a>Sammanfattning
Att ansluta program har alltid varit en del av arbetet när man skapar kompletta lösningar. Mängden scenarier som dyker upp och kräver att program och tjänster ska kunna kommunicera med varandra kommer med all säkerhet att öka eftersom fler och fler program och enheter ansluts till Internet. Syftet med Service Bus är att göra denna absolut nödvändiga funktion enklare att implementera och se till att tekniken blir mer tillgänglig, genom att tillhandahålla molnbaserad teknik och använda köer, ämnen, reläer och händelsehubbar.

[svc-bus]: ./media/hybrid-solutions/SvcBus_01_architecture.png
[queues]: ./media/hybrid-solutions/SvcBus_02_queues.png
[topics-subs]: ./media/hybrid-solutions/SvcBus_03_topicsandsubscriptions.png
[relay]: ./media/hybrid-solutions/SvcBus_04_relay.png
[Event Hubs overview]: https://msdn.microsoft.com/library/azure/dn836025.aspx
