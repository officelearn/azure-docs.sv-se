---
title: "Konfigurera ett ”Kör som”-konto i Azure | Microsoft Docs"
description: "Självstudie som steg för steg beskriver hur du skapar, testar och använder autentisering med säkerhetsobjekt i Azure Automation."
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
keywords: "tjänstobjektnamn, setspn, azure-autentisering"
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/06/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 7230fb1a8d27708c40040950e3ec8950c6c04780
ms.lasthandoff: 03/07/2017


---
# <a name="authenticate-runbooks-with-azure-run-as-account"></a>Autentisera runbooks med ett ”Kör som”-konto i Azure
Det här avsnittet beskriver hur du konfigurerar ett Automation-konto från Azure Portal med funktionen Kör som-konto för att autentisera runbooks som hanterar resurser i Azure Resource Manager eller Azure Service Management.

När du skapar ett nytt Automation-konto på Azure-portalen skapas automatiskt:

* Kör som-kontot. Det här kontot skapar ett nytt namn för tjänstobjektet i Azure Active Directory, ett certifikat, och tilldelar rollbaserad åtkomstkontroll (RBAC) för rollen Deltagare. Rollbaserad åtkomstkontroll används för att hantera Resource Manager-resurser med hjälp av runbooks.   
* Ett klassiskt Kör som-konto. Det här kontot skapas genom uppladdningen av ett hanteringscertifikat, som används för att hantera Azure Service Management eller klassiska resurser med hjälp av runbooks.  

Detta gör processen enklare för dig och hjälper dig att snabbt börja skapa och distribuera runbooks för dina automatiseringsbehov.      

Med ett Kör som-konto eller ett klassiskt Kör som-konto kan du:

* Tillhandahålla en standardiserad metod för att autentisera med Azure när du hanterar Azure Resource Manager- eller Azure Service Management-resurser från runbooks på Azure-portalen.  
* Automatisera användningen av globala runbooks som konfigurerats i Azure Alerts.

> [!NOTE]
> [Azure Alerts-integreringsfunktionen](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) med Automation Global Runbooks kräver ett Automation-konto som konfigurerats med ett Kör som-konto och ett klassiskt Kör som-konto. Du kan välja ett Automation-konto som redan har ett definierat Kör som-konto och klassiskt Kör som-konto, eller så kan du välja att skapa ett nytt.
>  

Vi visar hur du skapar Automation-kontot från Azure Portal, hur du uppdaterar ett Automation-konto med hjälp av PowerShell, hur du hanterar kontokonfigurationen och hur du autentiserar i dina runbooks.

Innan vi gör det finns det dock några saker som det är viktigt att du förstår och tänker på.

1. Detta påverkar inte befintliga Automation-konton som redan har skapats i den klassiska distributionsmodellen eller Resource Manager-distributionsmodellen.  
2. Detta fungerar endast för Automation-konton som skapats via Azure-portalen.  Om du försöker skapa ett konto från den klassiska portalen så replikeras inte Kör som-kontokonfigurationen.
3. Om du för närvarande har runbooks och tillgångar (t.ex. scheman och variabler) som tidigare har skapats för att hantera klassiska resurser och du vill använda dessa runbooks för att autentisera med det nya klassiska Kör som-kontot, måste du skapa ett klassiskt Kör som-konto med Managing an Run As Account 
(Hantera ett Kör som-konto) eller uppdatera ditt befintliga konto med hjälp av PowerShell-skriptet nedan.  
4. För att autentisera med det nya Kör som-kontot och det klassiska Kör som-kontot för Automation måste du ändra dina befintliga runbooks med hjälp av exempelkoden nedan.  **Observera** att Kör som-kontot är avsett för verifiering mot Resource Manager-resurser med hjälp av det certifikatbaserade tjänstobjektnamnet, medan det klassiska Kör som-kontot används för att autentisera mot Service Management-resurser med hanteringscertifikatet.     

## <a name="create-a-new-automation-account-from-the-azure-portal"></a>Skapa ett nytt Automation-konto från Azure Portal
I det här avsnittet ska du utföra följande steg för att skapa ett nytt Azure Automation-konto från Azure-portalen.  När du följer stegen skapas både Kör som-kontot och det klassiska Kör som-kontot.  

