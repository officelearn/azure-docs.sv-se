---
title: Microsoft Azure Data Box Tunga systemkrav| Microsoft-dokument
description: Lär dig mer om programvaru- och nätverkskraven för din Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260077"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Azure Data Box Tunga systemkrav

I den här artikeln beskrivs de viktiga systemkraven för din Azure Data Box Heavy-enhet och för klienter som ansluter till enheten. Vi rekommenderar att du granskar informationen noggrant innan du distribuerar databoxen tung och sedan hänvisar tillbaka till den efter behov under distributionen och efterföljande åtgärd.

Systemkraven omfattar:

* **Programvarukrav för värdar som ansluter till Data Box Heavy** - beskriver plattformar som stöds, webbläsare för det lokala webbgränssnittet, SMB-klienter och eventuella ytterligare krav för värdar som kan ansluta till databoxen.
* **Nätverkskrav för Data Box Heavy** - ger information om nätverkskraven för optimal drift av Data Box Heavy-enheten.

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

Datacentret måste ha höghastighetsnätverk. För snabbaste kopieringshastigheter kan två 40 GBE-anslutningar användas parallellt (en per nod). Om du inte har 40 GBE tillgängligt rekommenderar vi att du har minst två 10 GBE-anslutningar (en per nod).

### <a name="port-requirements"></a>Portkrav

I följande tabell visas de portar som måste öppnas i brandväggen för att möjliggöra SMB- eller NFS-trafik. I den här tabellen refererar *i* eller *inkommande* till den riktning från vilken inkommande klient begär åtkomst till enheten. *Utgående* eller *utgående* refererar till i vilken riktning din Data Box Heavy-enhet skickar data externt, utöver distributionen: till exempel utgående till Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>Nästa steg

* [Distribuera din Azure-databox](data-box-deploy-ordered.md)
