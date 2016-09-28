<properties
    pageTitle="Olika sätt att skapa en virtuell Linux-dator | Microsoft Azure"
    description="Beskriver vilka alternativ du kan välja mellan när du skapar en virtuell Linux-dator i Azure och länkar till verktyg och självstudier för varje metod."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure-services"
    ms.date="07/06/2016"
    ms.author="iainfou"/>


# Olika sätt att skapa en virtuell Linux-dator med Resource Manager

I Azure kan du skapa en virtuell dator på olika sätt med hjälp av Resource Manager-distributionsmodellen, som passar olika användare och syften. I den här artikeln sammanfattar vi skillnaderna och alternativen som du kan välja mellan när du skapar virtuella Linux-datorer.

## Azure CLI 

Azure CLI är tillgänglig på plattformar via ett npm-paket, paket tillhandahållna via distribution eller Docker-behållare. Du kan läsa mer om [hur du installerar och konfigurerar Azure CLI](../xplat-cli-install.md). Följande självstudier innehåller exempel på hur du använder Azure CLI. Läs alla artiklarna för mer information om de CLI-snabbstartkommandon som visas:

* [Skapa en virtuell Linux-dator från Azure CLI för utveckling och testning](virtual-machines-linux-quick-create-cli.md)

    ```bash
    azure vm quick-create -M ~/.ssh/azure_id_rsa.pub -Q CoreOS
    ```

* [Skapa en säker virtuell Linux-dator med hjälp av en Azure-mall](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

    ```bash
    azure group create --name TestRG --location WestUS 
        --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
    ```

* [Skapa en virtuell Linux-dator från grunden med hjälp av Azure CLI](virtual-machines-linux-create-cli-complete.md)

* [Lägg till en disk till en virtuell Linux-dator](virtual-machines-linux-add-disk.md)

    ```bash
    azure vm disk attach-new --resource-group TestRG --vm-name TestVM <size-in-GB>
    ```

## Azure-portalen

Det grafiska användargränssnittet för [Azure Portal](https://portal.azure.com) är ett enkelt sätt att testa en virtuell dator, särskilt om du precis börjat med Azure eftersom det inte finns något att installera på din dator. Använd Azure Portal när du skapar den virtuella datorn:

* [Skapa en virtuell Linux-dator med hjälp av Azure Portal](virtual-machines-linux-quick-create-portal.md) 
* [Anslut en disk med hjälp av Azure Portal](virtual-machines-linux-attach-disk-portal.md)

## Alternativ för operativsystem och avbildning
När du skapar en virtuell dator, väljer du en avbildning baserat på vilket operativsystem du vill köra. Azure och dess samarbetspartner erbjuder många avbildningar, varav några innehåller förinstallerade program och verktyg. Du kan också ladda upp en av dina egna avbildningar (se nedan).

### Azure-avbildningar
Du kan använda `azure vm image` CLI-kommandon för att se vad som finns tillgängligt via utgivare, distributionsutgåva och version.

Lista tillgängliga utgivare:

```bash
azure vm image list-publishers --location WestUS
```

Lista tillgängliga produkter (erbjudanden) för en viss utgivare:

```bash
azure vm image list-offers --location WestUS --publisher Canonical
```

Lista tillgängliga SKU:er (distributionsutgåvor) för ett givet erbjudande:

```bash
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

Lista alla tillgängliga bilder för en viss version:

```bash
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

Se [Analysera och välja avbildningar för virtuell Azure-datorer med Azure CLI](virtual-machines-linux-cli-ps-findimage.md) för fler exempel på hur du hittar och använder tillgängliga avbildningar.

Kommandona `azure vm quick-create` och `azure vm create` har också vissa alias som du kan använda för att snabbt komma åt vanliga distributioner och deras senaste versioner. Det är lättare än att behöva ange utgivare, erbjudande, SKU och version varje gång du skapar en virtuell dator:

| Alias     | Utgivare | Erbjudande        | SKU         | Version |
|:----------|:----------|:-------------|:------------|:--------|
| CentOS    | OpenLogic | Centos       | 7.2         | senaste  |
| CoreOS    | CoreOS    | CoreOS       | Stable      | senaste  |
| Debian    | credativ  | Debian       | 8           | senaste  |
| openSUSE  | SUSE      | openSUSE     | 13.2        | senaste  |
| RHEL      | Redhat    | RHEL         | 7.2         | senaste  |
| SLES      | SLES      | SLES         | 12-SP1      | senaste  |
| UbuntuLTS | Canonical | UbuntuServer | 14.04.4-LTS | senaste  |

### Använda en egen avbildning

Om du behöver specifika anpassningar kan du använda en avbildning baserat på en befintlig virtuell Azure-dator genom att *avbilda* den virtuella datorn eller genom att ladda upp en egen avbildning som lagras på en virtuell hårddisk (VHD). Mer information om distributioner som stöds och hur du använder egna avbildningar finns i följande artiklar:

* [Azure-godkända distributioner](virtual-machines-linux-endorsed-distros.md)

* [Information om icke-godkända distributioner](virtual-machines-linux-create-upload-generic.md)

* [Avbilda en virtuell Linux-dator som en Resource Manager-mall](virtual-machines-linux-capture-image.md). Snabbstartskommandon:

    ```bash
    azure vm deallocate --resource-group TestRG --vm-name TestVM
    azure vm generalize --resource-group TestRG --vm-name TestVM
    azure vm capture --resource-group TestRG --vm-name TestVM --vhd-name-prefix CapturedVM
    ```

## Nästa steg

* Använd någon av följande självstudiekurser för att skapa en virtuell Linux-dator från [portalen](virtual-machines-linux-quick-create-portal.md), med [CLI](virtual-machines-linux-quick-create-cli.md) eller genom att använda en Azure Resource Manager-[mall](virtual-machines-linux-cli-deploy-templates.md).

* När du har skapat en virtuell Linux-dator kan du enkelt [lägga till en datadisk](virtual-machines-linux-add-disk.md).

* Snabba steg för att [återställa ett lösenord eller SSH-nycklar och hantera användare](virtual-machines-linux-using-vmaccess-extension.md)



<!--HONumber=Sep16_HO3-->


