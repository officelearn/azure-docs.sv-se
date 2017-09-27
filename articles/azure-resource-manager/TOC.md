# Översikt
## [Vad är Resource Manager?](resource-group-overview.md)
## [Resursproviders och resurstyper](resource-manager-supported-services.md)
## [Resource Manager och klassisk distribution](resource-manager-deployment-model.md)
## [Prenumerationsåtgärder](resource-manager-subscription-governance.md)
## [Hanterade program](managed-application-overview.md)

# Kom igång
## [Skapa och distribuera mall](resource-manager-create-first-template.md)
## [VS Code-tillägg för mallar](resource-manager-vscode-extension.md)
## [Visual Studio med Resurshanteraren](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

# Exempel
## [Kodexempel](https://azure.microsoft.com/en-us/resources/samples/?service=azure-resource-manager)
## Azure PowerShell
### [Distribuera mall](resource-manager-samples-powershell-deploy.md)

## Azure CLI
### [Distribuera mall](resource-manager-samples-cli-deploy.md)

# Gör så här för att
## Skapa mallar
### [Mall-avsnitt](resource-group-authoring-templates.md)
### [Metodtips för mallar](resource-manager-template-best-practices.md)
### [Länk till andra mallar](resource-group-linked-templates.md)
### [Definiera beroendet mellan resurser](resource-group-define-dependencies.md)
### [Skapa flera instanser](resource-group-create-multiple.md)
### [Ange en plats](resource-manager-template-location.md)
### [Tilldela taggar](resource-manager-template-tags.md)
### [Ange underordnat resursnamn och typ](resource-manager-template-child-resource.md)
### [Uppdatera resurs](resource-manager-update.md)
### [Använda objekt för parametrar](resource-manager-objects-as-parameters.md)
### [Dela tillstånd mellan länkade mallar](best-practices-resource-manager-state.md)
### [Mönster för att utforma mallar](best-practices-resource-manager-design-templates.md)


## Distribuera
### Azure PowerShell
#### [Distribuera mall](resource-group-template-deploy.md)
#### [Distribuera privat mall med SAS-token](resource-manager-powershell-sas-token.md)
#### [Exportera mall och distribuera om](resource-manager-export-template-powershell.md)
### Azure CLI
#### [Distribuera mall](resource-group-template-deploy-cli.md)
#### [Distribuera privat mall med SAS-token](resource-manager-cli-sas-token.md)
#### [Exportera mall och distribuera om](resource-manager-export-template-cli.md)
### Azure Portal
#### [Distribuera resurser](resource-group-template-deploy-portal.md)
#### [Exportera mall](resource-manager-export-template.md)
### [REST API](resource-group-template-deploy-rest.md)
### [Distribution mellan resursgrupper](resource-manager-cross-resource-group-deployment.md)
### [Kontinuerlig integrering med Visual Studio Team Services](../vs-azure-tools-resource-groups-ci-in-vsts.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [Skicka säkra värden under distributionen](resource-manager-keyvault-parameter.md)

## Hantera
### [Azure PowerShell](powershell-azure-resource-manager.md)
### [Azure CLI](xplat-cli-azure-resource-manager.md)
### [Azure Portal](resource-group-portal.md)
### [REST API](resource-manager-rest-api.md)
### [Använd taggar för att organisera resurser](resource-group-using-tags.md)
### [Flytta resurser till ny grupp eller prenumeration](resource-group-move-resources.md)
### [Styrningsexempel](resource-manager-subscription-examples.md)

## Styr åtkomsten
### Skapa tjänstens huvudnamn
#### [Azure PowerShell](resource-group-authenticate-service-principal.md)
#### [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Azure CLI 1.0](resource-group-authenticate-service-principal-cli.md)
#### [Azure Portal](resource-group-create-service-principal-portal.md)
### [Autentiserings-API för att få åtkomst till prenumerationer](resource-manager-api-authentication.md)
### [Lås resurser](resource-group-lock-resources.md)

## Ange resursprinciper
### [Vad är resursprinciper?](resource-manager-policy.md)
### [Tilldela en princip med hjälp av Azure Portal](resource-manager-policy-portal.md)
### [Tilldela en princip med hjälp av skript](resource-manager-policy-create-assign.md)
### Exempel
#### [Taggar](resource-manager-policy-tags.md)
#### [Namngivningskonventioner](resource-manager-policy-naming-convention.md)
#### [Nätverk](resource-manager-policy-network.md)
#### [Storage](resource-manager-policy-storage.md)
#### [Virtuell Linux-dator](../virtual-machines/linux/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Virtuell Windows-dator](../virtual-machines/windows/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)

## Använda hanterade program
### [Publicera tjänstkatalogprogram](managed-application-publishing.md)
### [Använd tjänstkatalogprogram](managed-application-consumption.md)
### [Publicera marknadsplatsprogram](managed-application-author-marketplace.md)
### [Använd marknadsplatsprogram](managed-application-consume-marketplace.md)
### [Skapa definitioner för användargränssnitt](managed-application-createuidefinition-overview.md)

