---
title: Installera uppdateringar på StorSimple Virtual Array | Microsoft Docs
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
ms.date: 02/07/2017
ms.author: alkohli
ms.openlocfilehash: cd3fdec0e50d64af28306034e6e1086e67526b7d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51251061"
---
# <a name="install-update-04-on-your-storsimple-virtual-array"></a>Installera uppdatering 0.4 på StorSimple Virtual Array

## <a name="overview"></a>Översikt

Den här artikeln beskriver de steg som krävs för att installera uppdatering 0.4 på StorSimple Virtual Array via det lokala webbgränssnittet eller via Azure portal. Du måste tillämpa uppdateringar eller snabbkorrigeringar att hålla din StorSimple Virtual Array uppdaterade. 

Tänk på att installera en uppdatering eller snabbkorrigering startar om enheten. Med hänsyn till att StorSimple Virtual Array är en enskild nod-enhet, alla i/o pågår avbryts och din enhet upplever driftstopp. 

Innan du installerar en uppdatering, rekommenderar vi att du vidtar volymer eller resurser offline på värden första och sedan enheten. Detta minskar risken för skadade data.

> [!IMPORTANT]
> Om du kör uppdatering 0.1 eller GA programvaruversioner, måste du använda metoden snabbkorrigering via det lokala webbgränssnittet för att installera uppdatering 0.3. Om du kör uppdatering 0.2 eller senare, rekommenderar vi att installerar du uppdateringar via Azure portal.
 

## <a name="use-the-local-web-ui"></a>Använd det lokala webbgränssnittet

Det finns två steg när du använder det lokala webbgränssnittet:

* Ladda ned uppdateringen eller snabbkorrigeringen
* Installera uppdateringen eller snabbkorrigeringen

### <a name="download-the-update-or-the-hotfix"></a>Ladda ned uppdateringen eller snabbkorrigeringen

Utför följande steg för att hämta programuppdateringen från Microsoft Update Catalog.

#### <a name="to-download-the-update-or-the-hotfix"></a>Ladda ned uppdateringen eller snabbkorrigeringen

1. Starta Internet Explorer och navigera till [ http://catalog.update.microsoft.com ](https://catalog.update.microsoft.com).

2. Om det här är första gången du använder Microsoft Update Catalog på den här datorn klickar du på **Installera** när du uppmanas att installera tillägget för Microsoft Update Catalog.

3. I sökrutan i Microsoft Update-katalogen, anger du numret för Knowledge Base (KB) för den snabbkorrigering som du vill hämta. Ange **3216577** för uppdatering 0.4 och klicka sedan på **Search**.
   
    I listan över snabbkorrigeringar visas, till exempel **StorSimple Virtual Array uppdatering 0.4**.
   
    ![Sökkatalog](./media/storsimple-virtual-array-install-update-04/download1.png)

4. Klicka på **Lägg till**. Uppdateringen läggs till i korgen.

5. Klicka på **View Basket** (Visa korg).

6. Klicka på **Hämta**. Ange eller **Bläddra** till en lokal plats där du vill att nedladdningarna ska läggas. Uppdateringarna hämtas till den angivna platsen och placeras i en undermapp med samma namn som uppdateringen. Mappen kan också kopieras till en nätverksresurs som kan nås från enheten.

7. Öppna den kopierade mappen, du bör se en paketfil för Microsoft Update fristående `WindowsTH-KB3011067-x64`. Den här filen används för att installera uppdatering eller snabbkorrigering.

### <a name="install-the-update-or-the-hotfix"></a>Installera uppdateringen eller snabbkorrigeringen

Kontrollera att du har uppdateringen eller snabbkorrigeringen ned antingen lokalt på värden eller komma åt via en nätverksresurs innan installationen av uppdateringen eller snabbkorrigeringen. 

Använd den här metoden för att installera uppdateringar på en enhet som kör GA eller uppdatera 0,1 programvaruversioner. Den här proceduren tar mindre än 2 minuter att slutföra. Utför följande steg för att installera uppdatering eller snabbkorrigering.

#### <a name="to-install-the-update-or-the-hotfix"></a>Att installera uppdateringen eller snabbkorrigeringen

1. I det lokala webbgränssnittet går du till **Underhåll** > **programuppdateringen**.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update/update1m.png)

2. I **uppdatering filsökväg**, ange filnamnet för uppdateringen eller snabbkorrigeringen. Du kan också bläddra till installationsfilen för uppdateringen eller snabbkorrigeringen om placeras på en nätverksresurs. Klicka på **Verkställ**.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update/update2m.png)

3. En varning visas. Beroende på det här är en enskild nod-enhet, när uppdateringen har tillämpats, enheten startas om och stilleståndstid. Klicka på kryssikonen.
   
   ![uppdatera enhet](./media/storsimple-virtual-array-install-update/update3m.png)

4. Uppdateringen startar. När enheten har uppdaterats, den startar om. Lokala Användargränssnittet är inte tillgänglig under den tiden.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update/update5m.png)

5. När omstarten är klar kommer du till den **logga in** sidan. Kontrollera att programmet har uppdaterats i det lokala webbgränssnittet, gå till **Underhåll** > **programuppdateringen**. Den visa programvaruversionen ska vara **10.0.0.0.0.10289.0** för uppdatering 0.4.
   
   > [!NOTE]
   > Vi rapporterar programvaruversionerna i ett något annorlunda sätt i det lokala webbgränssnittet och Azure-portalen. Exempelvis kan det lokala webbgränssnittet rapporterar **10.0.0.0.0.10289** och Azure portal rapporter **10.0.10289.0** för samma version.
   
    ![uppdatera enhet](./media/storsimple-virtual-array-install-update/update6m.png)

## <a name="use-the-azure-portal"></a>Använda Azure-portalen

Om du kör Update 0,2 och senare, rekommenderar vi att du installerar uppdateringar via Azure portal. Portalen proceduren kräver att användaren att skanna, ladda ned och installera uppdateringarna. Den här proceduren tar cirka 7 minuter för att slutföra. Utför följande steg för att installera uppdatering eller snabbkorrigering.

[!INCLUDE [storsimple-virtual-array-install-update-via-portal](../../includes/storsimple-virtual-array-install-update-via-portal-04.md)]

När installationen är klar (som visas jobbets status på 100%), går du till din StorSimple Device Manager-tjänsten. Välj **enheter** och markera och klicka på den enhet som du vill uppdatera listan med enheter som är anslutna till den här tjänsten. I den **inställningar** gå till bladet **hantera** och väljer **enhetsuppdateringar**. Den visa programvaruversionen ska vara **10.0.10289.0**.


## <a name="next-steps"></a>Nästa steg

Läs mer om [administrera StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

