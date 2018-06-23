---
title: Webb-API-gränssnitt i Knowledge utforskning Service API | Microsoft Docs
description: Använda web API-gränssnitt för att skapa en omfattande, semantiska sökinställningar i den kunskap utforskning Service (KES) API i kognitiva Services.
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: 16c5680eb4f249a5d37e6b90eea92cfff7090eef
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351546"
---
# <a name="web-api-interface"></a>API för webbgränssnitt
Modellfiler som har skapats av tjänsten Knowledge utforskning kan värdbaserad och nås via en uppsättning web API: er.  API: er kan befinna sig på den lokala datorn med hjälp av [ `host_service` ](CommandLine.md#host_service-command) kommando eller kan distribueras till en Azure cloud service med hjälp av den [ `deploy_service` ](CommandLine.md#deploy_service-command) kommando.  Båda metoderna exponera följande API-slutpunkter:
* [*tolka* ](interpretMethod.md) – tolkar en frågesträng för naturligt språk. Returnerar kommenterade tolkningar för att aktivera funktioner för automatisk ifyllning i sökrutor som förutsäger vad användaren skriver.
* [*utvärdera* ](evaluateMethod.md) – utvärdera och returnerar resultatet av ett strukturerade frågeuttryck.
* [*calchistogram* ](calchistogramMethod.md) – beräknar ett histogram över attributvärden för objekt som returnerats av en strukturerad frågeuttryck.

Dessa API-metoder att används tillsammans, skapa en omfattande semantiska sökinställningar.  Få en naturligt språk frågesträng den *tolka* metoden ger kommenterade versioner av indatafrågan med strukturerade frågeuttryck, baserat på de underliggande grammatik och index.  Den *utvärdera* metoden utvärderar strukturerade frågeuttrycket och returnerar matchande index-objekt för visning.  Den *calchistogram* metoden beräknar attributet värdet distributioner för att aktivera filtrering och förfining.

**Exempel**

I en domän för academic publikationer, om en användare skriver strängen ”Latenta s” den *tolka* metod kan ge en uppsättning rangordnas tolkningar tyder på att användaren kan söka efter nyckelordet ”Latenta semantiska analysen”, den rubriken ”Latenta strukturanalys” eller andra uttryck som börjar med ”Latenta s”.  Den här informationen kan användas för att snabbt leder användaren till önskade sökresultat.

För den här domänen i *utvärdera* metoden kan användas för att hämta en uppsättning matchande publikationer från academic indexet och *calchistogram* metoden kan användas för att beräkna fördelningen av attribut värden för de matchande publikationer som kan användas för att ytterligare filter och avgränsa sökresultaten.

Observera att REST API-anrop för att förbättra läsbarhet exemplen innehåller tecken (till exempel blanksteg) som inte har URL-kodade. Din kod måste du använda rätt URL-kodning.
