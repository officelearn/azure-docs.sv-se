---
title: Visa ändringar i filinnehåll med Azure Automation
description: Använd funktionen filen innehållsändringar i ändringsspårning för att visa innehållet i en fil som har ändrats.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: georgewallace
ms.author: gwallace
ms.date: 07/03/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f974d446337b8c962d60be9aab964c6d2ebb535b
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56821079"
---
# <a name="view-contents-of-a-file-that-is-being-tracked-with-change-tracking"></a>Visa innehållet i en fil som spåras med ändringsspårning

Filspårning innehåll kan du visa innehållet i en fil före och efter en ändring som spåras med ändringsspårning. Om du vill göra detta måste sparas i filens innehåll till ett lagringskonto efter varje ändring sker.

## <a name="requirements"></a>Krav

* Ett standardlagringskonto med hjälp av Resource Manager-distributionsmodellen krävs för att lagra innehåll. Premium- och klassisk distribution modellen storage-konton ska inte användas. Mer information om lagringskonton finns i [om Azure storage-konton](../storage/common/storage-create-storage-account.md)

* Lagringskontot används kan bara ha 1 Automation-konto som är anslutna.

* [Ändringsspårning](automation-change-tracking.md) är aktiverat i ditt Automation-konto.

## <a name="enable-file-content-tracking"></a>Aktivera innehåll filspårning

1. Öppna ditt Automation-konto i Azure-portalen och välj sedan **ändringsspårning**.
2. På menyn högst upp väljer **redigera inställningar för**.
3. Välj **filinnehåll** och klicka på **länk**. Då öppnas det **Lägg till innehållsplats för ändringsspårning** fönstret.

   ![aktivera](./media/change-tracking-file-contents/enable.png)

4. Välj prenumeration och storage-konto du använder för att lagra innehållet i filen till. Om du vill aktivera innehåll filspårning för alla befintliga spårade filer väljer **på** för **ladda upp innehåll för alla inställningar**. Du kan därefter ändra detta för varje sökväg.

   ![Ange storage-konto](./media/change-tracking-file-contents/storage-account.png)

5. När du har aktiverat, visas lagringskontot och SAS-URI: er. SAS-URI: er upphör att gälla efter 365 dagar och kan skapas på nytt genom att klicka på den **återskapa** knappen.

   ![Lista nycklar](./media/change-tracking-file-contents/account-keys.png)

## <a name="add-a-file"></a>Lägg till en fil

I följande steg vägleder dig genom att aktivera ändringsspårning för en fil:

1. På den **redigera inställningar för** sidan **ändringsspårning**, väljer du antingen **Windows filer** eller **Linux-filer** och på  **Lägg till**

1. Fyll i informationen för sökvägen till filen och välj **SANT** under **ladda upp innehåll för alla inställningar**. Den här inställningen aktiverar filinnehåll spårning för den filsökvägen.

   ![Lägg till en linux-fil](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="viewing-the-contents-of-a-tracked-file"></a>Visa innehållet i en spårade fil

1. När en ändring har upptäckts för fil eller en fil i sökvägen, visas den i portalen. Välj filen ändras från en lista över ändringar. Den **ändringsinformation** egenskapsruta visas.

   ![Lista ändringar](./media/change-tracking-file-contents/change-list.png)

1. På den **ändringsinformation** kan du se standard före och efter filen information längst upp till vänster, klickar på **visa ändringar i filinnehåll** att se innehållet i filen.

  ![Information om ändringar](./media/change-tracking-file-contents/change-details.png)

1. Den nya sidan visar filinnehållet i en sida-vid-sida-vy. Du kan också välja **infogade** att visa ändringarna infogade.

  ![Visa filändringar](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="next-steps"></a>Nästa steg

Gå kursen på ändringsspårning vill veta mer om hur du använder lösningen:

> [!div class="nextstepaction"]
> [Felsöka ändringar i din miljö](automation-tutorial-troubleshoot-changes.md)

* Använd [Loggsökningar i Azure Monitor-loggar](../log-analytics/log-analytics-log-searches.md) att visa detaljerad spårning av data.

