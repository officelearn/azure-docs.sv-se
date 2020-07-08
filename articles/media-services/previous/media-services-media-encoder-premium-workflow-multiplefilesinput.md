---
title: Flera indatafiler och komponent egenskaper med Premium-kodare – Azure | Microsoft Docs
description: I det här avsnittet beskrivs hur du använder setRuntimeProperties för att använda flera indatafiler och skicka anpassade data till Media Encoder Premium Workflow medie processor.
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
ms.openlocfilehash: 4a51a67200ec411f0f9362bc92bf7926f221b1f2
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86056811"
---
# <a name="using-multiple-input-files-and-component-properties-with-premium-encoder"></a>Använda flera indatafiler och komponent egenskaper med Premium-kodare
## <a name="overview"></a>Översikt
Det finns scenarier där du kan behöva anpassa komponent egenskaper, ange Clip List XML-innehåll eller skicka flera indatafiler när du skickar en aktivitet med **Media Encoder Premium Workflow** medie processorn. Några exempel är:

* Överlappa text på video och ange textvärdet (till exempel aktuellt datum) vid körning för varje indata-video.
* Anpassa Clip List-XML (för att ange en eller flera källfiler, med eller utan trimning osv.).
* Lägga till en logo typ bild i indata-videon medan videon kodas.
* Kodning av flera språk.

För att låta **Media Encoder Premium Workflow** veta att du ändrar vissa egenskaper i arbets flödet när du skapar uppgiften eller skickar flera indatafiler, måste du använda en konfigurations sträng som innehåller **setRuntimeProperties** och/eller **transcodeSource**. Det här avsnittet beskriver hur du använder dem.

## <a name="configuration-string-syntax"></a>Syntax för konfigurations sträng
Konfigurations strängen som ska anges i encoding-aktiviteten använder ett XML-dokument som ser ut så här:

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

Följande är C#-koden som läser XML-konfigurationen från en fil, uppdaterar den med rätt video namn och skickar den till uppgiften i ett jobb:

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

## <a name="customizing-component-properties"></a>Anpassa komponent egenskaper
### <a name="property-with-a-simple-value"></a>Egenskap med ett enkelt värde
I vissa fall är det praktiskt att anpassa en komponent egenskap tillsammans med den arbets flödes fil som ska köras av Media Encoder Premium Workflow.

Anta att du har utformat ett arbets flöde som överlappar text på dina videor och att texten (till exempel dagens datum) ska anges vid körning. Du kan göra detta genom att skicka texten som ska anges som det nya värdet för egenskapen text för överlägget-komponenten från encoding-aktiviteten. Du kan använda den här metoden för att ändra andra egenskaper för en komponent i arbets flödet (till exempel placering eller färg för överlägget, bit hastigheten för AVC-kodare osv.).

**setRuntimeProperties** används för att åsidosätta en egenskap i arbets flödets komponenter.

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
Ange en egenskap som förväntar sig ett XML-värde genom att kapsla in med `<![CDATA[ and ]]>` .

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
> Se till att du inte anger någon vagn retur strax efter `<![CDATA[` .

### <a name="propertypath-value"></a>propertyPath-värde
I föregående exempel var propertyPath "/Media File in/filename" eller "/inactiveTimeout" eller "clipListXml".
Detta är i allmänhet namnet på komponenten, sedan namnet på egenskapen. Sökvägen kan ha mer eller färre nivåer, t. ex. "/primarySourceFile" (eftersom egenskapen finns i roten i arbets flödet) eller "/video-bearbetning/bild överlägg/opacitet" (eftersom överlägget är i en grupp).    

Om du vill kontrol lera sökvägen och egenskaps namnet använder du knappen åtgärd som är direkt bredvid varje egenskap. Du kan klicka på den här Åtgärds knappen och välja **Redigera**. Detta visar det faktiska namnet på egenskapen och direkt ovanför den, namn området.

![Åtgärd/Redigera](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture6_actionedit.png)

![Egenskap](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture7_viewproperty.png)

