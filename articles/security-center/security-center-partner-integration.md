---
title: Integrera säkerhetslösningar i Azure Security Center | Microsoft Docs
description: Läs hur Azure Security Center integrerar med partners för att förbättra den övergripande säkerheten för dina Azure-resurser.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 6af354da-f27a-467a-8b7e-6cbcf70fdbcb
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2020
ms.author: memildin
ms.openlocfilehash: 48869140ba8cd1a9598562b0057b0005d8fcd9c7
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758020"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Integrera säkerhetslösningar i Azure Security Center
Det här dokumentet hjälper dig att hantera säkerhetslösningar som redan är anslutna till Azure Security Center och lägga till nya.

## <a name="integrated-azure-security-solutions"></a>Integrerade Azure-säkerhetslösningar
Med Security Center är det enkelt att aktivera integrerade säkerhetslösningar i Azure. Fördelarna innefattar:

- **Förenklad distribution**: Security Center erbjuder effektiviserad etablering av integrerade partnerlösningar. För lösningar som antimalware och sårbarhetsbedömning kan Security Center etablera agenten på dina virtuella datorer. För brandväggsenheter kan Security Center ta hand om en stor del av den nätverkskonfiguration som krävs.
- **Integrerade identifieringar**: Säkerhetshändelser från partnerlösningar samlas in automatiskt, aggregeras och visas som en del av Säkerhetscenter-aviseringar och incidenter. Dessa händelser kombineras också med identifieringar från andra källor för att tillhandahålla funktioner för avancerad hotidentifiering.
- **Enhetlig övervakning och hantering av hälsa**: Kunderna kan använda integrerade hälsohändelser för att snabbt övervaka alla partnerlösningar. Grundläggande hantering finns tillgängligt med enkel åtkomst till avancerad konfiguration med hjälp av partnerlösningen.

För närvarande inkluderar integrerade säkerhetslösningar sårbarhetsbedömning av [Qualys](https://www.qualys.com/public-cloud/#azure) och [Rapid7](https://www.rapid7.com/products/insightvm/) och Microsoft Application Gateway Web Application Firewall.

> [!NOTE]
> Security Center installerar inte Log Analytics-agenten på partnervirvliga enheter eftersom de flesta säkerhetsleverantörer förbjuder externa agenter som körs på sina enheter.

Mer information om integrering av sårbarhetsskanningsverktyg från Qualys, inklusive en inbyggd skanner som är tillgänglig för standardnivåkunder, finns i: 

- [Integrerad sårbarhetsskanner för virtuella datorer](built-in-vulnerability-assessment.md).
- [Distribuera en lösning för sårbarhetsskanning av partner](partner-vulnerability-assessment.md).

Security Center erbjuder också sårbarhetsanalys för din:

* SQL-databaser – se [Utforska sårbarhetsbedömningsrapporter i instrumentpanelen för sårbarhetsbedömning](security-center-iaas-advanced-data.md#explore-vulnerability-assessment-reports)
* Azure Container Registry avbildningar - se [Azure Container Registry integration med Security Center (Preview)](azure-container-registry-integration.md)

## <a name="how-security-solutions-are-integrated"></a>Så här integreras säkerhetslösningar
Azure-säkerhetslösningar som distribueras från Security Center ansluts automatiskt. Du kan också ansluta andra säkerhetsdatakällor, inklusive datorer som körs lokalt eller i andra moln.

[![Integrerings av partnerlösningar](./media/security-center-partner-integration/security-solutions-page.png)](./media/security-center-partner-integration/security-solutions-page.png#lightbox)

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>Hantera integrerade Azure-säkerhetslösningar och andra datakällor

1. Öppna **Security Center**från [Azure-portalen](https://azure.microsoft.com/features/azure-portal/).

1. På Security Centers meny väljer du **Säkerhetslösningar**.

På sidan **Säkerhetslösningar** kan du se hälsotillståndet för integrerade Azure-säkerhetslösningar och köra grundläggande hanteringsuppgifter.

### <a name="connected-solutions"></a>Anslutna lösningar

Avsnittet **Anslutna lösningar** innehåller säkerhetslösningar som för närvarande är anslutna till Security Center. Det visar också hälsostatus för varje lösning.  

![Anslutna lösningar](./media/security-center-partner-integration/connected-solutions.png)

Status för en partnerlösning kan vara:

* **Friska** (gröna) - inga hälsofrågor.
* **Ohälsosamma** (röda) - det finns en hälsofråga som kräver omedelbar uppmärksamhet.
* **Stoppad rapportering** (orange) - lösningen har slutat rapportera sin hälsa.
* **Inte rapporterats** (grå) - lösningen har inte rapporterat något ännu och inga hälsodata är tillgängliga. Statusen för en lösning kan inte rapporteras om den har anslutits nyligen och fortfarande distribueras.

> [!NOTE]
> Om hälsostatusdata inte är tillgängliga visar Security Center datum och tid för den senaste händelsen som togs emot för att ange om lösningen rapporterar eller inte. Om inga hälsodata är tillgängliga och inga aviseringar har tagits emot inom de senaste 14 dagarna anger Security Center att lösningen är fel eller inte rapporterar.
>
>

Välj **VISA** för ytterligare information och alternativ, till exempel:

   - **Lösningskonsol** - Öppnar hanteringsupplevelsen för den här lösningen.
   - **Länka VM** - Öppnar sidan Länkprogram. Här kan du ansluta resurser till partnerlösningen.
   - **Ta bort lösningen**
   - **Konfigurera**

   ![Information om partnerlösning](./media/security-center-partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>Identifierade lösningar

Security Center identifierar automatiskt säkerhetslösningar som körs i Azure men inte är anslutna till Security Center och visar lösningarna i avsnittet **Identifierade lösningar.** Dessa lösningar omfattar Azure-lösningar, till exempel [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)och partnerlösningar.

> [!NOTE]
> Standardnivån av Security Center krävs på prenumerationsnivån för funktionen identifierade lösningar. Se [Priser](security-center-pricing.md) om du vill veta mer om prisnivåerna.
>

Välj **CONNECT** under en lösning som kan integreras med Security Center och meddelas om säkerhetsvarningar.

### <a name="add-data-sources"></a>Lägg till datakällor

Avsnittet **Lägg till datakällor** innehåller andra tillgängliga datakällor som kan anslutas. Om du vill få anvisningar om att lägga till data från någon av dessa källor klickar du på **LÄGG TILL**.

![Datakällor](./media/security-center-partner-integration/add-data-sources.png)



## <a name="next-steps"></a>Nästa steg

Den här artikeln berättade om hur man integrerar partnerlösningar i Security Center. Relaterad information finns i följande artiklar:

* [Exportera säkerhetsvarningar och rekommendationer](continuous-export.md). Lär dig hur du konfigurerar en integrering med Azure Sentinel eller någon annan SIEM.
* [Övervakning av säkerhetshälsa i Security Center](security-center-monitoring.md). Lär dig att övervaka hälsotillståndet för dina Azure-resurser.