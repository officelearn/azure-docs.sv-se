---
title: Konfigurera offlineuppspelningLäss direktuppspelning med Azure Media Services v3
description: Den här artikeln visar hur du konfigurerar ditt Azure Media Services-konto för streaming PlayReady för Windows 10 offline.
services: media-services
keywords: DASH, DRM, Widevine offlineläge, ExoPlayer, Android
documentationcenter: ''
author: willzhan
manager: steveng
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/01/2019
ms.author: willzhan
ms.openlocfilehash: 151aadadb5674f7f144d42b1f9d5115501ed381d
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887239"
---
# <a name="offline-playready-streaming-for-windows-10-with-media-services-v3"></a>Offline PlayReady Streaming för Windows 10 med Media Services v3

Azure Media Services stöder hämtning/uppspelning offline med DRM-skydd. Den här artikeln innehåller offlinesupport för Azure Media Services för Windows 10/PlayReady-klienter. Du kan läsa om offline-läge stöd för iOS / FairPlay och Android / Widevine enheter i följande artiklar:

- [FairPlay-direktuppspelning offline för iOS](offline-fairplay-for-ios.md)
- [Offline Widevine Streaming för Android](offline-widevine-for-android.md)

> [!NOTE]
> Offline DRM faktureras endast för att göra en enda begäran om en licens när du hämtar innehållet. Eventuella fel faktureras inte.

## <a name="overview"></a>Översikt

Det här avsnittet ger en del bakgrund om uppspelning av offlineläge, särskilt varför:

* I vissa länder/regioner är Internettillgänglighet och/eller bandbredd fortfarande begränsad.Användare kan välja att ladda ner först för att kunna titta på innehåll i tillräckligt hög upplösning för tillfredsställande tittarupplevelse. I det här fallet är problemet inte nätverkstillgänglighet, utan det är begränsad nätverksbandbredd. OTT/OVP-leverantörer ber om stöd för offlineläge.
* Som avslöjas på Netflix 2016 Q3 aktieägarkonferens, ladda ner innehåll är en "ofta efterfrågade funktionen", och "vi är öppna för det" sade Reed Hastings, Netflix VD.
* Vissa innehållsleverantörer kan inte tillåta leverans av DRM-licenser utanför ett lands/regions gräns. Om en användare behöver resa utomlands och fortfarande vill titta på innehåll behövs nedladdning offline.
 
Utmaningen vi står inför när vi implementerar offlineläge är följande:

* MP4 stöds av många spelare, kodarverktyg, men det finns ingen bindning mellan MP4-behållaren och DRM.
* På lång sikt är CFF med CENC rätt väg att gå. Men idag är verktyg / spelare stöd ekosystemet inte där ännu. Vi behöver en lösning i dag.
 
Tanken är: smooth streaming[(PIFF)](https://docs.microsoft.com/iis/media/smooth-streaming/protected-interoperable-file-format)filformat med H264/AAC har en bindning med PlayReady (AES-128 CTR). Individuell smidig streaming .ismv fil (förutsatt att ljudet är muxed i video) är själv en fMP4 och kan användas för uppspelning. Om ett smidigt strömmande innehåll går via PlayReady-kryptering blir varje ISMV-fil en PlayReady-skyddad fragmenterad MP4. Vi kan välja en .ismv-fil med önskad bitrate och byta namn på den som .mp4 för nedladdning.

Det finns två alternativ för att vara värd för PlayReady-skyddad MP4 för progressiv nedladdning:

* Man kan placera den här MP4:n i samma anläggnings-/medietjänsttillgång och utnyttja slutpunkten för direktuppspelning av Azure Media Services för progressiv nedladdning.
* Man kan använda SAS locator för progressiv nedladdning direkt från Azure Storage, förbi Azure Media Services.
 
Du kan använda två typer av PlayReady-licensleverans:

* PlayReady-licensleveranstjänst i Azure Media Services;
* PlayReady licensservrar värd var som helst.

Nedan finns två uppsättningar testtillgångar, den första med PlayReady-licensleverans i AMS medan den andra med min PlayReady-licensserver finns på en Azure VM:

Tillgång #1:

* Progressiv nedladdningsadress:[https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4")
* PlayReady LA_URL (AMS):[https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/](https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/)

Tillgång #2:

* Progressiv nedladdningsadress:[https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (lokalt):[https://willzhan12.cloudapp.net/playready/rightsmanager.asmx](https://willzhan12.cloudapp.net/playready/rightsmanager.asmx)

För uppspelningstestning använde vi ett universellt Windows-program på Windows 10. I [Windows 10 Universal prover](https://github.com/Microsoft/Windows-universal-samples), Det finns en grundläggande spelare exempel som kallas [Adaptive Streaming Sample](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming). Allt vi behöver göra är att lägga till koden för oss att välja nedladdad video och använda den som källa, istället för adaptiv streaming källa. Ändringarna är i knappen klicka händelsehanterare:

```csharp
private async void LoadUri_Click(object sender, RoutedEventArgs e)
{
    //Uri uri;
    //if (!Uri.TryCreate(UriBox.Text, UriKind.Absolute, out uri))
    //{
    // Log("Malformed Uri in Load text box.");
    // return;
    //}
    //LoadSourceFromUriTask = LoadSourceFromUriAsync(uri);
    //await LoadSourceFromUriTask;

    //willzhan change start
    // Create and open the file picker
    FileOpenPicker openPicker = new FileOpenPicker();
    openPicker.ViewMode = PickerViewMode.Thumbnail;
    openPicker.SuggestedStartLocation = PickerLocationId.ComputerFolder;
    openPicker.FileTypeFilter.Add(".mp4");
    openPicker.FileTypeFilter.Add(".ismv");
    //openPicker.FileTypeFilter.Add(".mkv");
    //openPicker.FileTypeFilter.Add(".avi");

    StorageFile file = await openPicker.PickSingleFileAsync();

    if (file != null)
    {
       //rootPage.NotifyUser("Picked video: " + file.Name, NotifyType.StatusMessage);
       this.mediaPlayerElement.MediaPlayer.Source = MediaSource.CreateFromStorageFile(file);
       this.mediaPlayerElement.MediaPlayer.Play();
       UriBox.Text = file.Path;
    }
    else
    {
       // rootPage.NotifyUser("Operation cancelled.", NotifyType.ErrorMessage);
    }

    // On small screens, hide the description text to make room for the video.
    DescriptionText.Visibility = (ActualHeight < 500) ? Visibility.Collapsed : Visibility.Visible;
}
```

![Uppspelning av PlayReady-skyddad fMP4 i offlineläge](./media/offline-playready-for-windows/offline-playready1.jpg)

Eftersom videon är under PlayReady skydd, kommer skärmdumpen inte att kunna inkludera videon.

Sammanfattningsvis har vi uppnått offlineläge på Azure Media Services:

* Innehållstranscoding och PlayReady-kryptering kan göras i Azure Media Services eller andra verktyg.
* Innehåll kan finnas i Azure Media Services eller Azure Storage för progressiv nedladdning.
* PlayReady-licensleverans kan vara från Azure Media Services eller någon annanstans.
* Det förberedda smidigt strömmande innehållet kan fortfarande användas för onlinestreaming via DASH eller smidigt med PlayReady som DRM.

## <a name="next-steps"></a>Nästa steg

[Utforma multi-DRM-innehållsskyddssystem med åtkomstkontroll](design-multi-drm-system-with-access-control.md)
