---
title: Installera version 1.0 på StorSimple Virtual Array | Microsoft Docs
description: Beskriver hur du använder StorSimple Virtual Array webbgränssnittet för att tillämpa uppdateringar med hjälp av metoden för Azure-portalen och snabbkorrigeringen
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: fa53213e577028628d48db91704578e23888f2a8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58013867"
---
# <a name="install-update-10-on-your-storsimple-virtual-array"></a>Installera version 1.0 på StorSimple Virtual Array

## <a name="overview"></a>Översikt

Den här artikeln beskriver de steg som krävs för att installera version 1.0 på StorSimple Virtual Array via det lokala webbgränssnittet eller via Azure portal.

Installation av uppdateringar eller snabbkorrigeringar att hålla din StorSimple Virtual Array uppdaterade. Innan du installerar en uppdatering, rekommenderar vi att du vidtar volymer eller resurser offline på värden första och sedan enheten. Detta minskar risken för skadade data. När volymer eller resurser är offline kan bör du också utföra en manuell säkerhetskopiering av enheten.

> [!IMPORTANT]
> - Version 1.0 motsvarar **10.0.10296.0** programvaruversionen på din enhet. För information om vad som är nytt i den här uppdateringen, går du till [viktig information för uppdatering 1.0](storsimple-virtual-array-update-1-release-notes.md).
>
> - Tänk på att installera en uppdatering eller snabbkorrigering startar om enheten. Med hänsyn till att StorSimple Virtual Array är en enskild nod-enhet, alla i/o pågår avbryts och din enhet upplever driftstopp.
>
> - Uppdatering 1 är tillgänglig i Azure-portalen bara om den virtuella matrisen Kör uppdatering 0.6. För virtuella matriser som kör Föruppdatering 0,6 versioner, måste du installera uppdatering 0.6 först och sedan installera uppdatering 1.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Om du kör Update 0,2 och senare, rekommenderar vi att du installerar uppdateringar via Azure portal. Portalen proceduren kräver att användaren att skanna, ladda ned och installera uppdateringarna. Beroende på den version av din virtuella matris körs kan är tillämpa en uppdatering via Azure portal olika.

 - Om din virtuella matris Kör uppdatering 0.6, installerar uppdatering 1 (10.0.10296.0) direkt på din enhet i Azure-portalen. Den här proceduren tar cirka 7 minuter för att slutföra.
 - Om din virtuella matris kör en version före uppdatering 0.6, har uppdaterats i två steg. Azure-portalen kan du först installerar uppdatering 0.6 (10.0.10293.0) på din enhet. Startar om den virtuella matrisen och portalen installerar uppdatering 1 (10.0.10296.0) på din enhet. Den här proceduren tar ungefär 15 minuter för att slutföra.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-1.md)]

När installationen är klar, går du till din StorSimple Device Manager-tjänsten. Välj **enheter** och markera och klicka på den enhet som du uppdaterade nyss. Gå till **Inställningar > Hantera > Enhetsuppdateringar**. Den visa programvaruversionen ska vara **10.0.10296.0**.

![Programvaruversion efter uppdateringen](./media/storsimple-virtual-array-install-update-1/azupdate17m1.png)

## <a name="use-the-local-web-ui"></a>Använd det lokala webbgränssnittet

Det finns två steg när du använder det lokala webbgränssnittet:

* Ladda ned uppdateringen eller snabbkorrigeringen
* Installera uppdateringen eller snabbkorrigeringen

> [!IMPORTANT] 
> **Fortsätt med den här uppdateringen bara om du kör uppdatering 0.6 (10.0.10293.0). Om du kör en tidigare version [installera uppdatering 0.6](storsimple-virtual-array-install-update-06.md) på din enhet första och tillämpa sedan uppdatering 1.**

### <a name="download-the-update-or-the-hotfix"></a>Ladda ned uppdateringen eller snabbkorrigeringen

Om din virtuella matris Kör uppdatering 0.6, utför du följande steg för att ladda ned uppdatering 1 från Microsoft Update-katalogen.

#### <a name="to-download-the-update-or-the-hotfix"></a>Ladda ned uppdateringen eller snabbkorrigeringen

