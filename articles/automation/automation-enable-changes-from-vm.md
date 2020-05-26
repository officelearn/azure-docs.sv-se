---
title: Aktivera Azure Automation Ändringsspårning och inventering från en virtuell Azure-dator
description: Den här artikeln beskriver hur du aktiverar Ändringsspårning och inventering från en virtuell Azure-dator.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 5379f2c46bbeaba4ee8509603b7b739b75d08f04
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836795"
---
# <a name="enable-change-tracking-and-inventory-from-an-azure-vm"></a>Aktivera Ändringsspårning och inventering från en virtuell Azure-dator

Den här artikeln beskriver hur du kan använda en virtuell Azure-dator för att aktivera [ändringsspårning-och inventerings](change-tracking.md) funktionen på andra datorer. Om du vill aktivera virtuella Azure-datorer i stor skala måste du aktivera en befintlig virtuell dator med hjälp av Ändringsspårning och inventering. 

> [!NOTE]
> När du aktiverar Ändringsspårning och inventering, stöds bara vissa regioner för att länka en Log Analytics arbets yta och ett Automation-konto. En lista över mappnings par som stöds finns i [region mappning för Automation-konto och Log Analytics-arbetsyta](how-to/region-mappings.md).

## <a name="prerequisites"></a>Förutsättningar

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

* Information om hur du arbetar med funktionen finns i [hantera ändringsspårning och inventering](change-tracking-file-contents.md).
* Information om konfiguration av omfattningar finns i [arbeta med scope-konfigurationer för ändringsspårning och inventering](automation-scope-configurations-change-tracking.md).
* Information om hur du använder funktionen för att identifiera program vara som är installerad i din miljö finns i [identifiera vilken program vara som är installerad på dina virtuella datorer](automation-tutorial-installed-software.md).
* Om du inte vill integrera ditt Automation-konto med en Log Analytics arbets yta när du aktiverar funktionen, se [ta bort länken till arbets ytan från Automation-kontot](automation-unlink-workspace-change-tracking.md).
* När du har distribuerat ändringar av virtuella datorer kan du ta bort dem enligt beskrivningen i [ta bort virtuella datorer från ändringsspårning och inventering](automation-remove-vms-from-change-tracking.md).
* Information om hur du felsöker allmänna problem med funktionen finns i [felsöka ändringsspårning-och inventerings problem](troubleshoot/change-tracking.md).
