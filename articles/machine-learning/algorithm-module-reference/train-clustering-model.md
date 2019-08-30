---
title: 'Träna kluster modell: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen träna kluster modell i Azure Machine Learning tjänst för att träna kluster modeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: 4883b1420913eb4e5f3bd5f13a95e410370d9184
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128482"
---
# <a name="train-clustering-model"></a>Träna klustringsmodellen

I den här artikeln beskrivs en modul i Visual Interface (för hands version) för Azure Machine Learning tjänst.

Använd den här modulen för att träna en kluster modell.

Modulen tar en modell för en modell som inte är tränad och som du redan har [](k-means-clustering.md) konfigurerat med hjälp av modulen för att klustra och träna modellen med en etikettad eller omärkt data uppsättning. Modulen skapar både en utbildad modell som du kan använda för förutsägelse och en uppsättning kluster tilldelningar för varje ärende i tränings data.

> [!NOTE]
> En kluster modell kan inte tränas med modulen [träna modell](train-model.md) , som är den allmänna modulen för utbildning av Machine Learning-modeller. Det beror på att [tåg modellen](train-model.md) endast fungerar med övervakade Learning-algoritmer. K-och andra klustrade algoritmer tillåter oövervakad inlärning, vilket innebär att algoritmen kan lära sig från omärkta data.  
  
## <a name="how-to-use-train-clustering-model"></a>Använda träna kluster modell  
  
1.  Lägg till modulen **träna klustring modell** i experimentet i Studio. Du hittar modulen under **Machine Learning moduler**, i kategorin **träna** .  
  
2. Lägg till modulen [K-betyder klustring](k-means-clustering.md) eller en annan anpassad modul som skapar en kompatibel kluster modell och anger parametrarna för kluster modellen.  
    
3.  Koppla en data uppsättning för träning till den högra indatan i **träna kluster modellen**.
  
5.  I **kolumn uppsättning**väljer du de kolumner från data uppsättningen som ska användas för att skapa kluster. Se till att välja kolumner som gör lämpliga funktioner: Undvik till exempel att använda ID: n eller andra kolumner som har unika värden, eller kolumner som har samma värden.

    Om en etikett är tillgänglig kan du antingen använda den som en funktion eller lämna den.  
  
6. Välj alternativet, **Markera kryss rutan för Lägg till eller ta bort kontroll för resultat**, om du vill spara tränings data tillsammans med den nya kluster etiketten.

    Om du avmarkerar det här alternativet visas bara kluster tilldelningarna. 

7. Kör experimentet eller klicka på modulen **träna klustring modell** och välj **Kör vald**.  
  
### <a name="results"></a>Resultat

När utbildningen har slutförts:


+  Du visar värdena i data uppsättningen genom att högerklicka på modulen, välja **resultat data uppsättningar**och klicka på **visualisera**.

+ Om du vill spara den tränade modellen för senare åter användning högerklickar du på modulen, väljer **tränad modell**och klickar på **Spara som utbildad modell**.

+ Använd [tilldela data till kluster](assign-data-to-clusters.md)om du vill generera Poäng från modellen.



## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för att Azure Machine Learning-tjänsten. 