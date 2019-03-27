---
title: Azure SQL Database och SQL Data Warehouse dataidentifiering och klassificering | Microsoft Docs
description: Azure SQL Database och data identifiering och klassificering
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: ronitr
ms.author: ronitr
ms.reviewer: vanto
manager: craigg
ms.date: 03/22/2019
ms.openlocfilehash: 74bd3af3e1ffd126f8cb4f2347e4566cc4708e25
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2019
ms.locfileid: "58495994"
---
# <a name="azure-sql-database-and-sql-data-warehouse-data-discovery--classification"></a>Azure SQL Database och SQL Data Warehouse dataidentifiering och klassificering

Dataidentifiering och klassificering (för närvarande i förhandsversion) ger avancerade funktioner i Azure SQL-databas för **identifierar**, **klassificera**, **märkning**  &  **skyddar** känslig data i dina databaser.
Identifiera och klassificera dina mest känsliga data (business- och finansiella institutioner vård, personligt identifierbar information (PII) och så vidare.) kan spela upp en central roll i din organisations information protection datasekretesstandarder. Det kan fungera som en infrastruktur för:

- Hjälper uppfylla data sekretesstandarder och efterlevnadskrav.
- Olika säkerhetsscenarier, till exempel övervakning (granskning) och Varna vid avvikande åtkomsten till känsliga data.
- Kontrollera åtkomst till och Härdning av säkerheten för databaser som innehåller mycket känsliga data.

Dataidentifiering och klassificering är en del av den [avancerade datasäkerhet](sql-database-advanced-data-security.md) (ADS) erbjudande som en enhetlig paket för avancerade funktioner för SQL-säkerhet. dataidentifiering och klassificering kan nås och hanteras via den centrala SQL ADS-portalen.

