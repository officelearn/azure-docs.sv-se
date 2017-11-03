---
title: "Tillgångar i Azure Automation-autentiseringen | Microsoft Docs"
description: "Inloggningstillgångar i Azure Automation innehålla säkerhetsreferenser som kan användas för att autentisera till resurser som nås av den eller de DSC-konfigurationen. Den här artikeln beskriver hur du skapar inloggningstillgångar och använda dem i en runbook eller DSC-konfigurationen."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: 3209bf73-c208-425e-82b6-df49860546dd
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/14/2017
ms.author: bwren
ms.openlocfilehash: 70f6272d936b097a9458dfa18279a70727da9d6b
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2017
---
# <a name="credential-assets-in-azure-automation"></a>Inloggningstillgångar i Azure Automation
Ett Automation-autentiseringsuppgiftstillgång innehåller en [PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential) objekt som innehåller säkerhetsreferenser, till exempel användarnamn och lösenord. Runbooks och DSC-konfigurationer kan använda cmdlet: ar som accepterar ett PSCredential-objekt för autentisering eller de kan extrahera användarnamnet och lösenordet för PSCredential-objekt för att förse vissa program eller tjänster som kräver autentisering. Egenskaperna för en autentiseringsuppgift lagras säkert i Azure Automation och kan nås i runbook eller DSC-konfigurationen med den [Get-AutomationPSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) aktivitet.

> [!NOTE]
> Säkra tillgångar i Azure Automation inkluderar autentiseringsuppgifter, certifikat, anslutningar och krypterade variabler. Dessa tillgångar krypteras och lagras i Azure-Automation med en unik nyckel som skapas för varje automation-konto. Den här nyckeln är krypterad med ett certifikat för master och lagras i Azure Automation. Innan du sparar en säker resurs, nyckeln för automation-kontot dekrypteras med master certifikatet och sedan används för att kryptera tillgången.  

## <a name="azure-classic-powershell-cmdlets"></a>Azure klassiska PowerShell-cmdlets
Cmdlets i följande tabell används för att skapa och hantera automatisering inloggningstillgångar med Windows PowerShell.  De levereras som en del av den [Azure PowerShell-modulen](/powershell/azure/overview) som är tillgänglig för användning i Automation-runbooks och DSC-konfigurationer.

