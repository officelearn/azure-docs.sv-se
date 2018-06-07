---
title: Rapportera användningsdata för Azure-stacken till Azure | Microsoft Docs
description: Lär dig hur du ställer in användningsinformation för rapportering i Azure-stacken.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: brenduns
ms.reviewer: alfredop
ms.openlocfilehash: daaaf6c574c4b169c19ebec42ad68e2d818ca1cb
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34603710"
---
# <a name="report-azure-stack-usage-data-to-azure"></a>Rapportera Azure Stack användningsdata till Azure 

Användningsdata, kallas även förbrukningsdata, representerar mängden resurser som används. 

Azure med flera noder Stack-system som använder förbrukningsbaserad fakturering modellen bör rapportera användningsdata till Azure för fakturering ändamål.  Azure Stack-operatörer bör konfigurera sina Azure Stack-instans rapportera användningsdata till Azure.

> [!NOTE]
> Rapportering av användningsdata krävs för Azure-stacken med flera noder användare licens under lön-som-du-Använd modellen. Det är valfritt för kunder som har licens under kapacitet modellen (finns i [köpa sidan](https://azure.microsoft.com/overview/azure-stack/how-to-buy/). För Azure-stacken Development Kit användare Azure Stack-operatörer rapportera användningsdata och testa funktionen. Användare debiteras dock inte för alla de utsätts för användning. 


![flödet för fakturering](media/azure-stack-usage-reporting/billing-flow.png)

Användningsdata skickas till Azure via Azure bryggan från Azure-stacken. I Azure, handel systemet bearbetar användningsdata och genererar växeln. När växeln skapas, ägare Azure-prenumeration kan visa och ladda ner det från den [Azure Kontocenter](https://account.windowsazure.com/Subscriptions). Mer information om hur Azure-stacken är licensierad, referera till den [Azure Stack paketera och prissättning dokumentet](https://go.microsoft.com/fwlink/?LinkId=842847&clcid=0x409).

## <a name="set-up-usage-data-reporting"></a>Konfigurera rapportering av användningsdata

Om du vill konfigurera användningsrapport data måste du [registrera Azure Stack-instans med Azure](azure-stack-register.md). Komponenten Azure Bridge i Azure-stacken, som ansluter Azure Stack till Azure, och skickar användningsdata är konfigurerad som en del av registreringsprocessen. Följande användningsdata skickas till Azure från Azure Stack:

- **Läsa ID** – unikt ID för den resurs som har förbrukats.
- **Antal** – mängden Resursanvändning.
- **Plats** – platsen där den aktuella Azure-stacken resursen har distribuerats.
- **Resurs-URI** – fullständigt kvalificerade URI: N för den resurs som användning rapporteras.
- **Prenumerations-ID** – prenumerations-ID för den Azure Stack-användare som är lokala (Azure Stack)-prenumerationen.
- **Tid** – Start- och tid för användningsdata. Det finns vissa fördröjning mellan tiden när dessa resurser används i Azure-stacken och när användningsdata rapporteras till handel. Azure Stack mängder användningsdata för per dygn och reporting användningsdata för commerce pipeline i Azure tar ytterligare några timmar. Därför kan användning som sker inom kort före midnatt visas i Azure påföljande dag.

## <a name="generate-usage-data-reporting"></a>Generera en användningsrapport data

1. Testa användningsdata reporting skapa några resurser i Azure-stacken. Du kan till exempel skapa en [lagringskonto](azure-stack-provision-storage-account.md), [Windows Server VM](azure-stack-provision-vm.md) och en Linux VM med Basic och Standard-SKU: er för att se hur core användning rapporteras. Användningsdata för olika typer av resurser rapporteras under olika mätare.

2. Lämna din resurser som körs för några timmar. Användningsinformation som samlas in ungefär en gång i timmen. Efter att samla in, är dessa data skickas till Azure och bearbetas i Azure commerce-systemet. Den här processen kan ta upp till några timmar.

## <a name="view-usage---csp-subscriptions"></a>Visa användningen - CSP-prenumerationer

Om du har registrerat din Azure-stacken använder en CSP-prenumeration kan du visa din användning och kostnader på samma sätt som du visar Azure-förbrukningen. Azure Stack-användning som ska inkluderas i fakturan och filen avstämning tillgängliga via [Partnercenter](https://partnercenter.microsoft.com/partner/home). Filen avstämning uppdateras varje månad. Du kan använda API: er för Partner Center om du behöver åtkomst till senaste användningsinformation för Azure-stacken.

   ![partnercenter](media/azure-stack-usage-reporting/partner-center.png)


## <a name="view-usage--enterprise-agreement-subscriptions"></a>Visa användningen – Enterprise-avtal prenumerationer

Om du har registrerat din Azure-stacken använder en Enterprise-avtal prenumeration kan du visa användnings- och tillägg i den [EA Portal](https://ea.azure.com/). Azure Stack-användning ska inkluderas i avancerade hämtningar tillsammans med Azure usage under rapportavsnittet i EA-portalen. 

## <a name="view-usage--other-subscriptions"></a>Visa användningen – andra prenumerationer

Om du har registrerat din Azure-stacken använder en annan prenumerationstyp, till exempel en betala per prenumeration kan du visa användning och kostnader i Azure kontocenter. Logga in på den [Azure Kontocenter](https://account.windowsazure.com/Subscriptions) som Azure kontoadministratör och välj den Azure-prenumeration som du använde för att registrera den Azure-stacken. Du kan visa användningsdata för Azure-stacken, beloppet debiteras för var och en av resurserna du använder som visas i följande bild:

   ![flödet för fakturering](media/azure-stack-usage-reporting/pricing-details.png)

För Azure-stacken Development Kit debiteras inte Azure Stack resurser så visas priset som $0.00.

## <a name="which-azure-stack-deployments-are-charged"></a>Vilka Azure Stack-distributioner debiteras?

Resursanvändning är gratis för Azure-stacken Development Kit. De virtuella datorerna i arbetsbelastningen och lagringstjänster Apptjänster debiteras för Azure-stacken med flera noder system.

## <a name="are-users-charged-for-the-infrastructure-vms"></a>Användare debiteras för infrastrukturen som virtuella datorer?

Nej. Användningsdata för vissa Azure Stack-resursprovidern VMs rapporteras till Azure, men det finns inga avgifter för dessa virtuella datorer eller för de virtuella datorerna som skapas under distributionen att aktivera Azure Stack-infrastruktur.  

Användare endast debiteras för virtuella datorer som körs under klient prenumerationer. Alla arbetsbelastningar måste distribueras under klient prenumerationer att följa licensvillkoren för Azure-stacken.

## <a name="i-have-a-windows-server-license-i-want-to-use-on-azure-stack-how-do-i-do-it"></a>Jag har en Windows Server-licens som du vill använda Azure-stacken, hur gör jag?

Med hjälp av befintliga licenser undviker genererar användning mätare. Befintliga Windows Server-licenser kan användas i Azure-stacken, enligt beskrivningen i avsnittet ”använda befintliga program med Azure Stack” i [licensiering guiden Azure Stack](https://go.microsoft.com/fwlink/?LinkId=851536&clcid=0x409). Kunder som behöver distribuera sina virtuella datorer i Windows Server enligt beskrivningen i den [Hybrid-förmån för Windows Server-licens](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing) artikel för att kunna använda sina befintliga licenser.

## <a name="which-subscription-is-charged-for-the-resources-consumed"></a>Vilken prenumeration debiteras för de resurser som används?
Den prenumeration som har angetts när [registrera Azure stacken med Azure](azure-stack-register.md) debiteras.

## <a name="what-types-of-subscriptions-are-supported-for-usage-data-reporting"></a>Vilka typer av prenumerationer stöds för rapportering av användningsdata?

För Azure-stacken multinode stöds Enterprise-avtal (EA) och CSP prenumerationer. Enterprise-avtal (EA), betala per användning, CSP och MSDN-prenumerationer stöd för Azure-stacken Development Kit rapportering av användningsdata.

## <a name="does-usage-data-reporting-work-in-sovereign-clouds"></a>Stöder användningsdata reporting arbete i suveräna moln?

I Azure-stacken Development Kit kräver rapportering av användningsdata prenumerationer som skapas i det globala Azure systemet. Prenumerationer som skapats i en av suveräna molnen (Azure Government, Azure Tyskland och Azure Kina moln) kan inte registreras med Azure, så att de inte har stöd för rapportering av användningsdata.

## <a name="how-can-users-identify-azure-stack-usage-data-in-the-azure-billing-portal"></a>Hur kan användare identifiera Azure Stack användningsdata i Azure portal för fakturering?

Användare kan se Azure Stack användningsdata i filen användning information. Om du vill veta om hur du hämtar filen användning information, referera till den [hämta användning filen från Azure Kontocenter artikeln](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-from-the-account-center-csv). Användning av information filen innehåller Azure Stack-mätare som identifierar Azure Stack lagring och virtuella datorer. Alla resurser som används i Azure-stacken rapporteras under region med namnet ”Azure stacken”.

## <a name="why-doesnt-the-usage-reported-in-azure-stack-match-the-report-generated-from-azure-account-center"></a>Varför matchar inte användningsdata som rapporteras i Azure-stacken rapporten genereras från Azure Account Center?

Det finns alltid en fördröjning mellan användningsdata som rapporteras av Azure Stack-användning API: er och användningsdata som rapporteras av Azure Kontocenter. Den här fördröjningen är den tid som krävs för att överföra data om programvaruanvändning på Azure-stacken till Azure handel. På grund av den här fördröjningen användning som sker inom kort före midnatt kan visas i Azure påföljande dag. Om du använder den [API: er för användning av Azure Stack](azure-stack-provider-resource-api.md), och jämföra resultatet till användning som rapporteras i Azure portal för fakturering, du kan se skillnad.

## <a name="next-steps"></a>Nästa steg

* [API för provideranvändning](azure-stack-provider-resource-api.md)  
* [API för klientanvändning](azure-stack-tenant-resource-usage-api.md)
* [Vanliga frågor och svar om användning](azure-stack-usage-related-faq.md)
* [Hantera användningen och fakturering som en Molntjänstleverantör](azure-stack-add-manage-billing-as-a-csp.md)
