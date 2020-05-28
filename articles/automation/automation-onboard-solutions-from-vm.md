---
title: Aktivera Azure Automation Uppdateringshantering från en virtuell Azure-dator
description: Den här artikeln beskriver hur du aktiverar Uppdateringshantering från en virtuell Azure-dator.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 254ff2906d2b80b6c34ba930daffbb2c65f2b22f
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117931"
---
# <a name="enable-update-management-from-an-azure-vm"></a>Aktivera Uppdateringshantering från en virtuell Azure-dator

I den här artikeln beskrivs hur du kan använda en virtuell Azure-dator för att aktivera [uppdateringshantering](automation-update-management.md) -funktionen på andra datorer. Om du vill aktivera virtuella Azure-datorer i stor skala måste du aktivera en befintlig virtuell dator med hjälp av Uppdateringshantering. 

> [!NOTE]
> När du aktiverar Uppdateringshantering, stöds bara vissa regioner för att länka en Log Analytics arbets yta och ett Automation-konto. En lista över mappnings par som stöds finns i [region mappning för Automation-konto och Log Analytics-arbetsyta](how-to/region-mappings.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-konto](automation-offering-get-started.md) för att hantera datorer.
* En [virtuell dator](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure Portal på https://portal.azure.com.

## <a name="enable-update-management"></a>Aktivera uppdateringshantering

1. I [Azure Portal](https://portal.azure.com)väljer du **virtuella datorer** eller söker efter och väljer **virtuella datorer** från start sidan.

2. Välj den virtuella dator som du vill aktivera Uppdateringshantering för. Virtuella datorer kan finnas i vilken region som helst, oavsett platsen för ditt Automation-konto. Du

3. På sidan virtuell dator under **åtgärder**väljer du **uppdateringshantering**.

4. Du måste ha `Microsoft.OperationalInsights/workspaces/read` behörighet för att avgöra om den virtuella datorn är aktive rad för en arbets yta. Mer information om ytterligare behörigheter som krävs finns i [behörigheter som krävs för att aktivera datorer](automation-role-based-access-control.md#feature-setup-permissions). Information om hur du aktiverar flera datorer samtidigt finns i [aktivera uppdateringshantering från ett Automation-konto](automation-onboard-solutions-from-automation-account.md).

5. Välj Log Analytics arbets yta och Automation-konto och klicka på **Aktivera** för att aktivera uppdateringshantering. Det tar upp till 15 minuter att slutföra installationen. 

    ![Aktivera uppdateringshantering](media/automation-tutorial-update-management/manageupdates-update-enable.png)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>Begränsa omfånget för distributionen

Uppdateringshantering använder en omfattnings konfiguration i arbets ytan för att rikta in de datorer som ska ta emot uppdateringar. Mer information finns i [begränsa uppdateringshantering distributions omfång](automation-scope-configurations-update-management.md).

## <a name="next-steps"></a>Nästa steg

* Om du vill använda Uppdateringshantering för virtuella datorer läser du [Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer](automation-tutorial-update-management.md).
* För konfiguration av omfattningar, se [begränsa uppdateringshantering distributions omfång](automation-scope-configurations-update-management.md).
* Om du inte längre behöver Log Analytics arbets ytan går du till anvisningar i [ta bort länk till arbets yta från Automation-konto för uppdateringshantering](automation-unlink-workspace-update-management.md).
* Om du vill ta bort virtuella datorer från Uppdateringshantering, se [ta bort virtuella datorer från uppdateringshantering](automation-remove-vms-from-update-management.md).
* Information om hur du felsöker allmänna Uppdateringshantering fel finns i [felsöka uppdateringshantering problem](troubleshoot/update-management.md).
* Information om hur du felsöker problem med Windows Update-agenten finns i [Felsöka problem med Windows Update-agenten](troubleshoot/update-agent-issues.md).
* Information om hur du felsöker problem med Linux Update-agenten finns i[Felsöka problem med Linux Update Agent](troubleshoot/update-agent-issues-linux.md).
