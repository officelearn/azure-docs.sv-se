---
title: 'Identifieringsmodell för tågavvikelse: Modulreferens'
titleSuffix: Azure Machine Learning
description: Lär dig hur du använder modulen Identifieringsmodell för tågavvikelse för att skapa en tränad avvikelseidentifieringsmodell.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 78ed2d85729cce94e8dfa579545f558d2cfe4651
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502976"
---
# <a name="train-anomaly-detection-model"></a>Identifieringsmodell för tågavvikelse

I den här artikeln beskrivs hur du använder modulen **Identifieringsmodell för tågavvikelse** i Azure Machine Learning designer (förhandsversion) för att skapa en tränad avvikelseidentifieringsmodell.

Modulen tar som indata en uppsättning modellparametrar för avvikelseidentifieringsmodell och en omärkt datauppsättning. Den returnerar en tränad avvikelseidentifieringsmodell, tillsammans med en uppsättning etiketter för träningsdata.  

Mer information om algoritmerna för avvikelseidentifiering i designern finns i följande avsnitt: 

+ [Identifiering av PCA-baserad avvikelse](pca-based-anomaly-detection.md)  

## <a name="how-to-configure-train-anomaly-detection-model"></a>Konfigurerar identifieringsmodell för tågavvikelse 

1.  Lägg till modulen **Identifiering av tågavvikelse i** pipelinen i designern. Du hittar den här modulen i kategorin **Avvikelseidentifiering.**

2. Anslut en av modulerna som utformats för avvikelseidentifiering, till exempel [PCA-baserad avvikelseidentifiering](pca-based-anomaly-detection.md)

    Andra typer av modeller stöds inte. om du kör pipelinen får du felet: Alla modeller måste ha samma elevtyp.  

3.  Konfigurera avvikelseidentifieringsmodulen genom att välja etikettkolumnen och ange andra parametrar som är specifika för algoritmen.  

4.  Koppla en träningsdatauppsättning till den högra inmatningen av **Train Anomaly Detection Model**.  

5.  Skicka pipelinen.  

## <a name="results"></a>Resultat

Efter träningen är klar:

+ Om du vill visa modellens parametrar högerklickar du på modulen och väljer **Visualisera**. 

+ Om du vill skapa förutsägelser använder du [Poängmodell](score-model.md) med nya indata.

+ Om du vill spara en ögonblicksbild av den tränade modellen väljer du modulen och klickar på ikonen **Registrera datauppsättning** under fliken **Utdata+loggar** på den högra panelen.   

 
## <a name="next-steps"></a>Nästa steg

Se uppsättningen [moduler som är tillgängliga](module-reference.md) för Azure Machine Learning. 

Se [Undantag och felkoder för designern (förhandsversion)](designer-error-codes.md) för en lista över fel som är specifika för designermodulerna.
'