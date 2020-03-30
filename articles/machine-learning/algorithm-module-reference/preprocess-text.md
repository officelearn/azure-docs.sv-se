---
title: 'Förprocesstext: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Förprocesstext i Azure Machine Learning för att rensa och förenkla text.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/01/2019
ms.openlocfilehash: 6e4d4c8f798418e090caeba091dec33c71f0458f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477501"
---
# <a name="preprocess-text"></a>Förbearbeta text

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd modulen **Förprocesstext** för att rensa och förenkla texten. Den stöder dessa vanliga textbehandlingsåtgärder:

* Borttagning av stoppord
* Använda reguljära uttryck för att söka efter och ersätta specifika målsträngar
* Lemmatization, som konverterar flera relaterade ord till en enda kanonisk form
* Normalisering av skiftläge
* Borttagning av vissa teckenklasser, till exempel tal, specialtecken och sekvenser av upprepade tecken som "aaaa"
* Identifiering och borttagning av e-post och webbadresser

**Förprocesstextmodulen** stöder för närvarande endast engelska.

## <a name="configure-text-preprocessing"></a>Konfigurera förbearbetning av text  

1.  Lägg till **förprocesstextmodulen** i pipelinen i Azure Machine Learning. Du hittar den här modulen under **Textanalys**.

1. Anslut en datauppsättning som har minst en kolumn som innehåller text.

1. Välj språk i listrutan **Språk.**

1. **Textkolumn att rengöra**: Markera den kolumn som du vill förbearbeta.

1. **Ta bort stoppord:** Markera det här alternativet om du vill använda en fördefinierad stopword-lista på textkolumnen. 

    Stopword-listor är språkberoende och anpassningsbara.

1. **Lemmatisering**: Välj det här alternativet om du vill att ord ska representeras i sin kanoniska form. Det här alternativet är användbart för att minska antalet unika förekomster av annars liknande texttoken.

    Lemmatization-processen är mycket språkberoende..

1. **Identifiera meningar:** Välj det här alternativet om du vill att modulen ska infoga en meningsgränsmarkering när du utför analyser.

    Den här modulen använder `|||` en serie med tre pipe-tecken för att representera meningsslutavslutaren.

1. Utför valfria sök- och ersättningsåtgärder med reguljära uttryck.

    * **Anpassat reguljärt uttryck**: Definiera den text du söker efter.
    * **Anpassad ersättningssträng**: Definiera ett enda ersättningsvärde.

1. **Normalisera skiftläge till gemener**: Välj det här alternativet om du vill konvertera ASCII-versaler till deras gemener.

    Om tecknen inte normaliseras betraktas samma ord med versaler och gemener som två olika ord.

1. Du kan också ta bort följande typer av tecken eller teckensekvenser från den bearbetade utdatatexten:

    * **Ta bort tal:** Välj det här alternativet om du vill ta bort alla numeriska tecken för det angivna språket. Identifieringsnummer är domänberoende och språkberoende. Om numeriska tecken är en integrerad del av ett känt ord kanske talet inte tas bort.
    
    * **Ta bort specialtecken**: Använd det här alternativet om du vill ta bort eventuella icke-alfanumeriska specialtecken.
    
    * **Ta bort dubbletttecken:** Välj det här alternativet om du vill ta bort extra tecken i alla sekvenser som upprepas i mer än två gånger. En sekvens som "aaaaa" skulle till exempel reduceras till "aa".
    
    * **Ta bort e-postadresser:** Välj det `<string>@<string>`här alternativet om du vill ta bort en sekvens i formatet .  
    * **Ta bort webbadresser:** Välj det här alternativet om du `http`vill `https` `ftp`ta bort alla sekvenser som innehåller följande URL-prefix: , ,`www`
    
1. **Expandera verbsammandragningar**: Det här alternativet gäller endast språk som använder verbsammandragningar. för närvarande endast på engelska. 

    Genom att välja det här alternativet kan du till exempel ersätta frasen *"skulle inte stanna där"* med *"skulle inte stanna där".*

1. **Normalisera omvänt snedstreck till snedstreck:** Välj det `\\` `/`här alternativet om du vill mappa alla förekomster av till .

1. **Dela token på specialtecken:** Välj det här alternativet om `&`du `-`vill bryta ord på tecken som , och så vidare. Det här alternativet kan också minska specialtecken när det upprepas mer än två gånger. 

    `MS---WORD` Strängen skulle till exempel delas upp `MS`i `-`tre `WORD`token, , och .

1. Skicka pipelinen.

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 