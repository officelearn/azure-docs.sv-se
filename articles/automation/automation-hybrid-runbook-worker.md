---
title: Azure Automation Hybrid Runbook Worker
description: Den här artikeln innehåller information om hur du installerar och använder Hybrid Runbook Worker, som är en funktion i Azure Automation som du kan använda för att köra runbooks på datorer i ditt lokala datacenter eller molnprovider.
services: automation
ms.subservice: process-automation
ms.date: 04/05/2019
ms.topic: conceptual
ms.openlocfilehash: 1e4470ce5ac69390cf8d361577b9ebf0013e4e51
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405794"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>Automatisera resurser i ditt datacenter eller i molnet med hjälp av Hybrid Runbook Worker

Runbooks i Azure Automation kanske inte har åtkomst till resurser i andra moln eller i din lokala miljö eftersom de körs på Azure-molnplattformen. Du kan använda hybridkörningsarbetsfunktionen i Azure Automation för att köra runbooks direkt på datorn som är värd för rollen och mot resurser i miljön för att hantera dessa lokala resurser. Runbooks lagras och hanteras i Azure Automation och levereras sedan till en eller flera tilldelade datorer.

Följande bild illustrerar den här funktionen:

![Översikt över Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

Varje Hybrid Runbook Worker är medlem i en hybridkörningsarbetsgrupp som du anger när du installerar agenten. En grupp kan innehålla en enda agent, men du kan installera flera agenter i en grupp för hög tillgänglighet. Varje dator kan vara värd för en Hybrid Worker-rapportering till ett Automation-konto.

När du startar en runbook på en Hybrid Runbook Worker anger du den grupp som körs på. Varje anställd i gruppen av söker efter Azure Automation för att se om det finns några jobb tillgängliga. Om ett jobb är tillgängligt tar den första arbetaren som får jobbet det. Bearbetningstiden för jobbkön beror på hybridarbetarens maskinvaruprofil och belastning. Du kan inte ange en viss arbetare. Hybrid Runbook Workers delar inte många av de gränser som Azure sandlådor har. De har inte samma begränsningar för diskutrymme, minne eller nätverksuttag. Hybrid Runbook Workers begränsas endast av resurserna på Hybrid Runbook Worker själv. Dessutom delar Hybrid Runbook Workers inte den 180-minuters [beskärda](automation-runbook-execution.md#fair-share) deltidsgränsen som Azure sandlådor har. Mer information om servicegränserna för Azure-sandlådor och Hybrid Runbook Workers finns i [jobbgränserna](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

## <a name="install-a-hybrid-runbook-worker"></a>Installera en hybridkörningsarbetare

Processen för att installera en Hybrid Runbook Worker beror på operativsystemet. Tabellen nedan definierar distributionstyperna.

|Operativsystem  |Distributionstyper  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Manuell](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

Den rekommenderade installationsmetoden är att använda en Automation-runbook för att helt automatisera processen med att konfigurera en Windows-dator. Den andra metoden är att följa en steg-för-steg-procedur för att manuellt installera och konfigurera rollen. För Linux-datorer kör du ett Python-skript för att installera agenten på datorn.

> [!NOTE]
> Om du vill hantera konfigurationen av servrarna som stöder hybridkörningsarbetsrollen med DSC (Desired State Configuration) måste du lägga till servrarna som DSC-noder. Mer information om hur du läser in dem för hantering med DSC finns i [Onboarding-datorer för hantering av Azure Automation DSC](automation-dsc-onboarding.md).
>
>Om du aktiverar [lösningen Update Management](automation-update-management.md)konfigureras alla datorer som är anslutna till din Azure Log Analytics-arbetsyta automatiskt som en Hybrid Runbook Worker för att stödja runbooks som ingår i den här lösningen. Datorn är dock inte registrerad hos några Hybrid Worker-grupper som redan har definierats i ditt Automation-konto. Datorn kan läggas till i en Hybrid Runbook Worker-grupp i ditt Automation-konto för att stödja Automation-runbooks så länge du använder samma konto för både lösningen och Hybrid Runbook Worker-gruppmedlemskapet. Den här funktionen har lagts till i version 7.2.12024.0 av Hybrid Runbook Worker.

Granska [informationen för att planera nätverket](#network-planning) innan du börjar distribuera en Hybrid Runbook Worker. När du har distribuerat arbetaren granskar du [Kör runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) för att lära dig hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller annan molnmiljö.

Datorn kan läggas till i en Hybrid Runbook Worker-grupp i ditt Automation-konto för att stödja Automation-runbooks så länge du använder samma konto för både lösningen och Hybrid Runbook Worker-gruppmedlemskapet. Den här funktionen har lagts till i version 7.2.12024.0 av Hybrid Runbook Worker.

## <a name="a-nameremove-a-hybrid-runbook-workerremove-a-hybrid-runbook-worker-from-an-on-premises-computer"></a><a name="remove-a-hybrid-runbook-worker">Ta bort en hybridkörningsarbetare från en lokal dator

Du kan ta bort en Hybrid Runbook Worker från en lokal dator enligt beskrivningen i det här avsnittet för Windows och Linux.

### <a name="remove-the-worker-on-windows"></a>Ta bort arbetaren i Windows

1. Gå till ditt Automation-konto i Azure-portalen.
2. Under **Kontoinställningar**väljer du **Nycklar** och noterar värdena för **URL** och **Primär åtkomstnyckel**.

3. Öppna en PowerShell-session i administratörsläge och kör följande kommando med url:en och de primära nyckelvärdena för åtkomst. Använd `Verbose` parametern för en detaljerad logg över borttagningsprocessen. Om du vill ta bort inaktuella datorer `machineName` från hybridarbetsgruppen använder du den valfria parametern.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="remove-the-worker-on-linux"></a>Ta bort arbetaren på Linux

Du kan använda `ls /var/opt/microsoft/omsagent` kommandot på Hybrid Runbook Worker för att hämta arbetsyte-ID. En mapp skapas med namnet workspace-ID.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Den här koden tar inte bort Microsoft Monitoring Agent från datorn. Den tar bara bort funktionaliteten och konfigurationen för hybridkörningsarbetsrollen.

## <a name="remove-a-hybrid-worker-group"></a>Ta bort en Hybrid Worker-grupp

Om du vill ta bort en hybridkörningsarbetsgrupp måste du först ta bort Hybrid Runbook Worker från alla datorer som är medlemmar i gruppen. Använd sedan följande steg för att ta bort gruppen:

1. Öppna Automation-kontot i Azure-portalen.
2. Välj **Hybridarbetsgrupper** under **Processautomation**. Markera den grupp som du vill ta bort. Egenskapssidan för den gruppen visas.

   ![Sidan Egenskaper](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. På egenskapssidan för den markerade gruppen väljer du **Ta bort**. Ett meddelande ber dig bekräfta den här åtgärden. Välj **Ja** om du är säker på att du vill fortsätta.

   ![Bekräftelsemeddelande](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Den här processen kan ta flera sekunder att slutföra. Du kan spåra förloppet under **Meddelanden** på menyn.

## <a name="configure-your-network"></a><a name="network-planning"></a>Konfigurera nätverket

### <a name="hybrid-worker-role"></a>Rollen Hybridarbetare

För att Hybrid Runbook Worker ska kunna ansluta till och registrera sig med Azure Automation måste den ha åtkomst till portnumret och url:erna som beskrivs i det här avsnittet. Den här åtkomsten är överst till de [portar och webbadresser som krävs för att Microsoft Monitoring Agent](../azure-monitor/platform/agent-windows.md) ska kunna ansluta till Azure Monitor-loggar.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Om du använder en proxyserver för kommunikation mellan agenten och Azure Automation-tjänsten kontrollerar du att lämpliga resurser är tillgängliga. Tidsgränsen för begäranden från Hybrid Runbook Worker och Automation-tjänsterna är 30 sekunder. Efter 3 försök misslyckas begäran. Om du använder en brandvägg för att begränsa åtkomsten till internet måste du konfigurera brandväggen så att den tillåter åtkomst. Om du använder Log Analytics-gatewayen som proxy kontrollerar du att den är konfigurerad för hybridarbetare. Instruktioner om hur du gör detta finns i [Konfigurera Log Analytics-gatewayen för Automation Hybrid Workers](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

Följande port och url:er krävs för att hybridkörningsarbetsrollen ska kunna kommunicera med Automation:

* Port: Endast TCP 443 krävs för utgående internet.
* Global WEBBADRESS: *.azure-automation.net
* Global URL för US Gov, Virginia: –*.azure-automation.us
* Agenttjänst: https://\<arbetsytaId\>.agentsvc.azure-automation.net

Vi rekommenderar att du använder de adresser som anges när undantag definieras. För IP-adresser kan du hämta [Microsoft Azure Datacenter IP Ranges](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Den här filen uppdateras varje vecka och har de för närvarande distribuerade intervallen och eventuella kommande ändringar av IP-intervallen.

Om du har ett Automation-konto som har definierats för en viss region kan du begränsa kommunikationen till det regionala datacentret. I följande tabell visas DNS-posten för varje region:

| **Regionen** | **DNS-post** |
| --- | --- |
| USA, västra centrala | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| USA, södra centrala |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| USA, östra 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| USA, västra 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |
| Kanada, centrala |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa, västra |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa, norra |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Sydostasien |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Indien, centrala |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Japan, östra |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Australien, centrala |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| Australien, östra |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Sydöstra Australien |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Storbritannien, södra | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Gov, Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

För en lista över region-IP-adresser i stället för regionnamn hämtar du [XML-filen för Azure Datacenter IP-adress](https://www.microsoft.com/download/details.aspx?id=41653) från Microsoft Download Center.

> [!NOTE]
> XML-filen För Azure Datacenter IP-adress visar de IP-adressintervall som används i Microsoft Azure-datacenter. Filen innehåller beräknings-, SQL- och lagringsområden.
>
>En uppdaterad fil publiceras varje vecka. Filen återspeglar de för närvarande distribuerade intervallen och eventuella kommande ändringar av IP-intervallen. Nya intervall som visas i filen används inte i datacenter i minst en vecka.
>
> Det är en bra idé att ladda ner den nya XML-filen varje vecka. Uppdatera sedan webbplatsen för att identifiera tjänster som körs i Azure på rätt sätt. Azure ExpressRoute-användare bör notera att den här filen används för att uppdatera BGP-annonseret (Border Gateway Protocol) för Azure-utrymme under den första veckan i varje månad.

### <a name="update-management"></a>Uppdateringshantering

Utöver de standardadresser och portar som Hybrid Runbook Worker kräver krävs följande adresser specifikt för uppdateringshantering. Kommunikation till dessa adresser sker över port 443.

|Azure, offentlig  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du konfigurerar runbooks för att automatisera processer i ditt lokala datacenter eller annan molnmiljö finns i [Kör runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Mer information om hur du felsöker hybridkörningsarbetare finns i [Felsöka hybridkörningsarbetare](troubleshoot/hybrid-runbook-worker.md#general).