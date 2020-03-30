---
title: Flera indatafiler och komponentegenskaper med Premium Encoder - Azure | Microsoft-dokument
description: I det här avsnittet beskrivs hur du använder setRuntimeProperties för att använda flera inmatningsfiler och skicka anpassade data till mediakodaren Premium Workflow mediaprocessor.
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
ms.date: 03/18/2019
ms.author: xpouyat
ms.reviewer: anilmur;juliako
ms.openlocfilehash: 27bdf82d4515678e28eadf07fe325860fe5df063
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251003"
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Använda flera indatafiler och komponentegenskaper med Premium-kodare
## <a name="overview"></a>Översikt
Det finns scenarier där du kan behöva anpassa komponentegenskaper, ange XML-innehåll i klipplistan eller skicka flera indatafiler när du skickar en uppgift till **medieprocessorn För Mediekodare Premium.** Några exempel är:

* Överlagra text på video och ange textvärdet (till exempel det aktuella datumet) vid körning för varje indatavideo.
* Anpassa XML-koden för klipplistan (om du vill ange en eller flera källfiler, med eller utan trimning osv.).
* Överlagra en logotypbild på indatavideon medan videon är kodad.
* Flera ljudspråkkodning.

Om du vill att **Media Encoder Premium Workflow** ska veta att du ändrar vissa egenskaper i arbetsflödet när du skapar uppgiften eller skickar flera indatafiler måste du använda en konfigurationssträng som innehåller **setRuntimeProperties** och/eller **transcodeSource**. I det här avsnittet beskrivs hur du använder dem.

## <a name="configuration-string-syntax"></a>Syntax för konfigurationssträng
Den konfigurationssträng som ska anges i kodningsuppgiften använder ett XML-dokument som ser ut så här:

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

Följande är C#-koden som läser XML-konfigurationen från en fil, uppdaterar den med rätt videofilnamn och skickar den till uppgiften i ett jobb:

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

## <a name="customizing-component-properties"></a>Anpassa komponentegenskaper
### <a name="property-with-a-simple-value"></a>Egenskap med ett enkelt värde
I vissa fall är det användbart att anpassa en komponentegenskap tillsammans med arbetsflödesfilen som ska köras av Media Encoder Premium Workflow.

Anta att du har utformat ett arbetsflöde som överlagrar text på dina videor, och texten (till exempel det aktuella datumet) ska anges vid körning. Du kan göra detta genom att skicka texten som ska anges som nytt värde för textegenskapen för överlagrar komponenten från kodningsuppgiften. Du kan använda den här mekanismen för att ändra andra egenskaper för en komponent i arbetsflödet (t.ex. placeringen eller färgen på överlägget, bithastigheten för AVC-kodaren osv.).

**setRuntimeProperties** används för att åsidosätta en egenskap i komponenterna i arbetsflödet.

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
Om du vill ange en egenskap som förväntar `<![CDATA[ and ]]>`sig ett XML-värde kapslar du in med hjälp av .

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
> Se till att inte sätta `<![CDATA[`en vagn retur strax efter .

### <a name="propertypath-value"></a>egenskapPath-värde
I föregående exempel var egenskappath "/Media File Input/filename" eller "/inactiveTimeout" eller "clipListXml".
Detta är i allmänhet namnet på komponenten, sedan namnet på egenskapen. Sökvägen kan ha fler eller färre nivåer, till exempel "/primarySourceFile" (eftersom egenskapen är roten till arbetsflödet) eller "/Videobearbetning/Grafisk överlagring/opacitet" (eftersom överlägget finns i en grupp).    

Om du vill kontrollera sökvägen och egenskapsnamnet använder du åtgärdsknappen som ligger omedelbart bredvid varje egenskap. Du kan klicka på den här åtgärdsknappen och välja **Redigera**. Detta kommer att visa dig det faktiska namnet på egenskapen, och omedelbart ovanför den, namnområdet.

