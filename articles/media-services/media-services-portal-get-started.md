<properties
    pageTitle=" Komma igång med att leverera innehåll på begäran med hjälp av den klassiska Azure-portalen | Microsoft Azure"
    description="Den här vägledningen visar dig stegen för att implementera en leveransapp för Video-on-Demand-innehåll (VoD) med Azure Media Services med hjälp av den klassiska Azure-portalen."
    services="media-services"
    documentationCenter=""
    authors="Juliako"
    manager="erikre"
    editor=""/>

<tags
    ms.service="media-services"
    ms.workload="media"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/22/2016"
    ms.author="juliako"/>


# Komma igång med att leverera innehåll på begäran med hjälp av den klassiska Azure-portalen


[AZURE.INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]


Den här vägledningen visar dig stegen för att implementera en grundläggande leveransapp för Video-on-Demand-innehåll (VoD) med hjälp av den klassiska Azure-portalen.

> [AZURE.NOTE] Du behöver ett Azure-konto för att slutföra den här självstudien. Mer information om den [kostnadsfria utvärderingsversionen av Azure](/pricing/free-trial/?WT.mc_id=A261C142F). 


Vägledningen innehåller följande uppgifter:

1.  Skapa ett Azure Media Services-konto.
2.  Konfigurera strömningsslutpunkt.
1.  Överföra en videofil.
1.  Koda källfilen till en uppsättning MP4-filer med anpassningsbar bithastighet.
1.  Publicera tillgången och få URL:er för strömning och progressiv överföring.  
1.  Spela upp ditt innehåll.


## Skapa ett Azure Media Services-konto

