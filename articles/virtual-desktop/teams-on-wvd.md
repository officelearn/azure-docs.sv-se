---
title: Microsoft Teams på Windows Virtual Desktop – Azure
description: Använda Microsoft Teams på Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a860b005457c6e02187423a3ffbbc63fe7c758b1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187536"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Använd Microsoft Teams på Windows Virtual Desktop

> Gäller för: Windows 10 och Windows 10 IoT Enterprise

Virtualiserade miljöer presenterar en unik uppsättning utmaningar för Collaboration-appar som Microsoft-team, inklusive ökad latens, hög värd processor användning och dåliga övergripande ljud-och video prestanda. Om du vill veta mer om hur du använder Microsoft Teams i VDI-miljöer kan du kolla in [Teams för virtualiserad Desktop-infrastruktur](https://docs.microsoft.com/microsoftteams/teams-for-vdi).

## <a name="prerequisites"></a>Krav

Innan du kan använda Microsoft Teams på Windows Virtual Desktop måste du göra följande saker:

- Installera [Windows Skriv bords klient](connect-windows-7-and-10.md) på en Windows 10-enhet som uppfyller [maskin varu kraven](https://docs.microsoft.com/microsoftteams/hardware-requirements-for-the-teams-app)för Microsoft Teams.
- Anslut till en virtuell Windows 10-dator med flera sessioner eller Windows 10 Enterprise (VM).
- [Förbered ditt nätverk](https://docs.microsoft.com/microsoftteams/prepare-network) för Microsoft-team.

## <a name="use-unoptimized-microsoft-teams"></a>Använd ej optimerade Microsoft-team

Du kan använda Microsoft Teams i dina Windows-miljöer för virtuella skriv bord för att utnyttja chatt-och samarbets funktionerna i Microsoft Teams. Windows Virtual Desktop stöder inte Teams för VDI-ljud/video-optimeringar (AV). Anrop och möten stöds inte. Om din organisations principer tillåter kan du fortfarande göra ljud samtal och ansluta till möten med ljud, men optimering av ljud kvalitet som inte optimerats varierar beroende på värd konfigurationen och kanske inte är tillförlitlig. Om du vill veta mer kan du titta [på team om prestanda för VDI-prestanda](https://docs.microsoft.com/microsoftteams/teams-for-vdi#teams-on-vdi-performance-considerations).

### <a name="prepare-your-image-for-teams"></a>Förbered din avbildning för team

Om du vill aktivera installation av team per dator anger du följande register nyckel på värden:

```shell
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams\IsWVDEnvironment]
  Type: REG_DWORD
  Value: 1
```

### <a name="install-microsoft-teams"></a>Installera Microsoft Teams

Du kan distribuera Skriv bords appen för team med en installation per dator. Så här installerar du Microsoft Teams i din Windows Virtual Desktop-miljö:

1. Ladda ned [Teams MSI-paketet](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) som matchar din miljö. Vi rekommenderar att du använder 64-bitars installations programmet på ett 64-bitars operativ system.
2. Kör det här kommandot för att installera MSI på den virtuella värddatorn.

      ```shell
      msiexec /i <msi_name> /l*v < install_logfile_name> ALLUSER=1
      ```

      Detta kommer att installera Teams till antingen programfiler eller programfiler (x86). Nästa gången du loggar in och startar team ber appen om dina autentiseringsuppgifter.

      > [!NOTE]
      > Användare och administratörer kan inte inaktivera automatisk start för team under inloggningen just nu.

      Kör det här kommandot om du vill avinstallera MSI från den virtuella värddatorn:

      ```shell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      > [!NOTE]
      > Om du installerar team med MSI-inställningen ALLUSER = 1 kommer automatiska uppdateringar att inaktive ras. Vi rekommenderar att du uppdaterar team minst en gång i månaden. Om du vill veta mer om hur du distribuerar Teams Desktop-appen kan du läsa [distribuera Skriv bords appen till den virtuella datorn](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm).

## <a name="customize-remote-desktop-protocol-properties-for-a-host-pool"></a>Anpassa Remote Desktop Protocol egenskaper för en värd pool
Om du anpassar en värd Pools Remote Desktop Protocol egenskaper (RDP), till exempel flera skärmar, aktiverar mikrofon och omdirigering av ljud kan du leverera en optimal upplevelse för dina användare utifrån deras behov. Du kan anpassa RDP-egenskaperna i Windows Virtual Desktop med parametern **-CustomRdpProperty** i cmdleten **set-RdsHostPool** .
Se [Inställningar för RDP-filer som stöds](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/rdp-files?context=/azure/virtual-desktop/context/context) för en fullständig lista över vilka egenskaper som stöds och deras standardvärden.
