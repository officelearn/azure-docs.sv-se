---
title: 'Designmönster för Azure Cosmos DB: Appar för sociala medier'
description: Läs mer om ett designmönster för sociala nätverk genom att utnyttja flexibiliteten för lagring i Azure Cosmos DB och andra Azure-tjänster.
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: maquaran
ms.openlocfilehash: 36b77ff6666c2c8b0d27cbdc8552ade15b21d005
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100371"
---
# <a name="going-social-with-azure-cosmos-db"></a>Socialt med Azure Cosmos DB

Att leva i ett enormt sammankopplade society innebär att vid en viss tidpunkt i vardagen du bli en del av en **socialt nätverk**. Du kan använda sociala nätverk för att hålla kontakten med vänner, kolleger, familj, eller ibland att dela din passion med personer med gemensamma intressen.

Som tekniker eller utvecklare kan du kanske har funderat över hur dessa nätverk lagra och sammankoppling dina data. Eller du kanske har även har gett för att skapa eller skapa ett nytt sociala nätverk för en specifik nischmarknader marknad. Det är då betydande frågan: Hur lagras alla dessa data?

Anta att du skapar en ny och shiny sociala nätverk där användarna kan publicera artiklar med relaterade media, t.ex. bilder, videor och musik. Användare kan kommentera inlägg och ge punkter för betygsättning. Det blir en feed med inlägg som användarna ska se och interagera med på landningssidan för webbplats. Den här metoden ljud inte komplexa på först, men för enkelhetens skull, stoppa vi det. (Du gräver anpassade flöden som påverkas av relationer, men det är mer omfattande än målet med den här artikeln.)

Så, hur du för att lagra dessa data och var?

