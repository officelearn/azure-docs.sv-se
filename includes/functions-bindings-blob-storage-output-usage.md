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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
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

<sup>1</sup> Kräver "in" bindning `direction` i *function.json* eller `FileAccess.Read` i ett C#-klassbibliotek. Du kan dock använda behållarobjektet som körningen ger för att göra skrivåtgärder, till exempel överföra blobbar till behållaren.

<sup>2</sup> Kräver "inout" bindning `direction` i *function.json* eller `FileAccess.ReadWrite` i ett C#-klassbibliotek.

Om du försöker binda till någon av Storage SDK-typerna och får ett felmeddelande kontrollerar du att du har en referens till [rätt Storage SDK-version](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

I async-funktioner använder du `IAsyncCollector` returvärdet `out` eller i stället för en parameter.

Bindning `string` `Byte[]` till eller rekommenderas endast om blob-storleken är liten, eftersom hela blob-innehållet läses in i minnet. Generellt är det att föredra `Stream` `CloudBlockBlob` att använda en eller typ. Mer information finns i [Samtidighet och minnesanvändning](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) tidigare i den här artikeln.
