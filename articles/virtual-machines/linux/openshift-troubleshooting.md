---
title: Felsöka OpenShift-distribution i Azure | Microsoft Docs
description: Felsöka OpenShift-distribution i Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/19/2019
ms.author: haroldw
ms.openlocfilehash: af6746e7246b8783e5bdbef34cf1b57427aa7ebb
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001124"
---
# <a name="troubleshoot-openshift-deployment-in-azure"></a>Felsöka OpenShift-distribution i Azure

Om OpenShift klustret inte har distribuerat, ger Azure-portalen utdata om felet. Utdata kan vara svårt att läsa vilket gör det svårt att identifiera problemet. Skanna snabbt dessa utdata för slutkoden 3, 4 eller 5. Följande innehåller information om de här tre slutkoder för aktiviteter:

- Slutkod 3: Ditt Red Hat-prenumeration användarnamn / lösenord eller organisations-ID / Sidladdning är felaktig
- Slutkod 4: Red Hat Pool-ID är felaktigt eller det finns inga rättigheter
- Slutkod 5: Det går inte att etablera Docker tunn Pool-volym

För alla andra slutkoder för aktiviteter, ansluta till värdarna via ssh för att visa loggfilerna.

**OpenShift Container Platform**

SSH till värden för ansible-spelbok. Använd skyddsmiljö-värd för mallen eller Marketplace-erbjudande. Från skyddsmiljö kan du SSH till alla andra noder i klustret (master, infrastruktur, CN, beräkning). Du måste vara root att visa loggfilerna. Rot är inaktiverad för SSH-åtkomst som standard så att inte använda roten SSH till andra noder.

**OKD**

SSH till värden för ansible-spelbok. Använd master-0-värden för mallen OKD (version 3,9 och tidigare). Använd skyddsmiljö-värd för mallen OKD (version 3.10 och senare). Från ansible-spelbok värd kan du SSH till alla andra noder i klustret (master, infrastruktur, CN, beräkning). Du måste vara root (sudo su-) att visa loggfilerna. Rot är inaktiverad för SSH-åtkomst som standard så att inte använda roten SSH till andra noder.

## <a name="log-files"></a>Loggfiler

Loggfiler (stderr och stdout) för värd förberedelse skript finns i `/var/lib/waagent/custom-script/download/0` på alla värdar. Om ett fel uppstod vid förberedelsen av värden, visa loggfilerna för att fastställa felet.

Om förberedelse av skripten har körts sedan loggen filer i den `/var/lib/waagent/custom-script/download/1` katalog med ansible-spelbok-värden måste undersökas. Om felet uppstod under verklig installation av OpenShift, visas felet i filen stdout. Använd den här informationen för att kontakta Support för ytterligare hjälp.

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

1. Privata nyckel har lösenfras
2. Key vault-hemlighet med privat nyckel som inte har skapats korrekt
3. Autentiseringsuppgifter för tjänstens huvudnamn har angetts felaktigt
4. Tjänstens huvudnamn har inte deltagaråtkomst till resursgruppen.

### <a name="private-key-has-a-passphrase"></a>Privat nyckel har en lösenfras

Du ser ett fel som behörighet nekas för ssh. SSH till ansible-spelbok värden att söka efter en lösenfras för den privata nyckeln.

### <a name="key-vault-secret-with-private-key-wasnt-created-correctly"></a>Key vault-hemlighet med privat nyckel som inte har skapats korrekt

Den privata nyckeln kopieras till ansible spelbok host - ~/.ssh/id_rsa. Kontrollera den här filen är korrekt. Testa genom att öppna en SSH-session till en av noderna i klustret från värden för ansible-spelbok.

### <a name="service-principal-credentials-were-entered-incorrectly"></a>Autentiseringsuppgifter för tjänstens huvudnamn har angetts felaktigt

När du anger indata till mallen eller Marketplace-erbjudande, har felaktig information angetts. Kontrollera att du använder rätt appId (clientId) och lösenord (clientSecret) för tjänstens huvudnamn. Kontrollera genom att följande azure cli-kommando.

```bash
az login --service-principal -u <client id> -p <client secret> -t <tenant id>
```

### <a name="service-principal-doesnt-have-contributor-access-to-the-resource-group"></a>Tjänstens huvudnamn har inte deltagaråtkomst till resursgruppen.

Om Azure-molnet-providern är aktiverad, måste tjänstens huvudnamn som används ha deltagaråtkomst till resursgruppen. Kontrollera genom att följande azure cli-kommando.

```bash
az group update -g <openshift resource group> --set tags.sptest=test
```

## <a name="additional-tools"></a>Ytterligare verktyg

Du kan också använda följande kommandon för att få mer information för vissa fel:

1. status för systemctl \<service >
2. journalctl -xe
