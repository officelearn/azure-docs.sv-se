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
ms.openlocfilehash: fa3888d28c52625684676036def7e2920b77b5ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77202117"
---
Du kan använda följande parametertyper för den utlösande blobben:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* En POCO serializable som JSON
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> Kräver "inout" bindning `direction` i *function.json* eller `FileAccess.ReadWrite` i ett C#-klassbibliotek.

Om du försöker binda till någon av Storage SDK-typerna och får ett felmeddelande kontrollerar du att du har en referens till [rätt Storage SDK-version](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x).

Bindning `string` `Byte[]`till , eller POCO rekommenderas endast om blob-storleken är liten, eftersom hela blob-innehållet läses in i minnet. Generellt är det att föredra `Stream` `CloudBlockBlob` att använda en eller typ. Mer information finns i [Samtidighet och minnesanvändning](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage) senare i den här artikeln.