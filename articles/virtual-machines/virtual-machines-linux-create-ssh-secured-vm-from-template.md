<properties
    pageTitle="Skapa en virtuell Linux-dator med hjälp av en Azure-mall | Microsoft Azure"
    description="Skapa en virtuell Linux-dator i Azure med hjälp av en Azure Resource Manager-mall."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/17/2016"
    ms.author="v-livech"/>


# Skapa en virtuell Linux-dator med hjälp av en Azure-mall

I den här artikeln lär du dig hur du snabbt distribuerar en virtuell Linux-dator i Azure med hjälp av en Azure-mall.  Artikeln kräver ett Azure-konto ([skaffa en kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/)) och att [Azure CLI](../xplat-cli-install.md) är inloggad (`azure login`) och i Resource Manager-läge (`azure config mode arm`).  Du kan också snabbt distribuera en virtuell Linux-dator med hjälp av [Azure Portal](virtual-machines-linux-quick-create-portal.md) eller [Azure CLI](virtual-machines-linux-quick-create-cli.md).

## Snabb kommandosammanfattning

```bash
azure group create \
-n quicksecuretemplate \
-l eastus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

## Detaljerad genomgång

Med mallar kan du skapa virtuella datorer i Azure med inställningar som du vill anpassa under starten, till exempel användarnamn och värdnamn. För den här artikeln startar vi en Azure-mall med hjälp av en virtuell Ubuntu-dator tillsammans med en nätverkssäkerhetsgrupp med port 22 öppen för SSH.

Azure Resource Manager-mallar är JSON-filer som kan användas för enkla engångsuppgifter som generering av en virtuell Ubuntu-dator, så som det görs i den här artikeln.  Azure-mallar kan också användas för att skapa komplexa Azure-konfigurationer av hela miljöer som en distributionsstack för testning, utveckling eller produktion.

## Skapa den virtuella Linux-datorn

Följande kodexempel illustrerar hur du anropar `azure group create` för att skapa en resursgrupp och distribuera en SSH-skyddad virtuell Linux-dator på samma gång med hjälp av [den här Azure Resource Manager-mallen](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Tänk på att i ditt exempel måste du använda namn som är unika för din miljö. I det här exemplet används `quicksecuretemplate` som resursgruppens namn, `securelinux` som namn på den virtuella datorn och `quicksecurelinux` som ett underdomännamn.

```bash
azure group create \
-n quicksecuretemplate \
-l eastus \
--template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
```

Resultat

```bash
info:    Executing command group create
+ Getting resource group quicksecuretemplate
+ Creating resource group quicksecuretemplate
info:    Created resource group quicksecuretemplate
info:    Supply values for the following parameters
sshKeyData: ssh-rsa AAAAB3Nza<..ssh public key text..>VQgwjNjQ== vlivech@azure
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<..subid text..>/resourceGroups/quicksecuretemplate
data:    Name:                quicksecuretemplate
data:    Location:            eastus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

I det exemplet distribueras en virtuell dator med parametern `--template-uri`.  Du kan också ladda ned eller skapa en mall lokalt och överföra mallen med hjälp av parametern `--template-file` med en sökväg till mallfilen som argument. Azure CLI uppmanar dig att ange de parametrar som krävs av mallen.

## Nästa steg

Sök i [mallgalleriet](https://azure.microsoft.com/documentation/templates/) för att se vilka appramverk som du ska distribuera härnäst.



<!--HONumber=Sep16_HO3-->


