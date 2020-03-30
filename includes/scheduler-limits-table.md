---
title: ta med fil
description: ta med fil
services: scheduler
ms.service: scheduler
author: derek1ee
ms.topic: include
ms.date: 08/16/2016
ms.author: deli
ms.custom: include file
ms.openlocfilehash: eb13d889cb72911e2268b7538a74336befe3320b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75392427"
---
I följande tabell beskrivs var och en av de viktigaste kvoterna, begränsningarna, standardvärdena och begränsningarna i Azure Scheduler.

| Resurs | Begränsa beskrivning |
| -------- | ----------------- |
| **Jobbstorlek** | Den maximala jobbstorleken är 16 000. Om en PUT- eller PATCH-åtgärd resulterar i en jobbstorlek som är större än den här gränsen returneras en statuskod för 400 felaktiga begäranden. | 
| **Jobbsamlingar** | Det maximala antalet jobbsamlingar per Azure-prenumeration är 200 000. | 
| **Jobb per samling** | Som standard är det maximala antalet jobb fem jobb i en kostnadsfri jobbsamling och 50 jobb i en vanlig jobbsamling. Du kan ändra det maximala antalet jobb i en jobbsamling. Alla jobb i en jobbsamling är begränsade till värdet som angetts för jobbsamlingen. Om du försöker skapa fler jobb än den maximala jobbkvoten misslyckas begäran med statuskoden 409 Conflict. | 
| **Dags att starta tid** | Den maximala "tiden till starttid" är 18 månader. |
| **Intervall för återkommande** | Det maximala upprepningsintervallet är 18 månader. | 
| **Frekvens** | Som standard är den maximala frekvenskvoten en timme i en kostnadsfri jobbsamling och en minut i en vanlig jobbsamling. <p>Du kan göra den maximala frekvensen för en jobbsamling lägre än den maximala. Alla jobb i jobbsamlingen är begränsade till värdet som angetts för jobbsamlingen. Om du försöker skapa ett jobb med en högre frekvens än den maximala frekvensen för jobbsamlingen misslyckas begäran med en statuskod för 409 konflikter. | 
| **Kroppsstorlek** | Den maximala kroppsstorleken för en begäran är 8 192 tecken. |
| **Begär URL-storlek** | Den maximala storleken för en URL för begäran är 2 048 tecken. |
| **Antal rubriker** | Det maximala antalet huvuden är 50 rubriker. | 
| **Aggregerad rubrikstorlek** | Den maximala sammanlagda huvudstorleken är 4 096 tecken. |
| **Timeout** | Tidsgränsen för begäran är statisk, det vill de kan inte konfigureras. och är 60 sekunder för HTTP-åtgärder. För längre driftåtgärder följer du HTTP-asynkrona protokoll. Returnera till exempel ett 202-kort men fortsätta arbeta i bakgrunden. | 
| **Jobbhistorik** | Den maximala svarstexten som lagras i jobbhistoriken är 2 048 byte. |
| **Kvarhållning av jobbhistorik** | Jobbhistoriken sparas i upp till två månader eller upp till de senaste 1 000 avrättningarna. | 
| **Slutförd och felaktig jobbkvarhållning** | Slutförda och felaktiga jobb hålls i 60 dagar. |
||| 

