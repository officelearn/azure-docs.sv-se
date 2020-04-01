---
title: Huvuduppgiftstilldelning för Windows Virtual Desktop-tjänsten - Azure
description: Så här skapar du tjänsthuvudnamn och tilldelar roller med hjälp av PowerShell i Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 09/09/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 61b5017609d99f2f0074c67d3838cf351ea38bea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79365431"
---
# <a name="tutorial-create-service-principals-and-role-assignments-by-using-powershell"></a>Självstudiekurs: Skapa tjänsthuvudnamn och rolltilldelningar med hjälp av PowerShell

Tjänsthuvudnamn är identiteter som du kan skapa i Azure Active Directory för att tilldela roller och behörigheter för ett visst ändamål. I Windows Virtual Desktop kan du skapa ett huvudnamn för tjänsten för att:

- Automatisera specifika hanteringsuppgifter för Virtuella datorer i Windows.
- Använd som autentiseringsuppgifter i stället för MFA-obligatoriska användare när du kör en Azure Resource Manager-mall för Windows Virtual Desktop.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa ett tjänsthuvudnamn i Azure Active Directory.
> * Skapa en rolltilldelning i Windows Virtual Desktop.
> * Logga in på Windows Virtual Desktop med hjälp av tjänstens huvudnamn.

## <a name="prerequisites"></a>Krav

Innan du kan skapa tjänsthuvudnamn och rolltilldelningar måste du göra tre saker:

1. Installera AzureAD-modulen. Om du vill installera modulen kör du PowerShell som administratör och kör följande cmdlet:

    ```powershell
    Install-Module AzureAD
    ```

2. [Ladda ned och importera PowerShell-modulen Windows Virtual Desktop](/powershell/windows-virtual-desktop/overview/).

3. Följ alla instruktioner i den här artikeln i samma PowerShell-session. Processen kanske inte fungerar om du avbryter PowerShell-sessionen genom att stänga fönstret och öppna det igen senare.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Skapa ett huvudnamn för tjänsten i Azure Active Directory

När du har uppfyllt förutsättningarna i PowerShell-sessionen kör du följande PowerShell-cmdlets för att skapa ett huvudnamn för tjänsten för flera tjänster i Azure.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```
## <a name="view-your-credentials-in-powershell"></a>Visa dina autentiseringsuppgifter i PowerShell

Innan du skapar rolltilldelningen för tjänstens huvudnamn kan du visa dina autentiseringsuppgifter och skriva ned dem för framtida referens. Lösenordet är särskilt viktigt eftersom du inte kan hämta det när du har stängt den här PowerShell-sessionen.

Här är de tre autentiseringsuppgifterna du bör skriva ner och de cmdlets du behöver köra för att få dem:

- Lösenord:

    ```powershell
    $svcPrincipalCreds.Value
    ```

- Klientorganisations-ID:

    ```powershell
    $aadContext.TenantId.Guid
    ```

- Program-ID:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="create-a-role-assignment-in-windows-virtual-desktop-preview"></a>Skapa en rolltilldelning i förhandsversionen av Windows Virtual Desktop

Därefter måste du skapa en rolltilldelning så att tjänstens huvudnamn kan logga in på Windows Virtual Desktop. Se till att logga in med ett konto som har behörighet att skapa rolltilldelningar.

Hämta och importera först [Windows Virtual Desktop PowerShell-modulen](/powershell/windows-virtual-desktop/overview/) som du kan använda i PowerShell-sessionen om du inte redan har gjort det.

Kör följande PowerShell-cmdletar för att ansluta till Windows Virtual Desktop och visa dina klienter.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant
```

När du hittar klientnamnet för klienten som du vill skapa en rolltilldelning för använder du det namnet i följande cmdlet:

```powershell
$myTenantName = "<Windows Virtual Desktop Tenant Name>"
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName $myTenantName
```

## <a name="sign-in-with-the-service-principal"></a>Logga in med tjänstens huvudnamn

När du har skapat en rolltilldelning för tjänstens huvudnamn kontrollerar du att tjänstens huvudnamn kan logga in på Windows Virtual Desktop genom att köra följande cmdlet:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

När du har loggat in kontrollerar du att allt fungerar genom att testa några Windows Virtual Desktop PowerShell-cmdletar med tjänstens huvudnamn.

## <a name="next-steps"></a>Nästa steg

När du har skapat tjänstens huvudnamn och tilldelat den en roll i windows virtual desktop-klienten kan du använda den för att skapa en värdpool. Om du vill veta mer om värdpooler fortsätter du till självstudien för att skapa en värdpool i Windows Virtual Desktop.

 > [!div class="nextstepaction"]
 > [Skapa en värdpool med Azure Marketplace](./create-host-pools-azure-marketplace.md)
