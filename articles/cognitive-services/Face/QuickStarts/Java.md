---
title: 'Snabbstart: Identifiera ansikten i en bild med Azure REST API och Java'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten ska du använda Azure ansikts-REST API med Java för att identifiera ansikten i en bild.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: quickstart
ms.date: 11/09/2018
ms.author: pafarley
ms.openlocfilehash: 2c035734d443eba01af6f167681ae289401dbcb4
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2019
ms.locfileid: "54212880"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-java"></a>Snabbstart: Identifiera ansikten i en bild med REST API och Java

I den här snabbstarten ska du använda Azure ansikts-REST API med Java för att identifiera mänskliga ansikten i en bild.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar. 

## <a name="prerequisites"></a>Nödvändiga komponenter

- En ansikts-API-prenumerationsnyckel. Du kan hämta nycklar för en kostnadsfri utvärderingsprenumeration från [Testa Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Följ instruktionerna i [Skapa ett konto för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för att prenumerera på tjänsten Ansikts-API och få din nyckel.
- En valfri Java IDE.

## <a name="create-the-java-project"></a>Skapa Java-projekt

Skapa en ny Java-app för kommandoraden i din IDE och lägga till en **Main**-klass med en **main**-metod. Ladda därefter ned följande globala bibliotek från Maven-lagringsplatsen till katalogen `lib` i projektet:
* `org.apache.httpcomponents:httpclient:4.5.6`
* `org.apache.httpcomponents:httpcore:4.4.10`
* `org.json:json:20170516`
* `commons-logging:commons-logging:1.1.2`

## <a name="add-face-detection-code"></a>Lägga till kod för ansiktsavkänning

Öppna huvudklassen i ditt projekt. Här lägger du till den kod som behövs för att läsa in bilder och identifiera ansikten.

### <a name="import-packages"></a>Importera paket

Lägg till följande `import`-instruktioner överst i filen.

```java
// This sample uses Apache HttpComponents:
// http://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/
// https://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/

import java.net.URI;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.HttpClientBuilder;
import org.apache.http.util.EntityUtils;
import org.json.JSONArray;
import org.json.JSONObject;
```

### <a name="add-essential-fields"></a>Lägg till grundläggande fält

Lägg till följande fält i klassen **Main**. Dessa data anger hur du ansluter till ansiktsigenkänningstjänsten och var du hämtar indata. Du måste uppdatera fältet `subscriptionKey` med värdet för din prenumerationsnyckel, och du kan behöva ändra strängen `uriBase` så att den innehåller rätt regionsidentifierare (en lista över alla regionsslutpunkter finns i [dokument om Ansikts-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)). Du kan också ange värdet `imageWithFaces` till en sökväg som pekar på en annan bildfil.

Fältet `faceAttributes` är en lista över vissa typer av attribut. Det anger vilken information som ska hämtas om de identifierade ansiktena.

```Java
// Replace <Subscription Key> with your valid subscription key.
private static final String subscriptionKey = "<Subscription Key>";

// NOTE: You must use the same region in your REST call as you used to
// obtain your subscription keys. For example, if you obtained your
// subscription keys from westus, replace "westcentralus" in the URL
// below with "westus".
//
// Free trial subscription keys are generated in the "westus" region. If you
// use a free trial subscription key, you shouldn't need to change this region.
private static final String uriBase =
    "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect";

private static final String imageWithFaces =
    "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}";

private static final String faceAttributes =
    "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";
```

### <a name="call-the-face-detection-rest-api"></a>Anropa REST API för ansiktsavkänning

Lägg till följande metod till **Main**-metoden. Den skapar ett REST-anrop till Ansikts-API för att identifiera ansiktsinformation i fjärrbilden (`faceAttributes`-strängen anger vilka ansiktsattribut som ska hämtas). Sedan skriver den utdata till en JSON-sträng.

```Java
HttpClient httpclient = HttpClientBuilder.create().build();

try
{
    URIBuilder builder = new URIBuilder(uriBase);

    // Request parameters. All of them are optional.
    builder.setParameter("returnFaceId", "true");
    builder.setParameter("returnFaceLandmarks", "false");
    builder.setParameter("returnFaceAttributes", faceAttributes);

    // Prepare the URI for the REST API call.
    URI uri = builder.build();
    HttpPost request = new HttpPost(uri);

    // Request headers.
    request.setHeader("Content-Type", "application/json");
    request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

    // Request body.
    StringEntity reqEntity = new StringEntity(imageWithFaces);
    request.setEntity(reqEntity);

    // Execute the REST API call and get the response entity.
    HttpResponse response = httpclient.execute(request);
    HttpEntity entity = response.getEntity();
```

### <a name="parse-the-json-response"></a>Tolka JSON-svar

Direkt under föregående kod lägger du till följande block, som konverterar returnerade JSON-data till ett mer lättläst format innan du skriver ut dem till konsolen. Stäng slutligen TryCatch-blocket.

```Java
    if (entity != null)
    {
        // Format and display the JSON response.
        System.out.println("REST Response:\n");

        String jsonString = EntityUtils.toString(entity).trim();
        if (jsonString.charAt(0) == '[') {
            JSONArray jsonArray = new JSONArray(jsonString);
            System.out.println(jsonArray.toString(2));
        }
        else if (jsonString.charAt(0) == '{') {
            JSONObject jsonObject = new JSONObject(jsonString);
            System.out.println(jsonObject.toString(2));
        } else {
            System.out.println(jsonString);
        }
    }
}
catch (Exception e)
{
    // Display error message.
    System.out.println(e.getMessage());
}
```

## <a name="run-the-app"></a>Kör appen

Kompilera koden och kör den. Ett lyckat svar visar ansiktsinformation i lättläst JSON-format i konsolfönstret. Exempel:

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  },
  "faceAttributes": {
    "makeup": {
      "eyeMakeup": true,
      "lipMakeup": true
    },
    "facialHair": {
      "sideburns": 0,
      "beard": 0,
      "moustache": 0
    },
    "gender": "female",
    "accessories": [],
    "blur": {
      "blurLevel": "low",
      "value": 0.06
    },
    "headPose": {
      "roll": 0.1,
      "pitch": 0,
      "yaw": -32.9
    },
    "smile": 0,
    "glasses": "NoGlasses",
    "hair": {
      "bald": 0,
      "invisible": false,
      "hairColor": [
        {
          "color": "brown",
          "confidence": 1
        },
        {
          "color": "black",
          "confidence": 0.87
        },
        {
          "color": "other",
          "confidence": 0.51
        },
        {
          "color": "blond",
          "confidence": 0.08
        },
        {
          "color": "red",
          "confidence": 0.08
        },
        {
          "color": "gray",
          "confidence": 0.02
        }
      ]
    },
    "emotion": {
      "contempt": 0,
      "surprise": 0.005,
      "happiness": 0,
      "neutral": 0.986,
      "sadness": 0.009,
      "disgust": 0,
      "anger": 0,
      "fear": 0
    },
    "exposure": {
      "value": 0.67,
      "exposureLevel": "goodExposure"
    },
    "occlusion": {
      "eyeOccluded": false,
      "mouthOccluded": false,
      "foreheadOccluded": false
    },
    "noise": {
      "noiseLevel": "low",
      "value": 0
    },
    "age": 22.9
  },
  "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f"
}]
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat ett enkelt Java-konsolprogram som använder REST-anrop med Ansikts-API i Azure för att identifiera ansikten i en bild och returnera deras attribut. Därefter lär dig hur du gör mer med den här funktionen i en Android-app.

> [!div class="nextstepaction"]
> [Självstudier: Skapa en Android-app för att upptäcka och rama in ansikten](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)
