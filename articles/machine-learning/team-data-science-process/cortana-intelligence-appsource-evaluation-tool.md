---
title: "Cortana Intelligence lösning utvärderingsverktyg | Microsoft Docs"
description: "Här är alla steg som du behöver utföra för att publicera Cortana Intelligence-lösningen till AppSource som Microsoft-Partner."
services: machine-learning
documentationcenter: 
author: AnupamMicrosoft
manager: jhubbard
editor: cgronlun
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/07/2017
ms.author: anupams;v-bruham;garye
ms.openlocfilehash: 8340ae1231b7f40f739d7bbb63cee1bf0f095ab5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="cortana-intelligence-solution-evaluation-tool"></a>Utvärderingsverktyg för Cortana Intelligence-lösningar
## <a name="overview"></a>Översikt
Du kan använda verktyget Cortana Intelligence lösning utvärdering för att bedöma din avancerade Analyslösningar kompatibiliteten med Microsofts rekommenderade säkerhetsmetoder. Microsoft är glada att arbeta med våra samarbetspartners (ISV: er / SIs) att tillhandahålla lösningar för hög kvalitet för kunder, återförsäljare och implementering. Den här guiden ska gå igenom hur du med hjälp av verktyget lösning utvärdering med din lösning och beskriver specifika metodtips i söker efter.

