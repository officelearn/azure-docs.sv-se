---
title: 'Azure Event Grid SDK: er'
description: 'Beskriver SDK: er för Azure Event Grid. Dessa SDK: er ger hantering, publicering och konsumtion.'
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/19/2019
ms.author: spelluru
ms.openlocfilehash: 7f05665f4bcc5449c1a81fa24582b333b0a944e0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "60822835"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Event Grid SDK: er för hantering och publicering

Event Grid innehåller SDK: er som gör att du kan hantera dina resurser och publicera händelser via programmering.

## <a name="management-sdks"></a>Hanterings-SDK: er

Med SDK: er för hantering kan du skapa, uppdatera och ta bort event Grid-ämnen och-prenumerationer. För närvarande är följande SDK: er tillgängliga:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Kör](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>SDK: er för data plan

Med data Plans-SDK: erna kan du publicera händelser till ämnen genom att ta hand om autentisering, bilda händelsen och asynkront publicera till den angivna slut punkten. De gör det också möjligt att använda första part händelser. För närvarande är följande SDK: er tillgängliga:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Kör](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Nästa steg

* Exempel program finns i [Event Grid kod exempel](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid).
* En introduktion till Event Grid finns i [Vad är event Grid?](overview.md)
* Event Grid-kommandon i Azure CLI finns i [Azure CLI](/cli/azure/eventgrid).
* För Event Grid kommandon i PowerShell, se [PowerShell](/powershell/module/az.eventgrid).
