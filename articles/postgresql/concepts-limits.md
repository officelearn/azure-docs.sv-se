---
title: Begränsningar i Azure-databas för PostgreSQL
description: Den här artikeln beskriver begränsningar i Azure-databas för PostgreSQL, till exempel antal anslutning och lagringsalternativ för motorn.
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 06/04/2018
ms.openlocfilehash: 5cd829236d8d8a58e68f7bf766790aa3f0cb656e
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757424"
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Begränsningar i Azure-databas för PostgreSQL
I följande avsnitt beskrivs kapacitet och funktionella gränser i databastjänsten för.

## <a name="maximum-connections"></a>Maximalt antal anslutningar
Det maximala antalet anslutningar per prisnivå och vCores är följande: 

|**Prisnivå**| **vCore(s)**| **Högsta antal anslutningar** |
|---|---|---|
|Basic| 1| 50 |
|Basic| 2| 100 |
|Generellt syfte| 2| 150|
|Generellt syfte| 4| 250|
|Generellt syfte| 8| 480|
|Generellt syfte| 16| 950|
|Generellt syfte| 32| 1500|
|Minnesoptimerad| 2| 150|
|Minnesoptimerad| 4| 250|
|Minnesoptimerad| 8| 480|
|Minnesoptimerad| 16| 950|

När anslutningar överskrider gränsen, får du följande fel:
> Oåterkalleligt fel: Det gick tyvärr redan för många klienter

Azure systemet kräver fem anslutningar till övervaka Azure-databas för PostgreSQL-servern. 

## <a name="functional-limitations"></a>Funktionella begränsningar
### <a name="scale-operations"></a>Skalningsåtgärder
1.  Dynamisk skalning av servrar över prisnivåer stöds inte för närvarande. Att växla mellan nivåerna Basic, generella eller Minnesoptimerade.
2.  Minska lagringsstorleken för server stöds inte för närvarande.

### <a name="server-version-upgrades"></a>Version serveruppgraderingarna
- Automatisk migrering mellan större database engine versioner stöds inte för närvarande.

### <a name="subscription-management"></a>Prenumerationshantering
- Dynamiskt flytta servrar över prenumerationer och resursgrupper stöds inte för närvarande.

### <a name="point-in-time-restore-pitr"></a>Punkt-i-tid-återställning (PITR)
1.  När du använder funktionen PITR, skapas den nya servern med samma konfiguration som den är baserad på-servern.
2.  Återställa en borttagen server stöds inte.

## <a name="next-steps"></a>Nästa steg
- Förstå [vad som är tillgängligt i varje prisnivå](concepts-pricing-tiers.md)
- Lär dig mer om [PostgreSQL-databas-versioner som stöds](concepts-supported-versions.md)
- Granska [säkerhetskopiera och återställa en server i Azure-databas för PostgreSQL med Azure-portalen](howto-restore-server-portal.md)
