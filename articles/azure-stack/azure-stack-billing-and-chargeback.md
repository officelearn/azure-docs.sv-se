---
title: "Kunden fakturerings- och återbetalning i Azure-stacken | Microsoft Docs"
description: "Lär dig hur du hämtar information om användning från Azure-stacken."
services: azure-stack
documentationcenter: 
author: AlfredoPizzirani
manager: byronr
editor: 
ms.assetid: a9afc7b6-43da-437b-a853-aab73ff14113
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: alfredop
ms.openlocfilehash: ea7510c239ee07a9a27f3e682e61a6b08eb5694d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="usage-and-billing-in-azure-stack"></a>Användnings- och fakturering i Azure-stacken

Användning representerar antalet resurser som används av en användare. Azure-stacken samlar in användningsinformation för varje användare och används för att debiterar dem. Den här artikeln beskriver hur användare i Azure-stacken debiteras för Resursanvändning och hur faktureringsinformation används för analytics, återbetalning, osv.

Azure stacken innehåller infrastruktur för att samla in och sammanställa användningsdata för alla resurser och för att vidarebefordra data till Azure handel. Du kan komma åt dessa data och exportera den till en faktureringssystem med hjälp av ett fakturerings-kort eller exportera den till ett business intelligence-verktyg som Microsoft Power BI. När du exporterar, är det här faktureringsinformation används för analytics eller överföras till ett system för återbetalning.

![Konceptuell modell ett fakturerings-kort ansluter Azure Stack till en fakturering program](media/azure-stack-billing-and-chargeback/image1.png)

## <a name="usage-pipeline"></a>Användning av pipeline

Varje resursprovider i Azure-stacken avger användningsdata enligt resursutnyttjande. Webbtjänsten Usage regelbundet (varje timme eller varje dag) samlar in dessa användningsdata och lagrar den i användningsdatabasen. Lagrade användningsdata kan nås av Azure-stacken operatörer och användare lokalt genom att använda API: er för användning. 

Om du har [registrerad Azure Stack-instans med Azure](azure-stack-register.md), användning Bridge har konfigurerats för att skicka användningsdata till Azure handel. När data är tillgängliga i Azure, har du åtkomst till den via fakturerings-portalen eller med hjälp av Azure användning API: er. Referera till den [data användningsrapport](azure-stack-usage-reporting.md) Läs mer om vilka rapporteras mer data till Azure. 

Följande bild visar viktiga komponenter i pipeline för användning:

![Användning av pipeline](media/azure-stack-billing-and-chargeback/usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>Information om vilka användning kan hitta, och hur?

Azure-stacken resurs-leverantörer, till exempel bearbetning, lagring och nätverk, generera användningsdata i timmen för varje prenumeration. Användningsdata innehåller information om den resurs som används som resursnamn, prenumeration som används, antalet används, osv. Mer information om mätare ID resurser, referera till den [användning API vanliga frågor och svar](azure-stack-usage-related-faq.md) artikel. 

När användningsdata har samlats in, är det [rapporteras till Azure](azure-stack-usage-reporting.md) att generera en faktura som kan granskas via Azure portal för fakturering. 

> [!NOTE]
> Rapportering av användningsdata krävs inte för Azure-stacken Development Kit och Azure-stacken integrerat systemanvändare som har licens under kapacitet modellen. Läs mer om licensiering i Azure-stacken i den [paketering och prissättning](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf) datablad.

Azure-fakturering portalen visar användningsdata bara för de avgiftsbelagda resurserna. Utöver resurserna som avgiftsbelagda Azure Stack samlar in användningsdata för en bredare uppsättning resurser som du kan komma åt i Azure-stacken miljön via REST API: er eller PowerShell. Azure Stack-operatörer kan hämta användningsdata för alla användare prenumerationer medan en användare kan få deras användningsinformation.

## <a name="retrieve-usage-information"></a>Hämta information om användning

För att generera användningsdata, bör du ha resurser som körs och aktivt med systemet, till exempel, en aktiv virtuell dator eller ett lagringskonto som innehåller vissa data osv. Om du inte vet om du har några resurser som körs i Azure-stacken Marketplace, distribuera en virtuell dator (VM) och kontrollera den virtuella datorn körs övervakning bladet för att se till att den. Använd följande PowerShell-cmdlets om du vill visa användningsdata:

1. [Installera PowerShell för Azure-stacken.](azure-stack-powershell-install.md)
2. [Konfigurera Azure Stack användarens](user/azure-stack-powershell-configure-user.md) eller [Azure Stack operatorn](azure-stack-powershell-configure-admin.md) PowerShell-miljö 

3. Använd för att hämta användningsdata i [Get-UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates) PowerShell-cmdlet:

   ```powershell
   Get-UsageAggregates -ReportedStartTime "<Start time for usage reporting>" -ReportedEndTime "<end time for usage reporting>" -AggregationGranularity <Hourly or Daily>
   ```

## <a name="next-steps"></a>Nästa steg

[Rapportera Azure Stack användningsdata till Azure](azure-stack-usage-reporting.md)

[Providern Resursanvändning API](azure-stack-provider-resource-api.md)

[Resursanvändning API-klient](azure-stack-tenant-resource-usage-api.md)

[Användning-relaterade vanliga frågor och svar](azure-stack-usage-related-faq.md)

