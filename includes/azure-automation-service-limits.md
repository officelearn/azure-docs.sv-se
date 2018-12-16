---
title: ta med fil
description: ta med fil
services: automation
author: georgewallace
ms.service: automation
ms.topic: include
ms.date: 12/13/2018
ms.author: gwallace
ms.custom: include file
ms.openlocfilehash: f3ae2289112948dea7d2649c4fad6b1cafb3804b
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444400"
---
#### <a name="process-automation"></a>Processautomatisering

| Resurs | Övre gräns |Anteckningar|
| --- | --- |---|
| Maximalt antal nya jobb som ska skickas med 30 sekunders mellanrum per Automation-konto (icke schemalagda jobb) |100 |När den här gränsen träffar, misslyckas de efterföljande begärandena om att skapa ett jobb. Klienten får ett felsvar.|
| Maximalt antal samtidiga jobb som körs på samma instans av tid per Automation-konto (icke schemalagda jobb) |200 |När den här gränsen träffar, misslyckas de efterföljande begärandena om att skapa ett jobb. Klienten får ett felsvar.|
| Maxstorlek jobbmetadata för en rullande period på 30 dagar. | 10GB (cirka 4 miljoner jobb)|När den här gränsen träffar, misslyckas de efterföljande begärandena om att skapa ett jobb. |
| Maximalt antal moduler som kan importeras med 30 sekunders mellanrum per Automation-konto |5 ||
| Maxstorlek på en modul |100 MB ||
| Kör jobbtid - kostnadsfri nivå |500 minuter per prenumeration per kalendermånad ||
| Högsta mängd diskutrymme som tillåts per sandbox  **<sup>1</sup>** |1 GB |Gäller för Azure sandboxar|
| Högsta mängd minne som tilldelas en sandbox  **<sup>1</sup>** |400 MB |Gäller för Azure sandboxar|
| Maxantal nätverk sockets tillåts per sandbox  **<sup>1</sup>** |1000 |Gäller för Azure sandboxar|
| Maximala körtid som tillåts per runbook  **<sup>1</sup>** |3 timmar |Gäller för Azure sandboxar|
| Maxantal Automation-konton i en prenumeration |Obegränsat ||
|Maximalt antal samtidiga jobb som ska köras på en enda Hybrid Runbook Worker|50 ||
| Max Runbook-jobbet parametrar storlek   | 512 kb||
| Max Runbook-parametrar   | 50|Du kan skicka en JSON- eller XML-sträng till en parameter och parsa den med runbook om du har nått gränsen för 50 parametern|
| Maxstorlek för webhook-nyttolasten |  512 kb|
| Maximalt antal dagar som jobbdata sparas|30 dagar|
| Max PowerShell Workflow användartillståndets storlek |5 MB| Gäller för PowerShell Workflow-runbooks när kontrollpunkter arbetsflöde.|

**<sup>1</sup>**  en Sandbox-miljön är en delad miljö som kan användas av flera jobb, jobb med hjälp av samma sandlåda är bundna av resursbegränsningar av sandbox-miljön.

#### <a name="change-tracking-and-inventory"></a>Ändringsspårning och inventering

I följande tabell visar de spårade objekt gränserna per dator för ändringsspårning.

| **Resurs** | **Gränsen**| **Anteckningar** |
|---|---|---|
|Fil|500||
|Register|250||
|Windows-programvara|250|Omfattar inte programuppdateringar|
|Linux-paket|1250||
|Tjänster|250||
|Daemon|250||
