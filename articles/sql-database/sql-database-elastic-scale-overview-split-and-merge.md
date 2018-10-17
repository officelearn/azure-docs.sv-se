---
title: Flytta data mellan utskalade molndatabaser | Microsoft Docs
description: 'Beskriver hur du manipulera fragment och flytta data via en lokal tjänst med elastisk databas API: er.'
services: sql-database
ms.service: sql-database
ms.subservice: elastic-scale
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: fb87a67d84588b5199a5d31530530d5afb7985e7
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353690"
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>Flytta data mellan utskalade molndatabaser

Om du är en programvara som en tjänst-utvecklare och plötsligt enorma begäran görs i din app, måste du hantera tillväxt. Så får du lägga till flera databaser (fragment). Hur du för att distribuera om data till de nya databaserna utan att störa dataintegriteten? Använd den **dela / sammanslå verktyget** att flytta data från databaser som är begränsad till de nya databaserna.  

Dela / sammanslå verktyget körs som en Azure-webbtjänst. En administratör eller utvecklare använder du verktyget för att flytta shardletar (data från en shard) mellan olika databaser (fragment). Verktyget använder fragmentkarthantering för att upprätthålla tjänstens metadata-databasen och säkerställa konsekvent mappningar.

![Översikt][1]

## <a name="download"></a>Ladda ned

[Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>Dokumentation

1. [Elastiska databaser dela och slå samman verktyget självstudien](sql-database-elastic-scale-configure-deploy-split-and-merge.md)
2. [Dela / Sammanslå säkerhetskonfiguration](sql-database-elastic-scale-split-merge-security-configuration.md)
3. [Säkerhetsöverväganden för dela / sammanslå](sql-database-elastic-scale-split-merge-security-configuration.md)
4. [Karthantering för shard](sql-database-elastic-scale-shard-map-management.md)
5. [Migrera befintliga databaser för att skala ut](sql-database-elastic-convert-to-use-elastic-tools.md)
6. [Verktyg för elastiska databaser](sql-database-elastic-scale-introduction.md)
7. [Ordlista för verktyg för elastiska databaser](sql-database-elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Varför ska man använda verktyget för dela / sammanslå

- **Flexibilitet**

  Program behöva stretch flexibelt längre än till en enskild Azure SQL DB-databas. Använd verktyget för att flytta data som behövs för att nya databaser samtidigt som du behåller integritet.

- **Dela att växa**

  Skapa ytterligare kapacitet med horisontell partitionering data för att öka totala kapaciteten att hantera explosiv tillväxt och genom att distribuera den över stegvis fler databaser tills kapacitetsbehov är uppfyllda. Det här är ett typiskt exempel av den **dela** funktionen.

- **Slå samman för att minska**

  Kapacitet behöver krympa på grund av ett företag säsongsbetonad. Verktyget kan du skala ned till färre skalningsenheter när företag saktar. Funktionen ”dokument” i tjänsten elastisk skalning dela / sammanslå täcker det här kravet.

- **Hantera anslutningar genom att flytta shardletar**

  Med flera innehavare per databas, kan fördelningen av shardletar till shards leda till kapacitet flaskhalsar på vissa fragment. Detta kräver omfördelning av shardletar eller för att flytta upptagen shardletar till nya eller mindre utnyttjade shards.

## <a name="concepts--key-features"></a>Koncept och viktiga funktioner

- **Kund-baserade tjänster**

  Dela / sammanslå levereras som en tjänst som värd för kunden. Du måste distribuera och hantera tjänsten i din Microsoft Azure-prenumeration. Paketet du ladda ned från NuGet innehåller en konfigurationsmall för att slutföra med information för din specifika distribution. Se den [dela / sammanslå självstudien](sql-database-elastic-scale-configure-deploy-split-and-merge.md) mer information. Eftersom tjänsten körs i Azure-prenumerationen kan du styra och konfigurera de flesta säkerhetsaspekter i tjänsten. Standardmallen innehåller alternativ för att konfigurera SSL, certifikatbaserad klientautentisering, kryptering för lagrade autentiseringsuppgifter, DoS-skydd och IP-begränsningar. Du hittar mer information om säkerhetsaspekterna i följande dokument [dela / sammanslå säkerhetskonfiguration](sql-database-elastic-scale-split-merge-security-configuration.md).

  Standard distribuerat service körs med en arbetare och en webbroll. Var och en använder A1 VM-storlek i Azure Cloud Services. Du inte kan ändra dessa inställningar när du distribuerar paketet, kan du ändra dem efter en lyckad distribution i körs Molntjänsten (via Azure portal). Observera att arbetsrollen inte måste konfigureras i mer än en enda instans av tekniska skäl.

- **Shard kartan integration**

  Dela / sammanslå tjänsten samverkar med fragmentkartan för programmet. När du använder tjänsten dela / sammanslå dela eller slå samman intervall eller för att flytta shardletar mellan shards, håller tjänsten automatiskt fragmentkartan aktuella. Om du vill göra det tjänsten ansluter till databasen av programmet och underhåller intervall och mappningar som pågår för dela/sammanslå/flytta begäranden. Detta säkerställer att fragmentkartan alltid anger aktuell när du ska dela / sammanslå åtgärder. Dela upp, implementeras sammanfoga och shardlet dataflyttsåtgärderna genom att flytta en batch med shardletar från källan shard till mål-fragment. Under åtgärden shardlet förflyttning shardletar omfattas av den aktuella batchen markeras som offline i fragmentkartan och är inte tillgängliga för databeroende routning-anslutningar som använder den **OpenConnectionForKey** API.

- **Konsekvent shardlet anslutningar**

  Dataförflyttning att starta en ny grupp med shardletar, tillhandahålls alla fragmentkartan databeroende routning anslutningar till fragment som lagrar shardlet har avslutats och efterföljande anslutningar från fragmentkartan API: er till shardletar blockeras medan dataförflyttning pågår för att undvika inkonsekvenser. Anslutningar till andra shardletar på samma fragment också få avslutas, men lyckas igen omedelbart på försök igen. När batchen flyttas shardletar markeras online igen för mål-fragment och källdata tas bort från käll-fragment. Tjänsten går igenom de här stegen för varje batch tills alla shardletar har flyttats. Detta leder till flera anslutningsåtgärder kill under loppet av dela/sammanslå/flytta kompletteringsåtgärden.  

- **Hantera shardlet tillgänglighet**

  Begränsa anslutningen avslutar den aktuella gruppen med shardletar som beskrivs ovan begränsar omfattningen för otillgänglighet till en batch med shardletar i taget. Detta är att föredra över en metod där det fullständiga fragmentet skulle vara offline för alla shardletar under loppet av en delad tunnel eller merge-åtgärd. Storleken på en batch definieras som antalet distinkta shardletar att flytta samtidigt, är en konfigurationsparameter. Det kan definieras för varje dela och slå samman åtgärd beroende på programmets tillgänglighet och prestanda behov. Observera att det adressintervall som håller på att låsas i fragmentkartan kan vara större än den angivna batchstorleken. Det beror på att tjänsten hämtar intervallet storlek så att det faktiska antalet nyckelvärden för horisontell partitionering i data matchar cirka batchstorleken. Detta är viktigt att komma ihåg särskilt för sparsamt ifyllda horisontell partitionering nycklar.

- **Lagring för metadata**

  Dela / sammanslå-tjänsten använder en databas att upprätthålla dess status och att hålla loggar under bearbetning av begäran. Du skapar den här databasen i sin prenumeration samt anslutningssträngen för den i konfigurationsfilen för service-distributionen. Administratörer från användarens organisation kan också ansluta till den här databasen att granska begäran om förlopp och undersöka detaljerad information om potentiella fel.

- **Horisontell partitionering medvetenhet**

  Dela / sammanslå tjänsten skiljer mellan (1) shardade tabeller, (2) referenstabeller och (3) vanliga tabeller. Semantiken för en åtgärd för dela/sammanslå/flytta beror på vilken typ av tabellen används och definieras enligt följande:

  - **Shardade tabeller**

    Dela, slå samman och flytta operations flytta shardletar från källa till mål-fragment. När installationen har slutförts för övergripande begäran finns dessa shardletar inte längre på källan. Observera att måltabeller måste finnas på mål-fragment och får inte innehålla data i målområde innan bearbetningen av åtgärden.

  - **Referenstabeller**

    För referenstabeller, delning, slå samman och flytta operations kopiera data från källan till målet fragment. Observera att inga ändringar inträffar på mål-fragment för en viss tabell om det finns redan en rad i den här tabellen på målet. Tabellen måste vara tom för valfri referens tabell kopieringen ska bearbetas.

  - **Andra tabeller**

    Andra tabeller kan finnas på källan eller målet för en dela och slå samman åtgärd. Dela / sammanslå service ignorerar tabellerna för dataförflyttning och kopieringsåtgärder. Observera dock att de stör dessa åtgärder vid begränsningar.

    Information om referens jämfört med delat tabeller kommer från den `SchemaInfo` API: er på fragmentkartan. I följande exempel illustrerar användningen av dessa API: er på ett visst fragment kartan manager-objekt:

    ```c#
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
    ```

    Tabeller ”region” och ”nation” definieras som referenstabeller och kommer att kopieras med dela/sammanslå/flytta åtgärder. ”kund” och ”order” definieras i sin tur som shardade tabeller. `C_CUSTKEY` och `O_CUSTKEY` fungerar som nyckeln för horisontell partitionering.

- **Referensintegritet**

  Dela / sammanslå tjänsten analyserar alla beroenden mellan tabeller och använder nyckel-primära sekundärnyckelrelationer för att mellanlagra åtgärder för att flytta referenstabeller och shardletar. I allmänhet kopieras referenstabeller först i beroendeordningen, och sedan shardletar kopieras i ordning beroenden inom varje grupp. Detta är nödvändigt för att FK PK-begränsningar i mål-fragment respekteras när nya data anländer.

- **Shard kartan konsekvens och eventuell slutförande**

  Om det förekommer fel, dela och slå samman tjänsten återupptar åtgärder efter eventuella avbrott och syftar till att slutföra i förloppet begäranden. Men det kan finnas ett oåterkalleligt situationer, t.ex. när mål-fragment tappas bort eller komprometteras inte repareras. Under dessa omständigheter fortsätta vissa shardletar som skulle flyttas att finnas på käll-fragment. Tjänsten säkerställer shardlet mappningar uppdateras bara när nödvändiga data har har kopierats till målet. Shardletar endast tas bort på källan när alla data har kopierats till målet och de motsvarande mappningarna har uppdaterats. Borttagningen sker i bakgrunden medan intervallet är redan online på mål-fragment. Dela / sammanslå tjänsten garanterar alltid är korrekt mappning som lagras i fragmentkartan.

## <a name="the-split-merge-user-interface"></a>Användargränssnittet för dela / sammanslå

Dela / sammanslå service-paketet innehåller en arbetsroll och en webbroll. Webbrollen används för att skicka förfrågningar om dela och slå samman i ett interaktivt sätt. Huvudkomponenterna i användargränssnittet är följande:

- **Åtgärdstyp**

  Typ av åtgärd är en alternativknapp som styr vilken typ av åtgärd som utförs av tjänsten för den här begäran. Du kan välja mellan delning, slå samman och flytta scenarier. Du kan också avbryta en tidigare har skickats. Du kan använda dela, slå samman och flytta begäranden för intervallet fragmentkartor. Lista shardkartor endast stöd för flyttåtgärder.

- **Fragmentkartan**

 Nästa avsnitt av parametrarna som innehåller information om fragmentkartan och databasen som är värd för din fragmentkartan. I synnerhet måste du ange namnet på Azure SQL Database-servern och databasen som är värd shardmap, autentiseringsuppgifter för att ansluta till fragment kartan databasen och slutligen på namnet på fragmentkartan. Åtgärden accepterar för närvarande endast en enda uppsättning autentiseringsuppgifter. Dessa autentiseringsuppgifter måste ha tillräcklig behörighet för att utföra ändringar i fragmentkartan samt att användardata på shards.

- **Käll-intervall (dela och slå samman)**

  En åtgärd med dela och slå samman bearbetar ett intervall med hjälp av dess låga och höga nyckel. Om du vill ange en åtgärd med ett obundna hög nyckelvärde, markera kryssrutan ”övre nyckel är max” och lämnar hög nyckelfältet tom. Intervallet nyckelvärden som du anger måste inte exakt matcha en mappning och dess gränser i din fragmentkartan. Om du inte anger någon adressintervallsgränser alls kommer tjänsten härleda närmaste intervallet åt dig automatiskt. Du kan använda GetMappings.ps1 PowerShell-skript för att hämta de aktuella mappningarna i en viss fragmentkartan.

- **Dela källa beteende (delad)**

  Definiera punkt om du vill dela käll-intervallet för dela-åtgärder. Du kan göra detta genom att tillhandahålla shardingnyckel där du vill att delning ska ske. Använd knappen Ange om du vill den nedre delen av intervallet (förutom den delade nyckeln) för att flytta eller om du vill att den övre delen att flytta (inklusive den delade nyckeln).

- **Källan Shardlet (flytta)**

  Flytta skiljer sig från dela eller merge-åtgärder eftersom de inte kräver ett intervall för att beskriva källan. En källa för flytt identifieras bara med horisontell partitionering nyckelvärdet som du planerar att flytta.

- **Mål-fragment (delad)**

  När du har angett informationen på källan för split-åtgärden, måste du definiera där du vill att data ska kopieras till genom att ange Azure SQL Db-server och databasnamnet för målet.

- **Målområde (merge)**

  Sammanfoga operations flytta shardletar till en befintlig shard. Du kan identifiera det befintliga fragmentet genom att tillhandahålla adressintervallsgränser för befintliga intervallet som du vill sammanfoga med.

- **Batchstorlek**

  Batchstorleken styr antalet shardletar går offline i taget under dataförflyttning. Det här är ett heltalsvärde där du kan använda lägre värden när du är känsliga för långa stilleståndsperioder för shardletar. Högre värden ökar den tid som en viss shardlet är offline men kan du förbättra prestandan.

- **Åtgärds-ID (Avbryt)**

  Om du har en pågående åtgärd som inte längre behövs kan avbryta du åtgärden genom att ange dess åtgärds-ID i det här fältet. Du kan hämta åtgärds-ID från statustabellen för begäran (se avsnittet 8.1) eller från utdata i webbläsaren där du skickade begäran.

## <a name="requirements-and-limitations"></a>Krav och begränsningar

Den aktuella implementationen av tjänsten dela / sammanslå lyder under följande krav och begränsningar:

- Shards måste finnas och vara registrerade i fragmentkartan innan en åtgärd för dela / sammanslå i dessa fragment kan utföras.
- Tjänsten skapar inte tabeller eller andra databasobjekt automatiskt som en del av driften. Det innebär att schemat för alla shardade tabeller och referenstabeller måste finnas på mål-fragment före alla åtgärder för dela/sammanslå/flytta. Shardade tabeller måste i synnerhet vara tomt i intervallet där nya shardletar ska läggas till av en åtgärd för dela/sammanslå/flytta. I annat fall misslyckas åtgärden första konsekvenskontrollen för mål-fragment. Tänk också på den referens som data kopieras bara om referensen tabellen är tom och att det finns inga konsekvensgarantier avseende andra samtidiga skrivningsåtgärder på referenstabeller. Vi rekommenderar detta: när du kör dela/sammanslå åtgärder kan inga andra skrivåtgärder göra ändringar i tabellerna referens.
- Tjänsten är beroende av rad-identiteten som etablerats med ett unikt index eller nyckeln som innehåller nyckeln för horisontell partitionering för att förbättra prestanda och tillförlitlighet för stora shardletar. På så sätt kan tjänsten för att flytta data på en ännu finare granularitet än bara nyckelvärdet horisontell partitionering. Detta hjälper till att minska den maximala mängden loggutrymmet och lås som krävs under åtgärden. Överväg att skapa ett unikt index eller primärnyckel inklusive shardingnyckel i en viss tabell om du vill använda tabellen med dela/sammanslå/flytta begäranden. Av prestandaskäl bör nyckeln för horisontell partitionering vara den ledande kolumnen i nyckeln eller index.
- Under bearbetning av begäran kanske vissa shardlet data som finns både på käll- och mål-fragment. Detta är nödvändigt att skydda mot fel under shardlet flödet. Integreringen av dela och slå samman med fragmentkartan säkerställer att anslutningar via en databeroende routning API: er med hjälp av den **OpenConnectionForKey** -metoden i fragmentkartan inte ser alla mellanliggande inkonsekvent tillstånd. Men när du ansluter till källan eller målet shards utan att använda den **OpenConnectionForKey** metoden inkonsekvent mellanliggande tillstånd kan vara synliga när du ska dela/sammanslå/flytta begäranden. Dessa anslutningar kan visa partiell eller dubbla resultat beroende på tidpunkten eller fragment underliggande anslutningen. Den här begränsningen innehåller för närvarande anslutningar av Elastic Scale Multi-Factor-Shard-frågor.
- Metadata-databasen för dela / sammanslå tjänsten delas inte mellan olika roller. Till exempel måste en roll för dela / sammanslå tjänsten som körs i Förproduktion peka på en annan metadata-databasen än rollen produktion.

## <a name="billing"></a>Fakturering

Dela / sammanslå-tjänsten körs som en molntjänst i Microsoft Azure-prenumerationen. Därför gäller avgifter för cloud services för din instans av tjänsten. Såvida inte du ofta utföra åtgärder för dela/sammanslå/flytta, rekommenderar vi du tar bort dela / sammanslå Molntjänsten. Som sparar kostnader för att köra eller distribueras cloud service-instanser. Du kan distribuera och starta lätt att köra flödet konfigurationen när du behöver att utföra delad tunnel eller merge-åtgärder.

## <a name="monitoring"></a>Övervakning

### <a name="status-tables"></a>Status för tabeller

Dela / sammanslå Service tillhandahåller den **RequestStatus** tabellen i store metadatabasen för övervakning av färdiga och pågående begäranden. Tabellen innehåller en rad för varje begäran för dela / sammanslå som har skickats till den här instansen av tjänsten dela och slå samman. Det ger följande information för varje begäran:

- **Tidsstämpel**

  Tid och datum när begäran startade.

- **Åtgärds-ID**

  Ett GUID som unikt identifierar begäran. Den här förfrågan kan också användas för att avbryta åtgärden medan det pågår fortfarande.

- **Status**

  Det aktuella tillståndet för begäran. För förfrågningar om pågående visas även aktuell fas där begäran är.

- **CancelRequest**

  En flagga som anger om begäran har avbrutits.

- **Pågår**

  En procentandel uppskattning av åtgärden har slutförts. Ett värde på 50 anger att åtgärden är klar ungefär 50%.

- **Detaljer**

  Ett XML-värde som ger en mer detaljerad rapport. Förloppet-rapporten uppdateras regelbundet när uppsättningar med rader kopieras från källan till målet. Den här kolumnen innehåller också mer detaljerad information om felet vid fel eller undantag.

### <a name="azure-diagnostics"></a>Azure Diagnostics

Dela / sammanslå-tjänsten använder Azure Diagnostics baserat på Azure SDK 2.5 för övervakning och diagnostik. Du styr diagnostikkonfigurationen som beskrivs här: [hur du aktiverar diagnostik i Azure Cloud Services och virtuella datorer](../cloud-services/cloud-services-dotnet-diagnostics.md). Det nedladdade paketet innehåller två diagnostikkonfigurationer – en för webbrollen och en för arbetsrollen. Den innehåller definitioner för att logga prestandaräknare, IIS-loggar, händelseloggar i Windows och händelseloggar för dela / sammanslå program.

## <a name="deploy-diagnostics"></a>Distribuera diagnostik

Om du vill aktivera övervakning och diagnostik med diagnostik-konfiguration för webb- och worker-roller som tillhandahålls av NuGet-paketet, kör du följande kommandon med hjälp av Azure PowerShell:

```powershell
    $storage_name = "<YourAzureStorageAccount>"
    $key = "<YourAzureStorageAccountKey"
    $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key  
    $config_path = "<YourFilePath>\SplitMergeWebContent.diagnostics.xml"
    $service_name = "<YourCloudServiceName>"
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWeb"
    $config_path = "<YourFilePath>\SplitMergeWorkerContent.diagnostics.xml"
    $service_name = "<YourCloudServiceName>"
    Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Production -Role "SplitMergeWorker"
```

Du hittar mer information om hur du konfigurerar och distribuerar här diagnostikinställningar: [hur du aktiverar diagnostik i Azure Cloud Services och virtuella datorer](../cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="retrieve-diagnostics"></a>Hämta diagnostik

Du kan enkelt komma åt dina diagnostikdata från Visual Studio Server Explorer i Azure del i Server Explorer-trädet. Öppna Visual Studio-instansen och i menyraden klickar du på Visa och Server Explorer. Klicka på ikonen Azure för att ansluta till din Azure-prenumeration. Gå sedan till Azure Storage -> -> `<your storage account>` -> tabeller -> WADLogsTable. Mer information finns i [Server Explorer](https://msdn.microsoft.com/library/x603htbk.aspx).

![WADLogsTable][2]

WADLogsTable markerat i bilden ovan innehåller detaljerade händelser från programloggen för dela / sammanslå-tjänsten. Observera att standardkonfigurationen av det Hämta paketet riktar sig till en Produktionsdistribution. Därför är den period då loggar och räknare hämtas från tjänstinstanser stora (5 minuter). Lägre intervallet för testning och utveckling genom att justera inställningarna för startdiagnostik för webb- eller worker-roll efter dina behov. Högerklicka på rollen i Visual Studio Server Explorer (se ovan) och justera sedan överföra perioden i dialogrutan för konfigurationsinställningar för diagnostik:

![Konfiguration][3]

## <a name="performance"></a>Prestanda

I allmänhet är bättre prestanda kan förväntas från desto högre mer högpresterande tjänstnivåer i Azure SQL Database. Högre i/o, processor och minne allokeringar för högre tjänstnivåerna dra Masskopieringen och ta bort åtgärder som använder tjänsten dela och slå samman. Därför att öka tjänstnivån för dessa databaser för en definierad, begränsad tidsperiod.

Tjänsten utför även verifiering frågor som en del av dess normal drift. Frågorna verifiering kontrollera om oväntat finns data i intervallet mål och se till att alla åtgärder för dela/sammanslå/flytta startar från ett konsekvent tillstånd. De här frågorna som alla fungerar över horisontell partitionering nyckelintervall definieras av omfattningen av åtgärden och batchstorlek som tillhandahålls som en del av definitionen för förfrågningen. De här frågorna gör bäst ifrån sig när ett index finns som har shardingnyckel som ledande kolumn.

Dessutom kan kan en egenskap för unikhet med shardingnyckel som ledande kolumn tjänsten du använder en optimerad metod som begränsar resursförbrukning när det gäller loggutrymmet och minne. Den här egenskapen för unikhet krävs för att flytta stora datamängder (vanligtvis över 1GB).

## <a name="how-to-upgrade"></a>Så här uppgraderar du

1. Följ stegen i [distribuerar du en tjänst för dela / sammanslå](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
2. Ändra din molntjänstkonfigurationsfilen för dela / sammanslå distributionen så att de nya konfigurationsparametrarna. En ny obligatorisk parameter är information om certifikatet som används för kryptering. Ett enkelt sätt att göra detta är att jämföra den nya mall för konfigurationsfilen från nedladdningen mot din befintliga konfiguration. Kontrollera att du lägger till inställningarna för ”DataEncryptionPrimaryCertificateThumbprint” och ”DataEncryptionPrimary” för både webb- och worker-roll.
3. Se till att alla som körs dela / sammanslå åtgärder har slutförts innan du distribuerar uppdateringen till Azure. Du kan enkelt göra detta genom att fråga tabellerna RequestStatus och PendingWorkflows i dela / sammanslå metadata-databasen för pågående begäranden.
4. Uppdatera dina befintliga molntjänstdistribution för dela / sammanslå i Azure-prenumerationen med det nya paketet och uppdaterade konfigurationsfilen.

Du behöver inte etablera en ny databas för metadata för dela / sammanslå att uppgradera. Den nya versionen kommer automatiskt att uppgradera den befintliga metadata-databasen till den nya versionen.

## <a name="best-practices--troubleshooting"></a>Bästa metoder och felsökning

- Definiera en test-klient och arbeta med dina viktigaste dela/sammanslå/flytta åtgärder med testklienten över flera shard. Se till att alla metadata har definierats korrekt i din fragmentkartan och att åtgärderna inte bryter mot begränsningarna eller främmande nycklar.
- Behåll testklienten datastorlek ovanför den maximala datastorleken för största klienten för att se till att det inte uppstår några datastorlek-relaterade problem. Detta hjälper dig att utvärdera en övre gräns på den tid det tar för att flytta en enda klient.
- Kontrollera att ditt schema tillåter borttagningar. Dela / sammanslå-tjänsten kräver möjligheten att ta bort data från källan shard när data har har kopierats till målet. Till exempel **ta bort utlösare** kan förhindra att tjänsten från att ta bort data på källan och kan orsaka åtgärder misslyckas.
- Nyckeln för horisontell partitionering bör vara den ledande kolumnen i primärnyckeln eller unik indexdefinitionen. Som ger högsta prestanda för frågor som delad tunnel eller merge-verifiering och för faktiska data förflyttning och borttagning av åtgärder som använder alltid nyckelintervall för horisontell partitionering.
- Samordna dela / sammanslå tjänsten i regionen och data center där databasen finns.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/sql-database-elastic-scale-overview-split-and-merge/diagnostics-config.png
