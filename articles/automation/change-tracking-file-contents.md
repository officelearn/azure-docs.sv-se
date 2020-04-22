---
title: Visa ändringar av filinnehåll med Azure Automation
description: Använd funktionen för ändringsspårning av filinnehåll för att visa innehållet i en fil som har ändrats.
services: automation
ms.subservice: change-inventory-management
ms.date: 07/03/2018
ms.topic: conceptual
ms.openlocfilehash: 57c3c2c7a0c923921c727ccea7839940457bc1ee
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682998"
---
# <a name="view-contents-of-a-file-that-is-being-tracked-with-change-tracking"></a>Visa innehållet i en fil som spåras med ändringsspårning

Med filinnehållsspårning kan du visa innehållet i en fil före och efter en ändring som spåras med ändringsspårning. För att göra detta sparas filinnehållet i ett lagringskonto när varje ändring har inträffat.

## <a name="requirements"></a>Krav

* Ett standardlagringskonto med resurshanterarens distributionsmodell krävs för att lagra filinnehåll. Lagringskonton för premium- och klassiska distributionsmodellmodeller bör inte användas. Mer information om lagringskonton finns i [Om Azure-lagringskonton](../storage/common/storage-create-storage-account.md)

* Det lagringskonto som används kan bara ha 1 Automation-konto anslutet.

* [Ändringsspårning](automation-change-tracking.md) är aktiverat i ditt Automation-konto.

## <a name="enable-file-content-tracking"></a>Aktivera spårning av filinnehåll

1. Öppna ditt Automation-konto i Azure-portalen och välj sedan **Ändra spårning**.
2. Välj Redigera inställningar **på**den övre menyn .
3. Välj **Filinnehåll** och klicka på **Länk**. Då öppnas fönstret **Lägg till innehållsplats för ändringsspårning.**

   ![Aktivera](./media/change-tracking-file-contents/enable.png)

4. Välj det prenumerations- och lagringskonto som ska användas för att lagra filinnehållet till. Om du vill aktivera filinnehållsspårning för alla befintliga spårade filer väljer du **På** **för uppladdning av filinnehåll för alla inställningar**. Du kan ändra detta för varje filsökväg efteråt.

   ![ange lagringskonto](./media/change-tracking-file-contents/storage-account.png)

5. När lagringskontot och SAS Uris har aktiverats visas det. SAS Uris upphör att gälla efter 365 dagar och kan återskapas genom att klicka på knappen **Återskapa.**

   ![lista kontonycklar](./media/change-tracking-file-contents/account-keys.png)

## <a name="add-a-file"></a>Lägga till en fil

Följande steg går igenom att aktivera ändringsspårning för en fil:

1. På sidan Redigera inställningar i **Ändra spårning**väljer du antingen fliken **Windows-filer** eller **Linux-filer** och klickar på **Lägg till**

1. Fyll i informationen för filsökvägen och välj Sant under **Ladda upp filinnehåll för alla inställningar**. Den här inställningen aktiverar endast filinnehållsspårning för den filsökvägen.

   ![lägga till en Linux-fil](./media/change-tracking-file-contents/add-linux-file.png)

## <a name="viewing-the-contents-of-a-tracked-file"></a>Visa innehållet i en spårad fil

1. När en ändring har upptäckts för filen eller en fil i sökvägen visas den i portalen. Markera filändringen i listan över ändringar. Fönstret Ändra information visas.

   ![liständringar](./media/change-tracking-file-contents/change-list.png)

1. I fönstret Ändra information visas standardinformationen före och efter fil. Välj Visa ändringar av **filinnehåll** om du vill visa innehållet i filen.

   ![ändra information](./media/change-tracking-file-contents/change-details.png)

1. På den nya sidan visas filinnehållet i en sida vid sida-vy. Du kan också välja **Infogad** om du vill visa en infogad vy över ändringarna.

   ![visa filändringar](./media/change-tracking-file-contents/view-file-changes.png)

## <a name="next-steps"></a>Nästa steg

Besök självstudien om ändringsspårning om du vill veta mer om hur du använder lösningen:

> [!div class="nextstepaction"]
> [Felsöka ändringar i miljön](automation-tutorial-troubleshoot-changes.md)

* Använd [loggsökningar i Azure Monitor-loggar](../log-analytics/log-analytics-log-searches.md) för att visa detaljerade ändringsspårningsdata.

