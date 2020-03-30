---
title: Vm Snapshot Linux-tillägg för Azure Backup
description: Ta programmet konsekvent säkerhetskopiering av den virtuella datorn från Azure Backup med vm-ögonblicksbildtillägg
services: backup, virtual-machines-linux
documentationcenter: ''
author: trinadhkotturu
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.date: 12/17/2018
ms.author: trinadhk
ms.openlocfilehash: d0ad54c19749d670f9ab753e1e6d8eb130475ffc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415126"
---
# <a name="vm-snapshot-linux-extension-for-azure-backup"></a>Vm Snapshot Linux-tillägg för Azure Backup



Azure Backup ger stöd för säkerhetskopiering av arbetsbelastningar från lokalt till moln och säkerhetskopiering av molnresurser till Recovery Services-valvet. Azure Backup använder vm-ögonblicksbildtillägg för att ta ett program konsekvent säkerhetskopiering av den virtuella Azure-datorn utan att behöva stänga av den virtuella datorn. Vm Snapshot Linux-tillägget publiceras och stöds av Microsoft som en del av Azure Backup-tjänsten. Azure Backup installerar tillägget som en del av den första schemalagda säkerhetskopieringen som utlösts efter säkerhetskopiering. I det här dokumentet beskrivs de plattformar, konfigurationer och distributionsalternativ som stöds för tillägget Ögonblicksbild av virtuella datorer.

VMSnapshot-tillägget visas endast i Azure-portalen för icke-hanterade virtuella datorer.

## <a name="prerequisites"></a>Krav

### <a name="operating-system"></a>Operativsystem
En lista över operativsystem som stöds finns i [operativsystem som stöds av Azure Backup](../../backup/backup-azure-arm-vms-prepare.md#before-you-start)

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för vm-ögonblicksbildtillägget. Tillägget kräver aktivitets-ID - detta identifierar säkerhetskopieringsjobbet som utlöste ögonblicksbild på den virtuella datorn, status blob uri - där status för ögonblicksbildåtgärden skrivs, schemalagd starttid för ögonblicksbilden, loggar blob uri - där loggar som motsvarar ögonblicksbildaktivitet är skrivna, objstr- representation av VM-diskar och metadata.  Eftersom dessa inställningar ska behandlas som känsliga data bör de lagras i en skyddad inställningskonfiguration. Azure VM-tilläggsskyddade inställningsdata krypteras och dekrypteras endast på den virtuella måldatorn. Observera att dessa inställningar rekommenderas att skickas från Azure Backup-tjänsten endast som en del av säkerhetskopieringsjobbet.

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
      "objectStr": "<blob SAS uri representation of VM sent by Azure Backup service to extension>",
      "logsBlobUri": "<blob uri where logs of command execution by extension are written to>",
      "statusBlobUri": "<blob uri where status of the command executed by extension is written>"
    }
  }
}
```

### <a name="property-values"></a>Egenskapsvärden

| Namn | Värde / Exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| taskId (på uppdrag) | e07354cf-041e-4370-929f-25a319ce8933_1 | sträng |
| kommandotStartTimeUTCTicks | 6.36458E+17 | sträng |
| språk | sv-se | sträng |
| objectStr (objektStr) | Kodning av sas uri array- "blobSASUri":\/\/["https:\/sopattna5365.blob.core.windows.net\/vhds vmubuntu1404ltsc201652903941.vhd?sv=2014-02-14&sr=b&sig=TywkROXL1zvhXcLujtCut8g3jTpgbE6JpSWRLZxAdtA%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw". "https:\/\/sopattna8461.blob.core.windows.net\/vhds\/vmubuntu1404ltsc-20160629-122418.vhd?sv=2014-02-14&sr=b&sig= 5S0A6YDWvVwqPAkzWXVy%2BS%2FqMwzFMbamT5upwx05v8Q%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna8461.blob.core.windows.net\/bootdiagnostics-vmubuntu1-deb58392-ed5e-48be-9228-ff681b0cd3ee\/vmubuntu1404ltsc-20160629-122541.vhd?sv=2014-02-14&sr=b&sig=X0Me2djByksBBMVXMGIUrcycvhQSfjYvqKLeRA7nBD4%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", " https:\/\/sopattna5365.blob.core.windows.net\/vhds\/vmubuntu1404ltsc-20160701-163922.vhd?sv=2014-02-14&sr=b&sig=oXvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r%2BC%2BNIAork%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw", "https:\/\/sopattna5365.blob.core.windows.net\/vhds\/vmubuntu1404ltsc-20170705-124311.vhd?sv=2014-02-14&sr=b&sig=ZUM9d28Mvvm% 2FfrhJ71TFZh0Ni90m38bBs3zMl%2FQ9rs0%3D&st=2017-11-09T14%3A23%3A28Z&se=2017-11-09T17%3A38%3A28Z&sp=rw"] | sträng |
| loggarBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | sträng |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | sträng |



## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. Det rekommenderade sättet att lägga till ett vm-ögonblicksbildtillägg till en virtuell dator är dock genom att aktivera säkerhetskopiering på den virtuella datorn. Detta kan uppnås genom en Resource Manager-mall.  En exempelmall för Resource Manager som möjliggör säkerhetskopiering på en virtuell dator finns i [Snabbstartsgalleriet](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/)i Azure .


## <a name="azure-cli-deployment"></a>Azure CLI-distribution

Azure CLI kan användas för att aktivera säkerhetskopiering på en virtuell dator. Efter aktivera säkerhetskopiering, första schemalagda säkerhetskopiering jobb kommer att installera Vm ögonblicksbild förlängning på den virtuella datorn.

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för tilläggsdistributioner kan hämtas från Azure-portalen och med hjälp av Azure CLI. Om du vill se distributionstillståndet för tillägg för en viss virtuell dator kör du följande kommando med Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Utdata för tilläggskörning loggas till följande fil:

```
/var/log/waagent.log
```

### <a name="error-codes-and-their-meanings"></a>Felkoder och deras betydelser

Felsökningsinformation finns på [felsökningsguiden](../../backup/backup-azure-vms-troubleshoot.md)för säkerhetskopiering av Azure VM .

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/). Du kan också arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj Hämta support. Information om hur du använder Azure Support finns i [vanliga frågor och svar om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
