---
title: 'Designmönster för Azure Cosmos DB: Appar för sociala medier'
description: Lär dig mer om ett designmönster för sociala nätverk genom att utnyttja lagringsflexibiliteten för Azure Cosmos DB och andra Azure-tjänster.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: maquaran
ms.openlocfilehash: 8428e417f5f86edca77edae6ca4b7ef84e5ff425
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73827299"
---
# <a name="going-social-with-azure-cosmos-db"></a>Going social med Azure Cosmos DB

Att leva i ett massivt sammanlänkat samhälle innebär att du någon gång i livet blir en del av ett **socialt nätverk.** Du använder sociala nätverk för att hålla kontakten med vänner, kollegor, familj eller ibland för att dela din passion med personer med gemensamma intressen.

Som ingenjörer eller utvecklare kanske du har undrat hur dessa nätverk lagrar och kopplar samman dina data. Eller du kanske till och med har fått i uppdrag att skapa eller arkitekt ett nytt socialt nätverk för en viss nischmarknad. Det är då den viktiga frågan uppstår: Hur lagras alla dessa data?

Anta att du skapar ett nytt och skinande socialt nätverk där användarna kan publicera artiklar med relaterade medier, till exempel bilder, videor eller till och med musik. Användare kan kommentera inlägg och ge poäng för betyg. Det kommer att finnas ett flöde av inlägg som användarna kommer att se och interagera med på huvudwebbplatsens målsida. Denna metod låter inte komplicerat i början, men för enkelhetens skull, låt oss sluta där. (Du kan gräva i anpassade användarflöden som påverkas av relationer, men det går utöver målet med den här artikeln.)

Så, hur lagrar du dessa data och var?

