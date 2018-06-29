---
title: 'Azure DB Cosmos-designmönstret: sociala medier appar | Microsoft Docs'
description: Läs mer om ett designmönster för sociala nätverk genom att utnyttja flexibiliteten för lagring av Azure Cosmos DB och andra Azure-tjänster.
keywords: sociala medier appar
services: cosmos-db
author: ealsur
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: maquaran
ms.openlocfilehash: f81a087a2595db41dbe84a54ad1fd01adf043515
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060411"
---
# <a name="going-social-with-azure-cosmos-db"></a>Gå sociala med Azure Cosmos DB
Bor i ett massivt sammankopplade society innebär att på någon punkt i livslängd du blir en del av en **sociala nätverk**. Du kan använda sociala nätverk för att hålla kontakten med vänner, kolleger, familj, och ibland att dela din passion med personer med gemensamma intressen.

Som tekniker och utvecklare kan du kanske har funderat över hur dessa nätverk lagra och kopplas samman dina data, eller kan ha även har gett för att skapa eller skapa ett nytt sociala nätverk för en specifik nischer marknad yourselves. Det är då betydande frågan: hur lagras dessa data?

Anta att du skapar ett nytt och blank sociala nätverk, där användarna kan skicka artiklar med relaterade mediet, till exempel bilder, videofilmer och musik. Användare kan kommentera inlägg och ge punkter för klassificering. Det blir en feed tjänster som användarna ser och kunna interagera med på webbplats-landningssidan. Detta inte låter komplexa (överst), men för enkelhetens skull, gör stoppa det (du kan utforska anpassade feeds påverkas av relationer, men den överskrider syftet med den här artikeln).

Så, hur ska du spara det och var?

