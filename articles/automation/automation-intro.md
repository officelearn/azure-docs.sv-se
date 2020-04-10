---
title: Översikt över Azure Automation
description: Lär dig hur du använder Azure Automation för att automatisera livscykeln för infrastruktur och program.
services: automation
ms.subservice: process-automation
keywords: azure automation, DSC, powershell, tillståndskonfiguration, uppdateringshantering, ändringsspårning, DSC, lager, runbooks, python, grafisk
ms.date: 10/18/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: 8ee8fd4d9a81746be7b65aeb6410691a5e3aea96
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010247"
---
# <a name="an-introduction-to-azure-automation"></a>En introduktion till Azure Automation

Den här artikeln innehåller en kort översikt över Azure Automation och ger svar på några vanliga frågor. Om du vill ha mer information om de olika funktionerna kan du välja länkarna i den här översikten.

## <a name="about-azure-automation"></a>Om Azure Automation

Azure Automation levererar en molnbaserad automatiserings- och konfigurationstjänst som stöder konsekvent hantering i dina Azure- och icke-Azure-miljöer. Den omfattar processautomatisering, konfigurationshantering, uppdateringshantering, delade funktioner och heterogena funktioner. Automatisering ger dig fullständig kontroll under distribution, drift och avveckling av arbetsbelastningar och resurser.

![Funktioner för automatisering](media/automation-overview/automation-overview.png)

## <a name="process-automation"></a>Processautomatisering

Med processautomatisering i Azure Automation kan du automatisera frekventa, tidskrävande och felbenägna molnhanteringsuppgifter. Den här tjänsten hjälper dig att fokusera på arbete som tillför affärsvärde. Genom att minska felkällorna och öka effektiviteten bidrar det också till att sänka dina driftskostnader. Processautomatiseringens driftsmiljö beskrivs i [Runbook-körningen i Azure Automation](automation-runbook-execution.md).

Processautomatisering stöder integrering av Azure-tjänster och andra offentliga system som krävs för att distribuera, konfigurera och hantera dina heltäckande processer. Med tjänsten kan du skapa [runbooks](automation-runbook-types.md) grafiskt, i PowerShell eller använda Python. Genom att använda en [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)kan du förena hanteringen genom att orkestrera i lokala miljöer. [Webhooks](automation-webhooks.md) låter dig uppfylla förfrågningar och säkerställa kontinuerlig leverans och åtgärder genom att utlösa automatisering från ITSM, DevOps och övervakningssystem. 

## <a name="configuration-management"></a>Konfigurationshantering

Azure Automation [state configuration](automation-dsc-overview.md) är en molnbaserad lösning för PowerShell desired state configuration (DSC) som tillhandahåller tjänster för företagsmiljöer. Med den här funktionen kan du hantera dina DSC-resurser i Azure Automation och tillämpa konfigurationer på virtuella eller fysiska datorer från en DSC-pull-server i Azure-molnet. Du kan övervaka och automatiskt uppdatera datorkonfigurationer över fysiska och virtuella datorer, på Windows eller Linux, i molnet eller lokalt. Med lagerstöd kan du fråga in-guest-resurser för insyn i installerade program och andra konfigurationsobjekt.
 
Konfigurationstjänsten för Azure Automation-tillstånd ger omfattande rapporterings- och sökfunktioner. Du kan använda dessa funktioner för att hitta detaljerad information om vad som är konfigurerat i ett operativsystem. Tjänsten stöder ändringsspårning mellan tjänster, demoner, programvara, register och filer i din miljö för att hjälpa dig att diagnostisera oönskade ändringar och höja aviseringar. En viktig relaterad funktion är rapportering av större händelser, till exempel händelser som utfärdas när noder avviker från sina tilldelade konfigurationer. 

## <a name="update-management"></a>Hantering av uppdateringar

Azure Automation innehåller [uppdateringshanteringslösningen](automation-update-management.md) för Windows- och Linux-system i hybridmiljöer. Med den här lösningen får du insyn i uppdateringsefterlevnad över Azure och andra moln och lokalt. Med uppdateringshantering kan du skapa schemalagda distributioner som dirigerar installationen av uppdateringar i ett definierat underhållsfönster. Om en uppdatering inte ska installeras på en dator kan du använda uppdateringshanteringsfunktioner för att utesluta den från en distribution.

## <a name="shared-capabilities"></a>Delade funktioner

