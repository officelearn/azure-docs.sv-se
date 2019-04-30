---
title: ta med fil
description: ta med fil
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 01/11/2019
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: ac6b008597b6d6e557a0cc412c00c2202231bc3d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60921867"
---
> [!NOTE]
> Flera delar begäranden kräver vanligtvis tre uppgifter:
> * En **Content-Type** rubrik:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * En **Content-Disposition**:
>   * `form-data; name="metadata"`
> * Filinnehållet att ladda upp
>
> **Content-Type** och **Content-Disposition** varierar beroende på scenario för användning.

Flera delar begäranden kan göras via programmering (via C#), via ett REST-klient eller verktyg som [Postman](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#multi). REST-klientverktyg kan ha olika supportnivåer för komplexa begäranden i flera delar. Konfigurationsinställningar kan också variera något från verktyget till verktyget. Kontrollera vilket verktyg som passar bäst för dina behov.

> [!IMPORTANT]
> Flera delar begäranden som görs till Azure Digital Twins Management API: er vanligtvis består av två delar:
> * Blobbmetadata (till exempel en tillhörande mimetyp) som har deklarerats av **Content-Type** och/eller **Content-Disposition**
> * Blobbinnehåll bland annat Ostrukturerade innehållet i en fil som ska laddas upp
>
> Ingen av de två delarna som krävs för **KORRIGERA** begäranden. Båda värdena krävs för **POST** eller skapa-åtgärder.

Den [användandet Snabbstart källkoden](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) innehåller fullständig C# exempel visas hur du begär multipart mot Azure Digital Twins Management API: erna.