## <a name="multiple-input-files"></a>Flera indatafiler
Varje aktivitet som du skickar till **Media Encoder Premium Workflow** kräver två till gångar:

* Den första är en *arbets flödes till gång* som innehåller en arbets flödes fil. Du kan utforma arbets flödes filer med hjälp av [arbetsflödesdesigner](media-services-workflow-designer.md).
* Den andra är en *medie till gång* som innehåller de mediefiler som du vill koda.

När du skickar flera mediafiler till **Media Encoder Premium Workflow** -kodaren gäller följande begränsningar:

* Alla mediefiler måste finnas i samma *medie till gång*. Det finns inte stöd för att använda flera medie till gångar.
* Du måste ange den primära filen i den här medie till gången (helst är detta den viktigaste video filen som kodaren uppmanas att bearbeta).
* Det är nödvändigt att överföra konfigurations data som innehåller **setRuntimeProperties** -och/eller **transcodeSource** -elementet till processorn.
  * **setRuntimeProperties** används för att åsidosätta filename-egenskapen eller en annan egenskap i arbets flödets komponenter.
  * **transcodeSource** används för att ange klipp LISTANS XML-innehåll.

Anslutningar i arbets flödet:

* Om du använder en eller flera medie fil ingångs komponenter och planerar att använda **setRuntimeProperties** för att ange fil namnet ska du inte ansluta den primära fil komponentens PIN-kod till dem. Kontrol lera att det inte finns någon anslutning mellan det primära filobjektet och medie filens indata.
* Om du föredrar att använda Clip List-XML och en medie käll komponent kan du ansluta båda.

![Ingen anslutning från primär käll fil till medie fil indata](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture0_nopin.png)

*Det finns ingen anslutning från den primära filen till medie filens indata-komponent (er) om du använder setRuntimeProperties för att ställa in filename-egenskapen.*

![Anslutning från Clip List-XML till klipp List källa](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture1_pincliplist.png)

*Du kan ansluta Clip List-XML till medie källa och använda transcodeSource.*

### <a name="clip-list-xml-customization"></a>XML-anpassning av Clip List
Du kan ange Clip List-XML i arbets flödet vid körning med **transcodeSource** i XML-konfigurationsfilen. Detta kräver att klipp listens XML-kod är ansluten till komponenten för medie källan i arbets flödet.

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

Om du vill ange/primarySourceFile för att använda den här egenskapen som namn på utdatafilerna genom att använda uttryck, rekommenderar vi att du skickar Clip List-XML-filen som en egenskap *efter* egenskapen/primarySourceFile för att undvika att klipp listan åsidosätts av inställningen/primarySourceFile.

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

Med ytterligare ram-korrekt trimning:

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

## <a name="example-1--overlay-an-image-on-top-of-the-video"></a>Exempel 1: lägga till en bild ovanpå videon

### <a name="presentation"></a>Presentation
Överväg ett exempel där du vill täcka in en logo typ bild i indata-videon medan videon kodas. I det här exemplet heter ininspelnings videon "Microsoft_HoloLens_Possibilities_816p24.mp4" och logo typen kallas "logo.png". Du bör utföra följande steg:

* Skapa en arbets flödes till gång med arbets flödes filen (se följande exempel).
* Skapa en medie till gång, som innehåller två filer: MyInputVideo.mp4 som primär fil och MyLogo.png.
* Skicka en uppgift till Media Encoder Premium Workflow medie processor med ovanstående ingångs till gångar och ange följande konfigurations sträng.

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

I exemplet ovan skickas namnet på video filen till indatafilen för medie filen och egenskapen primarySourceFile. Namnet på logo typ filen skickas till en annan media-indata som är ansluten till komponenten för grafiskt överlägg.

> [!NOTE]
> Video filens namn skickas till egenskapen primarySourceFile. Orsaken till detta är att använda den här egenskapen i arbets flödet för att skapa rätt namn på utdatafilen med hjälp av uttryck, till exempel.

