---
title: Hämtning av Azure Kinect Body tracking SDK
description: Lär dig hur du hämtar varje version av Azure Kinect sensor SDK i Windows eller Linux.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, Kinect, SDK, hämtnings uppdatering, senaste, tillgänglig, installation, brödtext, spårning
ms.openlocfilehash: 0ac0598d893617f341b9e1fd4d45c0c3e3f3c619
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359603"
---
# <a name="download-azure-kinect-body-tracking-sdk"></a>Hämta Azure Kinect Body tracking SDK

Det här dokumentet innehåller länkar för att installera varje version av Azure Kinect Body tracking SDK.

## <a name="azure-kinect-body-tracking-sdk-contents"></a>Innehåll för Azure Kinect Body tracking SDK

- Sidhuvud och bibliotek för att bygga ett program för innehålls spårning med hjälp av Azure Kinect DK.
- Distribuerbara DLL-filer som behövs för innehålls spårnings program med hjälp av Azure Kinect DK.
- Exempel på brödtext som spårar program.

## <a name="windows-download-links"></a>Länkar för Windows-hämtning

Version       | Ladda ned
--------------|----------
1.0.1 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100942) - [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.1)
1.0.0 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100848) - [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/1.0.0)
0.9.5 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100636) - [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.5)
0.9.4 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100415) - [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.4)
0.9.3 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100307) - [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.3)
0.9.2 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100128) - [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.2)
0.9.1 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=100063) - [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.1)
0.9.0 | [MSI](https://www.microsoft.com/en-us/download/details.aspx?id=58402) - [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Kinect.BodyTracking/0.9.0)

## <a name="linux-installation-instructions"></a>Installations anvisningar för Linux

För närvarande är den enda distribution som stöds Ubuntu 18,04. Information om hur du begär stöd för andra distributioner finns på [den här sidan](https://aka.ms/azurekinectfeedback).

Först måste du konfigurera [Microsofts paket lagrings plats](https://packages.microsoft.com/)genom att följa anvisningarna [här](/windows-server/administration/linux-package-repository-for-microsoft-software).

`libk4abt<major>.<minor>-dev`Paketet innehåller de huvuden-och cmake-filer som ska byggas `libk4abt` .
`libk4abt<major>.<minor>`Paketet innehåller de delade objekt som krävs för att köra körbara filer som är beroende av `libk4abt` och exempel visnings programmet.

De grundläggande självstudierna kräver `libk4abt<major>.<minor>-dev` paketet. Kör för att installera den

`sudo apt install libk4abt1.0-dev`

Om kommandot lyckas är SDK klart för användning.

> [!NOTE]
> När du installerar SDK måste du komma ihåg vilken sökväg du installerar till. Till exempel "C:\Program Files\Azure Kinect Body tracking SDK 1.0.0". Du hittar exemplen som refereras i artiklar i den här sökvägen.
> Bröd text spårnings exemplen finns i mappen [Body-tracking-samples](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples) i Azure-Kinect-samples-lagringsplatsen. Du hittar exemplen som refereras i artiklar här.

## <a name="change-log"></a>Ändringslogg

### <a name="v101"></a>v-1.0.1
* [Fel korrigering] Åtgärda problemet att SDK kraschar om du läser in onnxruntime.dll från sökväg på Windows version 19025 eller senare: [länk](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/932)

### <a name="v100"></a>v-1.0.0
* Zoomfunktionen Lägg till C#-omslutningen i MSI-installationsprogrammet.
* [Fel korrigering] Åtgärda problemet att huvud rotationen inte kan identifieras korrekt: [länk](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/997)
* [Fel korrigering] Åtgärda problemet att CPU-användningen går upp till 100% på Linux-datorn: [länk](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1007)
* Stickprov Lägg till två exempel i exempel lagrings platsen. Exempel 1 visar hur du transformerar text spårnings resultat från [länken](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/camera_space_transform_sample)djup yta till färg utrymme. exempel 2 visar hur du identifierar [länken](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples/floor_detector_sample) golv plan

### <a name="v095"></a>v-0.9.5
* Zoomfunktionen C#-stöd. C#-omslutningen är packad i NuGet-paketet.
* Zoomfunktionen Stöd för flera spårare. Det går att skapa flera spårare. Nu kan användaren skapa flera Spårare för att spåra kroppar från olika Azure Kinect-enheter.
* Zoomfunktionen Bearbetnings stöd för flera trådar i CPU-läge. När du kör i CPU-läge kommer alla kärnor att användas för att maximera hastigheten.
* Zoomfunktionen Lägg till `gpu_device_id` i `k4abt_tracker_configuration_t` struktur. Tillåt användare att ange en annan GPU-enhet än den som används som standard för att köra Body tracking-algoritmen.
* [Fel korrigering/bryta ändring] Korrigera skrivfel i ett gemensamt namn. Ändra gemensamt namn från `K4ABT_JOINT_SPINE_NAVAL` till `K4ABT_JOINT_SPINE_NAVEL` .

### <a name="v094"></a>v-0.9.4
* Zoomfunktionen Stöd för att lägga till hand lederna. SDK ger information om tre ytterligare leder för varje hand: HAND, HANDTIP, TUMMe.
* Zoomfunktionen Lägg till förtroende nivå för förutsägelse för varje identifierad skarv.
* Zoomfunktionen Lägg till stöd för processor läge. Genom att ändra `cpu_only_mode` värdet i `k4abt_tracker_configuration_t` , nu kan SDK köras i CPU-läge som inte kräver att användaren har ett kraftfullt grafik kort.

### <a name="v093"></a>v-0.9.3
* Zoomfunktionen Publicera en ny DNN-modell dnn_model_2_0. Onnx, som i stor utsträckning förbättrar stabiliteten i text spårningen.
* Zoomfunktionen Inaktivera den temporala utjämning som är standard. De spårade lederna kommer att svara mer.
* Zoomfunktionen Förbättra noggrannheten hos Body index kartan.
* [Fel korrigering] Åtgärda fel på att sensor orienterings inställningen inte är effektiv.
* [Fel korrigering] Ändra body_index_maps typ från K4A_IMAGE_FORMAT_CUSTOM till K4A_IMAGE_FORMAT_CUSTOM8.
* [Känt problem] Två nära instanser kan sammanfogas till ett enda instans segment.

### <a name="v092"></a>v-0.9.2
* [Viktig ändring] Uppdateringen är beroende av den senaste Azure Kinect-sensor SDK-1.2.0.
* [API-ändring] `k4abt_tracker_create` funktionen börjar ta en `k4abt_tracker_configuration_t` inmatad funktion. 
* [API-ändring] Ändra `k4abt_frame_get_timestamp_usec` API till `k4abt_frame_get_device_timestamp_usec` så att det blir mer särskilt och konsekvent med sensor-SDK-1.2.0.
* Zoomfunktionen Tillåt användare att ange sensor monterings orientering när spåraren skapar en spårning för att uppnå mer exakta resultat vid brödtext vid montering i olika vinklar.
* Zoomfunktionen Ange ett nytt API `k4abt_tracker_set_temporal_smoothing` för att ändra mängden temporal utjämning som användaren vill tillämpa.
* Zoomfunktionen Lägg till C++-omslutningen K4ABT. HPP.
* Zoomfunktionen Lägg till versions definitions huvud k4abtversion. h.
* [Fel korrigering] Åtgärda fel som orsakade mycket hög CPU-användning.
* [Fel korrigering] Korrigera fel vid kraschering av loggar.

### <a name="v091"></a>v-0.9.1 till och
* [Fel korrigering] Korrigera minnes läcka när spåraren förstörs
* [Fel korrigering] Bättre fel meddelanden för saknade beroenden
* [Fel korrigering] Rapportera inte utan att krascha när du skapar en andra spår instans
* [Fel korrigering] Loggning av miljövariabler fungerar nu korrekt
* Linux-support

### <a name="v090"></a>v-0.9.0

* [Viktig ändring] Degraderade SDK-beroendet till CUDA 10,0 (från CUDA 10,1). ONNX Runtime har officiellt bara stöd för upp till CUDA 10,0.
* [Viktig ändring] Växlat till ONNX-körningsmiljön i stället för Tensorflow Runtime. Minskar den första ram start tiden och minnes användningen. Det minskar också storleken på den binära SDK: n.
* [API-ändring] Har bytt namn `k4abt_tracker_queue_capture()` till `k4abt_tracker_enqueue_capture()`
* [API-ändring] `k4abt_frame_get_body()` Dela upp i två separata funktioner: `k4abt_frame_get_body_skeleton()` och `k4abt_frame_get_body_id()` . Nu kan du fråga efter Body-ID utan att alltid kopiera hela Skeleton-strukturen.
* [API-ändring] Funktionen har lagts  `k4abt_frame_get_timestamp_usec()` till för att förenkla stegen för användarna att fråga efter tidsstämpeln för Body-ramen.
* Bättre bättre kontroll av Body-algoritmens precision och spårning av tillförlitlighet

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Kinect DK](about-azure-kinect-dk.md)

- [Konfigurera Azure Kinect DK](set-up-azure-kinect-dk.md)

- [Konfigurera innehålls spårning i Azure Kinect](body-sdk-setup.md)