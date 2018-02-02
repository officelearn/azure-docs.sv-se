---
title: "Azure händelse rutnätet SDK"
description: "Beskriver SDK: erna för Azure händelse rutnätet. Dessa SDK: er ger hantering, publicering och användning."
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: 9c56e4c3314090ad55017d5c681a0cfd7bf5722c
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="event-grid-sdks-for-management-and-publishing"></a>Händelsen rutnätet SDK-verktyg för hantering och publicering

Händelsen rutnätet innehåller SDK-verktyg som hjälper dig att hantera resurser och publicera händelser programmässigt.

## <a name="management-sdks"></a>Management-SDK

Management SDK kan du skapa, uppdatera och ta bort händelsen rutnätet ämnen och prenumerationer. Finns för närvarande följande SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid)
* [Gå](https://github.com/Azure/azure-sdk-for-go)
* [Node](https://www.npmjs.com/package/azure-arm-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-mgmt-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid)

## <a name="publish-sdks"></a>Publicera SDK

Publicera med hjälp av SDK: er kan du publicera händelser till avsnitt som tar hand om autentisering, som utgör händelsen och skicka asynkront till den angivna slutpunkten. Finns för närvarande följande SDK:

* [.NET](https://www.nuget.org/packages/Microsoft.Azure.EventGrid)
* [Gå](https://github.com/Azure/azure-sdk-for-go)
* [Node](https://www.npmjs.com/package/azure-eventgrid)
* [Python](https://pypi.python.org/pypi/azure-eventgrid)
* [Ruby](https://rubygems.org/gems/azure_event_grid)

## <a name="next-steps"></a>Nästa steg

* En introduktion till händelse rutnätet finns [vad är händelsen rutnätet?](overview.md)
* Händelsen rutnätet kommandon i Azure CLI, se [Azure CLI](/cli/azure/eventgrid).
* Händelsen rutnätet kommandon i PowerShell, se [PowerShell](/powershell/module/azurerm.eventgrid).