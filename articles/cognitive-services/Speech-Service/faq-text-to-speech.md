---
title: Vanliga frågor om tal till Text-tjänsten i Azure | Microsoft Docs
description: Här är svaren på de mest populära frågorna om tal till Text.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 06/11/2018
ms.author: panosper
ms.openlocfilehash: 4a29435c0ace79fc3a5d3a5a42a0e91bdbc8da5e
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2018
ms.locfileid: "37082832"
---
# <a name="text-to-speech-frequently-asked-questions"></a>Vanliga och frågor svar om text till tal

Om du inte hittar svar på frågor här, kan du pröva att besvara anpassad tal Service-communityn på [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) och [UserVoice](https://cognitive.uservoice.com/)

## <a name="general"></a>Allmänt

**Fråga**: Vad är skillnaden mellan standard och anpassade röst?

**Svaret**: standard röst (kallas även modeller röst teckensnitt) har tränats med Microsoft ägs data och redan har distribuerats i molnet. Anpassade röst modeller Tillåt användaren att anpassa en genomsnittlig modell och överföra timbre och uttryck sätt beroende på högtalaren röst format eller för att träna en helt ny modell baserat på data i utbildning förberetts av användaren. Idag vill fler och fler kunder ha en en av en typ, företagsanpassad röst för sina robotar. Den anpassade röst skapa plattformen är det rätta valet för som.

**Fråga**: där startar om du vill använda en standard röst-modell?

**Svaret**: mer än 80 standard röst modeller i över 45 språk som är tillgängliga via HTTP-begäranden. Först måste du hämta en [prenumeration nyckeln](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started). Om du vill göra REST-anrop till före distribuerade röst modeller finns i [information här](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/rest-apis#text-to-speech).

**Fråga**: om jag vill använda en röstmeddelanden modell är API: et samma som standard röster?

**Svaret**: när du har anpassade röst modellen skapas och distribueras, får du en slutpunkt som är unik för din modell. Du måste ange slutpunkten i HTTP-begäranden, använda rösten prata i dina appar. Samma funktioner som är tillgängliga via REST API för text till tal-tjänsten är också tillgängligt för anpassade slutpunkten. Se hur du [skapa och använda anpassade slutpunkten](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#create-and-use-a-custom-endpoint).

**Fråga**: måste du förbereda utbildning-data för att skapa anpassade röst modeller på egen hand?

**Svaret**: måste du förbereda utbildning-data för dig själv. En samling taldata krävs för att skapa en anpassad röst-modell. Den här samlingen består av en uppsättning ljudfiler av tal inspelningar och en textfil för utskrift av varje ljudfil. Resultatet av din digitala röst beroende kraftigt av kvaliteten på utbildningsdata. Det är viktigt att inspelningen är klar i ett tyst rum med hög kvalitet stående mikrofon för att skapa en bra TTS-röst. Konsekvent volym tala hastighet, tala breddsteg och även konsekvens i lättfattliga maner, ovanor av tal är nödvändiga för att skapa en bra digitala röst. Vi rekommenderar starkt att du har de rösterna registreras i en inspelning studio.
För tillfället vi inte stödja online inspelning eller några spela in studio rekommendationer. Format-krav finns [hur du förbereder inspelningar och betyg](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/how-to-customize-voice-font#prepare-recordings-and-transcripts)
 
**Fråga**: vilka skript ska jag använda för att registrera taldata för anpassade röst utbildning? 

**Svaret**: begränsar vi inte skript för röstinspelning. Du kan använda egna skript för att registrera tal. Bara se till att du har tillräckligt med fonetisk täckning i dina taldata. För att träna en anpassad röst du kan börja med en liten mängd taldata, som kan vara 50 olika meningar (om 3-5 minuter för). Mer data som du anger, desto mer naturlig rösten. Du kan börja träna en röst fullt teckensnitt när du anger inspelningar av mer än 2000 meningarna (cirka 3-4 timmar för). För att få en fullständig röst hög kvalitet, måste du förbereda inspelningar av mer än 6000 meningarna (om 8-10 timmar för).  
Vi ger ytterligare tjänster för att hjälpa dig att förbereda skript för registrering. Kontakta [anpassad röst kundsupport](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) för frågor.

**Fråga**: Vad händer om jag behöver högre samtidighet än standardvärdet eller vad som erbjuds i portal?

**Svaret**: du kan skala upp din modell i steg om 20 samtidiga begäranden. Kontakta [anpassad röst kundsupport](mailto:customvoice@microsoft.com?subject=Inquiries%20about%20scripts%20generation%20for%20Custom%20Voice%20creation) för förfrågningar om högre skalning.

**Fråga**: kan jag hämta Mina modellen och kör lokalt?

**Svaret**: modeller kan hämtas och köras lokalt.

## <a name="next-steps"></a>Nästa steg

* [Felsökning](troubleshooting.md)
* [Viktig information](releasenotes.md)