Du kan ha erfarenhet av SQL-databaser eller har en uppfattning om [relationsmodellering av data](https://en.wikipedia.org/wiki/Relational_model). Du kan börja rita något på följande sätt:

![Diagram som illustrerar en relativ relationsmodell](./media/social-media-apps/social-media-apps-sql.png)

En perfekt normaliserad och vacker datastruktur... som inte skalas.

Missförstå mig inte, jag har arbetat med SQL-databaser hela mitt liv. De är bra, men som alla mönster, praxis och mjukvaruplattform, det är inte perfekt för varje scenario.

Varför är inte SQL det bästa valet i det här scenariot? Låt oss titta på strukturen i ett enda inlägg. Om jag ville visa inlägget i en webbplats eller ett program, skulle jag behöva göra en fråga med ... genom att gå med åtta tabeller (!) bara för att visa ett enda inlägg. Nu bild en ström av inlägg som dynamiskt ladda och visas på skärmen, och du kan se vart jag ska.

Du kan använda en enorm SQL-instans med tillräckligt med ström för att lösa tusentals frågor med många kopplingar för att tjäna ditt innehåll. Men varför skulle du, när en enklare lösning finns?

## <a name="the-nosql-road"></a>Den NoSQL vägen

Den här artikeln hjälper dig att modellera din sociala plattforms data med Azures NoSQL-databas [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) på ett kostnadseffektivt sätt. Den berättar också hur du använder andra Azure Cosmos DB-funktioner som [Gremlin API](../cosmos-db/graph-introduction.md). Med hjälp av en [NoSQL-metod,](https://en.wikipedia.org/wiki/NoSQL) lagring av data, i JSON-format och tillämpning av [denormalisering](https://en.wikipedia.org/wiki/Denormalization)kan det tidigare komplicerade inlägget omvandlas till ett enda [dokument:](https://en.wikipedia.org/wiki/Document-oriented_database)

    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["https://myfirstimage.png","https://mysecondimage.png"],
        "videos":[
            {"url":"https://myfirstvideo.mp4", "title":"The first video"},
            {"url":"https://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"https://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"https://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

Och det kan fås med en enda fråga, och utan kopplingar. Den här frågan är mycket enkel och okomplicerad, och, budgetmässigt, kräver det mindre resurser för att uppnå ett bättre resultat.

Azure Cosmos DB ser till att alla egenskaper indexeras med dess automatiska indexering. Den automatiska indexeringen kan även [anpassas.](index-policy.md) Den schemafria metoden gör att vi kan lagra dokument med olika och dynamiska strukturer. Kanske i morgon vill du inlägg att ha en lista över kategorier eller hashtags i samband med dem? Cosmos DB kommer att hantera de nya dokumenten med de tillagda attributen utan extra arbete som krävs av oss.

Kommentarer till ett inlägg kan behandlas som andra inlägg med en överordnad egendom. (Den här metoden förenklar objektmappningen.)

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

Och alla sociala interaktioner kan lagras på ett separat objekt som räknare:

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

Att skapa flöden är bara en fråga om att skapa dokument som kan innehålla en lista med post-ID med en viss relevansordning:

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

Du kan ha en "senaste" ström med inlägg ordnade efter skapandedatum. Eller så kan du ha en "hetaste" ström med dessa inlägg med fler gillar under de senaste 24 timmarna. Du kan även implementera en anpassad ström för varje användare baserat på logik som anhängare och intressen. Det skulle fortfarande vara en lista över tjänster. Det är en fråga om hur man bygger dessa listor, men läsprestanda förblir obehindrat. När du har skaffat en av dessa listor utfärdar du en enda fråga till Cosmos DB med [nyckelordet IN](sql-query-keywords.md#in) för att få sidor med inlägg i taget.

Flödesströmmarna kan byggas med hjälp av [Azure App Services](https://azure.microsoft.com/services/app-service/) bakgrundsprocesser: [Webjobs](../app-service/webjobs-create.md). När ett inlägg har skapats kan bakgrundsbearbetning utlösas med hjälp av [Azure](https://azure.microsoft.com/services/storage/) [Storage-köer](../storage/queues/storage-dotnet-how-to-use-queues.md) och webbjobb som utlöses med hjälp av [Azure Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)och implementerar postspridningen inuti strömmar baserat på din egen anpassade logik.

Poäng och gilla-markeringar över ett inlägg kan bearbetas på ett uppskjutet sätt med samma teknik för att skapa en så småningom konsekvent miljö.

Anhängare är svårare. Cosmos DB har en dokumentstorleksgräns och läsning/skrivning av stora dokument kan påverka programmets skalbarhet. Så du kanske funderar på att lagra anhängare som ett dokument med denna struktur:

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

Den här strukturen kan fungera för en användare med några tusen följare. Om någon kändis går med i leden, dock kommer detta tillvägagångssätt leda till en stor dokumentstorlek, och det kan så småningom slå dokumentet storlekstak.

För att lösa detta problem kan du använda en blandad metod. Som en del av användarstatistikdokumentet kan du lagra antalet följare:

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

Du kan lagra det faktiska diagrammet med följare med Azure Cosmos DB [Gremlin API](../cosmos-db/graph-introduction.md) för att skapa [hörn](http://mathworld.wolfram.com/GraphVertex.html) för varje användare och [kanter](http://mathworld.wolfram.com/GraphEdge.html) som upprätthåller "A-följer-B"-relationer. Med Gremlin API kan du hämta följare till en viss användare och skapa mer komplexa frågor för att föreslå personer gemensamt. Om du lägger till innehållskategorier som andra gillar eller gillar i diagrammet kan du börja väva upplevelser som innehåller identifiering av smart innehåll, vilket föreslår innehåll som de personer du följer gillar eller hittar personer som du kan ha mycket gemensamt med.

Dokumentet Användarstatistik kan fortfarande användas för att skapa kort i användargränssnittet eller snabbprofilförhandsgranskningar.

## <a name="the-ladder-pattern-and-data-duplication"></a>"Ladder"-mönstret och datadubbel

Som du kanske har märkt i JSON-dokumentet som refererar till ett inlägg, finns det många förekomster av en användare. Och du skulle ha gissat rätt, dessa dubbletter innebär att den information som beskriver en användare, med tanke på denna denormalisering, kan finnas på mer än ett ställe.

Om du vill tillåta snabbare frågor ådrar du dig dataduplicering. Problemet med den här bieffekten är att om en användares data ändras genom någon åtgärd måste du hitta alla aktiviteter som användaren någonsin har gjort och uppdatera dem alla. Det låter inte praktiskt, eller hur?

Du ska lösa det genom att identifiera nyckelattributen för en användare som du visar i ditt program för varje aktivitet. Om du visuellt visar ett inlägg i ditt program och visar bara skaparens namn och bild, varför lagra alla användarens data i attributet "createdBy"? Om du för varje kommentar bara visar användarens bild behöver du egentligen inte resten av användarens information. Det är där något jag kallar "Ladder mönster" blir inblandad.

Låt oss ta användarinformation som ett exempel:

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

Genom att titta på denna information kan du snabbt upptäcka vilken som är kritisk information och som inte är, vilket skapar en "Ladder":

![Diagram över ett stegmönster](./media/social-media-apps/social-media-apps-ladder.png)

Det minsta steget kallas en UserChunk, den minimala information som identifierar en användare och den används för dataduplicering. Genom att minska den duplicerade datastorleken till endast den information du "visar" minskar du risken för massiva uppdateringar.

Det mellersta steget kallas användaren. Det är alla data som kommer att användas på de flesta prestandaberoende frågor på Cosmos DB, den mest åtkomsttillgångna och kritiska. Den innehåller den information som representeras av en UserChunk.

Den största är den utökade användaren. Den innehåller viktig användarinformation och andra data som inte behöver läsas snabbt eller har slutlig användning, till exempel inloggningsprocessen. Dessa data kan lagras utanför Cosmos DB, i Azure SQL Database eller Azure Storage Tables.

Varför skulle du dela användaren och även lagra denna information på olika ställen? För ur prestandasynpunkt, ju större dokument, desto dyrare frågor. Håll dokumenten smala, med rätt information för att göra alla dina prestandaberoende frågor för ditt sociala nätverk. Lagra annan extra information för eventuella scenarier som fullständiga profilredigeringar, inloggningar och datautvinning för användningsanalys och Big Data-initiativ. Du bryr dig verkligen inte om datainsamlingen för datautvinning är långsammare, eftersom den körs på Azure SQL Database. Du har dock bekymmer om att användarna har en snabb och smal upplevelse. En användare som lagras på Cosmos DB skulle se ut så här:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"\@john"
    }

Och ett inlägg skulle se ut:

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
            "id":"dse4-qwe2-ert4-aad2",
            "username":"johndoe"
        }
    }

När en redigering uppstår där ett segmentattribut påverkas kan du enkelt hitta de berörda dokumenten. Använd bara frågor som pekar på de indexerade attributen, till exempel `SELECT * FROM posts p WHERE p.createdBy.id == "edited_user_id"`, och uppdatera sedan segmenten.

## <a name="the-search-box"></a>Sökrutan

Användare kommer att generera, lyckligtvis, mycket innehåll. Och du bör kunna ge möjlighet att söka och hitta innehåll som kanske inte är direkt i deras innehåll strömmar, kanske för att du inte följer skaparna, eller kanske du bara försöker hitta det gamla inlägget du gjorde sex månader sedan.

Eftersom du använder Azure Cosmos DB kan du enkelt implementera en sökmotor med [Azure Cognitive Search](https://azure.microsoft.com/services/search/) på några minuter utan att skriva någon kod, förutom sökprocessen och användargränssnittet.

Varför är denna process så lätt?

Azure Cognitive Search implementerar vad de kallar [Indexerare,](https://msdn.microsoft.com/library/azure/dn946891.aspx)bakgrundsprocesser som ansluts i dina datadatabaser och automatiskt lägger till, uppdaterar eller tar bort dina objekt i indexen. De stöder en [Azure SQL Database indexerare](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/), [Azure Blobs indexerare](../search/search-howto-indexing-azure-blob-storage.md) och tack och lov, [Azure Cosmos DB indexerare](../search/search-howto-index-documentdb.md). Övergången av information från Cosmos DB till Azure Cognitive Search är enkel. Båda teknikerna lagrar information i JSON-format, så du behöver bara [skapa ditt index](../search/search-create-index-portal.md) och mappa attributen från dina dokument som du vill indexera. Klart! Beroende på storleken på dina data kommer allt innehåll att vara tillgängligt för sökning på inom några minuter av den bästa Search-as-a-Service-lösningen i molninfrastruktur.

Om du vill ha mer information om Azure Cognitive Search finns i [Liftarens guide till sökning](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/).

## <a name="the-underlying-knowledge"></a>Den underliggande kunskapen

När du har lagrat allt detta innehåll som växer och växer varje dag, kanske du upptäcker att tänka: Vad kan jag göra med all denna ström av information från mina användare?

Svaret är enkelt: Sätt det att fungera och lära av det.

Men vad kan du lära dig? Några enkla exempel är [sentimentanalys,](https://en.wikipedia.org/wiki/Sentiment_analysis)innehållsrekommendationer baserade på en användares preferenser eller till och med en automatiserad innehållsmoderator som ser till att innehållet som publiceras av ditt sociala nätverk är säkert för familjen.

Nu när jag har fastnat, tror du förmodligen att du behöver lite doktorsexamen i matematik vetenskap för att extrahera dessa mönster och information ur enkla databaser och filer, men du skulle ha fel.

[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/), en del av [Cortana Intelligence Suite](https://social.technet.microsoft.com/wiki/contents/articles/36688.introduction-to-cortana-intelligence-suite.aspx), är en fullständigt hanterad molntjänst som låter dig skapa arbetsflöden med hjälp av algoritmer i ett enkelt dra-och-släpp-gränssnitt, koda dina egna algoritmer i [R](https://en.wikipedia.org/wiki/R_\(programming_language\))eller använda några av de redan inbyggda och redo att använda API: er som: [Text Analytics](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [Content Moderator eller [Rekommendationer](https://gallery.azure.ai/Solution/Recommendations-Solution).

För att uppnå något av dessa Machine Learning-scenarier kan du använda [Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) för att få information från olika källor. Du kan också använda [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) för att bearbeta informationen och generera en utdata som kan bearbetas av Azure Machine Learning.

Ett annat tillgängligt alternativ är att använda [Azure Cognitive Services](https://www.microsoft.com/cognitive-services) för att analysera användarnas innehåll. inte bara kan du förstå dem bättre (genom att analysera vad de skriver med [Text Analytics API),](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)men du kan också upptäcka oönskade eller mogna innehåll och agera därefter med [Computer Vision API](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). Cognitive Services innehåller många färdiga lösningar som inte kräver någon form av maskininlärningskunskap att använda.

## <a name="a-planet-scale-social-experience"></a>En social upplevelse i planetskala

Det finns en sista, men inte minst, viktig artikel jag måste ta itu med: **skalbarhet**. När du utformar en arkitektur bör varje komponent skalas på egen hand. Du kommer så småningom att behöva bearbeta mer data, eller du kommer att vilja ha en större geografisk täckning. Tack och lov, att uppnå båda uppgifterna är en **nyckelfärdig upplevelse** med Cosmos DB.

Cosmos DB stöder dynamisk partitionering out-of-the-box. Det skapar automatiskt partitioner baserat på en viss **partitionsnyckel**, som definieras som ett attribut i dina dokument. Definiera rätt partitionsnyckel måste göras vid designtillfället. Mer information finns [i Partitionering i Azure Cosmos DB](partitioning-overview.md).

För en social upplevelse måste du anpassa partitioneringsstrategin till hur du frågar och skriver. (Till exempel läsningar inom samma partition är önskvärda och undviker "aktiva punkter" genom att sprida skrivningar på flera partitioner.) Några alternativ är: partitioner baserade på en temporal nyckel (dag/månad/vecka), efter innehållskategori, geografisk region eller efter användare. Allt beror egentligen på hur du ska fråga data och visa data i din sociala upplevelse.

Cosmos DB kör dina frågor (inklusive [aggregat)](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)över alla partitioner transparent, så du behöver inte lägga till någon logik när dina data växer.

Med tiden kommer du så småningom att växa i trafiken och din resursförbrukning (mätt i [ru: er](request-units.md), eller begärandeenheter) kommer att öka. Du kommer att läsa och skriva oftare när din användarbas växer. Användarbasen börjar skapa och läsa mer innehåll. Så förmågan **att skala din genomströmning** är avgörande. Det är enkelt att öka dina ru:er. Du kan göra det med några klick på Azure-portalen eller genom [att utfärda kommandon via API](https://docs.microsoft.com/rest/api/cosmos-db/replace-an-offer).

![Skala upp och definiera en partitionsnyckel](./media/social-media-apps/social-media-apps-scaling.png)

Vad händer om det blir bättre? Anta att användare från en annan region, ett annat land eller en annan kontinent märker din plattform och börjar använda den. Vilken stor överraskning!

Men vänta! Du inser snart att deras erfarenhet av din plattform inte är optimal. De är så långt borta från din operativa region att latensen är fruktansvärd. Du vill uppenbarligen inte att de ska sluta. Om det bara fanns ett enkelt sätt **att utöka din globala räckvidd?** Det finns det!

Cosmos DB kan du [replikera dina data globalt](../cosmos-db/tutorial-global-distribution-sql-api.md) och transparent med ett par klick och automatiskt välja bland de tillgängliga regionerna från din [klientkod](../cosmos-db/tutorial-global-distribution-sql-api.md). Den här processen innebär också att du kan ha [flera redundansregioner](high-availability.md).

När du replikerar dina data globalt måste du se till att dina klienter kan dra nytta av dem. Om du använder en webbklient eller använder API:er från mobila klienter kan du distribuera [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) och klona din Azure App Service i alla önskade regioner, med hjälp av en prestandakonfiguration för att stödja din utökade globala täckning. När dina klienter kommer åt frontend eller API: er dirigeras de till närmaste App Service, som i sin tur ansluter till den lokala Cosmos DB-repliken.

![Lägga till global täckning till din sociala plattform](./media/social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>Slutsats

Den här artikeln kastar lite ljus i alternativen att skapa sociala nätverk helt på Azure med billiga tjänster. Det ger resultat genom att uppmuntra användningen av en lagringslösning med flera lager och datadistribution som kallas "Ladder".

![Diagram över interaktion mellan Azure-tjänster för sociala nätverk](./media/social-media-apps/social-media-apps-azure-solution.png)

Sanningen är att det inte finns någon silverkula för den här typen av scenarier. Det är synergin som skapas av kombinationen av bra tjänster som gör att vi kan skapa fantastiska upplevelser: hastigheten och friheten hos Azure Cosmos DB för att tillhandahålla ett bra socialt program, intelligensen bakom en förstklassig söklösning som Azure Cognitive Search, flexibiliteten i Azure App Services för att vara värd för inte ens språkoberoende program utan kraftfulla bakgrundsprocesser och den utbyggbara Azure Storage och Azure SQL Database för lagring av enorma mängder data och den analytiska kraften i Azure Machine Learning till skapa kunskap och intelligens som kan ge feedback till dina processer och hjälpa oss att leverera rätt innehåll till rätt användare.

## <a name="next-steps"></a>Nästa steg

Mer information om användningsfall för Cosmos DB finns i [Vanliga användningsfall för Cosmos DB](use-cases.md).
