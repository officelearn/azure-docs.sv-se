---
title: Azure Automation Hybrid Runbook Worker
description: Den här artikeln innehåller information om installation och användning av Hybrid Runbook Worker som är en funktion i Azure Automation där du kan köra runbooks på datorer i ditt lokala datacenter eller molnet providern.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/25/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 270990d5dea53c2467bf2d7df4695a14b3dbec8c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="automate-resources-in-your-data-center-or-cloud-with-hybrid-runbook-worker"></a>Automatisera resurser i ditt datacenter eller molnet med Hybrid Runbook Worker

Runbooks i Azure Automation kan inte komma åt resurser i andra moln eller lokala miljö eftersom de körs i Azure-molnet. Funktionen Hybrid Runbook Worker i Azure Automation kan du köra runbooks direkt på den dator där rollen och mot resurser i miljön för att hantera de lokala resurserna. Runbooks lagras och hanteras i Azure Automation och sedan levereras till en eller flera särskilda datorer.

Den här funktionen visas i följande bild:

![Hybrid Runbook Worker-översikt](media/automation-hybrid-runbook-worker/automation.png)

## <a name="hybrid-runbook-worker-groups"></a>Hybrid Runbook Worker-grupper

Varje Runbook Worker-Hybrid är medlem i en Hybrid Runbook Worker-grupp som du anger när du installerar agenten. En grupp kan innehålla en enskild agent, men du kan installera flera agenter i en grupp för hög tillgänglighet.

När du startar en runbook på en Hybrid Runbook Worker, anger du den grupp som den körs på. Varje worker i gruppen avsöker Azure Automation för att se om det finns inga jobb. Om det finns ett jobb har den första worker som klarar av tar den. Du kan inte ange en viss worker.

## <a name="installing-a-hybrid-runbook-worker"></a>Installera en Hybrid Runbook Worker

Processen för att installera en Hybrid Runbook worker är olika beroende på Operativsystemet. Följande tabell innehåller länkar till de olika metoder som du kan använda för att installera en Hybrid Runbook Worker. Om du vill installera och konfigurera en Windows-Hybrid Runbook Worker, finns det två metoder. Den rekommenderade metoden använder en Automation-runbook för att helt automatisera processen som krävs för att konfigurera en Windows-dator. Den andra metoden är följer stegvisa instruktioner för att manuellt installera och konfigurera rollen. För Linux-datorer kör du ett Python-skript för att installera agenten på datorn

|Operativsystem  |Distributionstyper  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Manuell](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-linux-hybrid-runbook-worker)        |

> [!NOTE]
> Om du vill hantera konfigurationen av dina servrar som stöder Hybrid Runbook Worker-rollen med önskad tillstånd Configuration DSC () som du behöver lägga till dem som DSC-noder. Mer information om onboarding dem för hantering med DSC, se [Onboarding datorer för hantering av Azure Automation DSC](automation-dsc-onboarding.md).
>
>Om du aktiverar den [uppdatering hanteringslösning](automation-update-management.md), valfri dator som är ansluten till logganalys-arbetsytan konfigureras automatiskt som en Hybrid Runbook Worker som stöd för runbooks som ingår i den här lösningen. Det har inte registrerats med några Hybrid Worker-grupper som redan har definierats i ditt Automation-konto. Datorn kan läggas till en Hybrid Runbook Worker-grupp i ditt Automation-konto till stöd för Automation-runbooks så länge som du använder samma konto för både lösning och Hybrid Runbook Worker-gruppmedlemskap. Den här funktionen har lagts till i version 7.2.12024.0 av Hybrid Runbook Worker.

