---
title: Översikt över Azure Automation tillstånds konfiguration
description: En översikt över Azure Automation tillstånds konfiguration (DSC), dess villkor och kända problem
keywords: PowerShell DSC, önskad tillstånds konfiguration, PowerShell DSC Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3f6d15e67122afcbea3cc294c803a302e961bdbd
ms.sourcegitcommit: 57a7d4f67635212f5bf0c56e58fd87c8ec366f2c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68372546"
---
# <a name="azure-automation-state-configuration-overview"></a>Översikt över Azure Automation tillstånds konfiguration

Azure Automation tillstånds konfiguration är en Azure-tjänst som gör att du kan skriva, hantera och kompilera PowerShell- [konfigurationer](/powershell/dsc/configurations)(Desired State Configuration), importera [DSC-resurser](/powershell/dsc/resources)och tilldela konfigurationer till mål noder, allt i kunde.

## <a name="why-use-azure-automation-state-configuration"></a>Varför ska du använda konfiguration av Azure Automation tillstånd

Azure Automation tillstånds konfiguration ger flera fördelar jämfört med att använda DSC utanför Azure.

### <a name="built-in-pull-server"></a>Inbyggd hämtnings Server

Azure Automation tillstånds konfiguration är en DSC-pull-server som liknar [Windows-funktionen DSC-service](/powershell/dsc/pullserver) så att målnoden automatiskt tar emot konfigurationer, överensstämmer med det önskade läget och rapporterar tillbaka till deras kompatibilitet. Den inbyggda hämtnings servern i Azure Automation eliminerar behovet av att konfigurera och underhålla en egen hämtnings Server. Azure Automation kan rikta in virtuella eller fysiska Windows-eller Linux-datorer, i molnet eller lokalt.

### <a name="management-of-all-your-dsc-artifacts"></a>Hantering av alla dina DSC-artefakter

Azure Automation tillstånds konfiguration hämtar samma hanterings skikt till [PowerShell Desired State Configuration](/powershell/dsc/overview) som Azure Automation erbjudanden för PowerShell-skript.

Från Azure Portal eller från PowerShell kan du hantera alla DSC-konfigurationer, resurser och mål-noder.

