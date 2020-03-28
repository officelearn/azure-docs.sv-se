---
title: 'Självstudiekurs: Använda formulärmedakterare med Azure Logic Apps för att analysera fakturor - Form recognizer'
titleSuffix: Azure Cognitive Services
description: I den här självstudien använder du Formulär recognizer med Azure Logic Apps för att skapa ett arbetsflöde som automatiserar processen med att träna en modell och testa den med hjälp av exempeldata.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/27/2020
ms.author: nitinme
ms.openlocfilehash: d71d9c7e6570e562fe4c692ede1d07b70c923cb6
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77118263"
---
# <a name="tutorial-use-form-recognizer-with-azure-logic-apps-to-analyze-invoices"></a>Självstudiekurs: Använda formulärmedakterare med Azure Logic Apps för att analysera fakturor

I den här självstudien skapar du ett arbetsflöde i Azure Logic Apps som använder Form Recognizer, en tjänst som ingår i Azure Cognitive Services-paketet, för att extrahera data från fakturor. Först tränar du en formulärrekänningsmodell med hjälp av en exempeldatauppsättning och sedan testar du modellen på en annan datauppsättning.

Här är vad den här guiden täcker:

> [!div class="checklist"]
> * Begär åtkomst för formulärinformation
> * Skapa en Azure Storage-blob-behållare
> * Ladda upp exempeldata till Azure-blob-behållaren
> * Skapa en Azure Logic App
> * Konfigurera logikappen så att den använder en formulärkon recognizer-resurs
> * Testa arbetsflödet genom att köra logikappen

## <a name="prerequisites"></a>Krav

