---
title: Installera Remote Rendering-paketet för Unity
description: Förklarar hur du installerar klient-DLL-filerna för fjärrstyrning för Unity
author: florianborn71
ms.author: flborn
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 86ffe47c009f9e0ae121ffc6ab57bff8fb73210f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681186"
---
# <a name="install-the-remote-rendering-package-for-unity"></a>Installera Remote Rendering-paketet för Unity

Azure fjärrrendering använder ett Unity-paket för att kapsla in integrationen i Unity.

## <a name="manage-the-remote-rendering-packages-in-unity"></a>Hantera de fjärranslutna åter givnings paketen i Unity

Unity-paket är behållare som kan hanteras via enhetens [paket hanterare](https://docs.unity3d.com/Packages/com.unity.package-manager-ui@1.8/manual/index.html).
Det här paketet innehåller hela C#-API: et och alla binärfiler för plugin-programmet som krävs för att använda Azure-fjärrrendering med Unity.
I följande enhets namngivnings schema för paket kallas paketet **com. Microsoft. Azure. Remote-rendering**.

Paketet ingår inte i [plats för arr-exempel](https://github.com/Azure/azure-remote-rendering)och är inte tillgängligt i enhetens interna paket register. Om du vill lägga till den i ett projekt måste du manuellt redigera projekt `manifest.md` filen för att lägga till följande:
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
När du har lagt till det kan du använda paket hanteraren för att se till att du har den senaste versionen.
Mer omfattande instruktioner finns i [självstudien: Konfigurera ett Unity-projekt från grunden](../../tutorials/unity/project-setup.md).

## <a name="unity-render-pipelines"></a>Unity Render-pipeline

Fjärrrendering fungerar med både den **universella åter givnings pipelinen** och **standard åter givnings pipelinen**. Av prestanda skäl rekommenderas den universella åter givnings pipelinen.

Om du vill använda **pipeline för universell Render**måste dess paket installeras i Unity. Detta kan antingen göras i enhetens **paket hanterings** gränssnitt (paket namn **Universal RP**, version 7.2.1 eller senare) eller via `Packages/manifest.json` filen, enligt beskrivningen i [själv studie kursen om installation av Unity-projekt](../../tutorials/unity/project-setup.md#configure-the-projects-manifest).

## <a name="next-steps"></a>Nästa steg

* [Unity Game-objekt och-komponenter](objects-components.md)
* [Självstudie: Konfigurera ett Unity-projekt från grunden](../../tutorials/unity/project-setup.md)
