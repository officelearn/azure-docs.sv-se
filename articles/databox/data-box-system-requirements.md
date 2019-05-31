---
title: Systemkrav för Microsoft Azure Data Box | Microsoft Docs
description: Lär dig mer om programvaran och nätverkskraven för din Azure Data Box-enhet
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: 3bce5dd1dd34c53276c5486cc255c4cd93bb6080
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242187"
---
# <a name="azure-data-box-system-requirements"></a>Azure Data Box-systemkrav

Den här artikeln beskriver viktiga systemkraven för din Microsoft Azure Data Box och för klienter som ansluter till Data Box. Vi rekommenderar att du har läst informationen noggrant innan du distribuerar din Data box-enhet och sedan refererar tillbaka till det som behövs under distributionen och efterföljande driften.

Systemkraven är:

* **Programvarukrav för värdar som ansluter till Data Box** -beskriver plattformarna som stöds, webbläsare för det lokala webbgränssnittet, SMB-klienter och ytterligare krav för värdar som kan ansluta till Data Box.
* **Nätverkskrav för Data Box** – innehåller information om nätverkskrav för optimala driften av Data Box.


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

Datacentret måste ha höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE. Om en 10 GbE-anslutning inte är tillgänglig användas en 1 GbE-datalänk för att kopiera data men kopian hastighet som påverkas.

## <a name="next-steps"></a>Nästa steg

* [Distribuera Azure Data Box](data-box-deploy-ordered.md)
