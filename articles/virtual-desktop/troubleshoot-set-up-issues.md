---
title: Windows Virtual Desktop-värdpool för klientpool – Azure
description: Felsöka och lösa problem med klient- och värdpoolen under installationen av en windows virtual desktop-klientmiljö.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 01/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 36b15b41279edc60d337a7ba70abe2ca64d4bc7f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371604"
---
# <a name="tenant-and-host-pool-creation"></a>Skapa klient- och värdpool

Den här artikeln innehåller problem under den första installationen av Windows Virtual Desktop-klienten och den relaterade värdpoolinfrastrukturen för sessionsvärd.

## <a name="provide-feedback"></a>Ge feedback

Besök [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) för att diskutera Windows Virtual Desktop-tjänsten med produktteamet och aktiva communitymedlemmar.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Hämta avbildningen för flera sessionser för Windows 10 Enterprise

Om du vill använda multisessionsavbildningen för Windows 10 Enterprise går du till Azure Marketplace och väljer **Kom igång** > **Microsoft Windows 10** > och Windows [10 Enterprise för virtuella skrivbord, version 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

![En skärmbild av valet av Windows 10 Enterprise för virtuella skrivbord, version 1809.](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Skapa Windows Virtual Desktop-klientorganisation

Det här avsnittet beskriver potentiella problem när du skapar windows virtual desktop-klienten.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Fel: Användaren har inte behörighet att fråga hanteringstjänsten

![Skärmbild av PowerShell-fönstret där en användare inte har behörighet att fråga hanteringstjänsten.](media/UserNotAuthorizedNewTenant.png)

Exempel på råfel:

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

**Orsak:** Användaren som är inloggad har inte tilldelats rollen TenantCreator i sin Active Directory i Azure.

**Rättad:** Följ instruktionerna i [Tilldela programrollen Klientcreator till en användare i din Azure Active Directory-klient](tenant-setup-azure-active-directory.md#assign-the-tenantcreator-application-role). När du har följt instruktionerna har du en användare tilldelad rollen TenantCreator.

![Skärmbild av klientcreatorrollen tilldelad.](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Virtuella datorer för Windows Virtual Desktop-session

Virtuella datorer för sessionsvärdar kan skapas på flera sätt, men Windows Virtual Desktop-teamet stöder endast vm-etableringsproblem relaterade till [Azure Marketplace-erbjudandet.](https://azuremarketplace.microsoft.com/) Mer information finns i [Problem med att använda Windows Virtual Desktop – Etablera en värdpool azure Marketplace-erbjudande](#issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering).

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Problem med windows virtual desktop – Etablera ett Azure Marketplace-erbjudande för värdpool

Windows Virtual Desktop – Etablera en värdpoolmall är tillgänglig från Azure Marketplace.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Fel: När du använder länken från GitHub visas meddelandet "Skapa ett kostnadsfritt konto"

![Skärmbild för att skapa ett gratis konto.](media/be615904ace9832754f0669de28abd94.png)

**Orsak 1:** Det finns inte aktiva prenumerationer i kontot som används för att logga in på Azure eller så har kontot behörighet att visa prenumerationerna.

**Åtgärda 1:** Logga in med ett konto som har deltagaråtkomst (minst) till prenumerationen där virtuella datorer för sessionsvärdar ska distribueras.

**Orsak 2:** Prenumerationen som används är en del av en Microsoft Cloud Service Provider (CSP) klientorganisation.

**Åtgärda 2:** Gå till GitHub-platsen för **skapa och etablera ny värdpool för Windows Virtual Desktop** och följ följande instruktioner:

1. Högerklicka på **Distribuera till Azure** och välj **Kopiera länkadress**.
2. Öppna **Anteckningar** och klistra in länken.
3. Innan tecknet # infogar du klientnamnet för CSP-slutkunden.
4. Öppna den nya länken i en webbläsare så läser Azure-portalen in mallen.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

### <a name="error-you-receive-template-deployment-is-not-valid-error"></a>Fel: Felet "malldistributionen är ogiltig" visas

![Skärmbild av "malldistribution ... är ogiltigt" fel](media/troubleshooting-marketplace-validation-error-generic.png)

Innan du vidtar särskilda åtgärder måste du kontrollera aktivitetsloggen för att se det detaljerade felet för den misslyckade distributionsvalideringen.

Så här visar du felet i aktivitetsloggen:

1. Avsluta det aktuella Azure Marketplace-distributionserbjudandet.
2. Sök efter i det övre sökfältet och välj **Aktivitetslogg**.
3. Hitta en aktivitet med namnet **Verifiera distribution** som har **statusen Misslyckades** och välj aktiviteten.
   ![Skärmbild av enskilda **Validate Deployment**-aktivitet med statusen **Failed**](media/troubleshooting-marketplace-validation-error-activity-summary.png)

4. Välj JSON och rulla sedan ned till skärmens nederkant tills du ser fältet "statusMessage".
   ![Skärmbild av misslyckad aktivitet, med en röd ruta runt statusMessage-egenskapen för JSON-texten.](media/troubleshooting-marketplace-validation-error-json-boxed.png)

Om din operationsmall överskrider kvotgränsen kan du göra något av följande för att åtgärda det:

 - Kör Azure Marketplace med de parametrar som du använde första gången, men den här gången använder du färre virtuella datorer och VM-kärnor.
 - Öppna länken som visas i **fältet statusMessage** i en webbläsare för att skicka en begäran om att öka kvoten för din Azure-prenumeration för den angivna VM SKU.

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Azure Resource Manager-mall och DSC-fel (PowerShell Desired State Configuration)

Följ dessa instruktioner för att felsöka misslyckade distributioner av Azure Resource Manager-mallar och PowerShell DSC.

1. Granska fel i distributionen med [Visa distributionsåtgärder med Azure Resource Manager](../azure-resource-manager/resource-manager-deployment-operations.md).
2. Om det inte finns några fel i distributionen granskar du fel i aktivitetsloggen med [visa aktivitetsloggar för att granska åtgärder på resurser](../azure-resource-manager/resource-group-audit.md).
3. När felet har identifierats använder du felmeddelandet och resurserna i [Felsöka vanliga Azure-distributionsfel med Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md) för att åtgärda problemet.
4. Ta bort alla resurser som skapats under den tidigare distributionen och försök distribuera mallen igen.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Fel: Distributionen misslyckades....\<värdnamn>/joindomain

![Skärmbilden av distributionen misslyckades.](media/e72df4d5c05d390620e07f0d7328d50f.png)

Exempel på råfel:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Orsak 1:** Autentiseringsuppgifter som anges för att ansluta virtuella datorer till domänen är felaktiga.

**Åtgärda 1:** Se felet "Felaktiga autentiseringsuppgifter" för virtuella datorer är inte anslutna till domänen i [vm-konfigurationen](troubleshoot-vm-configuration.md)för session värd .

**Orsak 2:** Domännamnet löser inte.

**Åtgärda 2:** Se [Fel: Domännamnet matchas inte](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve) i [VM-konfigurationen för session värd](troubleshoot-vm-configuration.md).

**Orsak 3:** Dns-konfigurationen (Virtual Network) är inställd på **Standard**.

Så här åtgärdar du följande:

1. Öppna Azure Portal och gå till fliken **Virtuella nätverk.**
2. Hitta ditt virtuella nätverk och välj sedan **DNS-servrar**.
3. MENYN DNS-servrar ska visas till höger på skärmen. Välj **Anpassad**på den menyn .
4. Kontrollera att DNS-servrarna som anges under Anpassad matchar domänkontrollanten eller Active Directory-domänen. Om du inte ser DNS-servern kan du lägga till den genom att ange dess värde i fältet **Lägg till DNS-server.**

### <a name="error-your-deployment-failedunauthorized"></a>Fel: Distributionen misslyckades...\Obehörig

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Orsak:** Prenumerationen du använder är en typ som inte kan komma åt nödvändiga funktioner i den region där kunden försöker distribuera. Msdn-, free- eller education-prenumerationer kan till exempel visa det här felet.

**Rättad:** Ändra din prenumerationstyp eller -region till en som kan komma åt de funktioner som krävs.

### <a name="error-vmextensionprovisioningerror"></a>Fel: VMExtensionProvisioningError

![Det gick inte att skärmbild av distributionen med terminaletableringstillstånd.](media/7aaf15615309c18a984673be73ac969a.png)

**Orsak 1:** Övergående fel med Windows Virtual Desktop-miljö.

**Orsak 2:** Övergående fel med anslutning.

**Rättad:** Bekräfta att Windows Virtual Desktop-miljön är felfri genom att logga in med PowerShell. Slutför vm-registreringen manuellt i [Skapa en värdpool med PowerShell](create-host-pools-powershell.md).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Fel: Det angivna administratörsanvändarnamnet är inte tillåtet

![Det gick inte att skärmbild av distributionen där en angiven administratör inte är tillåten.](media/f2b3d3700e9517463ef88fa41875bac9.png)

Exempel på råfel:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Orsak:** Lösenord som anges innehåller förbjudna delsträngar (admin, administratör, root).

**Rättad:** Uppdatera användarnamn eller använd olika användare.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Fel: VM har rapporterat ett fel vid bearbetning av tillägg

![Det gick inte att skärmbild av resursåtgärden som slutfördes med terminaletableringstillstånd i distributionen misslyckades.](media/49c4a1836a55d91cd65125cf227f411f.png)

Exempel på råfel:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-provision-host-pool-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
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

**Orsak:** PowerShell DSC-tillägget kunde inte få administratörsåtkomst på den virtuella datorn.

**Rättad:** Bekräfta att användarnamn och lösenord har administrativ åtkomst på den virtuella datorn och kör Azure Resource Manager-mallen igen.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Fel: DistributionFailed – PowerShell DSC-konfigurationen 'FirstSessionHost' kompletterad med fel

![Skärmbild av distributionen misslyckas med PowerShell DSC-konfigurationen 'FirstSessionHost' som slutfördes med Fel.Screenshot of deployment fail with PowerShell DSC Configuration 'FirstSessionHost' completed with Error(s).](media/64870370bcbe1286906f34cf0a8646ab.png)

Exempel på råfel:

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

**Orsak:** PowerShell DSC-tillägget kunde inte få administratörsåtkomst på den virtuella datorn.

**Rättad:** Bekräfta att användarnamn och lösenord har administrativ åtkomst på den virtuella datorn och kör Azure Resource Manager-mallen igen.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Fel: DistributionMissilerad – OgiltigResourceReference

Exempel på råfel:

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

**Orsak:** En del av resursgruppsnamnet används för vissa resurser som skapas av mallen. På grund av namnet som matchar befintliga resurser kan mallen välja en befintlig resurs från en annan grupp.

**Rättad:** När du kör Azure Resource Manager-mallen för att distribuera virtuella datorer för sessionsvärdar gör du de två första tecknen unika för ditt namn på prenumerationsresursgruppen.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Fel: DistributionMissilerad – OgiltigResourceReference

Exempel på råfel:

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

**Orsak:** Det här felet beror på att nätverkskortet som skapats med Azure Resource Manager-mallen har samma namn som ett annat nätverkskort som redan finns i VNET.

**Rättad:** Använd ett annat värdprefix.

### <a name="error-deploymentfailed--error-downloading"></a>Fel: DistributionMissilerad – Felhämtning

Exempel på råfel:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-provision-host-pool-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-prod
/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Orsak:** Det här felet beror på en statisk väg, brandväggsregel eller NSG som blockerar hämtningen av zip-filen som är knuten till Azure Resource Manager-mallen.

**Rättad:** Ta bort blockerande statisk väg, brandväggsregel eller NSG. Du kan också öppna Azure Resource Manager-mallen json-filen i en textredigerare, ta länken till zip-filen och hämta resursen till en tillåten plats.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Fel: Användaren har inte behörighet att fråga hanteringstjänsten

Exempel på råfel:

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

**Orsak:** Den angivna klientadministratören för Windows Virtual Desktop har ingen giltig rolltilldelning.

**Rättad:** Användaren som skapade Windows Virtual Desktop-klienten måste logga in på Windows Virtual Desktop PowerShell och tilldela den användare försöksanvändaren en rolltilldelning. Om du kör mallparametrarna för GitHub Azure Resource Manager följer du dessa instruktioner med PowerShell-kommandon:

```PowerShell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName "RDS Contributor" -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>Fel: Användaren kräver MFA (Azure Multi Factor Authentication)

![Skärmbild av distributionen misslyckades på grund av brist på multifaktorautentisering (MFA)](media/MFARequiredError.png)

Exempel på råfel:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Orsak:** Den angivna Windows Virtual Desktop-klientadministratören kräver Att Azure Multi-Factor Authentication (MFA) loggar in.

**Rättad:** Skapa ett tjänsthuvudnamn och tilldela den en roll för din Windows Virtual Desktop-klient genom att följa stegen i [Självstudiekurs: Skapa tjänsthuvudnamn och rolltilldelningar med PowerShell](create-service-principal-role-powershell.md). När du har verifierat att du kan logga in på Windows Virtual Desktop med tjänstens huvudnamn kör du Azure Marketplace-erbjudandet eller GitHub Azure Resource Manager-mallen, beroende på vilken metod du använder. Följ instruktionerna nedan för att ange rätt parametrar för din metod.

Om du kör Azure Marketplace-erbjudandet anger du värden för följande parametrar för att autentisera till Windows Virtual Desktop:

- Windows Virtual Desktop-klient RDS Ägare: Tjänsthuvudnamn
- Program-ID: Programidentifieringen av det nya tjänsthuvudnamnet som du skapade
- Lösenord/Bekräfta lösenord: Lösenordshemligheten du genererade för tjänstens huvudnamn
- Azure AD-klient-ID: Azure AD-klient-ID för tjänsthuvudstaden som du skapade

Om du kör mallen GitHub Azure Resource Manager anger du värden för följande parametrar som ska autentiseras korrekt till Windows Virtual Desktop:

- Klientadministratörsanvändarnamn (UPN) eller Program-ID: Programidentifieringen av det nya tjänsthuvudhuvudet som du skapade
- Klientadministratörslösenord: Lösenordshemligheten som du genererade för tjänstens huvudnamn
- IsServicePrincipal: **sant**
- AadTenantId: Azure AD-klient-ID för tjänsthuvudnamnet som du skapade

## <a name="next-steps"></a>Nästa steg

- En översikt över felsökning av Windows Virtual Desktop och eskaleringsspåren finns i [Felsökningsöversikt, feedback och support](troubleshoot-set-up-overview.md).
- Mer om du vill felsöka problem när du konfigurerar en virtuell dator (VM) i Windows Virtual Desktop finns i [Konfigurationen för virtuell dator för session värd](troubleshoot-vm-configuration.md).
- Mer om du vill felsöka problem med Windows Virtual Desktop-klientanslutningar finns i [Windows Virtual Desktop-tjänstanslutningar](troubleshoot-service-connection.md).
- Om du vill felsöka problem med fjärrskrivbordsklienter finns i [Felsöka klienten för fjärrskrivbord](troubleshoot-client.md)
- Mer om du vill felsöka problem när du använder PowerShell med Virtuellt Windows-skrivbord finns i [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Mer information om tjänsten finns i [Windows Virtual Desktop-miljö](environment-setup.md).
- Information om hur du går igenom en felsökningsguide finns i [Självstudiekurs: Felsöka Resource Manager-malldistributioner](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Mer information om granskningsåtgärder finns i [Granskningsåtgärder med Resurshanteraren](../azure-resource-manager/management/view-activity-logs.md).
- Mer information om åtgärder för att fastställa fel under distributionen finns i [Visa distributionsåtgärder](../azure-resource-manager/templates/deployment-history.md).
