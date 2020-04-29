---
title: 'Självstudie: använda formulär igenkänning med Azure Logic Apps för att analysera fakturor-formulär tolken'
titleSuffix: Azure Cognitive Services
description: I den här självstudien använder du formulär igenkänning med Azure Logic Apps för att skapa ett arbets flöde som automatiserar processen med att träna en modell och testa den med hjälp av exempel data.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: tutorial
ms.date: 01/27/2020
ms.author: nitinme
ms.openlocfilehash: d71d9c7e6570e562fe4c692ede1d07b70c923cb6
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "77118263"
---
# <a name="tutorial-use-form-recognizer-with-azure-logic-apps-to-analyze-invoices"></a>Självstudie: använda formulär igenkänning med Azure Logic Apps för att analysera fakturor

I den här självstudien skapar du ett arbets flöde i Azure Logic Apps som använder formulär igenkänning, en tjänst som ingår i Azure Cognitive Services Suite för att extrahera data från fakturor. Först tränar du en formulär igenkännings modell med en exempel data uppsättning och testar sedan modellen på en annan data uppsättning.

Den här själv studie kursen beskriver följande:

> [!div class="checklist"]
> * Begär åtkomst för formulär tolken
> * Skapa en Azure Storage BLOB-behållare
> * Ladda upp exempel data till Azure Blob-behållaren
> * Skapa en Azure Logic-app
> * Konfigurera Logic-appen så att den använder en formulär igenkännings resurs
> * Testa arbets flödet genom att köra Logic app

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/).

## <a name="understand-the-invoice-to-be-analyzed"></a>Förstå fakturan som ska analyseras

