---
title: Använda Azure AD i Azure Automation för att autentisera till Azure
description: Lär dig hur du använder Azure AD i Azure Automation som leverantör för autentisering till Azure.
services: automation
ms.date: 03/30/2020
ms.topic: conceptual
ms.openlocfilehash: 90338a1ffa79e6c2347832cb2e74633db02ec72d
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548340"
---
# <a name="use-azure-ad-in-azure-automation-to-authenticate-to-azure"></a>Använda Azure AD i Azure Automation för att autentisera till Azure

[Azure Active Directory (AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) -tjänsten aktiverar ett antal administrativa uppgifter, till exempel användarhantering, domänhantering och konfiguration med enkel inloggning. I den här artikeln beskrivs hur du använder Azure AD i Azure Automation som leverantör för autentisering till Azure. 

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="installing-azure-ad-modules"></a>Installera Azure AD-moduler

Du kan aktivera Azure AD via följande PowerShell-moduler:

* Azure Active Directory PowerShell för graph (AzureRM- och Az-moduler). Azure Automation levereras med AzureRM-modulen och dess senaste uppgradering, Az-modulen. Funktionen inkluderar icke-interaktiv autentisering till Azure med Azure AD-användare (OrgId) autentiseringsuppgifter-baserad autentisering. Se [Azure AD 2.0.2.76](https://www.powershellgallery.com/packages/AzureAD/2.0.2.76).

* Microsoft Azure Active Directory för Windows PowerShell (MSOnline-modul). Den här modulen möjliggör interaktioner med Microsoft Online, inklusive Office 365.

>[!NOTE]
>PowerShell Core stöder inte MSOnline-modulen. Om du vill använda modulen cmdlets måste du köra dem från Windows PowerShell. Du uppmanas att använda de nyare Azure Active Directory PowerShell for Graph-modulerna i stället för MSOnline-modulen. 

### <a name="preinstallation"></a>Preinstallation

Innan du installerar Azure AD-modulerna på datorn:

* Avinstallera alla tidigare versioner av AzureRM/Az-modulen och MSOnline-modulen. 

* Avinstallera inloggningsassistenten för Microsoft Online Services för att säkerställa att de nya PowerShell-modulerna fungerar korrekt.  

### <a name="install-the-azurerm-and-az-modules"></a>Installera AzureRM- och Az-modulerna

>[!NOTE]
>Om du vill arbeta med dessa moduler måste du använda PowerShell version 5.1 eller senare med en 64-bitarsversion av Windows. 

1. Installera WMF (Windows Management Framework) 5.1. Se [Installera och konfigurera WMF 5.1](https://docs.microsoft.com/powershell/scripting/wmf/setup/install-configure?view=powershell-7).

2. Installera AzureRM och/eller Az med instruktioner i [Installera Azure PowerShell på Windows med PowerShellGet](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0).

### <a name="install-the-msonline-module"></a>Installera MSOnline-modulen

>[!NOTE]
>Om du vill installera MSOnline-modulen måste du vara medlem i en Office 365-administratörsroll. Se [Om administratörsroller](https://docs.microsoft.com/microsoft-365/admin/add-users/about-admin-roles?view=o365-worldwide).

1. Kontrollera att Funktionen Microsoft .NET Framework 3.5.x är aktiverad på datorn. Det är troligt att datorn har en nyare version installerad, men bakåtkompatibilitet med äldre versioner av .NET Framework kan aktiveras eller inaktiveras. 

2. Installera 64-bitarsversionen av [Inloggningsassistenten för Microsoft Online Services](https://www.microsoft.com/download/details.aspx?id=41950).

3. Kör Windows PowerShell som administratör för att skapa en upphöjd Windows PowerShell-kommandotolk.

4. Distribuera Azure Active Directory från [MSOnline 1.0](http://www.powershellgallery.com/packages/MSOnline/1.0).

5. Om du uppmanas att installera NuGet-providern skriver du Y och trycker på RETUR.

6. Om du uppmanas att installera modulen från [PSGallery](https://www.powershellgallery.com/)skriver du Y och trycker på RETUR.

### <a name="install-support-for-pscredential"></a>Installera stöd för PSCredential

Azure Automation använder [pscredential-klassen](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) för att representera en referenstillgång. Skripten hämtar `PSCredential` objekt `Get-AutomationPSCredential` med hjälp av cmdleten. Mer information finns [i Autentiseringsuppgifter i Azure Automation](shared-resources/credentials.md).

## <a name="assigning-a-subscription-administrator"></a>Tilldela en prenumerationsadministratör

Du måste tilldela en administratör för Azure-prenumerationen. Den här personen har rollen som Ägare för prenumerationsomfattningen. Se [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md). 

## <a name="changing-the-azure-ad-users-password"></a>Ändra Azure AD-användarens lösenord

Så här ändrar du Azure AD-användarens lösenord:

1. Logga ut från Azure.

2. Låt administratören logga in på Azure som Azure AD-användare just skapat, med hjälp av det fullständiga användarnamnet (inklusive domänen) och ett tillfälligt lösenord. 

3. Be administratören ändra lösenordet när du uppmanas att göra det.

## <a name="configuring-azure-automation-to-use-the-azure-ad-user-to-manage-the-azure-subscription"></a>Konfigurera Azure Automation så att Azure AD-användaren kan hantera Azure-prenumerationen

För att Azure Automation ska kunna kommunicera med Azure AD måste du hämta autentiseringsuppgifterna som är associerade med Azure-anslutningen till Azure AD. Exempel på dessa autentiseringsuppgifter är klient-ID, prenumerations-ID och liknande. Mer information om anslutningen mellan Azure och Azure AD finns i [Ansluta din organisation till Azure Active Directory](https://docs.microsoft.com/azure/devops/organizations/accounts/connect-organization-to-azure-ad?view=azure-devops).

## <a name="creating-a-credential-asset"></a>Skapa en referenstillgång

Med Azure-autentiseringsuppgifterna för Azure AD tillgängliga är det dags att skapa en Azure Automation-referenstillgång för att på ett säkert sätt lagra Azure AD-autentiseringsuppgifterna så att DSC-skript (Runbooks and Desire State Configuration) kan komma åt dem. Du kan göra detta med antingen Azure-portalen eller PowerShell-cmdlets.

### <a name="create-the-credential-asset-in-azure-portal"></a>Skapa autentiseringstillgången i Azure Portal

Du kan använda Azure-portalen för att skapa autentiseringstillgången. Gör den här åtgärden från ditt Automation-konto med **autentiseringsuppgifter** under **Delade resurser**. Se [Autentiseringsuppgifter i Azure Automation](shared-resources/credentials.md).

### <a name="create-the-credential-asset-with-windows-powershell"></a>Skapa autentiseringstillgången med Windows PowerShell

Om du vill förbereda en ny autentiseringstillgång i `PSCredential` Windows PowerShell skapar skriptet först ett objekt med det tilldelade användarnamnet och lösenordet. Skriptet använder sedan det här objektet för att skapa tillgången via ett anrop till cmdleten [New-AzureAutomationCredential.](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) Alternativt kan skriptet anropa Cmdlet hämta [autentiseringsuppgifter](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) för att uppmana användaren att skriva in ett namn och lösenord. Se [Autentiseringsuppgifter i Azure Automation](shared-resources/credentials.md). 

## <a name="managing-azure-resources-from-an-azure-automation-runbook"></a>Hantera Azure-resurser från en Azure Automation-runbook

Du kan hantera Azure-resurser från Azure Automation-runbooks med hjälp av autentiseringstillgången. Nedan finns ett exempel på PowerShell-runbook som samlar in den autentiseringsuppgifter som ska användas för att stoppa och starta virtuella datorer i en Azure-prenumeration. Den här runbooken använder `Get-AutomationPSCredential` först för att hämta autentiseringsuppgifterna som ska användas för att autentisera till Azure. Den anropar sedan [cmdlet connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.6.1) för att ansluta till Azure med hjälp av autentiseringsuppgifterna. Skriptet använder [cmdleten Select-AzureSubscription](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0) för att välja prenumerationen att arbeta med. 

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

* Du hittar information om resurser för automatiseringsautentiseringsuppgifter i [Autentiseringsuppgifter i Azure Automation](shared-resources/credentials.md).
* Se [Hantera moduler i Azure Automation](shared-resources/modules.md) för att ta reda på hur du arbetar med Automation-moduler.
* Mer information om de metoder som kan användas för att starta en runbook i Azure Automation finns [i Starta en runbook i Azure Automation](automation-starting-a-runbook.md).
* Mer information om PowerShell, inklusive språkreferens- och utbildningsmoduler, finns i [PowerShell Docs](https://docs.microsoft.com/powershell/scripting/overview).