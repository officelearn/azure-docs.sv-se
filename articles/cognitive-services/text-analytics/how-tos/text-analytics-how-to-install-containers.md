---
title: Installera och köra behållare
titleSuffix: Text Analytics - Cognitive Services - Azure
description: Så här hämtar, installerar och kör behållare för textanalys i den här genomgången självstudien.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 11/14/2018
ms.author: diberry
ms.openlocfilehash: 99bdb42d9a0d86d0d2acc4a6272e0c802042e6b5
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51635047"
---
# <a name="install-and-run-containers"></a>Installera och köra behållare

Skapa behållare är en metod för distribution av programvara där ett program eller tjänst kommer som en behållaravbildning. Konfiguration och beroenden för programmet eller tjänsten ingår i behållaravbildningen. Behållaravbildningen kan sedan distribueras på en behållarvärd med lite eller ingen ändring. Behållare är isolerade från varandra och det underliggande operativsystemet, med mindre avtryck än en virtuell dator. Behållare kan instansieras behållaravbildningar för kortsiktig uppgifter och tas bort när den inte längre behövs.

Textanalys tillhandahåller följande uppsättning Docker-behållare som innehåller en delmängd av funktionerna:

| Container| Beskrivning |
|----------|-------------|
|Extrahering av nyckelfraser | Extraherar viktiga fraser för att identifiera de viktigaste aspekterna. Exempel: För den inmatade texten ”Maten var härlig och personalen var underbar” returnerar API:et de huvudsakliga diskussionsämnena: ”mat” och ”underbar personal”. |
|Språkidentifiering | Upp till 120 språk, identifierar och rapporterar vilket språk som indatatexten skrivs. Behållaren rapporterar en enda språkkod för varje dokument som ingår i begäran. Språkkoden paras med poäng som anger styrkan hos poängen. |
|Attitydanalys | Analyserar rå text efter ledtrådar om positiv eller negativ attityd. Detta API returnerar attitydpoäng mellan 0 och 1 för varje dokument, där 1 är det mest positiva. Analysis-modeller tränas före med hjälp av en omfattande mängd text och naturligt språk tekniker från Microsoft. För [utvalda språk](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages.md) kan API:et analysera och poängsätta råtext som du anger, och direkt returnera resultat till det anropande programmet. |

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="preparation"></a>Förberedelse

Du måste uppfylla följande krav innan du kan använda textanalys behållare:

