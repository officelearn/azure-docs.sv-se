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
ms.openlocfilehash: d700dfcf5a7b6e9ada2a755335689ffa571e4c3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334912"
---
#### <a name="process-automation"></a>Processautomatisering

| Resurs | Gräns |Anteckningar|
| --- | --- |---|
| Maximalt antal nya jobb som kan skickas var 30:e sekund per Azure Automation-konto (icke-schemalagda jobb) |100 |När den här gränsen har uppnåtts misslyckas efterföljande begäranden om att skapa ett jobb. Klienten får ett felmeddelande.|
| Maximalt antal samtidiga jobb som körs samtidigt vid samma tidsinstans per Automation-konto (icke-schemalagda jobb) |200 |När den här gränsen har uppnåtts misslyckas efterföljande begäranden om att skapa ett jobb. Klienten får ett felmeddelande.|
| Maximal lagringsstorlek för jobbmetadata för en rullande 30 dagar | 10 GB (cirka 4 miljoner arbetstillfällen)|När den här gränsen har uppnåtts misslyckas efterföljande begäranden om att skapa ett jobb. |
| Maximal gräns för jobbström|1MB|En enda ström kan inte vara större än 1 MB.|
| Maximalt antal moduler som kan importeras var 30:e sekund per Automation-konto |5 ||
| Maximal storlek på en modul |100 MB ||
| Speltid, Kostnadsfri nivå |500 minuter per prenumeration per kalendermånad ||
| Maximalt antal tillåtna diskutrymme per sandlåda<sup>1</sup> |1 GB |Gäller endast för Azure-sandlådor.|
| Maximal mängd minne som ges till en sandlåda<sup>1</sup> |400 MB |Gäller endast för Azure-sandlådor.|
| Maximalt antal nätverksuttag som tillåts per sandlåda<sup>1</sup> |1,000 |Gäller endast för Azure-sandlådor.|
| Maximal körning tillåts per runbook<sup>1</sup> |3 timmar |Gäller endast för Azure-sandlådor.|
| Maximalt antal Automation-konton i en prenumeration |Obegränsad ||
| Maximalt antal hybridarbetsgrupper per automationskonto|4 000||
|Maximalt antal samtidiga jobb som kan köras på en enda Hybrid Runbook Worker|50 ||
| Parameterstorlek för maximalt runbookjobb   | 512 kilobit||
| Maximala runbook-parametrar   | 50|Om du når gränsen på 50 parametrar kan du skicka en JSON- eller XML-sträng till en parameter och tolka den med runbooken.|
| Maximal webhook nyttolaststorlek |  512 kilobit|
| Maximala dagar som jobbdata behålls|30 dagar|
| Maximal maximal powershell-arbetsflödestillståndsstorlek |5 MB| Gäller powershell-arbetsflödeskörningar när arbetsflödet för kontrollpunkter ska kontrolleras.|

<sup>1.</sup> En sandlåda är en delad miljö som kan användas av flera jobb. Jobb som använder samma sandlåda är bundna av sandlådans resursbegränsningar.

#### <a name="change-tracking-and-inventory"></a>Ändringsspårning och inventering

I följande tabell visas de spårade artikelgränserna per dator för ändringsspårning.

| **Resurs** | **Gräns**| **Obs!** |
|---|---|---|
|Fil|500||
|Register|250||
|Windows-programvara|250|Innehåller inte programuppdateringar.|
|Linux-paket|1,250||
|Tjänster|250||
|Daemon|250||

#### <a name="update-management"></a>Uppdateringshantering

I följande tabell visas gränserna för uppdateringshantering.

| **Resurs** | **Gräns**| **Obs!** |
|---|---|---|
|Antal datorer per uppdateringsdistribution|1000||