> [!NOTE]
> Användaren som utför de här stegen måste vara medlem i rollen Tjänstadministratörer eller medadministratör för prenumerationen som beviljar åtkomst till prenumerationen för användaren. Användaren måste också läggas till som en användare i prenumerationens förvalda Active Directory. Kontot behöver inte tilldelas till en privilegierad roll. Användare som inte är medlemmar i prenumerationens Active Directory innan de läggs till i rollen Medadministratör för prenumerationen, läggs till i Active Directory som gäst och ser "Du har inte behörighet att skapa..." varningen i **Lägg till Automation-konto**-bladet. Användare som har lagts till i rollen medadministratör först, kan tas bort från prenumerationens Active Directory och läggas till igen för att göra dem till fullständiga användare i Active Directory. Den här situationen kan verifieras från rutan **Azure Active Directory** i Azure-portalen genom att välja **Användare och grupper**, välja **Alla användare** och när du har valt den specifika användaren, väljer du **Profilen**.  Värdet för attributet **Användartyp** under användarens profil bör inte vara lika med **Gäst**.  
> 

1. Logga in på Azure Portal med ett konto som är medlem i rollen Prenumerationsadministratörer och som är medadministratör för prenumerationen.
2. Välj **Automation-konton**.
3. Klicka på **Lägg till** på bladet Automation-konton.<br>![Lägga till ett Automation-konto](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)
   
   > [!NOTE]
   > Om du ser i följande varning i bladet **Lägga till ett Automation-konto** beror det på att ditt konto inte är medlem i rollerna administratör eller medadministratör för prenumerationen.<br>![Varningsmeddelande för Lägga till ett Automation-konto](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)
   > 
   > 
4. På bladet **Lägg till Automation-konto** skriver du namnet på det nya Automation-kontot i rutan **Namn**.
5. Om du har mer än en prenumeration anger du den prenumeration som du vill använda för det nya kontot, samt en ny eller befintlig **resursgrupp** och en **plats** för Azure-datacentret.
6. Kontrollera att värdet **Ja** har valts för **Skapa Kör som-konto i Azure** och klicka på knappen **Skapa**.  
   
   > [!NOTE]
   > Om du väljer att inte skapa ”Kör som”-kontot genom att välja alternativet **Nej** visas ett varningsmeddelande på bladet **Lägg till Automation-konto**.  Kontot skapas på Azure-portalen, men har inte motsvarande autentiseringsidentitet i den klassiska prenumerationskatalogtjänsten eller Resource Manager-prenumerationskatalogtjänsten och har därför inte åtkomst till resurser i din prenumeration.  Det innebär att runbooks som refererar till det här kontot inte kan autentisera och utföra åtgärder mot resurser i dessa distributionsmodeller.
   > 
   > ![Varningsmeddelande för Lägga till ett Automation-konto](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)<br>
   > Om tjänstobjektnamnet inte skapas tilldelas inte rollen Deltagare.
   > 

7. Medan Azure skapar Automation-kontot kan du följa förloppet under **Meddelanden** på menyn.

### <a name="resources-included"></a>Resurser som ingår
När Automation-kontot har skapats skapas flera resurser automatiskt.  I följande tabell sammanfattas resurserna för Kör som-kontot.<br>

| Resurs | Beskrivning |
| --- | --- |
| AzureAutomationTutorial-runbook |Ett exempel på en grafisk runbook som visar hur du autentiserar med hjälp av Kör som-kontot och hur du hämtar alla Resource Manager-resurser. |
| AzureAutomationTutorialScript-runbook |Ett exempel på en PowerShell-runbook som visar hur du autentiserar med hjälp av Kör som-kontot och hur du hämtar alla Resource Manager-resurser. |
| AzureRunAsCertificate |Certifikattillgång som skapas automatiskt när Automation-kontot genereras eller med hjälp av PowerShell-skriptet nedan för ett befintligt konto.  Den gör att du kan autentisera med Azure så att du kan hantera Azure Resource Manager-resurser från runbooks.  Det här certifikatet har en livslängd på ett år. |
| AzureRunAsConnection |Anslutningstillgång som skapas automatiskt när Automation-kontot genereras eller med hjälp av PowerShell-skriptet nedan för ett befintligt konto. |

I följande tabell sammanfattas resurserna för det klassiska Kör som-kontot.<br>

