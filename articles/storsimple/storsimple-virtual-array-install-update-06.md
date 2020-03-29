---
title: Installera uppdatering 0.6 på StorSimple Virtual Array | Microsoft-dokument
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
ms.date: 05/18/2017
ms.author: alkohli
ms.openlocfilehash: 5f0be5d8378cd1640d3052f2e56c8161e2c0b203
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "62116899"
---
# <a name="install-update-06-on-your-storsimple-virtual-array"></a>Installera uppdatering 0.6 på den virtuella storsimple-matrisen

## <a name="overview"></a>Översikt

I den här artikeln beskrivs de steg som krävs för att installera Uppdatering 0.6 på din Virtuella StorSimple-matris via det lokala webbgränssnittet och via Azure-portalen. Du installerar programuppdateringar eller snabbkorrigeringar för att hålla din StorSimple Virtual Array uppdaterad.

Innan du installerar en uppdatering rekommenderar vi att du tar volymerna eller resurserna offline på värden först och sedan enheten. Detta minskar risken för skadade data. När volymerna eller resurserna är offline bör du också göra en manuell säkerhetskopiering av enheten.

> [!IMPORTANT]
> - Uppdatering 0.6 motsvarar **programvaruversionen av programvaran 10.0.10293.0** på enheten. Information om vad som är nytt i den här uppdateringen finns i [Viktig information för uppdatering 0.6](storsimple-virtual-array-update-06-release-notes.md).
>
> - Om du kör Uppdatering 0.2 eller senare rekommenderar vi att du installerar uppdateringarna via Azure-portalen. Om du kör programversioner av Update 0.1 eller GA måste du använda snabbkorrigeringsmetoden via det lokala webbgränssnittet för att installera uppdatering 0.6.
>
> - Tänk på att om du installerar en uppdatering eller snabbkorrigering startas om enheten. Med tanke på att StorSimple Virtual Array är en enda nodenhet störs alla I/O som pågår och enheten drabbas av driftstopp.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Om du kör Uppdatering 0.2 och senare rekommenderar vi att du installerar uppdateringar via Azure-portalen. Portalproceduren kräver att användaren söker igenom, hämtar och installerar uppdateringarna. Den här proceduren tar cirka 7 minuter att slutföra. Gör följande för att installera uppdateringen eller snabbkorrigeringen.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

När installationen är klar går du till tjänsten StorSimple Device Manager. Välj **Enheter** och välj och klicka sedan på den enhet som du just har uppdaterat. Gå till **Inställningar > Hantera > enhetsuppdateringar**. Den visade programvaruversionen ska vara **10.0.10293.0**.

## <a name="use-the-local-web-ui"></a>Använda det lokala webbgränssnittet

Det finns två steg när du använder det lokala webbgränssnittet:

* Hämta uppdateringen eller snabbkorrigeringen
* Installera uppdateringen eller snabbkorrigeringen

### <a name="download-the-update-or-the-hotfix"></a>Hämta uppdateringen eller snabbkorrigeringen

Utför följande steg för att hämta programuppdateringen från Microsoft Update Catalog.

#### <a name="to-download-the-update-or-the-hotfix"></a>Så här hämtar du uppdateringen eller snabbkorrigeringen

