---
title: StorSimple Snapshot Manager administration | Microsoft Docs
description: Ger en översikt och länkar till mer information om administrationsuppgifter för StorSimple Snapshot Manager-lösningen och arbetsflöden.
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
ms.openlocfilehash: bc72da98800ef85ef14be0882ba856fbf01386b9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60630027"
---
# <a name="use-storsimple-snapshot-manager-to-administer-your-storsimple-solution"></a>Använd StorSimple Snapshot Manager för att administrera din StorSimple-lösning

## <a name="overview"></a>Översikt
StorSimple Snapshot Manager är en snapin-modul i Microsoft Management Console (MMC) som förenklar dataskydd och hantering av säkerhetskopiering i en miljö med Microsoft Azure StorSimple. Med StorSimple Snapshot Manager hantera Microsoft Azure StorSimple data i datacentret och i molnet som en enda integrerad lagringslösning, vilket förenklar säkerhetskopiering processer och minska kostnaderna.

Hanteringskonsol för StorSimple Snapshot Manager kan du skapa konsekvent och point-in-time säkerhetskopior av lokala data och molndata. Du kan till exempel använda konsolen för att:

* Konfigurera, säkerhetskopiera och ta bort volymer.
* Konfigurera volymgrupper så säkerhetskopierade data är konsekventa.
* Hantera principer för säkerhetskopiering så att data säkerhetskopieras enligt ett förutbestämt schema.
* Skapa oberoende kopior av data, som kan lagras i molnet och används för haveriberedskap.

Den här artikeln innehåller länkar till självstudier som beskriver StorSimple Snapshot Manager och hur du använder den för att slutföra system administrationsuppgifter och arbetsflöden.

* Mer information om StorSimple Snapshot Manager-komponenter och arkitektur som finns i [vad är StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) 
* Om du vill hämta StorSimple Snapshot Manager går du till [hämtningssidan för StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).
* För StorSimple Snapshot Manager distributionsprocedurer, gå till [distribuera StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

> [!NOTE]
> Du kan inte använda StorSimple Snapshot Manager för att hantera Microsoft Azure StorSimple Virtual Array (även kallat StorSimple lokala virtuella enheter).


## <a name="storsimple-snapshot-manager-tasks-and-workflows"></a>StorSimple Snapshot Manager-aktiviteter och arbetsflöden
Du kan använda StorSimple Snapshot Manager för att övervaka och hantera aktuella, schemalagda och slutförda jobb. Dessutom ger StorSimple Snapshot Manager en katalog med upp till 64 slutförda säkerhetskopieringar. Du kan använda katalogen för att hitta och återställa volymer eller enskilda filer. 

| OM DU VILL GÖRA DETTA... | ANVÄND DEN HÄR SJÄLVSTUDIEKURSEN... |
|:--- |:--- |
| Läs mer om StorSimple Snapshot Manager |[Vad är StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) |
| Installera StorSimple Snapshot Manager<br>Reinstall StorSimple Snapshot Manager<br>Ta bort StorSimple Snapshot Manager |[Deploy StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md) |
| Använd StorSimple Snapshot Manager menyer och funktioner:<ul><li>Menyraden</li><li>Verktygsfältet</li><li>Omfattning</li><li>Resultatfönstret</li><li>Åtgärdsfönstret</li><li>Tangentbordsnavigering och genvägar</li></ul> |[Användargränssnitt för StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md) |
| Använd de vanliga MMC-funktioner som ingår i StorSimple Snapshot Manager:<ul><li>Visa</li><li>Nytt fönster härifrån</li><li>Uppdatera</li><li>Exportera lista</li><li>Hjälp</li></ul> |[Använd MMC menyn aktiviteter i StorSimple Snapshot Manager](storsimple-snapshot-manager-mmc-menu.md) |
| Lägga till eller ersätta en enhet<br>Anslut en enhet<br>Verifiera importerade volymgrupper<br>Uppdatera anslutna enheter<br>Autentisera en enhet<br>Visa enhetsinformation<br>Ta bort en enhetskonfiguration<br>Ändra enhetens lösenord<br>Ersätt en misslyckad enhet<br> |[Använd StorSimple Snapshot Manager för att ansluta och hantera StorSimple-enheter](storsimple-snapshot-manager-manage-devices.md) |
| Monteringsvolymer<br>Visa information om volymer<br>Ta bort en volym<br>Genomsök igen volymer<br>Konfigurera och säkerhetskopiering av en enkel volym<br>Konfigurera och en dynamisk speglad volym för säkerhetskopiering |[Använd StorSimple Snapshot Manager för att visa och hantera volymer](storsimple-snapshot-manager-manage-volumes.md) |
| Visa volymgrupper<br>Skapa en volym-grupp<br>Säkerhetskopiera en volym-grupp<br>Redigera en volym-grupp<br>Ta bort en volym-grupp |[Använd StorSimple Snapshot Manager för att skapa och hantera volymgrupper](storsimple-snapshot-manager-manage-volume-groups.md) |
| Skapa en säkerhetskopieringspolicy <br>Redigera en princip för säkerhetskopiering<br>Ta bort en princip för säkerhetskopiering |[Använd StorSimple Snapshot Manager för att skapa och hantera principer för säkerhetskopiering](storsimple-snapshot-manager-manage-backup-policies.md) |
| Visa och hantera schemalagda säkerhetskopieringsjobb<br>Visa och hantera de senaste säkerhetskopieringsjobb<br>Visa och hantera pågående säkerhetskopieringsjobb |[Använd StorSimple Snapshot Manager för att visa och hantera säkerhetskopieringsjobb](storsimple-snapshot-manager-manage-backup-jobs.md) |
| Återställa en volym<br>Klona en volym eller en volym grupp<br>Ta bort en säkerhetskopia<br>Återställa en fil<br>Återställ StorSimple Snapshot Manager-databasen |[Använd StorSimple Snapshot Manager för att hantera säkerhetskopieringskatalogen](storsimple-snapshot-manager-manage-backup-catalog.md) |

## <a name="next-steps"></a>Nästa steg
[Ladda ned StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

