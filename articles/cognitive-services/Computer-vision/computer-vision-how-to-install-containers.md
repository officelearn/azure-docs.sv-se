---
title: Hur du installerar och kör behållare
titlesuffix: Computer Vision - Cognitive Services - Azure
description: Så här hämtar, installerar och kör behållare för visuellt innehåll i den här genomgången självstudien.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 67dbf3bdf6631785fc876283847e36349e857a77
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634650"
---
# <a name="install-and-run-containers"></a>Installera och köra containrar

Skapa behållare är en metod för distribution av programvara där ett program eller tjänst kommer som en behållaravbildning. Konfiguration och beroenden för programmet eller tjänsten ingår i behållaravbildningen. Behållaravbildningen kan sedan distribueras på en behållarvärd med lite eller ingen ändring. Behållare är isolerade från varandra och det underliggande operativsystemet, med mindre avtryck än en virtuell dator. Behållare kan instansieras behållaravbildningar för kortsiktig uppgifter och tas bort när den inte längre behövs.

Identifiera Text-delen av visuellt innehåll är också tillgängligt som en Docker-behållare. Det kan du identifiera och extrahera utskrivna text från bilder för olika objekt med olika ytor och bakgrunder, till exempel kvitton och affischer visitkort.  
> [!IMPORTANT]
> Behållaren identifiera Text fungerar för närvarande bara på engelska.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="preparation"></a>Förberedelse

Du måste uppfylla följande krav innan du använder behållaren identifiera Text:

