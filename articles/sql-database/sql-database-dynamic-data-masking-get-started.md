---
title: Azure SQL Database dynamisk datamaskning | Microsoft docs
description: SQL Database dynamisk datamaskning begränsar exponering av känsliga data genom att maskera den för icke-privilegierade användare
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
ms.date: 03/04/2019
ms.openlocfilehash: c174167268e95e55b0916d2340005538d3ae8add
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57336792"
---
# <a name="sql-database-dynamic-data-masking"></a>SQL Database dynamisk datamaskning

SQL Database dynamisk datamaskning begränsar exponering av känsliga data genom att maskera den för icke-privilegierade användare. 

Dynamisk datamaskning förhindrar obehörig åtkomst till känsliga data genom att låta kunder ange hur mycket av känsliga data som avslöja med minimal påverkan på programnivån. Det är en principbaserad säkerhetsfunktion som fungerar genom att dölja känslig data i resultatuppsättningen för en fråga över angivna databasfält, medan data i databasen förblir oförändrad.

Till exempel en representant på ett Callcenter kan identifiera anropare genom flera siffrorna i sina kreditkortsnummer, men de dataobjekt bör inte exponeras fullständigt för servicerepresentant. Du kan definiera en maskningsregel att masker alla men de sista fyra siffrorna i alla kreditkortsnummer i resultatet som angetts av en fråga. Ett annat exempel är en mask för lämpliga data kan du definiera i syfte för att skydda personligt identifierbar information (PII), så att utvecklare kan fråga produktionsmiljöer för felsökning utan brott mot kompatibilitetsföreskrifter.

## <a name="sql-database-dynamic-data-masking-basics"></a>SQL Database dynamisk datamaskning grunderna

Du ställer in en dynamisk datamaskning principen i Azure portal genom att välja åtgärden i din SQL Database konfigurationsbladet eller inställningsbladet för dynamisk datamaskning.

### <a name="dynamic-data-masking-permissions"></a>Dynamiska data maskning behörigheter

Dynamisk datamaskning kan konfigureras av administratören för Azure SQL Database,-serveradministratören eller [SQL-säkerhetsansvarig](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#sql-security-manager) roller.

### <a name="dynamic-data-masking-policy"></a>Dynamiska data maskning princip

* **SQL-användare uteslutna från maskering** – en uppsättning SQL-användare eller AAD-identiteter som hämtar omaskerat data i SQL-frågeresultat. Användare med administratörsbehörighet är alltid uteslutna från maskering och finns i den ursprungliga informationen utan någon mask.
* **Maskera regler** – en uppsättning regler som definierar de avsedda fält som ska vara dold och maskningsfunktion som används. De angivna fält kan definieras med ett databasnamn för schemat, tabellnamnet och kolumnnamnet.
* **Maskera funktioner** – en uppsättning metoder som styr exponering av data för olika scenarier.

| Maskeringsfunktion | Maskera logik |
| --- | --- |
| **Standard** |**Fullständig maskning enligt datatyperna för respektive fält**<br/><br/>• Använd XXXX eller färre Xs om fältet är mindre än 4 tecken för strängdatatyper (nchar, ntext, nvarchar).<br/>• Använd värdet noll för numeriska datatyper (bigint, bit, decimal, int, pengar, numeriska, smallint, smallmoney, tinyint, float, real).<br/>• Använd 01-01-1900 för datum/tid-datatyper (datum, datetime2, datetime, datetimeoffset, smalldatetime, tid).<br/>• För SQL-variant, aktuell typ standardvärdet används.<br/>• För XML-dokumentet <masked/> används.<br/>• Använd ett tomt värde för speciella datatyper (tidsstämpel tabell, hierarchyid, GUID, binary, bild, varbinary spatial typer). |
| **Kreditkort** |**Maskera metod, som visar de sista fyra siffrorna i fälten avsedda** och lägger till en konstant sträng som ett prefix i form av ett kreditkort.<br/><br/>XXXX-XXXX-XXXX-1234 |
| **E-post** |**Maskera metod, som visar den första bokstaven och ersätter domänen med XXX.com** med hjälp av en konstantsträng prefix i form av en e-postadress.<br/><br/>aXX@XXXX.com |
| **Slumptal** |**Maskering av metoden, vilket genererar ett slumptal** enligt de valda gränser och faktiska datatyper. Om de angivna gränserna är lika med, är maskningsfunktion ett tal.<br/><br/>![Navigeringsfönstret](./media/sql-database-dynamic-data-masking-get-started/1_DDM_Random_number.png) |
| **Anpassad text** |**Maskera metod, som visar de första och sista tecknet** och lägger till en anpassad utfyllnad sträng i mitten. Om den ursprungliga strängen är kortare än exponerat prefix och suffix, används endast utfyllnad strängen. <br/>prefix[padding]suffix<br/><br/>![Navigeringsfönstret](./media/sql-database-dynamic-data-masking-get-started/2_DDM_Custom_text.png) |

<a name="Anchor1"></a>

### <a name="recommended-fields-to-mask"></a>Rekommenderade fält att maskera

Rekommendationsmotorn DDM flaggar vissa fält från databasen som potentiellt känsliga, vilket kan vara bra kandidater för Maskning. Dynamisk Datamaskning-bladet i portalen visas de rekommendera kolumnerna för din databas. Allt du behöver göra är att klicka på **Lägg till Mask** för en eller flera kolumner och sedan **spara** att tillämpa en mask för dessa fält.

## <a name="set-up-dynamic-data-masking-for-your-database-using-powershell-cmdlets"></a>Konfigurera dynamisk datamaskering för din databas med Powershell-cmdletar

Se [cmdlet: ar för Azure SQL Database](https://docs.microsoft.com/powershell/module/az.sql).

## <a name="set-up-dynamic-data-masking-for-your-database-using-rest-api"></a>Konfigurera dynamisk datamaskning för din databas med hjälp av REST API

Se [åtgärder för Azure SQL Database](https://docs.microsoft.com/rest/api/sql/).
