---
title: Installera uppdatering 1.1 på StorSimple Virtual Array | Microsoft Docs
description: Beskriver hur du tillämpar uppdateringar med hjälp av Azure-portalen och lokala webbgränssnittet för StorSimple Virtual Array
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/18/2018
ms.author: alkohli
ms.openlocfilehash: 88b903d68e4398b4e30b0b7435279c29bee6cd6b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58005225"
---
# <a name="install-update-11-on-your-storsimple-virtual-array"></a>Installera uppdatering 1.1 på StorSimple Virtual Array

## <a name="overview"></a>Översikt

Den här artikeln beskriver de steg som krävs för att installera uppdateringen 1.1 på StorSimple Virtual Array via det lokala webbgränssnittet eller via Azure portal.

Installation av uppdateringar eller snabbkorrigeringar att hålla din StorSimple Virtual Array uppdaterade. Innan du installerar en uppdatering, rekommenderar vi att du vidtar volymer eller resurser offline på värden första och sedan enheten. Detta minskar risken för skadade data. När volymer eller resurser är offline kan bör du också utföra en manuell säkerhetskopiering av enheten.

> [!IMPORTANT]
> - Uppdatera 1.1 motsvarar **10.0.10307.0** programvaruversionen på din enhet. För information om vad som är nytt i den här uppdateringen, går du till [viktig information för uppdatera 1.1](storsimple-virtual-array-update-11-release-notes.md).
>
> - Tänk på att installera en uppdatering eller snabbkorrigering startar om enheten. Med hänsyn till att StorSimple Virtual Array är en enskild nod-enhet, alla i/o pågår avbryts och din enhet upplever driftstopp.
>
> - Uppdatering 1.1 är tillgänglig i Azure-portalen bara om den virtuella matrisen Kör uppdatering 1. För virtuella matriser som kör uppdatering 0.6 versioner, måste du först installera version 1.0 och tillämpa uppdateringen 1.1.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Om du kör Update 0,2 och senare, rekommenderar vi att du installerar uppdateringar via Azure portal. Portalen proceduren kräver att användaren att skanna, ladda ned och installera uppdateringarna. Beroende på den version av din virtuella matris körs kan är tillämpa en uppdatering via Azure portal olika.

 - Om din virtuella matris Kör uppdatering 1, installerar uppdateringen 1.1 (10.0.10307.0) Azure-portalen direkt på din enhet. Den här proceduren tar cirka 10 minuter för att slutföra.
 - Om din virtuella matris Kör uppdatering 0.6, har uppdaterats i två steg. Azure-portalen kan du först installerar version 1.0 (10.0.10296.0) på din enhet. Startar om den virtuella matrisen och portalen installerar uppdateringen 1.1 (10.0.10307.0) på din enhet. Den här proceduren tar ungefär 15 minuter för att slutföra.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-11.md)]

När installationen är klar, går du till din StorSimple Device Manager-tjänsten. Välj **enheter** och markera och klicka på den enhet som du uppdaterade nyss. Gå till **Inställningar > Hantera > Enhetsuppdateringar**. Den visa programvaruversionen ska vara **10.0.10307.0**.

![Programvaruversion efter uppdateringen](./media/storsimple-virtual-array-install-update-11/azupdate17m2.png)

## <a name="use-the-local-web-ui"></a>Använd det lokala webbgränssnittet

Det finns två steg när du använder det lokala webbgränssnittet:

* Ladda ned uppdateringen eller snabbkorrigeringen
* Installera uppdateringen eller snabbkorrigeringen

> [!IMPORTANT] 
> **Fortsätt med den här uppdateringen bara om du kör uppdatering 1 (10.0.10296.0). Om du kör uppdatering 0.6, [installera uppdatering 1](storsimple-virtual-array-install-update-1.md) på din enhet första och tillämpa uppdateringen 1.1.**

### <a name="download-the-update-or-the-hotfix"></a>Ladda ned uppdateringen eller snabbkorrigeringen

Utför följande steg för att ladda ned uppdateringen 1.1 från Microsoft Update-katalogen.

#### <a name="to-download-the-update-or-the-hotfix"></a>Ladda ned uppdateringen eller snabbkorrigeringen

