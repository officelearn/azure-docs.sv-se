---
title: Microsoft Azure Data Box-enhet system krav | Microsoft Docs
description: Lär dig mer om programvaran och nätverkskraven för din Azure Data Box-enhet
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/20/2020
ms.author: alkohli
ms.openlocfilehash: 496069ebf64340bc55f03df8dc15304b4888bec0
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86200313"
---
# <a name="azure-data-box-system-requirements"></a>Azure Data Box system krav

I den här artikeln beskrivs viktiga system krav för din Microsoft Azure Data Box-enhet och för klienter som ansluter till Data Box-enhet. Vi rekommenderar att du läser informationen noggrant innan du distribuerar din Data Box-enhet och sedan går tillbaka till den vid behov under distributionen och efterföljande åtgärder.

System kraven är:

* **Program varu krav för värdar som ansluter till data Box-enhet** – beskriver de plattformar som stöds, webbläsare för det lokala webb gränssnittet, SMB-klienter och eventuella ytterligare krav för värdar som kan ansluta till data Box-enhet.
* **Nätverks krav för data Box-enhet** – ger information om nätverks kraven för den optimala driften av data Box-enhet.


## <a name="software-requirements"></a>Programvarukrav

Program varu kraven innehåller information om de operativ system som stöds, vilka webbläsare som stöds för det lokala webb gränssnittet och SMB-klienter.

### <a name="supported-operating-systems-for-clients"></a>Operativsystem som stöds för klienter

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-filesystems-for-linux-clients"></a>Fil system som stöds för Linux-klienter

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]


> [!IMPORTANT] 
> Anslutning till Data Box-enhet resurser stöds inte via REST för export order. 

### <a name="supported-storage-accounts"></a>Lagringskonton som stöds

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]


### <a name="supported-storage-types"></a>Lagringstyper som stöds

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Webbläsare som stöds

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Nätverks krav

Datacentret måste ha höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE. Om en 10-GbE-anslutning inte är tillgänglig kan en 1 – GbE-datalänk användas för att kopiera data, men kopierings hastigheten påverkas.

### <a name="port-requirements"></a>Portkrav

I följande tabell visas de portar som måste öppnas i brand väggen för att tillåta SMB-eller NFS-trafik. I den här tabellen avser *i* eller *inkommande* den riktning från vilken inkommande klient begär åtkomst till din enhet. *Out* eller *utgående* avser i vilken riktning din data Box-enhet enhet skickar data externt, utöver distributionen: till exempel utgående till Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Nästa steg

* [Distribuera Azure Data Box](data-box-deploy-ordered.md)
