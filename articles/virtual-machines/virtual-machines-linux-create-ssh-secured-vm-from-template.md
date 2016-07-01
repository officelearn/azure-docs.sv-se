<properties
    pageTitle="Skapa en säker virtuell Linux-dator med hjälp av en Azure-mall | Microsoft Azure"
    description="Skapa en säker virtuell Linux-dator i Azure med hjälp av en Azure Resource Manager-mall."
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
    ms.date="04/27/2016"
    ms.author="v-livech"/>

# Skapa en säker virtuell Linux-dator med hjälp av en Azure-mall

För att skapa en virtuell Linux-dator från en mall krävs [Azure CLI](../xplat-cli-install.md) i resurshanterarläge (`azure config mode arm`).

## Snabb kommandosammanfattning

```bash
chrisl@fedora$ azure group create -n <exampleRGname> -l <exampleAzureRegion> [--template-uri <URL> | --template-file <path> | <template-file> -e <parameters.json file>]
```

## Detaljerad genomgång

Med mallar kan du skapa virtuella datorer i Azure med inställningar som du vill anpassa under starten, till exempel användarnamn och värdnamn. I den här artikeln ska vi fokusera på hur du kommer igång med en virtuell Ubuntu-dator med hjälp av en Azure-mall som skapar en nätverkssäkerhetsgrupp (NSG) med endast en öppen port (22 för SSH) och som kräver SSH-nycklar för inloggning.

Azure Resource Manager-mallarna är JSON-filer som kan användas för enkla engångsuppgifter – som genereringen av en virtuell Ubuntu-dator i den här artikeln – eller utvecklingen av komplexa Azure-konfigurationer i hela miljöer som en testnings-, utvecklings- eller produktionsdistribution med distribution till nätverk, operativsystem och programstack.

## Skapa den virtuella Linux-datorn

Följande kodexempel illustrerar hur du anropar `azure group create` för att skapa en resursgrupp och distribuera en SSH-skyddad virtuell Linux-dator på samma gång med hjälp av [den här Azure Resource Manager-mallen](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json). Tänk på att i ditt exempel måste du använda namn som är unika för din miljö. I det här exemplet används `quicksecuretemplate` som resursgruppens namn, `securelinux` som namn på den virtuella datorn och `quicksecurelinux` som ett underdomännamn.

```bash
chrisl@fedora$ azure group create -n quicksecuretemplate -l eastus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
info:    Executing command group create
+ Getting resource group quicksecuretemplate
+ Creating resource group quicksecuretemplate
info:    Created resource group quicksecuretemplate
info:    Supply values for the following parameters
adminUserName: ops
sshKeyData: ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDDRZ/XB8p8uXMqgI8EoN3dWQw... user@contoso.com
dnsLabelPrefix: quicksecurelinux
vmName: securelinux
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment "azuredeploy"
data:    Id:                  /subscriptions/<guid>/resourceGroups/quicksecuretemplate
data:    Name:                quicksecuretemplate
data:    Location:            eastus
data:    Provisioning State:  Succeeded
data:    Tags: null
data:
info:    group create command OK
```

Du kan skapa en ny resursgrupp och distribuera en virtuell dator med hjälp av parametern `--template-uri` eller så kan du ladda ned eller skapa en mall lokalt och överföra mallen med hjälp av parametern `--template-file` med en sökväg till mallfilen som argument. Azure CLI uppmanar dig att ange de parametrar som krävs av mallen.

## Nästa steg

När du skapar virtuella Linux-datorer med mallar vill du antagligen se vilka andra appramverk som är tillgängliga för användning med mallar. Sök i [mallgalleriet](https://azure.microsoft.com/documentation/templates/) för att se vilka appramverk som du ska distribuera härnäst.



<!--HONumber=Jun16_HO2-->


