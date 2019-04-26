---
title: Montera Avere vFXT - Azure
description: Hur du monterar klienter med Avere vFXT för Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: 41065b4ac6bc486e204c2bfd72b78ba8722270c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60409386"
---
# <a name="mount-the-avere-vfxt-cluster"></a>Montera Avere vFXT-klustret  

Följ dessa steg för att ansluta klientdatorer till vFXT klustret.

1. Bestäm hur du belastningsutjämnar klienttrafik mellan klusternoderna. Läs [saldo klientbelastningen](#balance-client-load)nedan för information. 
1. Identifiera IP-adress och knutpunkt sökvägen att montera.
1. Problem i [monteringskommando](#mount-command-arguments), med rätt argument.

## <a name="balance-client-load"></a>Belastningsutjämna klientbelastningen

För att belastningsutjämna klientbegäranden mellan noderna i klustret, ska du ansluta klienter till en fullständig uppsättning klientinriktade IP-adresser. Det finns flera enkla sätt att automatisera den här uppgiften.

> [!TIP] 
> Andra metoder för belastningsutjämning kan vara lämpligt för stora eller komplicerade system. [öppna ett supportärende](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) om du behöver hjälp.)
> 
> Om du föredrar att använda en DNS-server för belastningsutjämning för automatisk serversidan, måste du konfigurera och hantera din egen DNS-server i Azure. I så fall kan du konfigurera DNS-resursallokering för vFXT klustret enligt det här dokumentet: [DNS-klusterkonfiguration Avere](avere-vfxt-configure-dns.md).

### <a name="sample-balanced-client-mounting-script"></a>Exemplet belastningsutjämnade klienten montera skript

Det här kodexemplet använder klienternas IP-adresser som ett randomizing element för att distribuera klienter till alla vFXT klustrets tillgängliga IP-adresser.

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
        echo "${ROUND_ROBIN_IP}:${NFS_PATH}    ${DEFAULT_MOUNT_POINT}    nfs hard,nointr,proto=tcp,mountproto=tcp,retry=30 0 0" >> /etc/fstab
        mkdir -p "${DEFAULT_MOUNT_POINT}"
        chown nfsnobody:nfsnobody "${DEFAULT_MOUNT_POINT}"
    fi
    if ! grep -qs "${DEFAULT_MOUNT_POINT} " /proc/mounts; then
        retrycmd_if_failure 12 20 mount "${DEFAULT_MOUNT_POINT}" || exit 1
    fi   
} 
```

Funktionen ovan är en del av i Batch-exempel som är tillgängliga i den [Avere vFXT exempel](https://github.com/Azure/Avere#tutorials) plats.

## <a name="create-the-mount-command"></a>Skapa mount-kommando 

> [!NOTE]
> Om du inte har skapat en ny blobbehållare när du skapar din Avere vFXT kluster, följer du stegen i [konfigurerar du lagring](avere-vfxt-add-storage.md) innan du försöker montera klienter.

Från klienten den ``mount`` kommandot mappar den virtuella servern (vserver) på vFXT klustret till en sökväg i det lokala filsystemet. Formatet är ``mount <vFXT path> <local path> {options}``

Det finns tre element i mount-kommandot: 

* vFXT sökväg - (en kombination av IP-adress och namnområde knutpunkt sökväg som beskrivs nedan)
* lokal sökväg - sökväg på klienten 
* Montera kommandoalternativ - (anges i [montera kommandoargumenten](#mount-command-arguments))

### <a name="junction-and-ip"></a>Knutpunkt och IP

Vserver sökvägen är en kombination av dess *IP-adress* plus sökvägen till en *namnområde knutpunkt*. Knutpunkt för namnområdet är en virtuell sökväg som definierades när lagringssystemet har lagts till.

Om klustret har skapats i Blob storage, är namnområdessökvägen `/msazure`

Exempel: ``mount 10.0.0.12:/msazure /mnt/vfxt``

Om du vill lägga till lagring när du har skapat klustret, knutpunkt namnrymdssökväg motsvarar värdet i **Namespace sökväg** när du skapar knutpunkten. Exempel: Om du använde ``/avere/files`` som sökväg för namnområdet klienterna monterar *IP-adress*: / avere/filer till deras lokala monteringspunkt.

![”Lägg till ny knutpunkt” dialogen med/avere/filer i sökvägsfältet namnområde](media/avere-vfxt-create-junction-example.png)


IP-adressen är en av de IP-adresser för klientinriktade som definierats för vserver. Du kan hitta intervallet för IP-adresser på två platser i Kontrollpanelen Avere för klientinriktade:

* **VServers** tabell (fliken instrumentpanel) - 

  ![Fliken instrumentpanel i Kontrollpanelen Avere med fliken VServer markerad i tabellen nedan i diagrammet och IP-adressavsnitt inringade](media/avere-vfxt-ip-addresses-dashboard.png)

* **Inför klientnätverk** inställningssidan - 

  ![Inställningar > VServer > konfigurationssidan för klienten som riktas mot nätverket med en inringad avsnittet adressintervall i tabellen för en viss vserver](media/avere-vfxt-ip-addresses-settings.png)

Förutom sökvägar, inkludera den [montera kommandoargumenten](#mount-command-arguments) beskrivs nedan när du monterar varje klient.

### <a name="mount-command-arguments"></a>Montera kommandoargumenten

För att säkerställa en smidig klienten montering, skicka dessa inställningar och argumenten i mount-kommando: 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``


| Nödvändiga inställningar | |
--- | --- 
``hard`` | Mjuk monterar till vFXT klustret är kopplade till programfel och förlust av data. 
``proto=netid`` | Det här alternativet stöder lämplig hantering av NFS-nätverksfel.
``mountproto=netid`` | Det här alternativet stöder lämplig hantering av nätverksfel för mount-åtgärder.
``retry=n`` | Ange ``retry=30`` att undvika tillfälliga montera fel. (Ett annat värde rekommenderas i förgrunden monterar.)

| Önskade inställningar  | |
--- | --- 
``nointr``            | Alternativet ”nointr” är prioriterade för klienter med äldre kernlar (före April 2008) som har stöd för det här alternativet. Observera att alternativet ”intro” är standardvärdet.


## <a name="next-steps"></a>Nästa steg

När du har monterat klienter kan använda du dem för att fylla i backend-datalagring (core-filer). Referera till dessa dokument för mer information om ytterligare konfigurationsåtgärder:

* [Flytta data till klustret core filer](avere-vfxt-data-ingest.md) – hur du använder flera klienter och trådar för att effektivt ladda upp data
* [Anpassa kluster justering](avere-vfxt-tuning.md) -skräddarsy klusterinställningar så att de passar din arbetsbelastning
* [Hantera klustret](avere-vfxt-manage-cluster.md) -starta eller stoppa klustret och hantera noder
