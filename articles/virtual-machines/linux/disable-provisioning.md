---
title: Inaktivera eller ta bort etablerings agenten
description: Lär dig hur du inaktiverar eller tar bort etablerings agenten i virtuella Linux-datorer och avbildningar.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: d5d173e0b0204ee9e9dbe6e8b51d38d4e42d4fc2
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2020
ms.locfileid: "85306935"
---
# <a name="disable-or-remove-the-linux-agent-from-vms-and-images"></a>Inaktivera eller ta bort Linux-agenten från virtuella datorer och avbildningar

Innan du tar bort Linux-agenten måste du förstå vilken virtuell dator som inte kommer att kunna utföras efter att Linux-agenten har tagits bort.

[Tillägg](https://docs.microsoft.com/azure/virtual-machines/extensions/overview) för virtuella Azure-datorer (VM) är små program som tillhandahåller konfigurations-och automatiserings åtgärder efter distributionen på virtuella Azure-datorer, tillägg installeras och hanteras av Azures kontroll plan. Det är jobbet i [Azure Linux-agenten](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) att bearbeta plattforms tilläggets kommandon och se till att rätt tillstånd för tillägget finns i den virtuella datorn.

Azure-plattformen är värd för många tillägg som sträcker sig från konfigurations-, övervaknings-, säkerhets-och verktygs program för virtuella datorer. Det finns ett stort urval av de första och tredje parts tilläggen, exempel på viktiga scenarier som tillägg används för:
* Stöd för Azure-tjänster från första part, till exempel Azure Backup, övervakning, disk kryptering, säkerhet, plats replikering och andra.
* SSH-/lösen ords återställning
* Konfiguration av virtuella datorer – körning av anpassade skript, installation av chef, Puppet-agenter osv.
* Produkter från tredje part, till exempel AV-produkter, sårbarhets verktyg för virtuella datorer, verktyg för övervakning av virtuella datorer och appar.
* Tillägg kan paketeras med en ny VM-distribution. De kan till exempel vara en del av en större distribution, konfigurera program på VM-etablering eller köra mot alla tillägg som stöds och som hanteras av system efter distribution.

## <a name="disabling-extension-processing"></a>Inaktiverar tilläggs bearbetning

Det finns flera sätt att inaktivera bearbetning av tillägg, beroende på dina behov, men innan du fortsätter **måste** du ta bort alla tillägg som distribueras till den virtuella datorn, till exempel med hjälp av AZ CLI, och du kan [Visa](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-list) och [ta bort](https://docs.microsoft.com/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-delete):

```bash
az vm extension delete -g MyResourceGroup --vm-name MyVm -n extension_name
```

### <a name="disable-at-the-control-plane"></a>Inaktivera vid kontroll planet
Om du inte är säker på om du kommer att behöva tillägg i framtiden kan du lämna Linux-agenten installerad på den virtuella datorn och sedan inaktivera tilläggs bearbetnings kapacitet från plattformen. Det här alternativet är tillgängligt i `Microsoft.Compute` API `2018-06-01` -versionen eller högre, och har inte något beroende av versionen av Linux-agenten installerad.

```bash
az vm update -g <resourceGroup> -n <vmName> --set osProfile.allowExtensionOperations=false
```
Du kan enkelt återaktivera den här tilläggs bearbetningen från plattformen med kommandot ovan, men ange det till true.

### <a name="optional---reduce-the-functionality"></a>Valfritt – minska funktionerna 

Du kan också ställa in Linux-agenten i läget Nedsatt funktionalitet. I det här läget kommunicerar gäst agenten fortfarande med Azure Fabric och rapporterar gäst status på ett mycket mer begränsat sätt, men behandlar inga tilläggs uppdateringar. För att minska funktionerna måste du göra en konfigurations ändring på den virtuella datorn. Om du vill aktivera igen måste du använda SSH i den virtuella datorn, men om du är utelåst från den virtuella datorn skulle du inte kunna återaktivera bearbetning av tillägg, det här kan vara ett problem om du behöver göra en SSH eller lösen ords återställning.

Om du vill aktivera det här läget krävs WALinuxAgent version 2.2.32 eller högre, och ange följande alternativ i/etc/waagent.conf:

```bash
Extensions.Enabled=n
```

Detta **måste** göras tillsammans med "inaktivera vid kontroll planet".

## <a name="remove-the-linux-agent-from-a-running-vm"></a>Ta bort Linux-agenten från en virtuell dator som körs

Se till att du har **tagit bort** alla befintliga tillägg från den virtuella datorn före, enligt ovan.

### <a name="step-1-disable-extension-processing"></a>Steg 1: inaktivera tilläggs bearbetning

Du måste inaktivera tilläggs bearbetning.

```bash
az vm update -g <resourceGroup> -n <vmName> --set osProfile.allowExtensionOperations=false
```
> [!Note]
> 
> Om du inte gör det kommer plattformen att försöka skicka tilläggs konfigurationen och tids gränsen efter 40min.

### <a name="step-2-remove-the-azure-linux-agent"></a>Steg 2: ta bort Azure Linux-agenten

Kör något av följande, som rot, för att ta bort Azure Linux-agenten:

#### <a name="for-ubuntu-1804"></a>För Ubuntu >= 18,04
```bash
apt -y remove walinuxagent
```

#### <a name="for-redhat--77"></a>För redhat >= 7,7
```bash
yum -y remove WALinuxAgent
```

#### <a name="for-suse"></a>För SUSE
```bash
zypper --non-interactive remove python-azure-agent
```

### <a name="step-3-optional-remove-the-azure-linux-agent-artifacts"></a>Steg 3: (valfritt) ta bort Azure Linux Agent-artefakter
> [!IMPORTANT] 
>
> Du kan ta bort alla artefakter i Linux-agenten, men det innebär att du inte kan installera om den vid ett senare tillfälle. Därför rekommenderar vi att du inaktiverar Linux-agenten först och sedan tar du bara bort Linux-agenten med hjälp av ovanstående. 

Om du vet att du inte någonsin installerar om Linux-agenten igen kan du köra följande:

#### <a name="for-ubuntu-1804"></a>För Ubuntu >= 18,04
```bash
apt -y remove walinuxagent
rm -f /etc/waagent.conf
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-redhat--77"></a>För redhat >= 7,7
```bash
yum -y remove WALinuxAgent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

#### <a name="for-suse"></a>För SUSE
```bash
zypper --non-interactive remove python-azure-agent
rm -f /etc/waagent.conf.rpmsave
rm -rf /var/lib/waagent
rm -f /var/log/waagent.log
```

## <a name="preparing-an-image-without-the-linux-agent"></a>Förbereda en avbildning utan Linux-agenten
Om du har en avbildning som redan innehåller Cloud-Init och du vill ta bort Linux-agenten, men ändå etablera med Cloud-Init, kör du stegen i steg 2 (och eventuellt steg 3) som rot för att ta bort Azure Linux-agenten. Därefter tar följande bort konfigurationen och cachelagrade data i Cloud-Init och förbereder den virtuella datorn för att skapa en anpassad avbildning.

```bash
cloud-init clean --logs --seed 
```

## <a name="deprovision-and-create-an-image"></a>Avetablera och skapa en avbildning
Linux-agenten kan rensa några av de befintliga bildmetadatana, med steget "waagent-deprovision + User", men när det har tagits bort måste du utföra åtgärder som nedan och ta bort alla andra känsliga data från den.

- Ta bort alla befintliga SSH Host-nycklar

   ```bash
   rm /etc/ssh/ssh_host_*key*
   ```
- Ta bort administratörs kontot

   ```bash
   touch /var/run/utmp
   userdel -f -r <admin_user_account>
   ```
- Ta bort rot lösen ordet

   ```bash
   passwd -d root
   ```

När du har slutfört ovanstående kan du skapa den anpassade avbildningen med hjälp av Azure CLI.


**Skapa en vanlig hanterad avbildning**
```bash
az vm deallocate -g <resource_group> -n <vm_name>
az vm generalize -g <resource_group> -n <vm_name>
az image create -g <resource_group> -n <image_name> --source <vm_name>
```

**Skapa en avbildnings version i ett galleri för delad avbildning**

```bash
az sig image-version create \
    -g $sigResourceGroup 
    --gallery-name $sigName 
    --gallery-image-definition $imageDefName 
    --gallery-image-version 1.0.0 
    --managed-image /subscriptions/00000000-0000-0000-0000-00000000xxxx/resourceGroups/imageGroups/providers/images/MyManagedImage
```
### <a name="creating-a-vm-from-an-image-that-does-not-contain-a-linux-agent"></a>Skapa en virtuell dator från en avbildning som inte innehåller en Linux-Agent
När du skapar den virtuella datorn från avbildningen utan någon Linux-Agent måste du se till att den virtuella datorns distributions konfiguration anger att tillägg inte stöds på den här virtuella datorn.

> [!NOTE] 
> 
> Om du inte gör det kommer plattformen att försöka skicka tilläggs konfigurationen och tids gränsen efter 40min.

Om du vill distribuera den virtuella datorn med tillägg inaktiverade kan du använda Azure CLI med [--Enable-agent](https://docs.microsoft.com/cli/azure/vm#az-vm-create).

```bash
az vm create \
    --resource-group $resourceGroup \
    --name $prodVmName \
    --image RedHat:RHEL:8.1-ci:latest \
    --admin-username azadmin \
    --ssh-key-value "$sshPubkeyPath" \
    --enable-agent false
```

Du kan också göra detta med hjälp av Azure Resource Manager (ARM) Mallar genom att ställa in `"provisionVMAgent": false,` .

```json
"osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "linuxConfiguration": {
        "disablePasswordAuthentication": "true",
        "provisionVMAgent": false,
        "ssh": {
            "publicKeys": [
                {
                    "path": "[concat('/home/', parameters('adminUsername'), '/.ssh/authorized_keys')]",
                    "keyData": "[parameters('adminPublicKey')]"
```

## <a name="next-steps"></a>Nästa steg

Mer information finns i avsnittet om [etablering av Linux](provisioning.md).
