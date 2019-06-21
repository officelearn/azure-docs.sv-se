---
title: Recept på Docker-behållare
titleSuffix: Azure Cognitive Services
description: Använd dessa behållare recept för att skapa Cognitive Services-behållare som kan återanvändas. Behållare kan byggas med vissa eller alla konfigurationsinställningar så att de inte behövs när behållaren har startats. När du har den här nytt lager av behållare (med inställningar) och du har testat lokalt kan lagra du behållaren i ett behållarregister. När behållaren startar behöver bara de inställningar som för närvarande inte lagras i behållaren.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: dapine
ms.openlocfilehash: 8a9d0d9f35c30636f4b9150c63b64098071e12e9
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67271484"
---
# <a name="create-containers-for-reuse"></a>Skapa behållare för återanvändning

Använd dessa behållare recept för att skapa Cognitive Services-behållare som kan återanvändas. Behållare kan byggas med vissa eller alla konfigurationsinställningar så att de är _inte_ behövs när behållaren har startats.

När du har den här nytt lager av behållare (med inställningar) och du har testat lokalt kan lagra du behållaren i ett behållarregister. När behållaren startar behöver bara de inställningar som för närvarande inte lagras i behållaren. Behållaren privat register innehåller konfiguration kan du skicka dessa inställningar i.

## <a name="docker-run-syntax"></a>Docker kör syntax

Alla `docker run` exemplen i det här dokumentet förutsätter en Windows-konsolen med en `^` rad fortsättning tecken. Tänk på följande för eget bruk:

* Ändra inte argumentens ordning om du inte är bekant med docker-behållare.
* Om du använder ett annat operativsystem än Windows eller en konsol än Windows-konsolen, Använd rätt konsolen/terminal, mappen syntax för monterar och tecknet för fortsättning för konsolen och system.  Eftersom behållaren Cognitive Services är en Linux-operativsystem, använder target montera en Linux-style mappen syntax.
* `docker run` exemplen använder katalogen av den `c:` enhet för att undvika eventuella behörighetskonflikter på Windows. Om du vill använda en viss katalog som den inkommande katalogen kan du behöva ge docker tjänsten behörighet.

## <a name="store-no-configuration-settings-in-image"></a>Store inga konfigurationsinställningar i bild

I exempel `docker run` kommandon för varje tjänst lagra inte alla konfigurationsinställningar i behållaren. När du startar behållaren från konsolen eller registry-tjänsten, måste dessa inställningar att skicka in. Behållaren privat register innehåller konfiguration kan du skicka dessa inställningar i.

## <a name="reuse-recipe-store-all-configuration-settings-with-container"></a>Återanvända recept: lagra alla konfigurationsinställningar med behållare

Om du vill lagra alla konfigurationsinställningar, skapa en `Dockerfile` med dessa inställningar.

Problem med den här metoden:

* Den nya behållaren har en separat namn och tagg från originalförpackningen.
* För att kunna ändra dessa inställningar behöver du ändra värden i Dockerfile, återskapa avbildningen och publicera i registret.
* Om någon får åtkomst till ditt behållarregister eller din lokala värd, kan de köra behållaren och använder Cognitive Services-slutpunkter.
* Om din Cognitive Service inte kräver inkommande monterar, inte lägga till den `COPY` rader till din Dockerfile.

Skapa Dockerfile och hämta från den befintliga Cognitive Services-behållare som du vill använda, och sedan använda docker-kommandon i Dockerfile för att ange eller hämta information behållaren måste.

Det här exemplet:

* Anger fakturering slutpunkten, `{BILLING_ENDPOINT}` från värden miljö nyckeln använder `ENV`.
* Anger fakturering API-nyckeln, `{ENDPOINT_KEY}` från värden användarens miljö nyckeln med hjälp av ”ENV.

### <a name="reuse-recipe-store-billing-settings-with-container"></a>Återanvända recept: lagra fakturering inställningar med behållare

Det här exemplet visar hur du skapar Text Analytics sentiment behållare från en Dockerfile.

