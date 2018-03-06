---
title: Kombinera kolumner genom exempel omvandling med Azure Machine Learning arbetsstationen
description: "Referensdokumentet för transformeringen kombinera kolumner efter exempel"
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.openlocfilehash: 469fefa48ea795a56b0e7525f99634c5149a780f
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2018
---
# <a name="combine-columns-by-example-transformation"></a>Kombinera kolumner med exempel omvandling
Den här omvandlingen tillåter användaren att lägga till en ny kolumn genom att kombinera värden från flera kolumner. Användaren kan ange en avgränsare eller ange kombinerade exempelvärden att utföra den här transformationen. När användaren ger exempel på kombination, omvandlingen hanteras av samma **av exempel** motor som används i den **härledd kolumn efter exempel** transformeringen.

## <a name="how-to-perform-this-transformation"></a>Hur du utför den här omvandlingen

Följ dessa steg om du vill utföra den här transformeringen:
1. Markera minst två kolumner som du vill kombinera i en kolumn. 
2. Välj **kombinera kolumner efter exempel** från den **transformerar** menyn. Eller högerklicka på rubriken för någon av de markerade kolumnerna och välj **kombinera kolumner efter exempel** på snabbmenyn. Transformera redigeraren öppnas och lägga till en ny kolumn bredvid den högra mest markerade kolumnen. Den nya kolumnen som innehåller de kombinerade värden åtskilda av en avgränsare. Valda kolumner kan identifieras av kryssrutorna på kolumnrubrikerna. Tillägg och borttagning av kolumner från markeringen kan göras med hjälp av kryssrutorna.
3. Du kan uppdatera det kombinerade värdet i den nya kolumnen. Det uppdaterade värdet används som exempel Läs transformeringen.
4. Klicka på **OK** att acceptera transformeringen.

### <a name="transform-editor-advanced-mode"></a>Transformera editor: Avancerat läge

Avancerat läge ger en bättre upplevelse för att kombinera kolumner. 

Att välja **avgränsare** under **kombinera kolumner efter** gör det möjligt för användaren att ange strängar i den **avgränsare** textruta. Fliken ut från den **avgränsare** textruta för att förhandsgranska resultat i data gird. Tryck på **OK** att genomföra transformeringen.

Att välja **exempel** under **kombinera kolumner efter** gör det möjligt för användaren att ange kombinerade exempelvärden. Dubbelklicka på raderna i rutnätet för att flytta upp en rad som ett exempel. Ange i utdata som förväntas i textrutan mot den upphöjda raden. Fliken ut från den **avgränsare** textruta för att förhandsgranska resultat i data gird. Tryck på **OK** att genomföra transformeringen. 

Användaren kan växla mellan den **grundläggande läget** och **Avancerat läge** genom att klicka på länkarna i Redigeraren för transformeringen.

### <a name="transform-editor-send-feedback"></a>Transformera editor: skicka Feedback

Klicka på den **skicka feedback** länkar öppnas den **Feedback** dialogen med kommentarrutan förväg till exempel användaren har angett. Användaren bör granska innehållet i kommentarrutan och ger mer information som hjälper oss att förstå problemet. Om användaren inte vill dela data med Microsoft användaren bör ta bort förinställd exempeldata innan du klickar på den **skicka Feedback** knappen. 

### <a name="editing-existing-transformation"></a>Redigera befintliga omvandling

En användare kan redigera en befintlig **kombinera kolumn av exempel** transformera genom att välja **redigera** alternativ för steget omvandling. Klicka på **redigera** öppnar Redigeraren transformeringen i **grundläggande läget**. Användaren kan ange den **Avancerat läge** genom att klicka på länken i huvudet. Alla exemplen som angavs under skapandet av transformeringen visas.

## <a name="example-using-separators"></a>Exempel på användning av avgränsare

Ett komma följt av ett blanksteg används som avgränsare i det här exemplet för att kombinera den *gata*, *Stad*, *tillstånd*, och *ZIP* kolumner.

|Gatuadress|Ort|Status|ZIP|Kolumn|
|:----|:----|:----|:----|:----|
|16011 N.E. 36th sätt|REDMOND|WA|98052|16011 N.E. 36th way, REDMOND, WA, 98052|
|16021 N.E. 36th sätt|REDMOND|WA|98052|16021 N.E. 36th way, REDMOND, WA, 98052|
|16031 N.E. 36th sätt|REDMOND|WA|98052|16031 N.E. 36th way, REDMOND, WA, 98052|
|16041 N.E. 36th sätt|REDMOND|WA|98052|16041 N.E. 36th way, REDMOND, WA, 98052|
|16051 N.E. 36th sätt|REDMOND|WA|98052|16051 N.E. 36th way, REDMOND, WA, 98052|
|16061 N.E. 36th sätt|REDMOND|WA|98052|16061 N.E. 36th way, REDMOND, WA, 98052|
|3460 157th minimering NE|REDMOND|WA|98052|3460 157th minimering NE, REDMOND, WA, 98052|
|3350 157th Ave N.E.|REDMOND|WA|98052|3350 157th Ave N.E., REDMOND, WA, 98052|
|3240 157th minimering N.E.|REDMOND|WA|98052|3240 157th minimering N.E., REDMOND, WA, 98052|

## <a name="example-using-by-example"></a>Exemplet med hjälp av exempel

Värdet i **fetstil** har angetts som ett exempel.

|Date|Månad|År|Timme|Minut|Sekund|Kombinerade kolumn|
|:----|:----|:----|:----|:----|:----|:----|
|13|Okt|2016|15|01|23|**13-dessa-2016 15:01:23 PDT**|
|16|Okt|2016|16|22|33|16-dessa-2016 15:01:33 PDT|
|17|Okt|2016|12|43|12|17-dessa-2016 15:01:12 PDT|
|12|Nov|2016|14|22|44|12-Nov-2016 15:01:44 PDT|
|23|Nov|2016|01|52|45|23-Nov-2016 15:01:45 PDT|
|16|Jan|2017|22|34|56|16-Jan-2016 15:01:56 PDT|
|23|Mar|2017|01|55|25|23-Mar-2016 15:01:25 PDT|
|16|Apr|2017|11|34|36|16-april-2016 15:01:36 PDT|

