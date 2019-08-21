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
ms.openlocfilehash: 414381d42c52e7489fbd6aaacf0f079cdd978ebe
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642102"
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

<sup>1</sup> kräver "i"- `direction` bindning i *Function. JSON* eller `FileAccess.Read` i C# ett klass bibliotek. Du kan dock använda behållarobjektet som körningen tillhandahåller för att utföra Skriv åtgärder, till exempel att ladda upp blobar till behållaren.

<sup>2</sup> kräver "Inout"- `direction` bindning i *Function. JSON* eller `FileAccess.ReadWrite` i C# ett klass bibliotek.

Om du försöker binda till en av lagrings-SDK-typerna och få ett fel meddelande kontrollerar du att du har en referens till [rätt Storage SDK-version](#azure-storage-sdk-version-in-functions-1x).

I async-funktioner, använder du det returnera värdet eller `IAsyncCollector` i stället för en `out` parametern.

Bindning till `string` eller `Byte[]` rekommenderas endast om BLOB-storleken är liten, eftersom hela BLOB-innehållet läses in i minnet. I allmänhet är det bättre att använda en `Stream` eller `CloudBlockBlob` -typ. Mer information finns i [samtidighets-och minnes användning](#trigger---concurrency-and-memory-usage) tidigare i den här artikeln.