---
title: Hantera Office 365-tjänster med Azure Automation
description: Berättar hur du använder Azure Automation för att hantera Office 365-prenumerationstjänster.
services: automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 9cb505ced907b143fbd6a5f4f30c818092005bb8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80550427"
---
# <a name="manage-office-365-services-using-azure-automation"></a>Hantera Office 365-tjänster med Azure Automation

Du kan använda Azure Automation för hantering av Office 365-prenumerationstjänster för produkter som Microsoft Word och Microsoft Outlook. Interaktioner med Office 365 aktiveras av [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). Se [Använda Azure AD i Azure Automation för att autentisera till Azure](automation-use-azure-ad.md).

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Krav

Du behöver följande för att hantera Office 365-prenumerationstjänster i Azure Automation.

* En Azure-prenumeration. Se [Beslutsguide för prenumeration](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/).
* Ett Automation-objekt i Azure för att lagra autentiseringsuppgifterna och runbooksna för användarkonton. Se [En introduktion till Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro).
* Azure AD. Se [Använda Azure AD i Azure Automation för att autentisera till Azure](automation-use-azure-ad.md).
* En Office 365-klient med ett konto. Se [Konfigurera din Office 365-klientorganisation](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-developer-tenant).

## <a name="installing-the-msonline-and-msonlineext-modules"></a>Installera MSOnline- och MSOnlineExt-modulerna

Användning av Office 365 i Azure Automation kräver Microsoft`MSOnline` Azure Active Directory för Windows PowerShell (modul). Du behöver också modulen [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35), vilket förenklar Azure AD-hantering i miljöer med en och flera innehavare. Installera modulerna enligt beskrivningen i [Använd Azure AD i Azure Automation för att autentisera till Azure](automation-use-azure-ad.md).

>[!NOTE]
>Om du vill använda MSOnline PowerShell måste du vara medlem i Azure AD. Gästanvändare kan inte använda modulen.

## <a name="creating-an-azure-automation-account"></a>Skapa ett Azure Automation-konto

För att kunna slutföra stegen i den här artikeln behöver du ett konto i Azure Automation. Se [Skapa ett Azure Automation-konto](automation-quickstart-create-account.md).
 
## <a name="adding-msonline-and-msonlineext-as-assets"></a>Lägga till MSOnline och MSOnlineExt som tillgångar

Lägg nu till de installerade MSOnline- och MSOnlineExt-modulerna för att aktivera Office 365-funktioner. Se [Hantera moduler i Azure Automation](shared-resources/modules.md).

1. I Azure-portalen väljer du **Automation-konton**.
2. Välj ditt Automation-konto.
3. Välj **galleriet Moduler** under **Delade resurser**.
4. Sök efter MSOnline.
5. Välj `MSOnline` PowerShell-modulen och klicka på **Importera** om du vill importera modulen som en tillgång.
6. Upprepa steg 4 och 5 `MSOnlineExt` för att hitta och importera modulen. 

## <a name="creating-a-credential-asset-optional"></a>Skapa en referenstillgång (valfritt)

