---
title: "Flera inkommande filer och egenskaperna för komponenten med Premium-kodare - Azure | Microsoft Docs"
description: "Det här avsnittet beskriver hur du använder setRuntimeProperties att använda flera inkommande filer och skickar egna data till medieprocessor Media Encoder Premium arbetsflöde."
services: media-services
documentationcenter: 
author: xpouyat
manager: cfowler
editor: 
ms.assetid: 7fb35bdd-9891-4401-a65b-ef3cc8190e8a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: xpouyat;anilmur;juliako
ms.openlocfilehash: df1ee5089a0af6ffce1431b658843fcb34a66ce5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Använda flera indatafiler och egenskaperna för komponenten med Premium-kodare
## <a name="overview"></a>Översikt
Det finns scenarier där du kan behöva anpassa egenskaperna för komponenten anger Clip listan XML-innehåll eller skicka flera indatafiler när du skickar en uppgift med den **Media Encoder Premium arbetsflöde** medieprocessor. Några exempel är:

* Ovanpå text på video och ange textvärde (till exempel det aktuella datumet) vid körning för varje inkommande video.
* Anpassa Clip listan XML-filen (om du vill ange en eller flera källfiler, med eller utan trimning osv.).
* Ovanpå en logotyp på indata video medan kodas video.
* Flera språk för ljud-kodning.

Så att den **Media Encoder Premium arbetsflöde** vet att du ändrar vissa egenskaper i arbetsflödet när du skapar uppgiften eller skicka flera indatafiler måste du använda en configuration-sträng som innehåller **setRuntimeProperties** och/eller **transcodeSource**. Det här avsnittet beskriver hur de används.

## <a name="configuration-string-syntax"></a>Strängsyntaxen konfiguration
Av konfigurationssträngen som ska anges i aktiviteten kodning använder ett XML-dokument som ser ut så här:

```xml
<?xml version="1.0" encoding="utf-8"?>
<transcodeRequest>
  <transcodeSource>
  </transcodeSource>
  <setRuntimeProperties>
    <property propertyPath="Media File Input/filename" value="VideoFileName.mp4" />
  </setRuntimeProperties>
</transcodeRequest>
```

Följande är C#-kod som läser XML-konfigurationen från en fil, uppdatera det med rätt video filnamnet och skickar den till aktivitet i ett jobb:

```c#
string premiumConfiguration = ReadAllText(@"D:\home\site\wwwroot\Presets\SetRuntime.xml").Replace("VideoFileName", myVideoFileName);

// Declare a new job.
IJob job = _context.Jobs.Create("Premium Workflow encoding job");

// Get a media processor reference, and pass to it the name of the 
// processor to use for the specific task.
IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");

// Create a task with the encoding details, using a string preset.
ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
                              processor,
                              premiumConfiguration,
                              TaskOptions.None);

// Specify the input assets
task.InputAssets.Add(workflow); // workflow asset
task.InputAssets.Add(video); // video asset with multiple files

// Add an output asset to contain the results of the job. 
// This output is specified as AssetCreationOptions.None, which 
// means the output asset is not encrypted. 
task.OutputAssets.AddNew("Output asset", AssetCreationOptions.None);
```

## <a name="customizing-component-properties"></a>Anpassa egenskaperna för komponenten
### <a name="property-with-a-simple-value"></a>Egenskapen med ett enkelt värde
I vissa fall är det bra att anpassa en komponent egenskap tillsammans med arbetsflödesfilen som ska utföras av Media Encoder Premium arbetsflöde.

Anta att du skapat ett arbetsflöde överlägg texten på videor och text (till exempel det aktuella datumet) måste anges vid körning. Du kan göra detta genom att skicka texten som ska anges som det nya värdet för egenskapen text för komponenten överlägget från aktiviteten kodning. Du kan använda den här mekanismen för att ändra andra egenskaper för en komponent i arbetsflödet (till exempel positionen eller färgen på överlägget, bithastighet AVC-kodaren osv.).

**setRuntimeProperties** används för att åsidosätta en egenskap i komponenter i arbetsflödet.

Exempel:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="MyInputVideo.mp4" />
      <property propertyPath="/primarySourceFile" value="MyInputVideo.mp4" />
      <property propertyPath="Optional Text Overlay/Text To Image Converter/text" value="Today is Friday the 13th of May, 2016"/>
  </setRuntimeProperties>
