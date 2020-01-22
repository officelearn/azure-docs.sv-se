---
title: 'Tillämpa omvandling: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Använd omvandling i Azure Machine Learning om du vill ändra en indata-datauppsättning baserat på en tidigare beräknad omvandling.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: a48ce60dca9f4221e364d53567f5b53719deb18c
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314529"
---
# <a name="apply-transformation-module"></a>Tillämpa omvandling modul

I den här artikeln beskrivs en modul i Azure Machine Learning designer.

Använd den här modulen för att ändra en data uppsättning som baseras på en tidigare beräknad omvandling.  
  
Om du till exempel använde z-poängen för att normalisera dina utbildnings data med hjälp av modulen **normalisera data** , skulle du vilja använda det z-Poäng värde som har beräknats för utbildning under bedömnings fasen. I Azure Machine Learning kan du spara normaliserings metoden som en transformering och sedan använda **tillämpa omvandling** för att tillämpa z-poängen i indata innan du påsöker.
  
Azure Machine Learning ger stöd för att skapa och använda många olika typer av anpassade transformeringar. Du kanske till exempel vill spara och återanvända omvandlingar till:  
  
- Ta bort eller Ersätt saknade värden med **rensade data som saknas**
- Normalisera data med **normaliserade data**
  

## <a name="how-to-use-apply-transformation"></a>Använda tillämpa omvandling  
  
1. Lägg till modulen **Använd omvandling** i din pipeline. Du hittar den här modulen under **Machine Learning**i kategorin **Poäng** . 
  
2. Leta upp en befintlig omvandling som ska användas som inmatad.  Tidigare sparade omvandlingar finns i gruppen **Transforms** i det vänstra navigerings fönstret.  
  
   
  
3. Anslut den data uppsättning som du vill transformera. Data uppsättningen ska ha exakt samma schema (antal kolumner, kolumn namn, data typer) som den data uppsättning som omvandlingen först konstruerades för.  
  
4. Inga andra parametrar måste anges eftersom all anpassning utförs när omvandlingen definieras.  
  
5. Om du vill tillämpa en omvandling på den nya data uppsättningen kör du pipelinen.  

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 