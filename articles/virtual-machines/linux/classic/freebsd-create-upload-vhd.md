---
title: Skapa och ladda upp en FreeBSD VM-avbildning | Microsoft Docs
description: "Lär dig hur du skapar och överför en virtuell hårddisk (VHD) som innehåller FreeBSD-operativsystem för att skapa en virtuell Azure-dator"
services: virtual-machines-linux
documentationcenter: 
author: thomas1206
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/08/2017
ms.author: huishao
ms.openlocfilehash: 7b41826f071174df8f00af56a228e0f31c3cfe2f
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="create-and-upload-a-freebsd-vhd-to-azure"></a>Skapa och ladda upp en FreeBSD VHD till Azure
Den här artikeln visar hur du skapar och överför en virtuell hårddisk (VHD) som innehåller FreeBSD-operativsystem. När du har överfört kan du använda den som en egen avbildning för att skapa en virtuell dator (VM) i Azure.

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Information om hur du överför en virtuell Hårddisk med hjälp av Resource Manager-modellen finns [här](../upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du har följande objekt:

* **En Azure-prenumeration**--om du inte har ett konto kan du skapa en på bara några minuter. Om du har en MSDN-prenumeration, se [månatliga Azure-kredit för Visual Studio-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Annars Lär dig hur du [skapa ett kostnadsfritt utvärderingskonto](https://azure.microsoft.com/pricing/free-trial/).  
* **Azure PowerShell-verktyg**--av Azure PowerShell-modulen måste installeras och konfigureras för att använda din Azure-prenumeration. För att hämta modulen, se [Azure hämtar](https://azure.microsoft.com/downloads/). En genomgång som beskriver hur du installerar och konfigurerar modulen finns här. Använd den [Azure hämtar](https://azure.microsoft.com/downloads/) för att ladda upp den virtuella Hårddisken.
* **FreeBSD operativsystem i en VHD-fil**--en stöds FreeBSD operativsystemet måste installeras på en virtuell hårddisk. Det finns flera verktyg för att skapa VHD-filer. Du kan till exempel använda en virtualiseringslösning som Hyper-V för att skapa VHD-filen och installera operativsystemet. Instruktioner om hur du installerar och använder Hyper-V finns i [installera Hyper-V och skapa en virtuell dator](http://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Nyare VHDX-format stöds inte i Azure. Du kan konvertera disken till VHD-format med hjälp av Hyper-V Manager eller cmdlet [convert-vhd](https://technet.microsoft.com/library/hh848454.aspx). Dessutom finns en [självstudier om hur du använder FreeBSD med Hyper-V på MSDN](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx).
>
>

Den här uppgiften innehåller följande fyra steg:

## <a name="step-1-prepare-the-image-for-upload"></a>Steg 1: Förbered bilden för överföring
På den virtuella datorn där du installerade operativsystemet FreeBSD, Slutför följande procedurer:

1. Aktivera DHCP.

        # echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
        # service netif restart
2. Aktivera SSH.

    Se till att SSH-server har installerats och konfigurerats för att starta när datorn startas. Som standard aktiveras efter installationen från FreeBSD-skiva. 
3. Ställ in en seriekonsol.

        # echo 'console="comconsole vidconsole"' >> /boot/loader.conf
        # echo 'comconsole_speed="115200"' >> /boot/loader.conf
4. Installera sudo.

    Rot-konto har inaktiverats i Azure. Det innebär att du behöver använda sudo från en icke-privilegierade användare att köra kommandon med förhöjda privilegier.

        # pkg install sudo
   
5. Förutsättningar för Azure-agenten.

        # pkg install python27  
        # pkg install Py27-setuptools  
        # ln -s /usr/local/bin/python2.7 /usr/bin/python   
        # pkg install git
6. Installera Azure-agenten.

    Den senaste versionen av Azure-agenten alltid finns på [github](https://github.com/Azure/WALinuxAgent/releases). Version 2.0.10 officiellt stöder FreeBSD 10 & 10.1, och versionen 2.1.4 + (inklusive 2.2.x) stöder officiellt FreeBSD 10.2 och senare versioner.

        # git clone https://github.com/Azure/WALinuxAgent.git  
        # cd WALinuxAgent  
        # git tag  
        …
        WALinuxAgent-2.0.16
        …
        v2.1.4
        v2.1.4.rc0
        v2.1.4.rc1

    För 2.0, vi använder 2.0.16 som exempel:

        # git checkout WALinuxAgent-2.0.16
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  

    För 2.1 kan vi använda 2.1.4 som exempel:

        # git checkout v2.1.4
        # python setup.py install  
        # ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  
        # ln -sf /usr/local/sbin/waagent2.0 /usr/sbin/waagent2.0

   > [!IMPORTANT]
   > När du har installerat Azure-agenten är en bra idé att kontrollera att den körs:
   >
   >

        # waagent -version
        WALinuxAgent-2.1.4 running on freebsd 10.3
        Python: 2.7.11
        # ps auxw | grep waagent
        root   639   0.0  0.5 104620 17520 u0- I    05:17    0:00.20 python /usr/local/sbin/waagent -daemon (python2.7)
        # cat /var/log/waagent.log
7. Ta bort etableringen av systemet.

    Ta bort etableringen av systemet för att rensa den och gör den lämplig för reprovisioning. Följande kommando tar också bort det senaste kontot för etablerad användare och associerade data:

        # echo "y" |  /usr/local/sbin/waagent -deprovision+user  
        # echo  'waagent_enable="YES"' >> /etc/rc.conf

    Nu kan du stänga av den virtuella datorn.

## <a name="step-2-prepare-the-connection-to-azure"></a>Steg 2: Förbered anslutningen till Azure
Kontrollera att du använder Azure CLI i den klassiska distributionsmodellen (`azure config mode asm`), sedan logga in på ditt konto:

```azurecli
azure login
```


<a id="upload"> </a>


## <a name="step-3-upload-the-vhd-file"></a>Steg 3: Överför VHD-filen

Du behöver ett lagringskonto för att överföra VHD-filen till. Du kan antingen välja ett befintligt lagringskonto eller [skapa en ny](../../../storage/common/storage-create-storage-account.md).

När du överför VHD-filen placerar du den någonstans i Blob storage. Följande är vissa termer som du ska använda när du överför filen:

* **BlobStorageURL** är URL-Adressen för det lagringskontot som du skapade i steg 2.
* **YourImagesFolder** är behållare i Blob storage där du vill lagra bilderna.
* **VHDName** är den etikett som visas i den klassiska Azure portalen för att identifiera den virtuella hårddisken.
* **PathToVHDFile** är den fullständiga sökvägen och namnet på VHD-filen.

Från Azure PowerShell-fönster som du använde i det föregående steget, skriver du:

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-4-create-a-vm-with-the-uploaded-vhd-file"></a>Steg 4: Skapa en virtuell dator med överförda VHD-filen
När du har överfört VHD-filen kan du lägga till den som en bild i listan över anpassade avbildningar som är associerade med din prenumeration och skapa en virtuell dator med den här anpassade avbildningen.

1. Från Azure PowerShell-fönster som du använde i det föregående steget, skriver du:

        Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

   > [!NOTE]
   > Använda Linux som OS-typen. Den aktuella versionen av Azure PowerShell accepterar endast ”Linux” eller ”Windows” som en parameter.
   >
   >
2. När du har slutfört föregående steg visas den nya avbildningen när du väljer den **bilder** fliken på den klassiska Azure-portalen.  

    ![Välj en bild](./media/freebsd-create-upload-vhd/addfreebsdimage.png)
3. Skapa en virtuell dator från galleriet. Den här nya avbildningen är nu tillgänglig under **Mina avbildningar**.
4. Välj den nya avbildningen. Gå sedan igenom anvisningarna för att ange ett värdnamn, lösenord, SSH-nyckeln och så vidare.

    ![Anpassad avbildning](./media/freebsd-create-upload-vhd/createfreebsdimageinazure.png)
5. När du har slutfört etableringen ser du ditt FreeBSD VM körs i Azure.

    ![FreeBSD-avbildning i azure](./media/freebsd-create-upload-vhd/freebsdimageinazure.png)
