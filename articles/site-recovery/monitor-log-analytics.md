---
title: Övervaka Azure-webbplatsåterställning med Azure-övervakarloggar
description: Lär dig hur du övervakar Azure Site Recovery med Azure Monitor Logs (Log Analytics)
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: raynew
ms.openlocfilehash: f20d0d38a7fbd831d3e97a69373bac04b9b330aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133410"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>Övervaka Site Recovery med Azure Monitor-loggar

I den här artikeln beskrivs hur du övervakar datorer som replikeras av Azure [Site Recovery,](site-recovery-overview.md)använder [Azure Monitor Logs](../azure-monitor/platform/data-platform-logs.md)och [Log Analytics](../azure-monitor/log-query/log-query-overview.md).

Azure Monitor Logs tillhandahåller en loggdataplattform som samlar in aktivitets- och diagnostikloggar, tillsammans med andra övervakningsdata. I Azure Monitor Logs använder du Log Analytics för att skriva och testa loggfrågor och för att interaktivt analysera loggdata. Du kan visualisera och fråga loggresultat och konfigurera aviseringar för att vidta åtgärder baserat på övervakade data.

För Site Recovery kan du Azure Monitor Loggar som hjälper dig att göra följande:

- **Övervaka hälsa och status för webbplatsåterställning**. Du kan till exempel övervaka replikeringshälsa, test redundansstatus, platsåterställningshändelser, återställningspunktmål (RPO: er) för skyddade datorer och disk-/dataändringshastigheter.
- **Ställ in aviseringar för webbplatsåterställning**. Du kan till exempel konfigurera aviseringar för datorns hälsotillstånd, status för testundanpassning eller platsåterställningsstatus.

Att använda Azure Monitor-loggar med platsåterställning stöds för Azure till Azure-replikering och **Azure to Azure** **VMware VM/fysisk server till Azure-replikering.**

> [!NOTE]
> Om du vill hämta informationsloggarna för omsättnings- och uppladdningshastigheten för VMware och fysiska datorer måste du installera en Microsoft-övervakningsagent på Process Server. Den här agenten skickar loggarna för replikeringsdatorerna till arbetsytan. Den här funktionen är endast tillgänglig för 9.30 mobility agent version och framåt.

## <a name="before-you-start"></a>Innan du börjar

Du behöver det här:

- Minst en maskin skyddad i ett Recovery Services-valv.
- En Log Analytics-arbetsyta för att lagra loggar för webbplatsåterställning. [Läs mer om](../azure-monitor/learn/quick-create-workspace.md) hur du konfigurerar en arbetsyta.
- En grundläggande förståelse för hur du skriver, kör och analyserar loggfrågor i Log Analytics. [Läs mer](../azure-monitor/log-query/get-started-portal.md).

Vi rekommenderar att du granskar [vanliga övervakningsfrågor](monitoring-common-questions.md) innan du börjar.

## <a name="configure-site-recovery-to-send-logs"></a>Konfigurera platsåterställning för att skicka loggar

1. Klicka på **Diagnostikinställningar** > **Lägg till diagnostikinställning**i valvet .

    ![Välj diagnostikloggning](./media/monitoring-log-analytics/add-diagnostic.png)

2. Ange ett namn i **Diagnostikinställningar**och markera kryssrutan **Skicka till Logganalys**.
3. Välj prenumerationen för Azure Monitor Logs och arbetsytan Logganalys.
4. Välj **Azure Diagnostics** i växlingsknappen.
5. Välj alla loggar med prefixet **AzureSiteRecovery**i logglistan . Klicka sedan på **OK**.

    ![Välj arbetsyta](./media/monitoring-log-analytics/select-workspace.png)

Platsåterställningsloggarna börjar matas in i en tabell (**AzureDiagnostics**) på den valda arbetsytan.

## <a name="configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs"></a>Konfigurera Microsofts övervakningsagent på Process Server för att skicka logger för omsättnings- och överföringshastighet

Du kan samla in information om dataomsättningshastighet och information om uppladdningshastighet för dina VMware/fysiska datorer lokalt. För att aktivera detta måste en Microsoft-övervakningsagent installeras på Process Server.

