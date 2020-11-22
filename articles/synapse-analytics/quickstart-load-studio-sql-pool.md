---
title: 'Snabb start: Mass inläsning av data med dedikerad SQL-pool'
description: Använd Synapse Studio för att överföra data till en dedikerad SQL-pool i Azure Synapse Analytics.
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.date: 11/16/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 8d70a45b89142f6f9c6990fdbb4b79751c82275f
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2020
ms.locfileid: "95237484"
---
# <a name="quickstart-bulk-loading-with-synapse-sql"></a>Snabb start: Mass inläsning med Synapse SQL

Det har aldrig varit enklare att läsa in data när du använder guiden för Mass inläsning i Synapse Studio. I den här guiden får du hjälp med att skapa ett T-SQL-skript med [copy-instruktionen](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) för Mass inläsning av data. 

## <a name="entry-points-to-the-bulk-load-wizard"></a>Start punkter i guiden för Mass inläsning

Du kan nu enkelt samla in data med hjälp av dedikerade SQL-pooler med enkel högerklickning på följande områden i Synapse Studio:

- En fil eller mapp från ett Azure Storage-konto som är kopplat till din arbets yta genom ![ att högerklicka på en fil eller mapp från ett lagrings konto](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## <a name="prerequisites"></a>Förutsättningar

- Den här guiden skapar en KOPIERINGs instruktion som använder Azure AD-vidarekoppling för autentisering. Din [Azure AD-användare måste ha åtkomst](
./sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples.md#d-azure-active-directory-authentication) till arbets ytan med minst Azure-rollen Storage BLOB data Contributor till ADLS Gen2 kontot. 

- Du måste ha de [behörigheter som krävs för att använda kopierings instruktionen](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#permissions) och skapa tabell behörigheter om du skapar en ny tabell som ska läsas in på.

- Den länkade tjänst som är kopplad till ADLS Gen2 kontot **måste ha åtkomst till** / **filmappen** för att kunna läsas in. Om den länkade tjänstens autentiseringsmekanism exempelvis är hanterad identitet, måste den hanterade arbets ytans identitet ha minst behörighet för lagrings-BLOB-läsare på lagrings kontot.

- Om VNet är aktiverat på din arbets yta, se till att den integrerade körningen som är kopplad till det ADLS Gen2-konto som är länkade till data källans källdata och felfilen har aktive rad redigering aktive rad. Interaktiv redigering krävs för autoschema-identifiering, för att Visa käll filens innehåll och bläddra ADLS Gen2 lagrings konton i guiden.

### <a name="steps"></a>Steg

1. Välj det lagrings konto och den fil eller mapp som du läser in från panelen käll lagrings plats. Guiden kommer automatiskt att försöka identifiera Parquet-filer. Om filtypen Parquet inte kan bekräftas används den avgränsade texten som standard.

   ![Välja käll plats](./sql/media/bulk-load/bulk-load-source-location.png)

2. Välj fil formats inställningar, inklusive det lagrings konto där du vill skriva avvisade rader (felfilen). För närvarande stöds endast CSV-och Parquet-filer.

    ![Välja fil formats inställningar](./sql/media/bulk-load/bulk-load-file-format-settings.png)

3. Du kan välja "Förhandsgranska data" för att se hur KOPIERINGs instruktionen kommer att parsa filen för att hjälpa dig att konfigurera fil formats inställningarna. Välj förhands gransknings data varje gång du ändrar fil formats inställningen för att se hur KOPIERINGs instruktionen kommer att parsa filen med den uppdaterade inställningen: för hands version av ![ data](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

> [!NOTE]  
>
> - För hands version av data med fält som innehåller flera bokstäver stöds inte i guiden för Mass inläsning. Guiden för Mass inläsning förhands granskning av data i en enskild kolumn när en avslutning med flera tecken har angetts. 
> - Det finns stöd för att ange rader med flera rader i KOPIERINGs instruktionen. Detta stöds dock inte i guiden för Mass inläsning där ett fel kommer att returneras.

4. Välj den dedikerade SQL-pool som du använder för att läsa in, inklusive om belastningen kommer att användas för en befintlig tabell eller ny tabell: Välj ![ mål plats](./sql/media/bulk-load/bulk-load-target-location.png)

5. Välj "Konfigurera kolumn mappning" för att kontrol lera att du har rätt kolumn mappning. Observera att kolumn namn identifieras automatiskt om "Härled kolumn namn" har Aktiver ATS. För nya tabeller är det viktigt att konfigurera kolumn mappningen för att uppdatera mål kolumnens data typer: ![ Konfigurera kolumn mappning](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)

6. Välj "öppna skript" och ett T-SQL-skript genereras med KOPIERINGs instruktionen att läsa in från data Lake: ![ Öppna SQL-skriptet](./sql/media/bulk-load/bulk-load-target-final-script.png)

## <a name="next-steps"></a>Nästa steg

- Se artikeln [Kopiera instruktion](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax) för mer information om kopierings funktioner
- Se artikeln [Översikt över data inläsning](./sql-data-warehouse/design-elt-data-loading.md#what-is-elt)
