---
title: Översikt över Azure Automation
description: Lär dig hur du använder Azure Automation för att automatisera livscykeln för infrastruktur och program.
services: automation
ms.service: automation
ms.component: process-automation
author: eamonoreilly
ms.author: eamono
keywords: azure automation, DSC, powershell, desired state configuration, update management, change tracking, inventory, runbooks, python, graphical
ms.date: 03/15/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: ce30a9eb11cd951ca7d8197c15c3becba333f5a9
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="an-introduction-to-azure-automation"></a>En introduktion till Azure Automation

Azure Automation är en molnbaserad tjänst för automatisering och konfiguration. Med tjänsten får du en enhetlig hantering i Azure och andra miljöer. Den består av funktioner för processautomatisering, uppdateringshantering och konfiguration. Azure Automation ger fullständig kontroll under distribution, drift och inaktivering av arbetsbelastningar och resurser.
Den här artikeln innehåller en kort översikt över Azure Automation och ger svar på några vanliga frågor. Om du vill ha mer information om de olika funktionerna kan du välja länkarna i den här översikten.

## <a name="azure-automation-capabilities"></a>Azure Automation-funktioner

![Översikt över funktioner i Azure Automation](media/automation-overview/automation-overview.png)

### <a name="process-automation"></a>Processautomatisering

Med Azure Automation kan du automatisera vanliga, tidskrävande och felbenägna molnhanteringsuppgifter. Automatiseringen gör att du kan fokusera på arbete som ger ett mervärde för verksamheten. Genom att minska felkällorna och öka effektiviteten bidrar det också till att sänka dina driftskostnader. Du kan integrera Azure-tjänster och andra offentliga system som krävs för distribution, konfigurering och hantering av dina processer från slutpunkt till slutpunkt. Med tjänsten kan du [skapa runbooks](automation-runbook-types.md) grafiskt i PowerShell eller Python. Med hjälp av en Hybrid Runbook Worker får du en enhetlig hantering genom att samordna lokala miljöer. Med [webhooks](automation-webhooks.md) kan du utföra begäranden och säkerställa kontinuerlig leverans och drift genom att utlösa automatisering från ITSM, DevOps och övervakningssystem.

### <a name="configuration-management"></a>Konfigurationshantering

Azure Automation [Desired State Configuration](automation-dsc-overview.md) är en molnbaserad lösning för PowerShell DSC som tillhandahåller tjänster som krävs för företagsmiljöer. Hantera dina DSC-resurser i Azure Automation och tillämpa konfigurationer på virtuella eller fysiska datorer från en DSC-hämtningsserver i Azure-molnet. Lösningen innehåller omfattande rapporter som informerar dig om viktiga händelser, t.ex. när noder avviker från sin tilldelade konfiguration. Du kan övervaka och automatiskt uppdatera datorkonfiguration på fysiska och virtuella datorer, Windows eller Linux, i molnet eller lokalt.

Med inventering av gästresurser kan du få insyn i installerade program och andra konfigurationsobjekt. Det finns omfattande rapporterings- och sökfunktioner som gör att du snabbt kan hitta detaljerad information som visar vad som konfigurerats i operativsystemet. Du kan spåra ändringar i tjänster, daemons, programvara, register och filer. På så sätt kan du snabbt kan identifiera saker som kan orsaka problem. Dessutom kan DSC hjälpa dig att diagnostisera och varna när oönskade ändringar sker i din miljö.

### <a name="update-management"></a>Hantering av uppdateringar

Uppdatera Windows- och Linux-system i hybridmiljöer med Azure Automation. Du kan se uppdateringsefterlevnad i Azure, lokalt och andra moln. Du kan skapa schemalagda distributioner för att samordna installationen av uppdateringar inom en definierad underhållsperiod. Om en uppdatering inte ska installeras på en dator kan du ta bort den från distributionen.

### <a name="shared-capabilities"></a>Delade funktioner

