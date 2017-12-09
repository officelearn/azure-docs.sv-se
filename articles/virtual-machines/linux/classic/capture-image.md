---
title: "Hämta en avbildning av en Linux-VM | Microsoft Docs"
description: "Lär dig mer om att hämta en avbildning av en Linux-baserade Azure virtuell dator (VM) skapas med den klassiska distributionsmodellen."
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 17d7ffee-a58e-4290-9de1-64c3cf1ddc05
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: iainfou
ms.openlocfilehash: f140ff3841259251ddaf39124db2756084b26a53
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>Spara en klassisk virtuell Linux-dator som en avbildning
> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Lär dig hur du [utför dessa steg med hjälp av Resource Manager-modellen](../capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Den här artikeln visar hur du skapar en klassiska Azure-dator (VM) kör Linux som en bild för att skapa andra virtuella datorer. Den här avbildningen innehåller operativsystemdisken och datadiskar som är kopplade till den virtuella datorn. Det finns inget nätverkskonfigurationen, så du behöver konfigurera som när du skapar den andra virtuella datorn från avbildningen.

Azure lagrar bilden under **bilder**, tillsammans med eventuella avbildningar som du har överfört. Mer information om avbildningar finns [om avbildningar av virtuella datorer i Azure][About Virtual Machine Images in Azure].

## <a name="before-you-begin"></a>Innan du börjar
Dessa instruktioner förutsätter att du redan skapat en virtuell Azure-dator med hjälp av den klassiska distributionsmodellen och konfigurerat operativsystemet, inklusive bifoga datadiskar. Om du behöver skapa en virtuell dator kan läsa [hur du skapar en virtuell Linux-dator][How to Create a Linux Virtual Machine].

## <a name="capture-the-virtual-machine"></a>Avbilda den virtuella datorn
1. [Ansluta till den virtuella datorn](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) med hjälp av en SSH-klient av önskat slag.
2. Skriv följande kommando i SSH-fönstret. Utdata från `waagent` kan variera beroende på vilken version av det här verktyget:

    ```bash
    sudo waagent -deprovision+user
    ```

    Föregående kommando försöker att rensa systemet och gör den lämplig för reprovisioning. Den här åtgärden utför följande uppgifter:

   * Tar bort SSH-nycklar för värd (om Provisioning.RegenerateSshHostKeyPair är ”y” i konfigurationsfilen)
   * Rensar nameserver konfigurationen i /etc/resolv.conf
   * Tar bort den `root` användarlösenord från/etc/shadow (om Provisioning.DeleteRootPassword ”y” i konfigurationsfilen)
   * Tar bort cachelagrade lån för DHCP-klienter
   * Återställer värdnamnet till localhost.localdomain
   * Tar bort sist etablerats användarkonto (hämtades från /var/lib/waagent) **och tillhörande data**.

     > [!NOTE]
     > Avetablering tar bort filer och data att ”generalisera” bilden. Endast köra kommandot på en virtuell dator som du vill samla in som en ny mall för avbildningen. Är det inte säkert att avbildningen är avmarkerad av all känslig information eller lämpar sig för omfördelning till tredje part.

3. Typen **y** att fortsätta. Du kan lägga till den `-force` parametern för att undvika det här steget bekräftelse.
4. Typen **avsluta** att stänga SSH-klienten.

   > [!NOTE]
   > Stegen förutsätter att du redan har [installerade Azure CLI](../../../cli-install-nodejs.md) på klientdatorn. Följande steg kan även utföras den [Azure-portalen](http://portal.azure.com).

5. Öppna Azure CLI och logga in på Azure-prenumerationen från klientdatorn. Mer information läser [Anslut till en Azure-prenumeration från Azure CLI](../../../xplat-cli-connect.md).

   > [!NOTE]
   > Logga in på portalen i Azure-portalen.

6. Kontrollera att du befinner dig i Service Management-läge:

    ```azurecli
    azure config mode asm
    ```

7. Stäng av den virtuella datorn som redan avetableras. I följande exempel stängs den virtuella datorn med namnet `myVM`:

    ```azurecli
    azure vm shutdown myVM
    ```
   Om det behövs kan du visa en lista alla de virtuella datorerna i din prenumeration har skapat med hjälp`azure vm list`

   > [!NOTE]
   > Om du använder Azure portal, Välj den virtuella datorn och klicka på **stoppa** att stänga av den virtuella datorn.

8. Fånga avbildningen när den virtuella datorn har stoppats. I följande exempel samlar in den virtuella datorn med namnet `myVM` och skapar en generaliserad avbildning med namnet `myNewVM`:

    ```azurecli
    azure vm capture -t myVM myNewVM
    ```

    Den `-t` underkommandot tar bort den ursprungliga virtuella datorn.

    > [!NOTE]
    > I Azure-portalen kan du göra en avbildning genom att välja **bild** från navmenyn. Du måste ange följande information för avbildningen: namn, resursgruppens namn, plats, typ av operativsystem och blob-sökvägen för lagring.

9. Den nya avbildningen är nu tillgänglig i listan över avbildningar som kan användas för att konfigurera en ny virtuell dator. Du kan visa den med kommandot:

   ```azurecli
   azure vm image list
   ```

   På den [Azure-portalen](http://portal.azure.com), den nya avbildningen visas i den **VM-avbildningar (klassisk)** som tillhör den **Compute** tjänster. Du kan komma åt **VM-avbildningar (klassisk)** genom att klicka på _fler tjänster_ längst ned i Azure service lista och sedan söker den **Compute** tjänster.   

   ![Avbildningen lyckades](./media/capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>Nästa steg
Bilden är redo att användas för att skapa virtuella datorer. Du kan använda kommandot Azure CLI `azure vm create` och ange Avbildningsnamnet som du skapade. Mer information finns i [med hjälp av Azure CLI med klassiska distributionsmodellen](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

Du kan också använda den [Azure-portalen](http://portal.azure.com) att skapa en egen virtuell dator med hjälp av den **bild** metoden och välja den bild som du skapade. Mer information finns i [hur du skapar en anpassad VM][How to Create a Custom Virtual Machine].

**Se även:** [användarhandboken för Azure Linux-Agent](../agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[About Virtual Machine Images in Azure]:../../virtual-machines-linux-classic-about-images.md
[How to Create a Custom Virtual Machine]:create-custom.md
[How to Attach a Data Disk to a Virtual Machine]:attach-disk.md
[How to Create a Linux Virtual Machine]:create-custom.md
