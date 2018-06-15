---
title: Installera uppdatering 0,6 på virtuella StorSimple-matrisen | Microsoft Docs
description: Beskriver hur du använder virtuella StorSimple-matris webbgränssnittet för att tillämpa uppdateringar med hjälp av Azure portal och snabbkorrigering metoden
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
ms.openlocfilehash: 111976cd684561f5bc63b92f09a20470fe3036d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875909"
---
# <a name="install-update-06-on-your-storsimple-virtual-array"></a>Installera uppdateringen 0,6 på din virtuella StorSimple-matris

## <a name="overview"></a>Översikt

Den här artikeln beskriver de steg som krävs för att installera uppdateringen 0,6 på din virtuella StorSimple-matrisen via lokala webbgränssnittet och via Azure portal. Du kan tillämpa uppdateringar eller snabbkorrigeringar för att hålla det uppdaterat din virtuella StorSimple-matris.

Innan du installerar en uppdatering, rekommenderar vi att du vidtar volymer eller resurser offline på värden först och sedan enheten. Detta minimerar möjlighet att data skadas. När volymer eller resurser som är offline, bör du också se en manuell säkerhetskopiering av enheten.

> [!IMPORTANT]
> - Uppdatera 0,6 motsvarar **10.0.10293.0** programvaruversionen på enheten. För information om vad är nytt i den här uppdateringen, gå till [viktig information för uppdatering 0,6](storsimple-virtual-array-update-06-release-notes.md).
>
> - Om du kör uppdatering 0,2 eller senare, rekommenderar vi att installerar du uppdateringar via Azure portal. Om du kör uppdatering 0.1 eller GA programvaruversioner måste du använda metoden snabbkorrigeringen via lokala webbgränssnittet för att installera uppdateringen 0,6.
>
> - Tänk på att installera en uppdatering eller snabbkorrigering startar om enheten. Med hänsyn till att den virtuella StorSimple-matrisen är en enskild nod-enhet, avbryts alla i/o pågår och din enhet upplever driftstopp.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Om du kör uppdatering 0,2 och senare, rekommenderar vi att du installerar uppdateringar via Azure-portalen. Portalen proceduren kräver att användaren skanna, hämta och installera uppdateringarna. Den här proceduren tar cirka 7 minuter för att slutföra. Utför följande steg för att installera uppdatering eller snabbkorrigering.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

När installationen är klar, gå till Enhetshanteraren för StorSimple-tjänsten. Välj **enheter** och markera och klicka på den enhet som du precis har uppdaterats. Gå till **Inställningar > Hantera > Enhetsuppdateringar**. Programvaruversionen visas ska vara **10.0.10293.0**.

## <a name="use-the-local-web-ui"></a>Använda lokala webbgränssnittet

Det finns två steg när du använder lokala webbgränssnittet:

* Hämta uppdateringen eller snabbkorrigeringen
* Installera uppdateringen eller snabbkorrigeringen

### <a name="download-the-update-or-the-hotfix"></a>Hämta uppdateringen eller snabbkorrigeringen

Utför följande steg för att hämta programuppdateringen från Microsoft Update Catalog.

#### <a name="to-download-the-update-or-the-hotfix"></a>Hämta uppdateringen eller snabbkorrigeringen

