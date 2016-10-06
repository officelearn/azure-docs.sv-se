<properties
    pageTitle="Olika sätt att skapa en virtuell Linux-dator | Microsoft Azure"
    description="Lär dig mer om vilka alternativ som du kan välja mellan när du skapar en virtuell Linux-dator i Azure, inklusive länkar till verktyg och självstudier för varje metod."
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
    ms.date="09/27/2016"
    ms.author="iainfou"/>


# Olika sätt att skapa en virtuell Linux-dator i Azure

I Azure har du tillräcklig flexibilitet för att kunna skapa en virtuell Linux-dator (VM) med hjälp av de verktyg och arbetsflöden som du känner dig mest bekväm med. I den här artikeln sammanfattas skillnader och exempel när du ska skapa dina virtuella Linux-datorer.


## Azure CLI 

Azure CLI är tillgänglig på plattformar via ett npm-paket, paket tillhandahållna via distribution eller Docker-behållare. Du kan läsa mer om [hur du installerar och konfigurerar Azure CLI](../xplat-cli-install.md). Följande självstudier innehåller exempel på hur du använder Azure CLI. Läs alla artiklarna för mer information om de CLI-snabbstartkommandon som visas:

- [Skapa en virtuell Linux-dator från Azure CLI för utveckling och testning](virtual-machines-linux-quick-create-cli.md)
    - I följande exempel skapas en virtuell CoreOS-dator med hjälp av en offentlig nyckel med namnet `azure_id_rsa.pub`:

    ```bash
    azure vm quick-create -ssh-publickey-file ~/.ssh/azure_id_rsa.pub \
        --image-urn CoreOS
    ```

- [Skapa en säker virtuell Linux-dator med hjälp av en Azure-mall](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
    - I följande exempel skapas en virtuell dator med hjälp av en mall som lagras på GitHub:

    ```bash
    azure group create --name TestRG --location WestUS 
        --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json
    ```

- [Skapa en fullständig Linux-miljö med Azure CLI](virtual-machines-linux-create-cli-complete.md)
    - Innefattar att skapa en belastningsutjämnare och flera virtuella datorer i en tillgänglighetsuppsättning.

- [Lägg till en disk till en virtuell Linux-dator](virtual-machines-linux-add-disk.md)
    - I följande exempel läggs en disk på 5 GB till på en befintlig virtuell dator med namnet `TestVM`:

    ```bash
    azure vm disk attach-new --resource-group TestRG --vm-name TestVM \
        --size-in-GB 5
    ```

## Azure Portal

I [Azure-portalen](https://portal.azure.com) kan du snabbt skapa en virtuell dator eftersom det inte finns något att installera i ditt system. Använd Azure Portal när du skapar den virtuella datorn:

- [Skapa en virtuell Linux-dator med hjälp av Azure Portal](virtual-machines-linux-quick-create-portal.md) 
- [Anslut en disk med hjälp av Azure Portal](virtual-machines-linux-attach-disk-portal.md)


## Alternativ för operativsystem och avbildning
När du skapar en virtuell dator, väljer du en avbildning baserat på vilket operativsystem du vill köra. Azure och dess samarbetspartner erbjuder många avbildningar, varav några innehåller förinstallerade program och verktyg. Eller ladda upp en av dina egna avbildningar (se [följande avsnitt](#use-your-own-image)).

### Azure-avbildningar
Använd `azure vm image` CLI-kommandon för att se vad som finns tillgängligt via utgivare, distributionsutgåva och version.

Lista tillgängliga utgivare enligt följande:

```bash
azure vm image list-publishers --location WestUS
```

Lista tillgängliga produkter (erbjudanden) för en viss utgivare enligt följande:

```bash
azure vm image list-offers --location WestUS --publisher Canonical
```

Lista tillgängliga SKU:er (distributionsutgåvor) för ett givet erbjudande enligt följande:

```bash
azure vm image list-skus --location WestUS --publisher Canonical --offer UbuntuServer
```

Lista alla tillgängliga avbildningar för en viss version enligt följande:

```bash
azure vm image list --location WestUS --publisher Canonical --offer UbuntuServer --sku 16.04.0-LTS
```

Se [Analysera och välja avbildningar för virtuell Azure-datorer med Azure CLI](virtual-machines-linux-cli-ps-findimage.md) för fler exempel på hur du hittar och använder tillgängliga avbildningar.

Kommandona `azure vm quick-create` och `azure vm create` har alias som du kan använda för att snabbt komma åt vanliga distributioner och deras senaste versioner. Det går snabbare att använda alias än ange utgivare, erbjudande, SKU och version varje gång du skapar en virtuell dator:

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

Om du behöver specifika anpassningar kan du använda en avbildning baserad på en befintlig virtuell Azure-dator genom att *avbilda* den virtuella datorn. Du kan också ladda upp en avbildning som skapats lokalt. Mer information om distributioner som stöds och hur du använder egna avbildningar finns i följande artiklar:

- [Azure-godkända distributioner](virtual-machines-linux-endorsed-distros.md)

- [Information om icke-godkända distributioner](virtual-machines-linux-create-upload-generic.md)

- [Avbilda en virtuell Linux-dator som en Resource Manager-mall](virtual-machines-linux-capture-image.md).
    - Snabbstart med exempelkommandon för att avbilda en befintlig virtuell dator:

    ```bash
    azure vm deallocate --resource-group TestRG --vm-name TestVM
    azure vm generalize --resource-group TestRG --vm-name TestVM
    azure vm capture --resource-group TestRG --vm-name TestVM --vhd-name-prefix CapturedVM
    ```

## Nästa steg

- Skapa en virtuell Linux-dator från [portalen](virtual-machines-linux-quick-create-portal.md), med [CLI](virtual-machines-linux-quick-create-cli.md) eller genom att använda en [Azure Resource Manager-mall](virtual-machines-linux-cli-deploy-templates.md).

- När du har skapat en virtuell Linux-dator kan du [lägga till en datadisk](virtual-machines-linux-add-disk.md).

- Snabba steg för att [återställa ett lösenord eller SSH-nycklar och hantera användare](virtual-machines-linux-using-vmaccess-extension.md)



<!--HONumber=Sep16_HO4-->


