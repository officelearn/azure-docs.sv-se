---
title: "Installera och konfigurera Terraform för att etablera virtuella datorer och annan infrastruktur i Azure | Microsoft Docs"
description: "Lär dig hur du installerar och konfigurerar Terraform för att skapa Azure-resurser"
services: virtual-machines-linux
documentationcenter: virtual-machines
author: echuvyrov
manager: jtalkar
editor: na
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/23/2017
ms.author: echuvyrov
ms.openlocfilehash: 533add8948544e37dc27623a2f51aad1054b1bef
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="install-and-configure-terraform-to-provision-vms-and-other-infrastructure-into-azure"></a>Installera och konfigurera Terraform för att etablera virtuella datorer och annan infrastruktur till Azure
 
Terraform ger ett enkelt sätt att definiera, förhandsgranska och distribuera moln-infrastruktur med hjälp av en [enkel templating språk](https://www.terraform.io/docs/configuration/syntax.html). Den här artikeln beskriver de nödvändiga stegen för att använda Terraform att etablera resurser i Azure. 

> [!TIP]
> Terraform installeras som standard i den [Bash i Azure Cloud Shell upplevelsen](/azure/cloud-shell/quickstart). Förkonfigureras också med autentiseringsuppgifter och [Azure Terraform moduler](https://registry.terraform.io/modules/Azure). Genom att använda molnet Shell kan du hoppa över installationen/delar av det här dokumentet.

## <a name="install-terraform"></a>Installera Terraform

Så här installerar du Terraform, [hämta](https://www.terraform.io/downloads.html) paketet för ditt operativsystem i en separat installationskatalog. Den innehåller en enda körbar fil som bör du också definiera en global sökväg. Anvisningar om hur du anger sökvägen för Linux och Mac finns i [den här webbsidan](https://stackoverflow.com/questions/14637979/how-to-permanently-set-path-on-linux). Anvisningar för hur du anger sökvägen till Windows, gå till [den här webbsidan](https://stackoverflow.com/questions/1618280/where-can-i-set-path-to-make-exe-on-windows). 

Kontrollera sökvägen konfigurationen med det `terraform` kommando. Du bör se en lista över tillgängliga alternativ för Terraform som utdata:

```bash
azureuser@Azure:~$ terraform
Usage: terraform [--version] [--help] <command> [args]
```

## <a name="set-up-terraform-access-to-azure"></a>Ställ in Terraform åtkomst till Azure

Konfigurera [en Azure AD-tjänstens huvudnamn](/cli/azure/create-an-azure-service-principal-azure-cli) att aktivera Terraform etablera resurser i Azure. Tjänstens huvudnamn ger Terraform skript med hjälp av autentiseringsuppgifter för att etablera resurser i din Azure-prenumeration.

Det finns flera sätt att skapa ett program för Azure AD och Azure AD-tjänsten säkerhetsobjekt. Den enklaste och snabbaste vägen idag är att använda Azure CLI 2.0 som [du kan hämta och installera på Windows, Linux eller en Mac](/cli/azure/install-azure-cli).

Logga in att administrera din Azure-prenumeration genom att följande kommando:

   `az login`

Om du har flera Azure-prenumerationer, deras information returneras av den `az login` kommando. Ange den `SUBSCRIPTION_ID` miljövariabeln för värdet för den returnerade `id` från den prenumeration som du vill använda. 

Ange den prenumeration som du vill använda för den här sessionen.

```azurecli-interactive
az account set --subscription="${SUBSCRIPTION_ID}"
```

Fråga konto för att hämta prenumerationen ID och klient-ID-värden.

```azurecli-interactive
az account show --query "{subscriptionId:id, tenantId:tenantId}"
```

Därefter måste du skapa separata autentiseringsuppgifter för Terraform.

```azurecli-interactive
az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${SUBSCRIPTION_ID}"
```

AppId, lösenordet, sp_name och klient returneras. Anteckna appId och lösenord.

Öppna ett nytt gränssnitt för att testa dina autentiseringsuppgifter och kör följande kommando, använder de returnerade värdena för sp_name, lösenord och klientorganisations:

```azurecli-interactive
az login --service-principal -u SP_NAME -p PASSWORD --tenant TENANT
az vm list-sizes --location westus
```

## <a name="configure-terraform-environment-variables"></a>Konfigurera Terraform miljövariabler

Konfigurera Terraform för att använda klient-ID, prenumerations-ID, klient-ID och klientens hemliga från tjänstens huvudnamn när du skapar Azure-resurser. Ange följande miljövariabler som används automatiskt av den [Azure Terraform moduler](https://registry.terraform.io/modules/Azure).

- ARM_SUBSCRIPTION_ID
- ARM_CLIENT_ID
- ARM_CLIENT_SECRET
- ARM_TENANT_ID

Du kan använda det här exempelskriptet shell för att ange de variablerna:

```bash
#!/bin/sh
echo "Setting environment variables for Terraform"
export ARM_SUBSCRIPTION_ID=your_subscription_id
export ARM_CLIENT_ID=your_appId
export ARM_CLIENT_SECRET=your_password
export ARM_TENANT_ID=your_tenant_id
```

## <a name="run-a-sample-script"></a>Kör ett exempelskript

Skapa en fil `test.tf` i en tom katalog och klistra in följande skript. 

```tf
provider "azurerm" {
}
resource "azurerm_resource_group" "rg" {
        name = "testResourceGroup"
        location = "westus"
}
```

Spara filen och kör sedan `terraform init`. Detta kommando hämtar Azure moduler som krävs för att skapa en Azure-resursgrupp. Du ser i följande utdata:

```
* provider.azurerm: version = "~> 0.3"

Terraform has been successfully initialized!
```

Förhandsgranska skriptet med `terraform plan`, och sedan skapa den `testResouceGroup` resursgrupp med `terraform apply`:

```
An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.rg
      id:       <computed>
      location: "westus"
      name:     "testResourceGroup"
      tags.%:   <computed>

azurerm_resource_group.rg: Creating...
  location: "" => "westus"
  name:     "" => "testResourceGroup"
  tags.%:   "" => "<computed>"
azurerm_resource_group.rg: Creation complete after 1s
```

## <a name="next-steps"></a>Nästa steg

Du har installerat Terraform och konfigurerat Azure autentiseringsuppgifter så att du kan starta distribution av infrastrukturen i din Azure-prenumeration. Du kan sedan testat installationen genom att skapa en tom Azure-resursgrupp.

> [!div class="nextstepaction"]
> [Skapa en virtuell dator i Azure med Terraform](terraform-create-complete-vm.md)

