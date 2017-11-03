---
title: "Spåra ändringar i dina virtuella Azure-datorer | Microsoft Docs"
description: "Använd ändringsspårning för att spåra ändringar i filer och registerposter på virtuella datorer."
services: automation
documentationcenter: automation
author: eslesar
manager: carmonm
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: automation
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: infrastructure
ms.date: 09/25/2017
ms.author: eslesar
ms.custom: 
ms.openlocfilehash: 5c6e8390ec8533fc7ab281c212e47a6982b30f1a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="track-changes-in-your-azure-virtual-machines"></a>Spåra ändringar i dina virtuella Azure-datorer

Genom att aktivera spårning av ändringar, kan du spåra ändringar av filer och registernycklar i Windows på virtuella datorer. Identifierar konfigurationsändringar kan hjälpa dig hitta operativa problem.

Du kan aktivera ändringsspårning direkt från din virtuella Azure-datorn.

Om du inte har en virtuell Azure-dator kan du skapa en genom att följa anvisningarna i den [Windows quickstart](../virtual-machines/windows/quick-create-portal.md) eller [Linux quickstart](../virtual-machines/linux/quick-create-portal.md) artikel.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal
Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="enable-change-tracking-for-an-azure-virtual-machine"></a>Aktivera ändringsspårning för en virtuell Azure-dator

1. I rutan till vänster på Azure Portal väljer du **Virtuella datorer**.
2. Välj en virtuell dator i listan.
3. I den virtuella datorn fönstret under **Operations**väljer **ändringsspårning**. 

   ![Publicera vm för ändringsspårning](./media/automation-vm-change-tracking/change-onboard-vm-blade.png)  
    Den **aktivera uppdateringshantering** öppnas.

    Verifieringen utförs för att fastställa om ändringsspårning är aktiverad för den virtuella datorn. Om ändringsspårning inte är aktiverad, visas en banderoll, och du får välja att aktivera lösningen.

   ![Ändra spårning publicera configuration banderoll](./media/automation-vm-change-tracking/change-onboard-banner.png)

4. Markera banderollen för att aktivera lösningen. Om du inte har följande objekt läggs de automatiskt:

   * [Logga Analytics](../log-analytics/log-analytics-overview.md) arbetsytan
   * [Automation](../automation/automation-offering-get-started.md) konto

5. Välj ett logganalys arbetsutrymme att lagra dataloggar från ändringsspårning, Välj ett Automation-konto för att spåra ändringar och välj sedan **aktivera**.  
    Ett statusfält meddelar dig att lösningen aktiveras. Processen kan ta upp till 15 minuter.

## <a name="configure-change-tracking"></a>Konfigurera spårning av ändringar

När ändringsspårning har aktiverats kan den **ändringsspårning** visas. 

Om du vill välja vilka filer och registernycklar för att spåra, Välj **redigera inställningar för**.

   ![Redigera inställningar för spårning av ändringar](./media/automation-vm-change-tracking/change-edit-settings.png)

   Den **arbetsytan Configuration** öppnas. 

I den **arbetsytan Configuration** fönstret Lägg till registernycklar i Windows, Windows-filer eller Linux-filer som ska spåras, som beskrivs i följande tre avsnitt.

### <a name="add-a-windows-registry-key"></a>Lägger till en Windows-registernyckeln

1. På den **Windows-registret** väljer **Lägg till**.  
    Den **lägga till Windows-registret för ändringsspårning** öppnas.

   ![Ändringsspårning lägga till registret](./media/automation-vm-change-tracking/change-add-registry.png)

2. Under **aktiverad**väljer **SANT**.
3. I den **objektnamnet** anger du ett eget namn.
4. (Valfritt) I den **grupp** ange ett gruppnamn.
5. I den **Windows-registernyckeln** anger du namnet på den registernyckel som du vill spåra.
6. Välj **Spara**.

### <a name="add-a-windows-file"></a>Lägga till en Windows-fil

1. På den **Windows-filer** väljer **Lägg till**.  
    Den **lägga till Windows-filen för ändringsspårning** öppnas.

   ![Ändringsspårning lägga till Windows-filen](./media/automation-vm-change-tracking/change-add-win-file.png)

2. Under **aktiverad**väljer **SANT**.
3. I den **objektnamnet** anger du ett eget namn.
4. (Valfritt) I den **grupp** ange ett gruppnamn.
5. I den **ange sökvägen** ange det fullständiga sökväg och namnet på filen som du vill spåra.
6. Välj **Spara**.

### <a name="add-a-linux-file"></a>Lägga till en Linux-fil

1. På den **Linux filer** väljer **Lägg till**.  
    Den **Lägg till Linux-fil för ändringsspårning** öppnas.

   ![Ändringsspårning lägga till Linux-fil](./media/automation-vm-change-tracking/change-add-linux-file.png)

2. Under **aktiverad**väljer **SANT**.
3. I den **objektnamnet** anger du ett eget namn.
4. (Valfritt) I den **grupp** ange ett gruppnamn.
5. I den **ange sökvägen** ange det fullständiga sökväg och namnet på filen som du vill spåra.
6. I den **Sökvägstyp** väljer du antingen **filen** eller **Directory**.
7. Under **rekursion**, för att spåra ändringar för den angivna sökvägen och alla filer och mappar under den, markerar du **på**. Om du vill spåra bara den valda sökvägen eller välj **av**.
8. Under **Använd Sudo**, för att spåra filer som behöver den `sudo` kommandot för att få åtkomst till, Välj **på**. Annars väljer **av**.
9. Välj **Spara**.

## <a name="view-changes"></a>Visa ändringar

I den **ändringsspårning** och du kan visa ändringarna i var och en av de olika kategorierna för din virtuella dator över tid.

   ![Ändringsspårning lägga till vyn förändringar](./media/automation-vm-change-tracking/change-view-changes.png)

Du kan välja kategorier och tidsintervall för att visa. Längst upp i fönstret kan du visa en grafisk representation av ändringar över tid. Längst ned i fönstret kan du visa en lista över nyligen gjorda ändringar.

## <a name="view-change-tracking-log-data"></a>Visa ändringsspårning loggdata

Ändringsspårning genererar loggdata som skickas till logganalys. Om du vill söka i loggarna genom att köra frågor, Välj **logganalys** överst i den **ändringsspårning** fönster.

   ![Ändringsspårning logganalys](./media/automation-vm-change-tracking/change-log-analytics.png)

Mer information om hur du kör och söker loggfiler i logganalys finns [logganalys](../log-analytics/log-analytics-overview.md).

## <a name="next-steps"></a>Nästa steg

* Läs mer om spårning av ändringar i [spåra ändringar av programvaran i din miljö med lösningen för att ändra spårning](../log-analytics/log-analytics-change-tracking.md).
* Läs mer om att hantera uppdateringar för dina virtuella datorer i [uppdatering hanteringslösning i OMS](../operations-management-suite/oms-solution-update-management.md).
