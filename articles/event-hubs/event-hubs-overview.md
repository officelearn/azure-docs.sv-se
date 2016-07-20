<properties 
    pageTitle="Översikt av händelsehubbar i Azure | Microsoft Azure"
    description="Introduktion och översikt av  händelsehubbar i Azure."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="04/15/2016"
    ms.author="sethm" />

# Översikt av händelsehubbar i Azure

Många moderna lösningar strävar efter att ge anpassningsbara kundupplevelser eller att förbättra produkter genom kontinuerlig feedback och automatisk telemetri. Sådana lösningar står inför utmaningen hur de på ett säkert och tillförlitligt sätt ska bearbeta mycket stora mängder information från många samtidiga utgivare. Händelsehubbar i Microsoft Azure är en hanterad plattformstjänst som utgör en grund för storskaligt dataintag inom ett stort antal scenarier. Exempel på sådana scenarier är beteendespårning i mobilappar, trafikinformation från webbservergrupper, inspelning av spelhändelser i konsolspel eller telemetridata som samlats in från industriella datorer eller anslutna fordon. I lösningsarkitekturer fungerar händelsehubbar ofta som ”dörren” för en händelsepipeline, ofta kallad en *händelseinmatare*. En händelseinmatare är en komponent eller tjänst som placeras mellan händelseutgivare och -konsumenter och frikopplar produktion av en händelseström från användningen av de händelserna.

![Händelsehubbar](./media/event-hubs-overview/IC759856.png)

Händelsehubbar i Azure är en tjänst för händelsebearbetning som ger händelse- och telemetriingång till molnet i massiv skala med kort svarstid och hög tillförlitlighet. Den här tjänsten, som används med andra nedströms tjänster, är särskilt användbar inom programinstrumentering, bearbetning av användarupplevelser eller arbetsflöden samt scenarier i sakernas internet. Händelsehubbar har funktioner för att hantera meddelandeströmmar,  och även om händelsehubbar påminner om köer och ämnen har de egenskaper som skiljer sig väldigt mycket från traditionella meddelandetjänster för företag. Meddelandetjänstscenarier för företag kräver vanligtvis ett antal avancerade funktioner som sekvensering, dead-lettering, stöd för transaktioner och starka leveransgarantier. Det största problemet för händelseintag är däremot ett högt genomflöde och flexibilitet vid bearbetningen av händelseströmmar. Funktionerna i händelsehubbar skiljer sig därför från Service Bus-ämnen på det sättet att de fokuserar på ett högt genomflöde och händelsebearbetningsscenarier. Därför implementerar händelsehubbar inte en del av de meddelandefunktioner som är tillgängliga för ämnen. Om du behöver de funktionerna är ämnen fortfarande att föredra.

En händelsehubb skapas på namnområdesnivån  i Service Bus, ungefär som köer och ämnen. Händelsehubbar använder AMQP och HTTP som sina primära API-gränssnitt. Följande diagram visar relationen mellan händelsehubbar och Service Bus.

![Händelsehubbar](./media/event-hubs-overview/IC741188.png)

## Konceptuell översikt

