---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 713068b940532331ee4a6c5f5a92816d328e41ec
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2019
ms.locfileid: "55736021"
---
Innan du kan använda Azure CLI med Resource Manager-kommandon och mallar för att distribuera Azure-resurser och arbetsbelastningar med resursgrupper, måste du ha ett konto hos Azure. Om du inte har något konto kan du skaffa ett [kostnadsfritt Azure-utvärderingskonto här](https://azure.microsoft.com/pricing/free-trial/).

Om du inte redan har installerat Azure CLI och anslutit till din prenumeration kan du läsa [Installera Azure CLI](../articles/cli-install-nodejs.md), ange läget till `arm` med `azure config mode arm` och anslut till Azure med kommandot `azure login`.

## <a name="cli-versions-to-complete-the-task"></a>CLI-versioner för att slutföra uppgiften
Du kan slutföra uppgiften med någon av följande CLI-versioner:

- Azure klassiskt CLI – vår CLI för de klassiska distributionsmodellen och resource Manager-distributionsmodellerna (den här artikeln)
- [Azure CLI](../articles/virtual-machines/linux/cli-manage.md) – vår nästa generations CLI för distributionsmodellen i resource management

## <a name="basic-azure-resource-manager-commands-in-azure-classic-cli"></a>Grundläggande Azure Resource Manager-kommandon i Azure klassiska CLI

Den här artikeln beskriver grundläggande kommandon som du vill använda med Azure klassiskt CLI för att hantera och interagera med dina resurser (främst virtuella datorer) i din Azure-prenumeration.  Om du behöver mer ingående hjälp med särskilda kommandoradsväxlingar och alternativ kan du använda hjälp och alternativ för onlinekommandon genom att skriva `azure <command> <subcommand> --help` eller `azure help <command> <subcommand>`.

> [!NOTE]
> De här exemplen omfattar inte mallbaserade åtgärder som brukar rekommenderas för distribueringar av virtuella datorer i Resource Manager. Mer information finns i [Use the Azure CLI with Azure Resource Manager](../articles/xplat-cli-azure-resource-manager.md) (Använda Azure CLI med Azure Resource Manager) och [Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI](../articles/virtual-machines/linux/create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (Distribuera och hantera virtuella datorer med Azure Resource Manager-mallar och Azure CLI).
> 
> 

| Aktivitet | Resource Manager |
| --- | --- | --- |
| Skapa en grundläggande virtuell dator |`azure vm quick-create [options] <resource-group> <name> <location> <os-type> <image-urn> <admin-username> <admin-password>`<br/><br/>(Hämta `image-urn` från kommandot `azure vm image list`. Fler exempel finns i [den här artikeln ](../articles/virtual-machines/linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .) |
| Skapa en virtuell Linux-dator |`azure  vm create [options] <resource-group> <name> <location> -y "Linux"` |
| Skapa en virtuell Windows-dator |`azure  vm create [options] <resource-group> <name> <location> -y "Windows"` |
| Lista över virtuella datorer |`azure  vm list [options]` |
| Hämta information om en virtuell dator |`azure  vm show [options] <resource_group> <name>` |
| Starta en virtuell dator |`azure vm start [options] <resource_group> <name>` |
| Stoppa en virtuell dator |`azure vm stop [options] <resource_group> <name>` |
| Frigöra en virtuell dator |`azure vm deallocate [options] <resource-group> <name>` |
| Starta om en virtuell dator |`azure vm restart [options] <resource_group> <name>` |
| Ta bort en virtuell dator |`azure vm delete [options] <resource_group> <name>` |
| Avbilda en virtuell dator |`azure vm capture [options] <resource_group> <name>` |
| Skapa en virtuell dator av en användaravbildning |`azure  vm create [options] –q <image-name> <resource-group> <name> <location> <os-type>` |
| Skapa en virtuell dator från en särskild disk |`azure  vm create [options] –d <os-disk-vhd> <resource-group> <name> <location> <os-type>` |
| Lägg till en datadisk i en virtuell dator |`azure  vm disk attach-new [options] <resource-group> <vm-name> <size-in-gb> [vhd-name]` |
| Ta bort en datadisk från en virtuell dator |`azure  vm disk detach [options] <resource-group> <vm-name> <lun>` |
| Lägg till ett allmänt tillägg i en virtuell dator |`azure  vm extension set [options] <resource-group> <vm-name> <name> <publisher-name> <version>` |
| Lägg till ett åtkomsttillägg i en virtuell dator |`azure vm reset-access [options] <resource-group> <name>` |
| Lägg till ett Docker-tillägg i en virtuell dator |`azure  vm docker create [options] <resource-group> <name> <location> <os-type>` |
| Ta bort ett virtuellt datortillägg |`azure  vm extension set [options] –u <resource-group> <vm-name> <name> <publisher-name> <version>` |
| Få användning av virtuella datorresurser |`azure vm list-usage [options] <location>` |
| Hämta alla tillgängliga storlekar för virtuella datorer |`azure vm sizes [options]` |

## <a name="next-steps"></a>Nästa steg
* Fler exempel på CLI-kommandon bortom grundläggande hantering av virtuella datorer finns i [Using the Azure CLI with Azure Resource Manager](../articles/virtual-machines/azure-cli-arm-commands.md) (Använda Azure CLI med Azure Resource Manager).
