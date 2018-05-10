---
title: 'Azure API: er för fakturering | Microsoft Docs'
description: Läs mer om Azure Billing-användning och RateCard APIs som används för att ge insikter om Azure resursförbrukning och trender.
services: ''
documentationcenter: ''
author: tonguyen
manager: tonguyen
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/9/2017
ms.author: mobandyo
ms.openlocfilehash: d94536bd0ab10c3e380855b66bc10dc9af6195a3
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2018
---
# <a name="use-azure-billing-apis-to-programmatically-get-insight-into-your-azure-usage"></a>Använd Azure Billing API: er för att genom programmering få bättre överblick på din användning av Azure
Använd Azure fakturering API: er som hämtar användnings- och data till din önskade verktyg för dataanalys. Användning av Azure och RateCard APIs kan hjälpa dig korrekt förutsäga och hantera dina kostnader. API: erna implementeras som en Resource Provider och en del av API: er som exponeras av Azure Resource Manager.  

## <a name="azure-invoice-download-api-preview"></a>Azure faktura Download API (förhandsgranskning)
En gång i [Anmäl har slutförts](billing-manage-access.md#opt-in), ladda ned fakturor med förhandsversionen av [faktura API](/rest/api/billing). Bland annat:

* **Azure rollbaserad åtkomstkontroll** -konfigurera åtkomstprinciper på den [Azure-portalen](https://portal.azure.com) eller via [Azure PowerShell-cmdlets](/powershell/azure/overview) att ange vilka användare eller program kan få åtkomst till den Prenumerationens användningsdata. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen antingen den fakturering Reader, Reader, ägare eller deltagare rollen för att få åtkomst till användningsdata för en viss Azure-prenumeration.
* **Datum filtrering** -användning i `$filter` parametern för att hämta alla fakturor i omvänd kronologisk ordning av periodslut fakturadatum. 

> [!NOTE]
> Den här funktionen är i första versionen av förhandsgranskning och kan vara föremål för bakåtkompatibilitet är inkompatibel ändringar. För närvarande, är det inte tillgängliga för vissa prenumerationserbjudanden (EA, CSP, AIO stöds inte) och Azure Tyskland.

## <a name="azure-resource-usage-api-preview"></a>Azure Resursanvändning API (förhandsgranskning)
Använd Azure [resurs användning API](https://msdn.microsoft.com/library/azure/mt219003) hämtar dina data uppskattade Azure förbrukningen. Detta API innehåller:

* **Azure rollbaserad åtkomstkontroll** -konfigurera åtkomstprinciper på den [Azure-portalen](https://portal.azure.com) eller via [Azure PowerShell-cmdlets](/powershell/azure/overview) att ange vilka användare eller program kan få åtkomst till den Prenumerationens användningsdata. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen antingen den fakturering Reader, Reader, ägare eller deltagare rollen för att få åtkomst till användningsdata för en viss Azure-prenumeration.
* **Varje timme och dag** – anropare kan ange om de vill ha sina Azure användningsdata i varje timme tidsgrupper eller dagligen tidsgrupper. Standardvärdet är varje dag.
* **Instansen metadata (inklusive resurstaggar)** – hämta instansen detaljnivåer som fullständiga resurs-uri (/subscriptions/ {prenumerations-id} /..), grupp resursinformation och resurstaggar. Dessa metadata hjälper dig att deterministiskt och genom programmering allokera användning av taggarna för användningsfall som mellan debitering.
* **Resursmetadata** -resursinformation som mätaren namn, mätaren kategori, mätaren underkategori, enhet och region ge anroparen en bättre förståelse för vad förbrukades. Vi arbetar också för att justera resurs metadata terminologi i Azure-portalen, Azure användning CSV, EA fakturering CSV och andra offentliga upplevelser, så att du kan korrelera data över upplevelser.
* **Syntax för olika erbjudandetyper** – användningsdata är tillgängliga för erbjudandetyper som betala per användning, MSDN, Summa, kredit och EA, utom [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-invoice#retrieve-usage-data-for-a-specific-subscription).

## <a name="azure-resource-ratecard-api-preview"></a>Azure-resurs RateCard API (förhandsgranskning)
Använd den [Azure Resource RateCard API](https://msdn.microsoft.com/library/azure/mt219005) att hämta listan över tillgängliga Azure-resurser och uppskattade prisinformation för varje. Detta API innehåller:

* **Azure rollbaserad åtkomstkontroll** -konfigurera principer för åtkomst på den [Azure-portalen](https://portal.azure.com) eller via [Azure PowerShell-cmdlets](/powershell/azure/overview) att ange vilka användare eller program kan få åtkomst till den RateCard data. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen Reader, ägare eller deltagare rollen för att få åtkomst till användningsdata för en viss Azure-prenumeration.
* **Stöd för betala per användning, MSDN, Summa och kredit erbjudanden (EA och [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-pricelist#get-prices-by-using-the-azure-rate-card) stöds inte)** -detta API ger Azure erbjudande nivå hastighet information.  Anroparen av denna API måste klara i erbjudandet information för att hämta resursinformation och priser. Vi kan för närvarande inte att ange EA priser eftersom EA erbjudanden har anpassat kostnader per registrering. 

## <a name="scenarios"></a>Scenarier
Här följer några scenarier som möjliggörs med en kombination av användningen och RateCard APIs:

* **Azure utgifter under månaden** - använda en kombination av användningen och RateCard APIs få bättre insikter om ditt moln utgifter under månaden. Du kan analysera per timme och dag buckets användnings- och kostnad uppskattningar.
* **Ställa in aviseringar** – använda användningen och RateCard APIs för att hämta uppskattade molnet förbrukning och kostnader och konfigurera resurs- eller monetära baserade aviseringar.
* **Förutsäga faktura** – Get uppskattade användnings- och molnet tillbringar och tillämpa maskininlärningsalgoritmer för att förutsäga växeln skulle vara i slutet av faktureringsperioden.
* **Före förbrukning kostnad analysis** – Använd RateCard API för att förutse hur mycket din faktura är för din förväntade användning när du flyttar dina arbetsbelastningar till Azure. Om du har befintliga arbetsbelastningar i andra moln eller privata moln, kan du även mappa förbrukningen i Azure tillbringar priser för att få en bättre uppfattning av Azure. Denna uppskattning ger dig möjlighet att pivotera på erbjudande och jämföra och kontrast mellan olika erbjudandetyper utöver betala per användning, t.ex. summa och kredit. API: et även ger dig möjlighet att se kostnaden skillnader efter region och kan du göra en vad händer om kostnadsanalys som hjälper dig att fatta distributionsbeslut.
* **Konsekvensanalys** -
  
  * Du kan fastställa om det är mer kostnadseffektivt att köra arbetsbelastningar i en annan region, eller på en annan konfiguration av Azure-resurs. Azure resurskostnader kan variera beroende på Azure-regionen du använder.
  * Du kan också bestämma om en annan typ av Azure-erbjudande ger en bättre hastighet på Azure-resurs.
  
## <a name="partner-solutions"></a>Partnerlösningar
[Molnet Cruiser och Microsoft Azure Billing API-integrering](billing-usage-rate-card-partner-solution-cloudcruiser.md) beskriver hur [moln Cruiser Express för Azure-paket](http://www.cloudcruiser.com/partners/microsoft/) fungerar direkt från Windows Azure Pack (WAP)-portalen. Du kan hantera operationella och finansiella aspekter av Microsoft Azure privat eller värdbaserade offentliga moln sömlöst från ett enda användargränssnitt.   

## <a name="next-steps"></a>Nästa steg
* Kolla kodexempel på GitHub:
  * [Fakturan API-kodexempel](https://go.microsoft.com/fwlink/?linkid=845124)

  * [Användnings-API-kodexempel](https://github.com/Azure-Samples/billing-dotnet-usage-api)

  * [RateCard API-kodexempel](https://github.com/Azure-Samples/billing-dotnet-ratecard-api)

* Läs mer om Azure Resource Manager i [översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). 



