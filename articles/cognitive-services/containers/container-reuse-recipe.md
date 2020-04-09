---
title: Recept för Docker-behållare
titleSuffix: Azure Cognitive Services
description: Lär dig hur du skapar, testar och lagrar behållare med några eller alla dina konfigurationsinställningar för distribution och återanvändning.
services: cognitive-services
author: aahill
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 7380ff58d033a68565de7e419ff318f7bdec121d
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875086"
---
# <a name="create-containers-for-reuse"></a>Skapa containrar för återanvändning

Använd dessa behållarrecept för att skapa Cognitive Services-behållare som kan återanvändas. Behållare kan byggas med vissa eller alla konfigurationsinställningar så att de _inte_ behövs när behållaren startas.

När du har det här nya lagret av behållare (med inställningar) och du har testat det lokalt kan du lagra behållaren i ett behållarregister. När behållaren startar behöver den bara de inställningar som för närvarande inte lagras i behållaren. Den privata registerbehållaren ger konfigurationsutrymme där du kan skicka in dessa inställningar.

## <a name="docker-run-syntax"></a>Syntax för Docker-körning

Alla `docker run` exempel i det här dokumentet `^` förutsätter en Windows-konsol med ett linjefortsättningstecken. Tänk på följande för eget bruk:

* Ändra inte ordningen på argumenten om du inte är väl förtrogen med dockerbehållare.
* Om du använder ett annat operativsystem än Windows, eller en annan konsol än Windows-konsolen, använder du rätt konsol/terminal, mappsyntax för fästen och linjefortsättningstecken för konsolen och systemet.  Eftersom Cognitive Services-behållaren är ett Linux-operativsystem använder målfästet en mappsyntax för Linux-stil.
* `docker run`exempel använder katalogen `c:` utanför enheten för att undvika behörighetskonflikter i Windows. Om du behöver använda en viss katalog som indatakatalog kan du behöva ge dockertjänsten behörighet.

## <a name="store-no-configuration-settings-in-image"></a>Lagra inga konfigurationsinställningar i bilden

`docker run` Exempelkommandona för varje tjänst lagrar inga konfigurationsinställningar i behållaren. När du startar behållaren från en konsol- eller registertjänst måste dessa konfigurationsinställningar gå in. Den privata registerbehållaren ger konfigurationsutrymme där du kan skicka in dessa inställningar.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Återanvänd recept: lagra alla konfigurationsinställningar med behållare

Skapa en `Dockerfile` med dessa inställningar för att lagra alla konfigurationsinställningar.

Problem med detta tillvägagångssätt:

* Den nya behållaren har ett separat namn och en separat tagg från den ursprungliga behållaren.
* För att ändra dessa inställningar måste du ändra värdena för Dockerfile, återskapa avbildningen och publicera om till registret.
* Om någon får åtkomst till ditt behållarregister eller din lokala värd kan de köra behållaren och använda slutpunkterna Cognitive Services.
* Om din kognitiva tjänst inte kräver indatafästen `COPY` ska du inte lägga till raderna i Dockerfile.

Skapa Dockerfile, hämta från den befintliga Cognitive Services-behållaren som du vill använda och använd sedan dockerkommandon i Dockerfile för att ange eller hämta information som behållaren behöver.

Det här exemplet:

* Ställer in faktureringsslutpunkten `{BILLING_ENDPOINT}` från värdens `ENV`miljönyckel med .
* Ställer in API-nyckeln `{ENDPOINT_KEY}` för fakturering, från värdens miljönyckel med "ENV.Sets the billing API-key, from the host's environment key using 'ENV.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Återanvänd recept: lagra faktureringsinställningar med behållare

Det här exemplet visar hur du skapar textanalysens sentimentbehållare från en Dockerfile.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

