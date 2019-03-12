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
ms.openlocfilehash: b3788ede23a423bebf96661ea88b227bfb5fdf4c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553877"
---
I följande tabell beskrivs de större kvoter, gränser, standardinställningar och begränsningar i Azure Scheduler.

| Resurs | Beskrivning av gränsen |
| -------- | ----------------- |
| **Jobbstorlekar** | Den maximala jobbstorlekar är 16 000. Om en PUT- eller en korrigeringsåtgärd resulterar i en jobbstorlek som är större än den här gränsen, returneras statuskod 400 Felaktig begäran. | 
| **Jobbsamlingar** | Det maximala antalet jobbsamlingar per Azure-prenumeration är 200 000. | 
| **Jobb per samling** | Som standard är det maximala antalet jobb fem jobb i en fri jobbsamling och 50 jobb i en standard jobbsamling. <p>Du kan ändra det maximala antalet jobb i en jobbsamling. Alla jobb i en jobbsamling är begränsade till det värde som anges i jobbsamlingen. Om du försöker skapa fler jobb än de maximala jobb kvoten, misslyckas denna begäran med statuskod 409 konflikt. | 
| **Tid för starttid** | Det högsta antalet ”time till starttid” är 18 månader. |
| **Återkommande intervall** | Största antal upprepningar span är 18 månader. | 
| **Frekvens** | Som standard är maximala frekvens kvoten en timme i en fri jobbsamling och en minut i en standard jobbsamling. <p>Du kan göra den maximala frekvensen på en jobbsamling lägre än maximalt. Alla jobb i jobbsamlingen är begränsade till det värde som anges i jobbsamlingen. Om du försöker skapa ett jobb med en frekvens som är högre än den maximala frekvensen på jobbsamlingen, misslyckas denna begäran med statuskod 409 konflikt. | 
| **Textstorleken** | Den maximala textstorleken för en begäran är 8 192 tecken. |
| **Storleksförfrågningar med URL: en** | Den maximala storleken för en begärd URL är 2 048 tecken. |
| **Huvud-antal** | Maximal huvud-antalet är 50 rubriker. | 
| **Sammanställd huvudstorlek** | Den maximala sammanlagda huvudstorlek är 4 096 tecken. |
| **Timeout** | Timeout för begäran är statiska, det vill säga inte konfigureras. och är 60 sekunder för HTTP-åtgärder. För längre åtgärder, följer du de asynkrona HTTP-protokoll. Till exempel returnera 202 omedelbart men fortsätta arbeta i bakgrunden. | 
| **Jobbhistorik** | Maximal svarstexten lagras i jobbhistoriken är 2 048 byte. |
| **Kvarhållning av jobb** | Jobbhistorik sparas i upp till två månader eller upp till den senaste 1 000 körningar. | 
| **Kvarhållning av tillståndet Completed eller faulted jobb** | Tillståndet Completed eller faulted jobb hålls i 60 dagar. |
||| 

