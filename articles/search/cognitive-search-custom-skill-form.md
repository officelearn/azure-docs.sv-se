---
title: Anpassad färdighet för formulärre recognizeer (C#)
titleSuffix: Azure Cognitive Search
description: Lär dig hur du skapar en anpassad färdighet för formulärreformare med C# och Visual Studio.
manager: nitinme
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-search
ms.topic: article
ms.date: 01/21/2020
ms.openlocfilehash: 713b790c432f0e416392243262aed4b0fcda8892
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274580"
---
# <a name="example-create-a-form-recognizer-custom-skill"></a>Exempel: Skapa en anpassad färdighet för formulärdekänning

I det här exemplet med Azure Cognitive Search skillset får du lära dig hur du skapar en anpassad färdighet för formulärreformare med C# och Visual Studio. Formulärreformare analyserar dokument och extraherar nyckel-/värdepar och tabelldata. Genom att slå in Formuläre recognizer i det [anpassade färdighetsgränssnittet](cognitive-search-custom-skill-interface.md)kan du lägga till den här funktionen som ett steg i en heltäckande anrikningspipeline. Pipelinen kan sedan läsa in dokumenten och göra andra omformningar.

## <a name="prerequisites"></a>Krav

- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) (valfri utgåva).
- Minst fem former av samma typ. Du kan använda exempeldata som medföljer den här guiden.

## <a name="create-a-form-recognizer-resource"></a>Skapa en formulärkonformeringsresurs

[!INCLUDE [create resource](../cognitive-services/form-recognizer/includes/create-resource.md)]

## <a name="train-your-model"></a>Träna din modell

Du måste träna en formulärmedkännare modell med dina indataformulär innan du använder denna färdighet. Följ [snabbstarten för cURL](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/quickstarts/curl-train-extract) för att lära dig hur du tränar en modell. Du kan använda exempelformulären som anges i snabbstarten eller använda dina egna data. När modellen har tränats kopierar du dess ID-värde till en säker plats.

## <a name="set-up-the-custom-skill"></a>Konfigurera den anpassade färdigheten

