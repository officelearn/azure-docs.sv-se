---
title: ta med fil
description: ta med fil
services: functions
author: craigshoemaker
ms.service: functions
ms.topic: include
ms.date: 09/25/2018
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: fc5b43dcdee394fea023124171fb42c1a18224dc
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64733277"
---
Tillägget paket gör alla bindningar som publicerats av Azure Functions-teamet tillgängligt via en inställning i den *host.json* fil. För lokal utveckling, se till att du har den senaste versionen av [Azure Functions Core Tools](../articles/azure-functions/functions-run-local.md#install-the-azure-functions-core-tools).

För att använda tillägget paket måste du uppdatera den *host.json* filen för att inkludera följande post för `extensionBundle`:

```json
{
    "version": "2.0",
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    }
}
```

- Den `id` egenskapen refererar till namnområdet för Microsoft Azure Functions tillägget paket.
- Den `version` refererar till versionen av paketet.

Paketera versioner ökningen som paket i paketet ändras. Huvudversion dataändringar görs bara när paket i bunten flytta en högre version. Den `version` egenskapen använder den [notation för intervall för att ange versionsintervall](https://docs.microsoft.com/nuget/reference/package-versioning#version-ranges-and-wildcards). Functions-körning hämtar alltid den högsta tillåtna version av definieras av versionsintervall eller intervall.

När du refererar till tillägget-paket i ditt projekt är alla standard-bindningar tillgängliga för dina funktioner. Bindningarna som är tillgängliga i den [tillägget paket](https://github.com/Azure/azure-functions-extension-bundles/blob/master/src/Microsoft.Azure.Functions.ExtensionBundle/extensions.json) är:

|Paket  |Version  |
|---------|---------|
|Microsoft.Azure.WebJobs.Extensions.CosmosDB|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.DurableTask|1.8.0|
|Microsoft.Azure.WebJobs.Extensions.EventGrid|2.0.0|
|Microsoft.Azure.WebJobs.Extensions.EventHubs|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SendGrid|3.0.0|
|Microsoft.Azure.WebJobs.Extensions.ServiceBus|3.0.3|
|Microsoft.Azure.WebJobs.Extensions.SignalRService|1.0.0|
|Microsoft.Azure.WebJobs.Extensions.Storage|3.0.4|
|Microsoft.Azure.WebJobs.Extensions.Twilio|3.0.0|