---
title: Felsöka
description: Felsöka information för Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: 7ee219ae5ace0f0da398cc542f410d3c895c8bd4
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80679990"
---
# <a name="troubleshoot"></a>Felsöka

På den här sidan visas vanliga problem som stör Azure Remote Rendering och sätt att lösa dem.

## <a name="client-cant-connect-to-server"></a>Klienten kan inte ansluta till servern

Se till att brandväggarna (på enheten, inuti routrar osv.) inte blockerar följande portar:

* **50051 (TCP)** - krävs för första anslutning (HTTP-handslag)
* **8266 (TCP+UDP)** - krävs för dataöverföring
* **5000 (TCP)**, **5433 (TCP)**, **8443 (TCP)** - krävs för [ArrInspector](tools/arr-inspector.md)

## <a name="error-disconnected-videoformatnotavailable"></a>Felet "Frånkopplad: VideoformatIntevampbar"

Kontrollera att din GPU stöder avkodning av maskinvaruvideo. Se [Development PC](../overview/system-requirements.md#development-pc).

Om du arbetar på en bärbar dator med två GPU: er, är det möjligt att GPU du kör på som standard, inte ger hårdvara video avkodning funktionalitet. Om så är fallet, försök att tvinga din app att använda den andra GPU. Detta är ofta möjligt i GPU-drivrutinsinställningarna.

## <a name="h265-codec-not-available"></a>H265 codec är inte tillgängligt

Det finns två orsaker till att servern kan vägra att ansluta till en **codec inte tillgängligt** fel.

**H265-codec:et är inte installerat:**

Se först till att installera **HEVC Video Extensions** som nämns i [avsnittet Programvara](../overview/system-requirements.md#software) i systemkraven.

Om du fortfarande stöter på problem, se till att ditt grafikkort stöder H265 och du har den senaste grafikdrivrutinen installerad. Se avsnittet [Utvecklingsdator](../overview/system-requirements.md#development-pc) i systemkraven för leverantörsspecifik information.

**Codec är installerad, men kan inte användas:**

Orsaken till det här problemet är en felaktig säkerhetsinställning på DLL:erna. Det här problemet visas inte när du försöker titta på videor kodade med H265. Om du installerar om codec-koden löser du inte heller problemet. Utför i stället följande steg:

1. Öppna ett **PowerShell med administratörsrättigheter** och kör

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    Det kommandot bör `InstallLocation` mata ut kodc, något i stil med:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Öppna mappen i Utforskaren
1. Det bör finnas en **x86** och en **x64** undermapp. Högerklicka på en av mapparna och välj **Egenskaper**
    1. Välj fliken **Säkerhet** och klicka på knappen **Avancerade** inställningar
    1. Klicka på **Ändra** för **ägaren**
    1. Skriva **administratörer** i textfältet
    1. Klicka på **Kontrollera namn** och **OK**
1. Upprepa stegen ovan för den andra mappen
1. Upprepa också stegen ovan på varje DLL-fil i båda mapparna. Det bör finnas fyra DLL helt och hållet.

Gör så här för var och en av de fyra DLL:erna för att kontrollera att inställningarna nu är korrekta:

1. Välj **egenskaper > >-redigera**
1. Gå igenom listan över alla **grupper / användare** och se till att var och en har Läs & **Kör** rätt uppsättning (bocken i **tillåt** kolumnen måste kryssas)

## <a name="low-video-quality"></a>Låg videokvalitet

Videokvaliteten kan äventyras antingen av nätverkskvalitet eller den saknade H265-videocodecen.

* Se stegen för att [identifiera nätverksproblem](#unstable-holograms).
* Se [systemkraven](../overview/system-requirements.md#development-pc) för installation av den senaste grafikdrivrutinen.

## <a name="black-screen-after-successful-model-loading"></a>Svart skärm efter lyckad modellinläsning

Om du är ansluten till renderingskörningen och har läst in en modell framgångsrikt, men bara ser en svart skärm efteråt, kan detta ha några olika orsaker.

Vi rekommenderar att du testar följande saker innan du gör en mer djupgående analys:

* Är H265 codec installerad? Även om det bör finnas en återgång till H264 codec, har vi sett fall där denna reserv inte fungerade korrekt. Se [systemkraven](../overview/system-requirements.md#development-pc) för installation av den senaste grafikdrivrutinen.
* När du använder ett Unity-projekt stänger du Unity, tar bort det tillfälliga *biblioteket* och *obj-mapparna* i projektkatalogen och läser in/bygger projektet igen. I vissa fall cachelagrade data orsakade provet att inte fungera korrekt utan någon uppenbar anledning.

Om dessa två steg inte hjälpte, är det nödvändigt att ta reda på om videobildrutor tas emot av klienten eller inte. Detta kan efterfrågas programmässigt enligt beskrivningen i kapitlet om [prestandafrågor på serversidan.](../overview/features/performance-queries.md) Har `FrameStatistics struct` en medlem som anger hur många videobildrutor som har tagits emot. Om det här antalet är större än 0 och ökar med tiden tar klienten emot faktiska videobildrutor från servern. Följaktligen måste det vara ett problem på klientsidan.

### <a name="common-client-side-issues"></a>Vanliga problem på klientsidan

**Modellen är inte inne i vyn frustum:**

I många fall visas modellen korrekt men ligger utanför kamerans frustum. En vanlig orsak är att modellen har exporterats med en långt utanför centrum pivot så det klipps av kamerans långt klippning plan. Det hjälper till att fråga modellens markeringsram programmässigt och visualisera rutan med Unity som en linjeruta eller skriva ut dess värden till felsökningsloggen.

**Unity-renderingspipelinen innehåller inte renderingskrokarna:**

Azure Remote Rendering krokar i Unity återge pipeline för att göra ramen sammansättning med videon, och att göra reprojection. Om du vill kontrollera att dessa krokar finns öppnar du menyn *Fönster > Analys > Ramfelfelbugger*. Aktivera den och se till att `HolographicRemotingCallbackPass` det finns två poster för på gång:

![Felsökare för enhetsram](./media/troubleshoot-unity-pipeline.png)

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>Enhetskod med api:et för fjärråtergivning kompileras inte

Växla *byggtypen* för Unity-lösningen till **Debug**. När du testar ARR i `UNITY_EDITOR` Unity-redigeraren är definiera endast tillgängligt i "Debug" builds. Observera att detta inte är relaterat till den byggtyp som används för [distribuerade program,](../quickstarts/deploy-to-hololens.md)där du bör föredra "Release" bygger.

## <a name="unstable-holograms"></a>Instabila hologram

Om renderade objekt verkar röra sig tillsammans med huvudrörelser, kan du stöta på problem med *Sent skede Reprojection* (LSR). Se avsnittet om [om omprojektering](../overview/features/late-stage-reprojection.md) i slutet av scenen för vägledning om hur man ska hantera en sådan situation.

En annan orsak till instabila hologram (wobbling, skevhet, jittering eller hoppning hologram) kan vara dålig nätverksanslutning, i synnerhet otillräcklig nätverksbandbredd, eller för hög latens. En bra indikator för kvaliteten på nätverksanslutningen `ARRServiceStats.VideoFramesReused`är [prestandastatistikvärdet](../overview/features/performance-queries.md) . Återanvända bildrutor anger situationer där en gammal videobildruta behövde återanvändas på klientsidan eftersom ingen ny videobildruta var tillgänglig , till exempel på grund av paketförlust eller på grund av variationer i nätverksfördröjning. Om `ARRServiceStats.VideoFramesReused` är ofta större än noll, indikerar detta ett nätverksproblem.

Ett annat värde `ARRServiceStats.LatencyPoseToReceiveAvg`att titta på är . Det bör konsekvent vara under 100 ms. Om du ser högre värden betyder det att du är ansluten till ett datacenter som är för långt bort.

En lista över möjliga minskningar finns i [riktlinjerna för nätverksanslutning](../reference/network-requirements.md#guidelines-for-network-connectivity).

## <a name="next-steps"></a>Nästa steg

* [Systemkrav](../overview/system-requirements.md)
* [Nätverkskrav](../reference/network-requirements.md)
