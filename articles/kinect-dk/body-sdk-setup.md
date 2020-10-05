---
title: Snabb start – konfigurera innehålls spårning i Azure Kinect
description: I den här snabb starten ska du konfigurera Body tracking SDK för Azure Kinect.
author: qm13
ms.author: quentinm
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: Kinect, Azure, sensor, åtkomst, djup, SDK, brödtext, spårning, gemensamt, installations program, CUDA, NVIDIA
ms.openlocfilehash: 2cf4c1097730f88fc4bd66c28e1bdddd7fea8640
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "85277984"
---
# <a name="quickstart-set-up-azure-kinect-body-tracking"></a>Snabb start: Konfigurera Azure Kinect Body tracking

Den här snabb starten vägleder dig genom processen för att få text spårning igång på din Azure Kinect DK.

## <a name="system-requirements"></a>Systemkrav

För Body tracking SDK krävs en NVIDIA-GPU som är installerad på värddatorn. Det rekommenderade dator kravet för text spårnings värden beskrivs på sidan [system krav](system-requirements.md) .

## <a name="install-software"></a>Installera programvara

### <a name="install-the-latest-nvidia-driver"></a>[Installera den senaste NVIDIA-drivrutinen](https://www.nvidia.com/Download/index.aspx?lang=en-us)

Hämta och installera den senaste NVIDIA-drivrutinen för grafik kortet. Äldre driv rutiner kanske inte är kompatibla med CUDA-binärfilerna distribueras om med Body tracking SDK.

### <a name="visual-c-redistributable-for-visual-studio-2015"></a>[Visual C++ Redistributable för Visual Studio 2015](https://www.microsoft.com/en-us/download/details.aspx?id=48145)

Hämta och installera Visual C++ Redistributable för Visual Studio 2015. 

## <a name="set-up-hardware"></a>Konfigurera maskinvara

### <a name="set-up-azure-kinect-dk"></a>[Konfigurera Azure Kinect DK](set-up-azure-kinect-dk.md)

Starta [Azure Kinect Viewer](azure-kinect-viewer.md) för att kontrol lera att din Azure Kinect DK har kon figurer ATS korrekt.

## <a name="download-the-body-tracking-sdk"></a>Ladda ned SDK för kroppsspårning
 
1. Välj länken för att [Ladda ned Body tracking SDK](body-sdk-download.md)
2. Installera Body tracking SDK på din dator.

## <a name="verify-body-tracking"></a>Verifiera innehålls spårning

Starta **Azure Kinect Body tracking Viewer** för att kontrol lera att Body tracking SDK har kon figurer ATS korrekt. Visnings programmet installeras med SDK msi-installationsprogrammet. Du hittar den på Start-menyn eller på `<SDK Installation Path>\tools\k4abt_simple_3d_viewer.exe` .

Om du inte har en kraftfull tillräckligt stor GPU och fortfarande vill testa resultatet kan du starta **Azure Kinect Body tracking Viewer** på kommando raden med följande kommando: `<SDK Installation Path>\tools\k4abt_simple_3d_viewer.exe CPU`

Om allt är korrekt konfigurerat visas ett fönster med ett moln och spårade organ i 3D-läge.


![Brödtext som spårar 3D Viewer](./media/quickstarts/samples-simple3dviewer.png)

## <a name="examples"></a>Exempel

Du hittar exemplen på hur du använder Body tracking SDK [här](https://github.com/microsoft/Azure-Kinect-Samples/tree/master/body-tracking-samples).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
>[Bygg din första kroppsspårningsapp](build-first-body-app.md)

