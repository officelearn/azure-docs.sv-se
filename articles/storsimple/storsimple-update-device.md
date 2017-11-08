---
title: Uppdatera din StorSimple-enhet | Microsoft Docs
description: "Förklarar hur du använder funktionen StorSimple för att installera vanliga och underhåll läge uppdateringar och snabbkorrigeringar."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 786059f5-2a38-4105-941d-0860ce4ac515
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 247c422d7ea3feeec1342bb20f99390a07eb4f7a
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="update-your-storsimple-8000-series-device"></a>Uppdatera enheten StorSimple 8000-serien
> [!NOTE]
> Den klassiska portalen för StorSimple är föråldrad. Din StorSimple-enhetshanterare flyttas automatiskt till den nya Azure portalen enligt utfasningen schemat. Du får ett e-postmeddelande och portalmeddelandet för flyttningen. Det här dokumentet kommer också att dragits tillbaka snart. Frågor om flyttningen, se [vanliga frågor och svar: flyttar till Azure-portalen](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Översikt
StorSimple uppdateringar funktionerna kan du enkelt kan behålla din StorSimple-enhet uppdaterade. Beroende på uppdateringstyp av kan tillämpa du uppdateringar på enheten via den klassiska Azure-portalen eller via Windows PowerShell-gränssnittet. Den här självstudiekursen beskriver uppdateringstyperna och hur du installerar dem.

Du kan använda två typer av uppdateringar: 

* Vanliga (eller normalläge) uppdateringar
* Underhåll läge uppdateringar

Du kan installera regelbundna uppdateringar via den klassiska Azure-portalen eller Windows PowerShell; dock måste du använda Windows PowerShell för att installera uppdateringar för underhåll läge. 

Varje uppdatering är separat, nedan.

### <a name="regular-updates"></a>Regelbundna uppdateringar
Regelbundna uppdateringar är icke-störande uppdateringar som kan installeras när enheten är i normalläge. Uppdateringarna tillämpas på varje enhet styrenhet via Microsoft Update-webbplatsen. 

> [!IMPORTANT]
> En domänkontrollant och växling vid fel kan uppstå under uppdateringen. Men påverkar detta inte systemets tillgänglighet eller åtgärden.
> 
> 

* Mer information om hur du installerar regelbundna uppdateringar via den klassiska Azure-portalen finns [installera regelbundna uppdateringar via den klassiska Azure-portalen](#install-regular-updates-via-the-azure-classic-portal).
* Du kan också installera regelbundna uppdateringar via Windows PowerShell för StorSimple. Mer information finns i [installerar regelbundna uppdateringar via Windows PowerShell för StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Underhåll läge uppdateringar
Underhåll läge uppdateringar är störande uppdateringar, till exempel disk firmware-uppgraderingar. Dessa uppdateringar kräver att enheten ska placeras i underhållsläge. Mer information finns i [steg 2: Ange underhållsläge](#step2). Du kan inte använda den klassiska Azure-portalen för att installera uppdateringar för underhåll läge. I stället måste du använda Windows PowerShell för StorSimple. 

Mer information om hur du installerar uppdateringar för underhåll lägen finns [installera Underhåll läge uppdateringar via Windows PowerShell för StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [!IMPORTANT]
> Underhåll läge uppdateringar måste tillämpas separat på varje domänkontrollant. 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Installera regelbundna uppdateringar via den klassiska Azure-portalen
Du kan använda den klassiska Azure-portalen för att tillämpa uppdateringar på din StorSimple-enhet.

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Installera regelbundna uppdateringar via Windows PowerShell för StorSimple
Du kan också använda Windows PowerShell för StorSimple för att tillämpa uppdateringar regular (normalt läge).

> [!IMPORTANT]
> Du kan installera regelbundna uppdateringar med hjälp av Windows PowerShell för StorSimple, rekommenderar vi att du installerar regelbundna uppdateringar från den klassiska Azure-portalen. Från och med uppdatering 1 kommer före kontroller att utföras innan du installerar uppdateringar från portalen. Kontrollerna före åsidosätter fel och ge en jämnare upplevelse. 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Installera Underhåll läge uppdateringar via Windows PowerShell för StorSimple
Du kan använda Windows PowerShell för StorSimple för att installera uppdateringar för underhåll läge för din StorSimple-enhet. Alla i/o-begäranden är pausade i det här läget. Tjänster, till exempel beständiga RAM-minne (NVRAM) eller klustertjänsten stoppas. Både domänkontrollanter startas om när du anger eller avsluta det här läget. När du lämnar det här läget kommer att återuppta alla tjänster och bör vara felfritt. (Detta kan ta några minuter.)

Om du behöver tillämpa Underhåll läge uppdateringar visas en avisering via den klassiska Azure-portalen som du har uppdateringar som måste installeras. Den här aviseringen innehåller instruktioner för att använda Windows PowerShell för StorSimple för att installera uppdateringarna. När du har uppdaterat enheten kan du använda samma procedur för att ändra enheten till standardläget. Stegvisa instruktioner finns [steg 4: avsluta underhållsläget](#step4).

> [!IMPORTANT]
> * Innan du anger underhållsläge, kontrollera att båda styrenheter är felfri genom att kontrollera den **maskinvarustatus** på den **Underhåll** sida i den klassiska Azure-portalen. Kontakta Microsofts Support för nästa steg om styrenheten inte är felfri. Mer information finns i kontaktar Microsoft support. 
> * När du är i underhållsläge, måste du först installera uppdateringen på en domänkontrollant och sedan på den andra styrenheten.
> 
> 

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>Steg 1: Anslut till seriekonsol<a name="step1">
Först måste du använda ett program, till exempel PuTTY för att komma åt seriekonsolen. Följande procedur beskriver hur du använder PuTTY för att ansluta till seriekonsol.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>Steg 2: Ange underhållsläge<a name="step2">
När du ansluter till konsolen kan du avgöra om det finns uppdateringar för att installera och ange Underhållsläge för att installera dem.

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>Steg 3: Installera uppdateringar<a name="step3">
Installera uppdateringarna.

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>Steg 4: Avsluta underhållsläge<a name="step4">
Slutligen ska du avsluta underhållsläget.

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Installera snabbkorrigeringar via Windows PowerShell för StorSimple
Till skillnad från uppdateringar för Microsoft Azure StorSimple installeras snabbkorrigeringarna från en delad mapp. Precis som med uppdateringar, finns det två typer av snabbkorrigeringar: 

* Vanliga snabbkorrigeringar 
* Underhåll läge snabbkorrigeringar  

Följande procedurer beskriver hur du använder Windows PowerShell för StorSimple för att installera vanliga och underhåll läge snabbkorrigeringar.

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>Vad händer med uppdateringar om du utför en fabriksåterställning på enheten?
Om en enhet återställs till fabriksinställningarna, förloras alla uppdateringar. När fabriksåterställning enheten är registrerad och konfigurerad, måste du manuellt installera uppdateringar via den klassiska Azure-portalen och/eller Windows PowerShell för StorSimple. Läs mer om fabriksåterställning [återställa enheten till fabriksinställningarna](storsimple-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [med Windows PowerShell för StorSimple för att administrera din StorSimple-enhet](storsimple-windows-powershell-administration.md).
* Lär dig mer om [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

