---
title: 'Edit Metadata: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen redigera Metadata i Azure Machine Learning-tjänsten för att ändra metadata som associeras med kolumner i en datauppsättning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 282652adb917450c262e08bf10c3c6e537b829e7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072709"
---
# <a name="edit-metadata-module"></a>Redigera Metadata-modul

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd modulen redigera Data för att ändra metadata som associeras med kolumner i en datauppsättning. Värde och datatyp för datauppsättningen ändras efter att redigera Metadata-modulen.

Vanliga metadata förändringarna kan innefatta:
  
+ Behandla booleskt eller numeriska kolumner som kategoriska värden.
  
+ Som anger vilken kolumn som innehåller den **klass** etiketten eller innehåller de värden som du vill att kategorisera eller förutsäga.
  
+ Markera kolumner som funktioner.
  
+ Ändra datum/tid-värden till numeriska värden eller vice versa.
  
+ Byta namn på kolumner.
  
 Använd redigera Metadata när du behöver att ändra definitionen för en kolumn, vanligtvis för att uppfylla kraven för en underordnad modul. Till exempel vissa moduler fungerar bara med specifika datatyper eller kräver flaggor för kolumner, till exempel `IsFeature` eller `IsCategorical`.  
  
 När du har utfört den nödvändiga åtgärden kan du återställa metadata till det ursprungliga tillståndet.
  
## <a name="configure-edit-metadata"></a>Konfigurera redigera Metadata
  
1. Lägg till modulen redigera Metadata till ditt experiment i Azure Machine Learning och anslut den datauppsättning som du vill uppdatera. Du kan hitta datauppsättningen under **Dataomvandling** i den **ändra** kategori.
  
1. Välj **starta kolumnväljaren** och välj den kolumn eller en uppsättning kolumner att arbeta med. Du kan välja kolumner individuellt efter namn eller index eller välja en grupp med kolumner av typen.  
  
1. Välj den **datatypen** alternativet om du vill tilldela en annan datatyp till de markerade kolumnerna. Du kan behöva ändra datatypen för vissa åtgärder. Till exempel om datauppsättningen för källan har siffror som hanteras som text, måste du ändra dem till en numerisk datatyp innan du använder matematiska åtgärder.

    + Datatyper som stöds är **sträng**, **heltal**, **dubbla**, **booleskt**, och **DateTime**.

    + Om du väljer flera kolumner, måste du aktivera metadata ändringarna *alla* markerade kolumner. Anta exempelvis att du väljer två eller tre numeriska kolumner. Du kan ändra dem alla till en sträng datatypen och byta namn på dem i en enda åtgärd. Du kan dock ändra en kolumn till datatypen string och en annan kolumn från ett flyttal till ett heltal.
  
    + Om du inte anger en ny datatyp, är kolumnmetadata oförändrade.

    + Kolumntypen och värden ändras när du utför åtgärden Redigera Metadata. Du kan återställa den ursprungliga datatypen när som helst genom att använda Redigera Metadata för att återställa Kolumndatatypen.  

    > [!NOTE]
    > Om du ändrar någon typ av tal till det **DateTime** skriver, lämna den **DateTime-Format** tomt. För närvarande kan det inte går att ange formatet för mål-data.  

1. Välj den **Kategoriskt** alternativet för att ange att värdena i de markerade kolumnerna ska behandlas som kategorier.

    Du kan till exempel har en kolumn som innehåller talen 0, 1 och 2 men vet att talen faktiskt betyder ”Smoker”, ”icke-smoker” och ”okänt”. I så fall genom att flagga kolumnen som kategoriska du se till att värdena används endast att gruppera data och inte i numeriska beräkningar.
  
1. Använd den **fält** om du vill ändra hur att Azure Machine Learning använder data i en modell.

    + **Funktionen**: Använd det här alternativet för att flagga en kolumn som en funktion i moduler som fungerar endast för funktionen kolumner. Som standard behandlas först alla kolumner som funktioner.  
  
    + **Etiketten**: Använd det här alternativet för att markera den etiketten, som kallas även för förutsägbara attributet eller mål-variabeln. Många moduler kräver exakt en etikettkolumnen är tillgänglig i datauppsättningen.

        I många fall kan Azure Machine Learning härleda att en kolumn innehåller en klass etikett. Genom att ange dessa metadata kan du se till att kolumnen identifieras korrekt. Det här alternativet ändras inte datavärden. Ändras det enda sättet att hantera vissa maskininlärningsalgoritmer data.
  
    > [!TIP]
    > Har du data som inte passar in i följande kategorier? Din datauppsättning kan exempelvis innehålla värden som unika identifierare som inte är användbart som variabler. Sådana ID: N kan ibland orsaka problem när de används i en modell.
    >
    > Som tur är kan Azure Machine Learning och ser till alla dina data så att du inte behöver ta bort sådana kolumner från datauppsättningen. När du vill utföra åtgärder på en särskild uppsättning kolumner kan bara ta bort alla andra kolumner tillfälligt med hjälp av den [Välj kolumner i datauppsättning](select-columns-in-dataset.md) modulen. Du kan senare införa kolumnerna i datauppsättningen med hjälp av den [Lägg till kolumner](add-columns.md) modulen.  
  
1. Använd följande alternativ för att rensa tidigare val och återställa metadata till standardvärdena.  
  
    + **Rensa funktionen**: Använd det här alternativet för att ta bort flaggan för funktionen.  
  
         Alla kolumner behandlas först som funktioner. Du kan behöva använda det här alternativet för att förhindra att numeriska kolumner som behandlas som variabler för moduler som utför matematiska operationer.
  
    + **Rensa etikett**: Använd det här alternativet för att ta bort den **etikett** metadata från den angivna kolumnen.  
  
    + **Rensa poäng**: Använd det här alternativet för att ta bort den **poäng** metadata från den angivna kolumnen.  
  
         Du markera för närvarande inte uttryckligen en kolumn som en poäng i Azure Machine Learning. Men resultera vissa åtgärder i en kolumn som flaggas som en poäng internt. Dessutom kan en anpassad R-modul utdata poäng värden.

1. För **nya kolumnnamnen**, ange det nya namnet på den markerade kolumnen eller kolumnerna.  
  
    + Kolumnnamn kan använda tecken som stöds av UTF-8-kodning. Tomma strängar, null-värden eller namn som består enbart av blanksteg är inte tillåtna.  
  
    + Byt namn på flera kolumner genom att ange namnen som en kommaavgränsad lista efter vilka kolumnindex.  
  
    + Alla valda kolumner måste ändras. Du kan inte utelämna eller hoppa över kolumner.  
  
1. Kör experimentet.  

## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten.
