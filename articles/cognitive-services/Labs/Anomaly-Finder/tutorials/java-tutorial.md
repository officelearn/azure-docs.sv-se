---
title: 'Självstudier: Avvikelseidentifiering, Java'
titlesuffix: Azure Cognitive Services
description: Utforska en Java-app som använder API:et för avvikelseidentifiering. Skicka ursprungliga datapunkter till API:et och få det förväntade värdet och avvikelsepunkter.
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.subservice: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: ba66c96f9129b253fc5897c3a2eaaefb53056cea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60709911"
---
# <a name="tutorial-anomaly-detection-with-java-application"></a>Självstudier: Avvikelseidentifiering med Java-program

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Den här artikeln visar hur du använder ett enkelt Java-program för att anropa API:et för avvikelseidentifiering.  
I exemplet skickas tidsseriedata till API:et för avvikelseidentifiering tillsammans med din prenumerationsnyckel, och därefter hämtas alla avvikelsepunkter och det förväntade värdet för varje datapunkt från API:et.

## <a name="prerequisites"></a>Nödvändiga komponenter

### <a name="platform-requirements"></a>Plattformskrav

Den här självstudien har utvecklats med hjälp av [IntelliJ IDEA](https://www.jetbrains.com/idea). Och du måste också installera [Java Development Kit (JDK)](https://aka.ms/azure-jdks) version 1.8+ och ett uppdaterat [Apache Maven](https://maven.apache.org/)-verktyg.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Prenumerera på avvikelseidentifiering och få en prenumerationsnyckel 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]
 

## <a name="download-the-tutorial-project"></a>Ladda ned projektet för självstudien

1. Gå till [Java-databasen](https://github.com/MicrosoftAnomalyDetection/java-sample) MicrosoftAnomalyDetection.
2. Klicka på knappen Clone or download (Klona eller ladda ned).
3. Klicka på Download ZIP (Ladda ned ZIP) för att ladda ned en .zip-fil för självstudieprojektet.

<a name="Step1"></a>
### <a name="open-the-tutorial-project"></a>Öppna självstudieprojektet

1. Extrahera .zip-filen för självstudieprojektet.
2. I IntelliJ IDEA klickar du på **File > Open** (Arkiv > Öppna), så visas dialogrutan Open File or Project (Öppna fil eller projekt).
3. Välj rotsökvägen för det extraherade projektet och klicka sedan på OK.
4. I projektpanelen expanderar du **src > main > java**.
5. Dubbelklicka på com.microsoft.cognitiveservice.anomalydetection.Main.java för att läsa in filen i redigeraren.

<a name="Step2"></a>
### <a name="replace-subscriptionkey-and-uri-region"></a>Ersätt subscriptionKey och URI-region

```
// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
public static final String subscriptionKey = "<Subscription Key>";

public static final String uriBase = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

```

<a name="Step3"></a>
### <a name="build-and-run-the-tutorial-project"></a>Skapa och köra självstudieprojektet

1. Ta fram menyn genom att högerklicka någonstans på källkodsfliken com.microsoft.cognitiveservice.anomalydetection.Main.java. 
2. Välj Run 'Main.main()'
3. Resultatet av exempelbegäran returneras och visas i terminalen.

### <a name="result-of-the-tutorial-project"></a>Resultat av självstudieprojektet

[!INCLUDE [diagrams](../includes/diagrams.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API-referens](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
