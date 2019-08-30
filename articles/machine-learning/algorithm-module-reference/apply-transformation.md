---
title: 'Tillämpa omvandling: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen Använd omvandling i Azure Machine Learning-tjänsten för att ändra en indata-datauppsättning baserat på en tidigare beräknad omvandling.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 8b4ec9e71d9ce932a7b206467db91760f4a4fc47
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128971"
---
# <a name="apply-transformation-module"></a>Tillämpa omvandling modul

I den här artikeln beskrivs en modul i Visual Interface (för hands version) för Azure Machine Learning tjänst.

Använd den här modulen för att ändra en data uppsättning som baseras på en tidigare beräknad omvandling.  
  
Om du till exempel använde z-poängen för att normalisera dina utbildnings data med hjälp av modulen **normalisera data** , skulle du vilja använda det z-Poäng värde som har beräknats för utbildning under bedömnings fasen. I Azure Machine Learning kan du spara normaliserings metoden som en transformering och sedan använda **tillämpa omvandling** för att tillämpa z-poängen i indata innan du påsöker.
  
Azure Machine Learning ger stöd för att skapa och använda många olika typer av anpassade transformeringar. Du kanske till exempel vill spara och återanvända omvandlingar till:  
  
- Ta bort eller Ersätt saknade värden med **rensade data som saknas**
- Normalisera data med normaliserade **data**
  

## <a name="how-to-use-apply-transformation"></a>Använda tillämpa omvandling  
  
1. Lägg till modulen **Använd omvandling** i experimentet. Du hittar den här modulen under **Machine Learning**i kategorin **Poäng** . 
  
2. Leta upp en befintlig omvandling som ska användas som inmatad.  Tidigare sparade omvandlingar finns i gruppen Transforms i det vänstra navigerings fönstret.  
  
   
  
3. Anslut den data uppsättning som du vill transformera. Data uppsättningen ska ha exakt samma schema (antal kolumner, kolumn namn, data typer) som den data uppsättning som omvandlingen först konstruerades för.  
  
4. Inga andra parametrar måste anges eftersom all anpassning utförs när omvandlingen definieras.  
  
5. Om du vill tillämpa en omvandling på den nya data uppsättningen kör du experimentet.  

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 