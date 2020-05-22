---
title: Aktivera Azure Automation Ändringsspårning och inventering från en virtuell Azure-dator
description: Den här artikeln beskriver hur du aktiverar Ändringsspårning och inventering från en virtuell Azure-dator.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 4eccdef6bd3f2bfcd0eced8281f7b998536f22a9
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83749188"
---
# <a name="enable-change-tracking-and-inventory-from-an-azure-vm"></a>Aktivera Ändringsspårning och inventering från en virtuell Azure-dator

Den här artikeln beskriver hur du kan använda en virtuell Azure-dator för att aktivera [ändringsspårning-och inventerings](change-tracking.md) funktionen på andra datorer. Om du vill aktivera virtuella Azure-datorer i stor skala måste du aktivera en befintlig virtuell dator med hjälp av Ändringsspårning och inventering. 

> [!NOTE]
> När du aktiverar Ändringsspårning och inventering, stöds bara vissa regioner för att länka en Log Analytics arbets yta och ett Automation-konto. En lista över mappnings par som stöds finns i [region mappning för Automation-konto och Log Analytics-arbetsyta](how-to/region-mappings.md).

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-konto](automation-offering-get-started.md) för att hantera datorer.
* En [virtuell dator](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Aktivera Ändringsspårning och inventering

1. I [Azure Portal](https://portal.azure.com)väljer du **virtuella datorer** eller söker efter och väljer **virtuella datorer** från start sidan.

2. Välj den virtuella dator som du vill aktivera Ändringsspårning och inventering för. Virtuella datorer kan finnas i vilken region som helst, oavsett platsen för ditt Automation-konto.

3. På sidan VM väljer du antingen **inventering** eller **ändrings spårning** under **konfigurations hantering**.

4. Du måste ha `Microsoft.OperationalInsights/workspaces/read` behörighet för att avgöra om den virtuella datorn är aktive rad för en arbets yta. Mer information om ytterligare behörigheter som krävs finns i [funktions installations behörigheter](automation-role-based-access-control.md#feature-setup-permissions). Information om hur du aktiverar flera datorer samtidigt finns i [aktivera ändringsspårning och inventering från ett Automation-konto](automation-enable-changes-from-auto-acct.md).

5. Välj Log Analytics arbets yta och Automation-konto och klicka på **Aktivera** för att aktivera ändringsspårning och inventering för den virtuella datorn. Det tar upp till 15 minuter att slutföra installationen. 

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Kontrol lera omfattnings konfigurationen

Ändringsspårning och inventering använder en omfattnings konfiguration i arbets ytan för att rikta in de datorer som ska aktive ras för funktionen. Omfattnings konfigurationen är en grupp med en eller flera sparade sökningar som används för att begränsa omfånget för funktionen till vissa datorer. Mer information finns i [arbeta med scope-konfigurationer för ändringsspårning och inventering](automation-scope-configurations-change-tracking.md).

## <a name="next-steps"></a>Nästa steg

* [Hantera Ändringsspårning och inventering](change-tracking-file-contents.md)
* [Arbeta med scope-konfigurationer för Ändringsspårning och inventering](automation-scope-configurations-change-tracking.md)
* [Identifiera programvara på en virtuell dator](automation-tutorial-installed-software.md)
* [Ta bort länken mellan arbets ytan och automation-kontot för Ändringsspårning och inventering](automation-unlink-workspace-change-tracking.md)
* [Ta bort virtuella datorer från Ändringsspårning och inventering](automation-remove-vms-from-change-tracking.md)
* [Felsöka ändringar på en virtuell Azure-dator](automation-tutorial-troubleshoot-changes.md)
* [Felsöka Ändringsspårning-och inventerings problem](troubleshoot/change-tracking.md)