1. Gå till log analytics-arbetsytan och klicka på **Avancerade inställningar**.
2. Klicka på sidan **Anslutna källor** och välj **windowsservrar**ytterligare .
3. Hämta Windows Agent (64 bitar) på Process Server. 
4. [Hämta arbetsyte-ID och nyckel](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key)
5. [Konfigurera agent för att använda TLS 1.2](../azure-monitor/platform/agent-windows.md#configure-agent-to-use-tls-12)
6. [Slutför agentinstallationen](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard) genom att ange det erhållna arbetsyte-ID:t och nyckeln.
7. När installationen är klar går du till Log Analytics arbetsyta och klickar på **Avancerade inställningar**. Gå till **sidan Data** och klicka vidare på Windows **Prestandaräknare**. 
8. Klicka på **'+'** för att lägga till följande två räknare med samplingsintervall på 300 sekunder:

        ASRAnalytics(*)\SourceVmChurnRate 
        ASRAnalytics(*)\SourceVmThrpRate 

Data för omsättning och uppladdningshastighet börjar matas in på arbetsytan.


## <a name="query-the-logs---examples"></a>Fråga loggarna - exempel

Du hämtar data från loggar med hjälp av loggfrågor skrivna med [Kusto-frågespråket](../azure-monitor/log-query/get-started-queries.md). Det här avsnittet innehåller några exempel på vanliga frågor som du kan använda för övervakning av webbplatsåterställning.

> [!NOTE]
> Några av exemplen använder **replicationProviderName_s** inställd på **A2A**. Detta hämtar virtuella Azure-datorer som replikeras till en sekundär Azure-region med hjälp av Site Recovery. I de här exemplen kan du ersätta **A2A** med **InMageAzureV2**om du vill hämta lokala virtuella datorer för VMware eller fysiska servrar som replikeras till Azure med hjälp av Site Recovery.


### <a name="query-replication-health"></a>Hälsotillstånd för frågereplikering

Den här frågan ritar ett cirkeldiagram för den aktuella replikeringshälsan för alla skyddade virtuella Azure-datorer, uppdelade i tre tillstånd: Normal, Varning eller Kritisk.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>Version av tjänsten Query Mobility

Den här frågan ritar ett cirkeldiagram för virtuella Azure-datorer som replikeras med Site Recovery, uppdelad efter den version av Mobilitetsagenten som de kör.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , agentVersion_s  
| summarize count() by agentVersion_s  
| render piechart 
```

### <a name="query-rpo-time"></a>Fråge-RPO-tid

Den här frågan ritar ett stapeldiagram över virtuella Azure-datorer som replikeras med Site Recovery, uppdelade efter återställningspunktmål (RPO): Mindre än 15 minuter, mellan 15-30 minuter, mer än 30 minuter.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| extend RPO = case(rpoInSeconds_d <= 900, "<15Min",   
rpoInSeconds_d <= 1800, "15-30Min", ">30Min")  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , RPO  
| summarize Count = count() by RPO  
| render barchart 
```

![Fråga RPO](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>Jobb för återställning av frågewebbplatser

Den här frågan hämtar alla jobb för återställning av webbplats (för alla scenarier för haveriberedskap), som utlösts under de senaste 72 timmarna och deras slutförandetillstånd.

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>Frågeplatsåterställningshändelser

Den här frågan hämtar alla site recovery-händelser (för alla katastrofåterställningsscenarier) som tagits upp under de senaste 72 timmarna, tillsammans med deras allvarlighetsgrad. 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>Felläge för frågetest (cirkeldiagram)

Den här frågan ritar ett cirkeldiagram för test redundansstatus för virtuella Azure-datorer som replikeras med Site Recovery.

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , Resource, failoverHealth_s  
| summarize count() by failoverHealth_s  
| render piechart 
```

### <a name="query-test-failover-state-table"></a>Felläge för frågetest (tabell)

Den här frågan ritar en tabell för test redundansstatus för virtuella Azure-datorer som replikeras med Site Recovery.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>RPO för frågemaskin

Den här frågan ritar ett trenddiagram som spårar RPO för en viss Azure VM (ContosoVM123) under de senaste 72 timmarna.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![RPO för frågemaskin](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-an-azure-vm"></a>Frågedataändringshastighet (omsättning) och uppladdningshastighet för en Virtuell Azure-dator

Den här frågan ritar ett trenddiagram för en specifik Azure VM (ContosoVM123), som representerar dataändringshastigheten (skrivbyte per sekund) och dataöverföringshastighet. 

```
AzureDiagnostics   
| where Category in ("AzureSiteRecoveryProtectedDiskDataChurn", "AzureSiteRecoveryReplicationDataUploadRate")   
| extend CategoryS = case(Category contains "Churn", "DataChurn",   
Category contains "Upload", "UploadRate", "none")  
| extend InstanceWithType=strcat(CategoryS, "_", InstanceName_s)   
| where TimeGenerated > ago(24h)   
| where InstanceName_s startswith "ContosoVM123"   
| project TimeGenerated , InstanceWithType , Churn_MBps = todouble(Value_s)/1048576   
| render timechart  
```
![Ändring av frågedata](./media/monitoring-log-analytics/example3.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-a-vmware-or-physical-machine"></a>Frågedataändringshastighet (omsättning) och uppladdningshastighet för en VMware eller fysisk dator

> [!Note]
> Se till att du ställer in övervakningsagenten på Process Server för att hämta dessa loggar. Se [steg för att konfigurera övervakningsagenten](#configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs).

Den här frågan ritar ett trenddiagram för en specifik **diskdisk0** för ett replikerat objekt **win-9r7sfh9qlru**, som representerar dataändringshastigheten (skrivbyte per sekund) och dataöverföringshastighet. Du hittar disknamnet på **diskbladet** för det replikerade objektet i återställningstjänstvalvet. Förekomstnamn som ska användas i frågan är DNS-namnet på datorn följt av _ och disknamn som i det här exemplet.

```
Perf
| where ObjectName == "ASRAnalytics"
| where InstanceName contains "win-9r7sfh9qlru_disk0"
| where TimeGenerated >= ago(4h) 
| project TimeGenerated ,CounterName, Churn_MBps = todouble(CounterValue)/5242880 
| render timechart
```
Process Server skickar dessa data var femte minut till log analytics-arbetsytan. Dessa datapunkter representerar det genomsnittliga beräknat i 5 minuter.

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>Sammanfattning av frågekatastrofåterställning (Azure till Azure)

Den här frågan ritar en sammanfattningstabell för virtuella Azure-datorer som replikeras till en sekundär Azure-region.  Den visar VM-namn, replikerings- och skyddsstatus, RPO, test redundansstatus, mobilitetsagentversion, eventuella aktiva replikeringsfel och källplatsen.

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>Sammanfattning av frågekatastrofåterställning (VMware/fysiska servrar)

Den här frågan ritar en sammanfattningstabell för virtuella datorer med VMware och fysiska servrar som replikeras till Azure.  Den visar datorns namn, replikerings- och skyddsstatus, RPO, test redundansstatus, mobilitetsagentversion, eventuella aktiva replikeringsfel och relevant processserver.

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>Ställ in aviseringar - exempel

Du kan ställa in site recovery-aviseringar baserat på Azure Monitor-data. [Läs mer](../azure-monitor/platform/alerts-log.md#managing-log-alerts-from-the-azure-portal) om hur du konfigurerar loggaviseringar. 

> [!NOTE]
> Några av exemplen använder **replicationProviderName_s** inställd på **A2A**. Detta anger aviseringar för virtuella Azure-datorer som replikeras till en sekundär Azure-region. I de här exemplen kan du ersätta **A2A** med **InMageAzureV2** om du vill ställa in aviseringar för lokala virtuella datorer med VMware eller fysiska servrar som replikeras till Azure.

### <a name="multiple-machines-in-a-critical-state"></a>Flera datorer i kritiskt tillstånd

Ställ in en avisering om mer än 20 replikerade Virtuella Azure-datorer hamnar i ett kritiskt tillstånd.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
För aviseringen anger du **Tröskelvärdet** till 20.

### <a name="single-machine-in-a-critical-state"></a>En maskin i kritiskt tillstånd

Ställ in en avisering om en specifik replikerad Azure VM går in i ett kritiskt tillstånd.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
För aviseringen anger du **Tröskelvärdet** till 1.

### <a name="multiple-machines-exceed-rpo"></a>Flera maskiner överskrider RPO

Ställ in en avisering om RPO för mer än 20 Virtuella Azure-datorer överstiger 30 minuter.
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
För aviseringen anger du **Tröskelvärdet** till 20.

### <a name="single-machine-exceeds-rpo"></a>En maskin överskrider RPO

Ställ in en avisering om RPO för en enda Azure VM överstiger 30 minuter.

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
För aviseringen anger du **Tröskelvärdet** till 1.

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>Test redundans för flera datorer överstiger 90 dagar

Ställ in en avisering om den senaste lyckade testundansen var mer än 90 dagar, för mer än 20 virtuella datorer. 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
För aviseringen anger du **Tröskelvärdet** till 20.

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>Test redundans för en maskin överstiger 90 dagar

Ställ in en avisering om den senaste lyckade testundansen för en viss virtuell dator var mer än 90 dagar sedan.
```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| where name_s == "ContosoVM123"  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
För aviseringen anger du **Tröskelvärdet** till 1.

### <a name="site-recovery-job-fails"></a>Jobbet för återställning av webbplats misslyckas

Ställ in en avisering om ett site recovery-jobb (i det här fallet jobbet För reprotect) misslyckas för alla scenarion för återställning av webbplatser under den sista dagen. 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

För aviseringen anger du **Tröskelvärdet** till 1 och **Period** till 1440 minuter för att kontrollera fel under den sista dagen.

## <a name="next-steps"></a>Nästa steg

[Läs mer om](site-recovery-monitor-and-troubleshoot.md) inbyggd övervakning av webbplatsåterställning.
