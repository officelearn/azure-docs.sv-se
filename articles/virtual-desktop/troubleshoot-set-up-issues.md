---
title: Virtuellt skrivbord i Windows-klient och värden att skapa poolen - Azure
description: Hur du felsöker och löser klient och värden pool utfärdar under installationen av en miljö för virtuella Windows-skrivbordet organisationer.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 9f52d1b949310792ffedc6e4723fbfb423fb5dfc
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928504"
---
# <a name="tenant-and-host-pool-creation"></a>Klient- och skapa en pool

Den här artikeln beskriver problem under installationen av virtuella Windows-skrivbordet och relaterade session värd pool infrastruktur.

## <a name="provide-feedback"></a>Ge feedback

Vi inte är för närvarande tar supportärenden när virtuella Windows-skrivbordet är i förhandsversionen. Gå till den [Windows Desktop Tech-Community virtuella](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) att diskutera virtuellt skrivbord i Windows-tjänsten med produktteamet och aktiva community-medlemmar.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Windows 10 Enterprise flera session bilden hämtades

Om du vill använda Windows 10 Enterprise flera session bild, gå till Azure Marketplace, Välj **börjar** > **Microsoft Windows 10** > och [Windows 10 Enterprise för Förhandsversion av virtuella skrivbord, Version 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

![En skärmbild på hur du väljer Windows 10 Enterprise virtuella skrivbord i förhandsvisningen, Version 1809.](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Skapa virtuellt skrivbord i Windows-klient

Det här avsnittet beskrivs möjliga problem när du skapar virtuella skrivbordet i Windows-klient.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Fel: Användaren behörighet inte att skicka frågor till management-tjänsten

![Skärmbild av PowerShell-fönster som en användare inte behörighet att skicka frågor till management-tjänsten.](media/UserNotAuthorizedNewTenant.png)

Exempel på rådatafel:

```Error
   New-RdsTenant : User isn't authorized to query the management service.
   ActivityId: ad604c3a-85c6-4b41-9b81-5138162e5559
   Powershell commands to diagnose the failure:
   Get-RdsDiagnosticActivities -ActivityId ad604c3a-85c6-4b41-9b81-5138162e5559
   At line:1 char:1
   + New-RdsTenant -Name "testDesktopTenant" -AadTenantId "01234567-89ab-c ...
   + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       + CategoryInfo          : FromStdErr: (Microsoft.RDInf...nt.NewRdsTenant:NewRdsTenant) [New-RdsTenant], RdsPowerSh
      ellException
       + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.RDInfra.RDPowershell.Tenant.NewRdsTenant
```

**Orsak:** Den användare som är inloggad har inte tilldelats rollen TenantCreator i Azure Active Directory.

**Fix:** Följ instruktionerna i [tilldela rollen TenantCreator program till en användare i din Azure Active Directory-klient](https://docs.microsoft.com/azure/virtual-desktop/tenant-setup-azure-active-directory#assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant). När du har följt anvisningarna, har du en användare som tilldelats rollen TenantCreator.

![Skärmbild av TenantCreator rollen tilldelad.](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Skapa virtuella Windows-skrivbordet på nytt värden virtuella datorer

Värd för fjärrskrivbordssession virtuella datorer kan skapas på flera olika sätt, men Remote Desktop Services/Windows virtuellt skrivbord team har endast stöd för VM-etableringstillstånd problem relaterade till Azure Resource Manager-mallen. Azure Resource Manager-mallen finns i [Azure Marketplace](https://azuremarketplace.microsoft.com/) och [GitHub](https://github.com/).

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Problem med hjälp av Windows virtuella skrivbordet – etablera en värd pool Azure Marketplace-erbjudande

Windows virtuella skrivbordet – etablera en mall för värd-pool är tillgängliga från Azure Marketplace.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Fel: När du använder länken från GitHub, meddelandet ”skapa ett kostnadsfritt konto” visas

![Skärmbild för att skapa ett kostnadsfritt konto.](media/be615904ace9832754f0669de28abd94.png)

**Orsak 1:** Det inte finns aktiva prenumerationer i kontot används för att logga in på Azure eller det konto som används har inte behörighet att visa prenumerationer.

**Fix 1:** Logga in med ett konto som har deltagaråtkomst (minimum) till prenumerationen som var värd för fjärrskrivbordssession virtuella datorer ska distribueras.

**Orsak 2:** Prenumerationen som används är en del av en Microsoft Cloud Service Provider (CSP)-klient.

**Fix 2:** Gå till GitHub-plats för **skapa och etablera nya virtuella Windows-skrivbordet värd poolen** och Följ dessa anvisningar:

1. Högerklicka på **distribuera till Azure** och välj **kopia länka adress**.
2. Öppna **anteckningar** och klistra in länken.
3. Infoga CSP slutet kundnamn klient innan tecknet #.
4. Öppna en ny länk i en webbläsare och Azure-portalen läser in mallen.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Azure Resource Manager-mall och PowerShell Desired State Configuration (DSC) fel

Följ dessa instruktioner för att felsöka misslyckade distributioner av Azure Resource Manager-mallar och PowerShell DSC.

1. Granska fel i en distribution med hjälp av [visa distributionsåtgärder med Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).
2. Om det finns några fel i distributionen, granska fel i aktiviteten log med [visa aktivitetsloggar till granska åtgärder på resurser](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
3. När felet har identifierats, använda ett felmeddelande och resurserna i [felsöka vanliga Azure-distributionsfel med Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors) att åtgärda problemet.
4. Ta bort alla resurser som skapades under föregående distributionen och försök igen om du distribuerar mallen igen.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Fel: Distributionen misslyckades...<hostname>/joindomain

![Distributionen misslyckades-skärmbild.](media/e72df4d5c05d390620e07f0d7328d50f.png)

Exempel på rådatafel:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. 
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Orsak 1:** Autentiseringsuppgifterna för att ansluta till virtuella datorer till domänen är felaktiga.

**Fix 1:** Om du ser felet ”felaktiga autentiseringsuppgifter” för virtuella datorer inte är anslutna till domänen i [sessionskonfiguration värden VM](troubleshoot-vm-configuration.md).

**Orsak 2:** Domännamnet inte går att lösa.

**Fix 2:** Om du ser felet ”domännamnet inte lösa” för virtuella datorer inte är anslutna till domänen i [sessionskonfiguration värden VM](troubleshoot-vm-configuration.md).

### <a name="error-vmextensionprovisioningerror"></a>Fel: VMExtensionProvisioningError

![Skärmbild av din distributionen misslyckades med slutliga etableringstillståndet misslyckades.](media/7aaf15615309c18a984673be73ac969a.png)

**Orsak 1:** Tillfälligt fel med virtuella skrivbord i Windows-miljön.

**Orsak 2:** Tillfälligt fel med anslutningen.

**Fix:** Bekräfta virtuellt skrivbord i Windows-miljön är felfri genom att logga in med hjälp av PowerShell. Slutför registreringen för virtuell dator manuellt i [skapar du en värd-pool med PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Fel: Det specificerade administratörsanvändarnamnet är inte tillåten

![Skärmbild av distributionen misslyckades i en administratör som angetts är inte tillåten.](media/f2b3d3700e9517463ef88fa41875bac9.png)

Exempel på rådatafel:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Orsak:** Lösenordet innehåller otillåtna delsträngar (admin, administratör, rot).

**Fix:** Uppdatera användarnamn eller Använd olika användare.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Fel: Den virtuella datorn rapporterade ett fel vid bearbetningen av tillägget

![Skärmbild av resursåtgärden slutfördes med slutliga etableringstillståndet i din distributionen misslyckades.](media/49c4a1836a55d91cd65125cf227f411f.png)

Exempel på rådatafel:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-hostpool4-preview-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
 { "provisioningOperation": "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T21:43:05.1416423Z",
 "duration": "PT7M56.8150879S", "trackingId": "43c4f71f-557c-4abd-80c3-01f545375455", "statusCode": "Conflict",
 "statusMessage": { "status": "Failed", "error": { "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'. 
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few: 
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message: 
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] } }, "targetResource": 
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft. 
 Compute/virtualMachines/desktop-1/extensions/dscextension",
 "resourceType": "Microsoft.Compute/virtualMachines/extensions", "resourceName": "desktop-1/dscextension" } }}
```

**Orsak:** PowerShell DSC-tillägget kunde inte hämta administratörsåtkomst på den virtuella datorn.

**Fix:** Kontrollera användarnamnet och lösenordet har administratörsbehörighet på den virtuella datorn och kör Azure Resource Manager-mallen igen.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Fel: DeploymentFailed – PowerShell DSC-konfigurationen 'FirstSessionHost' slutfördes med fel

![Skärmbild av distributionen misslyckas med PowerShell DSC-konfigurationen 'FirstSessionHost' slutfördes med fel.](media/64870370bcbe1286906f34cf0a8646ab.png)

Exempel på rådatafel:

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list 
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",  
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

**Orsak:** PowerShell DSC-tillägget kunde inte hämta administratörsåtkomst på den virtuella datorn.

**Fix:** Kontrollera användarnamnet och lösenordet har administratörsbehörighet på den virtuella datorn och kör Azure Resource Manager-mallen igen.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Fel: DeploymentFailed – InvalidResourceReference

Exempel på rådatafel:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Orsak:** En del av resursgruppens namn används för vissa resurser som skapas av mallen. På grund av ett namn som matchar befintliga resurser kan mallen välja en befintlig resurs från en annan grupp.

**Fix:** När du kör Azure Resource Manager-mall för att distribuera virtuella datorer för fjärrskrivbordssessioner kan du se de första två tecknen unik för din prenumeration resursgruppens namn.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Fel: DeploymentFailed – InvalidResourceReference

Exempel på rådatafel:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

**Orsak:** Det här felet beror på det nätverkskort som har skapats med Azure Resource Manager-mallen har samma namn som ett annat nätverkskort redan i det virtuella nätverket.

**Fix:** Använd en annan värd-prefixet.

### <a name="error-deploymentfailed--error-downloading"></a>Fel: DeploymentFailed – fel vid nedladdning

Exempel på rådatafel:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-hostpool-3-preview-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-private-preview-
1/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Orsak:** Det här felet beror på en statisk väg, en brandväggsregel eller en NSG som blockerar hämtning av zip-filen som är kopplad till Azure Resource Manager-mallen.

**Fix:** Ta bort blockerar statisk väg, brandväggsregel och NSG. Du kan också öppna Azure Resource Manager-mall för json-fil i en textredigerare, ta länken till zip-fil och ladda ned resursen till en tillåten plats.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Fel: Användaren behörighet inte att skicka frågor till management-tjänsten

Exempel på rådatafel:

```Error
"response": { "content": { "startTime": "2019-04-01T17:45:33.3454563+00:00", "endTime": "2019-04-01T17:48:52.4392099+00:00", 
"status": "Failed", "error": { "code": "VMExtensionProvisioningError", "message": "VM has reported a failure when processing 
extension 'dscextension'. Error message: \"DSC Configuration 'FirstSessionHost' completed with error(s). 
Following are the first few: PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource
 functionality with error message: User is not authorized to query the management service.
\nActivityId: 1b4f2b37-59e9-411e-9d95-4f7ccd481233\nPowershell commands to diagnose the failure:
\nGet-RdsDiagnosticActivities -ActivityId 1b4f2b37-59e9-411e-9d95-4f7ccd481233\n 
The SendConfigurationApply function did not succeed.\"." }, "name": "2c3272ec-d25b-47e5-8d70-a7493e9dc473" } } }}
```

**Orsak:** Den angivna virtuella Windows-skrivbordet klientorganisationens administratören har inte en giltig rolltilldelning.

**Fix:** Användaren som skapade virtuella skrivbordet i Windows-klient måste logga in på Windows PowerShell för virtuella skrivbord och tilldela försök användaren en rolltilldelning. Om du använder GitHub Azure Resource Manager-mallens parametrar kan du följa instruktionerna med hjälp av PowerShell-kommandon:

```PowerShell
Add-RdsAccount -DeploymentUrl “https://rdbroker.wvd.microsoft.com”
Set-RdsContext -Name <Windows Virtual Desktop tenant group name>
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName “RDS Contributor” -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>Fel: Användaren måste Azure Multi-Factor Authentication (MFA)

![Skärmbild av distributionen misslyckades på grund av avsaknad av Multi-Factor Authentication (MFA)](media/MFARequiredError.png)

Exempel på rådatafel:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Orsak:** Den angivna virtuella Windows-skrivbordet innehavaradministration kräver Azure Multi-Factor Authentication (MFA) för inloggning.

**Fix:** Skapa ett tjänstobjekt och tilldela den en roll för virtuellt skrivbord i Windows-klienten genom att följa stegen i [självstudien: Skapa tjänstens huvudnamn och rolltilldelningar med PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-service-principal-role-powershell). När du har verifierat att du kan logga in på virtuella Windows-skrivbordet med tjänstens huvudnamn, kör du i Azure Marketplace-erbjudandet eller GitHub Azure Resource Manager-mallen, beroende på vilken metod du använder. Följ anvisningarna nedan för att ange rätt parametrar för metoden.

Om du använder Azure Marketplace erbjuder kan du ange värden för följande parametrar att kunna autentisera till virtuella Windows-skrivbordet:

- Virtuella Windows-skrivbordet klient RDS ägare: Tjänstens huvudnamn
- Program-ID: Program-ID för nytt huvudnamn för tjänsten som du har skapat
- Lösenord/Bekräfta lösenord: Lösenord-hemlighet som du skapade för tjänstens huvudnamn
- Azure AD-klient-ID: Azure AD-klient-ID för tjänstens huvudnamn som du har skapat

Om du använder GitHub Azure Resource Manager-mall kan du ange värden för följande parametrar att kunna autentisera till virtuella Windows-skrivbordet:

- Klient Admin användarens huvudnamn (UPN) eller program-ID: Program-ID för nytt huvudnamn för tjänsten som du har skapat
- Administratörslösenord för klient: Lösenord-hemlighet som du skapade för tjänstens huvudnamn
- IsServicePrincipal: **true**
- AadTenantId: Azure AD-klient-ID för tjänstens huvudnamn som du har skapat

## <a name="next-steps"></a>Nästa steg

- En översikt om hur du felsöker virtuella Windows-skrivbordet och Eskalering spårar finns [översikt, feedback och support](troubleshoot-set-up-overview.md).
- Felsökning av problem när du konfigurerar en virtuell dator (VM) i virtuella Windows-skrivbordet beskrivs [Session Värdkonfiguration för virtuell dator](troubleshoot-vm-configuration.md).
- Felsökning av problem med virtuella skrivbord i Windows-klientanslutningar beskrivs [Remote Desktop-klientanslutningar](troubleshoot-client-connection.md).
- När du felsöker problem när du använder PowerShell med virtuella Windows-skrivbordet, se [Windows PowerShell för virtuella skrivbord](troubleshoot-powershell.md).
- Läs mer om förhandsversionen av tjänsten i [Windows Desktop förhandsversionsmiljön](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Om du vill gå igenom en självstudiekurs om felsökning finns i [självstudien: Felsöka malldistributioner för Resource Manager-](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Läs om granskning åtgärder i [granskningsåtgärder med Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Mer information om åtgärder för att avgöra felen under distributionen, se [visa distributionsåtgärder](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).