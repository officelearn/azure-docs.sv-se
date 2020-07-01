---
title: Vad är nytt i Formigenkänning?
titleSuffix: Azure Cognitive Services
description: Förstå de senaste ändringarna i formulärets tolknings-API.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: pafarley
ms.openlocfilehash: ad87c7e3517aad8a611c711773c5e1cc99b24f88
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/30/2020
ms.locfileid: "85562883"
---
# <a name="whats-new-in-form-recognizer"></a>Vad är nytt i Formigenkänning?

Formulär igenkännings tjänsten uppdateras regelbundet. Använd den här artikeln för att hålla dig uppdaterad med funktions förbättringar, korrigeringar och dokumentations uppdateringar.

> [!NOTE]
> Snabb starter och guider för formulär igenkänning använder alltid den senaste versionen av API, om inget annat anges.

## <a name="june-2020"></a>Juni 2020

### <a name="new-features"></a>Nya funktioner
* **CopyModel-API har lagts till i klient-SDK: er** Du kan nu använda klient-SDK: er för att kopiera modeller från en prenumeration till en annan. Se [säkerhetskopiera och återställa modeller](./disaster-recovery.md) för allmän information om den här funktionen.
* **Azure Active Directory-integrering** Du kan nu använda autentiseringsuppgifterna för AAD för att autentisera dina klient objekt i SDK: er för formulär igenkänning.
* **SDK-/regionsspecifika ändringar** Detta inkluderar både mindre funktions tillägg och avbrytande ändringar. Se SDK-ChangeLogs för mer information.
  * [C# SDK Preview 3 ändringsloggen](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Python SDK Preview 3 ändringsloggen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Java SDK Preview 3 ändringsloggen](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Java Script SDK Preview 3 ändringsloggen](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>April 2020

### <a name="new-features"></a>Nya funktioner
* **SDK-stöd för Forms IGENKÄNNING API v 2.0 offentlig för hands version** Den här månaden utökade vi vår tjänst support för att inkludera en för hands versions-SDK för formulär igenkännings versionen v 2.0 (för hands version). Använd länkarna nedan för att komma igång med ditt val av språk: 
   * [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/formrecognizer)
   * [Java SDK](https://docs.microsoft.com/java/api/overview/azure/formrecognizer)
   * [Python SDK](https://docs.microsoft.com/python/api/overview/azure/formrecognizer)
   * [JavaScript SDK](https://docs.microsoft.com/javascript/api/overview/azure/formrecognizer)

  Den nya SDK: n stöder alla funktioner i v 2.0-REST API för formulär igenkänning. Du kan till exempel träna en modell med eller utan etiketter och extrahera text, nyckel värde par och tabeller från dina formulär, extrahera data från kvitton med den förbyggda tjänsten för inaktuella inleveranser och extrahera text och tabeller med layout tjänsten från dina dokument. Du kan dela din feedback på SDK: erna via [feedback-formuläret för SDK](https://aka.ms/FR_SDK_v1_feedback).
 
* **Kopiera anpassad modell** Nu kan du kopiera modeller mellan regioner och prenumerationer med hjälp av funktionen ny kopiera anpassad modell. Innan du anropar API: t för kopiera anpassad modell måste du först få behörighet att kopiera till mål resursen genom att anropa åtgärden Kopiera auktorisering mot mål resursens slut punkt.
   * [Skapa en kopierings auktorisering](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/CopyCustomFormModelAuthorization) REST API
   * [Kopiera en anpassad modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/CopyCustomFormModel) REST API 

### <a name="security-improvements"></a>Förbättringar av säkerhet

* Kundhanterade nycklar är nu tillgängliga för FormRecognizer. Mer information finns i [data kryptering i vila för formulär igenkänning](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/form-recognizer-encryption-of-data-at-rest).
* Använd hanterade identiteter för åtkomst till Azure-resurser med Azure Active Directory. Mer information finns i [ge åtkomst till hanterade identiteter](https://docs.microsoft.com/azure/cognitive-services/authentication#authorize-access-to-managed-identities).

## <a name="march-2020"></a>Mars 2020 

### <a name="new-features"></a>Nya funktioner

* **Värde typer för etiketter** Nu kan du ange vilka typer av värden du märker med formulär tolkens exempel etikett verktyg. Följande värde typer och varianter stöds för närvarande:
  * `string`
    * standard, `no-whitespaces``alphanumeric`
  * `number`
    * objekt`currency`
  * `date` 
    * standard, `dmy` , `mdy` ,`ymd`
  * `time`
  * `integer`

  Mer information om hur du använder den här funktionen finns i guiden [exempel etikett verktyg](./quickstarts/label-tool.md#specify-tag-value-types) .


* **Tabell visualisering** Verktyget för att märka exempel visar nu tabeller som har identifierats i dokumentet. På så sätt kan du Visa tabellerna som har identifierats och extraherats från dokumentet innan du märker och analyserar. Den här funktionen kan växlas på/av med alternativet lager.

  Detta är ett exempel på hur tabeller identifieras och extraheras:

  > [!div class="mx-imgBorder"]
  > ![Tabell visualisering med verktyget för exempel etiketter](./media/whats-new/formre-table-viz.png)

    De extraherade tabellerna är tillgängliga i JSON-utdata under `"pageResults"` .

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