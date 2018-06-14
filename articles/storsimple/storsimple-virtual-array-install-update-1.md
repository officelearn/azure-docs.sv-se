---
title: Installera uppdatering 1.0 på virtuella StorSimple-matrisen | Microsoft Docs
description: Beskriver hur du använder virtuella StorSimple-matris webbgränssnittet för att tillämpa uppdateringar med hjälp av Azure portal och snabbkorrigering metoden
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
ms.openlocfilehash: a85290f3f56eb1e1bf57524c43c6d4fea36129f7
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
ms.locfileid: "24012080"
---
# <a name="install-update-10-on-your-storsimple-virtual-array"></a>Installera uppdatering 1.0 på din virtuella StorSimple-matris

## <a name="overview"></a>Översikt

Den här artikeln beskriver de steg som krävs för att installera uppdatering 1.0 på din StorSimple virtuell matrisen via lokala webbgränssnittet och via Azure portal.

Du kan tillämpa uppdateringar eller snabbkorrigeringar för att hålla det uppdaterat din virtuella StorSimple-matris. Innan du installerar en uppdatering, rekommenderar vi att du vidtar volymer eller resurser offline på värden först och sedan enheten. Detta minimerar möjlighet att data skadas. När volymer eller resurser som är offline, bör du också se en manuell säkerhetskopiering av enheten.

> [!IMPORTANT]
> - Uppdatering 1.0 motsvarar **10.0.10296.0** programvaruversionen på enheten. För information om vad är nytt i den här uppdateringen, gå till [viktig information för uppdatering 1.0](storsimple-virtual-array-update-1-release-notes.md).
>
> - Tänk på att installera en uppdatering eller snabbkorrigering startar om enheten. Med hänsyn till att den virtuella StorSimple-matrisen är en enskild nod-enhet, avbryts alla i/o pågår och din enhet upplever driftstopp.
>
> - Uppdatering 1 finns i Azure-portalen bara om den virtuella matrisen Kör uppdatering 0,6. För virtuella matriser som körs före uppdateringen 0,6 versioner måste du installera uppdateringen 0,6 först och sedan installera uppdatering 1.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Om du kör uppdatering 0,2 och senare, rekommenderar vi att du installerar uppdateringar via Azure-portalen. Portalen proceduren kräver att användaren skanna, hämta och installera uppdateringarna. Tillämpa en uppdatering via Azure portal skiljer sig beroende på programvaruversionen virtuella matrisen körs.

 - Om din virtuella matris Kör uppdatering 0,6, installerar Azure-portalen direkt uppdatering 1 (10.0.10296.0) på din enhet. Den här proceduren tar cirka 7 minuter för att slutföra.
 - Om virtuella matrisen kör en tidigare version än uppdatering 0,6, görs uppdatering i två steg. Azure-portalen först installerar uppdateringen 0,6 (10.0.10293.0) på din enhet. Virtuella matrisen startas om och portalen installerar uppdatering 1 (10.0.10296.0) på din enhet. Den här proceduren tar ungefär 15 minuter för att slutföra.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-1.md)]

När installationen är klar, gå till Enhetshanteraren för StorSimple-tjänsten. Välj **enheter** och markera och klicka på den enhet som du precis har uppdaterats. Gå till **Inställningar > Hantera > Enhetsuppdateringar**. Programvaruversionen visas ska vara **10.0.10296.0**.

![Programvaruversion efter uppdateringen](./media/storsimple-virtual-array-install-update-1/azupdate17m1.png)

## <a name="use-the-local-web-ui"></a>Använda lokala webbgränssnittet

Det finns två steg när du använder lokala webbgränssnittet:

* Hämta uppdateringen eller snabbkorrigeringen
* Installera uppdateringen eller snabbkorrigeringen

> [!IMPORTANT] 
> **Fortsätt med den här uppdateringen bara om du kör uppdatering 0,6 (10.0.10293.0). Om du kör en tidigare version [uppdatering 0,6](storsimple-virtual-array-install-update-06.md) på enheten första och tillämpa sedan uppdatering 1.**

### <a name="download-the-update-or-the-hotfix"></a>Hämta uppdateringen eller snabbkorrigeringen

Om din virtuella matris Kör uppdatering 0,6, utför följande steg för att ladda ned uppdatering 1 från Microsoft Update-katalogen.

#### <a name="to-download-the-update-or-the-hotfix"></a>Hämta uppdateringen eller snabbkorrigeringen