```Dockerfile
FROM mcr.microsoft.com/azure-cognitive-services/sentiment:latest
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
```

Skapa och köra behållaren [lokalt](#how-to-use-container-on-your-local-host) eller från din [privat register behållare](#how-to-add-container-to-private-registry) efter behov.

### <a name="reuse-recipe-store-billing-and-mount-settings-with-container"></a>Återanvända recept: lagra fakturering och Monteringsinställningar med behållare

Det här exemplet visar hur du använder Språkförståelse, sparar fakturerings- och modeller från Dockerfile.

* Kopior modellen Språkförståelse (LUIS) fil från värdens filen med `COPY`.
* LUIS-behållaren stöder mer än en modell. Om alla modeller lagras i samma mapp, behöver du alla något `COPY` instruktionen.
* Kör docker-filen från modellen indatakatalog relativa överordnade. I följande exempel kör den `docker build` och `docker run` kommandon från relativa överordnade `/input`. Först `/input` på den `COPY` kommandot är värddatorns katalog. Andra `/input` är behållarens katalog.

```Dockerfile
FROM <container-registry>/<cognitive-service-container-name>:<tag>
ENV billing={BILLING_ENDPOINT}
ENV apikey={ENDPOINT_KEY}
ENV EULA=accept
COPY /input /input
```

Skapa och köra behållaren [lokalt](#how-to-use-container-on-your-local-host) eller från din [privat register behållare](#how-to-add-container-to-private-registry) efter behov.

## <a name="how-to-use-container-on-your-local-host"></a>Hur du använder behållare på din lokala värd

För att skapa Docker-filen, Ersätt `<your-image-name>` med det nya namnet på avbildningen sedan använda:

```console
docker build -t <your-image-name> .
```

Kör avbildningen och ta bort den när behållaren stoppas (`--rm`):

```console
docker run --rm <your-image-name>
```

## <a name="how-to-add-container-to-private-registry"></a>Hur du lägger till behållaren privat register

Följ dessa steg för att använda Dockerfile och placera den nya avbildningen i ditt privata behållarregister.  

1. Skapa en `Dockerfile` med texten från återanvändning recept. En `Dockerfile` har inte ett tillägg.

1. Ersätt alla värden i hakparenteser med dina egna värden.

1. Skapa filen till en bild på kommandoraden eller terminal, med följande kommando. Ersätt värdena i vinkelparenteser, `<>`, med dina egna namn och tagg.  

    Alternativet taggen `-t`, är ett sätt att lägga till information om vad du har ändrat för behållaren. Till exempel ett behållarnamn på `modified-LUIS` anger originalförpackningen har varit lager. Ett taggnamn av `with-billing-and-model` visar hur behållaren Språkförståelse (LUIS) har ändrats.

    ```Bash
    docker build -t <your-new-container-name>:<your-new-tag-name> .
    ```

1. Logga in på Azure CLI från en konsol. Det här kommandot öppnar en webbläsare och kräver autentisering. När autentiseringen är klar kan du stänga webbläsaren och fortsätta arbeta i konsolen.

    ```azure-cli
    az login
    ```

1. Logga in på ditt privata register med Azure CLI från en konsol.

    Ersätt värdena i vinkelparenteser, `<my-registry>`, med ditt eget namn i registret.  

    ```azure-cli
    az acr login --name <my-registry>
    ```

    Du kan också logga in med docker-inloggning om du har tilldelats ett huvudnamn för tjänsten.

    ```Bash
    docker login <my-registry>.azurecr.io
    ```

1. Tagga behållaren med den privata register-platsen. Ersätt värdena i vinkelparenteser, `<my-registry>`, med ditt eget namn i registret. 

    ```Bash
    docker tag <your-new-container-name>:<your-new-tag-name> <my-registry>.azurecr.io/<your-new-container-name-in-registry>:<your-new-tag-name>
    ```

    Om du inte använder ett taggnamn `latest` är underförstått.

1. Skicka den nya avbildningen till ditt privata behållarregister. När du visar ditt privata behållarregister kommer behållarens namn som används i följande CLI-kommando att namnet på databasen.

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