---
title: ta med fil
description: ta med fil
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 01/02/2019
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 1c6579776b86decb78c172578cbe55a66c05d78f
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54026561"
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

Flera delar begäranden kan göras via programmering (via C#), via ett REST-klient eller verktyg som [Postman](https://www.getpostman.com/). REST-klientverktyg kan ha olika supportnivåer för komplexa begäranden i flera delar. Kontrollera vilket verktyg som passar bäst för dina behov.

> [!IMPORTANT]
> Flera delar begäranden som görs till Azure Digital Twins Management API: erna har två delar:
> * Blobbmetadata (till exempel en tillhörande mimetyp) som har deklarerats av **Content-Type** och **Content-Disposition**
> * Blobbinnehåll bland annat Ostrukturerade innehållet i en fil som ska laddas upp
>
> Ingen av de två delarna som krävs för **KORRIGERA** begäranden. Båda värdena krävs för **POST** eller skapa-åtgärder.
