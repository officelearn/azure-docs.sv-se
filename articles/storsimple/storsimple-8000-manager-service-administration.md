---
title: StorSimple Device Manager tjänstadministration | Microsoft Docs
description: Lär dig hur du hanterar din StorSimple-enhet med hjälp av StorSimple Device Manager-tjänsten i Azure-portalen.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60723314"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>Använda StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet

## <a name="overview"></a>Översikt

Den här artikeln beskriver gränssnittet StorSimple Device Manager service, inklusive hur du ansluter till det, de olika alternativen och länkar till specifika arbetsflöden som kan utföras via det här Gränssnittet. Den här vägledningen gäller för både; den fysiska StorSimple-enheten och molninstallationen.

När du har läst den här artikeln kan du lära dig att:

* Ansluta till StorSimple Device Manager-tjänsten
* Administrera din StorSimple-enhet via StorSimple Device Manager-tjänsten

## <a name="connect-to-storsimple-device-manager-service"></a>Ansluta till StorSimple Device Manager-tjänsten

StorSimple Device Manager-tjänsten körs i Microsoft Azure och ansluter till flera StorSimple-enheter. Du kan använda en central Microsoft Azure-portalen som körs i en webbläsare för att hantera dessa enheter. Gör följande om du vill ansluta till StorSimple Device Manager-tjänsten.

