---
title: Dataidentifiering och -klassificering
description: Klassificering av Azure SQL Database och data identifiering &
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.devlang: ''
ms.topic: conceptual
author: barmichal
ms.author: mibar
ms.reviewer: vanto
ms.date: 08/22/2019
ms.openlocfilehash: 2767b1b5603baa311d480109988d66dd136297ab
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808074"
---
# <a name="azure-sql-database-and-sql-data-warehouse-data-discovery--classification"></a>& Klassificering för Azure SQL Database och SQL Data Warehouse data identifiering

Data identifierings & klassificering innehåller avancerade funktioner som är inbyggda i Azure SQL Database för att **upptäcka**, **klassificera**och **märka** & **skydda** känsliga data i dina databaser.

Att identifiera och klassificera dina mest känsliga data (företags-, finans-, sjukvårds-och person uppgifter) och så vidare kan du spela en pivot-roll i din organisations informations skydds datasekretesstandarder. Den kan fungera som infrastruktur för:

- Hjälpa till att uppfylla data sekretess standarder och regler för efterlevnad av efterlevnad.
- Olika säkerhets scenarier, till exempel övervakning (granskning) och aviseringar om avvikande åtkomst till känsliga data.
- Kontrol lera åtkomst till och härdning av säkerheten för databaser som innehåller mycket känsliga data.

Data identifierings & klassificering är en del av erbjudandet för [Avancerad data säkerhet](sql-database-advanced-data-security.md) (Ads), som är ett enhetligt paket för avancerade SQL-säkerhetsfunktioner. data identifiering & klassificering kan nås och hanteras via den centrala SQL ADS-portalen.

