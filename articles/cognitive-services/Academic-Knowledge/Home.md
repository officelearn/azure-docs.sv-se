---
title: Academic Knowledge API för Microsoft akademisk diagram | Microsoft Docs
description: Använd Academic Knowledge API för att tolka användarfrågor och hämta omfattande information från Academic diagrammet i kognitiva Microsoft-tjänster.
services: cognitive-services
author: mvorvoreanu
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/27/2017
ms.author: mivorvor
ms.openlocfilehash: e241f9a87cd58b62eafd754bd3cb4283aa0a1e92
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352344"
---
# <a name="academic-knowledge-api"></a>Academic Knowledge API

Välkommen till Academic Knowledge API. Med den här tjänsten kan du tolka användarfrågor för akademiskt innehåll och hämta innehållsrik information från Microsoft Academic Graph (MAG). Kunskapsbasen magnetiska DATAORDSLÄSARE är ett webb-scale heterogena entitet diagram som består av enheter som modellen vetenskaplig aktiviteter: fältet för undersökning, författare, institut, papper, plats och händelsen. 

Magnetiska DATAORDSLÄSARE data bryts från Bing web index samt en interna kunskapsbas från Bing. På grund av pågående Bing indexering, innehåller detta API ny information från webben efter identifiering och indexering av Bing. Baserat på denna dataset, Academic Knowledge API: er kan en knowledge-driven, interaktiva dialogruta som kombinerar sömlöst reaktiv sökning med proaktiv förslag upplevelser och söka papper diagrammet sökresultat histogram distributioner av den attributvärden för en uppsättning dokument och relaterade entiteter.

Mer information om Microsoft Academic Graph finns [ http://aka.ms/academicgraph ](http://aka.ms/academicgraph).

Academic Knowledge API: et har flyttats från kognitiva Services-förhandsgranskning till kognitiva Services övningar. Nya startsidan för projektet är: [ https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge ](https://labs.cognitive.microsoft.com/en-us/project-academic-knowledge). Din befintliga API-nyckel ska fortsätta arbeta fram 24: e kan 2018. Generera en ny API-nyckel efter detta datum. Observera betald förhandsgranskningen inte kommer längre tillgängliga när den befintliga nyckeln har löpt ut. Kontakta vårt team om den kostnadsfria nivån API inte är tillräcklig för dina egna behov. 

## <a name="features"></a>Funktioner
Academic Knowledge API består av fyra relaterade REST-slutpunkter:  
  1. **tolka** – tolkar en frågesträng för användare av naturligt språk. Returnerar kommenterade tolkningar för att aktivera funktioner för automatisk ifyllning i sökrutor som förutsäger vad användaren skriver.  
  2. **utvärdera** – utvärderar ett frågeuttryck och returnerar Academic Knowledge entitet resultat.  
  3. **calchistogram** – beräknar ett histogram fördelningen av attributvärden för academic entiteter som returneras av ett frågeuttryck, till exempel distribution av citat per år för en viss författare.  
  4. **diagrammet Sök** – söker efter en diagrammet mönstret och returnerar resultaten matchande entitet.

Dessa API-metoder kan används tillsammans, du skapa en omfattande semantiska sökinställningar. Får en användare på en frågesträng i **tolka** metoden ger dig en kommenterade version av frågan och ett strukturerade frågeuttryck (valfritt) för att slutföra användarfrågan baserat på semantiken för underliggande akademisk data. Till exempel om en användare skriver strängen *Latenta s*, **tolka** metod kan ge en uppsättning rangordnas tolkningar tyder på att användaren kan söka efter fältet undersökning  *Latenta semantiska analysen*, dokumentet *Latenta struktur analysis*, eller andra uttryck för entitet som börjar med *Latenta s*. Den här informationen kan användas för att snabbt leder användaren till önskade sökresultat.

Den **utvärdera** metoden kan användas för att hämta en uppsättning matchande papper entiteter från academic knowledge base och **calchistogram** metoden kan användas för att beräkna fördelningen av attributvärden för en uppsättning papper enheter som kan användas för att filtrera sökresultaten ytterligare.        

Den **diagram Sök** metoden har två lägen: *json* och *lambda*. Den *json* läge kan utföra diagram för mönstermatchning enligt graph-mönster som anges av ett JSON-objekt. Den *lambda* läge kan utföra serversidan beräkningar under diagrammet traversals enligt användardefinierade lambda-uttryck.

## <a name="getting-started"></a>Komma igång 
Se underavsnitt längst till vänster för detaljerad dokumentation.  Observera att REST API-anrop för att förbättra läsbarhet exemplen innehåller tecken (till exempel blanksteg) som inte har URL-kodade.  Din kod måste du använda rätt URL-kodning.
