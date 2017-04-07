---
title: "Konfigurera ett Kör som-konto i Azure | Microsoft Docs"
description: "Den här självstudien beskriver steg för steg hur du skapar, testar och använder autentisering med säkerhetsobjekt i Azure Automation."
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
keywords: "tjänstobjektnamn, setspn, azure-autentisering"
ms.assetid: 2f783441-15c7-4ea0-ba27-d7daa39b1dd3
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/06/2017
ms.author: magoedte
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: fbca3d195290551d37606e231b997a40a602351f
ms.lasthandoff: 03/28/2017


---

# <a name="authenticate-runbooks-with-an-azure-run-as-account"></a>Autentisera runbookflöden med ett Kör som-konto i Azure
Den här artikeln beskriver hur du konfigurerar ett Azure Automation-konto på Azure Portal. Du gör detta genom att använda funktionen Kör som-konto för att autentisera runbookflöden som hanterar resurser i Azure Resource Manager eller Azure Service Management.

När du skapar ett Automation-konto på Azure Portal skapar du automatiskt två konton:

* Ett Kör som-konto. Det här kontot skapar ett tjänstobjekt i Azure Active Directory (AD Azure) och ett certifikat. Det tilldelar också den rollbaserade åtkomstkontrollen (RBAC) för deltagare, som hanterar Resource Manager-resurser med hjälp av runbookflöden.
* Ett klassiskt Kör som-konto. Det här kontot överför ett hanteringscertifikat som används för att hantera Service Management-resurser eller klassiska resurser med hjälp av runbookflöden.

Genom att skapa ett Automation-konto kan du underlätta processen och snabbt börja skapa och distribuera runbookflöden för dina automatiseringsbehov.

Med Kör som-konton och klassiska Kör som-konton kan du:

* Tillhandahålla en standardiserad metod för att autentisera med Azure när du hanterar Resource Manager- eller Service Management-resurser från runbookflöden på Azure Portal.
* Automatisera användningen av globala runbook som du kan konfigurera i Azure Alerts.

> [!NOTE]
> För [integreringsfunktionen för Azure-aviseringar](../monitoring-and-diagnostics/insights-receive-alert-notifications.md) med globala Automation-runbookflöden krävs ett Automation-konto som konfigurerats med ett Kör som-konto och ett klassiskt Kör som-konto. Du kan välja ett Automation-konto som redan har definierade Kör som-konton och klassiska Kör som-konton eller välja att skapa ett nytt Automation-konto.
>  

Den här artikeln beskriver hur du skapar ett Automation-konto från Azure Portal, hur du uppdaterar ett Automation-konto med hjälp av Azure PowerShell, hur hanterar kontokonfigurationen samt hur du autentiserar i dina runbookflöden.

Innan du skapar ett Automation-konto är det viktigt att du förstår och tänker på följande:

* Nya Automation-konton som du skapar påverkar inte Automation-konton som du redan har skapat i den klassiska distributionsmodellen eller i Resource Manager-distributionsmodellen.
* Processen fungerar endast för Automation-konton som du skapar på Azure Portal. Om du försöker skapa ett konto från den klassiska Azure-portalen så replikeras inte Kör som-kontokonfigurationen.
* Om du redan har runbookflöden och tillgångar (till exempel scheman eller variabler) för att hantera klassiska resurser och du vill att dina runbookflöden ska autentisera med det nya klassiska Kör som-kontot, gör du något av följande:

  * Om du vill skapa ett klassiskt Kör som-konto följer du anvisningarna i avsnittet ”Hantera ett Kör som-konto”. 
  * Om du vill uppdatera ett befintligt konto använder du PowerShell-skriptet i avsnittet ”Uppdatera ett Automation-konto med hjälp av PowerShell”.