* Azure-prenumeration - [Skapa en gratis](https://azure.microsoft.com/free/).

## <a name="understand-the-invoice-to-be-analyzed"></a>Förstå fakturan som ska analyseras

Exempeldatauppsättningen som du ska använda för att träna och testa modellen är tillgänglig som en ZIP-fil från [GitHub](https://go.microsoft.com/fwlink/?linkid=2090451). Hämta och extrahera ZIP-filen och öppna en PDF-fakturafil under mappen **/Train.** Observera att den har en tabell med fakturanummer, fakturadatum och så vidare. 

> [!div class="mx-imgBorder"]
> ![Exempel på faktura](media/tutorial-form-recognizer-with-logic-apps/sample-receipt.png)

I den här självstudien får du lära dig hur du använder ett Azure Logic Apps-arbetsflöde för att extrahera informationen från tabeller som dessa till JSON-format.

## <a name="create-an-azure-storage-blob-container"></a>Skapa en Azure Storage-blob-behållare

Du använder den här behållaren för att ladda upp exempeldata som krävs för att träna modellen.

1. Följ instruktionerna i [Skapa ett Azure Storage-konto](../../storage/common/storage-account-create.md) för att skapa ett lagringskonto. Använd **formulärrecostorage** som lagringskontonamn.
1. Följ instruktionerna i [Skapa en Azure-blob-behållare](../../storage/blobs/storage-quickstart-blobs-portal.md) för att skapa en behållare i Azure Storage-kontot. Använd **formulärrecocontainer** som behållarnamn. Se till att du ställer in den offentliga åtkomstnivån till **Behållare (anonym läsåtkomst för behållare och blobbar)**.

    > [!div class="mx-imgBorder"]
    > ![Skapa blobcontainer](media/tutorial-form-recognizer-with-logic-apps/create-blob-container.png)

## <a name="upload-sample-data-to-the-azure-blob-container"></a>Ladda upp exempeldata till Azure-blob-behållaren

Ladda ned exempeldata som finns på [GitHub](https://go.microsoft.com/fwlink/?linkid=2090451). Extrahera data till en lokal mapp och ladda upp innehållet i mappen **/Train** till den **formulärberättare** som du skapade tidigare. Följ instruktionerna vid [Ladda upp en blockblob](../../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob) för att överföra data till en behållare.

Kopiera url:en för behållaren. Du behöver den här webbadressen senare i självstudien. Om du har skapat lagringskontot och behållaren med samma namn som anges i den här självstudien blir url:en *\/https: /formrecostorage.blob.core.windows.net/formrecocontainer/*.

## <a name="create-a-form-recognizer-resource"></a>Skapa en formulärkonformeringsresurs

[!INCLUDE [create resource](./includes/create-resource.md)]

## <a name="create-your-logic-app"></a>Skapa en logikapp

Du kan använda Azure Logic Apps för att automatisera och dirigera uppgifter och arbetsflöden. I den här självstudien skapar du en logikapp som utlöses genom att ta emot en faktura som du vill analysera som en bifogad fil i ett e-postmeddelande. I det här arbetsflödet utför du följande uppgifter:
* Konfigurera logikappen så att den utlöses automatiskt när du får ett e-postmeddelande med en bifogad faktura.
* Konfigurera logikappen så att den använder en **formulärretetågsmodell** för att träna en modell med hjälp av exempeldata som du laddade upp till Azure-blob-lagringen.
* Konfigurera logikappen så att den använder en **formulärreformsanalysåtgärd** för att använda den modell som du redan har tränat. Den här komponenten analyserar fakturan som du tillhandahåller den här logikappen baserat på den modell som den tränade tidigare.

Följ dessa steg för att konfigurera arbetsflödet.

1. På Huvudmenyn i Azure väljer du Skapa en > **resursintegrationslogikapp** > **Logic App**. **Create a resource**

1. Under **Skapa en logikapp** anger du information om din logikapp som visas här. När du är klar väljer du **Skapa**.

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Namn** | <*logik-app-namn*> | Logikappnamnet, som bara kan innehålla bokstäver, siffror, bindestreck`-`( ), understreck (`.``_`), parenteser (`(`och `)`punkter ( ). I det här exemplet används "My-First-Logic-App". |
   | **Prenumeration** | <*Azure-prenumeration-namn*> | Ditt Azure-prenumerationsnamn |
   | **Resursgrupp** | <*Azure-resource-group-namn*> | Namnet på [Azure-resursgruppen](./../../azure-resource-manager/management/overview.md) som används för att ordna relaterade resurser. I det här exemplet används "My-First-LA-RG". |
   | **Location** | <*Azure-region*> | Regionen där du vill lagra din logikappinformation. I det här exemplet används "Västra USA". |
   | **Logga Analytics** | Av | Behåll inställningen **Av** för diagnostisk loggning. |
   ||||

1. När Azure har distribuerat din app väljer du **Meddelanden** > **gå till resurs** för din distribuerade logikapp i Verktygsfältet i Azure. Du kan också söka efter och välja logikappen genom att skriva namnet i sökrutan.

   Logic Apps Designer öppnas och det visas en sida med en introduktionsvideo och vanliga utlösare. Under **Mallar** väljer du **Tom logikapp**.

   > [!div class="mx-imgBorder"]
   > ![Välj tom mall för logikapp](./../../logic-apps/media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

### <a name="configure-the-logic-app-to-trigger-the-workflow-when-an-email-arrives"></a>Konfigurera logikappen så att arbetsflödet utlöses när ett e-postmeddelande anländer

I den här självstudien utlöser du arbetsflödet när ett e-postmeddelande tas emot med en bifogad faktura. Den här självstudien använder Office 365 som e-posttjänst, men du kan använda alla andra e-postleverantörer som du vill använda.

1. På flikarna väljer du Alla, väljer **Office 365 Outlook**och väljer sedan **När ett nytt e-postmeddelande anländer**under **Utlösare**.

    ![Utlösa logikapp via ett inkommande e-postmeddelande](media/tutorial-form-recognizer-with-logic-apps/logic-app-email-trigger.png)

1. Klicka på **Logga in i**rutan Office **365 Outlook** och ange information om du vill logga in på ett Office 365-konto.

1. Gör följande i nästa dialogruta.
    1. Välj den mapp som ska övervakas för alla nya e-postmeddelanden.
    1. För **Har-bilagor**väljer du **Ja**. Detta säkerställer att endast e-postmeddelanden med bifogade filer utlöser arbetsflödet.
    1. För **Inkludera bifogade filer**väljer du **Ja**. Detta säkerställer att innehållet i bilagan används vid bearbetning nedströms.

        > [!div class="mx-imgBorder"]
        > ![Konfigurera e-postutlösare för logikapp](media/tutorial-form-recognizer-with-logic-apps/logic-app-specify-email-folder.png)

1. Klicka på **Spara** i verktygsfältet högst upp.

### <a name="configure-the-logic-app-to-use-form-recognizer-train-model-operation"></a>Konfigurera logikappen för att använda åtgärden Form Recognizer Train Model

Innan du kan använda tjänsten Formulärrecenser för att analysera fakturor måste du träna en modell genom att tillhandahålla några exempelfakturor som modellen kan analysera och lära av.

1. Välj **Nytt steg**och sök efter **Formulärkonformerare**under Välj en **åtgärd**. Välj **Formulärre recognizeer**i resultaten som visas och välj sedan **Träna modell**under de åtgärder som är tillgängliga för Formulärre recognizeer .

    > [!div class="mx-imgBorder"]
    > ![Träna en formulärfaktormodell](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-train-model.png)

1. Ange ett namn på anslutningen i dialogrutan Formulärkonformerare och ange slutpunkts-URL:en och nyckeln som du hämtade för resursen Formulärkonformerare.

    > [!div class="mx-imgBorder"]
    > ![Anslutningsnamn för Formulärdekänning](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-create-connection.png)

    Klicka på **Skapa**.

1. Ange URL:en för behållaren där du laddade upp exempeldata för **Källa**i dialogrutan **Tågmodell.**

    > [!div class="mx-imgBorder"]
    > ![Lagringsbehållare för exempelfakturor](media/tutorial-form-recognizer-with-logic-apps/source-for-train-model.png)

1. Klicka på **Spara** i verktygsfältet högst upp.

### <a name="configure-the-logic-app-to-use-the-form-recognizer-analyze-form-operation"></a>Konfigurera logikappen så att den använder åtgärden Analysera formulär för formulär

I det här avsnittet lägger du till åtgärden **Analysera formulär** i arbetsflödet. Den här åtgärden använder den redan tränade modellen för att analysera en ny faktura som tillhandahålls till logikappen.

1. Välj **Nytt steg**och sök efter **Formulärkonformerare**under Välj en **åtgärd**. Välj **Formulärre recognizeer**i resultaten som visas och välj sedan **Analysera formulär**under de åtgärder som är tillgängliga för Formuläre recognizeer .

    > [!div class="mx-imgBorder"]
    > ![Analysera en formulärakänningsmodell](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-analyze-model.png)

1. Gör följande i dialogrutan **Analysera formulär:**

    1. Klicka på textrutan **Modell-ID** och välj **modelId**under fliken Dynamiskt innehåll under fliken **Dynamiskt innehåll** . Genom att göra detta ger du flödesprogrammet med modell-ID för den modell som du tränade i det sista avsnittet.

        > [!div class="mx-imgBorder"]
        > ![Använda ModelID för formulärdekänningsapparat](media/tutorial-form-recognizer-with-logic-apps/analyze-form-model-id.png)

    2. Klicka på textrutan **Dokument** och välj **Bifogat innehåll**under fliken Dynamiskt innehåll under fliken **Dynamiskt innehåll** . Detta konfigurerar flödet så att exempelfakturafilen som är kopplad i e-postmeddelandet som utlöser arbetsflödet används.

        > [!div class="mx-imgBorder"]
        > ![Använda e-postbilaga för att analysera fakturor](media/tutorial-form-recognizer-with-logic-apps/analyze-form-input-data.png)

1. Klicka på **Spara** i verktygsfältet högst upp.

### <a name="extract-the-table-information-from-the-invoice"></a>Extrahera tabellinformationen från fakturan

I det här avsnittet konfigurerar du logikappen för att extrahera informationen från tabellen i fakturorna.

1. Välj **Lägg till en åtgärd**och under Välj en **åtgärd**väljer du **Komponera** **och** under de åtgärder som är tillgängliga.
    ![Extrahera tabellinformation från fakturan](media/tutorial-form-recognizer-with-logic-apps/extract-table.png)

1. Klicka på textrutan **Ingångar** i dialogrutan **Skriv** och välj **tabeller**i dialogrutan som dyker upp.

    > [!div class="mx-imgBorder"]
    > ![Extrahera tabellinformation från fakturan](media/tutorial-form-recognizer-with-logic-apps/select-tables.png)

1. Klicka på **Spara**.

## <a name="test-your-logic-app"></a>Testa logikappen

Om du vill testa logikappen använder du exempelfakturorna i mappen **/Test** i exempeldatauppsättningen som du hämtade från [GitHub](https://go.microsoft.com/fwlink/?linkid=2090451). Följ de här stegen:

1. Välj **Kör** i verktygsfältet högst upp i Azure Logic Apps-designern för din app. Arbetsflödet är nu aktivt och väntar på att få ett e-postmeddelande med fakturan bifogad.
1. Skicka ett e-postmeddelande med en exempelfaktura kopplad till den e-postadress som du angav när logikappen skapas. Kontrollera att e-postmeddelandet levereras till mappen som du angav när logikappen konfigureras.
1. Så snart e-postmeddelandet levereras till mappen visar Logic Apps Designer en skärm med förloppet för varje steg. I skärmbilden nedan ser du att ett e-postmeddelande med bifogad fil tas emot och arbetsflödet pågår.

    > [!div class="mx-imgBorder"]
    > ![Starta arbetsflödet genom att skicka ett e-postmeddelande](media/tutorial-form-recognizer-with-logic-apps/logic-apps-email-arrived-progress.png)

1. När alla steg i arbetsflödet har körts visar Logic Apps Designer en grön kryssruta mot varje steg. Välj För varje **2**i designerfönstret och välj sedan **Komponera**.

    > [!div class="mx-imgBorder"]
    > ![Arbetsflödet har slutförts](media/tutorial-form-recognizer-with-logic-apps/logic-apps-verify-output.png)

    Kopiera **utdata** från rutan UTDATA och klistra in den i valfri textredigerare.

1. Jämför JSON-utdata med exempelfakturan som du skickade som en bifogad fil i e-postmeddelandet. Kontrollera att JSON-data motsvarar data i tabellen i fakturan.

    ```json
    [
      {
        "id": "table_0",
        "columns": [
          {
            "header": [
              {
                "text": "Invoice Number",
                "boundingBox": [
                  38.5,
                  585.2,
                  113.4,
                  585.2,
                  113.4,
                  575.8,
                  38.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "7689302",
                  "boundingBox": [
                    38.5,
                    549.8,
                    77.3,
                    549.8,
                    77.3,
                    536.2,
                    38.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Date",
                "boundingBox": [
                  139.7,
                  585.2,
                  198.5,
                  585.2,
                  198.5,
                  575.8,
                  139.7,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "3/09/2015",
                  "boundingBox": [
                    139.7,
                    548.1,
                    184,
                    548.1,
                    184,
                    536.2,
                    139.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Invoice Due Date",
                "boundingBox": [
                  240.5,
                  585.2,
                  321,
                  585.2,
                  321,
                  575.8,
                  240.5,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "6/29/2016",
                  "boundingBox": [
                    240.5,
                    549,
                    284.8,
                    549,
                    284.8,
                    536.2,
                    240.5,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "Charges",
                "boundingBox": [
                  341.3,
                  585.2,
                  381.2,
                  585.2,
                  381.2,
                  575.8,
                  341.3,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "$22,123.24",
                  "boundingBox": [
                    380.6,
                    548.5,
                    430.5,
                    548.5,
                    430.5,
                    536.2,
                    380.6,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          },
          {
            "header": [
              {
                "text": "VAT ID",
                "boundingBox": [
                  442.1,
                  590,
                  474.8,
                  590,
                  474.8,
                  575.8,
                  442.1,
                  575.8
                ]
              }
            ],
            "entries": [
              [
                {
                  "text": "QR",
                  "boundingBox": [
                    447.7,
                    549.8,
                    462.6,
                    549.8,
                    462.6,
                    536.2,
                    447.7,
                    536.2
                  ],
                  "confidence": 1
                }
              ]
            ]
          }
        ]
      }
    ]
    ```
    Du har slutfört den här guiden!

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerar du ett Azure Logic Apps-arbetsflöde för att använda Formulärkonformerare för att träna en modell och extrahera innehållet i en faktura. Lär dig sedan hur du skapar en utbildningsdatauppsättning så att du kan skapa ett liknande scenario med dina egna formulär.

> [!div class="nextstepaction"]
> [Skapa en träningsdatauppsättning](build-training-data-set.md)