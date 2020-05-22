---
title: Aktivera Azure Automation Uppdateringshantering från Automation-konto
description: Den här artikeln beskriver hur du aktiverar Uppdateringshantering från ett Automation-konto.
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: a521ff690f59b6beafd1113b177b43193dc7447e
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/21/2020
ms.locfileid: "83743991"
---
# <a name="enable-update-management-from-an-automation-account"></a>Aktivera Uppdateringshantering från ett Automation-konto

I den här artikeln beskrivs hur du kan använda ditt Automation-konto för att aktivera [uppdateringshantering](automation-update-management.md) funktionen för virtuella datorer i din miljö. Om du vill aktivera virtuella Azure-datorer i stor skala måste du aktivera en befintlig virtuell dator med hjälp av Uppdateringshantering. 

> [!NOTE]
> När du aktiverar Uppdateringshantering, stöds bara vissa regioner för att länka en Log Analytics arbets yta och ett Automation-konto. En lista över mappnings par som stöds finns i [region mappning för Automation-konto och Log Analytics-arbetsyta](how-to/region-mappings.md).

## <a name="prerequisites"></a>Krav

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-konto](automation-offering-get-started.md) för att hantera datorer.
* En [virtuell dator](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure på https://portal.azure.com .

## <a name="enable-update-management"></a>Aktivera uppdateringshantering

1. I ditt Automation-konto väljer du **uppdaterings hantering** under **uppdaterings hantering**.

2. Välj Log Analytics arbets yta och Automation-konto och klicka på **Aktivera** för att aktivera uppdateringshantering. Det tar upp till 15 minuter att slutföra installationen.

    ![Aktivera uppdateringshantering](media/automation-onboard-solutions-from-automation-account/onboardsolutions2.png)

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Kontrol lera omfattnings konfigurationen

Uppdateringshantering använder en omfattnings konfiguration i arbets ytan för att rikta in de datorer som ska aktive ras för funktionen. Omfattnings konfigurationen är en grupp med en eller flera sparade sökningar som används för att begränsa omfånget för funktionen till vissa datorer. Mer information finns i [arbeta med scope-konfigurationer för uppdateringshantering](automation-scope-configurations-update-management.md).

## <a name="enable-azure-vms"></a>Aktivera virtuella Azure-datorer

1. Från ditt Automation-konto väljer du **uppdaterings hantering** under **uppdaterings hantering**.

2. Klicka på **+ Lägg till virtuella Azure-datorer** och välj en eller flera virtuella datorer i listan. Virtuella datorer som inte kan aktive ras är nedtonade och kan inte väljas. Virtuella Azure-datorer kan finnas i vilken region som helst, oavsett platsen för ditt Automation-konto. 

3. Klicka på **Aktivera** för att lägga till de valda virtuella datorerna i dator gruppens sparade sökning efter funktionen.

    ![Aktivera virtuella Azure-datorer](media/automation-onboard-solutions-from-automation-account/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Aktivera virtuella datorer som inte är från Azure

Datorer som inte i Azure måste läggas till manuellt. 

1. Från ditt Automation-konto väljer du **uppdaterings hantering** under **uppdaterings hantering**.

2. Klicka på **Lägg till icke-Azure-dator**. Den här åtgärden öppnar ett nytt webbläsarfönster med [instruktioner för att installera och konfigurera Log Analytics agent för Windows](../azure-monitor/platform/log-analytics-agent.md) så att datorn kan börja rapportera uppdateringshantering åtgärder. Om du aktiverar en dator som för närvarande hanteras av Operations Manager krävs inte en ny agent och informationen i arbets ytan anges i den befintliga agenten.

## <a name="enable-machines-in-the-workspace"></a>Aktivera datorer i arbets ytan

Manuellt installerade datorer eller datorer som redan rapporterar till din arbets yta måste läggas till Azure Automation för att Uppdateringshantering ska kunna aktive ras. 

1. Från ditt Automation-konto väljer du **uppdaterings hantering** under **uppdaterings hantering**.

2. Välj **hantera datorer**. Knappen **hantera datorer** kan vara nedtonad om du tidigare har valt alternativet **Aktivera på alla tillgängliga och framtida datorer**

    ![Sparade sökningar](media/automation-onboard-solutions-from-automation-account/managemachines.png)

4. Om du vill aktivera Uppdateringshantering för alla tillgängliga datorer väljer du **Aktivera på alla tillgängliga datorer** på sidan hantera datorer. Den här åtgärden inaktiverar kontrollen för att lägga till datorer individuellt. Den här uppgiften lägger till alla namn på de datorer som rapporterar till arbets ytan till den dator grupp som sparade Sök frågan. När det här alternativet är markerat inaktive ras knappen **hantera datorer** .

5. Om du vill aktivera funktionen för alla tillgängliga datorer och framtida datorer väljer du **Aktivera på alla tillgängliga och framtida datorer**. Det här alternativet tar bort sparade sökningar och omfattnings konfiguration från arbets ytan och öppnar funktionen för alla Azure-och icke-Azure-datorer som rapporterar till arbets ytan. När det här alternativet är markerat inaktive ras knappen **hantera datorer** permanent, eftersom det inte finns någon omfattnings konfiguration kvar.

6. Om det behövs kan du lägga till omfångs konfigurationerna genom att lägga till de första sparade sökningarna igen. Mer information finns i [arbeta med scope-konfigurationer för uppdateringshantering](automation-scope-configurations-update-management.md).

7. Om du vill aktivera funktionen för en eller flera datorer väljer du **Aktivera på valda datorer** och klickar på **Lägg till** bredvid varje dator för att aktivera funktionen. Den här uppgiften lägger till de valda dator namnen i den dator grupp sparade Sök frågan för funktionen.

## <a name="next-steps"></a>Nästa steg

* Om du vill använda Uppdateringshantering för virtuella datorer läser du [Hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer](automation-tutorial-update-management.md).
* För konfiguration av omfattningar, se [arbeta med scope-konfigurationer för uppdateringshantering](automation-scope-configurations-update-management.md).
* Om du inte längre behöver Log Analytics arbets ytan går du till anvisningar i [ta bort länk till arbets yta från Automation-konto för uppdateringshantering](automation-unlink-workspace-update-management.md).
* Om du vill ta bort virtuella datorer från Uppdateringshantering, se [ta bort virtuella datorer från uppdateringshantering](automation-remove-vms-from-update-management.md).
* Information om hur du felsöker allmänna Uppdateringshantering fel finns i [felsöka uppdateringshantering problem](troubleshoot/update-management.md).
* Information om hur du felsöker problem med Windows Update-agenten finns i [Felsöka problem med Windows Update-agenten](troubleshoot/update-agent-issues.md).
* Information om hur du felsöker problem med Linux Update-agenten finns i[Felsöka problem med Linux Update Agent](troubleshoot/update-agent-issues-linux.md).
