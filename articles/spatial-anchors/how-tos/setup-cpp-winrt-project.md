---
title: Installera Azure spatiala ankare för ett C++/WinRT HoloLens-program
description: Konfigurera ett C++/WinRT HoloLens-projekt för att använda Azures ankare för spatialdata
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 43d5c1ae03c359dcbef21f8e7ba3205bc6ab0004
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096121"
---
# <a name="configuring-azure-spatial-anchors-in-a-cwinrt-hololens-project"></a>Konfigurera Azure spatiala ankare i ett C++/WinRT HoloLens-projekt

## <a name="requirements"></a>Krav

* [Visual Studio 2019](https://www.visualstudio.com/downloads/) installerat med **universell Windows-plattform utvecklings** arbets belastning och **Windows 10 SDK-komponenten (10.0.18362.0 eller senare)** .

## <a name="configuring-a-project"></a>Konfigurera ett projekt

Azures spatiala ankare för HoloLens-och C++-/WinRT distribueras med hjälp av [Microsoft. Azure. SpatialAnchors. WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) NuGet-paketet.

Följ anvisningarna [här](/nuget/consume-packages/install-use-packages-visual-studio) för att använda Visual Studios NuGet Package Manager för att installera [Microsoft. Azure. SpatialAnchors. WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) NuGet-paketet i projektet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gör så här: skapa och hitta ankare i C++/WinRT](./create-locate-anchors-cpp-winrt.md)