---
title: Montera en Azure HPC-cache
description: Ansluta klienter till en Azure HPC cache-tjänst
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: v-erkell
ms.openlocfilehash: 0ccd256ed399fff7c364883ada45d2d15c75a120
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70775356"
---
# <a name="mount-the-azure-hpc-cache"></a>Montera Azure HPC-cachen

När cachen har skapats kan NFS-klienter komma åt den med ett enkelt monterings kommando.

Använd de monterings adresser som anges på sidan cache-översikt och den virtuella namn rymds-sökväg som du angav när du skapade lagrings målet. 

![skärm bild av översikts sidan för Azure HPC-instansen med en markerings ruta runt monterings adress listan längst ned till höger](media/mount-addresses.png)

> [!NOTE] 
> Cachens cache-adresser motsvarar nätverks gränssnitten i cachens undernät. Dessa nätverkskort visas i resurs gruppen med namn som slutar `-cluster-nic-` med och en siffra. Ändra inte eller ta bort dessa gränssnitt, annars blir cachen otillgänglig.

De virtuella namn Rymdernas sökvägar visas på sidan **lagrings mål** . 
![skärm bild av cachens lagrings mål panel med en markerings ruta runt en post i kolumnen sökväg i tabellen](media/storage-target-path.png)

## <a name="mount-command-syntax"></a>Montera kommandosyntax

Använd ett monterings kommando som följande:

> sudo Mount *cache_mount_address*:/*namespace_path* *local_path* {*Options*}

Exempel: 

```
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount 10.0.0.28:/blob-demo-0722 ./hpccache/ -orw,tcp,mountproto=tcp,vers3,hard,intr
root@test-client:/tmp# 
```

När det här kommandot har slutförts ska innehållet i lagrings exporten visas i ``hpccache`` katalogen på klienten. 

> [!NOTE] 
> Klienterna måste kunna få åtkomst till det virtuella nätverket och under nätet som är hus din cache. Du kan till exempel skapa virtuella klient datorer i samma virtuella nätverk eller använda en slut punkt, gateway eller annan lösning i det virtuella nätverket för åtkomst från utanför. Kom ihåg att det inte finns något annat som kan finnas i cachens undernät.

### <a name="mount-command-options"></a>Monterings kommando alternativ

För en robust klient montering ska du överföra dessa inställningar och argument i monterings kommandot: 

``mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}``

| Rekommenderade monterings kommando inställningar | |
--- | --- 
``hard`` | Mjuka monteringar till vFXT-klustret är kopplade till program fel och eventuell data förlust. 
``proto=netid`` | Det här alternativet stöder lämplig hantering av NFS-nätverks fel.
``mountproto=netid`` | Det här alternativet stöder lämplig hantering av nätverks fel för monterings åtgärder.
``retry=n`` | Ange ``retry=30`` för att undvika tillfälliga monterings problem. (Ett annat värde rekommenderas i förgrunds monteringar.)

## <a name="next-steps"></a>Nästa steg

* Om du vill flytta data till cachens lagrings mål läser du [fylla i ny Azure Blob Storage](hpc-cache-ingest.md).
