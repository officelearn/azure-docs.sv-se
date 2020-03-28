---
title: Montera klienter i Microsoft Azure FXT Edge Filer-klustret
description: Så här kan NFS-klientdatorer montera Azure FXT Edge Filer hybridlagringscache
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 43223db298e4ad170ea6d0687a342b3aee35500e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "80130775"
---
# <a name="tutorial-mount-the-cluster"></a>Självstudiekurs: Montera klustret

Den här självstudien lär dig hur du monterar NFS-klienter i Azure FXT Edge Filer-klustret. Klienter monterar de virtuella namnområdessökvägarna som du tilldelade när du lade till serverdelslagring.

Denna handledning lär:

> [!div class="checklist"]
> * Strategier för belastningsutjämning klienter över hela utbudet av klientinriktade IP-adresser
> * Så här skapar du en monteringssökväg från en klientvänd IP-adress och namnområdeskorsning
> * Vilka argument som ska användas i ett monteringskommando

Den här självstudien tar cirka 45 minuter att slutföra.

## <a name="steps-to-mount-the-cluster"></a>Steg för att montera klustret

Följ dessa steg för att ansluta klientdatorer till ditt Azure FXT Edge Filer-kluster.

1. Bestäm hur klienttrafiken mellan klusternoderna ska belastningsbalanseras. Läs [Balans klient belastning](#balance-client-load), nedan, för mer information.
1. Identifiera klustrets IP-adress och kopplingsväg för montering.
1. Bestäm den klientvända banan för fästet.
1. Utfärda [kommandot Montera](#use-recommended-mount-command-options)med lämpliga argument.

## <a name="balance-client-load"></a>Balansera klientbelastning

Om du vill balansera klientbegäranden mellan alla noder i klustret bör du montera klienter till alla klientinriktade IP-adresser. Det finns flera sätt att automatisera den här uppgiften.

Mer information om utjämning av DNS-belastning för klustret finns i [Konfigurera DNS för Azure FXT Edge Filer-klustret](fxt-configure-network.md#configure-dns-for-load-balancing). Om du vill använda den här metoden måste du underhålla en DNS-server, vilket inte förklaras i dessa artiklar.

En enklare metod för små installationer är att använda ett skript för att tilldela IP-adresser i hela intervallet vid klientmonteringstid.

Andra belastningsutjämningsmetoder kan vara lämpliga för stora eller komplicerade system. Kontakta din Microsoft-representant eller öppna en [supportbegäran om](fxt-support-ticket.md) hjälp. (Azure Load Balancer stöds för närvarande *inte* med Azure FXT Edge Filer.)

## <a name="create-the-mount-command"></a>Skapa kommandot montera

Från din klient ``mount`` mappar kommandot den virtuella servern (vserver) på Azure FXT Edge Filer-klustret till en sökväg i det lokala filsystemet.

Formatet är``mount <FXT cluster path> <local path> {options}``

Det finns tre element i monteringskommandot:

* klustersökväg - en kombination av IP-adress och namnområdeskorsningssökväg som beskrivs nedan
* lokal sökväg - sökvägen till klienten
* kommandotolk - (listat i [Använda rekommenderade monteringskommandoalternativ](#use-recommended-mount-command-options))

### <a name="create-the-cluster-path"></a>Skapa klustersökvägen

Klustersökvägen är en kombination av *vserver-IP-adressen* plus sökvägen till en *namnområdeskorsning*. Namnområdeskorsningen är en virtuell sökväg som du definierade när du [lade till lagringssystemet](fxt-add-storage.md#create-a-junction).

Om du till ``/fxt/files`` exempel använde som namnområdessökväg skulle dina klienter montera *IP_address*:/fxt/files till deras lokala monteringspunkt.

![Dialogrutan "Lägg till ny korsning" med /avere/files i namnområdessökvägen](media/fxt-mount/fxt-junction-example.png)

IP-adressen är en av de klientvända IP-adresser som definierats för vservern. Du hittar utbudet av klientinriktade IPs på två ställen i klustrets kontrollpanel:

* **VServers-tabell** (fliken Instrumentpanel) -

  ![Fliken Instrumentpanel på Kontrollpanelen med fliken VServer markerad i datatabellen under diagrammet och AVSNITTET IP-adress inringad](media/fxt-mount/fxt-ip-addresses-dashboard.png)

* **Sidan Inställningar för klientvändiga nätverk** -

  ![Inställningar > VServer > konfigurationssida för klientvänt nätverks med en cirkel runt avsnittet Adressintervall i tabellen för en viss vserver](media/fxt-mount/fxt-ip-addresses-settings.png)

Kombinera IP-adressen och namnområdessökvägen för att skapa klustersökvägen för kommandot Mount.

Exempel på kommandot klientmontering:``mount 10.0.0.12:/sd-access /mnt/fxt {options}``

### <a name="create-the-local-path"></a>Skapa den lokala sökvägen

Den lokala sökvägen för kommandot montera är upp till dig. Du kan ange vilken sökvägsstruktur du vill ha som en del av det virtuella namnområdet. Utforma ett namnområde och en lokal sökväg som är praktisk för klientarbetsflödet.

Mer information om det klientinriktade namnområdet finns i [namnområdesöversikten](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)för klusterkonfiguration .

Förutom sökvägarna, inkludera [de monteringskommandoalternativ](#use-recommended-mount-command-options) som beskrivs nedan när du monterar varje klient.

### <a name="use-recommended-mount-command-options"></a>Använd rekommenderade monteringskommandoalternativ

För att säkerställa en sömlös klientmontering, skicka dessa inställningar och argument i ditt monteringskommando:

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Nödvändiga inställningar | |
--- | ---
``hard`` | Mjuka fästen till Azure FXT Edge Filer-klustret är associerade med programfel och eventuell dataförlust.
``proto=netid`` | Det här alternativet stöder lämplig hantering av NFS-nätverksfel.
``mountproto=netid`` | Det här alternativet stöder lämplig hantering av nätverksfel för monteringsåtgärder.
``retry=n`` | Ställ ``retry=30`` in för att undvika tillfälliga monteringsfel. (Ett annat värde rekommenderas i förgrundsfästen.)

| Önskade inställningar  | |
--- | ---
``nointr``            | Om dina klienter använder äldre OS-kärnor (före april 2008) som stöder det här alternativet använder du det. Alternativet "intr" är standard.

## <a name="next-steps"></a>Nästa steg

När du har monterat klienter kan du testa arbetsflödet och komma igång med klustret.

Om du behöver flytta data till en ny cloud core filer, dra nytta av cachestrukturen med hjälp av parallella data som intar. Vissa strategier beskrivs i [Flytta data till ett vFXT-kluster](https://docs.microsoft.com/azure/avere-vfxt/avere-vfxt-data-ingest). (Avere vFXT för Azure är en molnbaserad produkt som använder cachelagringsteknik som påminner mycket lik Azure FXT Edge Filer.)

Läs [Maskinvarustatus för Övervaka Azure FXT Edge Filer](fxt-monitor.md) om du behöver felsöka maskinvaruproblem.
