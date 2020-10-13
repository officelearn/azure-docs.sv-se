---
title: inkludera fil
description: inkludera fil
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 0c0ab0e62a5d951f0bc0e237f44cf55c5b8e16cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "77202104"
---
Du kan binda till följande typer för att skriva blobbar:

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>81.1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>11.2</sup>
* `CloudBlockBlob`<sup>11.2</sup>
* `CloudPageBlob`<sup>11.2</sup>
* `CloudAppendBlob`<sup>11.2</sup>

<sup>1</sup> kräver "i"-bindning `direction` i *function.jspå* eller `FileAccess.Read` i ett C#-klass bibliotek. Du kan dock använda behållarobjektet som körningen tillhandahåller för att utföra Skriv åtgärder, till exempel att ladda upp blobar till behållaren.

<sup>2</sup> kräver "Inout"-bindning `direction` i *function.jspå* eller `FileAccess.ReadWrite` i ett C#-klass bibliotek.

Om du försöker binda till en av lagrings-SDK-typerna och få ett fel meddelande kontrollerar du att du har en referens till [rätt Storage SDK-version](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

I async Functions använder du returvärdet eller `IAsyncCollector` i stället för en `out` parameter.

Bindning till `string` eller `Byte[]` rekommenderas endast om BLOB-storleken är liten, eftersom hela BLOB-innehållet läses in i minnet. I allmänhet är det bättre att använda en `Stream` eller- `CloudBlockBlob` typ. Mer information finns i [samtidighets-och minnes användning](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) tidigare i den här artikeln.
