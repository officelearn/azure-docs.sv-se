---
title: Kombinera kolumner med exempel omvandling med hjälp av Azure Machine Learning Workbench
description: Referensdokument för transformeringen kombinera kolumner med exempel
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ROBOTS: NOINDEX
ms.openlocfilehash: c952882a0a12e288eaf4f5f1074a9a2e876a21d6
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986242"
---
# <a name="combine-columns-by-example-transformation"></a>Kombinera kolumner med exempel transformering

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)] 


Den här omvandlingen tillåter användare att lägga till en ny kolumn genom att kombinera värden från flera kolumner. Användaren kan ange en avgränsare eller innehåller exempel på kombinerade värden för att utföra den här transformeringen. När användaren innehåller exempel på kombination, transformeringen hanteras av samma **av exempel** motor som används i den **Härled kolumner med exempel** omvandla.

## <a name="how-to-perform-this-transformation"></a>Hur du utför den här omvandlingen

Följ dessa steg om du vill utföra den här transformeringen:
1. Markera två eller flera kolumner som du vill kombinera i en kolumn. 
2. Välj **kombinera kolumner med exempel** från den **omvandlar** menyn. Eller högerklicka på rubriken för någon av de markerade kolumnerna och välj **kombinera kolumner med exempel** på snabbmenyn. Omvandla redigeringsprogrammet öppnas och en ny kolumn läggs bredvid den högra mest markerade kolumnen. Den nya kolumnen innehåller de kombinerade värden åtskilda av en avgränsare. Valda kolumner kan identifieras av kryssrutorna på kolumnrubrikerna. Tillägg och borttagning av kolumner från markeringen kan göras med hjälp av kryssrutorna.
3. Du kan uppdatera det kombinerade värdet i den nya kolumnen. Det uppdaterade värdet används som exempel för att lära dig transformeringen.
4. Klicka på **OK** att acceptera omvandlingen.

### <a name="transform-editor-advanced-mode"></a>Omvandla Redigerare: Avancerat läge

Avancerat läge tillhandahåller en rikare upplevelse för att kombinera kolumner. 

Att välja **avgränsare** under **kombinera kolumner efter** gör att användaren anger strängar i den **avgränsare** textrutan. Fliken ut från den **avgränsare** textrutan för att förhandsgranska resultat i data gird. Tryck på **OK** utför transformering.

Att välja **exempel** under **kombinera kolumner efter** gör det möjligt för användarna att ange kombinerade exempelvärden. Dubbelklicka på raderna i rutnätet för att flytta upp en rad som ett exempel. Ange utdata som förväntas i textrutan mot den upphöjda raden. Fliken ut från den **avgränsare** textrutan för att förhandsgranska resultat i data gird. Tryck på **OK** utför transformering. 

Användare kan växla mellan den **standardläge** och **Avancerat läge** genom att klicka på länkarna i Redigeraren för att omvandla.

### <a name="transform-editor-send-feedback"></a>Omvandla Redigerare: skicka Feedback

När du klickar på den **skicka feedback** länken öppnas den **Feedback** dialogen med kommentarrutan innehåller exempel användaren har angetts. Användaren bör granska innehållet i kommentarrutan och ge mer information som hjälper oss att förstå problemet. Om användaren inte vill dela data med Microsoft användaren bör ta bort förifyllda exempeldata innan du klickar på den **skicka Feedback** knappen. 

### <a name="editing-existing-transformation"></a>Redigera befintliga omvandling

En användare kan redigera en befintlig **kombinera av Kolumnexempel** omvandla genom att välja **redigera** alternativ i steget omvandling. När du klickar på **redigera** öppnas redigeraren transformera i **standardläge**. Användaren kan ange den **Avancerat läge** genom att klicka på länken i rubriken. Alla exempel som angavs under skapandet av transformeringen som det visas.

## <a name="example-using-separators"></a>Exempel med avgränsare

Kommatecken följt av ett blanksteg används som avgränsare i det här exemplet för att kombinera den *gata*, *Stad*, *tillstånd*, och *ZIP* kolumner.

|Gata|Ort|Status|ZIP|Kolumn|
|:----|:----|:----|:----|:----|
|16011 N.E. 36th sätt|REDMOND|WA|98052|16011 N.E. 36th way, REDMOND, WA, 98052|
|16021 N.E. 36th sätt|REDMOND|WA|98052|16021 N.E. 36th way, REDMOND, WA, 98052|
|16031 N.E. 36th sätt|REDMOND|WA|98052|16031 N.E. 36th way, REDMOND, WA, 98052|
|16041 N.E. 36th sätt|REDMOND|WA|98052|16041 N.E. 36th way, REDMOND, WA, 98052|
|16051 N.E. 36th sätt|REDMOND|WA|98052|16051 N.E. 36th way, REDMOND, WA, 98052|
|16061 N.E. 36th sätt|REDMOND|WA|98052|16061 N.E. 36th way, REDMOND, WA, 98052|
|3460 157th minimering NE|REDMOND|WA|98052|3460 157th minimering NE, REDMOND, WA, 98052|
|3350 157th para N.E.|REDMOND|WA|98052|3350 157th para N.E., REDMOND, WA, 98052|
|3240 157th minimering N.E.|REDMOND|WA|98052|3240 157th minimering N.E., REDMOND, WA, 98052|

## <a name="example-using-by-example"></a>Exempel på användning av efter exempel

Värdet i **fetstil** har angetts som ett exempel.

|Date|Månad|År|Timme|Minut|Sekund|Kombinerade kolumn|
|:----|:----|:----|:----|:----|:----|:----|
|13|Okt|2016|15|01|23|**13 – oktober-2016 15:01:23 (Stillahavstid)**|
|16|Okt|2016|16|22|33|16-okt-2016 15:01:33 (Stillahavstid)|
|17|Okt|2016|12|43|12|17 oktober 2016 15:01:12 (Stillahavstid)|
|12|Nov|2016|14|22|44|12 november 2016 15:01:44 (Stillahavstid)|
|23|Nov|2016|01|52|45|23-Nov-2016 15:01:45 (Stillahavstid)|
|16|Jan|2017|22|34|56|16-Jan-2016 15:01:56 (Stillahavstid)|
|23|Mar|2017|01|55|25|23 mars 2016 15:01:25 (Stillahavstid)|
|16|Apr|2017|11|34|36|16-Apr-2016 15:01:36 (Stillahavstid)|