## <a name="getting-started"></a>Komma igång
Kontrollera [hämta](https://aka.ms/aa-evaluation-tool-download) och installera verktyget Cortana Intelligence lösning utvärdering.

Krav:
- Windows 10: [officiella platsen för Windows 10](https://www.microsoft.com/en-us/windows)
- Azure Powershell: [installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0).

## <a name="identifying-your-app"></a>Identifierar din app
När installationen är klar öppnar du verktyget och påbörja första utvärderingen.

![Öppna utvärderingsverktyg](./media/cortana-intelligence-appsource-evaluation-tool/1-open-evaluation-tool.png)

Ange information om din lösning.

![Anslut Azure-prenumeration](./media/cortana-intelligence-appsource-evaluation-tool/2-connect-azure-subscription.png)

Ansluta till din Azure-prenumeration och ange den resursgrupp som innehåller din app.

![Välj resurser](./media/cortana-intelligence-appsource-evaluation-tool/3-select-resources.png)

När resursgruppen har lästs in, markera de resurser som ingår i din lösning och identifiera dataresurser som antingen tillgänglighet:
- Införandet
- Förbrukning
- internt

Vi använder informationen för att bättre förstå hur din lösning är att använda olika komponenter och för att säkerställa användarinriktad komponenter stämmer överens med bästa praxis.

### <a name="ingestion"></a>Införandet
Införandet i det här fallet innebär alla eventuella datakällor som används för att ta emot data utanför lösningen eller några tjänster utanför lösningen använder för att skicka data till den.

### <a name="consumption"></a>Förbrukning
Förbrukning i det här fallet innebär alla datauppsättningar som används för att skicka data till slutanvändare, antingen direkt eller indirekt. Exempel:
- Datauppsättningar som används i direktfrågan från PowerBI.
- Datauppsättningar frågas i en WebApp.

>[!NOTE]
Om en viss resurs används för både införandet och förbrukning, Välj **förbrukning**.

### <a name="internal"></a>internt
Används internt för alla dataresurser som används bara i internt programbearbetning.

Därefter uppmanas du att ange giltiga autentiseringsuppgifter för alla databaser som anges i föregående steg:

![Ange test krav](./media/cortana-intelligence-appsource-evaluation-tool/4-set-test-prerequisites.png)

## <a name="solution-test-cases"></a>Lösning testfall
Verktyget lösning utför en mängd testerna på din lösning.

![Körning av test](./media/cortana-intelligence-appsource-evaluation-tool/5-set-test-execution.png)

När testen är klara, uppmanas du att ge en förklaring eller en motivering för varför din lösning uppfyller inte kravet.

![Ange en motivering](./media/cortana-intelligence-appsource-evaluation-tool/6-provide-business-justification.png)

Till exempel om din lösning publicerar till Azure SQL DW, kräver utvärdering testerna att du även publicera till Azure Analysis Services. 

Din lösning kan använda IaaS virtuella datorer som kör Sql Server Analysis Services i stället för Azure Analysis Services. Detta är en godkänd orsak till fel i testet.
## <a name="packaging-your-evaluation-results"></a>Paketera din utvärderingsresultat
När du har slutfört testfall utvärdering paketet kommer att exporteras till en zip-fil och du blir ombedd att ge feedback om utvärderingsverktyget. 

Du behöver dela det här testet resultat zip-filen med Microsoft för din lösning som ska utvärderas innan du hämtar godkännande som ska läggas till AppSource

![Utvärderingsverktyget för klass](./media/cortana-intelligence-appsource-evaluation-tool/7-grade-evaluation-tool.png)

Ovan avsnitt i det här artikeln beskriver olika funktioner i verktyget, låt oss nu granska typer av bästa praxis som resulterar i det här verktyget.

## <a name="security-evaluation-considerations"></a>Säkerhetsaspekter för utvärdering
### <a name="databases-should-use-azure-active-directory-authentication"></a>Databaser ska använda Azure Active Directory-autentisering
Azure SQL- eller Azure SQL DW resurser i sloution ska aktiveras med Azure Active Directory (AAD)-autentisering. AAD innehåller en enda plats för att hantera alla dina identiteter och roller.

| Mer information om | Finns den här artikeln |
| --- | --- |
| AAD med SQL Database och SQL Data Warehouse | [Använda Azure Active Directory-autentisering för autentisering med SQL Database eller SQL Data Warehouse](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication) |
| Konfigurera och hantera AAD | [Konfigurera och hantera Azure Active Directory-autentisering med SQL Database eller SQL Data Warehouse](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication-configure) |
| Autentisering med Azure Webbappar | [Autentisering och auktorisering i Azure App Service](https://docs.microsoft.com/en-us/azure/app-service/app-service-authentication-overview) |
| Konfigurera Webbappar i AAD | [Så här konfigurerar du din App tjänstprogram att använda Azure Active Directory-inloggning](https://docs.microsoft.com/en-us/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication)|

### <a name="datasets-accessible-to-end-users-should-support-role-based-access-control"></a>Datauppsättningar som är tillgängliga för slutanvändarna ska ha stöd för rollbaserad åtkomstkontroll
Köra utvärderingsverktyget blir du ombedd att ange reporting eller publicera resurser. Det förutsätts att resurserna är avsedda för åtkomst av slutanvändare, inte utvecklare. Dessa resurser bör ge rollbaserad åtkomstkontroll (RBAC) för att se till att slutanvändarna bara komma åt auktoriserade data.

Mer specifikt kan konfigureras med RBAC någon av följande Azure-resurser och anses acceptabla:
- Säker HDInsight, se [en introduktion till Hadoop-säkerhet med domänanslutna HDInsight-kluster](https://docs.microsoft.com/en-us/azure/hdinsight/hdinsight-domain-joined-introduction)
- Azure SQL Se [AAD-autentisering med Azure SQL]( https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication)
- Azure Analysis Services, se [hantera databasroller och användare för Azure Analysis Services](https://docs.microsoft.com/azure/analysis-services/analysis-services-database-users)
- Azure SQL Data Warehouse (Tänk på att eftersom SQL DW stöder RBAC, inte rekommenderas för direkta slutanvändarnas åtkomst.)

Om du använder en annan resurstyp som stöder RBAC kan ange som i testfall justering.

### <a name="azure-data-lake-store-should-use-at-rest-encryption"></a>Azure Data Lake Store ska använda kryptering i vila
Azure Data Lake Store (ADLS) stöder kryptering i vila som standard med hjälp av ADLS-hanterade krypteringsnycklar. Du kan också konfigurera kryptering med Azure Key Vault.

Information om hur du anger ADLS krypteringsinställningar [skapa ett Azure Data Lake Store-konto](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-get-started-portal#create-an-azure-data-lake-store-account).

### <a name="azure-sql-and-azure-sql-data-warehouse-should-use-encryption"></a>Azure SQL och Azure SQL Data Warehouse ska använda kryptering
Azure SQL och Azure SQL DW båda stöder Transparent Data kryptering (TDE), vilket ger realtid kryptering och dekryptering av både data och loggfiler.

| Mer information om | Finns den här artikeln |
| --- | --- |
| Transparent datakryptering (TDE) | [Transparent datakryptering](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-tde) |
| Azure SQL Data Warehouse med TDE | [SQL Data Warehouse Encrption TDE TSQL](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-encryption-tde-tsql) |
| Konfigurera Azure SQL med TDE | [Transparent datakryptering med Azure SQL Database](https://docs.microsoft.com/en-us/sql/relational-databases/security/encryption/transparent-data-encryption-with-azure-sql-database) |
| Konfigurera Azure SQL med Always Encrypted | [SQL-databas Encrypted Always Azure Key Vault](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-always-encrypted-azure-key-vault)|

Förutom TDE, Azure SQL stöder också Always Encrypted, en ny krypteringsteknik för data som garanterar data krypteras inte bara i vila och under flytt mellan klienten och servern, utan även när data används när du kör kommandon på servern.

### <a name="any-virtual-machines-must-be-deployed-from-the-azure-marketplace"></a>Alla virtuella datorer måste distribueras från Azure Marketplace
För att ge en konsekvent säkerhetsnivå över AppSource kräver vi att alla virtuella datorer som distribueras som en del av en Cortana Intelligence-lösning som är certifierade och publiceras på Azure Marketplace.

Den aktuella listan över Azure Marketplace-bilder finns [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute).

Information om hur du publicerar en avbildning av virtuell dator för Azure Marketplace finns [Guide för att skapa en avbildning av virtuell dator för Azure Marketplace](https://docs.microsoft.com/en-us/azure/marketplace-publishing/marketplace-publishing-vm-image-creation).

## <a name="scalability-evaluation-considerations"></a>Överväganden för utvärdering av skalbarhet
### <a name="cortana-intelligence-solutions-should-include-a-scalable-big-data-platform"></a>Cortana Intelligence-lösningar ska innehålla en skalbar stordataplattform
Cortana Intelligence-lösningar bör skala till mycket stora datamängder. Detta innebär i Azure, de ska innehålla en av de två plattformarna för skalning Petabyte data:
- Azure Data Lake Store
- Azure SQL Data Warehouse

Om din lösning inte behöver support för dessa datamängder eller om du använder en alternativ dataplattform du förklara detta i testfall justering.
### <a name="cortana-intelligence-solutions-should-include-dedicated-ingestion-data-environments"></a>Cortana Intelligence-lösningar bör innehålla dedikerade införandet data miljöer
Cortana Intelligence-lösningar Undvik direkt infoga data i relationsdatakällor. Rådata bör i stället lagras i en Ostrukturerade miljö med idempotent infogningar/uppdateringar i alla relationella Arkiv med hjälp av Azure Data Factory.

Mer information om hur du kopierar data med Azure Data Factory [Självstudier: skapa en pipeline med Kopieringsaktiviteten med Visual Studio](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-copy-activity-tutorial-using-visual-studio).

### <a name="azure-sql-data-warehouse-should-use-polybase-for-data-ingestion"></a>Azure SQL Data Warehouse bör använda PolyBase för datapåfyllning
Azure SQL DW stöder PolyBase för mycket skalbar, parallella datapåfyllning. PolyBase kan du använda Azure SQL DW för problemet frågor mot externa datauppsättningar som lagras i Azure Blob Storage eller Azure Data Lake Store. Detta ger högre prestanda alternativa metoder för massuppdateringar.

Anvisningar för att komma igång med PolyBase och Azure SQL DW finns [Läs in data med PolyBase i SQL Data Warehouse](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-get-started-load-with-polybase).

Läs mer om bästa praxis med PolyBase och Azure SQL DW [Guide för att använda PolyBase i SQL Data Warehouse](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-load-polybase-guide).

## <a name="availability-evaluation-considerations"></a>Tillgänglighet för utvärdering

### <a name="datasets-accessible-to-end-users-should-support-a-large-volume-of-concurrent-users"></a>Datauppsättningar som är tillgängliga för slutanvändarna ska ha stöd för ett stort antal samtidiga användare
Köra utvärderingsverktyget blir du ombedd att ange reporting eller publicera resurser. Det förutsätts att resurserna är avsedda för åtkomst av slutanvändare, inte utvecklare. Medel stort antal samtidiga användare ska ha stöd för dessa resurser.

Mer specifikt får Azure SQL Data Warehouse inte vara den enda tillgängliga för slutanvändare datakällan. Om Azure SQL DW anges som en resurs för Privilegierade användare, ska Azure Analysis Services göras tillgängliga för vanliga användare.

Läs mer om Azure SQL DW samtidighet gränser [hantering samtidighet och arbetsbelastning i SQL Data Warehouse](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-develop-concurrency).

Läs mer om Azure Analysis Services [Analysis Services-översikt](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview).

### <a name="azure-sql-resources-should-have-a-read-only-replica-for-failover"></a>Azure SQL-resurser ska ha en skrivskyddad replik för redundans
Geo-replikering till en sekundär instans stöd för Azure SQL-databaser. Denna instans kan användas som ett failover-instans för att tillhandahålla hög tillgänglighet program.

Mer information om geo-replikering för Azure SQL-databaser finns [SQL Database GEO-replikering översikt](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-geo-replication-overview).

Anvisningar om hur du konfigurerar geo-replikering för SQL Azure finns [konfigurera aktiv geo-replikering för Azure SQL Database med Transact-SQL](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-geo-replication-transact-sql).

### <a name="azure-sql-data-warehouse-should-have-geo-redundant-backups-enabled"></a>Azure SQL Data Warehouse bör ha geo-redundant säkerhetskopior aktiverad
Azure SQL DW har stöd för daglig säkerhetskopiering till geo-redundant lagring. Den här georeplikering gör att du kan återställa datalagret även i situationer där du inte kan komma åt ögonblicksbilder som lagras i din primära region. Den här funktionen är aktiverad som standard och bör inte inaktivera för Cortana Intelligence-lösningar.

Mer information om Azure SQL DW-säkerhetskopiering och återställning finns här [säkerhetskopiering av SQL Data Warehouse](https://docs.microsoft.com/en-us/azure/sql-data-warehouse/sql-data-warehouse-backups).

### <a name="virtual-machines-should-be-configured-with-availability-sets"></a>Virtuella datorer ska konfigureras med tillgänglighetsuppsättningar
Virtuella Azure-datorer ska konfigureras i tillgänglighetsuppsättningar för att minimera effekten av planerade och oplanerat underhållshändelser.

Läs mer om virtuella Azure-datorn tillgänglighet [hantera tillgängligheten för Windows-datorer i Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/manage-availability).

## <a name="other-evaluation-considerations"></a>Andra överväganden för utvärdering
### <a name="cortana-intelligence-apps-should-use-a-centralized-tool-for-data-orchestration"></a>Cortana Intelligence-appar bör använda ett centraliserat verktyg för data orchestration
Med ett enda verktyg för att hantera och schemaläggning dataförflyttning och omvandling säkerställer konsekvent runt viktiga data. Det ger en tydlig logik runt logik för omprövning, beroende management, aviseringen-loggning och så vidare. Vi rekommenderar användning av [Azure Data Factory](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-introduction) för data orchestration i Azure.

Om du använder ett verktyg än Azure Data Factory för data orchestration Beskriv vilka verktyg eller verktyg som du använder.
### <a name="azure-machine-learning-models-should-be-retrained-using-azure-data-factory"></a>Azure Machine Learning-modeller bör retrained med hjälp av Azure Data Factory
Azure Machine Learning (AzureML) innehåller lättanvända verktyg för att skapa och distribuera förutsägelsemodellering och maskininlärning pipelines. Det är dock viktigt att Produktionsdistribution av dessa modeller för AzureML inte baseras på en enda fast dataset, men i stället anpassar sig efter flyttade dynamics av verkliga fenomen.

Läs mer om hur du skapar omtränings webbtjänster i AzureML [träna om Machine Learning-modeller via programmering](https://docs.microsoft.com/en-us/azure/machine-learning/machine-learning-retrain-models-programmatically).

Mer information om hur du automatiserar modellen utbildning processen med Azure Data Factory finns [uppdaterar Azure Machine Learning-modeller med Uppdateringsresursaktivitet](https://docs.microsoft.com/en-us/azure//data-factory/v1/data-factory-azure-ml-update-resource-activity).

## <a name="existing-documentation"></a>Befintliga dokumentationen
[Microsoft Azure certifierade växa moln](https://azure.microsoft.com/en-us/marketplace/programs/certified/)

[Microsoft Azure certifierad för Cortana Intellignece](https://azure.microsoft.com/en-us/marketplace/programs/certified/cortana/)

