---
title: Sammanfatta data
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Sammanfatta data i Azure Machine Learning för att generera en grundläggande beskrivande statistikrapport för kolumnerna i en datauppsättning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 01/27/2020
ms.openlocfilehash: b0def12582dd3795e1b17334406e28d77c3c5656
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477450"
---
# <a name="summarize-data"></a>Sammanfatta data

I den här artikeln beskrivs en modul med Azure Machine Learning designer (förhandsversion).

Använd modulen Sammanfatta data för att skapa en uppsättning statistiska standardmått som beskriver varje kolumn i indatatabellen.

Sammanfattningsstatistik är användbar när du vill förstå egenskaperna för hela datauppsättningen. Du kan till exempel behöva veta:

- Hur många värden saknas i varje kolumn?
- Hur många unika värden finns det i en funktionskolumn?
- Vad är medelvärdet och standardavvikelsen för varje kolumn?

Modulen beräknar de viktiga poängen för varje kolumn och returnerar en rad med sammanfattningsstatistik för varje variabel (datakolumn) som anges som indata.

## <a name="how-to-configure-summarize-data"></a>Konfigurera sammanfatta data  

1. Lägg till modulen **Sammanfatta data** i pipelinen. Du hittar den här modulen i kategorin **Statistiska funktioner** i designern.

1. Anslut den datauppsättning som du vill generera en rapport för.

    Om du bara vill rapportera om vissa kolumner använder du modulen [Välj kolumner i datauppsättning](select-columns-in-dataset.md) för att projicera en delmängd kolumner att arbeta med.

1. Inga ytterligare parametrar krävs. Som standard analyserar modulen alla kolumner som tillhandahålls som indata, och beroende på typen av värden i kolumnerna matar ut en relevant statistikuppsättning enligt beskrivningen i avsnittet [Resultat.](#results)

1. Skicka pipelinen.

## <a name="results"></a>Resultat

Rapporten från modulen kan innehålla följande statistik. 

|Kolumnnamn|Beskrivning|
|------|------|  
|**Funktion**|Kolumnens namn|
|**Räkna**|Antal rader för alla rader|
|**Unikt värdeantal**|Antal unika värden i kolumnen|
|**Antal värden saknas**|Antal unika värden i kolumnen|
|**Min**|Lägsta värde i kolumnen|  
|**Max**|Högsta värde i kolumnen|
|**Medelvärde**|Medelvärde för alla kolumnvärden|
|**Genomsnittlig avvikelse**|Genomsnittlig avvikelse för kolumnvärden|
|**1: a kvartil**|Värde vid första kvartilen|
|**Median**|Mediankolumnvärde|
|**Tredje kvartilen**|Värde vid tredje kvartilen|
|**Läge**|Läge för kolumnvärden|
|**Intervall**|Heltal som representerar antalet värden mellan högsta och lägsta värden|
|**Provavvikelse**|Varians för kolumn; se Anmärkning|
|**Exempel på standardavvikelse**|Standardavvikelse för kolumn; se Anmärkning|
|**Prov skevhet**|Skevhet för kolumn; se Anmärkning|
|**Prov Kurtosis**|Kurtosis för kolonn; se Anmärkning|
|**P0.5**|0,5% percentil|
|**P1**|1% percentil|
|**P5**|5% percentil|
|**P95 (S)**|95% percentil|
|**P99.5**|99,5% percentil |

## <a name="technical-notes"></a>Tekniska anmärkningar

- För icke-numeriska kolumner beräknas endast värdena för Antal, Unikt värdeantal och Antal saknade värden. För annan statistik returneras ett null-värde.

- Kolumner som innehåller booleska värden bearbetas med hjälp av följande regler:

    - Vid beräkning av Min tillämpas ett logiskt OCH.
    
    - Vid beräkning av Max används ett logiskt ELLER
    
    - När du beräknar område kontrollerar modulen först om antalet unika värden i kolumnen är lika med 2.
    
    - Vid beräkning av statistik som kräver flyttalsberäkningar behandlas värdena i True som 1.0 och värden för False behandlas som 0,0.

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning.  
