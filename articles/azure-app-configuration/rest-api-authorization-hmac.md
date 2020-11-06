---
title: Azure App konfiguration REST API-HMAC-auktorisering
description: Använd HMAC för auktorisering mot Azure App konfiguration med hjälp av REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 8d1f8a17f51711cc5c10567797e1224f96eb7630
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424396"
---
# <a name="hmac-authorization---rest-api-reference"></a>HMAC-auktorisering-REST API referens

När HMAC-autentisering används, hamnar åtgärder i en av två kategorier, läsa eller skriva. Läs-och skriv åtkomst nycklar ger behörighet att anropa alla åtgärder. Skrivskyddade åtkomst nycklar ger behörighet att bara anropa Läs åtgärder. Om en åtkomst nyckel är skrivskyddad eller om Skriv skyddet bestäms av `readOnly` egenskapen. Om du försöker göra en skrivbegäran med en skrivskyddad åtkomst nyckel kommer begäran att bli obehörig.

## <a name="obtaining-access-keys"></a>Hämta åtkomst nycklar

Specifikationen som beskriver åtkomst nycklar och det API som används för att hämta dem beskrivs i den Azure App konfigurations resurs leverantörs specifikationen [här](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/appconfiguration/resource-manager/Microsoft.AppConfiguration/stable/2019-10-01/appconfiguration.json). Åtkomst nycklar hämtas via åtgärden "ConfigurationStores_ListKeys".

## <a name="errors"></a>Fel

```http
HTTP/1.1 403 Forbidden
```

**Orsak:** Åtkomst nyckeln som används för att autentisera begäran ger inte de behörigheter som krävs för att utföra den begärda åtgärden.
**Lösning:** Hämta en åtkomst nyckel som ger behörighet att utföra den begärda åtgärden och Använd den för att autentisera begäran.
