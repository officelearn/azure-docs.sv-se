---
title: Installera fjärrrenderingspaketet för Unity
description: Förklarar hur du installerar DLL-klientens DLL-filer för fjärrrendering för Unity
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 86ffe47c009f9e0ae121ffc6ab57bff8fb73210f
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681186"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Installera fjärrrenderingspaketet för Unity

Azure Remote Rendering använder ett Unity-paket för att kapsla in integreringen i Unity.

## <a name="manage-the-remote-rendering-packages-in-unity"></a>Hantera fjärrrenderingspaketen i Unity

Unity-paket är behållare som kan hanteras via Unitys [Package Manager.](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html)
Det här paketet innehåller hela C#API samt alla plugin-binärfiler som krävs för att använda Azure Remote Rendering med Unity.
Efter Unitys namngivningsschema för paket kallas paketet **com.microsoft.azure.remote-rendering**.

Paketet är inte en del av [ARR-exempeldatabasen](https://github.com/Azure/azure-remote-rendering)och den är inte tillgänglig från Unitys interna paketregister. Om du vill lägga till den i ett projekt `manifest.md` måste du redigera projektets fil manuellt för att lägga till följande:
```json
{
  "scopedRegistries": [
    {
      "name": "Azure Mixed Reality Services",
      "url": "https://api.bintray.com/npm/microsoft/AzureMixedReality-NPM/",
      "scopes": ["com.microsoft.azure"]
    }
   ],
  "dependencies": {
    "com.microsoft.azure.remote-rendering": "0.1.11",
    ...existing dependencies...
  }
}
```
När detta har lagts till kan du använda Unity Package Manager för att se till att du har den senaste versionen.
Mer omfattande instruktioner ges i [handledningen: Inrätta en Unity projekt från grunden](../../tutorials/unity/project-setup.md).

## <a name="unity-render-pipelines"></a>Unity gör rörledningar

Fjärrrendering fungerar med både **universalreningspipelinen** och **standardrendepipelinen**. Av prestandaskäl rekommenderas universalåtergivningspipelinen.

Om du vill använda **den universella renderingspipelinen**måste dess paket installeras i Unity. Detta kan antingen göras i Unity's **Package Manager** UI (paketnamn **Universal RP**, version 7.2.1 eller nyare), eller genom `Packages/manifest.json` filen, som beskrivs i Unity projektet setup [handledning](../../tutorials/unity/project-setup.md#configure-the-projects-manifest).

## <a name="next-steps"></a>Nästa steg

* [Unity spel objekt och komponenter](objects-components.md)
* [Självstudiekurs: Ställa in ett Unity-projekt från grunden](../../tutorials/unity/project-setup.md)