| Resurs | Beskrivning |
| --- | --- |
| AzureClassicAutomationTutorial-runbook |Ett exempel på en grafisk runbook som hämtar alla klassiska virtuella datorer i en prenumeration med hjälp av det klassiska Kör som-kontot (certifikat) och som sedan returnerar den virtuella datorns namn och status. |
| AzureClassicAutomationTutorial Script-runbook |Ett exempel på en PowerShell-runbook som hämtar alla klassiska virtuella datorer i en prenumeration med hjälp av det klassiska Kör som-kontot (certifikat) och som sedan returnerar den virtuella datorns namn och status. |
| AzureClassicRunAsCertificate |Certifikattillgång som skapas automatiskt och som används för att autentisera med Azure så att du kan hantera klassiska Azure-resurser från runbooks.  Det här certifikatet har en livslängd på ett år. |
| AzureClassicRunAsConnection |Anslutningstillgång som skapas automatiskt och som används för att autentisera med Azure så att du kan hantera klassiska Azure-resurser från runbooks. |

## <a name="verify-run-as-authentication"></a>Verifiera Kör som-autentisering
Nu ska vi köra ett litet test för att bekräfta att du kan autentisera med det nya Kör som-kontot.     

1. Öppna Automation-kontot som du skapade tidigare på Azure Portal.  
2. Öppna listan med runbooks genom att klicka på panelen **Runbooks**.
3. Välj **AzureAutomationTutorialScript**-runbooken och klicka sedan på **Starta** för att starta runbooken.  Ett meddelande visas där du bekräftar att du vill starta runbooken.
4. Ett [runbook-jobb](automation-runbook-execution.md) skapas, bladet för jobbet öppnas och jobbstatusen visas på panelen **Jobbsammanfattning**.  
5. Jobbets första status är *I kö* vilket betyder att det väntar på att en Runbook Worker i molnet ska bli tillgänglig. Därefter ändras statusen till *Startar* när en Runbook Worker gör anspråk på jobbet, och sedan till *Körs* när runbook-jobbet börjar köras.  
6. När runbook-jobbet har slutförts bör du se statusen **Slutfört**.<br> ![Runbook-test för säkerhetsobjekt](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)<br>
7. Om du vill visa ett detaljerat resultat av runbook-jobbet klickar du på panelen **Utdata**.
8. På bladet **Utdata** bör du se att autentiseringen har lyckats samt en lista över alla tillgängliga resurser i resursgruppen.
9. Stäng bladet **Utdata** och gå tillbaka till bladet **Jobbsammanfattning**.
10. Stäng bladet **Jobbsammanfattning** och motsvarande blad för **AzureAutomationTutorialScript**-runbooken.

## <a name="verify-classic-run-as-authentication"></a>Verifiera klassisk Kör som-autentisering
Nu ska vi köra ett litet test för att bekräfta att du kan autentisera med det nya klassiska Kör som-kontot.     