Många av du kanske har erfarenhet på SQL-databaser eller minst ha begreppet [relationella modellering av data](https://en.wikipedia.org/wiki/Relational_model) och du kanske att tro att starta rita ut så här:

![Diagram som illustrerar en relativ relationella modell](./media/social-media-apps/social-media-apps-sql.png) 

En perfekt normaliserade och snyggt datastruktur... som inte kan skalas. 

Hämta mig fel, jag har arbetat med SQL-databaser mitt liv, de är bra, men som var mönster, praxis och programvara plattform är inte perfekt för varje scenario.

Varför visas inte SQL det bästa valet i det här scenariot? Nu ska vi titta på strukturen för en enskild post kan visa den post i en webbplats eller ett program, måste göra en fråga med... Att bara visa en enda post, nu bilden som en dataström med inlägg som dynamiskt läsa in och visas på skärmen och du kan se där vi åtta tabellkopplingar (!).

Du kan självklart använder en enorma SQL-instans med tillräckligt med för att lösa tusentals frågor med dessa många kopplingar för att hantera ditt innehåll, men verkligen, varför skulle du, när det finns en enklare lösning?

## <a name="the-nosql-road"></a>NoSQL-väg
Den här artikeln hjälper dig i modeling din sociala plattform data med Azures NoSQL-databas [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) på ett kostnadseffektivt sätt samtidigt utnyttja andra Azure-Cosmos-DB funktioner som den [Gremlin Graph API](../cosmos-db/graph-introduction.md). Med hjälp av en [NoSQL](https://en.wikipedia.org/wiki/NoSQL) metod, datalagring i JSON-format och tillämpa [denormalization](https://en.wikipedia.org/wiki/Denormalization), tidigare komplicerad post kan omvandlas till en enda [dokument](https://en.wikipedia.org/wiki/Document-oriented_database):


    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["http://myfirstimage.png","http://mysecondimage.png"],
        "videos":[
            {"url":"http://myfirstvideo.mp4", "title":"The first video"},
            {"url":"http://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"http://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"http://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

Och kan hämtas med en enda fråga, och inga kopplingar. Detta är mycket mer lätt och budget-wise, den kräver färre resurser för att få ett bättre resultat.

Azure Cosmos-DB ser till att alla egenskaper som indexeras med dess automatisk indexering, som även kan vara [anpassade](indexing-policies.md). Metoden schemafria Låt oss lagra dokument med olika och dynamiska strukturer, imorgon kanske du vill inlägg ha en lista över kategorier eller hash-taggar som är kopplade till dem, Cosmos DB hanterar alla nya dokument med tillagda attribut med inget extra arbete krävs för oss.

Kommentarer om en post kan behandlas som andra tjänster med en överordnad-egenskap (Detta förenklar objekt-mappning). 

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":User2,
        "parent":"ew12-res2-234e-544f"
    }

    {
        "id":"asd2-fee4-23gc-jh67",
        "title":"Ditto!",
        "date":"2016-01-03",
        "createdBy":User3,
        "parent":"ew12-res2-234e-544f"
    }

Och all kommunikation kan lagras på ett separat objekt som räknare:

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

Att skapa flöden är bara en fråga om hur du skapar dokument som kan innehålla en lista över post-ID: n med en viss relevans ordning:

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

Du kan ha en ”senaste” dataström med listan sorteras efter skapandedatum, en ”senaste” ström med dessa poster med gillar mer under de senaste 24 timmarna, kan du även implementera en anpassad dataström för varje användare baserat på logik som blandare och intressen och det kan fortfarande vara en lista över  Bokför. Det handlar om hur du skapar de här listorna, men läsning prestanda förblir röra sig obehindrat. När du skaffar en av dessa listor kan du utfärda en enskild fråga till Cosmos-databas med hjälp av den [i operatorn](sql-api-sql-query.md#WhereClause) att hämta sidor i inlägg i taget.

Feed strömmar kan byggas med [Azure App Services](https://azure.microsoft.com/services/app-service/) background processer: [Webjobs](../app-service/web-sites-create-web-jobs.md). När en post har skapats behandling i bakgrunden kan aktiveras med hjälp av [Azure Storage](https://azure.microsoft.com/services/storage/) [köer](../storage/queues/storage-dotnet-how-to-use-queues.md) och Webjobs aktiveras med hjälp av den [Azure Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki), implementerar den Bokför spridning i strömmar som baseras på dina egna anpassade logik. 

Punkter och om över ett inlägg kan bearbetas i en uppskjuten sätt med samma metod för att skapa en överensstämmelse miljö.

Blandare är svårare. Cosmos DB har en storleksgräns för maximal dokumentet och läsning/skrivning stora dokument kan påverka skalbarhet i ditt program. Så får du tycker om lagra blandare som ett dokument med den här strukturen:

    {
        "id":"234d-sd23-rrf2-552d",
        "followersOf": "dse4-qwe2-ert4-aad2",
        "followers":[
            "ewr5-232d-tyrg-iuo2",
            "qejh-2345-sdf1-ytg5",
            //...
            "uie0-4tyg-3456-rwjh"
        ]
    }

Detta kan fungera för en användare med några tusentalsavgränsare styrstavsföljare, men om vissa ryktbarhet ansluts till block av det här sättet kan leda till en stor storlek och kan slutligen tryck dokumentet storlek fästpunkten.

Du kan använda en blandad metod för att lösa detta problem. Du kan lagra antalet blandare som en del av dokumentet statistik:

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

Och det faktiska diagrammet över blandare kan lagras i Azure Cosmos DB [Gremlin Graph API](../cosmos-db/graph-introduction.md), för att skapa [brytpunkter](http://mathworld.wolfram.com/GraphVertex.html) för varje användare och [kanter](http://mathworld.wolfram.com/GraphEdge.html) som underhåller den ”A-sätt – B” relationer. Graph API vi ska du inte bara hämta blandare med en viss användare men skapa mer komplexa frågor och även med vanliga föreslår personer. Om du lägger till diagrammet innehåll kategorier som personer som eller få, kan du starta vävning upplevelser som innehåller smart innehållsidentifiering föreslå innehåll än de som du följer som eller söka efter personer som du kanske har mycket gemensamt.

Statistik dokumentet kan användas för att skapa kort i Användargränssnittet eller snabb profil förhandsgranskningar.

## <a name="the-ladder-pattern-and-data-duplication"></a>”Stege” mönstret och data duplicering
Som du såg i JSON-dokumentet som refererar till en post, finns det flera förekomster av en användare. Och du skulle ha gissa höger, det innebär att den information som representerar en användare får den här denormalization kan finnas i mer än en plats.

För att möjliggöra snabbare frågor, innebära duplicerade data. Problem med den här sidoeffekt är att om du vill söka efter alla aktiviteter av en åtgärd som en användare dataändringar han någonsin matchade och uppdatera dem alla. Inte låter praktiska, höger?

Du ska lösa genom att identifiera nyckelattribut för en användare som du visar i ditt program för varje aktivitet. Om du visuellt visa en post i ditt program och visar bara den person som skaparen namn och bild, Varför spara alla användarens data i attributet ”Skapad”? Om du för varje kommentar du bara att visa användarens bild, behöver du inte verkligen resten av sin information. Det är där något jag anropa ”stege mönstret” kommer i play.

Låt oss ta användarinformation som exempel:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "address":"742 Evergreen Terrace",
        "birthday":"1983-05-07",
        "email":"john@doe.com",
        "twitterHandle":"\@john",
        "username":"johndoe",
        "password":"some_encrypted_phrase",
        "totalPoints":100,
        "totalPosts":24
    }

Genom att titta på den här informationen kan du snabbt identifiera som är viktig information och som därmed inte kan skapa ”stege”:

![Diagram över ett stege mönster](./media/social-media-apps/social-media-apps-ladder.png)

Minsta steg kallas en UserChunk minimal mängd information som identifierar en användare och den används för dataduplicering. Genom att minska storleken på den duplicerade data till den information som du ”visas” minska risken för massiv uppdateringar.

Steget mellersta kallas för användaren, är det fullständiga data som ska användas på de flesta prestanda beroende frågor på Cosmos DB, den mest ofta och viktiga. Den innehåller information som representeras av en UserChunk.

Den största är Extended-användare. Den innehåller alla kritiska användarinformation samt andra data som verkligen inte kräver för att läsas snabbt eller dess användning är eventuell (till exempel inloggningen). Dessa data kan lagras utanför Cosmos-DB i Azure SQL Database eller Azure Storage-tabeller.

Varför skulle du dela användaren och även spara informationen på olika platser? Eftersom från en prestanda synsätt, desto större dokument, desto costlier frågor. Skydda dokument tunn med rätt information för att göra alla prestanda beroende frågor för det sociala nätverket och lagra andra extra informationen för eventuell scenarier som fullständig profil redigering, inloggningar, även datautvinning för användningsanalys och Stordata initiativ. Du verkligen bryr dig inte om datainsamling för datautvinning är långsammare eftersom den körs på Azure SQL Database kan du har gäller dock att användarna har en snabb och smidig upplevelse. En användare som lagras på Cosmos DB skulle se ut så här:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"\@john"
    }

Och en Post skulle se ut:

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
            "id":"dse4-qwe2-ert4-aad2",
            "username":"johndoe"
        }
    }

Redigera uppstår där ett attribut för segmentet påverkas, är det enkelt att hitta berörda dokument med hjälp av frågor som pekar på indexerade attribut (Välj * FROM anslår p var p.createdBy.id == ”edited_user_id”) och sedan uppdaterar segment.

## <a name="the-search-box"></a>Sökrutan
Användare skapar som tur är mycket innehåll. Och du bör kunna ge möjlighet att söka och hitta innehåll som inte kanske direkt i deras innehåll dataströmmar kanske eftersom du inte följer skapare, eller så kanske du vill bara hitta att gamla post du gjorde sex månader sedan.

Tack och lov, och eftersom du använder Azure Cosmos DB, du kan enkelt implementera motorn hjälp [Azure Search](https://azure.microsoft.com/services/search/) på några minuter och utan att skriva en enda rad kod (andra än naturligtvis, sökningen och användargränssnitt).

Varför är det så enkelt?

Azure Search implementerar de anropa [indexerare](https://msdn.microsoft.com/library/azure/dn946891.aspx)bakgrund bearbetar den hooken i data-databaser och automagically lägga till, uppdatera eller ta bort dina objekt i index. De stöder en [Azure SQL Database indexerare](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/), [Azure BLOB indexerare](../search/search-howto-indexing-azure-blob-storage.md) och tack och lov, [Azure Cosmos DB indexerare](../search/search-howto-index-documentdb.md). Övergång till information från Cosmos-databasen till Azure Search är enkel som både store-information i JSON-format, behöver du bara [skapa ditt Index](../search/search-create-index-portal.md) och mappa, vilka attribut från dina dokument som du vill ska indexeras och det i några minuter (beroende på storleken på dina data), allt innehåll kommer att kunna genomsökas på begäran av den bästa lösningen för sökning som en tjänst i cloud-infrastruktur. 

Mer information om Azure Search kan du besöka den [Hitchhiker's Guide till Sök](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/).

## <a name="the-underlying-knowledge"></a>Den underliggande kunskapen
När du sparar det här innehållet som växer och växer varje dag, kan du hitta tänka: Vad kan jag göra med den här dataströmmen av information från Mina användare?

Svaret är enkla: få det att fungera och Läs från den.

Men det kan du lära dig? Några enkelt exempel är [sentiment analysis](https://en.wikipedia.org/wiki/Sentiment_analysis), innehåll rekommendationer baserat på användarens inställningar eller även en automatiserad innehåll kontrollanten som garanterar att allt innehåll som publicerats av sociala nätverket är säker för familjen.

Nu när jag fick du ansluta tänker du förmodligen behöver du vissa PhD i matematiska vetenskap att extrahera dessa mönster och information från enkla databaser och filer, men du kommer att fel.

[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/), en del av den [Cortana Intelligence Suite](https://social.technet.microsoft.com/wiki/contents/articles/36688.introduction-to-cortana-intelligence-suite.aspx), är en helt hanterad molntjänst som låter dig skapa arbetsflöden med hjälp av algoritmer i ett enkelt dra och släpp-gränssnitt, code algoritmerna i [ R](https://en.wikipedia.org/wiki/R_\(programming_language\)) eller använda några av de redan har byggts och redo att använda API: er som: [textanalys](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [innehåll kontrollant eller [rekommendationer](https://gallery.azure.ai/Solution/Recommendations-Solution).

Du kan använda om du vill åstadkomma något av dessa scenarier för Machine Learning [Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) att mata in information från olika källor och använda [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) att bearbeta informationen och skapa en utdata som kan bearbetas av Azure Machine Learning.

En annan tillgängliga alternativ är att använda [Microsoft kognitiva Services](https://www.microsoft.com/cognitive-services) att analysera användarna innehåll, inte bara kan du lättare att förstå dem (via analysera de skriver med [Text Analytics API](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)), men Du kan också identifiera oönskade eller mogen innehåll och fungerar därför med [datorn Vision API](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). Kognitiva Services innehåller många out box-lösningar som inte kräver någon form av Machine Learning kunskap för att använda.

## <a name="a-planet-scale-social-experience"></a>En planeten skala-upplevelsen
Det finns en sista, men inte minst viktiga artikel jag måste lösa: **skalbarhet**. När du utformar en arkitektur som det är viktigt att varje komponent kan skala på egen hand, antingen eftersom du måste behandla mer data eller eftersom du vill ha en större geografisk omfattning (eller båda!). Tack och lov, uppnå en komplicerad uppgift är en **NYCKELFÄRDIGT upplevelse** med Cosmos DB.

Har stöd för cosmos DB [dynamisk partitionering](https://azure.microsoft.com/blog/10-things-to-know-about-documentdb-partitioned-collections/) out-of-the-box genom att automatiskt skapa partitioner baserat på en viss **partitionsnyckel** (definierat som ett av attributen i dina dokument). Definiera rätt Partitionsnyckeln måste göras vid designtillfället och i åtanke de [metodtips](../cosmos-db/partition-data.md#designing-for-partitioning) tillgängliga; när det gäller en upplevelsen, din strategi för partitionering måste vara lika justerade med hur du frågar (läsningar inom samma partitionen är önskvärt) och skriva (undvika ”aktiva punkter” genom att sprida skrivningar på flera partitioner). Vissa alternativ är: partitioner baserat på en temporal nyckel (dag/månad/vecka), med innehåll kategori av geografisk region, av användaren. Det beror på hur du ska fråga efter data och visa den i din-upplevelsen. 

En intressant punkt värt att nämna att Cosmos DB kör dina frågor (inklusive [mängder](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)) för alla partitioner transparent, behöver du inte lägga till några logik som datamängden växer.

Med tiden kan du slutligen växer i trafik och förbrukning av nätverksresurser (mätt i [RUs](request-units.md), eller begära enheter) kommer att öka. Du kan läsa och skriva oftare när din användarbas växer och de kommer att börja skapa och läsa mer innehåll. möjligheten för **skala din genomströmning** är viktigt. Det är enkelt att öka din RUs, du kan göra det med några få klick på Azure-portalen eller genom [utfärda kommandon via API: et](https://docs.microsoft.com/rest/api/cosmos-db/replace-an-offer).

![Skala upp och definiera en partitionsnyckel](./media/social-media-apps/social-media-apps-scaling.png)

Vad händer om saker får bättre och användare från en annan region, land eller kontinent, Lägg märke till din plattform och börja använda det en bra oväntat!

Men vänta... du inser snart sina erfarenheter din plattform inte är den optimala; de är så långt från din operativa region att svarstiden är förskräckliga ut och du givetvis inte vill att de ska avslutas. Om bara det uppstod ett enkelt sätt att **utöka räckhåll globala**... men det finns!

Cosmos DB kan du [replikeras dina data globalt](../cosmos-db/tutorial-global-distribution-sql-api.md) och transparent med ett par klick och automatiskt välja bland tillgängliga regioner från din [klientkod](../cosmos-db/tutorial-global-distribution-sql-api.md). Det innebär också att du har [flera redundans regioner](regional-failover.md). 

När du replikerar data globalt, måste du se till att klienterna kan dra nytta av den. Om du använder en klientdel web eller åtkomst till API: er från mobila klienter kan du distribuera [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) och klona din Azure Apptjänst på alla önskade regioner, med en prestanda-konfiguration för att stödja dina utökade globala täckning. När klienterna har åtkomst till din klientdel eller API: er, kommer att dirigeras till den närmaste App Service som i sin tur ska ansluta till den lokala Cosmos-DB-repliken.

![Lägger till global täckning i din sociala plattform](./media/social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>Sammanfattning
Den här artikeln försöker sprida vissa enstaka i alternativen för att skapa sociala nätverk helt på Azure med prisvärda tjänster och tillhandahålla goda resultat genom att använda en flera lager lagring lösningen och data fördelning som kallas ”stege”.

![Diagram över interaktion mellan Azure-tjänster för sociala nätverk](./media/social-media-apps/social-media-apps-azure-solution.png)

Sanningen är att det finns ingen silver punkt för den här typen av scenarier, är det samverkan som skapats av en kombination av bra tjänster som ger oss möjlighet att skapa en bra upplevelse: hastighet och Azure Cosmos DB frihet att tillhandahålla en bra sociala program i tillgångsinformation bakom en förstklassig search-lösning som Azure Search Apptjänster för Azure som värd för program som inte även språkoberoende men kraftfull bakgrundsprocesser och utbyggbara Azure Storage och Azure SQL Database flexibilitet för lagring stora mängder data och analytiska kraften i Azure Machine Learning skapa kunskap och intelligence som kan ge feedback till dina processer och hjälp oss att leverera rätt innehåll till rätt användare.

## <a name="next-steps"></a>Nästa steg
Läs mer om användningsfall för Cosmos-DB i [vanliga Cosmos-DB användningsfall](use-cases.md).
