---
title: Använda Azure Backup för att ersätta din bandinfrastruktur
description: Lär dig hur Azure Backup ger bandliknande semantik som gör det möjligt att säkerhetskopiera och återställa data i Azure
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 04/30/2017
ms.author: dacurwin
ms.openlocfilehash: d768f0fae9487a555f6ace12303f8a4bd7cb8bd1
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65146028"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Flytta din långsiktig lagring från ett band till Azure-molnet
Azure Backup och System Center Data Protection Manager-kunder kan:

* Säkerhetskopiera data i scheman som bäst passar organisationens behov.
* Behålla säkerhetskopierade data under längre perioder
* Se Azure som en del av deras långsiktig kvarhållning måste (i stället för band).

Den här artikeln förklarar hur kunder kan aktivera principer för säkerhetskopiering och kvarhållning. Kunder som använder band för att lösa sina lång-sikt-kvarhållning måste nu har ett kraftfullt och genomförbart alternativ med tillgängligheten för den här funktionen. Funktionen är aktiverad i den senaste versionen av Azure Backup (som är tillgängligt [här](https://aka.ms/azurebackup_agent)). System Center DPM måste kunderna uppdatera till, minst, DPM 2012 R2 UR5 innan du använder DPM med Azure Backup-tjänsten.

## <a name="what-is-the-backup-schedule"></a>Vad är schema för säkerhetskopiering?
Schemat för säkerhetskopiering anger hur ofta säkerhetskopieringen. Den här inställningen i följande skärmbild visar till exempel att säkerhetskopieringar vidtas för varje dag kl. 18: 00 och vid midnatt.

![Dagsschema](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Kunderna kan även schemalägga en veckovis säkerhetskopiering. Den här inställningen i följande skärmbild visar till exempel att säkerhetskopieringar vidtas för varje alternativ söndag & onsdag kl. 9:30 och 01:00:00.

![Schema för veckovis](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>Vad är bevarandeprincipen?
Bevarandeprincipen anger hur länge som säkerhetskopieringen måste lagras. I stället för att bara ange en ”platt princip” för alla säkerhetskopieringspunkter kan kan kunderna ange olika bevarandeprinciper baserat på när säkerhetskopieringen görs. Till exempel bevaras säkerhetskopieringspunkt tas dagligen, vilket fungerar som en operational återställningspunkt, i 90 dagar. Säkerhetskopieringspunkten vidtas i slutet av varje kvartal, av granskningsskäl bevaras under en längre period.

![Bevarandeprincip](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

Det totala antalet ”kvarhållningspunkterna” anges i den här principen är 90 (dagliga poäng) + 40 (en varje kvartal för 10 år) = 130.

## <a name="example--putting-both-together"></a>Exempel – installera båda
![Exempelskärm](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Dagliga bevarandeprincip**: Säkerhetskopieringar per dag lagras i sju dagar.
2. **Kvarhållningsprincip för veckovis**: Säkerhetskopieringar varje dag vid midnatt och 18: 00 lördag bevaras i fyra veckor
3. **Månatliga bevarandeprincip**: Bevaras säkerhetskopieringar vid midnatt och 18: 00 på den sista lördagen för varje månad i 12 månader
4. **Bevarandeprincip per år**: Säkerhetskopieringar vid midnatt på den sista lördagen av varje mars bevaras under tio år

Det totala antalet ”kvarhållningspunkterna” (poäng som en kund kan återställa data) i föregående diagram beräknas enligt följande:

* två hanteringsplatser per dag för sju dagar = 14 återställningspunkter
* två hanteringsplatser per vecka för fyra veckor = 8 återställningspunkter
* två pekar per månad i 12 månader = 24 återställningspunkter
* en punkt per år och 10 år = 10 recovery pekar

Det totala antalet återställningspunkter är 56.

> [!NOTE]
> Du kan skapa upp till 9999 återställningspunkter per skyddad instans med Azure Backup. En skyddad instans är en dator, en server (fysisk eller virtuell) eller en arbetsbelastning som säkerhetskopierar till Azure.
>

## <a name="advanced-configuration"></a>Avancerad konfiguration
Genom att klicka på **ändra** i den föregående skärmbilden kunder har större flexibilitet att ange scheman.

![Ändra](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Backup finns:

* [Introduktion till Azure Backup](backup-introduction-to-azure-backup.md)
* [Prova Azure Backup](backup-try-azure-backup-in-10-mins.md)