> [!NOTE]
> Det här dokumentet relaterar till Azure SQL Database och Azure SQL Data Warehouse. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse. SQL Server (lokalt) finns i [identifiering och klassificering av SQL-data](https://go.microsoft.com/fwlink/?linkid=866999).

## <a id="subheading-1"></a>Vad är & klassificering av data identifiering

Data identifierings & klassificeringen introducerar en uppsättning avancerade tjänster och nya SQL-funktioner, vilket utgör ett nytt SQL Information Protection-paradigm som syftar till att skydda data, inte bara databasen:

- **Rekommendationer för identifierings &**

  Klassificerings motorn genomsöker din databas och identifierar kolumner som innehåller potentiellt känsliga data. Det ger dig ett enkelt sätt att granska och tillämpa lämpliga klassificerings rekommendationer via Azure Portal.

- **Märkning**

  Etiketter för känslighets klassificering kan märkas permanent på kolumner med nya attribut för klassificering av metadata som introduceras i SQL-motorn. Dessa metadata kan sedan användas för avancerade känslighets-baserade gransknings-och skydds scenarier.

- **Resultat uppsättnings känslighet för fråga**

  Känsligheten för frågeresultatet beräknas i real tid för gransknings syfte.

- **Synlighet**

  Databas klassificerings tillstånd kan visas på en detaljerad instrument panel i portalen. Dessutom kan du ladda ned en rapport (i Excel-format) som ska användas för efterlevnad & gransknings ändamål, samt andra behov.

## <a id="subheading-2"></a>Identifiera, klassificera & etiketter känsliga kolumner

I följande avsnitt beskrivs stegen för att identifiera, klassificera och märka kolumner som innehåller känsliga data i databasen, samt att visa det aktuella klassificerings läget för databasen och exportera rapporter.

Klassificeringen innehåller två attribut för metadata:

- Etiketter – huvudattributen för klassificering som används för att definiera känslighets nivån för de data som lagras i kolumnen.  
- Informations typer – ger ytterligare detaljerad information om vilken typ av data som lagras i kolumnen.

## <a name="define-and-customize-your-classification-taxonomy"></a>Definiera och anpassa din klassificerings-taxonomi

SQL data Discovery & klassificeringen innehåller en inbyggd uppsättning känslighets etiketter och en inbyggd uppsättning informations typer och identifierings logik. Nu har du möjlighet att anpassa taxonomin och definiera en uppsättning och rangordning av klassificerings konstruktioner som är specifika för din miljö.

Definition och anpassning av klassificerings taxonomin görs på en central plats för hela din Azure-klient. Den platsen finns [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), som en del av din säkerhets princip. Endast användare med administratörs behörighet för klient organisationens rot hanterings grupp kan utföra den här uppgiften.

Som en del av Information Protection princip hantering kan du definiera anpassade etiketter, ranka dem och koppla dem till en viss uppsättning informations typer. Du kan också lägga till egna anpassade informations typer och konfigurera dem med sträng mönster, som läggs till i identifierings logiken för att identifiera den här typen av data i dina databaser.
Läs mer om hur du anpassar och hanterar principer i [guiden för information Protection policy instruktion](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

När du har definierat principen för hela klienten kan du fortsätta med klassificeringen av enskilda databaser med hjälp av den anpassade principen.

## <a name="classify-your-sql-database"></a>Klassificera dina SQL Database

1. Gå till [Azure-portalen](https://portal.azure.com).

2. Gå till **Avancerad data säkerhet** under säkerhets rubriken i Azure SQL Databases fönstret. Klicka om du vill aktivera avancerad data säkerhet och klicka sedan på **klassificerings kortet data identifierings &** .

   ![Skanna en databas](./media/sql-data-discovery-and-classification/data_classification.png)

3. Fliken **Översikt** innehåller en sammanfattning av databasens aktuella klassificerings tillstånd, inklusive en detaljerad lista över alla klassificerade kolumner, som du även kan filtrera för att bara visa vissa schema delar, informations typer och etiketter. Om du ännu inte har klassificerat några kolumner kan du [gå vidare till steg 5](#step-5).

   ![Sammanfattning av aktuellt klassificerings tillstånd](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Om du vill ladda ned en rapport i Excel-format klickar du på alternativet **Exportera** i den övre menyn i fönstret.

   ![Exportera till Excel](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png)

5. <a id="step-5"></a>Om du vill börja klassificera dina data klickar du på **fliken klassificering** överst i fönstret.

    ![Klassificera data](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png)

6. Klassificerings motorn söker igenom databasen efter kolumner som innehåller potentiellt känsliga data och innehåller en lista över **rekommenderade kolumn klassificeringar**. Visa och tillämpa klassificerings rekommendationer:

   - Om du vill visa en lista över rekommenderade kolumn klassificeringar klickar du på panelen rekommendationer längst ned i fönstret:

      ![Klassificera dina data](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png)

   - Granska listan över rekommendationer – om du vill godkänna en rekommendation för en speciell kolumn markerar du kryss rutan i den vänstra kolumnen i relevant rad. Du kan också markera *alla rekommendationer* som godkända genom att markera kryss rutan i tabell rubriken rekommendationer.

       ![Granska rekommendations lista](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Använd de valda rekommendationerna genom att klicka på knappen blå **acceptera markerade rekommendationer** .

      ![Använd rekommendationer](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png)

7. Du kan också **klassificera kolumner manuellt** som ett alternativ, eller också till rekommendations-baserad klassificering:

   - Klicka på **Lägg till klassificering** i den översta menyn i fönstret.

      ![Lägg till klassificering manuellt](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png)

   - I Sammanhangs fönstret som öppnas väljer du det schema > tabell > kolumn som du vill klassificera, samt etiketten information typ och känslighet. Klicka sedan på knappen blå **Lägg till klassificering** längst ned i kontext fönstret.

      ![Välj kolumn att klassificera](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. Om du vill slutföra klassificeringen och etiketten (tag) i databas kolumnerna med de nya klassificerings-metadata klickar du på **Spara** i den övre menyn i fönstret.

   ![Spara](./media/sql-data-discovery-and-classification/10_data_classification_save.png)

## <a id="subheading-3"></a>Granska åtkomst till känsliga data

En viktig aspekt av informations skydds paradigmet är möjligheten att övervaka åtkomsten till känsliga data. [Azure SQL Database granskning](sql-database-auditing.md) har förbättrats för att innehålla ett nytt fält i gransknings loggen med namnet *data_sensitivity_information*, som loggar känslighets klassificeringarna (etiketter) för de faktiska data som returnerades av frågan.

![Gransknings logg](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a id="subheading-4"></a>Hantera data klassificering med T-SQL

Du kan använda T-SQL för att lägga till/ta bort kolumn klassificeringar, samt hämta alla klassificeringar för hela databasen.

> [!NOTE]
> När du använder T-SQL för att hantera etiketter, finns det ingen validering av etiketter som lagts till i en kolumn i principen för organisations informations skydd (den uppsättning etiketter som visas i Portal rekommendationer). Det är därför upp till dig att verifiera detta.

- Lägg till/uppdatera klassificeringen för en eller flera kolumner: [Lägg till känslighets klassificering](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Ta bort klassificeringen från en eller flera kolumner: [släpp känslighets klassificering](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Visa alla klassificeringar för databasen: [sys. sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

### <a name="manage-classifications-using-rest-apis"></a>Hantera klassificeringar med REST API: er

Du kan också använda REST-API: er för att hantera klassificeringar program mässigt. De publicerade REST API: erna har stöd för följande åtgärder:

- [Skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) – skapar eller uppdaterar känslighets etiketten för en specifik kolumn
- [Ta bort](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) – tar bort känslighets etiketten för en specifik kolumn
- [Inaktivera rekommendation](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/disablerecommendation) – inaktiverar känslighets rekommendationer för en specifik kolumn
- [Aktivera rekommendation](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/enablerecommendation) – möjliggör känslighets rekommendationer för en specifik kolumn (rekommendationer är aktiverade som standard för alla kolumner)
- [Get](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get) -hämtar en kolumns känslighets etikett
- [Lista aktuella efter databas](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase) – hämtar de aktuella känslighets etiketterna för en specifik databas

- [Lista rekommenderas av databasen](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) – hämtar de rekommenderade känslighets etiketterna för en specifik databas

## <a name="manage-data-discovery-and-classification-using-azure-powershell"></a>Hantera identifiering och klassificering av data med hjälp av Azure PowerShell

Du kan använda PowerShell för att hämta alla rekommenderade kolumner i en Azure SQL-databas och en hanterad instans.

### <a name="powershell-cmdlets-for-azure-sql-database"></a>PowerShell-cmdletar för Azure SQL Database

- [Get-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityclassification)
- [Set-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasesensitivityclassification)
- [Remove-AzSqlDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqldatabasesensitivityclassification)
- [Get-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasesensitivityrecommendation)
- [Aktivera – AzSqlDatabaSesensitivityRecommendation](https://docs.microsoft.com/en-us/powershell/module/az.sql/enable-azsqldatabasesensitivityrecommendation)
- [Disable-AzSqlDatabaseSensitivityRecommendation](https://docs.microsoft.com/en-us/powershell/module/az.sql/disable-azsqldatabasesensitivityrecommendation)

### <a name="powershell-cmdlets-for-managed-instance"></a>PowerShell-cmdletar för hanterad instans

- [Get-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityclassification)
- [Set-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlinstancedatabasesensitivityclassification)
- [Remove-AzSqlInstanceDatabaseSensitivityClassification](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlinstancedatabasesensitivityclassification)
- [Get-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlinstancedatabasesensitivityrecommendation)
- [Aktivera – AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/en-us/powershell/module/az.sql/enable-azsqlinstancedatabasesensitivityrecommendation)
- [Disable-AzSqlInstanceDatabaseSensitivityRecommendation](https://docs.microsoft.com/en-us/powershell/module/az.sql/disable-azsqlinstancedatabasesensitivityrecommendation)

## <a name="permissions"></a>Behörigheter

Följande inbyggda roller kan läsa data klassificeringen för en Azure SQL-databas: `Owner`, `Reader`, `Contributor`, `SQL Security Manager` och `User Access Administrator`.

Följande inbyggda roller kan ändra data klassificeringen för en Azure SQL-databas: `Owner``Contributor``SQL Security Manager`.

Lär dig mer om [RBAC för Azure-resurser](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a id="subheading-5"></a>Nästa steg

- Läs mer om [Avancerad data säkerhet](sql-database-advanced-data-security.md).
- Överväg att konfigurera [Azure SQL Database granskning](sql-database-auditing.md) för övervakning och granskning av åtkomst till dina klassificerade känsliga data.

<!--Anchors-->
[SQL data discovery & classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Automated/Programmatic classification]: #subheading-4
[Next Steps]: #subheading-5
