---
title: Så här byter du ut bandinfrastrukturen
description: Lär dig hur Azure Backup tillhandahåller bandliknande semantik som gör att du kan säkerhetskopiera och återställa data i Azure
ms.topic: conceptual
ms.date: 04/30/2017
ms.openlocfilehash: aeda1cefc84d425855c40b793f8334936541e63f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425110"
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Flytta din långtidslagring från band till Azure-molnet

Azure Backup och System Center Data Protection Manager kunder kan:

* Säkerhetskopiera data i scheman som bäst passar organisationens behov.
* Behåll säkerhetskopieringsdata under längre perioder.
* Gör Azure till en del av deras långsiktiga lagringsbehov (i stället för band).

I den här artikeln beskrivs hur kunder kan aktivera principer för säkerhetskopiering och kvarhållning. Kunder som använder band för att tillgodose sina långsiktiga lagringsbehov har nu ett kraftfullt och lönsamt alternativ med tillgängligheten av den här funktionen. Funktionen är aktiverad i den senaste versionen av Azure Backup (som finns [här).](https://aka.ms/azurebackup_agent) System Center DPM-kunder måste uppdatera till minst DPM 2012 R2 UR5 innan du använder DPM med Azure Backup-tjänsten.

## <a name="what-is-the-backup-schedule"></a>Vad är säkerhetskopieringsschemat?

Säkerhetskopieringsschemat anger frekvensen för säkerhetskopieringen. Inställningarna på följande skärm anger till exempel att säkerhetskopieringar tas dagligen klockan 18.00 och vid midnatt.

![Dagligt schema](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Kunder kan också schemalägga en veckovis säkerhetskopiering. Inställningarna på följande skärm anger till exempel att säkerhetskopieringar tas varje alternativ söndag & onsdag klockan 9:30 och 01:00.

![Veckoschema](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>Vad är bevarandeprincipen?

Bevarandeprincipen anger hur länge säkerhetskopian måste lagras. I stället för att bara ange en "platt princip" för alla säkerhetskopieringspunkter kan kunder ange olika bevarandeprinciper baserat på när säkerhetskopieringen görs. Till exempel bevaras säkerhetskopieringspunkten som tas dagligen, som fungerar som en operativ återställningspunkt, i 90 dagar. Säkerhetskopieringspunkten som tas i slutet av varje kvartal för granskningsändamål bevaras under en längre tid.

![Loggperiod](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

Det totala antalet "kvarhållningspoäng" som anges i denna policy är 90 (dagliga poäng) + 40 (en varje kvartal i 10 år) = 130.

## <a name="example--putting-both-together"></a>Exempel – Att sätta ihop båda

![Exempel på skärm](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Daglig lagringsprincip**: Säkerhetskopiering som tas dagligen lagras i sju dagar.
2. **Weekly retention policy**: Säkerhetskopior som tas vid midnatt och 18:00 lördag bevaras i fyra veckor.
3. **Månatlig lagringspolicy:** Säkerhetskopior som tas vid midnatt och 18:00 den sista lördagen i varje månad bevaras i 12 månader.
4. **Årlig bevarandepolicy**: Säkerhetskopior som tas vid midnatt den sista lördagen i varje mars bevaras i 10 år.

Det totala antalet "kvarhållningspunkter" (punkter från vilka en kund kan återställa data) i föregående diagram beräknas på följande sätt:

* två poäng per dag i sju dagar = 14 återställningspunkter
* två poäng per vecka i fyra veckor = 8 återställningspunkter
* två poäng per månad i 12 månader = 24 återställningspunkter
* en poäng per år och 10 år = 10 återställningspunkter

Det totala antalet återställningspunkter är 56.

> [!NOTE]
> Med Azure Backup kan du skapa upp till 9999 återställningspunkter per skyddad instans. En skyddad instans är en dator, server (fysisk eller virtuell) eller arbetsbelastning som säkerhetskopierar data till Azure.
>

## <a name="advanced-configuration"></a>Avancerad konfiguration

Genom att klicka på **Ändra** på föregående skärm kan kunderna ha ytterligare flexibilitet när det gäller att ange kvarhållningsscheman.

![Ändra](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Backup finns i:

* [Introduktion till Azure Backup](backup-introduction-to-azure-backup.md)
* [Prova Azure Backup](backup-try-azure-backup-in-10-mins.md)
