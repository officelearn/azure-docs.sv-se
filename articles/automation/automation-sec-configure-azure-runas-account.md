<properties
    pageTitle="Konfigurera ett ”Kör som”-konto i Azure | Microsoft Azure"
    description="Självstudie som steg för steg beskriver hur du skapar, testar och använder autentisering med säkerhetsobjekt i Azure Automation."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
    keywords="tjänstobjektnamn, setspn, azure-autentisering"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/17/2016"
    ms.author="magoedte"/>


# Autentisera runbooks med ett ”Kör som”-konto i Azure

Det här avsnittet beskriver hur du konfigurerar ett Automation-konto från Azure-portalen med funktionen Kör som-konto för att autentisera runbooks som hanterar resurser i Azure Resource Manager eller Azure Service Management.

När du skapar ett nytt Automation-konto på Azure-portalen skapas automatiskt:

- Kör som-kontot. Det här kontot skapar ett nytt namn för tjänstobjektet i Azure Active Directory, ett certifikat, och tilldelar rollbaserad åtkomstkontroll (RBAC) för rollen Deltagare. Rollbaserad åtkomstkontroll används för att hantera Resource Manager-resurser med hjälp av runbooks.   
- Ett klassiskt Kör som-konto. Det här kontot skapas genom uppladdningen av ett hanteringscertifikat, som används för att hantera Azure Service Management eller klassiska resurser med hjälp av runbooks.  

Detta gör processen enklare för dig och hjälper dig att snabbt börja skapa och distribuera runbooks för dina automatiseringsbehov.      

Med ett Kör som-konto eller ett klassiskt Kör som-konto kan du:

- Tillhandahålla en standardiserad metod för att autentisera med Azure när du hanterar Azure Resource Manager- eller Azure Service Management-resurser från runbooks på Azure-portalen.  
- Automatisera användningen av globala runbooks som konfigurerats i Azure Alerts.


>[AZURE.NOTE] [Azure Alerts-integreringsfunktionen](../azure-portal/insights-receive-alert-notifications.md) med Automation Global Runbooks kräver ett Automation-konto som konfigurerats med ett Kör som-konto och ett klassiskt Kör som-konto. Du kan välja ett Automation-konto som redan har ett definierat Kör som-konto och klassiskt Kör som-konto, eller så kan du välja att skapa ett nytt.

Vi visar hur du skapar Automation-kontot från Azure-portalen, hur du uppdaterar ett Automation-konto med hjälp av PowerShell och hur du autentiserar i dina runbooks.

Innan vi gör det finns det dock några saker som det är viktigt att du förstår och tänker på.

1. Detta påverkar inte befintliga Automation-konton som redan har skapats i den klassiska distributionsmodellen eller Resource Manager-distributionsmodellen.  
2. Detta fungerar endast för Automation-konton som skapats via Azure-portalen.  Om du försöker skapa ett konto från den klassiska portalen så replikeras inte Kör som-kontokonfigurationen.
3. Om du för närvarande har runbooks och tillgångar (t.ex. scheman och variabler) som tidigare har skapats för att hantera klassiska resurser och du vill använda dessa runbooks för att autentisera med det nya klassiska Kör som-kontot, måste du migrera dem till det nya Automation-kontot eller uppdatera ditt befintliga konto med hjälp av PowerShell-skriptet nedan.  
4. För att autentisera med det nya Kör som-kontot och det klassiska Kör som-kontot för Automation måste du ändra dina befintliga runbooks med hjälp av exempelkoden nedan.  **Observera** att Kör som-kontot är avsett för verifiering mot Resource Manager-resurser med hjälp av det certifikatbaserade tjänstobjektnamnet, medan det klassiska Kör som-kontot används för att autentisera mot Service Management-resurser med hanteringscertifikatet.     


## Skapa ett nytt Automation-konto från Azure-portalen

