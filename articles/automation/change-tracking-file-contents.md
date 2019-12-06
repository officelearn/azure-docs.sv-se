---
title: Visa ändringar i fil innehåll med Azure Automation
description: Använd funktionen ändring av fil innehåll i ändrings spårning för att visa innehållet i en fil som har ändrats.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 07/03/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4ab88aa2dc604172f00d875353dabba61fd101af
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850592"
---
# <a name="view-contents-of-a-file-that-is-being-tracked-with-change-tracking"></a>Visa innehållet i en fil som spåras med Ändringsspårning

Med fil innehålls spårning kan du visa innehållet i en fil före och efter en ändring som spåras med Ändringsspårning. Det gör du genom att spara fil innehållet till ett lagrings konto när varje ändring sker.

## <a name="requirements"></a>Krav

* Ett standard lagrings konto som använder distributions modellen för Resource Manager krävs för att lagra fil innehåll. Lagrings konton för Premium och klassisk distributions modell ska inte användas. Mer information om lagrings konton finns i [om Azure Storage-konton](../storage/common/storage-create-storage-account.md)

* Det lagrings konto som används kan bara ha ett Automation-konto anslutet.

* [Ändringsspårning](automation-change-tracking.md) är aktiverat i ditt Automation-konto.

## <a name="enable-file-content-tracking"></a>Aktivera spårning av fil innehåll

1. I Azure Portal öppnar du ditt Automation-konto och väljer sedan **ändrings spårning**.
2. På den översta menyn väljer du **Redigera inställningar**.
3. Välj **fil innehåll** och klicka på **Länka**. Då öppnas fönstret **Lägg till innehålls plats för ändringsspårning** .

   ![Aktivera](./media/change-tracking-file-contents/enable.png)

4. Välj den prenumeration och det lagrings konto som du vill använda för att lagra filens innehåll. Om du vill aktivera spårning av fil innehåll för alla befintliga spårade filer väljer du **på** för att **Ladda upp fil innehåll för alla inställningar**. Du kan ändra detta för varje fil Sök väg efteråt.

   ![Ange lagrings konto](./media/change-tracking-file-contents/storage-account.png)

5. När den är aktive rad visas lagrings kontot och SAS-URI: er. SAS-URI: er upphör att gälla efter 365 dagar och kan återskapas genom att klicka på knappen **skapa** igen.

   ![lista konto nycklar](./media/change-tracking-file-contents/account-keys.png)

## <a name="add-a-file"></a>Lägg till en fil

Följande steg beskriver hur du aktiverar ändrings spårning för en fil:

1. På sidan **Redigera inställningar** i **ändringsspårning**väljer du fliken **Windows-filer** eller **Linux-filer** och klickar på **Lägg till**

1. Fyll i informationen för fil Sök vägen och välj **Sant** under **överför fil innehåll för alla inställningar**. Med den här inställningen aktive ras fil innehålls spårning endast för fil Sök vägen.

   ![Lägg till en Linux-fil](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="viewing-the-contents-of-a-tracked-file"></a>Visa innehållet i en spårad fil

1. När en ändring har identifierats för filen eller en fil i sökvägen visas den i portalen. Välj fil ändringen i listan över ändringar. Fönstret **ändrings information** visas.

   ![lista ändringar](./media/change-tracking-file-contents/change-list.png)

1. På sidan **ändrings information** ser du filen standard före och efter fil information. i det övre vänstra hörnet klickar du på **Visa fil innehåll ändringar** för att se innehållet i filen.

   ![Ändra information](./media/change-tracking-file-contents/change-details.png)

1. På den nya sidan visas fil innehållet i en sida-vid-sida-vy. Du kan också välja **infogat** för att visa en infogad vy över ändringarna.

   ![Visa fil ändringar](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder lösningen finns i kursen om Ändringsspårning:

> [!div class="nextstepaction"]
> [Felsöka ändringar i din miljö](automation-tutorial-troubleshoot-changes.md)

* Använd [loggs ökningar i Azure Monitor loggar](../log-analytics/log-analytics-log-searches.md) om du vill visa detaljerade ändrings spårnings data.

