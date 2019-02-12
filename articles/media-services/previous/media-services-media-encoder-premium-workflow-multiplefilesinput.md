---
title: Flera indatafiler och egenskaperna för komponenten med Premium-kodare – Azure | Microsoft Docs
description: Det här avsnittet beskriver hur du använder setRuntimeProperties att använda flera inmatningsfiler och skicka anpassade data till mediebearbetare Media Encoder Premium Workflow.
services: media-services
documentationcenter: ''
author: xpouyat
manager: femila
editor: ''
ms.assetid: 7fb35bdd-9891-4401-a65b-ef3cc8190e8a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: xpouyat;anilmur;juliako
ms.openlocfilehash: 267de581c04d67738399cd683f57dabda472c0f8
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "55994259"
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Med flera indatafiler och egenskaperna för komponenten med Premium-kodare
## <a name="overview"></a>Översikt
Det finns scenarier där du kan behöva anpassa komponentegenskaperna för anger Clip lista XML-innehåll, eller skicka flera indatafiler när du skickar in en aktivitet med den **Media Encoder Premium Workflow** medieprocessor. Några exempel är:

* Överlägga text på video och ställer in textvärdet (till exempel det aktuella datumet) vid körning för varje indatavideon.
* Anpassa Clip lista XML (om du vill ange en eller flera källfiler, med eller utan att ta bort osv.).
* Överlägga en logotyp på indatavideo även om videon kodas.
* Flera språk-kodning.

Så att den **Media Encoder Premium Workflow** vet att du ändrar vissa egenskaper i arbetsflödet när du skapar uppgiften eller skicka flera indatafiler, du måste använda en configuration-sträng som innehåller  **setRuntimeProperties** och/eller **transcodeSource**. Det här avsnittet förklarar hur de används.

## <a name="configuration-string-syntax"></a>Strängsyntaxen för konfiguration
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

Följande är de C# kod som läser XML-konfigurationen från en fil, uppdatera det med filnamnet direkt video och skickar det till aktivitet i ett jobb:

