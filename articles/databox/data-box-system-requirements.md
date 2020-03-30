---
title: Systemkrav för Microsoft Azure Data Box| Microsoft-dokument
description: Lär dig mer om programvaran och nätverkskraven för din Azure Data Box-enhet
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 07/11/2019
ms.author: alkohli
ms.openlocfilehash: e232ad131b1c0930afcf5e7e78b386aba2c9490b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259960"
---
# <a name="azure-data-box-system-requirements"></a>Systemkrav för Azure Data Box

I den här artikeln beskrivs de viktiga systemkraven för microsoft Azure Data Box och för klienterna som ansluter till datarutan. Vi rekommenderar att du granskar informationen noggrant innan du distribuerar datarutan och sedan refererar till den efter behov under distributionen och efterföljande åtgärd.

Systemkraven omfattar:

* **Programvarukrav för värdar som ansluter till Data Box** - beskriver plattformar som stöds, webbläsare för det lokala webbgränssnittet, SMB-klienter och eventuella ytterligare krav för värdar som kan ansluta till datarutan.
* **Nätverkskrav för databoxen** - ger information om nätverkskraven för optimal drift av databoxen.


## <a name="software-requirements"></a>Programvarukrav

Programvarukraven omfattar information om operativsystem som stöds, webbläsare som stöds för det lokala webbgränssnittet och SMB-klienter.

### <a name="supported-operating-systems-for-clients"></a>Operativsystem som stöds för klienter

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Filsystem som stöds för Linux-klienter

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Lagringskonton som stöds

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Lagringstyper som stöds

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Webbläsare som stöds

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Krav på nätverk

Datacentret måste ha höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE. Om en 10 GBE-anslutning inte är tillgänglig kan en datalänk på 1 GBE användas för att kopiera data, men kopieringshastigheterna påverkas.

### <a name="port-requirements"></a>Portkrav

I följande tabell visas de portar som måste öppnas i brandväggen för att möjliggöra SMB- eller NFS-trafik. I den här tabellen refererar *i* eller *inkommande* till den riktning från vilken inkommande klient begär åtkomst till enheten. *Utgående* eller *utgående* refererar till i vilken riktning databoxenheten skickar data externt, utöver distributionen: till exempel utgående till Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Nästa steg

* [Distribuera din Azure-databox](data-box-deploy-ordered.md)
