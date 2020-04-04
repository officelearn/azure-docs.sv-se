---
title: Microsoft Teams på Windows Virtual Desktop - Azure
description: Så här använder du Microsoft Teams på Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8b9b33076a2c2cea27fea181b760a721488682c9
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657007"
---
# <a name="use-microsoft-teams-on-windows-virtual-desktop"></a>Använda Microsoft Teams på Virtuellt Windows-skrivbord

> Gäller: Windows 10 och Windows 10 IoT Enterprise

Virtualiserade miljöer innebär en unik uppsättning utmaningar för samarbetsappar som Microsoft Teams, inklusive ökad latens, hög processoranvändning för värdar och dålig övergripande ljud- och videoprestanda. Om du vill veta mer om hur du använder Microsoft Teams i VDI-miljöer kan du läsa [Teams for Virtualized Desktop Infrastructure](https://docs.microsoft.com/microsoftteams/teams-for-vdi).

## <a name="prerequisites"></a>Krav

Innan du kan använda Microsoft Teams på Windows Virtual Desktop måste du göra följande:

- Installera [Windows Desktop-klienten](connect-windows-7-and-10.md) på en Windows 10-enhet som uppfyller [maskinvarukraven för](https://docs.microsoft.com/microsoftteams/hardware-requirements-for-the-teams-app)Microsoft Teams .
- Anslut till en virtuell dator med Windows 10 Multi-session eller Windows 10 Enterprise (VM).
- [Förbered nätverket](https://docs.microsoft.com/microsoftteams/prepare-network) för Microsoft Teams.

## <a name="use-unoptimized-microsoft-teams"></a>Använda ooptimeerade Microsoft Teams

Du kan använda ooptimeerade Microsoft Teams i Windows Virtual Desktop-miljöer för att utnyttja de fullständiga chatt- och samarbetsfunktionerna i Microsoft Teams samt ljudsamtal. Ljudkvaliteten i samtal varierar beroende på din värdkonfiguration eftersom ooptimeerade samtal använder mer av din värd-CPU.

### <a name="prepare-your-image-for-teams"></a>Förbered din bild för teams

Om du vill aktivera installation per dator ställer du in följande registernyckel på värden:

```shell
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Teams\IsWVDEnvironment]
  Type: REG_DWORD
  Value: 0x1
```

### <a name="install-microsoft-teams"></a>Installera Microsoft Teams

Du kan distribuera Teams-skrivbordsappen med hjälp av en installation per dator. Så här installerar du Microsoft Teams i windows virtual desktop-miljö:

1. Ladda ned [Teams MSI-paketet](https://docs.microsoft.com/microsoftteams/teams-for-vdi#deploy-the-teams-desktop-app-to-the-vm) som matchar din miljö. Vi rekommenderar att du använder 64-bitarsinstallationsprogrammet på ett 64-bitars operativsystem.
2. Kör det här kommandot för att installera MSI till värddatorn.

      ```shell
      msiexec /i <msi_name> /l*v < install_logfile_name> ALLUSERS=1
      ```

      Detta kommer att installera Teams till antingen programfiler eller programfiler (x86). Nästa gång du loggar in och startar Teams kommer appen att be om dina autentiseringsuppgifter.

      > [!NOTE]
      > Användare och administratörer kan inte inaktivera automatisk start för Teams under inloggningen just nu.

      Om du vill avinstallera MSI från värddatorn kör du det här kommandot:

      ```shell
      msiexec /passive /x <msi_name> /l*v <uninstall_logfile_name>
      ```

      > [!NOTE]
      > Om du installerar Teams med MSI-inställningen ALLUSERS=1 inaktiveras automatiska uppdateringar. Vi rekommenderar att du uppdaterar Teams minst en gång i månaden.
