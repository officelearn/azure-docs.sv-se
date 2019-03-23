---
title: Vanliga frågor och svar – Ansikts-API
titlesuffix: Azure Cognitive Services
description: Här är några svar på de mest populära frågorna om Ansikts-API-tjänsten.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: sbowles
ms.openlocfilehash: 47ce80e1b0cefc01752d2445b751ebe1c2d65d08
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351345"
---
# <a name="face-api-frequently-asked-questions"></a>Vanliga och frågor svar om ansikts-API

> [!TIP]
> Om du inte hittar svar på dina frågor i den här vanliga frågor och svar, kan du prova att fråga Ansikts-API-community på [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) eller kontakta hjälp och Support på [UserVoice](https://cognitive.uservoice.com/).

-----
**Fråga**: Vilka faktorer kan minska Precision för Ansikts-API för taligenkänning, verifiering eller Sök liknande?

**Svar**: Vanligtvis är det fall där människor har svårt att identifiera någon inklusive:
* Hinder som blockerar ena eller båda ögon
* Tuffa belysning (till exempel allvarliga bakgrundsbelysningen)
* Ändringar av hår format eller videodetektion hår
* Ändringar på grund av ålder
* Extreme ansiktsuttryck (till exempel Skrikande)

Ansikts-API är ofta i utmanande fall som ovan, men noggrannhet kan minskas. Träna dina personer med foton som innehåller en stor vinklar och belysning för att göra erkännande mer robust och åtgärda dessa problem.

-----
**Fråga**:  Jag skicka binär data i men jag får felmeddelandet ”ogiltig ansiktsbilden”.

**Svar**:  Det här felet innebär att algoritmen som hade ett problem med parsning av avbildningen. Orsaker är:
* Format som stöds inmatad bild innehåller JPEG, PNG, GIF (den första bildrutan), BMP.
* Bildens filstorlek får inte vara större än 4 MB
* Ansikts-flashminnet storleksintervall är 36 x 36 till 4096 x 4 096 pixlar. Ansikten utanför det här intervallet kan inte identifieras
* Vissa ansikten kanske inte identifieras på grund av tekniska utmaningar, till exempel stora ansikte vinklar (head-attityd), stor ocklusion. Främre och nästan direkt ansikten har bästa resultat

-----