1. Starta Internet Explorer och navigera till [ https://catalog.update.microsoft.com ](https://catalog.update.microsoft.com).

2. Om du använder Microsoft Update-katalogen för första gången på den här datorn, klickar du på **installera** när du uppmanas att installera tillägget för Microsoft Update-katalogen.

3. I sökrutan i Microsoft Update-katalogen, anger du numret för Knowledge Base (KB) för den snabbkorrigering som du vill hämta. Ange **4337628** uppdatering 1.1, och klicka sedan på **Search**.
   
    I listan över snabbkorrigeringar visas, till exempel **StorSimple virtuell matris Update 1.1**.
   
    ![Sökkatalog](./media/storsimple-virtual-array-install-update-11/download1.png)

4. Klicka på **Hämta**.

5. Hämta de två filerna till en mapp. Du kan också kopiera mappen till en nätverksresurs som kan nås från enheten.

6. Öppna mappen där filerna finns.

    ![Filer i paketet](./media/storsimple-virtual-array-install-update-11/update01folder.png)

    Du ser två filer:
    -  En paketfil för Microsoft Update fristående `WindowsTH-KB3011067-x64`. Den här filen används för att uppdatera enhetens programvara.
    - En fil som innehåller kumulativa uppdateringar för juni `Windows8.1-KB4284815-x64`. Mer information om vad som ingår i den här samlade går du till [juni månatliga insamling av säkerhet](https://support.microsoft.com/help/4284815/windows-81-update-kb4284815).

### <a name="install-the-update-or-the-hotfix"></a>Installera uppdateringen eller snabbkorrigeringen

Innan uppdateringen eller snabbkorrigeringen, se till att:

 - Du har uppdateringen eller snabbkorrigeringen ned lokalt på värden eller också tillgängliga via en nätverksresurs.
 - Din virtuella matris Kör uppdatering 1 (10.0.10296.0). Om du kör uppdatering 0.6, [installera uppdatering 1](storsimple-virtual-array-install-update-1.md) första och installera sedan uppdateringen 1.1.

Den här proceduren tar cirka 4 minuter för att slutföra. Utför följande steg för att installera uppdatering eller snabbkorrigering.

#### <a name="to-install-the-update-or-the-hotfix"></a>Att installera uppdateringen eller snabbkorrigeringen

1. I det lokala webbgränssnittet går du till **Underhåll** > **programuppdateringen**. Anteckna den programvaruversion av som du kör. **Fortsätt med den här uppdateringen bara om du kör uppdatering 1 (10.0.10296.0). Om du kör uppdatering 0.6, [installera uppdatering 1](storsimple-virtual-array-install-update-1.md) på din enhet första och tillämpa uppdateringen 1.1.**
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-11/update1m.png)

2. I **uppdatering filsökväg**, ange filnamnet för uppdateringen eller snabbkorrigeringen. Du kan också bläddra till installationsfilen för uppdateringen eller snabbkorrigeringen om placeras på en nätverksresurs. Klicka på **Verkställ**.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-11/update2m.png)

3. En varning visas. Är en enskild nod-enhet, beroende på den virtuella matrisen när uppdateringen har tillämpats, enheten startas om och stilleståndstid. Klicka på kryssikonen.
   
   ![uppdatera enhet](./media/storsimple-virtual-array-install-update-11/update3m.png)

4. Uppdateringen startar. När enheten har uppdaterats, den startar om. Lokala Användargränssnittet är inte tillgänglig under den tiden.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-11/update5m.png)

5. När omstarten är klar kommer du till den **logga in** sidan. Kontrollera att programmet har uppdaterats i det lokala webbgränssnittet, gå till **Underhåll** > **programuppdateringen**. Den visa programvaruversionen ska vara **10.0.0.0.0.10307** för uppdateringen 1.1.
   
   > [!NOTE]
   > Vi rapporterar programvaruversionerna i ett något annorlunda sätt i det lokala webbgränssnittet och Azure-portalen. Exempelvis kan det lokala webbgränssnittet rapporterar **10.0.0.0.0.10307** och Azure portal rapporter **10.0.10307.0** för samma version.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-11/update6m.png)

6. Upprepa steg 2 – 4 om du vill installera en Windows-säkerhetskorrigering som använder filen `Windows8.1-KB4284815-x64`. Den virtuella matrisen startar om efter installationen och du måste logga in på det lokala webbgränssnittet.


## <a name="next-steps"></a>Nästa steg

Läs mer om [administrera StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).
