---
title: Skapa en virtuell dator för SQL Server i Azure PowerShell (klassisk) | Microsoft Docs
description: Innehåller steg och PowerShell-skript för att skapa en virtuell Azure-dator med SQL Server virtuella galleriavbildningar. Det här avsnittet använder läget för klassisk distribution.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-service-management
ms.assetid: b73be387-9323-4e08-be53-6e5928e3786e
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/07/2017
ms.author: jroth
ms.openlocfilehash: 66f44e27562f33373e0b67fe6e0ebf9c6bf99e03
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
ms.locfileid: "29399669"
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Etablera en virtuell dator med SQL Server med hjälp av Azure PowerShell (klassisk)

Den här artikeln innehåller anvisningar att skapa en virtuell dator med SQL Server i Azure med hjälp av PowerShell-cmdlets.

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

Resource Manager-version av det här avsnittet finns [etablera en virtuell dator med SQL Server med Azure PowerShell Resource Manager](../sql/virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>Installera och konfigurera PowerShell:
1. Om du inte har något Azure-konto besöker du sidan för [kostnadsfria utvärderingsversioner av Azure](https://azure.microsoft.com/pricing/free-trial/).
2. [Hämta och installera de senaste Azure PowerShell-kommandon](/powershell/azure/overview).
3. Starta Windows PowerShell och ansluta till din Azure-prenumeration med den **Add-AzureAccount** kommando.

   ```powershell
   Add-AzureAccount
   ```

## <a name="determine-your-target-azure-region"></a>Bestämma mål-Azure-region

SQL Server-dator ska finnas i en molnbaserad tjänst som finns på en viss Azure-region. Följande steg hjälper dig att fastställa din region, storage-konto och Molntjänsten som ska användas för resten av kursen.

1. Fastställa datacenter som du vill använda som värd för SQL Server-VM. Följande PowerShell-kommando visar en lista över tillgängliga regionnamn.

   ```powershell
   (Get-AzureLocation).Name
   ```

2. När du har hittat din önskade plats, ange en variabel med namnet **$dcLocation** till detta område. Följande kommando anger till exempel region för ”Öst oss”:

   ```powershell
   $dcLocation = "East US"
   ```

## <a name="set-your-subscription-and-storage-account"></a>Ange din prenumeration och storage-konto

1. Fastställa Azure-prenumerationen ska användas för den nya virtuella datorn.

   ```powershell
   (Get-AzureSubscription).SubscriptionName
   ```

2. Tilldela mål-Azure-prenumeration till den **$subscr** variabeln. Ange detta till din aktuella Azure-prenumeration.

   ```powershell
   $subscr="<subscription name>"
   Select-AzureSubscription -SubscriptionName $subscr –Current
   ```

3. Sök sedan efter befintliga lagringskonton. Följande skript visar alla lagringskonton som finns i din valda region:

   ```powershell
   (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName
   ```

   > [!NOTE]
   > Om du behöver ett nytt lagringskonto, måste du först skapa en alla gemen lagringskontonamnet med kommandot New AzureStorageAccount som i följande exempel: `New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation`

4. Tilldela mål lagringskontots namn till den **$staccount**. Använd sedan **Set AzureSubscription** för prenumeration och aktuella storage-konto.

   ```powershell
   $staccount="<storage account name>"
   Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
   ```

## <a name="select-a-sql-server-virtual-machine-image"></a>Välj en avbildning av virtuell dator för SQL Server

1. Ta reda på listan med tillgängliga virtuella datorer SQL Server-avbildningar från galleriet. Dessa avbildningar som alla har en **ImageFamily** egenskap som börjar med ”SQL”. Följande fråga visar produktfamiljen avbildningen till att SQL Server vara förinstallerat.

   ```powershell
   Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily
   ```

2. När du har hittat virtuella avbildningen familj kan det finnas flera publicerade bilder i den här serien. Använda följande skript för att hitta senaste publicerade virtuella avbildningens namn för din valda avbildningen familj (exempelvis **SQL Server 2016 RTM Enterprise på Windows Server 2012 R2**):

   ```powershell
   $family="<ImageFamily value>"
   $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

   echo "Selected SQL Server image name:"
   echo "   $image"
   ```

## <a name="create-the-virtual-machine"></a>Skapa den virtuella datorn

Skapa slutligen den virtuella datorn med PowerShell:

1. Skapa en molnbaserad tjänst som värd för den nya virtuella datorn. Observera att det är också möjligt att använda en befintlig molntjänst i stället. Skapa en ny variabel **$svcname** med det korta namnet för Molntjänsten.

   ```powershell
   $svcname = "<cloud service name>"
   New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
   ```

2. Ange namnet på virtuella datorn och storlek. Mer information om storlekar för virtuella datorer finns [storlekar för virtuella datorer för Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

   ```powershell
   $vmname="<machine name>"
   $vmsize="<Specify one: Large, ExtraLarge, A5, A6, A7, A8, A9, or see the link to the other VM sizes>"
   $vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image
   ```

3. Ange det lokala administratörskontot och lösenordet.

   ```powershell
   $cred=Get-Credential -Message "Type the name and password of the local administrator account."
   $vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
   ```

4. Kör följande skript för att skapa den virtuella datorn.

   ```powershell
   New-AzureVM –ServiceName $svcname -VMs $vm1
   ```

> [!NOTE]
> Ytterligare förklaring och konfigurationsalternativ finns i **skapa din kommandouppsättning** i avsnittet [Använd Azure PowerShell för att skapa och förkonfigurera Windows-baserade virtuella datorer](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="example-powershell-script"></a>Exempel PowerShell-skript

Följande skript innehåller ett exempel på en komplett skript som skapar en **SQL Server 2016 RTM Enterprise på Windows Server 2012 R2** virtuella datorn. Om du använder det här skriptet kan anpassa du variablerna inledande baserat på de föregående stegen i det här avsnittet.

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

## <a name="connect-with-remote-desktop"></a>Ansluta med fjärrskrivbord

1. Skapa RDP-filer i den aktuella användarens dokument-mappen för att starta de virtuella datorerna för att slutföra installationen:

   ```powershell
   $documentspath = [environment]::getfolderpath("mydocuments")
   Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"
   ```

2. Starta RDP-filen i katalogen dokument. Anslut med administratören användarnamnet och lösenordet som angavs tidigare (till exempel om användarnamnet är VMAdmin, ange ”\VMAdmin” som användaren och ange lösenord).

   ```powershell
   cd $documentspath
   .\vm1.rdp
   ```

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Slutföra konfigurationen av SQL Server-datorn för fjärråtkomst

När du har loggat in på datorn med fjärrskrivbord, konfigurerar du SQL Server baserat på instruktioner i [steg för att konfigurera SQL Server-anslutningen i en Azure VM](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## <a name="next-steps"></a>Nästa steg

Du kan hitta ytterligare instruktioner för att etablera virtuella datorer med PowerShell i den [virtuella datorer dokumentationen](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

I många fall är nästa steg att migrera dina databaser till den här nya SQL Server-VM. Databasen migrering vägledning finns [migrera en databas till SQL Server på en Azure VM](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Om du är intresserad av att använda Azure portal för att skapa SQL virtuella datorer, se [etablering av en SQL Server-dator på Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md). Observera att vägledningen som hjälper dig med portalen skapar virtuella datorer med rekommenderade Resource Manager-modellen i stället för den klassiska modellen som används i det här avsnittet för PowerShell.

Förutom dessa resurser, rekommenderar vi att du läser [andra avsnitt relaterade till kör SQL Server i Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
