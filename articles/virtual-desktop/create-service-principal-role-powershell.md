---
title: Skapa Windows Virtual Desktop Preview-tjänstens huvud namn och roll tilldelningar med hjälp av PowerShell – Azure
description: Hur du skapar tjänstens huvud namn och tilldelar roller med hjälp av PowerShell i för hands versionen av Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/12/2019
ms.author: helohr
ms.openlocfilehash: 3e9ee3f5dd04ef838f78b9731885b7ea48e6c99d
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/09/2019
ms.locfileid: "70811317"
---
# <a name="tutorial-create-service-principals-and-role-assignments-by-using-powershell"></a>Självstudier: Skapa tjänstens huvudnamn och rolltilldelningar med PowerShell

Tjänstens huvud namn är identiteter som du kan skapa i Azure Active Directory för att tilldela roller och behörigheter för ett specifikt syfte. I för hands versionen av Windows Virtual Desktop kan du skapa ett huvud namn för tjänsten för att:

- Automatisera vissa hanterings uppgifter för virtuella Windows-datorer.
- Använd som autentiseringsuppgifter i stället för MFA-obligatoriska användare när du kör en Azure Resource Manager mall för Windows Virtual Desktop.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa ett huvud namn för tjänsten i Azure Active Directory.
> * Skapa en roll tilldelning i Windows Virtual Desktop.
> * Logga in på Windows Virtual Desktop genom att använda tjänstens huvud namn.

## <a name="prerequisites"></a>Förutsättningar

Innan du kan skapa tjänstens huvud namn och roll tilldelningar måste du göra tre saker:

1. Installera AzureAD-modulen. Installera modulen genom att köra PowerShell som administratör och köra följande cmdlet:

    ```powershell
    Install-Module AzureAD
    ```

2. [Hämta och importera Windows Virtual Desktop PowerShell-modulen](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview)

3. Följ alla anvisningar i den här artikeln i samma PowerShell-session. Det kanske inte fungerar om du stänger fönstret och återgår till det senare.

## <a name="create-a-service-principal-in-azure-active-directory"></a>Skapa ett huvudnamn för tjänsten i Azure Active Directory

När du har uppfyllt kraven i PowerShell-sessionen kör du följande PowerShell-cmdletar för att skapa ett huvud namn för flera innehavare i Azure.

```powershell
Import-Module AzureAD
$aadContext = Connect-AzureAD
$svcPrincipal = New-AzureADApplication -AvailableToOtherTenants $true -DisplayName "Windows Virtual Desktop Svc Principal"
$svcPrincipalCreds = New-AzureADApplicationPasswordCredential -ObjectId $svcPrincipal.ObjectId
```

## <a name="view-your-credentials-in-powershell"></a>Visa dina autentiseringsuppgifter i PowerShell

Innan du avslutar PowerShell-sessionen visar du dina autentiseringsuppgifter och skriver ned dem för framtida bruk. Lösen ordet är särskilt viktigt eftersom du inte kan hämta det när du har stängt PowerShell-sessionen.

Här följer de tre autentiseringsuppgifterna som du bör skriva ned och vilka cmdlets du måste köra för att hämta dem:

- Ords

    ```powershell
    $svcPrincipalCreds.Value
    ```

- Klient-ID:

    ```powershell
    $aadContext.TenantId.Guid
    ```

- Program-ID:

    ```powershell
    $svcPrincipal.AppId
    ```

## <a name="create-a-role-assignment-in-windows-virtual-desktop-preview"></a>Skapa en roll tilldelning i för hands versionen av Windows Virtual Desktop

Härnäst ska du skapa en RDS-roll tilldelning i Windows Virtual Desktop för tjänstens huvud namn, vilket gör att tjänstens huvud namn kan logga in på Windows Virtual Desktop. Se till att använda ett konto som har behörighet att skapa RDS-roll tilldelningar.

Kör följande PowerShell-cmdlets för att ansluta till Windows Virtual Desktop och Visa dina RDS-klienter.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
Get-RdsTenant | FL
```

Använd TenantName för rätt klient och kör följande PowerShell-cmdletar för att skapa en roll tilldelning för tjänstens huvud namn i den angivna klient organisationen.

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Owner" -ApplicationId $svcPrincipal.AppId -TenantName "<my-rds-tenantname>"
```

## <a name="sign-in-with-the-service-principal"></a>Logga in med tjänstens huvud namn

När du har skapat en roll tilldelning för tjänstens huvud namn kontrollerar du att tjänstens huvud namn kan logga in på Windows Virtual Desktop genom att köra följande cmdlet:

```powershell
$creds = New-Object System.Management.Automation.PSCredential($svcPrincipal.AppId, (ConvertTo-SecureString $svcPrincipalCreds.Value -AsPlainText -Force))
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com" -Credential $creds -ServicePrincipal -AadTenantId $aadContext.TenantId.Guid
```

När du har loggat in ser du till att allt fungerar genom att testa några Windows PowerShell-cmdletar för virtuella datorer med tjänstens huvud namn.

## <a name="next-steps"></a>Nästa steg

När du har skapat tjänstens huvud namn och tilldelat rollen som en roll i din Windows-klient för virtuella datorer kan du använda den för att skapa en adresspool. Om du vill veta mer om värdar för pooler fortsätter du till självstudien för att skapa en adresspool i Windows Virtual Desktop.

 > [!div class="nextstepaction"]
 > [Självstudie för Windows virtuell Skriv bords värd](./create-host-pools-azure-marketplace.md)
