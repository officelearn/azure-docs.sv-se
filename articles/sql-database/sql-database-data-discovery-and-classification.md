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
ms.openlocfilehash: 599b2a280e386e49eb114f448f55b17ed7e823d7
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616756"
---
# <a name="data-discovery--classification-for-azure-sql-database-and-azure-synapse-analytics"></a>Dataidentifiering & klassificering för Azure SQL Database och Azure Synapse Analytics

DataIdentifiering & klassificering är inbyggd i Azure SQL Database. Det ger avancerade funktioner för att upptäcka, klassificera, märka och rapportera känsliga data i dina databaser.

Dina mest känsliga data kan omfatta affärs-, ekonomi-, hälso- och sjukvårds- eller personlig information. Att upptäcka och klassificera dessa data kan spela en central roll i organisationens informationsskyddsmetod. Tjänsten kan fungera som infrastruktur inom följande områden:

- Hjälper till att uppfylla standarder för datasekretess och krav för regelefterlevnad.
- Olika säkerhetsscenarier, till exempel övervakning (granskning) och aviseringar om avvikande åtkomst till känsliga data.
- Styra åtkomsten till och härda säkerheten för databaser som innehåller mycket känsliga data.

Data Discovery & Classification är en del av [advanced data security-erbjudandet,](sql-database-advanced-data-security.md) som är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. Du kan komma åt och hantera dataidentifiering & klassificering via den centrala **SQL Advanced Data Security-delen** av Azure-portalen.

