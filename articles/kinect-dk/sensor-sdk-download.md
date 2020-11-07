---
title: Ladda ned Azure Kinect sensor SDK
description: Lär dig hur du hämtar och installerar Azure Kinect sensor SDK i Windows och Linux.
author: Brent-A
ms.author: brenta
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Azure, Kinect, SDK, Hämta uppdatering, senaste, tillgänglig, installera
ms.openlocfilehash: 48a3df3962796cf561057e8ec85b754c974200f4
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358513"
---
# <a name="azure-kinect-sensor-sdk-download"></a>Ladda ned Azure Kinect sensor SDK

Den här sidan innehåller nedladdnings länkarna för varje version av Azure Kinect sensor SDK. Installations programmet innehåller alla filer som krävs för att utveckla för Azure-Kinect.

## <a name="azure-kinect-sensor-sdk-contents"></a>Azure Kinect sensor SDK-innehåll

- Sidhuvud och bibliotek för att bygga ett program med hjälp av Azure Kinect DK.
- Distribuerbara DLL-filer som krävs av program med hjälp av Azure Kinect DK.
- [Azure Kinect Viewer](azure-kinect-viewer.md).
- [Azure Kinect-inspelaren](azure-kinect-recorder.md).
- [Verktyget för inbyggd Azure Kinect-programvara](azure-kinect-firmware-tool.md).

## <a name="windows-download-link"></a>Länk till Windows-nedladdning

[Microsoft Installer](https://download.microsoft.com/download/3/d/6/3d6d9e99-a251-4cf3-8c6a-8e108e960b4b/Azure%20Kinect%20SDK%201.4.1.exe)  |  [GitHub käll kod](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/issues/1093)

> [!NOTE]
> När du installerar SDK måste du komma ihåg vilken sökväg du installerar till. Till exempel "C:\Program Files\Azure Kinect SDK 1,2". Du hittar de verktyg som refereras till i artiklar i den här sökvägen.

Du kan hitta tidigare versioner av Azure Kinect sensor SDK och inbyggd program vara på [GitHub](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

## <a name="linux-installation-instructions"></a>Installations anvisningar för Linux

För närvarande är den enda distribution som stöds Ubuntu 18,04. Information om hur du begär stöd för andra distributioner finns på [den här sidan](https://aka.ms/azurekinectfeedback).

Först måste du konfigurera [Microsofts paket lagrings plats](https://packages.microsoft.com/)genom att följa anvisningarna [här](/windows-server/administration/linux-package-repository-for-microsoft-software).

Nu kan du installera de nödvändiga paketen. `k4a-tools`Paketet innehåller [Azure Kinect Viewer](azure-kinect-viewer.md), [Azure Kinect-inspelaren](record-sensor-streams-file.md)och verktyget för [inbyggd Azure Kinect-programvara](azure-kinect-firmware-tool.md). Kör för att installera den

 `sudo apt install k4a-tools`

 `libk4a<major>.<minor>-dev`Paketet innehåller de huvuden-och cmake-filer som ska byggas `libk4a` .
`libk4a<major>.<minor>`Paketet innehåller de delade objekt som krävs för att köra körbara filer som är beroende av `libk4a` .

 De grundläggande självstudierna kräver `libk4a<major>.<minor>-dev` paketet. Kör för att installera den

 `sudo apt install libk4a1.1-dev`

Om kommandot lyckas är SDK klart för användning.

## <a name="change-log-and-older-versions"></a>Ändrings logg och äldre versioner

Du kan hitta ändrings loggen för Azure Kinect sensor SDK [här](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/CHANGELOG.md).

Om du behöver en äldre version av Azure Kinect sensor SDK hittar du den [här](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/blob/develop/docs/usage.md).

## <a name="next-steps"></a>Nästa steg

[Konfigurera Azure Kinect DK](set-up-azure-kinect-dk.md)