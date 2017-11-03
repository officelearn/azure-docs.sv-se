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
ms.openlocfilehash: 0010e01d4333b96696680ec6fbbeee74b17f46a3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
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

Den här uppgiften innehåller följande fem steg:

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

## <a name="step-2-create-a-storage-account-in-azure"></a>Steg 2: Skapa ett lagringskonto i Azure
Du behöver ett lagringskonto i Azure för att ladda upp en VHD-fil så att den kan användas för att skapa en virtuell dator. Du kan använda den klassiska Azure-portalen för att skapa ett lagringskonto.

1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com).
2. Välj på kommandofältet **ny**.
3. Välj **datatjänster** > **lagring** > **Snabbregistrering**.

    ![Snabbt skapa ett lagringskonto](./media/freebsd-create-upload-vhd/Storage-quick-create.png)
4. Fyll i fälten på följande sätt:

   * I den **URL** skriver du ett underdomännamn ska användas i URL: en för storage-konto. Posten kan innehålla 3 till 24 siffror och gemener. Det här namnet blir värdnamnet inom den URL som används för att adressera Azure Blob storage, Azure Queue storage eller Azure Table storage-resurser för prenumerationen.
   * I den **plats/Tillhörighetsgrupp** nedrullningsbara menyn, Välj den **platsen eller tillhörighetsgruppen** för lagringskontot. En tillhörighetsgrupp kan du placera dina molntjänster och lagring i samma datacenter.
   * I den **replikering** fältet, välja om du vill använda **Geo-Redundant** replikering för lagringskontot. GEO-replikering är aktiverad som standard. Det här alternativet replikerar data till en sekundär plats, utan kostnad, så att din lagring flyttas över till den platsen om en större fel uppstår på den primära platsen. Den sekundära platsen tilldelas automatiskt och kan inte ändras. Om du behöver mer kontroll över platsen för din molnbaserade lagringsenheter på grund av juridiska krav eller organisationens principer kan inaktivera du geo-replikering. Men tänk på att om du aktiverar senare geo-replikering kan du debiteras en avgift för överföringen av enstaka data replikeras dina befintliga data till den sekundära platsen. Lagringstjänster utan geo-replikering erbjuds med rabatt. Mer information om hur du hanterar geo-replikering av lagringskonton finns här: [Azure Storage-replikering](../../../storage/common/storage-redundancy.md).

     ![Ange information om lagringskonto](./media/freebsd-create-upload-vhd/Storage-create-account.png)
5. Välj **skapa Lagringskonto**. Kontot visas nu under **lagring**.

    ![Storage-konto har skapats](./media/freebsd-create-upload-vhd/Storagenewaccount.png)
6. Skapa sedan en behållare för dina överförda VHD-filer. Välj lagringskontonamn och välj sedan **behållare**.

    ![Kontoinformation för lagring](./media/freebsd-create-upload-vhd/storageaccount_detail.png)
7. Välj **skapa en behållare**.

    ![Kontoinformation för lagring](./media/freebsd-create-upload-vhd/storageaccount_container.png)
8. I den **namnet** skriver du ett namn för din behållare. I den **åtkomst** listrutan väljer du vilken typ av princip.

    ![Behållarens namn](./media/freebsd-create-upload-vhd/storageaccount_containervalues.png)

   > [!NOTE]
   > Som standard behållaren är privat och kan bara användas av ägare. Om du vill ge offentlig läsbehörighet till blobarna i behållaren, men inte behållaregenskaperna och metadata, Använd den **offentlig Blob** alternativet. Om du vill ge fullständig offentlig läsbehörighet för behållare och blobbar använda av **offentlig behållare** alternativet.
   >
   >

## <a name="step-3-prepare-the-connection-to-azure"></a>Steg 3: Förbered anslutningen till Azure
Innan du kan ladda upp en VHD-fil, måste du upprätta en säker anslutning mellan datorn och din Azure-prenumeration. Du kan använda i Azure Active Directory (AD Azure) eller certifikat-metoden för att göra detta.

### <a name="use-the-azure-ad-method-to-upload-a-vhd-file"></a>Använda Azure AD-metoden för att ladda upp en VHD-fil
1. Öppna Azure PowerShell-konsolen.
2. Ange följande kommando:  
    `Add-AzureAccount`

    Det här kommandot öppnas en inloggning där du kan logga in med ditt arbets- eller skolkonto.

    ![PowerShell-fönster](./media/freebsd-create-upload-vhd/add_azureaccount.png)
3. Azure autentiserar och sparar informationen om autentiseringsuppgifter. Sedan stängs fönstret.

### <a name="use-the-certificate-method-to-upload-a-vhd-file"></a>Använd metoden certifikat att ladda upp en VHD-fil
1. Öppna Azure PowerShell-konsolen.
2. Typ: `Get-AzurePublishSettingsFile`.
3. Ett fönster i webbläsaren öppnas och du uppmanas att hämta .publishsettings-fil. Den här filen innehåller information och ett certifikat för din Azure-prenumeration.

    ![Hämtningssidan för webbläsare](./media/freebsd-create-upload-vhd/Browser_download_GetPublishSettingsFile.png)
4. Spara .publishsettings-fil.
5. Typ: `Import-AzurePublishSettingsFile <PathToFile>`, där `<PathToFile>` är den fullständiga sökvägen till .publishsettings-fil.

   Mer information finns i [Kom igång med Azure-cmdlets](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx).

   Mer information om hur du installerar och konfigurerar PowerShell finns [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

## <a name="step-4-upload-the-vhd-file"></a>Steg 4: Överför VHD-filen
När du överför VHD-filen placerar du den någonstans i Blob storage. Följande är vissa termer som du ska använda när du överför filen:

* **BlobStorageURL** är URL-Adressen för det lagringskontot som du skapade i steg 2.
* **YourImagesFolder** är behållare i Blob storage där du vill lagra bilderna.
* **VHDName** är den etikett som visas i den klassiska Azure portalen för att identifiera den virtuella hårddisken.
* **PathToVHDFile** är den fullständiga sökvägen och namnet på VHD-filen.

Från Azure PowerShell-fönster som du använde i det föregående steget, skriver du:

        Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## <a name="step-5-create-a-vm-with-the-uploaded-vhd-file"></a>Steg 5: Skapa en virtuell dator med överförda VHD-filen
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