## Granska
### [Visa aktivitetsloggar](resource-group-audit.md)
### [Visa distributionsåtgärder](resource-manager-deployment-operations.md)

## Felsöka
### [Vanliga distributionsfel](resource-manager-common-deployment-errors.md)
### [Förstå vanliga distributionsfel](resource-manager-troubleshoot-tips.md)
### Åtgärda fel
#### [AccountNameInvalid](resource-manager-storage-account-name-errors.md)
#### [InvalidTemplate](resource-manager-invalid-template-errors.md)
#### [NoRegisteredProviderFound](resource-manager-register-provider-errors.md)
#### [NotFound](resource-manager-not-found-errors.md)
#### [ParentResourceNotFound](resource-manager-parent-resource-errors.md)
#### [RequestDisallowedByPolicy](resource-manager-policy-requestdisallowedbypolicy-error.md)
#### [ResourceQuotaExceeded](resource-manager-quota-errors.md)
#### [SkuNotAvailable](resource-manager-sku-not-available-errors.md)
### Distributionsfel för virtuell dator
#### Linux
##### [Distributionsproblem](../virtual-machines/linux/troubleshoot-deploy-vm.md)
##### [Problem med etablering och tilldelning](../virtual-machines/linux/troubleshoot-deployment-new-vm.md)
##### [Vanliga felmeddelanden](../virtual-machines/linux/error-messages.md)
#### Windows
##### [Distributionsproblem](../virtual-machines/windows/troubleshoot-deploy-vm.md)
##### [Problem med etablering och tilldelning](../virtual-machines/windows/troubleshoot-deployment-new-vm.md)
##### [Vanliga felmeddelanden](../virtual-machines/windows/error-messages.md)

# Referens
## [Mallformat](/azure/templates/)
## [Mallfunktioner](resource-group-template-functions.md)
### [Funktioner för matris och objekt](resource-group-template-functions-array.md)
### [Jämförelsefunktioner](resource-group-template-functions-comparison.md)
### [Distributionsfunktioner](resource-group-template-functions-deployment.md)
### [Logiska funktioner](resource-group-template-functions-logical.md)
### [Numeriska funktioner](resource-group-template-functions-numeric.md)
### [Resursfunktioner](resource-group-template-functions-resource.md)
### [Strängfunktioner](resource-group-template-functions-string.md)
## [Funktioner för användargränssnittsdefinition](managed-application-createuidefinition-functions.md)
## [Definitionselement för användargränssnitt](managed-application-createuidefinition-elements.md)
### [Microsoft.Common.DropDown](managed-application-microsoft-common-dropdown.md)
### [Microsoft.Common.FileUpload](managed-application-microsoft-common-fileupload.md)
### [Microsoft.Common.OptionsGroup](managed-application-microsoft-common-optionsgroup.md)
### [Microsoft.Common.PasswordBox](managed-application-microsoft-common-passwordbox.md)
### [Microsoft.Common.Section](managed-application-microsoft-common-section.md)
### [Microsoft.Common.TextBox](managed-application-microsoft-common-textbox.md)
### [Microsoft.Compute.CredentialsCombo](managed-application-microsoft-compute-credentialscombo.md)
### [Microsoft.Compute.SizeSelector](managed-application-microsoft-compute-sizeselector.md)
### [Microsoft.Compute.UserNameTextBox](managed-application-microsoft-compute-usernametextbox.md)
### [Microsoft.Network.PublicIpAddressCombo](managed-application-microsoft-network-publicipaddresscombo.md)
### [Microsoft.Network.VirtualNetworkCombo](managed-application-microsoft-network-virtualnetworkcombo.md)
### [Microsoft.Storage.MultiStorageAccountCombo](managed-application-microsoft-storage-multistorageaccountcombo.md)
### [Microsoft.Storage.StorageAccountSelector](managed-application-microsoft-storage-storageaccountselector.md)
## [PowerShell](/powershell/module/azurerm.resources)
## [Azure CLI](/cli/azure/resource)
## [NET](/dotnet/api/microsoft.azure.management.resourcemanager)
## [Java](/java/api/com.microsoft.azure.management.resources)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagement.html)
## [REST](/rest/api/resources/)

# Resurser
## [Azure-översikt](https://azure.microsoft.com/roadmap/?category=monitoring-management)
## [Priskalkylator](https://azure.microsoft.com/pricing/calculator/)
## [Tjänstuppdateringar](https://azure.microsoft.com/updates/?product=azure-resource-manager)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-resource-manager)
## [Begränsningsbegäranden](resource-manager-request-limits.md)
## [Spåra asynkrona åtgärder](resource-manager-async-operations.md)
## [Videoklipp](https://azure.microsoft.com/documentation/videos/index/?services=azure-resource-manager)
