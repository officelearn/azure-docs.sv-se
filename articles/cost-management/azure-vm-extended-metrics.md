---
title: Lägga till utökade mått för Azure virtual machines | Microsoft Docs
description: Den här artikeln hjälper dig att aktivera och konfigurera utökade diagnostikmåtten för virtuella datorer i Azure.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/12/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: b7e4665dc3579f357ce1e28bf34be35c931736bd
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35647673"
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Lägga till utökade mått för Azure-datorer

Kostnadshantering används Azure måttdata från virtuella datorer i Azure för att visa du detaljerad information om sina resurser. Måttdata, även kallat prestandaräknare, används av Cost Management för att generera rapporter. Dock Cost Management inte automatiskt samla in alla Azure måttdata från virtuella gästdatorer – du måste aktivera mått samling. Den här artikeln hjälper dig att aktivera och konfigurera ytterligare diagnostik mått för virtuella datorer i Azure.

När du har aktiverat mått samling kan du:

- Vet när dina virtuella datorer ansluter till deras minne, disk och CPU-gränser.
- Identifiera användningstrender och avvikelser.
- Styra kostnaderna genom storlek beroende på användningen.
- Hämta kostnad effektiva Bedömningsrekommendationer optimering från Cost Management.

Du kanske exempelvis vill övervaka processor och minne-% av virtuella datorer i Azure. Azure VM-mått som motsvarar _[värd] procent CPU_ och _[gäst] minnesprocent_.

> [!NOTE]
> Datainsamling för utökade mått stöds bara med Azure-övervakning på gästnivå. Kostnadshantering är inte kompatibel med Log Analytics VM-tillägg.

## <a name="verify-that-metrics-are-enabled-on-vms"></a>Kontrollera att mått är aktiverat på virtuella datorer

1. Logga in på Azure Portal på http://portal.azure.com.
2. Under **virtuella datorer**, Välj en virtuell dator och sedan under **övervakning**väljer **mått**. En lista över tillgängliga mått visas.
3. Välj vissa mått och ett diagram som visar data för dessa.  
    ![Exempel mått – värd procent CPU](./media/azure-vm-extended-metrics/metric01.png)

En begränsad uppsättning standardmått är tillgängliga för värdar i föregående exempel, men minne mått är inte. Minne mått är en del av utökade mått. Du måste utföra några ytterligare steg för att aktivera utökade mått. Följande information hjälper dig att aktivera dem.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Aktivera utökad mätvärden i Azure portal

Standardmått är värdmått för datorn. Den _[värd] procent CPU_ mått är ett exempel. Det finns också basmått för virtuella gästdatorer och de också kallas utökade mått. Exempel på utökade mått är _[gäst] minnesprocent_ och _[] Gästminne tillgängliga_.

Det är enkelt att aktivering av utökad mått. Aktivera övervakning på gästnivå för varje virtuell dator. När du aktiverar övervakning på gästnivå, är Azure diagnostics-agenten installerad på den virtuella datorn. Följande process är samma för virtuella datorer i klassiska och regelbundna och likadan ut för Windows och Linux-datorer.

Tänk på att både Azure och Linux övervakning på gästnivå kräver ett lagringskonto. När du aktiverar övervakning på gästnivå, om du inte väljer ett befintligt lagringskonto kan skapas ett åt dig.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>Aktivera övervakning på gästnivå på befintliga virtuella datorer

1. I **virtuella datorer**, visa din lista över dina virtuella datorer och väljer sedan en virtuell dator.
2. Under **övervakning**väljer **mått**.
3. Klicka på **diagnostikinställningar**.
4. På sidan diagnostik inställningar **aktivera övervakning på gästnivå**.  
    ![Aktivera på gäst-övervakning](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
5. Azure diagnostics-agenten är installerad på den virtuella datorn efter ett par minuter. Uppdatera sidan och listan över tillgängliga mått har uppdaterats med gästmått.  
    ![Utökade mått](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>Aktivera övervakning på gästnivå på nya virtuella datorer

När du skapar nya virtuella datorer kan du se till att du väljer **diagnostik för Gästoperativsystem**.

![Aktivera diagnostik för Gästoperativsystem](./media/azure-vm-extended-metrics/new-enable-diag.png)

## <a name="resource-manager-credentials"></a>Resource Manager-autentiseringsuppgifter

När du aktiverar utökade mått, kontrollerar du att kostnadshantering har åtkomst till din [Resource Manager autentiseringsuppgifter](activate-subs-accounts.md). Dina autentiseringsuppgifter krävs för kostnadshantering att samla in och visa information om prestanda för dina virtuella datorer. De också används för att skapa kostnadsrekommendationer för optimering. Kostnadshantering måste minst tre dagar prestandadata från en instans för att avgöra om det är en kandidat för en downsizing rekommendation.

## <a name="enable-vm-metrics-with-a-script"></a>Aktivera VM-mått med ett skript

Du kan aktivera mätvärden för virtuell dator med Azure PowerShell-skript. När du har många virtuella datorer som du vill aktivera mått på kan använda du ett skript för att automatisera processen. Exempelskript som finns på GitHub på [Azure aktivera diagnostik](https://github.com/Cloudyn/azure-enable-diagnostics).

## <a name="view-azure-performance-metrics"></a>Visa Azure prestandamått

Om du vill visa prestandamått på dina Azure-instanser i Cloudyn-portalen, gå till **tillgångar** > **Compute** > **instans Explorer**. I listan över VM-instanser, expandera en instans och sedan en resurs för att visa information.

![Instans-Explorer](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>Nästa steg

- Om du inte redan har aktiverat Azure Resource Manager API-åtkomst för dina konton, fortsätter du till [Activate Azure-prenumeration och konton](activate-subs-accounts.md).
