---
title: Installera uppdatering 1.1 på StorSimple Virtual Array | Microsoft-dokument
description: Beskriver hur du installerar uppdateringar med Azure-portalen och det lokala webbgränssnittet för StorSimple Virtual Array
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254487"
---
# <a name="install-update-11-on-your-storsimple-virtual-array"></a>Installera uppdatering 1.1 på den virtuella storsimple-matrisen

## <a name="overview"></a>Översikt

I den här artikeln beskrivs de steg som krävs för att installera uppdatering 1.1 på din Virtuella StorSimple-matris via det lokala webbgränssnittet och via Azure-portalen.

Du installerar programuppdateringar eller snabbkorrigeringar för att hålla din StorSimple Virtual Array uppdaterad. Innan du installerar en uppdatering rekommenderar vi att du tar volymerna eller resurserna offline på värden först och sedan enheten. Detta minskar risken för skadade data. När volymerna eller resurserna är offline bör du också göra en manuell säkerhetskopiering av enheten.

> [!IMPORTANT]
> - Uppdatering 1.1 motsvarar **programvaruversionen av programvaran 10.0.10307.0** på enheten. Information om vad som är nytt i den här uppdateringen finns i [Viktig information för uppdatering 1.1](storsimple-virtual-array-update-11-release-notes.md).
>
> - Tänk på att om du installerar en uppdatering eller snabbkorrigering startas om enheten. Med tanke på att StorSimple Virtual Array är en enda nodenhet störs alla I/O som pågår och enheten drabbas av driftstopp.
>
> - Uppdatering 1.1 är endast tillgänglig i Azure-portalen om den virtuella matrisen kör uppdatering 1. För virtuella matriser som kör Update 0.6-versioner måste du installera uppdatering 1.0 först och sedan installera uppdatering 1.1.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Om du kör Uppdatering 0.2 och senare rekommenderar vi att du installerar uppdateringar via Azure-portalen. Portalproceduren kräver att användaren söker igenom, hämtar och installerar uppdateringarna. Beroende på vilken programvaruversion din virtuella matris körs är det annorlunda att installera uppdatering via Azure-portalen.

 - Om den virtuella matrisen kör uppdatering 1 installerar Azure-portalen direkt uppdatering 1.1 (10.0.10307.0) på enheten. Den här proceduren tar cirka 10 minuter att slutföra.
 - Om den virtuella matrisen kör Uppdatering 0.6 görs uppdateringen i två steg. Azure-portalen installerar först Uppdatering 1.0 (10.0.10296.0) på din enhet. Den virtuella matrisen startas om och portalen installerar sedan uppdatering 1.1 (10.0.10307.0) på enheten. Den här proceduren tar cirka 15 minuter att slutföra.


[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-11.md)]

När installationen är klar går du till tjänsten StorSimple Device Manager. Välj **Enheter** och välj och klicka sedan på den enhet som du just har uppdaterat. Gå till **Inställningar > Hantera > enhetsuppdateringar**. Den visade programvaruversionen ska vara **10.0.10307.0**.

![Programvaruversion efter uppdatering](./media/storsimple-virtual-array-install-update-11/azupdate17m2.png)

## <a name="use-the-local-web-ui"></a>Använda det lokala webbgränssnittet

Det finns två steg när du använder det lokala webbgränssnittet:

* Hämta uppdateringen eller snabbkorrigeringen
* Installera uppdateringen eller snabbkorrigeringen

> [!IMPORTANT] 
> **Fortsätt bara med den här uppdateringen om du kör Uppdatering 1 (10.0.10296.0). Om du kör Uppdatering 0.6 [installerar du uppdatering 1](storsimple-virtual-array-install-update-1.md) på enheten först och installerar sedan uppdatering 1.1.**

### <a name="download-the-update-or-the-hotfix"></a>Hämta uppdateringen eller snabbkorrigeringen

Gör följande för att hämta Uppdatering 1.1 från Microsoft Update Catalog.

#### <a name="to-download-the-update-or-the-hotfix"></a>Så här hämtar du uppdateringen eller snabbkorrigeringen

1. Starta Internet Explorer [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)och navigera till .

2. Om du använder Microsoft Update Catalog för första gången på den här datorn klickar du på **Installera** när du uppmanas att installera tillägget Microsoft Update Catalog.