Azure Automation består av en uppsättning delade resurser som gör det enklare att automatisera och konfigurera dina miljöer i större skala.

* **[Rollbaserad åtkomstkontroll](automation-role-based-access-control.md)** – Kontrollera åtkomst till kontot med en Automation-operatörsroll som gör att aktiviteter kan köras utan tilldelning av redigeringsbehörigheter.
* **[Variabler](automation-variables.md)** – Ger dig ett sätt att lagra innehåll som kan användas i runbooks och konfigurationer. Du kan ändra värden utan att du behöver ändra runbooks och konfigurationer som hänvisar till dem.
* **[Autentiseringsuppgifter](automation-credentials.md)** – Lagra känslig information, som kan användas av runbooks och konfigurationer vid körning, på ett säkert sätt.
* **[Certifikat](automation-certificates.md)** – Lagra certifikat och gör dem tillgängliga vid körning så att de kan användas för autentisering och skydda distribuerade resurser.
* **[Anslutningar](automation-connections.md)** – Lagra namn/värde-par som innehåller gemensam information vid anslutning till system i anslutningsresurser. Anslutningar definieras av den som skapat modulen och används vid körning i runbooks och konfigurationer.
* **[Scheman](automation-schedules.md)** – Används i tjänsten för att utlösa automatisering vid fördefinierade tidpunkter.
* **[Integrering med källkontroll](automation-source-control-integration.md)** – Visar konfiguration som kod där runbooks eller konfigurationer kan checkas in i ett källkontrollsystem.
* **[PowerShell-moduler](automation-integration-modules.md)** – Moduler används för att hantera Azure och andra system. Importera till Automation-kontot för Microsoft, tredje part, community eller anpassade cmdlets och DSC-resurser.

### <a name="windows-and-linux"></a>Windows och Linux

Azure Automation kan användas i hybridmolnmiljöer och även för Windows och Linux. Du får ett enhetligt sätt att automatisera och konfigurera de arbetsbelastningar som distribueras och det operativsystem som arbetsbelastningarna körs på.

### <a name="community-gallery"></a>Communitygalleriet

I [Automation-galleriet](automation-runbook-gallery.md) kan du söka efter runbooks och moduler så att du snabbt kommer igång med att integrera och skapa processer från PowerShell-galleriet och Microsoft Script Center.

## <a name="common-scenarios-for-automation"></a>Vanliga scenarier för Automation

Azure Automation kan användas under hela livscykeln för din infrastruktur och dina program. Överför kunskap till systemet om hur organisationen levererar och underhåller arbetsbelastningar. Skriv på vanliga språk som PowerShell, Desired State Configuration, Python och grafiska runbook-flöden. Få en fullständig inventering av distribuerade resurser för målanpassning, rapportering och efterlevnad. Identifiera ändringar som kan leda till felaktig konfiguration och få bättre efterlevnad.

* **Skapa/distribuera resurser** – Distribuera virtuella datorer i en hybridmiljö med runbooks och Azure Resource Manager-mallar. Integrera i utvecklingsverktyg som Jenkins och Visual Studio Team Services.
* **Konfigurera virtuella datorer** – Utvärdera och konfigurera Windows- och Linux-datorer med önskad konfiguration för infrastrukturen och programmet.
* **Övervaka** – Identifiera ändringar på datorer som orsakar problem och åtgärda eller eskalera till hanteringssystem.
* **Skydda** – Placera den virtuella datorn i karantän om en säkerhetsvarning genereras. Ange gästkrav.
* **Reglera åtkomst** – Konfigurera rollbaserad åtkomstkontroll för team. Återställ oanvända resurser.

## <a name="pricing-for-automation"></a>Priser för Automation

Du kan granska priset för Azure Automation på [sidan med priser](https://azure.microsoft.com/pricing/details/automation/).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa ett Automation-konto](automation-quickstart-create-account.md)
