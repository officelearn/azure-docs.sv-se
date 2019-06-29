---
title: Docker compose behållare recept
titleSuffix: Azure Cognitive Services
description: Lär dig hur du distribuerar flera Cognitive Services-behållare. Den här proceduren visar hur du samordnar flera Docker-behållaravbildningar med Docker Compose.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 8afb7e866bc2a5fefe28a71653c4a2a87fdc7a5b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445795"
---
# <a name="use-multiple-containers-in-a-private-network-with-docker-compose"></a>Använda flera behållare i ett privat nätverk med Docker Compose

Lär dig hur du distribuerar flera Cognitive Services-behållare. Den här proceduren visar hur du samordnar flera Docker-behållaravbildningar med Docker Compose.

> [Docker Compose](https://docs.docker.com/compose/) är ett verktyg för att definiera och köra program med flera behållare Docker. Skriv använder en YAML-fil för att konfigurera din programtjänster. Med ett enda kommando, skapa och starta alla tjänster från din konfiguration.

När det är lämpligt, kan det vara intressant att samordna flera behållaravbildningar på en enda värddator. I den här artikeln har ska vi hämta tjänsterna identifiera Text och formuläret Igenkännande tillsammans.

## <a name="prerequisites"></a>Förutsättningar

Den här proceduren kräver flera verktyg som måste installeras och köras lokalt.

* Använda en Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* [Docker-motorn](https://www.docker.com/products/docker-engine) och verifiera att Docker CLI fungerar i ett konsolfönster.
* En Azure-resurs med rätt prisnivån. Inte alla prisnivåer arbetar du med den här behållaren:
  * **Visuellt** resurs med F0 eller standardpriserna nivåerna endast.
  * **Formuläret Igenkännande** resurs med F0 eller standardpriserna nivåerna endast.
  * **Cognitive Services** resurs med S0 prisnivå.

## <a name="request-access-to-the-container-registry"></a>Begär åtkomst till behållarregistret

Slutför och skicka den [Cognitive Services tal behållare formulär](https://aka.ms/speechcontainerspreview/) att begära åtkomst till behållaren. 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>Docker compose-fil

YAML-fil definierar alla tjänster som ska distribueras. De här tjänsterna är beroende av antingen en `DockerFile` eller en befintlig behållaravbildning i det här fallet använder vi två Förhandsgranska bilder. Kopiera och klistra in följande YAML-fil och spara den som *docker compose.yaml*. Ange rätt _apikey_, _fakturering_, och _endpoint URI_ värdena i den _docker-compose.yml_ filen nedan.

```yaml
version: '3.7'
services:
  forms:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer"
    environment:
       eula: accept
       billing: # < Your form recognizer billing URL >
       apikey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionApiKey: # < Your form recognizer API key >
       FormRecognizer__ComputerVisionEndpointUri: # < Your form recognizer URI >
    volumes:
       - type: bind
         source: e:\publicpreview\output
         target: /output
       - type: bind
         source: e:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text"
    environment:
      eula: accept
      apikey: # < Your recognize text API key >
      billing: # < Your recognize text billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> Skapa kataloger på värddatorn som anges under den `volumes` noden. Detta är nödvändigt eftersom katalogerna måste finnas innan du försöker montera en avbildning med volym-bindningar.

## <a name="start-the-configured-docker-compose-services"></a>Starta konfigurerade docker compose tjänster

Docker compose-fil kan hanteringen av alla definierade tjänstens supportlivscykel; Visa status för tjänsten och loggströmningen från Starta/Stoppa och återskapa tjänster. Öppna ett kommandoradsgränssnitt från projektkatalogen (där den *docker compose.yaml* filen finns).

> [!NOTE]
> Du kan undvika fel genom att se till att värddatorn är korrekt dela enheter med den **Docker-motorn**. Till exempel om *e:\publicpreview* används som en katalog i den *docker compose.yaml* dela den *E enhet* med docker.

Från kommandoradsgränssnittet, kör du följande kommando för att starta (eller starta om) alla tjänster som definierats i den *docker compose.yaml*:

```console
docker-compose up
```

Den första körningstid den `docker-compose up` med den här konfigurationen **Docker** hämtar de avbildningar som konfigurerats den `services` nod – ladda ned/montera dem:

```console
Pulling forms (containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer:)...
latest: Pulling from microsoft/cognitive-services-form-recognizer
743f2d6c1f65: Pull complete
72befba99561: Pull complete
2a40b9192d02: Pull complete
c7715c9d5c33: Pull complete
f0b33959f1c4: Pull complete
b8ab86c6ab26: Pull complete
41940c21ed3c: Pull complete
e3d37dd258d4: Pull complete
cdb5eb761109: Pull complete
fd93b5f95865: Pull complete
ef41dcbc5857: Pull complete
4d05c86a4178: Pull complete
34e811d37201: Pull complete
Pulling ocr (containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:)...
latest: Pulling from microsoft/cognitive-services-recognize-text
f476d66f5408: Already exists
8882c27f669e: Already exists
d9af21273955: Already exists
f5029279ec12: Already exists
1a578849dcd1: Pull complete
45064b1ab0bf: Download complete
4bb846705268: Downloading [=========================================>         ]  187.1MB/222.8MB
c56511552241: Waiting
e91d2aa0f1ad: Downloading [==============================================>    ]  162.2MB/176.1MB
```

Bilderna laddas ned och sedan bild tjänsterna är igång.

```console
Starting docker_ocr_1   ... done
Starting docker_forms_1 ... doneAttaching to docker_ocr_1, docker_forms_1forms_1  | forms_1  | forms_1  | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
forms_1  | 
forms_1  | 
forms_1  | Using '/input' for reading models and other read-only data.
forms_1  | Using '/output/forms/812d811d1bcc' for writing logs and other output data.
forms_1  | Logging to console.
forms_1  | Submitting metering to 'https://westus2.api.cognitive.microsoft.com/'.
forms_1  | WARNING: No access control enabled!
forms_1  | warn: Microsoft.AspNetCore.Server.Kestrel[0]
forms_1  |       Overriding address(es) 'http://+:80'. Binding to endpoints defined in UseKestrel() instead.
forms_1  | Hosting environment: Production
forms_1  | Content root path: /app/forms
forms_1  | Now listening on: http://0.0.0.0:5000
forms_1  | Application started. Press Ctrl+C to shut down.
ocr_1    | 
ocr_1    | 
ocr_1    | Notice: This Preview is made available to you on the condition that you agree to the Supplemental Terms of Use for Microsoft Azure Previews [https://go.microsoft.com/fwlink/?linkid=2018815], which supplement your agreement [https://go.microsoft.com/fwlink/?linkid=2018657] governing your use of Azure. If you do not have an existing agreement governing your use of Azure, you agree that your agreement governing use of Azure is the Microsoft Online Subscription Agreement [https://go.microsoft.com/fwlink/?linkid=2018755] (which incorporates the Online Services Terms [https://go.microsoft.com/fwlink/?linkid=2018760]). By using the Preview you agree to these terms.
ocr_1    |
ocr_1    | 
ocr_1    | Logging to console.
ocr_1    | Submitting metering to 'https://westcentralus.api.cognitive.microsoft.com/'.
ocr_1    | WARNING: No access control enabled!
ocr_1    | Hosting environment: Production
ocr_1    | Content root path: /
ocr_1    | Now listening on: http://0.0.0.0:5000
ocr_1    | Application started. Press Ctrl+C to shut down.
```

## <a name="verify-the-service-availability"></a>Kontrollera tjänsttillgänglighet

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

Nedan visas ett exempel på utdata:

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text    latest
```

### <a name="test-the-recognize-text-container"></a>Testa behållaren identifiera text

Öppna en webbläsare på värddatorn och gå till `localhost` med den angivna porten från den *docker compose.yaml*, till exempel `http://localhost:5021/swagger/index.html`. Du kan använda prova den här funktionen för API för att testa identifiera text-slutpunkt.

![Identifiera Text Swagger](media/recognize-text-swagger-page.png)

### <a name="test-the-form-recognizer-container"></a>Testa behållaren formuläret Igenkännande

Öppna en webbläsare på värddatorn och gå till `localhost` med den angivna porten från den *docker compose.yaml*, till exempel `http://localhost:5010/swagger/index.html`. Du kan använda prova den här funktionen för API för att testa formuläret Igenkännande slutpunkten.

![Form Recognizer Swagger](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Cognitive Services-behållare](../cognitive-services-container-support.md)