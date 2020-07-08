---
title: Hantera Office 365-tjänster med Azure Automation
description: Den här artikeln beskriver hur du använder Azure Automation för att hantera Office 365-prenumerations tjänster.
services: automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 322e2a3679ed29ab9ecc4cdc3c6e1fe4d0f20276
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "83831183"
---
# <a name="manage-office-365-services"></a>Hantera Office 365-tjänster

Du kan använda Azure Automation för hantering av Office 365-prenumerations tjänster för produkter som Microsoft Word och Microsoft Outlook. Interaktioner med Office 365 aktive ras av [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). Se [använda Azure AD i Azure Automation för att autentisera till Azure](automation-use-azure-ad.md).

## <a name="prerequisites"></a>Krav

Du behöver följande för att kunna hantera Office 365-prenumerations tjänster i Azure Automation.

* En Azure-prenumeration. Se [Guide för prenumerations beslut](https://docs.microsoft.com/azure/cloud-adoption-framework/decision-guides/subscriptions/).
* Ett Automation-objekt i Azure för att lagra användar kontots autentiseringsuppgifter och Runbooks. Se [en introduktion till Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro).
* Azure AD. Se [använda Azure AD i Azure Automation för att autentisera till Azure](automation-use-azure-ad.md).
* En Office 365-klient, med ett konto. Se [Konfigurera din Office 365-klient](https://docs.microsoft.com/sharepoint/dev/spfx/set-up-your-developer-tenant).

## <a name="install-the-msonline-and-msonlineext-modules"></a>Installera MSOnline-och MSOnlineExt-modulerna

Användning av Office 365 i Azure Automation kräver Microsoft Azure Active Directory för Windows PowerShell ( `MSOnline` modul). Du behöver också modulen [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35) , som fören klar hanteringen av Azure AD i miljöer med en och flera klienter. Installera modulerna enligt beskrivningen i [använda Azure AD i Azure Automation för att autentisera till Azure](automation-use-azure-ad.md).

>[!NOTE]
>Om du vill använda MSOnline PowerShell måste du vara medlem i Azure AD. Gäst användare kan inte använda modulen.

## <a name="create-an-azure-automation-account"></a>Skapa ett Azure Automation-konto

Du måste ha ett konto i Azure Automation för att kunna slutföra stegen i den här artikeln. Se [skapa ett Azure Automation-konto](automation-quickstart-create-account.md).
 
## <a name="add-msonline-and-msonlineext-as-assets"></a>Lägg till MSOnline och MSOnlineExt som till gångar

Lägg nu till de installerade MSOnline-och MSOnlineExt-modulerna för att aktivera Office 365-funktioner. Se [Hantera moduler i Azure Automation](shared-resources/modules.md).

1. I Azure Portal väljer du **Automation-konton**.
2. Välj ditt Automation-konto.
3. Välj **moduler-Galleri** under **delade resurser**.
4. Sök efter MSOnline.
5. Välj `MSOnline` PowerShell-modulen och klicka på **Importera** för att importera modulen som en till gång.
6. Upprepa steg 4 och 5 för att hitta och importera `MSOnlineExt` modulen. 

## <a name="create-a-credential-asset-optional"></a>Skapa en inloggnings till gång (valfritt)

Det är valfritt att skapa en inloggnings till gång för den administrativa Office 365-användare som har behörighet att köra skriptet. Det kan hjälpa till att fortsätta att exponera användar namn och lösen ord i PowerShell-skript. Instruktioner finns i [skapa en inloggnings till gång](automation-use-azure-ad.md#create-a-credential-asset).

## <a name="create-an-office-365-service-account"></a>Skapa ett Office 365-tjänst konto

Om du vill köra Office 365-prenumerations tjänster behöver du ett konto för Office 365-tjänsten med behörighet att göra vad du vill. Du kan använda ett globalt administratörs konto, ett konto per tjänst eller en funktion eller ett skript som ska köras. I alla fall kräver tjänst kontot ett komplext och säkert lösen ord. Se [Konfigurera Office 365 för företag](https://docs.microsoft.com/microsoft-365/admin/setup/setup?view=o365-worldwide). 

## <a name="connect-to-the-azure-ad-online-service"></a>Ansluta till Azure AD online-tjänsten

>[!NOTE]
>Om du vill använda MSOnline-modulens cmdlets måste du köra dem från Windows PowerShell. PowerShell-kärnan stöder inte dessa cmdletar.

Du kan använda MSOnline-modulen för att ansluta till Azure AD från Office 365-prenumerationen. Anslutningen använder ett Office 365-användar namn och lösen ord eller använder Multi-Factor Authentication (MFA). Du kan ansluta med hjälp av Azure Portal eller en Windows PowerShell-kommandotolk (behöver inte vara förhöjd).

Ett PowerShell-exempel visas nedan. Cmdleten [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) efterfrågar autentiseringsuppgifter och lagrar dem i `Msolcred` variabeln. Sedan använder cmdleten [Connect-MSOLService](https://docs.microsoft.com/powershell/module/msonline/connect-msolservice?view=azureadps-1.0) autentiseringsuppgifterna för att ansluta till Azure Directory online-tjänsten. Om du vill ansluta till en speciell Azure-miljö använder du `AzureEnvironment` parametern.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

Om du inte får några fel har du Anslut ATS. Ett snabb test är att köra en Office 365-cmdlet, till exempel, `Get-MsolUser` och se resultatet. Observera att ett vanligt problem är ett felaktigt lösen ord om du får fel.

>[!NOTE]
>Du kan också använda AzureRM-modulen eller AZ-modulen för att ansluta till Azure AD från Office 365-prenumerationen. Huvud anslutnings-cmdleten är [Connect-AzureAD](https://docs.microsoft.com/powershell/module/azuread/connect-azuread?view=azureadps-2.0). Denna cmdlet stöder `AzureEnvironmentName` parametern för vissa Office 365-miljöer.

## <a name="create-a-powershell-runbook-from-an-existing-script"></a>Skapa en PowerShell-Runbook från ett befintligt skript

Du får åtkomst till Office 365-funktioner från ett PowerShell-skript. Här är ett exempel på ett skript för autentiseringsuppgifter med namnet `Office-Credentials` med användar namnet `admin@TenantOne.com` . Den använder `Get-AutomationPSCredential` för att importera Office 365-autentiseringsuppgiften.

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

## <a name="run-the-script-in-a-runbook"></a>Kör skriptet i en Runbook

Du kan använda ditt skript i en Azure Automation Runbook. I exempel syfte använder vi PowerShell-runbooken.

1. Skapa en ny PowerShell-Runbook. Se [skapa en Azure Automation Runbook](https://docs.microsoft.com/azure/automation/automation-quickstart-create-runbook).
2. Från ditt Automation-konto väljer du **Runbooks** under **process automatisering**.
3. Välj den nya runbooken och klicka på **Redigera**.
4. Kopiera skriptet och klistra in det i text redigeraren för runbooken.
5. Välj **till gångar**och expandera sedan **autentiseringsuppgifter** och kontrol lera att Office 365-autentiseringsuppgiften finns där.
6. Klicka på **Spara**.
7. Välj **test fönster**och klicka sedan på **Starta** för att börja testa din Runbook. Se [Hantera Runbooks i Azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks).
8. När testningen är klar avslutar du från test-fönstret.

## <a name="publish-and-schedule-the-runbook"></a>Publicera och Schemalägg Runbook

Om du vill publicera och sedan schemalägga din Runbook, se [Hantera Runbooks i Azure Automation](https://docs.microsoft.com/azure/automation/manage-runbooks).

## <a name="next-steps"></a>Nästa steg

* Information om användning av autentiseringsuppgifter finns [i Hantera autentiseringsuppgifter i Azure Automation](shared-resources/credentials.md).
* Information om moduler finns [i hantera moduler i Azure Automation](shared-resources/modules.md).
* Om du behöver starta en Runbook kan du läsa [starta en Runbook i Azure Automation](start-runbooks.md).
* Information om PowerShell finns i [PowerShell-dokument](https://docs.microsoft.com/powershell/scripting/overview).