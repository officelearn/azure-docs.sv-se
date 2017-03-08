---
title: "Skapa en virtuell Linux-dator med hjälp av Azure Portal | Microsoft Docs"
description: "Skapa en virtuell Linux-dator med hjälp av Azure Portal."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cc5dc395-dc54-4402-8804-2bb15aba8ea2
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: hero-article
ms.date: 1/17/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: beff4fb41ed46b016088734054e7a7897fed1a30
ms.openlocfilehash: 7287b87b1e50e28de06a5363a1f35bd7ac34d51c
ms.lasthandoff: 02/15/2017


---
# <a name="create-a-linux-vm-on-azure-using-the-portal"></a>Skapa en virtuell Linux-dator i Azure med hjälp av portalen
I den här artikeln lär du dig hur du använder [Azure-portalen](https://portal.azure.com/) för att skapa en virtuell Linux-dator.

Kraven är:

* [ett Azure-konto](https://azure.microsoft.com/pricing/free-trial/)
* [offentliga och privata SSH-nyckelfiler](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="sign-in"></a>Logga in
Logga in på Azure Portal med din Azure-kontoidentitet. Klicka på **+ Ny** i det övre vänstra hörnet:

![Skapa en Azure-resurs](./media/virtual-machines-linux-quick-create-portal/create_new_resource.png)

## <a name="choose-vm"></a>Välj virtuell dator
Klicka på **Beräkna** under **Marketplace** och välj sedan **Ubuntu Server 16.04 LTS** i avbildningslistan **Aktuella appar**.  Kontrollera längst ned att distributionsmodellen är `Resource Manager` och klicka sedan på **Skapa**.

![Välj en avbildning av en virtuell dator från Azure Marketplace](./media/virtual-machines-linux-quick-create-portal/create_new_vm.png)

## <a name="enter-vm-options"></a>Ange alternativ för virtuell dator
På sidan **Grundläggande inställningar** anger du:

* ett namn för den virtuella datorn
* typ av virtuell datordisk (SSD som standard, eller hårddisk)
* ett användarnamn för administratören
* ställ in **Autentiseringstyp** till **Offentlig SSH-nyckel**
* din offentliga SSH-nyckel som en sträng (från `~/.ssh/`-katalogen)
* ett resursgruppsnamn eller välj en befintlig resursgrupp

och fortsätt genom att klicka på **OK**. Bladet bör se ut ungefär som på följande skärmbild:

![Ange de grundläggande alternativen för virtuella Azure-datorer](./media/virtual-machines-linux-quick-create-portal/enter_basic_vm_details.png)

## <a name="choose-vm-size"></a>Välj storlek på virtuell dator
Välj storlek på den virtuella datorn. I följande exempel väljer du **DS1_V2 Standard**, vilket gör att Ubuntu installeras på en Premium SSD. **S**:et i storleksbeteckningen för den virtuella datorn visar att den har stöd för SSD. Klicka på **Välj** så kan du konfigurera inställningarna.

![Välj storlek på en virtuell dator i Azure](./media/virtual-machines-linux-quick-create-portal/select_vm_size.png)

## <a name="storage-and-network"></a>Lagring och nätverk
På bladet **Inställningar** kan du välja att använda Azure Managed Disks för den virtuella datorn. Den aktuella standardinställningen är att använda ohanterade diskar. Azure Managed Disks hanteras av Azure-plattformen och kräver inte någon förberedelser eller plats för att lagra dem. Mer information om Azure Managed Disks finns i [Översikt över Azure Managed Disks](../storage/storage-managed-disks-overview.md). För ohanterade diskar behöver du skapa eller välja ett lagringskonto för virtuella hårddiskar:

![Välj lagringskonto för icke-hanterade diskar](./media/virtual-machines-linux-quick-create-portal/configure_non_managed_disks.png)

Om du väljer att använda Azure Managed Disks finns det inga ytterligare lagringsutrymmesalternativ att konfigurera, som följande exempel visar:

![Välj alternativet för Azure Managed Disks i portalen](./media/virtual-machines-linux-quick-create-portal/select_managed_disks.png)

Lämna resten av nätverksinställningarna på standardalternativen.

## <a name="confirm-vm-settings-and-launch"></a>Bekräfta inställningarna för virtuell dator och starta
Bekräfta inställningarna för din nya virtuella Ubuntu-dator och klicka på **OK**.

![Granska inställningarna för den virtuella Azure-datorn och skapa den virtuella datorn](./media/virtual-machines-linux-quick-create-portal/review_final_vm_settings.png)

## <a name="select-the-vm-resource"></a>Välj den virtuella datorresursen
Öppna portalens startsida och välj **Resursgrupper** på menyn i det övre vänstra hörnet. Om det behövs klickar du på de tre staplarna längst upp på menyn för att expandera listan enligt följande:

![Öppna listan över resursgrupper](./media/virtual-machines-linux-quick-create-portal/select_resource_group.png)

Välj en resursgrupp och klicka på en ny virtuell dator:

![Hitta nätverkskortsinställningarna för en virtuell Azure-dator](./media/virtual-machines-linux-quick-create-portal/select_vm_resource.png)

## <a name="find-the-public-ip"></a>Hitta den offentliga IP-adressen
Visa den **offentliga IP-adress** som har tilldelats den virtuella datorn:

![Skaffa den offentliga IP-adressen till den virtuella Azure-datorn](./media/virtual-machines-linux-quick-create-portal/view_public_ip_address.png)

## <a name="ssh-to-the-vm"></a>SSH till den virtuella datorn
SSH till den offentliga IP-adressen med hjälp av den offentliga SSH-nyckeln.  Du kan SSH direkt från terminalen från en Mac- eller Linux-arbetsstation. Om du är på en Windows-arbetsstation måste du använda PuTTY, MobaXTerm eller Cygwin för att SSH till Linux.  Om du inte redan är redo, finns här ett dokument som gör din Windows-arbetsstation redo att SSH till Linux.

[Så använder du SSH-nycklar med Windows på Azure](virtual-machines-linux-ssh-from-windows.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

```
ssh -i ~/.ssh/azure_id_rsa ops@40.112.255.214
```

## <a name="next-steps"></a>Nästa steg
Nu har du snabbt skapat en virtuell Linux-dator för testnings- eller demonstrationsändamål. Om du vill skapa en virtuell Linux-dator som är anpassad efter din infrastruktur kan du följa vilken som helst av dessa artiklar.

* [Skapa en virtuell Linux-dator i Azure med hjälp av mallar](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en SSH-skyddad virtuell Linux-dator i Azure med hjälp av mallar](virtual-machines-linux-create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Skapa en virtuell Linux-dator med hjälp av Azure CLI](virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


