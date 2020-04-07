---
title: Systemkrav
description: Visar systemkraven för Azure Remote Rendering
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: article
ms.openlocfilehash: c239f7062dc39492a0cf63ac3aadbaf94acbf032
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680445"
---
# <a name="system-requirements"></a>Systemkrav

> [!IMPORTANT]
> **Azure Remote Rendering** är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

I det här kapitlet visas de lägsta systemkraven för att arbeta med *Azure Remote Rendering* (ARR).

## <a name="development-pc"></a>Utveckling PC

* Windows 10 version 1903 eller senare.
* Uppdaterade grafikdrivrutiner.
* Valfritt: H265-maskinvaruvideoavkodare, om du vill använda lokal förhandsgranskning av fjärrrendeerat innehåll (till exempel i Unity).

> [!IMPORTANT]
> Windows Update levererar inte alltid de allra senaste GPU-drivrutinerna, kontrollera gpu-tillverkarens webbplats efter de senaste drivrutinerna:
>
> * [AMD-drivrutiner](https://www.amd.com/en/support)
> * [Intel-drivrutiner](https://www.intel.com/content/www/us/en/support/detect.html)
> * [NVIDIA-drivrutiner](https://www.nvidia.com/Download/index.aspx)

I tabellen nedan visas vilka GPU:er som stöder avkodning av H265-maskinvaruvideo.

| GPU tillverkare | Modeller som stöds |
|-----------|:-----------|
| Nvidia | Kontrollera **NVDEC-supportmatrisen** [längst ned på den här sidan](https://developer.nvidia.com/video-encode-decode-gpu-support-matrix). Din GPU behöver ett JA i **H.265 4:2:0 8-bitarskolumnen.** |
| Amd | GPU:er med minst version 6 av AMD:s [Unified Video Decoder](https://en.wikipedia.org/wiki/Unified_Video_Decoder#UVD_6). |
| Intel | Skylake och nyare processorer |

Även om rätt H265-codec kan installeras, kan säkerhetsegenskaper på codec DLL-filer orsaka codec initieringsfel. [Felsökningsguiden](../resources/troubleshoot.md#h265-codec-not-available) beskriver steg hur du löser problemet. DLL-problemet kan bara uppstå när du använder tjänsten i ett skrivbordsprogram, till exempel i Unity.

## <a name="devices"></a>Enheter

Azure Remote Rendering stöder för närvarande endast **HoloLens 2** och Windows desktop UWP som en målenhet.

Det är viktigt att använda den senaste HEVC-codec, eftersom nyare versioner har betydande förbättringar i svarstid. Så här kontrollerar du vilken version som är installerad på enheten:

1. Starta **Microsoft Store**.
1. Klicka på **"..."-knappen** längst upp till höger.
1. Välj **Hämtningar och uppdateringar**.
1. Sök i listan för **HEVC-videotillägg från enhetstillverkaren**.
1. Kontrollera att codec-koden i listan har minst version **1.0.21821.0**.
1. Klicka på knappen **Hämta uppdateringar** och vänta tills den installeras.

## <a name="network"></a>Nätverk

En stabil nätverksanslutning med låg latens är avgörande för en bra användarupplevelse.

Se dedikerat kapitel för [nätverkskrav](../reference/network-requirements.md).

Felsökning av nätverksproblem finns i [felsökningsguiden](../resources/troubleshoot.md#unstable-holograms).

## <a name="software"></a>Programvara

Följande program måste installeras:

* Den senaste versionen av **Visual Studio 2019** [(hämtad)](https://visualstudio.microsoft.com/vs/older-downloads/)
* **Windows SDK 10.0.18362.0** [(hämta)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* **GIT** [(ladda ner)](https://git-scm.com/downloads)
* Valfritt: Om du vill visa videoströmmen från servern på en stationär dator behöver du **HEVC Video Extensions** [(Microsoft Store-länk)](https://www.microsoft.com/p/hevc-video-extensions/9nmzlz57r3t7).

## <a name="unity"></a>Unity

För utveckling med Unity, installera

* Unity 2019.3.1 [(ladda ner)](https://unity3d.com/get-unity/download)
* Installera dessa moduler i Unity:
  * **UWP** – Stöd för universal windows-plattformsbygge
  * **IL2CPP** - Stöd för Windows Build (IL2CPP)

## <a name="next-steps"></a>Nästa steg

* [Snabbstart: Återge en modell med Unity](../quickstarts/render-model.md)
