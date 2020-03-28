---
title: 'Självstudiekurs: Generera metadata för Azure-avbildningar'
titleSuffix: Azure Cognitive Services
description: I den här självstudien får du lära dig hur du integrerar Azure Computer Vision-tjänsten i en webbapp för att generera metadata för avbildningar.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 7c83350dbecaf20e9b35f159b2c01824777bc665
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973721"
---
# <a name="tutorial-use-computer-vision-to-generate-image-metadata-in-azure-storage"></a>Självstudiekurs: Använd datorseende för att generera bildmetadata i Azure Storage

I den här självstudien får du lära dig hur du integrerar Azure Computer Vision-tjänsten i en webbapp för att generera metadata för uppladdade avbildningar. Detta är användbart för [FALL-scenarier (Digital Asset Management),](../Home.md#computer-vision-for-digital-asset-management) till exempel om ett företag snabbt vill generera beskrivande bildtexter eller sökbara nyckelord för alla bilder.

En fullständig appguide finns i [labbet för Azure Storage och Cognitive Services](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md) på GitHub, och den här självstudien omfattar i stort sett Övning 5 i labben. Du kanske vill skapa hela programmet genom att följa varje steg, men om du bara vill lära dig att integrera datorseende i en befintlig webbapp, läs här.

I den här självstudiekursen lär du dig att:

> [!div class="checklist"]
> * Skapa en resurs för Visuellt innehåll i Azure
> * Utföra bildanalys på Azure Storage-bilder
> * Koppla metadata till Azure Storage-bilder
> * Kontrollera bildmetadata med hjälp av Azure Storage Explorer

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar. 

## <a name="prerequisites"></a>Krav

- [Visual Studio 2017 Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) eller senare med arbetsbelastningarna ”ASP.NET and web development” (ASP.NET och webbutveckling) och ”Azure development” (Azure-utveckling) installerade.
- Ett Azure Storage-konto med en blob-behållare konfigurerad för [avbildningslagring](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise1) (följ övningar 1 i Azure Storage Lab om du behöver hjälp med det här steget).
- Verktyget Azure Storage Explorer (följ [övning 2 i Azure Storage-labbet](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise2) om du behöver hjälp med det här steget).
- En ASP.NET-webbapp med åtkomst till Azure Storage (följ [övning 3 i Azure Storage-labbet](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3) för att snabbt skapa en sådan app).

## <a name="create-a-computer-vision-resource"></a>Skapa en resurs för Visuellt innehåll

Du måste skapa en datorseenderesurs för ditt Azure-konto. Den här resursen hanterar din åtkomst till Azures datorseendetjänst. 

1. Följ instruktionerna i [Skapa en Azure Cognitive Services-resurs](../../cognitive-services-apis-create-account.md) för att skapa en datorseenderesurs.

1. Gå sedan till menyn för resursgruppen och klicka på api-prenumerationen för visuellt innehåll som du just skapade. Kopiera URL:en under **Slutpunkt** till en plats där du enkelt kan hämta den om en stund. Klicka sedan på **Visa åtkomstnycklar**.

    ![Azure portal-sida med url-adressen för slutpunkt och länken åtkomstnycklar som beskrivs](../Images/copy-vision-endpoint.png)
    
    [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]


1. I nästa fönstret kopierar du värdet för **KEY 1** (Nyckel 1) till Urklipp.

    ![Dialogrutan Hantera nycklar med kopieringsknappen kontur](../Images/copy-vision-key.png)

## <a name="add-computer-vision-credentials"></a>Lägg till autentiseringsuppgifter för Visuellt innehåll

Därefter lägger du till de autentiseringsuppgifter som krävs i appen så att den kan komma åt datorseenderesurser

Öppna ASP.NET-webbappen i Visual Studio och navigera till filen **Web.config** i roten av projektet. Lägg till följande `<appSettings>` satser i avsnittet `VISION_KEY` i filen och ersätt med nyckeln `VISION_ENDPOINT` som du kopierade i föregående steg och med webbadressen som du sparade i steget innan.

```xml
<add key="SubscriptionKey" value="VISION_KEY" />
<add key="VisionEndpoint" value="VISION_ENDPOINT" />
```

I Solution Explorer högerklickar du sedan på projektet och använder kommandot **Manage NuGet Packages** för att installera paketet **Microsoft.Azure.CognitiveServices.Vision.ComputerVision**. Det här paketet innehåller de typer som krävs för att anropa API för visuellt innehåll.

## <a name="add-metadata-generation-code"></a>Lägg till kod för generering av metadata

Därefter ska du lägga till koden som faktiskt utnyttjar tjänsten Datorseende för att skapa metadata för bilder. De här stegen gäller för ASP.NET-app i labbet, men du kan anpassa dem efter dina egna appar. Det är viktiga att du nu har en ASP.NET-webbapp som kan ladda upp bilder till en Azure Storage-container, läsa bilder från den och visa dem i vyn. Om du är osäker på det här steget är det bäst att följa [övning 3 i Azure Storage Lab](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise3). 

1. Öppna filen *HomeController.cs* i projektets **Controllers**-mapp och lägg till följande `using`-instruktioner överst i filen:

    ```csharp
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
    using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
    ```

1. Gå sedan till **Upload**-metoden. Den här metoden konverterar och laddar upp bilder till blob-lagring. Lägg till följande kod direkt efter det block som börjar med `// Generate a thumbnail`, eller i slutet av processen image-blob-creation (bild-blob-skapande). Den här koden tar den blob som innehåller bilden (`photo`) och använder Visuellt innehåll för att generera en beskrivning av den bilden. API för visuellt innehåll genererar även en lista med nyckelord som gäller för bilden. Den genererade beskrivningen och nyckelorden lagras i blobens metadata så att de kan hämtas senare.

    ```csharp
    // Submit the image to Azure's Computer Vision API
    ComputerVisionClient vision = new ComputerVisionClient(
        new ApiKeyServiceClientCredentials(ConfigurationManager.AppSettings["SubscriptionKey"]),
        new System.Net.Http.DelegatingHandler[] { });
    vision.Endpoint = ConfigurationManager.AppSettings["VisionEndpoint"];

    VisualFeatureTypes[] features = new VisualFeatureTypes[] { VisualFeatureTypes.Description };
    var result = await vision.AnalyzeImageAsync(photo.Uri.ToString(), features);

    // Record the image description and tags in blob metadata
    photo.Metadata.Add("Caption", result.Description.Captions[0].Text);

    for (int i = 0; i < result.Description.Tags.Count; i++)
    {
        string key = String.Format("Tag{0}", i);
        photo.Metadata.Add(key, result.Description.Tags[i]);
    }

    await photo.SetMetadataAsync();
    ```

1. Gå sedan till **indexmetoden** i samma fil. Den här metoden räknar upp de lagrade bildblobbarna i den riktade blob-behållaren (som **IListBlobItem-instanser)** och skickar dem till programvyn. Ersätt blocket `foreach` i den här metoden med följande kod. Den här koden anropar **CloudBlockBlob.FetchAttributes** för att hämta varje blobs kopplade metadata. Den extraherar den datorgenererade beskrivningen (`caption`) från metadata och lägger till den i objektet **BlobInfo**, som skickas till vyn.
    
    ```csharp
    foreach (IListBlobItem item in container.ListBlobs())
    {
        var blob = item as CloudBlockBlob;
    
        if (blob != null)
        {
            blob.FetchAttributes(); // Get blob metadata
            var caption = blob.Metadata.ContainsKey("Caption") ? blob.Metadata["Caption"] : blob.Name;
    
            blobs.Add(new BlobInfo()
            {
                ImageUri = blob.Uri.ToString(),
                ThumbnailUri = blob.Uri.ToString().Replace("/photos/", "/thumbnails/"),
                Caption = caption
            });
        }
    }
    ```

## <a name="test-the-app"></a>Testa appen

Spara dina ändringar i Visual Studio och tryck på **Ctrl + F5** för att starta programmet i webbläsaren. Använd appen för att ladda upp några bilder antingen från mappen ”photos” i labbresurserna eller från din egen mapp. När du håller markören över en av bilderna i vyn bör ett knappbeskrivningsfönster och den datorgenererade beskrivningen för bilden visas.

![Den datorgenererade bildtexten](../Images/thumbnail-with-tooltip.png)

Om du vill visa alla kopplade metadata kan använda Azure Storage Explorer för att visa den lagringscontainer som du använder för bilder. Högerklicka på någon av blobarna i containern och välj **Egenskaper**. I dialogrutan visas en lista över nyckel/värde-par. Den datorgenererade bildbeskrivningen lagras i objektet ”Caption” (Bildtext), och sökorden lagras i ”Tag0”, ”tagg1” och så vidare. När du är klar klickar du på **Avbryt** för att stänga dialogrutan.

![Dialogrutan Bildegenskaper med metadatataggar i listan](../Images/blob-metadata.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta att arbeta med webbappen läser du avsnittet [Nästa steg](#next-steps). Om du inte planerar att fortsätta använda det här programmet bör du ta bort alla appspecifika resurser. Om du vill ta bort resurser kan du ta bort resursgruppen som innehåller din Azure Storage-prenumeration och Datorseenderesurs. Detta tar bort lagringskontot, de blobar som laddades upp till det samt den App Service-resurs som krävs för att ansluta med ASP.NET-webbappen. 

Om du vill ta bort resursgruppen öppnar du fliken **Resursgrupper** i portalen, navigerar till den resursgrupp som du använde för det här projektet och klickar på **Ta bort resursgrupp** högst upp i vyn. Du uppmanas att skriva resursgruppens namn för att bekräfta att du vill ta bort det, eftersom det inte går att återställa en resursgrupp när den har tagits bort.

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerar du Azures datorseendetjänst i en befintlig webbapp för att automatiskt generera bildtexter och nyckelord för blob-bilder när de överförs. Gå nu till Azure Storage-labbet, övning 6, för att lära dig hur du lägger till sökfunktioner till webbappen. Detta drar nytta av de sökord som tjänsten Visuellt innehåll genererar.

> [!div class="nextstepaction"]
> [Lägga till sökning i din app](https://github.com/Microsoft/computerscience/blob/master/Labs/Azure%20Services/Azure%20Storage/Azure%20Storage%20and%20Cognitive%20Services%20(MVC).md#Exercise6)
