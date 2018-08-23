---
title: Snabbstartsartikel för Visual Studio Tools for Machine Learning på Azure | Microsoft Docs
description: Den här artikeln beskriver hur du kommer igång med Visual Studio Tools for Machine Learning, från att skapa ett experiment, träna en modell och operationalisera en webbtjänst.
services: machine-learning
ms.workload: data-services
author: chris-lauren
ms.author: chris.lauren
ms.service: machine-learning
ms.component: core
ms.custom: mvc, vs-azure
ms.topic: quickstart
ms.date: 11/15/2017
ms.openlocfilehash: 94bca4d7670b1ec6fba5057b8295f7a3caac2968
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2018
ms.locfileid: "42024376"
---
# <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools för AI
Visual Studio Tools för AI är ett tillägg i Visual Studio Code för att skapa, testa och distribuera djupinlärnings- och AI-lösningar. Tillägget är smidigt integrerat med Azure Machine Learning, till exempel en vy över körningshistorik med information om prestanda för tidigare träningar och anpassade mått. Det har också en utforskarvy med exempel för att söka efter och starta nya projekt med [Microsoft Cognitive Toolkit (kallades tidigare CNTK)](http://www.microsoft.com/en-us/cognitive-toolkit), [Google TensorFlow](https://www.tensorflow.org) och andra djupinlärningsramverk. Den innehåller även en utforskare för beräkningsmål som gör att du kan skicka jobb för att träna modeller i fjärrmiljöer som Azure Virtual Machines eller Linux-servrar med GPU. Det ger också en förenklad åtkomst till [Azure Batch AI (förhandsversion)](https://docs.microsoft.com/azure/batch-ai/).
 
## <a name="getting-started"></a>Komma igång 
Innan du börjar måste du ladda ned och installera [Visual Studio](https://www.visualstudio.com/downloads/). När du har öppnat Visual Studio utför du följande steg:
1. Klicka på ”Verktyg” på menyraden i Visual Studio och välj ”Tillägg och uppdateringar”.
2. Klicka på fliken ”Online” och välj ”Sök på Visual Studio Marketplace”.
3. Sök efter ”Visual Studio för AI”. 
3. Klicka på knappen **Hämta**. 
4. Starta om Visual Studio efter installationen. 

När Visual Studio har lästs in på nytt är tillägget aktivt. [Läs mer om hur du söker efter tillägg](hhttps://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions).

> [!NOTE]
> Visual Studio Tools för AI kräver Visual Studio 2015 eller 2017, Professional eller Enterprise Edition. Det stöder inte Apple OSX-versionen. 


## <a name="exploring-project-samples"></a>Utforska projektexempel
Visual Studio Tools för AI levereras med en exempelutforskare. Med exempelutforskaren kan du enkelt hitta exempel och prova dem med bara ett par klick. Så här öppnar du utforskaren:   
1. I menyraden klickar du på **AI-verktyg**.
2. Klicka på ”Azure Machine Learning-galleriet”.

En flik med alla Azure ML-exempel öppnas.

## <a name="creating-a-new-project-from-the-sample-explorer"></a>Skapa ett nytt projekt från exempelutforskaren 
Du kan söka bland olika exempel och visa mer information om dem. Låt oss söka tills vi hittar exemplet ”Klassificera Iris”. Gör så här om du vill skapa ett nytt projekt baserat på det här exemplet:
1. Klicka på **installationsknappen** i projektexemplet. En ny dialogruta öppnas. 
2. Välj en resursgrupp, ett konto och en arbetsyta.
3. Projekttypen kan stå kvar som Allmänt.
4. Ange en projektsökväg och ett projektnamn och tryck på RETUR. 
5. En dialogruta öppnas där du uppmanas att spara en lösning. Klicka på Spara. 

När du är klar öppnas ett nytt projekt i en ny instans av Visual Studio. 

> [!TIP]
> Du måste vara inloggad för att kunna komma åt din Azure-resurs. Ange ”az login” i den inbäddade terminal och följ anvisningarna. 

## <a name="submitting-experiment-with-the-new-project"></a>Skicka experiment med det nya projektet
Med det nya projektet öppnat i Visual Studio ska du skicka ett jobb till vårt andra beräkningsmål (lokal eller virtuell dator med Docker).
Skicka jobbet genom att göra följande: 
1. Från Solution Explorer högerklickar du på filen du vill skicka och väljer **Ange som startfil**.
2. Välj projektnamn, högerklicka och välj **Skicka jobb...**
3. En ny dialogruta öppnas så att du kan välja kluster (eller beräkningsmål) för att köra ditt skript.
4. Klicka på **Skicka**

När jobbet har skickats visar den inbäddade terminalen förloppet för körningarna.

## <a name="view-list-of-jobs"></a>Visa listan över jobb
När jobben har skickats kan du visa en lista med jobb från körningshistoriken.
1. I **Server Explorer** klickar du på **AI-verktyg**.
2. Välj sedan **Azure Machine Learning**
3. Klicka på menyn **Jobb**.

Jobbutforskaren listar alla experiment som skickats för det här projektet. 

## <a name="view-job-details"></a>Visa jobbinformation
Klicka på den första körningen i listan med jobbutforskarens vy öppen.
Panelen Jobbsammanfattning läses in samt Loggar och Utdata.

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Så här konfigurerar du Azure Machine Learning för att arbeta med en utvecklingsmiljö (IDE)](./how-to-configure-your-IDE.md)
