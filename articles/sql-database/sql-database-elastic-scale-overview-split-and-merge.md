---
title: Flytta data mellan databaser som skalats ut molntjänster | Microsoft Docs
description: 'Beskriver hur du hanterar shards och flytta data via en automatisk värdbaserad tjänst som använder API: er för elastisk databas.'
services: sql-database
manager: craigg
author: stevestein
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: sstein
ms.openlocfilehash: c70279bd52f7b0b0e0cbc27742eca93d9af5e630
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>Flytta data mellan utskalade molndatabaser
Om du är en programvara som en tjänst-utvecklare och plötsligt enorm begäran görs i appen, måste du anpassa tillväxt. Så får du lägga till flera databaser (shards). Hur du för att distribuera data till de nya databaserna utan att störa dataintegriteten? Använd den **för delade sökvägssammanslagning** att flytta data från begränsad databaser till de nya databaserna.  

Verktyget delade dokument körs som en Azure-webbtjänst. En administratör eller utvecklare använder du verktyget för att flytta shardlets (data från en Fragmentera) mellan olika databaser (shards). Verktyget använder Fragmentera kartan management för att upprätthålla tjänsten metadata-databasen och säkerställa konsekvent mappningar.

![Översikt][1]

## <a name="download"></a>Ladda ned
[Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>Dokumentation
1. [Elastisk dela Merge tool självstudie om databaser](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
2. [Dela dokument säkerhetskonfiguration](sql-database-elastic-scale-split-merge-security-configuration.md)
3. [Dela dokument säkerhetsaspekter](sql-database-elastic-scale-split-merge-security-configuration.md)
4. [Karthantering för shard](sql-database-elastic-scale-shard-map-management.md)
5. [Migrera befintliga databaser att skala ut](sql-database-elastic-convert-to-use-elastic-tools.md)
6. [Elastisk Databasverktyg](sql-database-elastic-scale-introduction.md)
7. [Ordlista för verktyg för elastisk databas](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Varför använda verktyget delade dokument?
**Flexibilitet**

Program behöver stretch flexibelt överskrider gränserna för en enskild Azure SQL DB-databas. Använda verktyget för att flytta data som behövs för att nya databaser samtidigt integritet.

**Dela att växa** 

Du måste öka totala kapaciteten att hantera enorm tillväxt. Gör du genom att skapa ytterligare kapacitet genom delning data och distribuera den över inkrementellt flera databaser tills kapacitetsbehov är uppfyllda. Detta är ett typiskt exempel av funktionen 'dela'. 

**Koppla till krympa**

Kapacitet måste krympa på grund av ett företag när. Verktyget kan du skala till färre skalenheter när business långsammare. Funktionen 'merge' i elastisk skalbarhet split-kopplingstjänsten beskriver det här kravet. 

**Hantera anslutningar genom att flytta shardlets**

Med flera klienter per databas leda tilldelning av shardlets till shards till kapacitet flaskhalsar på vissa delar. Detta kräver omfördelning shardlets eller flytta upptagen shardlets till nya eller mindre utnyttjade delar. 

## <a name="concepts--key-features"></a>Begrepp & viktiga funktioner
**Kund-värdbaserade tjänster**

Dela sammanslagna levereras som en kund-värdbaserad tjänst. Du måste distribuera och hantera tjänsten i din Microsoft Azure-prenumeration. Det paket som du hämtar från NuGet innehåller en konfigurationsmall för att slutföra med information för din distribution. Finns det [delade dokument kursen](sql-database-elastic-scale-configure-deploy-split-and-merge.md) mer information. Eftersom tjänsten körs i din Azure-prenumeration, kan du styra och konfigurera de flesta säkerhetsaspekter för tjänsten. Standardmallen innehåller alternativ för att konfigurera SSL, certifikatbaserad klientautentisering, kryptering för lagrade autentiseringsuppgifter, DoS skyddar och IP-begränsningar. Du hittar mer information om säkerhetsaspekterna i följande dokument [delade dokument säkerhetskonfiguration](sql-database-elastic-scale-split-merge-security-configuration.md).

Standard distribuerade tjänsten körs med en arbetare och en webbroll. Var används den A1 VM-storleken i Azure Cloud Services. Du inte kan ändra dessa inställningar när du distribuerar paketet, kan du ändra dem efter en lyckad distribution i körs Molntjänsten (via Azure portal). Observera att arbetsrollen inte måste konfigureras för mer än en instans av tekniska skäl. 

**Fragmentera kartan integrering**

Dela kopplingstjänsten samverkar med Fragmentera mappningen av programmet. När du använder dela kopplingstjänsten att dela eller sammanfoga intervall eller flytta shardlets mellan shards, håller tjänsten automatiskt Fragmentera kartan uppdaterade. Gör du genom tjänsten ansluter till Fragmentera kartan manager-databas i programmet och underhåller intervall och mappningar som delade/merge/move-begäran pågår. Detta säkerställer att fragmentera kartan visas alltid aktuell när ska dela dokument. Dela, implementeras dokument och shardlet förflyttning genom att flytta en batch med shardlets från Fragmentera för källan till målet Fragmentera. Under åtgärden shardlet flytt shardlets omfattas aktuell batch är markerad som offline i kartan Fragmentera och är inte tillgängliga för data-beroende anslutningar med hjälp av den **OpenConnectionForKey** API. 

**Konsekvent shardlet anslutningar**

Dataflyttning att starta en ny grupp med shardlets, alla angivna Fragmentera mappning data beroende routning anslutningar till Fragmentera lagra shardlet är avlivade och efterföljande anslutningar från Fragmentera kartan API: er till dessa shardlets blockeras när data flytt pågår för att undvika inkonsekvenser. Anslutningar till andra shardlets på samma Fragmentera också hämta avslutats, men kommer att lyckas igen omedelbart på försök igen. När gruppen flyttas shardlets markeras online igen för mål-Fragmentera och datakällan tas bort från källan Fragmentera. Tjänsten går igenom de här stegen för varje batch tills alla shardlets har flyttats. Detta leder till flera anslutningsåtgärder kill under slutföra move-dela/merge-åtgärden.  

**Hantera shardlet tillgänglighet**

Begränsa anslutningen om du avbryter den aktuella gruppen med shardlets som beskrivs ovan begränsar omfånget för tillfället till en batch med shardlets i taget. Detta är att föredra över en metod där fullständig Fragmentera är offline för alla shardlets under loppet av en delad eller merge-åtgärd. Storleken på en grupp definieras som antalet distinkta shardlets att flytta samtidigt, är en konfigurationsparameter. Det kan definieras för varje dela och slå samman åtgärd beroende på programmets tillgänglighet och prestanda behov. Observera att det intervall som håller på att låsas i kartan Fragmentera kan vara större än batchstorlek som har angetts. Det beror på att tjänsten hämtar intervallet storlek så att det faktiska antalet horisontell partitionering nyckelvärdena i data matchar cirka batchstorleken. Det här är viktigt att komma ihåg särskilt för glesbefolkade horisontell partitionering nycklar. 

**Utrymmet för metadata**

Dela kopplingstjänsten använder en databas om du vill bevara statusen och hålla loggar under bearbetning av begäran. Användaren i prenumerationen skapas denna databas och innehåller anslutningssträngen för den i konfigurationsfilen för tjänstdistributionen av. Administratörer från användarens organisation kan också ansluta till databasen att granska begäran förlopp och undersöka detaljerad information om potentiella problem.

**Horisontell partitionering medvetenhet**

Dela kopplingstjänsten skiljer mellan (1) delat tabeller och (2) referenstabellerna (3) normal tabeller. Semantiken för en move-dela/merge-åtgärd beror på vilken typ av tabellen som används och definieras enligt följande: 

* **Delat tabeller**: dela dokument och flytta flytta shardlets från källan till målet Fragmentera. Efter slutförande av övergripande begäran dessa shardlets inte längre finns på källan. Observera att måltabeller måste finnas på mål-Fragmentera och får inte innehålla data i målområdet innan bearbetningen av åtgärden. 
* **Refererar till tabeller**: för register, dela, slå samman och flytta operations kopiera data från källan till målet Fragmentera. Observera att inga ändringar inträffar på mål-Fragmentera för en viss tabell om det finns redan en rad i tabellen på målet. Tabellen måste vara tom för någon referens tabell kopieringsåtgärd bearbetas.
* **Andra tabeller**: andra tabeller kan finnas på källan eller målet för en delning och merge-åtgärd. Dela kopplingstjänsten ignorerar dessa tabeller för dataflyttning eller kopiering. Observera att de stör dessa åtgärder vid begränsningar.

Information om referens kontra delat tabeller tillhandahålls av den **SchemaInfo** API: er på kartan Fragmentera. I följande exempel visar hur dessa API: n på en viss Fragmentera kartan manager objektet smm: 

    // Create the schema annotations 
    SchemaInfo schemaInfo = new SchemaInfo(); 

    // Reference tables 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region")); 
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation")); 

    // Sharded tables 
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY")); 
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY")); 

    // Publish 
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo); 

Tabeller 'region' och 'landet' har definierats som referenstabellerna och kommer att kopieras med move-dela/merge-åtgärder. ”kund” och 'order' definieras i sin tur som delat tabeller. C_CUSTKEY och O_CUSTKEY fungera som en nyckel för horisontell partitionering. 

**Referensintegritet**

Dela kopplingstjänsten analyserar alla beroenden mellan tabellerna och använder primära nyckel sekundärnyckelrelationer för att mellanlagra åtgärderna för att flytta referenstabellerna och shardlets. I allmänhet kopieras referenstabellerna först i beroendeordningen, och sedan shardlets kopieras i den ordning de beroenden inom varje grupp. Detta är nödvändigt så att gäller FK PK begränsningar på mål-Fragmentera som nya data tas emot. 

**Fragmentera kartan konsekvens och eventuell slutförande**

Med fel, dela kopplingstjänsten återupptar åtgärder efter eventuella avbrott och syftet är att slutföra i förloppet begäranden. Det kan dock finnas oåterkalleligt situationer, t.ex. när mål Fragmentera tappas bort eller komprometteras inte repareras. Under dessa omständigheter kan vissa shardlets som skulle flyttas fortsätta att finnas på käll-Fragmentera. Tjänsten garanterar att shardlet mappningar uppdateras bara när uppgifter som krävs har kopierats till målet. Shardlets bara ta bort på källan när alla data har kopierats till målet och motsvarande mappningar har uppdaterats. Borttagningen sker i bakgrunden medan intervallet är redan online på mål-Fragmentera. Dela kopplingstjänsten garanterar alltid mappningar som lagras i Fragmentera mappningen är korrekt.

## <a name="the-split-merge-user-interface"></a>Dela dokument användargränssnittet
Dela kopplingstjänsten paketet innehåller en arbetsroll och en webbroll. Webbrollen används för att skicka förfrågningar om delade dokument på ett interaktivt sätt. Huvudkomponenterna i användargränssnittet är följande:

* Åtgärdstyp: Typ av åtgärd är en alternativknapp som styr typ av åtgärd som utförs av tjänsten för denna begäran. Du kan välja mellan delning, slå samman och flytta scenarier. Du kan också avbryta en tidigare skickade. Du kan använda delade, slå samman och flytta begäranden för intervallet Fragmentera maps. Lista Fragmentera mappar endast stöd för move-åtgärder.
* Fragmentera mappa: Nästa avsnitt av parametrar för begäran omfatta information om Fragmentera kartan och databasen som värd för kartan Fragmentera. I synnerhet måste du ange namnet på Azure SQL Database-servern och databasen som värd för shardmap autentiseringsuppgifter för anslutning till Fragmentera kartan databasen och slutligen namnet på kartan Fragmentera. För närvarande accepterar åtgärden bara en enda uppsättning autentiseringsuppgifter. Dessa autentiseringsuppgifter måste ha behörighet att utföra ändringar på Fragmentera kartan samt att användardata på shards.
* Käll-adressintervall (dela och sammanfoga): en åtgärd för dela och slå samman bearbetar ett intervall med den lägsta och högsta nyckeln. Om du vill ange en åtgärd med ett unbounded hög nyckelvärde, markera kryssrutan ”övre nyckel är max” och lämnar hög nyckelfältet tom. Intervallet nyckelvärden som du anger behöver inte matcha exakt en mappning och dess gränser i kartan Fragmentera. Om du inte anger någon adressintervallsgränser alls kommer tjänsten härleda närmaste intervallet du automatiskt. Du kan använda GetMappings.ps1 PowerShell-skript för att hämta aktuella mappningarna i en given Fragmentera karta.
* Dela källa beteende (delning): definiera platsen om du vill dela käll-adressintervall för dela-åtgärder. Det gör du genom att tillhandahålla nyckeln horisontell partitionering där du vill dela ska ske. Använd knappen Ange om du vill den nedre delen av intervallet (exklusive nyckeln delning) för att flytta eller om du vill att den övre delen att flytta (inklusive den delade nyckeln).
* Datakällan Shardlet (flytta): flytta operations skiljer sig från delade eller merge-åtgärder eftersom de inte kräver ett intervall för att beskriva källan. En källa för flytta identifieras bara av horisontell partitionering värdet för nyckeln som du planerar att flytta.
* Rikta Fragmentera (delning): när du har angett informationen på källan för split-åtgärden, måste du definiera där du vill att data ska kopieras till med Azure SQL Db-server och databas-namn för målet.
* Målområdet (merge): Merge-operationer flytta shardlets till en befintlig Fragmentera. Du kan identifiera befintliga Fragmentera genom att tillhandahålla adressintervallsgränser i befintliga intervallet som du vill koppla med.
* Batchstorleken: Batchstorleken reglerar antalet shardlets som ska försättas i offlineläge vid en tidpunkt under dataflytten. Detta är ett heltalsvärde där du kan använda mindre värden när du är känsligt för långa stilleståndsperioder för shardlets. Högre värden ökar den tid som en given shardlet är offline men kan förbättra prestanda.
* Åtgärds-Id (Avbryt): Om du har en pågående åtgärd som inte längre behövs, du kan avbryta åtgärden genom att ange dess åtgärds-ID i det här fältet. Du kan hämta åtgärds-ID från tabellen begäran status (se avsnittet 8.1) eller från utdata i webbläsaren där du skickade begäran.

## <a name="requirements-and-limitations"></a>Krav och begränsningar
Den aktuella implementationen av delade kopplingstjänsten regleras följande krav och begränsningar: 

* Shards måste finnas och vara registrerade i kartan Fragmentera innan en delad merge-åtgärd på dessa delar kan utföras. 
* Tjänsten kan inte skapa tabeller eller andra databasobjekt automatiskt som en del av dessa åtgärder. Det innebär att schemat för alla delat tabeller och referenstabeller måste finnas på mål-Fragmentera innan någon move-dela/merge-åtgärd. Delat tabeller måste i synnerhet vara tom i området där nya shardlets ska läggas till av en move-dela/merge-åtgärd. Annars misslyckas åtgärden första konsekvenskontrollen för mål-Fragmentera. Observera också att referensen kopieras bara data om referensen tabellen är tom och att det inte finns några konsekvenskontroll garantier avseende andra samtidiga skrivåtgärder på referens-tabeller. Vi rekommenderar detta: när du kör dela/merge-operationer kan inga andra skrivåtgärder göra ändringar i referenstabellerna.
* Tjänsten är beroende av raden identitet upprättas genom ett unikt index eller en nyckel som innehåller delning nyckel för att förbättra prestanda och tillförlitlighet för stora shardlets. Detta gör tjänsten för att flytta data på ett även ökad detaljnivå än bara nyckelvärdet horisontell partitionering. Detta hjälper till att minska maximal mängd loggutrymme och lås som krävs under åtgärden. Överväg att skapa ett unikt index eller primärnyckel inklusive delning nyckeln för en given tabell om du vill använda tabellen med delade/merge/move-begäranden. Av prestandaskäl ska nyckeln för horisontell partitionering inledande kolumn i nyckeln eller index.
* Under bearbetning av begäranden kanske vissa shardlet data finns både på käll- och mål-Fragmentera. Detta är nödvändigt för att skydda mot fel under transport av shardlet. Integrering av dela dokument med Fragmentera kartan säkerställer att anslutningar via data beroende routning API: er med hjälp av **OpenConnectionForKey** metod på kartan Fragmentera inte ser alla mellanliggande inkonsekvent tillstånd. Men när du ansluter till källan eller målet shards utan att använda den **OpenConnectionForKey** metoden inkonsekvent mellanliggande tillstånd kanske visas när du ska dela/merge/move-begäranden. Dessa anslutningar kan visa helt eller delvis dubbla resultat beroende på tidpunkten eller Fragmentera underliggande anslutningen. Den här begränsningen innehåller för närvarande anslutningar som görs av elastisk skalbarhet flera-Shard-frågor.
* Metadata-databasen för den delade kopplingstjänsten måste inte delas mellan olika roller. Till exempel måste en roll för den delade dokument-tjänst som körs i Förproduktion peka på en annan metadata-databasen än rollen produktion.

## <a name="billing"></a>Fakturering
Dela merge-tjänsten körs som en tjänst i molnet i Microsoft Azure-prenumerationen. Därför gäller avgifterna för molntjänster för din instans av tjänsten. Om du ofta utför move-dela/merge-åtgärder rekommenderar vi att du tar bort dina delade dokument Molntjänsten. Som kostnaderna för att köra eller distribueras molnet tjänstinstanser. Du kan distribuera och starta lätt körbara konfigurationen när du behöver utföra delade eller merge-åtgärder. 

## <a name="monitoring"></a>Övervakning
### <a name="status-tables"></a>Status för tabeller
Dela kopplingstjänsten ger den **RequestStatus** tabellen i metadata-databasen för lagring för övervakning av slutfördes och en pågående begäranden. Tabellen innehåller en rad för varje delad kopplingsbegäran som har skickats till den här instansen av delade kopplingstjänsten. Den ger följande information för varje begäran:

* **Tidsstämpel**: tid och datum då begäran startades.
* **Åtgärds-ID**: ett GUID som identifierar begäran. Denna begäran kan också användas för att avbryta åtgärden när den är fortfarande pågår.
* **Status för**: aktuell status för begäran. För pågående begäranden visas också aktuell fas där begäran är.
* **CancelRequest**: en flagga som anger om begäran har avbrutits.
* **Förlopp**: en del uppskattning av åtgärden har slutförts. Ett värde på 50 anger att åtgärden är klar ungefär 50%.
* **Information om**: en XML-värde som innehåller en mer detaljerad rapport. Rapporten status uppdateras regelbundet som anger rader kopieras från källan till målet. I den här kolumnen vid fel eller undantag innehåller också mer detaljerad information om felet.

### <a name="azure-diagnostics"></a>Azure Diagnostics
Dela kopplingstjänsten använder Azure-diagnostik baserat på Azure SDK 2.5 för övervakning och diagnostik. Du kan styra diagnostik konfigurationen som beskrivs här: [aktiverar diagnostik i Azure-molntjänster och virtuella datorer](../cloud-services/cloud-services-dotnet-diagnostics.md). Det nedladdade paketet innehåller två diagnostik konfigurationer - en för webbrollen och en för arbetsrollen. Konfigurationerna diagnostik för tjänsten följer du anvisningarna från [Cloud Service grunderna i Microsoft Azure](https://code.msdn.microsoft.com/windowsazure/Cloud-Service-Fundamentals-4ca72649). Den innehåller definitioner för att logga prestandaräknare, IIS loggar, Windows-händelseloggar och dela dokument, program-händelseloggar. 

## <a name="deploy-diagnostics"></a>Distribuera diagnostik
Kör följande kommandon med hjälp av Azure PowerShell om du vill aktivera övervakning och diagnostik med diagnostisk konfiguration för webb- och arbetsroller roller som tillhandahålls av NuGet-paketet: 

    $storage_name = "<YourAzureStorageAccount>" 

    $key = "<YourAzureStorageAccountKey" 

    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key  


    $config_path = "<YourFilePath>\SplitMergeWebContent.diagnostics.xml" 

    $service_name = "<YourCloudServiceName>" 

    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWeb" 


    $config_path = "<YourFilePath>\SplitMergeWorkerContent.diagnostics.xml" 

    $service_name = "<YourCloudServiceName>" 

    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWorker" 

Du hittar mer information om hur du konfigurerar och distribuerar här diagnostikinställningar: [aktiverar diagnostik i Azure-molntjänster och virtuella datorer](../cloud-services/cloud-services-dotnet-diagnostics.md). 

## <a name="retrieve-diagnostics"></a>Hämta diagnostik
Du kan enkelt komma åt din diagnostik från Visual Studio Server Explorer i Azure del i Server Explorer-trädet. Öppna en Visual Studio-instans och i menyraden klickar du på Visa och Server Explorer. Klicka på ikonen Azure för att ansluta till din Azure-prenumeration. Gå till Azure Storage -> -> <your storage account> -> tabeller -> WADLogsTable. Mer information finns i [surfning lagringsresurser med Server Explorer](http://msdn.microsoft.com/library/azure/ff683677.aspx). 

![WADLogsTable][2]

WADLogsTable markerade i figuren ovan innehåller detaljerade händelser från tjänsten delade dokument programloggen. Observera att standardkonfigurationen av det Hämta paketet riktar sig till en Produktionsdistribution. Därför är det intervall då loggar och räknare hämtas från tjänstinstanser stora (5 minuter). Lägre intervallet för testning och utveckling genom att justera diagnostikinställningar för webben eller arbetsrollen efter dina behov. Högerklicka på rollen i Visual Studio Server Explorer (se ovan) och justera sedan överföra perioden i dialogrutan för konfigurationsinställningar för diagnostik: 

![Konfiguration][3]

## <a name="performance"></a>Prestanda
I allmänhet förväntas bättre prestanda kan från desto högre mer performant tjänstnivåer i Azure SQL Database. Högre i/o-, processor- och minnesresurser tilldelningarna för högre tjänstnivåer dra nytta av masskopiering och delete-åtgärder som använder delade kopplingstjänsten. Av den anledningen bör öka tjänstnivån för dessa databaser för en definierad, begränsad tidsperiod.

Tjänsten utför även validering frågor som en del av dess normal drift. Frågorna verifiering för oväntat förekomsten av data i målintervallet och kontrollerar att alla delade/merge/flyttningsåtgärd startar från ett konsekvent tillstånd. Frågorna alla fungerar över horisontell partitionering nyckelintervall definieras av omfattningen av åtgärden och batchstorleken tillhandahålls som en del av definitionen för begäran. De här frågorna gör bäst ifrån sig när ett index som har horisontell partitionering nyckel som inledande kolumnen. 

Dessutom kan en unikhet egenskap med nyckel för horisontell partitionering som kolumnen inledande tjänsten för att använda en optimerad metod som begränsar förbrukning av nätverksresurser vad gäller loggutrymme och minne. Den här egenskapen unikhet krävs för att flytta stora datamängder (vanligtvis ovanför 1GB). 

## <a name="how-to-upgrade"></a>Så här uppgraderar du
1. Följ stegen i [distribuera en delad kopplingstjänsten](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
2. Ändra konfigurationsfilen cloud service för dina delade dokument distributionen så att de nya konfigurationsparametrarna. En ny obligatorisk parameter är information om certifikatet som används för kryptering. Ett enkelt sätt att göra detta är att jämföra den nya mall för konfigurationsfilen från download mot den befintliga konfigurationen. Kontrollera att du lägger till inställningar för ”DataEncryptionPrimaryCertificateThumbprint” och ”DataEncryptionPrimary” för både webb- och arbetsrollen.
3. Se till att alla som körs dela merge-åtgärder har slutförts innan du distribuerar uppdateringen till Azure. Du kan göra detta genom att fråga tabellerna RequestStatus och PendingWorkflows i delade sammanslagna metadata-databasen för pågående begäranden.
4. Uppdatera den befintliga cloud service-distributionen för att dela dokument i Azure-prenumeration med det nya paketet och uppdaterade konfigurationsfilen.

Du behöver inte att etablera en ny databas för metadata för att dela dokument att uppgradera. Den nya versionen uppgraderas automatiskt den befintliga metadata-databasen till den nya versionen. 

## <a name="best-practices--troubleshooting"></a>Bästa praxis och felsöka
* Definiera en Testklient och utnyttja dina viktigaste move-dela/merge-åtgärder med testklienten över flera delar. Se till att alla metadata har definierats korrekt i Fragmentera map och att åtgärderna inte strider mot villkor eller sekundärnycklar.
* Behåll testklienten datastorleken ovanför det maximala storleken på din största klient Se till att det inte uppstår datastorleken relaterade problem. Detta hjälper dig att utvärdera en övre gräns på den tid det tar för att flytta en enskild klient. 
* Kontrollera att schemat tillåter borttagning. Dela kopplingstjänsten kräver möjlighet att ta bort data från källan Fragmentera när data har kopierats till målet. Till exempel **ta bort utlösare** kan förhindra tjänsten från att ta bort data på käll- och kan orsaka åtgärder misslyckas.
* Nyckeln för horisontell partitionering bör vara den inledande kolumnen i din primär nyckel eller ett unikt Indexdefinition. Detta säkerställer att uppnå bästa prestanda för frågor för delade eller merge-verifiering och för faktiska data movement och borttagning åtgärder som använder alltid nyckelintervall för horisontell partitionering.
* Samordna split-kopplingstjänsten i den region och datacenter där databaserna finns. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png

