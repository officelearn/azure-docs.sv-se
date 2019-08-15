---
title: Azure Automation Hybrid Runbook Worker
description: Den här artikeln innehåller information om hur du installerar och använder Hybrid Runbook Worker, som är en funktion i Azure Automation som du kan använda för att köra Runbooks på datorer i ditt lokala data Center eller en moln leverantör.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 04/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 015318b1695f76121662b82e94b37de1ddb63b1b
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/12/2019
ms.locfileid: "68952943"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>Automatisera resurser i ditt data Center eller moln genom att använda Hybrid Runbook Worker

Runbooks i Azure Automation kanske inte har åtkomst till resurser i andra moln eller i din lokala miljö eftersom de körs på Azures moln plattform. Du kan använda Hybrid Runbook Worker funktionen i Azure Automation för att köra Runbooks direkt på datorn som är värd för rollen och mot resurser i miljön för att hantera de lokala resurserna. Runbooks lagras och hanteras i Azure Automation och levereras sedan till en eller flera tilldelade datorer.

Följande bild visar den här funktionen:

![Översikt över Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

Varje Hybrid Runbook Worker är medlem i en Hybrid Runbook Worker grupp som du anger när du installerar agenten. En grupp kan innehålla en enda agent, men du kan installera flera agenter i en grupp för hög tillgänglighet.

När du startar en Runbook på en Hybrid Runbook Worker anger du den grupp som den körs på. Varje anställd i gruppen avsöker Azure Automation för att se om några jobb är tillgängliga. Om ett jobb är tillgängligt tar den första arbets tagaren att hämta jobbet. Bearbetnings tiden för jobb kön beror på maskin varu profilen för Hybrid Worker och belastningen. Du kan inte ange en viss arbetare. Hybrid Runbook Worker delar inte många av de gränser som Azure-sandbox har. De har inte samma begränsningar för disk utrymme, minne eller nätverks-Sockets. Hybrid Runbook Worker begränsas endast av resurserna i själva Hybrid Runbook Worker. Dessutom delar hybrid Runbook Worker inte den 180 minuter långa [resurs](automation-runbook-execution.md#fair-share) tids gränsen som Azure-sandbox gör. Mer information om tjänst begränsningarna för Azure-sand lådor och hybrid Runbook Worker finns på sidan jobb [gränser](../azure-subscription-service-limits.md#automation-limits) .

## <a name="install-a-hybrid-runbook-worker"></a>Installera en Hybrid Runbook Worker

Processen för att installera en Hybrid Runbook Worker är beroende av operativ systemet. Följande tabell innehåller länkar till de metoder som du kan använda för installationen.

Du kan använda två metoder för att installera och konfigurera en Windows-Hybrid Runbook Worker. Den rekommenderade metoden använder en Automation-Runbook för att helt automatisera processen med att konfigurera en Windows-dator. Den andra metoden följer en steg-för-steg-procedur för att manuellt installera och konfigurera rollen. För Linux-datorer kör du ett Python-skript för att installera agenten på datorn.

|OS  |Distributions typer  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Bok](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

> [!NOTE]
> Om du vill hantera konfigurationen av dina servrar som har stöd för Hybrid Runbook Worker-rollen med önskad tillstånds konfiguration (DSC), måste du lägga till dem som DSC-noder. Mer information om hur du registrerar dem för hantering med DSC finns i [onboarding Machines for Management by Azure Automation DSC](automation-dsc-onboarding.md).
>
>Om du aktiverar [uppdateringshantering-lösningen](automation-update-management.md)konfigureras alla datorer som är anslutna till Azure Log Analytics-arbetsytan automatiskt som en hybrid Runbook Worker för att stödja Runbooks som ingår i den här lösningen. Datorn är dock inte registrerad med Hybrid Worker grupper som redan har definierats i ditt Automation-konto. Datorn kan läggas till i en Hybrid Runbook Worker grupp i ditt Automation-konto så att den stöder Automation-runbooks så länge du använder samma konto för både lösningen och Hybrid Runbook Worker grupp medlemskapet. Den här funktionen har lagts till i version 7.2.12024.0 av Hybrid Runbook Worker.

Granska [informationen för att planera nätverket](#network-planning) innan du börjar distribuera en hybrid Runbook Worker. När du har distribuerat arbetaren granskar du [Kör Runbooks på en hybrid Runbook Worker](automation-hrw-run-runbooks.md) för att lära dig hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala data Center eller annan moln miljö.

Datorn kan läggas till i en Hybrid Runbook Worker grupp i ditt Automation-konto så att den stöder Automation-runbooks så länge du använder samma konto för både lösningen och Hybrid Runbook Worker grupp medlemskapet. Den här funktionen har lagts till i version 7.2.12024.0 av Hybrid Runbook Worker.
## <a name="remove-a-hybrid-runbook-worker"></a>Ta bort en Hybrid Runbook Worker

Du kan ta bort en eller flera hybrid Runbook Worker från en grupp, eller så kan du ta bort gruppen, beroende på dina behov. Gör så här om du vill ta bort en Hybrid Runbook Worker från en lokal dator:

1. I Azure Portal går du till ditt Automation-konto.
2. Under **konto inställningar**väljer du **nycklar** och noterar värdena för **URL** och **primär åtkomst nyckel**. Du behöver den här informationen för nästa steg.

### <a name="windows"></a>Windows

Öppna en PowerShell-session i administratörs läge och kör följande kommando. Använd **-verbose-** växeln för en detaljerad logg över borttagnings processen.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>
```

Om du vill ta bort inaktuella datorer från hybrid Worker gruppen använder `machineName` du den valfria parametern.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="linux"></a>Linux

Du kan använda kommandot `ls /var/opt/microsoft/omsagent` på Hybrid Runbook Worker för att hämta workspaceid. Det finns en mapp i katalogen där namnet på mappen är arbetsyte-ID.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Den här koden tar inte bort Microsoft Monitoring Agent från datorn, endast funktioner och konfiguration av Hybrid Runbook Workers rollen.

## <a name="remove-a-hybrid-worker-group"></a>Ta bort en Hybrid Worker grupp

Om du vill ta bort en grupp måste du först ta bort Hybrid Runbook Worker från varje dator som är medlem i gruppen med hjälp av proceduren som visas ovan. Använd sedan följande steg för att ta bort gruppen:

1. Öppna Automation-kontot i Azure Portal.
2. Under **process automatisering**väljer du **hybrid Worker-grupper**. Välj den grupp som du vill ta bort. Sidan Egenskaper för gruppen visas.

   ![Sidan Egenskaper](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. På egenskaps sidan för den valda gruppen väljer du **ta bort**. Ett meddelande som uppmanar dig att bekräfta åtgärden. Välj **Ja** om du är säker på att du vill fortsätta.

   ![Bekräftelsemeddelande](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Den här processen kan ta flera sekunder att slutföra. Du kan spåra förloppet under **Meddelanden** på menyn.

## <a name="network-planning"></a>Konfigurera nätverket

### <a name="hybrid-worker-role"></a>Hybrid Worker roll

För att Hybrid Runbook Worker ska kunna ansluta till och registrera med Azure Automation måste den ha åtkomst till port numret och URL: erna som beskrivs i det här avsnittet. Den här åtkomsten finns överst i de [portar och URL: er som krävs för att Microsoft Monitoring Agent](../azure-monitor/platform/agent-windows.md) ska kunna ansluta till Azure Monitor loggar.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Om du använder en proxyserver för kommunikation mellan agenten och tjänsten Azure Automation, se till att lämpliga resurser är tillgängliga. Tids gränsen för begär Anden från Hybrid Runbook Worker och automatiserings tjänsterna är 30 sekunder. Efter 3 försök kommer begäran att Miss lyckas. Om du använder en brand vägg för att begränsa åtkomsten till Internet, måste du konfigurera brand väggen för att tillåta åtkomst. Om du använder Log Analytics gateway som proxy kontrollerar du att den har kon figurer ATS för Hybrid arbetare. Instruktioner för hur du gör detta finns i [konfigurera Log Analytics Gateway för Automation hybrid Worker](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

Följande port och URL-adresser krävs för att Hybrid Runbook Worker-rollen ska kunna kommunicera med Automation:

* Port: Endast TCP 443 krävs för utgående Internet åtkomst.
* Global URL: *. azure-automation.net
* Global URL för US Gov, Virginia: *. azure-automation.us
* Agent tjänst: https://\<workspaceId\>. agentsvc.Azure-Automation.net

Vi rekommenderar att du använder de adresser som anges när du definierar undantag. För IP-adresser kan du hämta [Microsoft Azure Data Center IP-intervall](https://www.microsoft.com/en-us/download/details.aspx?id=56519). Den här filen uppdateras varje vecka och har de för närvarande distribuerade intervallen och eventuella kommande ändringar i IP-intervallen.

Om du har ett Automation-konto som har definierats för en viss region kan du begränsa kommunikationen till det regionala data centret. Följande tabell innehåller DNS-posten för varje region:

| **Region** | **DNS-post** |
| --- | --- |
| Västra centrala USA | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Södra centrala USA |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| USA, östra 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Västra USA 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |
| Centrala Kanada |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Västra Europa |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Norra Europa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Sydostasien |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Indien, centrala |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Östra Japan |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Östra Australien |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Sydöstra Australien |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Storbritannien, södra | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| Virginia (USA-förvaltad region) | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Om du vill ha en lista över regions-IP-adresser i stället för region namn laddar du ned XML-filen för [Azure datacenter-IP-adress](https://www.microsoft.com/download/details.aspx?id=41653) från Microsoft Download Center

> [!NOTE]
> XML-filen för Azure datacenter-IP-adress innehåller de IP-adressintervall som används i Microsoft Azure Data Center. Filen innehåller beräknings-, SQL-och lagrings intervall.
>
>En uppdaterad fil publiceras varje vecka. Filen visar de för tillfället distribuerade intervallen och eventuella kommande ändringar i IP-intervallen. Nya intervall som visas i filen används inte i Data Center i minst en vecka.
>
> Det är en bra idé att ladda ned den nya XML-filen varje vecka. Uppdatera sedan webbplatsen för att identifiera tjänster som körs i Azure på rätt sätt. Azure ExpressRoute-användare bör Observera att den här filen används för att uppdatera Border Gateway Protocol-annonsering (BGP) för Azure-utrymmet under den första veckan i varje månad.

### <a name="update-management"></a>Uppdateringshantering

Utöver de standard adresser och portar som Hybrid Runbook Worker kräver måste följande adresser anges specifikt för Uppdateringshantering. Kommunikationen med de här adresserna görs via port 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

## <a name="next-steps"></a>Nästa steg

* Information om hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala data Center eller någon annan moln miljö finns i [köra Runbooks på en hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Information om hur du felsöker dina hybrid Runbook Worker finns i [Felsöka hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#general)