| Cmdlet: ar | Beskrivning |
|:--- |:--- |
| [Get-AzureAutomationCredential](/powershell/module/azure/get-azureautomationcredential?view=azuresmps-3.7.0) |Hämtar information om en autentiseringstillgång. Du kan bara hämta autentiseringsuppgifter sig själv från **Get-AutomationPSCredential** aktivitet. |
| [Ny AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Skapar en ny Automation-autentiseringsuppgifter. |
| [Ta bort - AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Tar bort autentiseringsuppgifter för Automation. |
| [Set - AzureAutomationCredential](/powershell/module/azure/new-azureautomationcredential?view=azuresmps-3.7.0) |Anger egenskaperna för en befintlig Automation-autentiseringsuppgift. |

## <a name="azurerm-powershell-cmdlets"></a>AzureRM PowerShell-cmdlets
För AzureRM för cmdlets i följande tabell att skapa och hantera automatisering inloggningstillgångar med Windows PowerShell.  De levereras som en del av den [AzureRM.Automation modulen](/powershell/azure/overview) som är tillgänglig för användning i Automation-runbooks och DSC-konfigurationer.

| Cmdlet: ar | Beskrivning |
|:--- |:--- |
| [Get-AzureRmAutomationCredential](/powershell/module/azurerm.automation/get-azurermautomationcredential?view=azurermps-4.4.0) |Hämtar information om en autentiseringstillgång.  |
| [Ny AzureRmAutomationCredential](/powershell/module/azurerm.automation/new-azurermautomationcredential?view=azurermps-4.4.0) |Skapar en ny Automation-autentiseringsuppgifter. |
| [Ta bort AzureRmAutomationCredential](/powershell/module/azurerm.automation/remove-azurermautomationcredential?view=azurermps-4.4.0) |Tar bort autentiseringsuppgifter för Automation. |
| [Ange AzureRmAutomationCredential](/powershell/module/azurerm.automation/set-azurermautomationcredential?view=azurermps-4.4.0) |Anger egenskaperna för en befintlig Automation-autentiseringsuppgift. |

## <a name="runbook-activities"></a>Runbook-aktiviteter
Aktiviteterna i följande tabell används för att komma åt autentiseringsuppgifter i en runbook och DSC-konfigurationer.

| Aktiviteter | Beskrivning |
|:--- |:--- |
| Get-AutomationPSCredential |Hämtar en autentiseringsuppgift om du vill använda i en runbook eller DSC-konfigurationen. Returnerar en [System.Management.Automation.PSCredential](http://msdn.microsoft.com/library/system.management.automation.pscredential) objekt. |

> [!NOTE]
> Du bör undvika att använda variabler i – Name-parametern i Get-AutomationPSCredential eftersom detta kan göra det svårare att hitta beroenden mellan runbooks eller DSC-konfigurationer och autentiseringsuppgifter tillgångar i designläge.

## <a name="python2-functions"></a>Python2 funktioner
Funktionen i följande tabell används för att komma åt autentiseringsuppgifter i en Python2 runbook.

| Funktionen | Beskrivning |
|:---|:---|
| automationassets.get_automation_credential | Hämtar information om en autentiseringstillgång. |

> [!NOTE]
> Du måste importera modulen ”automationassets” längst upp i Python-runbook för att komma åt funktionerna tillgång.

## <a name="creating-a-new-credential-asset"></a>Skapa en ny autentiseringstillgång

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Att skapa en ny autentiseringstillgång med Azure-portalen
1. Från ditt automation-konto klickar du på den **tillgångar** del för att öppna den **tillgångar** bladet.
2. Klicka på den **autentiseringsuppgifter** del för att öppna den **autentiseringsuppgifter** bladet.
3. Klicka på **lägga till en autentiseringsuppgift** längst upp på bladet.
4. Fyll i formuläret och klicka på **skapa** att spara de nya autentiseringsuppgifter.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Att skapa en ny autentiseringstillgång med Windows PowerShell
Följande exempelkommandon visar hur du skapar en ny automation-autentiseringsuppgift. Ett PSCredential-objekt skapas först med namnet och lösenordet och sedan för att skapa autentiseringsuppgiftstillgång. Du kan också använda den **Get-Credential** cmdlet uppmanas att ange ett namn och lösenord.

    $user = "MyDomain\MyUser"
    $pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
    $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
    New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred

### <a name="to-create-a-new-credential-asset-with-the-azure-classic-portal"></a>Skapa en ny autentiseringstillgång med den klassiska Azure-portalen
1. Från ditt automation-konto klickar du på **tillgångar** längst upp i fönstret.
2. Längst ned i fönstret klickar du på **Lägg till inställning**.
3. Klicka på **Lägg till autentiseringsuppgift**.
4. I den **autentiseringstypen** listrutan **PowerShell-autentiseringsuppgift**.
5. Slutför guiden och klickar du på kryssrutan för att spara de nya autentiseringsuppgifter.

## <a name="using-a-powershell-credential"></a>Med hjälp av en PowerShell-autentiseringsuppgift
Du kan hämta en autentiseringstillgång i en runbook eller DSC-konfigurationen med den **Get-AutomationPSCredential** aktivitet. Detta returnerar en [PSCredential-objekt](http://msdn.microsoft.com/library/system.management.automation.pscredential.aspx) som du kan använda med en aktivitet eller cmdlet som kräver en PSCredential-parameter. Du kan också hämta egenskaperna för objektet autentiseringsuppgifter att använda individuellt. Objektet har en egenskap för användarnamnet och säkert lösenord, eller så kan du använda den **GetNetworkCredential** metod för att returnera en [NetworkCredential](http://msdn.microsoft.com/library/system.net.networkcredential.aspx) objekt som ger en oskyddad version av lösenordet.

### <a name="textual-runbook-sample"></a>Text-runbook-exempel
Följande exempelkommandon visar hur du använder en PowerShell-autentiseringsuppgift i en runbook. I det här exemplet autentiseringsuppgifter hämtas och dess användarnamn och lösenord som har tilldelats till variabler.

    $myCredential = Get-AutomationPSCredential -Name 'MyCredential'
    $userName = $myCredential.UserName
    $securePassword = $myCredential.Password
    $password = $myCredential.GetNetworkCredential().Password


### <a name="graphical-runbook-sample"></a>Grafisk runbook-exempel
Du lägger till en **Get-AutomationPSCredential** aktivitet till en grafisk runbook genom att högerklicka på autentiseringsuppgifter i rutan bibliotek av grafiska redigerare och välja **Lägg till arbetsytan**.

![Lägg till autentiseringsuppgift på arbetsytan](media/automation-credentials/credential-add-canvas.png)

Följande bild visar ett exempel på hur du använder en autentiseringsuppgift i en grafisk runbook.  I det här fallet den används för autentisering av en runbook till Azure-resurser som beskrivs i [autentisera Runbooks med Azure AD-användarkonto](automation-create-aduser-account.md).  Den första aktiviteten hämtar de autentiseringsuppgifter som har åtkomst till Azure-prenumerationen.  Den **Add-AzureAccount** aktiviteten använder sedan autentiseringsuppgifterna för autentisering för alla aktiviteter som kommer efter den.  En [pipelinelänk](automation-graphical-authoring-intro.md#links-and-workflow) är här sedan **Get-AutomationPSCredential** förväntar sig ett enskilt objekt.  

![Lägg till autentiseringsuppgift på arbetsytan](media/automation-credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Med hjälp av en PowerShell-autentiseringsuppgift i DSC
När DSC-konfigurationer i Azure Automation kan referera till inloggningstillgångar med **Get-AutomationPSCredential**, inloggningstillgångar kan också överföras via parametrar, om så önskas. Mer information finns i [kompilering konfigurationer i Azure Automation DSC](automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Med hjälp av autentiseringsuppgifter i Python2
I följande exempel visas ett exempel på att komma åt autentiseringsuppgifter i Python2 runbooks.

    import automationassets
    from automationassets import AutomationAssetNotFound

    # get a credential
    cred = automationassets.get_automation_credential("credtest")
    print cred["username"]
    print cred["password"]

## <a name="next-steps"></a>Nästa steg
* Mer information om länkar i grafiska redigering finns [länkar i grafiska redigering](automation-graphical-authoring-intro.md#links-and-workflow)
* För att förstå de olika autentiseringsmetoder med automatisering Se [Azure Automation-säkerhet](automation-security-overview.md)
* Information om hur du kommer igång med grafiska runbooks finns i [Min första grafisk runbook](automation-first-runbook-graphical.md)
* Se hur du kommer igång med runbooks baserade på PowerShell-arbetsflöden i [Min första PowerShell-arbetsflödesbaserade runbook](automation-first-runbook-textual.md) 
* Kom igång med Python2 runbooks, se [min första Python2-runbook](automation-first-runbook-textual-python2.md) 

