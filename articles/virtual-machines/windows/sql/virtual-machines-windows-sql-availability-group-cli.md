---
title: Konfigurera en tillgänglighetsgrupp (Azure CLI)
description: Använd Azure CLI för att skapa Windows redundanskluster, tillgänglighetsgruppavlyssnare och intern belastningsutjämnare på en VIRTUELL SQL Server i Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: a6600af353daf2bfa7b49196f48ba5b60e6c45fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022369"
---
# <a name="use-the-azure-cli-to-configure-an-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Använda Azure CLI för att konfigurera en alltid på tillgänglighetsgrupp för SQL Server på en Virtuell Azure-dator
I den hÃ¤r artikeln beskrivs hur du anvÃ¤nder [Azure CLI fÃ¶r](/cli/azure/sql/vm?view=azure-cli-latest/) att distribuera ett Windows failover-kluster, Ã¤r till SQL Server-virtuella datorer i klustret och skapar den interna belastningsutjämnaren och lyssnaren fÃ¶r en always on-availability-grupp. Distributionen av gruppen Alltid på tillgänglighet sker fortfarande manuellt via SQL Server Management Studio (SSMS). 

## <a name="prerequisites"></a>Krav
Om du vill automatisera inställningarna för en alltid på tillgänglighetsgrupp med hjälp av Azure CLI måste du ha följande förutsättningar: 
- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- En resursgrupp med en domänkontrollant. 
- En eller flera domänanslutna [virtuella datorer i Azure som kör SQL Server 2016 (eller senare) Enterprise Edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) i samma *tillgänglighetsuppsättning eller olika tillgänglighetszoner* som har [registrerats hos SQL VM-resursprovidern](virtual-machines-windows-sql-register-with-resource-provider.md).  
- [Azure CLI](/cli/azure/install-azure-cli). 
- Ip-adresser med två tillgängliga (används inte av någon enhet). En är för den interna belastningsutjämnaren. Den andra är för tillgänglighetsgruppavlyssnaren inom samma undernät som tillgänglighetsgruppen. Om du använder en befintlig belastningsutjämnare behöver du bara en tillgänglig IP-adress för tillgänglighetsgruppens lyssnare. 

## <a name="permissions"></a>Behörigheter
Du behöver följande kontobehörigheter för att konfigurera gruppen Alltid på tillgänglighet med hjälp av Azure CLI: 

- Ett befintligt domänanvändarkonto som har **behörigheten Skapa datorobjekt** i domänen. Ett domänadministratörskonto har till exempel vanligtvis account@domain.comtillräcklig behörighet (till exempel: ). _Det här kontot bör också ingå i den lokala administratörsgruppen på varje virtuell dator för att skapa klustret._
- Domänanvändarkontot som styr SQL Server-tjänsten. 
 
## <a name="step-1-create-a-storage-account-as-a-cloud-witness"></a>Steg 1: Skapa ett lagringskonto som ett molnvittne
Klustret behöver ett lagringskonto för att fungera som molnvittne. Du kan använda ett befintligt lagringskonto eller skapa ett nytt lagringskonto. Om du vill använda ett befintligt lagringskonto går du vidare till nästa avsnitt. 

Följande kodavsnitt skapar lagringskontot: 
```azurecli-interactive
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

>[!TIP]
> Du kan se `az sql: 'vm' is not in the 'az sql' command group` felet om du använder en inaktuell version av Azure CLI. Hämta den [senaste versionen av Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) för att komma förbi det här felet.

## <a name="step-2-define-windows-failover-cluster-metadata"></a>Steg 2: Definiera metadata för Windows-redundanskluster
Kommandogruppen Azure CLI [az sql vm-grupp](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) hanterar metadata för WSFC-tjänsten (Windows Server Redundans kluster) som är värd för tillgänglighetsgruppen. Klustermetadata innehåller Active Directory-domänen, klusterkonton, lagringskonton som ska användas som molnvittne och SQL Server-version. Använd [az sql vm-gruppskap för](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) att definiera metadata för WSFC så att när den första virtuella DATORN för SQL Server läggs till skapas klustret som definierat. 

Följande kodavsnitt definierar metadata för klustret:
```azurecli-interactive
# Define the cluster metadata
# example: az sql vm group create -n Cluster -l 'West US' -g SQLVM-RG `
#  --image-offer SQL2017-WS2016 --image-sku Enterprise --domain-fqdn domain.com `
#  --operator-acc vmadmin@domain.com --bootstrap-acc vmadmin@domain.com --service-acc sqlservice@domain.com `
#  --sa-key '4Z4/i1Dn8/bpbseyWX' `
#  --storage-account 'https://cloudwitness.blob.core.windows.net/'

