---
title: Installera Azure spatiala ankare för enhets enhet
description: Konfigurera ett Unity-projekt för att använda Azures ankare för spatialdata
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 07/31/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: a23cff8bcef2d62b1e415997ffd6afc80cf47793
ms.sourcegitcommit: 85eb6e79599a78573db2082fe6f3beee497ad316
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/05/2020
ms.locfileid: "87812336"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Konfigurera Azure spatiala ankare i ett Unity-projekt

## <a name="requirements"></a>Krav

Azures spatiala ankare stöder för närvarande Unity 2019,4 (LTS) med följande konfigurationer.

* Unity 2019,4 med P.A. Foundation 3,1 stöds i Azure spatiala ankare 2.4.0 +.

## <a name="configuring-a-project"></a>Konfigurera ett projekt

Azures spatialdata för enhets ankare distribueras för närvarande med ett Unity Asset-paket ( `.unitypackage` ) som finns i [GitHub-versionerna](https://github.com/Azure/azure-spatial-anchors-samples/releases).

### <a name="import-the-asset-package"></a>Importera till gångs paketet

1. Ladda ned `AzureSpatialAnchors.unitypackage` filen för den version som du vill ska vara mål för [GitHub-versionerna](https://github.com/Azure/azure-spatial-anchors-samples/releases).
2. Följ instruktionerna [här](https://docs.unity3d.com/Manual/AssetPackagesImport.html) för att importera Unity Asset-paketet till projektet.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gör så här: skapa och hitta ankare i Unity](./create-locate-anchors-unity.md)
