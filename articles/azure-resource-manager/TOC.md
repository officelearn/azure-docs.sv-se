# [Dokumentation om Azure Resource Manager](index.md)

# Översikt
## [Vad är Resource Manager?](resource-group-overview.md)
## [Resource Manager och klassisk distribution](resource-manager-deployment-model.md)

# Kom igång
## [Skapa och distribuera mall](resource-manager-create-first-template.md)
## [VS Code-tillägg för mallar](resource-manager-vscode-extension.md)
## [Visual Studio med Resurshanteraren](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

# Begrepp
## [Resursproviders och resurstyper](resource-manager-supported-services.md)
## [Hanteringsgrupper](management-groups-overview.md)
## [Prenumerationsåtgärder](/azure/architecture/cloud-adoption-guide/subscription-governance?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
## [Mallar för molnkonsekvens](templates-cloud-consistency.md)

# Gör så här för att
## Skapa mallar
### [Mall-avsnitt](resource-group-authoring-templates.md)
#### [Parametrar](resource-manager-templates-parameters.md)
#### [Variabler](resource-manager-templates-variables.md)
#### [Funktioner](resource-group-authoring-templates.md#functions)
#### [Resurser](resource-manager-templates-resources.md)
#### [Utdata](resource-manager-templates-outputs.md)
### [Länkade och kapslade mallar](resource-group-linked-templates.md)
### [Definiera beroendet mellan resurser](resource-group-define-dependencies.md)
### [Skapa flera instanser](resource-group-create-multiple.md)
### [Uppdatera resurs](/azure/architecture/building-blocks/extending-templates/update-resource)

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
### [Flera resursgrupper eller prenumerationer](resource-manager-cross-resource-group-deployment.md)
### [Kontinuerlig integrering med Visual Studio Team Services](../vs-azure-tools-resource-groups-ci-in-vsts.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [Skicka säkra värden under distributionen](resource-manager-keyvault-parameter.md)

## Hantera
### [Azure PowerShell](powershell-azure-resource-manager.md)
### [Azure CLI](xplat-cli-azure-resource-manager.md)
### [Azure Portal](resource-group-portal.md)
### [Använd taggar för att organisera resurser](resource-group-using-tags.md)
### [Flytta resurser till ny grupp eller prenumeration](resource-group-move-resources.md)
### [Skapa EA-prenumerationer](programmatically-create-subscription.md)
### [Bevilja åtkomst för att skapa EA-prenumerationer](grant-access-to-create-subscription.md)
### [Skapa hanteringsgrupper](management-groups-create.md)
### [Hantera dina hanteringsgrupper](management-groups-manage.md)

## Styr åtkomsten
### Skapa tjänstens huvudnamn
#### [Azure PowerShell](resource-group-authenticate-service-principal.md)
#### [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Azure Portal](resource-group-create-service-principal-portal.md)
### [Autentiserings-API för att få åtkomst till prenumerationer](resource-manager-api-authentication.md)
### [Lås resurser](resource-group-lock-resources.md)

## Granska
### [Visa aktivitetsloggar](resource-group-audit.md)
### [Visa distributionsåtgärder](resource-manager-deployment-operations.md)

## Felsöka
### [Felsöka vanliga distributionsfel](resource-manager-common-deployment-errors.md)
### [AccountNameInvalid](resource-manager-storage-account-name-errors.md)
### [InvalidTemplate](resource-manager-invalid-template-errors.md)
### [Linux-distributionsproblem](../virtual-machines/linux/troubleshoot-deploy-vm.md)
### [NoRegisteredProviderFound](resource-manager-register-provider-errors.md)
### [NotFound](resource-manager-not-found-errors.md)
### [ParentResourceNotFound](resource-manager-parent-resource-errors.md)
### [Problem med etablering och allokering för Linux](../virtual-machines/linux/troubleshoot-deployment-new-vm.md)
### [Problem med etablering och allokering för Windows](../virtual-machines/windows/troubleshoot-deployment-new-vm.md)
### [RequestDisallowedByPolicy](resource-manager-policy-requestdisallowedbypolicy-error.md)
### [ReservedResourceName](resource-manager-reserved-resource-name.md)
### [ResourceQuotaExceeded](resource-manager-quota-errors.md)
### [SkuNotAvailable](resource-manager-sku-not-available-errors.md)
### [Windows-distributionsproblem](../virtual-machines/windows/troubleshoot-deploy-vm.md)

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
## [PowerShell](/powershell/module/azurerm.resources)
## [Azure CLI](/cli/azure/resource)
## [.NET](/dotnet/api/microsoft.azure.management.resourcemanager)
## [Java](/java/api/com.microsoft.azure.management.resources)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagement.html)
## [REST](/rest/api/resources/)

# Resurser
## [Azure-översikt](https://azure.microsoft.com/roadmap/?category=monitoring-management)
## [Priskalkylator](https://azure.microsoft.com/pricing/calculator/)
## [Tjänstuppdateringar](https://azure.microsoft.com/updates/?product=azure-resource-manager)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-resource-manager)
## [Hantera personliga data](resource-manager-personal-data.md)
## [Begränsningsbegäranden](resource-manager-request-limits.md)
## [Spåra asynkrona åtgärder](resource-manager-async-operations.md)
## [Videoklipp](https://azure.microsoft.com/documentation/videos/index/?services=azure-resource-manager)
