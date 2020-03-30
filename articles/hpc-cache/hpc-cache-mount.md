---
title: Montera en Azure HPC-cache
description: Ansluta klienter till en Azure HPC-cachetjänst
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: d906ed9a1a55e936c6374806a9037085c47e3b01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73582221"
---
# <a name="mount-the-azure-hpc-cache"></a>Montera Azure HPC-cachen

NFS-klienter kan komma åt den med ett enkelt monteringskommando när cacheminnet har skapats.

Monteringskommandot består av två element:

* En av cachens monteringsadresser (listad på cacheöversiktssidan)
* Den virtuella namnområdessökvägen som du angav när du skapade lagringsmålet

![skärmbild av översiktssidan för Azure HPC-cacheinstansen, med en markeringsruta runt listan montera adresser längst ned till höger](media/hpc-cache-mount-addresses.png)

> [!NOTE] 
> Cachefästeadresserna motsvarar nätverksgränssnitt i cachens undernät. I en resursgrupp visas dessa nätverkskort med `-cluster-nic-` namn som slutar och ett tal. Ändra eller ta inte bort dessa gränssnitt, för eftersom cachen blir otillgänglig.

Sökvägarna för det virtuella namnområdet visas på sidan **Lagringsmål.** Klicka på ett enskilt målnamn för lagring om du vill visa dess information, inklusive aggregerade namnområdessökvägar som är associerade med det.

![skärmbild av cachens målpanel För lagring, med en markeringsruta runt en post i kolumnen Sökväg i tabellen](media/hpc-cache-view-namespace-paths.png)

## <a name="mount-command-syntax"></a>Kommandosyntax för fästen

Använd ett monteringskommando som följande:

> sudo montera *cache_mount_address*:/*namespace_path* *local_path* {*alternativ*}

Exempel:

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard
root@test-client:/tmp# 
```

När det här kommandot lyckas ska innehållet i lagringsexporten vara synligt i katalogen ``hpccache`` på klienten.

> [!NOTE] 
> Dina klienter måste kunna komma åt det virtuella nätverk och det undernät som finns i cacheminnet. Skapa till exempel virtuella klientdator i samma virtuella nätverk eller använd en slutpunkt, gateway eller annan lösning i det virtuella nätverket för åtkomst utifrån. Kom ihåg att inget annat kan finnas i cachens undernät.

### <a name="mount-command-options"></a>Kommandoalternativ för Montera

För ett robust klientfäste skickar du dessa inställningar och argument i monteringskommandot: 

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Rekommenderade inställningar för monteringskommando | |
--- | --- 
``hard`` | Mjuka fästen till Azure HPC-cache är associerade med programfel och eventuell dataförlust. 
``proto=netid`` | Det här alternativet stöder lämplig hantering av NFS-nätverksfel.
``mountproto=netid`` | Det här alternativet stöder lämplig hantering av nätverksfel för monteringsåtgärder.
``retry=n`` | Ställ ``retry=30`` in för att undvika tillfälliga monteringsfel. (Ett annat värde rekommenderas i förgrundsfästen.)

## <a name="next-steps"></a>Nästa steg

* Om du vill flytta data till cachens lagringsmål läser [du Fyll i ny Azure Blob-lagring](hpc-cache-ingest.md).
