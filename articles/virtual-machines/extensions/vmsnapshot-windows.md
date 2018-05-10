---
title: Windows för ögonblicksbild av VM-tillägget för Azure Backup | Microsoft Docs
description: Ta programmet konsekvent säkerhetskopiering av den virtuella datorn från Azure Backup med hjälp av tillägget för VM-ögonblicksbild
services: backup, virtual-machines-windows
documentationcenter: ''
author: trinadhk
manager: jeconnoc
editor: ''
ms.assetid: 57759670-0baa-44db-ae14-8cdc00d3a906
ms.service: backup, virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 3/26/2018
ms.author: trinadhk
ms.openlocfilehash: 8426a2472a28cf287dfe574cb80da56108394ae8
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="vm-snapshot-windows-extension-for-azure-backup"></a>Windows för ögonblicksbild av VM-tillägget för Azure Backup

## <a name="overview"></a>Översikt

Azure-säkerhetskopiering har stöd för säkerhetskopiering av arbetsbelastningar från lokalt till molnet och säkerhetskopiering av molnresurser till Recovery Services-valvet. Azure Backup använder ögonblicksbilden tillägg för virtuell dator för att ta ett program konsekvent säkerhetskopiering av den virtuella Azure-datorn utan att behöva avstängning den virtuella datorn. VM-ögonblicksbild tillägget publiceras och stöds av Microsoft som en del av Azure Backup-tjänsten. Azure Backup installerar tillägget som en del av första schemalagda säkerhetskopiering utlösta efter att aktivera säkerhetskopiering. Det här dokumentet beskriver de plattformar som stöds, konfigurationer och distributionsalternativ för tillägg för VM-ögonblicksbild.

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem
En lista över operativsystem som stöds finns i [operativsystem som stöds av Azure Backup](../../backup/backup-azure-arm-vms-prepare.md#supported-operating-systems-for-backup)

### <a name="internet-connectivity"></a>Internetanslutning

VM-ögonblicksbild tillägget kräver att den virtuella måldatorn är ansluten till internet när vi gör en säkerhetskopia av den virtuella datorn.

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för snapshot-tillägg för virtuell dator. Tillägget kräver uppgifts-ID – detta anger säkerhetskopieringsjobbet som utlöste ögonblicksbilder på den virtuella datorn status blob-uri - där status för åtgärden ögonblicksbild skrivs Schemalagd starttid för ögonblicksbilden, loggar blob-uri - där loggar som motsvarar ögonblicksbild aktivitet skrivs, objstr-representation av Virtuella diskar och metadata.  Eftersom dessa inställningar ska behandlas som känsliga data, bör det lagras i en Inställningskonfiguration för skyddade. Azure för VM-tillägget skyddade inställningsdata krypteras och dekrypteras endast på den virtuella måldatorn. Observera att dessa inställningar rekommenderas för att skickas från Azure Backup-tjänsten som en del av säkerhetskopieringsjobb.

```json
{
  "type": "extensions",
  "name": "VMSnapshot",
  "location":"<myLocation>",
  "properties": {
    "publisher": "Microsoft.RecoveryServices",
    "type": "VMSnapshot",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "locale":"<location>",
      "taskId":"<taskId used by Azure Backup service to communicate with extension>",
      "commandToExecute": "snapshot",
      "commandStartTimeUTCTicks": "<scheduled start time of the snapshot task>",
      "vmType": "microsoft.compute/virtualmachines"
    },
    "protectedSettings": {
      "objectStr": "<blob SAS uri represenattion of VM sent by Azure Backup service to extension>",
      "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
      "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
    }
  }
}
```

### <a name="property-values"></a>Egenskapsvärden

| Namn | Värdet / exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | datum |
| taskId | e07354cf 041e-4370-929f-25a319ce8933_1 | sträng |
| commandStartTimeUTCTicks | 6.36458E + 17 | sträng |
| Nationella inställningar | en-us | sträng |
| objectStr | Kodning av sas uri matris-”blobSASUri”: [”https:\/\/sopattna5365.blob.core.windows.net\/virtuella hårddiskar\/vmwin1404ltsc201652903941.vhd? SA = 2014-02-14 & sr = b & sig = TywkROXL1zvhXcLujtCut8g3jTpgbE6JpSWRLZxAdtA % 3D & st = 2017-11-09T14% 3A23% 3A28Z & nvänd = 2017-11-09T17% 3A38% 3A28Z & sp = rw ””, https:\/\/sopattna8461.blob.core.windows.net\/virtuella hårddiskar\/vmwin1404ltsc-20160629-122418.vhd? SA = 2014-02-14 & sr = b & sig = 5S0A6YDWvVwqPAkzWXVy % 2BS % 2FqMwzFMbamT5upwx05v8Q % 3D & st = 2017-11-09T14% 3A23% 3A28Z & nvänd = 2017-11-09T17% 3A38% 3A28Z & sp = rw ””, https:\/ \/ sopattna8461.BLOB.Core.Windows.NET\/bootdiagnostics-vmwintu1-deb58392-ed5e-48be-9228-ff681b0cd3ee\/vmubuntu1404ltsc-20160629-122541.vhd? SA = 2014-02-14 & sr = b & sig = X0Me2djByksBBMVXMGIUrcycvhQSfjYvqKLeRA7nBD4% 3D & st = 2017-11-09T14% 3A23% 3A28Z & nvänd = 2017-11-09T17% 3A38% 3A28Z & sp = rw ””, https:\/\/sopattna5365.blob.core.windows.net\/virtuella hårddiskar\/vmwin1404ltsc-20160701-163922.vhd? SA = 2014-02-14 & sr = b & sig = oXvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r % 2BC % 2BNIAork % 3D & st = 2017-11-09T14% 3A23% 3A28Z & nvänd = 2017-11-09T17% 3A38% 3A28Z & sp = rw ””, https:\/ \/ sopattna5365.BLOB.Core.Windows.NET\/virtuella hårddiskar\/vmwin1404ltsc-20170705-124311.vhd? SA = 2014-02-14 & sr = b & sig = ZUM9d28Mvvm % 2FfrhJ71TFZh0Ni90m38bBs3zMl % 2FQ9rs0% 3D & st = 2017-11-09T14% 3A23% 3A28Z & Se = 2017-11-09T17% 3A38% 3A28Z & sp = rw ”] | sträng |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | sträng |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | sträng |



## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. Dock är det rekommenderade sättet att lägga till ett tillägg för VM-ögonblicksbild till en virtuell dator genom att aktivera säkerhetskopiering på den virtuella datorn. Detta kan ske via en Resource Manager-mall.  En Resource Manager-mall som gör det möjligt för säkerhetskopian på en virtuell dator kan hittas på den [Azure Quick Start-galleriet](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/).


## <a name="azure-cli-deployment"></a>Azure CLI-distribution

Azure CLI kan användas för att aktivera säkerhetskopiering på en virtuell dator. Post Aktivera säkerhetskopiering första schemalagda säkerhetskopieringsjobbet installerar Vm-ögonblicksbild tillägget på den virtuella datorn.

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>Felsöka och stöd

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för distributioner av tillägget kan hämtas från Azure-portalen och genom att använda Azure CLI. Om du vill se distributionsstatusen för tillägg för en viss virtuell dator, kör du följande kommando med hjälp av Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Tillägget utförande-utdatan loggas till följande fil:

```
C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot
```

### <a name="error-codes-and-their-meanings"></a>Felkoder och deras innebörd

Information om felsökning hittar du på den [Virtuella Azure-säkerhetskopiering felsökningsguide för](../../backup/backup-azure-vms-troubleshoot.md).

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [MSDN Azure och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du lagra en incident i Azure-supporten. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och välja Get support. Information om hur du använder Azure stöder finns i [vanliga frågor om Microsoft Azure-supporten](https://azure.microsoft.com/support/faq/).
