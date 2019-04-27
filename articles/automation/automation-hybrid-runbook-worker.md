---
title: Azure Automation Hybrid Runbook Worker
description: Den här artikeln innehåller information om att installera och använda Hybrid Runbook Worker som är en funktion i Azure Automation som du kan använda för att köra runbooks på datorer i ditt lokala datacenter eller annan molnleverantör.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 55dff6cf073612e3e5473da3a5f1bf722b2ccdbd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60739354"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>Automatisera resurser i ditt datacenter eller i molnet med hjälp av Hybrid Runbook Worker

Runbooks i Azure Automation kanske inte har åtkomst till resurser i andra moln eller i din lokala miljö eftersom de körs på Azure-molnplattformen. Du kan använda funktionen Hybrid Runbook Worker i Azure Automation för att köra runbooks direkt på den dator som är värd för rollen och mot resurser i miljön för att hantera dessa lokala resurser. Runbooks lagras och hanteras i Azure Automation och sedan levereras till en eller flera tilldelade datorer.

Följande bild illustrerar den här funktionen:

![Översikt över Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

Varje Hybrid Runbook Worker är medlem i en Hybrid Runbook Worker-grupp som du anger när du installerar agenten. En grupp kan innehålla en enda agent, men du kan installera flera agenter i en grupp för hög tillgänglighet.

När du startar en runbook på en Hybrid Runbook Worker, anger du den grupp som den körs på. Varje arbetsroll i gruppen avsöker Azure Automation för att se om det finns några jobb. Om det finns ett jobb tar den första worker som klarar av den. Bearbetningstid köns jobb beror på Hybrid worker-maskinvaruprofilen och belastning. Du kan inte ange en viss worker. Hybrid Runbook Worker delar inte många av de gränser som har Azure sandbox-miljöer. De har inte samma begränsningar på diskutrymme, minne eller nätverket sockets. Hybrid Runbook Worker begränsas bara av resurser på den Hybrid Runbook Worker själva. Dessutom Hybrid Runbook Worker delar inte 180 minut [rättmätiga del](automation-runbook-execution.md#fair-share) tidsgränsen som gör Azure sandbox-miljöer. Läs mer om tjänstbegränsningar för Azure sandbox-miljöer och Hybrid Runbook Worker i jobbet [gränser](../azure-subscription-service-limits.md#automation-limits) sidan.

> [!NOTE]
> Hybrid Runbook Worker stöds inte i Azure Kina.

## <a name="install-a-hybrid-runbook-worker"></a>Installera en Hybrid Runbook Worker

Processen för att installera en Hybrid Runbook Worker är beroende av Operativsystemet. I följande tabell innehåller länkar till metoder som du kan använda för att installera.

Du kan använda två metoder för att installera och konfigurera en Windows Hybrid Runbook Worker. Den rekommendera metoden att använda en Automation-runbook att helt automatisera processen med att konfigurera en Windows-dator. Den andra metoden följer stegvisa anvisningar för att manuellt installera och konfigurera rollen. För Linux-datorer kan köra du ett pythonskript för att installera agenten på datorn.

|Operativsystem  |Distributionstyper  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Manuell](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

> [!NOTE]
> Om du vill hantera konfigurationen av dina servrar som har stöd för Hybrid Runbook Worker-roll med Desired State Configuration (DSC), som du behöver lägga till dem som DSC-noder. Mer information om onboarding dem för hantering med DSC, se [konfigurera datorer för hantering av Azure Automation DSC](automation-dsc-onboarding.md).
>
>Om du aktiverar den [uppdateringshanteringslösningen](automation-update-management.md), konfigureras en dator som är ansluten till din Azure Log Analytics-arbetsyta automatiskt som en Hybrid Runbook Worker som stöd för runbooks som ingår i den här lösningen. Datorn är inte registrerad med några Hybrid Worker-grupper som redan har definierats i ditt Automation-konto. Datorn kan läggas till en Hybrid Runbook Worker-grupp i ditt Automation-konto för Automation-runbooks så länge du använder samma konto för både lösningen och Hybrid Runbook Worker-gruppmedlemskapet. Den här funktionen har lagts till i version 7.2.12024.0 av Hybrid Runbook Worker.

Granska den [information planerar nätverket](#network-planning) innan du börjar distribuera en Hybrid Runbook Worker. När du har distribuerat arbetaren kan granska [köra runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) att lära dig hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller andra moln.

## <a name="remove-a-hybrid-runbook-worker"></a>Ta bort en Hybrid Runbook Worker

Du kan ta bort en eller flera Hybrid Runbook Worker från en grupp eller du kan ta bort gruppen, beroende på dina krav. Ta bort en Hybrid Runbook Worker från en lokal dator med följande steg:

1. Gå till ditt Automation-konto i Azure-portalen.
2. Under **kontoinställningar**väljer **nycklar** och anteckna värdena för **URL** och **primära åtkomstnyckel**. Du behöver den här informationen för nästa steg.

### <a name="windows"></a>Windows

Öppna en PowerShell-session i administratörsläge och kör följande kommando. Använd den **-utförlig** växla för en detaljerad logg över borttagningen.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>
```

Ta bort inaktuella datorer från din Hybrid Worker-grupp med det valfria `machineName` parametern.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="linux"></a>Linux

Du kan använda kommandot `ls /var/opt/microsoft/omsagent` på den Hybrid Runbook Worker för att hämta workspaceid. Det finns en mapp i katalogen där namnet på mappen är arbetsytan Id.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Den här koden tar inte bort Microsoft Monitoring Agent från datorn, funktioner och konfiguration av Hybrid Runbook Worker-rollen.

## <a name="remove-a-hybrid-worker-group"></a>Ta bort en Hybrid Worker-grupp

Om du vill ta bort en grupp, måste du först ta bort Hybrid Runbook Worker från varje dator som är medlem i gruppen med hjälp av proceduren som visades tidigare. Sedan använder du följande steg för att ta bort gruppen:

1. Öppna Automation-kontot i Azure-portalen.
2. Under **Processautomatisering**väljer **Hybrid worker-grupper**. Välj den grupp som du vill ta bort. Egenskapssidan för gruppen visas.

   ![Sidan Egenskaper](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. På egenskapssidan för den valda gruppen väljer **ta bort**. Du ombeds att bekräfta åtgärden. Välj **Ja** om du vet att du vill fortsätta.

   ![Bekräftelsemeddelande](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Den här processen kan ta flera sekunder att slutföra. Du kan spåra förloppet under **Meddelanden** på menyn.

## <a name="network-planning"></a>Konfigurera nätverket

### <a name="hybrid-worker-role"></a>Hybrid Worker-roll

För att Hybrid Runbook Worker att ansluta till och registrera med Azure Automation måste den ha åtkomst till portnumret och URL: erna som beskrivs i det här avsnittet. Åtkomst är längst upp för att den [portar och URL: er som krävs för Microsoft Monitoring Agent](../azure-monitor/platform/agent-windows.md) att ansluta till Azure Monitor-loggar.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Se till att lämpliga resurser är tillgängliga om du använder en proxyserver för kommunikation mellan agenten och tjänsten Azure Automation. Timeout för begäranden från Hybrid Runbook Worker och Automation-tjänster är 30 sekunder. Efter 3 försök misslyckas begäran. Om du använder en brandvägg för att begränsa åtkomsten till internet, måste du konfigurera brandväggen att tillåta åtkomst. Om du använder Log Analytics-gateway som en proxy, se till att den är konfigurerad för hybrid Worker-arbeten. Anvisningar för hur du gör detta finns i [konfigurerar Log Analytics-gateway för Automation Hybrid Worker](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

Följande port och URL: er krävs för att Hybrid Runbook Worker-rollen ska kommunicera med Automation:

* Port: Endast TCP 443 krävs för utgående Internetåtkomst.
* Global URL: *.azure-automation.net
* Global URL: en för Virginia (USA-förvaltad region): *.azure automation.us
* Agent-tjänsten: https://\<workspaceId\>.agentsvc.azure-automation.net

Det rekommenderas att använda de adresser som anges när du definierar undantag. För IP-adresser som du kan ladda ned den [IP-intervall i Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Den här filen uppdateras varje vecka och har de aktuella intervall och eventuella kommande ändringar till IP-adressintervall.

Om du har ett Automation-konto som har definierats för en viss region kan begränsa du kommunikationen till det regionala datacentret. Följande tabell innehåller DNS-posten för varje region:

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

Ladda ned en lista över region IP-adresser i stället för regionnamn den [Azure Datacenter IP-adress](https://www.microsoft.com/download/details.aspx?id=41653) XML-fil från Microsoft Download Center.

> [!NOTE]
> Azure-Datacenter IP-adress XML-filen visas de IP-adressintervall som används i Microsoft Azure-datacenter. Filen innehåller compute, SQL och storage-intervall.
>
>Varje vecka publiceras en uppdaterad fil. Filen visar aktuella intervall och eventuella kommande ändringar till IP-adressintervall. De nya intervall som visas i filen används inte i datacenter för minst en vecka.
>
> Det är en bra idé att ladda ned den nya XML-filen varje vecka. Sedan uppdatera webbplatsen för att kunna identifiera vilka tjänster som körs i Azure. Azure ExpressRoute-användare bör Observera att den här filen används för att uppdatera Border Gateway Protocol (BGP)-annonsen Azure utrymme i den första veckan varje månad.

### <a name="update-management"></a>Uppdateringshantering

På standard-adresser och portar som kräver att Hybrid Runbook Worker, krävs följande adresser specifikt för hantering av uppdateringar. Kommunikation till dessa adresser görs via port 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

## <a name="next-steps"></a>Nästa steg

* Läs hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller andra moln i [köra runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md).
* Läs hur du felsöker Hybrid Runbook Worker-arbeten i [felsöka Hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#general)

