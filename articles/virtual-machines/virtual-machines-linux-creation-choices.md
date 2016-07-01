<properties
    pageTitle="Olika sätt att skapa en virtuell Linux-dator | Microsoft Azure"
    description="Beskriver vilka alternativ du kan välja mellan när du skapar en Linux-dator i Azure och innehåller länkar till ytterligare instruktioner"
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
    ms.date="04/12/2016"
    ms.author="iainfou"/>

# Olika sätt att skapa en virtuell Linux-dator med Resource Manager

I Azure kan du skapa en virtuell dator på olika sätt med hjälp av Resource Manager-distributionsmodellen, som passar olika användare och syften. I den här artikeln sammanfattar vi skillnaderna och alternativen som du kan välja mellan när du skapar virtuella Linux-datorer.

## Verktygsval

### Kommandogränssnitt: Azure CLI 

Använd Azure-kommandoradsgränssnittet från CLI. Du kan läsa mer om [hur du installerar Azure CLI](../xplat-cli-install.md) via npm, Dockerbehållare eller installationsskript. Följande självstudier innehåller exempel på hur du använder Azure CLI:

* [Skapa en virtuell Linux-dator från Azure CLI för utveckling och testning](virtual-machines-linux-quick-create-cli.md) 

* [Skapa en säker virtuell Linux-dator med hjälp av en Azure-mall](virtual-machines-linux-create-ssh-secured-vm-from-template.md)

* [Skapa en virtuell Linux-dator från grunden med hjälp av Azure CLI](virtual-machines-linux-create-cli-complete.md)

### Grafiskt användargränssnitt: Azure Portal

Det grafiska användargränssnittet för [Azure Portal](https://portal.azure.com) är ett enkelt sätt att testa en virtuell dator, särskilt om du precis börjat med Azure eftersom det inte finns något att installera på din dator. Använd Azure Portal när du skapar den virtuella datorn:

* [Skapa en virtuell dator som kör Linux med hjälp av Azure Portal](virtual-machines-linux-quick-create-portal.md) 

## Alternativ för operativsystem och avbildning

Med båda metoderna väljer du en avbildning baserat på vilket operativsystem du vill köra. Azure och dess samarbetspartner erbjuder många avbildningar, varav några innehåller förinstallerade program och verktyg. Du kan också ladda upp en av dina egna avbildningar.

### Azure-avbildningar

I samtliga artiklar ovan kan du enkelt använda en befintlig Azure-avbildning för att skapa en virtuell dator och anpassa den efter nätverk, nätverksbelastning och så vidare. Portalen fungerar som Azure Marketplace för Azure-avbildningar. Du kan visa liknande listor med hjälp av kommandoraden. Kör till exempel `azure vm image list` i Azure CLI om du vill hämta en lista över alla tillgängliga avbildningar, efter plats och utgivare. [Analysera och välja avbildningar för virtuell Azure-datorer med Azure CLI](virtual-machines-linux-cli-ps-findimage.md) innehåller exempel på hur du hittar och använder tillgängliga avbildningar.

### Använda en egen avbildning

Om du behöver specifika anpassningar kan du använda en avbildning baserat på en befintlig virtuell Azure-dator genom att *avbilda* den virtuella datorn eller genom att ladda upp en egen avbildning som lagras på en virtuell hårddisk (VHD). Mer information om distributioner som stöds och hur du använder egna avbildningar finns i följande artiklar:

* [Azure-godkända distributioner](virtual-machines-linux-endorsed-distros.md)

* [Information om icke-godkända distributioner](virtual-machines-linux-create-upload-generic.md)

* [Avbilda en virtuell Linux-dator som en Resource Manager-mall](virtual-machines-linux-capture-image.md). 

## Nästa steg

* Använd någon av följande självstudiekurser för att skapa en virtuell Linux-dator från [portalen](virtual-machines-linux-quick-create-portal.md), med [CLI](virtual-machines-linux-quick-create-cli.md) eller genom att använda en Azure Resource Manager-[mall](virtual-machines-linux-cli-deploy-templates.md).

* När du har skapat en virtuell Linux-dator kan du enkelt [lägga till en datadisk](virtual-machines-linux-add-disk.md).

* Snabba steg för att [återställa ett lösenord eller SSH-nycklar och hantera användare](virtual-machines-linux-using-vmaccess-extension.md)



<!--HONumber=Jun16_HO2-->


