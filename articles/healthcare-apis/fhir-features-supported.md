---
title: FHIR-funktioner som stöds i Azure – Azure API för FHIR
description: Den här artikeln beskriver vilka funktioner i FHIR-specifikationen som implementeras i Azure API för FHIR
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: a2851272d15ed2ce2c7f940cc3e429ef9f8b4446
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824265"
---
# <a name="features"></a>Funktioner

Azure API: et för FHIR tillhandahåller en helt hanterad distribution av Microsoft FHIR-Server för Azure. Servern är en implementering av den [FHIR](https://hl7.org/fhir) standard. Det här dokumentet visar de viktigaste funktionerna i FHIR-Server.

## <a name="fhir-version"></a>FHIR-version

aktuell version: `3.0.1`

## <a name="rest-api"></a>REST-API

| API                            | Stöds | Kommentar |
|--------------------------------|-----------|---------|
| läsa                           | Ja       |         |
| vread                          | Ja       |         |
| update                         | Ja       |         |
| Uppdatera med optimistisk låsning | Ja       |         |
| Uppdatera (villkorlig)           | Nej        |         |
| Patch                          | Nej        |         |
| delete                         | Ja       |         |
| ta bort (villkorlig)           | Nej        |         |
| skapa                         | Ja       | Stöd för både POST/PUT |
| Skapa (villkorlig)           | Nej        |         |
| sök                         | Delvis   | Se nedan |
| Funktioner                   | Ja       |         |
| batch                          | Nej        |         |
| Transaktionen                    | Nej        |         |
| Historik                        | Ja       |         |
| Växling                         | Delvis   | `self` och `next` stöds |
| en mellanhand                 | Nej        |         |

## <a name="search"></a>Search

Alla typer av search stöds. Kedjat parametrar och omvänd chaining är *inte* stöds.

| Parametern söktyp | Stöds | Kommentar |
|-----------------------|-----------|---------|
| Tal                | Ja       |         |
| Datum/DateTime         | Ja       |         |
| String                | Ja       |         |
| Token                 | Ja       |         |
| Referens             | Ja       |         |
| Sammansatta             | Ja       |         |
| Kvantitet              | Ja       | Problemet [#103](https://github.com/Microsoft/fhir-server/issues/103) |
| URI                   | Ja       |         |


| Modifierare             | Stöds | Kommentar |
|-----------------------|-----------|---------|
|`:missing`             | Ja       |         |
|`:exact`               | Ja       |         |
|`:contains`            | Ja       |         |
|`:text`                | Ja       |         |
|`:in` (token)          | Nej        |         |
|`:below` (token)       | Nej        |         |
|`:above` (token)       | Nej        |         |
|`:not-in` (token)      | Nej        |         |
|`:[type]` (referens)  | Nej        |         |
|`:below` (uri)         | Ja       |         |
|`:above` (uri)         | Nej        | Issue [#158](https://github.com/Microsoft/fhir-server/issues/158) |

| Vanliga sökparameter | Stöds | Kommentar |
|-------------------------| ----------|---------|
| `_id`                   | Ja       |         |
| `_lastUpdated`          | Ja       |         |
| `_tag`                  | Ja       |         |
| `_profile`              | Ja       |         |
| `_security`             | Ja       |         |
| `_text`                 | Nej        |         |
| `_content`              | Nej        |         |
| `_list`                 | Nej        |         |
| `_has`                  | Nej        |         |
| `_type`                 | Ja       |         |
| `_query`                | Nej        |         |

| Sök åtgärder       | Stöds | Kommentar |
|-------------------------|-----------|---------|
| `_filter`               | Nej        |         |
| `_sort`                 | Nej        |         |
| `_score`                | Nej        |         |
| `_count`                | Ja       |         |
| `_summary`              | Delvis   | `_summary=count` Det finns stöd för |
| `_include`              | Nej        |         |
| `_revinclude`           | Nej        |         |
| `_contained`            | Nej        |         |
| `_elements`             | Nej        |         |

## <a name="persistence"></a>Persistence

Microsoft FHIR Server har en modulär persistence-modul (se [ `Microsoft.Health.Fhir.Core.Features.Persistence` ](https://github.com/Microsoft/fhir-server/src/Microsoft.Health.Fhir.Core/Features/Persistence)).

För närvarande öppen källkod FHIR serverkoden innehåller en implementering för [Azure Cosmos DB](../cosmos-db/index-overview.md).

Cosmos DB är en globalt distribuerad Multi-modell (SQL-API, API för MongoDB osv.)-databas. Den stöder olika [konsekvensnivåer](../cosmos-db/consistency-levels.md). Standardmall för distribution konfigurerar FHIR-servern med `Strong` konsekvens, men konsekvens principen kan ändras (Allmänt avslappnade) på en begäran av begäran med hjälp av den `x-ms-consistency-level` huvudet i begäran.

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

FHIR-servern använder [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) för åtkomstkontroll. Mer specifikt rollbaserad åtkomstkontroll (RBAC) tillämpas om den `FhirServer:Security:Enabled` konfigurationsparameter anges till `true`, och alla förfrågningar (utom `/metadata`) till FHIR-servern måste ha `Authorization` begäran rubrikuppsättning till `Bearer <TOKEN>`. Token måste innehålla en eller flera roller som definierats i den `roles` anspråk. En begäran tillåts om token innehåller en roll som tillåter den angivna åtgärden på den angivna resursen.

För närvarande tillåtna åtgärder för en specifik roll som ska vidtas *globalt* på API: et.

## <a name="next-steps"></a>Nästa steg

I den här artikeln får har du läst om de stödda FHIR-funktionerna i Azure API för FHIR. Distribuera därefter en FHIR-API i Azure.
 
>[!div class="nextstepaction"]
>[Distribuera öppen källkod FHIR-server](fhir-oss-powershell-quickstart.md)