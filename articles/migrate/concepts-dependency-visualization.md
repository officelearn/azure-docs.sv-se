---
title: Beroendevisualisering i Azure Migrate
description: Innehåller en översikt över bedömnings beräkningar i Server Assessment service i Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: hamusa
ms.openlocfilehash: 75b2120b9fef904114d532e83d571c08e1a1034d
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75772320"
---
# <a name="dependency-visualization"></a>Visualisering av beroenden

I den här artikeln beskrivs funktionen för beroende visualisering i Azure Migrate: Server bedömning.

Beroende visualisering hjälper dig att förstå beroenden på datorer som du vill utvärdera och migrera. Du använder vanligt vis beroende mappning när du vill utvärdera datorer med högre Tillförlitlighets nivåer.

- I Azure Migrate: Server utvärdering samlar du ihop datorer i grupper för utvärdering. Grupper består vanligt vis av datorer som du vill migrera tillsammans, och beroende visualiseringen hjälper dig att kontrol lera maskin beroenden, så att du kan gruppera datorerna på ett korrekt sätt.
- Med visualisering kan du identifiera beroende system som behöver migreras tillsammans. Du kan identifiera om system som körs fortfarande används eller om system kan tas ur bruk i stället för att migreras.
- Visualisering av beroenden hjälper till att se till att ingenting är kvar bakom och Undvik oväntade avbrott under migreringen.
- Den här funktionen är särskilt användbar om du inte är helt medveten om datorer som är en del av appar och därför bör migreras tillsammans till Azure.


> [!NOTE]
> Funktionen för beroendevisualisering är inte tillgänglig i Azure Government.

## <a name="agent-based-and-agentless"></a>Agent-baserad och agent utan agent

Det finns två alternativ för att distribuera beroende visualisering:

- **Beroende visualisering för agent utan agent**: det här alternativet är för närvarande en för hands version och är endast tillgängligt för virtuella VMware-datorer. Du behöver inte installera några agenter på datorer. 
    - Det fungerar genom att samla in data för TCP-anslutningen från datorer som den är aktive rad för. [Läs mer](how-to-create-group-machine-dependencies-agentless.md).
När beroende identifiering har startats samlar enheten in data från datorer vid ett avsöknings intervall på fem minuter.
    - Följande data samlas in:
        - TCP-anslutningar
        - Namn på processer som har aktiva anslutningar
        - Namn på installerade program som kör ovanstående processer
        - Nej. anslutningar som identifieras vid varje avsöknings intervall
- **Agent-baserad beroende visualisering**: om du vill använda en agent-baserad beroende visualisering måste du hämta och installera följande agenter på varje lokal dator som du vill analysera.  
    - [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) måste vara installerad på varje dator. [Läs mer](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-mma) om hur du installerar MMA-agenten.
    - [Beroende agenten](../azure-monitor/platform/agents-overview.md#dependency-agent) måste installeras på varje dator. [Läs mer](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies#install-the-dependency-agent) om hur du installerar beroende agenten.
    - Om du har datorer utan Internetanslutning måste du dessutom ladda ned och installera Log Analytics-gatewayen på dem.

## <a name="agent-based-requirements"></a>Agent-baserade krav

### <a name="what-do-i-need-to-deploy-dependency-visualization"></a>Vad behöver jag för att distribuera beroende visualisering?

Innan du distribuerar beroende visualisering bör du ha ett Azure Migrate-projekt på plats, med verktyget Azure Migrate: Server bedömning som har lagts till i projektet. Du kan distribuera beroende visualisering när du har konfigurerat en Azure Migrate-apparat för att identifiera dina lokala datorer.

[Läs mer](how-to-assess.md) om hur du lägger till verktyget och hur du distribuerar en installation för [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md)eller fysiska servrar.


### <a name="how-does-it-work"></a>Hur fungerar det?

Azure Migrate använder [tjänstkarta](../operations-management-suite/operations-management-suite-service-map.md) -lösningen i [Azure Monitor loggar](../log-analytics/log-analytics-overview.md) för beroende visualisering.

- Om du vill utnyttja beroende visualiseringen måste du associera en Log Analytics arbets yta (ny eller befintlig) med ett Azure Migrate projekt.
- Arbets ytan måste vara i samma prenumeration som du skapar Azure Migrate-projektet i.
- Azure Migrate stöder arbets ytor som finns i regionerna östra USA, Sydostasien och Europa, västra. Det går inte att koppla arbets ytor i andra regioner till ett projekt. Observera också att arbets ytan måste vara i en region där [tjänstkarta stöds](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites).
- Det går inte att ändra arbets ytan för ett Azure Migrate projekt när den har lagts till.
- I Log Analytics taggas arbets ytan som är kopplad till Azure Migrate med projekt nyckeln för migreringen och projekt namnet.

    ![Navigera Log Analytics arbets ytan](./media/concepts-dependency-visualization/oms-workspace.png)



### <a name="do-i-need-to-pay-for-it"></a>Behöver jag betala för det?

Beroende visualisering kräver Tjänstkarta och en tillhör ande Log Analytics arbets yta. 

- Tjänstkarta-lösningen debiteras inga avgifter för de första 180 dagarna. Detta är från den dag som du kopplade Log Analytics arbets ytan till Azure Migrate projektet.
- Efter 180 dagar kommer standard Log Analytics avgifter att gälla.
- Om du använder någon annan lösning än Tjänstkarta i den associerade Log Analytics arbets ytan debiteras [standard Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/) avgifter.
- När Azure Migrate-projektet tas bort, tas inte arbets ytan bort tillsammans med den. När du har tagit bort projektet är Tjänstkarta användningen inte kostnads fri, och varje nod debiteras enligt den betalda nivån i Log Analytics arbets ytan.

Mer information om priser för Azure Migrate finns [här](https://azure.microsoft.com/pricing/details/azure-migrate/).

> [!NOTE]
> Om du har projekt som du har skapat innan du Azure Migrate allmän tillgänglighet den 28 februari 2018 kan du ha tillkommer ytterligare Tjänstkarta avgifter. För att säkerställa att du bara betalar efter 180 dagar rekommenderar vi att du skapar ett nytt projekt, eftersom befintliga arbets ytor före allmän tillgänglighet fortfarande kan debiteras.



### <a name="how-do-i-manage-the-workspace"></a>Hur gör jag för att hantera arbets ytan?

- När du registrerar agenter på arbets ytan använder du det ID och den nyckel som tillhandahålls av Azure Migrate-projektet.
- Du kan använda Log Analytics arbets ytan utanför Azure Migrate.
- Om du tar bort det associerade Azure Migrate-projektet tas arbets ytan inte bort automatiskt. Du måste [ta bort den manuellt](../azure-monitor/platform/manage-access.md).
- Ta inte bort arbets ytan som skapats av Azure Migrate, om du inte tar bort Azure Migrate-projektet. Om du gör det fungerar inte beroende visualiserings funktionen som förväntat.

## <a name="next-steps"></a>Nästa steg
- [Gruppera datorer med dator beroenden](how-to-create-group-machine-dependencies.md)
- [Lär dig mer](common-questions-discovery-assessment.md#what-is-dependency-visualization) om de vanliga frågorna om beroende visualisering.


