---
title: Webb-API-gränssnitt - Knowledge API för tjänst för Kunskapsutveckling
titlesuffix: Azure Cognitive Services
description: Använda web API-gränssnitt för att skapa en omfattande, semantiska sökfunktion i den kunskap utforskning Service (KES) API.
services: cognitive-services
author: bojunehsu
manager: nitinme
ms.service: cognitive-services
ms.subservice: knowledge-exploration
ms.topic: conceptual
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 970db5984eedebf98bbb087cfd0b4a35a21a0f54
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861195"
---
# <a name="web-api-interface"></a>Webb-API-gränssnitt

Modellfiler som skapats av tjänsten för Kunskapsutveckling kan som värd och kan nås via en uppsättning av webb-API: er.  API: erna kan vara baserad på den lokala datorn med den [ `host_service` ](CommandLine.md#host_service-command) kommandot eller kan distribueras till en Azure-molnet tjänsten med hjälp av den [ `deploy_service` ](CommandLine.md#deploy_service-command) kommando.  Båda metoderna exponerar följande API-slutpunkter:

* [*tolka* ](interpretMethod.md) – tolkar en frågesträng för naturligt språk. Returnerar kommenterade tolkningar för att aktivera funktioner för automatisk ifyllning i sökrutor som förutsäger vad användaren skriver.
* [*utvärdera* ](evaluateMethod.md) – utvärdera och returnerar resultatet av ett strukturerade frågeuttryck.
* [*calchistogram* ](calchistogramMethod.md) – beräknar ett histogram för attributvärden för objekt som returneras av en strukturerade frågeuttryck.

Dessa API-metoder att används tillsammans skapa en fullständig semantiska sökfunktion.  Får en frågesträng för naturligt språk i *tolka* metoden ger kommenterade versioner av indatafrågan med strukturerade frågeuttryck, baserat på de underliggande grammatik- eller indexhantering.  Den *utvärdera* metoden utvärderar strukturerade frågeuttryck och returnerar de matchande objekt index för visning.  Den *calchistogram* metoden beräknar attributet värdet distributioner för att aktivera filtrering och förfining.

**Exempel**

I en domän för akademiska publikationer, om en användare skriver strängen ”latent s” den *tolka* metoden kan ge en uppsättning rankad tolkningar som tyder på att användaren kan söka efter nyckelordet ”latent semantiska analys”, den rubriken ”latent struktur analysis” eller andra uttryck som börjar med ”latent s”.  Den här informationen kan användas för att snabbt vägleda användaren till önskat sökresultat.

För den här domänen i *utvärdera* metoden kan användas för att hämta en uppsättning matchande publikationer från akademiska indexet och *calchistogram* metoden kan användas för att beräkna fördelningen av attribut värden för de matchande publikationer som kan användas för att ytterligare filter och förfina sökresultaten.

Observera att för att förbättra läsbarheten för exemplen innehåller REST API-anropen tecken (såsom blanksteg) som inte har URL-kodats. Din kod behöver tillämpa lämpliga URL-kodningar.
