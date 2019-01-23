---
title: Installera genom att köra behållare
titlesuffix: Face - Azure Cognitive Services
description: Så här hämtar, installerar och kör behållare för ansikte i den här genomgången självstudien.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 01/22/2019
ms.author: diberry
ms.openlocfilehash: 310311c22be25960b15f20d573624f50b0f618b1
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54474820"
---
# <a name="install-and-run-containers"></a>Installera och köra containrar

Skapa behållare är en metod för distribution av programvara där ett program eller tjänst kommer som en behållaravbildning. Konfiguration och beroenden för programmet eller tjänsten ingår i behållaravbildningen. Behållaravbildningen kan sedan distribueras på en behållarvärd med lite eller ingen ändring. Behållare är isolerade från varandra och det underliggande operativsystemet, med mindre avtryck än en virtuell dator. Behållare kan instansieras behållaravbildningar för kortsiktig uppgifter och tas bort när den inte längre behövs.

Ansikte ger en standardiserad Linux-behållare för Docker, med namnet ansikte, som identifierar ansikten i bilder och identifierar attribut, inklusive ansiktslandmärken (till exempel och rörliga och ögon), kön, ålder och andra dator-förväntad ansiktsdrag. Förutom identifiering Kontrollera ansikte om två ansikten i samma bild eller olika bilder är samma med hjälp av ett förtroenderesultat eller jämföra ansikten mot en databas för att se om en likartade eller identiska ansikte finns redan. Det kan även sortera liknande ansikten i grupper, med hjälp av delade visual egenskaper.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="preparation"></a>Förberedelse

Du måste uppfylla följande krav innan du använder Face-behållaren:

