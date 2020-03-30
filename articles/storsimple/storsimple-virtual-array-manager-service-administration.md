---
title: Microsoft Azure StorSimple Manager Virtual Array administration | Microsoft-dokument
description: Lär dig hur du hanterar din lokala StorSimple-array med hjälp av StorSimple Device Manager-tjänsten i Azure-portalen.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "62123813"
---
# <a name="use-the-storsimple-device-manager-service-to-administer-your-storsimple-virtual-array"></a>Använda Tjänsten StorSimple Device Manager för att administrera din virtuella storsimple-array
![flödet för installationsprocess](./media/storsimple-virtual-array-manager-service-administration/manage4.png)

## <a name="overview"></a>Översikt
I den här artikeln beskrivs tjänstgränssnittet i StorSimple Device Manager, inklusive hur du ansluter till det och de olika tillgängliga alternativen, och länkar till de specifika arbetsflöden som kan utföras via det här användargränssnittet.

Efter att ha läst den här artikeln kommer du att veta hur man:

* Ansluta till Tjänsten StorSimple Device Manager
* Navigera i storsimple-enhetshanterarens användargränssnitt
* Administrera din Virtuella StorSimple-matris via StorSimple Device Manager-tjänsten

> [!NOTE]
> Om du vill visa vilka hanteringsalternativ som är tillgängliga för StorSimple 8000-serien går du till [Använd StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).
> 
> 

## <a name="connect-to-the-storsimple-device-manager-service"></a>Ansluta till Tjänsten StorSimple Device Manager
StorSimple Device Manager-tjänsten körs i Microsoft Azure och ansluter till flera virtuella StorSimple-matriser. Du använder en central Microsoft Azure-portal som körs i en webbläsare för att hantera dessa enheter. Gör följande om du vill ansluta till Tjänsten StorSimple Device Manager.

#### <a name="to-connect-to-the-service"></a>Så här ansluter du till tjänsten
1. Gå [https://ms.portal.azure.com](https://ms.portal.azure.com)till .
2. Logga in på Microsoft Azure-portalen med dina Microsoft-kontouppgifter (längst upp till höger i fönstret).
3. Navigera till Bläddra till Bläddra --> 'Filter' på StorSimple Enhetshanterare för att visa alla dina enhetshanterare i en viss prenumeration.

## <a name="use-the-storsimple-device-manager-service-to-perform-management-tasks"></a>Använda Tjänsten StorSimple Device Manager för att utföra hanteringsuppgifter
I följande tabell visas en sammanfattning av alla vanliga hanteringsuppgifter och komplexa arbetsflöden som kan utföras i sammanfattningsbladet för Tjänsten StorSimple Device Manager. Dessa uppgifter är ordnade baserat på de blad som de initieras på.

Om du vill ha mer information om varje arbetsflöde klickar du på lämplig procedur i tabellen.

#### <a name="storsimple-device-manager-workflows"></a>Arbetsflöden för StorSimple-Enhetshanteraren
| Om du vill göra detta ... | Använd den här proceduren |
| --- | --- |
| Skapa en tjänst</br>Ta bort en tjänst</br>Hämta nyckel för tjänstregistrering</br>Återskapa nyckeln för tjänstregistrering |[Distribuera Tjänsten StorSimple Device Manager](storsimple-virtual-array-manage-service.md) |
| Visa aktivitetsloggarna |[Använda storsimple-servicesammanfattningen](storsimple-virtual-array-service-summary.md) |
| Inaktivera en virtuell matris</br>Ta bort en virtuell matris |[Inaktivera eller ta bort en virtuell matris](storsimple-virtual-array-deactivate-and-delete-device.md) |
| Katastrofåterställning och enhetsväxling</br>Förutsättningar för redundans</br>Återställning av haveuliker för affärskontinuitet (BCDR)</br>Fel vid haveriberedskap |[Katastrofåterställning och enhetsväxling för den virtuella storsimple-matrisen](storsimple-virtual-array-failover-dr.md) |
| Säkerhetskopiera aktier och volymer</br>Gör en manuell säkerhetskopiering</br>Ändra schemat för säkerhetskopiering</br>Visa befintliga säkerhetskopior |[Säkerhetskopiera din StorSimple Virtual Array](storsimple-virtual-array-backup.md) |
| Klona resurser från en uppsättning säkerhetskopior</br>Klona volymer från en uppsättning säkerhetskopior</br>Återställning på objektnivå (endast filserver) |[Klona från en säkerhetskopia av storsimple-virtual array](storsimple-virtual-array-clone.md) |
| Om lagringskonton</br>Lägga till ett lagringskonto</br>Redigera ett lagringskonto</br>Ta bort ett lagringskonto |[Hantera lagringskonton för StorSimple Virtual Array](storsimple-virtual-array-manage-storage-accounts.md) |
| Om åtkomstkontrollposter</br>Lägga till eller ändra en åtkomstkontrollpost </br>Ta bort en åtkomstkontrollpost |[Hantera åtkomstkontrollposter för StorSimple Virtual Array](storsimple-virtual-array-manage-acrs.md) |
| Visa jobbinformation |[Hantera StorSimple-jobb med virtuell array](storsimple-virtual-array-manage-jobs.md) |
| Konfigurera varningsinställningar</br>Få varningsmeddelanden</br>Hantera aviseringar</br>Granska aviseringar |[Visa och hantera aviseringar för StorSimple Virtual Array](storsimple-virtual-array-manage-alerts.md) |
| Ändra lösenordet för enhetsadministratören |[Ändra administratörslösenordet för StorSimple Virtual Array-enheten](storsimple-virtual-array-change-device-admin-password.md) |
| Installera programuppdateringar |[Uppdatera din virtuella matris](storsimple-virtual-array-install-update.md) |

> [!NOTE]
> Du måste använda det [lokala webbgränssnittet](storsimple-ova-web-ui-admin.md) för följande uppgifter:
> 
> * [Hämta krypteringsnyckeln för tjänstdata](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
> * [Skapa ett supportpaket](storsimple-ova-web-ui-admin.md#generate-a-log-package)
> * [Stoppa och starta om en virtuell matris](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)
> 
> 

## <a name="next-steps"></a>Nästa steg
Information om webbgränssnittet och hur du använder det finns i [Använda webbgränssnittet i StorSimple för att administrera den virtuella storsimple-matrisen](storsimple-ova-web-ui-admin.md).