![Skärm bild av Azure Automation Sidan](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Importera rapport data till Azure Monitor loggar

Noder som hanteras med Azure Automation tillstånds konfiguration Skicka detaljerade rapporterings status data till den inbyggda hämtnings servern. Du kan konfigurera Azure Automation tillstånds konfiguration för att skicka dessa data till Log Analytics-arbetsytan. Information om hur du skickar status data för tillstånds konfiguration till din Log Analytics-arbetsyta finns i [vidarebefordra rapporterings data för Azure Automation tillstånds konfiguration till Azure Monitor loggar](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Förutsättningar

Överväg följande krav när du använder Azure Automation State Configuration (DSC).

### <a name="operating-system-requirements"></a>Operativ system krav

För noder som kör Windows stöds följande versioner:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

Följande distributioner/versioner stöds för noder som kör Linux:

DSC Linux-tillägget stöder alla Linux-distributioner som har godkänts [på Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) , förutom:

Distribution | Version
-|-
Debian  | alla versioner
Ubuntu  | 18,04

### <a name="dsc-requirements"></a>DSC-krav

För alla Windows-noder som körs i Azure installeras [WMF 5,1](https://docs.microsoft.com/powershell/wmf/setup/install-configure) vid onboarding.  För noder som kör Windows Server 2012 och Windows 7 [kommer WinRM att aktive ras](https://docs.microsoft.com/powershell/dsc/troubleshooting/troubleshooting#winrm-dependency).

För alla Linux-noder som körs i Azure installeras [POWERSHELL DSC för Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) vid onboarding.

### <a name="network-planning"></a>Konfigurera privata nätverk

Om noderna finns i ett privat nätverk krävs följande port och URL: er för tillstånds konfigurationen (DSC) för att kommunicera med Automation:

* Port: Endast TCP 443 krävs för utgående Internet åtkomst.
* Global URL: *. azure-automation.net
* Global URL för US Gov, Virginia: *. azure-automation.us
* Agent tjänst: https://\<workspaceId\>. agentsvc.Azure-Automation.net

Detta ger nätverks anslutningen för den hanterade noden att kommunicera med Azure Automation.
Om du använder DSC-resurser som kommunicerar mellan noder, till exempel [waitfor *-resurserna](https://docs.microsoft.com/powershell/dsc/reference/resources/windows/waitForAllResource), måste du också tillåta trafik mellan noderna.
Se dokumentationen för varje DSC-resurs för att förstå dessa nätverks krav.

#### <a name="proxy-support"></a>Stöd för proxy

Proxy-stöd för DSC-agenten finns i Windows version 1809 och senare.
Konfigurera det här alternativet genom att ange värdet för **ProxyURL** och **ProxyCredential** i [metaconfiguration-skriptet](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) som används för att registrera noder.
Proxy är inte tillgänglig i DSC för tidigare versioner av Windows.

För Linux-noder stöder DSC-agenten proxy och använder variabeln http_proxy för att fastställa URL: en.

#### <a name="azure-state-configuration-network-ranges-and-namespace"></a>Nätverks intervall och namnrymd för Azure State Configuration

Vi rekommenderar att du använder de adresser som anges när du definierar undantag. För IP-adresser kan du hämta [Microsoft Azure Data Center IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653). Den här filen uppdateras varje vecka och har de för närvarande distribuerade intervallen och eventuella kommande ändringar i IP-intervallen.

Om du har ett Automation-konto som har definierats för en viss region kan du begränsa kommunikationen till det regionala data centret. Följande tabell innehåller DNS-posten för varje region:

| **Region** | **DNS-post** |
| --- | --- |
| Västra centrala USA | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Södra centrala USA |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| East US   | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
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

Om du vill ha en lista över regions-IP-adresser i stället för region namn laddar du ned XML-filen för [Azure datacenter-IP-adress](https://www.microsoft.com/download/details.aspx?id=41653) från Microsoft Download Center

> [!NOTE]
> XML-filen för Azure datacenter-IP-adress innehåller de IP-adressintervall som används i Microsoft Azure Data Center. Filen innehåller beräknings-, SQL-och lagrings intervall.
>
>En uppdaterad fil publiceras varje vecka. Filen visar de för tillfället distribuerade intervallen och eventuella kommande ändringar i IP-intervallen. Nya intervall som visas i filen används inte i Data Center i minst en vecka.
>
> Det är en bra idé att ladda ned den nya XML-filen varje vecka. Uppdatera sedan webbplatsen för att identifiera tjänster som körs i Azure på rätt sätt. Azure ExpressRoute-användare bör Observera att den här filen används för att uppdatera Border Gateway Protocol-annonsering (BGP) för Azure-utrymmet under den första veckan i varje månad.

## <a name="introduction-video"></a>Introduktionsfilm

Föredrar du att titta eller läsa? Ta en titt på följande video från maj 2015 när Azure Automation State-konfigurationen först lanserades.

> [!NOTE]
> Medan begreppen och livs cykeln som diskuteras i den här videon är korrekta, har Azure Automation tillstånds konfigurationen förfallit ett parti sedan den här videon registrerades. Den är nu allmänt tillgänglig, har ett mycket mer omfattande gränssnitt i Azure Portal och har stöd för många ytterligare funktioner.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3467/player]

## <a name="next-steps"></a>Nästa steg

- För att komma igång, se [komma igång med konfiguration av Azure Automation tillstånd](automation-dsc-getting-started.md)
- Information om hur du kan publicera noder finns i [onboarding Machines for Management by Azure Automation State Configuration](automation-dsc-onboarding.md)
- Mer information om hur du kompilerar DSC-konfigurationer så att du kan tilldela dem till mål noder finns i [kompilera konfigurationer i Azure Automation tillstånds konfiguration](automation-dsc-compile.md)
- Referens för PowerShell-cmdlet finns i [Azure Automation cmdlets för tillstånds konfiguration](/powershell/module/azurerm.automation/#automation)
- För pris information, se [priser för Azure Automation tillstånds konfiguration](https://azure.microsoft.com/pricing/details/automation/)
- Om du vill se ett exempel på hur du använder Azure Automation tillstånds konfiguration i en pipeline för kontinuerlig distribution, se [kontinuerlig distribution med Azure Automation tillstånds konfiguration och choklad](automation-dsc-cd-chocolatey.md)
