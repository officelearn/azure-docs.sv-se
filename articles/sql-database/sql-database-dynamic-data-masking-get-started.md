---
title: Dynamisk datamaskning
description: Dynamisk data maskning begränsar känslig data exponering genom att maskera den till icke-privilegierade användare för SQL Database och Azure-Synapse
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/06/2020
tags: azure-synpase
ms.openlocfilehash: 2759644c68d65e76de222a0ac74f1d4900caddc0
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83121260"
---
# <a name="dynamic-data-masking-for-azure-sql-database-and-azure-synapse-analytics"></a>Dynamisk data maskning för Azure SQL Database och Azure Synapse Analytics

SQL Database dynamisk data maskning begränsar känslig data exponering genom att maskera den till icke-privilegierade användare. 

Dynamisk datamaskning förhindrar obehörig åtkomst till känsliga data genom att låta kunder ange hur mycket av känsliga data som avslöja med minimal påverkan på programnivån. Det är en principbaserad säkerhetsfunktion som fungerar genom att dölja känslig data i resultatuppsättningen för en fråga över angivna databasfält, medan data i databasen förblir oförändrad.

En tjänst representant i ett Call Center kan till exempel identifiera anropare med flera siffror i deras kreditkorts nummer, men dessa data objekt bör inte vara helt exponerade för tjänst representanten. En masknings regel kan definieras som maskerar alla de sista fyra siffrorna i ett kreditkorts nummer i resultat uppsättningen för en fråga. Ett annat exempel är att en lämplig datamask kan definieras för att skydda personligt identifierbar information (PII)-data, så att en utvecklare kan fråga produktions miljöer i fel söknings syfte utan att överskrida reglerna för efterlevnad.

## <a name="dynamic-data-masking-basics"></a>Grunder för dynamisk data maskering

Du konfigurerar en princip för dynamisk data maskering i Azure Portal genom att välja åtgärden dynamisk data maskning i bladet för SQL Database konfiguration eller bladet inställningar. Den här funktionen kan inte ställas in med Portal för Azure-Synapse (Använd PowerShell eller REST API)

### <a name="dynamic-data-masking-permissions"></a>Behörigheter för dynamisk data maskering

Dynamisk datamaskering kan konfigureras av rollerna Azure SQL Database admin, Server administratör eller [SQL Security Manager](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) .

### <a name="dynamic-data-masking-policy"></a>Princip för dynamisk data maskering

* **SQL-användare exkluderas från maskering** – en uppsättning SQL-användare eller AAD-identiteter som hämtar data som inte har maskerats i SQL-frågeresultaten. Användare med administratörs behörighet är alltid exkluderade från maskering och kan se ursprungliga data utan någon mask.
* **Masknings regler** – en uppsättning regler som definierar de angivna fälten som ska maskeras och den masknings funktion som används. De angivna fälten kan definieras med ett databas schema namn, tabell namn och kolumn namn.
* **Maskera funktioner** – en uppsättning metoder som styr exponeringen av data för olika scenarier.

| Maskerings funktion | Maskering av logik |
| --- | --- |
| **Standardvärde** |**Fullständig maskering enligt data typerna för de angivna fälten**<br/><br/>• Använd XXXX eller färre XS om fältets storlek är mindre än 4 tecken för sträng data typer (nchar, ntext, nvarchar).<br/>• Använd ett nollvärde för numeriska data typer (bigint, bit, decimal, int, Money, numeric, smallint, smallmoney, tinyint, Float, Real).<br/>• Använd 01-01-1900 för datum-/tids data typer (Date, datetime2, DateTime, DateTimeOffset, smalldatetime, Time).<br/>• För SQL-variant används standardvärdet för den aktuella typen.<br/>• För XML används dokumentet \< maskerat/>.<br/>• Använd ett tomt värde för särskilda data typer (tidsstämpel-tabell, hierarchyid, GUID, binär, bild, varbinary spatial types). |
| **Kreditkort** |**Masknings metod som visar de sista fyra siffrorna i de angivna fälten** och lägger till en konstant sträng som ett prefix i form av ett kredit kort.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **E-post** |**Maskerings metod som visar den första bokstaven och ersätter domänen med xxx.com** med ett konstant sträng-prefix i form av en e-postadress.<br/><br/>aXX@XXXX.com |
| **Slumptal** |**Maskerings metod, som genererar ett slumptal** enligt de valda gränserna och faktiska data typerna. Om de angivna gränserna är lika är Maskerings funktionen ett konstant nummer.<br/><br/>![Navigeringsfönster](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Anpassad text** |**Maskerings metod som visar de första och sista tecknen** och lägger till en anpassad utfyllnads sträng i mitten. Om den ursprungliga strängen är kortare än det exponerade prefixet och suffixet, används bara utfyllnads strängen. <br/>prefix [utfyllnad] suffix<br/><br/>![Navigeringsfönster](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Rekommenderade fält att maskera

DDM rekommenderar motorn att flagga vissa fält från databasen som potentiellt känsliga fält, vilket kan vara bra för att maskera. På bladet dynamisk data maskning i portalen ser du de rekommenderade kolumnerna för din databas. Allt du behöver göra är att klicka på **Lägg till mask** för en eller flera kolumner och sedan **Spara** för att tillämpa en mask för dessa fält.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Konfigurera dynamisk data maskning för din databas med PowerShell-cmdletar

### <a name="data-masking-policy"></a>Princip för data maskering

- [Get-AzSqlDatabaseDataMaskingPolicy](https://docs.microsoft.com/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingPolicy)
- [Set-AzSqlDatabaseDataMaskingPolicy](https://docs.microsoft.com/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingPolicy)

### <a name="data-masking-rules"></a>Regler för data maskering

- [Get-AzSqlDatabaseDataMaskingRule](https://docs.microsoft.com/powershell/module/az.sql/Get-AzSqlDatabaseDataMaskingRule)
- [New-AzSqlDatabaseDataMaskingRule](https://docs.microsoft.com/powershell/module/az.sql/New-AzSqlDatabaseDataMaskingRule)
- [Remove-AzSqlDatabaseDataMaskingRule](https://docs.microsoft.com/powershell/module/az.sql/Remove-AzSqlDatabaseDataMaskingRule)
- [Set-AzSqlDatabaseDataMaskingRule](https://docs.microsoft.com/powershell/module/az.sql/Set-AzSqlDatabaseDataMaskingRule)

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Konfigurera dynamisk data maskning för databasen med hjälp av REST API

Du kan använda REST API för att hantera data masknings principer och regler program mässigt. Den publicerade REST API har stöd för följande åtgärder:

### <a name="data-masking-policies"></a>Principer för datamaskering

- [Skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/datamaskingpolicies/createorupdate): skapar eller uppdaterar känslighets etiketten för den angivna kolumnen.
- [Get](https://docs.microsoft.com/rest/api/sql/datamaskingpolicies/get): hämtar en databas data masking-princip. 

### <a name="data-masking-rules"></a>Regler för data maskering

- [Skapa eller uppdatera](https://docs.microsoft.com/rest/api/sql/datamaskingrules/createorupdate): skapar eller uppdaterar en databas data masking-regel.
- [Lista efter databas](https://docs.microsoft.com/rest/api/sql/datamaskingrules/listbydatabase): hämtar en lista över regler för databas data maskning.
