---
title: 'Tillämpa omvandling: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du ändrar en indatauppsättning baserat på en tidigare beräknade omvandling med modulen gäller transformering i Azure Machine Learning-tjänsten.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 810f375642af49814049589cb83ad17fea578b13
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028717"
---
# <a name="apply-transformation-module"></a>Tillämpa omvandling modul

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen för att ändra en indatauppsättning baserat på en tidigare beräknade omvandling.  
  
Exempel: Om du använde z-poäng för att normalisera dina utbildningsdata med hjälp av den **normalisera Data** modulen, som du vill använda z-score-värde som beräknades för träning under den bedömnings fasen. I Azure Machine Learning, du kan spara metoden normalisering som en transformering och sedan använda **gäller omvandling** att tillämpa z-poäng på indata innan bedömning.
  
Azure Machine Learning har stöd för att skapa och använda många olika typer av anpassade transformeringar. Du kanske vill spara och återanvända transformeringar som:  
  
- Ta bort eller ersätta saknade värden, med hjälp av **Rensa Data som saknas**
- Normalisera data med **normalisera Data**
  

## <a name="how-to-use-apply-transformation"></a>Hur du använder gäller omvandling  
  
1. Lägg till den **gäller omvandling** modulen i experimentet. Du hittar den här modulen under **Maskininlärning**i den **poäng** kategori. 
  
2. Hitta en befintlig transformering ska användas som indata.  Tidigare sparade omvandlingar kan hittas i den **omvandlar** i det vänstra navigeringsfönstret.  
  
   
  
3. Anslut den datauppsättning som du vill omvandla. Datauppsättningen ska ha exakt samma schema (antal kolumner, kolumnnamn, datatyper) som den datamängd som transformeringen ursprungligen utvecklades.  
  
4. Inga andra parametrar måste anges eftersom alla anpassningar görs när du definierar transformeringen.  
  
5. Kör experimentet för att tillämpa en omvandling till den nya datauppsättningen.  

## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 