### <a name="step-by-step-workflow-creation"></a>Steg för steg skapa arbets flöde
Här följer stegen för att skapa ett arbets flöde som tar två filer som indata: en video och en bild. Bilden överläggs ovanpå videon.

Öppna **arbetsflödesdesigner** och välj **Arkiv**  >  **nytt**  >  **Omkoda skiss**.

Det nya arbets flödet visar tre element:

* Primär käll fil
* Clip List-XML
* Utdatafil/till gång  

![Nytt kodnings arbets flöde](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture9_empty.png)

*Nytt kodnings arbets flöde*

Börja med att lägga till en medie fil indataport för att kunna godkänna medie filen för indata. Om du vill lägga till en komponent i arbets flödet tittar du på den i sökrutan för databasen och drar önskad post till fönstret designer.

Lägg sedan till video filen som ska användas för att utforma arbets flödet. Det gör du genom att klicka på bakgrunds fönstret i arbetsflödesdesigner och leta efter egenskapen primär käll fil i den högra egenskaps rutan. Klicka på mappikonen och välj lämplig videofil.

![Primär fil källa](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture10_primaryfile.png)

*Primär fil källa*

Ange sedan video filen i komponenten för medie fil indata.   

![Indatakälla för medie fil](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture11_mediafileinput.png)

*Indatakälla för medie fil*

Så snart detta görs kommer medie filen indatamängd att inspektera filen och fylla i dess utmatnings Tapper för att återspegla den fil som den har inspekterat.

Nästa steg är att lägga till en "video data typs uppdatering" för att ange färg området till Rec. 709. Lägg till en "video format konverterare" som är inställd på data layout/layoutstil = konfigurerbar plan. Detta konverterar video strömmen till ett format som kan tas som källa för överlägget-komponenten.

![video data typs uppdaterings-och format konverterare](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter.png)

*Video data typs uppdaterings-och format konverterare*

![Typ av layout = konfigurerbar plan](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture12_formatconverter2.png)

*Typen av layout är konfigurerbar plan*

Lägg sedan till en video överlägg och Anslut den (okomprimerade) video-PIN-koden till den okomprimerade video-PIN-koden för medie filens indata.

Lägg till en annan media-indata (om du vill läsa in logo filen) klickar du på den här komponenten och byter namn till "medie fil indatafilens logo typ" och väljer en bild (en PNG-fil till exempel) i fil egenskapen. Anslut den okomprimerade avbildningens PIN-kod till den okomprimerade avbildningens PIN-kod för överlägget.

![Överläggning av komponent och avbildnings fil källa](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture13_overlay.png)

*Överläggning av komponent och avbildnings fil källa*

Om du vill ändra logo typens position på videon (till exempel kanske du vill placera den på 10 procent av det övre vänstra hörnet i videon) avmarkerar du kryss rutan "manuella indatatyper". Du kan göra detta eftersom du använder en medie fil indata för att tillhandahålla logo typ filen till överlägget-komponenten.

![Överläggets position](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture14_overlay_position.png)

*Överläggets position*

Om du vill koda video strömmen till H. 264 lägger du till komponenterna AVC video encoder och AAC Encoder till design ytan. Anslut PIN-märkena.
Konfigurera AAC-kodaren och välj ljud formats konvertering/för inställning: 2,0 (L, R).

![Ljud-och video kodare](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture15_encoders.png)

*Ljud-och video kodare*

Lägg nu till **ISO MPEG-4-multiplexorn** och **fil utmatnings** komponenter och Anslut de PIN-koder som visas.

![MP4-multiplexor och filutdata](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture16_mp4output.png)

*MP4-multiplexor och filutdata*

Du måste ange namnet på utdatafilen. Klicka på komponenten för **fil utdata** och redigera uttrycket för filen:

`${ROOT_outputWriteDirectory}\${ROOT_sourceFileBaseName}_withoverlay.mp4`

![Utdatafilens namn](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture17_filenameoutput.png)

*Utdatafilens namn*