I det här avsnittet ska du utföra följande steg för att skapa ett nytt Azure Automation-konto från Azure-portalen.  När du följer stegen skapas både Kör som-kontot och det klassiska Kör som-kontot.  

>[AZURE.NOTE] Användaren som utför de här stegen *måste* vara medlem i rollen Prenumerationsadministratörer och vara medadministratör för prenumerationen som beviljar åtkomst till prenumerationen för användaren.  Användaren måste också läggas till som en användare i prenumerationens förvalda Active Directory. Kontot behöver inte tilldelas till en privilegierad roll. 

1. Logga in på Azure-portalen med ett konto som är medlem i rollen Prenumerationsadministratörer och som är medadministratör för prenumerationen.
2. Välj **Automation-konton**.
3. Klicka på **Lägg till** på bladet Automation-konton.<br>![Lägga till ett Automation-konto](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)

    >[AZURE.NOTE] Om du ser i följande varning i bladet **Lägga till ett Automation-konto** beror det på att ditt konto inte är medlem i rollerna administratör eller medadministratör för prenumerationen.<br>![Varningsmeddelande för Lägg till Automation-konto](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)

4. På bladet **Lägg till Automation-konto** skriver du namnet på det nya Automation-kontot i rutan **Namn**.
5. Om du har mer än en prenumeration anger du den prenumeration som du vill använda för det nya kontot, samt en ny eller befintlig **resursgrupp** och en **plats** för Azure-datacentret.
6. Kontrollera att värdet **Ja** har valts för **Skapa Kör som-konto i Azure** och klicka på knappen **Skapa**.  

    >[AZURE.NOTE] Om du väljer att inte skapa ”Kör som”-kontot genom att välja alternativet **Nej** visas ett varningsmeddelande på bladet **Lägg till Automation-konto**.  Kontot skapas på Azure-portalen, men har inte motsvarande autentiseringsidentitet i den klassiska prenumerationskatalogtjänsten eller Resource Manager-prenumerationskatalogtjänsten och har därför inte åtkomst till resurser i din prenumeration.  Det innebär att runbooks som refererar till det här kontot inte kan autentisera och utföra åtgärder mot resurser i dessa distributionsmodeller.
    
    >![Varningsmeddelande för Lägg till Automation-konto](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)<br>
    Om tjänstobjektnamnet inte skapas tilldelas inte rollen Deltagare.


7. Medan Azure skapar Automation-kontot kan du följa förloppet under **Meddelanden** på menyn.

### Resurser som ingår

När Automation-kontot har skapats skapas flera resurser automatiskt.  I följande tabell sammanfattas resurserna för Kör som-kontot.<br>

Resurs|Beskrivning 
--------|-----------
AzureAutomationTutorial-runbook|Ett exempel på en PowerShell-runbook som visar hur du autentiserar med hjälp av Kör som-kontot och hur du hämtar alla Resource Manager-resurser.
AzureAutomationTutorialScript-runbook|Ett exempel på en PowerShell-runbook som visar hur du autentiserar med hjälp av Kör som-kontot och hur du hämtar alla Resource Manager-resurser. 
AzureRunAsCertificate|Certifikattillgång som skapas automatiskt när Automation-kontot genereras eller med hjälp av PowerShell-skriptet nedan för ett befintligt konto.  Den gör att du kan autentisera med Azure så att du kan hantera Azure Resource Manager-resurser från runbooks.  Det här certifikatet har en livslängd på ett år. 
AzureRunAsConnection|Anslutningstillgång som skapas automatiskt när Automation-kontot genereras eller med hjälp av PowerShell-skriptet nedan för ett befintligt konto.

I följande tabell sammanfattas resurserna för det klassiska Kör som-kontot.<br>

