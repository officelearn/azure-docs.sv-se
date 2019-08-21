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
ms.openlocfilehash: 557e34e8a6bddd36a92b0e212bda3609baa14407
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642167"
---
Du kan använda följande parameter typer för den Utlös ande blobben:

* `Stream`
* `TextReader`
* `string`
* `Byte[]`
* En POCO som är serialiserbar som JSON
* `ICloudBlob`<sup>1</sup>
* `CloudBlockBlob`<sup>1</sup>
* `CloudPageBlob`<sup>1</sup>
* `CloudAppendBlob`<sup>1</sup>

<sup>1</sup> kräver "Inout"- `direction` bindning i *Function. JSON* eller `FileAccess.ReadWrite` i C# ett klass bibliotek.

Om du försöker binda till en av lagrings-SDK-typerna och få ett fel meddelande kontrollerar du att du har en referens till [rätt Storage SDK-version](#azure-storage-sdk-version-in-functions-1x).

Bindning till `string`, `Byte[]`eller Poco rekommenderas endast om BLOB-storleken är liten, eftersom hela BLOB-innehållet läses in i minnet. I allmänhet är det bättre att använda en `Stream` eller `CloudBlockBlob` -typ. Mer information finns i [samtidighets-och minnes användning](#trigger---concurrency-and-memory-usage) längre fram i den här artikeln.