**Docker-motorn**: du måste ha Docker-motorn installerad lokalt. Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms), och [Windows](https://docs.docker.com/docker-for-windows/). På Windows, måste Docker konfigureras för att stödja Linux-behållare. Docker-behållare kan också distribueras direkt till [Azure Kubernetes Service](/azure/aks/), [Azure Container Instances](/azure/container-instances/), eller till en [Kubernetes](https://kubernetes.io/) kluster som distribueras till [Azure Stack](/azure/azure-stack/). Mer information om hur du distribuerar Kubernetes i Azure Stack finns i [distribuera Kubernetes i Azure Stack](/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).

Docker måste konfigureras för att tillåta behållarna för att ansluta till och skicka faktureringsdata till Azure.

**Liknar processen med Microsoft Container Registry och Docker**: du bör ha grundläggande kunskaper om både Microsoft Container Registry och Docker-begrepp som register, databaser, behållare, och behållaravbildningar samt kunskap grundläggande `docker` kommandon.  

Få en genomgång om grunderna för Docker och behållare finns i den [översikt över Docker](https://docs.docker.com/engine/docker-overview/).

### <a name="server-requirements-and-recommendations"></a>Krav och rekommendationer

Identifiera Text-behållare kräver minst 1 processorkärna, minst 2,6 GHz (gigahertz) eller snabbare och 8 gigabyte (GB) för allokerat minne, men vi rekommenderar minst 2 CPU-kärnor och 8 GB allokerat minne.

## <a name="request-access-to-the-private-container-registry"></a>Begär åtkomst till privat behållarregister

Du måste först slutför och skicka den [Cognitive Services Vision behållare formulär](https://aka.ms/VisionContainersPreview) att begära åtkomst till behållaren identifiera Text. Formuläret begär information om dig, ditt företag och Användarscenario som du använder behållaren. När skickat, Azure Cognitive Services-teamet har granskat formuläret för att säkerställa att du uppfyller villkoren för åtkomst till det privata behållarregistret.

> [!IMPORTANT]
> Du måste använda en e-postadress som är associerad med ett Microsoft-konto (MSA) eller Azure Active Directory (Azure AD)-konto i formuläret.

Om din begäran har godkänts får du ett e-postmeddelande med instruktioner om hur du hämtar dina autentiseringsuppgifter och komma åt det privata behållarregistret.

## <a name="create-a-computer-vision-resource-on-azure"></a>Skapa en resurs för visuellt innehåll på Azure

Du måste skapa en resurs för visuellt innehåll i Azure om du vill identifiera Text-behållare. När du skapar resursen kan använda du sedan prenumeration nyckel och slutpunkt URL: en från resursen att skapa en instans av behållaren. Läs mer om att skapa instanser en behållare, [skapa en instans av en behållare från en hämtade behållaravbildning](#instantiate-a-container-from-a-downloaded-container-image).

Utför följande steg för att skapa och hämta information från en Azure-resurs:

1. Skapa en Azure-resurs i Azure-portalen.  
   Om du vill identifiera Text-behållare måste du först skapa en resurs med motsvarande visuellt i Azure-portalen. Mer information finns i [Snabbstart: skapa ett Cognitive Services-konto i Azure portal](../cognitive-services-apis-create-account.md).

1. Hämta slutpunktsnyckeln för URL: en och -prenumeration för Azure-resursen.  
   När Azure-resursen har skapats måste du använda slutpunktsnyckeln för URL: en och -prenumeration från den här resursen för att skapa en instans av behållaren motsvarande identifiera Text. Du kan kopiera URL: en och prenumeration slutpunktsnyckeln från respektive sidorna Snabbstart och nycklar för visuellt innehåll resursen på Azure-portalen.

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

Behållaravbildning för behållaren identifiera Text är tillgänglig från ett privat Docker-behållarregister, med namnet `containerpreview.azurecr.io`, i Azure Container Registry. Behållaravbildning för identifiera Text behållaren måste laddas ned från lagringsplatsen till kör behållaren lokalt.

Använd den [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) för att ladda ned en behållaravbildning från databasen. Till exempel för att hämta den senaste identifiera Text-behållaravbildningen från databasen, använder du följande kommando:

```Docker
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text:latest
```

En fullständig beskrivning av tillgängliga taggar för behållaren identifiera Text, se [identifiera Text](https://go.microsoft.com/fwlink/?linkid=2018655) på Docker Hub.

> [!TIP]
> Du kan använda den [docker-avbildningar](https://docs.docker.com/engine/reference/commandline/images/) kommando för att lista din hämtade behållaravbildningar. Till exempel visar följande kommando ID, databasen och taggen för varje hämtade behållaravbildningen, formaterad som en tabell:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>Skapa en instans av en behållare från en hämtade behållaravbildning

Använd den [docker kör](https://docs.docker.com/engine/reference/commandline/run/) kommando för att skapa en instans av en behållare från en hämtade behållaravbildning. Till exempel följande kommando:

* Skapar en instans av en behållare från behållaravbildningen identifiera Text
* Allokerar två CPU-kärnor och 8 gigabyte (GB) minne
* Visar TCP-port 5000 och allokerar en pseudo-TTY för behållaren
* Automatiskt att ta bort behållaren när avslutas

```docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 2 containerpreview.azurecr.io/microsoft/cognitive-services-recognize-text Eula=accept Billing=https://westus.api.cognitive.microsoft.com/vision/v2.0 ApiKey=0123456789
```

När instansierats, kan du anropa åtgärder från behållaren med hjälp av behållarvärd URI. Följande värden URI representerar till exempel identifiera Text-behållare som har initierats i föregående exempel:

```http
http://localhost:5000/
```

> [!IMPORTANT]
> Du kan komma åt den [OpenAPI-specifikation](https://swagger.io/docs/specification/about/) (tidigare Swagger-specifikation), som beskriver de åtgärder som stöds av en initierad behållare från den `/swagger` relativ URI för den behållaren. Följande URI: N innehåller till exempel åtkomst till den OpenAPI-specifikationen för behållaren identifiera Text som har initierats i föregående exempel:
>
>  ```http
>  http://localhost:5000/swagger
>  ```

Du kan antingen [anropa REST API-åtgärder](https://docs.microsoft.com/azure/cognitive-services/computer-vision/vision-api-how-to-topics/howtocallvisionapi) tillgängliga från en behållare för synkront eller asynkront söker efter text, eller Använd den [Azure Cognitive Services datorn Vision SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision) klienten biblioteket för att anropa dessa åtgärder.  
> [!IMPORTANT]
> Du måste ha Azure Cognitive Services datorn Vision SDK version 3.2.0 eller senare om du vill använda klientbiblioteket med din behållare.

### <a name="asynchronous-text-recognition"></a>Asynkron textigenkänning

Du kan använda den `POST /vision/v2.0/recognizeText` och `GET /vision/v2.0/textOperations/*{id}*` operations tillsammans att asynkront känna igen utskrivna text i en bild, liknar hur tjänsten för visuellt innehåll använder de motsvarande REST-åtgärderna. Identifiera Text behållaren endast känner igen utskrivna text, inte handskriven text just nu, så den `mode` parametern normalt angetts för tjänståtgärd visuellt ignoreras av behållaren identifiera Text.

### <a name="synchronous-text-recognition"></a>Synkron textigenkänning

Du kan använda den `POST /vision/v2.0/recognizeTextDirect` åtgärden att synkront känna igen utskrivna text i en bild. Eftersom åtgärden sker synkront, förfrågans brödtext för den här åtgärden är samma som för den `POST /vision/v2.0/recognizeText` igen, men svaret brödtext för den här åtgärden är samma som returneras av den `GET /vision/v2.0/textOperations/*{id}*` igen.

### <a name="billing"></a>Fakturering

Identifiera Text behållaren skickar faktureringsinformation till Azure, med en motsvarande visuellt-resurs på ditt Azure-konto. Följande alternativ används för faktureringsändamål behållaren identifiera Text:

| Alternativ | Beskrivning |
|--------|-------------|
| `ApiKey` | API-nyckel för den resursen för visuellt innehåll som används för att spåra faktureringsinformation.<br/>Värdet för det här alternativet måste anges till en API-nyckel för den etablerade datorn Vision Azure-resursen som anges i `Billing`. |
| `Billing` | Slutpunkten för visuellt innehåll resursen används för att spåra faktureringsinformation.<br/>Värdet för det här alternativet måste anges till en etablerade datorn Vision Azure-resurs-URI-slutpunkten.|
| `Eula` | Anger att du har godkänt licensen för behållaren.<br/>Värdet för det här alternativet måste anges till `accept`. |

> [!IMPORTANT]
> Alla tre alternativ måste anges med giltiga värden eller behållaren startar inte.

Mer information om alternativen finns i [konfigurera behållare](computer-vision-resource-container-config.md).

## <a name="summary"></a>Sammanfattning

I den här artikeln beskrivs begrepp och arbetsflöde för att ladda ned, installera och visuellt behållare som körs. Sammanfattningsvis:

* Visuellt innehåll ger en Linux-behållare för Docker för att identifiera och extrahera utskrivna text.
* Avbildningar hämtas från ett privat behållarregister i Azure.
* Behållaravbildningar som körs i Docker.
* Du kan använda antingen SDK eller REST API för att anropa åtgärder i visuellt behållare genom att ange värden URI: N för behållaren.
* Du måste ange faktureringsinformation när instanser skapades av en behållare.
* ** Cognitive Services-behållare är inte licensierad för att köra inte är ansluten till Azure för att mäta. Kunder måste du aktivera behållarna för att kommunicera faktureringsinformation med tjänsten Avläsning av programvara vid alla tidpunkter. Cognitive Services-behållare Skicka inte kunddata (t.ex. bild eller text som analyseras) till Microsoft.  

## <a name="next-steps"></a>Nästa steg

* Granska [konfigurera behållare](computer-vision-resource-container-config.md) för konfigurationsinställningar
* Granska [visuellt översikt](Home.md) vill veta mer om att känna igen utskrivna och handskriven text  
* Referera till den [API för visuellt innehåll](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) mer information om de metoder som stöds av behållaren.
* Referera till [vanliga frågor (och svar FAQ)](FAQ.md) att lösa problem som rör visuellt funktioner.
