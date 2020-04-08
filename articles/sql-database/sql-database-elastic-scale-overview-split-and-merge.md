---
title: Flytta data mellan utskalade molndatabaser
description: Förklarar hur du manipulerar shards och flyttar data via en självvärd tjänst med hjälp av elastiska databas-API:er.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: c7eb1670ee911895bdba23921845b8795f4998af
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811298"
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>Flytta data mellan utskalade molndatabaser

Om du är en programvara som en tjänst utvecklare, och plötsligt din app genomgår en enorm efterfrågan, måste du tillgodose tillväxten. Så du lägger till fler databaser (shards). Hur omfördelar du data till de nya databaserna utan att störa dataintegriteten? Använd **verktyget för delad koppling** för att flytta data från begränsade databaser till de nya databaserna.  

Verktyget för delad koppling körs som en Azure-webbtjänst. En administratör eller utvecklare använder verktyget för att flytta shardlets (data från en shard) mellan olika databaser (shards). Verktyget använder fragmentkarthantering för att underhålla tjänstens metadatadatabas och säkerställa konsekventa mappningar.

![Översikt][1]

## <a name="download"></a>Ladda ned

[Microsoft.Azure.SqlDatabase.elasticscale.service.splitMerge](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>Dokumentation

1. [Självstudiekurs för split-merge-verktyg för elastisk databas](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
2. [Säkerhetskonfiguration för delad koppling](sql-database-elastic-scale-split-merge-security-configuration.md)
3. [Säkerhetsöverväganden för delad koppling](sql-database-elastic-scale-split-merge-security-configuration.md)
4. [Karthantering för shard](sql-database-elastic-scale-shard-map-management.md)
5. [Migrera befintliga databaser för att skala ut](sql-database-elastic-convert-to-use-elastic-tools.md)
6. [Elastiska databasverktyg](sql-database-elastic-scale-introduction.md)
7. [Ordlista för elastiska databasverktyg](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Varför använda verktyget för delad koppling

- **Flexibilitet**

  Program måste sträcka sig flexibelt utanför gränserna för en enda Azure SQL DB-databas. Använd verktyget för att flytta data efter behov till nya databaser samtidigt som du behåller integriteten.

- **Split att växa**

  För att öka den totala kapaciteten för att hantera explosiv tillväxt skapar du ytterligare kapacitet genom att sharding data och genom att distribuera dem över stegvis fler databaser tills kapacitetsbehoven uppfylls. Detta är ett utmärkt exempel på **delningsfunktionen.**

- **Sammanfoga till krympning**

  Kapacitetsbehovet minskar på grund av ett företags säsongsbetonade karaktär. Med verktyget kan du skala ned till färre skalenheter när verksamheten saktar ned. Funktionen "sammanfoga" i tjänsten för delad sammanfogning av elastisk skala täcker detta krav.

- **Hantera hotspots genom att flytta shardlets**

  Med flera klienter per databas kan allokeringen av shardlets till shards leda till kapacitetsflaskhalsar på vissa shards. Detta kräver omallokering av shardletar eller flytta upptagna shardlets till nya eller mindre utnyttjade shards.

## <a name="concepts--key-features"></a>Begrepp & viktiga funktioner

- **Tjänster som är värd för kunden**

  Split-merge levereras som en kundvärd tjänst. Du måste distribuera och vara värd för tjänsten i din Microsoft Azure-prenumeration. Paketet som du hämtar från NuGet innehåller en konfigurationsmall som du vill komplettera med informationen för din specifika distribution. Mer information finns i [självstudien](sql-database-elastic-scale-configure-deploy-split-and-merge.md) för delad koppling. Eftersom tjänsten körs i din Azure-prenumeration kan du styra och konfigurera de flesta säkerhetsaspekter av tjänsten. Standardmallen innehåller alternativ för att konfigurera TLS, certifikatbaserad klientautentisering, kryptering för lagrade autentiseringsuppgifter, DoS-bevakning och IP-begränsningar. Du hittar mer information om säkerhetsaspekterna i följande [dokumentkonfiguration för delad koppling](sql-database-elastic-scale-split-merge-security-configuration.md).

  Standardbe distribuerad tjänst körs med en arbetare och en webbroll. Var och en använder A1 VM-storleken i Azure Cloud Services. Även om du inte kan ändra dessa inställningar när du distribuerar paketet, kan du ändra dem efter en lyckad distribution i den löpmoldtjänsten (via Azure-portalen). Observera att arbetarrollen inte får konfigureras för mer än en instans av tekniska skäl.

- **Integrering av fragmentkarta**

  Tjänsten split-merge interagerar med fragmentkartan för programmet. När du använder tjänsten dela sammanfogning för att dela eller sammanfoga områden eller för att flytta shardlets mellan shards, håller tjänsten automatiskt fragmentkartan uppdaterad. För att göra det ansluter tjänsten till shard map manager-databasen för programmet och underhåller intervall och mappningar som delnings-/kopplings-/flyttbegärandens förlopp. Detta säkerställer att fragmentkartan alltid visar en uppdaterad vy när split-merge-åtgärder pågår. Åtgärder för att dela, sammanfoga och shardlet implementeras genom att flytta en batch med shardlets från källsvantvanten till målsvanten. Under shardlet-förflyttningsåtgärden markeras shardlets som omfattas av den aktuella batchen som offline i fragmentkartan och är inte tillgängliga för databeroende routningsanslutningar med **OpenConnectionForKey** API.

- **Konsekventa fragmentanslutningar**

  När dataförflyttning startar för en ny batch av shardlets blockeras alla fragmentbaserade routningsanslutningar till shardlagringen och efterföljande anslutningar från fragmentkart-API:erna till shardlets blockeras medan dataförflyttningen pågår för att undvika inkonsekvenser. Anslutningar till andra shardlets på samma fragment kommer också att få dödas, men kommer att lyckas igen omedelbart vid återförsök. När batchen har flyttats markeras shardlets online igen för målsvanten och källdata tas bort från källshardten. Tjänsten går igenom dessa steg för varje batch tills alla shardlets har flyttats. Detta kommer att leda till flera anslutningsavslag under hela split/merge/move-åtgärden.  

- **Hantera shardlet-tillgänglighet**

  Att begränsa anslutningsdödandet till den aktuella batchen av shardlets som diskuterats ovan begränsar omfattningen av otillgänglighet till en sats av shardlets i taget. Detta är att föredra framför en metod där hela shard skulle förbli offline för alla dess shardlets under en split eller merge operation. Storleken på en batch, definierad som antalet olika shardlets att flytta i taget, är en konfigurationsparameter. Det kan definieras för varje delning och sammanfogning beroende på programmets tillgänglighets- och prestandabehov. Observera att intervallet som låses i fragmentkartan kan vara större än den angivna batchstorleken. Detta beror på att tjänsten väljer intervallstorleken så att det faktiska antalet sharding nyckelvärden i data ungefär matchar batchstorleken. Detta är viktigt att komma ihåg särskilt för glesbefolkade skärvor nycklar.

- **Lagring av metadata**

  Tjänsten för delad koppling använder en databas för att behålla sin status och för att hålla loggar under bearbetning av begäran. Användaren skapar databasen i sin prenumeration och tillhandahåller anslutningssträngen för den i konfigurationsfilen för tjänstdistributionen. Administratörer från användarens organisation kan också ansluta till den här databasen för att granska förloppet för begäran och undersöka detaljerad information om potentiella fel.

- **Skärva-medvetenhet**

  Tjänsten split-merge skiljer mellan (1) fragmenterade tabeller, (2) referenstabeller och (3) normala tabeller. Semantiken för en split/merge/move-åtgärd beror på vilken typ av tabell som används och definieras på följande sätt:

  - **Fragmenterade tabeller**

    Dela, sammanfoga och flytta åtgärder flyttar fragment från källa till målsvant. Efter att den övergripande begäran har slutförts finns inte längre dessa shardlets på källan. Observera att måltabellerna måste finnas på målsvanten och får inte innehålla data i målområdet före bearbetningen av åtgärden.

  - **Referenstabeller**

    För referenstabeller kopierar delnings-, kopplings- och flyttningsåtgärderna data från källan till målsvanten. Observera dock att inga ändringar sker på målsvantumvanten för en viss tabell om det redan finns någon rad i den här tabellen i målet. Tabellen måste vara tom för att alla referenstabellkopieringsoperationer ska kunna bearbetas.

  - **Andra tabeller**

    Andra tabeller kan finnas på antingen källan eller målet för en split och merge-åtgärd. Tjänsten split-merge ignorerar dessa tabeller för dataförflyttningar eller kopieringsåtgärder. Observera dock att de kan störa dessa åtgärder i händelse av begränsningar.

    Informationen om referens kontra fragmenterade tabeller tillhandahålls `SchemaInfo` av API:erna på fragmentkartan. I följande exempel visas användningen av dessa API:er på ett visst fragmentkarthanteraresobjekt:

    ```csharp
    // Create the schema annotations
    SchemaInfo schemaInfo = new SchemaInfo();

    // reference tables
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region"));
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation"));

    // sharded tables
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY"));
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY"));

    // publish
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo);
    ```

    Tabellerna "region" och "nation" definieras som referenstabeller och kopieras med split-/merge/move-åtgärder. "kund" och "order" definieras i sin tur som fragmenterade tabeller. `C_CUSTKEY`och `O_CUSTKEY` fungera som skärva nyckeln.

- **Referensintegritet**

  Tjänsten split-merge analyserar beroenden mellan tabeller och använder externa nyckelnyckelrelationer för att arrangera åtgärderna för att flytta referenstabeller och shardlets. I allmänhet kopieras referenstabeller först i beroendeordning och sedan kopieras shardletar i ordning efter deras beroenden inom varje batch. Detta är nödvändigt så att FK-PK-begränsningar för målsvantvanten respekteras när de nya data anländer.

- **Fragmentkarta konsekvens och eventuellt slutförande**

  I närvaro av fel återupptar tjänsten split-merge åtgärder efter eventuella avbrott och syftar till att slutföra alla pågående begäranden. Det kan dock finnas oåterkalleliga situationer, t.ex. Under dessa omständigheter kan vissa shardlets som skulle flyttas fortsätta att finnas på källskärvan. Tjänsten säkerställer att fragmentmappningar endast uppdateras efter att nödvändiga data har kopierats till målet. Shardlets tas bara bort på källan när alla deras data har kopierats till målet och motsvarande mappningar har uppdaterats. Borttagningen sker i bakgrunden medan intervallet redan är online på målsvanten. Tjänsten split-merge säkerställer alltid korrektheten av de mappningar som lagras i fragmentkartan.

## <a name="the-split-merge-user-interface"></a>Användargränssnittet för delad koppling

Tjänstpaketet för delad koppling innehåller en arbetsroll och en webbroll. Webbrollen används för att skicka begäranden om delad koppling på ett interaktivt sätt. De viktigaste komponenterna i användargränssnittet är följande:

- **Åtgärdstyp**

  Åtgärdstypen är en alternativknapp som styr den typ av åtgärd som utförs av tjänsten för den här begäran. Du kan välja mellan scenarier för delning, koppling och flyttning. Du kan också avbryta en tidigare skickad åtgärd. Du kan använda delnings-, kopplings- och flyttbegäranden för intervallsarvkartor. Lista fragmentkartor stöder bara flyttåtgärder.

- **Shard Karta**

  Nästa avsnitt av parametrar för begäran täcker information om fragmentkartan och databasen som är värd för fragmentkartan. I synnerhet måste du ange namnet på Azure SQL Database-servern och databasen som är värd för fragmentkartan, autentiseringsuppgifter för att ansluta till fragmentkartdatabasen och slutligen namnet på fragmentkartan. För närvarande accepterar åtgärden bara en enda uppsättning autentiseringsuppgifter. Dessa autentiseringsuppgifter måste ha tillräcklig behörighet för att utföra ändringar i fragmentmappningen samt användardata för shards.

- **Källområde (dela och sammanfoga)**

  En split and merge-åtgärd bearbetar ett intervall med hjälp av dess låga och höga nyckel. Om du vill ange en åtgärd med ett obundet högt nyckelvärde markerar du kryssrutan "Högnyckel är max" och lämnar fältet med hög nyckel tomt. De intervallnyckelvärden som du anger behöver inte exakt matcha en mappning och dess gränser i fragmentkartan. Om du inte anger några intervallgränser alls kommer tjänsten att dra slutsatsen att det närmaste intervallet för dig automatiskt. Du kan använda PowerShell-skriptet GetMappings.ps1 för att hämta de aktuella mappningarna i en viss fragmentkarta.

- **Beteende för delad källa (delat)**

  För delade operationer definierar du punkten för att dela källområdet. Du gör detta genom att ange sharding-nyckeln där du vill att delningen ska ske. Använd alternativknappen ange om du vill att den nedre delen av intervallet (exklusive delningstangenten) ska flyttas, eller om du vill att den övre delen ska flytta (inklusive delningstangenten).

- **Källa Shardlet (flytta)**

  Flyttåtgärder skiljer sig från delnings- eller sammanfogningsåtgärder eftersom de inte kräver ett intervall för att beskriva källan. En källa för flyttning identifieras helt enkelt av det sharding-nyckelvärde som du planerar att flytta.

- **Målskärva (delad)**

  När du har angett informationen om källan till din delningsåtgärd måste du definiera var du vill att data ska kopieras till genom att tillhandahålla Azure SQL Db-servern och databasnamnet för målet.

- **Målområde (sammanfogning)**

  Sammanfoga åtgärder flyttar shardlets till en befintlig shard. Du identifierar den befintliga fragmentet genom att ange intervallgränserna för det befintliga intervallet som du vill sammanfoga med.

- **Batchstorlek**

  Batchstorleken styr antalet shardlets som kommer att gå offline vid en tidpunkt under dataflyttningen. Det här är ett heltalsvärde där du kan använda mindre värden när du är känslig för långa stilleståndsperioder för shardlets. Större värden ökar tiden som en viss shardlet är offline men kan förbättra prestanda.

- **Åtgärds-ID (avbryt)**

  Om du har en pågående åtgärd som inte längre behövs kan du avbryta åtgärden genom att ange dess operations-ID i det här fältet. Du kan hämta åtgärds-ID:t från statustabellen för begäran (se avsnitt 8.1) eller från utdata i webbläsaren där du skickade begäran.

## <a name="requirements-and-limitations"></a>Krav och begränsningar

Den nuvarande implementeringen av split-merge-tjänsten omfattas av följande krav och begränsningar:

- Shards måste finnas och registreras i fragmentkartan innan en split-merge-åtgärd på dessa shards kan utföras.
- Tjänsten skapar inte tabeller eller andra databasobjekt automatiskt som en del av dess åtgärder. Det innebär att schemat för alla fragmenterade tabeller och referenstabeller måste finnas på målsvanten före en delnings-/sammanfognings-/flyttåtgärd. Särskilt fragmenterade tabeller måste vara tomma i det område där nya shardlets ska läggas till genom en split/merge/move-åtgärd. Annars misslyckas åtgärden den första konsekvenskontrollen på målsvanten. Observera också att referensdata endast kopieras om referenstabellen är tom och att det inte finns några konsekvensgarantier för andra samtidiga skrivåtgärder i referenstabellerna. Vi rekommenderar detta: när du kör split/merge-åtgärder gör inga andra skrivåtgärder ändringar i referenstabellerna.
- Tjänsten är beroende av radidentitet som upprättats av ett unikt index eller en unik nyckel som innehåller sharding-nyckeln för att förbättra prestanda och tillförlitlighet för stora shardlets. Detta gör det möjligt för tjänsten att flytta data på en ännu finare granularitet än bara sharding nyckelvärdet. Detta bidrar till att minska den maximala mängden loggutrymme och lås som krävs under operationen. Överväg att skapa ett unikt index eller en primärnyckel, inklusive sharding-tangenten i en viss tabell om du vill använda tabellen med begäranden om delning/koppling/flytta. Av prestandaskäl bör sharding-tangenten vara den inledande kolumnen i nyckeln eller indexet.
- Under bearbetningen av begäran kan vissa shardlet-data finnas både på käll- och målshardvan. Detta är nödvändigt för att skydda mot fel under skärva rörelsen. Integreringen av split-merge med fragmentkartan säkerställer att anslutningar via databeroende routnings-API:er med metoden **OpenConnectionForKey** på fragmentkartan inte ser några inkonsekventa mellanliggande tillstånd. Men när du ansluter till källan eller målsvandrarna utan att använda **Metoden OpenConnectionForKey** kan inkonsekventa mellanliggande tillstånd vara synliga när begäranden om delning/koppling/flytt pågår. Dessa anslutningar kan visa partiella eller duplicerade resultat beroende på tidpunkten eller den fragment som ligger till grund för anslutningen. Den här begränsningen innehåller för närvarande anslutningar som gjorts av Elastisk skala Multi-Shard-Frågor.
- Metadatadatabasen för tjänsten för delad koppling får inte delas mellan olika roller. En roll för tjänsten för delad koppling som körs i mellanlagring måste till exempel peka på en annan metadatadatabas än produktionsrollen.

## <a name="billing"></a>Fakturering

Tjänsten för delad koppling körs som en molntjänst i din Microsoft Azure-prenumeration. Därför gäller avgifter för molntjänster för din instans av tjänsten. Om du inte ofta utför delnings-/sammanfognings-/flyttåtgärder rekommenderar vi att du tar bort molntjänsten för delad koppling. Det sparar kostnader för att köra eller distribuera molntjänstinstanser. Du kan distribuera om och starta din lätt körbara konfiguration när du behöver utföra delnings- eller kopplingsåtgärder.

## <a name="monitoring"></a>Övervakning

### <a name="status-tables"></a>Statustabeller

Tjänsten för delad koppling tillhandahåller tabellen **RequestStatus** i metadataarkivdatabasen för övervakning av slutförda och pågående begäranden. Tabellen visar en rad för varje begäran om delad koppling som har skickats till den här instansen av tjänsten för delad koppling. Den ger följande information för varje begäran:

- **Tidsstämpel**

  Tid och datum då begäran startades.

- **OperationId (på)**

  Ett GUID som unikt identifierar begäran. Den här begäran kan också användas för att avbryta åtgärden medan den fortfarande pågår.

- **Status**

  Det aktuella tillståndet för begäran. För pågående begäranden visas också den aktuella fasen där begäran är.

- **AvbrytRequest**

  En flagga som anger om begäran har avbrutits.

- **Framsteg**

  En procentuell uppskattning av slutförande för operationen. Värdet 50 anger att operationen är cirka 50 % klar.

- **Detaljer**

  Ett XML-värde som ger en mer detaljerad lägesrapport. Förloppsrapporten uppdateras regelbundet när rader kopieras från källa till mål. Vid fel eller undantag innehåller den här kolumnen också mer detaljerad information om felet.

### <a name="azure-diagnostics"></a>Microsoft Azure Diagnostics

Tjänsten för delad koppling använder Azure Diagnostics baserat på Azure SDK 2.5 för övervakning och diagnostik. Du styr diagnostikkonfigurationen enligt beskrivningen här: [Aktivera diagnostik i Azure Cloud Services och Virtual Machines](../cloud-services/cloud-services-dotnet-diagnostics.md). Hämtningspaketet innehåller två diagnostikkonfigurationer – en för webbrollen och en för arbetarrollen. Den innehåller definitioner för att logga prestandaräknare, IIS-loggar, Windows-händelseloggar och händelseloggar för delad koppling.

## <a name="deploy-diagnostics"></a>Distribuera diagnostik

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. För dessa cmdlets finns i [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är i stort sett identiska.

Om du vill aktivera övervakning och diagnostik med hjälp av diagnostikkonfigurationen för webb- och arbetsrollerna som tillhandahålls av NuGet-paketet kör du följande kommandon med Azure PowerShell:

```powershell
$storageName = "<azureStorageAccount>"
$key = "<azureStorageAccountKey"
$storageContext = New-AzStorageContext -StorageAccountName $storageName -StorageAccountKey $key
$configPath = "<filePath>\SplitMergeWebContent.diagnostics.xml"
$serviceName = "<cloudServiceName>"

Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext `
    -DiagnosticsConfigurationPath $configPath -ServiceName $serviceName `
    -Slot Production -Role "SplitMergeWeb"

Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext `
    -DiagnosticsConfigurationPath $configPath -ServiceName $serviceName `
    -Slot Production -Role "SplitMergeWorker"
```

Du hittar mer information om hur du konfigurerar och distribuerar diagnostikinställningar här: [Aktivera diagnostik i Azure Cloud Services och Virtual Machines](../cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="retrieve-diagnostics"></a>Hämta diagnostik

Du kan enkelt komma åt diagnostiken från Visual Studio Server Explorer i Azure-delen av Server Explorer-trädet. Öppna en Visual Studio-förekomst och klicka på Visa och Serverutforskaren i menyraden. Klicka på Azure-ikonen för att ansluta till din Azure-prenumeration. Navigera sedan till Azure -> Storage -> `<your storage account>` ->-tabeller -> WADLogsTable. Mer information finns i [Server Explorer](https://msdn.microsoft.com/library/x603htbk.aspx).

![WADLogsTable][2]

DEN WADLogsTable som visas i figuren ovan innehåller detaljerade händelser från den delade sammanfogningstjänstens programlogg. Observera att standardkonfigurationen för det hämtade paketet är inriktad på en produktionsdistribution. Därför är intervallet med vilket loggar och räknare hämtas från tjänstinstanserna stort (5 minuter). För test och utveckling, sänk intervallet genom att justera diagnostikinställningarna för webben eller arbetarrollen efter dina behov. Högerklicka på rollen i Utforskaren för Visual Studio Server (se ovan) och justera sedan överföringsperioden i dialogrutan för konfigurationsinställningarna för diagnostik:

![Konfiguration][3]

## <a name="performance"></a>Prestanda

I allmänhet kan bättre prestanda förväntas från de högre och mer högpresterande tjänstnivåerna i Azure SQL Database. Högre IO-, CPU- och minnesallokeringar för de högre tjänstnivåerna gynnar masskopierings- och borttagningsåtgärder som tjänsten för delad koppling använder. Öka därför tjänstnivån bara för dessa databaser under en definierad, begränsad tidsperiod.

Tjänsten utför också valideringsfrågor som en del av sin normala verksamhet. Dessa valideringsfrågor kontrollerar om det finns oväntad närvaro av data i målområdet och säkerställer att alla delnings-/kopplings-/flyttningsåtgärd startar från ett konsekvent tillstånd. Dessa frågor arbetar över fragmentering nyckelintervall som definieras av omfattningen av operationen och batchstorlek som tillhandahålls som en del av begäran definitionen. Dessa frågor fungerar bäst när ett index finns som har sharding-tangenten som den inledande kolumnen.

Dessutom gör en unik egenskap med sharding-nyckeln som den inledande kolumnen att tjänsten kan använda en optimerad metod som begränsar resursförbrukningen när det gäller loggutrymme och minne. Den här unika egenskapen krävs för att flytta stora datastorlekar (vanligtvis över 1 GB).

## <a name="how-to-upgrade"></a>Så här uppgraderar du

1. Följ stegen i [Distribuera en tjänst för delad koppling](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
2. Ändra konfigurationsfilen för molntjänsten för distributionen av delad koppling så att den återspeglar de nya konfigurationsparametrarna. En ny obligatorisk parameter är informationen om certifikatet som används för kryptering. Ett enkelt sätt att göra detta är att jämföra den nya konfigurationsmallfilen från hämtningen mot din befintliga konfiguration. Se till att du lägger till inställningarna för "DataEncryptionPrimaryCertificateThumbprint" och "DataEncryptionPrimary" för både webben och arbetarrollen.
3. Innan du distribuerar uppdateringen till Azure, se till att alla som körs split-merge åtgärder har avslutats. Du kan enkelt göra detta genom att fråga tabellerna RequestStatus och PendingWorkflows i metadatadatabasen för delad koppling för pågående begäranden.
4. Uppdatera din befintliga molntjänstdistribution för delad koppling i din Azure-prenumeration med det nya paketet och den uppdaterade tjänstkonfigurationsfilen.

Du behöver inte etablera en ny metadatadatabas för delad koppling för att uppgradera. Den nya versionen uppgraderar automatiskt den befintliga metadatadatabasen till den nya versionen.

## <a name="best-practices--troubleshooting"></a>Metodtips och felsökning

- Definiera en testklient och utnyttja dina viktigaste split/merge/move-åtgärder med testklienten över flera shards. Kontrollera att alla metadata har definierats korrekt i fragmentkartan och att åtgärderna inte bryter mot begränsningar eller externa nycklar.
- Håll testklientdatastorleken över den maximala datastorleken för din största klient för att säkerställa att du inte stöter på datastorleksrelaterade problem. Detta hjälper dig att bedöma en övre gräns på den tid det tar att flytta en enda klient runt.
- Kontrollera att schemat tillåter borttagningar. Tjänsten delad koppling kräver möjligheten att ta bort data från källsvantvanten när data har kopierats till målet. Ta till exempel **utlösare** kan förhindra att tjänsten tar bort data på källan och kan orsaka att åtgärder misslyckas.
- Sharding-nyckeln ska vara den inledande kolumnen i primärnyckeln eller den unika indexdefinitionen. Det säkerställer bästa prestanda för delnings- eller sammanfogningsverifieringsfrågor och för de faktiska dataflyttnings- och borttagningsåtgärder som alltid fungerar på fragmenteringsnyckelintervall.
- Samlokalera din tjänst för delad koppling i den region och det datacenter där databaserna finns.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png
