---
title: Montera en Azure HPC-cache (för hands version)
description: Ansluta klienter till en Azure HPC cache-tjänst
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: v-erkell
ms.openlocfilehash: 3b7a59afa0dea300e200b953d045d38218e99b22
ms.sourcegitcommit: a19bee057c57cd2c2cd23126ac862bd8f89f50f5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/23/2019
ms.locfileid: "71180921"
---
# <a name="mount-the-azure-hpc-cache-preview"></a>Montera Azure HPC cache (för hands version)

När cachen har skapats kan NFS-klienter komma åt den med ett enkelt monterings kommando.

Använd de monterings adresser som anges på sidan cache-översikt och den virtuella namn rymds-sökväg som du angav när du skapade lagrings målet. 

![skärm bild av översikts sidan för Azure HPC-instansen med en markerings ruta runt monterings adress listan längst ned till höger](media/hpc-cache-mount-addresses.png)

> [!NOTE] 
> Cachens cache-adresser motsvarar nätverks gränssnitten i cachens undernät. Dessa nätverkskort visas i resurs gruppen med namn som slutar `-cluster-nic-` med och en siffra. Ändra inte eller ta bort dessa gränssnitt, annars blir cachen otillgänglig.

De virtuella namn Rymdernas sökvägar visas på sidan **lagrings mål** . Klicka på mål namnet om du vill se information, inklusive den sammanställda sökvägen (eller sökvägar) som är kopplad till den. 
![skärm bild av cachens lagrings mål panel med en markerings ruta runt en post i kolumnen sökväg i tabellen](media/hpc-cache-view-namespace-paths.png)

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
``hard`` | Mjuka monteringar till Azure HPC cache är kopplade till program haverier och eventuell data förlust. 
``proto=netid`` | Det här alternativet stöder lämplig hantering av NFS-nätverks fel.
``mountproto=netid`` | Det här alternativet stöder lämplig hantering av nätverks fel för monterings åtgärder.
``retry=n`` | Ange ``retry=30`` för att undvika tillfälliga monterings problem. (Ett annat värde rekommenderas i förgrunds monteringar.)

## <a name="next-steps"></a>Nästa steg

* Om du vill flytta data till cachens lagrings mål läser du [fylla i ny Azure Blob Storage](hpc-cache-ingest.md).
