---
title: Infoga annonser på klientsidan | Microsoft-dokument
description: Den här artikeln visar hur du infogar annonser i dina media på klientsidan.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 65c9c747-128e-497e-afe0-3f92d2bf7972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 274ee09ae98dd229b255e58261f462e322be9f89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565748"
---
# <a name="inserting-ads-on-the-client-side"></a>Infoga annonser på klientsidan
Den här artikeln innehåller information om hur du infogar olika typer av annonser på klientsidan.

Information om dold textning och annonsstöd i direktuppspelning av livevideor finns i [Standarder för dold textning och annonsinfogning](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads).

> [!NOTE]
> Azure Media Player stöder för närvarande inte annonser.
> 
> 

## <a name="inserting-ads-into-your-media"></a><a id="insert_ads_into_media"></a>Infoga annonser i media
Azure Media Services ger stöd för annonsinfogning via Windows Media Platform: Player Frameworks. Spelarramverk med annonsstöd är tillgängliga för Windows 8-, Silverlight-, Windows Phone 8- och iOS-enheter. Varje spelarramverk innehåller exempelkod som visar hur du implementerar ett spelarprogram. Det finns tre olika typer av annonser som du kan infoga i din media:lista.

* **Linjära** – helbildsannonser som pausar huvudvideon.
* **Ickelinjära** – överlägg annonser som visas när huvudvideon spelas upp, vanligtvis en logotyp eller annan statisk bild placerad i spelaren.
* **Companion** – annonser som visas utanför spelaren.

Annonser kan placeras när som helst i huvudvideons tidslinje. Du måste tala om för spelaren när du ska spela upp annonsen och vilka annonser som ska spelas upp. Detta görs med hjälp av en uppsättning xml-baserade standardfiler: Video Ad Service Template (VAST), Digital Video Multiple Ad Playlist (VMAP), Media Abstract Sequencing Template (MAST) och Digital Video Player Ad Interface Definition (VPAID). VAST-filer anger vilka annonser som ska visas. VMAP-filer anger när olika annonser ska spelas upp och innehåller VAST XML. MAST-filer är ett annat sätt att sekvens annonser som också kan innehålla VAST XML. VPAID-filer definierar ett gränssnitt mellan videospelaren och annons- eller annonsservern.

Varje spelare ram fungerar på olika sätt och varje kommer att omfattas av sin egen artikel. I den här artikeln beskrivs de grundläggande mekanismer som används för att infoga annonser. Videospelarprogram begär annonser från en annonsserver. Annonsservern kan svara på flera olika sätt:

* Returnera en VAST-fil
* Returnera en VMAP-fil (med inbäddade VAST)
* Returnera en MAST-fil (med inbäddade VAST)
* Returnera en VAST-fil med VPAID-annonser

