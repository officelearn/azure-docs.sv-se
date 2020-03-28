---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 10/08/2019
ms.openlocfilehash: 5089af4a4e1714d49b844a1b6823487a3f6a8dcf
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74483046"
---
Börja använda tjänsten Avvikelsedetektor genom att skapa en av Azure-resurserna nedan.

* <a href="https://azure.microsoft.com/try/cognitive-services/#decision" target="_blank" rel="noopener">Skapa en utvärderingsresurs (öppnas på en ny flik)</a>
    * Ingen Azure-prenumeration behövs: 
    * Gäller i sju dagar, gratis. När du har registrerat dig kommer en utvärderingsnyckel och slutpunkt att vara tillgängliga på [Azure-webbplatsen](https://azure.microsoft.com/try/cognitive-services/my-apis/). 
    * Det här är ett bra alternativ om du vill prova Avvikelsedetektor, men inte har en Azure-prenumeration.

* <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector" target="_blank" rel="noopener">Skapa en avvikelsedetektorresurs (öppnas på en ny flik):</a>
    * Tillgänglig via Azure-portalen tills du tar bort resursen.
    * Använd den kostnadsfria prisnivån för att prova tjänsten och uppgradera senare till en betald nivå för produktion.



### <a name="create-an-environment-variable"></a>Skapa en miljövariabel

>[!NOTE]
> Slutpunkterna för icke-utvärderingsresurser som skapats efter den 1 juli 2019 använder det anpassade underdomänformatet som visas nedan. Mer information och en fullständig lista över regionala slutpunkter finns i [Anpassade underdomännamn för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Med hjälp av nyckeln och slutpunkten från resursen du skapade skapar du två miljövariabler för autentisering:

* `ANOMALY_DETECTOR_KEY`- Resursnyckeln för att autentisera dina begäranden.
* `ANOMALY_DETECTOR_ENDPOINT`- Resursslutpunkten för att skicka API-begäranden. Det kommer att se ut så här: 
  * `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

Följ instruktionerna för operativsystemet.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx ANOMALY_DETECTOR_KEY <replace-with-your-anomaly-detector-key>
setx ANOMALY_DETECTOR_ENDPOINT <replace-with-your-anomaly-detector-endpoint>
```

När du har lagt till miljövariabeln startar du om konsolfönstret.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

När du har lagt till miljövariabeln så kör `source ~/.bashrc` från konsolfönstret så att ändringarna träder i kraft.

#### <a name="macos"></a>[Macos](#tab/unix)

Redigera `.bash_profile`din och lägg till miljövariabeln:

```bash
export ANOMALY_DETECTOR_KEY=<replace-with-your-anomaly-detector-key>
export ANOMALY_DETECTOR_ENDPOINT=<replace-with-your-anomaly-detector-endpoint>
```

När du har lagt till miljövariabeln så kör `source .bash_profile` från konsolfönstret så att ändringarna träder i kraft.

***