**Docker-motorn**: Du måste ha Docker-motorn installerad lokalt. Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms), och [Windows](https://docs.docker.com/docker-for-windows/). På Windows, måste Docker konfigureras för att stödja Linux-behållare. Docker-behållare kan också distribueras direkt till [Azure Kubernetes Service](../../aks/index.yml), [Azure Container Instances](../../container-instances/index.yml), eller till en [Kubernetes](https://kubernetes.io/) kluster som distribueras till [Azure Stack](../../azure-stack/index.yml). Mer information om hur du distribuerar Kubernetes i Azure Stack finns i [distribuera Kubernetes i Azure Stack](../../azure-stack/user/azure-stack-solution-template-kubernetes-deploy.md).

Docker måste konfigureras för att tillåta behållarna för att ansluta till och skicka faktureringsdata till Azure.

**Liknar processen med Microsoft Container Registry och Docker**: Du bör ha grundläggande kunskaper om både Microsoft Container Registry och Docker-begrepp som register, databaser, behållare, och behållaravbildningar samt kunskaper om grundläggande `docker` kommandon.  

Få en genomgång om grunderna för Docker och behållare finns i den [översikt över Docker](https://docs.docker.com/engine/docker-overview/).

### <a name="container-requirements-and-recommendations"></a>Behållarkrav och rekommendationer

Ansikts-behållare kräver minst 1 processorkärna, minst 2,6 GHz (gigahertz) eller snabbare och 4 gigabyte (GB) för allokerat minne, men vi rekommenderar minst 2 CPU-kärnor och 6 GB allokerat minne.

## <a name="request-access-to-the-private-container-registry"></a>Begär åtkomst till privat behållarregister

Du först slutföra och skicka den [Cognitive Services Vision behållare formulär](https://aka.ms/VisionContainersPreview) att begära åtkomst till behållaren ansikte. Formuläret begär information om dig, ditt företag och Användarscenario som du använder behållaren. När skickat, Azure Cognitive Services-teamet har granskat formuläret för att säkerställa att du uppfyller villkoren för åtkomst till det privata behållarregistret.

> [!IMPORTANT]
> Du måste använda en e-postadress som är associerad med ett Microsoft-konto (MSA) eller Azure Active Directory (Azure AD)-konto i formuläret.

Om din begäran har godkänts får du ett e-postmeddelande med instruktioner om hur du hämtar dina autentiseringsuppgifter och komma åt det privata behållarregistret.

## <a name="create-a-face-resource-on-azure"></a>Skapa en Ansikts-resurs på Azure

Du måste skapa en Ansikts-resurs i Azure om du vill använda Ansikts-behållare. När du skapar resursen kan använda du sedan prenumeration nyckel och slutpunkt URL: en från resursen att skapa en instans av behållaren. Läs mer om att skapa instanser en behållare, [skapa en instans av en behållare från en hämtade behållaravbildning](#instantiate-a-container-from-a-downloaded-container-image).

Utför följande steg för att skapa och hämta information från en Ansikts-resurs:

1. Skapa en Ansikts-resurs i Azure-portalen.  
   Om du vill använda Ansikts-behållare, måste du först skapa en motsvarande Ansikts-resurs i Azure-portalen. Mer information finns i [ Snabbstart: Skapa ett Cognitive Services-konto i Azure portal](../cognitive-services-apis-create-account.md).

1. Hämta slutpunktsnyckeln för URL: en och -prenumeration för Azure-resursen.  
   När Azure-resursen har skapats måste du använda slutpunktsnyckeln för URL: en och -prenumeration från den här resursen för att skapa en instans av motsvarande Ansikts-behållaren. Du kan kopiera URL: en och prenumeration slutpunktsnyckeln från respektive sidorna Snabbstart och nycklar för Ansikts-resursen på Azure-portalen.

## <a name="log-in-to-the-private-container-registry"></a>Logga in på privat behållarregister

Det finns flera sätt att autentisera med det privata behållarregistret för Cognitive Services-behållare, men den rekommenderade metoden från kommandoraden är med hjälp av den [Docker CLI](https://docs.docker.com/engine/reference/commandline/cli/).

Använd den [docker-inloggning](https://docs.docker.com/engine/reference/commandline/login/) kommandot, som visas i följande exempel för att logga in på `containerpreview.azurecr.io`, privat behållarregister för Cognitive Services-behållare. Ersätt *\<användarnamn\>* med användarnamnet och *\<lösenord\>* med det angivna inloggningsuppgifterna lösenordet som du fick från Azure Cognitive Services-teamet.

```docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Om du har skyddat dina autentiseringsuppgifter i en textfil kan du sammanfoga innehållet i texten fil, använda den `cat` för att den `docker login` kommandot som visas i följande exempel. Ersätt *\<{passwordFile\>* med sökvägen och namnet på den textfil som innehåller lösenordet och *\<användarnamn\>* med användarnamnet tillhandahålla i dina autentiseringsuppgifter.

```docker
cat <passwordFile> | docker login containerpreview.azurecr.io -u <username> --password-stdin
```

## <a name="download-container-images-from-the-private-container-registry"></a>Hämta behållaravbildningar från privata container registry

Behållaravbildning för Ansikts-behållaren är tillgänglig från ett privat Docker-behållarregister, med namnet `containerpreview.azurecr.io`, i Azure Container Registry. Behållaravbildning för Ansikts-behållaren måste laddas ned från lagringsplatsen till kör behållaren lokalt.

Använd den [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) för att ladda ned en behållaravbildning från databasen. Till exempel för att hämta den senaste Ansikts-behållaravbildningen från databasen, använder du följande kommando:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

En fullständig beskrivning av tillgängliga taggar för Ansikts-behållaren finns [identifiera Text](https://go.microsoft.com/fwlink/?linkid=2018655) på Docker Hub.

> [!TIP]
> Du kan använda den [docker-avbildningar](https://docs.docker.com/engine/reference/commandline/images/) kommando för att lista din hämtade behållaravbildningar. Till exempel visar följande kommando ID, databasen och taggen för varje hämtade behållaravbildningen, formaterad som en tabell:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>Skapa en instans av en behållare från en hämtade behållaravbildning

Använd den [docker kör](https://docs.docker.com/engine/reference/commandline/run/) kommando för att skapa en instans av en behållare från en hämtade behållaravbildning. Till exempel följande kommando:

* Skapar en instans av en behållare från Ansikts-behållaravbildning
* Allokerar två CPU-kärnor och 6 gigabyte (GB) minne
* Visar TCP-port 5000 och allokerar en pseudo-TTY för behållaren
* Automatiskt att ta bort behållaren när avslutas

```Docker
docker run --rm -it -p 5000:5000 --memory 6g --cpus 2 containerpreview.azurecr.io/microsoft/cognitive-services-face Eula=accept Billing=https://westus.api.cognitive.microsoft.com/face/v1.0 ApiKey=0123456789
```

> [!IMPORTANT]
> Den `Eula`, `Billing`, och `ApiKey` alternativ måste anges för att skapa en instans av behållaren; i annat fall startar inte behållaren.  Mer information finns i [fakturering](#billing).

När instansierats, kan du anropa åtgärder från behållaren med hjälp av behållarvärd URI. Till exempel representerar följande värden URI Ansikts-behållare som har initierats i föregående exempel:

```http
http://localhost:5000/
```

> [!TIP]
> Du kan komma åt den [OpenAPI-specifikation](https://swagger.io/docs/specification/about/) (tidigare Swagger-specifikation), som beskriver de åtgärder som stöds av en initierad behållare från den `/swagger` relativ URI för den behållaren. Följande URI: N innehåller till exempel åtkomst till den OpenAPI-specifikationen för Ansikts-behållare som har initierats i föregående exempel:
>
>  ```http
>  http://localhost:5000/swagger
>  ```

Du kan antingen [anropa REST API-åtgärder](https://docs.microsoft.com/azure/cognitive-services/face/face-api-how-to-topics/howtodetectfacesinimage) tillgängliga från behållaren eller Använd den [Azure Cognitive Services Ansikts-klientbiblioteket](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/) -klientbiblioteket för att anropa dessa åtgärder.  
> [!IMPORTANT]
> Du måste ha Azure Cognitive Services Ansikts-klientbiblioteket version 2.0 eller senare om du vill använda klientbiblioteket med din behållare.

Den enda skillnaden mellan att en viss åtgärd från din behållare och anropa att samma åtgärd från en motsvarande tjänst på Azure är när du ska använda värden URI: N för din behållare, snarare än värden URI: N för en Azure-region för att anropa åtgärden. Om du vill använda en Ansikts-instans som körs i regionen västra USA Azure för att identifiera ansikten, skulle du till exempel anropa följande REST API-åtgärden:

```http
POST https://westus.api.cognitive.microsoft.com/face/v1.0/detect
```

Om du vill använda en Ansikts-behållare som körs på den lokala datorn under standardkonfigurationen för att identifiera ansikten, skulle du anropa följande REST API-åtgärden:

```http
POST http://localhost:5000/face/v1.0/detect
```

### <a name="billing"></a>Fakturering

Ansikts-behållaren skickar faktureringsinformation till Azure, med en motsvarande Ansikts-resurs på ditt Azure-konto. Följande kommandoradsalternativ som används av Ansikts-behållaren i debiteringssyfte:

| Alternativ | Beskrivning |
|--------|-------------|
| `ApiKey` | API-nyckel för Ansikts-resurs som används för att spåra faktureringsinformation.<br/>Värdet för det här alternativet måste anges till en API-nyckel för den etablerade ansikte Azure-resursen som anges i `Billing`. |
| `Billing` | Slutpunkten för Ansikts-resursen som används för att spåra faktureringsinformation.<br/>Värdet för det här alternativet måste anges till slutpunkten för en etablerad ansikte Azure-resurs-URI.|
| `Eula` | Anger att du har godkänt licensen för behållaren.<br/>Värdet för det här alternativet måste anges till `accept`. |

> [!IMPORTANT]
> Alla tre alternativ måste anges med giltiga värden eller behållaren startar inte.

Mer information om alternativen finns i [konfigurera behållare](face-resource-container-config.md).

## <a name="summary"></a>Sammanfattning

I den här artikeln beskrivs begrepp och arbetsflöde för att ladda ned, installera och köra Ansikts-behållare. Sammanfattningsvis:

* Ansikte tillhandahåller en Linux-behållare för Docker, med namnet smiley för att spåra ansikten eller identifiera ansikten med hjälp av en personer-databas.
* Avbildningar hämtas från ett privat behållarregister i Azure.
* Behållaravbildningar som körs i Docker.
* Du kan använda antingen SDK eller REST API för att anropa åtgärder i ansikte behållare genom att ange värden URI: N för behållaren.
* Du måste ange faktureringsinformation när instanser skapades av en behållare.

> [!IMPORTANT]
> Cognitive Services-behållare är inte licensierad för att köra inte är ansluten till Azure för att mäta. Kunder måste du aktivera behållarna för att kommunicera faktureringsinformation med tjänsten Avläsning av programvara vid alla tidpunkter. Cognitive Services-behållare Skicka inte kunddata (t.ex. bild eller text som analyseras) till Microsoft.

## <a name="next-steps"></a>Nästa steg

* Granska [konfigurera behållare](face-resource-container-config.md) för konfigurationsinställningar
* Granska [ansikte översikt](Overview.md) vill veta mer om att upptäcka och identifiera ansikten  
* Referera till den [Ansikts-API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) mer information om de metoder som stöds av behållaren.
* Referera till [vanliga frågor (och svar FAQ)](FAQ.md) att lösa problem som rör Ansikts-funktioner.
* Använder mer [Cognitive Services-behållare](../cognitive-services-container-support.md)