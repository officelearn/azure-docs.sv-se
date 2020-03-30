---
title: SDK:er för Azure-händelserutnät
description: Beskriver SDK:erna för Azure Event Grid. Dessa SDK:er tillhandahåller hantering, publicering och förbrukning.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/19/2019
ms.author: spelluru
ms.openlocfilehash: 7f05665f4bcc5449c1a81fa24582b333b0a944e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60822835"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>SDK:er för händelserutnät för hantering och publicering

Event Grid innehåller SDK:er som gör att du kan hantera dina resurser och publicera händelser programmässigt.

## <a name="management-sdks"></a>Hantering SDK

Med hanterings-SDK:erna kan du skapa, uppdatera och ta bort ämnen och prenumerationer på händelserutnät. För närvarande är följande SDK:er tillgängliga:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Kör](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>Dataplan SDK:er

Med dataplanets SDK:er kan du publicera händelser i ämnen genom att ta hand om autentisering, skapa händelsen och asynkront bokföra till den angivna slutpunkten. De gör det också möjligt för dig att konsumera första part händelser. För närvarande är följande SDK:er tillgängliga:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Kör](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Nästa steg

* Till exempel program, se [Exempelexempel för händelserutnätskod](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid).
* En introduktion till Händelserutnätet finns i [Vad är händelserutnät?](overview.md)
* Kommandon för händelserutnät i Azure CLI finns i [Azure CLI](/cli/azure/eventgrid).
* Kommandon för händelserutnät i PowerShell finns i [PowerShell](/powershell/module/az.eventgrid).
