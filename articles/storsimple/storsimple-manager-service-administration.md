---
title: Administrationen av StorSimple Manager | Microsoft Docs
description: "Lär dig hur du hanterar din StorSimple-enhet med StorSimple Manager-tjänsten i den klassiska Azure-portalen."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 2586582e-d85c-42e1-afb3-be734c1c0461
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2016
ms.author: alkohli
ms.openlocfilehash: 22924da07434a06f4c822d97a2afd02ea982e0e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-the-storsimple-manager-service-to-administer-your-storsimple-device"></a>Använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet
## <a name="overview"></a>Översikt
Den här artikeln beskriver StorSimple Managers tjänstgränssnitt, inklusive hur du ansluter till den, de olika alternativen och länkar ut till specifika arbetsflöden som kan utföras via Användargränssnittet. Den här vägledningen gäller både; fysiskt StorSimple och den virtuella enheten.

När du har läst den här artikeln får du lära dig att:

* Ansluta till StorSimple Manager-tjänsten
* Navigera StorSimple Manager-Gränssnittet
* Administrera din StorSimple-enhet via StorSimple Manager-tjänsten

## <a name="connect-to-storsimple-manager-service"></a>Ansluta till StorSimple Manager-tjänsten
StorSimple Manager-tjänsten körs i Microsoft Azure och ansluter till flera StorSimple-enheter. Du kan använda en central klassiska Microsoft Azure-portalen som körs i en webbläsare för att hantera dessa enheter. Gör följande för att ansluta till StorSimple Manager-tjänsten.

