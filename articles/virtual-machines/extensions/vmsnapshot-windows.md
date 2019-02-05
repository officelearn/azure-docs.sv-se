---
title: Windows för ögonblicksbild av VM-tillägg för Azure Backup | Microsoft Docs
description: Ta programkonsekvent säkerhetskopiering av den virtuella datorn från Azure Backup med hjälp av VM-tillägget för ögonblicksbild
services: backup, virtual-machines-windows
documentationcenter: ''
author: trinadhk
manager: jeconnoc
ms.service: backup, virtual-machines-windows
ms.topic: article
ms.date: 12/17/2018
ms.author: trinadhk
ms.openlocfilehash: c41f609786620c8b90d484813d675efcd667d1e1
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2019
ms.locfileid: "55692638"
---
# <a name="vm-snapshot-windows-extension-for-azure-backup"></a>Windows för ögonblicksbild av VM-tillägg för Azure Backup

Azure Backup har stöd för att säkerhetskopiera arbetsbelastningar från en lokal plats till molnet och säkerhetskopiering av molnresurser till Recovery Services-valvet. Azure Backup använder tillägget för ögonblicksbild av virtuell dator för att ta en programkonsekvent säkerhetskopiering av virtuella Azure-datorer utan att behöva att stänga av den virtuella datorn. Tillägget för VM-ögonblicksbild är publicerat och stöds av Microsoft som en del av Azure Backup-tjänsten. Azure Backup installerar tillägget som en del av första schemalagda säkerhetskopiering utlösta inlägget att aktivera säkerhetskopiering. Det här dokumentet beskriver de plattformar som stöds, konfigurationer och distributionsalternativ för tillägget för VM-ögonblicksbild.

## <a name="prerequisites"></a>Förutsättningar