Resurs|Beskrivning 
--------|-----------
AzureClassicAutomationTutorial-runbook|Ett exempel på en runbook som hämtar alla klassiska virtuella datorer i en prenumeration med hjälp av det klassiska Kör som-kontot (certifikat) och som sedan returnerar den virtuella datorns namn och status.
AzureClassicAutomationTutorial Script-runbook|Ett exempel på en runbook som hämtar alla klassiska virtuella datorer i en prenumeration med hjälp av det klassiska Kör som-kontot (certifikat) och som sedan returnerar den virtuella datorns namn och status.
AzureClassicRunAsCertificate|Certifikattillgång som skapas automatiskt och som används för att autentisera med Azure så att du kan hantera klassiska Azure-resurser från runbooks.  Det här certifikatet har en livslängd på ett år. 
AzureClassicRunAsConnection|Anslutningstillgång som skapas automatiskt och som används för att autentisera med Azure så att du kan hantera klassiska Azure-resurser från runbooks.  

## Verifiera Kör som-autentisering

Nu ska vi köra ett litet test för att bekräfta att du kan autentisera med det nya Kör som-kontot.     

1. Öppna Automation-kontot som du skapade tidigare på Azure-portalen.  
2. Öppna listan med runbooks genom att klicka på panelen **Runbooks**.
3. Välj **AzureAutomationTutorialScript**-runbooken och klicka sedan på **Starta** för att starta runbooken.  Ett meddelande visas där du bekräftar att du vill starta runbooken.
4. Ett [runbook-jobb](automation-runbook-execution.md) skapas, bladet för jobbet öppnas och jobbstatusen visas på panelen **Jobbsammanfattning**.  
5. Jobbets första status är *I kö* vilket betyder att det väntar på att en Runbook Worker i molnet ska bli tillgänglig. Därefter ändras statusen till *Startar* när en Runbook Worker gör anspråk på jobbet, och sedan till *Körs* när runbook-jobbet börjar köras.  
6. När runbook-jobbet har slutförts bör du se statusen **Slutfört**.<br> ![Runbook-test för säkerhetsobjekt](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)<br>
7. Om du vill visa ett detaljerat resultat av runbook-jobbet klickar du på panelen **Utdata**.
8. På bladet **Utdata** bör du se att autentiseringen har lyckats samt en lista över alla tillgängliga resurser i resursgruppen. 
9. Stäng bladet **Utdata** och gå tillbaka till bladet **Jobbsammanfattning**.
13. Stäng bladet **Jobbsammanfattning** och motsvarande blad för **AzureAutomationTutorialScript**-runbooken.

## Verifiera klassisk Kör som-autentisering

Nu ska vi köra ett litet test för att bekräfta att du kan autentisera med det nya klassiska Kör som-kontot.     

