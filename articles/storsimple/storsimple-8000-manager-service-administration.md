---
title: Tjänstadministration för StorSimple Enhetshanteraren | Microsoft-dokument
description: Lär dig hur du hanterar din StorSimple-enhet med hjälp av Tjänsten StorSimple Device Manager i Azure-portalen.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: alkohli
ms.openlocfilehash: b5490c4e79ee1458b498f539c0db2cc189fce7f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60723314"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>Använda Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet

## <a name="overview"></a>Översikt

I den här artikeln beskrivs tjänstgränssnittet i StorSimple Device Manager, inklusive hur du ansluter till det, de olika tillgängliga alternativen och länkar ut till de specifika arbetsflöden som kan utföras via det här användargränssnittet. Denna vägledning är tillämplig på båda. storSimples fysiska enhet och molnapparaten.

Efter att ha läst den här artikeln får du lära dig att:

* Ansluta till Tjänsten StorSimple Device Manager
* Administrera din StorSimple-enhet via StorSimple Device Manager-tjänsten

## <a name="connect-to-storsimple-device-manager-service"></a>Ansluta till Tjänsten StorSimple Device Manager

Tjänsten StorSimple Device Manager körs i Microsoft Azure och ansluter till flera StorSimple-enheter. Du använder en central Microsoft Azure-portal som körs i en webbläsare för att hantera dessa enheter. Gör följande om du vill ansluta till Tjänsten StorSimple Device Manager.

