---
title: Avvikelseidentifiering Java-app - kognitiva Microsoft-tjänster | Microsoft Docs
description: 'Utforska en Java-app som använder API: et för Avvikelseidentifiering identifiering i kognitiva Microsoft-tjänster. Skicka ursprungliga datapunkter-API: et och få ett förväntat värde och avvikelseidentifiering punkter.'
services: cognitive-services
author: wenya
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: wenya
ms.openlocfilehash: 228d440da358eba1322e2228c54f21e925e36ecd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353322"
---
# <a name="anomaly-detection-java-application"></a>Avvikelseidentifiering identifiering Java-program

Den här artikeln visas hur du använder ett enkelt Java-program för att anropa API: et för Avvikelseidentifiering identifiering.  
Exemplet skickar serien tidsdata Avvikelseidentifiering identifiering-API: et med din prenumeration nyckel och hämtar alla avvikelseidentifiering punkter och förväntat värde för varje datapunkt från API: et.

## <a name="prerequisites"></a>Förutsättningar

### <a name="platform-requirements"></a>Plattformskrav

Den här självstudiekursen har utvecklats med [IntelliJ IDEA](https://www.jetbrains.com/idea). Och du måste också installera [Java Development Kit (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/index.html) version 1.8 +, och en uppdaterad ['s Apache Maven](http://maven.apache.org/) skapa verktyget.

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Prenumerera på Avvikelseidentifiering och få en nyckel för prenumeration 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]
 

## <a name="download-the-tutorial-project"></a>Hämta projektet självstudiekursen

1. Gå till MicrosoftAnomalyDetection [Java databasen](https://github.com/MicrosoftAnomalyDetection/java-sample).
2. Klicka på klonen eller knappen ladda ned.
3. Klicka på Hämta ZIP om du vill hämta en .zip-fil i självstudiekursen projektet.

<a name="Step1"></a>
### <a name="open-the-tutorial-project"></a>Öppna projektet självstudiekursen

1. Extrahera .zip-filen i självstudiekursen projektet.
2. Klicka i IntelliJ IDEA **fil > Öppna**, visas dialogrutan Öppna filen eller projekt.
3. Välj rotsökvägen för det hämtade projektet och klicka på OK.
4. Expandera i panelen projekt **src > main > java**.
5. Dubbelklicka på com.microsoft.cognitiveservice.anomalydetection.Main.java för att läsa in filen i redigeraren.

<a name="Step2"></a>
### <a name="replace-subscriptionkey-and-uri-region"></a>Ersätt subscriptionKey och URI region

```
// **********************************************
// *** Update or verify the following values. ***
// **********************************************

// Replace the subscriptionKey string value with your valid subscription key.
public static final String subscriptionKey = "<Subscription Key>";

public static final String uriBase = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

```

<a name="Step3"></a>
### <a name="build-and-run-the-tutorial-project"></a>Skapa och köra projektet självstudiekursen

1. Ta fram menyn genom att högerklicka någonstans i com.microsoft.cognitiveservice.anomalydetection.Main.java källa kod-flik. 
2. Välj Kör Main.main()
3. Resultatet av exempelbegäran ska returneras och visas i terminal.

### <a name="result-of-the-tutorial-project"></a>Resultatet av kursen projektet

[!INCLUDE [diagrams](../includes/diagrams.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [REST API-referens](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
