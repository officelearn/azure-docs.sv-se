---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2019
ms.openlocfilehash: a7ae6cb1231e4c202dfd0a39602c03b33099d088
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554712"
---
Börja använda tjänsten avvikelse detektor genom att skapa en av Azure-resurserna nedan.

* En [utvärderings resurs](https://azure.microsoft.com/try/cognitive-services/#decision) (ingen Azure-prenumeration behövs): 
    * Gäller i sju dagar utan kostnad. Efter registreringen kommer en utvärderings nyckel och slut punkt att vara tillgängliga på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/). 
    * Det här är ett bra alternativ om du vill testa avvikelse detektor, men inte har någon Azure-prenumeration.

* En [avvikelse detektor resurs](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector):
    * Tillgängligt via [Azure Portal](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade) tills du tar bort resursen.
    * Använd den kostnads fria pris nivån för att testa tjänsten och uppgradera senare till en betald nivå för produktion.

### <a name="create-an-environment-variable"></a>Skapa en miljö variabel

>[!NOTE]
> Slut punkterna för icke-testresurser som skapats efter den 1 juli 2019 använder det anpassade under domän formatet som visas nedan. Mer information och en fullständig lista över regionala slut punkter finns i [anpassade under domän namn för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Med din nyckel och slut punkt från den resurs som du har skapat skapar du två miljövariabler för autentisering:

* `ANOMALY_DETECTOR_KEY`-resurs nyckeln för att autentisera dina begär Anden.
* `ANOMALY_DETECTOR_ENDPOINT`-resurs slut punkten för att skicka API-begäranden. Det kommer att se ut så här: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Följ anvisningarna för ditt operativ system.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx ANOMALY_DETECTOR_KEY your-anomaly-detector-key
setx ANOMALY_DETECTOR_ENDPOINT your-anomaly-detector-endpoint
```

Starta om konsol fönstret när du har lagt till miljövariabeln.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=your-anomaly-detector-key
export ANOMALY_DETECTOR_ENDPOINT=your-anomaly-detector-endpoint
```

När du har lagt till miljövariabeln så kör `source ~/.bashrc` från konsolfönstret så att ändringarna träder i kraft.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Redigera `.bash_profile` och Lägg till miljövariabeln:

```bash
export ANOMALY_DETECTOR_KEY=your-anomaly-detector-key
export ANOMALY_DETECTOR_ENDPOINT=your-anomaly-detector-endpoint
```

När du har lagt till miljövariabeln så kör `source .bash_profile` från konsolfönstret så att ändringarna träder i kraft.

***