---
title: Vanliga frågor om Ansikts-API-tjänsten | Microsoft Docs
description: Här är svaren på de mest populära frågorna om Ansikts-API-tjänsten.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 01/26/2017
ms.author: sbowles
ms.openlocfilehash: da2f75deef8a8beea3ba23b6a39eb6d2fe104b54
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351558"
---
# <a name="face-api-frequently-asked-questions"></a>Vanliga och frågor svar om ansikts-API
### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-face-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Om du inte hittar svar på frågor här, kan du pröva att besvara Ansikts-API-gemenskapen på [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) eller kontakta hjälp och Support på [UserVoice](https://cognitive.uservoice.com/).

-----
**Fråga**: vilka faktorer kan minska precisionen Ansikts-API för igenkänning, verifiering eller söka efter liknande?

**Svaret**: vanligtvis är det fall där människor har svårt att identifiera någon inklusive.
* Hinder blockerar en eller båda ögon
* Tuffa ljus, t.ex. allvarligt bakgrundsbelysningen
* Ändringar av hår format eller ansiktskirurgi hår
* Ändringar på grund av ålder
* Extreme ansikte uttryck (t.ex. Skrikande)

Yta API är ofta lyckas svårt fall som dessa, men kan minska noggrannhet. Träna din personer med foton som innehåller en stor vinklar och ljus recognition mer robusta och åtgärda dessa problem.

-----
**Fråga**: jag skicka binär data i men får ett ”ogiltig ansikte bild”-fel.

**Svaret**: Detta innebär att algoritmen haft problem parsning av avbildningen. Orsaker är:
* De inkommande bildformat som stöds omfattar JPEG, PNG, GIF (den första bildrutan), BMP.
* Bildens filstorlek ska inte vara större än 4MB
* Storlek är kan upptäckas står inför 36 x 36 till 4096 x 4 096 pixlar. Ytor utanför det här intervallet hittas inte
* Vissa ytor kanske inte identifieras på grund av tekniska utmaningar, t.ex. väldigt ansikte vinklar (head-attityd) stora är spärrat. Främre och nästan direkt ytor har bästa resultat

-----
