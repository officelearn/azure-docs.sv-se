---
title: Använda Azure AD i Azure Automation för autentisering till Azure
description: Lär dig hur du använder Azure AD inom Azure Automation som Provider för autentisering till Azure.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 90338a1ffa79e6c2347832cb2e74633db02ec72d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80548340"
---
# <a name="use-azure-ad-in-azure-automation-to-authenticate-to-azure"></a>Använda Azure AD i Azure Automation för autentisering till Azure

Tjänsten [Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) möjliggör ett antal administrativa uppgifter, till exempel användar hantering, domän hantering och konfiguration av enkel inloggning. Den här artikeln beskriver hur du använder Azure AD inom Azure Automation som Provider för autentisering till Azure. 

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installations anvisningar för AZ-modulen på Hybrid Runbook Worker finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med hjälp av [hur du uppdaterar Azure PowerShell moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="installing-azure-ad-modules"></a>Installera Azure AD-moduler

Du kan aktivera Azure AD genom följande PowerShell-moduler:

* Azure Active Directory PowerShell för Graph (AzureRM-och AZ-moduler). Azure Automation levereras med AzureRM-modulen och den senaste uppgraderingen, AZ-modulen. Funktionerna omfattar icke-interaktiv autentisering till Azure med autentisering baserad på OrgId-autentisering (Azure AD User). Se [Azure AD-2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76).

* Microsoft Azure Active Directory för Windows PowerShell (MSOnline-modul). Den här modulen möjliggör interaktioner med Microsoft Online, inklusive Office 365.

>[!NOTE]
>PowerShell-kärnan stöder inte modulen MSOnline. Om du vill använda cmdlets för moduler måste du köra dem från Windows PowerShell. Du uppmanas att använda den nyare Azure Active Directory PowerShell för graf-moduler i stället för MSOnline-modulen. 

### <a name="preinstallation"></a>Preinstallation

Innan du installerar Azure AD-modulerna på datorn:

* Avinstallera alla tidigare versioner av AzureRM/AZ-modulen och MSOnline-modulen. 

* Avinstallera Microsoft Online Services-inloggnings assistenten för att säkerställa att de nya PowerShell-modulerna fungerar korrekt.  

### <a name="install-the-azurerm-and-az-modules"></a>Installera AzureRM-och AZ-modulerna

>[!NOTE]
>Om du vill arbeta med dessa moduler måste du använda PowerShell version 5,1 eller senare med en 64-bitars version av Windows. 

1. Installera Windows Management Framework (WMF) 5,1. Se [Installera och konfigurera WMF 5,1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure?view=powershell-7).

2. Installera AzureRM och/eller AZ med hjälp av instruktionerna i [installera Azure PowerShell på Windows med PowerShellGet](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0).

### <a name="install-the-msonline-module"></a>Installera MSOnline-modulen

>[!NOTE]
>Om du vill installera MSOnline-modulen måste du vara medlem i en Office 365-administratörs roll. Se [om administratörs roller](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide).

1. Se till att funktionen Microsoft .NET Framework 3.5. x är aktive rad på datorn. Det är troligt att datorn har en senare version installerad, men bakåtkompatibilitet med äldre versioner av .NET Framework kan aktive ras eller inaktive ras. 

2. Installera 64-bitars versionen av [Microsoft Online Services-inloggnings assistenten](https://www.microsoft.com/download/details.aspx?id=41950).

3. Kör Windows PowerShell som administratör för att skapa en upphöjd kommando tolk för Windows PowerShell.

4. Distribuera Azure Active Directory från [MSOnline 1,0](http://www.powershellgallery.com/packages/MSOnline/1.0).

5. Om du uppmanas att installera NuGet-providern skriver du Y och trycker på RETUR.

6. Om du uppmanas att installera modulen från [PSGallery](https://www.powershellgallery.com/), Skriv Y och tryck på RETUR.

### <a name="install-support-for-pscredential"></a>Installera stöd för PSCredential

Azure Automation använder klassen [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) för att representera en referens till gång. Skripten hämtar `PSCredential` objekt med hjälp `Get-AutomationPSCredential` av cmdleten. Mer information finns i avsnittet [om inloggnings till gångar i Azure Automation](shared-resources/credentials.md).

## <a name="assigning-a-subscription-administrator"></a>Tilldela en prenumerations administratör

Du måste tilldela en administratör för Azure-prenumerationen. Den här personen har rollen ägare för prenumerations omfånget. Se [rollbaserad åtkomst kontroll i Azure Automation](automation-role-based-access-control.md). 

## <a name="changing-the-azure-ad-users-password"></a>Ändra Azure AD-användarens lösen ord

Ändra Azure AD-användarens lösen ord:

1. Logga ut från Azure.

2. Be administratören logga in på Azure som den Azure AD-användare som precis har skapat, med hjälp av det fullständiga användar namnet (inklusive domänen) och ett tillfälligt lösen ord. 

3. Be administratören att ändra lösen ordet när du uppmanas till det.

## <a name="configuring-azure-automation-to-use-the-azure-ad-user-to-manage-the-azure-subscription"></a>Konfigurera Azure Automation att använda Azure AD-användaren för att hantera Azure-prenumerationen

För att Azure Automation ska kunna kommunicera med Azure AD måste du hämta de autentiseringsuppgifter som är associerade med Azure-anslutningen till Azure AD. Exempel på dessa autentiseringsuppgifter är klient-ID, prenumerations-ID och liknande. Mer information om anslutningen mellan Azure och Azure AD finns i [Anslut din organisation till Azure Active Directory](https://docs.microsoft.com/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=azure-devops).

## <a name="creating-a-credential-asset"></a>Skapa en inloggnings till gång

Med Azures autentiseringsuppgifter för Azure AD är det dags att skapa en Azure Automation Credential-till gång för att lagra autentiseringsuppgifter för Azure AD på ett säkert sätt så att Runbooks och aktuella tillstånds konfigurations skript (DSC) kan komma åt dem. Du kan göra detta med hjälp av antingen Azure Portal-eller PowerShell-cmdletar.

### <a name="create-the-credential-asset-in-azure-portal"></a>Skapa inloggnings till gången i Azure Portal

Du kan använda Azure Portal för att skapa inloggnings till gången. Utför den här åtgärden från ditt Automation-konto med hjälp av **autentiseringsuppgifter** under **delade resurser**. Se [inloggnings till gångar i Azure Automation](shared-resources/credentials.md).

### <a name="create-the-credential-asset-with-windows-powershell"></a>Skapa inloggnings till gången med Windows PowerShell

För att förbereda en ny inloggnings till gång i Windows PowerShell skapar skriptet först `PSCredential` ett objekt med det tilldelade användar namnet och lösen ordet. Skriptet använder sedan det här objektet för att skapa till gången via ett anrop till cmdleten [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) . Alternativt kan skriptet anropa cmdleten [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) för att uppmana användaren att ange ett namn och lösen ord. Se [inloggnings till gångar i Azure Automation](shared-resources/credentials.md). 

## <a name="managing-azure-resources-from-an-azure-automation-runbook"></a>Hantera Azure-resurser från en Azure Automation Runbook

Du kan hantera Azure-resurser från Azure Automation-runbooks med hjälp av inloggnings till gången. Nedan visas ett exempel på en PowerShell-Runbook som samlar in den inloggnings till gång som ska användas för att stoppa och starta virtuella datorer i en Azure-prenumeration. Den här runbooken använder `Get-AutomationPSCredential` först för att hämta de autentiseringsuppgifter som ska användas för att autentisera till Azure. Sedan anropas cmdleten [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.6.1) för att ansluta till Azure med hjälp av autentiseringsuppgiften. Skriptet använder cmdleten [Select-AzureSubscription](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0) för att välja vilken prenumeration som ska användas. 

```azurepowershell
Workflow Stop-Start-AzureVM 
{ 
    Param 
    (    
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureSubscriptionId, 
        [Parameter(Mandatory=$true)][ValidateNotNullOrEmpty()] 
        [String] 
        $AzureVMList="All", 
        [Parameter(Mandatory=$true)][ValidateSet("Start","Stop")] 
        [String] 
        $Action 
    ) 
     
    $credential = Get-AutomationPSCredential -Name 'AzureCredential' 
    Connect-AzAccount -Credential $credential 
    Select-AzureSubscription -SubscriptionId $AzureSubscriptionId 
 
    if($AzureVMList -ne "All") 
    { 
        $AzureVMs = $AzureVMList.Split(",") 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
    } 
    else 
    { 
        $AzureVMs = (Get-AzVM).Name 
        [System.Collections.ArrayList]$AzureVMsToHandle = $AzureVMs 
 
    } 
 
    foreach($AzureVM in $AzureVMsToHandle) 
    { 
        if(!(Get-AzVM | ? {$_.Name -eq $AzureVM})) 
        { 
            throw " AzureVM : [$AzureVM] - Does not exist! - Check your inputs " 
        } 
    } 
 
    if($Action -eq "Stop") 
    { 
        Write-Output "Stopping VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Stop-AzVM -Force 
        } 
    } 
    else 
    { 
        Write-Output "Starting VMs"; 
        foreach -parallel ($AzureVM in $AzureVMsToHandle) 
        { 
            Get-AzVM | ? {$_.Name -eq $AzureVM} | Start-AzVM 
        } 
    } 
}
```  

## <a name="next-steps"></a>Nästa steg

* Du hittar information om till gångar för automatisering av autentiseringsuppgifter i [inloggnings till gångar i Azure Automation](shared-resources/credentials.md).
* Se [Hantera moduler i Azure Automation](shared-resources/modules.md) för att ta reda på hur du arbetar med Automation-moduler.
* Mer information om de metoder som kan användas för att starta en Runbook i Azure Automation finns i [starta en Runbook i Azure Automation](automation-starting-a-runbook.md).
* Mer information om PowerShell, inklusive språk referens-och inlärnings moduler finns i [PowerShell-dokumenten](https://docs.microsoft.com/powershell/scripting/overview).