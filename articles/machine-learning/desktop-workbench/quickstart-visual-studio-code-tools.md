---
title: Snabbstartsartikel för Visual Studio Code Tools for Machine Learning på Azure | Microsoft Docs
description: Den här artikeln beskriver hur du kommer igång med Visual Studio Code Tools for Machine Learning, från att skapa ett experiment, träna en modell och operationalisera en webbtjänst.
services: machine-learning
author: ahgyger
ms.author: ahgyger
manager: haining
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/15/2017
ms.openlocfilehash: 16379b94a751ccd61a76ce1d3dda6a3ff1515751
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38767076"
---
# <a name="visual-studio-code-tools-for-ai"></a>Visual Studio Code Tools för AI
Visual Studio Code Tools för AI är ett tillägg i Visual Studio Code för att skapa, testa och distribuera djupinlärnings- och AI-lösningar. Tillägget är smidigt integrerat med Azure Machine Learning, till exempel en vy över körningshistorik med information om prestanda för tidigare träningar och anpassade mått. Det har också en utforskarvy med exempel för att söka efter och starta nya projekt med [Microsoft Cognitive Toolkit (kallades tidigare CNTK)](http://www.microsoft.com/en-us/cognitive-toolkit), [Google TensorFlow](https://www.tensorflow.org) och andra djupinlärningsramverk. Den innehåller även en utforskare för beräkningsmål som gör att du kan skicka jobb för att träna modeller i fjärrmiljöer som Azure Virtual Machines eller Linux-servrar med GPU. 
 
## <a name="getting-started"></a>Komma igång 
Innan du börjar måste du ladda ned och installera [Visual Studio Code](https://code.visualstudio.com/Download). När du har öppnat Visual Studio Code utför du följande steg:
1. Klicka på tilläggsikonen i Aktivitetsfältet. 
2. Sök efter Visual Studio Code Tools för AI. 
3. Klicka på knappen **Installera**. 
4. Efter installationen klickar du på knappen **Läs in på nytt**. 

När Visual Studio Code har lästs in på nytt är tillägget aktivt. [Läs mer om hur du installerar tillägg](https://code.visualstudio.com/docs/editor/extension-gallery).

## <a name="exploring-project-samples"></a>Utforska projektexempel
Visual Studio Code Tools för AI levereras med en exempelutforskare. Med exempelutforskaren kan du enkelt hitta exempel och prova dem med bara ett par klick. Så här öppnar du utforskaren:   
1. Öppna kommandopaletten (Visa > **Kommandopalett** eller **Ctrl+Shift+P**).
2. Skriv ”AI Sample”. 
3. Du får en rekommendation om ”AI: Open Azure ML Sample Explorer”. Välj den och tryck på Retur. 

Du kan också klicka på ikonen för exempelutforskaren.

## <a name="creating-a-new-project-from-the-sample-explorer"></a>Skapa ett nytt projekt från exempelutforskaren 
Du kan söka bland olika exempel och visa mer information om dem. Låt oss söka tills vi hittar exemplet ”Klassificera Iris”. Gör så här om du vill skapa ett nytt projekt baserat på det här exemplet:
1. Klicka på installationsknappen i projektexemplet och följ anvisningarna för att skapa ett nytt projekt. 
2. Välj ett namn för projektet, till exempel ”Iris”.
3. Välj en mappsökväg för att skapa projektet och tryck på Retur. 
4. Välj en befintlig arbetsyta och tryck på Retur.

Därmed skapas projektet.

> [!TIP]
> Du måste vara inloggad för att kunna komma åt din Azure-resurs. Ange ”az login” i den inbäddade terminal och följ anvisningarna. 

## <a name="submitting-experiment-with-the-new-project"></a>Skicka experiment med det nya projektet
Med det nya projektet öppnat i Visual Studio Code skickar vi ett jobb till vårt andra beräkningsmål (lokal och virtuell dator med Docker).
I Visual Studio Code Tools för AI finns flera sätt att skicka ett experiment. 
1. Snabbmenyn (högerklicka) - **AI: Submit Job** (AI: Skicka jobb).
2. Från kommandopaletten: ”AI: Submit Job” (AI: Skicka jobb).
3. Du kan också köra kommandot direkt med Azure CLI, Machine Learning-kommandon med den inbäddade terminalen.

Öppna iris_sklearn.py, högerklicka och välj **AI: Submit Job** (AI: Skicka jobb).
1. Välj din plattform: ”Azure Machine Learning”.
2. Välj din körningskonfiguration: ”Docker-Python”.

> [!NOTE]
> Om det är första gången du skickar ett jobb får du ett meddelande om att "ingen Machine Learning-konfiguration hittades, skapar...". En JSON-fil öppnas. Spara den (**Ctrl + S**).

När jobbet har skickats visar den inbäddade terminalen förloppet för körningarna. 

## <a name="view-list-of-jobs"></a>Visa listan över jobb
När jobben har skickats kan du visa en lista med jobb från körningshistoriken.
1. Öppna kommandopaletten (Visa > **Kommandopalett** eller **Ctrl+Shift+P**).
2. Ange ”AI List” (AI-lista).
3. Du får en rekommendation om ”AI: List Jobs” (AI: Lista jobb). Markera det och tryck på Retur.

Jobblistevyn öppnas och alla körningar och relaterad information visas.

## <a name="view-job-details"></a>Visa jobbinformation
Klicka på den första körningen i listan i jobblistevyn.
Om du vill visa mer detaljerade resultat för ett jobb klickar du **jobb-ID:t** för att visa mer information. 

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Så här konfigurerar du Azure Machine Learning för att arbeta med en utvecklingsmiljö (IDE)](./how-to-configure-your-IDE.md)