1. Öppna Automation-kontot som du skapade tidigare på Azure Portal.  
2. Öppna listan med runbooks genom att klicka på panelen **Runbooks**.
3. Välj **AzureAutomationTutorialScript**-runbooken och klicka på **Starta** för att starta runbooken.  Ett meddelande visas där du bekräftar att du vill starta runbooken.
4. Ett [runbook-jobb](automation-runbook-execution.md) skapas, bladet för jobbet öppnas och jobbstatusen visas på panelen **Jobbsammanfattning**.  
5. Jobbets första status är *I kö* vilket betyder att det väntar på att en Runbook Worker i molnet ska bli tillgänglig. Därefter ändras statusen till *Startar* när en Runbook Worker gör anspråk på jobbet, och sedan till *Körs* när runbook-jobbet börjar köras.  
6. När runbook-jobbet har slutförts bör du se statusen **Slutfört**.<br> ![Runbook-test för säkerhetsobjekt](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
7. Om du vill visa ett detaljerat resultat av runbook-jobbet klickar du på panelen **Utdata**.
8. På bladet **Utdata** bör du se att autentiseringen har lyckats samt en lista över alla klassiska virtuella datorer i prenumerationen.
9. Stäng bladet **Utdata** och gå tillbaka till bladet **Jobbsammanfattning**.
10. Stäng bladet **Jobbsammanfattning** och motsvarande blad för **AzureClassicAutomationTutorialScript**-runbooken.

## <a name="managing-azure-run-as-account"></a>Hantera Kör som-konto i Azure
Under ditt Automation-kontos livslängd måste du förnya certifikatet innan det går ut. Om du tror att kontot har komprometterats kan du ta bort Kör som-kontot och återskapa det.  I det här avsnittet får du anvisningar om hur du utför sådana åtgärder.  

### <a name="self-signed-certificate-renewal"></a>Förnya självsignerade certifikat
Det självsignerade certifikat som skapades för Kör som-kontot i Azure kan förnyas när som helst tills det upphör att gälla, vilket sker ett år från skapandedatum.  När du förnyar det behålls det gamla giltiga certifikatet för att se till att alla runbooks som köar eller körs aktivt, som autentiseras med Kör som-kontot, inte påverkas.  Det här certifikatet fortsätter att existera tills det upphör att gälla.    

> [!NOTE]
> Om du har konfigurerat ditt Automation kör som-konto att använda ett certifikat utfärdat av din företagscertifikatutfärdare och du använder det här alternativet ersätts certifikatet av ett självsignerat certifikat.  

1. Öppna ditt Automation-konto på Azure Portal.  
2. I rutan för kontoegenskaper på Automation-kontobladet väljer du **Kör som-konton** under avsnittet **Kontoinställningar**.<br><br> ![Rutan Egenskaper för Automation-konto](media/automation-sec-configure-azure-runas-account/automation-account-properties-pane.png)<br><br>
3. På egenskapsbladet **Kör som-konton** väljer du antingen det Kör som-konto eller klassiska Kör som-konto som du vill förnya certifikatet för, och på egenskapsbladet för det valda kontot klickar du på **Förnya certifikat**.<br><br> ![Förnya certifikat för Kör som-konto](media/automation-sec-configure-azure-runas-account/automation-account-renew-runas-certificate.png)<br><br> Ett meddelande visas där du bekräftar att du vill fortsätta.  
4. Medan certifikatet förnyas kan du följa förloppet under **Meddelanden** på menyn.

### <a name="delete-run-as-account"></a>Ta bort Kör som-konto
I följande steg beskrivs hur du tar bort och återskapar ditt Azure Kör som- eller klassiska Kör som-konto.  När du utför den här åtgärden behålls Automation-kontot.  När du h ar tagit bort Kör som- eller det klassiska Kör som-kontot kan du skapa det i portalen.  

1. Öppna ditt Automation-konto på Azure Portal.  
2. I rutan för kontoegenskaper på Automation-kontobladet väljer du **Kör som-konton** under avsnittet **Kontoinställningar**.
3. På egenskapsbladet **Kör som-konton** väljer du antingen det Kör som-konto eller det klassiska Kör som-konto som du vill ta bort, och på egenskapsbladet för det valda kontot klickar du på **Ta bort**.<br><br> ![Ta bort Kör som-konto](media/automation-sec-configure-azure-runas-account/automation-account-delete-runas.png)<br><br>  Ett meddelande visas där du bekräftar att du vill fortsätta.
4. Medan kontot tas bort kan du följa förloppet under **Meddelanden** på menyn.  När borttagningen är slutförd kan du återskapa det från egenskapsbladet **Kör som-konton** och välja skapandealternativet **Kör som-konto i Azure**.<br><br> ![Återskapa Automation Kör som-konto](media/automation-sec-configure-azure-runas-account/automation-account-create-runas.png)<br> 

### <a name="misconfiguration"></a>Felaktig konfiguration
Om några av konfigurationsobjekten som behövs för att Kör som-kontot eller det klassiska Kör som-kontot ska fungera ordentligt tas bort eller inte skapades på rätt sätt under den första konfigurationen, som:

* Certifikattillgång 
* Anslutningstillgång 
* Kör som-konto har tagits bort från deltagarrollen
* Huvudnamn för tjänsten eller program i Azure AD

Automation identifierar ändringarna och meddelar dig med statusen **Ofullständig** i egenskapsbladet **Kör som-konton** för kontot.<br><br> ![Statusmeddelande om ofullständig kör som-konfiguration](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config.png)<br><br>När du väljer Kör som-kontot visas följande felmeddelande i kontots egenskapspanel:<br><br> ![Varningsmeddelande om ofullständig kör som-konfiguration](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config-msg.png).<br>  
Om kör som-kontot är felkonfigurerat kan du snabbt lösa detta genom att ta bort och återskapa kör som-kontot.   

## <a name="update-an-automation-account-using-powershell"></a>Uppdatera ett Automation-konto med hjälp av PowerShell
Här har du möjlighet att använda PowerShell för att uppdatera ett befintligt Automation-konto om:

1. Du har skapat ett Automation-konto, men avböjde att skapa Kör som-kontot. 
2. Du redan har ett Automation-konto för att hantera Resource Manager-resurser och du vill uppdatera det med ett Kör som-konto för runbook-autentisering.
4. Du redan har ett Automation-konto för att hantera klassiska resurser och du vill uppdatera det för att använda det klassiska Kör som-kontot i stället för att skapa ett nytt konto och migrera dina runbooks och resurser till det.   
5. Du vill skapa ett Azure kör som-konto och klassiskt kör som-konto med ett certifikat utfärdat av företagscertifikatutfärdaren

Det här skriptet har följande förutsättningar:

1. Skriptet stöder endast körning på Windows 10 och Windows Server 2016 med Azure Resource Manager-modul 2.01 och högre installerad.  Det stöds inte i tidigare versioner av Windows.  
2. Azure PowerShell 1.0 och högre. Information om den här versionen och hur du installerar den finns i [Installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs).
3. Du har skapat ett Automation-konto.  Skriptet nedan refererar till det här kontot som värdet för parametrarna -AutomationAccountName och -ApplicationDisplayName.

Hämta värdena för *SubscriptionID*, *ResourceGroup* och *AutomationAccountName*, som är obligatoriska parametrar för skripten, genom att först välja ditt Automation-konto på Azure-portalen från bladet **Automation-konto** och sedan välja **Alla inställningar**.  Från bladet **Alla inställningar** väljer du **Egenskaper** under **Kontoinställningar**.  Notera dessa värden i bladet **Egenskaper**.<br><br> ![Egenskaper för Automation-konto](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)  

### <a name="create-run-as-account-powershell-script"></a>Skapa PowerShell-skript för ett Kör som-konto
Det här PowerShell-skriptet har stöd för följande konfigurationer: 

* Skapa Azure Kör som-konto med självsignerat certifikat
* Skapa Azure Kör som-konto och Azure klassiskt kör som-konto med självsignerade certifikat
* Skapa Azure kör som-konto och Azure klassiskt kör som-konto med hjälp av Enterprise-certifikat
* Skapa Kör som-konto och klassiskt Kör som-konto i Azure med självsignerat certifikat i Azure Government-moln

Följande skapas beroende på vilka konfigurationsalternativ du väljer:

* Ett Azure AD-program som ska autentiseras med antingen det självsignerade certifikatet eller företagscertifiktatet skapar ett tjänsthuvudnamnskonto för programmet i Azure AD och tilldelar rollen Deltagare (du kan ändra rollen till Ägare eller en annan roll) för kontot i din aktuella prenumeration.  Mer information finns i artikeln [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).
* En Automation-certifikattillgång i det angivna Automation-kontot med namnet **AzureRunAsCertificate**, som innehåller certifikatet som används av tjänstobjektet.
* En Automation-anslutningstillgång i det angivna Automation-kontot med namnet **AzureRunAsConnection**, där applicationId, tenantId, subscriptionId och tumavtryck för certifikat lagras.    

För klassiskt Kör som-konto:

* En Automation-certifikatstillgång på det angivna Automation-kontot med namnet **AzureClassicRunAsCertificate**, som innehåller det självsignerade certifikatet eller ett certifikat utfärdat av företagets certifikatutgivare som används för att autentisera dina runbooks.
* En Automation-anslutningstillgång i det angivna Automation-kontot med namnet **AzureClassicRunAsConnection**, som innehåller prenumerationens namn, prenumerations-ID:t och certifikattillgångens namn.

Om du har valt alternativet att använda ett självsignerat certifikat för klassiskt Kör som skapar skriptet ett självsignerat hanteringscertifikat och sparar det i mappen för tillfälliga filer på datorn under den användarprofil som används för att köra PowerShell-sessionen: *%USERPROFILE%\AppData\Local\Temp*.  När skriptet har körts måste du överföra Azure-hanteringscertifikatet till hanteringsarkivet för prenumerationen som Automation-kontot skapades i.  Stegen nedan beskriver hur du kör skriptet och laddar upp certifikatet.  

1. Spara följande skript på datorn.  I det här exemplet sparar du det med filnamnet **New-RunAsAccount.ps1**.  
   
        #Requires -RunAsAdministrator
        Param (
        [Parameter(Mandatory=$true)]
        [String] $ResourceGroup,

        [Parameter(Mandatory=$true)]
        [String] $AutomationAccountName,

        [Parameter(Mandatory=$true)]
        [String] $ApplicationDisplayName,

        [Parameter(Mandatory=$true)]
        [String] $SubscriptionId,

        [Parameter(Mandatory=$true)]
        [Boolean] $CreateClassicRunAsAccount,

        [Parameter(Mandatory=$true)]
        [String] $SelfSignedCertPlainPassword,
 
        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPathForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

        [Parameter(Mandatory=$false)]
        [ValidateSet("AzureCloud","AzureUSGovernment")]
        [string]$EnvironmentName="AzureCloud",

        [Parameter(Mandatory=$false)]
        [int] $NoOfMonthsUntilExpired = 12
        )

        function CreateSelfSignedCertificate([string] $keyVaultName, [string] $certificateName, [string] $selfSignedCertPlainPassword,[string] $certPath, [string] $certPathCer, [string] $noOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"

        $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose 
        }

        function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {  
        $CurrentDate = Get-Date
        $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
        $KeyId = (New-Guid).Guid 

        $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $KeyCredential.StartDate = $CurrentDate
        $KeyCredential.EndDate= [DateTime]$PfxCert.GetExpirationDateString()
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.CertValue  = $keyValue

        # Use Key credentials and create AAD Application
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $KeyCredential
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId 
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id
   
        # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
        Sleep -s 15
        $NewRole = New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           Sleep -s 10
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
           $Retries++;
        }
           return $Application.ApplicationId.ToString();
        }

        function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName,[string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
        $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force   
        Remove-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
        New-AzureRmAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
        }

        function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
        Remove-AzureRmAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues 
        }

        Import-Module AzureRM.Profile
        Import-Module AzureRM.Resources

        $AzureRMProfileVersion= (Get-Module AzureRM.Profile).Version
        if (!(($AzureRMProfileVersion.Major -ge 2 -and $AzureRMProfileVersion.Minor -ge 1) -or ($AzureRMProfileVersion.Major -gt 2)))
        {
          Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
          return
        }
 
        Login-AzureRmAccount -EnvironmentName $EnvironmentName
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId

        # Create Run As Account using Service Principal
        $CertifcateAssetName = "AzureRunAsCertificate"
        $ConnectionAssetName = "AzureRunAsConnection"
        $ConnectionTypeName = "AzureServicePrincipal"
 
        if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
           $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
           $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
        } else {
           $CertificateName = $AutomationAccountName+$CertifcateAssetName
           $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
           $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
           $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
           CreateSelfSignedCertificate $KeyVaultName $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $NoOfMonthsUntilExpired
        }

        # Create Service Principal
        $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
        $ApplicationId=CreateServicePrincipal $PfxCert $ApplicationDisplayName

         # Create the automation certificate asset
         CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

         # Populate the ConnectionFieldValues
         $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
         $TenantID = $SubscriptionInfo | Select TenantId -First 1
         $Thumbprint = $PfxCert.Thumbprint
         $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId} 

         # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
         CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

        if ($CreateClassicRunAsAccount) {
           # Create Run As Account using Service Principal
           $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
           $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
           $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
           $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
                    "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates." + [Environment]::NewLine +
                    "Then click Upload and upload the .cer format of #CERT#" 
 
            if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
            $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
            $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
            $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
         } else {
            $ClassicRunAsAccountCertificateName = $AutomationAccountName+$ClassicRunAsAccountCertifcateAssetName
            $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
            $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
            $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
            $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
            CreateSelfSignedCertificate $KeyVaultName $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $NoOfMonthsUntilExpired
         }

         # Create the automation certificate asset
         CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

         # Populate the ConnectionFieldValues
         $SubscriptionName = $subscription.Subscription.SubscriptionName
         $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

         # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
         CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName $ClassicRunAsAccountConnectionFieldValues

         Write-Host -ForegroundColor red $UploadMessage
         }