* För att autentisera med det nya Kör som-kontot och det klassiska Kör som-kontot för Automation måste du ändra dina befintliga runbookflöden med hjälp av exempelkoden i avsnittet [Exempel på autentiseringskod](#authentication-code-examples).

    >[!NOTE]
    >Kör som-kontot används för autentisering mot Resource Manager-resurser med hjälp av det certifikatbaserade tjänstobjektet. Det klassisk Kör som-kontot används för att autentisera mot Service Management-resurser med ett hanteringscertifikat.

## <a name="create-an-automation-account-from-the-azure-portal"></a>Skapa ett Automation-konto från Azure Portal
I det här avsnittet skapar du ett Azure Automation-konto från Azure Portal, vilket i sin tur skapar både ett Kör som-konto och ett klassiskt Kör som-konto.

>[!NOTE]
>För att kunna skapa ett Automation-konto måste du vara medlem i rollen Tjänstadministratörer eller vara medadministratör för den prenumeration som ger åtkomst till prenumerationen. Du måste också läggas till som användare i prenumerationens Active Directory-standardinstans. Kontot behöver inte tilldelas en privilegierad roll.
>
>Om du inte är medlem i prenumerationens Active Directory-instans innan du läggs till i rollen som medadministratör för prenumerationen, läggs du till i Active Directory som gäst. I detta fall visas varningen ”Du har inte behörighet att skapa ...” på bladet **Lägg till Automation-konto**.
>
>Användare som har lagts till i rollen som medadministratör kan först tas bort från prenumerationens Active Directory-instans och sedan läggas till igen så att de blir fullständiga användare i Active Directory. Du kan kontrollera detta i rutan **Azure Active Directory** på Azure Portal genom att välja **Användare och grupper**, välja **Alla användare**, välja den specifika användaren och sedan välja **Profil**. Värdet för attributet **Användartyp** under användarens profil bör inte vara lika med **Gäst**.
>

1. Logga in på Azure Portal med ett konto som är medlem i rollen för prenumerationsadministratörer och som är medadministratör för prenumerationen.

2. Välj **Automation-konton**.

3. Klicka på **Lägg till** på bladet **Automation-konton**.
Bladet **Lägg till Automation-konto** öppnas.

 ![Bladet ”Lägg till Automation-konto”](media/automation-sec-configure-azure-runas-account/create-automation-account-properties-b.png)

   > [!NOTE]
   > Om ditt konto inte är medlem i rollen för prenumerationsadministratörer och medadministratör för prenumerationen visas följande varning på bladet **Lägg till Automation-konto**:
   >
   >![Varningsmeddelande för Lägg till Automation-konto](media/automation-sec-configure-azure-runas-account/create-account-without-perms.png)
   >
   >

4. På bladet **Lägg till Automation-konto** skriver du namnet på det nya Automation-kontot i rutan **Namn**.

5. Om du har mer än en prenumeration gör du följande:

    a. Under **Prenumeration** anger du en prenumeration för det nya kontot.

    b. Under **Resursgrupp** klickar du på **Skapa ny** eller på **Använd befintlig**.

    c. Under **Plats** anger du ett Azure-datacenter.

6. Under **Skapa Kör som-konto i Azure** väljer du **Ja** och klickar sedan på **Skapa**.

   > [!NOTE]
   > Om du väljer att inte skapa Kör som-kontot genom att välja **Nej** visas ett varningsmeddelande på bladet **Lägg till Automation-konto**. Även om kontot skapas på Azure Portal har det ingen motsvarande autentiseringsidentitet i katalogtjänsten för din klassiska prenumeration eller Resource Manager-prenumeration. Kontot har därför inte åtkomst till resurser i din prenumeration. Det här scenariot förhindrar att runbookflöden som refererar till det här kontot autentiseras och utför åtgärder mot resurser i dessa distributionsmodeller.
   >
   > ![Varningsmeddelande på bladet ”Lägg till Automation-konto”](media/automation-sec-configure-azure-runas-account/create-account-decline-create-runas-msg.png)
   >
   > Dessutom, eftersom tjänstobjektet inte skapas, så tilldelas inte rollen Deltagare.
   >

7. Medan Azure skapar Automation-kontot kan du följa förloppet under **Meddelanden** på menyn.

### <a name="resources"></a>Resurser
När Automation-kontot har skapats skapas flera resurser automatiskt. Resurserna sammanfattas i följande två tabeller:

#### <a name="run-as-account-resources"></a>Resurser för Kör som-konton

| Resurs | Beskrivning |
| --- | --- |
| AzureAutomationTutorial-runbook | Ett exempel på en grafisk runbook som visar hur du autentiserar med hjälp av Kör som-kontot och hur du hämtar alla Resource Manager-resurser. |
| AzureAutomationTutorialScript-runbook | Ett exempel på en PowerShell-runbook som visar hur du autentiserar med hjälp av Kör som-kontot och hur du hämtar alla Resource Manager-resurser. |
| AzureRunAsCertificate | Certifikattillgången som skapas automatiskt när du skapar ett Automation-konto eller använder följande PowerShell-skript för ett befintligt konto. Certifikatet gör att du kan autentisera med Azure så att du kan hantera Azure Resource Manager-resurser från runbookflöden. Certifikatet har en livslängd på ett år. |
| AzureRunAsConnection | Anslutningstillgången som skapas automatiskt när du skapar ett Automation-konto eller använder PowerShell-skriptet för ett befintligt konto. |

#### <a name="classic-run-as-account-resources"></a>Resurser för klassiska Kör som-konton

| Resurs | Beskrivning |
| --- | --- |
| AzureClassicAutomationTutorial-runbook | Ett exempel på en grafisk runbook som hämtar alla virtuella datorer som skapas med den klassiska distributionsmodellen i en prenumeration med hjälp av det klassiska Kör som-kontot (certifikat) och som sedan skriver namnet och statusen för de virtuella datorerna. |
| AzureClassicAutomationTutorial Script-runbook | Ett exempel på en PowerShell-runbook som hämtar alla klassiska virtuella datorer i en prenumeration med hjälp av det klassiska Kör som-kontot (certifikat) och som sedan skriver namnet och statusen för de virtuella datorerna. |
| AzureClassicRunAsCertificate | Certifikattillgången som skapas automatiskt och som du använder för att autentisera med Azure så att du kan hantera klassiska Azure-resurser från runbookflöden. Certifikatet har en livslängd på ett år. |
| AzureClassicRunAsConnection | Anslutningstillgången som skapas automatiskt och som du använder för att autentisera med Azure så att du kan hantera klassiska Azure-resurser från runbookflöden. |

## <a name="verify-run-as-authentication"></a>Verifiera Kör som-autentisering
Utför ett litet test för att bekräfta att du kan autentisera med hjälp av det nya Kör som-kontot.

1. Öppna Automation-kontot som du skapade tidigare på Azure Portal.

2. Öppna listan med runbookflöden genom att klicka på panelen med **runbookflöden**.

3. Välj **AzureAutomationTutorialScript**-runbooken och klicka sedan på **Starta** för att starta runbooken. Följande sker:
 * Ett [runbook-jobb](automation-runbook-execution.md) skapas, bladet **Jobb** visas och jobbstatusen visas på panelen **Jobbsammanfattning**.
 * Jobbets första status är **I kö** vilket betyder att det väntar på att en runbook-arbetsroll i molnet ska bli tillgänglig.
 * Statusen ändras till **Startar** när ett arbetsobjekt begär jobbet.
 * Statusen ändras till **Körs** när runbooken börjar köras.
 * När runbook-jobbet har körts bör statusen vara **Slutfört**.

       ![Security Principal Runbook Test](media/automation-sec-configure-azure-runas-account/job-summary-automationtutorialscript.png)
4. Om du vill visa ett detaljerat resultat av runbook-jobbet klickar du på panelen **Utdata**.  
Bladet **Utdata** visas och anger att runbooken har autentiserats och returnerat en lista över alla tillgängliga resurser i resursgruppen.

5. Stäng bladet **Utdata** och gå tillbaka till bladet **Jobbsammanfattning**.

6. Stäng bladet **Jobbsammanfattning** och motsvarande blad för **AzureAutomationTutorialScript**-runbooken.

## <a name="verify-classic-run-as-authentication"></a>Verifiera klassisk Kör som-autentisering
Utför ett liknande litet test för att bekräfta att du kan autentisera med hjälp av det nya klassiska Kör som-kontot.

1. Öppna Automation-kontot som du skapade tidigare på Azure Portal.

2. Öppna listan med runbookflöden genom att klicka på panelen med **runbookflöden**.

3. Välj **AzureAutomationTutorialScript**-runbooken och klicka på **Starta** för att starta runbooken. Följande sker:

 * Ett [runbook-jobb](automation-runbook-execution.md) skapas, bladet **Jobb** visas och jobbstatusen visas på panelen **Jobbsammanfattning**.
 * Jobbets första status är **I kö** vilket betyder att det väntar på att en runbook-arbetsroll i molnet ska bli tillgänglig.
 * Statusen ändras till **Startar** när ett arbetsobjekt begär jobbet.
 * Statusen ändras till **Körs** när runbooken börjar köras.
 * När runbook-jobbet har körts bör statusen vara **Slutfört**.

    ![Runbook-test för säkerhetsobjekt](media/automation-sec-configure-azure-runas-account/job-summary-automationclassictutorialscript.png)<br>
4. Om du vill visa ett detaljerat resultat av runbook-jobbet klickar du på panelen **Utdata**.  
Bladet **Utdata** visas och anger att runbooken har autentiserats och returnerat en lista över alla klassiska virtuella datorer i prenumerationen.

5. Stäng bladet **Utdata** och gå tillbaka till bladet **Jobbsammanfattning**.

6. Stäng bladet **Jobbsammanfattning** och motsvarande blad för **AzureAutomationTutorialScript**-runbooken.

## <a name="managing-your-run-as-account"></a>Hantera ett Kör som-konto
Någon gång innan Automation-kontot går ut måste du förnya certifikatet. Om du tror att ditt Kör som-konto har komprometterats kan du ta bort och återskapa det. Det här avsnittet beskriver hur du utför dessa åtgärder.

### <a name="self-signed-certificate-renewal"></a>Förnya självsignerade certifikat
Det självsignerade certifikatet som du skapade för Kör som-kontot går ut ett år efter det datum då det skapades. Du kan förnya det när som helst innan det upphör att gälla. När du förnyar det behålls det aktuella giltiga certifikatet för att säkerställa att alla runbookflöden som placerats i kö eller som körs aktivt, och som autentiserar med Kör som-kontot, inte påverkas negativt. Certifikatet förblir giltigt fram till dess förfallodatum.

> [!NOTE]
> Om du har konfigurerat ditt Kör som-konto för Automation att använda ett certifikat som utfärdats av din företagscertifikatutfärdare och du använder det här alternativet, så ersätts företagscertifikatet av ett självsignerat certifikat.

Du förnyar certifikatet genom att göra följande:

1. Öppna ditt Automation-konto på Azure Portal.

2. På bladet **Automation-konto** väljer du **Kör som-konton** under **Kontoinställningar** i rutan **Kontoegenskaper**.

    ![Egenskapsrutan för Automation-konto](media/automation-sec-configure-azure-runas-account/automation-account-properties-pane.png)
3. På egenskapsbladet för **Kör som-konton** väljer du antingen Kör som-kontot eller det klassiska Kör som-kontot som du vill förnya certifikatet för.

4. På bladet **Egenskaper** för det valda kontot klickar du på **Förnya certifikat**.

    ![Förnya certifikat för Kör som-konto](media/automation-sec-configure-azure-runas-account/automation-account-renew-runas-certificate.png)

5. Medan certifikatet förnyas kan du följa förloppet under **Meddelanden** på menyn.

### <a name="delete-a-run-as-or-classic-run-as-account"></a>Ta bort ett Kör som-konto eller ett klassiskt Kör som-konto
Det här avsnittet beskriver hur du tar bort och sedan återskapar ett Kör som-konto eller ett klassiskt Kör som-konto. När du utför den här åtgärden behålls Automation-kontot. När du har tagit bort ett Kör som-konto eller ett klassiskt Kör som-konto kan du återskapa det på Azure Portal.

1. Öppna ditt Automation-konto på Azure Portal.

2. På bladet **Automation-konto** väljer du **Kör som-konton** i rutan för kontoegenskaper.

3. På egenskapsbladet för **Kör som-konton** väljer du antingen Kör som-kontot eller det klassiska Kör som-kontot som du vill ta bort. Klicka på **Ta bort** på bladet **Egenskaper** för det valda kontot.

 ![Ta bort Kör som-konto](media/automation-sec-configure-azure-runas-account/automation-account-delete-runas.png)

4. Medan kontot tas bort kan du följa förloppet under **Meddelanden** på menyn.

5. När kontot har tagits bort måste du återskapa det på egenskapsbladet för **Kör som-konton** genom att välja alternativet Skapa för **Kör som-konto i Azure**.

 ![Återskapa Kör som-kontot för Automation](media/automation-sec-configure-azure-runas-account/automation-account-create-runas.png)

### <a name="misconfiguration"></a>Felaktig konfiguration
Vissa konfigurationsobjekt som krävs för att Kör som-kontot eller det klassiska Kör som-kontot ska fungera kanske har tagits bort eller kanske inte skapades korrekt under den ursprungliga konfigurationen. Exempel på dessa objekt är:

* Certifikattillgång
* Anslutningstillgång
* Kör som-konto har tagits bort från deltagarrollen
* Huvudnamn för tjänsten eller program i Azure AD

I den föregående instansen och andra instanser av felaktiga konfigurationer identifierar Automation-kontot ändringarna och visar statusen *Ofullständig* på egenskapsbladet för **Kör som-konton** för kontot.

![Konfigurationsstatusen Ofullständig för Kör som-konto](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config.png)

När du väljer Kör som-kontot visas följande felmeddelande i rutan **Egenskaper** för kontot:

![Varningsmeddelande om ofullständig konfiguration av Kör som-konto](media/automation-sec-configure-azure-runas-account/automation-account-runas-incomplete-config-msg.png).

Du kan snabbt lösa dessa problem med Kör som-kontot genom att ta bort och återskapa kontot.

## <a name="update-your-automation-account-by-using-powershell"></a>Uppdatera Automation-kontot med hjälp av PowerShell
Du kan använda PowerShell för att uppdatera ett befintligt Automation-konto om:

* Du skapar ett Automation-konto men väljer att inte skapa Kör som-kontot.
* Du redan har ett Automation-konto för att hantera Resource Manager-resurser och du vill uppdatera det med ett Kör som-konto för runbook-autentisering.
* Du redan hanterar klassiska resurser med hjälp av ett Automation-konto och du vill uppdatera det och använda det klassiska Kör som-kontot i stället för att skapa ett nytt konto och migrera dina runbookflöden och tillgångar till det.   
* Du vill skapa ett Kör som-konto och ett klassiskt Kör som-konto genom att använda ett certifikat utfärdat av en företagscertifikatutfärdare (CA).

Skriptet har följande krav:

* Skriptet kan endast köras i Windows 10 och Windows Server 2016 med Azure Resource Manager-moduler med version 2.01 och senare. Det stöds inte i tidigare versioner av Windows.
* Azure PowerShell 1.0 och senare. Information om PowerShell 1.0-versionen finns i [Installera och konfigurera Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Ett Automation-konto, som refereras som värdet för *-AutomationAccountName*- och *-ApplicationDisplayName*-parametrarna i följande PowerShell-skript.

Du hämtar värdena för *SubscriptionID*, *ResourceGroup* och *AutomationAccountName*, som är obligatoriska parametrar för skripten, genom att göra följande:
1. På Azure Portal väljer du ditt Automation-konto på bladet **Automation-konto** och väljer sedan **Alla inställningar**. 
2. På bladet **Alla inställningar** väljer du **Egenskaper** under **Kontoinställningar**. 
3. Notera värdena på bladet **Egenskaper**.

![Bladet Egenskaper för Automation-kontot](media/automation-sec-configure-azure-runas-account/automation-account-properties.png)  

### <a name="create-a-run-as-account-powershell-script"></a>Skapa ett PowerShell-skript för ett Kör som-konto
Det här PowerShell-skriptet har stöd för följande konfigurationer:

* Skapa ett Kör som-konto med hjälp av ett självsignerat certifikat.
* Skapa ett Kör som-konto och ett klassiska Kör som-konto med hjälp av ett självsignerat certifikat.
* Skapa ett Kör som-konto och ett klassiskt Kör som-konto med hjälp av ett företagscertifikat.
* Skapa ett Kör som-konto och ett klassiskt Kör som-konto med hjälp av ett självsignerat certifikat i Azure Government-molnet.

Skriptet skapar följande objekt, beroende på de konfigurationsalternativ som du väljer.

**För Kör som-konton:**

* Skapar ett Azure AD-program som ska exporteras med det självsignerade certifikatets eller företagscertifikatets offentliga nyckel, skapar ett tjänstobjektskonto för programmet i Azure AD och tilldelar rollen Deltagare för kontot i din aktuella prenumeration. Du kan ändra den här inställningen till Ägare eller en annan roll. Mer information finns i [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md).
* Skapar en Automation-certifikattillgång med namnet *AzureRunAsCertificate* i det angivna Automation-kontot. Certifikattillgången innehåller certifikatets privata nyckel som används av Azure AD-programmet.
* Skapar en Automation-anslutningstillgång med namnet *AzureRunAsConnection* i det angivna Automation-kontot. Anslutningstillgången innehåller applicationId, tenantId, subscriptionId och certifikatets tumavtryck.

**För klassiska Kör som-konton:**

* Skapar en Automation-certifikattillgång med namnet *AzureClassicRunAsCertificate* i det angivna Automation-kontot. Certifikattillgången innehåller den privata nyckelns certifikat som används av hanteringscertifikatet.
* Skapar en Automation-anslutningstillgång med namnet *AzureClassicRunAsConnection* i det angivna Automation-kontot. Anslutningstillgången innehåller prenumerationsnamnet, subscriptionId och certifikattillgångens namn.

>[!NOTE]
> Om du väljer något av alternativen för att skapa ett klassiskt Kör som-konto laddar du upp det offentliga certifikatet (filnamnstillägget .cer) när skriptet har körts till hanteringsarkivet för den prenumeration som Automation-kontot skapades i.
> 

Så här kör du skriptet och laddar upp certifikatet:

1. Spara följande skript på datorn. I det här exemplet sparar du det med filnamnet *New-RunAsAccount.ps1*.

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
        [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
        )

        function CreateSelfSignedCertificate([string] $keyVaultName, [string] $certificateName, [string] $selfSignedCertPlainPassword,
                                      [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
        $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
           -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
           -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired)

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
        $KeyCredential.EndDate = $KeyCredential.EndDate.AddDays(-1)
        $KeyCredential.KeyId = $KeyId
        $KeyCredential.CertValue  = $keyValue

        # Use key credentials and create an Azure AD application
        $Application = New-AzureRmADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $KeyId) -KeyCredentials $KeyCredential
        $ServicePrincipal = New-AzureRMADServicePrincipal -ApplicationId $Application.ApplicationId
        $GetServicePrincipal = Get-AzureRmADServicePrincipal -ObjectId $ServicePrincipal.Id

        # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
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

        # Create a Run As account by using a service principal
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
          CreateSelfSignedCertificate $KeyVaultName $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create a service principal
        $PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
        $ApplicationId=CreateServicePrincipal $PfxCert $ApplicationDisplayName

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

        # Populate the ConnectionFieldValues
        $SubscriptionInfo = Get-AzureRmSubscription -SubscriptionId $SubscriptionId
        $TenantID = $SubscriptionInfo | Select TenantId -First 1
        $Thumbprint = $PfxCert.Thumbprint
        $ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

        if ($CreateClassicRunAsAccount) {
            # Create a Run As account by using a service principal
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
             CreateSelfSignedCertificate $KeyVaultName $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
        }

        # Create the Automation certificate asset
        CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

        # Populate the ConnectionFieldValues
        $SubscriptionName = $subscription.Subscription.SubscriptionName
        $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

        # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
        CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName $ClassicRunAsAccountConnectionFieldValues

        Write-Host -ForegroundColor red $UploadMessage
        }

2. Klicka på **Start** på datorn och starta sedan **Windows PowerShell** med utökade användarrättigheter.

3. Från det upphöjda PowerShell-kommandoradsgränssnittet går du till mappen som innehåller skriptet som du skapade i steg 1.

4. Kör skriptet genom att använda de parametervärden för konfigurationen som du behöver.

    **Skapa ett Kör som-konto med hjälp av ett självsignerat certifikat**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false`

    **Skapa ett Kör som-konto och ett klassiska Kör som-konto med hjälp av ett självsignerat certifikat**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true`

    **Skapa ett Kör som-konto och ett klassiskt Kör som-konto med hjälp av ett företagscertifikat**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>`

    **Skapa ett Kör som-konto och ett klassiskt Kör som-konto med hjälp av ett självsignerat certifikat i Azure Government-molnet**  
    `.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment`

    > [!NOTE]
    > När skriptet har körts uppmanas du att autentisera med Azure. Logga in med ett konto som är medlem i rollen för prenumerationsadministratörer och som är medadministratör för prenumerationen.
    >
    >

Notera följande när skriptet har körts:
* Om du skapade ett klassiskt Kör som-konto med ett självsignerat offentligt certifikat (CER-fil) skapas och sparas det av skriptet i mappen för tillfälliga filer på datorn under användarprofilen *%USERPROFILE%\AppData\Local\Temp*, som du använde för att köra PowerShell-sessionen.
* Om du skapade ett klassiskt Kör som-konto med ett offentligt företagscertifikat (CER-fil) använder du det här certifikatet. Följ anvisningarna för hur du [laddar upp ett hanterings-API-certifikat till den klassiska Azure-portalen](../azure-api-management-certs.md) och verifiera sedan konfigurationen av autentiseringsuppgifterna med Service Management-resurser med hjälp av [exempelkoden för autentisering med Service Management-resurser](#sample-code-to-authenticate-with-service-management-resources). 
* Om du *inte* skapade ett klassiskt Kör som-konto autentiserar du med Resource Manager-resurser och verifierar konfigurationen av autentiseringsuppgifterna med hjälp av [exempelkoden för autentisering med Service Management-resurser](#sample-code-to-authenticate-with-resource-manager-resources).

## <a name="sample-code-to-authenticate-with-resource-manager-resources"></a>Exempelkod för att autentisera med Resource Manager-resurser
Du kan använda följande uppdaterade exempelkod, som kommer från exempel-runbooken *AzureAutomationTutorialScript*, om du vill autentisera med hjälp av Kör som-kontot för att hantera Resource Manager-resurser med dina runbookflöden.

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

Skriptet innehåller två ytterligare rader med kod som gör det möjligt att referera till en prenumerationskontext så att du enkelt kan arbeta med flera prenumerationer. En variabeltillgång med namnet *SubscriptionId* innehåller ID:t för prenumerationen. Efter cmdlet-instruktionen `Add-AzureRmAccount` används cmdleten [ `Set-AzureRmContext` ](https://msdn.microsoft.com/library/mt619263.aspx) med parameteruppsättningen *-SubscriptionId*. Om variabelnamnet är för allmänt kan du ändra det och lägga till ett prefix eller använda en annan namngivningskonvention så att det blir lättare att identifiera. Du kan också använda parameteruppsättningen *-SubscriptionName* i stället för *-SubscriptionId* med en tillhörande variabeltillgång.

Den cmdlet som du använder för autentisering i runbooken, `Add-AzureRmAccount`, använder parameteruppsättningen *ServicePrincipalCertificate*. Den autentiserar med hjälp av tjänstobjektscertifikatet, inte användarautentiseringsuppgifterna.

## <a name="sample-code-to-authenticate-with-service-management-resources"></a>Exempelkod för att autentisera med Service Management-resurser
Du kan använda följande uppdaterade exempelkod, som kommer från exempel-runbooken *AzureClassicAutomationTutorialScript*, om du vill autentisera med hjälp av det klassiska Kör som-kontot för att hantera klassiska resurser med dina runbookflöden.

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
* [Program och tjänstobjekt i Azure AD](../active-directory/active-directory-application-objects.md)
* [Rollbaserad åtkomstkontroll i Azure Automation](automation-role-based-access-control.md)
* [Certifikatöversikt för Azure Cloud Services](../cloud-services/cloud-services-certs-create.md)

