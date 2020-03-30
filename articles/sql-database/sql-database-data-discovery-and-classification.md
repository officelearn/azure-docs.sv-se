---
title: Dataidentifiering och -klassificering
description: Dataidentifiering & klassificering för Azure SQL Database och Azure Synapse Analytics
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
titleSuffix: Azure SQL Database and Azure Synapse
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/05/2020
tags: azure-synapse
ms.openlocfilehash: 98b21969ce86f84b20fbe78dbdf3002769ef65f5
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387877"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Dataidentifiering & klassificering för Azure SQL Database och Azure Synapse Analytics

Data Discovery & Klassificering ger avancerade funktioner som är inbyggda i Azure SQL Database för **att upptäcka**, **klassificera,** **märka** & **rapportering** av känsliga data i dina databaser.

Att upptäcka och klassificera dina mest känsliga data (företag, ekonomi, hälso- och sjukvård, personligt identifierbara data (PII) och så vidare.) kan spela en central roll i din organisationsinformationsskyddsstatus. Tjänsten kan fungera som infrastruktur inom följande områden:

- Hjälp med att uppfylla standarder för datasekretess och efterlevnadsregler.
- Olika säkerhetsscenarier, till exempel övervakning (granskning) och aviseringar om avvikande åtkomst till känsliga data.
- Åtkomstkontroll och stärkt säkerhet för databaser som innehåller mycket känsliga data.

Data Discovery & Classification är en del av [ADS-erbjudandet (Advanced Data Security),](sql-database-advanced-data-security.md) som är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. dataidentifiering & klassificering kan nås och hanteras via den centrala SQL ADS-portalen.