Händelsehubbar ger meddelandeströmmar via ett konsumentmönster indelat i partitioner. Köer och ämnen använder en [konkurrerande konsument](https://msdn.microsoft.com/library/dn568101.aspx)-modell där varje konsument försöker läsa från samma kö eller resurs. Denna konkurrens om resurser ledar i slutändan till komplexitet och skalningsgränser för program som bearbetar strömmar. Händelsehubbar använder ett partitionerat konsumentmönster där varje konsument bara läser en viss delmängd, eller partition, av meddelandeströmmen. Det här mönstret gör det möjligt att skala horisontellt för händelsebearbetning och tillhandahåller andra strömfokuserade funktioner som inte är tillgängliga i köer och ämnen.

### Partitioner

En partition är en ordnad sekvens av händelser som hålls kvar i en händelsehubb. När nya händelser anländer läggs de till i slutet av denna sekvens. En partition kan betraktas som en ”genomförandelogg”.

![Händelsehubbar](./media/event-hubs-overview/IC759857.png)

Partitioner behåller data under en konfigurerad kvarhållningstid som anges på händelsehubbnivån. Den här inställningen gäller för alla partitioner i händelsehubben. Händelser löper ut enligt ett tidsschema. Det går inte att ta bort dem. En händelsehubb innehåller flera partitioner. Varje partition är oberoende och innehåller sin egen sekvens av data. Partitioner växer därför ofta med olika hastigheter.

![Händelsehubbar](./media/event-hubs-overview/IC759858.png)

Antalet partitioner specificeras vid tidpunkten då händelsehubben skapas och måste vara mellan 2 och 32 (standard är 4). En partition är en mekanism för organisation av data och är mer relaterad till den grad av nedströms parallellism som krävs inom användarprogram än med genomflödet i händelsehubbar. Det gör att valet av antalet partitioner i en händelsehubb är direkt relaterat till det antal samtidiga läsare du förväntar dig. När en händelsehubb har skapats kan antalet partitioner inte ändras. Du bör därför bestämma antalet med tanke på förväntad storlek på lång sikt. Du kan överskrida gränsen på 32 partitioner genom att kontakta Service Bus-teamet.

Partitioner kan identifieras och det går att skicka till dem direkt, men det är oftast bäst att undvika att skicka data till specifika partitioner. Du kan i stället använda de konstruktioner på högre nivå som presenterades i avsnitten [Händelseutfärdare](#event-publisher) och [Utgivarprinciper](#capacity-and-security).

När man pratar om händelsehubbar kallar man meddelanden *händelsedata*. Händelsedata innehåller händelsens brödtext, en användardefinierad egenskapsuppsättning och olika metadata om händelsen, till exempel dess offset i partitionen och dess nummer i dataströmsekvensen. Partitioner är fyllda med en sekvens av händelsedata.

## Händelseutfärdare

En enhet som skickar händelser eller data till en händelsehubb är en *händelseutfärdare*. Händelseutfärdare kan utfärda händelser med hjälp av HTTPS  eller AMQP 1.0. Händelseutfärdare använder en signatur för delad åtkomst-token (SAS) för att identifiera sig på en händelsehubb, och kan ha en unik identitet, eller använda en vanlig SAS-token, beroende på kraven för scenariot.

Mer information om hur du arbetar med SAS finns i [autentisering med signatur för delad åtkomst med Service Bus](../service-bus/service-bus-shared-access-signature-authentication.md).

### Vanliga utfärdaruppgifter

I det här avsnittet beskrivs några  vanliga uppgifter för händelseutfärdare.

#### Hämta en SAS-token

Signatur för delad åtkomst (SAS) är autentiseringsmekanismen för händelsehubbar. Service Bus innehåller SAS-principer på namnområdes- och händelsehubbnivån. En SAS-token genereras från en SAS-nyckel och är en SHA-hash för en URL som kodats i ett specifikt format. Med hjälp av namnet på nyckeln (principen) och token kan Service Bus regenerera hash-värdet och därmed autentisera avsändaren. SAS-token för händelseutfärdare skapas vanligtvis med enbart behörighet för att **skicka** i en specifik händelsehubb. Den här URL-mekanismen med SAS-token är den grund för utfärdaridentifiering som presenterades i principen för utfärdare. Mer information om hur du arbetar med SAS finns i [autentisering med signatur för delad åtkomst med Service Bus](../service-bus/service-bus-shared-access-signature-authentication.md).

#### Publicera en händelse

Du kan publicera en händelse via AMQP 1.0 eller HTTPS. Service Bus innehåller en [EventHubClient](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventhubclient.aspx)-klass för att publicera händelser på en händelsehubb från .NET-klienter. För andra körningar och plattformar kan du använda alla AMQP 1.0-klienter, t.ex. [Apache Qpid](http://qpid.apache.org/). Du kan publicera händelser individuellt eller i batchar. En enstaka publikation (en instans av händelsedata ) har en begränsning på 256 KB, oavsett om det är en enskild händelse eller en batch. Att utfärda händelser som överstiger den gränsen resulterar i ett fel. Bästa metoden för en utfärdare är att vara ovetande om partitioner i händelsehubben och att bara ange en *partitionsnyckel* (presenteras i nästa avsnitt) eller sin identitet via sin SAS-token.

Valet att använda AMQP eller HTTPS är specifikt för användningsscenariot. AMQP kräver en beständig dubbelriktad socket och dessutom säkerhet på transportnivå (TLS) eller SSL/TLS. Detta kan vara en kostsam åtgärd vad gäller nätverkstrafik, men det sker endast i början av en AMQP-session. HTTPS har lägre inledande omkostnader, men kräver ytterligare SSL-omkostnader för varje begäran. AMQP erbjuder betydande besparingar vad gäller prestanda, svarstid och genomströmning för utfärdare som ofta publicerar händelser.

### Partitionsnyckeln

En partitionsnyckel är ett värde som används för att mappa inkommande händelsedata med specifika partitioner för att organisera data. Partitionsnyckeln är ett värde som avsändaren anger och som skickas till en händelsehubb. Det bearbetas via en statisk hash-funktion, vars resultat skapar tilldelningen av partition. Om du inte anger en partitionsnyckel när du publicerar en händelse, används en tilldelning enligt resursallokeringsmodellen. När händelseutfärdaren använder partitionsnycklar känner denne bara till sin partitionsnyckel, inte den partition där händelserna publiceras. Frikopplingen av nyckeln och partitionen gör att avsändaren inte behöver känna till så mycket om bearbetningen nedströms och lagringen av händelser. Partitionsnycklar är viktiga för att ordna data för nedströms bearbetning, men i sig är de i grunden orelaterade till partitioner. En identitet per enhet eller en användarunik identitet utgör en bra partitionsnyckel, men andra attribut, till exempel geografi, kan också användas för att gruppera relaterade händelser i en enda partition. Följande bild visar händelseavsändare som använder partitionsnycklar som de fäster i partitioner.

![Händelsehubbar](./media/event-hubs-overview/IC759859.png)

Händelsehubbar garanterar att alla händelser som delar samma partitionsnyckelvärde levereras i ordning och till samma partition. En viktig sak är att om partitionsnycklar används med utfärdarprinciper, vilka beskrivs i nästa avsnitt, måste utfärdarens identitet och värdet för partitionsnyckeln matcha. Annars uppstår ett fel.

### Händelsekonsumenter

En enhet som läser händelsedata från en händelsehubb är en händelsekonsument. Alla händelsekonsumenter läser händelseströmmen via partitioner i en konsumentgrupp. Varje partition får ha endast en aktiv läsare i taget. Alla konsumenter i en händelsehubb ansluter via AMQP 1.0-sessionen, dit händelser levereras när de blir tillgängliga. Klienten behöver inte söka efter datatillgänglighet.

#### Konsumentgrupper

Mekanismen för att publicera/prenumerera i händelsehubbar aktiveras via konsumentgrupper. En konsumentgrupp är en vy (tillstånd, position eller offset) av en hel händelsehubb. Konsumentgrupper gör det möjligt för flera användningsprogram att vart och ett ha en separat vy över händelseströmmen och att oberoende läsa strömmen i egen takt och med sina egna offset. Inom en arkitektur för strömbearbetning utgör varje nedströms program en konsumentgrupp. Om du vill skriva händelsedata till långsiktig lagring utgör programmet för att skriva data till lagring en konsumentgrupp. Bearbetning av komplexa händelser utförs av en annan, separat konsumentgrupp. Du får bara åtkomst till en partition via en konsumentgrupp. Det finns alltid en förinställd konsumentgrupp i en händelsehubb, och du kan skapa upp till 20 konsumentgrupper för en händelsehubb på standardnivå.

Följande är exempel på URI-konventionen för konsumentgrupper:

    //<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #1>
    //<my namespace>.servicebus.windows.net/<event hub name>/<Consumer Group #2>

Följande bild visar händelsekonsumenter inom konsumentgrupper.

![Händelsehubbar](./media/event-hubs-overview/IC759860.png)

#### Offsets för strömmar

En offset är en händelses position inom en partition. Föreställ dig en offset som en markör på klientsidan. Denna offset är en byte-numrering av händelsen. Detta gör att en händelsekonsument (läsare) kan ange en punkt i händelseströmmen där denne vill börja läsa händelser. Du kan ange offseten som en tidsstämpel eller ett offset-värde. Konsumenterna ansvarar för att lagra sina egna offset-värden utanför händelsehubbtjänsten.

![Händelsehubbar](./media/event-hubs-overview/IC759861.png)

Inom en partition innehåller varje händelse en offset. Denna offset används av konsumenterna för att visa platsen i händelsesekvensen i en given partition. Offsets kan skickas till händelsehubben antingen som ett nummer eller som ett tidsstämpelvärde när en läsare ansluter.

#### Kontrollpunkter

*Att skapa kontrollpunkter* är en process genom vilken läsare markerar eller sparar sin position inom en händelsesekvens i en partition. Att skapa kontrollpunkter är konsumentens ansvar och görs för varje partition i en konsumentgrupp. Det innebär att för varje konsumentgrupp måste varje läsare i partitionen hålla reda på sin nuvarande position i händelseströmmen. Läsaren kan sedan informera tjänsten när de anser att dataströmmen är klar. Om en läsare kopplar från en partition och den sedan återansluts kan han börja läsa vid den kontrollpunkt som tidigare skickades in av den senaste läsaren i den aktuella partitionen inom just den konsumentgruppen. När läsaren ansluter skickar partitionen denna offset till händelsehubben för att ange den plats där läsaren ska börja läsa. På så sätt kan du använda kontrollpunkter både för att markera händelser som ”klara” i nedströms program och för att skapa återhämtning i händelse av växling mellan läsare som körs på olika datorer. Eftersom händelsedata sparas under det kvarhållningsintervall som specificeras då händelsehubben skapas går det att återgå till äldre data genom att ange en lägre offset tack vare kontrollpunkterna. Tack vare den här mekanismen gör kontrollpunkterna det möjligt med växlingsåterhämtning och kontrollerade återspelningar av händelseströmmar.

#### Vanliga konsumentuppgifter

I det här avsnittet beskrivs vanliga uppgifter för händelsekonsumenter eller läsare i händelsehubbar. Alla konsumenter i händelsehubbar ansluter via AMQP 1.0. AMQP 1.0 är en sessions- och tillståndsmedveten dubbelriktad kommunikationskanal. Varje partition har en AMQP 1.0-länksession som gör det lättare att flytta händelser som åtskiljs av partitioner.

##### Ansluta till en partition

För att kunna konsumera händelser från en händelsehubb måste konsumenten ansluta till en partition. Som tidigare nämnts får du alltid åtkomst till en partition genom en konsumentgrupp. Som en del av modellen med konsumenter i olika partitioner kan bara en läsare åt gången vara aktiv i en partition inom en konsumentgrupp. När du ansluter direkt till partitioner är det vanligt att använda en leasingmekanism för att koordinera läsaranslutningar till specifika partitioner. På det sättet är det möjligt för varje partition i en konsumentgrupp att bara ha en aktiv läsare. Att hantera en läsares position i sekvensen  är en viktig uppgift som åstadkoms genom kontrollpunkter. Den här funktionen förenklas med hjälp av [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx)-klassen för .NET-klienter. [EventProcessorHost](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx) är en intelligent konsumentagent och beskrivs i nästa avsnitt.

##### Läsa händelser

När en AMQP 1.0-session och -länk har öppnats för en specifik partition, levereras händelser till AMQP 1.0-klienten av händelsehubbtjänsten. Den här leveransmekanismen gör det möjligt med ett högre genomflöde och kortare svarstid än i pull-baserade mekanismer som t.ex. HTTP GET. När händelser skickas till klienten innehåller varje instans av händelsedata viktiga metadata, till exempel offset- och sekvensnumret som används för att göra det lättare att skapa kontrollpunkter i händelsesekvensen.

![Händelsehubbar](./media/event-hubs-overview/IC759862.png)

Det är användarens ansvar att hantera denna offset på ett sätt som på bästa sätt gör det möjligt att hantera förloppet vid bearbetning av dataströmmen.

## Kapacitet och säkerhet

Händelsehubbar är en mycket skalbar parallell arkitektur för ingång av dataströmmar. Därför finns det flera viktiga aspekter att tänka på när du ska bestämma storleken på och skala en lösning som baseras på händelsehubbar. Den första av dessa kapacitetskontroller är *genomflödesenheter*, vilka beskrivs i följande avsnitt.

### Genomflödesenheter

Genomflödeskapaciteten i händelsehubbar styrs av genomflödesenheter. Genomflödesenheter är färdiga kapacitetsenheter. En genomflödesenhet omfattar följande:

- Ingång: Upp till 1 MB per sekund eller 1 000 händelser per sekund.

- Utgång: Upp till 2 MB per sekund.

Ingången begränsas till storleken på den kapacitet som antalet inköpta genomflödesenheter ger. Om du skickar data som överskrider den mängden resulterar det i felmeddelandet ”kvoten har överskridits”. Den här mängden är antingen 1 MB per sekund eller 1 000 händelser per sekund, beroende på vilket som kommer först. Utgången skapar inte begränsningsfel, men är begränsad till den mängd av dataöverföring som genomflödesenheterna ger: 2 MB per sekund per genomflödesenhet. Om du får felmeddelanden om publiceringsfrekvensen eller förväntar dig större utgång måste du kontrollera hur många genomflödesenheter du har köpt för det namnområde där händelsehubben skapades. För att skaffa fler genomflödesenheter kan du justera inställningen på sidan **namnområden** på fliken **Skala** i den [klassiska Azure-portalen][]. Du kan också ändra den här inställningen med hjälp av Azure-API:erna.

Partitioner är ett koncept för organisering av data men genomflödesenheter är helt och hållet ett koncept för kapacitet. Genomflödesenheter debiteras per timme och köps i förväg. När de väl har köpts debiteras de för minst en timme. Upp till 20 genomflödesenheter kan köpas för ett Service Bus-namnområde, och det finns en gräns för ett Azure-konto på högst 20 genomflödesenheter. Genomflödesenheterna är gemensamma för alla händelsehubbar i ett visst namnområde.

Genomflödesenheter allokeras på basis av tillgänglighet och det kan hända att de inte finns tillgängliga för omedelbart köp. Om du behöver en specifik kapacitet bör du köpa dessa enheter i förväg. Om du behöver fler än 20 genomflödesenheter kan du kontakta Service Bus-supporten och köpa fler enheter i block på 20, upp till de första 100 enheterna. Utöver det kan du också köpa block med 100 genomflödesenheter.

Du bör noggrant väga genomflödesenheter och partitioner för att uppnå bästa skala med händelsehubbar. En enstaka partition har en maximal skala på en genomflödesenhet. Antalet genomflödesenheter ska vara mindre än eller lika med antalet partitioner i en händelsehubb.

Utförlig prisinformation finns på [Priser för händelsehubbar](https://azure.microsoft.com/pricing/details/event-hubs/).

### Utgivarprincip

Med händelsehubbar får du granulär kontroll över utgivare via *utgivarprinciper*. Utgivarprinciper är en uppsättning körningsfunktioner utformade för att göra det möjligt att ha ett stort antal oberoende utgivare. Med utgivarprinciper använder varje utgivare sin egen unika identifierare vid publicering av händelser på en händelsehubb med hjälp av följande mekanism:

    //<my namespace>.servicebus.windows.net/<event hub name>/publishers/<my publisher name>

Du behöver inte skapa utgivarnamnen i förväg, men de måste matcha SAS-token som används när du publicerar en händelse för att garantera oberoende utgivaridentiteter. Mer information om SAS finns i [autentisering med signatur för delad åtkomst med Service Bus](../service-bus/service-bus-shared-access-signature-authentication.md). När du använder utgivarprinciper ställs **PartitionKey**-värdet in på utgivarens namn. Dessa värden måste matcha för att fungera korrekt.

## Sammanfattning

Händelsehubbar i Azure tillhandahåller en tjänst för händelse- och telemetribearbetning i hyperskala som kan användas för vanlig övervakning av program- och användararbetsflöden i alla skalor. Händelsehubbar har funktioner för att publicera och prenumerera med kort svarstid och i massiv skala och tjänar därmed som inkörsvägen för stordata. De här funktionerna har utgivarbaserade listor över identitet och återkallande och har utökats till vanliga scenarier inom sakernas internet. Mer information om att utveckla program i händelsehubbar finns i [Programmeringsguide för händelsehubbar](event-hubs-programming-guide.md).

## Nästa steg

Nu när du har lärt känna en del koncept inom händelsehubbar kan du gå vidare till följande scenarier:

- Kom igång med en [kurs om händelsehubbar]
- Ett komplett [exempelprogram som använder händelsehubbar].
- En [lösning för köade meddelanden] som använder Service Bus-köer.

[klassiska Azure-portalen]: http://manage.windowsazure.com
[kurs om händelsehubbar]: event-hubs-csharp-ephcs-getstarted.md
[exempelprogram som använder händelsehubbar]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-286fd097
[lösning för köade meddelanden]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 



<!--HONumber=Jun16_HO2-->