1. Starta Internet Explorer och navigera till [ https://catalog.update.microsoft.com ](https://catalog.update.microsoft.com).

2. Om du använder Microsoft Update-katalogen för första gången på den här datorn, klickar du på **installera** när du uppmanas att installera tillägget för Microsoft Update-katalogen.

3. I sökrutan i Microsoft Update-katalogen, anger du numret för Knowledge Base (KB) för den snabbkorrigering som du vill hämta. Ange **4047203** uppdatering 1.0, och klicka sedan på **Search**.
   
    I listan över snabbkorrigeringar visas, till exempel **StorSimple virtuell matris uppdatering 1.0**.
   
    ![Sökkatalog](./media/storsimple-virtual-array-install-update-1/download1.png)

4. Klicka på **Hämta**.

5. Hämta de två filerna till en mapp. Du kan också kopiera mappen till en nätverksresurs som kan nås från enheten.

6. Öppna mappen där filerna finns.

    ![Filer i paketet](./media/storsimple-virtual-array-install-update-1/update01folder.png)

    Du ser två filer:
    -  En paketfil för Microsoft Update fristående `WindowsTH-KB3011067-x64`. Den här filen används för att uppdatera enhetens programvara.
    - En fil som innehåller kumulativa uppdateringar för augusti `windows8.1-kb4034681-x64`. Mer information om vad som ingår i den här samlade går du till [augusti månatliga insamling av säkerhet](https://support.microsoft.com/help/4034681/windows-8-1-windows-server-2012-r2-update-kb40346810).

### <a name="install-the-update-or-the-hotfix"></a>Installera uppdateringen eller snabbkorrigeringen

Innan uppdateringen eller snabbkorrigeringen, se till att:

 - Du har uppdateringen eller snabbkorrigeringen ned lokalt på värden eller också tillgängliga via en nätverksresurs.
 - Din virtuella matris Kör uppdatering 0.6 (10.0.10293.0). Om du kör en version före uppdatering 0.6, [installera uppdatering 0.6](storsimple-virtual-array-install-update-06.md) första och sedan installera uppdatering 1.

Den här proceduren tar cirka 4 minuter för att slutföra. Utför följande steg för att installera uppdatering eller snabbkorrigering.

#### <a name="to-install-the-update-or-the-hotfix"></a>Att installera uppdateringen eller snabbkorrigeringen

1. I det lokala webbgränssnittet går du till **Underhåll** > **programuppdateringen**. Anteckna den programvaruversion av som du kör. **Fortsätt med den här uppdateringen bara om du kör uppdatering 0.6 (10.0.10293.0). Om du kör en tidigare version [installera uppdatering 0.6](storsimple-virtual-array-install-update-06.md) på din enhet första och tillämpa sedan uppdatering 1.**
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-1/update1m.png)

2. I **uppdatering filsökväg**, ange filnamnet för uppdateringen eller snabbkorrigeringen. Du kan också bläddra till installationsfilen för uppdateringen eller snabbkorrigeringen om placeras på en nätverksresurs. Klicka på **Verkställ**.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-1/update2m.png)

3. En varning visas. Är en enskild nod-enhet, beroende på den virtuella matrisen när uppdateringen har tillämpats, enheten startas om och stilleståndstid. Klicka på kryssikonen.
   
   ![uppdatera enhet](./media/storsimple-virtual-array-install-update-1/update3m.png)

4. Uppdateringen startar. När enheten har uppdaterats, den startar om. Lokala Användargränssnittet är inte tillgänglig under den tiden.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-1/update5m.png)

5. När omstarten är klar kommer du till den **logga in** sidan. Kontrollera att programmet har uppdaterats i det lokala webbgränssnittet, gå till **Underhåll** > **programuppdateringen**. Den visa programvaruversionen ska vara **10.0.0.0.0.10296** för version 1.0.
   
   > [!NOTE]
   > Vi rapporterar programvaruversionerna i ett något annorlunda sätt i det lokala webbgränssnittet och Azure-portalen. Exempelvis kan det lokala webbgränssnittet rapporterar **10.0.0.0.0.10296** och Azure portal rapporter **10.0.10296.0** för samma version.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-1/update6m.png)

6. Upprepa steg 2 – 4 om du vill installera en Windows-säkerhetskorrigering som använder filen `windows8.1-kb4012213-x64`. Den virtuella matrisen startar om efter installationen och du måste logga in på det lokala webbgränssnittet.

> [!NOTE]
> Om du använt direkt uppdatering 1 för en enhet som kör en version före uppdatering 0.6, saknar du vissa uppdateringar. Kontakta Microsoft Support angående nästa steg.

## <a name="next-steps"></a>Nästa steg

Läs mer om [administrera StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).
