---
title: Kontroll av underhåll
description: Lär dig hur du styr när underhåll tillämpas på dina virtuella Azure-datorer med hjälp av underhållskontroll.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: cynthn
ms.openlocfilehash: 58c0964d170f49066802b955f09dab01eaf998a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250184"
---
# <a name="preview-control-updates-with-maintenance-control-and-the-azure-cli"></a>Förhandsversion: Styra uppdateringar med underhållskontroll och Azure CLI

Hantera plattformsuppdateringar, som inte kräver en omstart, med hjälp av underhållskontroll. Azure uppdaterar ofta sin infrastruktur för att förbättra tillförlitlighet, prestanda, säkerhet eller starta nya funktioner. De flesta uppdateringar är transparenta för användarna. Vissa känsliga arbetsbelastningar, som spel, mediestreaming och ekonomiska transaktioner, kan inte tolerera ens några sekunder av en vm-frysning eller frånkoppling för underhåll. Underhållskontroll ger dig möjlighet att vänta på plattformsuppdateringar och tillämpa dem inom ett rullande 35-dagars fönster. 

Med underhållskontrollen kan du bestämma när du ska installera uppdateringar på dina isolerade virtuella datorer och Azure Dediced Hosts.

Med underhållskontroll kan du:
- Batchuppdateringar till ett uppdateringspaket.
- Vänta upp till 35 dagar med att installera uppdateringar. 
- Automatisera plattformsuppdateringar för ditt underhållsfönster med Azure Functions.
- Underhållskonfigurationer fungerar över prenumerationer och resursgrupper. 

> [!IMPORTANT]
> Underhållskontroll är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="limitations"></a>Begränsningar

- Virtuella datorer måste finnas på en [dedikerad värd](./linux/dedicated-hosts.md)eller skapas med en [isolerad VM-storlek](./linux/isolation.md).
- Efter 35 dagar tillämpas en uppdatering automatiskt.
- Användaren måste ha åtkomst till **resursdeltagare.**


## <a name="install-the-maintenance-extension"></a>Installera underhållstillägget

Om du väljer att installera [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) lokalt behöver du version 2.0.76 eller senare.

Installera `maintenance` cli-tillägget lokalt eller i Cloud Shell. 

```azurecli-interactive
az extension add -n maintenance
```


## <a name="create-a-maintenance-configuration"></a>Skapa en underhållskonfiguration

Används `az maintenance configuration create` för att skapa en underhållskonfiguration. I det här exemplet skapas en underhållskonfiguration med namnet *myConfig-begränsad* till värden. 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --name myConfig \
   --maintenanceScope host\
   --location  eastus
```

Kopiera konfigurations-ID:et från utdata som ska användas senare.

Med `--maintenanceScope host` hjälp säkerställer att underhållskonfigurationen används för att kontrollera uppdateringar av värden.

Om du försöker skapa en konfiguration med samma namn, men på en annan plats, får du ett felmeddelande. Konfigurationsnamnen måste vara unika för din prenumeration.

Du kan fråga efter tillgängliga `az maintenance configuration list`underhållskonfigurationer med .

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## <a name="assign-the-configuration"></a>Tilldela konfigurationen

Används `az maintenance assignment create` för att tilldela konfigurationen till din isolerade virtuella dator eller Azure Dedikerad värd.

### <a name="isolated-vm"></a>Isolerad virtuell dator

Tillämpa konfigurationen på en virtuell dator med konfigurationens ID. Ange `--resource-type virtualMachines` och ange namnet på `--resource-name`den virtuella datorn för och `--resource-group`resursgruppen för den `--location`virtuella datorn i och platsen för den virtuella datorn för . 

```azurecli-interactive
az maintenance assignment create \
   --resource-group myMaintenanceRG \
   --location eastus \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myMaintenanceRG/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig"
```

### <a name="dedicated-host"></a>Dedikerad värd

Om du vill använda en konfiguration på `--resource-type hosts`en `--resource-parent-name` dedikerad värd måste `--resource-parent-type hostGroups`du inkludera , med namnet på värdgruppen och . 

Parametern `--resource-id` är id för värden. Du kan använda [az vm-värd get-instance-view](/cli/azure/vm/host#az-vm-host-get-instance-view) för att få ID för din dedikerade värd.

```azurecli-interactive
az maintenance assignment create \
   -g myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myDhResourceGroup/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig" \
   -l eastus \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
