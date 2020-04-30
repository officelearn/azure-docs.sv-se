---
title: Vad är nytt i Formigenkänning?
titleSuffix: Azure Cognitive Services
description: Förstå de senaste ändringarna i formulärets tolknings-API.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 5f8c66db491b93278fedf1378d3df86e7ce5fdbf
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81531090"
---
# <a name="whats-new-in-form-recognizer"></a>Vad är nytt i Formigenkänning?

Formulär igenkännings tjänsten uppdateras regelbundet. Använd den här artikeln för att hålla dig uppdaterad med funktions förbättringar, korrigeringar och dokumentations uppdateringar.

> [!NOTE]
> Snabb starter och guider för formulär igenkänning använder alltid den senaste versionen av API, om inget annat anges.

## <a name="march-2020"></a>Mars 2020 

### <a name="new-features"></a>Nya funktioner

* **Värde typer för etiketter** Nu kan du ange vilka typer av värden du märker med formulär tolkens exempel etikett verktyg. Följande värde typer och varianter stöds för närvarande:
  * `string`
    * standard, `no-whitespaces``alphanumeric`
  * `number`
    * objekt`currency`
  * `date` 
    * standard, `dmy`, `mdy`,`ymd`
  * `time`
  * `integer`

  Mer information om hur du använder den här funktionen finns i guiden [exempel etikett verktyg](./quickstarts/label-tool.md#specify-tag-value-types) .


* **Tabell visualisering** Verktyget för att märka exempel visar nu tabeller som har identifierats i dokumentet. På så sätt kan du Visa tabellerna som har identifierats och extraherats från dokumentet innan du märker och analyserar. Den här funktionen kan växlas på/av med alternativet lager.

  Detta är ett exempel på hur tabeller identifieras och extraheras:

  > [!div class="mx-imgBorder"]
  > ![Tabell visualisering med verktyget för exempel etiketter](./media/whats-new/formre-table-viz.png)

    De extraherade tabellerna är tillgängliga i JSON-utdata `"pageResults"`under.

  > [!IMPORTANT]
  > Etiketterade tabeller stöds inte. Om tabeller inte känns igen och extrated automatiskt kan du bara märka dem som nyckel/värde-par. När du har namngett tabeller som nyckel/värde-par, förser du varje cell med ett unikt värde.

### <a name="extraction-enhancements"></a>Förbättringar av extrahering

I den här versionen ingår extraherings förbättringar och precisions förbättringar, i synnerhet möjlighet att märka och extrahera flera nyckel/värde-par i samma textrad. 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>Exempel verktyget för etikettering är nu öppen källkod

Formulär tolkens exempel etikett verktyg är nu tillgängligt som ett projekt med öppen källkod. Du kan integrera den i dina lösningar och göra kundspecifika ändringar så att de passar dina behov.

Mer information om verktyget formulär igenkännings exempel etikettering finns i dokumentationen som finns på [GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

### <a name="tls-12-enforcement"></a>Tvingande TLS 1.2

TLS 1,2 upprätthålls nu för alla HTTP-begäranden till den här tjänsten. Mer information finns i [Azure Cognitive Services Security](../cognitive-services-security.md).

## <a name="january-2020"></a>Januari 2020

I den här versionen introduceras formulär tolken 2,0 (för hands version). I avsnitten nedan finns mer information om nya funktioner, förbättringar och ändringar. 

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