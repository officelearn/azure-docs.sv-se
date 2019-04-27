---
title: 'Azure Event Grid-SDK: er'
description: 'Beskriver de SDK: erna för Azure Event Grid. Dessa SDK: er ger-, publicering och användning.'
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: reference
ms.date: 01/19/2019
ms.author: spelluru
ms.openlocfilehash: 7f05665f4bcc5449c1a81fa24582b333b0a944e0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822835"
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Event Grid-SDK: er för hantering och publicering

Event Grid tillhandahåller SDK: er som hjälper dig att hantera dina resurser och publicerar händelser programmässigt.

## <a name="management-sdks"></a>Hantering av SDK: er

Management SDK: er kan du skapa, uppdatera och ta bort event grid-ämnen och prenumerationer. För närvarande finns följande SDK: erna:

* [NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://search.maven.org/#search%7Cga%7C1%7Cazure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>SDK: er för data-plan

Dataplanet med hjälp av SDK: er kan du skicka händelser till avsnitt genom att ta hand om autentisering, som utgör händelsen och asynkront skicka information till den angivna slutpunkten. De kan du också använda den första part händelser. För närvarande finns följande SDK: erna:

* [NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Nästa steg

* Till exempel program finns i [Event Grid-kodexempel](https://azure.microsoft.com/resources/samples/?sort=0&service=event-grid).
* En introduktion till Event Grid finns i [vad är Event Grid?](overview.md)
* Event Grid-kommandon i Azure CLI finns i [Azure CLI](/cli/azure/eventgrid).
* Event Grid-kommandon i PowerShell finns i [PowerShell](/powershell/module/az.eventgrid).
