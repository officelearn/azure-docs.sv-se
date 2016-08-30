<properties
    pageTitle="Konfigurera ett ”Kör som”-konto i Azure | Microsoft Azure"
    description="Självstudie som steg för steg beskriver hur du skapar, testar och använder autentisering med säkerhetsobjekt i Azure Automation."
    services="automation"
    documentationCenter=""
    authors="mgoedtel"
    manager="jwhit"
    editor=""
    keywords="service principal name, setspn, azure authentication"/>
<tags
    ms.service="automation"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/07/2016"
    ms.author="magoedte"/>

# Autentisera runbooks med ett ”Kör som”-konto i Azure
I det här avsnittet lär du dig hur du konfigurerar ett Automation-konto från Azure-portalen med den nya funktionen ”Kör som”-konto (kallas även för ett tjänstobjekt) för att komma åt Azure Resource Manager i din prenumeration med Automation-runbooks.  När du skapar ett nytt Automation-konto på Azure-portalen skapas automatiskt ett nytt tjänstobjekt och tilldelas till RBAC-rollen Deltagare (rollbaserad åtkomstkontroll) i prenumerationen som standard.  Detta gör processen enklare för dig och hjälper dig att snabbt börja skapa och distribuera runbooks för dina automatiseringsbehov.      

Med ett tjänstobjekt kan du:

* Ange en standardiserad metod för att autentisera med Azure när du hanterar Azure Resource Manager-resurser med hjälp av runbooks.
* Automatisera användningen av globala runbooks som konfigurerats i Azure Alerts.


>[AZURE.NOTE] Azure [Alerts-integreringsfunktionen](../azure-portal/insights-receive-alert-notifications.md) med globala Automation-runbooks kräver ett Automation-konto som har konfigurerats med ett tjänstobjekt. Du kan antingen välja ett Automation-konto som redan har en definierad tjänstobjektsanvändare eller välja att skapa en ny.



Vi visar hur du skapar Automation-kontot från Azure-portalen och hur du uppdaterar ett konto med ett ”Kör som”-konto med hjälp av Azure PowerShell, samt hur du autentiserar med tjänstobjektet i dina runbooks.  

## Skapa ett nytt Automation-konto från Azure-portalen
I det här avsnittet ska du utföra följande steg för att skapa ett nytt Azure Automation-konto och tjänstobjekt från Azure-portalen.

>[AZURE.NOTE] Användaren som utför de här stegen *måste* ha rollen som prenumerationsadministratör.

1. Logga in på Azure-portalen som tjänstadministratör för den Azure-prenumeration som du vill hantera.
2. Välj **Automation-konton**.
3. Klicka på **Lägg till** på bladet Automation-konton.<br>![Lägga till ett Automation-konto](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties.png)
4. På bladet **Lägg till Automation-konto** skriver du namnet på det nya Automation-kontot i rutan **Namn**.
5. Om du har mer än en prenumeration anger du den som du vill använda för det nya kontot, samt en ny eller befintlig **resursgrupp** och en **plats** för Azure-datacentret.
6. Kontrollera att värdet **Ja** har valts för **Skapa Kör som-konto i Azure** och klicka på knappen **Skapa**.  

    ![Varningsmeddelande för Lägg till Automation-konto](media/automation-sec-configure-azure-runas-account/add-account-decline-create-runas-msg.png)

    >[AZURE.NOTE] Om du väljer att inte skapa ”Kör som”-kontot genom att välja alternativet **Nej** visas ett varningsmeddelande på bladet **Lägg till Automation-konto**.  När kontot skapas och tilldelas till rollen **Deltagare** i prenumerationen har den ingen tillhörande autentiseringsidentitet i din prenumerationskatalogtjänst och har därför ingen åtkomst till resurser i din prenumeration.  Det förhindrar att runbooks som refererar till det här kontot kan autentisera och utföra åtgärder mot Azure Resource Manager-resurser.

    ![Varningsmeddelande för Lägg till Automation-konto](media/automation-sec-configure-azure-runas-account/add-automation-acct-properties-error.png)

    >[AZURE.NOTE] Om du får ett meddelande om nekad behörighet när du klickar på **Skapa** beror det på att ditt konto inte har rollen som prenumerationsadministratör.  

