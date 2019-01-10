---
title: 'Självstudier: Utföra åtgärder för avbildning – Java'
titlesuffix: Azure Cognitive Services
description: Utforska en grundläggande Java Swing-app som använder API för visuellt innehåll i Azure Cognitive Services. Utför OCR, skapa miniatyrer och arbeta med visuella funktioner i en bild.
services: cognitive-services
author: KellyDF
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: tutorial
ms.author: kefre
ms.custom: seodec18
ms.date: 09/21/2017
ms.openlocfilehash: ef1180a8e8c833648e3b61705abf354cb4dfaed3
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117494"
---
# <a name="tutorial-computer-vision-api-java"></a>Självstudier: API för visuellt innehåll – Java

Den här självstudien visar funktionerna i Azure Cognitive Services REST API för visuellt innehåll.

Utforska ett Java Swing-program som använder REST API för visuellt innehåll för att utföra optisk teckenigenkänning (OCR), skapa miniatyrbilder med smart beskärning och identifiera, kategorisera, tagga och beskriva visuella funktioner, inklusive ansikten i en bild. Det här exemplet låter dig skicka en bild-URL för analys eller bearbetning. Du kan använda det här exemplet med öppen källkod som en mall för att skapa din egen app i Java och använda REST API för visuellt innehåll.

Den här självstudien beskrivs hur du använder API för visuellt innehåll till att:

> [!div class="checklist"]
> * Analysera en bild
> * Identifiera ett naturligt eller konstgjort landmärke i en bild
> * Identifiera en kändis i en bild
> * Skapa en kvalitetsminiatyr från en bild
> * Läs tryckt text i en bild
> * Läs handskriven text i en bild

Java Swing-formulärprogrammet har redan skrivits, men har inga funktioner. I den här självstudien får du lägga till den specifika koden för REST API för visuellt innehåll för att slutföra programmets funktioner.

## <a name="prerequisites"></a>Nödvändiga komponenter

### <a name="platform-requirements"></a>Plattformskrav

