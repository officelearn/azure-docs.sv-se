---
title: API-stöd i Azures statiska Web Apps med Azure Functions
description: Lär dig vilka API-funktioner Azure static Web Apps stöder
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 7f86ce9158b5b07b036c785c2973e8a5883ed686
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597144"
---
# <a name="api-support-in-azure-static-web-apps-preview-with-azure-functions"></a>API-stöd i för hands versionen av Azure statisk Web Apps med Azure Functions

Azures statiska Web Apps tillhandahåller Server lös API-slutpunkter via [Azure Functions](../azure-functions/functions-overview.md). Genom att utnyttja Azure Functions skalar API: er dynamiskt baserat på efter frågan och inkluderar följande funktioner:

- **Integrerad säkerhet** med direkt åtkomst till användarautentisering [och rollbaserad auktoriseringsdata](user-information.md) .
- **Sömlös routning** som gör _API_ -vägen tillgänglig för webbappen på ett säkert sätt utan att anpassade CORS-regler krävs.
- **Azure Functions** v3-kompatibel med Node. js 12.
- **Http-utlösare** och utgående bindningar.

## <a name="configuration"></a>Konfiguration

API-slutpunkter är tillgängliga för webbappen via _API_ -vägen. Även om den här vägen är fast har du kontroll över mappen där du hittar den associerade Azure Functions-appen. Du kan ändra den här platsen genom [att redigera arbets flödets yaml-fil](github-actions-workflow.md#build-and-deploy) som finns i mappen _. GitHub/arbets flöden_ för databasen.

## <a name="constraints"></a>Villkor

Azures statiska Web Apps tillhandahåller ett API via Azure Functions. Funktionerna i Azure Functions fokuserar på en bestämd uppsättning funktioner som gör att du kan skapa ett API för en webbapp och tillåta att webbappen ansluter till API: et på ett säkert sätt. Dessa funktioner följer vissa begränsningar, inklusive:

- API-vägens prefix måste vara _API_.
- Utlösare och bindningar är begränsade till [http](../azure-functions/functions-bindings-http-webhook.md).
  - Alla andra [Azure Functions-utlösare och bindningar](../azure-functions/functions-triggers-bindings.md#supported-bindings) förutom utgående bindningar är begränsade.
- Loggar är bara tillgängliga om du lägger till [Application Insights](../azure-functions/functions-monitoring.md) i din functions-app.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lägg till ett API](add-api.md)
