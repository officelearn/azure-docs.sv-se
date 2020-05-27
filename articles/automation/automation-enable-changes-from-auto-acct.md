---
title: Aktivera Azure Automation Ändringsspårning och inventering från Automation-konto
description: Den här artikeln beskriver hur du aktiverar Ändringsspårning och inventering från ett Automation-konto.
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 28c61e637a37c158dcd80c0f02b748b4813945fb
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/25/2020
ms.locfileid: "83826850"
---
# <a name="enable-change-tracking-and-inventory-from-an-automation-account"></a>Aktivera Ändringsspårning och inventering från ett Automation-konto

Den här artikeln beskriver hur du kan använda ditt Automation-konto för att aktivera [ändringsspårning-och inventerings](change-tracking.md) funktionen för virtuella datorer i din miljö. Om du vill aktivera virtuella Azure-datorer i stor skala måste du aktivera en befintlig virtuell dator med hjälp av Ändringsspårning och inventering. 

> [!NOTE]
> När du aktiverar Ändringsspårning och inventering, stöds bara vissa regioner för att länka en Log Analytics arbets yta och ett Automation-konto. En lista över mappnings par som stöds finns i [region mappning för Automation-konto och Log Analytics-arbetsyta](how-to/region-mappings.md).

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-konto](automation-offering-get-started.md) för att hantera datorer.
* En [virtuell dator](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure på https://portal.azure.com .

## <a name="enable-change-tracking-and-inventory"></a>Aktivera Ändringsspårning och inventering

1. Navigera till ditt Automation-konto och välj antingen **inventering** eller **ändrings spårning** under **konfigurations hantering**.

2. Välj Log Analytics arbets yta och Automation-konto och klicka på **Aktivera** för att aktivera ändringsspårning och inventering. Det tar upp till 15 minuter att slutföra installationen.

    ![Aktivera Ändringsspårning och inventering](media/automation-enable-changes-from-auto-acct/onboardsolutions.png)

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Kontrol lera omfattnings konfigurationen

Ändringsspårning och inventering använder en omfattnings konfiguration i arbets ytan för att rikta in de datorer som ska ta emot ändringar. Omfattnings konfigurationen är en grupp med en eller flera sparade sökningar som används för att begränsa omfånget för funktionen till vissa datorer. Mer information finns i [arbeta med scope-konfigurationer för ändringsspårning och inventering](automation-scope-configurations-change-tracking.md).

## <a name="enable-azure-vms"></a>Aktivera virtuella Azure-datorer

1. Från ditt Automation-konto väljer du **inventering** eller **ändrings spårning** under **konfigurations hantering**.

2. Klicka på **+ Lägg till virtuella Azure-datorer** och välj en eller flera virtuella datorer i listan. Virtuella datorer som inte kan aktive ras är nedtonade och kan inte väljas. Virtuella Azure-datorer kan finnas i vilken region som helst, oavsett platsen för ditt Automation-konto. 

3. Klicka på **Aktivera** för att lägga till de valda virtuella datorerna i dator gruppens sparade sökning efter funktionen. Mer information finns i [arbeta med scope-konfigurationer för ändringsspårning och inventering](automation-scope-configurations-change-tracking.md).

    ![Aktivera virtuella Azure-datorer](media/automation-enable-changes-from-auto-acct/enable-azure-vms.png)

## <a name="enable-non-azure-vms"></a>Aktivera virtuella datorer som inte är från Azure

Datorer som inte i Azure måste läggas till manuellt. 

1. Från ditt Automation-konto väljer du **inventering** eller **ändrings spårning** under **konfigurations hantering**.

2. Klicka på **Lägg till icke-Azure-dator**. Den här åtgärden öppnar ett nytt webbläsarfönster med [instruktioner för att installera och konfigurera Log Analytics agent för Windows](../azure-monitor/platform/log-analytics-agent.md) så att datorn kan börja rapportera ändringsspårning-och inventerings åtgärder. Om du aktiverar en dator som för närvarande hanteras av Operations Manager krävs inte en ny agent och informationen i arbets ytan anges i den befintliga agenten.

## <a name="enable-machines-in-the-workspace"></a>Aktivera datorer i arbets ytan

Manuellt installerade datorer eller datorer som redan rapporterar till din arbets yta måste läggas till Azure Automation för att Ändringsspårning och inventering ska kunna aktive ras. 

1. Från ditt Automation-konto väljer du **inventering** eller **ändrings spårning** under **konfigurations hantering**.

2. Välj **hantera datorer**. Knappen **hantera datorer** kan vara nedtonad om du tidigare har valt alternativet **Aktivera på alla tillgängliga och framtida datorer**

    ![Sparade sökningar](media/automation-enable-changes-from-auto-acct/managemachines.png)

3. Om du vill aktivera Ändringsspårning och inventering för alla tillgängliga datorer väljer du **Aktivera på alla tillgängliga datorer** på sidan hantera datorer. Den här åtgärden inaktiverar kontrollen för att lägga till datorer individuellt. Den här uppgiften lägger till alla namn på de datorer som rapporterar till arbets ytan till den dator grupp som sparade Sök frågan. När det här alternativet är markerat inaktive ras knappen **hantera datorer** .

4. Om du vill aktivera funktionen för alla tillgängliga datorer och framtida datorer väljer du **Aktivera på alla tillgängliga och framtida datorer**. Det här alternativet tar bort sparade sökningar och omfattnings konfiguration från arbets ytan och öppnar funktionen för alla Azure-och icke-Azure-datorer som rapporterar till arbets ytan. När det här alternativet är markerat inaktive ras knappen **hantera datorer** permanent, eftersom det inte finns någon omfattnings konfiguration kvar.

5. Om det behövs kan du lägga till omfångs konfigurationerna genom att lägga till de första sparade sökningarna igen. Mer information finns i [arbeta med scope-konfigurationer för ändringsspårning och inventering](automation-scope-configurations-change-tracking.md).

6. Om du vill aktivera funktionen för en eller flera datorer väljer du **Aktivera på valda datorer** och klickar på **Lägg till** bredvid varje dator för att aktivera funktionen. Den här uppgiften lägger till de valda dator namnen i den dator grupp sparade Sök frågan för funktionen.

## <a name="next-steps"></a>Nästa steg

* Mer information om funktionen finns i [hantera ändringsspårning och inventering](change-tracking-file-contents.md).
* Information om konfiguration av omfattningar finns i [arbeta med scope-konfigurationer för ändringsspårning och inventering](automation-scope-configurations-change-tracking.md).
* Information om hur du använder funktionen för att identifiera program vara som är installerad i din miljö finns i [identifiera vilken program vara som är installerad på dina virtuella datorer](automation-tutorial-installed-software.md).
* Om du inte vill integrera ditt Automation-konto med en Log Analytics arbets yta när du aktiverar funktionen, se [ta bort länken till arbets ytan från Automation-kontot](automation-unlink-workspace-change-tracking.md).
* När du har distribuerat ändringar av virtuella datorer kan du ta bort dem enligt beskrivningen i [ta bort virtuella datorer från ändringsspårning och inventering](automation-remove-vms-from-change-tracking.md).
* Information om hur du felsöker allmänna problem med funktionen finns i [felsöka ändringsspårning-och inventerings problem](troubleshoot/change-tracking.md).