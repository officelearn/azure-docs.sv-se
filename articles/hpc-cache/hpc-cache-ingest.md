---
title: Flytta data till en molnbehållare för Azure HPC-cache
description: Fylla i Azure Blob-lagring för användning med Azure HPC-cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: rohogue
ms.openlocfilehash: a5625341e3dd279d93a59c57cd3325245351723e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271881"
---
# <a name="move-data-to-azure-blob-storage"></a>Flytta data till Azure Blob-lagring

Om arbetsflödet innehåller att flytta data till Azure Blob-lagring kontrollerar du att du använder en effektiv strategi. Du kan antingen förinläsa data i en ny Blob-behållare innan du definierar den som ett lagringsmål, eller lägga till behållaren och sedan kopiera dina data med Azure HPC-cache.

I den här artikeln beskrivs de bästa sätten att flytta data till Blob-lagring för användning med Azure HPC-cache.

Tänk på dessa fakta:

* Azure HPC Cache använder ett specialiserat lagringsformat för att organisera data i Blob-lagring. Det är därför som ett Blob-lagringsmål antingen måste vara en ny, tom behållare eller en Blob-behållare som tidigare användes för Azure HPC-cachedata. <!--([Avere vFXT for Azure](https://azure.microsoft.com/services/storage/avere-vfxt/) also uses this cloud file system.)-->

* Det är effektivare att kopiera data via Azure HPC-cachen till ett backend-lagringsmål när du använder flera klienter och parallella åtgärder. Ett enkelt kopieringskommando från en klient flyttar data långsamt.

Ett Python-baserat verktyg är tillgängligt för att läsa in innehåll i en Blob-lagringsbehållare. Läs [Förinläsningsdata i Blob-lagring](#pre-load-data-in-blob-storage-with-clfsload) om du vill veta mer.

Om du inte vill använda inläsningsverktyget, eller om du vill lägga till innehåll i ett befintligt lagringsmål, följer du de parallella datatipsen i [Kopiera data via Azure HPC-cachen](#copy-data-through-the-azure-hpc-cache).

## <a name="pre-load-data-in-blob-storage-with-clfsload"></a>Förinläsningsdata i Blob-lagring med CLFSLoad

Du kan använda parametrarna <!--[Avere CLFSLoad](https://aka.ms/avere-clfsload)--> Avere CLFSLoad-verktyget för att kopiera data till en ny Blob-lagringsbehållare innan du lägger till den som ett lagringsmål. Det här verktyget körs på ett enda Linux-system och skriver data i det proprietära format som behövs för Azure HPC-cache. CLFSLoad är det mest effektiva sättet att fylla i en Blob-lagringsbehållare för användning med cachen.

Verktyget Avere CLFSLoad är tillgängligt på begäran från ditt Azure HPC-cacheteam. Fråga ditt team kontakt för det, eller öppna en [supportbiljett](hpc-cache-support-ticket.md) för att begära hjälp.

Det här alternativet fungerar endast med nya, tomma behållare. Skapa behållaren innan du använder Avere CLFSLoad.

Detaljerad information ingår i Avere CLFSLoad-distributionen, som är tillgänglig på begäran från Azure HPC Cache-teamet. <!-- [Avere CLFSLoad readme](https://github.com/microsoft/Avere-CLFSLoad/blob/master/README.md). --><!-- caution literal link -->

En allmän översikt över processen:

1. Förbered ett Linux-system (VM eller fysiskt) med Python version 3.6 eller senare. Python 3.7 rekommenderas för bättre prestanda.
1. Installera Programvaran Avere-CLFSLoad på Linux-systemet.
1. Kör överföringen från Linux-kommandoraden.

Verktyget Avere CLFSLoad behöver följande information:

* Lagringskonto-ID:et som innehåller din Blob-lagringsbehållare
* Namnet på den tomma Blob-lagringsbehållaren
* En SAS-token (Shared Access Signature) som gör att verktyget kan skriva till behållaren
* En lokal sökväg till datakällan – antingen en lokal katalog som innehåller data som ska kopieras eller en lokal sökväg till ett monterat fjärrsystem med data

<!-- The requirements are explained in detail in the [Avere CLFSLoad readme](https://aka.ms/avere-clfsload). -->

## <a name="copy-data-through-the-azure-hpc-cache"></a>Kopiera data via Azure HPC-cachen

Om du inte vill använda avere CLFSLoad-verktyget, eller om du vill lägga till en stor mängd data i ett befintligt Blob-lagringsmål, kan du kopiera det via cachen. Azure HPC Cache är utformad för att betjäna flera klienter samtidigt, så för att kopiera data via cachen bör du använda parallella skrivningar från flera klienter.

![Diagram som visar dataförflyttning med flera klienter: Längst upp till vänster har en ikon för lokal maskinvarulagring flera pilar som kommer från den. Pilarna pekar på fyra klientdatorer. Från varje klientdator pekar tre pilar mot Azure HPC-cachen. Från Azure HPC-cachen pekar flera pilar på Blob-lagring.](media/hpc-cache-parallel-ingest.png)

De ``cp`` ``copy`` kommandon eller som du vanligtvis använder för att överföra data från ett lagringssystem till ett annat är processer med en tråd som bara kopierar en fil i taget. Detta innebär att filservern intag av endast en fil i taget - vilket är ett slöseri med cachens resurser.

I det här avsnittet beskrivs strategier för att skapa ett filkopieringssystem med flera klienter med flera trådar för att flytta data till Blob-lagring med Azure HPC-cache. Det förklarar filöverföring begrepp och beslutspunkter som kan användas för effektiv datakopiering med hjälp av flera klienter och enkla kopieringskommandon.

Det förklarar också några verktyg som kan hjälpa. Verktyget ``msrsync`` kan användas för att delvis automatisera processen att dela upp en datauppsättning i buckets och använda rsync-kommandon. Skriptet ``parallelcp`` är ett annat verktyg som läser källkatalogen och utfärdar kopieringskommandon automatiskt.

### <a name="strategic-planning"></a>Strategisk planering

När du skapar en strategi för att kopiera data parallellt bör du förstå kompromisserna i filstorlek, filantal och katalogdjup.

* När filerna är små är det mått av intresse filer per sekund.
* När filerna är stora (10MiBi eller mer) är måttet av intresse byte per sekund.

Varje kopieringsprocess har en dataflödeshastighet och en överförd filhastighet, som kan mätas med tidsinställning av kopieringskommandots längd och factoring av filstorlek och filantal. Att förklara hur du mäter priserna ligger utanför det här dokumentets räckvidd, men det är absolut nödvändigt att förstå om du kommer att hantera små eller stora filer.

Strategier för parallella data som förtärs med Azure HPC-cache inkluderar:

* Manuell kopiering - Du kan manuellt skapa en kopia med flera trådar på en klient genom att köra mer än ett kopieringskommando samtidigt i bakgrunden mot fördefinierade uppsättningar av filer eller sökvägar. Läs [Azure HPC Cache data inta - manuell kopiering metod](hpc-cache-ingest-manual.md) för mer information.

* Delvis automatiserad ``msrsync``  -  ``msrsync`` kopiering med är ett omslagsverktyg som kör flera parallella ``rsync`` processer. Mer information finns [i Azure HPC Cache data inning - msrsync metod](hpc-cache-ingest-msrsync.md).

* Skriptkopiering ``parallelcp`` med - Lär dig hur du skapar och kör ett parallellkopieringsskript i [Azure HPC Cache-data som används - parallellkopieringsskriptmetod](hpc-cache-ingest-parallelcp.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat lagringsutrymmet får du lära dig hur klienter kan montera cacheminnet.

* [Få tillgång till Azure HPC-cachesystemet](hpc-cache-mount.md)
