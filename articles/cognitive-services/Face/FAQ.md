---
title: Vanliga frågor och svar – Ansikts-API
titlesuffix: Azure Cognitive Services
description: Här är några svar på de mest populära frågorna om Ansikts-API-tjänsten.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: sbowles
ms.openlocfilehash: 9b30fa0fbbd655c03800dadb19cc2568d404204d
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46129564"
---
# <a name="face-api-frequently-asked-questions"></a>Vanliga och frågor svar om ansikts-API

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-face-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Om du inte hittar svar på dina frågor i den här vanliga frågor och svar, kan du prova att fråga Ansikts-API-community på [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) eller kontakta hjälp och Support på [UserVoice](https://cognitive.uservoice.com/).

-----
**Fråga**: vilka faktorer kan minska Precision för Ansikts-API för taligenkänning, verifiering eller Sök liknande?

**Svar**: Allmänt är det fall där människor har svårt att identifiera någon inklusive.
* Hinder som blockerar ena eller båda ögon
* Tuffa drar t.ex. allvarliga bakgrundsbelysningen
* Ändringar av hår format eller videodetektion hår
* Ändringar på grund av ålder
* Extreme ansiktsuttryck (t.ex. Skrikande)

Ansikts-API är ofta i utmanande fall som dessa, men noggrannhet kan minskas. Träna dina personer med foton som innehåller en stor vinklar och belysning för att göra erkännande mer robust och åtgärda dessa problem.

-----
**Fråga**: jag skicka binär data i men jag får felmeddelandet ”ogiltig ansiktsbilden”.

**Svar**: Detta innebär att algoritmen som hade ett problem med parsning av avbildningen. Orsaker är:
* Format som stöds inmatad bild innehåller JPEG, PNG, GIF (den första bildrutan), BMP.
* Bildens filstorlek får inte vara större än 4MB
* Ansikts-flashminnet storleksintervall är 36 x 36 till 4096 x 4 096 pixlar. Ansikten utanför det här intervallet kommer inte att identifieras
* Vissa ansikten kanske inte identifieras på grund av tekniska utmaningar, t.ex. mycket stora ansikte vinklar (head-attityd), stor ocklusion. Främre och nästan direkt ansikten har bästa resultat

-----
