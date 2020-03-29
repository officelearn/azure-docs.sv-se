---
title: Windows-installation av Azure Security Center för IoT-agent | Microsoft-dokument
description: Lär dig mer om hur du installerar Azure Security Center för IoT-agent på 32- eller 64-bitars Windows-enheter.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: acc99f260931de7fd8c7566a3ff6daf43f34c5ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68597207"
---
# <a name="deploy-an-azure-security-center-for-iot-c-based-security-agent-for-windows"></a>Distribuera en Azure Security Center for IoT C#-baserad säkerhetsagent för Windows

Den här guiden förklarar hur du installerar Azure Security Center för IoT C#-baserad säkerhetsagent i Windows.

I den här guiden får du lära du dig att: 
> [!div class="checklist"]
> * Installera
> * Verifiera distributionen
> * Avinstallera agenten
> * Felsöka 

## <a name="prerequisites"></a>Krav

För andra plattformar och agent smaker, se [Välj rätt säkerhetsagent](how-to-deploy-agent.md).

1. Lokala administratörsrättigheter på den dator som du vill installera på. 

1. [Skapa en säkerhetsmodul](quickstart-create-security-twin.md) för enheten.

## <a name="installation"></a>Installation 

Om du vill installera säkerhetsagenten använder du följande arbetsflöde:

1. Installera Azure Security Center för IoT Windows C#-agenten på enheten. Hämta den senaste versionen till din dator från Azure Security Center för IoT [GitHub-databasen](https://github.com/Azure/Azure-IoT-Security-Agent-CS).

1. Extrahera innehållet i paketet och navigera till mappen /Install.

1. Öppna Windows PowerShell som administratör. 
1. Lägga till körbehörigheter i InstallSecurityAgent-skriptet genom att köra:<br>
    ```
    Unblock-File .\InstallSecurityAgent.ps1
    ```
    
    kör sedan:

    ```
    .\InstallSecurityAgent.ps1 -Install -aui <authentication identity> -aum <authentication method> -f <file path> -hn <host name> -di <device id> -cl <certificate location kind>
    ```
    
    Ett exempel:
    
    ```
    .\InstallSecurityAgent.ps1 -Install -aui Device -aum SymmetricKey -f c:\Temp\Key.txt -hn MyIotHub.azure-devices.net -di Mydevice1 -cl store
    ```
    
    Mer information om autentiseringsparametrar finns i [Konfigurera autentisering](concept-security-agent-authentication-methods.md).

Det här skriptet gör följande:

- Installerar förutsättningar.

- Lägger till en tjänstanvändare (med interaktiv inloggning inaktiverad).

- Installerar agenten som en **systemtjänst**.

- Konfigurerar agenten med de angivna autentiseringsparametrarna.


Om du vill ha ytterligare hjälp kan du använda kommandot Hämta hjälp i PowerShell <br>Exempel på hjälp:  
    ```Get-Help .\InstallSecurityAgent.ps1```

### <a name="verify-deployment-status"></a>Verifiera distributionsstatus

- Kontrollera agentdistributionsstatusen genom att köra:<br>
    ```sc.exe query "ASC IoT Agent"```

### <a name="uninstall-the-agent"></a>Avinstallera agenten

Så här avinstallerar du agenten:

1. Kör följande **PowerShell-skript** med parametern -mode inställd på **Avinstallera**.  

    ```
    .\InstallSecurityAgent.ps1 -Uninstall
    ``` 

## <a name="troubleshooting"></a>Felsökning

Om agenten inte startar aktiverar du loggning (loggning är *inaktiverat* som standard) för att få mer information.

Så här aktiverar du loggning:

1. Öppna konfigurationsfilen (General.config) för redigering med hjälp av en standardfilredigerare.

1. Redigera följande värden:

   ```xml
   <add key="logLevel" value="Debug" />
   <add key="fileLogLevel" value="Debug"/> 
   <add key="diagnosticVerbosityLevel" value="Some" /> 
   <add key="logFilePath" value="IoTAgentLog.log" />
   ```

    > [!NOTE]
    > Vi rekommenderar att **du** inaktiverar loggning när felsökningen är klar. Om du lämnar loggning **ökar** loggfilens storlek och dataanvändning. 

1. Starta om agenten genom att köra följande PowerShell eller kommandorad:

    **Powershell**
     ```
     Restart-Service "ASC IoT Agent"
     ```
     
   eller

    **Cmd**
     ```
     sc.exe stop "ASC IoT Agent" 
     sc.exe start "ASC IoT Agent" 
     ```

1. Läs loggfilen för mer information om felet.

   Loggfilsplats:`%WinDir%/System32/IoTAgentLog.log`


## <a name="next-steps"></a>Nästa steg
- Läs [översikt](overview.md) över Azure Security Center for IoT-tjänsten
- Läs mer om Azure Security Center för [IoT-arkitektur](architecture.md)
- Aktivera [tjänsten](quickstart-onboard-iot-hub.md)
- Läs [vanliga frågor och svar](resources-frequently-asked-questions.md)
- Förstå [aviseringar](concept-security-alerts.md)