---
title: Kontroll av underhåll
description: Lär dig hur du styr när underhåll tillämpas på dina virtuella Azure-datorer med underhålls kontroll.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 11/21/2019
ms.author: cynthn
ms.openlocfilehash: cf4c8c57121a06a607770470c5aef69c5edc0c5f
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2020
ms.locfileid: "77115634"
---
# <a name="preview-control-updates-with-maintenance-control-and-the-azure-cli"></a>För hands version: kontrol lera uppdateringar med underhålls kontroll och Azure CLI

Hantera plattforms uppdateringar, som inte kräver omstart, med hjälp av underhålls kontroll. Azure uppdaterar ofta sin infrastruktur för att förbättra tillförlitligheten, prestandan, säkerheten eller lansera nya funktioner. De flesta uppdateringar är transparenta för användarna. Vissa känsliga arbets belastningar, t. ex. spel, medie direkt uppspelning och ekonomiska transaktioner, kan inte tolerera ens några sekunder av en virtuell dator som fryser eller kopplar från för underhåll. Med underhålls kontrollen får du möjlighet att vänta på plattforms uppdateringar och tillämpa dem i ett rullande 35-dagars fönster. 

Med underhålls kontrollen kan du bestämma när du ska tillämpa uppdateringar på dina isolerade virtuella datorer och Azure-dedikerade värdar.

Med underhålls kontroll kan du:
- Batch-uppdateringar till ett uppdaterings paket.
- Vänta upp till 35 dagar för att installera uppdateringar. 
- Automatisera plattforms uppdateringar för underhålls perioden med Azure Functions.
- Underhålls konfigurationer fungerar mellan prenumerationer och resurs grupper. 

> [!IMPORTANT]
> Underhålls kontrollen är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="limitations"></a>Begränsningar

- Virtuella datorer måste finnas på en [dedikerad värd](./linux/dedicated-hosts.md)eller skapas med en [isolerad VM-storlek](./linux/isolation.md).
- Efter 35 dagar tillämpas en uppdatering automatiskt.
- Användaren måste ha åtkomst till **resurs deltagare** .


## <a name="install-the-maintenance-extension"></a>Installera underhålls tillägget

Om du väljer att installera [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) lokalt måste du ha version 2.0.76 eller senare.

Installera `maintenance` Preview CLI-tillägget lokalt eller i Cloud Shell. 

```azurecli-interactive
az extension add -n maintenance
```


## <a name="create-a-maintenance-configuration"></a>Skapa en underhålls konfiguration

Använd `az maintenance configuration create` för att skapa en underhålls konfiguration. I det här exemplet skapas en underhålls konfiguration med namnet *unconfig* som är begränsad till värden. 

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

Kopiera konfigurations-ID: t från de utdata som ska användas senare.

Med `--maintenanceScope host` ser du till att underhålls konfigurationen används för att styra uppdateringar av värden.

Om du försöker skapa en konfiguration med samma namn, men på en annan plats, får du ett fel meddelande. Konfigurations namn måste vara unika för din prenumeration.

Du kan fråga efter tillgängliga underhålls konfigurationer med `az maintenance configuration list`.

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

## <a name="assign-the-configuration"></a>Tilldela konfigurationen

Använd `az maintenance assignment create` för att tilldela konfigurationen till den isolerade virtuella datorn eller den dedikerade Azure-värden.

### <a name="isolated-vm"></a>Isolerad virtuell dator

Tillämpa konfigurationen på en virtuell dator med hjälp av konfigurationens ID. Ange `--resource-type virtualMachines` och ange namnet på den virtuella datorn för `--resource-name`och resurs gruppen för till den virtuella datorn i `--resource-group`och platsen för den virtuella datorn för `--location`. 

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

Om du vill tillämpa en konfiguration på en dedikerad värd måste du inkludera `--resource-type hosts``--resource-parent-name` med namnet på värd gruppen och `--resource-parent-type hostGroups`. 

Parametern `--resource-id` är värdens ID. Du kan använda [AZ VM Host get-instance-View](/cli/azure/vm/host#az-vm-host-get-instance-view) för att hämta ID för den dedikerade värden.

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

## <a name="check-configuration"></a>Kontrol lera konfigurationen

Du kan kontrol lera att konfigurationen har tillämpats korrekt eller kontrol lera vilken konfiguration som för närvarande tillämpas med hjälp av `az maintenance assignment list`.

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

Använd `az maintenance update list` för att se om det finns väntande uppdateringar. Uppdatera--prenumerationen är ID för den prenumeration som innehåller den virtuella datorn.

Om det inte finns några uppdateringar kommer kommandot att returnera ett fel meddelande som innehåller texten: `Resource not found...StatusCode: 404`.

Om det finns uppdateringar returneras bara en av dem, även om det finns flera väntande uppdateringar. Data för den här uppdateringen kommer att returneras i ett objekt:

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

För att söka efter väntande uppdateringar för en dedikerad värd. I det här exemplet formateras utdata som en tabell för läsbarhet. Ersätt värdena för resurserna med dina egna.

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

Använd `az maintenance apply update` för att tillämpa väntande uppdateringar. Vid lyckad kommer det här kommandot returnera JSON som innehåller information om uppdateringen.

### <a name="isolated-vm"></a>Isolerad virtuell dator

Skapa en begäran om att tillämpa uppdateringar på en isolerad virtuell dator.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>Dedikerad värd

Tillämpa uppdateringar på en dedikerad värd.

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

## <a name="check-the-status-of-applying-updates"></a>Kontrol lera status för att tillämpa uppdateringar 

Du kan kontrol lera förloppet för uppdateringarna med hjälp av `az maintenance applyupdate get`. 

Du kan använda `default` som uppdaterings namn för att se resultat för den senaste uppdateringen eller ersätta `myUpdateName` med namnet på den uppdatering som returnerades när du körde `az maintenance applyupdate create`.

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


## <a name="delete-a-maintenance-configuration"></a>Ta bort en underhålls konfiguration

Använd `az maintenance configuration delete` för att ta bort en underhålls konfiguration. Om du tar bort konfigurationen tas underhålls kontrollen bort från de tillhör ande resurserna.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --name myConfig
```

## <a name="next-steps"></a>Nästa steg
Mer information finns i [underhåll och uppdateringar](maintenance-and-updates.md).
