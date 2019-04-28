---
title: Microsoft Azure StorSimple Manager Virtual Array administration | Microsoft Docs
description: Lär dig hur du hanterar en lokal StorSimple Virtual Array med hjälp av StorSimple Device Manager-tjänsten i Azure-portalen.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 958244a5-f9f5-455e-b7ef-71a65558872e
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/1/2016
ms.author: alkohli
ms.openlocfilehash: bb6bb491ca71e5ced5aecc8137e9e1cbd950e80b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123813"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>Använda StorSimple Device Manager-tjänsten för att administrera din StorSimple Virtual Array
![processflöde för installationen](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Översikt
Den här artikeln beskriver gränssnittet StorSimple Device Manager service, inklusive hur du ansluter till den och de olika alternativen och innehåller länkar till specifika arbetsflöden som kan utföras via det här Gränssnittet.

När du har läst den här artikeln, vet du hur du:

* Ansluta till StorSimple Device Manager-tjänsten
* Navigera StorSimple Device Manager UI
* Administrera din StorSimple Virtual Array via StorSimple Device Manager-tjänsten

> [!NOTE]
> Om du vill visa hanteringsalternativ som är tillgängliga för enhet för StorSimple 8000-serien, gå till [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).
> 
> 

## <a name="connect-to-the-storsimple-device-manager-service"></a>Ansluta till StorSimple Device Manager-tjänsten
StorSimple Device Manager-tjänsten körs i Microsoft Azure och ansluter till flera StorSimple Virtual Array. Du kan använda en central Microsoft Azure-portalen som körs i en webbläsare för att hantera dessa enheter. Gör följande om du vill ansluta till StorSimple Device Manager-tjänsten.

#### <a name="to-connect-to-the-service"></a>Att ansluta till tjänsten
1. Gå till [https://ms.portal.azure.com](https://ms.portal.azure.com).
2. Med autentiseringsuppgifterna för ditt Microsoft-konto kan logga in på Microsoft Azure-portalen (finns i upp till höger i fönstret).
3. Gå till Bläddra--> ”Filtrera” på StorSimple Device Manager för att visa alla enhetshanterare i en viss prenumeration.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>Använda StorSimple Device Manager-tjänsten för att utföra administrativa uppgifter
I följande tabell visas en sammanfattning av alla vanliga administrationsuppgifter och komplexa arbetsflöden som kan utföras i sammanfattningsbladet för StorSimple Device Manager-tjänsten. De här uppgifterna har ordnats baserat på bladen har initierats.

Klicka på lämplig procedur i tabellen för mer information om varje arbetsflöde.

#### <a name="storsimple-device-manager-workflows"></a>StorSimple Device Manager-arbetsflöden
| Om du vill göra detta... | Använd den här proceduren |
| --- | --- |
| Skapa en tjänst</br>Ta bort en tjänst</br>Hämta nyckel för tjänstregistrering</br>Återskapa nyckel för tjänstregistrering |[Distribuera StorSimple Device Manager-tjänsten](storsimple-virtual-array-manage-service.md) |
| Visa aktivitetsloggar |[Använd sammanfattning för StorSimple-tjänsten](storsimple-virtual-array-service-summary.md) |
| Inaktivera en virtuell matris</br>Ta bort en virtuell matris |[Inaktivera eller ta bort en virtuell matris](storsimple-virtual-array-deactivate-and-delete-device.md) |
| Disaster recovery och enheten redundans</br>Förutsättningar för redundans</br>Kontinuitet för företag-haveriberedskap (BCDR)</br>Fel vid katastrofåterställning |[Disaster recovery och enheten redundans för StorSimple Virtual Array](storsimple-virtual-array-failover-dr.md) |
| Säkerhetskopiering av filresurser och volymer</br>Gör en manuell säkerhetskopia</br>Ändra schemat för säkerhetskopiering</br>Visa befintliga säkerhetskopieringar |[Säkerhetskopiera din StorSimple Virtual Array](storsimple-virtual-array-backup.md) |
| Klona resurser från en säkerhetskopia</br>Klona volymer från en säkerhetskopia</br>Återställning på objektnivå (endast filserver) |[Klona från en säkerhetskopia av StorSimple Virtual Array](storsimple-virtual-array-clone.md) |
| Om storage-konton</br>Lägg till ett lagringskonto</br>Redigera ett storage-konto</br>Ta bort ett lagringskonto |[Hantera lagringskonton för StorSimple Virtual Array](storsimple-virtual-array-manage-storage-accounts.md) |
| Om åtkomstkontrollposter</br>Lägg till eller ändra en åtkomstkontrollpost </br>Ta bort en åtkomstkontrollpost |[Hantera åtkomstkontrollposter för StorSimple Virtual Array](storsimple-virtual-array-manage-acrs.md) |
| Visa jobbinformation |[Hantera StorSimple Virtual Array-jobb](storsimple-virtual-array-manage-jobs.md) |
| Konfigurera aviseringsinställningar</br>Få varningsmeddelanden</br>Hantera aviseringar</br>Granska aviseringar |[Visa och hantera aviseringar för StorSimple Virtual Array](storsimple-virtual-array-manage-alerts.md) |
| Ändra enhetens administratörslösenord |[Ändra administratörslösenordet för enheten StorSimple Virtual Array](storsimple-virtual-array-change-device-admin-password.md) |
| Installation av programuppdateringar |[Uppdatera din virtuella matris](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> Du måste använda den [lokala webbgränssnittet](storsimple-ova-web-ui-admin.md) för följande uppgifter:
> 
> * [Hämta krypteringsnyckeln för tjänstdata](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [Skapa ett supportpaket](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [Stoppa och starta om en virtuell matris](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>Nästa steg
Information om webbgränssnittet och hur du använder den, går du till [använda StorSimple webbgränssnittet för att administrera din StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

