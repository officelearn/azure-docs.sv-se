---
title: ta med fil
description: ta med fil
services: automation
author: mgoedtel
ms.service: automation
ms.topic: include
ms.date: 10/14/2020
ms.author: magoedte
ms.custom: include file
ms.openlocfilehash: f01ca03167c7088c344f72e9943c103d9611bbc6
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2020
ms.locfileid: "92082242"
---
#### <a name="process-automation"></a>Processautomatisering

| Resurs | Gräns |Kommentarer|
| --- | --- |---|
| Maximalt antal nya jobb som kan skickas var 30 sekund per Azure Automation konto (ej schemalagda jobb) |100 |När den här gränsen uppnås kan efterföljande begär Anden om att skapa ett jobb inte utföras. Klienten får ett fel svar.|
| Maximalt antal samtidiga jobb som körs på samma tids instans per Automation-konto (ej schemalagda jobb) |200 |När den här gränsen uppnås kan efterföljande begär Anden om att skapa ett jobb inte utföras. Klienten får ett fel svar.|
| Maximal lagrings storlek för jobbets metadata för en rullande period på 30 dagar | 10 GB (cirka 4 000 000 jobb)|När den här gränsen uppnås kan efterföljande begär Anden om att skapa ett jobb inte utföras. |
| Maximal jobb Ströms gräns|1 MiB|En enda data ström får inte vara större än 1 MB.|
| Maximalt antal moduler som kan importeras var 30 sekund per Automation-konto |5 ||
| Maximal storlek för en modul |100 MB ||
| Jobb körnings tid, kostnads fri nivå |500 minuter per prenumeration per kalender månad ||
| Högsta mängd disk utrymme som tillåts per begränsat läge<sup>1</sup> |1 GB |Gäller endast för Azures sand boxar.|
| Maximal mängd minne som tilldelats till en begränsat läge<sup>1</sup> |400 MB |Gäller endast för Azures sand boxar.|
| Maximalt antal tillåtna nätverks platser per begränsat läge<sup>1</sup> |1 000 |Gäller endast för Azures sand boxar.|
| Högsta tillåtna körnings tid per Runbook<sup>1</sup> |3 timmar |Gäller endast för Azures sand boxar.|
| Maximalt antal Automation-konton i en prenumeration |Obegränsad ||
| Maximalt antal Hybrid Worker grupper per Automation-konto|4 000||
|Maximalt antal samtidiga jobb som kan köras på en enskild Hybrid Runbook Worker|50 ||
| Maximal parameter storlek för Runbook-jobb   | 512 kB||
| Maximalt antal Runbook-parametrar   | 50|Om du når 50-parameter gränsen kan du skicka en JSON-eller XML-sträng till en parameter och parsa den med runbooken.|
| Maximal nytto Last storlek för webhook |  512 kB|
| Maximalt antal dagar som jobb data kvarhålls|30 dagar|
| Maximal status storlek för PowerShell Workflow |5 MB| Gäller för PowerShell Workflow-Runbooks vid kontroll av arbets flöde.|

<sup>1</sup> En sandbox är en delad miljö som kan användas av flera jobb. Jobb som använder samma Sandbox är kopplade till resurs begränsningarna i sand boxen.

#### <a name="change-tracking-and-inventory"></a>Ändringsspårning och inventering

I följande tabell visas gränserna för spårade objekt per dator för ändrings spårning.

| **Resurs** | **Gräns**| **Kommentarer** |
|---|---|---|
|Fil|500||
|Register|250||
|Windows-programvara|250|Innehåller inte program uppdateringar.|
|Linux-paket|1 250||
|Tjänster|250||
|Program|250||

#### <a name="update-management"></a>Uppdateringshantering

I följande tabell visas gränserna för Uppdateringshantering.

| **Resurs** | **Gräns**| **Kommentarer** |
|---|---|---|
|Antal datorer per uppdaterings distribution|500||