az sql vm group create -n <cluster name> -l <region ex:eastus> -g <resource group name> `
  --image-offer <SQL2016-WS2016 or SQL2017-WS2016> --image-sku Enterprise --domain-fqdn <FQDN ex: domain.com> `
  --operator-acc <domain account ex: testop@domain.com> --bootstrap-acc <domain account ex:bootacc@domain.com> `
  --service-acc <service account ex: testservice@domain.com> `
  --sa-key '<PublicKey>' `
  --storage-account '<ex:https://cloudwitness.blob.core.windows.net/>'
```

## <a name="step-3-add-sql-server-vms-to-the-cluster"></a>Steg 3: Lägga till virtuella SQL Server-datorer i klustret
Om du lägger till den första virtuella datorn för SQL Server i klustret skapas klustret. Kommandot [az sql vm-tillägg till grupp](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) skapar klustret med det namn som tidigare angetts, installerar klusterrollen på virtuella SQL Server-datorer och lägger till dem i klustret. Efterföljande användning `az sql vm add-to-group` av kommandot lägga till fler SQL Server virtuella datorer till den nyligen skapade klustret. 

Följande kodavsnitt skapar klustret och lägger till den första virtuella datorn för SQL Server i det: 

```azurecli-interactive
# Add SQL Server VMs to cluster
# example: az sql vm add-to-group -n SQLVM1 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!
# example: az sql vm add-to-group -n SQLVM2 -g SQLVM-RG --sqlvm-group Cluster `
#  -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

az sql vm add-to-group -n <VM1 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
az sql vm add-to-group -n <VM2 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
  -b <bootstrap account password> -p <operator account password> -s <service account password>
```
Använd det här kommandot om du vill lägga till andra virtuella SQL Server-datorer i klustret. Ändra bara `-n` parametern för SQL Server VM-namnet. 

## <a name="step-4-create-the-availability-group"></a>Steg 4: Skapa tillgänglighetsgruppen
Skapa tillgänglighetsgruppen manuellt som vanligt genom att använda [SQL Server Management Studio,](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio) [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell)eller [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

>[!IMPORTANT]
> Skapa *inte* en lyssnare just nu eftersom detta görs via Azure CLI i följande avsnitt.  

## <a name="step-5-create-the-internal-load-balancer"></a>Steg 5: Skapa den interna belastningsutjämnaren

Gruppen Alltid på tillgänglighet kräver en intern instans av Azure Load Balancer. Den interna belastningsutjämnaren tillhandahåller en "flytande" IP-adress för tillgänglighetsgruppens lyssnare som möjliggör snabbare redundans och återanslutning. Om virtuella SQL Server-datorer i en tillgänglighetsgrupp ingår i samma tillgänglighetsuppsättning kan du använda en grundläggande belastningsutjämnare. Annars måste du använda en standardbelastningsutjämnare.  

> [!NOTE]
> Den interna belastningsutjämnaren bör finnas i samma virtuella nätverk som SQL Server VM-instanserna. 

Följande kodavsnitt skapar den interna belastningsutjämnaren:

```azurecli-interactive
# Create the internal load balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

>[!IMPORTANT]
> Den offentliga IP-resursen för varje VIRTUELL SQL Server bör ha en Standard SKU för att vara kompatibel med standardbelastningsutjämnaren. Om du vill ta reda på SKU för den virtuella datorns offentliga IP-resurs går du till **Resursgrupp,** väljer din **offentliga IP-adressresurs** för önskad VIRTUELL SQL Server och letar reda på värdet under **SKU** i **fönstret Översikt.**  

## <a name="step-6-create-the-availability-group-listener"></a>Steg 6: Skapa tillgänglighetsgrupplyssnare
När du har skapat tillgänglighetsgruppen manuellt kan du skapa lyssnaren med az [sql vm ag-listener](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create). 