Det är valfritt att skapa en autentiseringstillgång för den office 365-administratörsanvändare som har behörighet att köra skriptet. Det kan dock hjälpa att inte exponera användarnamn och lösenord i PowerShell-skript. Instruktioner finns i [Skapa en referenstillgång](automation-use-azure-ad.md#creating-a-credential-asset).

## <a name="creating-an-office-365-service-account"></a>Skapa ett Office 365-tjänstkonto

Om du vill köra Office 365-prenumerationstjänster behöver du ett Office 365-tjänstkonto med behörigheter för att göra vad du vill. Du kan använda ett globalt administratörskonto, ett konto per tjänst eller ha en funktion eller ett skript som ska köras. I vilket fall som helst kräver tjänstkontot ett komplext och säkert lösenord. Se [Konfigurera Office 365 för företag](https://docs.microsoft.com/microsoft-365/admin/setup/setup?view=o365-worldwide). 

## <a name="connecting-to-the-azure-ad-online-service"></a>Ansluta till Azure AD-onlinetjänsten

>[!NOTE]
>Om du vill använda CMDlets för MSOnline-modulen måste du köra dem från Windows PowerShell. PowerShell Core stöder inte dessa cmdlets.

Du kan använda MSOnline-modulen för att ansluta till Azure AD från Office 365-prenumerationen. Anslutningen använder ett användarnamn och lösenord för Office 365 eller använder MFA (Multifaktor authentication). Du kan ansluta med Azure-portalen eller en Windows PowerShell-kommandotolk (behöver inte höjas).

Ett PowerShell-exempel visas nedan. [Cmdlet-cmdlet-cmdlet-programmet hämtas](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) för `Msolcred` autentiseringsuppgifter och lagrar dem i variabeln. Sedan använder [cmdlet connect-MsolService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) autentiseringsuppgifterna för att ansluta till Azure-katalogtjänsten online. Om du vill ansluta till en viss `AzureEnvironment` Azure-miljö använder du parametern.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

Om du inte får några fel har du anslutit. Ett snabbt test är att köra en Office 365-cmdlet, till `Get-MsolUser`exempel, och se resultaten. Om du får felmeddelanden bör du tänka på att ett vanligt problem är ett felaktigt lösenord.

>[!NOTE]
>Du kan också använda AzureRM-modulen eller Az-modulen för att ansluta till Azure AD från Office 365-prenumerationen. Huvudanslutnings cmdlet är [Connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0). Den här cmdleten stöder parametern `AzureEnvironmentName` för specifika Office 365-miljöer.

## <a name="creating-a-powershell-runbook-from-an-existing-script"></a>Skapa en PowerShell-runbook från ett befintligt skript

Du kommer åt Office 365-funktioner från ett PowerShell-skript. Här är ett exempel på ett skript för `Office-Credentials` en autentiseringsdokument som namnges med `admin@TenantOne.com`användarnamnet . Den `Get-AutomationPSCredential` används för att importera Office 365-autentiseringsuppgifterna.

```powershell
$emailFromAddress = "admin@TenantOne.com" 
$emailToAddress = "servicedesk@TenantOne.com" 
$emailSMTPServer = "outlook.office365.com" 
$emailSubject = "Office 365 License Report" 

$credObject = Get-AutomationPSCredential -Name "Office-Credentials" 
Connect-MsolService -Credential $credObject

$O365Licenses = Get-MsolAccountSku | Out-String 
Send-MailMessage -Credential $credObject -From $emailFromAddress -To $emailToAddress -Subject $emailSubject -Body 

$O365Licenses -SmtpServer $emailSMTPServer -UseSSL
```

## <a name="running-the-script-in-a-runbook"></a>Köra skriptet i en runbook

Du kan använda skriptet i en Azure Automation-runbook. I syfte är att vi använder powershell-runbooktypen.

1. Skapa en ny PowerShell-runbook. Se [Skapa en Azure Automation-runbook](https://docs.microsoft.com/azure/automation/automation-quickstart-create-runbook).
2. Välj **Runbooks** under Process **Automation**i ditt Automation-konto .
3. Markera den nya runbooken och klicka på **Redigera**.
4. Kopiera skriptet och klistra in det i textredigeraren för runbooken.
5. Välj **RESURSER**och expandera sedan **autentiseringsuppgifter** och kontrollera att Office 365-autentiseringsuppgifterna finns där.
6. Klicka på **Spara**.
7. Välj **Testfönster**och klicka sedan på **Start** för att börja testa runbooken. Se [Hantera runbooks i Azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks).
8. När testningen är klar lämnar du testfönstret.

## <a name="publishing-and-scheduling-the-runbook"></a>Publicera och schemalägga runbooken

Information om hur du publicerar och sedan schemalägger din runbook finns [i Hantera runbooks i Azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks).

## <a name="next-steps"></a>Nästa steg

* Du hittar information om resurser för automatiseringsautentiseringsuppgifter i [Autentiseringsuppgifter i Azure Automation](shared-resources/credentials.md).
* Se [Hantera moduler i Azure Automation](shared-resources/modules.md) för att ta reda på hur du arbetar med Automation-moduler.
* En översikt över körningshantering finns [i Hantera runbooks i Azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks).
* Mer information om de metoder som kan användas för att starta en runbook i Azure Automation finns [i Starta en runbook i Azure Automation](automation-starting-a-runbook.md).
* Mer information om PowerShell, inklusive språkreferens- och utbildningsmoduler, finns i [PowerShell Docs](https://docs.microsoft.com/powershell/scripting/overview).