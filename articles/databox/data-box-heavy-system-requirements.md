---
title: Microsoft Azure Data Box tung systemkrav | Microsoft Docs
description: Läs mer om programvara och nätverkskrav för din Azure Data Box tung
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: article
ms.date: 05/22/2019
ms.author: alkohli
ms.openlocfilehash: b9e249885bd0e930773d4b374f85d72e60abdbdc
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66427735"
---
# <a name="azure-data-box-heavy-system-requirements-preview"></a>Systemkrav för Azure Data Box tung (förhandsversion)

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

## <a name="next-steps"></a>Nästa steg

* [Distribuera Azure Data Box](data-box-deploy-ordered.md)
