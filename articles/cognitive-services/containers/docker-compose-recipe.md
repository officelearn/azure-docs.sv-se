---
title: Använda Docker Compose för att distribuera flera containrar
titleSuffix: Azure Cognitive Services
description: Lär dig hur du distribuerar flera Cognitive Services-behållare. Den här artikeln visar hur du dirigerar flera Docker-behållaravbildningar med hjälp av Docker Compose.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: dapine
ms.openlocfilehash: bfbaa03469ee04ff900a215aadd8c814efcba761
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79037524"
---
# <a name="use-docker-compose-to-deploy-multiple-containers"></a>Använda Docker Compose för att distribuera flera containrar

Den här artikeln visar hur du distribuerar flera Azure Cognitive Services-behållare. Mer specifikt får du lära dig hur du använder Docker Compose för att dirigera flera Docker-behållaravbildningar.

> [Docker Compose](https://docs.docker.com/compose/) är ett verktyg för att definiera och köra Docker-program med flera behållare. I Compose använder du en YAML-fil för att konfigurera programmets tjänster. Sedan skapar och startar du alla tjänster från konfigurationen genom att köra ett enda kommando.

Det kan vara användbart att dirigera flera behållaravbildningar på en enda värddator. I den här artikeln samlar vi Read and Form Recognizer-behållarna.

## <a name="prerequisites"></a>Krav

Den här proceduren kräver flera verktyg som måste installeras och köras lokalt:

* En Azure-prenumeration. Om du inte har ett, skapa ett [gratis konto](https://azure.microsoft.com/free/) innan du börjar.
* [Docker Motor](https://www.docker.com/products/docker-engine). Bekräfta att Docker CLI fungerar i ett konsolfönster.
* En Azure-resurs med rätt prisnivå. Endast följande prisnivåer fungerar med den här behållaren:
  * **Datorseende** resurs med F0 eller Standard prisnivå endast.
  * **Formulär recognizer-resurs** med endast F0- eller standardprisnivå.
  * **Cognitive Services-resurs** med prisnivån S0.

## <a name="request-access-to-the-container-registry"></a>Begär åtkomst till behållarregistret

Fyll i och skicka [formuläret Begäran om begäran om begäran om cognitive services-talbehållare](https://aka.ms/speechcontainerspreview/). 

[!INCLUDE [Request access to the container registry](../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="docker-compose-file"></a>Docker Compose-fil

YAML-filen definierar alla tjänster som ska distribueras. Dessa tjänster är `DockerFile` beroende av antingen en eller en befintlig behållaravbildning. I det här fallet använder vi två förhandsgranskningsbilder. Kopiera och klistra in följande YAML-fil och spara den som *docker-compose.yaml*. Ange lämpliga **apikey-,** **fakturerings-** och **EndpointUri-värden** i filen.

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
         source: E:\publicpreview\output
         target: /output
       - type: bind
         source: E:\publicpreview\input
         target: /input
    ports:
      - "5010:5000"

  ocr:
    image: "containerpreview.azurecr.io/microsoft/cognitive-services-read"
    environment:
      eula: accept
      apikey: # < Your computer vision API key >
      billing: # < Your computer vision billing URL >
    ports:
      - "5021:5000"
```

> [!IMPORTANT]
> Skapa katalogerna på värddatorn som **volumes** anges under volymnoden. Den här metoden krävs eftersom katalogerna måste finnas innan du försöker montera en avbildning med hjälp av volymbindningar.

## <a name="start-the-configured-docker-compose-services"></a>Starta de konfigurerade Docker Compose-tjänsterna

En Docker Compose-fil gör det möjligt att hantera alla faser i en definierad tjänsts livscykel: starta, stoppa och återskapa tjänster; visa tjänstens status. och logga streaming. Öppna ett kommandoradsgränssnitt från projektkatalogen (där docker-compose.yaml-filen finns).

> [!NOTE]
> För att undvika fel, se till att värddatorn delar enheter med Docker Engine på rätt sätt. Om *E:\publicpreview* till exempel används som en katalog i *filen docker-compose.yaml* delar du enhet **E** med Docker.

Kör följande kommando för att starta (eller starta om) alla tjänster som definierats i *filen docker-compose.yaml* från kommandoradsgränssnittet:

```console
docker-compose up
```

Första gången Docker kör **docker-komponera upp** kommandot med hjälp av denna konfiguration, drar den avbildningar som konfigurerats under **tjänsten** noden och sedan hämtar och monterar dem:

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
Pulling ocr (containerpreview.azurecr.io/microsoft/cognitive-services-read:)...
latest: Pulling from microsoft/cognitive-services-read
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

När bilderna har hämtats startas bildtjänsterna:

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

## <a name="verify-the-service-availability"></a>Verifiera tjänstens tillgänglighet

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

Här är några exempel på utdata:

```
IMAGE ID            REPOSITORY                                                                 TAG
2ce533f88e80        containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer   latest
4be104c126c5        containerpreview.azurecr.io/microsoft/cognitive-services-read              latest
```

### <a name="test-containers"></a>Testbehållare

Öppna en webbläsare på värddatorn och gå till **localhost** med hjälp av den angivna porten http://localhost:5021/swagger/index.htmlfrån *filen docker-compose.yaml,* till exempel . Du kan till exempel använda **try it-funktionen** i API:et för att testa slutpunkten för formulärre recognizeer. Båda behållarna swagger sidor bör vara tillgängliga och testbara.

![Behållare för formulärdekänningsbehållare](media/form-recognizer-swagger-page.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Cognitive Services-behållare](../cognitive-services-container-support.md)
