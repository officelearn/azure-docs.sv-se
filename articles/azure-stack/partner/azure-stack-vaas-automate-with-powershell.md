---
title: Automatisera Azure Stack-verifiering med PowerShell | Microsoft Docs
description: Du kan automatisera Azure Stack-verifiering med PowerShell.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7f9a4ce4f1e16f69a1d8998e24c1bfe955d17d92
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57767115"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>Automatisera Azure Stack-verifiering med PowerShell

Verifiering som en tjänst (VaaS) gör möjligheten att automatisera starta tester med hjälp av den **LaunchVaaSTests.ps1** skript.

> [!NOTE]  
> Automation är endast tillgänglig för testet arbetsflödet. Verifiera paketet och lösningen validering arbetsflöden stöds endast via VaaS-portalen.

Det här skriptet kan användas för att:

> [!div class="checklist"]
> * Installationskrav
> * Installera och starta lokal agent
> * Starta en viss kategori av testerna som *integrering*, *funktionella*, *tillförlitlighet*
> * Rapportera testresultat

## <a name="launch-the-test-pass-workflow"></a>Starta testet arbetsflödet

1. Öppna en upphöjd PowerShell-prompt.

2. Kör följande skript för att ladda ned skriptet automation:

    ```PowerShell
    New-Item -ItemType Directory -Path <VaaSLaunchDirectory>
    Set-Location <VaaSLaunchDirectory>
    Invoke-WebRequest -Uri https://storage.azurestackvalidation.com/packages/Microsoft.VaaS.Scripts.latest.nupkg -OutFile "LaunchVaaS.zip"
    Expand-Archive -Path ".\LaunchVaaS.zip" -DestinationPath .\ -Force
    ```

3. Kör följande skript med lämpliga parametervärden:

    ```PowerShell
    $VaaSAccountCreds = New-Object System.Management.Automation.PSCredential "<VaaSUserId>", (ConvertTo-SecureString "<VaaSUserPassword>" -AsPlainText -Force)
    .\LaunchVaaSTests.ps1 -VaaSAccountCreds $VaaSAccountCreds `
                          -VaaSAccountTenantId <VaaSAccountTenantId> `
                          -VaaSSolutionName <VaaSSolutionName> `
                          -VaaSTestPassName <VaaSTestPassName> `
                          -VaaSTestCategories Integration,Functional `
                          -MaxScriptWaitTimeInHours 12 `
                          -ServiceAdminUserName <AzSServiceAdminUser> `
                          -ServiceAdminUserPassword <AzSServiceAdminPassword> `
                          -TenantAdminUserName <AzSTenantAdminUser> `
                          -TenantAdminUserPassword <AzSTenantAdminPassword> `
                          -CloudAdminUserName <AzSCloudAdminUser> `
                          -CloudAdminUserPassword <AzSCloudAdminPassword>
    ```

    **Parametrar**

    | Parameter | Beskrivning |
    | --- | --- |
    | VaaSUserId | Ditt VaaS användar-ID. |
    | VaaSUserPassword | Lösenordet VaaS. |
    | VaaSAccountTenantId | Klientorganisationens VaaS GUID. |
    | VaaSSolutionName | Namnet på VaaS lösningen som skickar testet körs. |
    | VaaSTestPassName | Namnet på testet VaaS skicka arbetsflöde som ska skapas. |
    | VaaSTestCategories | `Integration`, `Functional`, eller. `Reliability`. Om du använder flera värden, Avgränsa varje värde med ett kommatecken.  |
    | ServiceAdminUserName | Ditt Azure Stack-admin-tjänstkonto.  |
    | ServiceAdminPassword | Lösenordet för tjänsten på Azure Stack.  |
    | TenantAdminUserName | Administratören för den primära klienten.  |
    | TenantAdminPassword | Lösenordet för den primära klienten.  |
    | CloudAdminUserName | Molnet administratörens användarnamn.  |
    | CloudAdminPassword | Lösenordet för molnadministratören.  |

4. Granska resultatet av testet. Andra alternativ finns i [övervaka och hantera tester i portalen VaaS](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om PowerShell på Azure Stack kan du granska de senaste modulerna.

> [!div class="nextstepaction"]
> [Azure Stack Module](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0)
