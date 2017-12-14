---
title: "Skapa och ladda upp en VM-avbildning med hjälp av Powershell | Microsoft Docs"
description: "Lär dig att skapa och ladda upp en generaliserad Windows Server-avbildning (VHD) med den klassiska distributionsmodellen och Azure Powershell."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 8c4a08fe-7714-4bf0-be87-c728a7806d3f
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ms.openlocfilehash: c2540120bcb1eca9f4ba62c7dbc0675343bf4f99
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2017
---
# <a name="create-and-upload-a-windows-server-vhd-to-azure"></a>Skapa och ladda upp en Windows Server VHD till Azure
Den här artikeln visar hur du överför en egen generaliserad VM-avbildning som en virtuell hårddisk (VHD), så du kan använda för att skapa virtuella datorer. Mer information om diskar och virtuella hårddiskar i Microsoft Azure finns [om diskar och virtuella hårddiskar för virtuella datorer](../about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen. Du kan också [överför](../upload-generalized-managed.md) en virtuell dator med Resource Manager-modellen.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

## <a name="prerequisites"></a>Krav
Den här artikeln förutsätter att du har:

* **En Azure-prenumeration** -om du inte har någon, kan du [öppna ett Azure-konto gratis](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).
* **[Microsoft Azure PowerShell](/powershell/azure/overview)**  -du har Microsoft Azure PowerShell-modulen installerad och konfigurerad för att använda din prenumeration.
* **EN. VHD-filen** - stöds Windows operativsystem lagras i en VHD-fil och bifogas till en virtuell dator. Kontrollera om de serverroller som körs på den virtuella Hårddisken som stöds av Sysprep. Mer information finns i [Sysprep-stöd för serverroller](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

    > [!IMPORTANT]
    > VHDX-format stöds inte i Microsoft Azure. Du kan konvertera disken till VHD-format med hjälp av Hyper-V Manager eller [Convert-VHD cmdlet](http://technet.microsoft.com/library/hh848454.aspx). Mer information finns i det här [blogginlägg](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).

## <a name="step-1-prep-the-vhd"></a>Steg 1: Förbered dig den virtuella Hårddisken
Innan du överför den virtuella Hårddisken till Azure, måste den vara generaliserad med Sysprep-verktyget. Detta förbereder den virtuella Hårddisken som ska användas som en bild. Mer information om Sysprep finns [så att använda Sysprep: en introduktion](http://technet.microsoft.com/library/bb457073.aspx). Säkerhetskopiera den virtuella datorn innan du kör Sysprep.

Slutför följande procedur från den virtuella datorn som operativsystemet har installerats på:

1. Logga in till operativsystemet.
2. Öppna ett kommandotolksfönster som administratör. Ändra katalogen till **%windir%\system32\sysprep**, och kör sedan `sysprep.exe`.

    ![Öppna ett kommandotolksfönster](./media/createupload-vhd/sysprep_commandprompt.png)
3. Den **systemförberedelseverktyget** dialogrutan visas.

   ![Starta Sysprep](./media/createupload-vhd/sysprepgeneral.png)
4. I den **systemförberedelseverktyget**väljer **ange System Box Experience OOBE (Out of)** och se till att **Generalize** är markerad.
5. I **avstängningsalternativ**väljer **avstängning**.
6. Klicka på **OK**.

## <a name="step-2-create-a-storage-account-and-a-container"></a>Steg 2: Skapa ett lagringskonto och en behållare
Du behöver ett lagringskonto i Azure så att du har en plats för att överföra VHD-filen. Det här steget visar hur du skapar ett konto eller hämta den information du behöver från ett befintligt konto. Ersätt variabler i &lsaquo; hakparenteser &rsaquo; med din egen information.

1. Inloggning

    ```powershell
    Add-AzureAccount
    ```

2. Ange din Azure-prenumeration.

    ```powershell
    Select-AzureSubscription -SubscriptionName <SubscriptionName>
    ```

3. Skapa ett nytt lagringskonto. Namnet på lagringskontot måste vara unikt, 3 till 24 tecken. Namnet kan vara valfri kombination av bokstäver och siffror. Du måste också ange en plats som ”Öst oss”

    ```powershell
    New-AzureStorageAccount –StorageAccountName <StorageAccountName> -Location <Location>
    ```

4. Ange det nya lagringskontot som standard.

    ```powershell
    Set-AzureSubscription -CurrentStorageAccountName <StorageAccountName> -SubscriptionName <SubscriptionName>
    ```

5. Skapa en ny behållare.

    ```powershell
    New-AzureStorageContainer -Name <ContainerName> -Permission Off
    ```

## <a name="step-3-upload-the-vhd-file"></a>Steg 3: Överför VHD-filen
Använd den [Add-AzureVhd](https://docs.microsoft.com/en-us/powershell/module/azure/add-azurevhd) att överföra den virtuella Hårddisken.

Azure PowerShell-fönster som du använde i föregående steg, Skriv följande kommando och Ersätt variabler i &lsaquo; hakparenteser &rsaquo; med din egen information.

```powershell
Add-AzureVhd -Destination "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -LocalFilePath <LocalPathtoVHDFile>
```

## <a name="step-4-add-the-image-to-your-list-of-custom-images"></a>Steg 4: Lägga till avbildningen i listan över anpassade avbildningar
Använd den [Lägg till AzureVMImage](https://docs.microsoft.com/en-us/powershell/module/azure/add-azurevmimage) för att lägga till bilden i listan över egna, anpassade avbildningar.

```powershell
Add-AzureVMImage -ImageName <ImageName> -MediaLocation "https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/<vhdName>.vhd" -OS "Windows"
```

## <a name="next-steps"></a>Nästa steg
Du kan nu [skapa en egen virtuell dator](createportal.md) med hjälp av den avbildning som du har överfört.
