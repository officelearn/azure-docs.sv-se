---
title: Monteras klienter i Microsoft Azure FXT Edge Filer klustret
description: Hur NFS-klientdatorer kan montera Azure FXT Edge Filer hybrid storage cache
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 5471bf4041275d5988414def99dd2130f51fbb80
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67828024"
---
# <a name="tutorial-mount-the-cluster"></a>Självstudier: Montera klustret

Den här självstudien lär du dig hur du monterar NFS-klienterna i Azure FXT Edge Filer-klustret. Klienter montera virtuella namnområde sökvägarna som du valde när du har lagt till backend-lagring. 

Den här självstudien Lär: 

> [!div class="checklist"]
> * Strategier för att läsa in belastningsutjämning klienter över olika klientinriktade IP-adresser
> * Hur du skapar en monteringssökväg från klientinriktade IP-adress och namnområde knutpunkt
> * Vilka argument som ska användas i ett mount-kommando

Den här kursen tar cirka 45 minuter att slutföra.

## <a name="steps-to-mount-the-cluster"></a>Steg för att montera klustret

Följ dessa steg för att ansluta klientdatorer till ditt kluster i Azure FXT Edge Filer.

1. Bestäm hur du belastningsutjämnar klienttrafik mellan klusternoderna. Läs [saldo klientbelastningen](#balance-client-load)nedan för information. 
1. Identifiera kluster IP-adress och knutpunkt sökvägen att montera.
1. Visa klientinriktade sökvägen för monterings.
1. Problem i [monteringskommando](#use-recommended-mount-command-options), med rätt argument.

## <a name="balance-client-load"></a>Belastningsutjämna klientbelastningen

För att belastningsutjämna klientbegäranden mellan noderna i klustret, ska du ansluta klienter till en fullständig uppsättning klientinriktade IP-adresser. Det finns flera sätt att automatisera den här uppgiften.

Läs om resursallokering DNS belastningsutjämning för klustret, [konfigurera DNS för Azure FXT Edge Filer klustret](fxt-configure-network.md#configure-dns-for-load-balancing). Om du vill använda den här metoden måste du ha en DNS-server som inte beskrivs i de här artiklarna.

En enklare metod för små installationer är att använda ett skript för att tilldela IP-adresser i hela intervallet på monteringspunkten klienttid. 

Andra metoder för belastningsutjämning kan vara lämpligt för stora eller komplicerade system. Kontakta ditt Microsoft-representativa eller öppna en [supportförfrågan](fxt-support-ticket.md) om du behöver hjälp. (Azure Load Balancer är för närvarande *stöds inte* med Azure FXT Edge Filer.)

## <a name="create-the-mount-command"></a>Skapa mount-kommando 

Från klienten den ``mount`` kommandot mappar den virtuella servern (vserver) på klustret Azure FXT Edge Filer till en sökväg i det lokala filsystemet. 

Formatet är ``mount <FXT cluster path> <local path> {options}``

Det finns tre element i mount-kommandot: 

* kluster-sökväg - en kombination av IP-adress och namnområde knutpunkt sökväg som beskrivs nedan
* lokal sökväg - sökväg på klienten 
* Montera kommandoalternativ - (anges i [bör du använda kommandot monteringsalternativ](#use-recommended-mount-command-options))

### <a name="create-the-cluster-path"></a>Skapa kluster-sökväg

Kluster-sökvägen är en kombination av vserver *IP-adress* plus sökvägen till en *namnområde knutpunkt*. Knutpunkt för namnområdet är en virtuell sökväg som du angav när du [har lagts till lagringssystemet](fxt-add-storage.md#create-a-junction).

Exempel: Om du använde ``/fxt/files`` som sökväg för namnområdet klienterna monterar *IP-adress*: / fxt/filer till deras lokala monteringspunkt. 

![”Lägg till ny knutpunkt” dialogen med/avere/filer i sökvägsfältet namnområde](media/fxt-mount/fxt-junction-example.png)

IP-adressen är en av de IP-adresser för klientinriktade som definierats för vserver. Du kan hitta intervallet för IP-adresser på två platser i klustret på Kontrollpanelen för klientinriktade:

* **VServers** tabell (fliken instrumentpanel) - 

  ![Fliken instrumentpanel i Kontrollpanelen med fliken VServer markerad i tabellen nedan i diagrammet och IP-adressavsnitt inringade](media/fxt-mount/fxt-ip-addresses-dashboard.png)

* **Inför klientnätverk** inställningssidan - 

  ![Inställningar > VServer > konfigurationssidan för klienten som riktas mot nätverket med en inringad avsnittet adressintervall i tabellen för en viss vserver](media/fxt-mount/fxt-ip-addresses-settings.png)

Kombinera IP-adressen och namnområdessökvägen för att bilda klustret sökvägen för monteringskommandot. 

Klienten montera kommando: ``mount 10.0.0.12:/sd-access /mnt/fxt {options}``

### <a name="create-the-local-path"></a>Skapa den lokala sökvägen

Den lokala sökvägen för monteringskommandot är upp till dig. Du kan ange valfri sökväg-struktur som du vill som en del av det virtuella namnområdet. Skapa ett namnområde och en lokal sökväg som passar ditt klient-arbetsflöde. 

Mer information om klientinriktade namnområdet finns i konfigurationsguiden för klustret [namnområde översikt](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html).

Förutom sökvägar, inkludera den [montera kommandoalternativ](#use-recommended-mount-command-options) beskrivs nedan när du monterar varje klient.

### <a name="use-recommended-mount-command-options"></a>Använd kommandot för rekommenderade monteringsalternativ

För att säkerställa en smidig klienten montering, skicka dessa inställningar och argumenten i mount-kommando: 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Nödvändiga inställningar | |
--- | --- 
``hard`` | Mjuk monterar till klustret Azure FXT Edge Filer är associerade med programfel och förlust av data. 
``proto=netid`` | Det här alternativet stöder lämplig hantering av NFS-nätverksfel.
``mountproto=netid`` | Det här alternativet stöder lämplig hantering av nätverksfel för mount-åtgärder.
``retry=n`` | Ange ``retry=30`` att undvika tillfälliga montera fel. (Ett annat värde rekommenderas i förgrunden monterar.)

| Önskade inställningar  | |
--- | --- 
``nointr``            | Om klienterna använder äldre OS-kernel (före April 2008) som har stöd för det här alternativet kan du använda den. Alternativet ”intro” är standardvärdet.

## <a name="next-steps"></a>Nästa steg

När du har monterat klienter, du kan testa ditt arbetsflöde och kom igång med ditt kluster.

Om du behöver flytta data till en ny cloud core filer kan dra nytta av cache-strukturen med hjälp av parallella mata in. Några strategier beskrivs i [flytta data till ett kluster med vFXT](https://docs.microsoft.com/azure/avere-vfxt/avere-vfxt-data-ingest). (Avere vFXT för Azure är en molnbaserad produkt som använder cachelagringsteknik liknar Azure FXT Edge-Filer.)

Läs [övervaka Azure FXT Edge Filer maskinvarustatus](fxt-monitor.md) om du behöver felsöka eventuella problem med hårdvaran. 