#### <a name="to-connect-to-the-service"></a>Att ansluta till tjänsten
1. Navigera till [https://portal.azure.com/](https://portal.azure.com/).
2. Med autentiseringsuppgifterna för ditt Microsoft-konto kan logga in på Microsoft Azure-portalen (finns i upp till höger i fönstret).
3. Rulla ned det vänstra navigeringsfönstret åtkomst till StorSimple Device Manager-tjänsten.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>Administrera StorSimple-enhet med StorSimple Device Manager-tjänsten

I följande tabell visas en sammanfattning av alla vanliga administrationsuppgifter och komplexa arbetsflöden som kan utföras i Användargränssnittet för StorSimple Device Manager-tjänsten. De här uppgifterna har ordnats baserat på UI-blad som har initierats.

Klicka på lämplig procedur i tabellen för mer information om varje arbetsflöde.

#### <a name="storsimple-device-manager-workflows"></a>StorSimple Device Manager-arbetsflöden

| Om du vill göra detta... | Använd den här proceduren. |
| --- | --- |
| Skapa en tjänst</br>Ta bort en tjänst</br>Hämta tjänstregistreringsnyckel</br>Återskapa tjänstregistreringsnyckel |[Distribuera en StorSimple Device Manager-tjänst](storsimple-8000-manage-service.md) |
| Visa aktivitetsloggar |[Använd Tjänstsammanfattning för StorSimple Device Manager](storsimple-8000-service-dashboard.md) |
| Ändra krypteringsnyckeln för tjänstdata</br>Visa åtgärdsloggar |[Använd instrumentpanelen för StorSimple Device Manager-tjänsten](storsimple-8000-service-dashboard.md) |
| Inaktivera en enhet</br>Ta bort en enhet |[Inaktivera eller ta bort en enhet](storsimple-8000-deactivate-and-delete-device.md) |
| Lär dig mer om disaster recovery och enheten redundans</br>Växla över till en fysisk enhet</br>Redundans till en virtuell enhet</br>Kontinuitet för företag-haveriberedskap (BCDR) |[Redundans och disaster recovery för din StorSimple-enhet](storsimple-8000-device-failover-disaster-recovery.md) |
| Lista över säkerhetskopior för en volym</br>Välj en säkerhetskopia</br>Ta bort en säkerhetskopia |[Hantera säkerhetskopior](storsimple-8000-manage-backup-catalog.md) |
| Klona en volym |[Klona en volym](storsimple-8000-clone-volume-u2.md) |
| Återställa en säkerhetskopia |[Återställa en säkerhetskopia](storsimple-8000-restore-from-backup-set-u2.md) |
| Om storage-konton</br>Lägg till ett lagringskonto</br>Redigera ett storage-konto</br>Ta bort ett lagringskonto</br>Rotation av storage-konton |[Hantera lagringskonton](storsimple-8000-manage-storage-accounts.md) |
| Om bandbreddsmallar</br>Lägg till en bandbreddsmall</br>Redigera en bandbreddsmall</br>Ta bort en bandbreddsmall</br>Använda en standardmall för bandbredd</br>Skapa en varar hela bandbreddsmallen som börjar vid en angiven tid |[Hantera bandbreddsmallar](storsimple-8000-manage-bandwidth-templates.md) |
| Om åtkomstkontrollposter</br>Skapa en åtkomstkontrollpost</br>Redigera en åtkomstkontrollpost</br>Ta bort en åtkomstkontrollpost |[Hantera åtkomstkontrollposter](storsimple-8000-manage-acrs.md) |
| Visa jobbinformation</br>Avbryta ett jobb |[Hantera jobb](storsimple-8000-manage-jobs-u2.md) |
| Få varningsmeddelanden</br>Hantera aviseringar</br>Granska aviseringar |[Visa och hantera aviseringar för StorSimple](storsimple-8000-manage-alerts.md) |
| Skapa övervakningsdiagram |[Övervaka din StorSimple-enhet](storsimple-monitor-device.md) |
| Lägg till volymbehållare</br>Ändra en volymbehållare</br>Ta bort en volymbehållare |[Hantera volymcontainrar](storsimple-8000-manage-volume-containers.md) |
| Lägg till en volym</br>Ändra en volym</br>Koppla från en volym</br>Ta bort en volym</br>Övervaka en volym |[Hantera volymer](storsimple-8000-manage-volumes-u2.md) |
| Ändra enhetsinställningar</br>Ändra tidsinställningar</br>Ändra inställningarna för DNS.md</br>Konfigurera nätverksgränssnitt |[Ändra enhetskonfigurationen för din StorSimple-enhet](storsimple-8000-modify-device-config.md) |
| Visa Webbproxyinställningar |[Konfigurera webbproxy för din enhet](storsimple-8000-configure-web-proxy.md) |
| Ändra enhetens administratörslösenord</br>Ändra lösenordet för StorSimple Snapshot Manager |[Ändra lösenord för StorSimple](storsimple-8000-change-passwords.md) |
| Konfigurera fjärrhantering |[Fjärransluta till din StorSimple-enhet](storsimple-8000-remote-connect.md) |
| Konfigurera aviseringsinställningar |[Visa och hantera aviseringar för StorSimple](storsimple-8000-manage-alerts.md) |
| Konfigurera CHAP för din StorSimple-enhet |[Konfigurera CHAP för din StorSimple-enhet](storsimple-configure-chap.md) |
| Lägga till en säkerhetskopieringspolicy</br>Lägg till eller ändra ett schema</br>Ta bort en princip för säkerhetskopiering</br>Gör en manuell säkerhetskopia</br>Skapa en anpassad princip för säkerhetskopiering med flera volymer och scheman |[Hantera säkerhetskopieringsprinciper](storsimple-8000-manage-backup-policies-u2.md) |
| Stoppa styrenheter</br>Starta om styrenheter</br>Stäng av styrenheter</br>Återställa enheten till fabriksinställningarna</br>(Ovan är för den lokala enheten) |[Hantera enhetskontrollanten för StorSimple](storsimple-8000-manage-device-controller.md) |
| Lär dig mer om StorSimple maskinvarukomponenter</br>Övervaka maskinvarustatus</br>(Ovan är för den lokala enheten) |[Övervaka maskinvarukomponenter](storsimple-8000-monitor-hardware-status.md) |
| Skapa ett supportpaket |[Skapa och hantera ett supportpaket](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| Installation av programuppdateringar |[Uppdatera din enhet](storsimple-update-device.md) |

## <a name="next-steps"></a>Nästa steg

Om du får problem med den dagliga driften av din StorSimple-enhet eller med någon av dess maskinvarukomponenter, se:

* [Felsök med verktyget diagnostik](storsimple-8000-diagnostics.md)
* [Använda StorSimple övervakning av övervakningsindikatorer](storsimple-monitoring-indicators.md)

Om du inte kan lösa problem och du måste skapa en tjänstbegäran, referera till [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md).

