---
title: Skapa en klassisk VIRTUELL SQL Server (PowerShell)
description: Innehåller steg och PowerShell-skript för att skapa en virtuell Azure-dator med virtuella SQL Server-galleriavbildningar. I det här avsnittet används det klassiska distributionsläget.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914797"
---
# <a name="provision-a-sql-server-virtual-machine-using-azure-powershell-classic"></a>Etablera en virtuell SQL Server-dator med Azure PowerShell (Klassisk)

[!INCLUDE [classic-vm-deprecation](../../../../includes/classic-vm-deprecation.md)]

Den här artikeln innehåller steg för hur du skapar en virtuell SQL Server-dator i Azure med hjälp av PowerShell-cmdletar.

> [!NOTE] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och Classic](../../../azure-resource-manager/management/deployment-models.md). Den här artikeln beskriver hur du använder den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

Resurshanterarens version av det här avsnittet finns [i Etablera en virtuell SQL Server-dator med Azure PowerShell Resource Manager](../sql/virtual-machines-windows-ps-sql-create.md).

### <a name="install-and-configure-powershell"></a>Installera och konfigurera PowerShell:
1. Om du inte har något Azure-konto besöker du sidan för [kostnadsfria utvärderingsversioner av Azure](https://azure.microsoft.com/pricing/free-trial/).
2. [Ladda ned och installera de senaste Azure PowerShell-kommandona](/powershell/azure/overview).
3. Starta Windows PowerShell och anslut den till din Azure-prenumeration med kommandot **Add-AzureAccount.**

   ```powershell
   Add-AzureAccount
   ```

## <a name="determine-your-target-azure-region"></a>Bestäm din Azure-målregion

Din virtuella SQL Server-dator kommer att finnas i en molntjänst som finns i en viss Azure-region. Följande steg hjälper dig att bestämma din region, lagringskonto och molntjänst som ska användas för resten av självstudien.

1. Bestäm vilket datacenter du vill använda för att vara värd för din virtuella SQL Server-dator. Följande PowerShell-kommando visar en lista över tillgängliga regionnamn.

   ```powershell
   (Get-AzureLocation).Name
   ```

2. När du har identifierat önskad plats anger du en variabel med namnet **$dcLocation** till den regionen. Följande kommando anger till exempel regionen till "Östra USA":

   ```powershell
   $dcLocation = "East US"
   ```

## <a name="set-your-subscription-and-storage-account"></a>Ange prenumeration och lagringskonto

1. Bestäm den Azure-prenumeration som du ska använda för den nya virtuella datorn.

   ```powershell
   (Get-AzureSubscription).SubscriptionName
   ```

2. Tilldela din Azure-prenumeration för mål till **variabeln $subscr.** Ange sedan detta som din nuvarande Azure-prenumeration.

   ```powershell
   $subscr="<subscription name>"
   Select-AzureSubscription -SubscriptionName $subscr –Current
   ```

3. Kontrollera sedan om det finns befintliga lagringskonton. Följande skript visar alla lagringskonton som finns i den valda regionen:

   ```powershell
   (Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName
   ```

   > [!NOTE]
   > Om du behöver ett nytt lagringskonto skapar du först ett all-lower-case-lagringskontonamn med kommandot New-AzureStorageAccount som i följande exempel:`New-AzureStorageAccount -StorageAccountName "<storage account name>" -Location $dcLocation`

4. Tilldela namnet på mållagringskontot till **$staccount**. Använd sedan **Set-AzureSubscription** för att ange prenumerations- och aktuellt lagringskonto.

   ```powershell
   $staccount="<storage account name>"
   Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount
   ```

## <a name="select-a-sql-server-virtual-machine-image"></a>Välj en virtuell SQL Server-avbildning

1. Ta reda på listan över tillgängliga sql server-avbildningar från galleriet. Dessa bilder har alla en **ImageFamily** egenskap som börjar med "SQL". I följande fråga visas den avbildningsfamilj som är tillgänglig för dig som har SQL Server förinstallerad.

   ```powershell
   Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily
   ```

2. När du hittar bildfamiljen för den virtuella datorn kan det finnas flera publicerade bilder i den här familjen. Använd följande skript för att hitta det senast publicerade avbildningsnamnet för den virtuella datorn för den valda avbildningsfamiljen (till exempel **SQL Server 2016 RTM Enterprise på Windows Server 2012 R2):**

   ```powershell
   $family="<ImageFamily value>"
   $image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

   echo "Selected SQL Server image name:"
   echo "   $image"
   ```

## <a name="create-the-virtual-machine"></a>Skapa den virtuella datorn

Skapa slutligen den virtuella datorn med PowerShell:

1. Skapa en molntjänst som är värd för den nya virtuella datorn. Observera att det också är möjligt att använda en befintlig molntjänst i stället. Skapa en ny variabel **$svcname** med molntjänstens korta namn.

   ```powershell
   $svcname = "<cloud service name>"
   New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation
   ```

2. Ange namnet på den virtuella datorn och en storlek. Mer information om storlekar på virtuella datorer finns i [Storlekar för virtuella datorer för Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

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
> Mer information och konfigurationsalternativ finns i avsnittet **Skapa kommandouppsättningen** i [Använd Azure PowerShell för att skapa och förkonfigurera Windows-baserade virtuella datorer](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="example-powershell-script"></a>Exempel på PowerShell-skript

Följande skript är ett exempel på ett fullständigt skript som skapar ett **VIRTUELLT SQL Server 2016 RTM Enterprise på Windows Server 2012 R2** virtuell dator. Om du använder det här skriptet måste du anpassa de första variablerna baserat på föregående steg i det här avsnittet.

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

## <a name="connect-with-remote-desktop"></a>Anslut med fjärrskrivbord

1. Skapa RDP-filerna i den aktuella användarens dokumentmapp för att starta dessa virtuella datorer för att slutföra installationen:

   ```powershell
   $documentspath = [environment]::getfolderpath("mydocuments")
   Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"
   ```

2. Starta RDP-filen i dokumentkatalogen. Anslut med administratörens användarnamn och lösenord som angavs tidigare (om ditt användarnamn till exempel var VMAdmin anger du \VMAdmin" som användare och anger lösenordet).

   ```powershell
   cd $documentspath
   .\vm1.rdp
   ```

## <a name="complete-the-configuration-of-the-sql-server-machine-for-remote-access"></a>Slutför konfigurationen av SQL Server-datorn för fjärråtkomst

När du har loggat in på datorn med fjärrskrivbord konfigurerar du SQL Server baserat på instruktionerna i [Steg för att konfigurera SQL Server-anslutning i en Virtuell Azure.](virtual-machines-windows-classic-sql-connect.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)

## <a name="next-steps"></a>Nästa steg

Du kan hitta ytterligare instruktioner för etablering av virtuella datorer med PowerShell i [dokumentationen för virtuella datorer](../classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

I många fall är nästa steg att migrera dina databaser till den nya virtuella datorn för SQL Server. Vägledning för databasmigrering finns i [Migrera en databas till SQL Server på en Azure VM](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json).

Om du också är intresserad av att använda Azure-portalen för att skapa virtuella SQL-datorer läser [du Etablera en virtuell SQL Server-dator på Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md). Observera att självstudien som går igenom portalen skapar virtuella datorer med den rekommenderade Resource Manager-modellen i stället för den klassiska modellen som används i det här PowerShell-avsnittet.

Utöver dessa resurser rekommenderar vi att du granskar [andra avsnitt som rör att köra SQL Server i Virtuella Azure-datorer](../sql/virtual-machines-windows-sql-server-iaas-overview.md).