```csharp
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
### <a name="property-with-a-simple-value"></a>Egenskap med ett enkelt värde
I vissa fall är det användbart att anpassa en komponent egenskap tillsammans med arbetsflödesfilen som ska utföras av Media Encoder Premium Workflow.

Anta att du skapat ett arbetsflöde överlägg texten på dina videor och texten (till exempel det aktuella datumet) ska anges vid körning. Du kan göra detta genom att skicka texten som ska ställas in som det nya värdet för egenskapen text för komponenten överlägget från aktiviteten kodning. Du kan använda den här mekanismen för att ändra andra egenskaper för en komponent i arbetsflödet (till exempel positionen eller färgen på överlägget, bithastigheten encoder AVC osv.).

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

### <a name="property-with-an-xml-value"></a>Egenskap med ett XML-värde
Om du vill ange en egenskap som förväntar sig en XML-värdet som kapslar in med hjälp av `<![CDATA[ and ]]>`.

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
> Se till att du inte placera en vagnretur returnerade precis efter `<![CDATA[`.

### <a name="propertypath-value"></a>propertyPath-värde
I föregående exempel kan propertyPath var ”/ mediefil indata/filename” eller ”/ inactiveTimeout” eller ”clipListXml”.
Det här är i allmänhet är namnet på komponenten och sedan egenskapens namn. Sökvägen kan ha fler eller färre nivåer, som ”/ primarySourceFile” (eftersom egenskapen är i roten för arbetsflödet) eller ”/ Video bearbetning/bild överlägget/opacitet” (eftersom överlägget är i en grupp).    

Använd knappen åtgärd som är direkt bredvid varje egenskap för att kontrollera sökvägen och egenskapen. Du kan klicka på den här åtgärden och välja **redigera**. Detta visar namnet på egenskapen och direkt ovanför det namnområdet.

![Åtgärd/redigera](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Egenskap ](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Flera indatafiler
Varje aktivitet som du skickar in till den **Media Encoder Premium Workflow** kräver två tillgångar:

* Den första är en *arbetsflöde tillgången* som innehåller en arbetsflödesfil. Du kan utforma Arbetsflödesfiler med hjälp av den [Arbetsflödesdesigner](media-services-workflow-designer.md).
* Den andra är en *Medietillgången* som innehåller filen media som du vill koda.

När du skickar flera mediefiler i den **Media Encoder Premium Workflow** encoder, följande begränsningar gäller:

* Alla mediefiler måste vara i samma *Medietillgången*. Användning av flera Medietillgångar stöds inte.
* Du måste ange den primära filen i den här Medietillgången (Vi rekommenderar det här är huvudsakliga videofilen som kodaren uppmanas sedan att bearbeta).
* Det är nödvändigt att skicka konfigurationsdata som innehåller den **setRuntimeProperties** och/eller **transcodeSource** element till processorn.
  * **setRuntimeProperties** används för att åsidosätta Filnamnsegenskapen eller en annan egenskap i komponenter i arbetsflödet.
  * **transcodeSource** används för att ange Clip lista XML-innehåll.

Anslutningar i arbetsflödet:

* Om du använder en eller flera komponenter i Media-fil som indata och planerar att använda **setRuntimeProperties** för att ange namnet på filen, sedan Anslut inte primärfilen komponenten PIN-koden till dem. Se till att det finns ingen koppling mellan det primära filen-objektet och Media filen indata.
* Om du föredrar att använda Clip lista XML och en mediekälla komponent, kan så du ansluta båda tillsammans.

![Ingen anslutning från primära källfilen till Media-fil som indata](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Det finns ingen anslutning från den primära filen till Media-fil som indata komponenterna om du använder setRuntimeProperties för att ange Filnamnsegenskapen.*

![Anslutningen från Clip lista XML till Clip Listkälla](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Du kan ansluta Clip lista XML till mediekälla och använder transcodeSource.*

### <a name="clip-list-xml-customization"></a>Clip lista XML-anpassning
Du kan ange Clip lista XML i arbetsflödet vid körning med hjälp av **transcodeSource** sträng XML i konfigurationen. Detta kräver att PIN-koden för Clip lista XML som ska anslutas till komponenten mediekälla i arbetsflödet.

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

Om du vill ange /primarySourceFile om du vill använda den här egenskapen för att namnge utdatafilerna med hjälp av ”uttryck, så vi rekommenderar att skicka Clip lista XML-filen som en egenskap *när* egenskapen /primarySourceFile slipper klippet Lista åsidosättas av inställningen /primarySourceFile.

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

Med ytterligare RAM exakta optimering:

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

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>Exempel 1: Överlappar en bild på videon

### <a name="presentation"></a>Presentation
Tar ett exempel där du vill täcka över en logotyp på indatavideo även om videon kodas. I det här exemplet indatavideon har namnet ”Microsoft_HoloLens_Possibilities_816p24.mp4” och logotypen som heter ”logo.png”. Du bör utföra följande steg:

* Skapa en tillgång i arbetsflödet med arbetsflödesfilen (se exemplet nedan).
* Skapa en tillgång med Media, som innehåller två filer: MyInputVideo.mp4 som den primära filen och MyLogo.png.
* Skicka en uppgift till mediebearbetare Media Encoder Premium Workflow med ovanstående inkommande tillgångar och ange följande konfigurationssträngen.

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

I exemplet ovan är skickas namnet på videofilen till komponenten Media-fil som indata och egenskapen primarySourceFile. Namnet på logotypfilen skickas till en annan Media-fil som indata som är ansluten till komponenten grafisk överlägg.

> [!NOTE]
> Namnet på video skickas till egenskapen primarySourceFile. Anledningen är att använda den här egenskapen i arbetsflödet för att skapa rätt utdatafilnamnet med uttryck, till exempel.

### <a name="step-by-step-workflow-creation"></a>Stegvisa arbetsflödet skapats
Här följer stegen för att skapa ett arbetsflöde som använder två filer som indata: en video och en bild. Det kommer överlappar bilden på videon.

Öppna **Arbetsflödesdesigner** och välj **filen** > **ny arbetsyta** > **Omkoda skissen**.

Det nya arbetsflödet visar tre element:

* Primär källfilen
* Clip List-XML
* Filen/Utdatatillgången  

![Ny Kodningsarbetsflöden](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Ny Kodningsarbetsflöden*

Börja med att lägga till en Media filen inkommande komponent för att kunna acceptera inkommande mediefilen. Om du vill lägga till en komponent i arbetsflödet, söker efter den i sökrutan lagringsplats och dra den önskade posten till fönstret designer.

Lägg sedan till videofilen som ska användas för att utforma ditt arbetsflöde. Om du vill göra det klickar du på fönstret bakgrund i Arbetsflödesdesignern och leta efter egenskapen primära källfilen i fönstret till höger egenskapen. Klicka på mappikonen och välj lämplig videofilen.

![Primär filkälla](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Primär filkälla*

Ange därefter videofilen i komponenten Media-fil som indata.   

![Mediefil indatakälla](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Mediefil indatakälla*

När det är klart, granska filen komponenten Media-fil som indata och fylla i dess utdata PIN-koder för att återspegla den fil som det kontrolleras.

Nästa steg är att lägga till en ”Video Data typ Updater” om du vill ange färg utrymme till Rec.709. Lägg till en ”Video Format konverterare” som har angetts till Layout/datalayouten typ = konfigurerbara plan. Videoströmmen konverteras till ett format som kan vidtas som en källa för komponenten överlägg.

![video Data typ Updater- och Format konverterare](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*video Data typ Updater- och Format konverterare*

![Layouttyp = konfigurerbara plan](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Layouten är konfigurerbara plan*

Sedan lägger till en Video Företagsdataskydd komponent och ansluta (okomprimerad) video PIN-koden till (okomprimerad) video PIN-koden för den media-fil som indata.

Lägg till en annan Media-fil som indata (för att läsa in logotypfilen), klickar du på den här komponenten och Byt namn på den till ”Media filen indata logotyp” och välj en avbildning (en .png-fil till exempel) i filegenskapen. Anslut okomprimerade bild PIN-koden till överlägget okomprimerade bild-PIN-kod.

![Överlägget komponent- och bildfiler filkälla](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Överlägget komponent- och bildfiler filkälla*

Om du vill ändra positionen för logotyp på videon (till exempel du kanske vill placera den på 10 procent rabatt på det övre vänstra hörnet av videon), avmarkera kryssrutan ”manuell indata”. Du kan göra detta eftersom du använder en Media fil som indata för att tillhandahålla Logotypfil till överlägget komponenten.

![Överlägget position](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Överlägget position*

Om du vill koda videoströmmen till H.264, lägger du till AVC videokodare och AAC-kodare komponenterna till designytan. Anslut de PIN-koderna.
Konfigurera AAC-kodare och välja ljud Format konvertering/förinställning: 2.0 (L, R).

![Ljud- och kodare](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Ljud- och kodare*

Lägg nu till den **ISO Mpeg-4 multiplexor** och **filen utdata** komponenter och ansluta de PIN-koderna som visas.

![MP4 multiplexor och filen utdata](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4 multiplexor och filen utdata*

Du måste ange namnet på utdatafilen. Klicka på den **filen utdata** komponent och redigera uttryck för filen:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Filnamn för utdata](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Filnamn för utdata*

Du kan köra arbetsflödet lokalt för att kontrollera att den fungerar korrekt.

När den är klar kan du köra den i Azure Media Services.

Börja med att förbereda en tillgång i Azure Media Services med två filer i den: videofilen och -logotypen. Du kan göra detta med hjälp av .NET eller REST API. Du kan också göra detta med hjälp av Azure portal eller [Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

Den här självstudien visar hur du hanterar tillgångar med AMSE. Det finns två sätt att lägga till filer till en tillgång:

* Skapa en lokal mapp, kopiera två filer, och dra och släppa mappen till den **tillgången** fliken.
* Ladda upp videofilen som en tillgång, visa tillgångsinformationen, gå till fliken filer och ladda upp en fil (logotypen).

> [!NOTE]
> Se till att ange en primär fil i tillgången (video huvudfilen).

![Tillgångsfiler i AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Tillgångsfiler i AMSE*

Välj tillgången och välja att koda med Premium-kodare. Ladda upp arbetsflödet och markera den.

Klicka på knappen för att överföra data till processorn och Lägg till följande XML-filen för att ställa in egenskaperna runtime:

![Premium-kodare i AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Premium-kodare i AMSE*

Klistra sedan in följande XML-data. Du måste ange namnet på videofilen för både Media-fil som indata och primarySourceFile. Ange namnet på filnamnet för logotypen för.

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

Om du använder .NET SDK för att skapa och köra aktiviteten, har den här XML-data som ska skickas som konfigurationssträngen.

```csharp
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

När jobbet har slutförts, visar MP4-fil i utdatatillgången överlägget!

![Företagsdataskydd för videon](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Företagsdataskydd för videon*

Du kan hämta Exempelarbetsflöde från [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).

## <a name="example-2--multiple-audio-language-encoding"></a>Exempel 2: Kodning för flera språk

Ett exempel på flera språk kodningsarbetsflöden är tillgänglig i [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding).

Den här mappen innehåller ett Exempelarbetsflöde som kan användas för att koda en MXF-fil till en tillgång med flera MP4-filer med flera ljudspår.

Det här arbetsflödet förutsätter att MXF-filen innehåller ett ljudspår; ytterligare ljudspår ska skickas som separata ljudfiler (WAV eller MP4...).

Om du vill koda, följer du dessa steg:

* Skapa en tillgång med Media Services med MXF-fil- och ljudfiler (0-18 ljudfiler).
* Kontrollera att filen MXF har angetts som en primär fil.
* Skapa ett jobb och en uppgift med processorer i Premium-kodare för arbetsflödet. Använda arbetsflödet för tillhandahålls (MultiMP4-1080p-19audio-v1.workflow).
* Skickar data setruntime.xml till aktiviteten (om du använder Azure Media Services Explorer kan använda knappen ”Skicka xml-data i arbetsflödet”).
  * Uppdatera XML-data för att specificera taggar för rätt fil namnen och språk.
  * Arbetsflödet har ljud komponenter med namnet ljud 1 till ljud 18.
  * RFC5646 stöds för språktaggen.

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

* Den kodade tillgången innehåller ljudspår för flera språk och dessa spårar bör väljas i Azure Media Player.

## <a name="see-also"></a>Se också
* [Vi presenterar Premium Encoding i Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [Hur du använder Premium Encoding i Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [Koda innehåll på begäran med Azure Media Services](media-services-encode-asset.md#media-encoder-premium-workflow)
* [Media Encoder Premium Workflow format och -codec](media-services-premium-workflow-encoder-formats.md)
* [Exempelfilerna för arbetsflöde](https://github.com/Azure/azure-media-services-samples)
* [Azure Media Services Explorer-verktyget](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
