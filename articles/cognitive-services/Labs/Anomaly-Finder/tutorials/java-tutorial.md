---
title: Avvikelseidentifiering Java-app – Microsoft Cognitive Services | Microsoft Docs
description: 'Utforska en Java-app som använder API: T för Avvikelseidentifiering identifiering i Microsoft Cognitive Services. Skicka ursprungliga datapunkter till API: et och få det förväntade värdet och avvikelseidentifiering punkter.'
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: ac26e29f4a839f69b123489600c2c83fe395c48a
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/03/2018
ms.locfileid: "48247913"
---
# <a name="anomaly-detection-java-application"></a>Avvikelseidentifiering identifiering av Java-program

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Den här artikeln visar hur du använder ett enkelt Java-program för att anropa API: T för Avvikelseidentifiering identifiering.  
I exempel skickar time series-data till API: T för Avvikelseidentifiering identifiering med din prenumerationsnyckel och sedan hämtar alla avvikelser punkter och förväntat värde för varje datapunkt från API: et.

## <a name="prerequisites"></a>Förutsättningar

### <a name="platform-requirements"></a>Plattformskrav

Den här självstudien har utvecklats med [IntelliJ IDEA](https://www.jetbrains.com/idea). Och du måste också installera [Java Development Kit (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/index.html) version 1.8 +, och en uppdaterad ['s Apache Maven](http://maven.apache.org/) skapa verktyget.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Prenumerera på identifiering av avvikelser och få en prenumerationsnyckel 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]
 

## <a name="download-the-tutorial-project"></a>Ladda ned självstudieprojektet

1. Gå till MicrosoftAnomalyDetection [Java databasen](https://github.com/MicrosoftAnomalyDetection/java-sample).
2. Klicka på klonen eller knappen ladda ned.
3. Klicka på ladda ned ZIP om du vill hämta en ZIP-fil med självstudieprojektet.

<a name="Step1"></a>
### <a name="open-the-tutorial-project"></a>Öppna självstudieprojektet

1. Extrahera .zip-filen för självstudieprojektet.
2. I IntelliJ IDEA klickar du på **fil > Öppna**, visas dialogrutan Öppna fil eller projekt.
3. Rotsökvägen för extraherat projekt och sedan klicka på OK.
4. I panelen projekt expanderar **src > huvudsakliga > java**.
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

1. Ta fram menyn genom att högerklicka någonstans i com.microsoft.cognitiveservice.anomalydetection.Main.java källa kod-flik. 
2. Välj Kör Main.main()
3. Resultatet av exemplet begäran ska returneras och visas i terminalen.

### <a name="result-of-the-tutorial-project"></a>Resultatet av självstudieprojektet

[!INCLUDE [diagrams](../includes/diagrams.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API-referens](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
