---
title: "Skapa en virtuell Linux-dator med hjälp av Azure Portal | Microsoft Docs"
description: "Skapa en virtuell Linux-dator med hjälp av Azure Portal."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cc5dc395-dc54-4402-8804-2bb15aba8ea2
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: hero-article
ms.date: 10/28/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b1446cd8892e14988ff428eaa03233f8e9aefb8a


---
# <a name="create-a-linux-vm-on-azure-using-the-portal"></a>Skapa en virtuell Linux-dator i Azure med hjälp av portalen
I den här artikeln lär du dig hur du använder [Azure-portalen](https://portal.azure.com/) för att skapa en virtuell Linux-dator.

Kraven är:

* [ett Azure-konto](https://azure.microsoft.com/pricing/free-trial/)
* [offentliga och privata SSH-nyckelfiler](virtual-machines-linux-mac-create-ssh-keys.md)

## <a name="sign-in"></a>Logga in
När du är inloggad på Azure Portal med din Azure-kontoidentitet klickar du på **+ Nytt** i det övre vänstra hörnet:

![screen1](../media/virtual-machines-linux-quick-create-portal/screen1.png)

## <a name="choose-vm"></a>Välj virtuell dator
Klicka på **Virtual Machines** på **Marketplace** och sedan på **Ubuntu Server 14.04 LTS** i listan **Aktuella appar**.  Kontrollera längst ned att distributionsmodellen är `Resource Manager` och klicka sedan på **Skapa**.

![screen2](../media/virtual-machines-linux-quick-create-portal/screen2.png)

## <a name="enter-vm-options"></a>Ange alternativ för virtuell dator
På sidan **Grundläggande inställningar** anger du:

* ett namn för den virtuella datorn
* ett användarnamn för administratören
* autentiseringstypen till **Offentlig SSH-nyckel**
* din offentliga SSH-nyckel som en sträng (från `~/.ssh/`-katalogen)
* ett resursgruppsnamn eller väljer en befintlig grupp

och klickar på **OK** för att fortsätta och välja en storlek för den virtuella datorn. Det bör se ut ungefär som på följande skärmbild:

![screen3](../media/virtual-machines-linux-quick-create-portal/screen3.png)

## <a name="choose-vm-size"></a>Välj storlek på virtuell dator
Välj storleken **DS1** som installerar Ubuntu på en Premium SSD och klicka på **Välj** för att konfigurera inställningar.

![screen4](../media/virtual-machines-linux-quick-create-portal/screen4.png)

## <a name="storage-and-network"></a>Lagring och nätverk
I **Inställningar** lämnar du standardinställningarna för Storage- och nätverksvärdena och klickar på **OK** för att visa sammanfattningen.  Observera att disktypen har angetts till Premium SSD eftersom du valde DS1. Bokstaven **S** representerar SSD.

![screen5](../media/virtual-machines-linux-quick-create-portal/screen5.png)

## <a name="confirm-vm-settings-and-launch"></a>Bekräfta inställningarna för virtuell dator och starta
Bekräfta inställningarna för din nya virtuella Ubuntu-dator och klicka på **OK**.

![screen6](../media/virtual-machines-linux-quick-create-portal/screen6.png)

## <a name="find-the-vm-nic"></a>Hitta det virtuella nätverkskortet
Öppna portalens instrumentpanel och välj ditt nätverkskort i **Nätverksgränssnitt**

![screen7](../media/virtual-machines-linux-quick-create-portal/screen7.png)

## <a name="find-the-public-ip"></a>Hitta den offentliga IP-adressen
Öppna menyn Offentliga IP-adresser under inställningarna för nätverkskortet

![screen8](../media/virtual-machines-linux-quick-create-portal/screen8.png)

## <a name="ssh-to-the-vm"></a>SSH till den virtuella datorn
SSH till den offentliga IP-adressen med hjälp av den offentliga SSH-nyckeln.  Du kan SSH direkt från terminalen från en Mac- eller Linux-arbetsstation. Om du är på en Windows-arbetsstation måste du använda PuTTY, MobaXTerm eller Cygwin för att SSH till Linux.  Om du inte redan är redo, finns här ett dokument som gör din Windows-arbetsstation redo att SSH till Linux.

[Så använder du SSH-nycklar med Windows på Azure](virtual-machines-linux-ssh-from-windows.md)

```
ssh -i ~/.ssh/azure_id_rsa ubuntu@13.91.99.206
```

## <a name="next-steps"></a>Nästa steg
Nu har du snabbt skapat en virtuell Linux-dator för testnings- eller demonstrationsändamål. Om du vill skapa en virtuell Linux-dator som är anpassad efter din infrastruktur kan du följa vilken som helst av dessa artiklar.

* [Skapa en virtuell Linux-dator i Azure med hjälp av mallar](virtual-machines-linux-cli-deploy-templates.md)
* [Skapa en SSH-skyddad virtuell Linux-dator i Azure med hjälp av mallar](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
* [Skapa en virtuell Linux-dator med hjälp av Azure CLI](virtual-machines-linux-create-cli-complete.md)




<!--HONumber=Nov16_HO2-->


