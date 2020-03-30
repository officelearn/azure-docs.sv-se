---
title: 'Använd omvandling: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Använd omvandling i Azure Machine Learning för att ändra en indatauppsättning baserat på en tidigare beräknad omvandling.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/05/2020
ms.openlocfilehash: ccf9d0c3eef50c7dfd838f1929e52506e8984879
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78395261"
---
# <a name="apply-transformation-module"></a>Använd omformningsmodul

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen om du vill ändra en indatauppsättning baserat på en tidigare beräknad omvandling.

Om du till exempel använde z-poäng för att normalisera träningsdata med hjälp av modulen **Normalisera data,** skulle du vilja använda z-poängvärdet som beräknades för träning även under bedömningsfasen. I Azure Machine Learning kan du spara normaliseringsmetoden som en transformering och sedan använda **Använd omvandling** för att tillämpa z-poängen på indata innan du gör mål.

## <a name="how-to-save-transformations"></a>Så här sparar du omvandlingar

Med designern kan du spara dataomvandlingar som **datauppsättningar** så att du kan använda dem i andra pipelines.

1. Välj en dataomvandlingsmodul som har körts.

1. Välj fliken **Utdata + loggar.**

1. Välj **ikonen Spara** om du vill spara **resultatomvandlingen**.

## <a name="how-to-use-apply-transformation"></a>Så här använder du Använd omvandling  
  
1. Lägg till modulen **Använd omvandling** i pipelinen. Du hittar den här modulen i avsnittet **Modellbedömning & utvärdering** i modulpaletten. 
  
1. Hitta den sparade omvandling som du vill använda under **Datauppsättningar** > **Mina datauppsättningar** i modulpaletten.

1. Anslut utdata för den sparade omvandlingen till den vänstra indataporten i modulen **Använd omvandling.**

    Datauppsättningen ska ha exakt samma schema (antal kolumner, kolumnnamn, datatyper) som den datauppsättning som omvandlingen först utformades för.  
  
1. Anslut datauppsättningsutdata för önskad modul till den högra indataporten i modulen **Använd omvandling.**
  
1. Om du vill använda en omvandling på den nya datauppsättningen kör du pipelinen.  

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 