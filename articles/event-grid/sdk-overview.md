---
title: Azure händelse rutnätet SDK
description: 'Beskriver SDK: erna för Azure händelse rutnätet. Dessa SDK: er ger hantering, publicering och användning.'
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 05/04/2018
ms.author: tomfitz
ms.openlocfilehash: d9bb4b3b161060f20fca34760872a24cbfcabf30
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Händelsen rutnätet SDK-verktyg för hantering och publicering

Händelsen rutnätet innehåller SDK-verktyg som hjälper dig att hantera resurser och publicera händelser programmässigt.

## <a name="management-sdks"></a>Management-SDK

Management SDK kan du skapa, uppdatera och ta bort händelsen rutnätet ämnen och prenumerationer. Finns för närvarande följande SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure.eventgrid-2018-01-01/azure-mgmt-eventgrid)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="data-plane-sdks"></a>Data plan SDK

Dataplan SDK kan du skicka händelser till avsnitt som tar hand om autentisering, som utgör händelsen och skicka asynkront till den angivna slutpunkten. De kan du också använda den första part händelser. Finns för närvarande följande SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Go](https://github.com/Azure/azure-sdk-for-go)
* [Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-eventgrid)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Nästa steg

* En introduktion till händelse rutnätet finns [vad är händelsen rutnätet?](overview.md)
* Händelsen rutnätet kommandon i Azure CLI, se [Azure CLI](/cli/azure/eventgrid).
* Händelsen rutnätet kommandon i PowerShell, se [PowerShell](/powershell/module/azurerm.eventgrid).