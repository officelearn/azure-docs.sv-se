---
title: Felsöka fel för Docker-klienten på Windows med hjälp av Visual Studio | Microsoft Docs
description: Felsöka problem som uppstår när du använder Visual Studio för att skapa och distribuera webbappar till Docker på Windows med hjälp av Visual Studio 2017.
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
ms.openlocfilehash: cd88dec2ad79ad9f4b4c004866060be86b777cd9
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2018
ms.locfileid: "49311192"
---
# <a name="troubleshoot-visual-studio-2017-development-with-docker"></a>Felsöka Visual Studio 2017-utveckling med Docker

När du arbetar med Visual Studio Tools for Docker kan det uppstå problem när du skapar eller felsöka ditt program. Nedan finns några vanliga åtgärder för felsökning.

## <a name="volume-sharing-is-not-enabled-enable-volume-sharing-in-the-docker-ce-for-windows-settings--linux-containers-only"></a>Delning av volymen har inte aktiverats. Aktivera volym delning i Docker CE för Windows-inställningar (endast Linux-behållare)

Att lösa problemet:

1. Högerklicka på **Docker för Windows** i meddelandefältet och välj sedan **inställningar**.
1. Välj **delade enheter** och dela systemenheten tillsammans med den enhet där projektet finns.

> [!NOTE]
> Om filer visas delade kan du fortfarande behöva klickar du på länken ”Återställ autentiseringsuppgifter...” längst ned i dialogrutan för att återaktivera delning av volymen. Om du vill fortsätta när du har återställt autentiseringsuppgifter, kan du behöva starta om Visual Studio.

![delade enheter](./media/vs-azure-tools-docker-troubleshooting-docker-errors/shareddrives.png)

## <a name="mounts-denied"></a>Monterar nekad

När du använder Docker för macOS, kan det uppstå ett fel som refererar till mappen /usr/local/share/dotnet/sdk/NuGetFallbackFolder. Lägga till mappen på fliken fildelning i Docker.

## <a name="unable-to-start-debugging"></a>Det går inte att starta felsökning

En orsak kan vara relaterade till att inaktuella felsökning komponenterna för din användarprofil. Kör följande kommandon för att ta bort dessa mappar så att de senaste felsökning komponenterna hämtas på nästa felsökningssessionen.

- del %userprofile%\vsdbg
- del %userprofile%\onecoremsvsmon

## <a name="errors-specific-to-networking-when-debugging-your-application"></a>Fel som är specifika för nätverk när du felsöker ditt program

Försök att köra skriptet nedladdningsbara från [Rensa behållare värden nätverk](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/master/windows-server-container-tools/CleanupContainerHostNetworking), vilket uppdaterar nätverksrelaterade-komponenter på värddatorn.


## <a name="microsoftdockertools-github-repo"></a>Microsoft/DockerTools GitHub-lagringsplatsen

Andra problem som uppstår, se [Microsoft/DockerTools](https://github.com/microsoft/dockertools/issues) problem.