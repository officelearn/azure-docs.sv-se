---
title: Installera Azure spatiala ankare för enhets enhet
description: Konfigurera ett Unity-projekt för att använda Azures ankare för spatialdata
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 2330310b0bf4e165af71208477db128650a787cf
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2020
ms.locfileid: "95496959"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Konfigurera Azure spatiala ankare i ett Unity-projekt

Den här guiden visar hur du kommer igång med Azures spatiala ankare SDK i ditt Unity-projekt.

## <a name="requirements"></a>Krav

Azures spatiala ankare stöder för närvarande Unity 2019,4 (LTS) med följande konfigurationer.

* Unity 2019,4 med P.A. Foundation 3,1 stöds i Azure spatiala ankare 2.4.0 +.

## <a name="configuring-a-project"></a>Konfigurera ett projekt

### <a name="add-the-unity-package-manager-packages-to-your-project"></a>[Lägg till paket hanterarens paket hanterare i projektet](#tab/UPMPackage)

Azure spatiala ankare för Unity är för närvarande distribuerade med UPM-paket (Unit Package Manager). Du hittar dessa paket i vårt [NPM-register](https://bintray.com/microsoft/AzureMixedReality-NPM). Mer information om hur du arbetar med omfångs paket register i ett Unity-projekt finns i den officiella Unit-dokumentationen [här](https://docs.unity3d.com/Manual/upm-scoped.html).

#### <a name="add-the-registry-to-your-unity-project"></a>Lägg till registret i ditt Unity-projekt

1. Navigera till din Unity Project-mapp i Utforskaren `Packages` . Öppna projekt manifest filen `manifest.json` i en text redigerare.
2. Längst upp i filen, på samma nivå som `dependencies` avsnittet, lägger du till följande post för att ta med Azures ankare för spatialdata till ditt projekt. `scopedRegistries`Posten visar en enhet där du kan söka efter Azure spatial ankare SDK-paket.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-19&highlight=2-10)]

#### <a name="add-the-sdk-packages-to-your-unity-project"></a>Lägg till SDK-paketen i ditt Unity-projekt

| Plattform | Paketnamn                                    |
|----------|-------------------------------------------------|
| Android  | com. Microsoft. Azure. spatial-ankare – SDK. Android |
| iOS      | com. Microsoft. Azure. spatial-Anchors-SDK. iOS     |
| HoloLens | com. Microsoft. Azure. spatial-ankare – SDK. Windows |

1. För varje plattform (Android/iOS/HoloLens) som du vill stödja i projektet lägger du till en post med paket namnet och paket versionen i `dependencies` avsnittet i projekt manifestet. Nedan visas ett exempel.

    [!code-json[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-scoped-registry-setup.md?range=9-22&highlight=12-14)]

2. Spara och Stäng `manifest.json` filen. När du återgår till Unity ska Unity automatiskt identifiera projekt Manifestets ändring och hämta de angivna paketen. Du kan expandera `Packages` mappen i projektvyn för att kontrol lera att rätt paket har importer ATS.

#### <a name="android-only-configure-the-maintemplategradle-file"></a>Endast Android: Konfigurera filen mainTemplate. gradle

1. Gå till **Redigera**  >  **projekt inställnings**  >  **spelare**.
2. I **panelen kontrollant** för **Player-inställningar** väljer du **Android** -ikonen.
3. Under avsnittet **skapa** markerar du kryss rutan **anpassad mall för huvud Gradle** för att generera en anpassad Gradle-mall på `Assets\Plugins\Android\mainTemplate.gradle` .
4. Öppna `mainTemplate.gradle` filen i en text redigerare.
5. I `dependencies` avsnittet klistrar du in följande beroenden:

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

När allt är färdigt `dependencies` bör ditt avsnitt se ut ungefär så här:

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

### <a name="import-the-asset-package"></a>[Importera till gångs paketet](#tab/UnityAssetPackage)

> [!WARNING]
> Fördelningen av paket till gångs paket för Azures spatialdata SDK var inaktuell i version 2.5.0 och är inte längre tillgänglig från och med 2.6.0.

1. Ladda ned `AzureSpatialAnchors.unitypackage` filen för den version som du vill ska vara mål för [GitHub-versionerna](https://github.com/Azure/azure-spatial-anchors-samples/releases).
2. Följ instruktionerna [här](https://docs.unity3d.com/Manual/AssetPackagesImport.html) för att importera Unity Asset-paketet till projektet.

---

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gör så här: skapa och hitta ankare i Unity](./create-locate-anchors-unity.md)
