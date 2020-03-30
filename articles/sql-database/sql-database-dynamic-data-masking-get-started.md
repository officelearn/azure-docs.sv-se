---
title: Dynamisk datamaskning
description: Dynamisk datamaskering begränsar känslig dataexponering genom att maskera den för icke-privilegierade användare för SQL Database och Azure Synapse
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
ms.openlocfilehash: e5b281d59245d8fbd32b18f4ac5fe577fc7ff309
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78192922"
---
# <a name="dynamic-data-masking-for-azure-sql-database-and-azure-synapse-analytics"></a>Dynamisk datamaskering för Azure SQL Database och Azure Synapse Analytics

Dynamisk datamaskering i SQL Database begränsar känslig dataexponering genom att maskera den för användare som inte är privilegierade. 

Dynamisk datamaskning förhindrar obehörig åtkomst till känsliga data genom att låta kunder ange hur mycket av känsliga data som avslöja med minimal påverkan på programnivån. Det är en principbaserad säkerhetsfunktion som fungerar genom att dölja känslig data i resultatuppsättningen för en fråga över angivna databasfält, medan data i databasen förblir oförändrad.

En servicerepresentant på ett callcenter kan till exempel identifiera de som ringer med flera siffror i sitt kreditkortsnummer, men dessa dataobjekt bör inte vara helt exponerade för servicerepresentanten. En maskeringsregel kan definieras som maskerar alla utom de fyra sista siffrorna i ett kreditkortsnummer i resultatuppsättningen för en fråga. Som ett annat exempel kan en lämplig datamask definieras för att skydda personligt identifierbara informationsdata (PII) så att en utvecklare kan fråga produktionsmiljöer för felsökningsändamål utan att bryta mot efterlevnadsreglerna.

## <a name="dynamic-data-masking-basics"></a>Grundläggande om dynamisk datamaskering

Du ställer in en princip för dynamisk datamaskering i Azure-portalen genom att välja den dynamiska datamaskeringsåtgärden i konfigurationsbladet eller inställningsbladet för SQL Database. Den här funktionen kan inte anges med hjälp av portal för Azure Synapse (Använd Powershell eller REST API)

### <a name="dynamic-data-masking-permissions"></a>Dynamiska datamaskeringsbehörigheter

Dynamisk datamaskering kan konfigureras av azure SQL Database admin, serveradministratör eller [SQL Security Manager-roller.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager)

### <a name="dynamic-data-masking-policy"></a>Princip för dynamisk datamaskering

* **SQL-användare som inte är maskerade** - En uppsättning SQL-användare eller AAD-identiteter som får omaskerade data i SQL-frågeresultatet. Användare med administratörsbehörighet är alltid undantagna från maskering och ser de ursprungliga data utan mask.
* **Maskeringsregler** - En uppsättning regler som definierar de angivna fälten som ska maskeras och maskeringsfunktionen som används. De angivna fälten kan definieras med hjälp av ett databasschemanamn, tabellnamn och kolumnnamn.
* **Maskeringsfunktioner** - En uppsättning metoder som styr exponeringen av data för olika scenarier.

| Maskeringsfunktion | Maskeringslogik |
| --- | --- |
| **Default** |**Fullständig maskering enligt datatyperna i de angivna fälten**<br/><br/>• Använd XXXX eller färre X om fältets storlek är mindre än 4 tecken för strängdatatyper (nchar, ntext, nvarchar).<br/>• Använd ett nollvärde för numeriska datatyper (bigint, bit, decimal, int, pengar, numeriskt, smallint, smallmoney, tinyint, float, real).<br/>• Använd 01-01-1900 för datum-/tidsdatatyper (datum, datumtid2, datetime, datetimeoffset, smalldatetime, tid).<br/>• För SQL-variant används standardvärdet för den aktuella typen.<br/>• För XML \<används det maskerade/> dokumentet.<br/>• Använd ett tomt värde för speciella datatyper (tidsstämpeltabell, hierarki, GUID, binär, bild, varbinära rumsliga typer). |
| **Kreditkort** |**Maskeringsmetod, som visar de fyra sista siffrorna i de angivna fälten** och lägger till en konstant sträng som prefix i form av ett kreditkort.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **Email** |**Maskeringsmetod, som exponerar den första bokstaven och ersätter domänen med XXX.com** med hjälp av ett konstant strängprefix i form av en e-postadress.<br/><br/>aXX@XXXX.com |
| **Slumpmässigt tal** |**Maskeringsmetod, som genererar ett slumptal** enligt de valda gränserna och faktiska datatyper. Om de angivna gränserna är lika är maskeringsfunktionen ett konstant tal.<br/><br/>![Navigeringsfönster](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Anpassad text** |**Maskeringsmetod, som exponerar det första och sista tecknen** och lägger till en anpassad utfyllnadssträng i mitten. Om den ursprungliga strängen är kortare än det exponerade prefixet och suffixet används bara utfyllnadssträngen. <br/>prefix[utfyllnad]suffix<br/><br/>![Navigeringsfönster](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Rekommenderade fält att maskera

DDM-rekommendationer motorn, flaggor vissa fält från databasen som potentiellt känsliga fält, vilket kan vara bra kandidater för maskering. I bladet Dynamisk datamaskering i portalen visas de rekommenderade kolumnerna för databasen. Allt du behöver göra är att klicka på **Lägg till mask** för en eller flera kolumner och sedan **Spara** om du vill använda en mask för dessa fält.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Konfigurera dynamisk datamaskering för databasen med PowerShell-cmdlets

Se [Azure SQL Database Cmdlets](https://docs.microsoft.com/powershell/module/az.sql).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Konfigurera dynamisk datamaskering för databasen med REST API

Se [Åtgärder för Azure SQL Database](https://docs.microsoft.com/rest/api/sql/).