3. I sökrutan i Microsoft Update Catalog anger du knowledge base-numret (KB) för den snabbkorrigering som du vill hämta. Ange **4337628** för uppdatering 1.1 och klicka sedan på **Sök**.
   
    Snabbkorrigeringslistan visas till exempel **StorSimple Virtual Array Update 1.1**.
   
    ![Sökkatalog](./media/storsimple-virtual-array-install-update-11/download1.png)

4. Klicka på **Ladda ner**.

5. Ladda ned de två filerna till en mapp. Du kan också kopiera mappen till en nätverksresurs som kan nås från enheten.

6. Öppna mappen där filerna finns.

    ![Filer i paketet](./media/storsimple-virtual-array-install-update-11/update01folder.png)

    Du ser två filer:
    -  En fristående paketfil `WindowsTH-KB3011067-x64`för Microsoft Update . Den här filen används för att uppdatera enhetsprogrammet.
    - En fil som innehåller kumulativa uppdateringar för juni `Windows8.1-KB4284815-x64`. Mer information om vad som ingår i den här sammanslagningen finns i [juni månatliga säkerhetssamarbete](https://support.microsoft.com/help/4284815/windows-81-update-kb4284815).

### <a name="install-the-update-or-the-hotfix"></a>Installera uppdateringen eller snabbkorrigeringen

Innan installationen av uppdateringen eller snabbkorrigeringen ska du se till att:

 - Du har hämtat uppdateringen eller snabbkorrigeringen antingen lokalt på värden eller tillgänglig via en nätverksresurs.
 - Den virtuella matrisen kör uppdatering 1 (10.0.10296.0). Om du kör Uppdatering 0.6 [installerar du uppdatering 1](storsimple-virtual-array-install-update-1.md) först och installerar sedan uppdatering 1.1.

Den här proceduren tar cirka 4 minuter att slutföra. Gör följande för att installera uppdateringen eller snabbkorrigeringen.

#### <a name="to-install-the-update-or-the-hotfix"></a>Så här installerar du uppdateringen eller snabbkorrigeringen

1. Gå till > **Underhållsprogramuppdatering**i **Maintenance**det lokala webbgränssnittet . Anteckna den programvaruversion som du kör. **Fortsätt bara med den här uppdateringen om du kör Uppdatering 1 (10.0.10296.0). Om du kör Uppdatering 0.6 [installerar du uppdatering 1](storsimple-virtual-array-install-update-1.md) på enheten först och installerar sedan uppdatering 1.1.**
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-11/update1m.png)

2. Ange filnamnet för uppdateringen eller snabbkorrigeringen i **sökvägen**uppdatera. Du kan också bläddra till installationsfilen för uppdatering eller snabbkorrigering om den placeras på en nätverksresurs. Klicka på **Använd**.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-11/update2m.png)

3. En varning visas. Med tanke på den virtuella matrisen är en enda nod enhet, efter uppdateringen tillämpas, enheten startar om och det finns driftstopp. Klicka på checkikonen.
   
   ![uppdatera enhet](./media/storsimple-virtual-array-install-update-11/update3m.png)

4. Uppdateringen startar. När enheten har uppdaterats startas den om. Det lokala användargränssnittet är inte tillgängligt under den här varaktigheten.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-11/update5m.png)

5. När omstarten är klar tas du till **inloggningssidan.** Om du vill kontrollera att enhetsprogrammet har uppdaterats går du till > **Underhållsprogramuppdatering**i det lokala webbgränssnittet . **Maintenance** Den visade programvaruversionen ska vara **10.0.0.0.0.10307** för uppdatering 1.1.
   
   > [!NOTE]
   > Vi rapporterar programvaruversionerna på ett något annorlunda sätt i det lokala webbgränssnittet och Azure-portalen. Det lokala webbgränssnittet rapporterar till exempel **10.0.0.0.0.10307** och Azure-portalen **rapporterar 10.0.10307.0** för samma version.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-11/update6m.png)

6. Upprepa steg 2-4 för att installera `Windows8.1-KB4284815-x64`säkerhetskorrigeringen för Windows med hjälp av filen . Den virtuella matrisen startas om efter installationen och du måste logga in på det lokala webbgränssnittet.


## <a name="next-steps"></a>Nästa steg

Läs mer om [hur du administrerar den virtuella storsimple-matrisen](storsimple-ova-web-ui-admin.md).
