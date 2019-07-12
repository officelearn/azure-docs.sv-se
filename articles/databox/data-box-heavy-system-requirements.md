---
title: Microsoft Azure Data Box tung systemkrav | Microsoft Docs
description: Läs mer om programvara och nätverkskrav för din Azure Data Box tung
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 6097dba9193fce4c8158137b488080107881994a
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839781"
---
# <a name="azure-data-box-heavy-system-requirements"></a>Azure Data Box tung systemkrav

Den här artikeln beskriver viktiga systemkraven för din Azure Data Box tung enhet och för klienter som ansluter till enheten. Vi rekommenderar att du har läst informationen noggrant innan du distribuerar din Data Box tung och referera tillbaka till det som behövs under distributionen och efterföljande driften.

Systemkraven är:

* **Programvarukrav för värdar som ansluter till Data Box tung** -beskriver plattformarna som stöds, webbläsare för det lokala webbgränssnittet, SMB-klienter och ytterligare krav för värdar som kan ansluta till Data Box.
* **Nätverkskrav för Data Box-tung** – innehåller information om nätverkskrav för optimala driften av Data Box tung enheten.

## <a name="software-requirements"></a>Programvarukrav

Programvarukrav innehålla information om de operativsystem som stöds och webbläsare som stöds för det lokala webbgränssnittet SMB-klienter.

### <a name="supported-operating-systems-for-clients"></a>Operativsystem som stöds för klienter

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]

### <a name="supported-file-systems-for-linux-clients"></a>Filsystem som stöds för Linux-klienter

[!INCLUDE [data-box-supported-file-systems-clients](../../includes/data-box-supported-file-systems-clients.md)]

### <a name="supported-storage-accounts"></a>Stöds storage-konton

[!INCLUDE [data-box-supported-storage-accounts](../../includes/data-box-supported-storage-accounts.md)]

### <a name="supported-storage-types"></a>Lagringstyper som stöds

[!INCLUDE [data-box-supported-storage-types](../../includes/data-box-supported-storage-types.md)]

### <a name="supported-web-browsers"></a>Webbläsare som stöds

[!INCLUDE [data-box-supported-web-browsers](../../includes/data-box-supported-web-browsers.md)]

## <a name="networking-requirements"></a>Nätverkskrav

Datacentret måste ha höghastighetsnätverk. För den snabbaste kopia hastigheter kan två 40 GbE-anslutningar användas parallellt (ett per nod). Om du inte har tillgängliga för 40 GbE, rekommenderar vi att du har minst två 10 GbE-anslutningar (en per nod).

### <a name="port-requirements"></a>Krav på nätverksportar

I följande tabell visas de portar som måste öppnas i brandväggen för att tillåta för SMB- eller NFS-trafik. I den här tabellen *i* eller *inkommande* avser riktningen från vilka inkommande begäranden för klientåtkomst till din enhet. *Ut* eller *utgående* refererar till Data Box tung enheten skickar data externt, utöver distributionen riktning: till exempel utgående till Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]

## <a name="next-steps"></a>Nästa steg

* [Distribuera Azure Data Box](data-box-deploy-ordered.md)