2. Starta **Windows PowerShell** från **startskärmen** med utökade användarrättigheter.
3. Från det upphöjda PowerShell-kommandoradsgränssnittet navigerar du till mappen som innehåller skriptet som du skapade i steg 1 och kör skriptinställningen med de obligatoriska parametervärdena baserat på konfigurationen som du behöver.  

    **Skapa Kör som-konto för Azure med ett självsignerat certifikat**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword>` 

    **Skapa Azures kör som-konto och Azures klassiska kör som-konto med självsignerade certifikat**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **Skapa Azures kör som-konto och Azure klassiska kör som-konto med hjälp av företagscertifikat**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **Skapa Azures kör som-konto och klassiskt Azure kör som-konto med självsignerade certifikat i Azure Government-moln**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`
 
    > [!NOTE]
    > Du uppmanas att autentisera med Azure när du kör skriptet. Du måste logga in med ett konto som är medlem i rollen Prenumerationsadministratörer och som är medadministratör för prenumerationen.
    > 
    > 

När skriptet har slutförts, om du har skapat en klassiskt kör som-konto, behöver du kopiera certifikatet som skapades i användarprofilen **Temp**-mapp.  Följ anvisningarna för att [ladda upp ett API-hanteringscertifikat](../azure-api-management-certs.md) till den klassiska Azure-portalen och gå sedan till [exempelkoden](#sample-code-to-authenticate-with-service-management-resources) för att validera konfigurationen av autentiseringsuppgifterna med Service Management-resurser.  Om du inte har skapat ett klassiskt Kör som-konto, se [exempelkoden](#sample-code-to-authenticate-with-resource-manager-resources) nedan för att autentisera med Resource Manager-resurser och validera konfigurationen av autentiseringsuppgifter eller hänvisa till [exempelkoden](#sample-code-to-authenticate-with-service-management-resources) för att validera konfigurationen av autentiseringsuppgifterna med Service Management-resurser.

## <a name="sample-code-to-authenticate-with-resource-manager-resources"></a>Exempelkod för att autentisera med Resource Manager-resurser
Du kan använda den uppdaterade exempelkoden nedan, som kommer från exempel-runbooken **AzureAutomationTutorialScript**, och autentisera med ”Kör som”-kontot för att hantera Resource Manager-resurser med dina runbooks.   

    $connectionName = "AzureRunAsConnection"
    $SubId = Get-AutomationVariable -Name 'SubscriptionId'
    try
    {
       # Get the connection "AzureRunAsConnection "
       $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

       "Signing in to Azure..."
       Add-AzureRmAccount `
         -ServicePrincipal `
         -TenantId $servicePrincipalConnection.TenantId `
         -ApplicationId $servicePrincipalConnection.ApplicationId `
         -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
       "Setting context to a specific subscription"     
       Set-AzureRmContext -SubscriptionId $SubId              
    }
    catch {
        if (!$servicePrincipalConnection)
        {
           $ErrorMessage = "Connection $connectionName not found."
           throw $ErrorMessage
         } else{
            Write-Error -Message $_.Exception
            throw $_.Exception
         }
    }


Skriptet innehåller två ytterligare rader med kod som gör det möjligt att referera till en prenumerationskontext så att du enkelt kan arbeta mellan flera prenumerationer. En variabeltillgång med namnet SubscriptionId innehåller ID:t för prenumerationen och, efter Add-AzureRmAccount-cmdleten, definieras [cmdleten Set-AzureRmContext](https://msdn.microsoft.com/library/mt619263.aspx) med parameteruppsättningen *-SubscriptionId*. Om variabelnamnet är för generellt kan du ändra namnet på variabeln och ta med ett prefix eller en annan namnkonvention som gör det lättare att identifiera den. Du kan också använda parameteruppsättningen -SubscriptionName i stället för -SubscriptionId med en tillhörande variabeltillgång.    

Observera cmdleten som används för autentisering i runbooken – **Add-AzureRmAccount** använder *ServicePrincipalCertificate*-parameteruppsättningen.  Den autentiserar med hjälp av tjänstobjektets certifikat, inte autentiseringsuppgifter.  

## <a name="sample-code-to-authenticate-with-service-management-resources"></a>Exempelkod för att autentisera med Service Management-resurser
Du kan använda den uppdaterade exempelkoden nedan, som kommer från exempel-runbooken **AzureClassicAutomationTutorialScript**, om du vill autentisera med det klassiska Kör som-kontot för att hantera klassiska resurser med dina runbooks.

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID

## <a name="next-steps"></a>Nästa steg
* Mer information om tjänstobjekt finns i [Programobjekt och tjänstobjekt](../active-directory/active-directory-application-objects.md).
* Mer information om rollbaserad åtkomstkontroll i Azure Automation finns i [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).
* Mer information om certifikat och Azure-tjänster finns i [Översikt över certifikat för Azure Cloud Services](../cloud-services/cloud-services-certs-create.md)


