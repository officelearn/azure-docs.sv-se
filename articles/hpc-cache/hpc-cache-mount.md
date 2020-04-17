---
title: Montera en Azure HPC-cache
description: Ansluta klienter till en Azure HPC-cachetjänst
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: a44232f06b455e20530271723e816c2117b339a0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458417"
---
# <a name="mount-the-azure-hpc-cache"></a>Montera Azure HPC-cachen

NFS-klienter kan komma åt den med `mount` ett enkelt kommando när cacheminnet har skapats. Kommandot ansluter en specifik sökväg för lagringsmål på Azure HPC-cachen till en lokal katalog på klientdatorn.

Kommandot montera består av följande element:

* En av cachens monteringsadresser (listad på cacheöversiktssidan)
* Den virtuella namnområdessökvägen som du angav när du skapade lagringsmålet
* Den lokala sökvägen som ska användas på klienten
* Kommandoparametrar som optimerar framgången för den här typen av NFS-fäste

Sidan **Montera instruktioner** för cacheminnet samlar in information och rekommenderade alternativ för dig och skapar ett prototypfästekommando som du kan kopiera. Läs verktyget För instruktioner för [montering finns](#use-the-mount-instructions-utility) i Mer information.

## <a name="prepare-clients"></a>Förbereda klienter

Kontrollera att dina klienter kan montera Azure HPC-cachen genom att följa riktlinjerna i det här avsnittet.

### <a name="provide-network-access"></a>Ge nätverksåtkomst

Klientdatorerna måste ha nätverksåtkomst till cachens virtuella nätverk och privata undernät.

Skapa till exempel virtuella klientdator i samma virtuella nätverk eller använd en slutpunkt, gateway eller annan lösning i det virtuella nätverket för åtkomst utifrån. (Kom ihåg att inget annat än själva cachen ska finnas i cachens undernät.)

### <a name="install-utilities"></a>Installera verktyg

Installera lämplig Linux-verktygsprogramvara för att stödja NFS-monteringskommandot:

* För Red Hat Enterprise Linux eller SuSE:`sudo yum install -y nfs-utils`
* För Ubuntu eller Debian:`sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>Skapa en lokal sökväg

Skapa en lokal katalogsökväg på varje klient för att ansluta till cachen. Skapa en sökväg för varje namnområdessökväg som du vill montera.

Exempel: `sudo mkdir -p /mnt/hpc-cache-1/target3`

Sidan [Montera instruktioner](#use-the-mount-instructions-utility) i Azure-portalen innehåller ett prototypkommando som du kan kopiera.

När du ansluter klientdatorn till cacheminnet associerar du sökvägen med en virtuell namnområdessökväg som representerar en lagringsmålexport. Skapa kataloger för var och en av de virtuella namnområdessökvägar som klienten ska använda.

## <a name="use-the-mount-instructions-utility"></a>Använd monteringsinstruktionsverktyget

Du kan använda sidan **Montera instruktioner** i Azure-portalen för att skapa ett kopieringsbart monteringskommando. Öppna sidan från avsnittet **Konfigurera** i cachevyn i portalen.

Innan du använder kommandot på en klient, se till att klienten uppfyller `mount` förutsättningarna och har den programvara som behövs för att använda NFS-kommandot enligt beskrivningen ovan i [Prepare-klienter](#prepare-clients).

![skärmbild av en Azure HPC-cacheinstans i portalen, med sidan Konfigurera > montera instruktioner inläst](media/mount-instructions.png)

Följ den här proceduren för att skapa kommandot montera.

1. Anpassa **sökvägsfältet För klient.** Det här fältet ger ett exempelkommando som du kan använda för att skapa en lokal sökväg på klienten. Klienten kommer åt innehållet från Azure HPC-cachen lokalt i den här katalogen.

   Klicka på fältet och redigera kommandot så att det innehåller önskat katalognamn. Namnet visas i slutet av strängen efter`sudo mkdir -p`

   ![skärmbild av klientsökvägsfältet med markören placerad i slutet](media/mount-edit-client.png)

   När du har redigerat fältet uppdateras kommandot Montera längst ned på sidan med den nya klientsökvägen.

1. Välj **cachemonterad adress** i listan. Den här menyn listar alla [cachens klientmonteringspunkter](#find-mount-command-components).

   Balansera klientbelastningen över alla tillgängliga monteringsadresser för bättre cacheprestanda.

   ![skärmbild av cachemontera adressfältet, med väljare som visar tre IP-adresser att välja mellan](media/mount-select-ip.png)

1. Välj den **virtuella namnområdessökväg** som ska användas för klienten. Dessa sökvägar länkar till export på backend-lagringssystemet.

   ![skärmbild av fältet namnområdessökvägar, med väljaren öppen](media/mount-select-target.png)

   Du kan visa och ändra virtuella namnområdessökvägar på portalsidan Lagringsmål. Läs [Lägg till lagringsmål](hpc-cache-add-storage.md) för att se hur.

   Mer information om Azure HPC-cachens aggregerade namnområdesfunktion läser [du Planera det aggregerade namnområdet](hpc-cache-namespace.md).

1. **Kommandofältet Montera** i steg tre fylls automatiskt i med ett anpassat monteringskommando som använder monteringsadressen, den virtuella namnområdessökvägen och klientsökvägen som du anger i föregående fält.

   Klicka på kopieringssymbolen till höger i fältet om du vill kopiera den automatiskt till Urklipp.

   ![skärmbild av fältet namnområdessökvägar, med väljaren öppen](media/mount-command-copy.png)

1. Använd kommandot kopierad montering på klientdatorn för att ansluta det till Azure HPC-cachen. Du kan utfärda kommandot direkt från klientkommandoraden eller inkludera kommandot Mount i ett klientinstallationsskript eller en mall.

## <a name="understand-mount-command-syntax"></a>Förstå kommandot syntax för fästning

Kommandot Montera har följande formulär:

> sudo montera {*alternativ*} *cache_mount_address*:/*namespace_path* *local_path*

Exempel:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
root@test-client:/tmp#
```

När det här kommandot lyckas visas innehållet i lagringsexporten i katalogen ``hpccache`` på klienten.

### <a name="mount-command-options"></a>Kommandoalternativ för Montera

För ett robust klientfäste skickar du dessa inställningar och argument i monteringskommandot:

> montera -o hård,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}