Granska de [information för att planera nätverket](#network-planning) innan du börjar distribuera en Hybrid Runbook Worker. När en runbook worker har distribuerats, granska [köra runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) att lära dig hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller andra molnmiljö.

## <a name="removing-hybrid-runbook-worker"></a>Tar bort Hybrid Runbook Worker

Du kan ta bort en eller flera Runbook Worker-hybrider från en grupp och du tar bort gruppen, beroende på dina krav. Utför följande steg för att ta bort en Hybrid Runbook Worker från en lokal dator:

1. Navigera till ditt Automation-konto i Azure-portalen.
2. Från den **inställningar** bladet väljer **nycklar** och anteckna värdena för fältet **URL** och **primära åtkomstnyckeln**. Du behöver den här informationen för nästa steg.

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
> Microsoft Monitoring Agent tas inte bort från datorn, funktioner och konfiguration av Hybrid Runbook Worker-rollen.

## <a name="remove-hybrid-worker-groups"></a>Ta bort Hybrid Worker-grupper

Om du vill ta bort en grupp, måste du först ta bort Hybrid Runbook Worker från varje dator som är medlem i gruppen med hjälp av proceduren som visades tidigare och utför sedan följande steg för att ta bort gruppen.

1. Öppna Automation-kontot i Azure-portalen.
1. Under **Processautomatisering**väljer **Hybrid worker grupper**. Välj den grupp som du vill ta bort. När du har valt en viss grupp av **Hybrid worker-gruppen** egenskaper visas.

   ![Sidan hybrid Runbook Worker-grupp](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

1. På egenskapssidan för den valda gruppen **ta bort**. Ett meddelande visas där du ombeds bekräfta åtgärden, Välj **Ja** om du är säker på att du vill fortsätta.

   ![Ta bort grupp bekräftelsedialogruta](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Den här processen kan ta flera sekunder att slutföra och du kan spåra förloppet under **Meddelanden** på menyn.

## <a name="network-planning"></a>Konfigurera nätverket

### <a name="hybrid-worker-role"></a>Hybrid Worker-rollen

För Runbook Worker-hybriden att ansluta till och registrera med logganalys, måste den ha åtkomst till portnumret och URL: erna som beskrivs i det här avsnittet. Detta är tillägg till den [portar och URL: er som krävs för Microsoft Monitoring Agent](../log-analytics/log-analytics-agent-windows.md) att ansluta till logganalys.

Om du använder en proxyserver för kommunikation mellan agenten och Log Analytics-tjänsten måste du kontrollera att lämpliga resurser är tillgängliga. Om du använder en brandvägg för att begränsa åtkomsten till internet, måste du konfigurera brandväggen att tillåta åtkomst till.

Följande porten och URL: er krävs att kommunicera med Automation Hybrid Runbook Worker rollen:

* Port: Endast TCP 443 krävs för utgående Internetåtkomst.
* Globala URL: *.azure-automation.net
* Globala URL för USA Gov Virginia: *.azure automation.us
* Agent-tjänsten: https://\<workspaceId\>.agentsvc.azure-automation.net

Om du har ett Automation-konto som har definierats för en viss region, kan du begränsa kommunikation till det regionala datacentret. Följande tabell innehåller DNS-post för varje region.

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
> Azure-Datacenter IP-adress XML-filen visas de IP-adressintervall som används i Microsoft Azure-datacenter. Beräkning, SQL och lagring adressintervall ingår i filen.
>
>En uppdaterad fil skickas varje vecka. Filen visar för tillfället distribuerade intervall och eventuella kommande ändringar till IP-adressintervall. Nya adressintervall som visas i filen används inte i datacenter för minst en vecka.
>
> Det är en bra idé att hämta den nya XML-filen varje vecka. Uppdatera din webbplats om du vill identifiera tjänster som körs i Azure. Azure ExpressRoute användare Observera att den här filen används för att uppdatera Border Gateway Protocol (BGP) annonsen Azure utrymme den första veckan i månaden.

### <a name="update-management"></a>Uppdateringshantering

Förutom standard adresser och portar som kräver Hybrid Runbook Worker, krävs följande adresser specifikt för hantering av uppdateringar. Kommunikation till dessa adresser görs via port 443.

|Azure Public  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*. ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *. oms.opinsights.azure.us        |
|*.blob.core.windows.net|*. blob.core.usgovcloudapi.net|

## <a name="troubleshooting"></a>Felsökning

Hybrid Runbook Worker är beroende av en agent för att kommunicera med ditt Automation-konto för att registrera worker, ta emot runbook-jobb och rapportera status. För Windows är den här agenten Microsoft Monitoring Agent. Linux är OMS-Agent för Linux. Om registreringen av worker misslyckas, är här några möjliga orsaker till felet:

### <a name="the-hybrid-worker-is-behind-a-proxy-or-firewall"></a>Worker-hybriden finns bakom en proxyserver eller brandvägg

Kontrollera att datorn är utgående åtkomst till *.azure automation.net på port 443.

### <a name="the-computer-the-hybrid-worker-is-running-on-has-less-than-the-minimum-hardware-requirements"></a>Worker-hybriden körs på datorn har mindre än minimikraven för maskinvara

Datorer som kör Runbook Worker-hybriden ska uppfylla minimikraven för maskinvara innan du utser den som värd för den här funktionen. Annars blir överbelastad datorn beroende på resursutnyttjande andra bakgrundsprocesser och konkurrens på grund av runbooks under körning och orsaka fördröjningar för runbook-jobb eller timeout.

Bekräfta att den dator som är tilldelad att köra funktionen Hybrid Runbook Worker uppfyller minimikraven på maskinvara. Om den finns, kan du övervaka användningen av CPU och minne för att fastställa alla korrelation mellan prestanda för Hybrid Runbook Worker-processer och Windows. Om det finns minne eller CPU-belastning, kan detta tyda på att behöva uppgradera eller lägga till fler processorer eller öka minne för att adressera resurs flaskhalsar och åtgärda felet. Du kan också markera en annan beräkningsresurser som stöder de minimikrav och skala när arbetsbelastning indikera att öka krävs.

Mer information om felsökning av för ett specifikt operativsystem finns [Linux Hybrid Runbook Worker](automation-linux-hrw-install.md#troubleshooting) eller [Windows Hybrid Runbook Worker](automation-windows-hrw-install.md#troubleshooting)

## <a name="next-steps"></a>Nästa steg

Granska [köra runbooks på en Hybrid Runbook Worker](automation-hrw-run-runbooks.md) att lära dig hur du konfigurerar dina runbooks för att automatisera processer i ditt lokala datacenter eller andra molnmiljö.
