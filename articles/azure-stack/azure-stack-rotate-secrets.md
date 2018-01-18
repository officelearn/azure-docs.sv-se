---
title: Rotera hemligheter i Azure-stacken | Microsoft Docs
description: "Lär dig mer om att rotera din hemliga i Azure-stacken."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 49071044-6767-4041-9EDD-6132295FA551
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/08/2018
ms.author: mabrigg
ms.openlocfilehash: e2e9d93af3889714ade1d0364a6f747c184e6d75
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2018
---
# <a name="rotate-secrets-in-azure-stack"></a>Rotera hemligheter i Azure-stacken

*Gäller för: Azure Stack integrerat system*

Uppdatera ditt lösenord för Azure Stack-komponenter på en vanlig takt.

## <a name="updating-the-passwords-for-the-baseboard-management-controller-bmc"></a>Uppdatering av lösenord för den hanteringsstyrenhet för baskort (BMC)

Huvudkortshanteringskontroller (BMC) övervaka det fysiska tillståndet för dina servrar. Specifikationer och instruktioner om hur du uppdaterar lösenordet för BMC variera beroende på maskinvaruleverantören OEM-tillverkaren (OEM).

1. Uppdatera bmc-Styrenheten på fysiska servrar för Azure-stacken genom att följa OEM-instruktioner. Lösenord för varje BMC i din miljö måste vara samma.
2. Öppna en privilegierad slutpunkt i Azure Stack-sessioner. Anvisningar finns i avsnittet [med Privilegierade slutpunkten i Azure-stacken](azure-stack-privileged-endpoint.md).
3. När din PowerShell prompten har ändrats till **[IP-adress eller ERCS VM name]: PS >** eller **[azs ercs01]: PS >**, beroende på miljön, kör `Set-BmcPassword` genom att köra `invoke-command`. Skicka dina Privilegierade endpoint sessionsvariabeln som en parameter. Exempel:

    ```powershell
    # Interactive Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PECred = Get-Credential "<Domain>\CloudAdmin" -Message "PE Credentials" 
    $NewBMCpwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    ```
    
    Du kan också använda statiska PowerShell-version med lösenord som kodraderna:
    
    ```powershell
    # Static Version
    $PEip = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEUser = "<Privileged Endpoint user for exmaple Domain\CloudAdmin>"
    $PEpwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PECred = New-Object System.Management.Automation.PSCredential ($PEUser, $PEpwd) 
    $NewBMCpwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force 

    $PEPSession = New-PSSession -ComputerName $PEip -Credential $PECred -ConfigurationName "PrivilegedEndpoint" 

    Invoke-Command -Session $PEPSession -ScriptBlock {
        Set-Bmcpassword -bmcpassword $using:NewBMCpwd
    }
    ```

## <a name="next-steps"></a>Nästa steg

Mer information om säkerhet och Azure-stacken finns [Azure Stack infrastruktur säkerhetstillståndet](azure-stack-security-foundations.md).
