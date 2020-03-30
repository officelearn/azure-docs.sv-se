---
title: 'Träna klustermodell: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Train Clustering Model i Azure Machine Learning för att träna klustermodeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: c29baf3cdda998a2ab78c84f3311b84d37086bcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477433"
---
# <a name="train-clustering-model"></a>Träna klustringsmodellen

I den här artikeln beskrivs en modul i Azure Machine Learning designer (förhandsversion).

Använd den här modulen för att träna en klustermodell.

Modulen tar en otränad klustermodell som du redan har konfigurerat med hjälp av [K-Means Clustering-modulen](k-means-clustering.md) och tränar modellen med hjälp av en märkt eller omärkt datauppsättning. Modulen skapar både en tränad modell som du kan använda för förutsägelse och en uppsättning klustertilldelningar för varje enskilt fall i träningsdata.

> [!NOTE]
> Det går inte att träna en klustermodell med modulen [Tågmodell,](train-model.md) som är den allmänna modulen för att träna maskininlärningsmodeller. Det beror [på att Train Model](train-model.md) endast fungerar med övervakade inlärningsalgoritmer. K-medel och andra klusteralgoritmer tillåter oövervakad inlärning, vilket innebär att algoritmen kan lära sig av omärkta data.  
  
## <a name="how-to-use-train-clustering-model"></a>Så här använder du Train Clustering Model  

1.  Lägg till modulen **Train Clustering Model** i pipelinen i designern. Du hittar modulen under **Maskininlärningsmoduler**i kategorin **Tåg.**  
  
2. Lägg till [K-Means Clustering-modulen](k-means-clustering.md) eller en annan anpassad modul som skapar en kompatibel klustermodell och ange parametrarna för klustermodellen.  
    
3.  Koppla en träningsdatauppsättning till den högra inmatningen av **Train Clustering Model**.
  
5.  I **Kolumnuppsättning**väljer du de kolumner från den datauppsättning som ska användas i byggkluster. Var noga med att markera kolumner som gör bra funktioner: till exempel undvika att använda ID:er eller andra kolumner som har unika värden eller kolumner som har samma värden.

    Om det finns en etikett kan du antingen använda den som en funktion eller utelämna den.  
  
6. Välj alternativet **Sök efter tillägg eller avmarkera endast för resultat**, om du vill mata ut träningsdata tillsammans med den nya klusteretiketten.

    Om du avmarkerar det här alternativet skrivs bara klustertilldelningarna ut. 

7. Skicka pipelinen eller klicka på modulen **Träna klustermodell** och välj **Kör markerad**.  
  
### <a name="results"></a>Resultat

Efter att utbildningen har avslutats:

+ Om du vill spara en ögonblicksbild av den tränade modellen väljer du fliken **Utdata** på den högra panelen i **tågmodellmodulen.** Välj ikonen **Registrera datauppsättning** om du vill spara modellen som en återanvändbar modul.

+ Om du vill generera poäng från modellen använder du [Tilldela data till kluster](assign-data-to-clusters.md).

## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 