1. Starta Internet Explorer och gå till [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Om du använder Microsoft Update-katalogen för första gången på den här datorn, klickar du på **installera** när du uppmanas att installera tillägget för Microsoft Update-katalogen.

3. Ange antalet den snabbkorrigering som du vill hämta Knowledge Base (KB) i sökrutan i Microsoft Update-katalogen. Ange **4047203** uppdatering 1.0, och klicka sedan på **Sök**.
   
    Snabbkorrigeringen listan visas till exempel **StorSimple virtuell matris uppdatering 1.0**.
   
    ![Sökkatalog](./media/storsimple-virtual-array-install-update-1/download1.png)

4. Klicka på **Hämta**.

5. Hämta filerna till en mapp. Du kan också kopiera mappen till en nätverksresurs som kan nås från enheten.

6. Öppna mappen där filerna finns.

    ![Filer i paketet](./media/storsimple-virtual-array-install-update-1/update01folder.png)

    Du ser två filer:
    -  En paketfil för Microsoft Update fristående `WindowsTH-KB3011067-x64`. Den här filen används för att uppdatera enheten.
    - En fil som innehåller kumulativa uppdateringar för augusti `windows8.1-kb4034681-x64`. Mer information om vad som ingår i den samlade går du till [augusti månatliga insamling av säkerhet](https://support.microsoft.com/help/4034681/windows-8-1-windows-server-2012-r2-update-kb40346810).

### <a name="install-the-update-or-the-hotfix"></a>Installera uppdateringen eller snabbkorrigeringen

Före installationen uppdatering eller snabbkorrigering kontrollerar du att:

 - Du har uppdateringen eller snabbkorrigeringen hämtas lokalt på värden eller tillgängligt via en nätverksresurs.
 - Virtuella matrisen körs Update 0,6 (10.0.10293.0). Om du kör en tidigare version än uppdatering 0,6, [uppdatering 0,6](storsimple-virtual-array-install-update-06.md) först och sedan installera uppdatering 1.

Den här proceduren tar cirka 4 minuter för att slutföra. Utför följande steg för att installera uppdatering eller snabbkorrigering.

#### <a name="to-install-the-update-or-the-hotfix"></a>Så här installerar du uppdateringen eller snabbkorrigeringen

1. Gå till i det lokala webbgränssnittet **Underhåll** > **programuppdateringen**. Anteckna den programvaruversion av som du kör. **Fortsätt med den här uppdateringen bara om du kör uppdatering 0,6 (10.0.10293.0). Om du kör en tidigare version [uppdatering 0,6](storsimple-virtual-array-install-update-06.md) på enheten första och tillämpa sedan uppdatering 1.**
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-1/update1m.png)

2. I **uppdatering filsökväg**, ange filnamnet för uppdateringen eller snabbkorrigeringen. Du kan även bläddra till installationsfilen uppdatering eller snabbkorrigering om placeras på en nätverksresurs. Klicka på **Använd**.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-1/update2m.png)

3. En varning visas. Det angivna virtuella matrisen är en enskild nod-enhet när uppdateringen har genomförts, enheten startas om och det finns en avbrottstid. Klicka på kryssikonen.
   
   ![uppdatera enhet](./media/storsimple-virtual-array-install-update-1/update3m.png)

4. Uppdateringen startar. När enheten har uppdaterats, startas om. Lokala Användargränssnittet är inte tillgängligt i den här tiden.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-1/update5m.png)

5. När omstarten är klar, kommer du till den **inloggning** sidan. Kontrollera att enhetens programvara har uppdaterats i lokala webbgränssnittet, gå till **Underhåll** > **programuppdateringen**. Programvaruversionen visas ska vara **10.0.0.0.0.10296** för uppdatering 1.0.
   
   > [!NOTE]
   > Vi rapporterar programvaruversioner i ett lite annorlunda sätt i lokala webbgränssnittet och Azure portal. Till exempel lokala webbgränssnittet rapporterar **10.0.0.0.0.10296** och Azure portal rapporter **10.0.10296.0** för samma version.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-1/update6m.png)

6. Upprepa steg 2 – 4 om du vill installera en Windows-säkerhetskorrigering med hjälp av filen `windows8.1-kb4012213-x64`. Virtuella matrisen startas om efter installationen och du behöver logga in på det lokala webbgränssnittet.

> [!NOTE]
> Om du använder direkt uppdatering 1 i en enhet som kör en tidigare version än uppdatering 0,6, saknas vissa uppdateringar. Kontakta Microsoft Support för nästa steg.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [administrera din virtuella StorSimple-matris](storsimple-ova-web-ui-admin.md).
