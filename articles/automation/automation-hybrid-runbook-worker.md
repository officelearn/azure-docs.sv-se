---
title: Azure Automation Hybrid Runbook Worker
description: Den här artikeln innehåller information om installation och användning av Hybrid Runbook Worker, vilket är en funktion i Azure Automation som du kan använda för att köra runbooks på datorer i ditt lokala datacenter eller molnet providern.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: be79f0111cb569509cb05b24c99f86d4ca9534b0
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064057"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>Automatisera resurser i ditt datacenter eller molnet med hjälp av Hybrid Runbook Worker

Runbooks i Azure Automation kan inte komma åt resurser i andra moln eller lokala miljö eftersom de körs på plattformen Azure-molnet. Du kan använda funktionen Hybrid Runbook Worker i Azure Automation för att köra runbooks direkt på den dator som är värd för rollen och mot resurser i miljön för att hantera de lokala resurserna. Runbooks lagras och hanteras i Azure Automation och sedan levereras till en eller flera särskilda datorer.

Följande bild illustrerar den här funktionen:

![Översikt över Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

Varje Runbook Worker-Hybrid är medlem i en Hybrid Runbook Worker-grupp som du anger när du installerar agenten. En grupp kan innehålla en enskild agent, men du kan installera flera agenter i en grupp för hög tillgänglighet.

När du startar en runbook på en Hybrid Runbook Worker, anger du den grupp som den körs på. Varje worker i gruppen avsöker Azure Automation för att se om det finns inga jobb. Om ett jobb är tillgänglig, tar den första worker som klarar av den. Du kan inte ange en viss worker.

## <a name="install-a-hybrid-runbook-worker"></a>Installera en Hybrid Runbook Worker

Processen för att installera en Hybrid Runbook Worker är beroende av Operativsystemet. Följande tabell innehåller länkar till metoder som du kan använda för att installera. 

Du kan använda två metoder för att installera och konfigurera en Windows-Hybrid Runbook Worker. Den rekommenderade metoden använder en Automation-runbook för att helt automatisera processen med att konfigurera en Windows-dator. Den andra metoden är följer stegvisa instruktioner för att manuellt installera och konfigurera rollen. För Linux-datorer, kan du köra en Python-skript för att installera agenten på datorn.

|Operativsystem  |Distributionstyper  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Manuell](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

> [!NOTE]
> Du måste lägga till dem som DSC-noder för att hantera konfigurationen för dina servrar som stöder Hybrid Runbook Worker-rollen med önskad tillstånd Configuration DSC (). Mer information om onboarding dem för hantering med DSC, se [Onboarding datorer för hantering av Azure Automation DSC](automation-dsc-onboarding.md).
>
>Om du aktiverar den [uppdatering hanteringslösning](automation-update-management.md), alla datorer som är ansluten till Azure logganalys-arbetsytan konfigureras automatiskt som en Hybrid Runbook Worker som stöd för runbooks som ingår i den här lösningen. Datorn har inte registrerats med några Hybrid Worker-grupper som redan har definierats i ditt Automation-konto. Datorn kan läggas till en Hybrid Runbook Worker-grupp i ditt Automation-konto till stöd för Automation-runbooks så länge som du använder samma konto för både lösningen och Hybrid Runbook Worker-gruppmedlemskap. Den här funktionen har lagts till version 7.2.12024.0 av Hybrid Runbook Worker.

Granska de [information för att planera nätverket](#network-planning) innan du börjar distribuera en Hybrid Runbook Worker. När du har distribuerat arbetaren kan granska [köra runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) att lära dig hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller andra molnmiljö.

## <a name="remove-a-hybrid-runbook-worker"></a>Ta bort en Hybrid Runbook Worker

Du kan ta bort en eller flera Runbook Worker-hybrider från en grupp och du tar bort gruppen, beroende på dina krav. Utför följande steg för att ta bort en Hybrid Runbook Worker från en lokal dator:

1. Gå till ditt Automation-konto i Azure-portalen.
2. Under **inställningar**väljer **nycklar** och anteckna värdena för **URL** och **primära åtkomstnyckeln**. Du behöver den här informationen för nästa steg.

### <a name="windows"></a>Windows

Öppna ett PowerShell-session i administratörsläge och kör följande kommando. Använd den **-Verbose** växla för en detaljerad logg över borttagningen.

```powershell
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>
```

Ta bort inaktuella datorer från Hybrid Worker-grupp med det valfria `machineName` parameter.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="linux"></a>Linux

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Den här koden tar inte bort Microsoft Monitoring Agent från datorn, funktioner och konfiguration av Hybrid Runbook Worker-rollen.

## <a name="remove-a-hybrid-worker-group"></a>Ta bort en Hybrid Worker-grupp

Om du vill ta bort en grupp, måste du först ta bort Hybrid Runbook Worker från varje dator som är medlem i gruppen med hjälp av proceduren som visades tidigare. Utför sedan följande steg för att ta bort gruppen:

1. Öppna Automation-kontot i Azure-portalen.
1. Under **Processautomatisering**väljer **Hybrid worker grupper**. Välj den grupp som du vill ta bort. Egenskapssidan för gruppen visas.

   ![Sidan Egenskaper](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. På egenskapssidan för den valda gruppen väljer **ta bort**. Du ombeds bekräfta åtgärden. Välj **Ja** om du är säker på att du vill fortsätta.

   ![Bekräftelsemeddelande](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Den här processen kan ta flera sekunder att slutföra. Du kan spåra förloppet under **Meddelanden** på menyn.

## <a name="network-planning"></a>Konfigurera nätverket

### <a name="hybrid-worker-role"></a>Hybrid Worker-rollen

För Runbook Worker-hybriden att ansluta till och registrera med logganalys, måste den ha åtkomst till portnumret och URL: erna som beskrivs i det här avsnittet. Åtkomst är tillägg till den [portar och URL: er som krävs för Microsoft Monitoring Agent](../log-analytics/log-analytics-agent-windows.md) att ansluta till logganalys.

Om du använder en proxyserver för kommunikation mellan agenten och Log Analytics-tjänsten måste du kontrollera att lämpliga resurser är tillgängliga. Om du använder en brandvägg för att begränsa åtkomsten till internet, måste du konfigurera brandväggen att tillåta åtkomst till.

Följande porten och URL: er krävs att kommunicera med Automation Hybrid Runbook Worker rollen:

* Port: Endast TCP 443 krävs för utgående Internetåtkomst.
* Globala URL: *.azure-automation.net
* Globala URL för USA Gov Virginia: *.azure automation.us
* Agent-tjänsten: https://\<workspaceId\>.agentsvc.azure-automation.net

Om du har ett Automation-konto som har definierats för en viss region, kan du begränsa kommunikation till det regionala datacentret. Följande tabell innehåller DNS-post för varje region:

| **Region** | **DNS-post** |
| --- | --- |
| Västra centrala USA | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus agentservice-produktprenumeration-1.azure-automation.net |
| Södra centrala USA |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Östra USA 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Centrala Kanada |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Västra Europa |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Norra Europa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Sydostasien |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Indien, centrala |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Östra Japan |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Sydöstra Australien |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Storbritannien, södra | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks agentservice-produktprenumeration-1.azure-automation.net |
| Virginia (USA-förvaltad region) | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge agentservice-produktprenumeration-1.azure-automation.us |

En lista över region IP-adresser i stället för regionnamn, hämta den [Azure Datacenter-IP-adress](https://www.microsoft.com/download/details.aspx?id=41653) XML-fil från Microsoft Download Center.

> [!NOTE]
> Azure-Datacenter IP-adress XML-filen visas de IP-adressintervall som används i Microsoft Azure-datacenter. Filen innehåller beräkning, SQL och lagring intervall.
>
>En uppdaterad fil skickas varje vecka. Filen visar för tillfället distribuerade intervall och eventuella kommande ändringar till IP-adressintervall. Nya adressintervall som visas i filen används inte i datacenter för minst en vecka.
>
> Det är en bra idé att hämta den nya XML-filen varje vecka. Uppdatera din webbplats om du vill identifiera tjänster som körs i Azure. Azure ExpressRoute användare Observera att den här filen används för att uppdatera Border Gateway Protocol (BGP)-annonsen Azure utrymme i den första veckan i månaden.

### <a name="update-management"></a>Uppdateringshantering

Förutom standard adresser och portar som kräver Hybrid Runbook Worker, krävs följande adresser specifikt för hantering av uppdateringar. Kommunikation till dessa adresser görs via port 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|

## <a name="troubleshoot"></a>Felsöka

Information om hur du felsöker Hybrid Runbook Worker-arbeten finns [felsökning Hybrid Runbook Worker](troubleshoot/hybrid-runbook-worker.md#general)

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller andra molnmiljö finns [köra runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md).