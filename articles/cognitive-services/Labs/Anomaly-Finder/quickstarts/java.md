---
title: 'Hur du använder API: et för Avvikelseidentifiering Finder med Java - kognitiva Microsoft-tjänster | Microsoft Docs'
description: Hämta information och exempel på kod för att snabbt komma igång med Java och Anomaly Detection i kognitiva tjänster.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: kefre
ms.openlocfilehash: 8152c23e6c5332d243d851be56bab1e4085dbe5a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353346"
---
# <a name="use-the-anomaly-finder-api-with-java"></a>Använda Avvikelseidentifiering Finder API med Java

Den här artikeln innehåller information och kodexempel som hjälper dig att snabbt komma igång med API: et för Avvikelseidentifiering identifiering med Java för att utföra uppgiften för att få resultat av identifiering av avvikelseidentifiering för tid series-data.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-the-anomaly-detection-api-using-java"></a>Hämta avvikelseidentifiering punkter med Avvikelseidentifiering identifiering API använder Java

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data"></a>Exempel på tid series-data

Exempel på den tid som datapunkter i serien är som följer.

[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-java-example"></a>Analysera data och få avvikelseidentifiering punkter Java-exempel

Utför följande steg om du vill köra exemplet:
1. Skapa en ny kommandoradsverktyget App.
2. Ersätt Main-klassen med följande kod (behålla `package` instruktioner).
3. Ersätt den `[YOUR_SUBSCRIPTION_KEY]` värde med en giltig prenumeration nyckel.
4. Ersätt den `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` med exemplet eller egna datapunkter.
5. Ladda ned dessa globala bibliotek från Maven-databasen till den `lib` katalogen i ditt projekt:
   * `org.apache.httpcomponents:httpclient:4.5.2`
6. Kör 'Main.

```java

import org.apache.http.HttpEntity;
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;
import org.apache.http.util.EntityUtils;

public class Main {
    // **********************************************
    // *** Update or verify the following values. ***
    // **********************************************

    // Replace the subscriptionKey string value with your valid subscription key.
    public static final String subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";

    public static final String uriBase = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

    public static void main(String[] args) {
        final String content = "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]";

        CloseableHttpClient client = HttpClients.createDefault();
        HttpPost request = new HttpPost(uriBase);

        // Request headers.
        request.setHeader("Content-Type", "application/json");
        request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

        try {
            StringEntity params = new StringEntity(content);
            request.setEntity(params);

            CloseableHttpResponse response = client.execute(request);
            try {
                HttpEntity respEntity = response.getEntity();
                if (respEntity != null) {
                    System.out.println("----------");
                    System.out.println(response.getStatusLine());
                    System.out.println("Response content is :\n");
                    System.out.println(EntityUtils.toString(respEntity, "utf-8"));
                    System.out.println("----------");
                }
            } catch (Exception respEx) {
                respEx.printStackTrace();
            } finally {
                response.close();
            }

        } catch (Exception ex) {
            System.err.println("Exception on Anomaly Detection: " + ex.getMessage());
            ex.printStackTrace();
        } finally {
            try {
                client.close();
            } catch (Exception e) {
                System.err.println("Exception on closing HttpClient: " + e.getMessage());
                e.printStackTrace();
            }
        }
    }
}

```

### <a name="example-response"></a>Exempelsvar

Ett lyckat svar returneras i JSON. Exempelsvar är som följer.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Java-app](../tutorials/java-tutorial.md)
