---
title: Vad är nytt i Formigenkänning?
titleSuffix: Azure Cognitive Services
description: Förstå de senaste ändringarna i formulärets tolknings-API.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: pafarley
ms.openlocfilehash: cb5639dcf0e13ea03d34604816b3939085674c2e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456370"
---
# <a name="whats-new-in-form-recognizer"></a>Vad är nytt i Formigenkänning?

I den här artikeln beskrivs de viktigaste ändringarna som medföljer nya versioner av formulär igenkännings-API: et.

> [!NOTE]
> Snabb starter och guider i den här dokument uppsättningen använder alltid den senaste versionen av API, om de inte anger något annat.

## <a name="form-recognizer-20-preview"></a>Formulär igenkänning 2,0 (för hands version)

> [!IMPORTANT]
> Formulär igenkänning 2,0 är för närvarande tillgängligt för prenumerationer i `West US 2` och `West Europe` regioner. Om din prenumeration inte finns i den här regionen använder du 1,0-API: et. Snabb starterna för utbildning och användning av en anpassad modell är tillgängliga för både v 1.0 och v 2.0.

### <a name="new-features"></a>Nya funktioner

* **Anpassad modell**
  * **Träna med etiketter** Nu kan du träna en anpassad modell med manuellt märkta data. Detta resulterar i bättre modeller och kan skapa modeller som fungerar med komplexa formulär eller formulär som innehåller värden utan nycklar.
  * **Asynkront API** Du kan använda asynkrona API-anrop för att träna med och analysera stora data uppsättningar och filer.
  * **Stöd för TIFF-fil** Nu kan du träna med och extrahera data från TIFF-dokument.
  * **Förbättringar av extraherings precision**

* **Fördefinierad kvitto modell**
  * **Tip-belopp** Nu kan du extrahera Tip-mängder och andra handskrivna värden.
  * **Extrahering av rad objekt** Du kan extrahera rad objekt värden från inleveranser.
  * **Konfidens intervall** Du kan visa modellens förtroende för varje extraherat värde.
  * **Förbättringar av extraherings precision**

* **Extrahering av layout** Du kan nu använda layout-API: et för att extrahera text data och tabell data från formulären.

### <a name="custom-model-api-changes"></a>Ändringar i anpassade modell-API

Alla API: er för utbildning och användning av anpassade modeller har bytt namn och vissa synkrona metoder är nu asynkrona. Följande är viktiga ändringar:

* Processen för att träna en modell är nu asynkron. Du initierar utbildning genom **/Custom/Models** -API-anropet. Anropet returnerar ett åtgärds-ID, som du kan skicka till **Custom/Models/{modelID}** för att returnera inlärnings resultatet.
* Nyckel/värde-extrahering initieras nu av **/Custom/Models/{modelID}/Analyze** -API-anropet. Anropet returnerar ett åtgärds-ID, som du kan skicka till **Custom/Models/{modelID}/analyzeResults/{resultID}** för att returnera extraherings resultatet.
* Åtgärds-ID för träna-åtgärden finns nu i **plats** rubriken för HTTP-svar, inte **Åtgärds platsens** huvud.

### <a name="receipt-api-changes"></a>Ändringar i kvitto-API

API: erna för läsning av försäljnings kvitton har bytt namn.

* Extrahering av kvitto data initieras nu av **/prebuilt/Receipt/Analyze** -API-anropet. Anropet returnerar ett åtgärds-ID, som du kan skicka till **/prebuilt/Receipt/analyzeResults/{resultID}** för att returnera extraherings resultatet.

### <a name="output-format-changes"></a>Format ändringar

JSON-Svaren för alla API-anrop har nya format. Vissa nycklar och värden har lagts till, tagits bort eller bytt namn. Se snabb starter för exempel på aktuella JSON-format.

## <a name="next-steps"></a>Nästa steg

Kom igång med [formulär igenkännings-API: erna](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)genom att slutföra en [snabb start](quickstarts/curl-train-extract.md) .