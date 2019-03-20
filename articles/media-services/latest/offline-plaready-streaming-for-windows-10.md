---
title: Konfigurera ditt konto för offline strömning av PlayReady skyddat innehåll – Azure
description: Den här artikeln visar hur du konfigurerar ditt Azure Media Services-konto för direktuppspelning PlayReady för Windows 10 offline.
services: media-services
keywords: DASH, DRM, Widevine Offline Mode, ExoPlayer, Android
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
ms.openlocfilehash: 9e29b08da35b9fd2f479f1d4e3b0d89ed881344b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57902009"
---
# <a name="offline-playready-streaming-for-windows-10"></a>PlayReady offline Streaming för Windows 10

Azure Media Services stöder offline download/uppspelning med DRM-skydd. Den här artikeln beskriver offline stöd för Azure Media Services för Windows 10/PlayReady-klienter. Du kan läsa om stöd för offline-läge för iOS/FairPlay- och Android/Widevine enheter i följande artiklar:

- [Offline FairPlay Streaming för iOS](offline-fairplay-for-ios.md)
- [Offline Widevine för direktuppspelning för Android](offline-widevine-for-android.md)

## <a name="overview"></a>Översikt

Det här avsnittet ger bakgrundsinformation om offline-läge utmatning särskilt varför:

* I vissa länder är Internet tillgänglighet och/eller bandbredd fortfarande begränsad. Användare kan välja att hämta först om du vill kunna titta på innehållet i tillräckligt hög matchning för tillfredsställande tittarupplevelse. I det här fallet oftast brukar problemet är inte nätverkets tillgänglighet, i stället det nätverksbandbredden är begränsad. OTT/OVP providers ber för stöd för offline-läge.
* Som visades på Netflix 2016 Q3 aktieägare konferensen, nedladdning av innehåll är en ”om begärda funktion” och ”vi är öppen för den” säger av Reed Hastings, Netflix VD.
* Vissa innehållsleverantörer kan neka DRM-licensleverans utöver ett land kantlinje. Om en användare behöver reser utomlands och fortfarande vill titta på innehållet, krävs offline nedladdning.
 
Den stora utmaningen vi möter implementera offline-läge är följande:

* MP4 stöds av många spelare, encoder verktyg, men det finns inga bindningar mellan MP4-behållare och DRM;
* CFF med CENC är bättre att på lång sikt. Idag, är verktyg/player support-ekosystem dock inte det ännu. Vi behöver en lösning i dag.
 
Tanken är: smooth streaming ([PIFF](https://go.microsoft.com/?linkid=9682897))-format med H264/AAC har en bindning med PlayReady (AES-128 CTR). Jämn direktuppspelning .ismv fil (förutsatt att ljud är muxed i videon) är i sig en fMP4 och kan användas för uppspelning. Om en smidig strömningsinnehåll går igenom PlayReady-kryptering, filernas .ismv blir en PlayReady skyddade fragmenterad MP4. Vi kan välja en .ismv-fil med önskad bithastighet och Byt namn på den som .mp4 för hämtning.

Det finns två alternativ för den som är värd för PlayReady skyddade MP4 för progressiv nedladdning:

* Placera den här MP4 i samma behållare/media service tillgång och utnyttja Azure Media Services strömningsslutpunkt för progressiv nedladdning;
* En kan använda SAS-lokaliserare för progressiv nedladdning direkt från Azure Storage, vilket kringgår Azure Media Services.
 
Du kan använda två typer av PlayReady-licensleverans:

* Playreadys licensleveranstjänst i Azure Media Services;
* PlayReady servrar för fjärrskrivbordslicenser finns på olika platser.

Nedan finns två typer av test-tillgångar, den första mallen med PlayReady-licensleverans i AMS samtidigt som den andra mallen med hjälp av Mina PlayReady-licensserver som finns på en Azure-dator:

Plats #1:

* URL för progressiv nedladdning: [https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4")
* PlayReady LA_URL (AMS): [https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/](https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/)

Tillgången #2:

* URL för progressiv nedladdning: [https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4](https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500_H264_1644kbps_AAC_und_ch2_256kbps.mp4)
* PlayReady LA_URL (lokalt): [https://willzhan12.cloudapp.net/playready/rightsmanager.asmx](https://willzhan12.cloudapp.net/playready/rightsmanager.asmx)

För att spela upp testa använde vi ett universellt Windows-program på Windows 10. I [Windows 10 Universal-exempel](https://github.com/Microsoft/Windows-universal-samples), det är ett grundläggande player-exempel som kallas [anpassningsbar direktuppspelning exempel](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/AdaptiveStreaming). Allt vi behöver göra är att lägga till kod att välja hämtade video och använda den som källan, i stället för anpassningsbar strömning källa. Ändringarna visas i knappen klickar du på händelsehanteraren:

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

![Offline-läge uppspelning av PlayReady skyddade fMP4](./media/offline-playready-for-windows/offline-playready1.jpg)


Eftersom videon är under PlayReady-skydd, skärmbilden inte inkludera videon.

Sammanfattningsvis, har vi uppnått offline-läge på Azure Media Services:

* Innehåll transkodning och PlayReady-kryptering kan göras i Azure Media Services eller andra verktyg.
* Innehållet kan finnas i Azure Media Services eller Azure Storage för progressiv nedladdning;
* PlayReady-licensleverans kan vara från Azure Media Services eller någon annanstans.
* Förberedd jämn direktuppspelning innehållet fortfarande kan användas för online strömning via DASH eller smooth med PlayReady som DRM.

## <a name="next-steps"></a>Nästa steg

[Designen av ett system med multi-DRM innehållsskydd med åtkomstkontroll](design-multi-drm-system-with-access-control.md)
