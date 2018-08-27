---
title: 'API: er med Azure fakturering | Microsoft Docs'
description: Läs mer om Azure-fakturering användning och RateCard APIs som används för att ge insikter om Azure resursförbrukning och trender.
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
ms.date: 5/10/2018
ms.author: mobandyo
ms.openlocfilehash: 650fac6208adf8f904384454b2e66e26e45893f1
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42918860"
---
# <a name="use-azure-billing-apis-to-programmatically-get-insight-into-your-azure-usage"></a>Använd Azure Billing API: er för att programmässigt få insikt i din Azure-användning
Använda Azure Billing API: er att hämta användnings- och data till din önskade analysverktyg. Azure Resource-användning och RateCard APIs kan hjälpa dig att korrekt förutse och hantera dina kostnader. API: er implementeras som en Provider för nätverksresurser och en del av familjen av API: er som exponeras av Azure Resource Manager.  

> [!div class="nextstepaction"]
> [Hjälp oss att förbättra Azure fakturering docs](https://go.microsoft.com/fwlink/p/?linkid=2010091)

## <a name="azure-invoice-download-api-preview"></a>API för nedladdning av faktura för Azure (förhandsversion)
När den [anmälan har slutförts](billing-manage-access.md#opt-in), ladda ned fakturor med hjälp av förhandsversionen av [faktura API](/rest/api/billing). Funktionerna omfattar:

* **Azure rollbaserad åtkomstkontroll** -konfigurera åtkomstprinciper på den [Azure-portalen](https://portal.azure.com) eller via [Azure PowerShell-cmdlets](/powershell/azure/overview) att ange vilka användare eller program kan få åtkomst till den Prenumerationens användningsdata. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen till antingen den Billing Reader, läsare, ägare eller deltagare roll för att få åtkomst till användningsdata för en viss Azure-prenumeration.
* **Datum filtrering** – Använd den `$filter` parametern för att hämta alla fakturor i omvänd kronologisk ordning av periodslut fakturadatum. 

> [!NOTE]
> Den här funktionen är i första versionen av förhandsgranskning och kan vara föremål för bakåtkompatibilitet inkompatibel ändringar. För närvarande, är det inte tillgänglig för vissa prenumerationserbjudanden (EA, CSP, AIO som inte stöds) och Azure Germany.

## <a name="azure-resource-usage-api-preview"></a>Azure-Resursanvändning API (förhandsversion)
Använd Azure [Resource användning API](https://msdn.microsoft.com/library/azure/mt219003) att hämta uppskattad Azure-förbrukning-data. API: et innehåller:

* **Azure rollbaserad åtkomstkontroll** -konfigurera åtkomstprinciper på den [Azure-portalen](https://portal.azure.com) eller via [Azure PowerShell-cmdlets](/powershell/azure/overview) att ange vilka användare eller program kan få åtkomst till den Prenumerationens användningsdata. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen till antingen den Billing Reader, läsare, ägare eller deltagare roll för att få åtkomst till användningsdata för en viss Azure-prenumeration.
* **Varje timme eller varje dag** – anropare ange buckets om de vill ha sina data i Azure-användning i per timme eller varje dag buckets. Standardvärdet är varje dag.
* **Instans-metadata (inklusive resurstaggar)** – hämta instansnivå information som fullständigt kvalificerade resurs-uri (/subscriptions/ {prenumerations-id} /...), grupp resursinformationen och resurstaggar. Dessa metadata kan du deterministiskt och programmässigt allokera användning efter taggar, för användningsfall som cross-debitering.
* **Resursmetadata** -resursinformation som mätningsnamn, underkategori, underkategori för mätning, enhet och region ger anroparen en bättre förståelse för vad som förbrukades. Vi arbetar även om du vill justera resursen metadata terminologi för Azure-portalen, Azure-användning CSV, EA fakturering CSV och andra offentliga upplevelser, så att du kan korrelera data över upplevelser.
* **Användningen av olika erbjudandetyper** – användningsdata är tillgängliga för typer av erbjudanden som betalar per användning, MSDN, monetära åtaganden, kredit och EA, utom [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-invoice#retrieve-usage-data-for-a-specific-subscription).

## <a name="azure-resource-ratecard-api-preview"></a>Azure-resurs RateCard-API (förhandsversion)
Använd den [RateCard-API för Azure Resource](https://msdn.microsoft.com/library/azure/mt219005) att hämta listan över tillgängliga Azure-resurser och uppskattade prisinformationen för varje. API: et innehåller:

* **Azure rollbaserad åtkomstkontroll** -konfigurera dina åtkomstprinciper på den [Azure-portalen](https://portal.azure.com) eller via [Azure PowerShell-cmdlets](/powershell/azure/overview) att ange vilka användare eller program kan få åtkomst till den RateCard data. Anropare måste använda standard Azure Active Directory-token för autentisering. Lägg till anroparen läsare, ägare eller deltagare rollen för att få åtkomst till användningsdata för en viss Azure-prenumeration.
* **Stöd för betala per användning, MSDN, monetära åtaganden och krediterbjudanden (EA och [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-pricelist#get-prices-by-using-the-azure-rate-card) stöds inte)** -API: et tillhandahåller Azure erbjudandet på servernivå rate information.  Anroparen av detta API måste klara i erbjudandeinformation att få information om resursen och priser. Vi kan för närvarande inte att tillhandahålla EA-priser eftersom EA erbjudanden har anpassat kostnader per registrering. 

## <a name="scenarios"></a>Scenarier
Här följer några scenarier som möjliggörs med en kombination av användning och RateCard-APIs:

* **Azure-kostnader under månaden** – Använd en kombination av användning och RateCard APIs du får bättre insikter om ditt moln spendera under månaden. Du kan analysera per timme och dagliga buckets för användning och avgifter beräkningar.
* **Ställa in aviseringar** – Använd användning och RateCard-APIs för att få uppskattade molnförbrukning och kostnader och konfigurera resurs- eller monetära-baserade aviseringar.
* **Förutsäga faktura** – Get din uppskattade förbrukning och molnet spendera och använda machine learning-algoritmer för att förutsäga fakturan skulle vara i slutet av faktureringsperioden.
* **Före förbrukningskostnad analysis** – Använd RateCard-API för att förutspå hur mycket din faktura är för din förväntade användning när du flyttar dina arbetsbelastningar till Azure. Om du har befintliga arbetsbelastningar i andra moln eller privata moln du kan också mappa din användning med Azure priserna för att få en bättre uppfattning av Azure-utgifter. Den här beräkningen ger dig möjlighet att pivotera erbjudande, och jämför och kontrastera mellan olika erbjudandetyper utöver betala per användning, som summa i förskott och kredit. API: et även ger dig möjlighet att se skillnaderna kostnaden per region och kan du göra en vad om kostnadsanalys som hjälper dig att distributionsbeslut.
* **Konsekvensanalys** -
  
  * Du kan fastställa om det är mer kostnadseffektivt att köra arbetsbelastningar i en annan region eller på en annan konfiguration av Azure-resursen. Kostnader för Azure-resurs kan variera beroende på Azure-region du använder.
  * Du kan också bestämma om en annan typ av Azure-erbjudande ger ett bättre pris på en Azure-resurs.
  
## <a name="partner-solutions"></a>Partnerlösningar
[Cloud Cruiser och Microsoft Azure Billing API-Integration](billing-usage-rate-card-partner-solution-cloudcruiser.md) beskriver hur [Cloud Cruiser Express för Azure-paket](http://www.cloudcruiser.com/partners/microsoft/) fungerar direkt från Windows Azure Pack (WAP)-portalen. Du kan smidigt hantera drift och ekonomi aspekter av Microsoft Azures privata eller värdbaserade offentliga moln från ett enda användargränssnitt.   

## <a name="next-steps"></a>Nästa steg
* Kolla in kodexemplen på GitHub:
  * [Kodexempel för API för faktura](https://go.microsoft.com/fwlink/?linkid=845124)

  * [Kodexempel för API för användning](https://github.com/Azure-Samples/billing-dotnet-usage-api)

  * [Kodexempel för API för RateCard](https://github.com/Azure-Samples/billing-dotnet-ratecard-api)

* Läs mer om Azure Resource Manager i [översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). 



