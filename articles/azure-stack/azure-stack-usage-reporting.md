---
title: Rapportera användningsdata för Azure Stack till Azure | Microsoft Docs
description: Lär dig hur du ställer in användningsdata för rapportering i Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: 1cf0d0f6676da78d9b8b159abce2c83c8a850c15
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55252174"
---
# <a name="report-azure-stack-usage-data-to-azure"></a>Rapportera användningsdata för Azure Stack till Azure

Användningsdata, även kallat förbrukningsdata, representerar mängden resurser som används.

Azure Stack med flera noder system som använder förbrukningsbaserad faktureringsmodellen ska rapportera användningsdata till Azure för fakturering. Azure Stack-operatörer konfigurera deras Azure Stack-instans rapportera användningsdata till Azure.

> [!IMPORTANT]
> Alla arbetsbelastningar [måste distribueras under klient prenumerationerna](#are-users-charged-for-the-infrastructure-vms) att följa licensvillkoren för Azure Stack.

Rapportering av användning krävs för de Azure Stack med flera noder användare med licens under betalning som du-användning-modell. Det är valfritt för kunder som licensierar under modellen kapacitet (se den [köpa sidan](https://azure.microsoft.com/overview/azure-stack/how-to-buy/). För Azure Stack Development Kit användare, Azure Stack-operatörer rapportera användningsdata och testa funktionen. Användare debiteras dock inte för eventuell användning som leder de.

![fakturering flöde](media/azure-stack-usage-reporting/billing-flow.png)

Användningsdata skickas till Azure via Azure-brygga från Azure Stack. I Azure, handelssystem bearbetar användningsdata och genererar fakturan. När fakturan skapas Azure-prenumerationsägare kan visa och ladda ned det från den [Azure Kontocenter](https://account.windowsazure.com/Subscriptions). Läs om hur Azure Stack är licensierad i den [Azure Stack förpackning och priser dokumentet](https://go.microsoft.com/fwlink/?LinkId=842847).

## <a name="set-up-usage-data-reporting"></a>Konfigurera rapportering av användning

Om du vill konfigurera rapportering av användning, måste du [registrera din Azure Stack-instans med Azure](azure-stack-register.md). Azure-brygga komponent i Azure Stack, som ansluter Azure Stack till Azure och skickar användningsdata, är konfigurerad som en del av registreringsprocessen. Följande användningsdata skickas till Azure från Azure Stack:

- **Mäter ID** – unikt ID för den resurs som förbrukades.
- **Antal** – mängden Resursanvändning.
- **Plats** – plats där den aktuella Azure Stack-resursen har distribuerats.
- **Resurs-URI för** – fullständigt kvalificerade URI för den resursen som användning rapporteras.
- **Prenumerations-ID** – prenumerations-ID för Azure Stack-användare, vilket är den lokala (Azure Stack)-prenumerationen.
- **Tid** – Start- och tidpunkten för användningsdata. Det finns en fördröjning mellan tiden när dessa resurser används i Azure Stack och när användningsdata som rapporteras till handel. Användningsdata för Azure Stack-mängder för var 24: e timme och rapportering av användningsdata till commerce pipelinen i Azure börjar du ytterligare några timmar. Därför kan användningen strax innan midnatt visas i Azure nästa dag.

## <a name="generate-usage-data-reporting"></a>Generera rapportering av användning

- Testa rapportering av användningsdata genom att skapa några resurser i Azure Stack. Du kan till exempel skapa en [lagringskonto](azure-stack-provision-storage-account.md), [Windows Server VM](azure-stack-provision-vm.md) och en Linux VM med Basic och Standard-SKU: er för att se hur core användning rapporteras. Användningsdata för olika typer av resurser rapporteras under olika taxor.

- Lämna din resurser som körs i några timmar. Användningsinformation som samlas in ungefär en gång i timmen. Efter att samla in dessa data överförs till Azure och bearbetas i Azure commerce-systemet. Den här processen kan ta upp till några timmar.

## <a name="view-usage---csp-subscriptions"></a>Visa användning – CSP-prenumerationer

Om du har registrerat din Azure-stacken med hjälp av en CSP-prenumeration kan du visa din användning och avgifter på samma sätt som du kan visa Azure-förbrukning. Azure Stack-användning ska inkluderas i din faktura och i avstämningsfilen tillgänglig via den [Partnercenter](https://partnercenter.microsoft.com/partner/home). Avstämningsfilen uppdateras varje månad. Du kan använda API: er för Partner Center om du vill få åtkomst till de senaste Azure Stack-användningsinformation.

![partnercenter](media/azure-stack-usage-reporting/partner-center.png)

## <a name="view-usage--enterprise-agreement-subscriptions"></a>Visa användning – Enterprise Agreement-prenumerationer

Om du har registrerat din Azure-stacken med hjälp av ett Enterprise Agreement-prenumeration kan du visa din användning och kostnader på den [EA-portalen](https://ea.azure.com/). Användningen av Azure Stack ingår i de avancerade nedladdningarna tillsammans med Azure-användning under rapportavsnittet i den här portalen. 

## <a name="view-usage--other-subscriptions"></a>Visa användning – andra prenumerationer

Om du har registrerat din Azure-Stack med hjälp av någon annan prenumeration skriver; till exempel en prenumeration med Användningsbaserad betalning, kan du visa användning och avgifter i Azure kontocenter. Logga in på den [Azure Kontocenter](https://account.windowsazure.com/Subscriptions) som Azure-kontot Administratör och välj den prenumeration som du använde för att registrera Azure Stack. Du kan visa användningsdata för Azure Stack, hur mycket debiteras för var och en av resurserna du använder, enligt följande bild:

![fakturering flöde](media/azure-stack-usage-reporting/pricing-details.png)

För Azure Stack Development Kit debiteras inte Azure Stack-resurser, så priset visas som $0,00.

## <a name="which-azure-stack-deployments-are-charged"></a>Vilka Azure Stack-distributioner debiteras?

Resursanvändningen är gratis för Azure Stack Development Kit. Azure Stack-system med flera noder, virtuella datorerna i arbetsbelastningen, lagringstjänster och Apptjänster, som debiteras.

## <a name="are-users-charged-for-the-infrastructure-vms"></a>Användare debiteras för infrastrukturens virtuella datorer?

Nej. Användningsdata för vissa Azure Stack-resursprovidern virtuella datorer rapporteras till Azure, men det finns inga avgifter för dessa virtuella datorer, och inte heller för de virtuella datorerna som skapades under distributionen att aktivera Azure Stack-infrastruktur.  

Användarna debiteras bara för virtuella datorer som körs under klient-prenumerationerna. Alla arbetsbelastningar måste distribueras under klient prenumerationer att följa licensvillkoren för Azure Stack.

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-how-do-i-do-it"></a>Jag har en Windows Server-licens som jag vill använda på Azure Stack, hur gör jag det?

Med de befintliga licenserna undviker genererar användning taxor. Befintliga Windows Server-licenser kan användas i Azure Stack, enligt beskrivningen i avsnittet ”använda befintlig programvara med Azure Stack” i den [Licensieringsguiden för Azure Stack](https://go.microsoft.com/fwlink/?LinkId=851536). Måste kunderna distribuera sina Windows Server virtuella datorer enligt beskrivningen i den [Hybrid-förmånen för Windows Server-licens](../virtual-machines/windows/hybrid-use-benefit-licensing.md) artikeln för att kunna använda sina befintliga licenser.

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>Vilken prenumeration som debiteras för de resurser som används?

Den prenumeration som har angetts när [registrera Azure Stack med Azure](azure-stack-register.md) debiteras.

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>Vilka typer av prenumerationer stöds för rapportering av användning?

Enterprise Agreement (EA) och CSP-prenumerationer stöds för Azure Stack med flera noder. Enterprise Agreement (EA), betala per användning, CSP och MSDN-prenumerationer stöd för Azure Stack Development Kit rapportering av användning.

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>Fungerar användningsdata reporting arbets i suveräna moln?

I Azure Stack Development Kit kräver användningsrapportering data prenumerationer som skapas i det globala Azure-systemet. Prenumerationer som skapats i någon av suveräna moln (Azure Government, Azure Tyskland och Azure Kina moln) kan inte registreras med Azure, så att de inte stöder rapportering av användning.

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>Varför matchar inte den användning som rapporteras i Azure Stack rapporten som genereras från Azure-Kontocenter?

Det finns alltid en fördröjning mellan de användningsdata som rapporteras av API: er för Azure Stack-användningen och de användningsdata som rapporteras av Azure Kontocenter. Den här fördröjningen är den tid som krävs för att överföra data om programvaruanvändning Azure Stack på Azure handel. På grund av den här fördröjningen användningen strax innan midnatt kan visas i Azure nästa dag. Om du använder den [Azure Stack-användning API: er](azure-stack-provider-resource-api.md), och jämför resultatet till användningen av rapporteras i Azure-faktureringsportalen kan du se skillnad.

## <a name="next-steps"></a>Nästa steg

* [API för provideranvändning](azure-stack-provider-resource-api.md)  
* [API för klientanvändning](azure-stack-tenant-resource-usage-api.md)
* [Vanliga frågor och svar om användning](azure-stack-usage-related-faq.md)
* [Hantera användning och fakturering som en Molntjänstleverantör](azure-stack-add-manage-billing-as-a-csp.md)