#### <a name="to-connect-to-the-service"></a>Så här ansluter du till tjänsten
1. Navigera [https://portal.azure.com/](https://portal.azure.com/)till .
2. Logga in på Microsoft Azure-portalen med dina Microsoft-kontouppgifter (längst upp till höger i fönstret).
3. Bläddra nedåt i det vänstra navigeringsfönstret för att komma åt Tjänsten StorSimple Device Manager.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>Administrera StorSimple-enhet med StorSimple Enhetshanteraren

I följande tabell visas en sammanfattning av alla vanliga hanteringsuppgifter och komplexa arbetsflöden som kan utföras inom tjänstgränssnittet för StorSimple-enhetshanteraren. Dessa uppgifter är ordnade baserat på de gränssnittsblad som de initieras på.

Om du vill ha mer information om varje arbetsflöde klickar du på lämplig procedur i tabellen.

#### <a name="storsimple-device-manager-workflows"></a>Arbetsflöden för StorSimple-Enhetshanteraren

| Om du vill göra detta ... | Använd den här proceduren. |
| --- | --- |
| Skapa en tjänst</br>Ta bort en tjänst</br>Hämta tjänstregistreringsnyckel</br>Återskapa tjänstregistreringsnyckel |[Distribuera en StorSimple Enhetshanteraren-tjänst](storsimple-8000-manage-service.md) |
| Visa aktivitetsloggarna |[Använda servicesammanfattningen för Enhetshanteraren i StorSimple](storsimple-8000-service-dashboard.md) |
| Ändra krypteringsnyckeln för tjänstdata</br>Visa åtgärdsloggarna |[Använda tjänstinstrumentpanelen För StorSimple-enhetshanteraren](storsimple-8000-service-dashboard.md) |
| Inaktivera en enhet</br>Ta bort en enhet |[Inaktivera eller ta bort en enhet](storsimple-8000-deactivate-and-delete-device.md) |
| Läs mer om katastrofåterställning och redundans för enheter</br>Redundans till en fysisk enhet</br>Redundans till en virtuell enhet</br>Återställning av haveuliker för affärskontinuitet (BCDR) |[Redundans- och haveriberedskap för Din StorSimple-enhet](storsimple-8000-device-failover-disaster-recovery.md) |
| Lista säkerhetskopior för en volym</br>Välj en säkerhetskopia</br>Ta bort en säkerhetskopia |[Hantera säkerhetskopior](storsimple-8000-manage-backup-catalog.md) |
| Klona en volym |[Klona en volym](storsimple-8000-clone-volume-u2.md) |
| Återställa en säkerhetskopia |[Återställa en säkerhetskopia](storsimple-8000-restore-from-backup-set-u2.md) |
| Om lagringskonton</br>Lägga till ett lagringskonto</br>Redigera ett lagringskonto</br>Ta bort ett lagringskonto</br>Nyckelrotation av lagringskonton |[Hantera lagringskonton](storsimple-8000-manage-storage-accounts.md) |
| Om bandbreddsmallar</br>Lägga till en bandbreddsmall</br>Redigera en bandbreddsmall</br>Ta bort en bandbreddsmall</br>Använda en standardbandbreddsmall</br>Skapa en bandbreddsmall hela dagen som startar vid en viss tidpunkt |[Hantera bandbreddsmallar](storsimple-8000-manage-bandwidth-templates.md) |
| Om åtkomstkontrollposter</br>Skapa en åtkomstkontrollpost</br>Redigera en åtkomstkontrollpost</br>Ta bort en åtkomstkontrollpost |[Hantera åtkomstkontrollposter](storsimple-8000-manage-acrs.md) |
| Visa jobbinformation</br>Avbryta ett jobb |[Hantera jobb](storsimple-8000-manage-jobs-u2.md) |
| Få varningsmeddelanden</br>Hantera aviseringar</br>Granska aviseringar |[Visa och hantera StorSimple-aviseringar](storsimple-8000-manage-alerts.md) |
| Skapa övervakningsdiagram |[Övervaka din StorSimple-enhet](storsimple-monitor-device.md) |
| Lägga till en volymbehållare</br>Ändra en volymbehållare</br>Ta bort en volymbehållare |[Hantera volymcontainrar](storsimple-8000-manage-volume-containers.md) |
| Lägga till en volym</br>Ändra en volym</br>Koppla från en volym</br>Ta bort en volym</br>Övervaka en volym |[Hantera volymer](storsimple-8000-manage-volumes-u2.md) |
| Ändra enhetsinställningar</br>Ändra tidsinställningar</br>Ändra DNS.md inställningar</br>Konfigurera nätverksgränssnitt |[Ändra enhetskonfiguration för Din StorSimple-enhet](storsimple-8000-modify-device-config.md) |
| Visa inställningar för webbproxy |[Konfigurera webbproxy för enheten](storsimple-8000-configure-web-proxy.md) |
| Ändra lösenord för enhetsadministratör</br>Ändra lösenord för StorSimple Snapshot Manager |[Ändra StorSimple-lösenord](storsimple-8000-change-passwords.md) |
| Konfigurera fjärrhantering |[Ansluta på distans till din StorSimple-enhet](storsimple-8000-remote-connect.md) |
| Konfigurera varningsinställningar |[Visa och hantera StorSimple-aviseringar](storsimple-8000-manage-alerts.md) |
| Konfigurera CHAP för StorSimple-enheten |[Konfigurera CHAP för StorSimple-enheten](storsimple-configure-chap.md) |
| Lägga till en säkerhetskopieringspolicy</br>Lägga till eller ändra ett schema</br>Ta bort en princip för säkerhetskopiering</br>Gör en manuell säkerhetskopiering</br>Skapa en anpassad princip för säkerhetskopiering med flera volymer och scheman |[Hantera säkerhetskopieringsprinciper](storsimple-8000-manage-backup-policies-u2.md) |
| Stoppa enhetsstyrenheter</br>Starta om enhetsstyrenheter</br>Stänga av enhetsstyrenheter</br>Återställa enheten till fabriksinställningar</br>(Ovan är endast för lokal enhet) |[Hantera StorSimple-enhetsstyrenhet](storsimple-8000-manage-device-controller.md) |
| Läs mer om LagringSimples maskinvarukomponenter</br>Övervaka maskinvarustatus</br>(Ovan är endast för lokal enhet) |[Övervaka maskinvarukomponenter](storsimple-8000-monitor-hardware-status.md) |
| Skapa ett supportpaket |[Skapa och hantera ett supportpaket](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| Installera programuppdateringar |[Uppdatera din enhet](storsimple-update-device.md) |

## <a name="next-steps"></a>Nästa steg

Om du får problem med den dagliga driften av Din StorSimple-enhet eller med någon av dess maskinvarukomponenter läser du:

* [Felsöka med diagnostikverktyget](storsimple-8000-diagnostics.md)
* [Använd StorSimple-övervakningsindikatorlysdioder](storsimple-monitoring-indicators.md)

Om du inte kan lösa problemen och behöver skapa en tjänstbegäran läser du [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md).

