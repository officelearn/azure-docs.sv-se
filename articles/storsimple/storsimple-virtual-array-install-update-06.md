---
title: Installera uppdatering 0.6 på StorSimple Virtual Array | Microsoft Docs
description: Beskriver hur du använder StorSimple Virtual Array webbgränssnittet för att tillämpa uppdateringar med hjälp av metoden för Azure-portalen och snabbkorrigeringen
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
ms.date: 05/18/2017
ms.author: alkohli
ms.openlocfilehash: 89ca7a5fa13696856bed108e4cbf7462b536b4bb
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246624"
---
# <a name="install-update-06-on-your-storsimple-virtual-array"></a>Installera uppdatering 0.6 på StorSimple Virtual Array

## <a name="overview"></a>Översikt

Den här artikeln beskriver de steg som krävs för att installera uppdatering 0.6 på StorSimple Virtual Array via det lokala webbgränssnittet eller via Azure portal. Installation av uppdateringar eller snabbkorrigeringar att hålla din StorSimple Virtual Array uppdaterade.

Innan du installerar en uppdatering, rekommenderar vi att du vidtar volymer eller resurser offline på värden första och sedan enheten. Detta minskar risken för skadade data. När volymer eller resurser är offline kan bör du också utföra en manuell säkerhetskopiering av enheten.

> [!IMPORTANT]
> - Uppdatering 0.6 motsvarar **10.0.10293.0** programvaruversionen på din enhet. För information om vad som är nytt i den här uppdateringen, går du till [viktig information för uppdatering 0.6](storsimple-virtual-array-update-06-release-notes.md).
>
> - Om du kör uppdatering 0.2 eller senare, rekommenderar vi att installerar du uppdateringar via Azure portal. Om du kör uppdatering 0.1 eller GA programvaruversioner, måste du använda metoden snabbkorrigering via det lokala webbgränssnittet för att installera uppdatering 0.6.
>
> - Tänk på att installera en uppdatering eller snabbkorrigering startar om enheten. Med hänsyn till att StorSimple Virtual Array är en enskild nod-enhet, alla i/o pågår avbryts och din enhet upplever driftstopp.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Om du kör Update 0,2 och senare, rekommenderar vi att du installerar uppdateringar via Azure portal. Portalen proceduren kräver att användaren att skanna, ladda ned och installera uppdateringarna. Den här proceduren tar cirka 7 minuter för att slutföra. Utför följande steg för att installera uppdatering eller snabbkorrigering.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

När installationen är klar, går du till din StorSimple Device Manager-tjänsten. Välj **enheter** och markera och klicka på den enhet som du uppdaterade nyss. Gå till **Inställningar > Hantera > Enhetsuppdateringar**. Den visa programvaruversionen ska vara **10.0.10293.0**.

## <a name="use-the-local-web-ui"></a>Använd det lokala webbgränssnittet

Det finns två steg när du använder det lokala webbgränssnittet:

* Ladda ned uppdateringen eller snabbkorrigeringen
* Installera uppdateringen eller snabbkorrigeringen

### <a name="download-the-update-or-the-hotfix"></a>Ladda ned uppdateringen eller snabbkorrigeringen

Utför följande steg för att hämta programuppdateringen från Microsoft Update Catalog.

#### <a name="to-download-the-update-or-the-hotfix"></a>Ladda ned uppdateringen eller snabbkorrigeringen