1. Öppna Automation-kontot som du skapade tidigare på Azure-portalen.  
2. Öppna listan med runbooks genom att klicka på panelen **Runbooks**.
3. Välj **AzureAutomationTutorialScript**-runbooken och klicka på **Starta** för att starta runbooken.  Ett meddelande visas där du bekräftar att du vill starta runbooken.
4. Ett [runbook-jobb](automation-runbook-execution.md) skapas, bladet för jobbet öppnas och jobbstatusen visas på panelen **Jobbsammanfattning**.  
5. Jobbets första status är *I kö* vilket betyder att det väntar på att en Runbook Worker i molnet ska bli tillgänglig. Därefter ändras statusen till *Startar* när en Runbook Worker gör anspråk på jobbet, och sedan till *Körs* när runbook-jobbet börjar köras.  
6. När runbook-jobbet har slutförts bör du se statusen **Slutfört**.<br> ![Runbook-test för säkerhetsobjekt](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
7. Om du vill visa ett detaljerat resultat av runbook-jobbet klickar du på panelen **Utdata**.
8. På bladet **Utdata** bör du se att autentiseringen har lyckats samt en lista över alla klassiska virtuella datorer i prenumerationen. 
9. Stäng bladet **Utdata** och gå tillbaka till bladet **Jobbsammanfattning**.
13. Stäng bladet **Jobbsammanfattning** och motsvarande blad för **AzureClassicAutomationTutorialScript**-runbooken.

## Uppdatera ett Automation-konto med hjälp av PowerShell

Här har du möjlighet att använda PowerShell för att uppdatera ett befintligt Automation-konto om:

1. Du har skapat ett Automation-konto, men avböjde att skapa Kör som-kontot. 
2. Du redan har ett Automation-konto för att hantera Resource Manager-resurser och du vill uppdatera det med ett Kör som-konto för runbook-autentisering. 
2. Du redan har ett Automation-konto för att hantera klassiska resurser och du vill uppdatera det för att använda det klassiska Kör som-kontot i stället för att skapa ett nytt konto och migrera dina runbooks och resurser till det.   

Kontrollera följande innan du fortsätter:

1. Du har laddat ned och installerat [Windows Management Framework (WMF) 4.0](https://www.microsoft.com/download/details.aspx?id=40855) om du kör Windows 7.   
    Om du kör Windows Server 2012 R2, Windows Server 2012, Windows 2008 R2, Windows 8.1 eller Windows 7 SP1 så finns [Windows Management Framework 5.0](https://www.microsoft.com/download/details.aspx?id=50395) tillgängligt för installation.
2. Azure PowerShell 1.0. Information om den här versionen och hur du installerar den finns i [Installera och konfigurera Azure PowerShell](../powershell-install-configure.md). 
3. Du har skapat ett Automation-konto.  Båda skripten nedan refererar till det här kontot som värdet för parametrarna -AutomationAccountName och -ApplicationDisplayName.

Hämta värdena för *SubscriptionID*, *ResourceGroup* och *AutomationAccountName*, som är obligatoriska parametrar för skripten, genom att först välja ditt Automation-konto på Azure-portalen från bladet **Automation-konto** och sedan välja **Alla inställningar**.  Från bladet **Alla inställningar** väljer du **Egenskaper** under **Kontoinställningar**.  Notera dessa värden i bladet **Egenskaper**.<br> ![Egenskaper för Automation-konto](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)  

### Skapa PowerShell-skript för ett Kör som-konto

Följande konfigureras med PowerShell-skriptet nedan:

- Ett Azure AD-program som autentiseras med det självsignerade certifikatet, skapar ett tjänstobjekt för programmet i Azure AD och tilldelar rollen Deltagare (du kan ändra rollen till Ägare eller en annan roll) för kontot i din aktuella prenumeration.  Mer information finns i artikeln [Rollbaserad åtkomstkontroll i Azure Automation](../automation/automation-role-based-access-control.md).
- En Automation-certifikattillgång i det angivna Automation-kontot med namnet **AzureRunAsCertificate**, som innehåller certifikatet som används av tjänstobjektet.
- En Automation-anslutningstillgång i det angivna Automation-kontot med namnet **AzureRunAsConnection**, där applicationId, tenantId, subscriptionId och tumavtryck för certifikat lagras.    

Stegen nedan beskriver hur du kör skriptet.

1. Spara följande skript på datorn.  I det här exemplet sparar du det med filnamnet **New-AzureServicePrincipal.ps1**.  

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
        [String] $CertPlainPassword,

        [Parameter(Mandatory=$false)]
        [int] $NoOfMonthsUntilExpired = 12
        )

        Login-AzureRmAccount
        Import-Module AzureRM.Resources
        Select-AzureRmSubscription -SubscriptionId $SubscriptionId

        $CurrentDate = Get-Date
        $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
        $KeyId = (New-Guid).Guid
        $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")

        $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"

        $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose

        $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate -ArgumentList @($CertPath, $CertPlainPassword)
        $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

        $KeyCredential = New-Object  Microsoft.Azure.Commands.Resources.Models.ActiveDirectory.PSADKeyCredential
        $KeyCredential.StartDate = $CurrentDate
        $KeyCredential.EndDate= $EndDate
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.Type = "AsymmetricX509Cert"
        $KeyCredential.Usage = "Verify"
        $KeyCredential.Value = $KeyValue

        # Use Key credentials
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $ApplicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $keyCredential

        New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId | Write-Verbose
        Get-AzureRmADServicePrincipal | Where {$_.ApplicationId -eq $Application.ApplicationId} | Write-Verbose

        $NewRole = $null
        $Retries = 0;
        While ($NewRole -eq $null -and $Retries -le 6)
        {
           # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
           Sleep 5
           New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
           Sleep 10
           $NewRole = Get-AzureRMRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
           $Retries++;
        } 

        # Get the tenant id for this subscription
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1

        # Create the automation resources
        New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name AzureRunAsCertificate -Password $CertPassword -Exportable | write-verbose

        # Create a Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        $ConnectionAssetName = "AzureRunAsConnection"
        Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
        $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues

2. Starta **Windows PowerShell** från **startskärmen** med utökade användarrättigheter.
3. Från det upphöjda PowerShell-kommandoradsgränssnittet går du till mappen som innehåller skriptet som du skapade i steg 1 och kör skriptet med dina värden för parametrarna *-ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName*, *-SubscriptionId* och *-CertPlainPassword*.<br>

    >[AZURE.NOTE] Du uppmanas att autentisera med Azure när du kör skriptet. Du måste logga in med ett konto som är medlem i rollen Prenumerationsadministratörer och som är medadministratör för prenumerationen.
    
        .\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName> 
        -AutomationAccountName <NameofAutomationAccount> `
        -ApplicationDisplayName <DisplayNameofAutomationAccount> `
        -SubscriptionId <SubscriptionId> `
        -CertPlainPassword "<StrongPassword>"  
<br>

När skriptet har körts går du till [exempelkoden](#sample-code-to-authenticate-with-resource-manager-resources) nedan för att autentisera med Resource Manager-resurser och validera konfigurationen av autentiseringsuppgifterna. 

### Skapa ett PowerShell-skript för ett klassiskt Kör som-konto

Följande konfigureras med PowerShell-skriptet nedan:

- En Automation-certifikatstillgång i det angivna Automation-kontot med namnet **AzureClassicRunAsCertificate**, som innehåller certifikatet som används för att autentisera dina runbooks.
- En Automation-anslutningstillgång i det angivna Automation-kontot med namnet **AzureClassicRunAsConnection**, som innehåller prenumerationens namn, prenumerations-ID:t och certifikattillgångens namn.

Skriptet skapar ett självsignerat hanteringscertifikat och sparar det i mappen för temporära filer på datorn under användarprofilen som används för att köra PowerShell-sessionen: *%USERPROFILE%\AppData\Local\Temp*.  När skriptet har körts måste du överföra Azure-hanteringscertifikatet till hanteringsarkivet för prenumerationen som Automation-kontot skapades i.  Stegen nedan beskriver hur du kör skriptet och laddar upp certifikatet.  

1. Spara följande skript på datorn.  I det här exemplet sparar du det med filnamnet **New-AzureClassicRunAsAccount.ps1**.

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
        [String] $CertPlainPassword,

        [Parameter(Mandatory=$false)]
        [int] $NoOfMonthsUntilExpired = 12
        )

        Login-AzureRmAccount
        Import-Module AzureRM.Resources
        $Subscription = Select-AzureRmSubscription -SubscriptionId $SubscriptionId
        $SubscriptionName = $subscription.Subscription.SubscriptionName

        $CurrentDate = Get-Date
        $EndDate = $CurrentDate.AddMonths($NoOfMonthsUntilExpired)
        $KeyId = (New-Guid).Guid
        $CertPath = Join-Path $env:TEMP ($ApplicationDisplayName + ".pfx")
        $CertPathCer = Join-Path $env:TEMP ($ApplicationDisplayName + ".cer")

        $Cert = New-SelfSignedCertificate -DnsName $ApplicationDisplayName -CertStoreLocation cert:\LocalMachine\My -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider"

        $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
        Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $CertPath -Password $CertPassword -Force | Write-Verbose
        Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $CertPathCer -Type CERT | Write-Verbose

        # Create the automation resources
        $ClassicCertificateAssetName = "AzureClassicRunAsCertificate"
        New-AzureRmAutomationCertificate -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Path $CertPath -Name $ClassicCertificateAssetName  -Password $CertPassword -Exportable | write-verbose

        # Create a Automation connection asset named AzureClassicRunAsConnection in the Automation account. This connection uses the ClassicCertificateAssetName.
        $ConnectionAssetName = "AzureClassicRunAsConnection"
        Remove-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -Force -ErrorAction SilentlyContinue
        $ConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicCertificateAssetName}
        New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureClassicCertificate -ConnectionFieldValues $ConnectionFieldValues 

        Write-Host -ForegroundColor red "Please upload the cert $CertPathCer to the Management store by following the steps below."
        Write-Host -ForegroundColor red "Log in to the Microsoft Azure Management portal (https://manage.windowsazure.com) and select Settings -> Management Certificates."
        Write-Host -ForegroundColor red "Then click Upload and upload the certificate $CertPathCer"

2. Starta **Windows PowerShell** från **startskärmen** med utökade användarrättigheter.  
3. Från det upphöjda PowerShell-kommandoradsgränssnittet går du till mappen som innehåller skriptet som du skapade i steg 1 och kör skriptet med dina värden för parametrarna *-ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName*, *-SubscriptionId* och *-CertPlainPassword*.<br>

    >[AZURE.NOTE] Du uppmanas att autentisera med Azure när du kör skriptet. Du måste logga in med ett konto som är medlem i rollen Prenumerationsadministratörer och som är medadministratör för prenumerationen.
   
        .\New-AzureClassicRunAsAccount.ps1 -ResourceGroup <ResourceGroupName> 
        -AutomationAccountName <NameofAutomationAccount> `
        -ApplicationDisplayName <DisplayNameofAutomationAccount> `
        -SubscriptionId <SubscriptionId> `
        -CertPlainPassword "<StrongPassword>" 

När skriptet har körs måste du kopiera certifikatet som skapades i användarprofilens **Temp**-mapp.  Följ anvisningarna för att [ladda upp ett API-hanteringscertifikat](../azure-api-management-certs.md) till den klassiska Azure-portalen och gå sedan till [exempelkoden](#sample-code-to-authenticate-with-service-management-resources) för att validera konfigurationen av autentiseringsuppgifterna med Service Management-resurser. 

## Exempelkod för att autentisera med Resource Manager-resurser

Du kan använda den uppdaterade exempelkoden nedan, som kommer från exempel-runbooken **AzureAutomationTutorialScript**, och autentisera med ”Kör som”-kontot för att hantera Resource Manager-resurser med dina runbooks.   

    $connectionName = "AzureRunAsConnection"
    $SubId = Get-AutomationVariable -Name 'SubscriptionId'
    try
    {
       # Get the connection "AzureRunAsConnection "
       $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
       
       "Logging in to Azure..."
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

## Exempelkod för att autentisera med Service Management-resurser

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


## Nästa steg

- Mer information om tjänstobjekt finns i [Programobjekt och tjänstobjekt](../active-directory/active-directory-application-objects.md).
- Mer information om rollbaserad åtkomstkontroll i Azure Automation finns i [Rollbaserad åtkomstkontroll i Azure Automation](../automation/automation-role-based-access-control.md).
- Mer information om certifikat och Azure-tjänster finns i [Översikt över certifikat för Azure Cloud Services](../cloud-services/cloud-services-certs-create.md)



<!--HONumber=Oct16_HO1-->