Azure Automation erbjuder ett antal delade funktioner, inklusive delade resurser, rollbaserad åtkomstkontroll, flexibel schemaläggning, källkontrollintegrering, granskning och taggning.

### <a name="shared-resources"></a><a name="shared-resources"></a>Delade resurser

Azure Automation består av en uppsättning delade resurser som gör det enklare att automatisera och konfigurera dina miljöer i större skala.

* **[Scheman](automation-schedules.md)** - Trigger Automation-åtgärder vid fördefinierade tider.
* **[Moduler](automation-integration-modules.md)** - Hantera Azure och andra system. Du kan importera moduler till Automation-kontot för Microsoft- och tredjeparts-, community- och anpassade cmdlets- och DSC-resurser.
* **[Galleriet Moduler](automation-runbook-gallery.md)** – stöder inbyggd integrering med PowerShell-galleriet så att du kan visa runbooks och importera dem till Automation-kontot. I galleriet kan du snabbt komma igång med att integrera och redigera dina processer från PowerShell-galleriet och Microsoft Script Center.
* **[Python 2-paket](python-packages.md)** - Stöd Python 2 runbooks för ditt Automation-konto.
* **[Autentiseringsuppgifter](automation-credentials.md)** - Lagra känslig information som runbooks och konfigurationer kan använda på körning.
* **[Anslutningar](automation-connections.md)** - Lagra namn-värde par av gemensam information för anslutningar till system. Modulförfattaren definierar anslutningar i runbooks och konfigurationer för användning vid körning.
* **[Certifikat](automation-certificates.md)** - Definiera information som ska användas vid autentisering och säkring av distribuerade resurser när de används av runbooks eller DSC-konfigurationer under körning. 
* **[Variabler](automation-variables.md)** - Håll ned innehåll som kan användas över runbooks och konfigurationer. Du kan ändra variabla värden utan att behöva ändra någon av de runbooks eller konfigurationer som refererar till dem.

### <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Azure Automation stöder rollbaserad åtkomstkontroll (RBAC) för att reglera åtkomsten till Automation-kontot och dess resurser. Mer information om hur du konfigurerar RBAC på ditt Automation-konto, runbooks och jobb finns i [Rollbaserad åtkomstkontroll för Azure Automation](automation-role-based-access-control.md).

### <a name="source-control-integration"></a>Källkontrollsintegrering

Azure Automation tillåter [källkontrollintegration](source-control-integration.md). Den här funktionen främjar konfigurationen som kod där runbooks eller konfigurationer kan checkas in i ett källkontrollsystem.

## <a name="heterogeneous-support-windows-and-linux"></a>Heterogent stöd (Windows och Linux)

Automation är utformat för att fungera i din hybridmolnmiljö och även dina Windows- och Linux-system. Det ger ett konsekvent sätt att automatisera och konfigurera distribuerade arbetsbelastningar och de operativsystem som kör dem.

## <a name="common-scenarios-for-automation"></a>Vanliga scenarier för Automation

Azure Automation stöder hantering under hela livscykeln för din infrastruktur och dina program. Här följer exempel på några vanliga scenarier:

* **Skriv runbooks** - Författare PowerShell, PowerShell Workflow, grafik, Python 2 och DSC runbooks på vanliga språk. 
* **Skapa och distribuera resurser** – Distribuera virtuella datorer i en hybridmiljö med runbooks och Azure Resource Manager-mallar. Integrera i utvecklingsverktyg som Jenkins och Azure DevOps.
* **Konfigurera virtuella datorer** – utvärdera och konfigurera Windows- och Linux-datorer med konfigurationer för infrastrukturen och programmet.
* **Dela kunskap** - Överför kunskap till systemet om hur din organisation levererar och underhåller arbetsbelastningar. 
* **Hämta lager** - Få en fullständig inventering av distribuerade resurser för inriktning, rapportering och efterlevnad. 
* **Hitta ändringar** - Identifiera ändringar som kan orsaka felkonfiguration och förbättra den operativa efterlevnaden.
* **Övervaka** - Isolera datorändringar som orsakar problem och åtgärda eller eskalera dem till hanteringssystem.
* **Skydda** - Karantändatorer om säkerhetsvarningar utlöses. Ange gästkrav.
* **Govern** - Ställ in RBAC för team. Återställ oanvända resurser.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-automation"></a>Priser för Automation

Du kan granska priserna som är associerade med Azure Automation på [prissidan.](https://azure.microsoft.com/pricing/details/automation/)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa ett automatiseringskonto](automation-quickstart-create-account.md)

