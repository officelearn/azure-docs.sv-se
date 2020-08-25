---
title: FHIR funktioner som stöds i Azure – Azure API för FHIR
description: I den här artikeln beskrivs vilka funktioner i FHIR-specifikationen som implementeras i Azure API för FHIR
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 46568bf3969d050fd964c85278debd9d599db266
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88796569"
---
# <a name="features"></a>Funktioner

Azure API för FHIR tillhandahåller en fullständigt hanterad distribution av Microsoft FHIR-servern för Azure. Servern är en implementering av [FHIR](https://hl7.org/fhir) -standarden. Det här dokumentet listar huvud funktionerna i FHIR-servern.

## <a name="fhir-version"></a>FHIR-version

Senaste version som stöds: `4.0.1`

Tidigare versioner som också stöds är: `3.0.2`

## <a name="rest-api"></a>REST-API

| API                            | Stöds – PaaS | Support – OSS (SQL) | Support – OSS (Cosmos DB) | Kommentar                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| läsa                           | Ja       | Ja       | Ja       |                                                     |
| vread                          | Ja       | Ja       | Ja       |                                                     |
| update                         | Ja       | Ja       | Ja       |                                                     |
| uppdatera med optimistisk låsning | Ja       | Ja       | Ja       |                                                     |
| uppdatering (villkorligt)           | Ja       | Ja       | Ja       |                                                     |
| 9.0a                          | Inga        | Inga        | Inga        |                                                     |
| delete                         | Ja       | Ja       | Ja       |                                                     |
| ta bort (villkorligt)           | Inga        | Inga        | Inga        |                                                     |
| skapa                         | Ja       | Ja       | Ja       | Stöd för både efter-och-placering                               |
| Skapa (villkorlig)           | Ja       | Ja       | Ja       |                                                     |
| sök                         | Delvis   | Delvis   | Delvis   | Se nedan                                           |
| kedjad sökning                 | Inga        | Ja       | Inga        |                                           |
| omvänd länkad sökning         | Inga        | Inga        | Inga        |                                            |
| funktioner                   | Ja       | Ja       | Ja       |                                                     |
| batch                          | Ja       | Ja       | Ja       |                                                     |
| transaktionen                    | Inga        | Ja       | Inga        |                                                     |
| historik                        | Ja       | Ja       | Ja       |                                                     |
| växlings                         | Delvis   | Delvis   | Delvis   | `self` och `next` stöds                     |
| mellanhand                 | Inga        | Inga        | Inga        |                                                     |

## <a name="search"></a>Sök

Alla typer av Sök parametrar stöds. 

| Typ av Sök parameter | Stöds – PaaS | Support – OSS (SQL) | Support – OSS (Cosmos DB) | Kommentar |
|-----------------------|-----------|-----------|-----------|---------|
| Antal                | Ja       | Ja       | Ja       |         |
| Datum/DateTime         | Ja       | Ja       | Ja       |         |
| Sträng                | Ja       | Ja       | Ja       |         |
| Token                 | Ja       | Ja       | Ja       |         |
| Referens             | Ja       | Ja       | Ja       |         |
| Sammansatt             | Ja       | Ja       | Ja       |         |
| Quantity              | Ja       | Ja       | Ja       |         |
| URI                   | Ja       | Ja       | Ja       |         |
| Speciella               | Inga        | Inga        | Inga        |         |


| Modifierare             | Stöds – PaaS | Support – OSS (SQL) | Support – OSS (Cosmos DB) | Kommentar |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Ja       | Ja       | Ja       |         |
|`:exact`               | Ja       | Ja       | Ja       |         |
|`:contains`            | Ja       | Ja       | Ja       |         |
|`:text`                | Ja       | Ja       | Ja       |         |
|`:in` åtkomsttokenbegäran          | Inga        | Inga        | Inga        |         |
|`:below` åtkomsttokenbegäran       | Inga        | Inga        | Inga        |         |
|`:above` åtkomsttokenbegäran       | Inga        | Inga        | Inga        |         |
|`:not-in` åtkomsttokenbegäran      | Inga        | Inga        | Inga        |         |
|`:[type]` förhållande  | Inga        | Inga        | Inga        |         |
|`:below` URI         | Ja       | Ja       | Ja       |         |
|`:not`                 | Inga        | Inga        | Inga        |         |
|`:above` URI         | Inga        | Inga        | Inga        | Problem [#158](https://github.com/Microsoft/fhir-server/issues/158) |

| Vanlig Sök parameter | Stöds – PaaS | Support – OSS (SQL) | Support – OSS (Cosmos DB) | Kommentar |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Ja       | Ja       | Ja       |         |
| `_lastUpdated`          | Ja       | Ja       | Ja       |         |
| `_tag`                  | Ja       | Ja       | Ja       |         |
| `_profile`              | Ja       | Ja       | Ja       |         |
| `_security`             | Ja       | Ja       | Ja       |         |
| `_text`                 | Inga        | Inga        | Inga        |         |
| `_content`              | Inga        | Inga        | Inga        |         |
| `_list`                 | Ja       | Ja       | Ja       |         |
| `_has`                  | Inga        | Inga        | Inga        |         |
| `_type`                 | Ja       | Ja       | Ja       |         |
| `_query`                | Inga        | Inga        | Inga        |         |

| Sök åtgärder       | Stöds – PaaS | Support – OSS (SQL) | Support – OSS (Cosmos DB) | Kommentar |
|-------------------------|-----------|-----------|-----------|---------|
| `_filter`               | Inga        | Inga        | Inga        |         |
| `_sort`                 | Inga        | Inga        | Inga        |         |
| `_score`                | Inga        | Inga        | Inga        |         |
| `_count`                | Ja       | Ja       | Ja       |         |
| `_summary`              | Delvis   | Delvis   | Delvis   | `_summary=count` stöds |
| `_include`              | Inga        | Ja       | Inga        |         |
| `_revinclude`           | Inga        | Ja       | Inga        | Inkluderade objekt är begränsade till 100. |
| `_contained`            | Inga        | Inga        | Inga        |         |
| `_elements`             | Inga        | Inga        | Inga        |         |

## <a name="persistence"></a>Bevarande

Microsoft FHIR-servern har en pluggad beständig modul (se [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

För närvarande inkluderar FHIR-serverns kod med öppen källkod en implementering för [Azure Cosmos DB](../cosmos-db/index-overview.md) och [SQL Database](https://azure.microsoft.com/services/sql-database/).

Cosmos DB är en globalt distribuerad multi-Model-databas (SQL API, MongoDB API osv.). Den har stöd för olika [konsekvens nivåer](../cosmos-db/consistency-levels.md). Standardmallen för distribution konfigurerar FHIR-servern med `Strong` konsekvens, men konsekvens policyn kan ändras (i allmänhet avslappnadt) på en begäran med hjälp av begär `x-ms-consistency-level` ande huvudet.

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

FHIR-servern använder [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) för åtkomst kontroll. Rollbaserade Access Control (RBAC) framtvingas, om `FhirServer:Security:Enabled` konfigurations parametern är inställd på `true` och alla begär Anden (utom `/metadata` ) till FHIR-servern måste ha `Authorization` begär ande huvud inställt på `Bearer <TOKEN>` . Token måste innehålla en eller flera roller som definieras i `roles` anspråket. En begäran kommer att tillåtas om token innehåller en roll som tillåter den angivna åtgärden på den angivna resursen.

För närvarande tillämpas de tillåtna åtgärderna för en specifik roll *globalt* på API: et.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du läst om de FHIR-funktioner som stöds i Azure API för FHIR. Distribuera sedan Azure-API: et för FHIR.
 
>[!div class="nextstepaction"]
>[Distribuera Azure API för FHIR](fhir-paas-portal-quickstart.md)
