---
title: Azure Automation State Configuration-översikt
description: En översikt över Azure Automation tillstånd Configuration (DSC), dess villkor och kända problem
keywords: PowerShell dsc, önskad tillståndskonfiguration, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dd2ba0ec3427cd99da3321b50fb43f4c00f2d1a9
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/26/2019
ms.locfileid: "56822829"
---
# <a name="azure-automation-state-configuration-overview"></a>Azure Automation State Configuration-översikt

Azure Automation State Configuration är en Azure-tjänst som gör det möjligt att skriva, hantera och kompilera PowerShell Desired State Configuration (DSC) [konfigurationer](/powershell/dsc/configurations), importera [DSC-resurser](/powershell/dsc/resources), och tilldela konfigurationer till målnoder, allt i molnet.

## <a name="why-use-azure-automation-state-configuration"></a>Varför ska jag använda Azure Automation State Configuration

Azure Automation-Tillståndskonfiguration ger flera fördelar jämfört med hjälp av DSC utanför Azure.

### <a name="built-in-pull-server"></a>Inbyggda hämtningsserver

Azure Automation-Tillståndskonfiguration tillhandahåller en DSC-hämtningsserver som liknar den [Windows funktionen DSC-tjänst](/powershell/dsc/pullserver) så att målnoder får automatiskt konfigurationer, följer av önskat tillstånd och rapportera tillbaka om deras efterlevnad. Inbyggda hämtningsservern i Azure Automation eliminerar behovet av att ställa in och underhålla din egen hämtningsserver. Azure Automation kan riktas mot virtuella eller fysiska Windows- eller Linux datorer, i molnet eller lokalt.

### <a name="management-of-all-your-dsc-artifacts"></a>Hantering av alla DSC-artefakter

Azure Automation State Configuration tar samma hanteringslager till [PowerShell Desired State Configuration](/powershell/dsc/overview) som Azure Automation erbjuder för PowerShell-skript.

Från Azure portal eller PowerShell, kan du hantera alla dina DSC-konfigurationer, resurser och målnoder.

![Skärmbild av sidan Azure Automation](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Importera rapportering av data till Azure Monitor-loggar

Noder som hanteras med Azure Automation-Tillståndskonfiguration Skicka detaljerad status för rapporteringsdata till inbyggda hämtningsservern. Du kan konfigurera Azure Automation State Configuration för att skicka dessa data till Log Analytics-arbetsytan. Läs hur du skickar Tillståndskonfiguration statusdata till Log Analytics-arbetsytan i [vidarebefordra Azure Automation Tillståndskonfiguration rapporterar data till Azure Monitor-loggar](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Förutsättningar

Överväg följande krav när du använder Azure Automation tillstånd Configuration (DSC).

### <a name="operating-system-requirements"></a>Operativsystemkrav

För noder som kör Windows, stöds följande versioner:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

För noderna som kör Linux, stöds följande distributioner/versioner:

DSC-Linux-tillägget har stöd för Linux-distributioner [på Azure-godkända](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) utom:

Distribution | Version
-|-
Debian  | Alla versioner
Ubuntu  | 18.04

### <a name="dsc-requirements"></a>DSC-krav

För alla Windows-noder som körs i Azure, [WMF 5.1](https://docs.microsoft.com/powershell/wmf/5.1/install-configure) kommer att installeras under publiceringen.  För noder som kör Windows Server 2012 och Windows 7, [WinRM aktiveras](https://docs.microsoft.com/powershell/dsc/troubleshooting/troubleshooting#winrm-dependency).

Alla Linux-noder som körs i Azure, [PowerShell DSC för Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) kommer att installeras under publiceringen.

### <a name="network-planning"></a>Konfigurera privata nätverk

Om noderna finns i ett privat nätverk kan krävs följande port och URL: er för tillstånd Configuration (DSC) att kommunicera med Automation:

* Port: Endast TCP 443 krävs för utgående Internetåtkomst.
* Global URL: *.azure-automation.net
* Global URL: en för Virginia (USA-förvaltad region): *.azure automation.us
* Agent-tjänsten: https://\<workspaceId\>.agentsvc.azure-automation.net

Vi rekommenderar att du använder de adresser som anges när du definierar undantag. För IP-adresser som du kan ladda ned den [IP-intervall i Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653). Den här filen uppdateras varje vecka och har de aktuella intervall och eventuella kommande ändringar till IP-adressintervall.

Om du har ett Automation-konto som har definierats för en viss region kan begränsa du kommunikationen till det regionala datacentret. Följande tabell innehåller DNS-posten för varje region:

| **Region** | **DNS-post** |
| --- | --- |
| Västra centrala USA | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Södra centrala USA |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| USA, östra 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Centrala Kanada |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Västra Europa |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Norra Europa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Sydostasien |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Indien, centrala |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Östra Japan |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
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

## <a name="introduction-video"></a>Introduktionsfilm

Föredrar du att titta eller läsa? Ta en titt på följande videoklipp från maj 2015, när Azure Automation-Tillståndskonfiguration först lanserades.

> [!NOTE]
> Koncept och livscykel som beskrivs i den här videon är korrekta, har Azure Automation-Tillståndskonfiguration utvecklats mycket sedan videon spelades. Det är nu allmänt tillgänglig, har ett mycket mer omfattande användargränssnitt i Azure-portalen och har stöd för många ytterligare funktioner.

<iframe src="https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player" width="640" height="320" allowFullScreen="true" frameBorder="0"></iframe>

## <a name="next-steps"></a>Nästa steg

- Kom igång genom att se [komma igång med Azure Automation State Configuration](automation-dsc-getting-started.md)
- Läs hur för att publicera noder i [konfigurera datorer för hantering av Azure Automation State Configuration](automation-dsc-onboarding.md)
- Läs om hur du kompilera DSC-konfigurationer så att du kan tilldela dem till målnoder i [kompilera konfigurationer i Azure Automation State Configuration](automation-dsc-compile.md)
- PowerShell-cmdlet-referens, se [tillståndskonfigurationen för Azure Automation-cmdletar](/powershell/module/azurerm.automation/#automation)
- Information om priser finns i [priser för Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/)
- Om du vill se ett exempel på hur du använder Azure Automation-Tillståndskonfiguration i en pipeline för kontinuerlig distribution, se [kontinuerlig distribution med hjälp av Azure Automation Tillståndskonfiguration och Chocolatey](automation-dsc-cd-chocolatey.md)
