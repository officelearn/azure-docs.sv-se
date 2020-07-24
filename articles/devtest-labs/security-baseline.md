---
title: Azures säkerhets bas linje för Azure DevTest Labs
description: Azures säkerhets bas linje för Azure DevTest Labs
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 4cdfab5df2e97c2bd39c1c104e9552fb940f24d9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87100040"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Azures säkerhets bas linje för Azure DevTest Labs

Azures säkerhets bas linje för Azure DevTest Labs innehåller rekommendationer som hjälper dig att förbättra säkerhets position för din distribution.

Bas linjen för den här tjänsten hämtas från [Azures prestandatest version 1,0](https://docs.microsoft.com/azure/security/benchmarks/overview), som ger rekommendationer om hur du kan skydda dina moln lösningar på Azure.

Mer information finns i [Översikt över Azure Security-bas linjer](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="logging-and-monitoring"></a>Loggning och övervakning

*Mer information finns i [säkerhets kontroll: loggning och övervakning](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: Använd godkända tids källor för synkronisering
**Vägledning:** Microsoft hanterar tids källor för Azure-resurser. Du kan dock hantera tids inställningar för synkronisering för dina beräknings resurser.

I följande artikel finns information om hur du konfigurerar tidssynkronisering för Azure Compute-resurser: [tidssynkronisering för virtuella Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync). 

**Azure Security Center övervakning:** Inte tillgänglig för närvarande

**Ansvar:** Utforskaren

### <a name="22-configure-central-security-log-management"></a>2,2: Konfigurera central hantering av säkerhets loggar
**Vägledning:** Aktivera diagnostikinställningar för Azure aktivitets logg och skicka loggarna till en Log Analytics-arbetsyta, Azure Event Hub eller Azure Storage-konto för arkivering. Aktivitets loggar ger insikt i de åtgärder som utförts på dina Azure DevTest Labs-instanser på hanterings Plans nivån. Med hjälp av Azures aktivitets logg data kan du fastställa "vad, vem och när" för alla Skriv åtgärder (skicka, skicka och ta bort) på hanterings Plans nivån för DevTest Labs-instanserna.

Mer information finns i [skapa diagnostikinställningar för att skicka plattforms loggar och mått till olika mål](../azure-monitor/platform/diagnostic-settings.md).

**Azure Security Center övervakning:** Inte tillgänglig för närvarande

**Ansvar:** Kund

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: Aktivera gransknings loggning för Azure-resurser
**Vägledning:** Aktivera diagnostikinställningar för Azure aktivitets logg och skicka loggarna till en Log Analytics-arbetsyta, Azure Event Hub eller Azure Storage-konto för arkivering. Aktivitets loggar ger insikt i de åtgärder som utförts på dina Azure DevTest Labs-instanser på hanterings Plans nivån. Med hjälp av Azures aktivitets logg data kan du fastställa "vad, vem och när" för alla Skriv åtgärder (skicka, skicka och ta bort) på hanterings Plans nivån för DevTest Labs-instanserna.

Mer information finns i [skapa diagnostikinställningar för att skicka plattforms loggar och mått till olika mål](../azure-monitor/platform/diagnostic-settings.md).

**Azure Security Center övervakning:** Inte tillgänglig för närvarande

**Ansvar:** Kund

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: samla in säkerhets loggar från operativ system
**Vägledning:** Azure DevTest Labs virtuella datorer skapas och ägs av kunden. Det är därför organisationens ansvar att övervaka den. Du kan använda Azure Security Center för att övervaka beräknings-OS. Data som samlas in av Security Center från operativ systemet innehåller OS-typ och version, OS (Windows-händelseloggen), processer som körs, dator namn, IP-adresser och inloggad användare. Log Analytics agent samlar även in krasch dum par-filer.

Mer information finns i följande artiklar: 

- [Så här samlar du in interna värd loggar för virtuella Azure-datorer med Azure Monitor](../azure-monitor/learn/quick-collect-azurevm.md)
- [Förstå Azure Security Center insamling av data](../security-center/security-center-enable-data-collection.md)

**Azure Security Center övervakning:** Ja

**Ansvar:** Kund

### <a name="25-configure-security-log-storage-retention"></a>2,5: Konfigurera säkerhets logg lagrings kvarhållning
***Vägledning:** I Azure Monitor anger du logg kvarhållningsperiod för Log Analytics arbets ytor som är kopplade till dina Azure DevTest Labs instanser enligt organisationens regler för efterlevnad.

Mer information finns i följande artikel: [så här anger du parametrar för logg bevarande](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="26-monitor-and-review-logs"></a>2,6: övervaka och granska loggar
**Vägledning:** Aktivera diagnostiska inställningar för Azure aktivitets logg och skicka loggarna till en Log Analytics-arbetsyta. Kör frågor i Log Analytics till att söka efter termer, identifiera trender, analysera mönster och tillhandahålla många andra insikter baserat på de aktivitets logg data som kan ha samlats in för Azure DevTest Labs.

Mer information finns i följande artiklar:

- [Så här aktiverar du diagnostikinställningar för Azure aktivitets logg](../azure-monitor/platform/diagnostic-settings.md)
- [Samla in och analysera Azure-aktivitets loggar i Log Analytics arbets yta i Azure Monitor](../azure-monitor/platform/activity-log.md)

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: aktivera aviseringar för avvikande aktivitet
**Vägledning:** Använd Azure Log Analytics-arbetsytan för att övervaka och Avisera om avvikande aktiviteter i säkerhets loggar och händelser relaterade till din Azure DevTest Labs.

Mer information finns i följande artikel: så här [aviserar du om Log Analytics-loggdata](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center övervakning:** Inte tillgänglig för närvarande

**Ansvar:** Kund

### <a name="28-centralize-anti-malware-logging"></a>2,8: centralisera loggning mot skadlig kod
**Vägledning:** Inte tillämpligt. Azure DevTest Labs bearbetar eller skapar inte relaterade loggar mot skadlig kod.

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="29-enable-dns-query-logging"></a>2,9: Aktivera loggning av DNS-frågor
**Vägledning:** Inte tillämpligt. Azure DevTest Labs bearbetar eller skapar inte DNS-relaterade loggar.

**Azure Security Center övervakning:** Ej tillämpligt

**Ansvar:** Kund

### <a name="210-enable-command-line-audit-logging"></a>2,10: Aktivera loggning av kommando rads granskning
**Vägledning:** Azure DevTest Labs skapar Azure Compute-datorer som ägs och hanteras av kunden. Använd Microsoft Monitoring Agent på alla virtuella Azure Windows-datorer som stöds för att logga skapande händelsen för processen och fältet kommandorad. För virtuella Azure Linux-datorer som stöds kan du manuellt konfigurera konsol loggning per nod och använda syslog för att lagra data. Använd också Azure Monitor Log Analytics arbets ytan för att granska loggar och köra frågor om loggade data från virtuella Azure-datorer.

- [Datainsamling i Azure Security Center](../security-center/security-center-enable-data-collection.md#data-collection-tier)
- [Så här kör du anpassade frågor i Azure Monitor](../azure-monitor/log-query/get-started-queries.md)
- [Syslog-datakällor i Azure Monitor](../azure-monitor/platform/data-sources-syslog.md)

**Azure Security Center övervakning:** Ja

**Ansvar:** Kund

## <a name="next-steps"></a>Nästa steg
Se följande artikel:

- [Säkerhets aviseringar för miljöer i Azure DevTest Labs](environment-security-alerts.md)