*Undernätsresurs-ID* är `/subnets/<subnetname>` värdet för bifogat till resurs-ID för den virtuella nätverksresursen. Så här identifierar du undernätsresurs-ID:
   1. Gå till din resursgrupp i [Azure-portalen](https://portal.azure.com). 
   1. Välj den virtuella nätverksresursen. 
   1. Välj **Egenskaper** i fönstret **Inställningar.** 
   1. Identifiera resurs-ID:et `/subnets/<subnetname>` för det virtuella nätverket och lägg till i slutet av det för att skapa undernätsresurs-ID. Ett exempel:
      - Ditt virtuella nätverksresurs-ID är:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
      - Ditt undernätsnamn är:`default`
      - Därför är ditt nätresurs-ID:`/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


Följande kodavsnitt skapar tillgänglighetsgrupplyssnaren:

```azurecli-interactive
# Create the availability group listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas, ex: sqlvm1 sqlvm2>
```

## <a name="modify-the-number-of-replicas-in-an-availability-group"></a>Ändra antalet repliker i en tillgänglighetsgrupp
Det finns ett extra komplext lager när du distribuerar en tillgänglighetsgrupp till virtuella SQL Server-datorer som finns i Azure. Resursprovidern och den virtuella datorn-gruppen hanterar nu resurserna. När du lägger till eller tar bort repliker i tillgänglighetsgruppen finns det därför ytterligare ett steg för att uppdatera lyssnarens metadata med information om virtuella SQL Server-datorer. När du ändrar antalet repliker i tillgänglighetsgruppen måste du också använda kommandot [az sql vm-gruppen ag-listener](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) för att uppdatera lyssnaren med metadata för virtuella SQL Server-datorer. 


### <a name="add-a-replica"></a>Lägga till en replik

Så här lägger du till en ny replik i tillgänglighetsgruppen:

1. Lägg till virtuell SQL Server i klustret:
   ```azurecli-interactive
   # Add the SQL Server VM to the cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. Använd SQL Server Management Studio för att lägga till SQL Server-instansen som en replik i tillgänglighetsgruppen.
1. Lägg till SQL Server VM-metadata till lyssnaren:
   ```azurecli-interactive
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>Ta bort en replik

Så här tar du bort en replik från tillgänglighetsgruppen:

1. Ta bort repliken från tillgänglighetsgruppen med hjälp av SQL Server Management Studio. 
1. Ta bort SQL Server VM-metadata från lyssnaren:
   ```azurecli-interactive
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Ta bort den virtuella datorn för SQL Server från klustret:
   ```azurecli-interactive
   # Remove the SQL VM from the cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-the-availability-group-listener"></a>Ta bort tillgänglighetsgrupplyssnaren
Om du senare behöver ta bort tillgänglighetsgruppavlyssnaren som konfigurerats med Azure CLI måste du gå igenom SQL VM-resursprovidern. Eftersom lyssnaren är registrerad via SQL VM-resursprovidern är det inte tillräckligt att bara ta bort den via SQL Server Management Studio. 

Den bästa metoden är att ta bort den via SQL VM-resursprovidern med hjälp av följande kodavsnitt i Azure CLI. Om du gör det tas metadata för tillgänglighetsgruppen lyssnare från SQL VM-resursprovidern. Lyssnaren tas också bort fysiskt från tillgänglighetsgruppen. 

```azurecli-interactive
# Remove the availability group listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över virtuella SQL Server-datorer](virtual-machines-windows-sql-server-iaas-overview.md)
* [Vanliga frågor och svar om virtuella SQL Server-datorer](virtual-machines-windows-sql-server-iaas-faq.md)
* [Viktig information för virtuella SQL Server-datorer](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Byta licensieringsmodeller för en VIRTUELL SQL Server](virtual-machines-windows-sql-ahb.md)
* [Översikt över alltid på tillgänglighetsgrupper &#40;SQL Server-&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Konfiguration av en serverinstans för Alltid på tillgänglighetsgrupper &#40;SQL Server&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Administration av en tillgänglighetsgrupp &#40;SQL Server-&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Övervakning av tillgänglighetsgrupper &#40;SQL Server-&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Översikt över Transact-SQL-uttryck för alltid på tillgänglighetsgrupper &#40;SQL Server-&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Översikt över PowerShell-cmdletar för alltid på tillgänglighetsgrupper &#40;SQL Server-&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
