---
title: Lägga till utökad mätvärden för virtuella datorer i Azure | Microsoft Docs
description: Den här artikeln hjälper dig att aktivera och konfigurera utökade diagnostik mått för din virtuella Azure-datorer.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/07/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 58245478cf49c030c435b487e233bbc893a2b9a3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/11/2018
ms.locfileid: "35296364"
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Lägga till utökad mätvärden för virtuella Azure-datorer

Kostnad Management används Azure mått data från ditt virtuella Azure-datorer för att visa du detaljerad information om sina resurser. Mätvärden, kallas även prestandaräknare, används av kostnaden Management för att generera rapporter. Dock kostnaden Management inte automatiskt samla in alla Azure mått data från gäst-VM – du måste aktivera mått samling. Den här artikeln hjälper dig att aktivera och konfigurera ytterligare diagnostik mått för din virtuella Azure-datorer.

När du har aktiverat mått samling kan du:

- Vet när dina virtuella datorer ansluter till deras minne, disk och CPU-gränser.
- Identifiera användningstrender och avvikelser.
- Kontrollera dina kostnader genom storlek efter användning.
- Hämta kostnaden effektiva storleksanpassa optimering rekommendationerna från kostnaden Management.

Du kanske vill övervaka processor och minne % av dina virtuella Azure-datorer. Virtuella Azure-mått motsvarar _[värd] procent CPU_ och _[gäst] minnesprocent_.

## <a name="verify-that-metrics-are-enabled-on-vms"></a>Kontrollera att mått har aktiverats på virtuella datorer

1. Logga in på Azure Portal på http://portal.azure.com.
2. Under **virtuella datorer**, Välj en virtuell dator och sedan under **övervakning**väljer **mått**. En lista över tillgängliga mått visas.
3. Ett diagram som visar data för dem att markera vissa mått.  
    ![Exempel mått – värden procent CPU](./media/azure-vm-extended-metrics/metric01.png)

En begränsad uppsättning standard mått är tillgängliga för värdar i föregående exempel, men minne är inte. Minne är en del av utökade mått. Du måste utföra några ytterligare steg för att aktivera utökad mått. Följande information hjälper dig att aktivera dem.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Aktivera utökad mätvärden i Azure-portalen

Standard mått är värden datorn mått. Den _[värd] procent CPU_ mått är ett exempel. Det finns också grundläggande mätvärden för gäst-VM och de också kallas utökade mått. Exempel på utökad mått är _[gäst] minnesprocent_ och _[] Gästminne_.

Aktivera utökad mått är enkelt. Aktivera övervakning av gästnivå för varje virtuell dator. När du aktiverar övervakning av gästnivå är Azure diagnostics-agenten installerad på den virtuella datorn. Följande process är samma för klassiska och regelbundna virtuella datorer och Windows och Linux virtuella datorer på samma.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Aktivera gästnivå övervakning på befintliga virtuella datorer

1. I **virtuella datorer**, visa en lista över dina virtuella datorer och väljer sedan en virtuell dator.
2. Under **övervakning**väljer **mått**.
3. Klicka på **diagnostikinställningar**.
4. På sidan diagnostik inställningar **aktivera övervakning på gästnivå**. Virtuella Linux-datorer kräver ett befintligt lagringskonto. Om du inte väljer ett lagringskonto för en virtuell Windows-dator, skapas ett åt dig.  
    ![Aktivera gäst nivån övervakning](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
5. Azure diagnostics-agenten är installerad på den virtuella datorn efter några minuter. Uppdatera sidan och uppdatera listan över tillgängliga mått med gäst.  
    ![Utökade mått](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Aktivera gästnivå övervakning på nya virtuella datorer

När du skapar nya virtuella datorer kan du se till att du väljer **Gästoperativsystem diagnostik**. Virtuella Linux-datorer kräver ett befintligt lagringskonto. Om du inte väljer ett lagringskonto för en virtuell Windows-dator, skapas ett åt dig.

![Aktivera diagnostik för gäst-OS](./media/azure-vm-extended-metrics/new-enable-diag.png)

## <a name="resource-manager-credentials"></a>Resource Manager-autentiseringsuppgifter

När du aktiverar utökade mått, kontrollera att kostnaden Management har åtkomst till din [Resource Manager autentiseringsuppgifter](activate-subs-accounts.md). Dina autentiseringsuppgifter krävs för kostnaden för att samla in och visa prestandadata för dina virtuella datorer. De används också att skapa rekommendationer för optimering av kostnaden. Kostnad Management måste minst tre dagar data från en instans för att avgöra om det är en kandidat för en downsizing rekommendation.

## <a name="enable-vm-metrics-with-a-script"></a>Aktivera mätvärden för virtuell dator med ett skript

Du kan aktivera mätvärden för virtuell dator med Azure PowerShell-skript. När du har många virtuella datorer som du vill aktivera mått på kan använda du ett skript för att automatisera processen. Exempelskript finns på GitHub på [aktivera Azure-diagnostik](https://github.com/Cloudyn/azure-enable-diagnostics).

## <a name="view-azure-performance-metrics"></a>Visa Azure prestandamått

Om du vill visa prestandamått för Azure-instanser i Cloudyn portal, navigerar du till **tillgångar** > **Compute** > **instans Explorer**. Expandera en instans i listan över VM-instanser och expandera sedan en resurs för att visa information.

![Instansen Explorer](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Nästa steg

- Om du inte redan har aktiverat Azure Resource Manager API-åtkomst för dina konton, fortsätter du till [aktivera Azure-prenumerationer och konton](activate-subs-accounts.md)
