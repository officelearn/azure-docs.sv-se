---
title: StorSimple Snapshot Manager administration | Microsoft-dokument
description: Innehåller en översikt och länkar till mer information om Administrationsuppgifter och arbetsflöden för Lösningsadministration i StorSimple Snapshot Manager.
services: storsimple
documentationcenter: NA
author: twooley
manager: timlt
editor: ''
ms.assetid: 1cdbb61d-bd16-4be4-ade2-ceab11508acb
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2016
ms.author: twooley
ms.openlocfilehash: b8fe87a2df633af310bc661308813a60987e77d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933283"
---
# <a name="use-storsimple-snapshot-manager-to-administer-your-storsimple-solution"></a>Använd StorSimple Snapshot Manager för att administrera din StorSimple-lösning

## <a name="overview"></a>Översikt
StorSimple Snapshot Manager är en MMC-snapin-modul (Microsoft Management Console) som förenklar dataskydd och säkerhetskopieringshantering i en Microsoft Azure StorSimple-miljö. Med StorSimple Snapshot Manager kan du hantera Microsoft Azure StorSimple-data i datacentret och i molnet som en enda integrerad lagringslösning, vilket förenklar säkerhetskopieringsprocesser och minskar kostnaderna.

Med den centrala hanteringskonsolen StorSimple Snapshot Manager kan du skapa konsekventa säkerhetskopior av lokala data och molndata. Du kan till exempel använda konsolen för att:

* Konfigurera, säkerhetskopiera och ta bort volymer.
* Konfigurera volymgrupper för att säkerställa att säkerhetskopierade data är programkonsekvent.
* Hantera principer för säkerhetskopiering så att data säkerhetskopieras enligt ett förutbestämt schema.
* Skapa oberoende kopior av data som kan lagras i molnet och användas för haveriberedskap.

Den här artikeln innehåller länkar till självstudier som beskriver StorSimple Snapshot Manager och hur du använder den för att slutföra systemadministrationsuppgifter och arbetsflöden.

* Mer information om StorSimple Snapshot Manager-komponenter och -arkitektur finns i [Vad är StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) 
* Om du vill hämta StorSimple Snapshot Manager går du till [nedladdningssidan för StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).
* För distributionsprocedurer för StorSimple Snapshot Manager går du till [Distribuera StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md).

> [!NOTE]
> Du kan inte använda StorSimple Snapshot Manager för att hantera Microsoft Azure StorSimple Virtual Arrays (kallas även StorSimple lokala virtuella enheter).


## <a name="storsimple-snapshot-manager-tasks-and-workflows"></a>StorSimple Snapshot Manager uppgifter och arbetsflöden
Du kan använda StorSimple Snapshot Manager för att övervaka och hantera aktuella, schemalagda och slutförda säkerhetskopieringsjobb. Dessutom innehåller StorSimple Snapshot Manager en katalog med upp till 64 slutförda säkerhetskopior. Du kan använda katalogen för att hitta och återställa volymer eller enskilda filer. 

| OM DU VILL GÖRA DET HÄR... | ANVÄND DEN HÄR SJÄLVSTUDIEN... |
|:--- |:--- |
| Läs mer om StorSimple Snapshot Manager |[Vad är StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md) |
| Installera StorSimple Snapshot Manager<br>Installera om StorSimple Snapshot Manager<br>Ta bort StorSimple Snapshot Manager |[Distribuera StorSimple Snapshot Manager](storsimple-snapshot-manager-deployment.md) |
| Använd Menyer och funktioner för StorSimple Snapshot Manager:<ul><li>Menyraden</li><li>Verktygsfält</li><li>Fönstret Omfattning</li><li>Resultatfönstret</li><li>Fönstret Åtgärder</li><li>Tangentbordsnavigering och kortkommandon</li></ul> |[Användargränssnittet i StorSimple Snapshot Manager](storsimple-use-snapshot-manager.md) |
| Använd de vanliga MMC-funktionerna som ingår i StorSimple Snapshot Manager:<ul><li>Visa</li><li>Nytt fönster från här</li><li>Uppdatera</li><li>Exportlista</li><li>Hjälp</li></ul> |[Använda MMC-menyåtgärderna i StorSimple Snapshot Manager](storsimple-snapshot-manager-mmc-menu.md) |
| Lägga till eller ersätta en enhet<br>Ansluta en enhet<br>Verifiera importerade volymgrupper<br>Uppdatera anslutna enheter<br>Autentisera en enhet<br>Visa enhetsinformation<br>Ta bort en enhetskonfiguration<br>Ändra ett enhetslösenord<br>Ersätta en misslyckad enhet<br> |[Använda StorSimple Snapshot Manager för att ansluta och hantera StorSimple-enheter](storsimple-snapshot-manager-manage-devices.md) |
| Montera volymer<br>Visa information om volymer<br>Ta bort en volym<br>Omsökningsvolymer<br>Konfigurera och säkerhetskopiera en enkel volym<br>Konfigurera och säkerhetskopiera en dynamisk speglad volym |[Använda StorSimple Snapshot Manager för att visa och hantera volymer](storsimple-snapshot-manager-manage-volumes.md) |
| Visa volymgrupper<br>Skapa en volymgrupp<br>Säkerhetskopiera en volymgrupp<br>Redigera en volymgrupp<br>Ta bort en volymgrupp |[Använda StorSimple Snapshot Manager för att skapa och hantera volymgrupper](storsimple-snapshot-manager-manage-volume-groups.md) |
| Skapa en säkerhetskopieringspolicy <br>Redigera en princip för säkerhetskopiering<br>Ta bort en princip för säkerhetskopiering |[Använda StorSimple Snapshot Manager för att skapa och hantera principer för säkerhetskopiering](storsimple-snapshot-manager-manage-backup-policies.md) |
| Visa och hantera schemalagda säkerhetskopieringsjobb<br>Visa och hantera de senaste säkerhetskopieringsjobben<br>Visa och hantera säkerhetskopieringsjobb som körs för närvarande |[Använda StorSimple Snapshot Manager för att visa och hantera säkerhetskopieringsjobb](storsimple-snapshot-manager-manage-backup-jobs.md) |
| Återställa en volym<br>Klona en volym- eller volymgrupp<br>Ta bort en säkerhetskopia<br>Återställa en fil<br>Återställa StorSimple Snapshot Manager-databasen |[Använda StorSimple Snapshot Manager för att hantera säkerhetskopieringskatalogen](storsimple-snapshot-manager-manage-backup-catalog.md) |

## <a name="next-steps"></a>Nästa steg
[Ladda ner StorSimple Snapshot Manager](https://www.microsoft.com/download/details.aspx?id=44220).