```

## <a name="check-configuration"></a>Kontrollera konfigurationen

Du kan kontrollera att konfigurationen har tillämpats korrekt eller `az maintenance assignment list`kontrollera vilken konfiguration som används för tillfället med .

### <a name="isolated-vm"></a>Isolerad virtuell dator

```azurecli-interactive
az maintenance assignment list \
   --provider-name Microsoft.Compute \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --query "[].{resource:resourceGroup, configName:name}" \
   --output table
```

### <a name="dedicated-host"></a>Dedikerad värd 

```azurecli-interactive
az maintenance assignment list \
   --resource-group myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
   --query "[].{ResourceGroup:resourceGroup,configName:name}" \
   -o table
```


## <a name="check-for-pending-updates"></a>Sök efter väntande uppdateringar

Används `az maintenance update list` för att se om det finns väntande uppdateringar. Uppdatera --prenumeration för att vara ID för prenumerationen som innehåller den virtuella datorn.

Om det inte finns några uppdateringar returneras ett felmeddelande som `Resource not found...StatusCode: 404`innehåller texten: .

Om det finns uppdateringar returneras bara en, även om det finns flera väntande uppdateringar. Data för den här uppdateringen returneras i ett objekt:

```text
[
  {
    "impactDurationInSec": 9,
    "impactType": "Freeze",
    "maintenanceScope": "Host",
    "notBefore": "2020-03-03T07:23:04.905538+00:00",
    "resourceId": "/subscriptions/9120c5ff-e78e-4bd0-b29f-75c19cadd078/resourcegroups/DemoRG/providers/Microsoft.Compute/hostGroups/demoHostGroup/hosts/myHost",
    "status": "Pending"
  }
]
  ```

### <a name="isolated-vm"></a>Isolerad virtuell dator

Sök efter väntande uppdateringar för en isolerad virtuell dator. I det här exemplet formateras utdata som en tabell för läsbarhet.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>Dedikerad värd

Så här söker du efter väntande uppdateringar för en dedikerad värd. I det här exemplet formateras utdata som en tabell för läsbarhet. Ersätt värdena för resurserna med dina egna.

```azurecli-interactive
az maintenance update list \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parentname myHostGroup \
   --resource-parent-type hostGroups \
   -o table
```

## <a name="apply-updates"></a>Tillämpa uppdateringar

Används `az maintenance apply update` för att tillämpa väntande uppdateringar. När det här kommandot lyckas returneras JSON som innehåller information om uppdateringen.

### <a name="isolated-vm"></a>Isolerad virtuell dator

Skapa en begäran om att installera uppdateringar på en isolerad virtuell dator.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>Dedikerad värd

Installera uppdateringar för en dedikerad värd.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## <a name="check-the-status-of-applying-updates"></a>Kontrollera status för att tillämpa uppdateringar 

Du kan kontrollera hur uppdateringarna `az maintenance applyupdate get`fortskrider med . 

Du kan `default` använda som uppdateringsnamn för att se `myUpdateName` resultatet för den senaste uppdateringen eller `az maintenance applyupdate create`ersätta med namnet på uppdateringen som returnerades när du körde .

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime kommer att vara den tidpunkt då uppdateringen blev klar, antingen initieras av dig eller av plattformen om självunderhåll fönster inte användes. Om det aldrig har varit en uppdatering som tillämpas via underhållskontroll kommer det att visa standardvärdet.

### <a name="isolated-vm"></a>Isolerad virtuell dator

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name default 
```

### <a name="dedicated-host"></a>Dedikerad värd

```azurecli-interactive
az maintenance applyupdate get \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \ 
   --resource-group myMaintenanceRG \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \ 
   --resource-parent-type hostGroups \
   --apply-update-name myUpdateName \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## <a name="delete-a-maintenance-configuration"></a>Ta bort en underhållskonfiguration

Används `az maintenance configuration delete` för att ta bort en underhållskonfiguration. Om du tar bort konfigurationen tas underhållskontrollen bort från de associerade resurserna.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## <a name="next-steps"></a>Nästa steg
Mer information finns i [Underhåll och uppdateringar](maintenance-and-updates.md).
