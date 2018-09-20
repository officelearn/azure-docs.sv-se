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
ms.openlocfilehash: 56b3f2899f1e77c1a2b840285e2c69bdb8987ff4
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363096"
---
# <a name="emotion-api-frequently-asked-questions"></a>Vanliga och frågor svar om känslo-API
 
> [!IMPORTANT]
> Förhandsversionen av video API avslutas den 30 oktober 2017. Prova den nya [förhandsversionen av Video Indexer API](https://azure.microsoft.com/services/cognitive-services/video-indexer/) att enkelt lyfta ut kunskaper från videor och förbättra innehållsidentifiering, till exempel sökresultaten genom att identifiera talade ord, ansikten, tecken och känslor. [Läs mer](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-emotion-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Om du inte hittar svar på dina frågor i den här vanliga frågor och svar, kan du prova att fråga Känslo-API-community på [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) eller kontakta hjälp och Support på [UserVoice](https://cognitive.uservoice.com/).  

-----

**Fråga**: *vilka typer av bilder får bäst resultat av Känslo-API?*

**Svar**: använda placerade, fullständig främre ansiktsigenkänning avbildningar för bästa resultat. Tillförlitlighet minskar med partiella främre ansikten och Känslo-API kan inte identifiera känslor i bilder där de står inför är roterad 45 grader eller mer.

-----

**Fråga**: *identifiera för hur många känslor kan Känslo-API?*

**Svar**: Känslo-API kan identifiera åtta olika allmänt accepterat känslor: 
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

**Svar**: Detta innebär att algoritmen som hade ett problem med parsning av avbildningen.  
* Format som stöds inmatad bild innehåller JPEG, PNG, GIF (den första bildrutan), BMP. 
* Bildens filstorlek får inte vara större än 4MB
* Ansikts-flashminnet storleksintervall är 36 x 36 till 4096 x 4 096 pixlar. Ansikten utanför det här intervallet kommer inte att identifieras
* Vissa ansikten kanske inte identifieras på grund av tekniska utmaningar, t.ex. mycket stora ansikte vinklar (head-attityd), stor ocklusion. Främre och nästan direkt ansikten har bästa resultat

-----