Exempel data uppsättningen som du ska använda för att träna och testa modellen är tillgänglig som en. zip-fil från [GitHub](https://go.microsoft.com/fwlink/?linkid=2090451). Hämta och extrahera. zip-filen och öppna en faktura-PDF-fil i mappen **/Train** . Observera att det finns en tabell med faktura numret, faktura datumet och så vidare. 

> [!div class="mx-imgBorder"]
> ![Exempel faktura](media/tutorial-form-recognizer-with-logic-apps/sample-receipt.png)

I den här självstudien får du lära dig hur du använder ett Azure Logic Apps-arbetsflöde för att extrahera informationen från tabeller som dessa till JSON-format.

## <a name="create-an-azure-storage-blob-container"></a>Skapa en Azure Storage BLOB-behållare

Du använder den här behållaren för att ladda upp exempel data som krävs för att träna modellen.

1. Följ instruktionerna i [skapa ett Azure Storage konto](../../storage/common/storage-account-create.md) för att skapa ett lagrings konto. Använd **formrecostorage** som lagrings konto namn.
1. Följ instruktionerna i [skapa en Azure Blob-behållare](../../storage/blobs/storage-quickstart-blobs-portal.md) för att skapa en behållare i Azure Storage-kontot. Använd **formrecocontainer** som behållar namn. Kontrol lera att du ställer in den offentliga åtkomst nivån på **container (anonym Läs åtkomst för behållare och blobbar)**.

    > [!div class="mx-imgBorder"]
    > ![Skapa blobcontainer](media/tutorial-form-recognizer-with-logic-apps/create-blob-container.png)

## <a name="upload-sample-data-to-the-azure-blob-container"></a>Ladda upp exempel data till Azure Blob-behållaren

Ladda ned exempel data som är tillgängliga på [GitHub](https://go.microsoft.com/fwlink/?linkid=2090451). Extrahera data till en lokal mapp och överför innehållet i mappen **/Train** till den **formrecocontainer** som du skapade tidigare. Följ anvisningarna i [Ladda upp en Block-Blob](../../storage/blobs/storage-quickstart-blobs-portal.md#upload-a-block-blob) för att ladda upp data till en behållare.

Kopiera URL: en för behållaren. Du behöver den här URL-adressen senare i självstudien. Om du har skapat lagrings kontot och behållaren med samma namn som visas i den här självstudien blir URL *: en https\/:/formrecostorage.blob.Core.Windows.net/formrecocontainer/*.

## <a name="create-a-form-recognizer-resource"></a>Skapa en formulär igenkännings resurs

[!INCLUDE [create resource](./includes/create-resource.md)]

## <a name="create-your-logic-app"></a>Skapa en logikapp

Du kan använda Azure Logic Apps för att automatisera och dirigera uppgifter och arbets flöden. I den här självstudien skapar du en logisk app som utlöses genom att ta emot en faktura som du vill analysera som en e-postbilaga. I det här arbets flödet utför du följande uppgifter:
* Konfigurera Logic-appen så att den utlöses automatiskt när du får ett e-postmeddelande med en bifogad faktura.
* Konfigurera Logic-appen så att den använder en formulär tolken **träna modell** för att träna en modell med hjälp av exempel data som du överförde till Azure Blob Storage.
* Konfigurera Logic-appen så att den använder en formulär tolk som **analyserar** formulär för att använda den modell som du redan har tränat. Den här komponenten kommer att analysera den faktura som du tillhandahåller för den här Logic-appen baserat på den modell som den tränade tidigare.

Följ dessa steg för att konfigurera arbets flödet.

1. Från huvud menyn i Azure väljer du **skapa en app för resurs** > **integrerings** > **logik**.

1. Under **Skapa en logikapp** anger du information om din logikapp som visas här. När du är klar väljer du **skapa**.

   | Egenskap | Värde | Beskrivning |
   |----------|-------|-------------|
   | **Namn** | <*Logic – App-Name*> | Ditt Logic app-namn, som endast får innehålla bokstäver, siffror, bindestreck (`-`), under streck`_`(), parenteser (`(`, `)`) och punkter (`.`). I det här exemplet används "min-First-Logic-app". |
   | **Prenumeration** | <*Azure-prenumeration-namn*> | Ditt Azure-prenumerations namn |
   | **Resursgrupp** | <*Azure-resurs-grupp-namn*> | Namnet på den [Azure-resurs grupp](./../../azure-resource-manager/management/overview.md) som används för att organisera relaterade resurser. I det här exemplet används "My-First-LA-RG". |
   | **Position** | <*Azure-region*> | Den region där du vill lagra information om din Logic Apps. I det här exemplet används "västra USA". |
   | **Log Analytics** | Av | Behåll inställningen **Av** för diagnostisk loggning. |
   ||||

1. När Azure har distribuerat din app går du till Azure-verktygsfältet och väljer **aviseringar** > **gå till resurs** för din distribuerade Logic-app. Eller så kan du hitta och välja din Logic-app genom att skriva namnet i sökrutan.

   Logic Apps Designer öppnas och det visas en sida med en introduktionsvideo och vanliga utlösare. Under **Mallar** väljer du **Tom logikapp**.

   > [!div class="mx-imgBorder"]
   > ![Välj tom mall för Logic app](./../../logic-apps/media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

### <a name="configure-the-logic-app-to-trigger-the-workflow-when-an-email-arrives"></a>Konfigurera Logic-appen så att den utlöser arbets flödet när ett e-postmeddelande tas emot

I den här självstudien utlöser du arbets flödet när ett e-postmeddelande tas emot med en bifogad faktura. I den här självstudien används Office 365 som e-posttjänst, men du kan använda andra e-postleverantörer som du vill använda.

1. Välj alla på flikarna, Välj **Office 365 Outlook**och välj sedan **när ett nytt e-postmeddelande kommer**under **utlösare**.

    ![Utlös Logic app via en inkommande e-post](media/tutorial-form-recognizer-with-logic-apps/logic-app-email-trigger.png)

1. I rutan **Office 365 Outlook** klickar du på **Logga**in och anger informationen för att logga in på ett Office 365-konto.

1. Utför följande steg i nästa dialog ruta.
    1. Välj den mapp som ska övervakas för nya e-postmeddelanden.
    1. För **har bifogade filer**väljer du **Ja**. Detta säkerställer att endast e-postmeddelanden med bifogade filer utlöser arbets flödet.
    1. För **Inkludera bifogade filer**väljer du **Ja**. Detta säkerställer att innehållet i den bifogade filen används i underordnad bearbetning.

        > [!div class="mx-imgBorder"]
        > ![Konfigurera e-utlösare för Logic app](media/tutorial-form-recognizer-with-logic-apps/logic-app-specify-email-folder.png)

1. Klicka på **Spara** i verktygsfältet längst upp.

### <a name="configure-the-logic-app-to-use-form-recognizer-train-model-operation"></a>Konfigurera Logic-appen så att den använder formulär tolken träna modell drift

Innan du kan använda formulär igenkännings tjänsten för att analysera fakturor måste du träna en modell genom att tillhandahålla den här typen av exempel fakturor som modellen kan analysera och lära sig från.

1. Välj **nytt steg**och under **Välj en åtgärd**söker du efter **formulär igenkänning**. Från resultaten som visas, väljer du **formulär tolken**och under de åtgärder som är tillgängliga för formulär igenkänning väljer du **träna modell**.

    > [!div class="mx-imgBorder"]
    > ![Träna en formulär igenkännings modell](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-train-model.png)

1. Ange ett namn för anslutningen i dialog rutan formulär tolk och ange slut punkts-URL: en och den nyckel som du hämtade för formulär igenkännings resursen.

    > [!div class="mx-imgBorder"]
    > ![Anslutnings namn för formulär tolken](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-create-connection.png)

    Klicka på **Skapa**.

1. I dialog rutan **träna modell** anger du URL **för den**behållare där du laddade upp exempel data.

    > [!div class="mx-imgBorder"]
    > ![Lagrings behållare för exempel fakturor](media/tutorial-form-recognizer-with-logic-apps/source-for-train-model.png)

1. Klicka på **Spara** i verktygsfältet längst upp.

### <a name="configure-the-logic-app-to-use-the-form-recognizer-analyze-form-operation"></a>Konfigurera Logi Kap par för att använda formulär tolken analys formulär åtgärd

I det här avsnittet lägger du till åtgärden **analysera formulär** i arbets flödet. Den här åtgärden använder den redan utbildade modellen för att analysera en ny faktura som har angetts för Logic app.

1. Välj **nytt steg**och under **Välj en åtgärd**söker du efter **formulär igenkänning**. Från resultaten som visas, väljer du **formulär tolken**och väljer **analysera formulär**under de åtgärder som är tillgängliga för formulär igenkänning.

    > [!div class="mx-imgBorder"]
    > ![Analysera en formulär tolks modell](media/tutorial-form-recognizer-with-logic-apps/logic-app-form-reco-analyze-model.png)

1. Utför följande steg i dialog rutan **analysera formulär** :

    1. Klicka på text rutan **modell-ID** och välj **modelId**i dialog rutan som öppnas under fliken **dynamiskt innehåll** . Genom att göra detta ger du Flow-appen med modell-ID: t för den modell som du tränade i det sista avsnittet.

        > [!div class="mx-imgBorder"]
        > ![Använda ModelID för formulär igenkänning](media/tutorial-form-recognizer-with-logic-apps/analyze-form-model-id.png)

    2. Klicka på text rutan **dokument** och i dialog rutan som öppnas, under fliken **dynamiskt innehåll** , väljer du **bilagor innehåll**. Detta konfigurerar flödet så att det använder exempel faktura filen som är bifogad i e-postmeddelandet som utlöser arbets flödet.

        > [!div class="mx-imgBorder"]
        > ![Använd e-postbilaga för att analysera fakturor](media/tutorial-form-recognizer-with-logic-apps/analyze-form-input-data.png)

1. Klicka på **Spara** i verktygsfältet längst upp.

### <a name="extract-the-table-information-from-the-invoice"></a>Extrahera tabell informationen från fakturan

I det här avsnittet konfigurerar du Logic-appen så att informationen från tabellen i fakturorna extraheras.

1. Välj **Lägg till en åtgärd**och under **Välj en åtgärd**söker du efter **Skriv** och under de åtgärder som är tillgängliga väljer du **skapa** igen.
    ![Extrahera tabell information från fakturan](media/tutorial-form-recognizer-with-logic-apps/extract-table.png)

1. I dialog rutan **Skriv** klickar du på text rutan **indata** och väljer **tabeller**i dialog rutan som öppnas.

    > [!div class="mx-imgBorder"]
    > ![Extrahera tabell information från fakturan](media/tutorial-form-recognizer-with-logic-apps/select-tables.png)

1. Klicka på **Spara**.

## <a name="test-your-logic-app"></a>Testa din Logic app

Om du vill testa Logic-appen använder du exempel fakturorna i mappen **/test** i den exempel data uppsättning som du laddade ned från [GitHub](https://go.microsoft.com/fwlink/?linkid=2090451). Följ de här stegen:

1. Från Azure Logic Apps designer för din app väljer du **Kör** i verktygsfältet längst upp. Arbets flödet är nu aktivt och väntar på att få ett e-postmeddelande med fakturan bifogad.
1. Skicka ett e-postmeddelande med en exempel faktura som är kopplad till den e-postadress som du angav när du skapade Logic-appen. Se till att e-postmeddelandet levereras till den mapp som du angav när du konfigurerade Logic-appen.
1. Så snart e-postmeddelandet levereras till mappen visar Logic Apps designer en skärm med förloppet för varje steg. I skärm bilden nedan ser du att ett e-postmeddelande med en bifogad fil tas emot och att arbets flödet pågår.

    > [!div class="mx-imgBorder"]
    > ![Starta arbets flödet genom att skicka ett e-postmeddelande](media/tutorial-form-recognizer-with-logic-apps/logic-apps-email-arrived-progress.png)

1. När alla steg i arbets flödet har körts, visar Logic Apps designer en grön kryss ruta mot varje steg. I fönstret designer väljer du **för varje 2**och väljer sedan **Skriv**.

    > [!div class="mx-imgBorder"]
    > ![Arbets flöde slutfört](media/tutorial-form-recognizer-with-logic-apps/logic-apps-verify-output.png)

    Kopiera utdata i rutan **utdata** och klistra in den i valfri text redigerare.

1. Jämför JSON-utdata med exempel fakturan som du skickade som en bifogad fil i e-postmeddelandet. Kontrol lera att JSON-data motsvarar data i tabellen i fakturan.

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
    Nu har du slutfört den här självstudien!

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapar du ett Azure Logic Apps-arbetsflöde för att använda formulär igenkänning för att träna en modell och extrahera innehållet i en faktura. Nu ska du lära dig hur du skapar en tränings data uppsättning så att du kan skapa ett liknande scenario med dina egna formulär.

> [!div class="nextstepaction"]
> [Skapa en träningsdatauppsättning](build-training-data-set.md)