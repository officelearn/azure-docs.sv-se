---
title: Vanliga användningsfall och scenarier för Azure Cosmos DB
description: 'Lär dig mer om de fem vanligaste användningsfallen för Azure Cosmos DB: användargenererat innehåll, händelseloggning, katalogdata, användarinställningar och Sakernas Internet (IoT).'
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/21/2019
ms.openlocfilehash: de2bc551547706fb820813e57996e77bf49148d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888934"
---
# <a name="common-azure-cosmos-db-use-cases"></a>Vanliga användningsfall för Azure Cosmos DB
Den här artikeln innehåller en översikt över flera vanliga användningsfall för Azure Cosmos DB.  Rekommendationerna i den här artikeln fungerar som utgångspunkt när du utvecklar ditt program med Cosmos DB.   

När du har läst den här artikeln kan du svara på följande frågor: 

* Vilka är de vanligaste användningsfallen för Azure Cosmos DB?
* Vilka är fördelarna med att använda Azure Cosmos DB för återförsäljarprogram?
* Vilka är fördelarna med att använda Azure Cosmos DB som datalager för Sakernas Internet-system (IoT) ?
* Vilka är fördelarna med att använda Azure Cosmos DB för webb- och mobilappar?

## <a name="introduction"></a>Introduktion
[Azure Cosmos DB](../cosmos-db/introduction.md) är Microsofts globalt distribuerade databastjänst. Tjänsten är utformad så att kunderna kan elastiskt (och oberoende) skala dataflöde och lagring över valfritt antal geografiska regioner. Azure Cosmos DB är den första globalt distribuerade databastjänsten på marknaden idag som erbjuder omfattande [servicenivåavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/) som omfattar dataflöde, svarstid, tillgänglighet och konsekvens. 

