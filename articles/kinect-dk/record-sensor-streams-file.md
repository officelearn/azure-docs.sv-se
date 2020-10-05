---
title: Snabb start – spela in Azure Kinect sensor strömmar till en fil
description: I den här snabb starten får du lära dig hur du registrerar data strömmar från sensorn SDK till en fil.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: Azure, Kinect, Record, Play, Reader, Matroska, MKV, streams, djup, RGB, kamera, färg, IMU, ljud, sensor
ms.openlocfilehash: 3dab147b593bf012bd6cd9c95d0195e84a2cbcf1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "85277977"
---
# <a name="quickstart-record-azure-kinect-sensor-streams-to-a-file"></a>Snabb start: spela in Azure Kinect sensor strömmar till en fil

Den här snabb starten innehåller information om hur du kan använda [Azure Kinect Recorder](azure-kinect-recorder.md) -verktyget för att registrera data strömmar från sensorn SDK till en fil.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Den här snabb starten förutsätter:

- Du har Azure Kinect DK ansluten till värddatorn och påslagen korrekt.
- Du är färdig med [konfigurationen](set-up-azure-kinect-dk.md) av maskin varan.

## <a name="create-recording"></a>Skapa inspelning

1. Öppna en kommando tolk och ange sökvägen till [Azure Kinect-inspelaren](azure-kinect-recorder.md), som finns i katalogen installerade verktyg som `k4arecorder.exe` . Till exempel: `C:\Program Files\Azure Kinect SDK\tools\k4arecorder.exe`.
2. Spela in 5 sekunder.

    `k4arecorder.exe -l 5 %TEMP%\output.mkv`

3. Som standard använder inspelaren NFOV Unbinned djup läge och utvärderar 1080p RGB vid 30 fps, inklusive IMU data. En fullständig översikt över inspelnings alternativ och tips finns i [Azure Kinect Recorder](azure-kinect-recorder.md).

## <a name="play-back-recording"></a>Spela upp inspelning

Du kan använda [Azure Kinect Viewer](azure-kinect-viewer.md) för att spela upp en inspelning.

1. Lansera [`k4aviewer.exe`](azure-kinect-viewer.md)
2. Unfold fliken **Öppna inspelning** och öppna din inspelning.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du registrerar sensor strömmar till en fil, är det dags att

> [!div class="nextstepaction"]
> [Skapa din första Azure Kinect-app](build-first-app.md)