Du kan köra arbets flödet lokalt för att kontrol lera att det körs på rätt sätt.

När den är klar kan du köra den i Azure Media Services.

Börja med att förbereda en till gång i Azure Media Services med två filer: video filen och logo typen. Du kan göra detta med hjälp av .NET eller REST API. Du kan också göra detta med hjälp av Azure Portal eller [Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE).

Den här självstudien visar hur du hanterar till gångar med AMSE. Det finns två sätt att lägga till filer till en till gång:

* Skapa en lokal mapp, kopiera de två filerna i den och dra och släpp mappen till fliken **till gångar** .
* Ladda upp video filen som en till gång, Visa till gångs information, gå till fliken filer och överför ytterligare en fil (logo typ).

> [!NOTE]
> Se till att ange en primär fil i till gången (huvud video filen).

![Till gångs filer i AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture18_assetinamse.png)

*Till gångs filer i AMSE*

Välj till gången och välj att koda den med Premium-kodaren. Ladda upp arbets flödet och markera det.

Klicka på knappen för att skicka data till processorn och Lägg till följande XML för att ange körnings egenskaper:

![Premium-kodare i AMSE](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture19_amsepremium.png)

*Premium-kodare i AMSE*

Klistra sedan in följande XML-data. Du måste ange namnet på video filen för både medie filens indata och primarySourceFile. Ange namnet på logo typens fil namn.

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

Om du använder .NET SDK för att skapa och köra uppgiften, måste dessa XML-data skickas som konfigurations strängen.

```csharp
public ITask AddNew(string taskName, IMediaProcessor mediaProcessor, string configuration, TaskOptions options);
```

När jobbet är klart visar MP4-filen i utmatnings till gången överlägget!

![Överlägg på videon](./media/media-services-media-encoder-premium-workflow-multiplefilesinput/capture21_resultoverlay.png)

*Överlägg på videon*

Du kan hämta exempel arbets flödet från [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/).

## <a name="example-2--multiple-audio-language-encoding"></a>Exempel 2: flera språk kodning för ljud

Ett exempel på ett arbets flöde för kodning av flera språk är tillgängligt i [GitHub](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows/MultilanguageAudioEncoding).

Den här mappen innehåller ett exempel arbets flöde som kan användas för att koda en MXF-fil till en multi MP4-fil till gång med flera ljud spår.

Det här arbets flödet förutsätter att MXF-filen innehåller ett ljud spår. de ytterligare ljud spåren ska skickas som separata ljudfiler (WAV eller MP4...).

Följ dessa steg om du vill koda:

* Skapa en Media Services till gång med MXF-filen och ljudfilerna (0 till 18 ljudfiler).
* Kontrol lera att MXF-filen har angetts som en primär fil.
* Skapa ett jobb och en aktivitet med hjälp av Premium Workflow Encoder-processorn. Använd arbets flödet som tillhandahålls (MultiMP4-1080p-19audio-v1. Workflow).
* Skicka setruntime.xml data till aktiviteten (om du använder Azure Media Services Utforskaren använder du "skicka XML-data till arbets flödes knappen").
  * Uppdatera XML-data för att ange rätt fil namn och språk etiketter.
  * Arbets flödet har ljud komponenter med namnet ljud 1 till ljud 18.
  * RFC5646 stöds för språk tag gen.

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

* Den kodade till gången innehåller flera språk ljud spår och dessa spår bör vara valbara i Azure Media Player.

## <a name="see-also"></a>Se även
* [Vi presenterar Premium encoding i Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services)
* [Så här använder du Premium encoding i Azure Media Services](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services)
* [Kodning av innehåll på begäran med Azure Media Services](media-services-encode-asset.md#media-encoder-premium-workflow)
* [Media Encoder Premium Workflow format och codec](media-services-premium-workflow-encoder-formats.md)
* [Exempel på arbetsflödesmallar](https://github.com/Azure/azure-media-services-samples)
* [Azure Media Services Explorer-verktyg](https://aka.ms/amse)

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