> [!NOTE]
> Den här artikeln gäller Azure SQL Database och Azure Synapse Analytics. För enkelhetens skull använder vi *SQL Database* här för att referera till både SQL Database och Azure Synapse. Information om SQL Server (lokalt) finns i [SQL Data Discovery and Classification](https://go.microsoft.com/fwlink/?linkid=866999).

## <a name="what-is-data-discovery--classification"></a><a id="what-is-dc"></a>Vad är klassificering & & dataidentifiering?

Data Discovery & Classification introducerar en uppsättning avancerade tjänster och nya SQL Database-funktioner. Det bildar ett nytt informationsskyddsparadigm för SQL Database, som syftar till att skydda data och inte bara databasen. Paradigmet omfattar:

- **Upptäckt och rekommendationer:** Klassificeringsmotorn söker igenom databasen och identifierar kolumner som innehåller potentiellt känsliga data. Det ger dig sedan ett enkelt sätt att granska och tillämpa rekommenderad klassificering via Azure-portalen.

- **Märkning:** Du kan tillämpa känslighetsklassificeringsetiketter beständigt på kolumner med hjälp av nya metadataattribut som har lagts till i SQL-databasmotorn. Dessa metadata kan sedan användas för avancerade, känslighetsbaserade gransknings- och skyddsscenarier.

- **Frågeresultat-inställd känslighet:** Känsligheten för en frågeresultatuppsättning beräknas i realtid för granskningsändamål.

- **Synlighet:** Du kan visa databasklassificeringstillståndet i en detaljerad instrumentpanel i Azure-portalen. Du kan också hämta en rapport i Excel-format som ska användas för efterlevnads- och granskningsändamål och andra behov.

## <a name="discover-classify-and-label-sensitive-columns"></a><a id="discover-classify-columns"></a>Identifiera, klassificera och märka känsliga kolumner

I det här avsnittet beskrivs stegen för:

- Identifiera, klassificera och märka kolumner som innehåller känsliga data i databasen.
- Visa databasens aktuella klassificeringstillstånd och exportera rapporter.

Klassificeringen innehåller två metadataattribut:

- **Etiketter**: De viktigaste klassificeringsattributen som används för att definiera känslighetsnivån för de data som lagras i kolumnen.  
- **Informationstyper**: Attribut som ger mer detaljerad information om vilken typ av data som lagras i kolumnen.

### <a name="define-and-customize-your-classification-taxonomy"></a>Definiera och anpassa klassificeringstaxonomin

Data Discovery & Classification levereras med en inbyggd uppsättning känslighetsetiketter och en inbyggd uppsättning informationstyper och identifieringslogik. Du kan anpassa den här taxonomin och definiera en uppsättning och rangordning av klassificeringskonstruktioner som är specifika för din miljö.

Du definierar och anpassar klassificeringstaxonomi på en central plats för hela Azure-organisationen. Den platsen finns i [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), som en del av din säkerhetsprinciper. Endast någon med administrativa rättigheter i organisationens rothanteringsgrupp kan utföra den här uppgiften.

Som en del av principhanteringen för SQL-informationsskydd kan du definiera anpassade etiketter, rangordna dem och associera dem med en vald uppsättning informationstyper. Du kan också lägga till egna anpassade informationstyper och konfigurera dem med strängmönster. Mönstren läggs till i identifieringslogiken för att identifiera den här typen av data i databaserna.

Läs mer om hur du anpassar och hanterar principen i hur du vill [styra SQL Information Protection](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

När principen för hela organisationen har definierats kan du fortsätta att klassificera enskilda databaser med hjälp av den anpassade principen.

### <a name="classify-your-sql-database"></a>Klassificera din SQL-databas

1. Gå till [Azure-portalen](https://portal.azure.com).

2. Gå till **Avancerad datasäkerhet** under rubriken **Säkerhet** i fönstret Azure SQL Database. Välj **Avancerad datasäkerhet**och välj sedan **dataidentifieringskortet & klassificering.**

   ![Fönstret Avancerad datasäkerhet i Azure-portalen](./media/sql-data-discovery-and-classification/data_classification.png)

3. På **sidan Dataidentifiering & klassificering** innehåller fliken **Översikt** en sammanfattning av databasens aktuella klassificeringstillstånd. Sammanfattningen innehåller en detaljerad lista över alla klassificerade kolumner, som du också kan filtrera för att bara visa specifika schemadelar, informationstyper och etiketter. Om du inte har klassificerat några kolumner [ännu, hoppa till steg 5](#step-5).

   ![Sammanfattning av aktuellt klassificeringstillstånd](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Om du vill hämta en rapport i Excel-format väljer du **Exportera** i fönstrets övre meny.

5. <a id="step-5"></a>Om du vill börja klassificera data väljer du fliken **Klassificering** på sidan **& klassificering.**

    Klassificeringsmotorn söker igenom databasen efter kolumner som innehåller potentiellt känsliga data och innehåller en lista över rekommenderade kolumnklassificeringar.

6. Visa och tillämpa klassificeringsrekommendationer:

   - Om du vill visa listan över rekommenderade kolumnklassificeringar markerar du rekommendationerpanelen längst ned i fönstret.

   - Om du vill acceptera en rekommendation för en viss kolumn markerar du kryssrutan i den vänstra kolumnen på den aktuella raden. Om du vill markera alla rekommendationer som godkända markerar du kryssrutan längst till vänster i tabellens huvud för rekommendationer.

       ![Granska och välj från lista över klassificeringsrekommendationer](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Om du vill använda de valda rekommendationerna väljer du **Acceptera valda rekommendationer**.

7. Du kan också klassificera kolumner manuellt, som ett alternativ eller utöver den rekommendationsbaserade klassificeringen:

   1. Välj **Lägg till klassificering** i fönstrets övre meny.

   1. I det sammanhangsfönster som öppnas markerar du det schema, den tabell och kolumn som du vill klassificera och informationstypen och känslighetsetiketten.

   1. Välj **Lägg till klassificering** längst ned i kontextfönstret.

      ![Markera en kolumn som ska klassificeras](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Om du vill slutföra klassificeringen och beständigt märka databaskolumnerna med de nya klassificeringsmetadata väljer du **Spara** i fönstrets övre meny.

## <a name="audit-access-to-sensitive-data"></a><a id="audit-sensitive-data"></a>Granskningsåtkomst till känsliga data

En viktig aspekt av informationsskyddsparadigmet är möjligheten att övervaka åtkomsten till känsliga data. [Azure SQL Database Auditing](sql-database-auditing.md) har förbättrats för att `data_sensitivity_information`inkludera ett nytt fält i granskningsloggen som heter . I det här fältet loggas känslighetsklassificeringarna (etiketterna) för de data som returnerades av en fråga. Här är ett exempel:

![Granskningslogg](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a name="permissions"></a><a id="permissions"></a>Behörigheter

Dessa inbyggda roller kan läsa dataklassificeringen av en Azure SQL-databas:

- Ägare
- Läsare
- Deltagare
- SQL-säkerhetshanterare
- Administratör för användaråtkomst

Dessa inbyggda roller kan ändra dataklassificeringen av en Azure SQL-databas:

- Ägare
- Deltagare
- SQL-säkerhetshanterare

Läs mer om rollbaserade behörigheter i [RBAC för Azure-resurser](https://docs.microsoft.com/azure/role-based-access-control/overview).

## <a name="manage-classifications"></a><a id="manage-classification"></a>Hantera klassificeringar

Du kan använda T-SQL, ett REST API eller PowerShell för att hantera klassificeringar.

### <a name="use-t-sql"></a>Använda T-SQL

Du kan använda T-SQL för att lägga till eller ta bort kolumnklassificeringar och hämta alla klassificeringar för hela databasen.

> [!NOTE]
> När du använder T-SQL för att hantera etiketter finns det ingen validering som etiketter som du lägger till i en kolumn finns i organisationens informationsskyddsprincip (uppsättningen etiketter som visas i portalrekommendationerna). Så det är upp till dig att validera detta.

Information om hur du använder T-SQL för klassificeringar finns i följande referenser:

- Så här lägger du till eller uppdaterar klassificeringen av en eller flera kolumner: [LÄGG TILL KÄNSLIGHETSKLASSIFICERING](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Så här tar du bort klassificeringen från en eller flera kolumner: [DROP SENSITIVITY CLASSIFICATION](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Så här visar du alla klassificeringar i databasen: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="use-the-rest-api"></a>Använda rest-API:et

Du kan använda REST API för att programmässigt hantera klassificeringar och rekommendationer. Det publicerade REST API:et stöder följande åtgärder:

- [Skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate): Skapar eller uppdaterar känslighetsetiketten för den angivna kolumnen.
- [Ta bort](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete): Tar bort känslighetsetiketten för den angivna kolumnen.
- [Inaktivera rekommendation:](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation)Inaktiverar känslighetsrekommendationer för den angivna kolumnen.
- [Aktivera rekommendation:](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation)Aktiverar känslighetsrekommendationer för den angivna kolumnen. (Rekommendationer är aktiverade som standard i alla kolumner.)
- [Hämta](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get): Hämtar känslighetsetiketten för den angivna kolumnen.
- [Lista Aktuell efter databas](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase): Hämtar de aktuella känslighetsetiketterna för den angivna databasen.
- [Lista rekommenderas av databasen:](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase)Hämtar de rekommenderade känslighetsetiketterna för den angivna databasen.

### <a name="use-powershell-cmdlets"></a>Använda PowerShell-cmdletar
Du kan använda PowerShell för att hantera klassificeringar och rekommendationer för Azure SQL Database och hanterade instanser.

#### <a name="powershell-cmdlets-for-sql-database"></a>PowerShell-cmdletar för SQL Database

- [Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Ta bort AzSqlDatabaseSensitivityKlassificering](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityKommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Aktivera-AzSqlDatabaSesensitivityKommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Inaktivera-AzSqlDatabaseSensitivityKommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

#### <a name="powershell-cmdlets-for-managed-instances"></a>PowerShell-cmdlets för hanterade instanser

- [Get-AzSqlInstanceDatabaseSensitivityKlassificering](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Ta bort-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityKommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Aktivera-AzSqlInstanceDatabaseSensitivityKommendation](https://docs.microsoft.com/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Inaktivera-AzSqlInstanceDatabaseSensitivityKommendation](https://docs.microsoft.com/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)


## <a name="next-steps"></a><a id="next-steps"></a>Nästa steg

- Läs mer om [avancerad datasäkerhet](sql-database-advanced-data-security.md).
- Överväg att konfigurera [Azure SQL Database Auditing](sql-database-auditing.md) för övervakning och granskning av åtkomst till dina klassificerade känsliga data.
- En presentation som innehåller dataidentifiering och klassificering finns i [Identifiera, klassificera, märka & skydda SQL-data | Data exponeras](https://www.youtube.com/watch?v=itVi9bkJUNc).
