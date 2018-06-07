---
title: Integrera externa övervakningslösning med Azure-stacken | Microsoft Docs
description: Lär dig mer om att integrera Azure stacken med en extern övervakningslösning i ditt datacenter.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 05/10/2018
ms.author: jeffgilb
ms.reviewer: thoroet
ms.openlocfilehash: d7c8520602132722fd0c7138de4a276b9ac2208a
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2018
ms.locfileid: "34807347"
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Integrera externa övervakningslösning med Azure-stacken

Du behöver övervaka Azure Stack-programvara, fysiska datorer och fysiska nätverksväxlar för övervakning av externa Azure Stack-infrastrukturen. Dessa områden ger en metod för att hämta information om hälsa och avisering:

- Azure Stack programvara erbjuder ett REST-baserad API för att hämta hälsotillstånd och aviseringar. Användning av programvarudefinierade som Storage Spaces Direct, lagring, hälsotillstånd och aviseringar är en del av programvaran övervakning.
- Fysiska datorer kan göra hälsa och aviseringsinformation tillgängliga via huvudkortshanteringskontroller (bmc).
- Fysiska nätverksenheter kan göra hälsa och aviseringsinformation tillgängliga via SNMP-protokollet.

Varje Azure Stack-lösning levereras med en maskinvara livscykel värd. Den här värden körs OEM-tillverkaren (OEM) maskinvaruleverantören övervakningsprogram för fysiska servrar och nätverksenheter. Om du vill kan du kringgå övervakningsinställningarna lösningar och integrera med befintliga övervakning direkt i ditt datacenter.

> [!IMPORTANT]
> Den externa övervakningslösning som du använder måste vara utan Agent. Du kan inte installera agenter från tredje part i Azure Stack-komponenter.

Följande diagram visar trafikflödet mellan en Azure-stacken integrerat system, maskinvara livscykel värden, en extern övervakningslösning och ett system för insamling av externa biljetter/data.

![Diagram över trafik mellan Azure stacken, övervakning och biljetter lösning.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

Den här artikeln förklarar hur du integrerar Azure stacken med externa övervakningslösningar, till exempel System Center Operations Manager och Nagios. Den omfattar också hur du arbetar med aviseringar via programmering med hjälp av PowerShell eller via REST API-anrop.

## <a name="integrate-with-operations-manager"></a>Integrera med Operations Manager

Du kan använda Operations Manager för externa övervakning av Azure-stacken. System Center Management Pack för Microsoft Azure-Stack kan du övervaka flera Azure-stacken distributioner med en Operations Manager-instans. Management pack använder hälsa resursprovidern och uppdatera resursprovidern REST API: er för att kommunicera med Azure-stacken. Om du planerar att kringgå OEM övervakning av program som körs på värden för maskinvara livscykel, kan du installera leverantörshanteringspaket för övervakning av fysiska servrar. Du kan också använda Operations Manager identifiering av nätverksenheter för att övervaka nätverksväxlar.

Management pack för Azure-stacken innehåller följande funktioner:

- Du kan hantera flera Azure Stack-distributioner
- Det finns stöd för Azure Active Directory (Azure AD) och Active Directory Federation Services (AD FS)
- Du kan hämta och stänga aviseringar
- Det finns en hälsa och en instrumentpanel kapacitet
- Innehåller Underhållsläge för automatisk identifiering för när korrigeringar och uppdateringar (P & U) pågår
- Innehåller framtvinga en uppdatering uppgifter för distribution och region
- Du kan lägga till anpassad information till en region
- Har stöd för meddelanden och rapportering

Du kan hämta System Center Management Pack för Microsoft Azure-stacken och den associerade [användarhandboken](https://www.microsoft.com/en-us/download/details.aspx?id=55184), eller direkt från Operations Manager.

Du kan integrera med Service Manager i System Center Operations Manager för en loggnings-lösning. Integrerad product connector kan dubbelriktad kommunikation som gör att du kan stänga en avisering i Azure-stacken och Operations Manager när du har löst tjänstbegäran i Service Manager.

Följande diagram visar integrering av Azure-stacken med en befintlig distribution av System Center. Du kan automatisera Service Manager ytterligare med System Center Orchestrator eller Service Management Automation (SMA) för att köra åtgärder i Azure-stacken.

![Diagram över integrering med OM, Service Manager och SMA.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Integrera med Nagios

En Nagios övervakning plugin-programmet har utvecklats tillsammans med Cloudbase partnerlösningar som är tillgängliga under licensen Tillåtande kostnadsfri programvara – MIT (Massachusetts Institute of Technology).

Plugin-programmet har skrivits i Python och utnyttjar resursprovidern hälsa REST API. Den erbjuder grundläggande funktioner för att hämta och Stäng varningar i Azure-stacken. Den låter dig lägga till flera Azure Stack-distributioner och skicka meddelanden som System Center management pack.

Plugin-programmet fungerar med Nagios Enterprise och Nagios kärnor. Du kan ladda ned det [här](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). Hämtningsplatsen innehåller också information om installation och konfiguration.

### <a name="plugin-parameters"></a>Plugin-parametrar

Konfigurera plugin-fil ”Azurestack_plugin.py” med följande parametrar:

| Parameter | Beskrivning | Exempel |
|---------|---------|---------|
| *arm_endpoint* | Azure Resource Manager (administratör) slutpunkt |https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Azure Resource Manager (administratör) slutpunkt  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | Admin prenumerations-ID | Hämta via administratörsportal eller PowerShell |
| *Användarnamn* | Operatorn prenumeration användarnamn | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | Operatorn prenumeration lösenord | mittlösenord |
| *client_id* | Client | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 * |
| *Region* |  Namn på Azure Stack område | lokal |
|  |  |

* PowerShell GUID som tillhandahålls är universal. Du kan använda den för varje distribution.

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Använda PowerShell för att övervaka hälsotillstånd och aviseringar

Om du inte använder Operations Manager, Nagios eller en Nagios-baserad lösning kan använda du PowerShell för att aktivera ett brett spektrum av övervakning av lösningar som kan integreras med Azure-stacken.

1. Om du vill använda PowerShell, se till att du har [PowerShell installeras och konfigureras](azure-stack-powershell-configure-quickstart.md) för en Azure-stacken operator-miljö. Installera PowerShell på en lokal dator som kan nå slutpunkten för Resource Manager (administratör) (https://adminmanagement. [Region]. [External_FQDN]).

2. Kör följande kommandon för att ansluta till Azure Stack-miljö som operatör Azure Stack:

   ```PowerShell  
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN]

   Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```

3. Använd kommandon, till exempel i följande exempel för att arbeta med aviseringar:
   ```PowerShell
    #Retrieve all alerts
    Get-AzsAlert

    #Filter for active alerts
    $Active=Get-AzsAlert | Where {$_.State -eq "active"}
    $Active

    #Close alert
    Close-AzsAlert -AlertID "ID"

    #Retrieve resource provider health
    Get-AzsRPHealth

    #Retrieve infrastructure role instance health
    $FRPID=Get-AzsRPHealth|Where-Object {$_.DisplayName -eq "Capacity"}
    Get-AzsRegistrationHealth -ServiceRegistrationId $FRPID.RegistrationId

    ```

## <a name="learn-more"></a>Läs mer

Information om inbyggda hälsoövervakning finns [övervaka hälsotillstånd och aviseringar i Azure-stacken](azure-stack-monitor-health.md).

## <a name="next-steps"></a>Nästa steg

[Integrering av säkerhet](azure-stack-integrate-security.md)