**Docker-motorn**: du måste ha Docker-motorn installerad lokalt. Docker innehåller paket som konfigurerar Docker-miljön på [macOS](https://docs.docker.com/docker-for-mac/), [Linux](https://docs.docker.com/engine/installation/#supported-platforms), och [Windows](https://docs.docker.com/docker-for-windows/). På Windows, måste Docker konfigureras för att stödja Linux-behållare. Docker-behållare kan också distribueras direkt till [Azure Kubernetes Service](/azure/aks/), [Azure Container Instances](/azure/container-instances/), eller till en [Kubernetes](https://kubernetes.io/) kluster som distribueras till [Azure Stack](/azure/azure-stack/). Mer information om hur du distribuerar Kubernetes i Azure Stack finns i [distribuera Kubernetes i Azure Stack](/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy).

Docker måste konfigureras för att tillåta behållarna för att ansluta till och skicka faktureringsdata till Azure.

**Liknar processen med Microsoft Container Registry och Docker**: du bör ha grundläggande kunskaper om både Microsoft Container Registry och Docker-begrepp som register, databaser, behållare, och behållaravbildningar samt kunskap grundläggande `docker` kommandon.  

Få en genomgång om grunderna för Docker och behållare finns i den [översikt över Docker](https://docs.docker.com/engine/docker-overview/).

### <a name="server-requirements-and-recommendations"></a>Krav och rekommendationer

I följande tabell beskrivs de minsta och rekommenderade CPU-kärnorna minst 2,6 GHz (gigahertz) eller snabbare, och minne i gigabyte (GB), att tilldela för varje behållare för textanalys.

| Container | Minimum | Rekommenderas |
|-----------|---------|-------------|
|Extrahering av nyckelfraser | 1 kärna, 2 GB minne | 1 kärna, 4 GB minne |
|Språkidentifiering | 1 kärna, 2 GB minne | 1 kärna, 4 GB minne |
|Attitydanalys | 1 kärna, 8 GB minne | 1 kärna, 8 GB minne |

## <a name="download-container-images-from-microsoft-container-registry"></a>Hämta behållaravbildningar från Microsoft Container Registry

Behållaravbildningar för textanalys är tillgängliga från Microsoft Container Registry. I följande tabell visas databaser från Microsoft Container Registry för textanalys behållare. Varje databas innehåller en behållaravbildning som måste laddas ned för att köra behållaren lokalt.

| Container | Lagringsplats |
|-----------|------------|
|Extrahering av nyckelfraser | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
|Språkidentifiering | `mcr.microsoft.com/azure-cognitive-services/language` |
|Attitydanalys | `mcr.microsoft.com/azure-cognitive-services/sentiment` |

Använd den [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) för att ladda ned en behållaravbildning från en lagringsplats. Till exempel för att hämta den senaste nyckeln diskussionsämne behållaravbildningen från databasen, använder du följande kommando:

```Docker
docker pull mcr.microsoft.com/azure-cognitive-services/keyphrase:latest
```

En fullständig beskrivning av tillgängliga taggar för textanalys-behållare finns i följande behållare i Docker Hub:

* [Extrahering av diskussionsämne](https://go.microsoft.com/fwlink/?linkid=2018757)
* [Språkidentifiering](https://go.microsoft.com/fwlink/?linkid=2018759)
* [Attitydanalys](https://go.microsoft.com/fwlink/?linkid=2018654)

> [!TIP]
> Du kan använda den [docker-avbildningar](https://docs.docker.com/engine/reference/commandline/images/) kommando för att lista din hämtade behållaravbildningar. Till exempel visar följande kommando ID, databasen och taggen för varje hämtade behållaravbildningen, formaterad som en tabell:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>  ```
>

## <a name="instantiate-a-container-from-a-downloaded-container-image"></a>Skapa en instans av en behållare från en hämtade behållaravbildning

Använd den [docker kör](https://docs.docker.com/engine/reference/commandline/run/) kommando för att skapa en instans av en behållare från en hämtade behållaravbildning. Till exempel följande kommando:

* Skapar en instans av en behållare från behållaravbildningen Attitydanalys
* Allokerar en CPU-kärna och 8 gigabyte (GB) minne
* Visar TCP-port 5000 och allokerar en pseudo-TTY för behållaren
* Automatiskt att ta bort behållaren när avslutas

```Docker
docker run --rm -it -p 5000:5000 --memory 8g --cpus 1 mcr.microsoft.com/azure-cognitive-services/sentiment Eula=accept Billing=https://westus.api.cognitive.microsoft.com/text/analytics/v2.0 ApiKey=0123456789

```

> [!IMPORTANT]
> Den `Eula`, `Billing`, och `ApiKey` kommandoradsalternativ måste anges för att skapa en instans av behållaren; i annat fall startar inte behållaren.  Mer information finns i [fakturering](#billing).

När instansierats, kan du anropa åtgärder från behållaren med hjälp av behållarvärd URI. Till exempel representerar följande värden URI Attitydanalys behållare som har initierats i föregående exempel:

```http
http://localhost:5000/
```

> [!TIP]
> Du kan komma åt den [OpenAPI-specifikation](https://swagger.io/docs/specification/about/) (tidigare Swagger-specifikation), som beskriver de åtgärder som stöds av en initierad behållare från den `/swagger` relativ URI för den behållaren. Följande URI: N innehåller till exempel åtkomst till den OpenAPI-specifikationen för Attitydanalys-behållare som har initierats i föregående exempel:
>
>  ```http
>  http://localhost:5000/swagger
>  ```

Du kan antingen [anropa REST API-åtgärder](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-call-api) tillgängliga från behållaren eller Använd den [Azure Cognitive Services Text Analytics SDK](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics) -klientbiblioteket för att anropa dessa åtgärder.  
> [!IMPORTANT]
> Du måste ha Azure Cognitive Services Text Analytics SDK version 2.1.0 eller senare om du vill använda klientbiblioteket med din behållare.

Den enda skillnaden mellan att en viss åtgärd från din behållare och anropa att samma åtgärd från en motsvarande tjänst på Azure är när du ska använda värden URI: N för din behållare, snarare än värden URI: N för en Azure-region för att anropa åtgärden. Om du vill använda en Text Analytics-instans som körs i regionen västra USA Azure skulle du till exempel anropa följande REST API-åtgärden:

```http
POST https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases
```

Om du vill använda en nyckel diskussionsämne behållare som körs på den lokala datorn under standardkonfigurationen kan anropa du följande REST API-åtgärden:

```http
POST http://localhost:5000/text/analytics/v2.0/keyPhrases
```

### <a name="billing"></a>Fakturering

Text Analytics behållarna skicka faktureringsinformation till Azure, med en motsvarande Text Analytics-resurs på ditt Azure-konto. Följande kommandoradsalternativ används för faktureringsändamål Text Analytics-behållare:

| Alternativ | Beskrivning |
|--------|-------------|
| `ApiKey` | API-nyckel för den resursen för textanalys som används för att spåra faktureringsinformation.<br/>Värdet för det här alternativet måste anges till en API-nyckel för den etablerade Text Analytics Azure-resursen som anges i `Billing`. |
| `Billing` | Slutpunkten för Text Analytics-resursen som används för att spåra faktureringsinformation.<br/>Värdet för det här alternativet måste anges till slutpunkten för en etablerad Text Analytics Azure-resurs-URI.|
| `Eula` | Anger att du har godkänt licensen för behållaren.<br/>Värdet för det här alternativet måste anges till `accept`. |

> [!IMPORTANT]
> Alla tre alternativ måste anges med giltiga värden eller behållaren startar inte.

Mer information om alternativen finns i [konfigurera behållare](../text-analytics-resource-container-config.md).

## <a name="summary"></a>Sammanfattning

I den här artikeln beskrivs begrepp och arbetsflöde för att ladda ned, installera och textanalys-behållare som körs. Sammanfattningsvis:

* Textanalys innehåller tre Linux-behållare för Docker, som kapslar in extrahering av diskussionsämne, språkidentifiering och attitydanalys.
* Avbildningar hämtas från ett privat behållarregister i Azure.
* Behållaravbildningar som körs i Docker.
* Du kan använda antingen SDK eller REST API för att anropa åtgärder i textanalys behållare genom att ange värden URI: N för behållaren.
* Du måste ange faktureringsinformation när instanser skapades av en behållare.
* ** Cognitive Services-behållare är inte licensierad för att köra inte är ansluten till Azure för att mäta. Kunder måste du aktivera behållarna för att kommunicera faktureringsinformation med tjänsten Avläsning av programvara vid alla tidpunkter. Cognitive Services-behållare Skicka inte kunddata (t.ex. bild eller text som analyseras) till Microsoft.  

## <a name="next-steps"></a>Nästa steg

* Granska [konfigurera behållare](../text-analytics-resource-container-config.md) för konfigurationsinställningar
* Granska [textanalys översikt](../overview.md) mer information om identifiering av diskussionsämne, språkidentifiering och attitydanalys  
* Referera till den [Textanalys](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) mer information om de metoder som stöds av behållaren.
* Referera till [vanliga frågor (och svar FAQ)](../text-analytics-resource-faq.md) att lösa problem som rör visuellt funktioner.