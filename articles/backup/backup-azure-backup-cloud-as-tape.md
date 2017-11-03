---
title: "Använda Azure Backup för att ersätta infrastrukturen band | Microsoft Docs"
description: "Lär dig hur Azure Backup ger band-liknande semantik som gör det möjligt att säkerhetskopiera och återställa data i Azure"
services: backup
documentationcenter: 
author: trinadhk
manager: vijayts
editor: 
ms.assetid: 2e1bb67d-986c-4437-8056-3a63169b4214
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 1/10/2017
ms.author: saurse;trinadhk;markgal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f0f3152daf5f91f7c9e540797bf09b21969d2d33
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="move-your-long-term-storage-from-tape-to-the-azure-cloud"></a>Flytta dina långsiktig lagring från ett band till Azure-molnet
Azure Backup och System Center Data Protection Manager-kunder kan:

* Säkerhetskopiera data i scheman som bäst passar organisationens behov.
* Behåll den säkerhetskopiera informationen under längre perioder
* Se Azure som en del av deras långsiktig kvarhållning måste (istället för band).

Den här artikeln förklarar hur kunder kan aktivera principer för säkerhetskopiering och kvarhållning. Kunder som använder band för att lösa sina lång-sikt-kvarhållning måste nu har ett kraftfullt och genomförbart alternativ med tillgängligheten för den här funktionen. Funktionen är aktiverad i den senaste versionen av Azure-säkerhetskopiering (som är tillgänglig [här](http://aka.ms/azurebackup_agent)). System Center DPM-kunder måste uppdatera till, åtminstone, DPM 2012 R2 UR5 innan du använder DPM med Azure Backup-tjänsten.

## <a name="what-is-the-backup-schedule"></a>Vad är schema för säkerhetskopiering?
Schemat för säkerhetskopiering anger hur ofta säkerhetskopieringen. Till exempel visa inställningarna i följande skärmbild säkerhetskopieringar tas dagligen vid 18: 00 och vid midnatt.

![Dagsschema](./media/backup-azure-backup-cloud-as-tape/dailybackupschedule.png)

Kunder kan även schemalägga en säkerhetskopiering varje vecka. Till exempel visa inställningarna i följande skärmbild att säkerhetskopieringar vidtas för varje alternativ söndag & onsdag på 9:30 och 1:00:00.

![Veckoschema](./media/backup-azure-backup-cloud-as-tape/weeklybackupschedule.png)

## <a name="what-is-the-retention-policy"></a>Vad är den bevarandeprincip?
Bevarandeprincipen anger hur länge som säkerhetskopieringen måste lagras. Kunder kan i stället för att bara ange en ”platt policy” för alla återställningspunkter för säkerhetskopiering, ange olika bevarandeprinciper baserat på när säkerhetskopieringen utförs. Till exempel bevaras säkerhetskopieringspunkt tas dagligen, som fungerar som en operativa återställningspunkt i 90 dagar. Säkerhetskopieringspunkt vidtas i slutet av varje kvartal granskningssyfte bevaras under en längre period.

![Bevarandeprincip](./media/backup-azure-backup-cloud-as-tape/retentionpolicy.png)

Det totala antalet ”bevarandepunkter” anges i den här principen är 90 (dagliga poäng) + 40 (en varje kvartal för 10 år) = 130.

## <a name="example--putting-both-together"></a>Exempel – kombinera båda
![Exempel skärmen](./media/backup-azure-backup-cloud-as-tape/samplescreen.png)

1. **Dagliga bevarandeprincip**: säkerhetskopieringar tas dagligen sparas i sju dagar.
2. **Varje vecka bevarandeprincip**: säkerhetskopior som gjorts varje dag vid midnatt och 18: 00 lördag bevaras i fyra veckor
3. **Månatliga bevarandeprincip**: bevaras säkerhetskopior som har gjorts vid midnatt och 18: 00 på sista lördag i varje månad i 12 månader
4. **Årlig bevarandeprincip**: säkerhetskopior som har gjorts vid midnatt på den sista lördagen varje mars bevaras för 10 år

Det totala antalet ”bevarandepunkter” (poäng som en kund kan återställa data) i föregående diagram beräknas enligt följande:

* två hanteringsplatser per dag för sju dagar = 14 återställningspunkter
* två hanteringsplatser per vecka för fyra veckor = 8 återställningspunkter
* två hanteringsplatser per månad i 12 månader = 24 återställningspunkter
* en punkt per år och 10 år = 10 recovery pekar

Det totala antalet återställningspunkter är 56.

> [!NOTE]
> Azure-säkerhetskopiering har inte en begränsning på antalet återställningspunkter.
>
>

## <a name="advanced-configuration"></a>Avancerad konfiguration
Genom att klicka på **ändra** i den föregående skärmbilden kunder har större flexibilitet att ange scheman.

![Ändra](./media/backup-azure-backup-cloud-as-tape/modify.png)

## <a name="next-steps"></a>Nästa steg
Mer information om Azure Backup finns:

* [Introduktion till Azure Backup](backup-introduction-to-azure-backup.md)
* [Prova Azure-säkerhetskopiering](backup-try-azure-backup-in-10-mins.md)
