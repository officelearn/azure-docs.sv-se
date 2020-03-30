---
title: Så här återställer du ett borttaget lagringskonto
description: Läs om hur du återställer ett borttaget lagringskonto
author: todmccoy
manager: dcscontentpm
ms.service: storage
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: rogarana
ms.subservice: files
services: storage
tags: ''
ms.openlocfilehash: 05465d4a03335ac607ba8981116c66fd6dac9416
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252636"
---
# <a name="how-to-recover-a-deleted-storage-account"></a>Så här återställer du ett borttaget lagringskonto

Azure Storage ger dataåtersåtersåtare genom automatiserade repliker, men hindrar inte användare eller programkod från att skada data, oavsett om de är oavsiktligt eller uppsåtligt. För att upprätthålla dataåtergivningen under instanser av program- eller användarfel krävs mer avancerade tekniker, till exempel att kopiera data till en sekundär lagringsplats med en granskningslogg.

I följande tabell ges översikt över omfattningen av återställning av lagringskonto beroende på replikeringsstrategin.

| |LRS|ZRS|GRS|RA - GRS|
|---|---|---|---|---|
|Azure Resource Manager för lagringskonto|Ja|Ja|Ja|Ja|
|Klassiska lagringskonto|Ja|Ja|Ja|Ja|

Samla in följande information och lämna in en supportbegäran med Microsoft Support:

* Lagringskontots namn
* Datum för radering
* Region för lagringskonto
* Hur togs lagringskontot bort?
* Vilken metod tog bort lagringskontot? (Portal, PowerShell, etc.)

Viktiga punkter

* Det kan i allmänhet ta upp till 15 dagar från borttagningstillfället för lagringstjänsten att utföra skräpinsamling, så lagringskonton kan inte återställas med ett serviceavtal.
* Microsoft Support försöker återställa behållaren/kontot på bästa sätt och kan inte garantera återställningen.

> [!NOTE]
> Återställningen kanske inte lyckas om kontot har återskapats. Om du redan har återskapat kontot måste du ta bort det först innan återställningen kan göras.