Azure Cosmos DB är en global distribuerad databas med flera modeller som används i en mängd olika program och användningsfall. Det är ett bra val för alla [serverlösa](https://azure.com/serverless) program som behöver låga order-of-millisekunder svarstider, och måste skala snabbt och globalt. Den stöder flera datamodeller (nyckelvärde, dokument, grafer och columnar) och många API:er för dataåtkomst, inklusive [Azure Cosmos DB:s API för MongoDB](mongodb-introduction.md), [SQL API](documentdb-introduction.md), [Gremlin API](graph-introduction.md)och Tabeller [API](table-introduction.md) internt och på ett utbyggbart sätt. 

Följande är några attribut för Azure Cosmos DB som gör den väl lämpad för högpresterande program med globala ambitioner.

* Azure Cosmos DB partitionerar dina data för hög tillgänglighet och skalbarhet. Azure Cosmos DB erbjuder 99,99 % garanti för tillgänglighet, dataflöde, låg latens och konsekvens på alla konton med en region och alla konton med flera regioner med avslappnad konsekvens och 99,999 % lästillgänglighet på alla databaskonton med flera regioner.
* Azure Cosmos DB har SSD-säkerhetskopierad lagring med svarstider med låg latens.
* Azure Cosmos DB:s stöd för konsekvensnivåer som eventuellt, konsekvent prefix, session och begränsad föråldring möjliggör full flexibilitet och låg kostnads-till-prestanda-förhållande. Ingen databastjänst erbjuder lika mycket flexibilitet som Azure Cosmos DB i nivåkonsekvens. 
* Azure Cosmos DB har en flexibel datavänlig prismodell som mäter lagring och dataflöde oberoende av detta.
* Azure Cosmos DB:s reserverade dataflödesmodell gör att du kan tänka i termer av antal läsningar/skrivningar i stället för CPU/minne/IOPs för den underliggande maskinvaran.
* Azure Cosmos DB:s design gör att du kan skala till massiva begärandevolymer i samma ordning som biljoner begäranden per dag.

Dessa attribut är bra i webb, mobil, spel och IoT-program som behöver låga svarstider och behöver hantera enorma mängder läsningar och skrivningar.

## <a name="iot-and-telematics"></a>IoT och telematik
IoT-användningsfall delar ofta vissa mönster i hur de matar in, bearbetar och lagrar data.  För det första måste dessa system inta skurar av data från enhetssensorer av olika språk. Därefter bearbetar och analyserar dessa system strömmande data för att härleda insikter i realtid. Data arkiveras sedan till kall lagring för batchanalys. Microsoft Azure erbjuder omfattande tjänster som kan tillämpas för IoT-användningsfall, inklusive Azure Cosmos DB, Azure Event Hubs, Azure Stream Analytics, Azure Notification Hub, Azure Machine Learning, Azure HDInsight och Power BI. 

![Referensarkitektur för Azure Cosmos DB IoT](./media/use-cases/iot.png)

Skurar av data kan intas av Azure Event Hubs eftersom det erbjuder hög dataflödesdatainmatning med låg latens. Data som intas och som måste bearbetas för insikt i realtid kan kanaliseras till Azure Stream Analytics för analys i realtid. Data kan läsas in i Azure Cosmos DB för adhoc-frågor. När data har lästs in i Azure Cosmos DB är data redo att efterfrågas. Dessutom kan nya data och ändringar av befintliga data läsas på ändringsflödet. Ändringsfeed är en beständig, tilläggslogg som lagrar ändringar i Cosmos-behållare i sekventiell ordning. Alla data eller bara ändringar av data i Azure Cosmos DB kan användas som referensdata som en del av realtidsanalys. Dessutom kan data ytterligare förfinas och bearbetas genom att ansluta Azure Cosmos DB-data till HDInsight för gris-, hive- eller kart-/minska-jobb.  Förfinade data läses sedan in tillbaka till Azure Cosmos DB för rapportering.   

En exempel-IoT-lösning med Azure Cosmos DB, EventHubs och Storm finns i [lagringsplatsen hdinsight-storm-examples på GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Mer information om Azure-erbjudanden för IoT finns [i Skapa internet för dina saker](https://www.microsoft.com/en-us/internet-of-things). 

## <a name="retail-and-marketing"></a>Detaljhandel och marknadsföring
Azure Cosmos DB används flitigt i Microsofts egna e-handelsplattformar, som kör Windows Store och XBox Live. Det används också i detaljhandeln för att lagra katalogdata och för händelseinköp i orderbearbetningspipelor.

Katalogdataanvändningsscenarier innebär att lagra och fråga en uppsättning attribut för entiteter som personer, platser och produkter. Några exempel på katalogdata är användarkonton, produktkataloger, IoT-enhetsregister och strukturlistor. Attribut för dessa data kan variera och kan ändras med tiden för att passa programkrav.

Tänk dig ett exempel på en produktkatalog för en bildelar leverantör. Varje del kan ha sina egna attribut utöver de gemensamma attribut som alla delar delar. Dessutom kan attribut för en viss del ändras följande år när en ny modell släpps. Azure Cosmos DB stöder flexibla scheman och hierarkiska data, och därför är det väl lämpat för lagring av produktkatalogdata.

![Referensarkitektur för Azure Cosmos DB-butikskatalog](./media/use-cases/product-catalog.png)

Azure Cosmos DB används ofta för händelseinköp för att driva händelsedrivna arkitekturer med hjälp av dess [ändringsflödesfunktioner.](change-feed.md) Ändringsfeeden ger underordnade mikrotjänster möjligheten att på ett tillförlitligt och stegvis läsa infogningar och uppdateringar (till exempel orderhändelser) som gjorts till en Azure Cosmos DB. Den här funktionen kan utnyttjas för att tillhandahålla ett beständigt händelsearkiv som meddelandemäklare för tillståndsförändrande händelser och arbetsflöde för enhetsorderbearbetning mellan många mikrotjänster (som kan implementeras som [serverlösa Azure Functions](https://azure.com/serverless)).

![Azure Cosmos DB beställa pipeline referensarkitektur](./media/use-cases/event-sourcing.png)

Dessutom kan data som lagras i Azure Cosmos DB integreras med HDInsight för stordataanalys via Apache Spark-jobb. Mer information om Spark Connector för Azure Cosmos DB finns i [Köra ett Spark-jobb med Cosmos DB och HDInsight](spark-connector.md).

## <a name="gaming"></a>Spel
Databasnivån är en viktig komponent i spelapplikationer. Moderna spel utför grafisk bearbetning på mobila/konsolklienter, men förlita sig på molnet för att leverera anpassat och personligt innehåll som statistik i spelet, integrering av sociala medier och högt poängtopplistor. Spel kräver ofta en millisekunders latenser för läsningar och skrivningar för att ge en engagerande spelupplevelse. En speldatabas måste vara snabb och kunna hantera massiva toppar i begäran priser under nya spel lanseringar och uppdateringar funktion.

Azure Cosmos DB används av spel som [The Walking Dead: No Man's Land](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) by Next [Games](https://www.nextgames.com/)och [Halo 5: Guardians](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Azure Cosmos DB ger följande fördelar för spelutvecklare:

* Azure Cosmos DB gör att prestanda kan skalas upp eller ned elastiskt. Detta gör det möjligt för spel att hantera uppdatering av profil och statistik från dussintals till miljontals samtidiga spelare genom att göra ett enda API-anrop.
* Azure Cosmos DB stöder millisekunder läser och skriver för att undvika fördröjningar under spelets gång.
* Azure Cosmos DB:s automatiska indexering gör det möjligt att filtrera mot flera olika egenskaper i realtid, till exempel hitta spelare efter deras interna spelar-ID eller deras GameCenter, Facebook, Google-ID eller fråga baserat på spelarmedlemskap i ett gille. Detta är möjligt utan att bygga komplex indexering eller fragmentering infrastruktur.
* Sociala funktioner, inklusive chattmeddelanden i spelet, medlemskap i spelargill, slutförda utmaningar, höga resultatlistor och sociala grafer är lättare att implementera med ett flexibelt schema.
* Azure Cosmos DB som en hanterad plattform-som-en-tjänst (PaaS) krävs minimal installation och hantering för att möjliggöra snabb iteration och minska tiden till marknaden.

![Referensarkitektur för Azure Cosmos DB-spel](./media/use-cases/gaming.png)

## <a name="web-and-mobile-applications"></a>Webb- och mobilappar
Azure Cosmos DB används ofta i webb- och mobilappar och lämpar sig väl för modellering av sociala interaktioner, integrering med tjänster från tredje part och för att skapa omfattande anpassade upplevelser. Cosmos DB SDKs kan användas bygga rika iOS och Android-program med den populära [Xamarin ram](mobile-apps-with-xamarin.md).  

### <a name="social-applications"></a>Sociala tillämpningar
Ett vanligt användningsfall för Azure Cosmos DB är att lagra och fråga användargenererat innehåll (UGC) för webb-, mobil- och sociala medieprogram. Några exempel på UGC är chattsessioner, tweets, blogginlägg, betyg och kommentarer. Ofta är UGC i sociala medier applikationer en blandning av fri form text, egenskaper, taggar och relationer som inte avgränsas av stel struktur. Innehåll som chattar, kommentarer och inlägg kan lagras i Cosmos DB utan att det krävs omvandlingar eller komplexa objekt till relationsmappningslager.  Dataegenskaper kan läggas till eller ändras enkelt för att matcha krav som utvecklare iterera över programkoden, vilket främjar snabb utveckling.  

Program som integreras med sociala nätverk från tredje part måste svara på ändrade scheman från dessa nätverk. Eftersom data indexeras automatiskt som standard i Cosmos DB är data redo att efterfrågas när som helst. Därför har dessa program flexibilitet att hämta prognoser enligt deras respektive behov.

Många av de sociala programmen körs i global skala och kan uppvisa oförutsägbara användningsmönster. Flexibilitet i skalning av datalagret är viktigt eftersom programlagret skalas för att matcha efterfrågan på användning.  Du kan skala ut genom att lägga till ytterligare datapartitioner under ett Cosmos DB-konto.  Dessutom kan du också skapa ytterligare Cosmos DB-konton i flera regioner. Information om tillgänglighet för Cosmos DB-tjänstregion finns i [Azure Regions](https://azure.microsoft.com/regions/#services).

![Referensarkitektur för Azure Cosmos DB-webbappar](./media/use-cases/apps-with-global-reach.png)

### <a name="personalization"></a>Personanpassning
Numera kommer moderna applikationer med komplexa vyer och upplevelser. Dessa är vanligtvis dynamiska, catering till användarens preferenser eller stämningar och varumärkesbehov. Därför måste program kunna hämta anpassade inställningar effektivt för att återge gränssnittselement och upplevelser snabbt. 

JSON, ett format som stöds av Cosmos DB, är ett effektivt format för att representera UI layout data eftersom det inte bara är lätt, men också kan enkelt tolkas av JavaScript. Cosmos DB erbjuder oförmögna konsekvensnivåer som tillåter snabba läsningar med skrivningar med låg latens. Därför är lagring av ui layout data inklusive personliga inställningar som JSON-dokument i Cosmos DB ett effektivt sätt att få dessa data över tråden.

![Referensarkitektur för Azure Cosmos DB-webbappar](./media/use-cases/personalization.png)

## <a name="next-steps"></a>Nästa steg

* För att komma igång med Azure Cosmos DB följer du våra [snabba starter](create-sql-api-dotnet.md), som hjälper dig att skapa ett konto och komma igång med Cosmos DB.

* Om du vill läsa mer om kunder som använder Azure Cosmos DB läser du sidan [med kundfallstudier.](https://azure.microsoft.com/case-studies/?service=cosmos-db)