> [!NOTE]
> Det här dokumentet gäller Azure SQL Database och Azure Synapse. För enkelhetens skull används SQL Database när du refererar till både SQL Database och Azure Synapse. För SQL Server (lokalt) finns i [SQL Data Discovery and Classification](https://go.microsoft.com/fwlink/?linkid=866999).

## <a name="what-is-data-discovery--classification"></a><a id="subheading-1"></a>Vad är dataidentifiering & klassificering

Data Discovery & Classification introducerar en uppsättning avancerade tjänster och nya SQL-funktioner, som bildar ett nytt SQL Information Protection paradigm som syftar till att skydda data, inte bara databasen:

- **Rekommendationer för & identifiering**

  Klassificeringsmotorn söker igenom databasen och identifierar kolumner som innehåller potentiellt känsliga data. Du får ett enkelt sätt att granska och tillämpa lämpliga klassificeringsrekommendationer via Azure-portalen.

- **Märkning**

  Känslighetsklassificeringsetiketter kan beständigt taggas i kolumner med hjälp av nya klassificeringsmetadataattribut som introduceras i SQL Engine. Sedan kan du använda dessa metadata till avancerad, känslighetsbaserad granskning och skydd.

- **Känslighet för frågeresultatuppsättning**

  Frågeresultatuppsättningens känslighet beräknas i realtid för granskning.

- **Synlighet**

  Databasklassificeringstillståndet kan visas i en detaljerad instrumentpanel i portalen. Dessutom kan du ladda ned en rapport (i Excel-format) och använda den till bland annat regelefterlevnad och revision.

## <a name="discover-classify--label-sensitive-columns"></a><a id="subheading-2"></a>Identifiera, klassificera & etikettkänsliga kolumner

I följande avsnitt beskrivs stegen för att identifiera, klassificera och märka kolumner som innehåller känsliga data i databasen, samt visa databasens aktuella klassificeringstillstånd och exportera rapporter.

Klassificeringen innehåller två metadataattribut:

- Etiketter – De viktigaste klassificeringsattributen, som används för att definiera känslighetsnivån för de data som lagras i kolumnen.  
- Informationstyper – Ange ytterligare granularitet till den typ av data som lagras i kolumnen.

## <a name="define-and-customize-your-classification-taxonomy"></a>Definiera och anpassa klassificeringstaxonomin

Data Discovery & Classification levereras med en inbyggd uppsättning känslighetsetiketter och en inbyggd uppsättning informationstyper och identifieringslogik. Du har nu möjlighet att anpassa den här taxonomin och definiera en uppsättning och rangordning av klassificeringskonstruktioner specifikt för din miljö.

Definition och anpassning av klassificeringstaxonomi görs på en central plats för hela Azure-klienten. Den platsen finns i [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), som en del av din säkerhetsprincip. Endast någon med administratörsrättigheter i rothanteringsgruppen för klienten kan utföra den här uppgiften.

Som en del av principhanteringen för informationsskydd kan du definiera anpassade etiketter, rangordna dem och associera dem med en markerad uppsättning informationstyper. Du kan också lägga till egna anpassade informationstyper och konfigurera dem med strängmönster, som sedan läggs till i identifieringslogiken för att identifiera den här typen av data i dina databaser.
Läs mer om hur du anpassar och hanterar din princip i [policyn för informationsskydd](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

När principen för hela klienten har definierats kan du fortsätta med klassificeringen av enskilda databaser med hjälp av den anpassade principen.

## <a name="classify-your-sql-database"></a>Klassificera din SQL-databas

1. Gå till [Azure-portalen](https://portal.azure.com).

2. Navigera till **Avancerad datasäkerhet** under rubriken Säkerhet i fönstret Azure SQL Database. Klicka här om du vill aktivera avancerad datasäkerhet och klicka sedan på **klassificeringskortet för dataidentifiering &.**

   ![Skanna en databas](./media/sql-data-discovery-and-classification/data_classification.png)

3. Fliken **Översikt** innehåller en sammanfattning av databasens aktuella klassificeringstillstånd, inklusive en detaljerad lista över alla klassificerade kolumner, som du också kan filtrera för att bara visa specifika schemadelar, informationstyper och etiketter. Om du ännu inte har klassificerat några kolumner [går du till steg 5](#step-5).

   ![Sammanfattning av aktuellt klassificeringstillstånd](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Om du vill hämta en rapport i **Excel-format** klickar du på alternativet Exportera i fönstrets övre meny.

5. <a id="step-5"></a>Om du vill börja klassificera dina data klickar du på **fliken Klassificering** högst upp i fönstret.

6. Klassificeringsmotorn söker igenom databasen efter kolumner som innehåller potentiellt känsliga data och innehåller en lista över **rekommenderade kolumnklassificeringar**. Så här visar och tillämpar du klassificeringsrekommendationer:

   - Om du vill visa listan över rekommenderade kolumnklassificeringar klickar du på rekommendationerpanelen längst ned i fönstret

   - Granska listan med rekommendationer – om du vill acceptera en rekommendation för en viss kolumn markerar du kryssrutan i den vänstra kolumnen på den aktuella raden. Du kan också markera *alla rekommendationer* som accepterade genom att markera kryssrutan i tabellens huvud för rekommendationer.

       ![Lista över granskningsrekommendationer](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Om du vill använda de valda rekommendationerna klickar du på den blå **knappen Acceptera valda rekommendationer.**

7. Du kan också klassificera kolumner **manuellt** som ett alternativ, eller dessutom, till den rekommendationsbaserade klassificeringen:

   - Klicka på **Lägg till klassificering** i fönstrets övre meny.

   - I det sammanhangsfönster som öppnas markerar du den schema > tabell > kolumn som du vill klassificera och informationstypen och känslighetsetiketten. Klicka sedan på den blå **Lägg till klassificeringsknappen** längst ned i kontextfönstret.

      ![Markera kolumn för att klassificera](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Om du vill slutföra klassificeringen och beständigt märka databaskolumnerna med de nya klassificeringsmetadata klickar du på **Spara** i fönstrets övre meny.

## <a name="auditing-access-to-sensitive-data"></a><a id="subheading-3"></a>Granska åtkomst till känsliga data

En viktig aspekt av informationsskyddsparadigmet är möjligheten att övervaka åtkomsten till känsliga data. [Azure SQL Database Auditing](sql-database-auditing.md) har förbättrats för att inkludera ett nytt fält i granskningsloggen som kallas *data_sensitivity_information*, som loggar känslighetsklassificeringar (etiketter) för de faktiska data som returnerades av frågan.

![Granskningslogg](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="subheading-4"></a>Behörigheter

Följande inbyggda roller kan läsa dataklassificeringen av `Owner`en `Reader` `Contributor`Azure `SQL Security Manager` `User Access Administrator`SQL-databas: , , och .

Följande inbyggda roller kan ändra dataklassificeringen för `Owner`en `Contributor` `SQL Security Manager`Azure SQL-databas: , .

Läs mer om [RBAC för Azure-resurser](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a name="manage-classifications"></a><a id="subheading-5"></a>Hantera klassificeringar

# <a name="t-sql"></a>[T-SQL](#tab/azure-t-sql)
Du kan använda T-SQL för att lägga till/ta bort kolumnklassificeringar samt hämta alla klassificeringar för hela databasen.

> [!NOTE]
> När du använder T-SQL för att hantera etiketter finns det ingen validering som det finns etiketter som läggs till i en kolumn i organisationens informationsskyddsprincip (uppsättningen etiketter som visas i portalrekommendationerna). Det är därför upp till er att validera detta.

- Lägg till/uppdatera klassificeringen av en eller flera kolumner: [LÄGG TILL KÄNSLIGHETSKLASSIFICERING](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Ta bort klassificeringen från en eller flera kolumner: [DROP SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Visa alla klassificeringar i databasen: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

# <a name="rest-apis"></a>[Api:er för vila](#tab/azure-rest-api)
Du kan använda REST API:er för att programmässigt hantera klassificeringar och rekommendationer. De publicerade REST-API:erna stöder följande åtgärder:

- [Skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) - Skapar eller uppdaterar känslighetsetiketten för en viss kolumn
- [Ta bort](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) - Tar bort känslighetsetiketten för en viss kolumn
- [Inaktivera rekommendation](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation) - Inaktiverar känslighetsrekommendationer för en viss kolumn
- [Aktivera rekommendation](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation) - Aktiverar känslighetsrekommendationer för en viss kolumn (rekommendationer är aktiverade som standard i alla kolumner)
- [Get](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get) - Hämtar känslighetsetiketten för en viss kolumn
- [Lista aktuell efter databas](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase) - Hämtar de aktuella känslighetsetiketterna för en viss databas
- [Lista rekommenderas av databas](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) - Hämtar de rekommenderade känslighetsetiketterna för en viss databas

# <a name="powershell-cmdlet"></a>[PowerShell-cmdlet](#tab/azure-powelshell)
Du kan använda PowerShell för att hantera klassificeringar och rekommendationer för Azure SQL Database och Managed Instance.

### <a name="powershell-cmdlet-for-azure-sql-database"></a>PowerShell-cmdlet för Azure SQL-databas
- [Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Ta bort AzSqlDatabaseSensitivityKlassificering](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityKommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Aktivera-AzSqlDatabaSesensitivityKommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Inaktivera-AzSqlDatabaseSensitivityKommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

### <a name="powershell-cmdlets-for-managed-instance"></a>PowerShell-cmdlets för hanterad instans
- [Get-AzSqlInstanceDatabaseSensitivityKlassificering](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Ta bort-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityKommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Aktivera-AzSqlInstanceDatabaseSensitivityKommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Inaktivera-AzSqlInstanceDatabaseSensitivityKommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

---

## <a name="next-steps"></a><a id="subheading-6"></a>Nästa steg

- Läs mer om [avancerad datasäkerhet](sql-database-advanced-data-security.md).
- Överväg att konfigurera [Azure SQL Database Auditing](sql-database-auditing.md) för övervakning och granskning av åtkomst till dina klassificerade känsliga data.
- En YouTube-presentation som innehåller klassificering & dataidentifiering finns i [Identifiera, klassificera, märka & skydda SQL-data | Data exponeras](https://www.youtube.com/watch?v=itVi9bkJUNc).

<!--Anchors-->
[What is data discovery & classification]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Permissions]: #subheading-4
[Manage classifications]: #subheading-5
[Next Steps]: #subheading-6
