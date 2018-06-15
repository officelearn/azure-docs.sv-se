---
title: Felsökning Docker-klienten i Windows med hjälp av Visual Studio | Microsoft Docs
description: Felsöka problem som kan uppstå när du använder Visual Studio för att skapa och distribuera webbprogram till Docker i Windows med hjälp av Visual Studio 2017.
services: azure-container-service
documentationcenter: na
author: devinb
manager: douge
editor: ''
ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 10/13/2017
ms.author: devinb
ms.openlocfilehash: 90dd5df4a607568e2f3a60791da2948af7ce4e50
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
ms.locfileid: "24002938"
---
# <a name="troubleshoot-visual-studio-2017-development-with-docker"></a>Felsökning av Visual Studio 2017 utveckling med Docker

När du arbetar med Visual Studio Tools för Docker, kan det uppstå problem när du skapar eller felsöka ditt program. Nedan finns några vanliga åtgärder för felsökning.

## <a name="volume-sharing-is-not-enabled-enable-volume-sharing-in-the-docker-ce-for-windows-settings--linux-containers-only"></a>Delning av volymen har inte aktiverats. Aktivera volym delning i Docker CE för Windows-inställningar (endast Linux-behållare)

Så här löser du problemet:

1. Högerklicka på **Docker för Windows** i meddelandefältet och välj sedan **inställningar**.
1. Välj **delade enheter** och dela systemenheten tillsammans med enheten där projektet finns.

> [!NOTE]
> Om filerna visas delade, kan du fortfarande behöver klickar du på länken ”Återställ autentiseringsuppgifter...” längst ned i dialogrutan för att återaktivera delning av volymen.

![delade enheter](./media/vs-azure-tools-docker-troubleshooting-docker-errors/shareddrives.png)

## <a name="unable-to-start-debugging"></a>Det gick inte att starta felsökning

En orsak kan vara relaterat till att inaktuella felsökning komponenterna i din mapp. Kör följande kommandon för att ta bort dessa mappar så att de senaste felsökning komponenterna hämtas på nästa felsökningssessionen.

- del %userprofile%\vsdbg
- del %userprofile%\onecoremsvsmon

## <a name="errors-specific-to-networking-when-debugging-your-application"></a>Fel som är specifika för nätverk när du felsöker ditt program

Försök köra skript som kan hämtas på [Rensa behållaren värden nätverk](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/master/windows-server-container-tools/CleanupContainerHostNetworking), som uppdateras av nätverksrelaterade komponenterna på värddatorn.


## <a name="microsoftdockertools-github-repo"></a>Microsoft/DockerTools GitHub-repo

Andra problem som uppstå finns [Microsoft/DockerTools](https://github.com/microsoft/dockertools/issues) problem.