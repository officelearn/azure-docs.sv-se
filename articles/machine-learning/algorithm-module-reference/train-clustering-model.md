---
title: 'Träna klustermodellen: Modulreferens'
titleSuffix: Azure Machine Learning service
description: Lär dig hur du använder modulen träna Clustering-modellen i Azure Machine Learning-tjänsten kan skapa kluster modeller.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 41cdec1d7f1c3932b17da6f9b1de518071f3f542
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028087"
---
# <a name="train-clustering-model"></a>Träna Clustering-modellen

Den här artikeln beskrivs en modul av det visuella gränssnittet (förhandsversion) för Azure Machine Learning-tjänsten.

Använd den här modulen för att träna en klustringsmodell.

Modulen tar ett omdöme clustering-modell som du redan har konfigurerat med hjälp av den [K-Means klustring](k-means-clustering.md) -modulen och träna modellen med en datauppsättning med märkta eller utan etikett. Modulen skapar både en tränad modell som du kan använda för förutsägelse och en uppsättning kluster tilldelningar för varje fall i träningsdata.

> [!NOTE]
> En klustring modellen kan inte vara tränas med hjälp av den [Träningsmodell](train-model.md) modulen, som är allmänt modulen för att träna machine learning-modeller. Det beror på [Träningsmodell](train-model.md) fungerar bara med övervakad inlärning algoritmer. K-means och andra klustring algoritmer kan oövervakade learning, vilket innebär att algoritmen som kan lära sig från omärkta data.  
  
## <a name="how-to-use-train-clustering-model"></a>Hur du använder klustring Träningsmodell  
  
1.  Lägg till den **klustring Träningsmodell** modul till ditt experiment i Studio. Du kan hitta modulen under **Machine Learning-moduler**i den **träna** kategori.  
  
2. Lägg till den [K-Means klustring](k-means-clustering.md) modul eller en annan anpassad modul som skapar en kompatibel klustring modellera och ange rapportparametrar för clustering-modell.  
    
3.  Bifoga en datauppsättning för träning i den högra indataporten för **klustring Träningsmodell**.
  
5.  I **kolumnuppsättningen**, markera kolumnerna från datamängden som används för att skapa kluster. Se till att välja kolumner som ska bra funktioner: till exempel Undvik att använda ID: N eller andra kolumner som unika värden eller kolumner som har samma värden.

    Om det finns en etikett kan du antingen använda den som en funktion eller lämna.  
  
6. Markerar du alternativet **söka efter Lägg till eller avmarkera endast resultat för**, om du vill spara träningsdata tillsammans med den nya etiketten för klustret.

    Om du avmarkerar det här alternativet är endast kluster tilldelningar utdata. 

7. Kör experimentet, eller klicka på den **klustring Träningsmodell** modul och välj **kör valda**.  
  
### <a name="results"></a>Resultat

När utbildning har slutförts:


+  Om du vill visa värdena i datauppsättningen, högerklicka på modulen, Välj **resultera datauppsättningar**, och klicka på **visualisera**.

+ Om du vill spara den tränade modellen för senare återanvändning, högerklicka på modulen, Välj **Trained model**, och klicka på **Spara som Trained Model**.

+ Generera poäng från modellen genom att använda [tilldela Data till ett kluster](assign-data-to-clusters.md).



## <a name="next-steps"></a>Nästa steg

Se den [uppsättning moduler som är tillgängliga](module-reference.md) till Azure Machine Learning-tjänsten. 