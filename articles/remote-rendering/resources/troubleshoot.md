---
title: Felsöka
description: Felsöknings information för Azure Remote rendering
author: florianborn71
ms.author: flborn
ms.date: 02/25/2020
ms.topic: troubleshooting
ms.openlocfilehash: 59dc64c952aab6b37e6a779ab1e7e85b9a8ab4b7
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84018828"
---
# <a name="troubleshoot"></a>Felsöka

Den här sidan listar vanliga problem som stör Azure-fjärrrendering och sätt att lösa dem.

## <a name="cant-link-storage-account-to-arr-account"></a>Det går inte att länka lagrings kontot till ARR-kontot

Ibland visas inte det fjärranslutna åter givnings kontot under [länkningen av ett lagrings konto](../how-tos/create-an-account.md#link-storage-accounts) . Åtgärda problemet genom att gå till kontot ARR i Azure Portal och välja **identitet** under gruppen **Inställningar** till vänster. Kontrol lera att **status** har angetts till **på**.
![Unity Frame debugger](./media/troubleshoot-portal-identity.png)

## <a name="client-cant-connect-to-server"></a>Klienten kan inte ansluta till servern

Se till att brand väggarna (på enheten, i routrarna osv.) inte blockerar följande portar:

* **50051 (TCP)** – krävs för inledande anslutning (http-handskakning)
* **8266 (TCP + UDP)** – krävs för data överföring
* **5000 (TCP)**, **5433 (tcp)**, **8443 (TCP)** – krävs för [ArrInspector](tools/arr-inspector.md)

## <a name="error-disconnected-videoformatnotavailable"></a>Fel ' `Disconnected: VideoFormatNotAvailable` '

Kontrol lera att GPU: n stöder maskinvarubaserad video avkodning. Se [Development PC](../overview/system-requirements.md#development-pc).

Om du arbetar på en bärbar dator med två GPU: er, är det möjligt att den GPU som du kör på som standard inte tillhandahåller funktioner för avkodning av maskin varu video. I så fall kan du försöka tvinga appen att använda den andra GPU: n. Detta är ofta möjligt i inställningarna för GPU-drivrutinen.

## <a name="h265-codec-not-available"></a>H265-codec är inte tillgänglig

Det finns två orsaker till varför servern kan nekas att ansluta till ett `codec not available` fel.

**H265-codecen är inte installerad:**

Se först till att installera **hevc video-tillägg** som anges i [program varu](../overview/system-requirements.md#software) delen av system kraven.

Om du fortfarande stöter på problem kontrollerar du att grafik kortet har stöd för H265 och att du har den senaste grafik driv rutinen installerad. Se avsnittet [Development PC](../overview/system-requirements.md#development-pc) i system krav för leverantörsspecifik information.

**Codecen installeras, men kan inte användas:**

Orsaken till det här problemet är en felaktig säkerhets inställning på DLL-filerna. Det här problemet visas inte när du försöker titta på videor som är kodade med H265. Problemet behöver inte åtgärdas om du installerar om codec: en. Utför i stället följande steg:

1. Öppna en **PowerShell med administratörs behörighet** och kör

    ```PowerShell
    Get-AppxPackage -Name Microsoft.HEVCVideoExtension
    ```
  
    Det kommandot ska utdata från `InstallLocation` codecen, något som liknar:
  
    ```cmd
    InstallLocation   : C:\Program Files\WindowsApps\Microsoft.HEVCVideoExtension_1.0.23254.0_x64__5wasdgertewe
    ```

1. Öppna mappen i Utforskaren
1. Det bör finnas en **x86** -och en **x64** -undermapp. Högerklicka på en av mapparna och välj **Egenskaper**
    1. Välj fliken **säkerhet** och klicka på knappen **avancerade** inställningar
    1. Klicka på **ändra** för **ägaren**
    1. Skriv **Administratörer** i textfältet
    1. Klicka på **kontrol lera namn** och **OK**
1. Upprepa stegen ovan för den andra mappen
1. Upprepa också stegen ovan på varje DLL-fil i båda mapparna. Det bör finnas fyra DLL-filer helt och hållet.

Kontrol lera att inställningarna är korrekta genom att göra detta för var och en av de fyra DLL-filerna:

1. Välj **egenskaper > säkerhets > redigera**
1. Gå igenom listan över alla **grupper/användare** och kontrol lera att var och en har behörigheten **läsa & att köra** rätt uppsättning (bocken i kolumnen **Tillåt** måste vara kryssad)

## <a name="low-video-quality"></a>Låg video kvalitet

Video kvaliteten kan komprometteras antingen av nätverks kvaliteten eller den saknade video-codecen för H265.

* Se stegen för att [identifiera nätverks problem](#unstable-holograms).
* Se [system kraven](../overview/system-requirements.md#development-pc) för att installera den senaste grafik driv rutinen.

## <a name="video-recorded-with-mrc-does-not-reflect-the-quality-of-the-live-experience"></a>Video som spelas in med MRC visar inte kvaliteten på Live-upplevelsen

En video kan spelas in på HoloLens genom en [MRC-avbildning (Mixed Reality Capture)](https://docs.microsoft.com/windows/mixed-reality/mixed-reality-capture-for-developers). Den resulterande videon har dock sämre kvalitet än Live-upplevelsen av två orsaker:
* Video hastigheten är en gräns på 30 Hz i stället för 60 Hz.
* Video bilderna går inte genom bearbetnings steget för [försenad fas](../overview/features/late-stage-reprojection.md) bearbetning, så videon verkar vara choppier.

Båda är olika begränsningar i inspelnings tekniken.

## <a name="black-screen-after-successful-model-loading"></a>Svart skärm efter lyckad modell inläsning

Om du är ansluten till åter givnings körningen och läste in en modell, men bara ser en svart skärm senare, kan detta ha några distinkta orsaker.

Vi rekommenderar att du testar följande saker innan du gör en mer djupgående analys:

* Är H265-kodeken installerad? Även om det bör finnas en återgång till H264,-kodeken har vi sett fall där den här återställningen inte fungerade korrekt. Se [system kraven](../overview/system-requirements.md#development-pc) för att installera den senaste grafik driv rutinen.
* När du använder ett Unity-projekt stänger du Unity, tar bort de tillfälliga *biblioteks* -och *OBJ* -mapparna i projekt katalogen och läser in/bygger projektet igen. I vissa fall orsakade cachelagrade data att exemplet inte fungerar korrekt utan uppenbar anledning.

Om dessa två steg inte var hjälp, krävs det att ta reda på om video ramar tas emot av klienten eller inte. Detta kan frågas program mässigt enligt beskrivningen i kapitlet [prestanda frågor på Server sidan](../overview/features/performance-queries.md) . `FrameStatistics struct`Har en medlem som anger hur många video bild rutor som har tagits emot. Om det här värdet är större än 0 och ökar med tiden tar klienten emot faktiska video ramar från servern. Det måste därför vara ett problem på klient sidan.

### <a name="common-client-side-issues"></a>Vanliga problem på klient Sidan

**Modellen överskrider gränserna för den valda virtuella datorn, särskilt det maximala antalet polygoner:**

Se vissa [begränsningar för VM-storlek](../reference/limits.md#overall-number-of-polygons).

**Modellen finns inte i kamerans Frustum:**

I många fall visas modellen korrekt men finns utanför kamerans Frustum. En vanlig orsak är att modellen har exporter ATS med en avlägsen Pivot-of-Center-Pivot så att den klipps av kamerans avlägsen urklipps plan. Det hjälper dig att fråga modellens markerings ruta program mässigt och visualisera rutan med enhets uppdelad som en linje eller skriva ut dess värden till fel söknings loggen.

Dessutom genererar konverterings processen en [utgående JSON-fil](../how-tos/conversion/get-information.md) tillsammans med den konverterade modellen. För att felsöka problem med modell placeringen är det värt att titta på `boundingBox` posten i [outputStatistics-avsnittet](../how-tos/conversion/get-information.md#the-outputstatistics-section):

```JSON
{
    ...
    "outputStatistics": {
        ...
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

Begränsnings rutan beskrivs som en `min` och `max` position i 3D-rymden, i meter. En koordinat på 1000,0 betyder att den är 1 kilometer bort från ursprunget.

Det kan finnas två problem med den här avgränsnings rutan som leder till osynlig geometri:
* **Rutan kan vara för långt bort**, så att objektet klipps ut helt på grund av ett långt plan Urklipp. `boundingBox`Värdena i det här fallet skulle se ut så här: `min = [-2000, -5,-5], max = [-1990, 5,5]` , med en stor förskjutning på x-axeln som ett exempel här. Du kan lösa den här typen av problem genom att aktivera `recenterToOrigin` alternativet i [modell konverterings konfigurationen](../how-tos/conversion/configure-model-conversion.md).
* **Rutan kan centreras men vara av en storlek som är för stor**. Det innebär att låt kameran starta i mitten av modellen, att dess geometri klipps i alla riktningar. Typiska `boundingBox` värden i det här fallet skulle se ut så här: `min = [-1000,-1000,-1000], max = [1000,1000,1000]` . Orsaken till den här typen av problem är vanligt vis en enhets skalnings konflikt. Kompensera genom att ange ett [skalnings värde under konverteringen](../how-tos/conversion/configure-model-conversion.md#geometry-parameters) eller markera käll modellen med rätt enheter. Skalning kan också tillämpas på rotnoden vid inläsning av modellen vid körning.

**Pipeline-renderingen omfattar inte åter givnings hookarna:**

Azure Remote rendering-hookar i Unity Render-pipeline för att göra en ram komposition med videon och för att göra omprojektionen. Öppna menyn för att kontrol lera att dessa hookar finns *:::no-loc text="Window > Analysis > Frame debugger":::* . Aktivera det och se till att det finns två poster för `HolographicRemotingCallbackPass` i pipelinen:

![Unity Frame debugger](./media/troubleshoot-unity-pipeline.png)

## <a name="unity-code-using-the-remote-rendering-api-doesnt-compile"></a>Uniting-kod som använder API för fjärrrendering kompileras inte

### <a name="use-debug-when-compiling-for-unity-editor"></a>Använd fel sökning vid kompilering för Unity Editor

Byt *build-typ* för unions lösningen till **Felsök**. När du testar ARR i Unity-redigeraren `UNITY_EDITOR` är definiera endast tillgänglig i "debug"-versioner. Observera att detta inte är relaterat till den build-typ som används för [distribuerade program](../quickstarts/deploy-to-hololens.md), där du bör föredra versions versioner.

### <a name="compile-failures-when-compiling-unity-samples-for-hololens-2"></a>Kompileringsfel vid kompilering av Unity-exempel för HoloLens 2

Vi har sett spurious-felen vid försök att kompilera Unity-exempel (snabb start, ShowCaseApp,..) för HoloLens 2. Visual Studio klagar på att det inte går att kopiera vissa filer så att de finns där. Om du når det här problemet:
* Ta bort alla tillfälliga Unit-filer från projektet och försök igen. Det vill säga, Stäng Unity, ta bort de temporära *biblioteks* -och *OBJ* -mapparna i projekt katalogen och läsa in/utveckla projektet igen.
* Se till att projekten finns i en katalog på disk med rimlig kort sökväg, eftersom kopierings steget ibland verkar köra problem med långa fil namn.
* Om det inte hjälper kan det vara så att MS Sense stör kopierings steget. Om du vill konfigurera ett undantag kör du detta register kommando från kommando raden (kräver administratörs rättigheter):
    ```cmd
    reg.exe ADD "HKLM\SOFTWARE\Policies\Microsoft\Windows Advanced Threat Protection" /v groupIds /t REG_SZ /d "Unity”
    ```
    
## <a name="unstable-holograms"></a>Instabila hologram

Om återgivna objekt ser ut att flyttas tillsammans med huvud förflyttningar, kan det hända att du stöter på problem med LSR ( *sent Stage-projektion* ). Se avsnittet om [omprojektion av sena steg](../overview/features/late-stage-reprojection.md) för vägledning om hur du kan använda en sådan situation.

En annan orsak till instabila hologram (wobbling, tänjning, Darr eller hopp) kan vara dåligt nätverks anslutning, särskilt otillräcklig nätverks bandbredd eller för hög latens. En bra indikator för nätverks anslutningens kvalitet är [prestanda statistik](../overview/features/performance-queries.md) svärdet `ARRServiceStats.VideoFramesReused` . Återanvändade ramar visar situationer där en gammal video RAM behövs återanvändas på klient sidan eftersom ingen ny video RAM var tillgänglig, till exempel på grund av paket förlust eller på grund av variationer i nätverks fördröjningen. Om `ARRServiceStats.VideoFramesReused` är ofta större än noll tyder detta på ett nätverks problem.

Ett annat värde att titta på är `ARRServiceStats.LatencyPoseToReceiveAvg` . Det bör ständigt vara under 100 MS. Om du ser högre värden betyder det att du är ansluten till ett Data Center som är för långt bort.

En lista över eventuella begränsningar finns i [rikt linjerna för nätverks anslutning](../reference/network-requirements.md#guidelines-for-network-connectivity).

## <a name="next-steps"></a>Nästa steg

* [Systemkrav](../overview/system-requirements.md)
* [Nätverkskrav](../reference/network-requirements.md)