Den här självstudien använder [Projektet AnalyzeForm](https://github.com/Azure-Samples/azure-search-power-skills/tree/master/Vision/AnalyzeForm) i [Azure Search Power Skills](https://github.com/Azure-Samples/azure-search-power-skills) GitHub-databasen. Klona den här databasen till den lokala datorn och navigera till **Vision/AnalyzeForm/** för att komma åt projektet. Öppna sedan _AnalyzeForm.csproj_ i Visual Studio. Det här projektet skapar en Azure Function-resurs som uppfyller det [anpassade kompetensgränssnittet](cognitive-search-custom-skill-interface.md) och kan användas för Azure Cognitive Search-anrikning. Det tar formulärdokument som indata och det utdata (som text) nyckeln / värdet par som du anger.

Lägg först till miljövariabler på projektnivå. Leta reda på **AnalyzeForm-projektet** i den vänstra rutan, högerklicka på det och välj **Egenskaper**. Klicka på fliken **Felsökning** i fönstret **Egenskaper** och leta sedan reda på fältet **Miljövariabler.** Klicka på **Lägg till** om du vill lägga till följande variabler:
* `FORMS_RECOGNIZER_ENDPOINT_URL`med värdet inställt på slutpunkts-URL:en.
* `FORMS_RECOGNIZER_API_KEY`med värdet inställt på din prenumerationsnyckel.
* `FORMS_RECOGNIZER_MODEL_ID`med värdet inställt på ID:t för den modell du har tränat.
* `FORMS_RECOGNIZER_RETRY_DELAY`med värdet inställt på 1000. Det här värdet är den tid i millisekunder som programmet väntar innan frågan försökers igen.
* `FORMS_RECOGNIZER_MAX_ATTEMPTS`med värdet inställt på 100. Det här värdet är antalet gånger som programmet kommer att fråga tjänsten när du försöker få ett lyckat svar.

Öppna _sedan AnalyzeForm.cs_ och leta `fieldMappings` reda på variabeln, som refererar till *filen field-mappings.json.* Den här filen (och variabeln som refererar till den) definierar listan med nycklar som du vill extrahera från formulären och en anpassad etikett för varje nyckel. Ett `{ "Address:", "address" }, { "Invoice For:", "recipient" }` värde på medel som skriptet bara sparar `Address:` värdena för de identifierade fälten och `Invoice For:` fälten, och det kommer att märka dessa värden med `"address"` respektive `"recipient"`, respektive.

Slutligen, notera `contentType` variabeln. Det här skriptet kör den angivna formulärreskriptmodellen på fjärrdokument som `application/json`refereras av URL, så innehållstypen är . Om du vill analysera lokala filer genom att inkludera deras byteströmmar i `contentType` HTTP-begäranden måste du ändra till lämplig [MIME-typ](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Complete_list_of_MIME_types) för filen.

## <a name="test-the-function-from-visual-studio"></a>Testa funktionen från Visual Studio

När du har redigerat projektet sparar du det och anger **AnalyzeForm-projektet** som startprojekt i Visual Studio (om det inte redan är inställt). Tryck sedan på **F5** för att köra funktionen i din lokala miljö. Använd en REST-tjänst som [Postman](https://www.postman.com/) för att anropa funktionen.

### <a name="http-request"></a>HTTP-begäran

Du ska göra följande begäran om att anropa funktionen.

```HTTP
POST https://localhost:7071/api/analyze-form
```

### <a name="request-body"></a>Begärandetext

Börja med förfråganstextmallen nedan.

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": { 
                "formUrl": "<your-form-url>",
                "formSasToken": "<your-sas-token>"
            }
        }
    ]
}
```

Här måste du ange webbadressen till ett formulär som har samma typ som de formulär som du tränat med. I testsyfte kan du använda ett av dina utbildningsformulär. Om du har följt snabbstarten för cURL finns dina formulär i ett Azure blob storage-konto. Öppna Azure Storage Explorer, leta reda på en formulärfil, högerklicka på den och välj **Hämta signatur för delad åtkomst**. Nästa dialogfönster innehåller en URL och SAS-token. Ange dessa strängar `"formUrl"` i `"formSasToken"` respektive fält i din begäran kropp, respektive.

> [!div class="mx-imgBorder"]
> ![Utforskaren för Azure-lagring; ett pdf-dokument är markerat](media/cognitive-search-skill-form/form-sas.png)

Om du vill analysera ett fjärrdokument som inte finns i Azure `"formUrl"` blob storage `"formSasToken"` klistrar du in webbadressen i fältet och lämnar fältet tomt.

> [!NOTE]
> När färdigheten är integrerad i en kompetens kommer URL:en och token att tillhandahållas av Kognitiv sökning.

### <a name="response"></a>Svar

Du bör se ett svar som liknar följande exempel:

```json
{
    "values": [
        {
            "recordId": "record1",
            "data": {
                "address": "1111 8th st. Bellevue, WA 99501 ",
                "recipient": "Southridge Video 1060 Main St. Atlanta, GA 65024 "
            },
            "errors": null,
            "warnings": null
        }
    ]
}
```

## <a name="publish-the-function-to-azure"></a>Publicera funktionen i Azure

När du är nöjd med funktionsbeteendet kan du publicera det.

1. Högerklicka på projektet i **Lösningsutforskaren** i Visual Studio och välj **Publicera**. Välj **Skapa ny** > **publicering**.
1. Om du inte redan har anslutit Visual Studio till ditt Azure-konto väljer du **Lägg till ett konto....**
1. Följ anvisningarna på skärmen. Ange ett unikt namn för din apptjänst, Azure-prenumerationen, resursgruppen, värdplanen och det lagringskonto som du vill använda. Du kan skapa en ny resursgrupp, en ny värdplan och ett nytt lagringskonto om du inte redan har dessa. När du är klar väljer du **Skapa**.
1. När distributionen är klar lägger du märke till webbadressen. Den här URL:en är adressen till din funktionsapp i Azure. Spara den på en tillfällig plats.
1. I [Azure-portalen](https://portal.azure.com)navigerar du till resursgruppen och letar efter den `AnalyzeForm` funktion som du publicerade. Under avsnittet **Hantera** bör du se Värdnycklar. Kopiera *standardvärdent* och spara den på en tillfällig plats.

## <a name="connect-to-your-pipeline"></a>Ansluta till pipelinen

Om du vill använda den här färdigheten i en pipeline för kognitiv sökning måste du lägga till en färdighetsdefinition i din kompetens. Följande JSON-block är en exempelsvandhetsdefinition (du bör uppdatera indata och utdata för att återspegla just ditt scenario och kompetensmiljö). Ersätt `AzureFunctionEndpointUrl` med funktions-URL:en och ersätt `AzureFunctionDefaultHostKey` med värdnyckeln.

```json
{ 
  "description":"Skillset that invokes the Form Recognizer custom skill",
  "skills":[ 
    "[... your existing skills go here]",
    { 
      "@odata.type":"#Microsoft.Skills.Custom.WebApiSkill",
      "name":"formrecognizer",
      "description":"Extracts fields from a form using a pre-trained form recognition model",
      "uri":"[AzureFunctionEndpointUrl]/api/analyze-form?code=[AzureFunctionDefaultHostKey]",
      "httpMethod":"POST",
      "timeout":"PT30S",
      "context":"/document",
      "batchSize":1,
      "inputs":[ 
        { 
          "name":"formUrl",
          "source":"/document/metadata_storage_path"
        },
        { 
          "name":"formSasToken",
          "source":"/document/metadata_storage_sas_token"
        }
      ],
      "outputs":[ 
        { 
          "name":"address",
          "targetName":"address"
        },
        { 
          "name":"recipient",
          "targetName":"recipient"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

I den här guiden har du skapat en anpassad färdighet från Azure Form Recognizer-tjänsten. Mer information om anpassade kunskaper finns i följande resurser. 

* [Azure Search Power Skills: en databas med anpassade kunskaper](https://github.com/*zure-Samples/azure-search-power-skills)
* [Lägga till en anpassad färdighet i en AI-anrikningspipeline](cognitive-search-custom-skill-interface.md)
* [Definiera en kunskapsuppsättning](cognitive-search-defining-skillset.md)
* [Skapa en kompetens (REST)](https://docs.microsoft.com/rest/api/*earchservice/create-skillset)
* [Kartberikade fält](cognitive-search-output-field-mapping.md)
