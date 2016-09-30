<properties 
    pageTitle="Så här utför du direktsänd strömning med lokala kodare med hjälp av Azure-portalen | Microsoft Azure" 
    description="Den här vägledningen visar dig stegen för att skapa en kanal som är konfigurerad för en genomströmningsleverans." 
    services="media-services" 
    documentationCenter="" 
    authors="juliako" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article"
    ms.date="09/05/2016" 
    ms.author="juliako"/>



#Så här utför du direktsänd strömning med lokala kodare med hjälp av Azure-portalen

> [AZURE.SELECTOR]
- [Portalen]( media-services-portal-live-passthrough-get-started.md)
- [.NET]( media-services-dotnet-live-encode-with-onpremises-encoders.md)
- [REST]( https://msdn.microsoft.com/library/azure/dn783458.aspx)

Den här vägledningen visar dig stegen för att använda Azure-portalen för att skapa en **kanal** som är konfigurerad för en genomströmningsleverans. 

##Krav

Följande krävs för att kunna genomföra vägledningen:

- Ett Azure-konto. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Ett Media Services-konto. Information om hur du skapar ett Media Services-konto finns i [Så här skapar du ett Media Services-konto](media-services-create-account.md).
- En webbkamera. Till exempel [Telestream Wirecast-kodaren](http://www.telestream.net/wirecast/overview.htm).

Vi rekommenderar att du tittar närmare på följande artiklar:

- [Support och direktsända kodare för Azure Media Services RTMP](https://azure.microsoft.com/blog/2014/09/18/azure-media-services-rtmp-support-and-live-encoders/)
- [Översikt över direktsänd strömning med Azure Media Services](media-services-manage-channels-overview.md)
- [Direktsänd strömning med lokala kodare som skapar dataströmmar i multibithastighet](media-services-live-streaming-with-onprem-encoders.md)


##<a id="scenario"></a>Vanligt scenario för direktsänd strömning

Följande steg beskriver uppgifter som ingår i att skapa vanliga appar för direktsänd strömning som använder kanaler som har konfigurerats för genomströmningsleverans. Den här vägledningen visar hur du skapar och hanterar en genomströmningskanal och direktsända händelser.

1. Anslut en videokamera till en dator. Starta och konfigurera en lokal direktsänd kodare som matar ut en RTMP- eller fragmenterad MP4-dataström i multibithastighet. Mer information finns i [Support och direktsända kodare för Azure Media Services RTMP](http://go.microsoft.com/fwlink/?LinkId=532824).
    
    Det här steget kan också utföras när du har skapat din kanal.

1. Skapa och starta en genomströmningskanal
1. Hämta kanalens infognings-URL. 

    Infognings-URL:en används av livekodaren för att skicka dataströmmen till kanalen.
1. Hämta kanalens förhandsgransknings-URL. 

    Använd denna URL för att kontrollera att din kanal tar emot den direktsända dataströmmen korrekt.

3. Skapa en direktsänd händelse eller ett direktsänt program. 

    När du använder Azure-portalen, skapas även en tillgång då du skapar en direktsänd händelse. 
      
    >[AZURE.NOTE]Se till att du har minst en strömningsreserverad enhet på den strömningsslutpunkt som du vill strömma innehåll från.
1. Starta händelsen eller programmet när du är redo att påbörja strömning och arkivering.
2. Som alternativ kan den direktsända kodaren få signal om att starta en annons. Annonsen infogas i utdataströmmen.
1. Stoppa händelsen eller programmet när du vill stoppa strömningen och arkiveringen av händelsen.
1. Ta bort händelsen eller programmet (och ta eventuellt bort tillgången).     

>[AZURE.IMPORTANT] Titta närmare på [Direktsänd strömning med lokala kodare som skapar dataströmmar i multibithastighet](media-services-live-streaming-with-onprem-encoders.md) för att lära dig mer om koncept och överväganden som rör direktsänd strömning med lokala kodare och genomströmningskanaler.

##Visa meddelanden och fel

Klicka på meddelandeikonen om du vill visa meddelanden och fel som genereras av Azure-portalen.

![Meddelanden](./media/media-services-portal-passthrough-get-started/media-services-notifications.png)

##Konfigurera strömningsslutpunkter 

Media Services tillhandahåller en dynamisk paketering som gör att du kan leverera dina MP4-filer med flera bithastigheter i följande strömningsformat: MPEG DASH, HLS, Smooth Streaming eller HDS utan att du behöver packa om till dessa strömningsformat. Med dynamisk paketering behöver du bara lagra och betala för filerna i ett enda lagringsformat och Media Services skapar och ger lämplig respons baserat på begäranden från en klient.

Om du vill dra nytta av dynamisk paketering behöver du minst en enhet för strömning för den strömningsslutpunkt från vilken du planerar att leverera ditt innehåll.  

Om du vill skapa och ändra antalet reserverade enheter för strömning gör du följande:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. I fönstret **Inställningar** klickar du på **Strömningsslutpunkter**. 

2. Klicka på den strömningsslutpunkt som är standard. 

    Fönstret **INFORMATION OM DEN STRÖMNINGSSLUTPUNKT SOM ÄR STANDARD** visas.

3. Flytta på skjutreglaget **Strömningsenheter** för att ange antalet strömningsenheter.

    ![Strömningsenheter](./media/media-services-portal-passthrough-get-started/media-services-streaming-units.png)

4. Klicka på knappen **Spara** för att spara ändringarna.

    >[AZURE.NOTE]Tilldelning av nya enheter kan ta cirka 20 minuter att slutföra.
    
##Skapa och starta genomströmningskanaler och händelser

En kanal är associerad med händelser och program som gör att du kan styra publicering och lagring av segment i en direktsänd dataström. Kanaler hanterar händelser. 
    
Du kan ange det antal timmar som du vill behålla inspelat innehåll för programmet genom att ställa in längden för **Arkivfönster**. Det här värdet kan anges från minst 5 minuter till högst 25 timmar. Även arkivfönstrets längd påverkar den maximala tid som klienter kan söka bakåt i tiden från den aktuella direktsända positionen. Händelser kan köras under den angivna tidsperioden men innehåll som understiger fönsterlängden ignoreras kontinuerligt. Värdet för den här egenskapen avgör också hur länge klientmanifesten kan växa.

Varje händelse är associerad till en tillgång. För att publicera händelsen måste du skapa en OnDemand-lokaliserare för den associerade tillgången. Med den här lokaliseraren kan du skapa en strömnings-URL som du kan tillhandahålla till dina klienter.

En kanal har stöd för upp till tre händelser som körs samtidigt så du kan skapa flera arkiv för samma inkommande dataström. På så sätt kan du publicera och arkivera olika delar av en händelse efter behov. Ditt verksamhetsbehov kan till exempel vara att arkivera 6 timmar av ett program, men bara sända 10 minuter. För att åstadkomma detta måste du skapa två program som körs samtidigt. Ett program ställs in för att arkivera 6 timmar av händelsen, men programmet publiceras inte. Det andra programmet ställs in för att arkivera i 10 minuter och det här programmet publiceras.

Du bör inte återanvända befintliga direktsända händelser. Skapa och starta istället en ny händelse för varje händelse.

Starta händelsen när du är redo att påbörja strömning och arkivering. Stoppa programmet när du vill stoppa strömningen och arkiveringen av händelsen. 

Om du vill ta bort arkiverat innehåll, stoppar du och tar bort händelsen och tar sedan bort associerade tillgången. En tillgång kan inte tas bort om den används av en händelse. Händelsen måste tas bort först. 

Även efter att du stoppat och tagit bort händelsen skulle användarna kunna strömma ditt arkiverade innehåll som en video på begäran så länge du inte tar bort tillgången.

Om du vill behålla det arkiverade innehållet, men inte att det ska vara tillgängligt för strömning, tar du bort strömningslokaliseraren.

###Använda portalen för att skapa en kanal 

Detta avsnitt visar hur du använder alternativet **Snabbregistrering** för att skapa en genomströmningskanal.

Mer information om genomströmningskanaler finns i [Direktsänd strömning med lokala kodare som skapar dataströmmar i multibithastighet](media-services-live-streaming-with-onprem-encoders.md).

1. I fönstret **Inställningar** klickar du på **Direktsänd strömning**. 

    ![Komma igång](./media/media-services-portal-passthrough-get-started/media-services-getting-started.png)
    
    Fönstret **Direktsänd strömning** visas.

3. Klicka på **Snabbregistrering** för att skapa en genomströmningskanal med RTMP-infogningsprotokollet.

    Fönstret **SKAPA EN NY KANAL** visas.
4. Namnge den nya kanalen och klicka på **Skapa**. 

    Detta skapar en genomströmningskanal med RTMP-infogningsprotokollet.

##Skapa händelser

1. Välj en kanal till vilken du vill lägga till en händelse.
2. Tryck på knappen **Direktsänd händelse**.

![Händelse](./media/media-services-portal-passthrough-get-started/media-services-create-events.png)


##Hämta infognings-URL:er

När kanalen har skapats kan du få infognings-URL:er som du tillhandahåller till livekodaren. Kodaren använder dessa URL:er för att mata in en direktsänd dataström.

![Skapad](./media/media-services-portal-passthrough-get-started/media-services-channel-created.png)

##Titta på händelsen

För att titta på händelsen klickar du på **Titta på** i Azure-portalen eller kopierar strömnings-URL:en och använder en valfri spelare. 
 
![Skapad](./media/media-services-portal-passthrough-get-started/media-services-default-event.png)

Direktsända händelser konverteras automatiskt till innehåll på begäran när de stoppas.

##Rensa

Mer information om genomströmningskanaler finns i [Direktsänd strömning med lokala kodare som skapar dataströmmar i multibithastighet](media-services-live-streaming-with-onprem-encoders.md).

- En kanal kan stoppas endast när alla händelser eller program i kanalen har stoppats.  När kanalen har stoppats medför den inga avgifter. När du vill starta den igen har den samma infognings-URL så att du inte behöver konfigurera om din kodare.
- En kanal kan bara tas bort när alla direktsända händelser i kanalen har tagits bort.

##Visa arkiverat innehåll

Även efter att du stoppat och tagit bort händelsen skulle användarna kunna strömma ditt arkiverade innehåll som en video på begäran så länge du inte tar bort tillgången. En tillgång kan inte tas bort om den används av en händelse. Händelsen måste tas bort först. 

För att hantera dina tillgångar väljer du **Inställning** och klickar på **Tillgångar**.

![Tillgångar](./media/media-services-portal-passthrough-get-started/media-services-assets.png)

##Nästa steg

Granska sökvägarna för Media Services-utbildning.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Ge feedback

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]



<!--HONumber=Sep16_HO3-->


