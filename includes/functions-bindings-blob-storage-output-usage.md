---
title: ta med fil
description: ta med fil
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 0c0ab0e62a5d951f0bc0e237f44cf55c5b8e16cc
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202104"
---
Du kan binda till följande typer för att skriva blobbar:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>2</sup>
* `CloudBlockBlob`<sup>2</sup>
* `CloudPageBlob`<sup>2</sup>
* `CloudAppendBlob`<sup>2</sup>

<sup>1</sup> kräver "i"-bindning `direction` i *Function. JSON* eller `FileAccess.Read` i C# ett klass bibliotek. Du kan dock använda behållarobjektet som körningen tillhandahåller för att utföra Skriv åtgärder, till exempel att ladda upp blobar till behållaren.

<sup>2</sup> kräver "Inout"-bindningen `direction` i *Function. JSON* eller `FileAccess.ReadWrite` C# i ett klass bibliotek.

Om du försöker binda till en av lagrings-SDK-typerna och få ett fel meddelande kontrollerar du att du har en referens till [rätt Storage SDK-version](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

I async Functions använder du returvärdet eller `IAsyncCollector` i stället för en `out`-parameter.

Bindning till `string` eller `Byte[]` rekommenderas endast om BLOB-storleken är liten, eftersom hela BLOB-innehållet läses in i minnet. I allmänhet är det bättre att använda en `Stream` eller `CloudBlockBlob` typ. Mer information finns i [samtidighets-och minnes användning](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) tidigare i den här artikeln.
