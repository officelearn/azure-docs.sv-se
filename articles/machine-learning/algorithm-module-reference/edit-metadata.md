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
ms.openlocfilehash: cfee607aca155b6cf68e5bddc40eb9c752df5e34
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028822"
---
# <a name="edit-metadata-module"></a>Redigera Metadata-modul

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen för att ändra metadata som associeras med kolumner i en datauppsättning. Värde och datatyp för datauppsättningen kommer att ändras när du har använt den **redigera Metadata** modulen. 
 
Vanliga metadata förändringarna kan innefatta:
  
+ Behandla booleskt eller numeriska kolumner som kategoriska värden  
  
+ Som anger vilken kolumn som innehåller den *klass* etikett, eller värden som du vill att kategorisera eller förutsäga  
  
+ Markera kolumner som funktioner
  
+ Ändra datum/tid-värden till ett numeriskt värde eller tvärtom  
  
+ Byta namn på kolumner
  
 Använda [redigera Metadata när du behöver att ändra definitionen för en kolumn, vanligtvis för att uppfylla kraven för en underordnad modul. Till exempel vissa moduler kan fungerar bara med specifika datatyper eller kräver flaggor för kolumner, till exempel `IsFeature` eller `IsCategorical`.  
  
 När du har utfört den nödvändiga åtgärden, kan du återställa metadata till det ursprungliga tillståndet. 
  
## <a name="configure-edit-metadata"></a>Konfigurera redigera Metadata
  
1.  Lägg till i Azure Machine Learning, [redigera Metadata](./edit-metadata.md) modulen i experimentet och koppla den datauppsättning som du vill uppdatera. Du hittar den under **Dataomvandling**i den **ändra** kategori.
  
2.  Klicka på **starta kolumnväljaren** och välj den kolumn eller en uppsättning kolumner att arbeta med. Du kan välja kolumner individuellt efter namn eller index, eller välja en grupp med kolumner av typen.  
  
3.  Välj den **datatypen** alternativet om du vill tilldela en annan datatyp till de markerade kolumnerna. Genom att ändra data typ kan behövas för vissa åtgärder: till exempel om datauppsättningen för källan har siffror som hanteras som text, måste du ändra dem till en numerisk datatyp innan du använder matematiska åtgärder. 

    + Datatyper som stöds är `String`, `Integer`, `Double`, `Boolean`, `DateTime`. 

    + Om flera kolumner har valts, måste du aktivera metadata ändringarna **alla** markerade kolumner. Anta exempelvis att du väljer 2 – 3 numeriska kolumner. Du kan ändra dem alla till datatypen string och byta namn på dem i en enda åtgärd. Du kan dock ändra en kolumn till datatypen string och en annan kolumn från ett flyttal till ett heltal.
  
    + Om du inte anger en ny datatyp är kolumnmetadata oförändrade. 
    
    + Kolumntypen och värden kommer att ändras när utför den [redigera Metadata](./edit-metadata.md) igen. Du kan återställa den ursprungliga datatypen när som helst med hjälp av [redigera Metadata](./edit-metadata.md) återställa Kolumndatatypen.  

    > [!NOTE]
    > Om du ändrar någon typ av tal till det **DateTime** skriver, lämna den **DateTime-Format** tomt. För närvarande, går det inte att ange formatet för mål-data.  

      
4.  Välj den **Kategoriskt** alternativet för att ange att värdena i de markerade kolumnerna ska behandlas som kategorier. 

    Du kan till exempel har en kolumn som innehåller talen 0,1 och 2, men vet att talen faktiskt betyder ”Smoker”, ”icke-smoker” och ”okänt”. I så fall genom att flagga kolumnen som kategoriska du kan se till att värdena inte används i numeriska beräkningar och bara vill gruppera data. 
  
5.  Använd den **fält** om du vill ändra hur att Azure Machine Learning använder data i en modell.

    + **Funktionen**: Använd det här alternativet för att flagga en kolumn som en funktion för användning med moduler som fungerar endast för funktionen kolumner. Som standard behandlas först alla kolumner som funktioner.  
  
    + **Etiketten**: Använd det här alternativet för att markera etiketten (även kallat den förutsägbara attributet eller målvariabel). Många moduler kräver att minst en (och enda) etikettkolumnen finnas i datauppsättningen. 
    
        I många fall kan Azure Machine Learning härleda en kolumn innehåller en klass etikett, men genom att ange dessa metadata kan du kontrollera att kolumnen identifieras korrekt. Det här alternativet ändras inte datavärden, enda sättet att hantera vissa maskininlärningsalgoritmer data.
  

  
    > [!TIP]
    >  Har data som inte passar in i följande kategorier?  Din datauppsättning kan exempelvis innehålla värden som unika identifierare som inte är användbara variabler. ID: N kan ibland orsaka problem när de används i en modell. 
    >   
    >  Som tur är ”under försättsbladen” behålls Azure Machine Learning alla dina data, så att du inte behöver ta bort sådana kolumner från datauppsättningen. När du vill utföra åtgärder på en särskild uppsättning kolumner kan bara ta bort alla andra kolumner tillfälligt med hjälp av den [Välj kolumner i datauppsättning](./select-columns-in-dataset.md) modulen. Du kan senare införa kolumnerna i datauppsättningen med hjälp av den [Lägg till kolumner](./add-columns.md) modulen.  
  
6. Använd följande alternativ för att rensa tidigare val och återställa metadata till standardvärdena.  
  
    + **Rensa funktionen**: Använd det här alternativet för att ta bort flaggan för funktionen.  
  
         Eftersom alla kolumner behandlas först som funktioner för moduler som utför matematiska operationer kan du behöva använda det här alternativet för att förhindra att numeriska kolumner som behandlas som variabler.
  
    + **Rensa etikett**: Använd det här alternativet för att ta bort den **etikett** metadata från den angivna kolumnen.  
  
    + **Rensa poäng**: Använd det här alternativet för att ta bort den **poäng** metadata från den angivna kolumnen.  
  
         Möjligheten att uttryckligen Markera en kolumn som en poäng är för närvarande inte tillgängliga i Azure Machine Learning. Men resultera vissa åtgärder i en kolumn som flaggas som en poäng internt. Dessutom kan en anpassad R-modul utdata poäng värden.
  
  
7.  För **nya kolumnnamnen**, skriver du det nya namnet på den markerade kolumnen eller kolumnerna.  
  
    + Kolumnnamn kan använda tecken som stöds av UTF-8-kodning. Tomma strängar, null-värden eller namn som består enbart av blanksteg är inte tillåtna.  
  
    + Om du vill byta namn på flera kolumner, Skriv namnen som en kommaavgränsad lista efter kolumnindex.  
  
    + Alla valda kolumner måste ändras. Du kan inte utelämna eller hoppa över kolumner.  
  
  
8.  Kör experimentet.  

## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 