> [!NOTE]
> Det här dokumentet avser Azure SQL Database och Azure SQL Data Warehouse. För enkelhetens skull används SQL Database när det gäller både SQL Database och SQL Data Warehouse. SQL Server (lokalt), se [SQL Dataidentifiering och klassificering](https://go.microsoft.com/fwlink/?linkid=866999).

## <a id="subheading-1"></a>Vad är dataidentifiering och klassificering

Dataidentifiering och klassificering introducerar en uppsättning avancerade tjänster och nya funktioner i SQL, som utgör en ny SQL Information Protection-paradigm syftar till att skydda data, inte bara databasen:

- **Identifiering och rekommendationer**

  Motor för attitydklassificering söker igenom din databas och identifierar kolumner som innehåller potentiellt känsliga data. Det ger dig ett enkelt sätt att granska och använder rekommendationerna för rätt klassificering via Azure portal.

- **Märkning**

  Etiketter för klassificering av känslighet kan vara beständigt taggade på kolumner med hjälp av nya klassificering metadata-attribut som introduceras i SQL-motor. Dessa metadata kan sedan användas för avancerade känslighet-baserade gransknings- och skydd scenarier.

- **Fråga resultatet set känslighet**

  Känslighet för frågeresultatet beräknas i realtid i granskningssyfte.

- **Synlighet**

  Klassificering databastillståndet kan visas i en detaljerad instrumentpanel i portalen. Dessutom kan du hämta en rapport (i Excel-format) för att användas för efterlevnad och granskning, samt andra behov.

## <a id="subheading-2"></a>Identifiera, klassificera och märka känsliga kolumner

I följande avsnitt beskrivs stegen för att identifiera, klassificera och märkning kolumner som innehåller känsliga data i databasen, som visar det aktuella tillståndet för klassificering för din databas och exportera rapporter.

Klassificeringen innehåller två metadata-attribut:

- Etiketter – huvudsakliga klassificering-attribut används för att definiera Känslighetsnivån för de data som lagras i kolumnen.  
- Typer av information – ange ytterligare granularitet i vilken typ av data som lagras i kolumnen.

## <a name="define-and-customize-your-classification-taxonomy"></a>Definiera och anpassa din klassificering taxonomi

SQL dataidentifiering och klassificering levereras med en inbyggd uppsättning känslighet etiketter och en inbyggd uppsättning informationstyper och identifiering av logik. Nu har du möjlighet att anpassa den här taxonomi och definiera en uppsättning och rangordning klassificering konstruktioner specifikt för din miljö.

Definitions- och anpassning av klassificering-taxonomi görs på en central plats för din hela Azure-klient. Att platsen är i [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), som en del av din säkerhetsprincip. Endast användare med administratörsbehörighet på klienten rot-hanteringsgrupp kan utföra den här uppgiften.

Som en del av Information Protection-principhantering, kan du definiera anpassade etiketter, rangordnas dem och koppla dem till en vald uppsättning informationstyper. Du kan också lägga till dina egna anpassade informationstyper och konfigurerar dem med sträng mönster som läggs till identifiering av logiken för att identifiera den här typen av data i dina databaser.
Läs mer om att anpassa och hantera din princip i den [instruktionsguide för Information Protection-principen](https://go.microsoft.com/fwlink/?linkid=2009845&clcid=0x409).

När du har definierat principen klienttäckande kan du fortsätta med klassificeringen av enskilda databaser med hjälp av en anpassad princip.

## <a name="classify-your-sql-database"></a>Klassificera din SQL-databas

1. Gå till [Azure-portalen](https://portal.azure.com).

2. Gå till **avancerade datasäkerhet** under rubriken säkerhet i Azure SQL Database-rutan. Klicka om du vill aktivera avancerad säkerhet och klicka sedan på den **dataidentifiering och klassificering (förhandsversion)** kort.

   ![Skanna en databas](./media/sql-data-discovery-and-classification/data_classification.png)

3. Den **översikt** fliken innehåller en sammanfattning av det aktuella tillståndet för klassificering för databasen, inklusive en detaljerad lista över alla klassificerade kolumner som du kan också filtrera för att visa endast specifika schemat delar, typer av information och etiketter. Om du ännu inte har klassificerats några kolumner [vidare till steg 5](#step-5).

   ![Sammanfattning av den aktuella klassificeringen tillstånd](./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png)

4. Om du vill hämta en rapport i Excel-format, klickar du på den **exportera** alternativ på menyn längst upp i fönstret.

   ![Exportera till Excel](./media/sql-data-discovery-and-classification/3_data_classification_export_report.png)

5. <a id="step-5"></a>För att börja klassificera dina data, klickar du på den **klassificering fliken** överst i fönstret.

    ![Klassificera du data](./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png)

6. Motor för attitydklassificering söker igenom din databas för kolumner som innehåller potentiellt känsliga data och innehåller en lista över **rekommenderas kolumnklassificeringar**. Visa och tillämpa klassificeringsrekommendationer:

   - Klicka på panelen rekommendationer längst ned i fönstret om du vill visa listan med rekommenderade kolumnklassificeringar:

      ![Klassificera dina data](./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png)

   - Granska listan över rekommendationer – för att acceptera en rekommendation för en viss kolumn, markera kryssrutan i den vänstra kolumnen i den relevanta raden. Du kan också markera *alla rekommendationer* som godkänts genom att markera kryssrutan i tabellrubrik rekommendationer.

       ![Granska rekommendationslistan](./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png)

   - Om du vill använda de valda rekommendationerna, klickar du på det blå fältet **Godkänn valda rekommendationer** knappen.

      ![Tillämpa rekommendationer](./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png)

7. Du kan också **manuellt klassificera** kolumner som ett alternativ eller dessutom rekommendationen-baserad klassificering:

   - Klicka på **Lägg till klassificering** i menyn längst upp i fönstret.

      ![Lägg till klassificering manuellt](./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png)

   - I den kontexten-fönstret som öppnas väljer du schemat > Tabell > kolumn som du vill klassificera och information typ och känsliga etiketten. Klicka sedan på det blå fältet **Lägg till klassificering** knappen längst ned i fönstret kontext.

      ![Välj kolumn att klassificera](./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png)

8. För att slutföra din klassificering och etiketter beständigt (tagg) databasen kolumner med nya metadata för klassificering, klickar du på **spara** i menyn längst upp i fönstret.

   ![Spara](./media/sql-data-discovery-and-classification/10_data_classification_save.png)

## <a id="subheading-3"></a>Granska åtkomst till känsliga data

En viktig aspekt av information protection-paradigm är möjligheten att övervaka åtkomsten till känsliga data. [Azure SQL Database Auditing](sql-database-auditing.md) har förbättrats för att inkludera ett nytt fält i granskningsloggen kallas *data_sensitivity_information*, som loggar känslighet klassificeringar (etiketter) faktiska data som returnerades av frågan.

![Granskningslogg](./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png)

## <a id="subheading-4"></a>Automatiserad/Programmatic klassificering

Du kan använda T-SQL för att lägga till/ta bort kolumnklassificeringar, samt hämta alla klassificeringar för hela databasen.

> [!NOTE]
> När du använder T-SQL för att hantera etiketter, finns det ingen validering som etiketter som lagts till i en kolumn som finns i organisationens information protection-principen (uppsättning etiketter som visas i portalen rekommendationer). Därför är det upp till dig att verifiera detta.

- Lägg till/uppdatera klassificeringen av en eller flera kolumner: [LÄGG TILL KÄNSLIGHETSKLASSIFICERING](https://docs.microsoft.com/sql/t-sql/statements/add-sensitivity-classification-transact-sql)
- Ta bort klassificeringen från en eller flera kolumner: [TA BORT KÄNSLIGHETSKLASSIFICERING](https://docs.microsoft.com/sql/t-sql/statements/drop-sensitivity-classification-transact-sql)
- Visa alla klassificeringar för databasen: [sys.sensitivity_classifications](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-sensitivity-classifications-transact-sql)

Du kan också använda REST API: er för att programmässigt hantera klassificeringar. Den publicerade REST-API: er har stöd för följande åtgärder:

- [Skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/createorupdate) – skapar eller uppdaterar känslighetsetikett för en viss kolumn
- [Ta bort](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/delete) – tar bort känslighetsetikett för en viss kolumn
- [Hämta](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/get) -hämtar känsliga etiketten för en viss kolumn
- [Lista över aktuella av databasen](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listcurrentbydatabase) -hämtar aktuella känslighet etiketterna för en viss databas
- [Listan rekommenderas av databasen](https://docs.microsoft.com/rest/api/sql/sensitivitylabels/listrecommendedbydatabase) -hämtar rekommenderade känslighet etiketterna för en viss databas

## <a name="permissions"></a>Behörigheter
Följande inbyggda roller kan läsa dataklassificering av en Azure SQL database: `Owner`, `Reader`, `Contributor`, `SQL Security Manager` och `User Access Administrator`.

Följande inbyggda roller kan ändra dataklassificering av en Azure SQL database: `Owner`, `Contributor`, `SQL Security Manager`.

Läs mer om [RBAC för Azure-resurser](https://docs.microsoft.com/azure/role-based-access-control/overview)

## <a id="subheading-5"></a>Nästa steg

- Läs mer om [avancerade datasäkerhet](sql-database-advanced-data-security.md).
- Överväg att konfigurera [Azure SQL Database Auditing](sql-database-auditing.md) för övervakning och granskning av åtkomst till dina klassificerade känsliga data.

<!--Anchors-->
[SQL data discovery & classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Automated/Programmatic classification]: #subheading-4
[Next Steps]: #subheading-5
