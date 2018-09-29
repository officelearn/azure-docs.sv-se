---
title: Projektet Akustik exempelgenomgång – Cognitive Services
description: Den här genomgången beskriver scen för Unity-exempel för projektet Akustik, inklusive distribution till skrivbord och VR.
services: cognitive-services
author: kegodin
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: eaf7ff9f7f791fd6d04e6b76d256b4987c50cd13
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434100"
---
# <a name="unity-sample-walkthrough"></a>Unity exempelgenomgång
Det här är en genomgång av exemplet projekt Akustik. Mer information om vilka Akustik projekt är kan du ta en titt på [introduktion till projektet Akustik](what-is-acoustics.md). Hjälp om du lägger till projektet Akustik-paket till en befintlig Unity-projekt kan använda den [komma igång-guiden](getting-started.md).

## <a name="requirements-for-running-the-sample-project"></a>Krav för att köra exempelprojektet
* Unity 2018.2 +, som använder .NET 4.x scripting körningsversion
* Windows 64-bitars Unity Editor
* Exemplet stöder Windows desktop-, UWP- och Android mål, inklusive monteras visar (HMDs)
* Azure Batch-prenumeration som behövs för ändamålet process

## <a name="sample-project-setup"></a>Exemplet projektkonfiguration
Hämta och importera den **MicrosoftAcoustics.Sample.unitypackage**. Under importen, Projektinställningar inklusive **Spatializer** och **skript körningsversion** uppdateras för att uppfylla plugin-programmets krav. När processen är klar visas ett fel i Unity-konsolen från **AcousticsGeometry.cs** om hur du ändrar körningsversion skript till **.NET 4.x motsvarande**. Inställningar för ändringen görs som en del av paketet importen, men kräver en omstart av Unity ska börja gälla. Starta om Unity nu.

## <a name="running-the-sample"></a>Köra exemplet
Exemplet innehåller en demo-scen **Assets/AcousticsDemo/ProjectAcousticsDemo.unity**. Den här scen har tre ljud källor. Som standard bara en ljudkälla spelas upp och de andra två har pausats. Dessa finns under **ljud källor** i den **hierarki**. Om du vill göra en allmän navigering skript, är Main kameran underordnad CameraHolder-objektet. 

![Vy av grupphierarkin](media/SampleHierarchyView.png)

Scenen redan har inbyggd och har en ACE-fil som är associerade med den **MicrosoftAcoustics** prefab i den **hierarki**. 

Lyssna på hur scenen låter genom att klicka på uppspelningsknappen i Unity-redigeraren. På skrivbordet, använda W, A, S, D och musen för att flytta. Om du vill jämföra hur scenen låter med och utan Akustik, trycker du på den **R** tills överläggningstext blir röd och säger ”Akustik: inaktiverad”. Om du vill se tangentbordsgenvägar för fler kontroller, trycker du på **F1**. Alla kontroller är också användbar genom att högerklicka för att välja åtgärden som ska utföras, sedan vänsterklicka på för att utföra åtgärden.

## <a name="targeting-other-platforms"></a>Riktar in sig på andra plattformar
Exemplet innehåller inställningar för att köras på Windows-skrivbordet, UWP-, Windows Mixed Reality-, Android- och Oculus Go. Som standard konfigureras projektet för Windows-skrivbordet. Om du vill rikta en VR-plattform, går du till player-inställningar (**Redigera > Inställningar > Player**), hitta den **XR inställningar**, och kontrollera den **virtuella verklighet stöds** kryssrutan.

![Aktivera VR](media/VRSupport.png)  

Ansluta ett VR-headset till din dator. Gå till **fil > Versionsinställningar**, och klicka på **skapa och köra** att distribuera exemplet till VR-headset. Navigera genom scen med rörelse-styrenheter för din headset, eller försök med W, A, S, D på tangentbordet.    
Om du vill rikta Android- och Oculus Go, väljer du Android från den **Versionsinställningar** menyn. Klicka på **växla Target**, sedan **skapa och köra**. Detta distribuerar scenen exemplet till den anslutna Android-enheten. Information om Unity-utveckling för Android finns i [Unity-dokumentation](https://docs.unity3d.com/Manual/android-GettingStarted.html).

![Target Android](media/TargetAndroid.png)  

## <a name="next-steps"></a>Nästa steg
* [Skapa ett Azure-konto](create-azure-account.md) för dina egna bakes
* Utforska den [utforma processen](design-process.md)

