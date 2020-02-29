---
title: Skapa en klassisk SQL Server VM (PowerShell)
description: Innehåller steg och PowerShell-skript för att skapa en virtuell Azure-dator med SQL Server Galleri avbildningar för virtuella datorer. I det här avsnittet används det klassiska distributions läget.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: b73be387-9323-4e08-be53-6e5928e3786e
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/07/2017
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 5bfdcfab37091a5f581ce147c0a6af5ccd8147a0
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "77914797"
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Etablera en SQL Server virtuell dator med Azure PowerShell (klassisk)

[!INCLUDE [classic-vm-deprecation](../../../../includes/classic-vm-deprecation.md)]

Den här artikeln innehåller anvisningar för hur du skapar en SQL Server virtuell dator i Azure med hjälp av PowerShell-cmdlets.

> [!NOTE] 
> Azure har två olika distributions modeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/management/deployment-models.md). Den här artikeln beskriver hur du använder den klassiska distributions modellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

Information om Resource Manager-versionen av det här avsnittet finns i [etablera en SQL Server virtuell dator med hjälp av Azure PowerShell Resource Manager](../sql/virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>Installera och konfigurera PowerShell:
1. Om du inte har något Azure-konto besöker du sidan för [kostnadsfria utvärderingsversioner av Azure](https://azure.microsoft.com/pricing/free-trial/).
2. [Hämta och installera de senaste Azure PowerShell-kommandona](/powershell/azure/overview).
3. Starta Windows PowerShell och Anslut den till din Azure-prenumeration med kommandot **Add-AzureAccount** .

   ```powershell
   Add-AzureAccount
   ```

## <a name="determine-your-target-azure-region"></a>Fastställ din Azure-region

Din SQL Server virtuella dator kommer att finnas i en moln tjänst som finns i en angiven Azure-region. Följande steg hjälper dig att fastställa din region, ditt lagrings konto och din moln tjänst som ska användas för resten av självstudien.

1. Bestäm vilket data Center du vill använda som värd för din SQL Server VM. Följande PowerShell-kommando visar en lista över tillgängliga regions namn.

   ```powershell
   (Get-AzureLocation).Name
   ```

2. När du har identifierat den önskade platsen ställer du in en variabel med namnet **$dcLocation** i regionen. Följande kommando anger till exempel regionen "USA, östra":

   ```powershell
   $dcLocation = "East US"
   ```

## <a name="set-your-subscription-and-storage-account"></a>Ange din prenumeration och ditt lagrings konto

1. Ta reda på vilken Azure-prenumeration du ska använda för den nya virtuella datorn.

   ```powershell
   (Get-AzureSubscription).SubscriptionName
   ```

2. Tilldela din Azure Azure-prenumeration till variabeln **$subscr** . Ange sedan den som din aktuella Azure-prenumeration.

   ```powershell
   $subscr="<subscription name>"
   Select-AzureSubscription -SubscriptionName $subscr –Current
   ```

3. Sök sedan efter befintliga lagrings konton. Följande skript visar alla lagrings konton som finns i den valda regionen:

   ```powershell
   (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName
   ```

   > [!NOTE]
   > Om du behöver ett nytt lagrings konto måste du först skapa ett lagrings konto med en versal med kommandot New-AzureStorageAccount som i följande exempel: `New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation`

4. Tilldela mål lagrings kontots namn till **$staccount**. Använd sedan **set-AzureSubscription** för att ange prenumerationen och det aktuella lagrings kontot.

   ```powershell
   $staccount="<storage account name>"
   Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
   ```

## <a name="select-a-sql-server-virtual-machine-image"></a>Välj en SQL Server avbildning av virtuell dator

1. Ta reda på listan med tillgängliga SQL Server virtuella dator avbildningar från galleriet. Dessa bilder har alla en **ImageFamily** -egenskap som börjar med "SQL". Följande fråga visar avbildnings familjen som är tillgänglig för dig som har SQL Server förinstallerat.

   ```powershell
   Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily
   ```

2. När du hittar avbildnings familjen för den virtuella datorn kan det finnas flera publicerade avbildningar i den här serien. Använd följande skript för att hitta det senaste publicerade avbildnings namnet för den virtuella datorn för din valda avbildnings familj (till exempel **SQL Server 2016 RTM Enterprise på Windows Server 2012 R2**):

   ```powershell
   $family="<ImageFamily value>"
   $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

   echo "Selected SQL Server image name:"
   echo "   $image"
   ```

## <a name="create-the-virtual-machine"></a>Skapa den virtuella datorn

Skapa slutligen den virtuella datorn med PowerShell:

1. Skapa en moln tjänst som värd för den nya virtuella datorn. Observera att det också är möjligt att använda en befintlig moln tjänst i stället. Skapa en ny variabel **$svcname** med det korta namnet på moln tjänsten.

   ```powershell
   $svcname = "<cloud service name>"
   New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
   ```

2. Ange namnet på den virtuella datorn och en storlek. Mer information om storlekar för virtuella datorer finns i [storlekar för virtuella datorer för Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

   ```powershell
   $vmname="<machine name>"
   $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
   $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image
   ```

3. Ange det lokala administratörs kontot och lösen ordet.

   ```powershell
   $cred=Get-Credential -Message "Type the name and password of the local administrator account."
   $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
   ```

4. Kör följande skript för att skapa den virtuella datorn.

   ```powershell
   New-AzureVM –ServiceName $svcname -VMs $vm1
   ```

> [!NOTE]
> Ytterligare förklaringar och konfigurations alternativ finns i avsnittet **skapa en kommando uppsättning** i [använda Azure PowerShell för att skapa och förkonfigurera Windows-baserade Virtual Machines](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="example-powershell-script"></a>Exempel på PowerShell-skript

Följande skript innehåller ett exempel på ett fullständigt skript som skapar en **SQL Server 2016 RTM Enterprise på Windows Server 2012 R2** virtuell dator. Om du använder det här skriptet måste du anpassa de inledande variablerna baserat på föregående steg i det här avsnittet.

```powershell
# Customize these variables based on your settings and requirements:
$dcLocation = "East US"
$subscr="mysubscription"
$staccount="mystorageaccount"
$family="SQL Server 2016 RTM Enterprise on Windows Server 2012 R2"
$svcname = "mycloudservice"
$vmname="myvirtualmachine"
$vmsize="A5"

# Set the current subscription and storage account
# Comment out the New-AzureStorageAccount line if the account already exists
Select-AzureSubscription -SubscriptionName $subscr –Current
New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

# Select the most recent VM image in this image family:
$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

# Create the new cloud service; comment out this line if cloud service exists already:
New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

# Create the VM config:
$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

# Set administrator credentials:
$cred=Get-Credential -Message "Type the name and password of the local administrator account."
$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

# Create the SQL Server VM:
New-AzureVM –ServiceName $svcname -VMs $vm1
```

## <a name="connect-with-remote-desktop"></a>Anslut med fjärr skrivbord

1. Skapa RDP-filerna i den aktuella användarens dokumentmall för att starta de virtuella datorerna för att slutföra installationen:

   ```powershell
   $documentspath = [environment]::getfolderpath("mydocuments")
   Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"
   ```

2. I katalogen dokument startar du RDP-filen. Anslut med administratörs användar namnet och lösen ordet som angavs tidigare (till exempel om ditt användar namn var VMAdmin anger du "\VMAdmin" som användare och anger lösen ordet).

   ```powershell
   cd $documentspath
   .\vm1.rdp
   ```

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Slutför konfigurationen av SQL Server datorn för fjärråtkomst

När du har loggat in på datorn med fjärr skrivbord konfigurerar du SQL Server baserat på anvisningarna i [steg för att konfigurera SQL Server anslutning i en virtuell Azure-dator](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## <a name="next-steps"></a>Nästa steg

Du hittar ytterligare instruktioner för hur du konfigurerar virtuella datorer med PowerShell i dokumentationen för [Virtual Machines](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

I många fall är nästa steg att migrera dina databaser till den nya SQL Server VM. Vägledning för databas migrering finns i [Migrera en databas till SQL Server på en virtuell Azure-dator](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Om du också är intresse rad av att använda Azure Portal för att skapa SQL Virtual Machines, se hur du [konfigurerar en SQL Server virtuell dator på Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md). Observera att den självstudie som vägleder dig genom portalen skapar virtuella datorer med den rekommenderade Resource Manager-modellen, i stället för den klassiska modellen som används i det här PowerShell-avsnittet.

Förutom de här resurserna rekommenderar vi att du granskar [andra avsnitt som handlar om att köra SQL Server i Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
