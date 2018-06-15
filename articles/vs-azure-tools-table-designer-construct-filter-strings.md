---
title: Hur du skapar filtersträngar för tabelldesign | Microsoft Docs
description: Hur du skapar filtersträngar för tabelldesign
services: visual-studio-online
author: ghogen
manager: douge
assetId: a1a10ea1-687a-4ee1-a952-6b24c2fe1a22
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/18/2016
ms.author: ghogen
ms.openlocfilehash: 7390e8f463d14f2e74e05263e970665a0199029a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31794287"
---
# <a name="constructing-filter-strings-for-the-table-designer"></a>Hur du skapar Filtersträngar för tabelldesign
## <a name="overview"></a>Översikt
Att filtrera data i en Azure-tabell som visas i Visual Studio **tabelldesignern**, du skapar en Filtersträng och ange det i filterfältet. Strängsyntaxen filter definieras av WCF Data Services och liknar en SQL WHERE-sats, men skickas till tabelltjänsten via en HTTP-begäran. Den **tabelldesignern** hanterar, kodning så för att filtrera på önskad egenskapsvärde, behöver du bara ange egenskapsnamn, jämförelseoperator, värde, och du kan också Boolesk operator i filterfältet. Du behöver inte inkludera frågealternativet $filter som om du konstruera en URL för att fråga tabellen via den [Storage Services REST API-referens](http://go.microsoft.com/fwlink/p/?LinkId=400447).

WCF Data Services baseras på den [Open Data Protocol](http://go.microsoft.com/fwlink/p/?LinkId=214805) (OData). Mer information om frågealternativet filter system (**$filter**), finns det [konventioner för OData-URI-specifikationen](http://go.microsoft.com/fwlink/p/?LinkId=214806).

## <a name="comparison-operators"></a>Jämförelseoperatorer
Följande logiska operatorer stöds för alla egenskapstyper av:

| Logisk operator | Beskrivning | Exempel Filtersträngen |
| --- | --- | --- |
| EQ |Lika med |Stad eq 'Redmond' |
| gt |Större än |Priset gt 20 |
| ge |Större än eller lika med |Pris-ge 10 |
| lt |Mindre än |Priset lt 20 |
| le |Mindre än eller lika med |Priset le 100 |
| ne |Inte lika med |Stad ne ”London” |
| och |Och |Priset le 200 och pris gt 3.5 |
| eller |Eller |Priset le 3.5 eller pris gt 200 |
| inte |inte |inte isAvailable |

När man skapar en Filtersträng är följande regler viktiga:

* Använd logiska operatorer för att jämföra en egenskap till ett värde. Observera att det inte går att jämföra en egenskap för ett dynamiskt värde. en sida av uttrycket måste vara en konstant.
* Alla delar av filtersträngen är skiftlägeskänsliga.
* Det konstanta värdet måste vara av samma datatyp som egenskapen för filtret för att returnera giltiga resultat. Mer information om egenskapstyper som stöds finns i [Förstå Tabelltjänst-datamodellen](http://go.microsoft.com/fwlink/p/?LinkId=400448).

## <a name="filtering-on-string-properties"></a>Filtrering på egenskaperna för anslutningssträngen
När du filtrerar på egenskaperna för anslutningssträngen omges av strängkonstant enkla citattecken.

I följande exempel filtrerar på den **PartitionKey** och **RowKey** egenskaper; ytterligare icke-nyckeltyp egenskaper kan också läggas till Filtersträngen:

    PartitionKey eq 'Partition1' and RowKey eq '00001'

Du kan sätta varje filteruttrycket inom parentes, även om det inte krävs:

    (PartitionKey eq 'Partition1') and (RowKey eq '00001')

Observera att tabelltjänsten inte har stöd för jokertecken används i frågor och de stöds inte i tabelldesignern antingen. Du kan dock utföra matchning med jämförelseoperatorer på önskad prefix prefix. I följande exempel returnerar entiteter med ett efternamn egenskap som börjar med bokstaven ”A”:

    LastName ge 'A' and LastName lt 'B'

## <a name="filtering-on-numeric-properties"></a>Filtrering på numeriska egenskaper
Ange hur många utan citattecken om du vill filtrera på ett heltal eller ett flyttal.

Det här exemplet returnerar alla entiteter med ålder egenskapen vars värde är större än 30:

    Age gt 30

Det här exemplet returnerar alla entiteter med egenskapen AmountDue vars värde är mindre än eller lika med 100.25:

    AmountDue le 100.25

## <a name="filtering-on-boolean-properties"></a>Filtrering på booleska egenskaper
Om du vill filtrera på ett booleskt värde, ange **SANT** eller **FALSKT** utan citattecken.

I följande exempel returneras alla entiteter där egenskapen IsActive är inställt på **SANT**:

    IsActive eq true

Du kan också skriva det här filteruttrycket utan logisk operator. I följande exempel tabelltjänsten också returneras alla entiteter där IsActive är **SANT**:

    IsActive

Du kan använda inte för att returnera alla entiteter om IsActive är false, operator:

    not IsActive

## <a name="filtering-on-datetime-properties"></a>Filtrering på datum/tid-egenskaper
Om du vill filtrera på ett DateTime-värde, ange den **datetime** följt av datum/tid-konstanten inom enkla citattecken. Datum/tid-konstanten måste vara i kombinerade UTC-format, enligt beskrivningen i [formatering DateTime egenskapsvärden](http://go.microsoft.com/fwlink/p/?LinkId=400449).

I följande exempel returnerar entiteter där egenskapen CustomerSince är lika med 10 juli 2008:

    CustomerSince eq datetime'2008-07-10T00:00:00Z'