| Rekommenderade inställningar för monteringskommando | |
--- | ---
``hard`` | Mjuka fästen till Azure HPC-cache är associerade med programfel och eventuell dataförlust.
``proto=tcp`` | Det här alternativet stöder lämplig hantering av NFS-nätverksfel.
``mountproto=tcp`` | Det här alternativet stöder lämplig hantering av nätverksfel för monteringsåtgärder.
``retry=<value>`` | Ställ ``retry=30`` in för att undvika tillfälliga monteringsfel. (Ett annat värde rekommenderas i förgrundsfästen.)

### <a name="find-mount-command-components"></a>Hitta monteringskommandokomponenter

Om du vill skapa ett **monteringskommando** utan att använda sidan Montera instruktioner kan du hitta monteringsadresserna på **sidan** Översikt över cacheminnet och sökvägarna för det virtuella namnområdet på **målsidan För lagring.**

![skärmbild av översiktssidan för Azure HPC-cacheinstansen, med en markeringsruta runt listan montera adresser längst ned till höger](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> Cachefästeadresserna motsvarar nätverksgränssnitt i cachens undernät. I en resursgrupp visas dessa nätverkskort med `-cluster-nic-` namn som slutar och ett tal. Ändra eller ta inte bort dessa gränssnitt, för eftersom cachen blir otillgänglig.

Sökvägarna för det virtuella namnområdet visas på informationssidan för varje lagringsmål. Klicka på ett enskilt målnamn för lagring om du vill visa dess information, inklusive aggregerade namnområdessökvägar som är associerade med det.

![skärmbild av ett informationsblad för ett lagringsmål (rubriken "Uppdatera lagringsmål"). Det finns en markeringsruta runt en post i kolumnen Virtuellt namnområde i tabellen](media/hpc-cache-view-namespace-paths.png)

## <a name="next-steps"></a>Nästa steg

* Om du vill flytta data till cachens lagringsmål läser [du Fyll i ny Azure Blob-lagring](hpc-cache-ingest.md).
