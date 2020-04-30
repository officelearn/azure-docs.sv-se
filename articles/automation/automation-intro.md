---
title: Översikt över Azure Automation
description: Lär dig hur du använder Azure Automation för att automatisera livscykeln för infrastruktur och program.
services: automation
ms.subservice: process-automation
keywords: Azure Automation, DSC, PowerShell, tillstånds konfiguration, uppdaterings hantering, ändrings spårning, DSC, inventering, Runbooks, python, grafisk
ms.date: 10/18/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: 8ee8fd4d9a81746be7b65aeb6410691a5e3aea96
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81010247"
---
# <a name="an-introduction-to-azure-automation"></a>En introduktion till Azure Automation

Den här artikeln innehåller en kort översikt över Azure Automation och ger svar på några vanliga frågor. Om du vill ha mer information om de olika funktionerna kan du välja länkarna i den här översikten.

## <a name="about-azure-automation"></a>Om Azure Automation

Azure Automation levererar en molnbaserad automatiserings-och konfigurations tjänst som stöder konsekvent hantering i dina Azure-och icke-Azure-miljöer. Den omfattar process automatisering, konfigurations hantering, uppdaterings hantering, delade funktioner och heterogena funktioner. Automation ger dig fullständig kontroll under distribution, drift och inaktive ring av arbets belastningar och resurser.

![Automatiserings funktioner](media/automation-overview/automation-overview.png)

## <a name="process-automation"></a>Processautomatisering

Process automatisering i Azure Automation gör att du kan automatisera frekventa, tids krävande och fel känsliga moln hanterings uppgifter. Den här tjänsten hjälper dig att fokusera på arbete som lägger till ett affärs värde. Genom att minska felkällorna och öka effektiviteten bidrar det också till att sänka dina driftskostnader. Process automatiserings operativ miljön beskrivs i [Runbook-körningen i Azure Automation](automation-runbook-execution.md).

Process automatisering stöder integrering av Azure-tjänster och andra offentliga system som krävs för att distribuera, konfigurera och hantera dina slut punkt till slut punkts processer. Med tjänsten kan du redigera [Runbooks](automation-runbook-types.md) grafiskt, i PowerShell eller med hjälp av python. Genom att använda en [hybrid Runbook Worker](automation-hybrid-runbook-worker.md)kan du förena hanteringen genom att dirigera i lokala miljöer. Med [Webhooks](automation-webhooks.md) kan du utföra begär Anden och säkerställa kontinuerlig leverans och drift genom att utlösa automatisering från ITSM, DevOps och övervaknings system. 

## <a name="configuration-management"></a>Konfigurationshantering

Azure Automation [tillstånds konfiguration](automation-dsc-overview.md) är en molnbaserad lösning för PowerShell (önskad tillstånds konfiguration) som tillhandahåller tjänster för företags miljöer. Med den här funktionen kan du hantera dina DSC-resurser i Azure Automation och tillämpa konfigurationer på virtuella eller fysiska datorer från en DSC-pull-server i Azure-molnet. Du kan övervaka och automatiskt uppdatera datorkonfigurationer mellan fysiska och virtuella datorer, i Windows eller Linux, i molnet eller lokalt. Med inventerings support kan du söka efter insyn i installerade program och andra konfigurations objekt i gäst resurser.
 
Tjänsten för Azure Automation tillstånds konfiguration ger omfattande rapporterings-och Sök funktioner. Du kan använda dessa funktioner för att hitta detaljerad information om vad som är konfigurerat i ett operativ system. Tjänsten stöder ändrings spårning i tjänster, daemons, program vara, register och filer i din miljö för att hjälpa dig att diagnostisera oönskade ändringar och utlösa aviseringar. En viktig relaterad funktion är rapportering av större händelser, till exempel händelser som utfärdats när noder avviker från sina tilldelade konfigurationer. 

## <a name="update-management"></a>Hantering av uppdateringar

Azure Automation innehåller lösningen för [uppdaterings hantering](automation-update-management.md) för Windows-och Linux-system i hybrid miljöer. Med den här lösningen får du insyn i att uppdatera kompatibiliteten mellan Azure och andra moln och lokalt. Med uppdaterings hantering kan du skapa schemalagda distributioner som dirigerar installationen av uppdateringar i en definierad underhålls period. Om en uppdatering inte bör installeras på en dator kan du använda funktionerna för uppdaterings hantering för att undanta den från en distribution.

## <a name="shared-capabilities"></a>Delade funktioner

