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
ms.openlocfilehash: 61f82771e53ac9bb594484b29bb109a03cee674b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554200"
---
#### <a name="process-automation"></a>Processautomatisering

| Resurs | Övre gräns |Anteckningar|
| --- | --- |---|
| Maximalt antal nya jobb som ska skickas med 30 sekunders mellanrum per Azure Automation-konto (nonscheduled jobb) |100 |När den här gränsen har nåtts misslyckas efterföljande förfrågningar för att skapa ett jobb. Klienten får ett felsvar.|
| Maximalt antal samtidiga jobb som körs på samma instans av tid per Automation-konto (nonscheduled jobb) |200 |När den här gränsen har nåtts misslyckas efterföljande förfrågningar för att skapa ett jobb. Klienten får ett felsvar.|
| Lagringsstorleken för jobbmetadata för en rullande period på 30 dagar | 10 GB (cirka 4 miljoner jobb)|När den här gränsen har nåtts misslyckas efterföljande förfrågningar för att skapa ett jobb. |
| Maximalt antal moduler som kan importeras med 30 sekunders mellanrum per Automation-konto |5 ||
| Maximal storlek på en modul |100 MB ||
| Uppgiftskörtid, kostnadsfria nivån |500 minuter per prenumeration per kalendermånad ||
| Högsta mängd diskutrymme som tillåts per sandbox<sup>1</sup> |1 GB |Gäller för Azure sandboxar.|
| Maximal mängd minne som tilldelas en sandbox<sup>1</sup> |400 MB |Gäller för Azure sandboxar.|
| Maximalt antal nätverk sockets tillåts per sandbox<sup>1</sup> |1,000 |Gäller för Azure sandboxar.|
| Maximala körtid som tillåts per runbook<sup>1</sup> |3 timmar |Gäller för Azure sandboxar.|
| Maximalt antal Automation-konton i en prenumeration |Obegränsad ||
|Maximalt antal samtidiga jobb som kan köras på en enda Hybrid Runbook Worker|50 ||
| Parameterstorleken för maximala runbook-jobb   | 512 kilobit||
| Maximal runbook-parametrar   | 50|Om du når gränsen på 50-parametern kan du skicka en JSON- eller XML-sträng till en parameter och parsa den till runbook.|
| Maximal webhook-nyttolasten |  512 kilobit|
| Maxantal dagar jobbdata behålls|30 dagar|
| Maximal storlek i PowerShell workflow tillstånd |5 MB| Gäller för PowerShell workflow-runbooks när kontrollpunkter arbetsflöde.|

<sup>1</sup>en Sandbox-miljön är en delad miljö som kan användas av flera jobb. Jobb som använder samma sandlåda bunden av resursbegränsningar av sandbox-miljön.

#### <a name="change-tracking-and-inventory"></a>Ändringsspårning och inventering

I följande tabell visas de spårade objekt gränserna per dator för att spåra ändringar.

| **Resurs** | **Gräns**| **Anteckningar** |
|---|---|---|
|Fil|500||
|Register|250||
|Windows-programvara|250|Inte innehåller programuppdateringar.|
|Linux-paket|1,250||
|Tjänster|250||
|Daemon|250||
