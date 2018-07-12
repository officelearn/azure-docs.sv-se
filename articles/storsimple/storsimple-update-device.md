---
title: Uppdatera din StorSimple-enhet | Microsoft Docs
description: Beskriver hur du använder funktionen StorSimple för att installera uppdateringar av vanliga och underhåll och snabbkorrigeringar.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: 786059f5-2a38-4105-941d-0860ce4ac515
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: v-sharos
ms.openlocfilehash: 412978d2c343394f295e336690ec72153dda4b79
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38452625"
---
# <a name="update-your-storsimple-8000-series-device"></a>Uppdatera din enhet i StorSimple 8000-serien
> [!NOTE]
> Den klassiska portalen för StorSimple är inaktuell. Dina StorSimple-enhetshanterare flyttas automatiskt till nya Azure Portal enligt utfasningsschemat. Du kommer att få ett e-postmeddelande och ett portalmeddelande om flytten. Det här dokumentet kommer också att dras tillbaka snart. Om du har frågor om flytten kan du läsa [Vanliga frågor och svar: Flytta till Azure Portal](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Översikt
Funktioner för StorSimple-uppdateringar kan du enkelt hålla din StorSimple-enhet som är uppdaterade. Beroende på uppdateringstyp av kan installera du uppdateringar till enheten via den klassiska Azure-portalen eller via Windows PowerShell-gränssnittet. Den här självstudien beskrivs uppdateringstyperna och hur du installerar dem.

Du kan använda två typer av uppdatering av enheter: 

* Vanliga (eller normalläge) uppdateringar
* Uppdateringar av underhållsläge

Du kan installera regelbundna uppdateringar via den klassiska Azure-portalen eller Windows PowerShell; dock måste du använda Windows PowerShell för att installera uppdateringar av underhållsläge. 

Varje uppdatering är beskrivs separat, nedan.

### <a name="regular-updates"></a>Regelbundna uppdateringar
Regelbundna uppdateringar är avbrottsfria uppdateringar som kan installeras när enheten är i normalläge. De här uppdateringarna tillämpas på varje enhetsstyrenhet via Microsoft Update-webbplatsen. 

> [!IMPORTANT]
> En kontrollenhetsredundans kan uppstå under uppdateringen. Men påverkar detta inte systemets tillgänglighet eller åtgärden.
> 
> 

* Mer information om hur du installerar regelbundna uppdateringar via den klassiska Azure-portalen finns i [installera regelbundna uppdateringar via den klassiska Azure-portalen](#install-regular-updates-via-the-azure-classic-portal).
* Du kan också installera regelbundna uppdateringar via Windows PowerShell för StorSimple. Mer information finns i [installera regelbundna uppdateringar via Windows PowerShell för StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Uppdateringar av underhållsläge
Uppdateringar av underhållsläge är störande uppdateringar, till exempel disk firmware uppgraderingar. Dessa uppdateringar kräver att enheten ska försättas i underhållsläge. Mer information finns i [steg 2: Ange underhållsläge](#step2). Du kan inte använda den klassiska Azure-portalen för att installera uppdateringar av underhållsläge. I stället måste du använda Windows PowerShell för StorSimple. 

Mer information om hur du installerar uppdateringar av underhållsläge finns [installera Underhåll uppdateringar via Windows PowerShell för StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [!IMPORTANT]
> Uppdateringar av underhållsläge måste tillämpas separat på varje domänkontrollant. 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Installera regelbundna uppdateringar via den klassiska Azure-portalen
Du kan använda den klassiska Azure-portalen för att tillämpa uppdateringar för din StorSimple-enhet.

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Installera regelbundna uppdateringar via Windows PowerShell för StorSimple
Du kan också använda Windows PowerShell för StorSimple för att tillämpa uppdateringar för vanliga (normalt läge).

> [!IMPORTANT]
> Du kan installera regelbundna uppdateringar med hjälp av Windows PowerShell för StorSimple, rekommenderar vi starkt att du installerar regelbundna uppdateringar via den klassiska Azure-portalen. Från och med uppdatering 1 kan utförs förhandskontroller innan du installerar uppdateringar från portalen. Dessa kontroller kommer förutse fel och se till att en jämnare upplevelse. 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Installera uppdateringar av underhållsläge via Windows PowerShell för StorSimple
Du kan använda Windows PowerShell för StorSimple för att installera uppdateringar av Underhållsläge för StorSimple-enheten. Alla i/o-förfrågningar har pausats i det här läget. Tjänster, till exempel beständigt minne (NVRAM) eller klustertjänsten stoppas. Båda styrenheterna startas om när du anger eller avsluta det här läget. När du avslutar det här läget kommer att återupptas alla tjänster och bör vara felfritt. (Detta kan ta några minuter.)

Om du behöver installera uppdateringar av underhållsläge, får du en avisering via den klassiska Azure-portalen att du har uppdateringarna som måste installeras. Den här aviseringen innehåller anvisningar för att använda Windows PowerShell för StorSimple för att installera uppdateringarna. När du har uppdaterat din enhet kan du använda samma procedur för att ändra enheten till standardläge. Stegvisa instruktioner finns i [steg 4: avsluta underhållsläget](#step4).

> [!IMPORTANT]
> * Innan du anger underhållsläge, kontrollera att båda styrenheterna är felfria genom att markera den **maskinvarustatus** på den **Underhåll** sida i den klassiska Azure-portalen. Kontakta Microsoft Support för nästa steg om kontrollanten inte är felfri. Mer information går du till Kontakta Microsoft Support. 
> * När du är i underhållsläge, som du ska använda uppdateringen först på en domänkontrollant och sedan på den andra styrenheten.
> 
> 

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>Steg 1: Anslut till seriekonsol <a name="step1">
Använd först ett program, till exempel PuTTY för att komma åt seriekonsolen. Följande procedur beskriver hur du använder PuTTY för att ansluta till seriekonsol.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>Steg 2: Ange underhållsläge <a name="step2">
När du ansluter till konsolen kan du kontrollera om det finns uppdateringar för att installera och ange Underhållsläge för att installera dem.

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>Steg 3: Installera dina uppdateringar <a name="step3">
Installera uppdateringarna.

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>Steg 4: Avsluta underhållsläget <a name="step4">
Slutligen avsluta underhållsläget.

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Installera snabbkorrigeringar via Windows PowerShell för StorSimple
Till skillnad från uppdateringar för Microsoft Azure StorSimple installeras snabbkorrigeringarna från en delad mapp. Precis som med uppdateringar, finns det två typer av snabbkorrigeringar: 

* Vanliga snabbkorrigeringar 
* Snabbkorrigeringar i underhållsläge  

Följande procedurer beskriver hur du använder Windows PowerShell för StorSimple för att installera ordinarie och snabbkorrigeringar i underhållsläge.

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>Vad händer med uppdateringar om du utför en fabriksåterställning av enheten?
Om en enhet återställs till fabriksinställningarna, förloras alla uppdateringar. När fabriksåterställning enheten är registrerad och konfigurerad, måste du manuellt installera uppdateringar via den klassiska Azure-portalen och/eller Windows PowerShell för StorSimple. Läs mer om fabriksåterställning [återställa enheten till fabriksinställningarna](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Nästa steg
* Läs mer om [använder Windows PowerShell för StorSimple för att administrera din StorSimple-enhet](storsimple-windows-powershell-administration.md).
* Läs mer om [med StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

