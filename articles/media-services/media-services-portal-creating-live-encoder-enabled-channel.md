<properties 
    pageTitle="Så här utför du direktsänd strömning med Azure Media Services för att skapa dataströmmar i multibithastighet med den klassiska Azure-portalen" 
    description="Den här självstudien visar dig stegen för att skapa en kanal som tar emot en direktsänd dataström med enkel bithastighet och kodar den till en dataström med multibithastighet med hjälp av den klassiska Azure-portalen." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako,anilmur" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article"
    ms.date="05/05/2016" 
    ms.author="juliako"/>


#Så här utför du direktsänd strömning med Azure Media Services för att skapa dataströmmar i multibithastighet med den klassiska Azure-portalen

> [AZURE.SELECTOR]
- [Portal](media-services-portal-creating-live-encoder-enabled-channel.md)
- [.NET](media-services-dotnet-creating-live-encoder-enabled-channel.md)
- [REST-API](https://msdn.microsoft.com/library/azure/dn783458.aspx)

Den här självstudien visar dig stegen för att skapa en **kanal** som tar emot en  direktsänd dataström med enkel bithastighet och kodar den till en dataström med multibithastighet.

>[AZURE.NOTE]Mer konceptinformation relaterad till kanaler som är aktiverade för Live Encoding finns i [Direktsänd strömning med Azure Media Services för att skapa dataströmmar i multibithastighet](media-services-manage-live-encoder-enabled-channels.md).

##Vanligt scenario för direktsänd strömning

Följande steg är allmänna steg som ingår i att skapa vanliga program för direktsänd strömning.

>[AZURE.NOTE] Den rekommenderade maximala längden för en direktsänd händelse är för närvarande 8 timmar. Kontakta amslived på Microsoft.com om du behöver köra en kanal under en längre tidsperiod.

1. Anslut en videokamera till en dator. Starta och konfigurera en lokal livekodare som kan mata ut en dataström med enkel bithastighet i något av följande protokoll: RTMP, Smooth Streaming eller RTP (MPEG TS). Mer information finns i [Support och livekodare för Azure Media Services RTMP](http://go.microsoft.com/fwlink/?LinkId=532824).
    
    Det här steget kan också utföras när du har skapat din kanal.

1. Skapa och starta en kanal. 

1. Hämta kanalens infognings-URL. 

    Infognings-URL:en används av livekodaren för att skicka dataströmmen till kanalen.
1. Hämta kanalens förhandsgransknings-URL. 

    Använd denna URL för att kontrollera att din kanal tar emot den direktsända dataströmmen korrekt.

3. Skapa ett program (som också kommer att skapa en tillgång). 
1. Publicera programmet (som skapar en OnDemand-positionerare för den associerade tillgången).  

    Se till att ha minst en reserverad enhet för strömning på den strömningsslutpunkt från vilken du vill strömma innehåll.
1. Starta programmet när du är redo att påbörja strömning och arkivering.
2. Som alternativ kan livekodaren få signal om att starta en annons. Annonsen infogas i utdataströmmen.
1. Stoppa programmet när du vill stoppa strömningen och arkiveringen av händelsen.
1. Ta bort programmet (och ta eventuellt bort tillgången).   

##I den här självstudien

I den här självstudien används den klassiska Azure-portalen för att utföra följande uppgifter: 

2.  Konfigurera strömningsslutpunkter.
3.  Skapa en kanal som är aktiverad för att utföra Live Encoding.
1.  Hämta infognings-URL:en i syfte att tillhandahålla den till livekodaren. Live Encoding använder denna URL för att infoga dataströmmen i kanalen. .
1.  Skapa ett program (och en tillgång)
1.  Publicera tillgången och hämta strömnings-URL:er  
1.  Spela upp ditt innehåll 
2.  Rensa

##Krav
Följande krävs för att kunna genomföra självstudien.

- Du behöver ett Azure-konto för att genomföra den här självstudien. Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information finns i [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
- Ett Media Services-konto. Mer information om att skapa ett Media Services-konto finns i [Skapa konto](media-services-create-account.md).
- En webbkamera och en kodare som kan skicka en direktsänd dataström i enkel bithastighet.

##Konfigurera strömningsslutpunkter med portalen

När du arbetar med Azure Media Services är ett av de vanligaste scenarierna att leverera strömning med anpassad bithastighet till dina klienter. Med strömning med anpassad bithastighet kan klienten växla till en dataström med högre eller lägre bithastighet då videon visas baserat på den aktuella nätverksbandbredden, processoranvändningen och andra faktorer. Media Services stöder följande tekniker för strömning med anpassningsbar bithastighet: HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH och HDS (endast för Adobe PrimeTime-/Access-licenser). 

När du arbetar med direktsänd strömning infogar en lokal livekodare (i vårt fall Wirecast) en direktsänd dataström i multibithastighet i din kanal. När dataströmmen har begärts av en användare, använder Media Services dynamisk paketering för att packa om källströmmen till den begärda anpassade bithastighetsströmmen (HLS, DASH eller Smooth). 

Om du vill dra nytta av dynamisk paketering behöver du minst en enhet för strömning för den **strömningsslutpunkt** från vilken du planerar att leverera ditt innehåll.

Om du vill ändra antalet reserverade enheter för strömning gör du följande:

1. På den [klassiska Azure-portalen](https://manage.windowsazure.com/) klickar du på **Media Services**. Klicka därefter på namnet på medietjänsten.

2. Välj sidan för STRÖMNINGSSLUTPUNKTER. Klicka därefter på den strömningsslutpunkt som du vill ändra.

3. För att specificera antalet strömningsenheter väljer du fliken SKALA och flyttar på skjutreglaget för **reserverad kapacitet**.

    ![Skalningssida](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-origin-scale.png)

4. Tryck på knappen SPARA för att spara ändringarna.

    Tilldelning av nya enheter tar cirka 20 minuter att slutföra. 

     
    >[AZURE.NOTE] Om man går från ett positivt värde för strömningsenheterna tillbaka till inget, kan strömningen inaktiveras i upp till en timme.
    >
    > Det högsta antalet enheter som har angetts för 24-timmarsperioden används för att beräkna kostnaden. Mer information om priser finns i [Prisuppgifter för Media Services](http://go.microsoft.com/fwlink/?LinkId=275107).

 
##Skapa en KANAL

1.  I den [klassiska Azure-portalen](http://manage.windowsazure.com/) klickar du på Media Services och sedan på namnet på Media Services-kontot.
2.  Välj KANAL-sidan.
3.  Välj Lägg till + för att lägga till en ny kanal.

Välj **Standard** för kodningstyper. Den här typen anger att du vill skapa en kanal som är aktiverad för Live Encoding. Det innebär att den inkommande dataströmmen i enkel bithastighet skickas till kanalen och kodas till en dataström med multibithastighet med hjälp av livekodarens angivna inställningar. Mer information finns i [Direktsänd strömning med Azure Media Services för att skapa dataströmmar i multibithastighet](media-services-manage-live-encoder-enabled-channels.md).

![standard0][standard0]

För kodningstypen **Standard** är de giltiga infogningsprotokollsalternativen:

- Fragmenterad MP4 med enkel bithastighet (Smooth Streaming)
- RTMP med enkel bithastighet
- RTP (MPEG-TS): MPEG-2-transportström över RTP.

Mer detaljerad information om varje protokoll finns i [Direktsänd strömning med Azure Media Services för att skapa dataströmmar i multibithastighet](media-services-manage-live-encoder-enabled-channels.md).

![standard1][standard1]

Du kan inte ändra indataprotokollet när kanalen eller dess associerade program körs. Om du behöver olika protokoll får du skapa separata kanaler för varje indataprotokoll.  

På sidan **Annonseringskonfiguration** kan du ange källan för annonsmarkörssignaler. Du kan bara välja API när du använder portalen, vilket betyder att livekodaren i kanalen bör lyssna på en asynkron annonsmarkörs-API. Du kan bara välja API när du använder portalen.

Mer information finns i [Direktsänd strömning med Azure Media Services för att skapa dataströmmar i multibithastighet](media-services-manage-live-encoder-enabled-channels.md).

![standard2][standard2]

På sidan **Encoding förinställt** kan du välja förinställda systeminställningar. För närvarande är den enda förinställda systeminställningen som du kan välja **Default 720p**.

![standard3][standard3]

På sidan **Skapa en kanal** kan du definiera de IP-adresser som får publicera video i den här kanalen. Tillåtna IP-adresser kan anges som antingen en enskild IP-adress (t.ex. ”10.0.0.1”), ett IP-intervall med en IP-adress och en CIDR-undernätsmask (t.ex. ”10.0.0.1/22”) eller ett IP-intervall med en IP-adress och en CIDR-undernätsmask med punktavgränsad decimalform (t.ex. ”10.0.0.1(255.255.252.0)”).

Om inga IP-adresser har angetts och det saknas regeldefinitioner kommer ingen IP-adress att tillåtas. Skapa en regel för att tillåta IP-adresser och ange 0.0.0.0/0.


![standard4][standard4]

>[AZURE.NOTE] För närvarande kan kanalstarten ta upp till 30 minuter. Kanalåterställning kan ta upp till 5 minuter.

När du har skapat kanalen kan du välja fliken **KODARE** där du kan visa dina kanalkonfigurationer. Du kan också hantera annonser och pekdatorer. 

![standard5][standard5]

Mer information finns i [Direktsänd strömning med Azure Media Services för att skapa dataströmmar i multibithastighet](media-services-manage-live-encoder-enabled-channels.md).


##Hämta infognings-URL:er

När kanalen har skapats kan du få infognings-URL:er som du tillhandahåller till livekodaren. Kodaren använder dessa URL:er för att mata in en direktsänd dataström.

![readychannel](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ready-channel.png)


![ingesturls](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-ingest-urls.png)


##Skapa och hantera ett program

###Översikt

En kanal är associerad till program som gör att du kan styra publicering och lagring av segment i en direktsänd dataström. Kanaler hanterar program. Relationen mellan kanal och program liknar den för traditionella media där en kanal har en konstant ström av innehåll och ett program är begränsat till en viss tidsinställd händelse på kanalen.

Du kan ange det antal timmar som du vill behålla inspelat innehåll för programmet genom att ställa in längden för **Arkivfönster**. Det här värdet kan anges från minst 5 minuter till högst 25 timmar. Även arkivfönstrets längd påverkar den maximala tid som klienter kan söka bakåt i tiden från den aktuella direktsända positionen. Program kan köras under den angivna tidsperioden men innehåll som understiger fönsterlängden ignoreras kontinuerligt. Värdet för den här egenskapen avgör också hur länge klientmanifesten kan växa.

Varje program är associerat med en tillgång. Om du vill publicera programmet måste du skapa en OnDemand-positionerare för den associerade tillgången. Med den här positioneraren kan du skapa en strömnings-URL som du kan tillhandahålla till dina klienter.

En kanal har stöd för upp till tre program som körs samtidigt så du kan skapa flera arkiv för samma inkommande dataström. På så sätt kan du publicera och arkivera olika delar av en händelse efter behov. Ditt verksamhetsbehov kan till exempel vara att arkivera 6 timmar av ett program, men bara sända 10 minuter. För att åstadkomma detta måste du skapa två program som körs samtidigt. Ett program ställs in för att arkivera 6 timmar av händelsen, men programmet publiceras inte. Det andra programmet ställs in för att arkivera i 10 minuter och det här programmet publiceras.

Du bör inte återanvända befintliga program för nya händelser. Skapa och starta istället ett nytt program för varje händelse.

Starta programmet när du är redo att påbörja strömning och arkivering. Stoppa programmet när du vill stoppa strömningen och arkiveringen av händelsen. 

Om du vill ta bort arkiverat innehåll, stoppar du och tar bort programmet och tar sedan bort den associerade tillgången. En tillgång kan inte tas bort om den används av ett program. Programmet måste tas bort först. 

Även efter att du har stoppat och tagit bort programmet skulle användarna kunna strömma ditt arkiverade innehåll som en video på begäran så länge som du inte tar bort tillgången.

Om du vill behålla det arkiverade innehållet, men inte att det ska vara tillgängligt för strömning, tar du bort strömningspositioneraren.

###Skapa/Starta/Stoppa program

När dataströmmen väl flödar till kanalen kan du påbörja strömningshändelsen genom att skapa en tillgång, ett program och en strömningspositionerare. Detta arkiverar dataströmmen och gör den tillgänglig för visning via strömningsslutpunkten. 

Det finns två sätt att starta en händelse: 

1. På **KANAL**-sidan trycker du på **Lägg till** för att lägga till ett nytt program.

    Ange: programnamn, tillgångsnamn, arkivfönster och krypteringsalternativ.
    
    ![createprogram](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-program.png)
    
    Om du markerat **Publicera det här programmet nu** skapas programmet PUBLICERINGS-URL:ER.
    
    Du kan trycka på **START** när du är redo att börja strömma programmet.

    Du kan trycka på SPELA UPP för att spela upp innehållet när programmet startas.


    ![createdprogram](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-created-program.png)

2. Du kan också använda en genväg och trycka på knappen **STARTA STRÖMNING** på **KANAL**-sidan. Detta skapar en tillgång, ett program och en strömningspositionerare.

    Programmet heter DefaultProgram och arkivfönstret har angetts till 1 timme.

    Du kan spela upp ett publicerat program från KANAL-sidan. 

    ![channelpublish](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-channel-play.png)


Om du klickar på **STOPPA STRÖMNING** på **KANAL**-sidan  stoppas standardprogrammet och tas bort. Tillgången är fortfarande där och du kan publicera eller avpublicera den från **INNEHÅLL**-sidan.

Om du växlar till **INNEHÅLL**-sidan visas de tillgångar som har skapats för dina program.

![contentasset](./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-content-assets.png)


##Spela upp innehåll

För att ge din användare en URL som kan användas för att strömma ditt innehåll, måste du först ”publicera” din tillgång (som det beskrivs i föregående avsnitt) genom att skapa en positionerare (när du publicerar en tillgång med portalen skapas positionerare åt dig). Positionerare ger åtkomst till filer som finns i tillgången. 

Beroende på vilket strömningsprotokoll som du vill använda för att spela upp ditt innehåll, kan du behöva ändra den URL som du får från kanal-/programlänken **PUBLICERA URL**.

Dynamisk paketering tar hand om paketeringen av den direktsända dataströmmen till det angivna protokollet. 

Som standard har en strömnings-URL följande format och du kan använda den för att spela Smooth Streaming-tillgångar:

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Lägg till (format = m3u8 aapl) till URL:en för att skapa en HLS-strömnings-URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Lägg till (format=mpd-time-csf) till URL:en för att skapa en MPEG DASH-strömnings-URL.

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Mer information om att leverera innehåll finns i [Leverera innehåll](media-services-deliver-content-overview.md).

Du kan spela upp Smooth Stream med hjälp av [AMS Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) eller använda iOS och Android-enheter för att spela upp HLS version 3.

##Rensa

Följ stegen nedan om du är klar med strömningen av händelser och vill rensa de resurser som etablerades tidigare.

- Stoppa sändningen av dataströmmen från kodaren.
- Stoppa kanalen. När kanalen har stoppats medför den inga avgifter. När du vill starta den igen har den samma infognings-URL så att du inte behöver konfigurera om din kodare.
- Du kan avbryta din strömningsslutpunkt om du inte vill fortsätta att tillhandahålla arkivet för din direktsända händelse som en strömning på begäran. När kanalen har stoppats medför den inga avgifter.
  

##Överväganden

- Den rekommenderade maximala längden för en direktsänd händelse är för närvarande 8 timmar. Kontakta amslived på Microsoft.com om du behöver köra en kanal under längre tidsperioder.
- Se till att ha minst en reserverad enhet för strömning på den strömningsslutpunkt från vilken du vill strömma innehåll.


##Utbildningsvägar för Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Ge feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



[standard0]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard0.png
[standard1]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard1.png
[standard2]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard2.png
[standard3]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard3.png
[standard4]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard4.png
[standard5]: ./media/media-services-portal-creating-live-encoder-enabled-channel/media-services-create-channel-standard_encode.png 


<!--HONumber=Jun16_HO2-->


