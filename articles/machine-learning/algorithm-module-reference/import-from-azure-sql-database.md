---
title: Importera från Azure SQL Database
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen importera data i Azure Machine Learning Visual Interface för att hämta data från en Azure SQL Database.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: 374776088d93813f39122f2018b00466c55d2b6e
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72694615"
---
# <a name="import-from-azure-sql-database"></a>Importera från Azure SQL Database

Den här artikeln beskriver hur du använder modulen [Importera data](import-data.md) i Azure Machine Learning Visual Interface för att hämta data från en Azure SQL Database.  

Om du vill importera data från en databas måste du ange både server namnet och databas namnet och en SQL-instruktion som definierar tabellen, vyn eller frågan.  

I allmänhet är det dyrare att lagra data i Azure-databaser än att använda tabeller eller blobbar i Azure. Det kan också finnas begränsningar för mängden data som du kan lagra i en databas, beroende på din prenumerations typ. Det finns dock inga transaktions avgifter mot SQL Azure Database, så alternativet är idealiskt för snabb åtkomst till mindre mängder information som används ofta, till exempel data uppslags tabeller eller data ord listor.

Att lagra data i en Azure Database rekommenderas också om du behöver kunna filtrera data innan du läser dem, eller om du vill spara förutsägelser eller mått till databasen för rapportering.

## <a name="how-to-import-data-from-azure-sql-database"></a>Så här importerar du data från Azure SQL Database

1. Lägg till modulen [Importera data](import-data.md) till din pipeline. Du hittar den här modulen i Visual Interface i kategorin data indata och utdata.

1. För **data källa**väljer du **Azure SQL Database**.

1. Ange följande alternativ som är speciella för Azure SQL Database.

    **Databas server namn**: Ange det Server namn som genereras av Azure. Den har vanligt vis formuläret `<generated_identifier>.database.windows.net`.

    **Databas namn**: Ange namnet på en befintlig databas på den server som du har angett.

    **Användar konto namn för Server**: Ange användar namnet för ett konto som har åtkomst behörighet för databasen.

    **Lösen ord för serverns användar konto**: Ange lösen ordet för det angivna användar kontot.

    **Databas fråga**: Skriv eller klistra in en SQL-instruktion som beskriver de data som du vill läsa. Validera alltid SQL-instruktionen och verifiera frågeresultaten i förväg med hjälp av ett verktyg som Visual Studio Server Explorer eller SQL Server Data Tools.

1. Om data uppsättningen som du läser i Azure Machine Learning inte förväntas ändras mellan körningar av pipelinen väljer du alternativet **Använd cachelagrat resultat** .

    Om det är markerat, om det inte finns några andra ändringar i modulens parametrar, läser pipelinen in data första gången modulen körs och använder därefter en cachelagrad version av data uppsättningen.

    Om du vill läsa in data uppsättningen på nytt i varje iteration av pipelinen avmarkerar du det här alternativet. Data uppsättningen läses in från källan varje gången eventuella parametrar ändras i [Importera data](import-data.md).

1. Köra en pipeline.

    När [import data](import-data.md) läser in data i visuellt gränssnitt kan en del implicit typ konvertering också utföras, beroende på vilka data typer som används i käll databasen.

## <a name="results"></a>Resultat

När importen är klar klickar du på data uppsättningen för utdata och väljer **visualisera** för att se om data har importer ATS korrekt.

Du kan också ändra data uppsättningen och dess metadata med verktygen i Visual Interface:

- Använd [Redigera metadata](edit-metadata.md) om du vill ändra kolumn namn, konvertera en kolumn till en annan datatyp eller ange vilka kolumner som är etiketter eller funktioner.

- Använd [Välj kolumner i data uppsättning](select-columns-in-dataset.md) för att välja en delmängd av kolumner.

- Använd [partition och exempel](partition-and-sample.md) för att avgränsa data uppsättningen efter villkor eller hämta de översta n raderna.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 