1. Starta Internet Explorer och navigera till [ http://catalog.update.microsoft.com ](https://catalog.update.microsoft.com).

2. Om du använder Microsoft Update-katalogen för första gången på den här datorn, klickar du på **installera** när du uppmanas att installera tillägget för Microsoft Update-katalogen.

3. I sökrutan i Microsoft Update-katalogen, anger du numret för Knowledge Base (KB) för den snabbkorrigering som du vill hämta. Ange **4023268** uppdatering 0.6 för och klicka sedan på **Search**.
   
    I listan över snabbkorrigeringar visas, till exempel **StorSimple Virtual Array uppdatering 0.6**.
   
    ![Sökkatalog](./media/storsimple-virtual-array-install-update-06/download1.png)

4. Klicka på **Hämta**.

5. Du bör se fem filer att ladda ned. Hämta var och en av dessa filer till en mapp. Mappen kan också kopieras till en nätverksresurs som kan nås från enheten.

6. Öppna mappen där filerna finns.
    ![Filer i paketet](./media/storsimple-virtual-array-install-update-06/update06folder.png)

    Du ser:
    -  En paketfil för Microsoft Update fristående `WindowsTH-KB3011067-x64`. Den här filen används för att uppdatera enhetens programvara.
    - En paketfil Geneva Monitoring Agent `GenevaMonitoringAgentPackageInstaller`. Den här filen används för att uppdatera tjänsten (MDS)-agenten för övervakning och diagnostik. Dubbelklicka på cab-filen. En _.msi_ visas. Markera filen, högerklicka, och sedan **extrahera** filen. Du använder den _.msi_ fil som ska uppdateras agenten.

        ![Extrahera Agentuppdatering för MDS-filen](./media/storsimple-virtual-array-install-update-06/extract-geneva-monitoring-agent-installer.png)

        > [!IMPORTANT]
        > Du behöver inte uppdatera MDS-agenten om du använder StorSimple uppdatering 0.5 (0.0.10293.0).

    - Tre filer som innehåller kritiska säkerhetsuppdateringar för Windows, `windows8.1-kb4012213-x64`,`windows8.1-kb3205400-x64`, och `windows8.1-kb4019213-x64`.


### <a name="install-the-update-or-the-hotfix"></a>Installera uppdateringen eller snabbkorrigeringen

Kontrollera att du har uppdateringen eller snabbkorrigeringen ned antingen lokalt på värden eller komma åt via en nätverksresurs innan installationen av uppdateringen eller snabbkorrigeringen.

Använd den här metoden för att installera uppdateringar på en enhet som kör GA eller uppdatera 0,1 programvaruversioner. Den här proceduren tar cirka 12 minuter att slutföra. Utför följande steg för att installera uppdatering eller snabbkorrigering.

#### <a name="to-install-the-update-or-the-hotfix"></a>Att installera uppdateringen eller snabbkorrigeringen

1. I det lokala webbgränssnittet går du till **Underhåll** > **programuppdateringen**. Anteckna den programvaruversion av som du kör. Om du kör **10.0.10290.0**, du behöver inte uppdatera MDS-agenten i steg 6.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. I **uppdatering filsökväg**, ange filnamnet för uppdateringen eller snabbkorrigeringen. Du kan också bläddra till installationsfilen för uppdateringen eller snabbkorrigeringen om placeras på en nätverksresurs. Klicka på **Verkställ**.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. En varning visas. Är en enskild nod-enhet, beroende på den virtuella matrisen när uppdateringen har tillämpats, enheten startas om och stilleståndstid. Klicka på kryssikonen.
   
   ![uppdatera enhet](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. Uppdateringen startar. När enheten har uppdaterats, den startar om. Lokala Användargränssnittet är inte tillgänglig under den tiden.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. När omstarten är klar kommer du till den **logga in** sidan. Kontrollera att programmet har uppdaterats i det lokala webbgränssnittet, gå till **Underhåll** > **programuppdateringen**. Den visa programvaruversionen ska vara **10.0.0.0.0.10293** för uppdatering 0.6.
   
   > [!NOTE]
   > Vi rapporterar programvaruversionerna i ett något annorlunda sätt i det lokala webbgränssnittet och Azure-portalen. Exempelvis kan det lokala webbgränssnittet rapporterar **10.0.0.0.0.10293** och Azure portal rapporter **10.0.10293.0** för samma version.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-06/update6m.png)

6. Hoppa över det här steget om du använder StorSimple Virtual Array uppdatering 0.5 (**10.0.10290.0**) innan du installerat den här uppdateringen. Du antecknade programvaruversionen i steg 1 innan du påbörjande att uppdatera. Om du körde uppdatering 0.5 är MDS-agenten redan uppdaterad.

    Om du kör en programvaruversion före uppdatering 0.5 är nästa steg för att uppdatera MDS-agenten. I den **programuppdateringen** går du till den **uppdatering filsökväg** och bläddra till den `GenevaMonitoringAgentPackageInstaller.msi` filen. Upprepa steg 2 – 4. När den virtuella matrisen har startats om logga in på det lokala webbgränssnittet.

7. Upprepa steg 2 – 4 installera Windows-säkerhet korrigeringar med hjälp av filer `windows8.1-kb4012213-x64`,`windows8.1-kb3205400-x64`, och `windows8.1-kb4019213-x64`. Den virtuella matrisen startas om efter varje installation och du måste logga in på det lokala webbgränssnittet.

## <a name="next-steps"></a>Nästa steg

Läs mer om [administrera StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

