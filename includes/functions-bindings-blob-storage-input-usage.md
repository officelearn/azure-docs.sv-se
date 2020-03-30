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
ms.openlocfilehash: 512d05b245f1279a977ba61d5b4c4904fc0f6bf2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77202130"
---
Du kan använda följande parametertyper för blob-indatabindningen:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* `CloudBlobContainer`
* `CloudBlobDirectory`
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> Kräver "inout" bindning `direction` i *function.json* eller `FileAccess.ReadWrite` i ett C#-klassbibliotek.

Om du försöker binda till någon av Storage SDK-typerna och får ett felmeddelande kontrollerar du att du har en referens till [rätt Storage SDK-version](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

Bindning `string` `Byte[]` till eller rekommenderas endast om blob-storleken är liten, eftersom hela blob-innehållet läses in i minnet. Generellt är det att föredra `Stream` `CloudBlockBlob` att använda en eller typ. Mer information finns i [Samtidighet och minnesanvändning](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) tidigare i den här artikeln.
