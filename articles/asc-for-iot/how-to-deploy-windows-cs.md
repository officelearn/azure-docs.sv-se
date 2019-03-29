---
title: Windows-installationen av ASC för IoT-agenten förhandsversionen | Microsoft Docs
description: Läs mer om hur du installerar ASC för IoT-agenten på 32-bitars eller 64-bitars Windows-enheter.
services: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.service: ascforiot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2019
ms.author: mlottner
ms.openlocfilehash: 5c3293746fcc52570e708fd4bfab446981d49c24
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2019
ms.locfileid: "58621846"
---
# <a name="deploy-an-asc-for-iot-c-based-security-agent-for-windows"></a>Distribuera en ASC för IoT C#-baserade security-agenten för Windows

> [!IMPORTANT]
> ASC för IoT är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Den här guiden beskriver hur du installerar ASC för IoT C#-baserade säkerhetsagenten på Windows.

I den här guiden får du lära du dig att: 
> [!div class="checklist"]
> * Installera
> * Verifiera distributionen
> * Avinstallera agenten
> * Felsöka 

## <a name="prerequisites"></a>Förutsättningar

Andra plattformar och agenten smaksättningar finns i [väljer rätt säkerhetsagenten](how-to-deploy-agent.md).

1. Behörighet som lokal administratör på den datorn som du vill installera på. 

1. [Skapa en modul för maskinvarusäkerhet](quickstart-create-security-twin.md) för enheten.

## <a name="installation"></a>Installation 

Om du vill installera säkerhetsagenten, gör du följande:

1. Så här installerar du ASC för IoT Windows C# agenten på enheten, ladda ned den senaste versionen på din dator från ASC för IoT [GitHub-lagringsplatsen](https://github.com/Azure/Azure-IoT-Security-Agent-CS).

2. Extrahera innehållet i paketet och navigera till mappen/Install.

3. Öppna Windows PowerShell som administratör. 
    1. Lägg till som körs behörigheter genom att köra skriptet InstallSecurityAgent ```Unblock-File .\InstallSecurityAgent.ps1```
    
        och kör:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    Exempel:
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    Se [hur du konfigurerar autentisering](concept-security-agent-authentication-methods.md) för mer information om autentiseringsparametrar.

Skriptet gör följande:

- Installerar nödvändiga komponenter.

- Lägger till en Serviceanvändare (med interaktiv inloggning inaktiverad).

- Installerar agent som en **systemtjänsten**.

- Konfigurerar agenten med de angivna autentiseringsparametrar.


Mer hjälp kan du använda kommandot Get-Help i PowerShell <br>Get-Help exempel:  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Kontrollera status för distribution

- Kontrollera agentstatus för distributionen genom att köra:<br>
    ```sc.exe query "ASC IoT Agent" ```

### <a name="uninstall-the-agent"></a>Avinstallera agenten

Avinstallera agenten:

1. Kör följande PowerShell-skript med den **-läge** parameteruppsättning till **avinstallera**.  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>Felsökning

Om agenten inte kan starta, aktivera loggning (loggning är *av* som standard) vill veta mer.

Aktivera loggning:

1. Öppna konfigurationsfilen (General.config) för redigering med en standardfil-redigerare.

1. Redigera följande värden:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/> 
   <add key="diagnosticVerbosityLevel" value="Some" /> 
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > Vi rekommenderar att aktivera loggning **av** när felsökningen har slutförts. Lämna loggning **på** ökar logga användning av storlek och data. 

1. Starta om agenten genom att köra följande PowerShell-kommandoraden:

    **PowerShell**
     ```
     Restart-Service "ASC IoT Agent"
     ```
     
   eller

    **CMD**
     ```
     sc.exe stop "ASC IoT Agent" 
     sc.exe start "ASC IoT Agent" 
     ```

1. Mer information om felet finns i loggfilen.

   Plats för loggfil: `%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>Nästa steg
- Läsa ASC för IoT-tjänsten [översikt](overview.md)
- Läs mer om ASC för IoT [arkitektur](architecture.md)
- Aktivera den [service](quickstart-onboard-iot-hub.md)
- Läs den [vanliga frågor och svar](resources-frequently-asked-questions.md)
- Förstå [aviseringar](concept-security-alerts.md)