---
title: ta med fil
description: ta med fil
services: log-analytics
author: MGoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 05/16/2018
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: 66cd09df128d454973d008adf4ffc5dd1017a18f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38756046"
---
Följande begränsningar gäller för Log Analytics-resurser per prenumeration:

| Resurs | Standardgräns | Kommentarer
| --- | --- | --- |
| Antal lediga arbetsytor per prenumeration | 10 | Den här gränsen kan inte höjas. |
| Antal betalda arbetsytor per prenumeration | Gäller inte | Du är begränsad till antalet resurser i en resursgrupp och antalet resursgrupper per prenumeration | 

>[!NOTE]
>Från och med den 2 April 2018 används automatiskt nya arbetsytor i en ny prenumeration på *Per GB* prisavtal.  För befintliga prenumerationer som skapats före 2 April eller en prenumeration som är kopplad till en befintlig EA-registrering, kan du fortsätta att välja mellan tre prisnivåer för nya arbetsytor. 
>

Följande begränsningar gäller för varje Log Analytics-arbetsyta:

|  | Kostnadsfri | Standard | Premium | Fristående | OMS | Per GB |
| --- | --- | --- | --- | --- | --- |--- |
| Datavolym som samlas in per dag |500 MB<sup>1</sup> |Ingen |Ingen | Ingen | Ingen | Ingen
| Datakvarhållningstid |7 dagar |1 månad |12 månader | 1 månad <sup>2</sup> | 1 månad <sup>2</sup>| 1 månad <sup>2</sup>|

<sup>1</sup> När kunderna når den dagliga dataöverföringsgränsen på 500 MB stoppas dataanalysen och återupptas i början av nästa dag. En dag baseras på UTC.

<sup>2</sup> kan du öka kvarhållningsperioden för data för den fristående och OMS prissättningsplaner Per GB och 730 dagar.

| Kategori | Begränsningar | Kommentarer
| --- | --- | --- |
| API för datainsamling | Maximal storlek för ett enskilt inlägg är 30 MB<br>Maximal storlek för fältvärden är 32 kB | Dela större volymer i flera olika inlägg<br>Fält som är längre än 32 kB trunkeras. |
| Sök-API | 5 000 poster returneras för ej sammanräknade data<br>500 000 poster för sammanräknade data | Sammanräknade data är en sökning som innefattar kommandot `summarize`
 