Skapa och kör behållaren [lokalt](#how-to-use-container-on-your-local-host) eller från den [privata registerbehållaren](#how-to-add-container-to-private-registry) efter behov.

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Återanvänd recept: lagra fakturerings- och monteringsinställningar med behållare

Det här exemplet visar hur du använder språk understanding, spara fakturering och modeller från Dockerfile.

* Kopierar standardfilen För språk understanding (LUIS) från `COPY`värdens filsystem med .
* LUIS-behållaren stöder mer än en modell. Om alla modeller lagras i samma mapp `COPY` behöver ni alla en sats.
* Kör dockerfilen från den relativa överordnade för modellens indatakatalog. I följande exempel kör `docker build` `docker run` du kommandona och `/input`från den relativa överordnade till . Det `/input` första `COPY` på kommandot är värddatorns katalog. Den `/input` andra är behållarens katalog.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

Skapa och kör behållaren [lokalt](#how-to-use-container-on-your-local-host) eller från den [privata registerbehållaren](#how-to-add-container-to-private-registry) efter behov.

## <a name="how-to-use-container-on-your-local-host"></a>Så här använder du behållaren på din lokala värd

Om du vill skapa `<your-image-name>` Docker-filen ersätter du med bildens nya namn och använder sedan:

```console
docker build -t <your-image-name> .
```

Så här kör du avbildningen och`--rm`tar bort den när behållaren stannar ( ):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Så här lägger du till behållare i privat register

Följ dessa steg för att använda Dockerfile och placera den nya avbildningen i ditt privata behållarregister.  

1. Skapa `Dockerfile` en med texten från återanvändningsreceptet. A `Dockerfile` har ingen förlängning.

1. Ersätt eventuella värden i vinkelparenteserna mot dina egna värden.

1. Skapa filen till en bild på kommandoraden eller terminalen med hjälp av följande kommando. Ersätt värdena i `<>`vinkelparenteserna, med ditt eget behållarnamn och -tagg.  

    Taggalternativet `-t`är ett sätt att lägga till information om vad du har ändrat för behållaren. Ett behållarnamn `modified-LUIS` för anger till exempel att den ursprungliga behållaren har skiktats. Ett taggnamn `with-billing-and-model` för anger hur BEHÅLLAREN För språk understanding (LUIS) har ändrats.

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Logga in på Azure CLI från en konsol. Det här kommandot öppnar en webbläsare och kräver autentisering. När du har autentiserats kan du stänga webbläsaren och fortsätta arbeta i konsolen.

    ```azurecli
    az login
    ```

1. Logga in på ditt privata register med Azure CLI från en konsol.

    Ersätt värdena i vinkelparenteserna `<my-registry>`med ditt eget registernamn.  

    ```azurecli
    az acr login --name <my-registry>
    ```

    Du kan också logga in med dockerinloggning om du har tilldelats ett huvudnamn för tjänsten.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Tagga behållaren med den privata registerplatsen. Ersätt värdena i vinkelparenteserna `<my-registry>`med ditt eget registernamn. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Om du inte använder ett `latest` taggnamn är underförstått underförstått.

1. Skicka den nya avbildningen till ditt privata behållarregister. När du visar ditt privata behållarregister kommer behållarnamnet som används i följande CLI-kommando att vara namnet på databasen.

    ```Bash
    docker push <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa och använda Azure Container Instance](azure-container-instance-recipe.md)

<!--
## Store input and output configuration settings

Bake in input params only

FROM containerpreview.azurecr.io/microsoft/cognitive-services-luis:<tag>
COPY luisModel1 /input/
COPY luisModel2 /input/

## Store all configuration settings

If you are a single manager of the container, you may want to store all settings in the container. The new, resulting container will not need any variables passed in to run. 

Issues with this approach:

* In order to change these settings, you will have to change the values of the Dockerfile and rebuild the file. 
* If someone gets access to your container registry or your local host, they can run the container and use the Cognitive Services endpoints. 

The following _partial_ Dockerfile shows how to statically set the values for billing and model. This example uses the 

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing=<billing value>
ENV apikey=<apikey value>
COPY luisModel1 /input/
COPY luisModel2 /input/
```

->