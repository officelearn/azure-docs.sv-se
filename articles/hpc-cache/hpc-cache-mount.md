---
title: Montera en Azure HPC-cache
description: Ansluta klienter till en Azure HPC cache-tjänst
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: d906ed9a1a55e936c6374806a9037085c47e3b01
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582221"
---
# <a name="mount-the-azure-hpc-cache"></a>Montera Azure HPC-cachen

När cachen har skapats kan NFS-klienter komma åt den med ett enkelt monterings kommando.

Monterings kommandot består av två element:

* Ett av cachens monterings adresser (visas på sidan cache-översikt)
* Den virtuella namn områdes Sök vägen som du angav när du skapade lagrings målet

![skärm bild av översikts sidan för Azure HPC-instansen med en markerings ruta runt monterings adress listan längst ned till höger](media/hpc-cache-mount-addresses.png)

> [!NOTE] 
> Cachens cache-adresser motsvarar nätverks gränssnitten i cachens undernät. I en resurs grupp visas dessa nätverkskort med namn som slutar på `-cluster-nic-` och ett tal. Ändra inte eller ta bort dessa gränssnitt, annars blir cachen otillgänglig.

De virtuella namn Rymdernas sökvägar visas på sidan **lagrings mål** . Klicka på ett enskilt lagrings mål namn om du vill se information om den, inklusive sammansatta namn rymds sökvägar.

![skärm bild av cachens lagrings mål panel med en markerings ruta runt en post i kolumnen sökväg i tabellen](media/hpc-cache-view-namespace-paths.png)

## <a name="mount-command-syntax"></a>Montera kommandosyntax

Använd ett monterings kommando som följande:

> sudo Mount *cache_mount_address*:/*namespace_path* *local_path* {*Options*}

Exempel:

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard
root@test-client:/tmp# 
```

När det här kommandot har slutförts ska innehållet i lagrings exporten visas i ``hpccache``-katalogen på klienten.

> [!NOTE] 
> Klienterna måste kunna få åtkomst till det virtuella nätverket och under nätet som är hus din cache. Du kan till exempel skapa virtuella klient datorer i samma virtuella nätverk eller använda en slut punkt, gateway eller annan lösning i det virtuella nätverket för åtkomst från utanför. Kom ihåg att det inte finns något annat som kan finnas i cachens undernät.

### <a name="mount-command-options"></a>Monterings kommando alternativ

För en robust klient montering ska du överföra dessa inställningar och argument i monterings kommandot: 

``mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Rekommenderade monterings kommando inställningar | |
--- | --- 
``hard`` | Mjuka monteringar till Azure HPC cache är kopplade till program haverier och eventuell data förlust. 
``proto=netid`` | Det här alternativet stöder lämplig hantering av NFS-nätverks fel.
``mountproto=netid`` | Det här alternativet stöder lämplig hantering av nätverks fel för monterings åtgärder.
``retry=n`` | Ange ``retry=30`` för att undvika tillfälliga monterings problem. (Ett annat värde rekommenderas i förgrunds monteringar.)

## <a name="next-steps"></a>Nästa steg

* Om du vill flytta data till cachens lagrings mål läser du [fylla i ny Azure Blob Storage](hpc-cache-ingest.md).
