---
title: Installera uppdateringar på StorSimple Virtual Array | Microsoft-dokument
description: Beskriver hur du använder webbgränssnittet StorSimple Virtual Array för att installera uppdateringar med azure-portalen och snabbkorrigeringsmetoden
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/07/2017
ms.author: alkohli
ms.openlocfilehash: b67fcb82bdcc94d7faeceedb7420a869e6578cad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "61436468"
---
# <a name="install-update-04-on-your-storsimple-virtual-array"></a>Installera uppdatering 0.4 på den virtuella storsimple-matrisen

## <a name="overview"></a>Översikt

I den här artikeln beskrivs de steg som krävs för att installera uppdatering 0.4 på din Virtuella StorSimple-matris via det lokala webbgränssnittet och via Azure-portalen. Du måste installera programuppdateringar eller snabbkorrigeringar för att hålla din StorSimple Virtual Array uppdaterad. 

Tänk på att om du installerar en uppdatering eller snabbkorrigering startas om enheten. Med tanke på att StorSimple Virtual Array är en enda nodenhet störs alla I/O som pågår och enheten drabbas av driftstopp. 

Innan du installerar en uppdatering rekommenderar vi att du tar volymerna eller resurserna offline på värden först och sedan enheten. Detta minskar risken för skadade data.

> [!IMPORTANT]
> Om du kör programversioner för Update 0.1 eller GA måste du använda snabbkorrigeringsmetoden via det lokala webbgränssnittet för att installera uppdatering 0.3. Om du kör Uppdatering 0.2 eller senare rekommenderar vi att du installerar uppdateringarna via Azure-portalen.
 

## <a name="use-the-local-web-ui"></a>Använda det lokala webbgränssnittet

Det finns två steg när du använder det lokala webbgränssnittet:

* Hämta uppdateringen eller snabbkorrigeringen
* Installera uppdateringen eller snabbkorrigeringen

### <a name="download-the-update-or-the-hotfix"></a>Hämta uppdateringen eller snabbkorrigeringen

Utför följande steg för att hämta programuppdateringen från Microsoft Update Catalog.

#### <a name="to-download-the-update-or-the-hotfix"></a>Så här hämtar du uppdateringen eller snabbkorrigeringen

1. Starta Internet Explorer [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)och navigera till .

2. Om det här är första gången du använder Microsoft Update Catalog på den här datorn klickar du på **Installera** när du uppmanas att installera tillägget för Microsoft Update Catalog.

3. I sökrutan i Microsoft Update Catalog anger du knowledge base-numret (KB) för den snabbkorrigering som du vill hämta. Ange **3216577** för uppdatering 0.4 och klicka sedan på **Sök**.
   
    Snabbkorrigeringslistan visas till exempel **StorSimple Virtual Array Update 0.4**.
   
    ![Sökkatalog](./media/storsimple-virtual-array-install-update-04/download1.png)

4. Klicka på **Lägg till**. Uppdateringen läggs till i korgen.

5. Klicka på **View Basket** (Visa korg).

6. Klicka på **Ladda ner**. Ange eller **Bläddra** till en lokal plats där du vill att nedladdningarna ska läggas. Uppdateringarna hämtas till den angivna platsen och placeras i en undermapp med samma namn som uppdateringen. Mappen kan också kopieras till en nätverksresurs som kan nås från enheten.

7. Öppna den kopierade mappen, bör du se en `WindowsTH-KB3011067-x64`Microsoft Update fristående paketfil . Den här filen används för att installera uppdateringen eller snabbkorrigeringen.

### <a name="install-the-update-or-the-hotfix"></a>Installera uppdateringen eller snabbkorrigeringen

Innan uppdateringen eller snabbkorrigeringsinstallationen ska du se till att du har uppdateringen eller snabbkorrigeringen nedladdad antingen lokalt på värden eller tillgänglig via en nätverksresurs. 

Använd den här metoden för att installera uppdateringar på en enhet som kör GA- eller Update 0.1-programversioner. Den här proceduren tar mindre än 2 minuter att slutföra. Gör följande för att installera uppdateringen eller snabbkorrigeringen.

#### <a name="to-install-the-update-or-the-hotfix"></a>Så här installerar du uppdateringen eller snabbkorrigeringen

1. Gå till > **Underhållsprogramuppdatering**i **Maintenance**det lokala webbgränssnittet .
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update/update1m.png)

2. Ange filnamnet för uppdateringen eller snabbkorrigeringen i **sökvägen**uppdatera. Du kan också bläddra till installationsfilen för uppdatering eller snabbkorrigering om den placeras på en nätverksresurs. Klicka på **Använd**.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update/update2m.png)

3. En varning visas. Med tanke på detta är en enda nod enhet, efter uppdateringen tillämpas, enheten startar om och det finns driftstopp. Klicka på checkikonen.
   
   ![uppdatera enhet](./media/storsimple-virtual-array-install-update/update3m.png)

4. Uppdateringen startar. När enheten har uppdaterats startas den om. Det lokala användargränssnittet är inte tillgängligt under den här varaktigheten.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update/update5m.png)

5. När omstarten är klar tas du till **inloggningssidan.** Om du vill kontrollera att enhetsprogrammet har uppdaterats går du till > **Underhållsprogramuppdatering**i det lokala webbgränssnittet . **Maintenance** Den visade programvaruversionen ska vara **10.0.0.0.0.10289.0** för uppdatering 0.4.
   
   > [!NOTE]
   > Vi rapporterar programvaruversionerna på ett något annorlunda sätt i det lokala webbgränssnittet och Azure-portalen. Det lokala webbgränssnittet rapporterar till exempel **10.0.0.0.0.10289** och Azure-portalen rapporterar **10.0.10289.0** för samma version.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update/update6m.png)

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Om du kör Uppdatering 0.2 och senare rekommenderar vi att du installerar uppdateringar via Azure-portalen. Portalproceduren kräver att användaren söker igenom, hämtar och installerar uppdateringarna. Den här proceduren tar cirka 7 minuter att slutföra. Gör följande för att installera uppdateringen eller snabbkorrigeringen.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

När installationen är klar (enligt uppgift enligt jobbstatus på 100 %) går du till tjänsten StorSimple Device Manager. Välj **Enheter** och välj och klicka sedan på den enhet som du vill uppdatera från listan över enheter som är anslutna till den här tjänsten. Gå till Avsnittet **Hantera** i bladet **Inställningar** och välj **Enhetsuppdateringar**. Den visade programvaruversionen ska vara **10.0.10289.0**.


## <a name="next-steps"></a>Nästa steg

Läs mer om [hur du administrerar den virtuella storsimple-matrisen](storsimple-ova-web-ui-admin.md).

