---
title: Känslo-API och svar | Microsoft Docs
description: 'Få svar på vanliga frågor och svar om Känslo-API: N i kognitiva Services.'
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 01/26/2017
ms.author: anroth
ms.openlocfilehash: 8532d7c00fd8d7b01d84b5e55cb9bbc60241789c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351576"
---
# <a name="emotion-api-frequently-asked-questions"></a>Vanliga och frågor svar om känslo-API
 
> [!IMPORTANT]
> Förhandsgranskning av video API avslutas på 30 oktober 2017. Prova den nya [Video indexeraren API Preview](https://azure.microsoft.com/services/cognitive-services/video-indexer/) enkelt extrahera insikter från videor och förbättra innehållsidentifiering upplevelser, till exempel sökresultat med hjälp av tal, ytor, tecken och känslor. [Läs mer](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-emotion-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Om du inte hittar svar på frågor här, kan du pröva att besvara Känslo-API-gemenskapen på [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) eller kontakta hjälp och Support på [UserVoice](https://cognitive.uservoice.com/).  

-----

**Fråga**: *vilka typer av avbildningar få bäst resultat från Känslo-API: et?*

**Svaret**: använda placerade, fullständig främre ansikte avbildningar för bästa resultat. Tillförlitlighet minskar med partiellt främre ytor och Känslo-API kan inte identifiera emotikoner i bilder där de står inför är roterade 45 grader eller mer.

-----

**Fråga**: *hur många emotikoner kan Känslo-API identifiera?*

**Svaret**: Känslo-API kan identifiera åtta olika allmänt accepterat emotikoner: 
* Glädje
* Ledsamhet
* Förvåning
* Ilska
* Rädsla
* Förakt
* Avsmak 
* Neutral 

-----

**Fråga**: *finns det något sätt att skicka direktuppspelad video-API: et och hämta resultatet samtidigt?*

**Svaret**: Använd bildbaserade känslo API och anropa i varje ram eller hoppa över ramar för prestanda.  Video bildruta för bildruta analys prover är tillgängliga.

-----

**Fråga**: *jag skicka binär data i, men den ger mig: ”Ogiltig ansikte bild.**

**Svaret**: Detta innebär att algoritmen haft problem parsning av avbildningen.  
* De inkommande bildformat som stöds omfattar JPEG, PNG, GIF (den första bildrutan), BMP. 
* Bildens filstorlek ska inte vara större än 4MB
* Storlek är kan upptäckas står inför 36 x 36 till 4096 x 4 096 pixlar. Ytor utanför det här intervallet hittas inte
* Vissa ytor kanske inte identifieras på grund av tekniska utmaningar, t.ex. väldigt ansikte vinklar (head-attityd) stora är spärrat. Främre och nästan direkt ytor har bästa resultat

-----
