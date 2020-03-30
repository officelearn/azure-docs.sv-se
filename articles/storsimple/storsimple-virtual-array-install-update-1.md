---
title: Installera uppdatering 1.0 på StorSimple Virtual Array | Microsoft-dokument
description: Beskriver hur du använder webbgränssnittet StorSimple Virtual Array för att installera uppdateringar med azure-portalen och snabbkorrigeringsmetoden
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254513"
---
# <a name="install-update-10-on-your-storsimple-virtual-array"></a>Installera uppdatering 1.0 på den virtuella storsimple-matrisen

## <a name="overview"></a>Översikt

I den här artikeln beskrivs de steg som krävs för att installera uppdatering 1.0 på din virtuella storsimple-matris via det lokala webbgränssnittet och via Azure-portalen.

Du installerar programuppdateringar eller snabbkorrigeringar för att hålla din StorSimple Virtual Array uppdaterad. Innan du installerar en uppdatering rekommenderar vi att du tar volymerna eller resurserna offline på värden först och sedan enheten. Detta minskar risken för skadade data. När volymerna eller resurserna är offline bör du också göra en manuell säkerhetskopiering av enheten.

> [!IMPORTANT]
> - Uppdatering 1.0 motsvarar **10.0.10296.0-programvaruversion** på enheten. Information om vad som är nytt i den här uppdateringen finns i [Viktig information för uppdatering 1.0](storsimple-virtual-array-update-1-release-notes.md).
>
> - Tänk på att om du installerar en uppdatering eller snabbkorrigering startas om enheten. Med tanke på att StorSimple Virtual Array är en enda nodenhet störs alla I/O som pågår och enheten drabbas av driftstopp.
>
> - Uppdatering 1 är endast tillgänglig i Azure-portalen om den virtuella matrisen kör Uppdatering 0.6. För virtuella matriser som kör versioner före uppdatering 0.6 måste du installera uppdatering 0.6 först och sedan installera uppdatering 1.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Om du kör Uppdatering 0.2 och senare rekommenderar vi att du installerar uppdateringar via Azure-portalen. Portalproceduren kräver att användaren söker igenom, hämtar och installerar uppdateringarna. Beroende på vilken programvaruversion din virtuella matris körs är det annorlunda att installera uppdatering via Azure-portalen.

 - Om den virtuella matrisen kör Uppdatering 0.6 installerar Azure-portalen direkt uppdatering 1 (10.0.10296.0) på din enhet. Den här proceduren tar cirka 7 minuter att slutföra.
 - Om den virtuella matrisen kör en version före uppdatering 0.6 görs uppdateringen i två steg. Azure-portalen installerar först Uppdatering 0.6 (10.0.10293.0) på din enhet. Den virtuella matrisen startas om och portalen installerar sedan uppdatering 1 (10.0.10296.0) på enheten. Den här proceduren tar cirka 15 minuter att slutföra.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-1.md)]

När installationen är klar går du till tjänsten StorSimple Device Manager. Välj **Enheter** och välj och klicka sedan på den enhet som du just har uppdaterat. Gå till **Inställningar > Hantera > enhetsuppdateringar**. Den visade programvaruversionen ska vara **10.0.10296.0**.

![Programvaruversion efter uppdatering](./media/storsimple-virtual-array-install-update-1/azupdate17m1.png)

## <a name="use-the-local-web-ui"></a>Använda det lokala webbgränssnittet

Det finns två steg när du använder det lokala webbgränssnittet:

* Hämta uppdateringen eller snabbkorrigeringen
* Installera uppdateringen eller snabbkorrigeringen

> [!IMPORTANT] 
> **Fortsätt bara med den här uppdateringen om du kör Uppdatering 0.6 (10.0.10293.0). Om du kör en tidigare version [installerar du uppdatera 0.6](storsimple-virtual-array-install-update-06.md) på enheten först och installerar sedan uppdatering 1.**

### <a name="download-the-update-or-the-hotfix"></a>Hämta uppdateringen eller snabbkorrigeringen

Om den virtuella matrisen kör Uppdatering 0.6 gör du följande för att hämta uppdatering 1 från Microsoft Update Catalog.

#### <a name="to-download-the-update-or-the-hotfix"></a>Så här hämtar du uppdateringen eller snabbkorrigeringen

1. Starta Internet Explorer [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)och navigera till .

2. Om du använder Microsoft Update Catalog för första gången på den här datorn klickar du på **Installera** när du uppmanas att installera tillägget Microsoft Update Catalog.

