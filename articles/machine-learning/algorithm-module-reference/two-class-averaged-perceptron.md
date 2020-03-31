---
title: 'Beslut Forest Regression: Modul Referens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Tvåklass medelvärde perceptron i Azure Machine Learning för att skapa en maskininlärningsmodell baserat på den genomsnittliga perceptronalgoritmen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 73e23dd7d350ea63e9fd8b933a525a9d8aad9e3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920781"
---
# <a name="two-class-averaged-perceptron-module"></a>Medelmodul med medel perceptron i två klasser

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen för att skapa en maskininlärningsmodell baserat på den genomsnittliga perceptronalgoritmen.  
  
Den här klassificeringsalgoritmen är en övervakad inlärningsmetod och kräver en *taggad datauppsättning*, som innehåller en etikettkolumn. Du kan träna modellen genom att tillhandahålla modellen och den taggade datauppsättningen som en indata till [Train Model](./train-model.md). Den tränade modellen kan sedan användas för att förutsäga värden för de nya indataexemplen.  

### <a name="about-averaged-perceptron-models"></a>Om genomsnittliga perceptronmodeller

Den *genomsnittliga perceptron metoden* är en tidig och enkel version av ett neuralt nätverk. I den här metoden är indata indelade i flera möjliga utdata baserat på en linjär funktion och kombineras sedan med en uppsättning vikter som härleds från funktionsvektorn – därav namnet "perceptron".

De enklare perceptronmodellerna är anpassade för att lära sig linjärt olika mönster, medan neurala nätverk (särskilt djupa neurala nätverk) kan modellera mer komplexa klassgränser. Perceptrons är dock snabbare, och eftersom de bearbetar ärenden seriellt kan perceptrons användas med kontinuerlig träning.

## <a name="how-to-configure-two-class-averaged-perceptron"></a>Konfigurera medel perceptron med två klasser

1.  Lägg till modulen **Medel perceptron i två klasser** i pipelinen.  

2.  Ange hur du vill att modellen ska tränas genom att ange alternativet **Skapa träningsläge.**  
  
    -   **Enkel parameter:** Om du vet hur du vill konfigurera modellen anger du en specifik uppsättning värden som argument.

    -   **Parameterintervall:** Välj det här alternativet om du inte är säker på de bästa parametrarna och vill köra ett parametervep. Välj ett intervall med värden som du vill iterera över, och [Tune Model Hyperparameters itererar](tune-model-hyperparameters.md) över alla möjliga kombinationer av de inställningar du angav för att bestämma de hyperparametrar som ger optimalt resultat.  
  
3.  För **utbildningsfrekvens**anger du ett värde för *utbildningsfrekvensen*. Inlärningshastighetsvärdena styr storleken på det steg som används i stokastisk gradientnedstigning varje gång modellen testas och korrigeras.
  
     Genom att göra hastigheten mindre testar du modellen oftare, med risk för att du kan fastna i en lokal platå. Genom att göra steget större kan du konvergera snabbare, med risk för att överskrida den sanna minima.
  
4.  För **Maximalt antal iterationer**anger du hur många gånger du vill att algoritmen ska undersöka träningsdata.  
  
     Stoppa tidigt ger ofta bättre generalisering. Öka antalet iterationer förbättrar montering, med risk för övermontering.
  
5.  För **slumptalsutsäde**anger du eventuellt ett heltalsvärde som ska användas som utsände. Användning av ett frö rekommenderas om du vill säkerställa reproducerbarhet av rörledningen över körningar.  
  
1.  Anslut en träningsdatauppsättning och en av utbildningsmodulerna:
  
    -   Om du ställer in **Skapa trainer-läge** till **En parameter**använder du modulen [Tågmodell.](train-model.md)




## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 