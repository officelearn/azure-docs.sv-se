---
title: Felsöka OpenShift Container Platform 3.11-distribution i Azure
description: Felsöka OpenShift Container Platform 3.11-distribution i Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: bd83a1ca731d81edb76a3c1bc07113ce96adb9ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066593"
---
# <a name="troubleshoot-openshift-container-platform-311-deployment-in-azure"></a>Felsöka OpenShift Container Platform 3.11-distribution i Azure

Om OpenShift-klustret inte distribueras kommer Azure-portalen att tillhandahålla felutdata. Utdata kan vara svårt att läsa vilket gör det svårt att identifiera problemet. Skanna snabbt utdata efter utgångskod 3, 4 eller 5. Följande innehåller information om dessa tre exit koder:

- Avsluta kod 3: Ditt Red Hat-prenumerationsanvändarnamn/lösenord eller organisations-ID/ Aktiveringsnyckel är felaktigt
- Avsluta kod 4: Ditt Red Hat Pool-ID är felaktigt eller så finns det inga rättigheter tillgängliga
- Avslutningskod 5: Det går inte att etablera dockers tunna poolvolym

För alla andra avslutningskoder ansluter du till värden via ssh för att visa loggfilerna.

**OpenShift Container Platform 3.11**

SSH till den ansible playbook värden. Använd skyddsvärden för mallen eller Marketplace-erbjudandet. Från bastionen kan du SSH till alla andra noder i klustret (master, infra, CNS, compute). Du måste vara root för att kunna visa loggfilerna. Root är inaktiverat för SSH-åtkomst som standard så använd inte roten till SSH till andra noder.

**OKD**

SSH till den ansible playbook värden. För OKD-mallen (version 3.9 och tidigare) använder du huvud-0-värden. Använd skyddsvärden för OKD-mallen (version 3.10 och senare). Från den ansible playbook-värden kan du SSH till alla andra noder i klustret (master, infra, CNS, compute). Du måste vara root (sudo su -) för att kunna visa loggfilerna. Root är inaktiverat för SSH-åtkomst som standard så använd inte roten till SSH till andra noder.

## <a name="log-files"></a>Loggfiler

Loggfilerna (stderr och stdout) för värdförberedelseskripten finns på `/var/lib/waagent/custom-script/download/0` alla värdar. Om ett fel uppstod under förberedelsen av värden kan du visa dessa loggfiler för att fastställa felet.

Om förberedelseskripten kördes måste loggfilerna `/var/lib/waagent/custom-script/download/1` i katalogen för den ansible playbook-värden undersökas. Om felet uppstod under den faktiska installationen av OpenShift visas felet i stdout-filen. Använd den här informationen för att kontakta supporten för ytterligare hjälp.

Exempel på utdata

