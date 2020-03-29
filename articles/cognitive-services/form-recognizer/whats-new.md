---
title: Vad är nytt i Formigenkänning?
titleSuffix: Azure Cognitive Services
description: Förstå de senaste ändringarna i API:et för formulärreform.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: pafarley
ms.openlocfilehash: 7f20244906581dd2869bbc7fcd997d5245540eda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155179"
---
# <a name="whats-new-in-form-recognizer"></a>Vad är nytt i Formigenkänning?

Tjänsten Formulärrekänningstjänsten uppdateras löpande. Använd den här artikeln för att hålla dig uppdaterad med funktionsförbättringar, korrigeringar och dokumentationsuppdateringar.

> [!NOTE]
> Snabbstartarna och stödlinjerna för Formulär recognizer använder alltid den senaste versionen av API:et, om inget annat anges.

## <a name="march-2020"></a>Mars 2020 

### <a name="extraction-enhancements"></a>Extraktionsförbättringar

Den här versionen innehåller extraktionsförbättringar och noggrannhetsförbättringar, särskilt möjligheten att märka och extrahera flera nyckel-/värdepar i samma textrad. 
 
### <a name="form-recognizer-sample-labeling-tool-is-now-open-source"></a>Exempeletikettverktyget för formuläridentater är nu öppen källkod

Exempeletikettverktyget för formulärreformat är nu tillgängligt som ett projekt med öppen källkod. Du kan integrera den i dina lösningar och göra kundspecifika ändringar för att möta dina behov.

Mer information om exempeletikningsverktyget för formulärreformat finns i dokumentationen som finns på [GitHub](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md).

### <a name="labeling-value-types"></a>Märkning av värdetyper

Värdetyper är nu tillgängliga för användning med exempeletikettverktyget för formulärmedkännare. Dessa värdetyper stöds för närvarande: 

* String
* Tal 
* Integer
* Datum 
* Tid

Den här bilden visar hur värdetypsmarkering ser ut i exempeletikettverktyget för formulärdeform:

> [!div class="mx-imgBorder"]
> ![Val av värdetyp med verktyg för exempeletiketter](./media/whats-new/formre-value-type.png)

Den extraherade tabellen finns i JSON-utdata i `pageResults`.

### <a name="table-visualization"></a>Tabellvisualisering 

Etikettverktyget för formulärreformat visar nu tabeller som har identifierats i dokumentet. På så sätt kan du visa de tabeller som har identifierats och extraherats från dokumentet innan du etiketterar och analyserar med exempeletikettverktyget för formulärmedkännare. Den här funktionen kan växlas på/av med hjälp av lageralternativet. 

Det här är ett exempel på hur tabeller känns igen och extraheras:

> [!div class="mx-imgBorder"]
> ![Tabellvisualisering med hjälp av exempeletikettverktyget](./media/whats-new/formre-table-viz.png)

> [!IMPORTANT]
> Det går inte att märka tabeller. Om tabeller inte känns igen och extrated automatiskt, kan du bara märka dem som nyckel / värde par. När du etiketterar tabeller som nyckel-/värdepar ska du märka varje cell som ett värde.

### <a name="tls-12-enforcement"></a>Tvingande TLS 1.2

* TLS 1.2 tillämpas nu för alla HTTP-begäranden till den här tjänsten. Mer information finns i [Azure Cognitive Services-säkerhet](../cognitive-services-security.md).

## <a name="january-2020"></a>Januari 2020

Den här versionen introducerar Form Recognizer 2.0 (förhandsversion). I avsnitten nedan hittar du mer information om nya funktioner, förbättringar och ändringar. 

### <a name="new-features"></a>Nya funktioner

* **Anpassad modell**
  * **Träna med etiketter** Du kan nu träna en anpassad modell med manuellt märkta data. Detta resulterar i bättre presterande modeller och kan producera modeller som fungerar med komplexa formulär eller formulär som innehåller värden utan nycklar.
  * **Asynkron API** Du kan använda async API-anrop för att träna med och analysera stora datauppsättningar och filer.
  * **Stöd för TIFF-filer** Du kan nu träna med och extrahera data från TIFF-dokument.
  * **Förbättringar av extraktionsnoggrannheten**

* **Fördefinierad kvittomodell**
  * **Tipsbelopp** Du kan nu extrahera tipsbelopp och andra handskrivna värden.
  * **Utvinning av radartikel** Du kan extrahera radartikelvärden från inleveranser.
  * **Konfidensvärden** Du kan visa modellens förtroende för varje extraherat värde.
  * **Förbättringar av extraktionsnoggrannheten**

* **Extrahering av layout** Du kan nu använda layout-API:et för att extrahera textdata och tabelldata från formulären.

### <a name="custom-model-api-changes"></a>Anpassade ändringar av modellens API

Alla API:er för utbildning och användning av anpassade modeller har bytt namn och vissa synkrona metoder är nu asynkrona. Följande är stora förändringar:

* Processen att träna en modell är nu asynkron. Du initierar utbildning via **API-anropet /custom/models.** Det här anropet returnerar ett åtgärds-ID som du kan skicka till **anpassade/modeller/{modelID}** för att returnera träningsresultaten.
* Utvinning av nyckel/värde initieras nu av **API-anropet /custom/models/{modelID}/analyze.** Det här anropet returnerar ett åtgärds-ID som du kan skicka till **anpassade/modeller/{modelID}/analyzeResults/{resultID}** för att returnera extraheringsresultaten.
* Operations-ID:erna för tågåtgärden finns nu i **platshuvudet** för HTTP-svar, inte **i huvudet för driftplats.**

### <a name="receipt-api-changes"></a>Api-ändringar för inleverans

API:erna för att läsa försäljningsinleveranser har bytt namn.

* Extrahering av inleveransdata initieras nu av **API-anropet /prebuilt/receipt/analyze.** Det här anropet returnerar ett åtgärds-ID som du kan skicka till **/fördefinierade/inleverans/analyzeResults/{resultID}** för att returnera extraheringsresultaten.

### <a name="output-format-changes"></a>Ändringar i utdataformat

JSON-svaren för alla API-anrop har nya format. Vissa nycklar och värden har lagts till, tagits bort eller bytt namn. Se snabbstarterna för exempel på aktuella JSON-format.

## <a name="next-steps"></a>Nästa steg

Slutför en [snabbstart](quickstarts/curl-train-extract.md) för att komma igång med [API:erna för formulärmedkänningsreformat](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm).