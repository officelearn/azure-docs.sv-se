---
title: Administrationen av StorSimple Enhetshanteraren | Microsoft Docs
description: "Lär dig hur du hanterar din StorSimple-enhet med hjälp av Enhetshanteraren för StorSimple-tjänsten i Azure-portalen."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/12/2017
ms.author: alkohli
ms.openlocfilehash: 0e7d7f44a70278a7777ba6c32c8e546074953fdc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-device"></a>Använda Enhetshanteraren för StorSimple-tjänsten för att administrera din StorSimple-enhet

## <a name="overview"></a>Översikt

Den här artikeln beskriver gränssnittet StorSimple Device Manager service, inklusive hur du ansluter till den, de olika alternativen och länkar ut till specifika arbetsflöden som kan utföras via Användargränssnittet. Den här vägledningen gäller både; den fysiska StorSimple-enheten och moln-enhet.

När du har läst den här artikeln får du lära dig att:

* Ansluta till StorSimple Device Manager-tjänsten
* Administrera din StorSimple-enhet via service Manager för StorSimple-enhet

## <a name="connect-to-storsimple-device-manager-service"></a>Ansluta till StorSimple Device Manager-tjänsten

StorSimple Device Manager-tjänsten körs i Microsoft Azure och ansluter till flera StorSimple-enheter. Du kan använda en central Microsoft Azure-portalen som körs i en webbläsare för att hantera dessa enheter. Gör följande för att ansluta till tjänsten StorSimple Enhetshanteraren.

#### <a name="to-connect-to-the-service"></a>Att ansluta till tjänsten
1. Gå till [https://portal.azure.com/](https://portal.azure.com/).
2. Med autentiseringsuppgifterna för ditt Microsoft-konto kan logga in på Microsoft Azure-portalen (finns i det övre högerkant fönstret).
3. Rulla ned det vänstra navigeringsfönstret för att komma åt StorSimple Device Manager-tjänsten.


## <a name="administer-storsimple-device-using-storsimple-device-manager-service"></a>Administrera StorSimple-enhet med hjälp av Enhetshanteraren för StorSimple-tjänsten

I följande tabell visas en sammanfattning av alla vanliga administrativa uppgifter och komplexa arbetsflöden som kan utföras i tjänsten StorSimple Enhetshanteraren Användargränssnittet. Dessa uppgifter är ordnade baserat på de UI-blad har initierats.

Mer information om varje arbetsflöde klickar du på lämplig procedur i tabellen.

#### <a name="storsimple-device-manager-workflows"></a>StorSimple Device Manager-arbetsflöden

| Om du vill göra detta... | Använd den här proceduren. |
| --- | --- |
| Skapa en tjänst</br>Ta bort en tjänst</br>Hämta nyckel för tjänstregistrering</br>Återskapa nyckeln för tjänstregistrering |[Distribuera en StorSimple Device Manager-tjänst](storsimple-8000-manage-service.md) |
| Kontrollera händelseloggarna för aktiviteten |[Använd StorSimple Enhetshanteraren Servicesammanfattning](storsimple-8000-service-dashboard.md) |
| Ändra krypteringsnyckeln för tjänstdata</br>Kontrollera händelseloggarna för åtgärden |[Använd instrumentpanelen för StorSimple Device Manager-tjänsten](storsimple-8000-service-dashboard.md) |
| Inaktivera en enhet</br>Ta bort en enhet |[Inaktivera eller ta bort en enhet](storsimple-8000-deactivate-and-delete-device.md) |
| Lär dig mer om disaster recovery och enheten redundans</br>Växling till en fysisk enhet</br>Växling till en virtuell enhet</br>Katastrofåterställning för verksamhetskontinuitet (BCDR) |[Redundans och disaster recovery för din StorSimple-enhet](storsimple-8000-device-failover-disaster-recovery.md) |
| Lista säkerhetskopieringar för en volym</br>Välj en säkerhetskopia</br>Ta bort en säkerhetskopia |[Hantera säkerhetskopior](storsimple-8000-manage-backup-catalog.md) |
| Klona en volym |[Klona en volym](storsimple-8000-clone-volume-u2.md) |
| Återställa en säkerhetskopia |[Återställa en säkerhetskopia](storsimple-8000-restore-from-backup-set-u2.md) |
| Om storage-konton</br>Lägg till ett lagringskonto</br>Redigera ett lagringskonto</br>Ta bort ett lagringskonto</br>Viktiga rotation av storage-konton |[Hantera lagringskonton](storsimple-8000-manage-storage-accounts.md) |
| Om bandbredd mallar</br>Lägg till en bandbreddsmall</br>Redigera en bandbreddsmall</br>Ta bort en bandbreddsmall</br>Använda en standardmall för bandbredd</br>Skapa en varar bandbreddsmall som börjar vid en angiven tid |[Hantera bandbreddsmallar](storsimple-8000-manage-bandwidth-templates.md) |
| Om access control-poster</br>Skapa en åtkomstkontrollpost</br>Redigera en åtkomstkontrollpost</br>Ta bort en åtkomstkontrollpost |[Hantera access control-poster](storsimple-8000-manage-acrs.md) |
| Visa jobbinformation</br>Avbryta ett jobb |[Hantera jobb](storsimple-8000-manage-jobs-u2.md) |
| Få varningsmeddelanden</br>Hantera aviseringar</br>Granska aviseringar |[Visa och hantera aviseringar för StorSimple](storsimple-8000-manage-alerts.md) |
| Skapa övervakning diagram |[Övervaka din StorSimple-enhet](storsimple-monitor-device.md) |
| Lägg till volymbehållare</br>Ändra en volymbehållare</br>Ta bort en volymbehållare |[Hantera volymbehållare](storsimple-8000-manage-volume-containers.md) |
| Lägg till en volym</br>Ändra en volym</br>Koppla från en volym</br>Ta bort en volym</br>Övervaka en volym |[Hantera volymer](storsimple-8000-manage-volumes-u2.md) |
| Ändra inställningar för enheter</br>Ändra inställningarna för tid</br>Ändra inställningar för DNS.md</br>Konfigurera nätverksgränssnitt |[Ändra enhetskonfigurationen för din StorSimple-enhet](storsimple-8000-modify-device-config.md) |
| Visa web proxy-inställningar |[Konfigurera webbproxy för din enhet](storsimple-8000-configure-web-proxy.md) |
| Ändra enhetens administratörslösenord</br>Ändra lösenordet för StorSimple Snapshot Manager |[Ändra StorSimple-lösenord](storsimple-8000-change-passwords.md) |
| Konfigurera fjärrhantering |[Fjärransluta till din StorSimple-enhet](storsimple-8000-remote-connect.md) |
| Konfigurera aviseringsinställningar |[Visa och hantera aviseringar för StorSimple](storsimple-8000-manage-alerts.md) |
| Konfigurera CHAP för din StorSimple-enhet |[Konfigurera CHAP för din StorSimple-enhet](storsimple-configure-chap.md) |
| Lägga till en säkerhetskopieringspolicy</br>Lägg till eller ändra ett schema</br>Ta bort en princip för säkerhetskopiering</br>Gör en manuell säkerhetskopia</br>Skapa en anpassad princip för säkerhetskopiering med flera volymer och scheman |[Hantera säkerhetskopieringsprinciper](storsimple-8000-manage-backup-policies-u2.md) |
| Stoppa styrenheter</br>Starta om styrenheter</br>Stäng av styrenheter</br>Återställa enheten till fabriksinställningarna</br>(Ovan är för lokala-enhet) |[Hantera styrenhet för StorSimple-enhet](storsimple-8000-manage-device-controller.md) |
| Lär dig mer om StorSimple maskinvarukomponenter</br>Övervakaren maskinvarustatus</br>(Ovan är för lokala-enhet) |[Övervakaren maskinvarukomponenter](storsimple-8000-monitor-hardware-status.md) |
| Skapa ett supportpaket |[Skapa och hantera ett stödpaket](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple) |
| Installera programuppdateringar |[Uppdatera din enhet](storsimple-update-device.md) |

## <a name="next-steps"></a>Nästa steg

Om du får problem med den dagliga driften av StorSimple-enheten eller någon av dess komponenter, se:

* [Felsöka med hjälp av verktyget Nätverksdiagnostik](storsimple-8000-diagnostics.md)
* [Använd StorSimple övervakning indikatorer](storsimple-monitoring-indicators.md)

Om du inte kan lösa problem och du behöver skapa en serviceförfrågan, referera till [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md).

