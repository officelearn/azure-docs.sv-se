---
title: ta med fil
description: ta med fil
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 10/02/2019
ms.custom: include file
ms.openlocfilehash: f67f271ee02e38e168ddfaa84ed1309321cf8702
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949164"
---
> [!NOTE]
> Multipart-begäranden kräver vanligt vis tre delar:
> * Ett rubrik för **innehålls typ** :
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * En **innehålls-disposition**:
>   * `form-data; name="metadata"`
> * Fil innehållet att ladda upp
>
> **Content-Type** och **Content-disposition** varierar beroende på användnings scenario.

Multipart-begäranden kan göras via programmering (via C#), via en rest-klient eller verktyg som [Postman](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#make-a-multipart-post-request). REST-klient verktyg kan ha olika stöd nivåer för komplexa multipart-begäranden. Konfigurations inställningarna kan också variera något från verktyg till verktyg. Kontrol lera vilket verktyg som passar bäst för dina behov.

> [!IMPORTANT]
> Multipart-begäranden som görs till Azure Digitals dubbla hanterings-API: er har vanligt vis två delar:
> * BLOB-metadata (t. ex. en associerad MIME-typ) som är deklarerad av **innehålls typ** och/eller **innehålls-disposition**
> * BLOB-innehåll som inkluderar ostrukturerat innehåll i en fil som ska överföras
>
> Ingen av de två delarna krävs för **korrigerings** begär Anden. Båda krävs för **post** -eller skapande åtgärder.

Käll koden för att använda [snabb starts guiden](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) innehåller fullständiga C# exempel som demonstrerar hur du gör multipart-begäranden mot Azure Digitals hanterings-API: er.