3. I sökrutan i Microsoft Update Catalog anger du knowledge base-numret (KB) för den snabbkorrigering som du vill hämta. Ange **4047203** för uppdatering 1.0 och klicka sedan på **Sök**.
   
    Snabbkorrigeringslistan visas till exempel **StorSimple Virtual Array Update 1.0**.
   
    ![Sökkatalog](./media/storsimple-virtual-array-install-update-1/download1.png)

4. Klicka på **Ladda ner**.

5. Ladda ned de två filerna till en mapp. Du kan också kopiera mappen till en nätverksresurs som kan nås från enheten.

6. Öppna mappen där filerna finns.

    ![Filer i paketet](./media/storsimple-virtual-array-install-update-1/update01folder.png)

    Du ser två filer:
    -  En fristående paketfil `WindowsTH-KB3011067-x64`för Microsoft Update . Den här filen används för att uppdatera enhetsprogrammet.
    - En fil som innehåller kumulativa uppdateringar för augusti `windows8.1-kb4034681-x64`. Mer information om vad som ingår i den här sammanslagningen finns i [augusti månadsvis säkerhetssamarbete](https://support.microsoft.com/help/4034681/windows-8-1-windows-server-2012-r2-update-kb40346810).

### <a name="install-the-update-or-the-hotfix"></a>Installera uppdateringen eller snabbkorrigeringen

Innan installationen av uppdateringen eller snabbkorrigeringen ska du se till att:

 - Du har hämtat uppdateringen eller snabbkorrigeringen antingen lokalt på värden eller tillgänglig via en nätverksresurs.
 - Den virtuella matrisen kör Uppdatering 0.6 (10.0.10293.0). Om du kör en version före uppdatering 0.6 [installerar du uppdatering 0.6](storsimple-virtual-array-install-update-06.md) först och installerar sedan uppdatering 1.

Den här proceduren tar cirka 4 minuter att slutföra. Gör följande för att installera uppdateringen eller snabbkorrigeringen.

#### <a name="to-install-the-update-or-the-hotfix"></a>Så här installerar du uppdateringen eller snabbkorrigeringen

1. Gå till > **Underhållsprogramuppdatering**i **Maintenance**det lokala webbgränssnittet . Anteckna den programvaruversion som du kör. **Fortsätt bara med den här uppdateringen om du kör Uppdatering 0.6 (10.0.10293.0). Om du kör en tidigare version [installerar du uppdatera 0.6](storsimple-virtual-array-install-update-06.md) på enheten först och installerar sedan uppdatering 1.**
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-1/update1m.png)

2. Ange filnamnet för uppdateringen eller snabbkorrigeringen i **sökvägen**uppdatera. Du kan också bläddra till installationsfilen för uppdatering eller snabbkorrigering om den placeras på en nätverksresurs. Klicka på **Använd**.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-1/update2m.png)

3. En varning visas. Med tanke på den virtuella matrisen är en enda nod enhet, efter uppdateringen tillämpas, enheten startar om och det finns driftstopp. Klicka på checkikonen.
   
   ![uppdatera enhet](./media/storsimple-virtual-array-install-update-1/update3m.png)

4. Uppdateringen startar. När enheten har uppdaterats startas den om. Det lokala användargränssnittet är inte tillgängligt under den här varaktigheten.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-1/update5m.png)

5. När omstarten är klar tas du till **inloggningssidan.** Om du vill kontrollera att enhetsprogrammet har uppdaterats går du till > **Underhållsprogramuppdatering**i det lokala webbgränssnittet . **Maintenance** Den visade programvaruversionen ska vara **10.0.0.0.0.10296** för uppdatering 1.0.
   
   > [!NOTE]
   > Vi rapporterar programvaruversionerna på ett något annorlunda sätt i det lokala webbgränssnittet och Azure-portalen. Det lokala webbgränssnittet rapporterar till exempel **10.0.0.0.0.10296** och Azure-portalen **rapporterar 10.0.10296.0** för samma version.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-1/update6m.png)

6. Upprepa steg 2-4 för att installera `windows8.1-kb4012213-x64`säkerhetskorrigeringen för Windows med hjälp av filen . Den virtuella matrisen startas om efter installationen och du måste logga in på det lokala webbgränssnittet.

> [!NOTE]
> Om du har tillämpat uppdatering 1 direkt på en enhet som kör en version före uppdatering 0.6 saknar du vissa uppdateringar. Kontakta Microsoft Support för nästa steg.

## <a name="next-steps"></a>Nästa steg

Läs mer om [hur du administrerar den virtuella storsimple-matrisen](storsimple-ova-web-ui-admin.md).