### <a name="operating-system"></a>Operativsystem
En lista över operativsystem som stöds finns i [operativsystem som stöds av Azure Backup](../../backup/backup-azure-arm-vms-prepare.md#before-you-start)

### <a name="internet-connectivity"></a>Internetanslutning

Tillägget för ögonblicksbild av virtuell dator kräver att den virtuella måldatorn är ansluten till internet när vi gör en säkerhetskopia av virtuell dator.

## <a name="extension-schema"></a>Tilläggsschema

Följande JSON visar schemat för tillägget för VM-ögonblicksbild. Tillägget kräver aktivitets-ID – detta anger att säkerhetskopieringsjobbet vilket utlöste ögonblicksbilder på den virtuella datorn, status för blob-uri - där status för ögonblicksbildsåtgärden skrivs Schemalagd starttid för ögonblicksbilden, loggar blob-uri - där loggarna som motsvarar ögonblicksbild uppgift skrivs, objstr-representation av VM-diskar och metadata.  Eftersom de här inställningarna ska behandlas som känsliga data, ska den lagras i en skyddad Konfigurationsinställningen. Azure VM-tillägget skyddade inställningsdata krypteras och dekrypteras bara på den virtuella måldatorn. Observera att dessa inställningar rekommenderas som ska skickas från Azure Backup-tjänsten endast som en del av säkerhetskopieringen.

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

| Namn | Värdet / exempel | Datatyp |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | datum |
| taskId | e07354cf-041e-4370-929f-25a319ce8933_1 | sträng |
| commandStartTimeUTCTicks | 6.36458E + 17 | sträng |
| nationella inställningar | en-us | sträng |
| objectStr | Kodning av sas uri matris-”blobSASUri”: [”https:\/\/sopattna5365.blob.core.windows.net\/virtuella hårddiskar\/vmwin1404ltsc201652903941.vhd? SA = 2014-02-14 & sr = b & sig = TywkROXL1zvhXcLujtCut8g3jTpgbE6JpSWRLZxAdtA % 3D & st = 2017-11-09T14% 3A23% 3A28Z & se = 2017-11-09T17% 3A38% 3A28Z & sp = rw ””, https:\/\/sopattna8461.blob.core.windows.net\/virtuella hårddiskar\/vmwin1404ltsc-20160629-122418.vhd? SA = 2014-02-14 & sr = b & sig = 5S0A6YDWvVwqPAkzWXVy % 2BS % 2FqMwzFMbamT5upwx05v8Q % 3D & st = 2017-11-09T14% 3A23% 3A28Z & se = 2017-11-09T17% 3A38% 3A28Z & sp = rw ””, https:\/ \/ sopattna8461.BLOB.Core.Windows.NET\/bootdiagnostics-vmwintu1-deb58392-ed5e-48be-9228-ff681b0cd3ee\/vmubuntu1404ltsc-20160629-122541.vhd? SA = 2014-02-14 & sr = b & sig = X0Me2djByksBBMVXMGIUrcycvhQSfjYvqKLeRA7nBD4% 3D & st = 2017-11-09T14% 3A23% 3A28Z & se = 2017-11-09T17% 3A38% 3A28Z & sp = rw ””, https:\/\/sopattna5365.blob.core.windows.net\/virtuella hårddiskar\/vmwin1404ltsc-20160701-163922.vhd? SA = 2014-02-14 & sr = b & sig = oXvtK2IXCNqWv7fpjc7TAzFDpc1GoXtT7r % 2BC % 2BNIAork % 3D & st = 2017-11-09T14% 3A23% 3A28Z & se = 2017-11-09T17% 3A38% 3A28Z & sp = rw ””, https:\/ \/ sopattna5365.BLOB.Core.Windows.NET\/virtuella hårddiskar\/vmwin1404ltsc-20170705-124311.vhd? SA = 2014-02-14 & sr = b & sig = ZUM9d28Mvvm % 2FfrhJ71TFZh0Ni90m38bBs3zMl % 2FQ9rs0% 3D & st = 2017-11-09T14% 3A23% 3A28Z & Se = 2017-11-09T17% 3A38% 3A28Z & sp = rw ”] | sträng |
| logsBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Logs.txt?sv=2014-02-14&sr=b&sig=DbwYhwfeAC5YJzISgxoKk%2FEWQq2AO1vS1E0rDW%2FlsBw%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | sträng |
| statusBlobUri | https://seapod01coord1exsapk732.blob.core.windows.net/bcdrextensionlogs-d45d8a1c-281e-4bc8-9d30-3b25176f68ea/sopattna-vmubuntu1404ltsc.v2.Status.txt?sv=2014-02-14&sr=b&sig=96RZBpTKCjmV7QFeXm5IduB%2FILktwGbLwbWg6Ih96Ao%3D&st=2017-11-09T14%3A33%3A29Z&se=2017-11-09T17%3A38%3A29Z&sp=rw | sträng |



## <a name="template-deployment"></a>Malldistribution

Azure VM-tillägg kan distribueras med Azure Resource Manager-mallar. Dock är det rekommenderade sättet att lägga till en VM-tillägget för ögonblicksbild till en virtuell dator genom att aktivera säkerhetskopiering på den virtuella datorn. Detta kan ske via Resource Manager-mall.  En Resource Manager-mall som sker säkerhetskopieringen på en virtuell dator kan hittas på den [Azure Quick Start-galleriet](https://azure.microsoft.com/resources/templates/101-recovery-services-backup-vms/).


## <a name="azure-cli-deployment"></a>Azure CLI-distribution

Azure CLI kan användas för att aktivera säkerhetskopiering på en virtuell dator. Post Aktivera säkerhetskopiering första schemalagda säkerhetskopieringsjobbet installerar tillägget för Vm-ögonblicksbild på den virtuella datorn.

```azurecli
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

## <a name="troubleshoot-and-support"></a>Felsökning och support

### <a name="troubleshoot"></a>Felsöka

Data om tillståndet för distributioner av tillägget kan hämtas från Azure-portalen och med hjälp av Azure CLI. Om du vill se distributionsstatusen för tillägg för en viss virtuell dator, kör du följande kommando med hjälp av Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Tillägget utförande-utdatan loggas till följande fil:

```
C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot
```

### <a name="error-codes-and-their-meanings"></a>Felkoder och deras innebörd

Information om felsökning finns på den [felsökningsguide för säkerhetskopiering av virtuella Azure-datorn](../../backup/backup-azure-vms-troubleshoot.md).

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experter på den [Azure för MSDN och Stack Overflow-forum](https://azure.microsoft.com/support/forums/). Alternativt kan du arkivera en Azure-support-incident. Gå till den [Azure supportwebbplats](https://azure.microsoft.com/support/options/) och väljer Get support. Information om hur du använder Azure-supporten finns i [vanliga frågor om Microsoft Azure-support](https://azure.microsoft.com/support/faq/).