Du kan ha upplevelse på SQL-databaser eller har begreppet [relationella modellering av data](https://en.wikipedia.org/wiki/Relational_model). Du kan börja rita något på följande sätt:

![Diagram som illustrerar en relativ relationsmodellen](./media/social-media-apps/social-media-apps-sql.png)

En perfekt normaliserade och snyggt datastruktur... som inte kan skalas.

Få inte mig fel, jag har jobbat med SQL-databaser mitt arbete. De är bra, men som alla plattformar för mönster, tips och programvara, den är inte perfekt för alla scenarier.

Varför inte SQL det bästa valet i det här scenariot? Låt oss titta på strukturen för ett enskilt inlägg. Om jag vill visa inlägget i en webbplats eller ett program skulle jag behöva kan en fråga med... genom att gå med åtta tables(!) bara för att visa en enda post. Bild nu en dataström med inlägg som dynamiskt läsa in och visas på skärmen och du kan se där jag.

Du kan använda en enorma SQL-instans med tillräckligt med för att lösa tusentals frågor med många kopplingar för att leverera ditt innehåll. Men varför skulle du, när det finns en enklare lösning?

## <a name="the-nosql-road"></a>NoSQL-väg

Den här artikeln vägleder dig i din sociala plattform datamodellering med Azures NoSQL-databas [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) kostnadseffektivt. Du får också information du hur du använder andra Azure Cosmos DB-funktioner som den [Gremlin-API](../cosmos-db/graph-introduction.md). Med hjälp av en [NoSQL](https://en.wikipedia.org/wiki/NoSQL) metoden kan lagra data i JSON-format och tillämpa [denormalisering](https://en.wikipedia.org/wiki/Denormalization), tidigare komplicerad inlägget kan omvandlas till en enda [dokumentet](https://en.wikipedia.org/wiki/Document-oriented_database):

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

Och det kan vara hämtat med en enda fråga, och inga kopplingar. Den här frågan är mycket enkel och problemfri och budget-wise, den kräver färre resurser för att få ett bättre resultat.

Azure Cosmos DB ser till att alla egenskaper indexeras med automatisk indexering. Automatisk indexering kan även vara [anpassade](index-policy.md). Schemafria-metoden kan vi lagra dokument med olika och dynamiska strukturer. Morgon kanske inlägg till har en lista över kategorier eller hash som är associerade med dem? Cosmos DB kommer att hantera nya dokument med tillagda attribut utan extra arbete krävs av oss.

Kommentarer om ett inlägg kan behandlas som andra inlägg med en överordnad-egenskap. (Den här övningen förenklar din objektmappningen.)

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

Det är bara för att skapa dokument som kan innehålla en lista över post ID: N med en viss relevans ordning för att skapa flöden:

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

Du kan ha en ”senaste” ström med inlägg sorterade efter skapandedatum. Eller du kan ha en ”hetaste” ström med dessa inlägg med mer likes under de senaste 24 timmarna. Du kan även implementera en anpassad dataström för varje användare baserat på logik som följare och intressen. Det vore fortfarande en lista över inlägg. Det är en fråga om hur du skapar dessa listor, men prestandan läsning förblir obehindrad. När du skaffar en av de här listorna du utfärda en enskild fråga till Cosmos DB med hjälp av den [i operatorn](how-to-sql-query.md#WhereClause) att hämta sidor för inlägg i taget.

Feed strömmar kan byggas med [Azure App Services](https://azure.microsoft.com/services/app-service/) background processer: [Webjobs](../app-service/webjobs-create.md). När en post skapas behandling i bakgrunden kan aktiveras med hjälp av [Azure Storage](https://azure.microsoft.com/services/storage/) [köer](../storage/queues/storage-dotnet-how-to-use-queues.md) och Webjobs som utlösts med hjälp av den [Azure Webjobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki), implementerar den Publicera spridning i strömmar som baseras på en egen anpassad logik.

Punkter och gilla-markeringar över ett inlägg kan bearbetas i ett uppskjutet sätt med samma metod för att skapa en konsekvent miljö.

Det är svårare följare. Cosmos DB har en storleksgräns för dokumentet och läsning/skrivning stora dokument kan påverka skalbarheten i ditt program. Så kan du tycker om att lagra följare som ett dokument med den här strukturen:

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

Den här strukturen kan fungera för en användare med några tusentals följare. Om vissa kändisar kopplar blocken, men den här metoden leder till en stor dokumentstorlek och det kanske så småningom nått gränsen för dokument-storlek.

Du kan använda en blandad metod för att lösa problemet. Som en del av användarstatistik dokumentet kan du lagra antal följare:

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

Du kan lagra faktiska diagram över följare med Azure Cosmos DB [Gremlin-API](../cosmos-db/graph-introduction.md) att skapa [hörn](http://mathworld.wolfram.com/GraphVertex.html) för varje användare och [kanter](http://mathworld.wolfram.com/GraphEdge.html) som underhåller den ”en-följer-B” relationer. Du kan hämta följare med en viss användare och skapa mer komplexa frågor för att föreslå personer i vanliga med Gremlin-API. Om du lägger till diagrammet innehåll kategorier som personer som eller dra nytta av kan börja du vävning upplevelser som innehåller smart identifiering av innehåll, föreslå innehåll att de personerna som du följer som eller söka efter personer som du kan ha mycket på.

Användarstatistik dokumentet kan användas för att skapa kort i Användargränssnittet eller snabb profil förhandsversioner.

## <a name="the-ladder-pattern-and-data-duplication"></a>”Ladder” mönster och data duplicering

Det är många förekomster av en användare som du kanske har märkt i JSON-dokumentet som refererar till ett inlägg. Och du skulle ha gissa rätt dessa dubbletter innebär att den information som beskriver en användare får den här denormalisering kan finnas i mer än en plats.

Om du vill tillåta frågor körs snabbare debiteras duplicerade data. Problem med detta är att om du vill hitta alla aktiviteter av någon åtgärd, en användares data ändras och, användaren någonsin gjorde och uppdatera alla. Inte låter praktiskt, direkt?

Du kommer att lösa det genom att identifiera viktiga punkter för en användare som du visar i ditt program för varje aktivitet. Om du visuellt visar ett inlägg i ditt program och visar bara Skaparens namn och bild, varför lagra alla användarens data i attributet ”createdBy”? Om du är för varje kommentar du visa användarens bild, behöver du inte verkligen resten av användarens information. Det är något jag kallar ”Ladder mönstret” blir var inblandad.

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

Genom att titta på den här informationen, kan du snabbt identifiera som är viktig information och som inte är, vilket skapar en ”Ladder”:

![Diagram över ett ladder mönster](./media/social-media-apps/social-media-apps-ladder.png)

Det minsta steget kallas en UserChunk minimal typ av information som identifierar en användare och det används för datadeduplicering. Genom att minska storleken på duplicerade data till den information som du ”visa”, kan du minska risken för stora uppdateringar.

Det mellersta steget kallas för användaren. Det är de fullständiga data som ska användas på de flesta prestanda-beroende frågor på Cosmos DB, den mest använda och kritiska. Den innehåller den information som representeras av en UserChunk.

Den största är den utökade användaren. Det omfattar kritiska användarinformationen och andra data som inte behöver läsas snabbt eller har eventuell användning, t.ex. Logga in igen. Dessa data kan lagras utanför Cosmos-DB i Azure SQL Database eller Azure Storage-tabeller.

Varför skulle du dela upp användaren och även spara informationen på olika platser? Eftersom från en prestanda synvinkel, desto större blir dokument, desto costlier frågor. Behåll dokument slimmade med rätt information för att göra alla prestanda-beroende frågor för ditt sociala nätverk. Store annan extra information för slutlig scenarier som fullständig profil redigeringar, inloggningar och datautvinning för användningsanalys och Stordata initiativ. Du egentligen inte bryr dig om datainsamling för datautvinning är långsammare, eftersom den körs på Azure SQL Database. Du har gäller dock att användarna får en snabb och smidig upplevelse. En användare som lagras på Cosmos DB ser ut som den här koden:

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

Redigerar uppstår där attributet segment påverkas, hittar du enkelt berörda dokumenten. Använd bara frågor som pekar på indexerade attribut, till exempel `SELECT * FROM posts p WHERE p.createdBy.id == "edited_user_id"`, och sedan uppdatera segment.

## <a name="the-search-box"></a>Sökrutan

Användare kommer som tur är kan generera mycket innehåll. Och du bör kunna ge möjlighet att söka efter innehåll som inte kanske är direkt i deras innehåll strömmar kanske eftersom du inte följer skaparna, eller kanske du försöker bara att hitta gamla inlägg har sex månader sedan.

Eftersom du använder Azure Cosmos DB kan du enkelt kan implementera ett Sök-motorn med [Azure Search](https://azure.microsoft.com/services/search/) på några få minuter utan att skriva någon kod än sökprocess och Användargränssnittet.

Varför är den här processen så enkelt?

Azure Search implementerar det kallar [indexerare](https://msdn.microsoft.com/library/azure/dn946891.aspx), bakgrund bearbetar den hooken i ditt datalager och automagically lägga till, uppdatera eller ta bort dina objekt i index. De stöder en [Azure SQL Database indexerare](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/), [indexerare på Azure Blobs](../search/search-howto-indexing-azure-blob-storage.md) och testningskostnader [Azure Cosmos DB-indexerare](../search/search-howto-index-documentdb.md). Övergång av information från Cosmos DB till Azure Search är enkelt. Båda teknikerna lagra information i JSON-format, så du behöver bara [skapa ditt Index](../search/search-create-index-portal.md) och mappa attribut från dina dokument som du vill ska indexeras. Klart! Beroende på storleken på dina data kommer att finnas allt innehåll som ska genomsökas vid inom några minuter med den bästa lösningen för sökning som en tjänst i molninfrastrukturen.

Mer information om Azure Search kan du besöka den [Hitchhiker's Guide till Search](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/).

## <a name="the-underlying-knowledge"></a>Den underliggande kunskapen

När du lagrar det här innehållet som ökar och ökar varje dag, kanske tänka: Vad kan jag göra med den här strömmen av information från Mina användare?

Det är enkelt att svaret: Placera den så att den fungerar och lär dig från den.

Men det kan du lära dig? Några enkla exempel [attitydanalys](https://en.wikipedia.org/wiki/Sentiment_analysis), innehåll rekommendationer baserat på användarens inställningar eller även en automatiserad content moderator som ser till att det innehåll som publicerats av sociala nätverket är säker för familjen.

Nu när jag något för dig låst, kommer du förmodligen tycker du behöver en doktorsexamen i matematiska science att extrahera dessa mönster och information från enkla databaser och filer, men du kommer att fel.

[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/)ingår i den [Cortana Intelligence Suite](https://social.technet.microsoft.com/wiki/contents/articles/36688.introduction-to-cortana-intelligence-suite.aspx), är en fullständigt hanterad molntjänst som låter dig skapa arbetsflöden med hjälp av algoritmer i ett enkelt dra och släpp-gränssnitt, code algoritmerna i [ R](https://en.wikipedia.org/wiki/R_\(programming_language\)), eller använda en del av redan skapats och är redo att använda API: er som: [Textanalys](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [Content Moderator, eller [rekommendationer](https://gallery.azure.ai/Solution/Recommendations-Solution).

Om du vill åstadkomma något av dessa scenarier för Machine Learning kan du använda [Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) att mata in information från olika källor. Du kan också använda [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) att bearbeta informationen och skapa utdata som kan bearbetas av Azure Machine Learning.

Ett annat tillgängliga alternativ är att använda [Azure Cognitive Services](https://www.microsoft.com/cognitive-services) att analysera innehåll, inte bara kan du lättare att förstå dem användarna (genom att analysera de skriver med [Textanalys](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)), men du kan också identifiera oönskade eller mogen innehåll och agera utifrån dessa med [API för visuellt innehåll](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). Cognitive Services innehåller många out-of the box-lösningar som inte kräver att alla typer av Machine Learning kunskaper.

## <a name="a-planet-scale-social-experience"></a>En global skala-upplevelsen

Det finns en sista, men inte minst viktiga artikeln jag måste åtgärda: **skalbarhet**. När du utformar en arkitektur för ska varje komponent skalas på egen hand. Du kommer behöva bearbeta mer data och du vill ha en större geografiska täckning. Testningskostnader uppnå båda uppgifterna är en **nyckelfärdiga** med Cosmos DB.

Cosmos DB stöder dynamisk partitionering out-of the box. Skapar automatiskt partitioner som baseras på en viss **partitionsnyckel**, som har definierats som ett attribut i dina dokument. Definiera rätt partitionsnyckel måste göras vid designtillfället. Mer information finns i [partitionering i Azure Cosmos DB](partitioning-overview.md).

För en social upplevelse, måste du justera partitioneringsstrategin med hur du frågar och skriva. (Till exempel läsningar inom samma partition är önskvärt, och undvika ”hotspots” genom att sprida skrivningar på flera partitioner.) Vissa alternativ är: partitioner baserat på en temporal key (dag/månad/vecka), av Innehållskategori, geografisk region eller användare. Allt beror på hur du fråga efter data och visa data i din-upplevelsen.

Cosmos DB kommer att köra dina frågor (inklusive [aggregeringar](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)) för alla partitioner transparent, så du behöver inte lägga till någon logik när dina data växer.

Med tiden, kommer du så småningom växer i trafik och din resursförbrukning (mätt i [ru: er](request-units.md), eller programbegäran) ökar. Du kan läsa och skriva ofta mer när användarbasen växer. Användarbasen börjar skapa och läsa mer innehåll. Så möjligheten för **skala ditt dataflöde** är viktigt. Det är enkelt att öka din ru: er. Du kan göra det med några få klick på Azure portal eller genom [utfärda kommandon via API: et](https://docs.microsoft.com/rest/api/cosmos-db/replace-an-offer).

![Skala upp och definiera en partitionsnyckel](./media/social-media-apps/social-media-apps-scaling.png)

Vad händer om saker hela tiden blir bättre? Anta att användare från en annan region, land eller kontinent Lägg märke till din plattform och börja använda den. Vad bra överraskning!

Men vänta! Du upptäcker snart sina erfarenheter din plattform inte optimalt. De inte är så länge din operational region att svarstiden är förskräckliga ut. Naturligtvis du inte dem att avsluta. Om bara det uppstod ett enkelt sätt att **utöka din globala räckvidd**? Det finns!

Cosmos DB kan du [replikera data globalt](../cosmos-db/tutorial-global-distribution-sql-api.md) och transparent med bara några klick och automatiskt välja bland de tillgängliga regionerna från din [klientkod](../cosmos-db/tutorial-global-distribution-sql-api.md). Den här processen innebär också att du kan ha [flera regioner för redundans](high-availability.md).

När du replikerar dina data globalt måste du se till att klienterna kan dra nytta av den. Om du använder en webbservergrupp eller åtkomst till API: er från mobila klienter som du kan distribuera [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) och klona Azure App Service på alla önskade regioner med hjälp av en prestandakonfiguration för för dina utökade globala täckning. När klienterna kommer åt din klientdel eller API: er, kommer att dirigeras till den närmaste App Service, som i sin tur kommer att ansluta till den lokala repliken av Cosmos DB.

![Att lägga till global täckning på sociala plattformen](./media/social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>Sammanfattning

Den här artikeln belyses vissa i alternativen för att skapa sociala nätverk helt på Azure med låg kostnad tjänster. Den levererar resultat genom att främja användning av en flera lager storage-lösningen och data-distribution kallas ”Ladder”.

![Diagram över interaktion mellan Azure-tjänster för sociala nätverk](./media/social-media-apps/social-media-apps-azure-solution.png)

Sanningen är att det finns inga universallösning för den här typen av scenarier. Det är synergin som skapats av en kombination av fantastiska tjänster som gör att vi kan ge bra upplevelser: hastighet och friheten i Azure Cosmos DB att tillhandahålla en bra sociala program, intelligens bakom en förstklassig söklösning som Azure Search i flexibiliteten i Azure App Services som värd för inte även språkoberoende program men kraftfull bakgrundsprocesser och utbyggbara Azure Storage och Azure SQL Database för att lagra stora mängder data och analytiska kraften i Azure Machine Learning för att Skapa kunskaper och insikter som kan ge feedback till dina processer och hjälp oss att leverera rätt innehåll till rätt användare.

## <a name="next-steps"></a>Nästa steg

Läs mer om användningsfall för Cosmos DB i [vanliga Cosmos-DB användningsfall](use-cases.md).
