---
title: ta med fil
description: ta med fil
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 02/07/2020
ms.custom: include file
ms.openlocfilehash: 0e7cb7e4aaa9862a2b4af51593c29793ea54dd14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77111226"
---
> [!NOTE]
> Flerdelade begäranden kräver vanligtvis tre delar:
> * En **rubrik av innehållstyp:**
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * En **innehållsdisposition:**
>   * `form-data; name="metadata"`
> * Filinnehållet som ska laddas upp
>
> **Innehållstyp** och **innehållsdisposition** varierar beroende på användningsscenario.

Flerdelade begäranden kan göras programmässigt (via C#), via en REST-klient eller ett verktyg som [Postman](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#make-a-multipart-post-request). REST-klientverktyg kan ha olika stödnivåer för komplexa flerdelade begäranden. Konfigurationsinställningarna kan också variera något från verktyg till verktyg. Kontrollera vilket verktyg som passar bäst för dina behov.

> [!IMPORTANT]
> Flerdelade begäranden som görs till Azure Digital Twins Management API:er har vanligtvis två delar:
> * Blobmetadata (till exempel en associerad MIME-typ) som deklareras av **Content-Type** och/eller **Content-Disposition**
> * Blob-innehåll som innehåller det ostrukturerade innehållet i en fil som ska laddas upp
>
> Ingen av de två delarna krävs för **PATCH-begäranden.** Båda krävs för **POST** eller skapa åtgärder.

[Quickstart-källkoden för beläggning](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) innehåller fullständiga C#-exempel som visar hur du gör flerdelade begäranden mot Azure Digital Twins Management API:er.