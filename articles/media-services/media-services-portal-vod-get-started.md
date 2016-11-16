---
title: " Komma igång med att leverera innehåll på begäran med hjälp av Azure Portal | Microsoft Docs"
description: "De här självstudierna visar dig stegen för att implementera ett grundläggande leveransprogram för Video-on-Demand-innehåll (VoD) med Azure Media Services-appen (AMS) med hjälp av Azure Portal."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 6c98fcfa-39e6-43a5-83a5-d4954788f8a4
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/30/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 25d8b204f328514ebcfc69228dcbf9342bdf205b


---
# <a name="get-started-with-delivering-content-on-demand-using-the-azure-portal"></a>Komma igång med att leverera innehåll på begäran med hjälp av Azure Portal
[!INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

De här självstudierna visar dig stegen för att implementera ett grundläggande leveransprogram för Video-on-Demand-innehåll (VoD) med Azure Media Services-appen (AMS) med hjälp av Azure Portal.

> [!NOTE]
> Du behöver ett Azure-konto för att slutföra den här självstudien. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Vägledningen innehåller följande uppgifter:

1. Skapa ett Azure Media Services-konto.
2. Konfigurera strömningsslutpunkt.
3. Överföra en videofil.
4. Koda källfilen till en uppsättning MP4-filer med anpassningsbar bithastighet.
5. Publicera tillgången och få URL:er för strömning och progressiv överföring.  
6. Spela upp ditt innehåll.

## <a name="create-an-azure-media-services-account"></a>Skapa ett Azure Media Services-konto
Stegen i det här avsnittet visar hur du skapar ett AMS-konto.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka på **+Ny** > **Webb + mobilt** > **Media Services**.
   
    ![Skapa Media Services](./media/media-services-portal-vod-get-started/media-services-new1.png)
3. Ange de erfordrade värdena i **SKAPA MEDIA SERVICES-KONTO**.
   
    ![Skapa Media Services](./media/media-services-portal-vod-get-started/media-services-new3.png)
   
   1. Ange namnet på det nya AMS-kontot vid **Kontonamn**. Namnet på ett Media Services-konto består av gemena bokstäver eller siffror utan blanksteg och 3 till 24 tecken.
   2. Vid Prenumeration väljer du mellan de olika Azure-prenumerationer som du har åtkomst till.
   3. I **Resursgrupp** väljer du ny eller befintlig resurs.  En resursgrupp är en samling resurser som delar livscykel, behörigheter och principer. Lär dig mer [här](../azure-resource-manager/resource-group-overview.md#resource-groups).
   4. För **Plats** väljer du den geografiska region som ska användas för att lagra media och metadataposter för ditt Media Services-konto. Den här regionen används för att bearbeta och strömma dina media. Endast de tillgängliga Media Services-regionerna visas i listrutan. 
   5. Vid **Storage-konto** väljer du ett lagringskonto för att tillhandahålla Blob Storage av medieinnehållet från ditt Media Services-konto. Du kan välja ett befintligt lagringskonto i samma geografiska region som ditt Media Services-konto eller skapa ett lagringskonto. Ett nytt lagringskonto skapas i samma region. Reglerna för namn på lagringskonton är desamma som för Media Services-konton.
      
       Mer information om lagring finns [här](../storage/storage-introduction.md).
   6. Välj **PIN-kod för instrumentpanelen** för att se förloppet för kontodistributionen.
4. Klicka på **Skapa** längst ned i formuläret.
   
    Statusen ändras till **Körs** när kontot har skapats. 
   
    ![Media Services-inställningar](./media/media-services-portal-vod-get-started/media-services-settings.png)
   
    För att hantera AMS-kontot (till exempel överföra videor, koda tillgångar och övervaka jobbförlopp) använder du fönstret **Inställningar**.

## <a name="manage-keys"></a>Hantera nycklar
Du behöver kontonamnet och den primära nyckelinformationen för att genom programmering få åtkomst till Media Services-kontot.

1. Välj ditt konto i Azure-portalen. 
   
    Fönstret **Inställningar** visas till höger. 
2. I fönstret **Inställningar** väljer du **Nycklar**. 
   
    I fönstret **Hantera nycklar** visas kontonamnet och de primära och sekundära nycklarna. 
3. Tryck på kopieringsknappen för att kopiera värdena.
   
    ![Media Services-nycklar](./media/media-services-portal-vod-get-started/media-services-keys.png)

## <a name="configure-streaming-endpoints"></a>Konfigurera strömningsslutpunkter
När du arbetar med Azure Media Services är ett av de vanligaste scenarierna att leverera video via strömning med anpassad bithastighet till dina klienter. Media Services stöder följande strömningstekniker för anpassningsbar bithastighet: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH och HDS (endast för Adobe PrimeTime-/Access-licenstagare).

Media Services tillhandahåller en dynamisk paketering som gör att du kan leverera ditt MP4-kodade innehåll med anpassad bithastighet i strömningsformat som stöds av Media Services (MPEG DASH, HLS, Smooth Streaming, HDS) direkt när du så önskar, utan att du behöver lagra på förhand packade versioner av vart och ett av dessa strömningsformat.

Om du vill använda dynamisk paketering ska du göra följande:

* Koda din mezzaninfil (källa) till en uppsättning MP4-filer med anpassningsbar bithastighet (kodningsstegen visas längre fram i den här vägledningen).  
* Skapa minst en enhet för strömning för den *strömningsslutpunkt* från vilken du planerar att leverera ditt innehåll. Stegen nedan visar hur du kan ändra antalet strömningsenheter.

Med dynamisk paketering behöver du bara lagra och betala för filerna i ett enda lagringsformat, och Media Services skapar och ger lämplig respons baserat på begäranden från en klient.

Om du vill skapa och ändra antalet reserverade enheter för strömning gör du följande:

1. I fönstret **Inställningar** klickar du på **Strömningsslutpunkter**. 
2. Klicka på den strömningsslutpunkt som är standard. 
   
    Fönstret **INFORMATION OM DEN STRÖMNINGSSLUTPUNKT SOM ÄR STANDARD** visas.
3. Flytta på skjutreglaget **Strömningsenheter** för att ange antalet strömningsenheter.
   
    ![Strömningsenheter](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)
4. Klicka på knappen **Spara** för att spara ändringarna.
   
   > [!NOTE]
   > Tilldelning av nya enheter kan ta cirka 20 minuter att slutföra.
   > 
   > 

## <a name="upload-files"></a>Överföra filer
För att strömma videor med Azure Media Services behöver du överföra källvideorna, koda dem till flera olika bithastigheter och publicera resultatet. I det här avsnittet beskrivs det första steget. 

1. I fönstret **Inställning** klickar du på **Tillgångar**.
   
    ![Överföra filer](./media/media-services-portal-vod-get-started/media-services-upload.png)
2. Klicka på knappen **Överför**.
   
    Fönstret **Överför en videotillgång** visas.
   
   > [!NOTE]
   > Det finns inga filstorleksbegränsningar.
   > 
   > 
3. Bläddra till den önskade videon på datorn, markera den och tryck på OK.  
   
    Överföringen startar och du kan följa förloppet under filnamnet.  

När överföringen är klar visas den nya tillgången i listan **Tillgångar**. 

## <a name="encode-assets"></a>Koda tillgångar
När du arbetar med Azure Media Services är ett av de vanligaste scenarierna att leverera strömning med anpassad bithastighet till dina klienter. Media Services stöder följande strömningstekniker för anpassningsbar bithastighet: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH och HDS (endast för Adobe PrimeTime-/Access-licenstagare). För att förbereda dina videor för strömning med anpassad bithastighet måste du koda källvideon till filer i multibithastighet. Du bör använda kodaren **Media Encoder Standard** för att koda dina videor.  

Media Services tillhandahåller också en dynamisk paketering som gör att du kan leverera dina MP4-filer med multibithastighet i följande strömningsformat: MPEG DASH, HLS, Smooth Streaming eller HDS utan att du behöver packa om till dessa strömningsformat. Med dynamisk paketering behöver du bara lagra och betala för filerna i ett enda lagringsformat, och Media Services skapar och ger lämplig respons baserat på begäranden från en klient.

Om du vill använda dynamisk paketering ska du göra följande:

* Koda din källfil till en uppsättning MP4-filer med multibithastighet (kodningsstegen visas längre fram i det här avsnittet).
* Hämta minst en strömmande enhet för den strömmande slutpunkten från vilken du planerar att leverera ditt innehåll. Mer information finns i avsnittet om att [konfigurera strömningsslutpunkter](media-services-portal-vod-get-started.md#configure-streaming-endpoints). 

### <a name="to-use-the-portal-to-encode"></a>Använda portalen för att koda
I det här avsnittet beskrivs de steg som du kan vidta för att koda ditt innehåll med Media Encoder Standard.

1. I fönstret **Inställningar** väljer du **Tillgångar**.  
2. I fönstret **Tillgångar** väljer du den tillgång som du vill koda.
3. Tryck på knappen **Koda**.
4. I fönstret **Koda en tillgång** väljer du processorn ”Media Encoder Standard” och en förinställning. Om du till exempel vet att din indatavideo har en upplösning på 1 920 x 1 080 bildpunkter, kan du använda förinställningen ”H264 multibithastighet 1080p”. Mer information om förinställningar finns i [denna](https://msdn.microsoft.com/library/azure/mt269960.aspx) artikel – det är viktigt att välja den förinställning som är mest lämplig för din videoinmatning. Om du har en video med låg upplösning (640 x 360) bör du inte använda standardförinställningen ”H264 multibithastighet 1080p”.
   
   Du kan redigera namnet på utdatatillgången och namnet på jobbet för enklare hantering.
   
   ![Koda tillgångar](./media/media-services-portal-vod-get-started/media-services-encode1.png)
5. Tryck på **Skapa**.

### <a name="monitor-encoding-job-progress"></a>Övervaka förloppet för kodningsjobb
Klicka på **Inställningar** (överst på sidan) för att övervaka förloppet för kodningsjobbet och välj sedan **Jobb**.

![Jobb](./media/media-services-portal-vod-get-started/media-services-jobs.png)

## <a name="publish-content"></a>Publicera innehåll
För att ge din användare en URL som kan användas för att strömma eller hämta ditt innehåll måste du först ”publicera” din tillgång genom att skapa en lokaliserare. Lokaliserare ger åtkomst till filer som finns i tillgången. Media Services stöder två typer av lokaliserare: 

* Strömningslokaliserare (OnDemandOrigin), som används för anpassad strömning (till exempel för strömning av MPEG DASH, HLS och Smooth Streaming). Om du vill skapa en strömningslokaliserare måste din tillgång innehålla en .ism-fil. 
* Progressiva SAS-lokaliserare, som används för leverans av video via progressiv hämtning.

En strömnings-URL har följande format och du kan använda det för att spela upp Smooth Streaming-tillgångar.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Lägg till (format = m3u8 aapl) till URL:en för att skapa en HLS-strömnings-URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Lägg till (format=mpd-time-csf) till URL:en för att skapa en MPEG DASH-strömnings-URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


En SAS-URL har följande format.

    {blob container name}/{asset name}/{file name}/{SAS signature}

> [!NOTE]
> Om du har använt portalen för att skapa lokaliserare före mars 2015, skapades lokaliserare med ett utgångsdatum två år senare.  
> 
> 

Du uppdaterar ett utgångsdatum för en lokaliserare med [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator)- eller [.NET](http://go.microsoft.com/fwlink/?LinkID=533259)-API:er. URL:en ändras när du uppdaterar en SAS-lokaliserare.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Använda portalen för att publicera en tillgång
Gör följande för att använda portalen för att publicera en tillgång:

1. Välj **Inställningar** > **Tillgångar**.
2. Välj den tillgång som du vill publicera.
3. Klicka sedan på knappen **Publicera**.
4. Välj typ av lokaliserare.
5. Tryck på **Lägg till**.
   
    ![Publicera](./media/media-services-portal-vod-get-started/media-services-publish1.png)

URL:en läggs till i listan över **publicerade URL:er**.

## <a name="play-content-from-the-portal"></a>Spela upp innehåll från portalen
Azure Portal har en innehållsspelare som du kan använda för att testa videon.

Klicka på önskad video och klicka sedan på knappen **Spela upp**.

![Publicera](./media/media-services-portal-vod-get-started/media-services-play.png)

Vissa förutsättningar gäller:

* Kontrollera att videon har publicerats.
* Denna**Media Player** spelar upp från den strömningsslutpunkt som är standard. Klicka för att kopiera URL:en och använd en annan spelare om du vill spela upp från en strömningsslutpunkt som inte är standard. Till exempel [Azure Media Services Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

## <a name="next-steps"></a>Nästa steg
Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]




<!--HONumber=Nov16_HO2-->


