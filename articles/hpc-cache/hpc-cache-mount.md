---
title: Montera en Azure HPC-cache
description: Ansluta klienter till en Azure HPC-cachetjänst
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: rohogue
ms.openlocfilehash: f176e30cfaf9a52e4f58091b7fc76098a4c88a48
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657373"
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

Skapa en lokal katalogsökväg på varje klient för att ansluta till cachen. Skapa en sökväg för varje lagringsmål som du vill montera.

Exempel: `sudo mkdir -p /mnt/hpc-cache-1/target3`

## <a name="use-the-mount-instructions-utility"></a>Använd monteringsinstruktionsverktyget

Öppna sidan **Montera instruktioner** från avsnittet **Konfigurera** i cachevyn i Azure-portalen.

![skärmbild av en Azure HPC-cacheinstans i portalen, med sidan Konfigurera > montera instruktioner inläst](media/mount-instructions.png)

Kommandosidan montera innehåller information om klientmonteringsprocessen och förutsättningarna, plus fält som du kan använda för att skapa ett kopieringsbart monteringskommando.

Så här använder du den här sidan:

<!--1.  In step one of **Mounting your file system**, enter the path that the client will use to access the Azure HPC Cache storage target.

   * This path is local to the client.
   * After you provide the directory name, the field populates with a command you can copy. Use this command on the client directly or in a setup script to create the directory path on the client VM. -->

1. Granska klientens förutsättningar och installera de verktyg `mount` som behövs för att använda NFS-kommandot enligt beskrivningen ovan i [Prepare-klienter](#prepare-clients).

1. Steg ett av **Montering av filsystemet**<!-- label will change --> ger ett exempelkommando för att skapa den lokala sökvägen på klienten. Det här är sökvägen som klienten använder för att komma åt innehållet från Azure HPC-cachen.

   Observera sökvägsnamnet så att du kan ändra det i kommandot om det behövs.

1. I steg två väljer du en av de tillgängliga IP-adresserna. Alla cachens [klientmonteringspunkter](#find-mount-command-components) visas här. Se till att du har ett system för att balansera belastningen mellan alla IP-adresser.

1. Fältet i steg tre fylls automatiskt med ett prototypmonteringskommando. Klicka på kopieringssymbolen till höger i fältet om du vill kopiera den automatiskt till Urklipp.

   > [!NOTE]
   > Kontrollera kopieringskommandot innan du använder det. Du kan behöva anpassa sökvägen för klientmonter och den virtuella namnområdessökvägen för lagringsmål, som ännu inte är valbara i det här gränssnittet. Du bör också uppdatera kommandoalternativen montera för att återspegla de [rekommenderade alternativen](#mount-command-options) nedan. Läs [Förstå kommandot syntax](#understand-mount-command-syntax) för att få hjälp.

1. Använd kommandot kopierad montering (med redigeringar om det behövs) på klientdatorn för att ansluta det till lagringsmålet på Azure HPC-cachen. Du kan utfärda kommandot direkt från klientkommandoraden eller inkludera kommandot Mount i ett klientinstallationsskript eller en mall.

## <a name="understand-mount-command-syntax"></a>Förstå kommandot syntax för fästning

Kommandot Montera har följande formulär:

> sudo montera *cache_mount_address*:/*namespace_path* *local_path* {*alternativ*}

Exempel:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -o hard,proto=tcp,mountproto=tcp,retry=30
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

Om du vill skapa ett **monteringskommando** utan att använda sidan Montera instruktioner kan du hitta monteringsadresserna på **sidan** Översikt över cacheminnet och sökvägarna för det virtuella namnområdet på sidan **Lagringsmål.**

![skärmbild av översiktssidan för Azure HPC-cacheinstansen, med en markeringsruta runt listan montera adresser längst ned till höger](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> Cachefästeadresserna motsvarar nätverksgränssnitt i cachens undernät. I en resursgrupp visas dessa nätverkskort med `-cluster-nic-` namn som slutar och ett tal. Ändra eller ta inte bort dessa gränssnitt, för eftersom cachen blir otillgänglig.

Sökvägarna för det virtuella namnområdet visas på sidan **Lagringsmål.** Klicka på ett enskilt målnamn för lagring om du vill visa dess information, inklusive aggregerade namnområdessökvägar som är associerade med det.

![skärmbild av cachens målpanel För lagring, med en markeringsruta runt en post i kolumnen Sökväg i tabellen](media/hpc-cache-view-namespace-paths.png)

## <a name="next-steps"></a>Nästa steg

* Om du vill flytta data till cachens lagringsmål läser [du Fyll i ny Azure Blob-lagring](hpc-cache-ingest.md).
