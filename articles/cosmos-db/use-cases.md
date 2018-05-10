---
title: Vanliga användningsområden och scenarier för Azure Cosmos DB | Microsoft Docs
description: 'Lär dig mer om upp fem användningsfall för Azure Cosmos DB: användaren genereras innehåll, händelseloggning, katalogdata, inställningar för användardata och Sakernas Internet (IoT).'
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.assetid: eca68a58-1a8c-4851-8cf8-6e4d2b889905
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/7/2017
ms.author: sngun
ms.openlocfilehash: 67ff272f669e65f0466865349691bd69156401a9
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="common-azure-cosmos-db-use-cases"></a>Vanliga användningsområden för Azure Cosmos DB
Den här artikeln innehåller en översikt över flera vanliga användningsområden för Azure Cosmos DB.  Rekommendationerna i den här artikeln fungera som en startpunkt som du utvecklar programmet med Cosmos DB.   

När du har läst den här artikeln kommer du att kunna svara på följande frågor: 

* Vad är vanliga användningsområden för Azure Cosmos DB?
* Vilka är fördelarna med att använda Azure Cosmos DB för retail program?
* Vilka är fördelarna med att använda Azure Cosmos DB som ett datalager för system Sakernas Internet (IoT)?
* Vilka är fördelarna med att använda Azure Cosmos DB för webb- och mobilprogram?

## <a name="introduction"></a>Introduktion
[Azure Cosmos-DB](../cosmos-db/introduction.md) är Microsofts tjänst för globalt distribuerad databas. Tjänsten är utformad för att tillåta kunder att Elastiskt (och oberoende) skala dataflöden och lagringsutrymmen till alla geografiska regioner. Azure Cosmos-DB är den första globalt distribuerad databas-tjänsten på marknaden idag erbjuder omfattande [serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/) omfattar dataflöde, svarstid, tillgänglighet och konsekvenskontroll. 