1. Starta Internet Explorer och gå till [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Om du använder Microsoft Update-katalogen för första gången på den här datorn, klickar du på **installera** när du uppmanas att installera tillägget för Microsoft Update-katalogen.

3. Ange antalet den snabbkorrigering som du vill hämta Knowledge Base (KB) i sökrutan i Microsoft Update-katalogen. Ange **4023268** för Update 0,6 och klicka sedan på **Sök**.
   
    Snabbkorrigeringen listan visas till exempel **StorSimple virtuell matris uppdatering 0,6**.
   
    ![Sökkatalog](./media/storsimple-virtual-array-install-update-06/download1.png)

4. Klicka på **Hämta**.

5. Du bör se fem filer som ska hämtas. Hämta var och en av dessa filer till en mapp. Mappen kan också kopieras till en nätverksresurs som kan nås från enheten.

6. Öppna mappen där filerna finns.
    ![Filer i paketet](./media/storsimple-virtual-array-install-update-06/update06folder.png)

    Du ser:
    -  En paketfil för Microsoft Update fristående `WindowsTH-KB3011067-x64`. Den här filen används för att uppdatera enheten.
    - En paketfil Geneva Monitoring Agent `GenevaMonitoringAgentPackageInstaller`. Den här filen används för att uppdatera agenten övervaknings- och diagnostikfunktionerna service (MDS). Dubbelklicka på cab-filen. En _.msi_ visas. Markera filen, högerklicka och sedan **extrahera** filen. Du använder den _.msi_ fil att uppdatera agenten.

        ![Extrahera uppdateringsfil MDS-agenten](./media/storsimple-virtual-array-install-update-06/extract-geneva-monitoring-agent-installer.png)

        > [!IMPORTANT]
        > Du behöver inte uppdatera MDS-agenten om du kör StorSimple uppdatering 0,5 (0.0.10293.0).

    - Tre filer som innehåller viktiga uppdateringar för Windows-säkerhet, `windows8.1-kb4012213-x64`,`windows8.1-kb3205400-x64`, och `windows8.1-kb4019213-x64`.


### <a name="install-the-update-or-the-hotfix"></a>Installera uppdateringen eller snabbkorrigeringen

Kontrollera att du har uppdateringen eller snabbkorrigeringen hämtas antingen lokalt på värden eller tillgängligt via en nätverksresurs före installationen uppdatering eller snabbkorrigering.

Använd den här metoden för att installera uppdateringar på en enhet som kör GA eller uppdatera 0,1 programvaruversioner. Den här proceduren tar cirka 12 minuter att slutföra. Utför följande steg för att installera uppdatering eller snabbkorrigering.

#### <a name="to-install-the-update-or-the-hotfix"></a>Så här installerar du uppdateringen eller snabbkorrigeringen

1. Gå till i det lokala webbgränssnittet **Underhåll** > **programuppdateringen**. Anteckna den programvaruversion av som du kör. Om du kör **10.0.10290.0**, du behöver inte uppdatera MDS-agenten i steg 6.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. I **uppdatering filsökväg**, ange filnamnet för uppdateringen eller snabbkorrigeringen. Du kan även bläddra till installationsfilen uppdatering eller snabbkorrigering om placeras på en nätverksresurs. Klicka på **Använd**.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. En varning visas. Det angivna virtuella matrisen är en enskild nod-enhet när uppdateringen har genomförts, enheten startas om och det finns en avbrottstid. Klicka på kryssikonen.
   
   ![uppdatera enhet](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. Uppdateringen startar. När enheten har uppdaterats, startas om. Lokala Användargränssnittet är inte tillgängligt i den här tiden.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. När omstarten är klar, kommer du till den **inloggning** sidan. Kontrollera att enhetens programvara har uppdaterats i lokala webbgränssnittet, gå till **Underhåll** > **programuppdateringen**. Programvaruversionen visas ska vara **10.0.0.0.0.10293** för uppdatering 0,6.
   
   > [!NOTE]
   > Vi rapporterar programvaruversioner i ett lite annorlunda sätt i lokala webbgränssnittet och Azure portal. Till exempel lokala webbgränssnittet rapporterar **10.0.0.0.0.10293** och Azure portal rapporter **10.0.10293.0** för samma version.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-06/update6m.png)

6. Hoppa över detta steg om du körde StorSimple virtuell matris uppdatering 0,5 (**10.0.10290.0**) innan du installerat den här uppdateringen. Du antecknade programvaruversionen i steg 1 innan du började uppdatera. Om du körde uppdateringen 0,5 är MDS-agenten redan uppdaterad.

    Om du kör en programvaruversion före uppdateringen 0,5 är nästa steg för att uppdatera MDS-agenten. I den **programuppdateringen** går du till den **uppdatering filsökväg** och bläddra till den `GenevaMonitoringAgentPackageInstaller.msi` filen. Upprepa steg 2-4. När den virtuella matrisen har startats om logga in på det lokala webbgränssnittet.

7. Upprepa steg 2-4 för att installera Windows-säkerhet åtgärdas med hjälp av filer `windows8.1-kb4012213-x64`,`windows8.1-kb3205400-x64`, och `windows8.1-kb4019213-x64`. Virtuella matrisen startas om efter varje installation och du behöver logga in på det lokala webbgränssnittet.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [administrera din virtuella StorSimple-matris](storsimple-ova-web-ui-admin.md).

