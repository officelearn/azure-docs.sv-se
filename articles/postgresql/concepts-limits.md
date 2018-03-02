---
title: "Begränsningar i Azure-databas för PostgreSQL"
description: "Den här artikeln beskriver begränsningar i Azure-databas för PostgreSQL, till exempel antal anslutning och lagringsalternativ för motorn."
services: postgresql
author: kamathsun
ms.author: sukamat
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: ba05308039e9743dd207333476e61a45c0ca166a
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="limitations-in-azure-database-for-postgresql"></a>Begränsningar i Azure-databas för PostgreSQL
Azure-databasen för PostgreSQL-tjänsten är tillgänglig som förhandsversion. I följande avsnitt beskrivs kapacitet och funktionella gränser i databastjänsten för.

## <a name="pricing-tier-maximums"></a>Priser för nivån maxkapacitet
Azure-databas för PostgreSQL har flera prisnivåer som du kan välja mellan när du skapar en server. Mer information finns i [prisnivåer i Azure-databas för PostgreSQL](concepts-pricing-tiers.md).  

Det finns ett maximalt antal anslutningar, enheter för beräkning och lagring i varje prisnivå: 

|Prisnivå| Compute-generering| vCore(s)| Högsta antal anslutningar |
|---|---|---|---|
|Basic| Gen 4| 1| 50 |
|Basic| Gen 4| 2| 100 |
|Basic| Gen 5| 1| 50 |
|Basic| Gen 5| 2| 100 |
|Generellt syfte| Gen 4| 2| 150|
|Generellt syfte| Gen 4| 4| 250|
|Generellt syfte| Gen 4| 8| 480|
|Generellt syfte| Gen 4| 16| 950|
|Generellt syfte| Gen 4| 32| 1500|
|Generellt syfte| Gen 5| 2| 150|
|Generellt syfte| Gen 5| 4| 250|
|Generellt syfte| Gen 5| 8| 480|
|Generellt syfte| Gen 5| 16| 950|
|Generellt syfte| Gen 5| 32| 1500|
|Minnesoptimerad| Gen 5| 2| 150|
|Minnesoptimerad| Gen 5| 4| 250|
|Minnesoptimerad| Gen 5| 8| 480|
|Minnesoptimerad| Gen 5| 16| 950|
|Minnesoptimerad| Gen 5| 32| 1900|

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