Den här självstudien har utvecklats med hjälp av NetBeans IDE. Mer specifikt den **Java SE**-version av NetBeans som du kan [hämta här](https://netbeans.org/downloads/index.html).

### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Prenumerera på API för visuellt innehåll och få en prenumerationsnyckel 

Innan du skapar exemplet, måste du prenumerera på API för visuellt innehåll som ingår i Azure Cognitive Services. Information om prenumeration och nyckelhantering finns i [Prenumerationer](https://azure.microsoft.com/try/cognitive-services/). Både de primära och sekundära nycklarna kan användas i den här självstudien. 

## <a name="acquire-the-incomplete-tutorial-project"></a>Hämta det ofullständiga självstudieprojektet

### <a name="download-the-tutorial-project"></a>Hämta självstudieprojektet

1. Gå till lagringsplatsen för [Cognitive Services Java-självstudien för visuellt innehåll ](https://github.com/Azure-Samples/cognitive-services-java-computer-vision-tutorial).
1. Klicka på knappen **Klona eller hämta**.
1. Klicka på **Ladda ned ZIP** för att hämta en .zip-fil för självstudieprojektet.

Du behöver inte extrahera innehållet i ZIP-filen eftersom NetBeans importerar projektet från ZIP-filen.

### <a name="import-the-tutorial-project"></a>Importera självstudieprojektet

Importera filen **cognitive-services-java-computer-vision-tutorial-master.zip** till NetBeans.

1. I NetBeans, klickar du på **Arkiv** > **Importera projekt** > **från ZIP...**. Dialogrutan **Importera projekt från ZIP** visas.
1. I fältet **ZIP-fil:**, klickar du på **Bläddra** för att leta efter filen **cognitive-services-java-computer-vision-tutorial-master.zip** och klickar sedan på **Öppna**.
1. Klicka på **Importera** från dialogrutan **Importera projekt från ZIP**.
1. I fönstrert **Projekt**, expanderar du **ComputerVision** > **Källpaket** > **&lt;standardpaket&gt;**. 
   Vissa versioner av NetBeans använder **src** istället för **Källpaket** > **&lt;standardpaket&gt;**. I så fall expanderar du **src**.
1. Dubbelklicka på **MainFrame.java** för att läsa in filen till NetBeans-redigeraren. Fliken **Design** i filen **MainFrame.java** visas.
1. Klicka på fliken **Källa** för att visa Java-källkoden.

### <a name="build-and-run-the-tutorial-project"></a>Skapa och kör självstudieprojektet

1. Tryck på **F6** för att skapa och köra självstudieprogrammet.

    I självstudieprogrammet, klickar du på en flik för att öppna fönstret för den funktionen. Knapparna har tomma metoder så de gör ingenting.

    Längst ned i fönstret är fälten **Prenumerationsnyckel** och **Prenumerationsregion**. Dessa fält måste fyllas i med en giltig prenumerationsnyckel och rätt region för prenumerationsnyckeln. Du kan skaffa en prenumerationsnyckel genom att följa anvisningarna i [Prenumerationer](https://azure.microsoft.com/try/cognitive-services/). Om du har köpt din prenumerationsnyckel från den kostnadsfria utvärderingsversionen via den länken så är standardregionen **westcentralus** rätt region för dina prenumerationsnycklar.

1. Avsluta självstudieprogrammet.

## <a name="add-the-tutorial-code-to-the-project"></a>Lägga till självstudiekursens kod i projektet

Java Swing-programmet konfigureras med sex flikar. Varje fil visar en annan funktion för API för visuellt innehåll (analysera, OCR, osv). De sex självstudiedelarna har inte beroenden så du kan lägga till ett avsnitt, alla sex delar eller valfri delmängd. Du kan lägga till delarna i valfri ordning.

### <a name="analyze-an-image"></a>Analysera en bild

Funktionen Analysera i API för visuellt innehåll genomsöker en bild efter fler än 2 000 identifierbara objekt, levande varelser, landskap och åtgärder. När analysen är klar, returnerar analysera ett JSON-objekt som beskriver bilden med beskrivande taggar, färganalys, beskrivningar och mer.

Utför följande steg för att slutföra analyserafunktionen i självstudieprogrammet:

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Lägga till händelsehanterarkoden för formulär-knappen

Händelsehanterarfunktionen **analyzeImageButtonActionPerformed** rensar formuläret, visar den bild som angetts i URL:en och anropar sedan metoden **AnalyzeImage** för att analysera bilden. När **AnalyzeImage** returneras, visar metoden det formaterade JSON-svaret i textområdet **Svar**, extraherar den första texten från **JSONObject** och visar beskrivningen och konfidensnivån att texten är korrekt.

Kopiera och klistra in följande kod till **analyzeImageButtonActionPerformed**-metoden.

> [!NOTE]
> NetBeans låter dig inte klistra in till metodens definitionsrad (```private void```) eller till den avslutande klammerparentesen för den metoden. Kopiera koden genom att kopiera raderna mellan metoddefinitionen och den avslutande klammerparentesen och klistra in dem över innehållet i metoden.

```java
    private void analyzeImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL analyzeImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        analyzeImage.setIcon(new ImageIcon());
        analyzeCaptionLabel.setText("");
        analyzeResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            analyzeImageUrl = new URL(analyzeImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(analyzeImageUrl);
            scaleAndShowImage(bImage, analyzeImage);
        } catch(IOException e) {
            analyzeResponseTextArea.setText("Error loading Analyze image: " + e.getMessage());
            return;
        }
        
        // Analyze the image.
        JSONObject jsonObj = AnalyzeImage(analyzeImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        analyzeResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("description") && jsonObj.getJSONObject("description").has("captions")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("description").getJSONArray("captions").getJSONObject(0);
            
            if (jsonCaption.has("text") && jsonCaption.has("confidence")) {
                
                analyzeCaptionLabel.setText("Caption: " + jsonCaption.getString("text") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Lägga till adaptern för REST API-anropet

Metoden **AnalyzeImage** omsluter REST API-anropet för att analysera en bild. Metoden returnerar ett **JSONObject** som beskriver bilden, eller **null** om ett fel uppstod.

Kopiera och klistra in metoden **AnalyzeImage** under metoden **analyzeImageButtonActionPerformed**.

```java
    /**
     * Encapsulates the Microsoft Cognitive Services REST API call to analyze an image.
     * @param imageUrl: The string URL of the image to analyze.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject AnalyzeImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call for Analyze Image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseAnalyze;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color,Adult");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
 ```

#### <a name="run-the-application"></a>Köra programmet

Tryck på **F6** för att köra programmet. Placera din prenumerationsnyckel i fältet **Prenumerationsnyckel** och kontrollera att du använder rätt region i **Prenumerationsregion**. Ange en URL för en bild som ska analyseras och klicka sedan på knappen **Analysera bild** för att analysera bilden och se resultatet.

### <a name="recognize-a-landmark"></a>Identifiera ett landmärke

Funktionen landmärken i visuellt innehåll analyserar en bild efter naturliga och konstgjorda landmärken, till exempel berg eller berömda byggnader. När analysen är klar, returnerar Landmärke ett JSON-objekt som identifierar landmärken i bilden.

Utför följande steg för att slutföra funktionen Landmärken i självstudiekursen:

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Lägga till händelsehanterarkoden för formulär-knappen

Händelsehanterarmetoden **landmarkImageButtonActionPerformed** rensar formuläret, visar den bild som angetts i URL:en och anropar sedan metoden **LandmarkImage** för att analysera bilden. När **LandmarkImage** returnerar, visar metoden det formaterade JSON-svaret i textområdet **Svar**, extraherar det första landmärkesnamnet från **JSONObject** och visar det i fönstret tillsammans med konfidensnivån att landmärket identifierades korrekt.

Kopiera och klistra in följande kod till metoden **landmarkImageButtonActionPerformed**.

> [!NOTE]
> NetBeans låter dig inte klistra in till metodens definitionsrad (```private void```) eller till den avslutande klammerparentesen för den metoden. Kopiera koden genom att kopiera raderna mellan metoddefinitionen och den avslutande klammerparentesen och klistra in dem över innehållet i metoden.

```java
    private void landmarkImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL landmarkImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        landmarkImage.setIcon(new ImageIcon());
        landmarkCaptionLabel.setText("");
        landmarkResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            landmarkImageUrl = new URL(landmarkImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(landmarkImageUrl);
            scaleAndShowImage(bImage, landmarkImage);
        } catch(IOException e) {
            landmarkResponseTextArea.setText("Error loading Landmark image: " + e.getMessage());
            return;
        }
        
        // Identify the landmark in the image.
        JSONObject jsonObj = LandmarkImage(landmarkImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        landmarkResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("result") && jsonObj.getJSONObject("result").has("landmarks")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("result").getJSONArray("landmarks").getJSONObject(0);
            
            if (jsonCaption.has("name") && jsonCaption.has("confidence")) {

                landmarkCaptionLabel.setText("Caption: " + jsonCaption.getString("name") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Lägga till adaptern för REST API-anropet

Metoden **LandmarkImage** omsluter REST API-anropet för att analysera en bild. Metoden returnerar ett **JSONObject** som beskriver de landmärken som identifierades i bilden, eller **null** om ett fel uppstod.

Kopiera och klistra in metoden **LandmarkImage** under metoden **landmarkImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to identify a landmark in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject LandmarkImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify a Landmark in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseLandmark;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-application"></a>Köra programmet

Tryck på **F6** för att köra programmet. Placera din prenumerationsnyckel i fältet **Prenumerationsnyckel** och kontrollera att du använder rätt region i **Prenumerationsregion**. Klicka på fliken **Landmärke**, ange en URL för en bild av ett landmärke och klicka sedan på knappen **Analysera bild** för att analysera bilden och se resultatet.

### <a name="recognize-celebrities"></a>Identifiera kändisar

Funktionen kändisar i visuellt innehåll analyserar en bild efter kända personer. När analysen är klar, returnerar Kändisar ett JSON-objekt som identifierar de kändisar som identifierats i bilden.

Utför följande steg för att slutföra funktionen Kändisar i självstudiekursen:

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Lägga till händelsehanterarkoden för formulär-knappen

Händelsehanterarmetoden **celebritiesImageButtonActionPerformed** rensar formuläret, visar den bild som angetts i URL:en och anropar sedan metoden **CelebritiesImage** för att analysera bilden. När **CelebritiesImage** returnerar, visar metoden det formaterade JSON-svaret i textområdet **Svar**, extraherar det första kändisnamnet från **JSONObject** och visar det i fönstret tillsammans med konfidensnivån att kändisen identifierades korrekt.

Kopiera och klistra in följande kod till metoden **celebritiesImageButtonActionPerformed**.

> [!NOTE]
> NetBeans låter dig inte klistra in till metodens definitionsrad (```private void```) eller till den avslutande klammerparentesen för den metoden. Kopiera koden genom att kopiera raderna mellan metoddefinitionen och den avslutande klammerparentesen och klistra in dem över innehållet i metoden.

```java
    private void celebritiesImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL celebritiesImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        celebritiesImage.setIcon(new ImageIcon());
        celebritiesCaptionLabel.setText("");
        celebritiesResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            celebritiesImageUrl = new URL(celebritiesImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(celebritiesImageUrl);
            scaleAndShowImage(bImage, celebritiesImage);
        } catch(IOException e) {
            celebritiesResponseTextArea.setText("Error loading Celebrity image: " + e.getMessage());
            return;
        }
        
        // Identify the celebrities in the image.
        JSONObject jsonObj = CelebritiesImage(celebritiesImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        celebritiesResponseTextArea.setText(jsonObj.toString(2));
                
        // Extract the text and confidence from the first caption in the description object.
        if (jsonObj.has("result") && jsonObj.getJSONObject("result").has("celebrities")) {

            JSONObject jsonCaption = jsonObj.getJSONObject("result").getJSONArray("celebrities").getJSONObject(0);
            
            if (jsonCaption.has("name") && jsonCaption.has("confidence")) {

                celebritiesCaptionLabel.setText("Caption: " + jsonCaption.getString("name") + 
                        " (confidence: " + jsonCaption.getDouble("confidence") + ").");
            }
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Lägga till adaptern för REST API-anropet

Metoden **CelebritiesImage** omsluter REST API-anropet för att analysera en bild. Metoden returnerar ett **JSONObject** som beskriver de kändisar som identifierades i bilden, eller **null** om ett fel uppstod.

Kopiera och klistra in metoden **CelebritiesImage** under metoden **celebritiesImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to identify celebrities in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject CelebritiesImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify celebrities in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseCelebrities;
            URIBuilder builder = new URIBuilder(uriString);

            // Request parameters. All of them are optional.
            builder.setParameter("visualFeatures", "Categories,Description,Color");
            builder.setParameter("language", "en");

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-application"></a>Köra programmet

Tryck på **F6** för att köra programmet. Placera din prenumerationsnyckel i fältet **Prenumerationsnyckel** och kontrollera att du använder rätt region i **Prenumerationsregion**. Klicka på fliken **Kändisar**, ange en URL för en bild av en kändis och klicka sedan på knappen **Analysera bild** för att analysera bilden och se resultatet.

### <a name="intelligently-generate-a-thumbnail"></a>Generera en miniatyrbild

Miniatyrfunktionen i visuellt innehåll skapar en miniatyrbild från en bild. Med hjälp av funktionen **Smart beskärning** så identifierar miniatyrfunktionen intresseområdet i en bild och centrerar miniatyrbilden i det här området att skapa mer estetiskt tilltalande miniatyrbilder.

Utför följande steg för att slutföra miniatyrfunktionen i självstudiekursen:

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Lägga till händelsehanterarkoden för formulär-knappen

Händelsehanterarmetoden **thumbnailImageButtonActionPerformed** rensar formuläret, visar den bild som angetts i URL:en och anropar sedan metoden **getThumbnailImage** för att skapa miniatyrbilden. När **getThumbnailImage** returnerar, visar metoden den skapade miniatyrbilden.

Kopiera och klistra in följande kod till metoden **thumbnailImageButtonActionPerformed**.

> [!NOTE]
> NetBeans låter dig inte klistra in till metodens definitionsrad (```private void```) eller till den avslutande klammerparentesen för den metoden. Kopiera koden genom att kopiera raderna mellan metoddefinitionen och den avslutande klammerparentesen och klistra in dem över innehållet i metoden.

```java
    private void thumbnailImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL thumbnailImageUrl;
        JSONObject jsonError[] = new JSONObject[1];
        
        // Clear out the previous image, response, and thumbnail, if any.
        thumbnailSourceImage.setIcon(new ImageIcon());
        thumbnailResponseTextArea.setText("");
        thumbnailImage.setIcon(new ImageIcon());

        // Display the image specified in the text box.
        try {
            thumbnailImageUrl = new URL(thumbnailImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(thumbnailImageUrl);
            scaleAndShowImage(bImage, thumbnailSourceImage);
        } catch(IOException e) {
            thumbnailResponseTextArea.setText("Error loading image to thumbnail: " + e.getMessage());
            return;
        }
        
        // Get the thumbnail for the image.
        BufferedImage thumbnail = getThumbnailImage(thumbnailImageUrl.toString(), jsonError);
        
        // A non-null value indicates error.
        if (jsonError[0] != null) {
            // Format and display the JSON error.
            thumbnailResponseTextArea.setText(jsonError[0].toString(2));
            return;
        }
        
        // Display the thumbnail.
        if (thumbnail != null) {
            scaleAndShowImage(thumbnail, thumbnailImage);
        }
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Lägga till adaptern för REST API-anropet

Metoden **getThumbnailImage** omsluter REST API-anropet för att analysera en bild. Metoden returnerar en **BufferedImage** som innehåller miniatyrbilden eller **null** om ett fel uppstod. Felmeddelandet kommer att returneras i det första elementet av **jsonError**-strängmatrisen.

Kopiera och klistra in följande **getThumbnailImage**-metod under **thumbnailImageButtonActionPerformed**-metoden.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to create a thumbnail for an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A BufferedImage containing the thumbnail, or null if a runtime error occurs. In the case
     * of an error, the error message will be returned in the first element of the jsonError string array.
     */
    private BufferedImage getThumbnailImage(String imageUrl, JSONObject[] jsonError) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to identify celebrities in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseThumbnail;
            URIBuilder uriBuilder = new URIBuilder(uriString);

            // Request parameters.
            uriBuilder.setParameter("width", "100");
            uriBuilder.setParameter("height", "150");
            uriBuilder.setParameter("smartCropping", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity requestEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(requestEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // Check for success.
            if (response.getStatusLine().getStatusCode() == 200)
            {
                // Return the thumbnail.
                return ImageIO.read(entity.getContent());
            }
            else
            {
                // Format and display the JSON error message.
                String jsonString = EntityUtils.toString(entity);
                jsonError[0] = new JSONObject(jsonString);
                return null;
            }
        }
        catch (Exception e)
        {
            String errorMessage = e.getMessage();
            System.out.println(errorMessage);
            jsonError[0] = new JSONObject(errorMessage);
            return null;
        }
    }
```

#### <a name="run-the-application"></a>Köra programmet

Tryck på **F6** för att köra programmet. Placera din prenumerationsnyckel i fältet **Prenumerationsnyckel** och kontrollera att du använder rätt region i **Prenumerationsregion**. Klicka på fliken **Miniatyrer**, ange en URL för en bild och klicka sedan på knappen **Skapa miniatyrer** för att analysera bilden och se resultatet.

### <a name="read-printed-text-ocr"></a>Läs tryckt text (OCR)

Optisk teckenigenkänning (OCR)-funktionen för visuellt innehåll analyserar en bild av tryckt text. När analysen är klar, returnerar OCR en JSON-objekt som innehåller texten och placeringen av texten i bilden.

Utför följande steg för att slutföra OCR-funktionen i självstudien:

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Lägga till händelsehanterarkoden för formulär-knappen

Händelsehanterarfunktionen **ocrImageButtonActionPerformed** rensar formuläret, visar den bild som angetts i URL:en och anropar sedan metoden **OcrImage** för att analysera bilden. När **OcrImage** returnerar, visar metoden den identifierade texten som formaterad JSON i textområdet **svar**.

Kopiera och klistra in följande kod till **ocrImageButtonActionPerformed**-metoden.

> [!NOTE]
> NetBeans låter dig inte klistra in till metodens definitionsrad (```private void```) eller till den avslutande klammerparentesen för den metoden. Kopiera koden genom att kopiera raderna mellan metoddefinitionen och den avslutande klammerparentesen och klistra in dem över innehållet i metoden.

```java
    private void ocrImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL ocrImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        ocrImage.setIcon(new ImageIcon());
        ocrResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            ocrImageUrl = new URL(ocrImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(ocrImageUrl);
            scaleAndShowImage(bImage, ocrImage);
        } catch(IOException e) {
            ocrResponseTextArea.setText("Error loading OCR image: " + e.getMessage());
            return;
        }
        
        // Read the text in the image.
        JSONObject jsonObj = OcrImage(ocrImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        ocrResponseTextArea.setText(jsonObj.toString(2));
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Lägga till adaptern för REST API-anropet

Metoden **OcrImage** omsluter REST API-anropet för att analysera en bild. Metoden returnerar en **JSONObject** av de JSON-data som returneras från anropet eller **null** om ett fel uppstod.

Kopiera och klistra in metoden **OcrImage** under metoden **ocrImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to read text in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject OcrImage(String imageUrl) {
        try (CloseableHttpClient httpclient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to read text in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseOcr;
            URIBuilder uriBuilder = new URIBuilder(uriString);

            // Request parameters.
            uriBuilder.setParameter("language", "unk");
            uriBuilder.setParameter("detectOrientation ", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();

            // If we got a response, parse it and display it.
            if (entity != null)
            {
                // Return the JSONObject.
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            } else {
                // No response. Return null.
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-application"></a>Köra programmet

Tryck på **F6** för att köra programmet. Placera din prenumerationsnyckel i fältet **Prenumerationsnyckel** och kontrollera att du använder rätt region i **Prenumerationsregion**. Klicka på fliken **OCR**, ange en URL till en bild av skriven text och klicka sedan på knappen **Läs bild** för att analysera en bild och se resultatet.

### <a name="read-handwritten-text-handwriting-recognition"></a>Läs handskriven text (handskriftsigenkänning)

Funktionen handskriftsigenkänning i API för visuellt innehåll analyserar en bild av handskriven text. När analysen är klar returnerar handskriftsigenkänning ett JSON-objekt som innehåller texten och placeringen av texten i bilden.

Utför följande steg för att slutföra funktionen handskriftsigenkänning i självstudieprogrammet:

#### <a name="add-the-event-handler-code-for-the-form-button"></a>Lägga till händelsehanterarkoden för formulär-knappen

Händelsehanterarfunktionen **handwritingImageButtonActionPerformed** rensar formuläret, visar den bild som angetts i URL:en och anropar sedan metoden **HandwritingImage** för att analysera bilden. När **HandwritingImage** returnerar, visar metoden den identifierade texten som formaterad JSON i textområdet **Svar**.

Kopiera och klistra in följande kod till **handwritingImageButtonActionPerformed**-metoden.

> [!NOTE]
> NetBeans låter dig inte klistra in till metodens definitionsrad (```private void```) eller till den avslutande klammerparentesen för den metoden. Kopiera koden genom att kopiera raderna mellan metoddefinitionen och den avslutande klammerparentesen och klistra in dem över innehållet i metoden.

```java
    private void handwritingImageButtonActionPerformed(java.awt.event.ActionEvent evt) {
        URL handwritingImageUrl;
        
        // Clear out the previous image, response, and caption, if any.
        handwritingImage.setIcon(new ImageIcon());
        handwritingResponseTextArea.setText("");
        
        // Display the image specified in the text box.
        try {
            handwritingImageUrl = new URL(handwritingImageUriTextBox.getText());
            BufferedImage bImage = ImageIO.read(handwritingImageUrl);
            scaleAndShowImage(bImage, handwritingImage);
        } catch(IOException e) {
            handwritingResponseTextArea.setText("Error loading Handwriting image: " + e.getMessage());
            return;
        }
        
        // Read the text in the image.
        JSONObject jsonObj = HandwritingImage(handwritingImageUrl.toString());
        
        // A return of null indicates failure.
        if (jsonObj == null) {
            return;
        }
        
        // Format and display the JSON response.
        handwritingResponseTextArea.setText(jsonObj.toString(2));
    }
```

#### <a name="add-the-wrapper-for-the-rest-api-call"></a>Lägga till adaptern för REST API-anropet

Metoden **HandwritingImage** omsluter de två REST API-anrop som behövs för att analysera en bild. Eftersom handskriftsigenkänning är en tidskrävande process så används en tvåstegsprocess. Det första anropet skickar bilden för bearbetning. Det andra anropet hämtar den identifierade texten när bearbetningen är klar.

När texten hämtas returnerar **HandwritingImage**-metoden en **JSONObject** som beskriver texten och platserna för texten, eller **null** om ett fel uppstod.

Kopiera och klistra in metoden **HandwritingImage** under metoden **handwritingImageButtonActionPerformed**.

```java
     /**
     * Encapsulates the Microsoft Cognitive Services REST API call to read handwritten text in an image.
     * @param imageUrl: The string URL of the image to process.
     * @return: A JSONObject describing the image, or null if a runtime error occurs.
     */
    private JSONObject HandwritingImage(String imageUrl) {
        try (CloseableHttpClient textClient = HttpClientBuilder.create().build();
             CloseableHttpClient resultClient = HttpClientBuilder.create().build())
        {
            // Create the URI to access the REST API call to read text in an image.
            String uriString = uriBasePreRegion + 
                    String.valueOf(subscriptionRegionComboBox.getSelectedItem()) + 
                    uriBasePostRegion + uriBaseHandwriting;
            URIBuilder uriBuilder = new URIBuilder(uriString);
            
            // Request parameters.
            uriBuilder.setParameter("handwriting", "true");

            // Prepare the URI for the REST API call.
            URI uri = uriBuilder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());

            // Request body.
            StringEntity reqEntity = new StringEntity("{\"url\":\"" + imageUrl + "\"}");
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response.
            HttpResponse textResponse = textClient.execute(request);
            
            // Check for success.
            if (textResponse.getStatusLine().getStatusCode() != 202) {
                // An error occurred. Return the JSON error message.
                HttpEntity entity = textResponse.getEntity();
                String jsonString = EntityUtils.toString(entity);
                return new JSONObject(jsonString);
            }
            
            String operationLocation = null;

            // The 'Operation-Location' in the response contains the URI to retrieve the recognized text.
            Header[] responseHeaders = textResponse.getAllHeaders();
            for(Header header : responseHeaders) {
                if(header.getName().equals("Operation-Location"))
                {
                    // This string is the URI where you can get the text recognition operation result.
                    operationLocation = header.getValue();
                    break;
                }
            }
            
            // NOTE: The response may not be immediately available. Handwriting recognition is an
            // async operation that can take a variable amount of time depending on the length
            // of the text you want to recognize. You may need to wait or retry this operation.
            //
            // This example checks once per second for ten seconds.
            
            JSONObject responseObj = null;
            int i = 0;
            do {
                // Wait one second.
                Thread.sleep(1000);
                
                // Check to see if the operation completed.
                HttpGet resultRequest = new HttpGet(operationLocation);
                resultRequest.setHeader("Ocp-Apim-Subscription-Key", subscriptionKeyTextField.getText());
                HttpResponse resultResponse = resultClient.execute(resultRequest);
                HttpEntity responseEntity = resultResponse.getEntity();
                if (responseEntity != null)
                {
                    // Get the JSON response.
                    String jsonString = EntityUtils.toString(responseEntity);
                    responseObj = new JSONObject(jsonString);
                }
            }
            while (i < 10 && responseObj != null &&
                    !responseObj.getString("status").equalsIgnoreCase("Succeeded"));
            
            // If the operation completed, return the JSON object.
            if (responseObj != null) {
                return responseObj;
            } else {
                // Return null for timeout error.
                System.out.println("Timeout error.");
                return null;
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
            return null;
        }
    }
```

#### <a name="run-the-application"></a>Köra programmet

Tryck på **F6** för att köra programmet. Placera din prenumerationsnyckel i fältet **Prenumerationsnyckel** och kontrollera att du använder rätt region i **Prenumerationsregion**. Klicka på fliken **Läs handskriven text**, ange en URL till en bild av handskriven text och klicka sedan på knappen **Läs bild** för att analysera en bild och se resultatet.

## <a name="next-steps"></a>Nästa steg

- [Självstudie: API för visuellt innehåll med C&#35;](CSharpTutorial.md)
- [Självstudie: API för visuellt innehåll med Python](PythonTutorial.md)
