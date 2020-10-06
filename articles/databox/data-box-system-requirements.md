---
title: Microsoft Azure Data Box-enhet system krav | Microsoft Docs
description: Lär dig mer om viktiga system krav för din Azure Data Box och för klienter som ansluter till Data Box-enhet.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 10/02/2020
ms.author: alkohli
ms.openlocfilehash: 5f1623ef4dde59e816e3afe5a5f5894c49469580
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2020
ms.locfileid: "91767855"
---
# <a name="azure-data-box-system-requirements"></a>Azure Data Box system krav

I den här artikeln beskrivs viktiga system krav för din Microsoft Azure Data Box-enhet och för klienter som ansluter till Data Box-enhet. Vi rekommenderar att du läser informationen noggrant innan du distribuerar din Data Box-enhet och sedan läser den efter behov under distributionen och åtgärden.

System kraven är:

* **Program varu krav:** För värdar som ansluter till Data Box-enhet, beskriver de operativ system som stöds, fil överförings protokoll, lagrings konton, lagrings typer och webbläsare för det lokala webb gränssnittet.
* **Nätverks krav:** För Data Box-enhet, beskriver nätverks anslutning och port krav för den optimala driften av Data Box-enhet.


## <a name="software-requirements"></a>Programvarukrav

Program varu kraven inkluderar stödda operativ system, fil överförings protokoll, lagrings konton, lagrings typer och webbläsare för det lokala webb gränssnittet.

### <a name="supported-operating-systems-for-clients"></a>Operativsystem som stöds för klienter

[!INCLUDE [data-box-supported-os-clients](../../includes/data-box-supported-os-clients.md)]


### <a name="supported-file-transfer-protocols-for-clients"></a>Fil överförings protokoll som stöds för klienter

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

Datacentret måste ha höghastighetsnätverk. Vi rekommenderar starkt att du har en anslutning på minst 10 GbE. Om en 10-GbE-anslutning inte är tillgänglig kan en 1-GbE-datalänk användas för att kopiera data, men kopierings hastigheten påverkas.

### <a name="port-requirements"></a>Portkrav

I följande tabell visas de portar som måste öppnas i brand väggen för att tillåta SMB-eller NFS-trafik. I den här tabellen *avser* (*inkommande*) den riktning från vilken inkommande klient begär åtkomst till din enhet. *Ut* (eller *utgående*) avser i vilken riktning din data Box-enhet enhet skickar data externt, utöver distributionen: till exempel utgående till Internet.

[!INCLUDE [data-box-port-requirements](../../includes/data-box-port-requirements.md)]


## <a name="next-steps"></a>Nästa steg

* [Distribuera Azure Data Box](data-box-deploy-ordered.md)
