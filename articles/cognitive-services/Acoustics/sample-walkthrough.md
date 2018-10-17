---
title: 'Exempel: Project Acoustics'
titlesuffix: Azure Cognitive Services
description: I den här genomgången beskrivs Unity-exempelscenen för Project Acoustics, inklusive distribution till skrivbord och VR.
services: cognitive-services
author: kegodin
manager: cgronlun
ms.service: cognitive-services
ms.component: acoustics
ms.topic: sample
ms.date: 08/17/2018
ms.author: kegodin
ms.openlocfilehash: f5ea565e68579dfad601d1037daeb4113e3daa43
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/10/2018
ms.locfileid: "48901166"
---
# <a name="unity-sample-walkthrough"></a>Genomgång av Unity-exempel
Det här är en genomgång av exemplet Project Acoustics. Mer information om vad Project Acoustics är finns i [Introduktion till Project Acoustics](what-is-acoustics.md). Om du vill ha hjälp med att lägga till paketet Project Acoustics till ett befintligt Unity-projekt använder du [guiden Komma igång](getting-started.md).

## <a name="requirements-for-running-the-sample-project"></a>Krav för körning av exempelprojektet
* Unity 2018.2 +, med .NET 4.x-skriptkörningsversion
* Windows 64-bitars Unity Editor
* Exemplet kan användas med UWP-, Android- och Windows-skrivbordsmål, inklusive HMD (head-mounted display)
* Azure Batch-prenumeration krävs för bake-processen

## <a name="sample-project-setup"></a>Exempel på projektkonfiguration
Hämta och importera **MicrosoftAcoustics.Sample.unitypackage**. Vid importen uppdateras projektinställningar som **Spatializer** och **Scripting Runtime Version** (Skriptkörningsversion) så att de motsvarar kraven i plugin-programmet. När detta är klart visas ett fel i Unity-konsolen från **AcousticsGeometry.cs** om att ändra Scripting Runtime Version (Skriptkörningsversion) till **motsvarande .NET 4.x**. Inställningar för ändringen görs som en del av paketimporten, men Unity måste startas om för att inställningarna ska börja gälla. Starta om Unity nu.

## <a name="running-the-sample"></a>Köra exemplet
Exemplet innehåller en demo-scen, **Assets/AcousticsDemo/ProjectAcousticsDemo.unity**. Scenen har tre ljudkällor. Som standard spelas bara en ljudkälla upp och de andra två är pausade. De finns under **Ljudkällor** i **Hierarki**. Som hjälp att skapa ett allmänt navigeringsskript är huvudkameran underordnad CameraHolder-objektet. 

![Hierarkisk vy](media/SampleHierarchyView.png)

Scenen har redan bakats och har en ACE-fil som är kopplad till den förbyggda **MicrosoftAcoustics** i **hierarkin**. 

Lyssna på hur scenen låter genom att klicka på uppspelningsknappen i Unity-redigeraren. På skrivbordet använder du W, A, S, D och musen för att flytta runt. Om du vill jämföra hur scenen låter med och utan akustik trycker du på knappen **R** tills överläggstexten blir röd och säger ”Acoustics: Disabled.” (”Akustik: inaktiverad”.) Om du vill se tangentbordsgenvägar för fler kontroller trycker du på **F1**. Du kan också använda kontrollerna genom att högerklicka för att välja den åtgärd som ska utföras sedan vänsterklicka för att utföra den.

## <a name="targeting-other-platforms"></a>Anpassa för andra plattformar
I exemplet finns inställningar för körning på Windows-skrivbordet, Desktop, UWP, Windows Mixed Reality, Android och Oculus Go. Som standard konfigureras projekten för Windows-skrivbordet. Om du vill anpassa en VR-plattform går du till spelarinställningarna (**Redigera > Projektinställningar > Spelare**), leta rätt på **XR-inställningarna** och markera kryssrutan **Virtual Reality Supported** (VR stöds).

![Aktivera VR](media/VRSupport.png)  

Anslut ett VR-headset till din dator. Gå till **Arkiv > Versionsinställningar** och klicka på **Skapa och kör** för att distribuera exemplet till VR-headsetet. Navigera genom scenen med rörelsekontrollerna för ditt headset eller prova att använda W, A, S, D på tangentbordet.    
Om du vill anpassa för Android och Oculus Go väljer du Android på menyn **Versionsinställningar**. Klicka först på **Växla mål** och sedan på **Skapa och kör**. Då distribueras exempelscenen till den anslutna Android-enheten. Information om Unity-utveckling för Android finns i [Unity-dokumentationen](https://docs.unity3d.com/Manual/android-GettingStarted.html).

![Anpassning för Android](media/TargetAndroid.png)  

## <a name="next-steps"></a>Nästa steg
* [Skapa ett Azure-konto](create-azure-account.md) för dina egna bakes
* Utforska [designprocessen](design-process.md)

