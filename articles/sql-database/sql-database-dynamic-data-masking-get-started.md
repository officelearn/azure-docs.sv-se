---
title: Azure SQL Database dynamisk datamaskning | Microsoft docs
description: "SQL-databas dynamisk datamaskning begränsar exponering av känsliga data genom att maskera till icke-privilegierade användare"
services: sql-database
author: ronitr
manager: craigg
ms.service: sql-database
ms.custom: security
ms.topic: article
ms.date: 03/09/2017
ms.author: ronitr
ms.openlocfilehash: 543b4bc15177cc198aaa3c312b563a4bd6e844fc
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="sql-database-dynamic-data-masking"></a>SQL-databas dynamisk datamaskning

SQL-databas dynamisk datamaskning begränsar exponering av känsliga data genom att maskera till icke-privilegierade användare. 

Dynamisk datamaskning förhindrar obehörig åtkomst till känsliga data genom att låta kunder ange hur mycket av känsliga data som avslöja med minimal påverkan på programnivån. Det är en principbaserad säkerhetsfunktion som fungerar genom att dölja känslig data i resultatuppsättningen för en fråga över angivna databasfält, medan data i databasen förblir oförändrad.

Till exempel en representant på ett Callcenter kan identifiera anropare med flera siffrorna i sina kreditkortsnummer, men de data som ska inte visas helt till representanten. En maskningsregel kan definieras att masker alla, men de sista fyra siffrorna i alla kreditkortsnummer i resultatet som angetts av en fråga. Ett annat exempel kan en lämplig mask definieras för att skydda personligt identifierbar information (PII) data så att utvecklare kan fråga produktionsmiljöer i felsökningssyfte utan brott mot förordningar.

## <a name="sql-database-dynamic-data-masking-basics"></a>SQL-databas dynamisk datamaskering grunderna
Du kan konfigurera en princip i Azure-portalen för dynamisk datamaskering genom att välja åtgärden i bladet för konfiguration av SQL-databas eller inställningsbladet för dynamisk datamaskering.

### <a name="dynamic-data-masking-permissions"></a>Dynamiska datamaskering behörigheter
Dynamisk datamaskning kan konfigureras med Azure Database admin, serveradministratör eller som säkerhetsroller.

### <a name="dynamic-data-masking-policy"></a>Dynamiska datamaskering princip
* **SQL-användare uteslutna från maskering** – en uppsättning SQL-användare eller AAD identiteter som hämtar omaskerat data i SQL-frågans resultat. Användare med administratörsbehörighet är alltid uteslutna från maskering och se de ursprungliga data utan någon mask.
* **Maskering regler** – en uppsättning regler som definierar de angivna fält som ska maskeras och maskningsfunktion som används. De angivna fält kan definieras med hjälp av ett databasnamn schemat, tabellnamnet och kolumnnamnet.
* **Maskering funktioner** – en uppsättning metoder som styr exponering av data för olika scenarier.

| Maskningsfunktion | Maskering logik |
| --- | --- |
| **Standard** |**Fullständig maskering enligt datatyperna för de angivna fält**<br/><br/>• Använd XXXX eller färre Xs om storleken på fältet är mindre än 4 tecken för strängdatatyper (nchar, ntext, nvarchar).<br/>• Använda värdet noll för numeriska datatyper (bigint, bit, decimal, int, money, numeriska, smallint, smallmoney, tinyint, float, real).<br/>• Använda 1900-01-01 för datum/tid-datatyper (datum, datetime2, datetime, datetimeoffset, smalldatetime, tid).<br/>• För SQL-variant, standardvärdet för den aktuella typen används.<br/>• För XML-dokumentet <masked/> används.<br/>• Använda ett tomt värde för speciella datatyper (tidsstämpel tabell, hierarchyid, GUID, binary, image, varbinary spatialtyper). |
| **Kreditkort** |**Maskering av-metoden, som visar de fyra sista siffrorna i fälten avsedda** och lägger till en konstant sträng som prefix i form av ett kreditkort.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **E-post** |**Maskering av-metoden, som visar den första bokstaven och ersätter domänen med XXX.com** med ett konstant sträng som prefix i form av en e-postadress.<br/><br/>aXX@XXXX.com |
| **Slumptal** |**Maskering av metoden, vilket genererar ett slumptal** enligt valda gränser och faktiska datatyper. Om de angivna gränserna är lika är ett tal med funktionen maskering.<br/><br/>![Navigeringsfönstret](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Anpassad text** |**Maskering av-metoden, som visar de första och sista tecknen** och lägger till en anpassad utfyllnad sträng i mitten. Om den ursprungliga strängen är kortare än exponerade prefixet och suffixet används utfyllnad strängen. <br/>prefix[padding]suffix<br/><br/>![Navigeringsfönstret](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Rekommenderade fält att maskera
DDM rekommendationer motorn flaggor vissa fält från databasen som potentiellt känsliga, vilket kan vara bra kandidater för maskering. I bladet dynamisk Datamaskering i portalen visas de rekommenderade kolumnerna för din databas. Allt du behöver göra är att klicka på **Lägg till Mask** för en eller flera kolumner och sedan **spara** att tillämpa en mask för dessa fält.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Konfigurera dynamisk datamaskering för databasen med hjälp av Powershell-cmdlets
Se [Azure SQL Database-Cmdlets](https://msdn.microsoft.com/library/azure/mt574084.aspx).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Konfigurera dynamisk datamaskning för databasen med hjälp av REST API
Se [åtgärder för Azure SQL-databaser](https://msdn.microsoft.com/library/dn505719.aspx).