7. Medan Azure skapar Automation-kontot kan du följa förloppet under **Meddelanden** på menyn.

### Resurser som ingår
När Automation-kontot har skapats skapas flera resurser automatiskt åt dig. De sammanfattas i tabellen nedan.

Resurs|Beskrivning 
----|----
AzureAutomationTutorial-runbook|En exempel-runbook som demonstrerar hur du autentiserar med hjälp av ”Kör som”-kontot och hur du visar de första tio virtuella Azure-datorerna i din prenumeration.
AzureRunAsCertificate|Certifikatstillgång som skapas om du antingen valde att skapa ett ”Kör som”-konto när Automation-kontot skapades eller med PowerShell-skriptet nedan för ett befintligt konto.  Det här certifikatet har en livslängd på ett år. 
AzureRunAsConnection|Anslutningstillgång som skapas om du antingen valde att skapa ett ”Kör som”-konto när Automation-kontot skapades eller med PowerShell-skriptet nedan för ett befintligt konto.
Moduler|15 moduler med cmdlets för Azure, PowerShell och Automation som du kan börja använda i dina runbooks direkt.  

## Uppdatera ett Automation-konto med hjälp av PowerShell
Proceduren nedan uppdaterar ett befintligt Automation-konto och skapar tjänstobjektet med hjälp av PowerShell.  Den här proceduren är nödvändig om du skapade ett konto men valde att inte skapa ”Kör som”-kontot.

Kontrollera följande innan du fortsätter:

