---
title: Vanliga frågor och svar – Känslo-API
titlesuffix: Azure Cognitive Services
description: Få svar på vanliga frågor om Känslo-API.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 6c1c4b8e5c2701f3c419a58bc3fdc33f7e629bbd
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238550"
---
# <a name="emotion-api-frequently-asked-questions"></a>Vanliga och frågor svar om känslo-API

> [!IMPORTANT]
> Känslo-API kommer att bli inaktuella den 15 februari 2019. Funktionen för igenkänning av känslor är nu allmänt tillgänglig som en del av den [Ansikts-API](https://docs.microsoft.com/azure/cognitive-services/face/).

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-emotion-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Om du inte hittar svar på dina frågor i den här vanliga frågor och svar, kan du prova att fråga Känslo-API-community på [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) eller kontakta hjälp och Support på [UserVoice](https://cognitive.uservoice.com/).  

-----

**Fråga**: *vilka typer av bilder får bäst resultat av Känslo-API?*

**Svar**: använda placerade, fullständig främre ansiktsigenkänning avbildningar för bästa resultat. Tillförlitlighet minskar med partiella främre ansikten och Känslo-API kan inte identifiera känslor i bilder där de står inför roteras mer än 45 grader.

-----

**Fråga**: *identifiera för hur många känslor kan Känslo-API?*

**Svar**: Känslo-API kan identifiera åtta olika allmänt accepterad känslor:
* Glädje
* Ledsamhet
* Förvåning
* Ilska
* Rädsla
* Förakt
* Avsmak
* Neutral

-----

**Fråga**: *finns det något sätt att skicka direktuppspelad video-API: et och samtidigt få resultatet?*

**Svar**: använda Bildbaserad känslo-API och anropa på varje ram eller hoppa över bildrutor för prestanda.  Analys av bildruta för bildruta videoexemplen är tillgängliga.

-----

**Fråga**: *jag skicka binär data i men då får jag: ”Ogiltig ansiktsbilden.**

**Svar**: det här meddelandet innebär att algoritmen som hade ett problem med parsning av avbildningen.  
* Avbildning av stöds-format är JPEG, PNG, GIF (den första bildrutan), BMP
* Bildens filstorlek får inte vara större än 4 MB
* Ansikts-flashminnet storleksintervall är 36 x 36 till 4096 x 4 096 pixlar. Ansikten utanför det här intervallet kan inte identifieras
* Vissa ansikten kanske inte identifieras på grund av tekniska utmaningar, exempelvis stora ansikte vinklar (head-attityd), stor ocklusion. Främre och nästan direkt ansikten har bästa resultat

-----
