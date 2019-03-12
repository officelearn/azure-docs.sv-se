---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/06/2019
ms.author: alkohli
ms.openlocfilehash: 348f7bdd333da4f4a6cb41a438b7aee08d6a6bbb
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555178"
---
Procedurerna för att fjärransluta till enheten skiljer sig beroende på klientens operativsystem.

### <a name="remotely-connect-from-a-windows-client"></a>Ansluta via en fjärranslutning från en Windows-klient

Innan du börjar måste du kontrollera att din Windows-klienten kör Windows PowerShell 5.0 eller senare.

Följ dessa steg för att ansluta via en fjärranslutning från en Windows-klient.

1. Köra en Windows PowerShell-session som administratör.
2. Kontrollera att Windows Remote Management-tjänsten körs på klienten. Skriv följande i kommandotolken:

    `winrm quickconfig`

3. Tilldela en variabel till enhetens IP-adress.

    $ip = "<device_ip>"

    Ersätt `<device_ip>` med IP-adressen för din enhet.

4. Om du vill lägga till IP-adressen för din enhet i listan över betrodda värdar för klientens, skriver du följande kommando:

    `Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force`

5. Starta en Windows PowerShell-session på enheten:

    `Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell`

6. Ange lösenordet när du tillfrågas. Använd samma lösenord som används för att logga in på det lokala webbgränssnittet. Standardlösenordet lokala Användargränssnittet är *Password1*. När du har anslutit till enheten med fjärr-PowerShell, se följande exempel på utdata:  

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

### <a name="remotely-connect-from-a-linux-client"></a>Ansluta via en fjärranslutning från en Linux-klient

På klienten för Linux som du använder för att ansluta:

- [Installera den senaste PowerShell Core för Linux](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-linux?view=powershell-6) från GitHub för att hämta SSH-fjärrkommunikationsfunktionen. 
- [Installera endast de `gss-ntlmssp` paket från modulen NTLM](https://github.com/Microsoft/omi/blob/master/Unix/doc/setup-ntlm-omi.md). För Ubuntu-klienter, använder du följande kommando:
    - `sudo apt-get install gss-ntlmssp`

Mer information går du till [PowerShell fjärrkommunikation via SSH](https://docs.microsoft.com/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-6).

Följ dessa steg för att ansluta via en fjärranslutning från en NFS-klient.

1. Öppna PowerShell-session, skriver du:

    `sudo pwsh`
 
2. För att ansluta med hjälp av den fjärranslutna klienten, skriver du:

    `Enter-PSSession -ComputerName $ip -Authentication Negotiate -ConfigurationName Minishell -Credential ~\EdgeUser`

    När du uppmanas, anger du lösenordet som används för att logga in på din enhet.
 
> [!NOTE]
> Den här proceduren fungerar inte på Mac OS.