1. Du har laddat ned och installerat [Windows Management Framework (WMF) 4.0](https://www.microsoft.com/download/details.aspx?id=40855) om du kör Windows 7.   
    Om du kör Windows Server 2012 R2, Windows Server 2012, Windows 2008 R2, Windows 8.1 eller Windows 7 SP1 så finns [Windows Management Framework 5.0](https://www.microsoft.com/download/details.aspx?id=50395) tillgängligt för installation.
2. Azure PowerShell 1.0. Information om den här versionen och hur du installerar den finns i [Installera och konfigurera Azure PowerShell](../powershell-install-configure.md). 
3. Du har skapat ett Automation-konto.  Skriptet nedan refererar till det här kontot som värdet för parametrarna -AutomationAccountName och -ApplicationDisplayName.


PowerShell-skriptet konfigurerar följande:

* Ett Azure AD-program som autentiseras med det självsignerade certifikatet, skapar ett tjänstobjekt för programmet i Azure AD och tilldelar rollen Deltagare (du kan ändra rollen till Ägare eller en annan roll) för kontot i din aktuella prenumeration.  Mer information finns i artikeln [Rollbaserad åtkomstkontroll i Azure Automation](../automation/automation-role-based-access-control.md).  
* En Automation-certifikattillgång i det angivna Automation-kontot **AzureRunAsCertificate**, som innehåller certifikatet som används i tjänstobjektet.
* En Automation-anslutningstillgång i det angivna Automation-kontot med namnet **AzureRunAsConnection**, där applicationId, tenantId, subscriptionId och tumavtryck för certifikat lagras.  


### Kör PowerShell-skriptet

1. Spara följande skript på datorn.  I det här exemplet sparar du det med filnamnet **New-AzureServicePrincipal.ps1**.  

    ```
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
    While ($NewRole -eq $null -and $Retries -le 2)
    {
      # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
      Sleep 5
      New-AzureRMRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
      Sleep 5
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
    $ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId.SubscriptionId}
    New-AzureRmAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
    ```
<br>
2. Starta **Windows PowerShell** från **startskärmen** med utökade användarrättigheter.
3. Från det upphöjda PowerShell-kommandoradsgränssnittet går du till mappen som innehåller skriptet som du skapade i steg 1 och kör skriptet med dina värden för parametrarna *-ResourceGroup*, *-AutomationAccountName*, *-ApplicationDisplayName*, *-SubscriptionId* och *-CertPlainPassword*.<br>

    ```
    .\New-AzureServicePrincipal.ps1 -ResourceGroup <ResourceGroupName> 
     -AutomationAccountName <NameofAutomationAccount> `
     -ApplicationDisplayName <DisplayNameofAutomationAccount> `
     -SubscriptionId <SubscriptionId> `
     -CertPlainPassword "<StrongPassword>"
    ```   
<br>

    >[AZURE.NOTE] Du uppmanas att autentisera med Azure när du kör skriptet.  Du *måste* logga in med ett konto som har rollen som tjänstadministratör i prenumerationen.  
<br>
4. När skriptet har slutförts går du vidare till nästa avsnitt för att testa och verifiera den nya konfigurationen för autentiseringsuppgifter.

### Verifiera autentiseringen
Nu ska vi köra ett litet test för att bekräfta att du kan autentisera med det nya tjänstobjektet. Om autentiseringen misslyckas går du tillbaka till steg 1 och kontrollerar vart och ett av stegen.    

1. Öppna Automation-kontot som du skapade tidigare på Azure-portalen.  
2. Öppna listan med runbooks genom att klicka på panelen **Runbooks**.
3. Skapa en ny runbook genom att klicka på knappen **Lägg till en runbook**, klicka på bladet **Lägg till runbook** och välja **Skapa en ny runbook**.
4. Ge runbooken namnet *Test-SecPrin-Runbook* och välj PowerShell för **Typ av runbook**.  Skapa runbooken genom att klicka på **Skapa**.
5. Klistra in följande kod på arbetsytan på bladet **Redigera PowerShell-runbook**:<br>

    ```
     $Conn = Get-AutomationConnection -Name AzureRunAsConnection 
     Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
     -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    ```  
<br>
6. Spara runbooken genom att klicka på **Spara**.
7. Öppna bladet **Test** genom att klicka på **Testfönster**.
8. Starta testet genom att klicka på **Starta**.
9. Ett [runbook-jobb](automation-runbook-execution.md) skapas och dess status visas i rutan.  
10. Jobbets första status är *I kö* vilket betyder att det väntar på att en Runbook Worker i molnet ska bli tillgänglig. Därefter ändras statusen till *Startar* när en Runbook Worker gör anspråk på jobbet, och sedan till *Körs* när runbook-jobbet börjar köras.  
11. När runbook-jobbet är klart visas dess utdata. I vårt fall bör statusen **Slutförd** visas.<br> ![Runbook-test för säkerhetsobjekt](media/automation-sec-configure-azure-runas-account/runbook-test-results.png)<br>
12. Stäng bladet **Test** för att återgå till arbetsytan.
13. Stäng bladet **Redigera PowerShell-runbook**.
14. Stäng bladet **Test-SecPrin-Runbook**.

## Exempelkod för att autentisera med Resource Manager-resurser

Du kan använda den uppdaterade exempelkoden nedan, som kommer från exempel-runbooken AzureAutomationTutorial, och autentisera med ”Kör som”-kontot för att hantera Resource Manager-resurser med dina runbooks. 

   ```
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
   ```

Skriptet innehåller två ytterligare rader med kod som gör det möjligt att referera till en prenumerationskontext så att du enkelt kan arbeta mellan flera prenumerationer. En variabeltillgång med namnet SubscriptionId innehåller ID:t för prenumerationen och, efter Add-AzureRmAccount-cmdleten, definieras [cmdleten Set-AzureRmContext](https://msdn.microsoft.com/library/mt619263.aspx) med parameteruppsättningen *-SubscriptionId*. Om variabelnamnet är för generellt kan du ändra namnet på variabeln och ta med ett prefix eller en annan namnkonvention som gör det lättare att identifiera den. Du kan också använda parameteruppsättningen -SubscriptionName i stället för -SubscriptionId med en tillhörande variabeltillgång.  

## Nästa steg
- Mer information om tjänstobjekt finns i [Programobjekt och tjänstobjekt](../active-directory/active-directory-application-objects.md).
- Mer information om rollbaserad åtkomstkontroll i Azure Automation finns i [Rollbaserad åtkomstkontroll i Azure Automation](../automation/automation-role-based-access-control.md).



<!--HONumber=jun16_HO2-->


