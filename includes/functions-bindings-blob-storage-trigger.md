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
ms.openlocfilehash: fa3888d28c52625684676036def7e2920b77b5ca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "77202117"
---
Du kan använda följande parameter typer för den Utlös ande blobben:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* En POCO som är serialiserbar som JSON
* `ICloudBlob`<sup>81.1</sup>
* `CloudBlockBlob`<sup>81.1</sup>
* `CloudPageBlob`<sup>81.1</sup>
* `CloudAppendBlob`<sup>81.1</sup>

<sup>1</sup> kräver "Inout"-bindning `direction` i *function.jspå* eller `FileAccess.ReadWrite` i ett C#-klass bibliotek.

Om du försöker binda till en av lagrings-SDK-typerna och få ett fel meddelande kontrollerar du att du har en referens till [rätt Storage SDK-version](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

Bindning till `string` , `Byte[]` eller Poco rekommenderas endast om BLOB-storleken är liten, eftersom hela BLOB-innehållet läses in i minnet. I allmänhet är det bättre att använda en `Stream` eller- `CloudBlockBlob` typ. Mer information finns i [samtidighets-och minnes användning](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) längre fram i den här artikeln.