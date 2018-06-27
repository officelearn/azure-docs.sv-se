---
title: Känslo-API Java för Snabbstart för Android | Microsoft Docs
description: Hämta information och en kodexempel för att hjälpa dig att snabbt komma igång med Java Känslo-API för Android i kognitiva Services.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 05/23/2017
ms.author: anroth
ms.openlocfilehash: 0e7d3991b195a83a8b87e306b3b34fbed2098581
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37018034"
---
# <a name="emotion-api-java-for-android-quick-start"></a>Känslo-API Java för Snabbstart för Android

> [!IMPORTANT]
> Förhandsgranskning av video API avslutas på 30 oktober 2017. Prova den nya [Video indexeraren API Preview](https://azure.microsoft.com/services/cognitive-services/video-indexer/) enkelt extrahera insikter från videor och förbättra innehållsidentifiering upplevelser, till exempel sökresultat med hjälp av tal, ytor, tecken och känslor. [Läs mer](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

Den här artikeln innehåller information och en kodexempel för att hjälpa dig att snabbt komma igång med den [Känslo känner igen metoden](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa) i Känslo-API Android klientbiblioteket. Exemplet visar hur du kan använda Java för att identifiera emotikoner uttryckt av personer. 

## <a name="prerequisites"></a>Förutsättningar
* Hämta Java Känslo-API för Android SDK [här](https://github.com/Microsoft/Cognitive-emotion-android)
* Hämta din kostnadsfria prenumerationen nyckel [här](https://azure.microsoft.com/try/cognitive-services/)

## <a name="recognize-emotions-java-for-android-example-request"></a>Identifiera emotikoner Java för Android Exempelbegäran

```java
// // This sample uses the Apache HTTP client from HTTP Components (http://hc.apache.org/httpcomponents-client-ga/)
import java.net.URI;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.DefaultHttpClient;
import org.apache.http.util.EntityUtils;

public class Main
{
    public static void main(String[] args)
    {
        HttpClient httpClient = new DefaultHttpClient();

        try
        {
            // NOTE: You must use the same region in your REST call as you used to obtain your subscription keys.
            //   For example, if you obtained your subscription keys from westcentralus, replace "westus" in the 
            //   URL below with "westcentralus".
            URIBuilder uriBuilder = new URIBuilder("https://westus.api.cognitive.microsoft.com/emotion/v1.0/recognize");

            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers. Replace the example key below with your valid subscription key.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", "13hc77781f7e4b19b5fcdd72a8df7156");

            // Request body. Replace the example URL below with the URL of the image you want to analyze.
            StringEntity reqEntity = new StringEntity("{ \"url\": \"http://example.com/images/test.jpg\" }");
            request.setEntity(reqEntity);

            HttpResponse response = httpClient.execute(request);
            HttpEntity entity = response.getEntity();

            if (entity != null)
            {
                System.out.println(EntityUtils.toString(entity));
            }
        }
        catch (Exception e)
        {
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="recognize-emotions-sample-response"></a>Identifiera emotikoner Exempelsvar
Lyckade anrop returnerar en matris av framsidan poster och deras associerade känslo poäng rangordnas efter ansikte rektangel storlek i fallande ordning. Ett tomt svar anger att ingen ytor upptäcktes. Känslo-posten innehåller följande fält:
* faceRectangle - placering av ansikte i avbildningen.
* resultat - Place poängen för varje yta i bilden. 

```json
application/json 
[
  {
    "faceRectangle": {
      "left": 68,
      "top": 97,
      "width": 64,
      "height": 97
    },
    "scores": {
      "anger": 0.00300731952,
      "contempt": 5.14648448E-08,
      "disgust": 9.180124E-06,
      "fear": 0.0001912825,
      "happiness": 0.9875571,
      "neutral": 0.0009861537,
      "sadness": 1.889955E-05,
      "surprise": 0.008229999
    }
  }
]
