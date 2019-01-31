---
title: Felsöka saknade data i Azure Active Directory-aktivitetsloggar | Microsoft Docs
description: Ger en lösning till saknade data i Azure Active Directory-aktivitetsloggar.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 1ceb903d5586dbcc824e3ad9de56e609f162e888
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55174490"
---
# <a name="troubleshoot-missing-data-in-the-azure-active-directory-activity-logs"></a>Felsökning: Saknade data i Azure Active Directory-aktivitetsloggar 

## <a name="i-cant-find-audit-logs-for-recent-actions-in-the-azure-portal"></a>Jag hittar inte spårningsloggar för de senaste åtgärderna i Azure-portalen

### <a name="symptoms"></a>Symtom

Jag utförde vissa åtgärder i Azure Portal och förväntade att se granskningsloggarna för dessa åtgärder på bladet `Activity logs > Audit Logs`, men det går inte att hitta dem.

 ![Rapportering](./media/troubleshoot-missing-audit-data/01.png)
 
### <a name="cause"></a>Orsak

Åtgärder visas inte omedelbart i aktivitetsloggarna. Tabellen nedan räknar upp våra svarstidsvärden för aktivitetsloggar. 

| Rapport | &nbsp; | Svarstid (P95) | Svarstid (P99) |
|--------|--------|---------------|---------------|
| Kataloggranskning | &nbsp; | 2 min | 5 min |
| Inloggningsaktivitet | &nbsp; | 2 min | 5 min | 

### <a name="resolution"></a>Lösning

Vänta i mellan 15 minuter och 2 timmar och se om åtgärderna visas i loggen. Om du inte ser loggarna även efter två timmar [skapar du en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) så undersöker vi problemet.

## <a name="i-cant-find-recent-user-sign-ins-in-the-azure-active-directory-sign-ins-activity-log"></a>Det går inte att hitta nyligen utförda användarinloggningar i aktivitetsloggen för inloggningsåtgärder i Azure Active Directory

### <a name="symptoms"></a>Symtom

Jag loggade nyligen in på Azure-portalen och förväntade mig att se inloggningsloggarna för de åtgärderna på bladet `Activity logs > Sign-ins`, men det går inte att hitta dem.

 ![Rapportering](./media/troubleshoot-missing-audit-data/02.png)
 
### <a name="cause"></a>Orsak

Åtgärder visas inte omedelbart i aktivitetsloggarna. Tabellen nedan räknar upp våra svarstidsvärden för aktivitetsloggar. 

| Rapport | &nbsp; | Svarstid (P95) | Svarstid (P99) |
|--------|--------|---------------|---------------|
| Kataloggranskning | &nbsp; | 2 min | 5 min |
| Inloggningsaktivitet | &nbsp; | 2 min | 5 min | 

### <a name="resolution"></a>Lösning

Vänta i mellan 15 minuter och 2 timmar och se om åtgärderna visas i loggen. Om du inte ser loggarna även efter två timmar [skapar du en supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) så undersöker vi problemet.

## <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Jag kan inte se mer än 30 dagars rapportdata i Azure-portalen

### <a name="symptoms"></a>Symtom

Jag kan inte se mer än 30 dagars inloggnings- och granskningsdata från Azure-portalen. Varför? 

 ![Rapportering](./media/troubleshoot-missing-audit-data/03.png)

### <a name="cause"></a>Orsak

Beroende på din licens lagrar Azure Active Directory aktivitetsrapporter under så här lång tid:

| Rapport           | &nbsp; |  Azure AD Kostnadsfri | Azure AD Premium P1 | Azure AD Premium P2 |
| ---              | ----   |  ---           | ---                 | ---                 |
| Kataloggranskning  | &nbsp; |   7 dagar     | 30 dagar             | 30 dagar             |
| Inloggningsaktivitet | &nbsp; | Inte tillgängligt. Du kan komma åt dina egna inloggningar i 7 dagar från det bladet för enskild användare | 30 dagar | 30 dagar             |

Mer information finns i [Kvarhållningsprinciper för rapporter i Azure Active Directory](reference-reports-data-retention.md).  

### <a name="resolution"></a>Lösning

Du har två alternativ för att behålla data längre än 30 dagar. Du kan använda [Azure AD Reporting-API:er](concept-reporting-api.md) för att hämta data programmatiskt och lagra dem i en databas. Du kan även integrera spårningsloggar i ett SIEM-system från tredje part, till exempel Splunk eller SumoLogic.

## <a name="next-steps"></a>Nästa steg

* [Kvarhållning av Azure AD-rapportering](reference-reports-data-retention.md).
* [Svarstider för Azure Active Directory-rapportering](reference-reports-latencies.md).
* [Vanliga frågor och svar om Azure Active Directory-rapportering](reports-faq.md).

