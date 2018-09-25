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
ms.openlocfilehash: be52bf289898dfcd3ee669f12329669f6fabd356
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006524"
---
I följande tabell beskrivs de större kvoter, gränser, standardinställningar och begränsningar i Azure Scheduler.

| Resurs | Beskrivning av gränsen |
| -------- | ----------------- |
| **Jobbstorlekar** | Maximal jobbstorlekar är 16 kB. Om en PUT- eller en korrigeringsåtgärd resulterar i en jobbstorlek som är större än den här gränsen, returneras statuskod 400 Felaktig begäran. | 
| **Jobbsamlingar** | Det maximala antalet jobbsamlingar per Azure-prenumeration är 200 000. | 
| **Jobb per samling** | Som standard är det maximala antalet jobb fem jobb i en fri jobbsamling och 50 jobb i en standard jobbsamling. <p>Du kan ändra det maximala antalet jobb i en jobbsamling. Alla jobb i en jobbsamling är begränsade till det värde som anges i jobbsamlingen. Om du försöker skapa fler jobb än de maximala jobb kvoten, misslyckas denna begäran med statuskod 409 konflikt. | 
| **Tid för starttid** | Det högsta antalet ”time till starttid” är 18 månader. |
| **Återkommande intervall** | Största antal upprepningar span är 18 månader. | 
| **Frekvens** | Som standard är maximala frekvens kvoten en timme i en fri jobbsamling och en minut i en standard jobbsamling. <p>Du kan göra den maximala frekvensen på en jobbsamling lägre än maximalt. Alla jobb i jobbsamlingen är begränsade till det värde som anges i jobbsamlingen. Om du försöker skapa ett jobb med en frekvens som är högre än den maximala frekvensen på jobbsamlingen, misslyckas denna begäran med statuskod 409 konflikt. | 
| **Textstorleken** | Den maximala textstorleken för en begäran är 8192 tecken. |
| **Storleksförfrågningar med URL: en** | Den maximala storleken för en begärd URL är 2048 tecken. |
| **Huvud-antal** | Maximal huvud-antalet är 50 rubriker. | 
| **Sammanställd huvudstorlek** | Den maximala sammanlagda huvudstorlek är 4096 tecken. |
| **Timeout** | Timeout för begäran är statiska (inte kan konfigureras) och är 60 sekunder för HTTP-åtgärder. För längre åtgärder, följer du de asynkrona HTTP-protokoll. Till exempel returnera 202 omedelbart men fortsätta arbeta i bakgrunden. | 
| **Jobbhistorik** | Maximal svarstexten lagras i jobbhistoriken är 2 048 byte. |
| **Kvarhållning av jobb** | Jobbhistorik sparas i upp till 2 månader eller upp till den senaste 1 000 körningar. | 
| **Kvarhållning av tillståndet Completed eller faulted jobb** | Tillståndet Completed eller faulted jobb hålls i 60 dagar. |
||| 

