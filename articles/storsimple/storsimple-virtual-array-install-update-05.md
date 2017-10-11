---
title: "Installera uppdatering 0,5 på virtuella StorSimple-matrisen | Microsoft Docs"
description: "Beskriver hur du använder virtuella StorSimple-matris webbgränssnittet för att tillämpa uppdateringar med hjälp av Azure portal och snabbkorrigering metoden"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/10/2017
ms.author: alkohli
ms.openlocfilehash: c47da5b90c16e2d5b5709e2a6affc026238b9468
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="install-update-05-on-your-storsimple-virtual-array"></a>Installera uppdateringen 0,5 på din virtuella StorSimple-matris

## <a name="overview"></a>Översikt

Den här artikeln beskriver de steg som krävs för att installera uppdateringen 0,5 på din virtuella StorSimple-matrisen via lokala webbgränssnittet och via Azure portal. Du måste tillämpa uppdateringar eller snabbkorrigeringar för att hålla det uppdaterat din virtuella StorSimple-matris.

Innan du installerar en uppdatering, rekommenderar vi att du vidtar volymer eller resurser offline på värden först och sedan enheten. Detta minimerar möjlighet att data skadas. När volymer eller resurser som är offline, bör du också se en manuell säkerhetskopiering av enheten.

> [!IMPORTANT]
> - Uppdatera 0,5 motsvarar **10.0.10290.0** programvaruversionen på enheten. För information om vad är nytt i den här uppdateringen, gå till [viktig information för uppdatering 0,5](storsimple-virtual-array-update-05-release-notes.md).
>
> - Om du kör uppdatering 0,2 eller senare, rekommenderar vi att installerar du uppdateringar via Azure portal. Om du kör uppdatering 0.1 eller GA programvaruversioner måste du använda metoden snabbkorrigeringen via lokala webbgränssnittet för att installera uppdateringen 0,5.
>
> - Tänk på att installera en uppdatering eller snabbkorrigering startar om enheten. Med hänsyn till att den virtuella StorSimple-matrisen är en enskild nod-enhet, avbryts alla i/o pågår och din enhet upplever driftstopp.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Om du kör uppdatering 0,2 och senare, rekommenderar vi att du installerar uppdateringar via Azure-portalen. Portalen proceduren kräver att användaren skanna, hämta och installera uppdateringarna. Den här proceduren tar cirka 7 minuter för att slutföra. Utför följande steg för att installera uppdatering eller snabbkorrigering.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

När installationen är klar, gå till Enhetshanteraren för StorSimple-tjänsten. Välj **enheter** och markera och klicka på den enhet som du precis har uppdaterats. Gå till **Inställningar > Hantera > Enhetsuppdateringar**. Programvaruversionen visas ska vara **10.0.10290.0**.

## <a name="use-the-local-web-ui"></a>Använda lokala webbgränssnittet

Det finns två steg när du använder lokala webbgränssnittet:

* Hämta uppdateringen eller snabbkorrigeringen
* Installera uppdateringen eller snabbkorrigeringen

### <a name="download-the-update-or-the-hotfix"></a>Hämta uppdateringen eller snabbkorrigeringen

Utför följande steg för att hämta programuppdateringen från Microsoft Update Catalog.

#### <a name="to-download-the-update-or-the-hotfix"></a>Hämta uppdateringen eller snabbkorrigeringen

1. Starta Internet Explorer och gå till [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Om det här är första gången du använder Microsoft Update Catalog på den här datorn klickar du på **Installera** när du uppmanas att installera tillägget för Microsoft Update Catalog.

3. Ange antalet den snabbkorrigering som du vill hämta Knowledge Base (KB) i sökrutan i Microsoft Update-katalogen. Ange **4021576** för Update 0,5 och klicka sedan på **Sök**.
   
    Snabbkorrigeringen listan visas till exempel **StorSimple virtuell matris uppdatering 0,5**.
   
    ![Sökkatalog](./media/storsimple-virtual-array-install-update-05/download1.png)

4. Klicka på **Hämta**. 

5. Du bör se två filer ska hämtas en *.msu* och en *CAB* fil. Hämta var och en av dessa filer till en mapp. Mappen kan också kopieras till en nätverksresurs som kan nås från enheten.

6. Öppna mappen där filerna finns.
    ![Filer i paketet](./media/storsimple-virtual-array-install-update-05/update05folder.png)

    Du ser:
    -  En paketfil för Microsoft Update fristående `WindowsTH-KB3011067-x64`. Den här filen används för att uppdatera enheten.
    - En paketfil Geneva Monitoring Agent `GenevaMonitoringAgentPackageInstaller`. Den här filen används för att uppdatera agenten övervaknings- och diagnostikfunktionerna service (MDS). Dubbelklicka på cab-filen. En .msi visas. Markera filen, högerklicka och sedan **extrahera** filen. Du kommer att använda den _.msi_ fil att uppdatera agenten.

        ![Extrahera uppdateringsfil MDS-agenten](./media/storsimple-virtual-array-install-update-05/extract-geneva-monitoring-agent-installer.png)
        
    

### <a name="install-the-update-or-the-hotfix"></a>Installera uppdateringen eller snabbkorrigeringen

Kontrollera att du har uppdateringen eller snabbkorrigeringen hämtas antingen lokalt på värden eller tillgängligt via en nätverksresurs före installationen uppdatering eller snabbkorrigering.

Använd den här metoden för att installera uppdateringar på en enhet som kör GA eller uppdatera 0,1 programvaruversioner. Den här proceduren tar mindre än 2 minuter att slutföra. Utför följande steg för att installera uppdatering eller snabbkorrigering.

#### <a name="to-install-the-update-or-the-hotfix"></a>Så här installerar du uppdateringen eller snabbkorrigeringen

1. Gå till i det lokala webbgränssnittet **Underhåll** > **programuppdateringen**.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. I **uppdatering filsökväg**, ange filnamnet för uppdateringen eller snabbkorrigeringen. Du kan även bläddra till installationsfilen uppdatering eller snabbkorrigering om placeras på en nätverksresurs. Klicka på **Använd**.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. En varning visas. Angivna detta är en enskild nod-enhet när uppdateringen har genomförts, enheten startas om och det finns en avbrottstid. Klicka på kryssikonen.
   
   ![uppdatera enhet](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. Uppdateringen startar. När enheten har uppdaterats, startas om. Lokala Användargränssnittet är inte tillgängligt i den här tiden.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. När omstarten är klar, kommer du till den **inloggning** sidan. Kontrollera att enhetens programvara har uppdaterats i lokala webbgränssnittet, gå till **Underhåll** > **programuppdateringen**. Programvaruversionen visas ska vara **10.0.0.0.0.10290.0** för uppdatering 0,5.
   
   > [!NOTE]
   > Vi rapporterar programvaruversioner i ett lite annorlunda sätt i lokala webbgränssnittet och Azure portal. Till exempel lokala webbgränssnittet rapporterar **10.0.0.0.0.10290** och Azure portal rapporter **10.0.10290.0** för samma version.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-05/update6m.png)

6. Nästa steg är att uppdatera MDS-agenten. I den **programuppdateringen** går du till den **uppdatering filsökväg** och bläddra till den `GenevaMonitoringAgentPackageInstaller.msi` filen. Upprepa steg 2-4. När den virtuella matrisen har startats om logga in på det lokala webbgränssnittet.

Uppdateringen är slutförd.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [administrera din virtuella StorSimple-matris](storsimple-ova-web-ui-admin.md).