![Åtgärd/redigera](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Egenskap](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Flera inmatningsfiler
Varje uppgift som du skickar till **Media Encoder Premium Workflow** kräver två resurser:

* Den första är en *arbetsflödestillgång* som innehåller en arbetsflödesfil. Du kan utforma arbetsflödesfiler med hjälp av [Arbetsflödesdesignern](media-services-workflow-designer.md).
* Den andra är en *medietillgång* som innehåller de mediefiler som du vill koda.

När du skickar flera mediefiler till **Media Encoder Premium Workflow-kodaren** gäller följande begränsningar:

* Alla mediefiler måste finnas i samma *medietillgång*. Det går inte att använda flera medietillgångar.
* Du måste ange den primära filen i den här medietillgången (helst är detta den viktigaste videofilen som kodaren uppmanas att bearbeta).
* Det är nödvändigt att skicka konfigurationsdata som innehåller **elementet setRuntimeProperties** och/eller **transcodeSource** till processorn.
  * **setRuntimeProperties** används för att åsidosätta filnamnsegenskapen eller en annan egenskap i komponenterna i arbetsflödet.
  * **omkodkälla** används för att ange XML-innehåll i klipplistan.

Anslutningar i arbetsflödet:

* Om du använder en eller flera mediafilinmatningskomponenter och planerar att använda **setRuntimeProperties** för att ange filnamnet ska du inte ansluta den primära filkomponentnålen till dem. Kontrollera att det inte finns någon koppling mellan det primära filobjektet och mediafilindata.
* Om du föredrar att använda XML för klipplista och en komponent i mediekällan kan du ansluta båda tillsammans.

![Ingen anslutning från primär källfil till mediefilinmatning](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Det finns ingen anslutning från den primära filen till Media File Input-komponenter om du använder setRuntimeProperties för att ange egenskapen filename.*

![XML-anslutning från klipplista till klipplistkälla](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Du kan ansluta CLIP List XML till mediekälla och använda transcodeSource.*

### <a name="clip-list-xml-customization"></a>XML-anpassning av Klipplista
Du kan ange XML-koden för klipplistan i arbetsflödet vid körning med hjälp av **transcodeSource** i konfigurationssträngen XML. Detta kräver att XML-stiftet för klipplistan ansluts till komponenten Mediekälla i arbetsflödet.

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

Om du vill ange /primarySourceFile för att använda den här egenskapen för att namnge utdatafilerna med hjälp av Uttryck rekommenderar vi att du skickar XML-koden för klipplistan som en egenskap *efter* egenskapen /primarySourceFile, för att undvika att klipplistan åsidosätts av inställningen /primarySourceFile.

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

Med ytterligare ram-exakt trimning:

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

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>Exempel 1: Överlagra en bild ovanpå videon

### <a name="presentation"></a>Presentation
Tänk dig ett exempel där du vill lägga över en logotypbild på indatavideon medan videon är kodad. I det här exemplet heter indatavideon "Microsoft_HoloLens_Possibilities_816p24.mp4" och logotypen heter "logo.png". Du bör utföra följande steg:

* Skapa en arbetsflödestillgång med arbetsflödesfilen (se följande exempel).
* Skapa en medietillgång, som innehåller två filer: MyInputVideo.mp4 som primär fil och MyLogo.png.
* Skicka en uppgift till medieprocessorn Media Encoder Premium Arbetsflöde med ovanstående indataresurser och ange följande konfigurationssträng.

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

I exemplet ovan skickas namnet på videofilen till komponenten Media File Input och egenskapen primarySourceFile. Namnet på logotypfilen skickas till en annan Media File Input som är ansluten till den grafiska övertäckningskomponenten.

> [!NOTE]
> Videofilnamnet skickas till egenskapen primarySourceFile. Anledningen till detta är att använda den här egenskapen i arbetsflödet för att skapa rätt utdatafilnamn med hjälp av uttryck, till exempel.

### <a name="step-by-step-workflow-creation"></a>Skapa arbetsflöde steg för steg
Här är stegen för att skapa ett arbetsflöde som tar två filer som indata: en video och en bild. Det kommer att överlagra bilden ovanpå videon.

Öppna **Arbetsflödesdesignern** och välj **Fil** > **ny arbetsyta** > **Omkoda Skiss**.

Det nya arbetsflödet visar tre element:

* Primär källfil
* XML för klipplista
* Utdatafil/tillgång  

![Nytt kodningsarbetsflöde](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Nytt kodningsarbetsflöde*

Om du vill acceptera indatamediefilen börjar du med att lägga till en komponent för mediefilsinmatning. Om du vill lägga till en komponent i arbetsflödet letar du efter den i sökrutan Databas och drar önskad post till designerfönstret.

Lägg sedan till den videofil som ska användas för att utforma arbetsflödet. Det gör du genom att klicka på bakgrundsfönstret i Arbetsflödesdesignern och leta efter egenskapen Primär källfil i egenskapsfönstret till höger. Klicka på mappikonen och välj lämplig videofil.

![Primär filkälla](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Primär filkälla*

Ange sedan videofilen i komponenten Mediefilinmatning.   

![Indatakälla för media](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Indatakälla för media*

Så snart detta är gjort kommer komponenten Media File Input att inspektera filen och fylla i dess utdatanålar för att återspegla filen som den inspekterade.

Nästa steg är att lägga till en "Video Data Type Updater" för att ange färgrymden till Rec.709. Lägg till en "Videoformat converter" som är inställd på Data layout / layout typ = Konfigurerbara Planar. Detta konverterar videoströmmen till ett format som kan tas som en källa till överlagringskomponenten.

![Videodatatyp Updater och Formatera konverterare](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Uppdateringsapparat för videodatatyp och formatkonverterare*

![Layouttyp = Konfigurerbar plana](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Layouttypen är konfigurerbar Planar*

Lägg sedan till en videoöverläggskomponent och ansluter (okomprimerad) videonål till videostiftet (okomprimerad) för mediefilinmatningen.

Lägg till en annan Media File Input (för att läsa in logotypen), klicka på den här komponenten och byt namn på den till "Media File Input Logo", och välj en bild (en PNG-fil till exempel) i filegenskapen. Anslut den okomprimerade bildnålen till den okomprimerade bildstiftet på överlägget.

![Överlagd komponent- och bildfilskälla](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Överlagd komponent- och bildfilskälla*

Om du vill ändra logotypens position på videon (du kanske till exempel vill placera den på 10 procent av videons övre vänstra hörn) avmarkerar du kryssrutan "Manuell inmatning". Du kan göra detta eftersom du använder en mediafilsinmatning för att ange logotypfilen till överlagringskomponenten.

![Överläggsposition](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Överläggsposition*

Om du vill koda videoströmmen till H.264 lägger du till avc-videokodaren och AAC-kodaren på designerytan. Anslut stiften.
Konfigurera AAC-kodaren och välj Konvertering/förinställning för ljudformat: 2.0 (L, R).

![Ljud- och videokodare](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Ljud- och videokodare*

Lägg nu till **ISO Mpeg-4 Multiplexer** och **File Output** komponenter och ansluta stiften som visas.

![MP4 multiplexer och filutdata](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4 multiplexer och filutdata*

Du måste ange namnet på utdatafilen. Klicka på komponenten **Filutdata** och redigera uttrycket för filen:

    ${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4

![Namn på filutdata](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Namn på filutdata*

Du kan köra arbetsflödet lokalt för att kontrollera att det körs korrekt.

När den är klar kan du köra den i Azure Media Services.

Förbered först en tillgång i Azure Media Services med två filer i den: videofilen och logotypen. Du kan göra detta med hjälp av .NET eller REST API. Du kan också göra detta med hjälp av Azure-portalen eller [Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

Den här självstudien visar hur du hanterar tillgångar med AMSE. Det finns två sätt att lägga till filer i en tillgång:

* Skapa en lokal mapp, kopiera de två filerna i den och dra och släpp mappen till fliken **Tillgång.**
* Ladda upp videofilen som en tillgång, visa tillgångsinformationen, gå till fliken filer och ladda upp ytterligare en fil (logotyp).

> [!NOTE]
> Se till att ange en primär fil i tillgången (huvudvideofilen).

![Tillgångsfiler i AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Tillgångsfiler i AMSE*

Välj tillgången och välj att koda den med Premium Encoder. Ladda upp arbetsflödet och välj det.

Klicka på knappen om du vill skicka data till processorn och lägg till följande XML för att ange egenskaper för körning:

![Premium-kodare i AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Premium-kodare i AMSE*

Klistra sedan in följande XML-data. Du måste ange namnet på videofilen för både Mediefilsinmatningen och primärKällaFile. Ange även namnet på logotypens namn.

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

Om du använder .NET SDK för att skapa och köra uppgiften måste dessa XML-data skickas som konfigurationssträng.

```csharp
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

När jobbet är klart visar MP4-filen i utdatatillgången överlägget!

![Överlägg på videon](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Överlägg på videon*

Du kan hämta exempelarbetsflödet från [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).

## <a name="example-2--multiple-audio-language-encoding"></a>Exempel 2: Flera ljudspråkkodning

Ett exempel på flera arbetsflöde för kodning av ljudspråk är tillgängligt i [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding).

Den här mappen innehåller ett exempelarbetsflöde som kan användas för att koda en MXF-fil till en multi MP4-filtillgång med flera ljudspår.

Det här arbetsflödet förutsätter att MXF-filen innehåller ett ljudspår. ytterligare ljudspår bör skickas som separata ljudfiler (WAV eller MP4...).

Så här kodar du:

* Skapa en Media Services-tillgång med MXF-filen och ljudfilerna (0 till 18 ljudfiler).
* Kontrollera att MXF-filen är inställd som en primär fil.
* Skapa ett jobb och en uppgift med premiumarbetsflödeskodarprocessorn. Använd arbetsflödet (MultiMP4-1080p-19audio-v1.workflow).
* Skicka setruntime.xml-data till uppgiften (om du använder Azure Media Services Explorer använder du knappen "Skicka xml-data till arbetsflödet").)
  * Uppdatera XML-data för att ange rätt filnamn och språktaggar.
  * Arbetsflödet har ljudkomponenter som heter Ljud 1 till Ljud 18.
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

* Den kodade tillgången innehåller ljudspår på flera språk och dessa spår bör kunna väljas i Azure Media Player.

## <a name="see-also"></a>Se även
* [Introduktion till Premium-kodning i Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [Så här använder du Premium-kodning i Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [Koda innehåll på begäran med Azure Media Services](media-services-encode-asset.md#media-encoder-premium-workflow)
* [Format och codec-kodc för Premium-arbetsflöde för Media Encoder](media-services-premium-workflow-encoder-formats.md)
* [Exempel på arbetsflödesfiler](https://github.com/Azure/azure-media-services-samples)
* [Azure Media Services Explorer-verktyg](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