```json
TASK [openshift_storage_glusterfs : Load heketi topology] **********************
fatal: [mycluster-master-0]: FAILED! => {"changed": true, "cmd": ["oc", "--config=/tmp/openshift-glusterfs-ansible-IbhnUM/admin.kubeconfig", "rsh", "--namespace=glusterfs", "deploy-heketi-storage-1-d9xl5", "heketi-cli", "-s", "http://localhost:8080", "--user", "admin", "--secret", "VuoJURT0/96E42Vv8+XHfsFpSS8R20rH1OiMs3OqARQ=", "topology", "load", "--json=/tmp/openshift-glusterfs-ansible-IbhnUM/topology.json", "2>&1"], "delta": "0:00:21.477831", "end": "2018-05-20 02:49:11.912899", "failed": true, "failed_when_result": true, "rc": 0, "start": "2018-05-20 02:48:50.435068", "stderr": "", "stderr_lines": [], "stdout": "Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2\n\tAllowing file volumes on cluster.\n\tAllowing block volumes on cluster.\n\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4\n\t\tAdding device /dev/sdd ... OK\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131\n\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdd ... OK\n\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f\n\t\tAdding device /dev/sde ... OK\n\t\tAdding device /dev/sdf ... OK\n\t\tAdding device /dev/sdd ... OK", "stdout_lines": ["Creating cluster ... ID: 794b285745b1c5d7089e1c5729ec7cd2", "\tAllowing file volumes on cluster.", "\tAllowing block volumes on cluster.", "\tCreating node mycluster-cns-0 ... ID: 45f1a3bfc20a4196e59ebb567e0e02b4", "\t\tAdding device /dev/sdd ... OK", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\tCreating node mycluster-cns-1 ... ID: 596f80d7bbd78a1ea548930f23135131", "\t\tAdding device /dev/sdc ... Unable to add device: Unable to execute command on glusterfs-storage-4zc42:   Device /dev/sdc excluded by a filter.", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdd ... OK", "\tCreating node mycluster-cns-2 ... ID: 42c0170aa2799559747622acceba2e3f", "\t\tAdding device /dev/sde ... OK", "\t\tAdding device /dev/sdf ... OK", "\t\tAdding device /dev/sdd ... OK"]}

PLAY RECAP *********************************************************************
mycluster-cns-0       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-1       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-cns-2       : ok=146  changed=57   unreachable=0    failed=0   
mycluster-infra-0     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-1     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-infra-2     : ok=143  changed=55   unreachable=0    failed=0   
mycluster-master-0    : ok=502  changed=198  unreachable=0    failed=1   
mycluster-master-1    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-master-2    : ok=348  changed=140  unreachable=0    failed=0   
mycluster-node-0      : ok=143  changed=55   unreachable=0    failed=0   
mycluster-node-1      : ok=143  changed=55   unreachable=0    failed=0   
localhost                  : ok=13   changed=0    unreachable=0    failed=0   

INSTALLER STATUS ***************************************************************
Initialization             : Complete (0:00:39)
Health Check               : Complete (0:00:24)
etcd Install               : Complete (0:01:24)
Master Install             : Complete (0:14:59)
Master Additional Install  : Complete (0:01:10)
Node Install               : Complete (0:10:58)
GlusterFS Install          : In Progress (0:03:33)
    This phase can be restarted by running: playbooks/openshift-glusterfs/config.yml

Failure summary:

  1. Hosts:    mycluster-master-0
     Play:     Configure GlusterFS
     Task:     Load heketi topology
     Message:  Failed without returning a message.
```

De vanligaste felen under installationen är:

1. Privat nyckel har lösenfras
2. Nyckelvalvs hemlighet med privat nyckel skapades inte korrekt
3. Tjänstens huvudreferenser angavs felaktigt
4. Tjänsthuvudnamn har inte deltagaråtkomst till resursgruppen

### <a name="private-key-has-a-passphrase"></a>Private Key har en lösenfras

Du ser ett felmeddelande om att behörigheten nekades för ssh. ssh till den ansible playbook värd för att kontrollera om en lösenfras på den privata nyckeln.

### <a name="key-vault-secret-with-private-key-wasnt-created-correctly"></a>Nyckelvalvs hemlighet med privat nyckel skapades inte korrekt

Den privata nyckeln kopieras till den ansible playbook värd - ~/.ssh /id_rsa. Bekräfta att filen är korrekt. Testa genom att öppna en SSH-session till en av klusternoderna från den ansible playbook-värden.

### <a name="service-principal-credentials-were-entered-incorrectly"></a>Tjänstens huvudreferenser angavs felaktigt

När du angav indata till mallen eller Marketplace-erbjudandet angavs felaktig information. Se till att du använder rätt appId (clientId) och lösenord (clientSecret) för tjänstens huvudnamn. Verifiera genom att utfärda följande azure cli-kommando.

```azurecli
az login --service-principal -u <client id> -p <client secret> -t <tenant id>
```

### <a name="service-principal-doesnt-have-contributor-access-to-the-resource-group"></a>Tjänsthuvudnamn har inte deltagaråtkomst till resursgruppen

Om Azure-molnleverantören är aktiverad måste tjänsthuvudnamnet som används ha deltagaråtkomst till resursgruppen. Verifiera genom att utfärda följande azure cli-kommando.

```azurecli
az group update -g <openshift resource group> --set tags.sptest=test
```

## <a name="additional-tools"></a>Ytterligare verktyg

För vissa fel kan du också använda följande kommandon för att få mer information:

1. systemctl \<status tjänst>
2. journalctl -xe
