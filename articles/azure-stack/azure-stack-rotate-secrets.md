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
ms.openlocfilehash: 0a4118a8927e4261fafa307af5b9c29623ce5c3f
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/13/2018
---
# <a name="rotate-secrets-in-azure-stack"></a>Rotera hemligheter i Azure-stacken

*Gäller för: Azure Stack integrerat system*

Uppdatera ditt lösenord för Azure Stack-komponenter på en vanlig takt.

## <a name="updating-the-passwords-for-the-baseboard-management-controller-bmc"></a>Uppdatering av lösenord för den hanteringsstyrenhet för baskort (BMC)

Huvudkortshanteringskontroller (BMC) övervaka det fysiska tillståndet för dina servrar. Specifikationer och instruktioner om hur du uppdaterar lösenordet för BMC variera beroende på maskinvaruleverantören OEM-tillverkaren (OEM).

1. Uppdatera bmc-Styrenheten på fysiska servrar för Azure-stacken genom att följa OEM-instruktioner. Lösenord för varje BMC i din miljö måste vara samma.
2. Öppna en privilegierad slutpunkt i Azure Stack-sessioner. Anvisningar finns i avsnittet [med Privilegierade slutpunkten i Azure-stacken](azure-stack-privileged-endpoint.md).
3. När din PowerShell prompten har ändrats till **[IP-adress eller ERCS VM name]: PS >** eller **[azs ercs01]: PS >**, beroende på miljön, kör `Set-BmcPassword` genom att köra `invoke-command`. Skicka dina Privilegierade endpoint sessionsvariabeln som en parameter.  
Exempel:
    ```powershell
    $PEPSession = New-PSSession -ComputerName <ERCS computer name> -Credential <CloudAdmin credential> -ConfigurationName "PrivilegedEndpoint"  
    
    Invoke-Command -Session $PEPSession -ScriptBlock {
        param($password)
        set-bmcpassword -bmcpassword $password
    } -ArgumentList (<LatestPassword as a SecureString>) 
    ```

## <a name="next-steps"></a>Nästa steg

Mer information om säkerhet och Azure-stacken finns [Azure Stack infrastruktur säkerhetstillståndet](azure-stack-security-foundations.md).