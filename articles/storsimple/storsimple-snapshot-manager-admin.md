---
title: StorSimple Snapshot Manager administration | Microsoft Docs
description: Ger en översikt och länkar till mer information om administrationsuppgifter för StorSimple Snapshot Manager lösning och arbetsflöden.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 1cdbb61d-bd16-4be4-ade2-ceab11508acb
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2016
ms.author: v-sharos
ms.openlocfilehash: a99b3d7336c3dc1a1f249915d6971a49f4b69be6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875433"
---
# <a name="use-storsimple-snapshot-manager-to-administer-your-storsimple-solution"></a>Använd StorSimple Snapshot Manager för att administrera din StorSimple-lösning

## <a name="overview"></a>Översikt
StorSimple Snapshot Manager är en snapin-modul i Microsoft Management Console (MMC) som förenklar dataskydd och hantering av säkerhetskopiering i en miljö med Microsoft Azure StorSimple. Med StorSimple Snapshot Manager hantera Microsoft Azure StorSimple-data i datacentret och i molnet som en enda integrerad lagringslösning därmed förenkla processer för säkerhetskopiering och minska kostnaderna.

Den centrala hanteringskonsolen för StorSimple Snapshot Manager kan du skapa konsekvent och tidpunkt i säkerhetskopior av lokala data och molndata. Du kan till exempel använda konsolen för att:

* Konfigurera, säkerhetskopiera och ta bort volymer.
* Konfigurera volym grupper så säkerhetskopierade data är programkonsekventa.
* Hantera principer för säkerhetskopiering så att data säkerhetskopieras enligt ett förutbestämt schema.
* Skapa oberoende kopior av data som kan lagras i molnet och används för katastrofåterställning.

Den här artikeln innehåller länkar till självstudier som beskriver StorSimple Snapshot Manager och hur du använder den för att slutföra systemadministrationsuppgifter och arbetsflöden.

* Läs mer om StorSimple Snapshot Manager-komponenter och arkitektur, [vad är StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) 
* Hämta StorSimple Snapshot Manager genom att gå till [hämtningssidan för StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).
* Gå till för StorSimple Snapshot Manager distributionsprocedurer [distribuera StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

> [!NOTE]
> Du kan inte använda StorSimple Snapshot Manager för att hantera Microsoft Azure StorSimple virtuell matriser (även kallat StorSimple lokala virtuella enheter).


## <a name="storsimple-snapshot-manager-tasks-and-workflows"></a>StorSimple Snapshot Manager aktiviteter och arbetsflöden
Du kan använda StorSimple Snapshot Manager för att övervaka och hantera aktuella schemalagda och slutförda säkerhetskopieringsjobb. Dessutom ger StorSimple Snapshot Manager en katalog med upp till 64 slutförda säkerhetskopieringar. Du kan använda katalogen för att hitta och återställa volymer eller enskilda filer. 

| OM DU VILL GÖRA DETTA... | ANVÄND DEN HÄR SJÄLVSTUDIEKURSEN... |
|:--- |:--- |
| Lär dig mer om StorSimple Snapshot Manager |[Vad är StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) |
| Installera StorSimple Snapshot Manager<br>Installera om StorSimple Snapshot Manager<br>Ta bort StorSimple Snapshot Manager |[Distribuera StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md) |
| Använd StorSimple Snapshot Manager menyer och funktioner:<ul><li>Menyraden</li><li>Verktygsfältet</li><li>Omfattning</li><li>Resultatfönstret</li><li>Åtgärdsfönstret</li><li>Tangentbordsnavigering och genvägar</li></ul> |[Användargränssnittet för StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md) |
| Använd vanliga MMC-funktioner som ingår i StorSimple Snapshot Manager:<ul><li>Visa</li><li>Nytt fönster härifrån</li><li>Uppdatera</li><li>Exportera lista</li><li>Hjälp</li></ul> |[Använda MMC-menyn åtgärder i StorSimple Snapshot Manager](storsimple-snapshot-manager-mmc-menu.md) |
| Lägga till eller ersätta en enhet<br>Anslut en enhet<br>Verifiera importerade volymen grupper<br>Uppdatera anslutna enheter<br>Autentisera en enhet<br>Visa information om enhet<br>Ta bort en enhetskonfiguration<br>Ändra lösenordet för enheten<br>Ersätta en misslyckad enhet<br> |[Använd StorSimple Snapshot Manager för att ansluta och hantera StorSimple-enheter](storsimple-snapshot-manager-manage-devices.md) |
| Montera volymer<br>Visa information om volymer<br>Ta bort en volym<br>Skanna volymer<br>Konfigurera och säkerhetskopiera en enkel volym<br>Konfigurera och säkerhetskopiera en dynamisk speglad volym |[Använd StorSimple Snapshot Manager för att visa och hantera volymer](storsimple-snapshot-manager-manage-volumes.md) |
| Visa grupper av volym<br>Skapa en volym-grupp<br>Säkerhetskopiera en volym-grupp<br>Redigera en grupp av volym<br>Ta bort en volym-grupp |[Använda StorSimple Snapshot Manager för att skapa och hantera grupper av volym](storsimple-snapshot-manager-manage-volume-groups.md) |
| Skapa en princip för säkerhetskopiering <br>Redigera en princip för säkerhetskopiering<br>Ta bort en princip för säkerhetskopiering |[Använd StorSimple Snapshot Manager för att skapa och hantera principer för säkerhetskopiering](storsimple-snapshot-manager-manage-backup-policies.md) |
| Visa och hantera schemalagda säkerhetskopieringsjobb<br>Visa och hantera senaste säkerhetskopieringsjobb<br>Visa och hantera säkerhetskopieringsjobb som körs |[Använd StorSimple Snapshot Manager för att visa och hantera säkerhetskopieringsjobb](storsimple-snapshot-manager-manage-backup-jobs.md) |
| Återställa en volym<br>Klona en volym eller en volym grupp<br>Ta bort en säkerhetskopia<br>Återställa en fil<br>Återställ databasen StorSimple Snapshot Manager |[Använd StorSimple Snapshot Manager för att hantera katalogen för säkerhetskopieringen](storsimple-snapshot-manager-manage-backup-catalog.md) |

## <a name="next-steps"></a>Nästa steg
[Hämta StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

