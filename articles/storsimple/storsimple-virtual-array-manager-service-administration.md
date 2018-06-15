---
title: Microsoft Azure StorSimple Manager virtuella matris administration | Microsoft Docs
description: Lär dig hur du hanterar lokala StorSimple virtuell matrisen med hjälp av Enhetshanteraren för StorSimple-tjänsten i Azure-portalen.
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
ms.openlocfilehash: a74a160eae88a2d03460a1346479c333d8f9d524
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875846"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>Använda Enhetshanteraren för StorSimple-tjänsten för att administrera din virtuella StorSimple-matris
![processflöde för installationen](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Översikt
Den här artikeln beskriver gränssnittet StorSimple Device Manager service, inklusive hur du ansluter till den och de olika alternativen och innehåller länkar till de specifika arbetsflöden som kan utföras via Användargränssnittet.

När du har läst den här artikeln kommer du att veta hur du:

* Ansluta till tjänsten StorSimple Device Manager
* Navigera StorSimple Enhetshanteraren UI
* Administrera din virtuella StorSimple-matris via StorSimple Device Manager service

> [!NOTE]
> Om du vill visa vilka hanteringsalternativ som finns tillgängliga för StorSimple 8000-serieenhet, gå till [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).
> 
> 

## <a name="connect-to-the-storsimple-device-manager-service"></a>Ansluta till tjänsten StorSimple Device Manager
StorSimple Device Manager-tjänsten körs i Microsoft Azure och ansluter till flera virtuella StorSimple-matriser. Du kan använda en central Microsoft Azure-portalen som körs i en webbläsare för att hantera dessa enheter. Gör följande för att ansluta till tjänsten StorSimple Enhetshanteraren.

#### <a name="to-connect-to-the-service"></a>Att ansluta till tjänsten
1. Gå till [https://ms.portal.azure.com](https://ms.portal.azure.com).
2. Med autentiseringsuppgifterna för ditt Microsoft-konto kan logga in på Microsoft Azure-portalen (finns i det övre högerkant fönstret).
3. Gå till Bläddra--> 'Filtrera' för StorSimple-enhet chefer att visa alla enhetshanterare för en viss prenumeration.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>Använda Enhetshanteraren för StorSimple-tjänsten för att utföra administrativa uppgifter
I följande tabell visas en sammanfattning av alla vanliga administrativa uppgifter och komplexa arbetsflöden som kan utföras i bladet StorSimple Enhetshanteraren service sammanfattning. Aktiviteterna är uppdelade utifrån blad har initierats.

Mer information om varje arbetsflöde klickar du på lämplig procedur i tabellen.

#### <a name="storsimple-device-manager-workflows"></a>StorSimple Device Manager-arbetsflöden
| Om du vill göra detta... | Använd den här proceduren |
| --- | --- | --- |
| Skapa en tjänst</br>Ta bort en tjänst</br>Hämta nyckel för tjänstregistrering</br>Återskapa nyckeln för tjänstregistrering |[Distribuera StorSimple enheten Manager-tjänsten](storsimple-virtual-array-manage-service.md) |
| Kontrollera händelseloggarna för aktiviteten |[Använda StorSimple-Tjänstsammanfattning](storsimple-virtual-array-service-summary.md) |
| Inaktivera en virtuell matris</br>Ta bort en virtuell matris |[Inaktivera eller ta bort en virtuell matris](storsimple-virtual-array-deactivate-and-delete-device.md) |
| Disaster recovery och enheten redundans</br>Krav för redundans</br>Katastrofåterställning för verksamhetskontinuitet (BCDR)</br>Fel vid katastrofåterställning |[Disaster recovery och enheten redundans för din virtuella StorSimple-matris](storsimple-virtual-array-failover-dr.md) |
| Säkerhetskopiera filresurser och volymer</br>Gör en manuell säkerhetskopia</br>Ändra schemat för säkerhetskopiering</br>Visa befintliga säkerhetskopior |[Säkerhetskopiera din virtuella StorSimple-matris](storsimple-virtual-array-backup.md) |
| Klona resurser från en säkerhetskopia</br>Klona volymer från en säkerhetskopia</br>Återställning på objektnivå (endast filserver) |[Klona från en säkerhetskopia av din virtuella StorSimple-matris](storsimple-virtual-array-clone.md) |
| Om storage-konton</br>Lägg till ett lagringskonto</br>Redigera ett lagringskonto</br>Ta bort ett lagringskonto |[Hantera storage-konton för den virtuella StorSimple-matris](storsimple-virtual-array-manage-storage-accounts.md) |
| Om access control-poster</br>Lägg till eller ändra en åtkomstkontrollpost </br>Ta bort en åtkomstkontrollpost |[Hantera access control-poster för den virtuella StorSimple-matris](storsimple-virtual-array-manage-acrs.md) |
| Visa jobbinformation |[Hantera virtuella StorSimple-matris jobb](storsimple-virtual-array-manage-jobs.md) |
| Konfigurera aviseringsinställningar</br>Få varningsmeddelanden</br>Hantera aviseringar</br>Granska aviseringar |[Visa och hantera aviseringar för den virtuella StorSimple-matrisen](storsimple-virtual-array-manage-alerts.md) |
| Ändra enhetens administratörslösenord |[Ändra virtuella StorSimple-matris enhetens administratörslösenord](storsimple-virtual-array-change-device-admin-password.md) |
| Installera programuppdateringar |[Uppdatera din virtuella matris](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> Du måste använda den [lokala webbgränssnittet](storsimple-ova-web-ui-admin.md) för följande uppgifter:
> 
> * [Hämta krypteringsnyckel för tjänstdata](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [Skapa ett supportpaket](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [Stoppa och starta om en virtuell matris](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>Nästa steg
Information om webbgränssnittet och hur du använder den, gå till [använda webbgränssnittet för StorSimple för att administrera din virtuella StorSimple-matris](storsimple-ova-web-ui-admin.md).

