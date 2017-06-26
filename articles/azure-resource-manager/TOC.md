# Översikt
## [Vad är Resource Manager?](resource-group-overview.md)
## [Resursproviders och resurstyper](resource-manager-supported-services.md)
## [Resource Manager och klassisk distribution](resource-manager-deployment-model.md)
## [Prenumerationsåtgärder](resource-manager-subscription-governance.md)
## [Hanterade program](managed-application-overview.md)

# Kom igång
## [Exportera mall](resource-manager-export-template.md)
## [Skapa din första mall](resource-manager-create-first-template.md)
## [Visual Studio med Resurshanteraren](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

# Exempel
## PowerShell
### [Distribuera mall](resource-manager-samples-powershell-deploy.md)

## Azure CLI
### [Distribuera mall](resource-manager-samples-cli-deploy.md)

# Gör så här för att
## Skapa mallar
### [Metodtips för mallar](resource-manager-template-best-practices.md)
### [Mall-avsnitt](resource-group-authoring-templates.md)
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
### PowerShell
#### [Distribuera mall](resource-group-template-deploy.md)
#### [Distribuera privat mall med SAS-token](resource-manager-powershell-sas-token.md)
#### [Exportera mall och distribuera om](resource-manager-export-template-powershell.md)
### Azure CLI
#### [Distribuera mall](resource-group-template-deploy-cli.md)
#### [Distribuera privat mall med SAS-token](resource-manager-cli-sas-token.md)
#### [Exportera mall och distribuera om](resource-manager-export-template-cli.md)
### [Portal](resource-group-template-deploy-portal.md)
### [REST-API](resource-group-template-deploy-rest.md)
### [Distribution mellan resursgrupper](resource-manager-cross-resource-group-deployment.md)
### [Kontinuerlig integrering med Visual Studio Team Services](../vs-azure-tools-resource-groups-ci-in-vsts.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [Skicka säkra värden under distributionen](resource-manager-keyvault-parameter.md)

## Hantera
### [PowerShell](powershell-azure-resource-manager.md)
### [Azure CLI](xplat-cli-azure-resource-manager.md)
### [Portal](resource-group-portal.md)
### [REST-API](resource-manager-rest-api.md)
### [Använd taggar för att organisera resurser](resource-group-using-tags.md)
### [Flytta resurser till ny grupp eller prenumeration](resource-group-move-resources.md)
### [Styrningsexempel](resource-manager-subscription-examples.md)

## Styr åtkomsten
### Skapa tjänstens huvudnamn
#### [PowerShell](resource-group-authenticate-service-principal.md)
#### [Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Azure CLI 1.0](resource-group-authenticate-service-principal-cli.md)
#### [Portal](resource-group-create-service-principal-portal.md)
### [Autentiserings-API för att få åtkomst till prenumerationer](resource-manager-api-authentication.md)
### [Lås resurser](resource-group-lock-resources.md)

## Ange resursprinciper
### [Vad är resursprinciper?](resource-manager-policy.md)
### [Tilldela en princip med hjälp av portalen](resource-manager-policy-portal.md)
### [Tilldela en princip med hjälp av skript](resource-manager-policy-create-assign.md)
### Exempel
#### [Resurstaggar](resource-manager-policy-tags.md)
#### [Storage](resource-manager-policy-storage.md)
#### [Virtuell Linux-dator](../virtual-machines/linux/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Virtuell Windows-dator](../virtual-machines/windows/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)

## Använda hanterade program
### [Publicera hanterat program](managed-application-publishing.md)
### [Använda hanterat program](managed-application-consumption.md)
### [Skapa definitioner för användargränssnitt](managed-application-createuidefinition-overview.md)

## Granska
### [Visa aktivitetsloggar](resource-group-audit.md)
### [Visa distributionsåtgärder](resource-manager-deployment-operations.md)

## Felsöka
### [Vanliga distributionsfel](resource-manager-common-deployment-errors.md)

# Referens
## [Mallformat](/azure/templates/)
## [Mallfunktioner](resource-group-template-functions.md)
### [Funktioner för matris och objekt](resource-group-template-functions-array.md)
### [Jämförelsefunktioner](resource-group-template-functions-comparison.md)
### [Distributionsfunktioner](resource-group-template-functions-deployment.md)
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
## [Begränsningsbegäranden](resource-manager-request-limits.md)
## [Spåra asynkrona åtgärder](resource-manager-async-operations.md)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-resource-manager)
## [Videoklipp](https://azure.microsoft.com/documentation/videos/index/?services=azure-resource-manager)
## [Tjänstuppdateringar](https://azure.microsoft.com/updates/?product=azure-resource-manager)
