---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 348f7bdd333da4f4a6cb41a438b7aee08d6a6bbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187805"
---
Beroende på klientens operativsystem är procedurerna för att fjärransluta till enheten olika.

### <a name="remotely-connect-from-a-windows-client"></a>Fjärransluta från en Windows-klient

Innan du börjar kontrollerar du att Windows-klienten kör Windows PowerShell 5.0 eller senare.

Följ dessa steg för att fjärransluta från en Windows-klient.

1. Kör en Windows PowerShell-session som administratör.
2. Kontrollera att Windows Remote Management-tjänsten körs på klienten. Skriv följande i kommandotolken:

    `winrm quickconfig`

3. Tilldela en variabel till enhetens IP-adress.

    $ip = "<device_ip>"

    Ersätt `<device_ip>` med enhetens IP-adress.

4. Om du vill lägga till ENHETENS IP-adress i klientens lista över betrodda värdar skriver du följande kommando:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Starta en Windows PowerShell-session på enheten:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. Ange lösenordet när du uppmanas att göra det. Använd samma lösenord som används för att logga in på det lokala webbgränssnittet. Standardlösenordet för lokalt webbgränssnitt är *Password1*. NÃ¤r du ã¶00 ansluter till enheten med fjÃ¤rrut powershell visas fÃ¶nde fÃ¶nde:  

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\WINDOWS\system32> winrm quickconfig
    WinRM service is already running on this machine.
    PS C:\WINDOWS\system32> $ip = "10.100.10.10"
    PS C:\WINDOWS\system32> Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    PS C:\WINDOWS\system32> Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell

    WARNING: The Windows PowerShell interface of your device is intended to be used only for the initial network configuration. Please engage Microsoft Support if you need to access this interface to troubleshoot any potential issues you may be experiencing. Changes made through this interface without involving Microsoft Support could result in an unsupported configuration.
    [10.100.10.10]: PS>
    ```

### <a name="remotely-connect-from-a-linux-client"></a>Fjärransluta från en Linux-klient

På Linux-klienten som du ska använda för att ansluta:

- [Installera den senaste PowerShell Core för Linux](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6) från GitHub för att få SSH-ommotsättningsfunktionen. 
- [Installera endast `gss-ntlmssp` paketet från NTLM-modulen](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). Använd följande kommando för Ubuntu-klienter:
    - `sudo apt-get install gss-ntlmssp`

Mer information finns i [PowerShell-omkreditering via SSH](https://docs.microsoft.com/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6).

Följ dessa steg för att fjärransluta från en NFS-klient.

1. Om du vill öppna PowerShell-sessionen skriver du:

    `sudo pwsh`
 
2. För anslutning med fjärrklienten skriver du:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    Ange lösenordet som används för att logga in på enheten när du uppmanas att göra det.
 
> [!NOTE]
> Den här proceduren fungerar inte på Mac OS.
