---
title: Konfigurera fjärrrendering för enhet
description: Så här initierar du Azure Remote Rendering i ett Unity-projekt
author: jakrams
ms.author: jakras
ms.date: 02/27/2020
ms.topic: how-to
ms.openlocfilehash: 0415c0e7ee1432521c3cc2026feff5fc2a41d77e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681147"
---
# <a name="set-up-remote-rendering-for-unity"></a>Konfigurera fjärrrendering för enhet

För att aktivera Azure Remote Rendering (ARR) i Unity tillhandahåller vi dedikerade metoder som tar hand om vissa Unity-specifika aspekter.

## <a name="startup-and-shutdown"></a>Start och avstängning

Om du vill initiera `RemoteManagerUnity`fjärrrendering använder du . Den här klassen `RemoteManager` anropar den allmänna men implementerar redan Unity-specifika detaljer för dig. Unity använder till exempel ett specifikt koordinatsystem. När `RemoteManagerUnity.Initialize`du ringer kommer rätt konvention att inrättas. Samtalet kräver också att du tillhandahåller Unity-kameran som ska användas för att visa det fjärrrenerade innehållet.

```cs
// initialize Azure Remote Rendering for use in Unity:
// it needs to know which camera is used for rendering the scene
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);
```

Ring om du vill `RemoteManagerStatic.ShutdownRemoteRendering()`stänga av Fjärrrendering.

När `AzureSession` en har skapats och valts som den primära `RemoteManagerUnity`renderingssessionen måste den registreras med:

```cs
RemoteManagerUnity.CurrentSession = ...
```

### <a name="full-example-code"></a>Fullständig exempelkod

Koden nedan visar alla steg som behövs för att initiera Azure Remote Rendering i Unity:

```cs
// initialize Remote Rendering
RemoteUnityClientInit clientInit = new RemoteUnityClientInit(Camera.main);
RemoteManagerUnity.InitializeManager(clientInit);

// create a frontend
AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// ... fill out accountInfo ...
AzureFrontend frontend = new AzureFrontend(accountInfo);

// start a session
AzureSession session = await frontend.CreateNewRenderingSessionAsync(new RenderingSessionCreationParams(RenderingSessionVmSize.Standard, 0, 30)).AsTask();

// let RemoteManagerUnity know about the session we want to use
RemoteManagerUnity.CurrentSession = session;

session.ConnectToRuntime(new ConnectToRuntimeParams());

/// When connected, load and modify content

RemoteManagerStatic.ShutdownRemoteRendering();
```

## <a name="convenience-functions"></a>Bekvämlighetsfunktioner

### <a name="session-state-events"></a>Händelser i sessionstillstånd

`RemoteManagerUnity.OnSessionUpdate`avger händelser för när dess sessionstillstånd ändras, se koddokumentationen för mer information.

### <a name="arrserviceunity"></a>ARRServiceUnity

`ARRServiceUnity`är en valfri komponent för att effektivisera installation och sessionshantering. Den innehåller alternativ för att automatiskt stoppa sessionen när programmet avslutas eller uppspelningsläge avslutas i redigeraren, samt automatiskt förnya sessionen lånet när det behövs. Den cachelagrar data som sessionsegenskaperna (se dess `LastProperties` variabel) och visar händelser för sessionstillståndsändringar och sessionsfel.

Det kan inte finnas mer `ARRServiceUnity` än en instans i taget. Det är avsett för att komma igång snabbare genom att implementera några vanliga funktioner. För ett större program kan det vara bättre att göra dessa saker själv, dock.

Ett exempel på hur du `ARRServiceUnity` ställer in och använder se [Självstudiekurs: Konfigurera ett Unity-projekt från grunden](../../tutorials/unity/project-setup.md).

## <a name="next-steps"></a>Nästa steg

* [Installera fjärrrenderingspaketet för Unity](install-remote-rendering-unity-package.md)
* [Självstudiekurs: Ställa in ett Unity-projekt från grunden](../../tutorials/unity/project-setup.md)