</transcodeRequest>
```

### <a name="property-with-an-xml-value"></a>Egenskapen med ett XML-värde
Om du vill ange en egenskap som förväntar sig ett XML-värde som kapslar in med hjälp av `<![CDATA[ and ]]>`.

Exempel:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

> [!NOTE]
> Kontrollera att inte att placera en transport returnerade precis efter `<![CDATA[`.

### <a name="propertypath-value"></a>propertyPath-värde
I föregående exempel kan propertyPath var ”/ mediafilen indata/filename” eller ”/ inactiveTimeout” eller ”clipListXml”.
Detta är generellt sett är namnet på komponenten och sedan namnet på egenskapen. Sökvägen kan innehålla fler eller färre nivåer, som ”/ primarySourceFile” (eftersom egenskapen är roten i arbetsflödet) eller ”/ Video bearbetning/bild överlägget/opacitet” (eftersom överlägget är i en grupp).    

Använd åtgärdsknappen som är direkt bredvid varje egenskap för att kontrollera sökvägen och egenskapen. Du kan klicka på åtgärdsknappen och välja **redigera**. Det här visas det faktiska namnet på egenskapen, och omedelbart ovanför det namnområdet.

![Åtgärden och redigera](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Egenskap](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Flera inkommande filer
Varje aktivitet som du skickar till den **Media Encoder Premium arbetsflöde** kräver två tillgångar:

* Den första är en *arbetsflöde tillgången* som innehåller ett arbetsflödesfil. Du kan utforma Arbetsflödesfiler med hjälp av den [Arbetsflödesdesignern](media-services-workflow-designer.md).
* Den andra är en *Media tillgången* som innehåller filen media som du vill koda.

När du skickar flera mediefiler till den **Media Encoder Premium arbetsflöde** kodare, följande villkor gäller:

* Alla mediefiler måste vara i samma *Media tillgången*. Flera mediaresurser stöds inte.
* Du måste ange den primära filen i tillgången Media (Vi rekommenderar detta är den huvudsakliga videofil som kodaren blir ombedd att bearbeta).
* Det är nödvändigt att skicka konfigurationsdata som innehåller den **setRuntimeProperties** och/eller **transcodeSource** element till processorn.
  * **setRuntimeProperties** används för att åsidosätta Filnamnsegenskapen eller en annan egenskap i komponenter i arbetsflödet.
  * **transcodeSource** används för att ange Clip listan XML-innehåll.

Anslutningar i arbetsflödet:

* Om du använder en eller flera Media filen indata komponenter och planerar att använda **setRuntimeProperties** om du vill ange filnamnet sedan Anslut inte primärfilen komponenten PIN-koden till dem. Kontrollera att det finns ingen anslutning mellan primärfilen objektet och Media filen Input(s).
* Om du föredrar att använda Clip listan XML och en mediekälla komponent kan sedan du ansluta båda tillsammans.

![Ingen anslutning från primära källfil till Media filen indata](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Det finns ingen anslutning från den primära filen till Media filen indata komponenterna om du använder setRuntimeProperties filename-egenskapen.*

![Anslutningen från Clip listan XML för att klippa datakälla](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Du kan ansluta Clip listan XML till mediekälla och använda transcodeSource.*

### <a name="clip-list-xml-customization"></a>Beskär listan XML-anpassning
Du kan ange Clip listan XML i arbetsflödet vid körning med hjälp av **transcodeSource** string XML i konfigurationen. Detta kräver Clip listan XML PIN-koden måste vara ansluten till komponenten mediekälla i arbetsflödet.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <transcodeSource>
      <clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>video-part1.mp4</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
      </clipList>
    </transcodeSource>
    <setRuntimeProperties>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

Om du vill ange /primarySourceFile om du vill använda den här egenskapen för att namnge utdatafilerna med hjälp av 'Uttryck', så vi rekommenderar att du skickar Clip listan XML-filen som en egenskap *när* egenskapen /primarySourceFile för att undvika att listan Clip åsidosättas av inställningen /primarySourceFile.

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="c:\temp\start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <mediaFile>
              <file>c:\temp\start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

Med ytterligare ram exakt hur:

```xml
<?xml version="1.0" encoding="utf-8"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="/primarySourceFile" value="start.mxf" />
      <property propertyPath="/inactiveTimeout" value="65" />
      <property propertyPath="clipListXml" value="xxx">
      <extendedValue><![CDATA[<clipList>
        <clip>
          <videoSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </videoSource>
          <audioSource>
            <trim>
              <inPoint fps="25">00:00:05:24</inPoint>
              <outPoint fps="25">00:00:10:24</outPoint>
            </trim>
            <mediaFile>
              <file>start.mxf</file>
            </mediaFile>
          </audioSource>
        </clip>
        <primaryClipIndex>0</primaryClipIndex>
        </clipList>]]>
      </extendedValue>
      </property>
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>Exempel 1: Överlagra en bild på videon

### <a name="presentation"></a>Presentation
Överväg ett exempel där du vill överlagra en logotyp på indata video medan kodas video. I det här exemplet inkommande videon heter ”Microsoft_HoloLens_Possibilities_816p24.mp4” och logotypen heter ”logo.png”. Du bör utföra följande steg:

* Skapa ett arbetsflöde tillgång med arbetsflödesfilen (se följande exempel).
* Skapa en tillgång med Media, som innehåller två filer: MyInputVideo.mp4 som den primära filen och MyLogo.png.
* Skicka en aktivitet till Media Encoder Premium arbetsflödet media-processor med ovan inkommande tillgångar och ange följande konfigurationssträng.

Konfiguration:

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

I exemplet ovan skickas namnet på filen video till komponenten Media filen indata och egenskapen primarySourceFile. Namnet på logotypfilen skickas till en annan filindata för Media som är ansluten till komponenten grafiska överlägget.

> [!NOTE]
> Video filnamnet skickas till egenskapen primarySourceFile. Anledningen är att använda den här egenskapen i arbetsflödet för att skapa rätt utdata filnamnet med uttryck, till exempel.

### <a name="step-by-step-workflow-creation"></a>Steg för steg-arbetsflödet skapats
Här följer stegen för att skapa ett arbetsflöde som använder två filer som indata: en video och en bild. Det kommer täcker bilden ovanpå videon.

Öppna **Arbetsflödesdesignern** och välj **filen** > **ny arbetsyta** > **Omkoda modell**.

Det nya arbetsflödet visar tre element:

* Primär källfilen
* Beskär List-XML
* Filen/Utdatatillgången  

![Arbetsflöde för ny kodning](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Arbetsflöde för ny kodning*

Börja med att lägga till en Media filen inkommande komponent för att acceptera inkommande mediefilen. Om du vill lägga till en komponent i arbetsflödet, leta efter den i sökrutan databasen och dra den aktuella posten till fönstret designer.

Lägg sedan till videofil som ska användas för att utforma ditt arbetsflöde. Klicka på fönstret bakgrunden i Arbetsflödesdesignern och leta efter egenskapen primära källfilen i rutan högra egenskapen gör du genom. Klicka på mappikonen och välj lämplig videofil.

![Primär källa](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Primär källa*

Ange därefter video-filen i komponenten Media filen indata.   

![Indatakällan för mediefiler](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Indatakällan för mediefiler*

När det är klart komponenten Media filen indata granska filen och fylla i dess utdata PIN-koder för att återspegla den fil som den kontrolleras.

Nästa steg är att lägga till en ”Video Data typen Updater” om du vill ange färg utrymmet ska Rec.709. Lägg till en ”Video konverteraren” som har angetts för Layout/layouten typ = konfigurerbara Planar. Video-ström konverteras till ett format som kan räknas som en källa för komponenten överlägget.

![video Data typen Updater och konverteraren](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Video Data typen Updater och konverteraren*

![Typ av layout = konfigurerbara Planar](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Typ av layout är konfigurerbar Planar*

Sedan lägger till en Video överlägg komponent och ansluta (okomprimerad) video PIN-koden till (okomprimerad) video PIN-kod media filen indata.

Lägga till en annan indata på grund av Media-fil (för att läsa in logotypfilen), klicka på den här komponenten och byta namn på den till ”Media filen indata logotypen” och markera en bild (en PNG-fil till exempel) i egenskapen. Ansluta okomprimerade bilden PIN-koden till överlägget okomprimerade image-PIN-kod.

![Källa för överlägget komponenten och avbildning](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Källa för överlägget komponenten och avbildning*

Om du vill ändra logotypen på videon position (till exempel du kanske vill placera 10 procent från det övre vänstra hörnet i videon), avmarkera kryssrutan ”manuell inmatning”. Du kan göra detta eftersom du använder en inmatning för Media-filen för att tillhandahålla logotypfilen till överlägget komponenten.

![Överlägget position](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Överlägget position*

Om du vill koda video-ström till H.264 att lägga till komponenter för AVC-videokodare och AAC kodare till designytan. Ansluta de PIN-koderna.
Ställ in AAC kodaren och välj ljud Format konvertering/förinställda: 2.0 (L, R).

![Ljud och Video kodare](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Ljud och Video kodare*

Lägg nu till den **ISO Mpeg-4 multiplexor** och **utdata** komponenter och ansluta de PIN-koderna som visas.

![MP4 multiplexor och utdata](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4 multiplexor och utdata*

Du måste ange namnet på utdatafilen. Klicka på den **utdata** komponent och redigera uttrycket för filen:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Filnamnet för utdata](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Filnamnet för utdata*

Du kan köra arbetsflödet lokalt för att kontrollera att den fungerar korrekt.

När den är klar kan du köra det i Azure Media Services.

Förbereda en tillgång i Azure Media Services med två filer: filen video och logotypen. Du kan göra detta med hjälp av .NET eller REST API. Du kan också göra detta med hjälp av Azure portal eller [Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

Den här kursen visar hur du hanterar tillgångar med AMSE. Det finns två sätt att lägga till filer till en tillgång:

* Skapa en lokal mapp, kopiera två filer, och dra och släppa mappen till den **tillgången** fliken.
* Överför en videofil som en tillgång, visa tillgångsinformationen, gå till fliken filer och ladda upp en fil (logotypen).

> [!NOTE]
> Se till att ange en primär fil i tillgången (video huvudfil).

![Tillgångsfiler i AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Tillgångsfiler i AMSE*

Välj tillgången och välj att koda med Premium-kodare. Överför arbetsflödet och markera den.

Klicka på knappen för att skicka data till processorn och Lägg till följande XML-filen för att ange egenskaper för körning:

![Premium-kodare i AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Premium-kodare i AMSE*

Klistra sedan in följande XML-data. Du måste ange namnet på filen video för både Media filen indata och primarySourceFile. Ange namnet på filnamnet för logotypen för.

```xml
<?xml version="1.0" encoding="utf-16"?>
  <transcodeRequest>
    <setRuntimeProperties>
      <property propertyPath="Media File Input/filename" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="/primarySourceFile" value="Microsoft_HoloLens_Possibilities_816p24.mp4" />
      <property propertyPath="Media File Input Logo/filename" value="logo.png" />
    </setRuntimeProperties>
  </transcodeRequest>
```

![setRuntimeProperties](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture20_amsexmldata.png)

*setRuntimeProperties*

Om du använder .NET SDK för att skapa och köra uppgiften, måste den här XML-data skickas som konfigurationssträngen.

```c#
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

När jobbet har slutförts visas MP4-fil i utdatatillgången överlägget!

![Överlägg på videon](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Överlägg på videon*

Du kan hämta Exempelarbetsflöde från [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).

## <a name="example-2--multiple-audio-language-encoding"></a>Exempel 2: Flera ljud språkkod

Ett exempel på flera språk för ljud kodning workfkow finns i [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding).

Den här mappen innehåller ett Exempelarbetsflöde som kan användas för att koda en MXF-fil till en tillgång multi MP4-filer med flera ljud spår.

Det här arbetsflödet förutsätter att MXF-filen innehåller ett ljud spår; ytterligare ljud spårar ska skickas som separata ljudfiler (WAV eller MP4...).

Om du vill koda, följer du dessa steg:

* Skapa en tillgång med Media Services med filen MXF och ljudfiler (0-18 ljudfiler).
* Kontrollera att filen MXF har angetts som en primär fil.
* Skapa ett jobb och en aktivitet med Premium-kodare arbetsflödet processor. Använda arbetsflödet som anges (MultiMP4-1080p-19audio-v1.workflow).
* Skickar setruntime.xml data till aktiviteten (om du använder Azure Media Services Explorer använda knappen ”Skicka XML-data i arbetsflödet”).
  * Uppdatera XML-data för att specificera taggar för rätt fil namn och språk.
  * Arbetsflödet har ljud komponenter med namnet ljud 1-18 ljud.
  * RFC5646 stöds för taggen språk.

```xml
<?xml version="1.0" encoding="utf-16"?>
<transcodeRequest>
  <setRuntimeProperties>
    <property propertyPath="Media File Input Video/filename" value="MainVideo.mxf" />
    <property propertyPath="Language/language_code" value="en" />
    <property propertyPath="/primarySourceFile" value="MainVideo.mxf" />
    <property propertyPath="Audio 1/Media File Input/filename" value="french-audio.wav" />
    <property propertyPath="Audio 1/Language/language_code" value="fr" />
    <property propertyPath="Audio 2/Media File Input/filename" value="german-audio.wav" />
    <property propertyPath="Audio 2/Language/language_code" value="de" />
    <property propertyPath="Audio 3/Media File Input/filename" value="japanese-audio.wav" />
    <property propertyPath="Audio 3/Language/language_code" value="ja" />
  </setRuntimeProperties>
</transcodeRequest>
```

* Den kodade tillgången innehåller flera språk ljud spårar och spåren ska väljas i Azure Media Player.

## <a name="see-also"></a>Se även
* [Introduktion till Premium-kodning i Azure Media Services](http://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [Hur du använder Premium kodning i Azure Media Services](http://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [Koda innehåll på begäran med Azure Media Services](media-services-encode-asset.md#media-encoder-premium-workflow)
* [Arbetsflöde för Media Encoder Premium format och codec](media-services-premium-workflow-encoder-formats.md)
* [Exempelfiler för arbetsflöde](https://github.com/AzureMediaServicesSamples/Encoding-Presets/tree/master/VoD/MediaEncoderPremiumWorkfows)
* [Azure Media Services Explorer-verktyget](http://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
