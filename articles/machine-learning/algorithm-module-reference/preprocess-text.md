---
title: 'Förbearbeta text: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen för Förbearbetad text i Azure Machine Learning-tjänsten för att rensa och förenkla text.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 09/01/2019
ms.openlocfilehash: 4ee5e90b36f7a8fb3bfb42cad425cbb272553b3a
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210837"
---
# <a name="preprocess-text"></a>Förbearbeta Text

I den här artikeln beskrivs en modul i Visual Interface (för hands version) för Azure Machine Learning tjänst.

Använd modulen för Förbearbetad **text** för att rensa och förenkla text. Det stöder följande vanliga text bearbetnings åtgärder:

* Borttagning av stopp-ord
* Använda reguljära uttryck för att söka efter och ersätta specifika mål strängar
* Lemmatisering, som konverterar flera relaterade ord till ett enda kanoniskt format
* Fall normalisering
* Borttagning av vissa klasser av tecken, t. ex. siffror, specialtecken och sekvenser av upprepade tecken, till exempel "AAAA"
* Identifiering och borttagning av e-post och webb adresser

Modulen för Förbearbetad **text** stöder för närvarande bara engelska.

## <a name="configure-text-preprocessing"></a>Konfigurera text förbehandling  

1.  Lägg till modulen för Förbearbetad **text** i experimentet i Azure Machine Learning-tjänsten. Du kan hitta den här modulen under **textanalys**.

1. Anslut en data uppsättning som har minst en kolumn som innehåller text.

1. Välj språk i list rutan **språk** .

1. **Text kolumn att rensa**: Välj den kolumn som du vill Förbearbeta.

1. **Ta bort stopp ord**: Välj det här alternativet om du vill använda en fördefinierad stoppord-lista i text kolumnen. 

    Stoppord-listor är språk beroende och anpassningsbara.

1. **Lemmatisering**: Välj det här alternativet om du vill att ord ska visas i deras kanoniska form. Det här alternativet är användbart för att minska antalet unika förekomster av andra liknande tokens.

    Lemmatisering-processen är mycket språk beroende...

1. **Identifiera meningar**: Välj det här alternativet om du vill att modulen ska infoga en ord avgränsnings markering när du utför analysen.

    I den här modulen används en serie med tre `|||` pipe-tecken för att representera menings avslutning.

1. Utför valfria Sök-och-ersätt-åtgärder med hjälp av reguljära uttryck.

    * **Anpassat reguljärt uttryck**: Definiera texten som du söker efter.
    * **Anpassad ersättnings sträng**: Definiera ett enda ersättnings värde.

1. **Normalisera versaler till gemener**: Välj det här alternativet om du vill konvertera ASCII-versaler till gemener.

    Om tecknen inte är normaliserade, betraktas samma ord i versaler och gemener som två olika ord.

1. Du kan också ta bort följande typer av tecken eller tecken serier från texten för bearbetade utdata:

    * **Ta bort nummer**: Välj det här alternativet om du vill ta bort alla numeriska tecken för det angivna språket. Identifierings nummer är domän beroende och språk beroende. Om numeriska tecken är en viktig del av ett känt ord, kan det hända att antalet inte tas bort.
    
    * **Ta bort specialtecken**: Använd det här alternativet om du vill ta bort icke-alfanumeriska specialtecken.
    
    * **Ta bort duplicerade tecken**: Välj det här alternativet om du vill ta bort extra tecken i sekvenser som upprepas för mer än två gånger. En sekvens som "AAAAA" skulle till exempel minskas till "AA".
    
    * **Ta bort e-post adresser**: Välj det här alternativet om du vill ta bort en `<string>@<string>`sekvens med formatet.  
    * **Ta bort URL: er**: Välj det här alternativet om du vill ta bort en sekvens som innehåller följande URL `http`- `https`prefix `ftp`:,,,`www`
    
1. **Expandera kontrakt för verb**: Det här alternativet gäller endast för språk som använder verb-kontrakt. för närvarande endast på engelska. 

    Genom att välja det här alternativet kan du till exempel ersätta frasen *"Behåll inte där* ".

1. **Normalisera omvända snedstreck till snedstreck**: Välj det här alternativet om du vill mappa `\\` alla `/`instanser av till.

1. **Dela tokens på specialtecken**: Välj det här alternativet om du vill dela upp ord på tecken som `&`, `-`och så vidare. Det här alternativet kan också minska de särskilda tecknen när de upprepas mer än två gånger. 

    Strängen `MS---WORD` skulle t. ex. vara indelad i tre tokens `MS` `-`,, och `WORD`.

1. Kör experimentet.

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 