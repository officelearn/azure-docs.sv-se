---
title: Använda Azure SQL-dator med CLI för att konfigurera Always On-tillgänglighetsgrupp för SQL Server på en Azure VM
description: 'Använda Azure CLI för att skapa Windows-redundanskluster, tillgänglighetsgruppens lyssnare och den interna belastningsutjämnaren på en SQL Server-dator i Azure. '
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 02/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 1c5c5f4c8125f801edc89d47851871d8eb06a2f9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58762879"
---
# <a name="use-azure-sql-vm-cli-to-configure-always-on-availability-group-for-sql-server-on-an-azure-vm"></a>Använda Azure SQL-dator med CLI för att konfigurera Always On-tillgänglighetsgrupp för SQL Server på en Azure VM
Den här artikeln beskriver hur du använder [Azure SQL-dator med CLI](/cli/azure/sql/vm?view=azure-cli-latest/) att distribuera en Windows-redundanskluster (WSFC), och lägga till SQL Server-datorer i klustret, samt skapa den interna belastningsutjämnaren och lyssnare för en Always On-tillgänglighetsgrupp.  Verklig distribution av Always On availability-gruppen fortfarande göras manuellt via SQL Server Management Studio (SSMS). 

## <a name="prerequisites"></a>Nödvändiga komponenter
För att automatisera installationen av en Always On-tillgänglighetsgrupp med hjälp av Azure SQL-dator med CLI, måste du redan har följande krav: 
- En [Azure-prenumeration](https://azure.microsoft.com/free/).
- En resursgrupp med en domänkontrollant. 
- En eller flera domänanslutna [virtuella datorer i Azure som kör SQLServer 2016 (eller högre) Enterprise edition](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) i den *samma tillgänglighetsuppsättning eller olika tillgänglighetszoner* som har [registrerad med SQL VM-resursprovidern](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider).  
- [Azure CLI](/cli/azure/install-azure-cli). 
- Två tillgängliga (inte används av en person) IP-adresser, en för den interna belastningsutjämnaren och en för tillgänglighetsgruppens lyssnare i samma undernät som tillgänglighetsgruppen. Om en befintlig belastningsutjämnare används krävs bara en tillgänglig IP-adress för tillgänglighetsgruppens lyssnare. 

## <a name="permissions"></a>Behörigheter
De följande behörigheterna krävs för att konfigurera Always On availability-gruppen med hjälp av Azure SQL-dator med CLI. 

- Ett befintligt domänanvändarkonto som har behörighet att ”skapa datorobjekt' i domänen.  Till exempel ett domänadministratörskonto vanligtvis har tillräcklig behörighet (t.ex.: account@domain.com). _Detta konto bör också vara en del av den lokala administratörsgruppen på varje virtuell dator för att skapa klustret._
- Domänanvändarkonto som styr SQL Server-tjänsten. 
 
## <a name="step-1---create-storage-account-as-a-cloud-witness"></a>Steg 1 – Skapa storage-konto som ett molnvittne
Klustret måste ha ett lagringskonto som fungerar som molnvittnet. Du kan använda alla befintliga lagringskonton eller du kan skapa ett nytt lagringskonto. Om du vill använda ett befintligt lagringskonto kan du gå vidare till nästa avsnitt. 

Följande kodfragment skapar lagringskontot: 
```azurecli
# Create the storage account
# example: az storage account create -n 'cloudwitness' -g SQLVM-RG -l 'West US' `
#  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true

az storage account create -n <name> -g <resource group name> -l <region ex:eastus> `
  --sku Standard_LRS --kind StorageV2 --access-tier Hot --https-only true
```

   >[!TIP]
   > Du kan se felet `az sql: 'vm' is not in the 'az sql' command group` om du använder en inaktuell version av Azure CLI. Ladda ned den [senaste versionen av Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest) att komma förbi det här felet.

## <a name="step-2---define-windows-failover-cluster-metadata"></a>Steg 2 – definiera Metadata för Windows-redundanskluster
Azure SQL VM CLI [az sql vm grupp](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest) kommandot gruppen hanterar metadata för tjänsten Windows-redundanskluster (WSFC) som är värd för tillgänglighetsgruppen. Klustermetadata innehåller AD-domän, kluster-konton, storage-konton som ska användas som molnvittne och SQL Server-version. Använd [az sql vm gruppen skapa](https://docs.microsoft.com/cli/azure/sql/vm/group?view=azure-cli-latest#az-sql-vm-group-create) för att definiera metadata för WSFC så att när den första SQL Server-VM läggs, klustret har skapats som har definierats. 

Följande kodavsnitt definierar metadata för klustret:
```azurecli
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

## <a name="step-3---add-sql-server-vms-to-cluster"></a>Steg 3 – lägga till SQL Server-datorer i klustret
Lägga till den första SQL Server-dator i klustret skapar klustret. Den [az sql vm Lägg till-till-grupp](https://docs.microsoft.com/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-add-to-group) kommandot klustret skapas med det namn du angav tidigare, installerar klusterrollen på SQL Server-datorer och lägger till dem i klustret. Efterföljande användningar av den `az sql vm add-to-group` kommando lägger till ytterligare SQL Server-datorer till det nya klustret. 

Följande kodfragment skapar klustret och lägger till den första SQL Server-dator: 

```azurecli
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
Använd kommandot för att lägga till några andra SQL Server-datorer i klustret, bara ändra den `-n` parameter för SQL Server-VM-namnet. 

## <a name="step-4---create-availability-group"></a>Steg 4 – skapa tillgänglighetsgrupp
Skapa tillgänglighetsgruppen manuellt som vanligt, med hjälp av antingen [SQL Server Management Studio](/sql/database-engine/availability-groups/windows/use-the-availability-group-wizard-sql-server-management-studio), [PowerShell](/sql/database-engine/availability-groups/windows/create-an-availability-group-sql-server-powershell), eller [Transact-SQL](/sql/database-engine/availability-groups/windows/create-an-availability-group-transact-sql). 

  >[!IMPORTANT]
  > Gör **inte** skapa en lyssnare just nu eftersom detta görs via Azure CLI i följande avsnitt.  

## <a name="step-5---create-internal-load-balancer"></a>Steg 5 – skapa en intern belastningsutjämnare

Always On (AG) tillgänglighetsgruppslyssnaren kräver en intern Azure Load Balancer (ILB). Den interna Belastningsutjämnaren ger en ”flytande” IP-adress för AG-lyssnare som möjliggör snabbare redundans och återanslutning. Om SQL Server-datorer i en tillgänglighetsgrupp är en del av samma tillgänglighetsuppsättning, kan du använda en grundläggande belastningsutjämnaren. Annars kan behöva du använda en Standard Load Balancer.  **Den interna Belastningsutjämnaren måste vara i samma virtuella nätverk som SQL Server-VM-instanser.** 

Följande kodfragment skapar den interna belastningsutjämnaren:

```azurecli
# Create the Internal Load Balancer
# example: az network lb create --name sqlILB -g SQLVM-RG --sku Standard `
# --vnet-name SQLVMvNet --subnet default

az network lb create --name sqlILB -g <resource group name> --sku Standard `
  --vnet-name <VNet Name> --subnet <subnet name>
```

  >[!IMPORTANT]
  > Den offentliga IP-adressresursen för varje SQL Server VM ska ha en standard-SKU att vara kompatibel med Standard Load Balancer. För att fastställa SKU för den Virtuella datorns offentliga IP-resurs, navigera till din **resursgrupp**väljer din **offentliga IP-adressen** resurs för den virtuella SQL Server-datorn, och leta upp värdet under **SKU**  av den **översikt** fönstret.  

## <a name="step-6---create-availability-group-listener"></a>Steg 6 – skapa tillgänglighetsgruppens lyssnare
När tillgänglighetsgruppen har skapats manuellt kan du skapa lyssnaren med [az sql vm ag-lyssnare](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-latest#az-sql-vm-group-ag-listener-create). 


- Den **undernätsresurs-ID** är värdet för `/subnets/<subnetname>` läggas till för resurs-ID för vNet-resurs. Du kan identifiera undernät resurs-ID genom att göra följande:
   1. Navigera till din resursgrupp i den [Azure-portalen](https://portal.azure.com). 
   1. Välj vNet-resurs. 
   1. Välj **egenskaper** i den **inställningar** fönstret. 
   1. Identifiera resurs-ID för det virtuella nätverket och lägger till `/subnets/<subnetname>`i slutet av det för att skapa undernät resurs-ID. Exempel:
        - Mitt vNet resurs-ID är: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet`
        - Min undernätets namn är `default`.
        - Därför är mitt undernät resurs-ID: `/subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default`


Följande kodfragment skapar tillgänglighetsgruppens lyssnare:

```azurecli
# Create the AG listener
# example: az sql vm group ag-listener create -n AGListener -g SQLVM-RG `
#  --ag-name SQLAG --group-name Cluster --ip-address 10.0.0.27 `
#  --load-balancer sqlilb --probe-port 59999  `
#  --subnet /subscriptions/a1a1-1a11a/resourceGroups/SQLVM-RG/providers/Microsoft.Network/virtualNetworks/SQLVMvNet/subnets/default `
#  --sqlvms sqlvm1 sqlvm2

az sql vm group ag-listener create -n <listener name> -g <resource group name> `
  --ag-name <availability group name> --group-name <cluster name> --ip-address <ag listener IP address> `
  --load-balancer <lbname> --probe-port <Load Balancer probe port, default 59999>  `
  --subnet <subnet resource id> `
  --sqlvms <names of SQL VM's hosting AG replicas ex: sqlvm1 sqlvm2>
```

## <a name="modify-number-of-replicas-in-availability-group"></a>Ändra antal repliker i tillgänglighetsgruppen
Det finns ytterligare ett lager med komplexiteten när du distribuerar en tillgänglighetsgrupp till SQL Server-datorer i Azure, eftersom resurser hanteras nu av resursprovidern och av den `virtual machine group`. Det innebär när du lägger till eller ta bort repliker i tillgänglighetsgruppen, finns det ytterligare ett steg för att uppdatera metadata för lyssnare med information om SQL Server-datorer. Därför när du ändrar antalet repliker i tillgänglighetsgruppen, måste du även använda den [az sql vm grupp ag-listener update](/cli/azure/sql/vm/group/ag-listener?view=azure-cli-2018-03-01-hybrid#az-sql-vm-group-ag-listener-update) kommando för att uppdatera lyssnaren med metadata för SQL Server-datorer. 


### <a name="add-a-replica"></a>Lägg till en replik

Om du vill lägga till en ny replik i tillgänglighetsgruppen, gör du följande:

1. Lägg till SQL Server-dator i klustret:
   ```azurecli
   # Add SQL Server VM to the Cluster
   # example: az sql vm add-to-group -n SQLVM3 -g SQLVM-RG --sqlvm-group Cluster `
   # -b Str0ngAzur3P@ssword! -p Str0ngAzur3P@ssword! -s Str0ngAzur3P@ssword!

   az sql vm add-to-group -n <VM3 Name> -g <Resource Group Name> --sqlvm-group <cluster name> `
   -b <bootstrap account password> -p <operator account password> -s <service account password>
   ```
1. Använda SQL Server Management Studio (SSMS) för att lägga till SQL Server-instansen som en replik i tillgänglighetsgruppen.
1. Lägg till SQL Server-VM-metadata i lyssnaren:
   ```azurecli
   # Update the listener metadata with the new VM
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2 sqlvm3

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs, along with new SQL VM>
   ```

### <a name="remove-a-replica"></a>Ta bort en replik

Om du vill ta bort en replik från tillgänglighetsgruppen, gör du följande:

1. Ta bort repliken från tillgänglighetsgruppen med SQL Server Management Studio (SSMS). 
1. Ta bort SQL Server-VM-metadata från lyssnaren:
   ```azurecli
   # Update the listener metadata by removing the VM from the SQLVMs list
   # example: az sql vm group ag-listener update -n AGListener `
   # -g sqlvm-rg --group-name Cluster --sqlvms sqlvm1 sqlvm2

   az sql vm group ag-listener update -n <Listener> `
   -g <RG name> --group-name <cluster name> --sqlvms <SQL VMs that remain>
   ```
1. Ta bort SQL Server-dator från klustret:
   ```azurecli
   # Remove SQL VM from cluster
   # example: az sql vm remove-from-group --name SQLVM3 --resource-group SQLVM-RG

   az sql vm remove-from-group --name <SQL VM name> --resource-group <RG name> 
   ```

## <a name="remove-availability-group-listener"></a>Ta bort tillgänglighetsgruppens lyssnare
Om du senare behöver ta bort tillgänglighetsgruppens lyssnare konfigurerats med Azure CLI, måste du gå igenom SQL VM-resursprovidern. Eftersom lyssnaren har registrerats via SQL VM-resursprovidern kan är bara tas bort via SQL Server Management Studio otillräcklig. Den ska faktiskt tas bort via SQL VM-resursprovidern med Azure CLI. Detta tar bort AG-lyssnare metadata från SQL VM-resursprovidern och tar bort fysiskt lyssnaren från tillgänglighetsgruppen. 

Följande kodavsnitt tar bort SQL tillgänglighetsgruppens lyssnare från både den SQL-resursprovidern och från tillgänglighetsgruppen: 

```azurecli
# Remove the AG listener
# example: az sql vm group ag-listener delete --group-name Cluster --name AGListener --resource-group SQLVM-RG

az sql vm group ag-listener delete --group-name <cluster name> --name <listener name > --resource-group <resource group name>
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar: 

* [Översikt över SQLServer-dator](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQLServer VM vanliga frågor och svar](virtual-machines-windows-sql-server-iaas-faq.md)
* [Viktig information om SQL Server-VM](virtual-machines-windows-sql-server-iaas-release-notes.md)
* [Växla licensieringsmodellerna för en SQL Server VM](virtual-machines-windows-sql-ahb.md)
* [Översikt över Always On-Tillgänglighetsgrupper &#40;SQLServer&#41;](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)   
* [Konfigurationen av en Server-instans för Always On-Tillgänglighetsgrupper &#40;SQLServer&#41;](/sql/database-engine/availability-groups/windows/configuration-of-a-server-instance-for-always-on-availability-groups-sql-server)   
* [Administration av en tillgänglighetsgrupp &#40;SQLServer&#41;](/sql/database-engine/availability-groups/windows/administration-of-an-availability-group-sql-server)   
* [Övervakning av Tillgänglighetsgrupper &#40;SQLServer&#41;](/sql/database-engine/availability-groups/windows/monitoring-of-availability-groups-sql-server)
* [Översikt över Transact-SQL-uttryck för Always On-Tillgänglighetsgrupper &#40;SQLServer&#41;](/sql/database-engine/availability-groups/windows/transact-sql-statements-for-always-on-availability-groups)   
* [Översikt över PowerShell-cmdletar för Always On-Tillgänglighetsgrupper &#40;SQLServer&#41;](/sql/database-engine/availability-groups/windows/overview-of-powershell-cmdlets-for-always-on-availability-groups-sql-server)  