Azure Cosmos-DB är en global distribuerade och flera olika modeller databas som används i en mängd olika program och användningsfall. Det är ett bra alternativ för alla [serverlösa](http://azure.com/serverless) program som måste låg ordning millisekunder svarstider och måste skalas snabbt och globalt. Den stöder flera datamodeller (nyckelvärde, dokument, diagram och kolumner) och många API: er för data åt inklusive [MongoDB API](mongodb-introduction.md), [SQL API](documentdb-introduction.md), [Graph API (Gremlin)](graph-introduction.md), och [tabeller API](table-introduction.md) internt och på extensible sätt. 

Följande är några attribut i Azure Cosmos DB som gör det passar bra för program med höga prestanda med globala ambitionsnivå.

* Azure Cosmos-DB partitionerar internt data för hög tillgänglighet och skalbarhet. Azure Cosmos-DB erbjuder 99,99% garantier för tillgänglighet, dataflöde, låg latens och konsekvent på alla enskild region och konton för alla flera regioner med Avslappnad konsekvens och 99,999% läsa tillgänglighet för alla konton i flera regioner databasen.
* Azure Cosmos-DB har SSD-baserad lagring med låg latens ordning millisekunder svarstider.
* Azure Cosmos DBS stöd för konsekvensnivåer som eventuell och konsekvent prefix, session och begränsat föråldrad möjliggör fullständig flexibilitet och låg kostnad till prestanda förhållandet. Ingen databas service erbjuder så mycket flexibilitet som Azure Cosmos DB i nivåer konsekvenskontroll. 
* Azure Cosmos-DB har en flexibel data eget prisnivå modell som mätare lagring och genomströmning oberoende av varandra.
* Azure Cosmos DB reserverat dataflöde modell kan du tänka på antalet läsning/skrivning i stället för IOPs/CPU/minne för den underliggande maskinvaran.
* Azure Cosmos DB design kan du skala till massiv begäran volymer i den ordning som BILJONTALS förfrågningar per dag.

Dessa attribut är användbara i webb-, mobil-, spel- och IoT-appar som behöver låg svarstid och behöver hantera stora mängder läsningar och skrivningar.

## <a name="iot-and-telematics"></a>IoT och telematik
IoT-användningsfall ofta delar vissa mönster i hur de infognings-, process, och lagra data.  Dessa system måste först att mata in belastning av data från enheten sensorer för olika språk. Sedan dessa system bearbeta och analysera data som skickats för att härleda realtidsinsikter. Kalla lagring för batch analytics arkiveras sedan data. Microsoft Azure erbjuder omfattande tjänster som kan användas för IoT-användningsfall inklusive Azure Cosmos DB, Azure Event Hubs, Azure Stream Analytics, Azure Notification Hub, Azure Machine Learning, Azure HDInsight och PowerBI. 

![Azure IoT för Cosmos-DB-Referensarkitektur](./media/use-cases/iot.png)

Belastning av data kan vara inhämtas av Händelsehubbar i Azure som den erbjuder datapåfyllning i högt dataflöde med låg latens. Data som inhämtas som behöver bearbetas realtid information som kan vara går till Azure Stream Analytics för realtidsanalys. Data kan läsas in i Azure Cosmos DB för ad hoc-frågor. När data läses in i Azure Cosmos DB, är data redo att efterfrågas. Dessutom kan kan nya data och ändringar av befintliga data läsas på Ändra feed. Ändra feeden är en beständig, Lägg till loggen som sparar ändringar av Cosmos DB samlingar i följd. Alla data eller bara ändringar av data i Azure Cosmos DB kan användas som för referensdata som en del av realtidsanalys. Dessutom kan data ytterligare förfinad och bearbetas av ansluter Azure Cosmos DB data till HDInsight för Pig, Hive eller karta/minska jobb.  Förfinad data läses sedan tillbaka till Azure Cosmos DB för rapportering.   

En exempel IoT-lösning med hjälp av Azure Cosmos DB, EventHubs och Storm finns i [hdinsight-storm-exempel lagringsplats på GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Läs mer på Azure-erbjudanden för IoT [skapa din Sakernas Internet](http://www.microsoft.com/server-cloud/internet-of-things.aspx). 

## <a name="retail-and-marketing"></a>Återförsäljarversion och marknadsföring
Azure Cosmos-DB används ofta i Microsofts egna e-handel plattformar, som kör Windows Store och XBox Live. Det används också i detaljhandel för lagring av data catalog och för händelsen som källa i ordning bearbetning pipelines.

Användningsscenarier för katalogen data omfattar lagring och hämtning av en uppsättning attribut för entiteter, till exempel personer, platser och produkter. Några exempel på katalogdata är användarkonton, produktkataloger, register för IoT-enhet och faktura för material som system. Attribut för den här informationen kan variera och kan ändras med tiden för att få plats programkrav.

Överväg ett exempel på en produktkatalogen för en bil delar leverantör. Varje del kan ha ett eget attribut förutom de vanliga attribut som delar alla delar. Attribut för en viss del kan dessutom ändra följande år när en ny modell släpps. Azure Cosmos-DB stöder flexibel scheman och hierarkiska data och därmed den passar bra för att lagra produkten katalogdata.

![Azure DB Cosmos retail katalog Referensarkitektur](./media/use-cases/product-catalog.png)

Azure Cosmos-DB används ofta för händelsen som källa till power händelse drivs arkitekturer med dess [ändra feed](change-feed.md) funktioner. Ändra feeden innehåller underordnade mikrotjänster möjlighet att på ett tillförlitligt sätt och inkrementellt läsa infogningar och uppdateringar (till exempel ordning händelser) för en Azure-Cosmos-databas. Den här funktionen kan utnyttjas för att ge en beständig händelse butik som förhandlare meddelande för ändring av tillstånd händelser och enheten bearbetning arbetsflöde mellan många mikrotjänster (som kan implementeras som [serverlösa Azure Functions](http://azure.com/serverless)).

![Azure Cosmos DB ordning pipeline Referensarkitektur](./media/use-cases/event-sourcing.png)

Data som lagras i Azure Cosmos DB kan dessutom integreras med HDInsight för analys av stordata med Apache Spark-jobb. Mer information i Spark-koppling för Azure Cosmos DB finns [kör ett Spark-jobb med Cosmos-databas och HDInsight](spark-connector.md).

## <a name="gaming"></a>Spel
Databasnivån är en viktig komponent i spelappar. Moderna spel utföra grafiska bearbetning av mobile-konsolen klienter, men den förlitar sig på molnet för att leverera anpassade och anpassade innehåll som i spelet statistik, sociala media integrering och hög poäng ledartavlor. Spel kräver ofta enskild millisekunders latens för läsningar och skrivningar för att tillhandahålla en engagera er i spelet upplevelse. En spel databasen måste vara snabb och kunna hantera massiv toppar i begäran priser under nytt spel startar och funktion uppdateringar.

Azure Cosmos-DB används av spel som [i genomgången döda: Nej Man mark](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/) av [nästa spel](http://www.nextgames.com/), och [Halo 5: vårdnadshavare](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Azure Cosmos-DB ger följande fördelar spel utvecklare:

* Azure Cosmos-DB kan prestanda skalas uppåt eller nedåt Elastiskt. Detta gör att spel att hantera uppdatering profil och statistik från dussintals till miljontals samtidiga spelare genom att göra en enda API-anrop.
* Azure Cosmos-DB stöder millisekunder läsningar och skrivningar för att undvika eventuella beräkningstider under spelet.
* Azure Cosmos DB automatisk indexering tillåter filtrering mot flera olika egenskaper i realtid, till exempel spelare av deras interna player ID: N eller deras GameCenter, Facebook, Google-ID: N eller fråga baserat på player medlemskap i en guild. Detta är möjligt utan att skapa komplexa indexera eller infrastruktur för horisontell partitionering.
* Sociala funktioner inklusive i spelet chatt meddelanden player guild medlemskap, utmaningar slutförts, hög poäng ledartavlor och sociala diagram är enklare att implementera med ett flexibelt schema.
* Azure Cosmos-DB som en hanterad plattform-som-en tjänst (PaaS) krävde minimal installation och hantering fungerar för att möjliggöra snabb iteration och minska tid till marknad.

![Referensarkitektur för Azure DB Cosmos-spel](./media/use-cases/gaming.png)

## <a name="web-and-mobile-applications"></a>Webb- och mobilprogram
Azure Cosmos-DB används ofta inom webb- och mobilprogram och passar bra för modellering kommunikation, integrera med tjänster från tredje part och för att skapa omfattande personlig upplevelse. Cosmos DB SDK kan vara används build omfattande iOS och Android-program med hjälp av den populära [Xamarin framework](mobile-apps-with-xamarin.md).  

### <a name="social-applications"></a>Sociala program
Ett vanligt användningsfall för Azure Cosmos DB är att lagra och fråga användargenererat innehåll (UGC) för webb-, mobil och sociala media program. Några exempel på UGC är chatt sessioner, tweets, blogginlägg, klassificeringar och kommentarer. UGC i sociala medieprogram är ofta en blandning av fri text, egenskaper, taggar och relationer som inte begränsas av hårda uppbyggnad. Innehåll, till exempel chatt, kommentarer och tjänster kan lagras i Cosmos DB utan transformationer eller komplexa objekt till relationella mappning lager.  Egenskaper för data kan läggas till eller ändras enkelt för att matcha krav som utvecklare iterera över programkoden, därför främja snabb utveckling.  

Program som integreras med tredje parts sociala nätverk måste svara på ändra scheman från dessa nätverk. Data indexeras automatiskt som standard i Cosmos DB, är data redo att efterfrågas när som helst. Därför måste har dessa program möjlighet att hämta projektioner enligt deras respektive behov.

Många av sociala program körs på global nivå och kan den orsaka oförutsägbara användningsmönster. Flexibilitet vid skalning datalagret är mycket viktigt allteftersom Applikationsnivån växer så att den matchar användarkrav.  Du kan skala ut genom att lägga till ytterligare datapartitioner under en Cosmos-DB-konto.  Dessutom kan du också skapa ytterligare Cosmos DB konton över flera regioner. Tjänsttillgänglighet Cosmos DB region, se [Azure-regioner](https://azure.microsoft.com/regions/#services).

![Azure DB Cosmos web app Referensarkitektur](./media/use-cases/apps-with-global-reach.png)

### <a name="personalization"></a>Personanpassning
Men i dag medföljer moderna program komplexa vyer och erfarenheter. Detta är vanligtvis dynamiska restaurang användarinställningar eller stämningsläge- och anpassning behov. Därför kan behöver program för att kunna hämta anpassade inställningar effektivt för att återge UI-element och upplevelser snabbt. 

JSON, ett format som stöds av Cosmos DB är en effektiv format för att representera UI layoutdata som den är inte bara lightweight, men även kan enkelt tolkas av JavaScript. Cosmos DB erbjuder justerbara konsekvensnivåer som tillåter snabb läsning med låg latens skrivningar. Därför kan är lagra UI layoutdata inklusive personliga inställningar som JSON-dokument i Cosmos-databasen ett effektivt sätt att hämta dessa data över nätverket.

![Azure DB Cosmos web app Referensarkitektur](./media/use-cases/personalization.png)

## <a name="next-steps"></a>Nästa steg
Om du vill komma igång med Azure Cosmos DB, Följ våra [snabbstarter](create-sql-api-dotnet.md), som hjälper dig att skapa ett konto och komma igång med Cosmos DB. 

Eller, om du vill läsa mer om kunder som använder Cosmos DB, följande kunden artiklar som är tillgängliga:

* [Jet.com](https://jet.com). E-handel utmanare eyes översta nivå, körs på Microsoft-molntjänster, utnyttjar Cosmos DB på global nivå.
* [Asos.com](http://www.asos.com/). Asos.com är en brittiska Mode och bästa onlinebutik. I huvudsak avsedda för barn vuxna, säljer Asos över 850 märken som sin egen uppsättning klädesplagg och tillbehör.
* [Toyota](https://www.toyota.com/). Toyota meddelar Corporation är japanska bil tillverkare. Toyota utnyttjas Cosmos DB för en global IoT-app.
* [Citrix](https://customers.microsoft.com/story/citrix). Citrix utvecklar single-sign-on lösning med hjälp av Azure Service Fabric och Azure Cosmos DB
* [TEXA](https://customers.microsoft.com/story/texaspa) TEXAS revolutionerande IoT-lösningen för vehicle ägare kan du spara tid, pengar, gas – och troligtvis bor.
* [Domino's Pizza](https://www.dominos.com). Domino's Pizza Inc. är en American pizza restaurang kedja.
* [Johnson styr](http://www.johnsoncontrols.com). Johnson kontroller är en global diversifierat teknik och flera industriella ledande betjäna ett stort antal kunder i fler än 150 länder.
* [Microsoft Windows Universal Store, Azure IoT Hub, Xbox Live och andra tjänster på Internet-skala](https://azure.microsoft.com/blog/how-azure-documentdb-planet-scale-nosql-helps-run-microsoft-s-own-businesses/). Hur skapar Microsoft massivt skalbara tjänster med hjälp av Azure Cosmos DB.
* [Microsoft Data och analyser team](https://customers.microsoft.com/story/microsoftdataandanalytics). Microsofts team för Data och analyser uppnår planeten skala big-datainsamling med Azure Cosmos DB
* [Sulekha.com](https://customers.microsoft.com/story/sulekha-uses-azure-documentdb-to-connect-customers-and-businesses-across-india). Sulekha används Azure Cosmos DB för att ansluta kunder och företag i Indien.
* [NewOrbit](https://customers.microsoft.com/story/neworbit-takes-flight-with-azure-documentdb). NewOrbit tar det rör sig med Azure Cosmos DB.
* [Affinio](https://customers.microsoft.com/doclink/affinio-switches-from-aws-to-azure-documentdb-to-harness-social-data-at-scale). Affinio växlar från AWS till Azure Cosmos DB att utnyttja sociala data i större skala.
* [Nästa spel](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/). Käppar Dead: Nej Man's mark game soars # 1 stöds av Azure Cosmos DB.
* [Halo](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). Hur Halo 5 implementerats sociala spel med hjälp av Azure Cosmos DB.
* [Cortana Analytics galleriet](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/). Cortana Analytics Gallery - en skalbar community-webbplats som bygger på Azure Cosmos DB.
* [Enkelt](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). Inledande Integrator får flerspråkig företag globala insyn i minuter med flexibla Molntekniker.
* [Nyheter Republiken](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Lägger till tillgångsinformation till Nyheter att tillhandahålla information med syftet för engagerade medborgarna. 
* [SGS internationella](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). För konsekvent färg världen aktivera stora märken SGS. Och SGS övergår i Azure.
* [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). Världsledande Telenor använder molnet för att flytta med en start hastighet. 
* [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). Arkivet för framtiden körs på snabb sökning och enkelt flödet av data.
* [Nucleo](https://customers.microsoft.com/story/azure-based-software-platform-breaks-down-barriers-bet). Azure-baserad programvaruplattform uppdelad barriärer mellan företag och kunder
* [Weka](https://customers.microsoft.com/story/weka-smart-fridge-improves-vaccine-management-so-more-people-can-be-protected-against-diseases). Weka smarta kombinerad kyl förbättrar vaccin hantering så att flera personer kan skyddas mot sjukdomar
* [Orange Tribes](https://customers.microsoft.com/story/theres-more-to-that-food-app-than-meets-the-eye-or-the-mouth). Det finns mer att mat appen än uppfyller ögat eller munnen.
* [Verklig Madrid](https://customers.microsoft.com/story/real-madrid-brings-the-stadium-closer-to-450-million-f). Verklig Madrid ger stadium närmare för 450 miljoner fläktar i världen, med Microsoft Cloud.
* [Tuku](https://customers.microsoft.com/story/tuku-makes-car-buying-fun-with-help-from-azure-services). TUKU gör bil köpa roliga med hjälp av Azure-tjänster