1. Starta Internet Explorer [https://catalog.update.microsoft.com](https://catalog.update.microsoft.com)och navigera till .

2. Om du använder Microsoft Update Catalog för första gången på den här datorn klickar du på **Installera** när du uppmanas att installera tillägget Microsoft Update Catalog.

3. I sökrutan i Microsoft Update Catalog anger du knowledge base-numret (KB) för den snabbkorrigering som du vill hämta. Ange **4023268** för uppdatering 0.6 och klicka sedan på **Sök**.
   
    Snabbkorrigeringslistan visas till exempel **StorSimple Virtual Array Update 0.6**.
   
    ![Sökkatalog](./media/storsimple-virtual-array-install-update-06/download1.png)

4. Klicka på **Ladda ner**.

5. Du bör se fem filer att ladda ner. Ladda ner var och en av dessa filer till en mapp. Mappen kan också kopieras till en nätverksresurs som kan nås från enheten.

6. Öppna mappen där filerna finns.
    ![Filer i paketet](./media/storsimple-virtual-array-install-update-06/update06folder.png)

    Du ser:
    -  En fristående paketfil `WindowsTH-KB3011067-x64`för Microsoft Update . Den här filen används för att uppdatera enhetsprogrammet.
    - En paketerad paketfil `GenevaMonitoringAgentPackageInstaller`för övervakningsagenten i Genève. Den här filen används för att uppdatera MDS-agenten (Monitoring and Diagnostics Service). Dubbelklicka på hyttfilen. En _MSI_ visas. Markera filen, högerklicka och **extrahera** filen. Du använder _MSI-filen_ för att uppdatera agenten.

        ![Extrahera MDS-agentuppdateringsfil](./media/storsimple-virtual-array-install-update-06/extract-geneva-monitoring-agent-installer.png)

        > [!IMPORTANT]
        > Du behöver inte uppdatera MDS-agenten om du kör StorSimple Update 0.5 (0.0.10293.0).

    - Tre filer som innehåller viktiga `windows8.1-kb4012213-x64``windows8.1-kb3205400-x64`säkerhetsuppdateringar `windows8.1-kb4019213-x64`för Windows, , och .


### <a name="install-the-update-or-the-hotfix"></a>Installera uppdateringen eller snabbkorrigeringen

Innan uppdateringen eller snabbkorrigeringsinstallationen ska du se till att du har uppdateringen eller snabbkorrigeringen nedladdad antingen lokalt på värden eller tillgänglig via en nätverksresurs.

Använd den här metoden för att installera uppdateringar på en enhet som kör GA- eller Update 0.1-programversioner. Den här proceduren tar cirka 12 minuter att slutföra. Gör följande för att installera uppdateringen eller snabbkorrigeringen.

#### <a name="to-install-the-update-or-the-hotfix"></a>Så här installerar du uppdateringen eller snabbkorrigeringen

1. Gå till > **Underhållsprogramuppdatering**i **Maintenance**det lokala webbgränssnittet . Anteckna den programvaruversion som du kör. Om du kör **10.0.10290.0**behöver du inte uppdatera MDS-agenten i steg 6.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-05/update1m.png)

2. Ange filnamnet för uppdateringen eller snabbkorrigeringen i **sökvägen**uppdatera. Du kan också bläddra till installationsfilen för uppdatering eller snabbkorrigering om den placeras på en nätverksresurs. Klicka på **Använd**.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-05/update2m.png)

3. En varning visas. Med tanke på den virtuella matrisen är en enda nod enhet, efter uppdateringen tillämpas, enheten startar om och det finns driftstopp. Klicka på checkikonen.
   
   ![uppdatera enhet](./media/storsimple-virtual-array-install-update-05/update3m.png)

4. Uppdateringen startar. När enheten har uppdaterats startas den om. Det lokala användargränssnittet är inte tillgängligt under den här varaktigheten.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-05/update5m.png)

5. När omstarten är klar tas du till **inloggningssidan.** Om du vill kontrollera att enhetsprogrammet har uppdaterats går du till > **Underhållsprogramuppdatering**i det lokala webbgränssnittet . **Maintenance** Den visade programvaruversionen ska vara **10.0.0.0.0.10293** för uppdatering 0.6.
   
   > [!NOTE]
   > Vi rapporterar programvaruversionerna på ett något annorlunda sätt i det lokala webbgränssnittet och Azure-portalen. Det lokala webbgränssnittet rapporterar till exempel **10.0.0.0.0.10293** och Azure-portalen **rapporterar 10.0.10293.0** för samma version.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update-06/update6m.png)

6. Hoppa över det här steget om du körde StorSimple Virtual Array Update 0.5 **(10.0.10290.0**) innan du använde den här uppdateringen. Du har antecknat programvaruversionen i steg 1 innan du började uppdatera. Om du körde Uppdatering 0.5 är MDS-agenten redan uppdaterad .

    Om du kör en programvaruversion före uppdatering 0.5 är nästa steg för dig att uppdatera MDS-agenten. Gå till **sökvägen Uppdatera filen** på sidan `GenevaMonitoringAgentPackageInstaller.msi` **Programuppdatering** och bläddra till filen. Upprepa steg 2-4. När den virtuella matrisen har startats om loggar du in på det lokala webbgränssnittet.

7. Upprepa steg 2-4 för att installera `windows8.1-kb4012213-x64`säkerhetskorrigeringarna i Windows med hjälp av filer ,`windows8.1-kb3205400-x64`och `windows8.1-kb4019213-x64`. Den virtuella matrisen startas om efter varje installation och du måste logga in på det lokala webbgränssnittet.

## <a name="next-steps"></a>Nästa steg

Läs mer om [hur du administrerar den virtuella storsimple-matrisen](storsimple-ova-web-ui-admin.md).

