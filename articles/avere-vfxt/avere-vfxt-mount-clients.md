---
title: Montera Avere vFXT - Azure
description: Så här monterar du klienter med Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: rohogue
ms.openlocfilehash: b8486b5a33226b1faa5e3874144129dbe7a1a2f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153419"
---
# <a name="mount-the-avere-vfxt-cluster"></a>Montera Avere vFXT-klustret

Följ dessa steg för att ansluta klientdatorer till ditt vFXT-kluster.

1. Bestäm hur klienttrafiken mellan klusternoderna ska belastningsbalanseras. Läs [Balans klient belastning](#balance-client-load), nedan, för mer information.
1. Identifiera IP-adressen och kopplingssökvägen som ska monteras.
1. Utfärda [kommandot Montera](#mount-command-arguments)med lämpliga argument.

## <a name="balance-client-load"></a>Balansera klientbelastning

Om du vill balansera klientbegäranden mellan alla noder i klustret bör du montera klienter till alla klientinriktade IP-adresser. Det finns flera enkla sätt att automatisera den här uppgiften.

> [!TIP]
> Andra belastningsutjämningsmetoder kan vara lämpliga för stora eller komplicerade system. [öppna en supportbiljett](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) för hjälp.)
>
> Om du föredrar att använda en DNS-server för automatisk belastningsutjämning på serversidan måste du konfigurera och hantera din egen DNS-server i Azure. I så fall kan du konfigurera round-robin DNS för vFXT-klustret enligt det här dokumentet: [Avere cluster DNS-konfiguration](avere-vfxt-configure-dns.md).

### <a name="sample-balanced-client-mounting-script"></a>Provbalanserad klientmonteringsskript

I det här kodexemplet används klient-IP-adresser som ett randomiseringselement för att distribuera klienter till alla vFXT-klustrets tillgängliga IP-adresser.

```bash
function mount_round_robin() {
    # to ensure the nodes are spread out somewhat evenly the default
    # mount point is based on this node's IP octet4 % vFXT node count.
    declare -a AVEREVFXT_NODES="($(echo ${NFS_IP_CSV} | sed "s/,/ /g"))"
    OCTET4=$((`hostname -i | sed -e 's/^.*\.\([0-9]*\)/\1/'`))
    DEFAULT_MOUNT_INDEX=$((${OCTET4} % ${#AVEREVFXT_NODES[@]}))
    ROUND_ROBIN_IP=${AVEREVFXT_NODES[${DEFAULT_MOUNT_INDEX}]}

    DEFAULT_MOUNT_POINT="${BASE_DIR}/default"

    # no need to write again if it is already there
    if ! grep --quiet "${DEFAULT_MOUNT_POINT}" /etc/fstab; then
        echo "${ROUND_ROBIN_IP}:${NFS_PATH}    ${DEFAULT_MOUNT_POINT}    nfs hard,proto=tcp,mountproto=tcp,retry=30 0 0" >> /etc/fstab
        mkdir -p "${DEFAULT_MOUNT_POINT}"
        chown nfsnobody:nfsnobody "${DEFAULT_MOUNT_POINT}"
    fi
    if ! grep -qs "${DEFAULT_MOUNT_POINT} " /proc/mounts; then
        retrycmd_if_failure 12 20 mount "${DEFAULT_MOUNT_POINT}" || exit 1
    fi
}
```

Funktionen ovan är en del av exemplet Batch som finns på exempelwebbplatsen [Avere vFXT.](https://github.com/Azure/Avere#tutorials)

## <a name="create-the-mount-command"></a>Skapa kommandot montera

> [!NOTE]
> Om du inte skapade en ny Blob-behållare när du skapade Avere vFXT-klustret lägger du till lagringssystem enligt beskrivningen i [Konfigurera lagring](avere-vfxt-add-storage.md) innan du försöker montera klienter.

Från klienten ``mount`` mappar kommandot den virtuella servern (vserver) i vFXT-klustret till en sökväg i det lokala filsystemet. Formatet är``mount <vFXT path> <local path> {options}``

Kommandot montera har tre element:

* vFXT-sökväg - en kombination av en IP-adress och namnområdeskorsningssökväg i klustret 9deskriven nedan)
* lokal sökväg - sökvägen till klienten
* kommandoty alternativ - som visas i [kommandot argument för Montera](#mount-command-arguments)

### <a name="junction-and-ip"></a>Korsning och IP

Vserver-sökvägen är en kombination av dess *IP-adress* plus sökvägen till en *namnområdeskorsning*. Namnområdeskopplingen är en virtuell sökväg som definierades när lagringssystemet lades till.

Om klustret skapades med Blob-lagring är namnområdessökvägen till den behållaren`/msazure`

Exempel: ``mount 10.0.0.12:/msazure /mnt/vfxt``

Om du har lagt till lagringsutrymme när du har skapat klustret är namnområdeskorsningssökvägen det värde som du anger i **namnområdessökvägen** när du skapar korsningen. Om du till ``/avere/files`` exempel använde som namnområdessökväg skulle klienterna montera *IP_address*:/avere/files till deras lokala monteringspunkt.

![Dialogrutan "Lägg till ny korsning" med /avere/files i namnområdessökvägen](media/avere-vfxt-create-junction-example.png) <!-- to do - change example and screenshot to vfxt/files instead of avere -->

IP-adressen är en av de klientvända IP-adresser som definierats för vservern. Du hittar utbudet av klientinriktade IPs på två ställen på Avere Kontrollpanelen:

* **VServers-tabell** (fliken Instrumentpanel) -

  ![Fliken Instrumentpanel på Kontrollpanelen i Avere med fliken VServer markerad i datatabellen under diagrammet och avsnittet IP-adress inringad](media/avere-vfxt-ip-addresses-dashboard.png)

* **Sidan Inställningar för klientvändiga nätverk** -

  ![Inställningar > VServer > konfigurationssida för klientvänt nätverks med en cirkel runt avsnittet Adressintervall i tabellen för en viss vserver](media/avere-vfxt-ip-addresses-settings.png)

Förutom sökvägarna, inkludera [kommandot argument för montering](#mount-command-arguments) som beskrivs nedan när varje klient monteras.

### <a name="mount-command-arguments"></a>Kommandoargument för Montera

För att säkerställa en sömlös klientmontering, skicka dessa inställningar och argument i ditt monteringskommando:

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Nödvändiga inställningar | |
--- | ---
``hard`` | Mjuka fästen till vFXT-klustret är associerade med programfel och eventuell dataförlust.
``proto=netid`` | Det här alternativet stöder lämplig hantering av NFS-nätverksfel.
``mountproto=netid`` | Det här alternativet stöder lämplig hantering av nätverksfel för monteringsåtgärder.
``retry=n`` | Ställ ``retry=30`` in för att undvika tillfälliga monteringsfel. (Ett annat värde rekommenderas i förgrundsfästen.)

## <a name="next-steps"></a>Nästa steg

När du har klienter monterade kan du använda dem för att kopiera data till en ny Blob-lagringsbehållare i klustret. Om du inte behöver fylla i ny lagring läser du de andra länkarna om du vill veta mer om ytterligare inställningsuppgifter:

* [Flytta data till en klusterkärna filer](avere-vfxt-data-ingest.md) - Hur man använder flera klienter och trådar för att effektivt ladda upp dina data till en ny kärna filer
* [Anpassa klusterjustering](avere-vfxt-tuning.md) - Anpassa klusterinställningarna så att de passar din arbetsbelastning
* [Hantera klustret](avere-vfxt-manage-cluster.md) - Så här startar eller stoppar du klustret och hanterar noder