Azure Automation erbjuder ett antal delade funktioner, inklusive delade resurser, rollbaserad åtkomst kontroll, flexibel schemaläggning, käll kontroll integrering, granskning och taggning.

### <a name="shared-resources"></a><a name="shared-resources"></a>Delade resurser

Azure Automation består av en uppsättning delade resurser som gör det enklare att automatisera och konfigurera dina miljöer i större skala.

* **[Scheman](automation-schedules.md)** – utlösa automatiserings åtgärder vid fördefinierade tidpunkter.
* **[Moduler](automation-integration-modules.md)** – Hantera Azure och andra system. Du kan importera moduler till Automation-kontot för Microsoft, tredje part, community och anpassade cmdlets och DSC-resurser.
* **[Galleri för moduler](automation-runbook-gallery.md)** – stöder inbyggd integrering med PowerShell-galleriet så att du kan visa Runbooks och importera dem till Automation-kontot. Galleriet gör det möjligt för dig att snabbt komma igång med att integrera och redigera dina processer från PowerShell-galleriet och Microsoft Script Center.
* **[Python 2-paket](python-packages.md)** – stöd för python 2-Runbooks för ditt Automation-konto.
* **[Autentiseringsuppgifter](automation-credentials.md)** – lagra känslig information på ett säkert sätt som Runbooks och konfigurationer kan använda vid körning.
* **[Anslutningar](automation-connections.md)** – lagra namn-värde-par med gemensam information för anslutningar till system. Modulen författare definierar anslutningar i Runbooks och konfigurationer som ska användas vid körning.
* **[Certifikat](automation-certificates.md)** – definiera information som ska användas vid autentisering och skydd av distribuerade resurser vid användning av RUNBOOKS eller DSC-konfigurationer vid körning. 
* **[Variabler](automation-variables.md)** -Behåll innehåll som kan användas mellan Runbooks och konfigurationer. Du kan ändra variabel värden utan att behöva ändra någon av Runbooks eller konfigurationer som refererar till dem.

### <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Azure Automation stöder rollbaserad åtkomst kontroll (RBAC) för att reglera åtkomsten till Automation-kontot och dess resurser. Mer information om hur du konfigurerar RBAC på ditt Automation-konto, Runbooks och jobb finns i [rollbaserad åtkomst kontroll för Azure Automation](automation-role-based-access-control.md).

### <a name="source-control-integration"></a>Källkontrollsintegrering

Med Azure Automation kan du [integrera käll kontroll](source-control-integration.md). Den här funktionen höjer konfigurationen som kod där Runbooks eller konfigurationer kan kontrol leras i ett käll kontroll system.

## <a name="heterogeneous-support-windows-and-linux"></a>Heterogent stöd (Windows och Linux)

Automation är utformat för att fungera i din hybrid moln miljö och även i dina Windows-och Linux-system. Det ger ett konsekvent sätt att automatisera och konfigurera distribuerade arbets belastningar och de operativ system som kör dem.

## <a name="common-scenarios-for-automation"></a>Vanliga scenarier för Automation

Azure Automation stöder hantering under hela livs cykeln för din infrastruktur och dina program. Här följer exempel på några vanliga scenarier:

* **Skriv Runbooks** – skapa PowerShell, PowerShell-arbetsflöde, grafiska, python 2 och DSC-Runbooks på vanliga språk. 
* **Bygg och distribuera resurser** – distribuera virtuella datorer i en hybrid miljö med hjälp av runbooks och Azure Resource Manager mallar. Integrera i utvecklingsverktyg, till exempel Jenkins och Azure DevOps.
* **Konfigurera virtuella datorer** – utvärdera och konfigurera Windows-och Linux-datorer med konfigurationer för infrastrukturen och programmet.
* **Dela kunskaps** överförings information i systemet om hur din organisation levererar och underhåller arbets belastningar. 
* **Hämta inventering** – hämta en fullständig inventering av distribuerade resurser för mål, rapportering och efterlevnad. 
* **Hitta ändringar** – identifiera ändringar som kan orsaka felaktig konfiguration och förbättra operativa krav.
* **Övervaka** – isolera dator ändringar som orsakar problem och åtgärda eller eskalera dem till hanterings system.
* **Skydda** – karantän datorer om säkerhets aviseringar aktive ras. Ange gästkrav.
* **Styr** – konfigurera RBAC för Teams. Återställ oanvända resurser.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-automation"></a>Priser för Automation

Du kan granska priserna som är kopplade till Azure Automation på sidan med [priser](https://azure.microsoft.com/pricing/details/automation/) .

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa ett Automation-konto](automation-quickstart-create-account.md)