### <a name="using-a-video-ad-service-template-vast-file"></a>Använda en VAST-fil (Video Ad Service Template)
En VAST-fil anger vilken annons eller vilka annonser som ska visas. Följande XML är ett exempel på en VAST-fil för en linjär annons:

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="115571748">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://www.myserver.com/tracking-resource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <TrackingEvents>
                  <Tracking event="start"><![CDATA[http://www.myserver.com/start-tracking-resource]]></Tracking>
                  <Tracking event="midpoint"><![CDATA[http://www.myserver.com/midpoint-tracking-resource]]></Tracking>
                  <Tracking event="complete"><![CDATA http://www.myserver.com/complete-tracking-resource]]></Tracking>
                  <Tracking event="expand"><![CDATA[http://www.myserver.com/expand-tracking-resource]]></Tracking>
                </TrackingEvents>
                <VideoClicks>
                  <ClickThrough id="Atlas Redirect"><![CDATA[http://www.myserver.com/click-resource]]></ClickThrough>
                  <ClickTracking id="Spare"></ClickTracking>
                </VideoClicks>
                <MediaFiles>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_300_4x3.wmv]]>
                  </MediaFile>
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
          <Extensions>
            <Extension type="Atlas">
            </Extension>
          </Extensions>
        </InLine>
      </Ad>
    </VAST>
```

Den linjära annonsen beskrivs av elementet <**linjär**>. Den anger annonsens varaktighet, spårningshändelser, klickning, klickspårning och ett antal **MediaFile-element.** Spårningshändelser anges i <**TrackingEvents**> element och gör det möjligt för en annonsserver att spåra olika händelser som inträffar när du visar annonsen. I det här fallet spåras händelser för start, mittpunkt, slutföra och expandera. Starthändelsen inträffar när annonsen visas. Mittpunktshändelsen inträffar när minst 50 % av annonsens tidslinje har visats. Den fullständiga händelsen inträffar när annonsen har körts till slutet. Händelsen Expandera inträffar när användaren utökar videospelaren till helskärm. Klickningar anges med ett <**ClickThrough**> element i ett <**VideoClicks**> element och anger en URI till en resurs som ska visas när användaren klickar på annonsen. ClickTracking anges i ett <**ClickTracking**> element, även inom <**VideoClicks**> elementet och anger en spårningsresurs som spelaren kan begära när användaren klickar på annonsen. Elementen <**MediaFile**> anger information om en viss kodning av en annons. När det finns mer än en <**MediaFile**> element, kan videospelaren välja den bästa kodningen för plattformen.

Linjära annonser kan visas i en angiven ordning. Det gör du `<Ad>` genom att lägga till ytterligare element i VAST-filen och ange ordningen med sekvensattributet. Följande exempel illustrerar detta:

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
      <Ad id="2" sequence="1">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:30</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
    </VAST>
```

Ickelinjära annonser anges `<Creative>` också i ett element. I följande exempel `<Creative>` visas ett element som beskriver en icke-linjär annons.

```xml
    <Creative id="video" sequence="1" AdID="">
      <NonLinearAds>
        <NonLinear width="216" height="121" minSuggestedDuration="00:00:15">
          <StaticResource creativeType="image/png"><![CDATA[http://myserver/images/image.png]]></StaticResource>
          <StaticResource creativeType="image/jpg"><![CDATA[http://myserver/images/image.jpg]]></StaticResource>
        </NonLinear>
        <TrackingEvents>
             <Tracking event="acceptInvitation"><![CDATA[http://myserver/tracking/trackingID]></Tracking>
             <Tracking event="collapse"><![CDATA[http://myserver/tracking/trackingID2]]></Tracking>
         </TrackingEvents>
       </NonLinearAds>
    </Creative>
```

Det <**icke-linjära** elementet> kan innehålla ett eller flera <**icke-linjära**> element, som alla kan beskriva en ickelinjär annons. Det icke-linjära elementet <**icke-linjära**> anger resursen för den icke-linjära annonsen. Resursen kan vara en <**StaticResource**>, en <**IFrameResource**> eller en <**HTMLResource**>. \<**StaticResource**> beskriver en icke-HTML-resurs och definierar ett creativeType-attribut som anger hur resursen visas:

Bild/gif, bild/jpeg, bild/png – resursen visas i en HTML-<**img**>-tagg.

Application/x-javascript – resursen visas i ett HTML-<**skript**> tagg.

Application/x-shockwave-flash – resursen visas i en Flash-spelare.

**IFrameResource** beskriver en HTML-resurs som kan visas i en IFrame. **HTMLResource** beskriver en bit HTML-kod som kan infogas på en webbsida. **TrackingEvents anger spårningshändelser** och URI att begära när händelsen inträffar. I det här exemplet spåras händelserna acceptInvitation och collapse. Mer information om elementet **Icke-linjära meddelanden** och dess underordnade finns i IAB.NET/VAST. Observera att **elementet TrackingEvents** finns i elementet **NonLinearAds** i stället för det **icke-linjära** elementet.

Medföljande annonser definieras `<CompanionAds>` i ett element. Elementet `<CompanionAds>` kan innehålla `<Companion>` ett eller flera element. Varje `<Companion>` element beskriver en kompletterande `<StaticResource>`annons `<IFrameResource>`och `<HTMLResource>` kan innehålla en , eller som anges på samma sätt som i en icke-linjär annons. En VAST fil kan innehålla flera kompletterande annonser och spelaren ansökan kan välja den lämpligaste annons att visa. För mer information om VAST, se [VAST 3.0](https://www.iab.net/media/file/VASTv3.0.pdf).

### <a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Använda en VMAP-fil (Digital Video Multiple Ad Playlist)
Med en VMAP-fil kan du ange när annonspauser inträffar, hur lång varje paus är, hur många annonser som kan visas i en paus och vilka typer av annonser som kan visas under en paus. Följande i en VMAP-exempel-fil som definierar en enda annonspaus:

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[https://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
                  <Creatives>
                    <Creative id="video" sequence="0" AdID="">
                      <Linear>
                        <Duration>00:00:32</Duration>
                        <MediaFiles>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scalable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv]]>
                          </MediaFile>
                        </MediaFiles>
                      </Linear>
                    </Creative>
                  </Creatives>
                </InLine>
              </Ad>
            </VAST>
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

En VMAP-fil börjar `<VMAP>` med ett element `<AdBreak>` som innehåller ett eller flera element, som var och en definierar en annonsbrytning. Varje annonsbrytning anger en rasttyp, rast-ID och tidsförskjutning. Attributet breakType anger vilken typ av annons som kan spelas upp under rasten: linjär, ickelinjär eller visning. Visa annonser mappas till VAST kompletterande annonser. Mer än en annonstyp kan anges i en kommaavgränsad lista.More than one ad type can bespecified in a komman (no spaces) separated list. BreakID är en valfri identifierare för annonsen. TimeOffset anger när annonsen ska visas. Det kan anges på något av följande sätt:

1. Tid – i hh:mm:ss eller hh:mm:ss.mmm format där .mmm är millisekunder. Värdet för det här attributet anger tiden från början av videotidslinjen till början av annonspausen.
2. Procent – i n% format där n är procentandelen av videotidslinjen som ska spelas upp innan annonsen spelas upp
3. Start/slut – anger att en annons ska visas före eller efter att videon har visats
4. Position – anger ordningen på annonsavbrott när tidpunkten för annonspauserna är okänd, till exempel i livestreaming. Ordningen för varje annonsbrytning anges i det #n format där n är ett heltal 1 eller mer. 1 betyder att annonsen ska spelas vid första tillfället, 2 betyder att annonsen ska spelas vid andra tillfälle och så vidare.

I `<AdBreak>` elementet kan det finnas ett <**AdSource->** element. Det <AdSource->-elementet innehåller följande attribut: **AdSource**

1. Id – anger en identifierare för annonskällan
2. allowMultipleAds – ett booleskt värde som anger om flera annonser kan visas under annonspausen
3. followRedirects – ett valfritt booleskt värde som anger om videospelaren ska respektera omdirigeringar i ett annonssvar

Det <AdSource->-elementet ger spelaren ett infogat annonssvar eller en referens till ett annonssvar. **AdSource** Den kan innehålla något av följande:

* `<VASTAdData>`anger att ett VAST-annonssvar är inbäddat i VMAP-filen
* `<AdTagURI>`en URI som refererar till ett annonssvar från ett annat system
* `<CustomAdData>`-en godtycklig sträng som representerar ett icke-VAST-svar

I det här exemplet anges ett in-line `<VASTAdData>` annonssvar med ett element som innehåller ett VAST-annonssvar. Mer information om de andra elementen finns i [VMAP](https://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap).

Det <AdBreak->-elementet kan också innehålla ett <**TrackingEvents**> element. **AdBreak** Med <**TrackingEvents**> element kan du spåra början eller slutet av en annonspaus eller om ett fel uppstod under annonspausen. Det <**TrackingEvents**>-elementet innehåller ett eller flera <**spårning**> element, som var och en anger en spårningshändelse och en spårnings-URI. Möjliga spårningshändelser är:

1. breakStart – spårar början på en annonspaus
2. breakEnd – spåra slutförandet av en annonspaus
3. fel – spårar ett fel som uppstod under annonspausen

I följande exempel visas en VMAP-fil som anger spårningshändelser

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <!--Inline VAST -->
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
          <vmap:Tracking event="breakend">
            http://MyServer.com/breakend.gif
          </vmap:Tracking>
          <vmap:Tracking event="error">
            http://MyServer.com/error.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

Mer information om <**TrackingEvents**> element och dess underordnade finns ihttp://iab.net/VMAP.pdf

### <a name="using-a-media-abstract-sequencing-template-mast-file"></a>Använda en MAST-fil (Media Abstract Sequencing Template)
Med en MAST-fil kan du ange utlösare som definierar när en annons visas. Följande är en EXEMPELMAM-fil som innehåller utlösare för en pre roll-annons, en mid-roll-annons och en post-roll-annons.

```xml
    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
      <triggers>
        <trigger id="preroll" description="preroll every item"  >
          <startConditions>
            <condition type="event" name="OnItemStart" />
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="midroll" description="midroll at 15 sec."  >
          <startConditions>
            <condition type="property" name="Position" value="00:00:15.0" operator="GEQ" />
          </startConditions>
          <endConditions>
            <condition type="event" name="OnItemEnd"/>
            <!--This 'resets' the trigger for the next clip-->
          </endConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="postroll" description="postroll"  >
          <startConditions>
            <condition type="event" name="OnItemEnd"/>
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
      </triggers>
    </MAST>
```


En MAST-fil börjar med ett **MAST-element** som innehåller ett **utlösarelement.** Elementet `<triggers>` innehåller ett eller **flera utlösarelement** som definierar när en annons ska spelas upp.

**Utlösarelementet** innehåller ett **startConditions-element** som anger när en annons ska börja spelas upp. Elementet **startConditions** innehåller ett `<condition>` eller flera element. När `<condition>` varje utvärderar till true initieras eller återkallas `<condition>` en utlösare beroende på om den finns i ett **startConditions-** eller **endConditions-element.** När `<condition>` flera element finns behandlas de som ett implicit ELLER, alla villkor som utvärderas till true kommer att orsaka att utlösaren initieras. `<condition>`element kan kapslas. När `<condition>` underordnade element är förinställda behandlas de som implicita OCH alla villkor måste utvärderas till true för att utlösaren ska initieras. Elementet `<condition>` innehåller följande attribut som definierar villkoret:

1. **typ** – anger vilken typ av villkor, händelse eller egenskap
2. **namn** – namnet på den egendom eller händelse som ska användas under utvärderingen
3. **värde** – det värde som en egenskap kommer att utvärderas mot
4. **operatör** – den drift som ska användas under utvärdering: EQ (lika), NEQ (inte lika), GTR (större), GEQ (större eller lika), LT (Mindre än), LEQ (mindre än eller lika), MOD (modulo)

**endConditions** innehåller `<condition>` också element. När ett villkor utvärderas till true återställs utlösaren. Elementet `<trigger>` innehåller också `<sources>` ett element som `<source>` innehåller ett eller flera element. Elementen `<source>` definierar URI till annonssvaret och typen av annonssvar. I det här exemplet ges en URI till ett VAST-svar.

```xml
    <trigger id="postroll" description="postroll"  >
      <startConditions>
        <condition/>
      </startConditions>
      <sources>
        <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
          <sources />
        </source>
      </sources>
    </trigger>
```

### <a name="using-video-player-ad-interface-definition-vpaid"></a>Använda definition av videospelare-annonsgränssnitt (VPAID)
VPAID är ett API för att aktivera körbara annonsenheter för att kommunicera med en videospelare. Detta möjliggör mycket interaktiva annonsupplevelser. Användaren kan interagera med annonsen och annonsen kan svara på åtgärder som vidtas av tittaren. En annons kan till exempel visa knappar som gör att användaren kan visa mer information eller en längre version av annonsen. Videospelaren måste stödja VPAID API och den körbara annonsen måste implementera API. När en spelare begär en annons från en annonsserver kan servern svara med ett VAST-svar som innehåller en VPAID-annons.

En körbar annons skapas i kod som måste köras i en körningsmiljö som Adobe Flash™ eller JavaScript som kan köras i en webbläsare. När en annonsserver returnerar ett VAST-svar som innehåller en VPAID-annons måste värdet för attributet apiFramework i elementet `<MediaFile>` vara "VPAID". Det här attributet anger att den inneslutna annonsen är en VPAID-körbar annons. Typattributet måste ställas in på MIME-typen för den körbara filen, till exempel "application/x-shockwave-flash" eller "application/x-javascript". Följande XML-kodavsnitt visar `<MediaFile>` elementet från ett VAST-svar som innehåller en VPAID-körbar annons.

```xml
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type="application/x-shockwaveflash"
                  width="640" height="480" apiFramework="VPAID">
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
```

En körbar annons kan initieras med `<Linear>` `<NonLinear>` hjälp av elementet `<AdParameters>` i eller elementen i ett VAST-svar. Mer information om `<AdParameters>` elementet finns i [VAST 3.0](https://www.iab.net/media/file/VASTv3.0.pdf). Mer information om VPAID API finns i [VPAID 2.0](https://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf).

## <a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Implementera en Windows- eller Windows Phone 8-spelare med annonssupport
Microsoft Media Platform: Player Framework för Windows 8 och Windows Phone 8 innehåller en samling exempelprogram som visar hur du implementerar ett videospelarprogram med hjälp av ramverket. Du kan hämta Ramverket för spelare och exempel från [Player Framework för Windows 8 och Windows Phone 8](https://playerframework.codeplex.com).

När du öppnar lösningen Microsoft.PlayerFramework.Xaml.Samples visas ett antal mappar i projektet. Annonsmappen innehåller exempelkoden som är relevant för att skapa en videospelare med annonsstöd. Inuti annons mappen finns ett antal XAML / cs filer som var och en visar hur man infogar annonser på ett annat sätt. I följande lista beskrivs var och en:

* AdPodPage.xaml Visar hur du visar en annonspod.
* AdSchedulingPage.xaml Visar hur du schemalägger annonser.
* FreeWheelPage.xaml Visar hur du använder FreeWheel-insticksprogrammet för att schemalägga annonser.
* MastPage.xaml Visar hur du schemalägger annonser med en MAST-fil.
* ProgrammaticAdPage.xaml Visar hur du schemalägger annonser i en video programmatiskt.
* ScheduleClipPage.xaml Visar hur du schemalägger en annons utan en VAST-fil.
* VastLinearCompanionPage.xaml Visar hur du infogar en linjär och kompletterande annons.
* VastNonLinearPage.xaml Visar hur du infogar en icke-linjär annons.
* VmapPage.xaml Visar hur du anger annonser med en VMAP-fil.

Vart och ett av dessa exempel använder klassen MediaPlayer som definieras av spelarramverket. De flesta exempel använder plugins som lägger till stöd för olika annonssvarsformat. ProgrammaticAdPage-exemplet interagerar programmässigt med en MediaPlayer-instans.

### <a name="adpodpage-sample"></a>Exempel på AdPodPage
I det här exemplet används AdSchedulerPlugin för att definiera när en annons ska visas. I det här exemplet är en mid-roll-annons schemalagd att spelas upp efter fem sekunder. Annonspodden (en grupp annonser som ska visas i ordning) anges i en VAST-fil som returneras från en annonsserver. URI till VAST-filen anges i `<RemoteAdSource>` elementet.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">

        <mmppf:MediaPlayer.Plugins>
            <ads:AdSchedulerPlugin>
                <ads:AdSchedulerPlugin.Advertisements>

                    <ads:MidrollAdvertisement Time="00:00:05">
                        <ads:MidrollAdvertisement.Source>
                            <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_adpod.xml" Type="vast"/>
                        </ads:MidrollAdvertisement.Source>
                    </ads:MidrollAdvertisement>

                </ads:AdSchedulerPlugin.Advertisements>
            </ads:AdSchedulerPlugin>
            <ads:AdHandlerPlugin/>
        </mmppf:MediaPlayer.Plugins>
    </mmppf:MediaPlayer>
```

Mer information om AdSchedulerPlugin finns [i Annonsering i Ramverket för Spelare på Windows 8 och Windows Phone 8](https://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation)

### <a name="adschedulingpage"></a>AdSchedulingPage
I det här exemplet används även AdSchedulerPlugin. Den schemalägger tre annonser, en förhandsannons, en mid-roll-annons och en post-roll-annons. URI till VAST för varje annons anges `<RemoteAdSource>` i ett element.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:PrerollAdvertisement>
                                <ads:PrerollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PrerollAdvertisement.Source>
                            </ads:PrerollAdvertisement>

                            <ads:MidrollAdvertisement Time="00:00:15">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                            <ads:PostrollAdvertisement>
                                <ads:PostrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PostrollAdvertisement.Source>
                            </ads:PostrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="freewheelpage"></a>FreeWheelPage
I det här exemplet används FreeWheelPlugin som anger ett källattribut som anger en URI som pekar på en SmartXML-fil som anger annonsinnehåll samt information om annonsplanering.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="mastpage"></a>MastPage (På andra sidan)
I det här exemplet används MastSchedulerPlugin som gör att du kan använda en MAST-fil. Attributet Source anger platsen för MAST-filen.
```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="programmaticadpage"></a>ProgrammaticAdPage
Det här exemplet interagerar programmässigt med MediaPlayer. Filen ProgrammaticAdPage.xaml instansierar MediaPlayer:

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>
```

Den ProgrammaticAdPage.xaml.cs filen skapar en AdHandlerPlugin, lägger till en TimelineMarker för att ange när en annons ska visas och lägger sedan till en hanterare för den MarkerReached-händelse som läser in en RemoteAdSource som anger en URI i en VAST-fil och sedan spelar upp annonsen.

```csharp
    public sealed partial class ProgrammaticAdPage : Microsoft.PlayerFramework.Samples.Common.LayoutAwarePage
        {
            AdHandlerPlugin adHandler;

            public ProgrammaticAdPage()
            {
                this.InitializeComponent();
                adHandler = new AdHandlerPlugin();
                player.Plugins.Add(new AdHandlerPlugin());
                player.Markers.Add(new TimelineMarker() { Time = TimeSpan.FromSeconds(5), Type = "myAd" });
                player.MarkerReached += pf_MarkerReached;
            }

            async void pf_MarkerReached(object sender, TimelineMarkerRoutedEventArgs e)
            {
                if (e.Marker.Type == "myAd")
                {
                    var adSource = new RemoteAdSource() { Type = VastAdPayloadHandler.AdType, Uri = new Uri("http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml") };
                    //var adSource = new AdSource() { Type = DocumentAdPayloadHandler.AdType, Payload = SampleAdDocument };
                    var progress = new Progress<AdStatus>();
                    try
                    {
                        await player.PlayAd(adSource, progress, CancellationToken.None);
                    }
                    catch { /* ignore */ }
                }
            }
```

### <a name="scheduleclippage"></a>ScheduleClipPage
I det här exemplet används AdSchedulerPlugin för att schemalägga en mellanuppningsannons genom att ange en WMV-fil som innehåller annonsen.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.cloudapp.net/html5/media/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:AdSource Type="clip">
                                        <ads:AdSource.Payload>
                                            <ads:ClipAdPayload MediaSource="http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv" MimeType="video/x-ms-wmv" />
                                        </ads:AdSource.Payload>
                                    </ads:AdSource>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearcompanionpage"></a>VastLinearCompanionPage
Det här exemplet illustrerar hur du använder AdSchedulerPlugin för att schemalägga en linjär annons i mitten av rullen med en tillhörande annons. Elementet `<RemoteAdSource>` anger platsen för VAST-filen.

```xml
    <mmppf:MediaPlayer Grid.Row="1"  x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_companions.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearnonlinearpage"></a>VastLinearNonLinearPage
I det här exemplet används AdSchedulerPlugin för att schemalägga en linjär och en icke-linjär annons. FILEN VAST anges med elementet. `<RemoteAdSource>`

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_nonlinear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vmappage"></a>VMAPPage
I det här exemplet används VmapSchedulerPlugin för att schemalägga annonser med en VMAP-fil. URI-filen till VMAP-filen anges i elementets `<VmapSchedulerPlugin>` källattribut.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

## <a name="implementing-an-ios-video-player-with-ad-support"></a>Implementera en iOS-videospelare med annonssupport
Microsoft Media Platform: Player Framework för iOS innehåller en samling exempelprogram som visar hur du implementerar ett videospelarprogram med ramverket. Du kan hämta Player Framework och exemplen från [Azure Media Player Framework](https://github.com/Azure/azure-media-player-framework). GitHub-sidan har en länk till en Wiki som innehåller ytterligare information om spelarramverket och en introduktion till spelarexemplet: [Azure Media Player Wiki](https://github.com/Azure/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).

### <a name="scheduling-ads-with-vmap"></a>Schemalägga annonser med VMAP
I följande exempel visas hur du schemalägger annonser med en VMAP-fil.

```csharp
    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest

    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
            {
                [self logFrameworkError];
            }
            else
            {
                // Schedule a list of ads using the downloaded VMAP manifest
                if (![framework scheduleVMAPWithManifest:manifest])
                {
                    [self logFrameworkError];
                }
            }
```

### <a name="scheduling-ads-with-vast"></a>Schemalägga annonser med VAST
Följande exempel visar hur du schemalägger en sen bindning VAST-annons.


```csharp
    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
    // Create an AdInfo object
     AdInfo *vastAdInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URL to VAST file
    vastAdInfo1.clipURL = [NSURL URLWithString:vastAd1];
    // set running time of ad
    vastAdInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    vastAdInfo1.mediaTime.clipBeginMediaTime = 0;
    vastAdInfo1.mediaTime.clipEndMediaTime = 10;
    vastAdInfo1.policy = @"policy for late binding VAST";
    // specify ad type
    vastAdInfo1.type = AdType_Midroll;
    vastAdInfo1.appendTo=-1;
    adIndex = 0;
    // schedule ad
    if (![framework scheduleClip:vastAdInfo1 atTime:adLinearTime forType:PlaylistEntryType_VAST andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

   Följande exempel visar hur du schemalägger en tidig bindning VAST-annons.

```csharp
    //Example:4 Schedule an early binding VAST ad
    //Download the VAST file
    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]])
    {
        [self logFrameworkError];
    }
    else
    {
        adLinearTime.startTime = 7;
        adLinearTime.duration = 0;

        // Create AdInfo instance
        AdInfo *vastAdInfo2 = [[[AdInfo alloc] init] autorelease];
        vastAdInfo2.mediaTime = [[[MediaTime alloc] init] autorelease];
        vastAdInfo2.policy = @"policy for early binding VAST";
        // specify ad type
        vastAdInfo2.type = AdType_Midroll;
        vastAdInfo2.appendTo=-1;
        // schedule ad
        if (![framework scheduleVASTClip:vastAdInfo2 withManifest:manifest atTime:adLinearTime andGetClipId:&adIndex])
        {
            [self logFrameworkError];
        }
    }
```

Följande exempel visar hur du infogar en annons med Rough Cut Editing (RCE)

```csharp
    //Example:1 How to use RCE.
    // specify manifest for ad content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // specify ad length
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
    // append ad content
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }
```

I följande exempel visas hur du schemalägger en annonspod.

```csharp
    //Example:5 Schedule an ad Pod.
    // Set start time for ad
    adLinearTime.startTime = 23;
    adLinearTime.duration = 0;

    // Specify URL to content
    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    // Create an AdInfo instance
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URI to ad content
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    // Set ad running time
    adpodInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.policy = @"policy for ad pod 1";
    // Set ad type
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    adIndex = 0;
    // Schedule ad
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

I följande exempel visas hur du schemalägger en icke-klibbig mellanuppningsannons. En icke-klibbig annons spelas bara en gång oavsett om tittaren söker.

```csharp
    //Example:6 Schedule a single non sticky mid roll Ad
    // specify URL to content
    NSString *oneTimeAd=@"http://wamsblureg001orig-hs.cloudapp.net/5389c0c5-340f-48d7-90bc-0aab664e5f02/Windows%208_%20You%20and%20Me%20Together-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // create an AdInfo instance
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    // set URL of ad
    oneTimeInfo.clipURL = [NSURL URLWithString:oneTimeAd];
    oneTimeInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    oneTimeInfo.mediaTime.clipBeginMediaTime = 0;
    oneTimeInfo.mediaTime.clipEndMediaTime = -1;
    oneTimeInfo.policy = @"policy for one-time ad";
    // set ad start time
    adLinearTime.startTime = 43;
    adLinearTime.duration = 0;
    // set ad type
    oneTimeInfo.type = AdType_Midroll;
    // non sticky ad
    oneTimeInfo.deleteAfterPlayed = YES;
    // schedule ad
    if (![framework scheduleClip:oneTimeInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

I följande exempel visas hur du schemalägger en klibbig mittenannons. En klibbig annons visas varje gång den angivna punkten på videotidslinjen nås.

```csharp
    //Example:7 Schedule a single sticky mid roll Ad
    NSString *stickyAd=@"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *stickyAdInfo = [[[AdInfo alloc] init] autorelease];
    // set URI to ad
    stickyAdInfo.clipURL = [NSURL URLWithString:stickyAd];
    stickyAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    stickyAdInfo.mediaTime.clipBeginMediaTime = 0;
    stickyAdInfo.mediaTime.clipEndMediaTime = 15;
    stickyAdInfo.policy = @"policy for sticky mid-roll ad";
    // set ad start time
    adLinearTime.startTime = 64;
    adLinearTime.duration = 0;
    // set ad type
    stickyAdInfo.type = AdType_Midroll;
    stickyAdInfo.deleteAfterPlayed = NO;
    // schedule ad
    if (![framework scheduleClip:stickyAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Följande exempel visar hur du schemalägger en annons efter kastet.

```csharp
    //Example:8 Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    // set ad length
    postAdInfo.mediaTime.clipEndMediaTime = 45;
    postAdInfo.policy = @"policy for post-roll ad";
    // set ad type
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Följande exempel visar hur du schemalägger en förbokad annons.

```csharp
    //Example:9 Schedule Pre Roll Ad
    NSString *adURLString = @"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 40; //You could play a portion of an Ad. Yeh!
    adInfo.mediaTime.clipEndMediaTime = 59;
    adInfo.policy = @"policy for pre-roll ad";
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    // schedule ad
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

Följande exempel visar hur du schemalägger en mellanliggande övertäckningsannons.

```csharp
    // Example10: Schedule a Mid Roll overlay Ad
    NSString *adURLString = @"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    //Create AdInfo instance
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 0;
    // specify ad length
    adInfo.mediaTime.clipEndMediaTime = 20;
    adInfo.policy = @"policy for mid-roll overlay ad";
    adInfo.appendTo = -1;
    // specify ad type
    adInfo.type = AdType_Midroll;
    // specify ad start time & duration
    adLinearTime.startTime = 300;
    adLinearTime.duration = 20;
    // schedule ad            if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```


## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
