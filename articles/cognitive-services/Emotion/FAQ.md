---
title: Vanliga frågor och svar – Känslo-API
titlesuffix: Azure Cognitive Services
description: Få svar på vanliga frågor om Känslo-API.
services: cognitive-services
author: anrothMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: d4e074a789a23d18349a32d65f7a4e9a6f41f6e8
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860039"
---
# <a name="emotion-api-frequently-asked-questions"></a>Vanliga och frågor svar om känslo-API

> [!IMPORTANT]
> Känslo-API:et blir inaktuellt den 15 februari 2019. Funktionen för känsloigenkänning är nu allmänt tillgänglig som en del av [ansikts-API:et](https://docs.microsoft.com/azure/cognitive-services/face/).

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-emotion-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Om du inte hittar svar på dina frågor i den här vanliga frågor och svar, kan du prova att fråga Känslo-API-community på [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) eller kontakta hjälp och Support på [UserVoice](https://cognitive.uservoice.com/).  

-----

**Fråga**: *Vilka typer av bilder får bästa resultat från Känslo-API?*

**Svar**: Använda placerade, fullständig främre ansiktsigenkänning avbildningar för bästa resultat. Tillförlitlighet minskar med partiella främre ansikten och Känslo-API kan inte identifiera känslor i bilder där de står inför roteras mer än 45 grader.

-----

**Fråga**: *Hur många känslor kan identifiera med Känslo-API?*

**Svar**: Känslo-API identifierar åtta olika allmänt accepterad känslor:
* Glädje
* Ledsamhet
* Förvåning
* Ilska
* Rädsla
* Förakt
* Avsmak
* Neutral

-----

**Fråga**: *Finns det några sätt att skicka direktuppspelad video-API: et och samtidigt få resultat?*

**Svar**: Använd avbildningen baserat känslo-API och anropa på varje ram eller hoppa över bildrutor för prestanda.  Analys av bildruta för bildruta videoexemplen är tillgängliga.

-----

**Fråga**: *Jag skicka binär data i men då får jag: ”Ogiltigt ansiktsbilden.**

**Svar**: Det här meddelandet innebär att algoritmen som hade ett problem med parsning av avbildningen.  
* Avbildning av stöds-format är JPEG, PNG, GIF (den första bildrutan), BMP
* Bildens filstorlek får inte vara större än 4 MB
* Ansikts-flashminnet storleksintervall är 36 x 36 till 4096 x 4 096 pixlar. Ansikten utanför det här intervallet kan inte identifieras
* Vissa ansikten kanske inte identifieras på grund av tekniska utmaningar, exempelvis stora ansikte vinklar (head-attityd), stor ocklusion. Främre och nästan direkt ansikten har bästa resultat

-----