#### <a name="to-connect-to-the-service"></a>Att ansluta till tjänsten
1. Gå till [https://manage.windowsazure.com/](https://manage.windowsazure.com/).
2. Med autentiseringsuppgifterna för ditt Microsoft-konto kan logga in på Microsoft Azure klassiska portal (finns i det övre högerkant fönstret).
3. Rulla ned det vänstra navigeringsfönstret för att få åtkomst till StorSimple Manager-tjänsten.

## <a name="navigate-storsimple-manager-service-ui"></a>Navigera StorSimple Manager-tjänsten UI
Navigering hierarkin för StorSimple Manager-tjänsten Användargränssnittet visas i följande tabell.

* **StorSimple Manager** landningssida går du till Användargränssnittet på tjänstenivå sidorna gäller för alla enheter i en tjänst.
* **Enheter** sidan går du till enhetsnivå – UI sidorna gäller för en specifik enhet.
* **Volymbehållare** sidan går du till sidan volymen som visar alla volymer som är kopplade till en enhet.

#### <a name="storsimple-manager-service-navigational-hierarchy"></a>Navigering hierarkin för StorSimple Manager-tjänsten
| Landningssida | Servicenivåer sidor | Enhetsnivå sidor | Enhetsnivå sidor |
| --- | --- | --- | --- |
| StorSimple Manager-tjänsten |Instrumentpanel |Instrumentpanel för enhet | |
| Enheter → |Övervaka | | |
| Säkerhetskopieringskatalogen |Volymen containers→ |Volymer | |
| Konfigurera (Service) |Principer för säkerhetskopiering | | |
| Jobb |Konfigurera (enhet) | | |
| Aviseringar |Underhåll | | |

![Video tillgänglig](./media/storsimple-manager-service-administration/Video_icon.png) **Video tillgänglig**

Om du vill se en video som vägleder dig genom användargränssnittet för StorSimple Manager-tjänsten klickar du på [här](https://azure.microsoft.com/documentation/videos/storsimple-manager-service-overview/).

## <a name="administer-storsimple-device-using-storsimple-manager-service"></a>Administrera StorSimple-enhet med StorSimple Manager-tjänsten
I följande tabell visas en sammanfattning av alla vanliga administrativa uppgifter och komplexa arbetsflöden som kan utföras i Användargränssnittet för StorSimple Manager-tjänsten. Dessa uppgifter är ordnade baserat på de UI-sidor som har initierats.

Mer information om varje arbetsflöde klickar du på lämplig procedur i tabellen.

#### <a name="storsimple-manager-workflows"></a>StorSimple Manager-arbetsflöden
| Om du vill göra detta... | Gå till den här sidan... | Använd den här proceduren. |
| --- | --- | --- |
| Skapa en tjänst</br>Ta bort en tjänst</br>Hämta nyckel för tjänstregistrering</br>Återskapa nyckeln för tjänstregistrering |StorSimple Manager-tjänsten |[Distribuera en StorSimple Manager-tjänst](storsimple-manage-service.md) |
| Ändra krypteringsnyckeln för tjänstdata</br>Kontrollera händelseloggarna för åtgärden |Instrumentpanel för StorSimple Manager-tjänsten → |[Använd instrumentpanelen för StorSimple Manager-tjänsten](storsimple-service-dashboard.md) |
| Inaktivera en enhet</br>Ta bort en enhet |Enheter för StorSimple Manager-tjänsten → |[Inaktivera eller ta bort en enhet](storsimple-deactivate-and-delete-device.md) |
| Lär dig mer om disaster recovery och enheten redundans</br>Växling till en fysisk enhet</br>Växling till en virtuell enhet</br>Katastrofåterställning för verksamhetskontinuitet (BCDR) |Enheter för StorSimple Manager-tjänsten → |[Redundans och disaster recovery för din StorSimple-enhet](storsimple-device-failover-disaster-recovery.md) |
| Lista säkerhetskopieringar för en volym</br>Välj en säkerhetskopia</br>Ta bort en säkerhetskopia |Säkerhetskopieringskatalog StorSimple Manager-tjänsten → |[Hantera säkerhetskopior](storsimple-manage-backup-catalog.md) |
| Klona en volym |Säkerhetskopieringskatalog StorSimple Manager-tjänsten → |[Klona en volym](storsimple-clone-volume.md) |
| Återställa en säkerhetskopia |Säkerhetskopieringskatalog StorSimple Manager-tjänsten → |[Återställa en säkerhetskopia](storsimple-restore-from-backup-set.md) |
| Om storage-konton</br>Lägg till ett lagringskonto</br>Redigera ett lagringskonto</br>Ta bort ett lagringskonto</br>Viktiga rotation av storage-konton |Konfigurera StorSimple Manager service → |[Hantera lagringskonton](storsimple-manage-storage-accounts.md) |
| Om bandbredd mallar</br>Lägg till en bandbreddsmall</br>Redigera en bandbreddsmall</br>Ta bort en bandbreddsmall</br>Använda en standardmall för bandbredd</br>Skapa en varar bandbreddsmall som börjar vid en angiven tid |Konfigurera StorSimple Manager service → |[Hantera bandbreddsmallar](storsimple-manage-bandwidth-templates.md) |
| Om access control-poster</br>Skapa en åtkomstkontrollpost</br>Redigera en åtkomstkontrollpost</br>Ta bort en åtkomstkontrollpost |Konfigurera StorSimple Manager service → |[Hantera access control-poster](storsimple-manage-acrs.md) |
| Visa jobbinformation</br>Avbryta ett jobb |Jobb för StorSimple Manager-tjänsten → |[Hantera jobb](storsimple-manage-jobs.md) |
| Få varningsmeddelanden</br>Hantera aviseringar</br>Granska aviseringar |Tjänstvarningar med StorSimple Manager → |[Visa och hantera aviseringar för StorSimple](storsimple-manage-alerts.md) |
| Visa anslutna initierare</br>Hitta enhetens serienummer</br>Hitta målet IQN |Enheter för StorSimple Manager-tjänsten → → instrumentpanelen |[Använd instrumentpanelen för StorSimple-enhet](storsimple-device-dashboard.md) |
| Skapa övervakning diagram |Enheter för StorSimple Manager-tjänsten → övervaka → |[Övervaka din StorSimple-enhet](storsimple-monitor-device.md) |
| Lägg till volymbehållare</br>Ändra en volymbehållare</br>Ta bort en volymbehållare |Volymbehållare för StorSimple Manager-tjänsten → enheter → |[Hantera volymbehållare](storsimple-manage-volume-containers.md) |
| Lägg till en volym</br>Ändra en volym</br>Koppla från en volym</br>Ta bort en volym</br>Övervaka en volym |StorSimple Manager-tjänsten → enheter → Volymbehållare →-volymer |[Hantera volymer](storsimple-manage-volumes.md) |
| Ändra inställningar för enheter</br>Ändra inställningarna för tid</br>Ändra inställningar för DNS.md</br>Konfigurera nätverksgränssnitt |StorSimple Manager service → enheter konfigurera → |[Ändra enhetskonfigurationen för din StorSimple-enhet](storsimple-modify-device-config.md) |
| Visa web proxy-inställningar |StorSimple Manager service → enheter konfigurera → |[Konfigurera webbproxy för din enhet](storsimple-configure-web-proxy.md) |
| Ändra enhetens administratörslösenord</br>Ändra lösenordet för StorSimple Snapshot Manager |StorSimple Manager service → enheter konfigurera → |[Ändra StorSimple-lösenord](storsimple-change-passwords.md) |
| Konfigurera fjärrhantering |StorSimple Manager service → enheter konfigurera → |[Fjärransluta till din StorSimple-enhet](storsimple-remote-connect.md) |
| Konfigurera aviseringsinställningar |StorSimple Manager service → enheter konfigurera → |[Visa och hantera aviseringar för StorSimple](storsimple-manage-alerts.md) |
| Konfigurera CHAP för din StorSimple-enhet |StorSimple Manager service → enheter konfigurera → |[Konfigurera CHAP för din StorSimple-enhet](storsimple-configure-chap.md) |
| Lägga till en säkerhetskopieringspolicy</br>Lägg till eller ändra ett schema</br>Ta bort en princip för säkerhetskopiering</br>Gör en manuell säkerhetskopia</br>Skapa en anpassad princip för säkerhetskopiering med flera volymer och scheman |Säkerhetskopieringsprinciper för StorSimple Manager service → enheter → |[Hantera säkerhetskopieringsprinciper](storsimple-manage-backup-policies.md) |
| Stoppa styrenheter</br>Starta om styrenheter</br>Stäng av styrenheter</br>Återställa enheten till fabriksinställningarna</br>(Ovan är för lokala-enhet) |Enheter för StorSimple Manager-tjänsten → → Underhåll |[Hantera styrenhet för StorSimple-enhet](storsimple-manage-device-controller.md) |
| Lär dig mer om StorSimple maskinvarukomponenter</br>Övervakaren maskinvarustatus</br>(Ovan är för lokala-enhet) |Enheter för StorSimple Manager-tjänsten → → Underhåll |[Övervakaren maskinvarukomponenter](storsimple-monitor-hardware-status.md) |
| Skapa ett supportpaket |Enheter för StorSimple Manager-tjänsten → → Underhåll |[Skapa och hantera ett stödpaket](storsimple-create-manage-support-package.md) |
| Installera programuppdateringar |Enheter för StorSimple Manager-tjänsten → → Underhåll |[Uppdatera din enhet](storsimple-update-device.md) |

## <a name="next-steps"></a>Nästa steg
Om du får problem med den dagliga driften av StorSimple-enheten eller någon av dess komponenter, se:

* [Felsöka en operativa enhet](storsimple-troubleshoot-operational-device.md)
* [Använd StorSimple övervakning indikatorer](storsimple-monitoring-indicators.md)

Om du inte kan lösa problem och du behöver skapa en serviceförfrågan, referera till [kontakta Microsoft Support](storsimple-contact-microsoft-support.md).

