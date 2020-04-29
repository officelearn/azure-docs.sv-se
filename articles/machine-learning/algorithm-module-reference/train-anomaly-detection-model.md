---
title: 'Träna avvikelse identifierings modell: modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen träna avvikelse identifiering modell för att skapa en utbildad avvikelse identifierings modell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 78ed2d85729cce94e8dfa579545f558d2cfe4651
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502976"
---
# <a name="train-anomaly-detection-model"></a>Träna avvikelseidentifieringsmodell

Den här artikeln beskriver hur du använder modulen **träna avvikelse identifierings modell** i Azure Machine Learning designer (för hands version) för att skapa en utbildad avvikelse identifierings modell.

Modulen tar som indata för en uppsättning modell parametrar för avvikelse identifierings modellen och en omärkt data uppsättning. Den returnerar en utbildad avvikelse identifierings modell, tillsammans med en uppsättning etiketter för tränings data.  

Mer information om algoritmer för avvikelse identifiering i designern finns i följande avsnitt: 

+ [PCA-baserad avvikelseidentifiering](pca-based-anomaly-detection.md)  

## <a name="how-to-configure-train-anomaly-detection-model"></a>Så här konfigurerar du träna avvikelse identifierings modell 

1.  Lägg till modulen **träna avvikelse identifierings modell** i din pipeline i designern. Du hittar den här modulen i kategorin för **avvikelse identifiering** .

2. Anslut en av modulerna som har utformats för avvikelse identifiering, till exempel [PCA-baserad avvikelse identifiering](pca-based-anomaly-detection.md)

    Andra typer av modeller stöds inte. Om du kör pipelinen får du ett fel meddelande: alla modeller måste ha samma elev typ.  

3.  Konfigurera modulen för avvikelse identifiering genom att välja kolumnen etikett och ange andra parametrar som är speciella för algoritmen.  

4.  Koppla en data uppsättning för träning till den högra indatan för **träna avvikelse identifierings modell**.  

5.  Skicka pipelinen.  

## <a name="results"></a>Resultat

När utbildningen är klar:

+ Om du vill visa modellens parametrar högerklickar du på modulen och väljer **visualisera**. 

+ Använd [Poäng modell](score-model.md) med nya indata för att skapa förutsägelser.

+ Om du vill spara en ögonblicks bild av den tränade modellen väljer du modulen och klickar på ikonen **registrera data uppsättning** under fliken **utdata + loggar** i den högra panelen.   

 
## <a name="next-steps"></a>Nästa steg

Se en [uppsättning moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 

Se [undantag och felkoder för designern (för hands version)](designer-error-codes.md) för en lista med fel som är speciella för designer-modulerna.
'