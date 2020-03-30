---
title: Uppdatera din StorSimple-enhet | Microsoft-dokument
description: I artikeln beskrivs hur du anvÃ¤nder uppdateringsfunktionen i StorSimple fÃ¶r att installera regelbundna uppdateringar och snabbkorrigeringar fÃ¶r underhållsläge.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: 786059f5-2a38-4105-941d-0860ce4ac515
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: twooley
ms.openlocfilehash: c9451afaefdd220b5f87d4650c7844f06926b03a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933421"
---
# <a name="update-your-storsimple-8000-series-device"></a>Uppdatera din StorSimple 8000-serieenhet
> [!NOTE]
> Den klassiska portalen för StorSimple är inaktuell. Dina StorSimple-enhetshanterare flyttas automatiskt till nya Azure Portal enligt utfasningsschemat. Du kommer att få ett e-postmeddelande och ett portalmeddelande om flytten. Det här dokumentet kommer också att dras tillbaka snart. Om du har frågor om flytten kan du läsa [Vanliga frågor och svar: Flytta till Azure Portal](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Översikt
Med StorSimple-uppdateringarna kan du enkelt hålla din StorSimple-enhet uppdaterad. Beroende på uppdateringstypen kan du installera uppdateringar på enheten via den klassiska Azure-portalen eller via Windows PowerShell-gränssnittet. I den här självstudien beskrivs uppdateringstyperna och hur du installerar var och en av dem.

Du kan använda två typer av enhetsuppdateringar: 

* Regelbundna uppdateringar (eller normalt läge)
* Uppdateringar av underhållsläge

Du kan installera regelbundna uppdateringar via den klassiska Azure-portalen eller Windows PowerShell. Du måste dock använda Windows PowerShell för att installera uppdateringar av underhållsläge. 

Varje uppdateringstyp beskrivs separat nedan.

### <a name="regular-updates"></a>Regelbundna uppdateringar
Regelbundna uppdateringar är icke-störande uppdateringar som kan installeras när enheten är i normalläge. Dessa uppdateringar tillämpas via webbplatsen Microsoft Update på varje enhetsstyrenhet. 

> [!IMPORTANT]
> En redundans för styrenhet kan inträffa under uppdateringsprocessen. Detta påverkar dock inte systemets tillgänglighet eller åtgärd.
> 
> 

* Mer information om hur du installerar vanliga uppdateringar via den klassiska Azure-portalen finns i [Installera vanliga uppdateringar via den klassiska Azure-portalen](#install-regular-updates-via-the-azure-classic-portal).
* Du kan också installera vanliga uppdateringar via Windows PowerShell för StorSimple. Mer information finns i [Installera vanliga uppdateringar via Windows PowerShell för StorSimple](#install-regular-updates-via-windows-powershell-for-storsimple).

### <a name="maintenance-mode-updates"></a>Uppdateringar av underhållsläge
Uppdateringar av underhållsläge är störande uppdateringar, till exempel uppgraderingar av den fasta programvaran för disk. Dessa uppdateringar kräver att enheten sätts i underhållsläge. Mer information finns i [steg 2: Ange underhållsläge](#step2). Du kan inte använda den klassiska Azure-portalen för att installera uppdateringar av underhållsläge. I stället måste du använda Windows PowerShell för StorSimple. 

Mer information om hur du installerar uppdateringar för underhållsläge finns i [Installera uppdateringar av underhållsläge via Windows PowerShell för StorSimple](#install-maintenance-mode-updates-via-windows-powershell-for-storsimple).

> [!IMPORTANT]
> Uppdateringar av underhållsläge måste tillämpas separat på varje styrenhet. 
> 
> 

## <a name="install-regular-updates-via-the-azure-classic-portal"></a>Installera regelbundna uppdateringar via den klassiska Azure-portalen
Du kan använda den klassiska Azure-portalen för att installera uppdateringar på din StorSimple-enhet.

[!INCLUDE [storsimple-install-updates-manually](../../includes/storsimple-install-updates-manually.md)]

## <a name="install-regular-updates-via-windows-powershell-for-storsimple"></a>Installera vanliga uppdateringar via Windows PowerShell för StorSimple
Du kan också använda Windows PowerShell för StorSimple för att tillämpa vanliga uppdateringar (normalläge).

> [!IMPORTANT]
> Även om du kan installera vanliga uppdateringar med Windows PowerShell för StorSimple rekommenderar vi starkt att du installerar vanliga uppdateringar via den klassiska Azure-portalen. Från och med uppdatering 1 utförs förkontroller innan uppdateringar installeras från portalen. Dessa förhandskontroller kommer att föregripa misslyckanden och säkerställa en smidigare upplevelse. 
> 
> 

[!INCLUDE [storsimple-install-regular-updates-powershell](../../includes/storsimple-install-regular-updates-powershell.md)]

## <a name="install-maintenance-mode-updates-via-windows-powershell-for-storsimple"></a>Installera uppdateringar av underhållsläge via Windows PowerShell för StorSimple
Du använder Windows PowerShell för StorSimple för att installera uppdateringar av underhållsläge på Din StorSimple-enhet. Alla I/O-begäranden pausas i det här läget. Tjänster som icke-flyktigt slumpmässigt åtkomstminne (NVRAM) eller klustertjänsten stoppas också. Båda handkontrollerna startas om när du går in i eller avslutar det här läget. När du avslutar det här läget återupptas alla tjänster och bör vara felfria. (Detta kan ta några minuter.)

Om du behöver installera uppdateringar för underhållsläge får du en avisering via den klassiska Azure-portalen att du har uppdateringar som måste installeras. Den här varningen innehåller instruktioner för hur du använder Windows PowerShell för StorSimple för att installera uppdateringarna. När du har uppdaterat enheten använder du samma procedur för att ändra enheten till vanligt läge. Steg-för-steg-instruktioner finns i [steg 4: Avsluta underhållsläge](#step4).

> [!IMPORTANT]
> * Innan du går in i underhållsläge kontrollerar du att båda enhetskontrollanterna är felfria genom att kontrollera **maskinvarustatusen** på **underhållssidan** i den klassiska Azure-portalen. Om styrenheten inte är felfri kontaktar du Microsoft Support för nästa steg. Mer information finns i Kontakta Microsoft Support. 
> * När du är i underhållsläge måste du först installera uppdateringen på en styrenhet och sedan på den andra styrenheten.
> 
> 

### <a name="step-1-connect-to-the-serial-console-a-namestep1"></a>Steg 1: Anslut till seriekonsolen<a name="step1">
Använd först ett program som PuTTY för att komma åt seriekonsolen. Följande procedur förklarar hur du använder PuTTY för att ansluta till seriekonsolen.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="step-2-enter-maintenance-mode-a-namestep2"></a>Steg 2: Ange underhållsläge<a name="step2">
När du har anslutit till konsolen bestämmer du om det finns uppdateringar att installera och går in i underhållsläge för att installera dem.

[!INCLUDE [storsimple-enter-maintenance-mode](../../includes/storsimple-enter-maintenance-mode.md)]

### <a name="step-3-install-your-updates-a-namestep3"></a>Steg 3: Installera uppdateringarna<a name="step3">
Installera sedan uppdateringarna.

[!INCLUDE [storsimple-install-maintenance-mode-updates](../../includes/storsimple-install-maintenance-mode-updates.md)]

### <a name="step-4-exit-maintenance-mode-a-namestep4"></a>Steg 4: Avsluta underhållsläge<a name="step4">
Avsluta slutligen underhållsläge.

[!INCLUDE [storsimple-exit-maintenance-mode](../../includes/storsimple-exit-maintenance-mode.md)]

## <a name="install-hotfixes-via-windows-powershell-for-storsimple"></a>Installera snabbkorrigeringar via Windows PowerShell för StorSimple
Till skillnad från uppdateringar för Microsoft Azure StorSimple installeras snabbkorrigeringar från en delad mapp. Precis som med uppdateringar finns det två typer av snabbkorrigeringar: 

* Vanliga snabbkorrigeringar 
* Snabbkorrigeringar för underhållsläge  

Följande procedurer förklarar hur du använder Windows PowerShell för StorSimple för att installera vanliga snabbkorrigeringar och underhållsläge.

[!INCLUDE [storsimple-install-regular-hotfixes](../../includes/storsimple-install-regular-hotfixes.md)]

[!INCLUDE [storsimple-install-maintenance-mode-hotfixes](../../includes/storsimple-install-maintenance-mode-hotfixes.md)]

## <a name="what-happens-to-updates-if-you-perform-a-factory-reset-of-the-device"></a>Vad händer med uppdateringar om du utför en fabriksåterställning av enheten?
Om en enhet återställs till fabriksinställningarna går alla uppdateringar förlorade. När fabriken-reset-enheten har registrerats och konfigurerats måste du manuellt installera uppdateringar via den klassiska Azure-portalen och/eller Windows PowerShell för StorSimple. Mer information om fabriksåterställning finns i [Återställa enheten till fabriksinställningarna](storsimple-8000-manage-device-controller.md#reset-the-device-to-factory-default-settings).

## <a name="next-steps"></a>Nästa steg
* Läs mer om [hur du använder Windows PowerShell för StorSimple för att administrera din StorSimple-enhet](storsimple-windows-powershell-administration.md).
* Läs mer om [hur du använder StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

