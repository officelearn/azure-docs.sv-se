---
title: 'Tillämpa omvandling: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Använd omvandling i Azure Machine Learning om du vill ändra en indata-datauppsättning baserat på en tidigare beräknad omvandling.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 06/05/2020
ms.openlocfilehash: e2b4233f8f59a26e7da532fca48aecbb41857b66
ms.sourcegitcommit: 20e246e86e25d63bcd521a4b4d5864fbc7bad1b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2020
ms.locfileid: "84488638"
---
# <a name="apply-transformation-module"></a>Använd omvandlings modul

I den här artikeln beskrivs en modul i Azure Machine Learning designer (för hands version).

Använd den här modulen för att ändra en data uppsättning som baseras på en tidigare beräknad omvandling.

Om du till exempel använde z-poängen för att normalisera dina utbildnings data med hjälp av modulen **normalisera data** , skulle du vilja använda det z-Poäng värde som har beräknats för utbildning under bedömnings fasen. I Azure Machine Learning kan du spara normaliserings metoden som en transformering och sedan använda **tillämpa omvandling** för att tillämpa z-poängen i indata innan du påsöker.

## <a name="how-to-save-transformations"></a>Så här sparar du omvandlingar

Med designern kan du spara data transformationer som data **uppsättningar** så att du kan använda dem i andra pipeliner.

1. Välj en datatransformerings-modul som har körts.

1. Välj fliken **utdata + loggar** .

1. Hitta transformeringens utdata och välj **register data uppsättningen** för att spara den som en modul under **data uppsättnings** kategorin i modulen modul.

## <a name="how-to-use-apply-transformation"></a>Använda tillämpa omvandling  
  
1. Lägg till modulen **Använd omvandling** i din pipeline. Du hittar den här modulen i avsnittet **utvärdering av modell bedömnings &** i modulen modul. 
  
1. Hitta den sparade omvandling som du vill använda under **data uppsättningar** i modulen modul.

1. Anslut utdata från den sparade omvandlingen till den vänstra Indataporten för modulen **Använd omvandling** .

    Data uppsättningen ska ha exakt samma schema (antal kolumner, kolumn namn, data typer) som den data uppsättning som omvandlingen först konstruerades för.  
  
1. Anslut data uppsättningens utdata från den önskade modulen till den högra Indataporten för modulen **Använd omvandling** .
  
1. Om du vill tillämpa en omvandling på den nya data uppsättningen kör du pipelinen.  

## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 