---
title: Så här fjärrfelsöker din kod med Azure Dev Spaces fungerar
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Beskriver processerna för fjärrfelsökning på Azure Kubernetes-tjänsten med Azure Dev Spaces
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, behållare
ms.openlocfilehash: a9f303ea6f954285a319be137a594f7ce127f740
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241405"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>Så här fjärrfelsöker din kod med Azure Dev Spaces fungerar

Azure Dev Spaces ger dig flera sätt att snabbt iterera och felsöka Kubernetes-program och samarbeta med ditt team i ett AKS-kluster (Azure Kubernetes Service). När projektet körs i ett utvecklingsutrymme är Azure Dev Spaces ett sätt att koppla till och felsöka ett program som körs i AKS.

I den här artikeln beskrivs hur fjärrfelsökning med Dev Spaces fungerar.

## <a name="debug-your-code"></a>Felsöka koden

För Java-, .NET Core- och Node.js-program kan du felsöka programmet som körs direkt i ditt utvecklingsutrymme med Visual Studio-kod eller Visual Studio. Visual Studio Code och Visual Studio tillhandahåller verktyg för att ansluta till ditt utvecklingsutrymme, starta programmet och bifoga en felsökare. När `azds prep`du har kört kan du öppna projektet i Visual Studio Code eller Visual Studio. Visual Studio Code eller Visual Studio genererar sina egna konfigurationsfiler för anslutning som är åtskilda från att köras `azds prep`. Inifrån Visual Studio Code eller Visual Studio kan du ange brytpunkter och starta programmet till ditt utvecklingsutrymme.

![Felsöka koden](media/get-started-node/debug-configuration-nodejs2.png)

När du startar programmet med Visual Studio Code eller Visual Studio för felsökning hanterar de start och `azds up`anslutning till ditt utvecklingsutrymme på samma sätt som att köra . Dessutom ger klientverktyget i Visual Studio Code och Visual Studio var och en ytterligare en parameter med specifik information för felsökning. Parametern innehåller namnet på felsökningsavbildningen, platsen för felsökningsfelaren i felsökningsavbildningen och målplatsen i programmets behållare för att montera felsökningsmappen.

Felsökningsavbildningen bestäms automatiskt av verktygen på klientsidan. Den använder en metod som liknar den som används under `azds prep`Dockerfile och Helm diagram generera när du kör . När felsökningsprogrammet har monterats i programmets avbildning `azds exec`körs den med .

## <a name="next-steps"></a>Nästa steg

Information om hur du kommer igång med Azure Dev Spaces för fjärrfelsökning av projektet finns i följande snabbstarter:

* [Snabbt iterera och felsöka med Visual Studio Code och Java][quickstart-java]
* [Snabbt iterera och felsöka med Visual Studio-kod och .NET][quickstart-netcore]
* [Snabbt iterera och felsöka med Visual Studio-kod och Node.js][quickstart-node]
* [Snabbt iterera och felsöka med Visual Studio och .NET Core][quickstart-vs]


[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md
