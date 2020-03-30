---
title: 'Redigera metadata: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Redigera metadata i Azure Machine Learning för att ändra metadata som är associerade med kolumner i en datauppsättning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 9853a3decc8d145fee58d1da526926e224ee2030
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064249"
---
# <a name="edit-metadata-module"></a>Redigera modulen Metadata

I den här artikeln beskrivs en modul som ingår i Azure Machine Learning designer (förhandsversion).

Använd modulen Redigera metadata för att ändra metadata som är associerade med kolumner i en datauppsättning. Datauppsättningens värde och datatyp ändras efter användning av modulen Redigera metadata.

Typiska metadataändringar kan omfatta:
  
+ Behandla booleska eller numeriska kolumner som kategoriska värden.
  
+ Anger vilken kolumn **class** som innehåller klassetiketten eller innehåller de värden som du vill kategorisera eller förutsäga.
  
+ Markera kolumner som funktioner.
  
+ Ändra datum-/tidsvärden till numeriska värden eller vice versa.
  
+ Byta namn på kolumner.
  
 Använd Redigera metadata när du behöver ändra definitionen av en kolumn, vanligtvis för att uppfylla kraven för en nedströmsmodul. Vissa moduler fungerar till exempel bara med specifika datatyper eller `IsFeature` kräver `IsCategorical`flaggor i kolumnerna, till exempel eller .  
  
 När du har utför den nödvändiga åtgärden kan du återställa metadata till dess ursprungliga tillstånd.
  
## <a name="configure-edit-metadata"></a>Konfigurera redigera metadata
  
1. I Azure Machine Learning designer lägger du till modulen Redigera metadata i pipelinen och ansluter den datauppsättning som du vill uppdatera. Du hittar modulen i kategorin **Dataomvandling.**
  
1. Klicka på **Redigera kolumn** på modulens högra panel och välj den kolumn eller uppsättning kolumner som ska arbetas med. Du kan välja kolumner individuellt efter namn eller index, eller så kan du välja en grupp kolumner efter typ.  
  
1. Välj alternativet **Datatyp** om du behöver tilldela de markerade kolumnerna en annan datatyp. Du kan behöva ändra datatypen för vissa åtgärder. Om källdatauppsättningen till exempel har tal som hanteras som text måste du ändra dem till en numerisk datatyp innan du använder matematiska åtgärder.

    + Datatyperna som stöds är **String**, **Intege**, **Double**, **Boolean**och **DateTime**.

    + Om du markerar flera kolumner måste du använda metadataändringarna på *alla* markerade kolumner. Anta till exempel att du väljer två eller tre numeriska kolumner. Du kan ändra dem alla till en strängdatatyp och byta namn på dem i en åtgärd. Du kan dock inte ändra en kolumn till en strängdatatyp och en annan kolumn från en flottör till ett heltal.
  
    + Om du inte anger någon ny datatyp är kolumnmetadata oförändrade.

    + Kolumntypen och värdena ändras när du har gjort åtgärden Redigera metadata. Du kan återställa den ursprungliga datatypen när som helst genom att använda Redigera metadata för att återställa kolumndatatypen.  

    > [!NOTE]
    > Om du ändrar någon typ av nummer till **datetime-typen** lämnar du fältet **DateTime-format** tomt. För närvarande går det inte att ange måldataformatet.  

1. Välj alternativet **Kategorisk** om du vill ange att värdena i de markerade kolumnerna ska behandlas som kategorier.

    Du kan till exempel ha en kolumn som innehåller siffrorna 0, 1 och 2, men vet att siffrorna faktiskt betyder "Rökare", "Icke-rökare" och "Okänd". I så fall, genom att flagga kolumnen som kategorisk du se till att värdena används endast för att gruppera data och inte i numeriska beräkningar.
  
1. Använd alternativet **Fält** om du vill ändra hur Azure Machine Learning använder data i en modell.

    + **Funktion**: Använd det här alternativet om du vill flagga en kolumn som en funktion i moduler som bara fungerar på funktionskolumner. Som standard behandlas alla kolumner inledningsvis som funktioner.  
  
    + **Etikett**: Använd det här alternativet för att markera etiketten, som också kallas det förutsägbara attributet eller målvariabeln. Många moduler kräver att exakt en etikettkolumn finns i datauppsättningen.

        I många fall kan Azure Machine Learning dra slutsatsen att en kolumn innehåller en klassetikett. Genom att ange dessa metadata kan du se till att kolumnen identifieras korrekt. Om du anger det här alternativet ändras inte datavärden. Det ändrar bara hur vissa maskininlärningsalgoritmer hanterar data.
  
    > [!TIP]
    > Har du data som inte passar in i dessa kategorier? Datauppsättningen kan till exempel innehålla värden som unika identifierare som inte är användbara som variabler. Ibland kan sådana ID-kort orsaka problem när de används i en modell.
    >
    > Lyckligtvis behåller Azure Machine Learning alla dina data, så att du inte behöver ta bort sådana kolumner från datauppsättningen. När du behöver utföra åtgärder på vissa särskilda kolumner tar du bara bort alla andra kolumner tillfälligt med hjälp av modulen [Välj kolumner i datauppsättning.](select-columns-in-dataset.md) Senare kan du sammanfoga kolumnerna tillbaka till datauppsättningen med hjälp av modulen [Lägg till kolumner.](add-columns.md)  
  
1. Använd följande alternativ för att rensa tidigare val och återställa metadata till standardvärdena.  
  
    + **Avmarkera funktion**: Använd det här alternativet för att ta bort funktionsflagga.  
  
         Alla kolumner behandlas inledningsvis som funktioner. För moduler som utför matematiska operationer kan du behöva använda det här alternativet för att förhindra att numeriska kolumner behandlas som variabler.
  
    + **Rensa etikett:** Använd det här alternativet om du vill ta bort **etikettmetadata** från den angivna kolumnen.  
  
    + **Rensa poäng:** Använd det här alternativet för att ta bort **poängmetadata** från den angivna kolumnen.  
  
         Du kan för närvarande inte uttryckligen markera en kolumn som en poäng i Azure Machine Learning. Vissa åtgärder resulterar dock i att en kolumn flaggas som en poäng internt. Dessutom kan en anpassad R-modul mata ut poängvärden.

1. För **Nya kolumnnamn**anger du det nya namnet på den markerade kolumnen eller kolumnerna.  
  
    + Kolumnnamn kan bara använda tecken som stöds av UTF-8-kodning. Tomma strängar, nulls eller namn som helt består av blanksteg är inte tillåtna.  
  
    + Om du vill byta namn på flera kolumner anger du namnen som en kommaavgränsad lista i ordningen för kolumnindexen.  
  
    + Alla markerade kolumner måste byta namn. Du kan inte utelämna eller hoppa över kolumner.  
  
1. Skicka pipelinen.  

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning.