1. I den [klassiska Azure-portalen](https://manage.windowsazure.com/) klickar du på **Ny**, på **Media Service** och sedan på **Snabbregistrering**.

    ![Snabbregistrering för Media Services](./media/media-services-portal-get-started/wams-QuickCreate.png)

2. Vid **NAMN** anger du namnet på det nya kontot. Namnet på ett Media Services-konto består av gemena bokstäver eller siffror utan blanksteg och 3 till 24 tecken.

3. Vid **REGION** väljer du den geografiska region som ska användas för att lagra metadataposter för ditt Media Services-konto. Endast de tillgängliga Media Services-regionerna visas i listrutan.

4. Vid **LAGRINGSKONTO** väljer du ett Storage-konto för att tillhandahålla Blob Storage för medieinnehållet från ditt Media Services-konto. Du kan välja ett befintligt lagringskonto i samma geografiska region som ditt Media Services-konto eller skapa ett nytt lagringskonto. Ett nytt lagringskonto skapas i samma region.

5. Om du har skapat ett nytt lagringskonto anger du ett namn för det i **NYTT LAGRINGSKONTONAMN**. Reglerna för namn på lagringskonton är desamma som för Media Services-konton.

6. Klicka på **Snabbregistrering** längst ned i formuläret.

    Du kan övervaka statusen för processen i meddelandeområdet längst ned i fönstret.

    Statusen ändras till aktiv när kontot har skapats.

    Längst ned på sidan visas knappen **HANTERA NYCKLAR**. När du klickar på den här knappen, visas en dialogruta med namnet på Media Services-kontot och de primära och sekundära nycklarna. Du behöver kontonamnet och den primära nyckelinformationen för att genom programmering få åtkomst till Media Services-kontot.

    ![Media Services-sida](./media/media-services-portal-get-started/wams-mediaservices-page.png)

    När du dubbelklickar på kontonamnet visas sidan Snabbstart som standard. På den här sidan kan du göra vissa hanteringsuppgifter som även finns tillgängliga på andra sidor i portalen. Du kan till exempel ladda upp en videofil från den här sidan eller från sidan INNEHÅLL.


## Konfigurera strömningsslutpunkter  med portalen

När du arbetar med Azure Media Services är ett av de vanligaste scenarierna att leverera strömning med anpassad bithastighet till dina klienter. Med strömning med anpassad bithastighet kan klienten växla till en dataström med högre eller lägre bithastighet då videon visas baserat på den aktuella nätverksbandbredden, processoranvändningen och andra faktorer. Media Services stöder följande strömningstekniker för anpassningsbar bithastighet: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH och HDS (endast för Adobe PrimeTime-/Access-licenstagare).

Media Services erbjuder dynamisk paketering som gör att du kan leverera ditt kodade innehåll (MP4 eller Smooth Streaming) med anpassningsbar bithastighet i strömningsformat som stöds av Media Services (MPEG DASH, HLS, Smooth Streaming, HDS) utan att du behöver ompaketera till dessa strömningsformat.

Om du vill använda dynamisk paketering ska du göra följande:

- Koda din mezzaninfil (källa) till en uppsättning MP4-filer med anpassningsbar bithastighet eller Smooth Streaming-filer med anpassad bithastighet (kodningsstegen visas längre fram i den här vägledningen).  
- Du behöver minst en enhet för strömning för den *strömningsslutpunkt* från vilken du planerar att leverera ditt innehåll.

Med dynamisk paketering behöver du bara lagra och betala för filerna i ett enda lagringsformat, och Media Services skapar och ger lämplig respons baserat på begäranden från en klient.

Om du vill ändra antalet reserverade enheter för strömning gör du följande:

1. På den [klassiska Azure-portalen](https://manage.windowsazure.com/) klickar du på **Media Services**. Klicka därefter på namnet på medietjänsten.

2. Välj sidan för STRÖMNINGSSLUTPUNKTER. Klicka sedan på den strömningsslutpunkt som du vill ändra.

3. För att specificera antalet strömningsenheter väljer du fliken **SKALNING** och flyttar på skjutreglaget för **reserverad kapacitet**.

    ![Skala sida](./media/media-services-portal-get-started/media-services-origin-scale.png)

4. Klicka på knappen **SPARA** för att spara ändringarna.

    Tilldelning av nya enheter tar cirka 20 minuter att slutföra.

    >[AZURE.NOTE] Om man går från ett positivt värde för strömningsenheterna tillbaka till inget, kan strömningen inaktiveras i upp till en timme.
    >
    > Det högsta antalet enheter som har angetts för 24-timmarsperioden används för att beräkna kostnaden. Mer information om priser finns i [Prisuppgifter för Media Services](http://go.microsoft.com/fwlink/?LinkId=275107).

## Överföra innehåll


1. I den [klassiska Azure-portalen](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409) klickar du på **Media Services** och sedan på namnet på Media Services-kontot.
2. Välj sidan INNEHÅLL.
3. Klicka på knappen **Överför** på sidan eller längst ned i portalen.
4. Bläddra till önskad tillgångsfil i dialogrutan **Överföra innehåll**. Klicka på filen och klicka sedan på **Öppna** eller tryck på RETUR.

    ![UploadContentDialog][uploadcontent]

5. I dialogrutan **Överföra innehåll**  klickar du på knappen Kontrollera för att acceptera **filen** och **innehållsnamnet**.
6. Överföringen startar och du kan följa förloppet längst ned i portalen.  

    ![JobStatus][status]

När överföringen är klar visas den nya tillgången i innehållslistan. Enligt konventionen har namnet ”**-Source**” som tillägg i slutet för att underlätta arbetet med att följa nytt innehåll som källinnehåll för kodningsuppgifter.

![ContentPage][contentpage]

Om filstorleksvärdet inte kan uppdateras när överföringsprocessen stoppas, väljer du knappen **Synka metadata**. Detta synkroniserar tillgångsfilens storlek med den faktiska filstorleken för lagring och uppdaterar värdet på innehållssidan.


## Koda innehåll

### Översikt

För att kunna leverera digital video via internet måste du komprimera mediet. Media Services innehåller en mediekodare med vilken du kan ange hur innehållet ska kodas (till exempel vilka codec som ska användas, filformat, upplösning och bithastighet.)

När du arbetar med Azure Media Services är ett av de vanligaste scenarierna att leverera strömning med anpassad bithastighet till dina klienter. Med strömning med anpassad bithastighet kan klienten växla till en dataström med högre eller lägre bithastighet då videon visas baserat på den aktuella nätverksbandbredden, processoranvändningen och andra faktorer. Media Services stöder följande tekniker för strömning med anpassningsbar bithastighet: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH och HDS (endast för Adobe PrimeTime-/Access-licenser).

Media Services tillhandahåller en dynamisk paketering som gör att du kan leverera ditt MP4- eller Smooth Streaming-kodade innehåll med anpassad bithastighet i strömningsformat som stöds av Media Services (MPEG DASH, HLS, Smooth Streaming eller HDS) utan att du behöver packa om till dessa strömningsformat.

Om du vill använda dynamisk paketering ska du göra följande:

- Koda din mezzaninfil (källa) till en uppsättning MP4-filer med anpassningsbar bithastighet eller Smooth Streaming-filer med anpassad bithastighet (kodningsstegen visas längre fram i den här vägledningen).
- Du behöver minst en enhet för strömning på begäran för den strömningsslutpunkt från vilken du planerar att leverera ditt innehåll. Mer information finns i [Skala reserverade enheter för strömning på begäran](media-services-manage-origins.md#scale_streaming_endpoints/).

Med dynamisk paketering behöver du bara lagra och betala för filerna i ett enda lagringsformat, och Media Services skapar och ger lämplig respons baserat på begäranden från en klient.

Observera att förutom att du kan använda funktionerna för dynamisk paketering, ger reserverade enheter för strömning på begäran särskild egresskapacitet (utgående datatrafik) som kan köpas i steg om 200 Mbit/s. Som standard konfigureras strömning på begäran i en delad instansmodell för vilken serverresurser (till exempel beräkning eller egresskapacitet) delas med andra användare. För att förbättra ett strömningsgenomflöde på begäran, rekommenderas det att du köper reserverade enheter för strömning på begäran.

### Koda

I det här avsnittet beskrivs de steg som du kan vidta för att koda ditt innehåll med Media Encoder Standard med hjälp av den klassiska Azure-portalen.

1.  Välj den fil du vill koda.
    Om kodning stöds för den här filtypen aktiveras knappen **PROCESS** längst ned på sidan INNEHÅLL.
4. I dialogrutan **Process** markerar du processorn **Media Encoder Standard**.
5. Välj någon av **Encoding-konfigurationerna**.

    ![Process2][process2]

    I ämnet [Strängar förinställda för uppgifter för Media Encoder Standard](https://msdn.microsoft.com/en-US/library/mt269960) förklaras vad varje förinställning innebär.  

5. Ange därefter önskat namn för utdatainnehåll eller godkänn standardinställningarna. Klicka därefter på knappen Kontrollera för att starta kodningsåtgärden. Du kan följa förloppet längst ned i portalen.
6. Välj **OK**.

    När kodningen är klar innehåller sidan INNEHÅLL den kodade filen.

    Om du vill visa förloppet för kodningsjobbet växlar du till sidan **JOBB**.  

    Om filstorleksvärdet inte kan uppdateras när kodningen är klar, väljer du knappen **Synka metadata**. Detta synkroniserar utdatatillgångsfilens storlek med den faktiska filstorleken i lagring och uppdaterar värdet på innehållssidan.


## Publicera innehåll

### Översikt

För att ge din användare en URL som kan användas för att strömma eller hämta ditt innehåll måste du först ”publicera” din tillgång genom att skapa en lokaliserare. Positionerare ger åtkomst till filer som finns i tillgången. Media Services stöder två typer av lokaliserare: OnDemandOrigin-lokaliserare som används för strömning av media (till exempel MPEG DASH, HLS eller Smooth Streaming) och lokaliserare för åtkomstsignatur (SAS) som används för att hämta filer.

När du använder den klassiska Azure-portalen för att publicera dina tillgångar, skapas lokaliserarna åt dig och du förses med en OnDemand-baserad URL (om din tillgång innehåller en .ism-fil) eller en SAS-URL.

En SAS-URL har följande format.

    {blob container name}/{asset name}/{file name}/{SAS signature}

En strömnings-URL har följande format och du kan använda det för att spela upp Smooth Streaming-tillgångar.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Lägg till (format = m3u8 aapl) till URL:en för att skapa en HLS-strömnings-URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Lägg till (format=mpd-time-csf) till URL:en för att skapa en MPEG DASH-strömnings-URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Lokaliserare har ett utgångsdatum. När du använder portalen för att publicera dina tillgångar, skapas lokaliserare med ett utgångsdatum 100 år senare.

>[AZURE.NOTE] Om du har använt portalen för att skapa lokaliserare före mars 2015, skapades lokaliserare med ett utgångsdatum två år senare.  

Du uppdaterar ett utgångsdatum för en lokaliserare med [REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator )- eller [.NET](http://go.microsoft.com/fwlink/?LinkID=533259)-API:er. Observera att URL:en ändras när du uppdaterar en SAS-lokaliserare.

### Publicera

Gör följande för att använda portalen för att publicera en tillgång:

1. Välj tillgången.
2. Klicka sedan på knappen Publicera.

 ![PublishedContent][publishedcontent]


## Spela upp innehåll från portalen

Den klassiska Azure-portalen har en innehållsspelare som du kan använda för att testa videon.

Klicka på önskad video och klicka sedan på knappen **Spela upp** längst ned i portalen.

Vissa förutsättningar gäller:

- Kontrollera att videon har publicerats.
- **MEDIA SERVICES-INNEHÅLLSSPELAREN** spelar upp från strömningsslutpunkten som är standard. Använd en annan spelare om du vill spela upp från en strömningsslutpunkt som inte är standard. Till exempel [Azure Media Services Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).


![AMSPlayer][AMSPlayer]



##Nästa steg: Utbildningsvägar för Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Ge feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## Letar du efter något annat?

Om det här ämnet inte innehöll det du förväntade dig, saknade något eller på något annat sätt inte motsvarade dina behov, får du gärna ge oss feedback i Disqus-tråden nedan.

### Ytterligare resurser
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Azure Media Services 101 – skaffa din video online nu!</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Azure Media Services 102 – dynamisk paketering och mobila enheter</a>


<!-- Anchors. -->


<!-- URLs. -->
[Klassisk Azure-portal]: http://manage.windowsazure.com/


<!-- Images -->
[portaloverview]: ./media/media-services-portal-get-started/media-services-content-page.png
[publishedcontent]: ./media/media-services-portal-get-started/media-services-upload-content-published.png
[uploadcontent]: ./media/media-services-portal-get-started/UploadContent.png
[status]: ./media/media-services-portal-get-started/Status.png
[encoder]: ./media/media-services-manage-content/EncoderDialog2.png
[branding]: ./media/branding-reporting.png
[contentpage]: ./media/media-services-portal-get-started/media-services-content-page.png
[process]: ./media/media-services-manage-content/media-services-process-video.png
[process2]: ./media/media-services-portal-get-started/media-services-process-video2.png
[encrypt]: ./media/media-services-manage-content/media-services-encrypt-content.png
[AMSPlayer]: ./media/media-services-portal-get-started/media-services-portal-player.png



<!--HONumber=sep16_HO1-->


