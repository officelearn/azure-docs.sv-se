---
title: Integrera externa övervakningslösning med Azure Stack | Microsoft Docs
description: Lär dig hur du integrerar Azure Stack med en extern lösning för övervakning i ditt datacenter.
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
ms.date: 02/06/2019
ms.author: jeffgilb
ms.reviewer: thoroet
ms.lastreviewed: 02/06/2019
ms.openlocfilehash: 77dda80e538c8b742a96e7b7f81abe8650ee6b5d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59257305"
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Integrera externa övervakningslösning med Azure Stack

Du behöver övervaka Azure Stack-programvara, de fysiska datorerna och de fysiska nätverksväxlarna för externa övervakning av Azure Stack-infrastruktur. Var och en av dessa områden erbjuder en metod för att hämta information om hälsotillstånd och avisering:

- Azure Stack-software erbjuder en REST-baserat API för att hämta hälsotillstånd och aviseringar. Användning av programdefinierade tekniker som Storage Spaces Direct, storage hälsotillstånd och aviseringar är en del av programvaran övervakning.
- Fysiska datorer kan tillgängliggöra hälso- och aviseringsinformation via hanteringsstyrenheter för baskort (bmc).
- Fysiska nätverksenheter kan göra hälso- och aviseringsinformation tillgängliga via SNMP-protokollet.

Varje Azure Stack-lösning levereras med en maskinvara livscykel-värd. Den här värden körs Original Equipment Manufacturer (OEM) maskinvaruleverantören övervakningsprogram för fysiska servrar och nätverksenheter. Kontrollera med leverantören av OEM-tillverkare om deras övervakningslösningar kan integreras med befintliga övervakningslösningar i ditt datacenter.

> [!IMPORTANT]
> Den externa övervakningslösning som du använder måste vara utan Agent. Du kan inte installera agenter från tredje part i Azure Stack-komponenterna.

Följande diagram visar trafikflödet mellan ett integrerat Azure Stack-system, maskinvara livscykel värden, en extern lösning för övervakning och ett system för insamling av externa biljetter/data.

![Diagrammet visar trafik mellan Azure Stack, övervakning och ärenden lösning.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

> [!NOTE]
> Externa övervakning integrering direkt med fysiska servrar är inte tillåtna och blockerade aktivt av åtkomstkontrollistor (ACL).  Externa övervakning integrering direkt med fysiska nätverksenheter stöds, kontrollera med leverantören av OEM-tillverkare om hur du aktiverar den här funktionen.

Den här artikeln förklarar hur du integrerar Azure Stack med externa övervakningslösningar, till exempel System Center Operations Manager och Nagios. Den innehåller också hur du arbetar med aviseringar programmässigt med hjälp av PowerShell eller via REST API-anrop.

## <a name="integrate-with-operations-manager"></a>Integrera med Operations Manager

Du kan använda Operations Manager för externa övervakning av Azure Stack. System Center Management Pack för Microsoft Azure Stack kan du övervaka flera Azure Stack-distributioner med en enda Operations Manager-instans. Management pack använder Health-resursprovidern och uppdatera resource provider REST API: er för att kommunicera med Azure Stack. Om du planerar att kringgå OEM-tillverkaren övervakning av program som körs på värden för maskinvara livscykel, kan du installera leverantörshanteringspaket för att övervaka fysiska servrar. Du kan också använda Operations Manager identifiering av nätverksenheter för att övervaka nätverksväxlar.

Management pack för Azure Stack innehåller följande funktioner:

- Du kan hantera flera Azure Stack-distributioner
- Det finns stöd för Azure Active Directory (Azure AD) och Active Directory Federation Services (AD FS)
- Du kan hämta och stänga aviseringar
- Det finns en hälsotillstånd och en instrumentpanel för kapacitet
- Innehåller Underhållsläge för automatisk identifiering för när korrigeringar och uppdateringar (P & U) pågår
- Innehåller framtvinga en uppdatering uppgifter för distribution och region
- Du kan lägga till anpassad information till en region
- Har stöd för meddelanden och rapportering

Du kan hämta System Center Management Pack för Microsoft Azure Stack och den associerade [användarhandboken](https://www.microsoft.com/en-us/download/details.aspx?id=55184), eller direkt från Operations Manager.

Du kan integrera med System Center Service Manager Operations Manager för en lösning för supportloggning. Integrerad produktanslutningen gör det möjligt för dubbelriktad kommunikation som gör att du kan stänga en avisering i Azure Stack och Operations Manager när du har löst en tjänstbegäran i Service Manager.

Följande diagram visar integrering av Azure Stack med en befintlig System Center-distribution. Du kan automatisera Service Manager ytterligare med System Center Orchestrator eller Service Management Automation (SMA) för att köra åtgärder i Azure Stack.

![Diagram som visar integrering med OM Service Manager och SMA.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Integrera med Nagios

En Nagios övervakning plugin-programmet har utvecklats tillsammans med Cloudbase partnerlösningar som är tillgängliga under licens för Tillåtande kostnadsfri programvara – MIT (Massachusetts Institute of Technology).

Plugin-programmet är skrivet i Python och utnyttjar hälsotillstånd resource provider REST API. Den erbjuder grundläggande funktioner för att hämta och stänga aviseringar i Azure Stack. Den kan du lägga till flera Azure Stack-distributioner och skicka meddelanden som System Center management pack.

Plugin-programmet fungerar med Nagios Enterprise och Nagios Core. Du kan hämta det [här](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). Hämtningsplatsen innehåller också information om installation och konfiguration.

### <a name="plugin-parameters"></a>Plugin-programmet parametrar

Konfigurera plugin-fil ”Azurestack_plugin.py” med följande parametrar:

| Parameter | Beskrivning | Exempel |
|---------|---------|---------|
| *arm_endpoint* | Azure Resource Manager (administratör)-slutpunkt | https://adminmanagement.local.azurestack.external |
| *api_endpoint* | Azure Resource Manager (administratör)-slutpunkt  | https://adminmanagement.local.azurestack.external |
| *Tenant_id* | Administratören prenumerations-ID | Hämta via administratörsportalen eller PowerShell |
| *User_name* | Operatorn prenumeration användarnamn | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | Operatorn prenumeration lösenord | mittlösenord |
| *Client_id* | Client | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *region* |  Azure Stack-Regionsnamn | lokal |
|  |  |

* PowerShell GUID som tillhandahålls är universal. Du kan använda den för varje distribution.

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Använd PowerShell för att övervaka hälsotillstånd och aviseringar

Om du inte använder Operations Manager, Nagios eller en Nagios-baserad lösning, kan du använda PowerShell för att aktivera ett brett utbud av övervakning av lösningar som kan integreras med Azure Stack.

1. Om du vill använda PowerShell, se till att du har [PowerShell installeras och konfigureras](azure-stack-powershell-configure-quickstart.md) för en Azure-stacken operator-miljö. Installera PowerShell på en lokal dator som kan nå slutpunkten för Resource Manager (administratör) (https://adminmanagement. [Region]. [External_FQDN]).

2. Kör följande kommandon för att ansluta till Azure Stack-miljön som Azure Stack-operatör:

   ```powershell
   Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https://adminmanagement.[Region].[External_FQDN]

   Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```

3. Använd kommandon till exempel för att arbeta med aviseringar:
   ```powershell
    #Retrieve all alerts
    $Alerts = Get-AzsAlert
    $Alerts

    #Filter for active alerts
    $Active = $Alerts | Where-Object { $_.State -eq "active" }
    $Active

    #Close alert
    Close-AzsAlert -AlertID "ID"

    #Retrieve resource provider health
    $RPHealth = Get-AzsRPHealth
    $RPHealth

    #Retrieve infrastructure role instance health
    $FRPID = $RPHealth | Where-Object { $_.DisplayName -eq "Capacity" }
    Get-AzsRegistrationHealth -ServiceRegistrationId $FRPID.RegistrationId
    ```

## <a name="learn-more"></a>Läs mer

Läs om hur inbyggda hälsoövervakning [övervaka hälsotillstånd och aviseringar i Azure Stack](azure-stack-monitor-health.md).

## <a name="next-steps"></a>Nästa steg

[Security-integrering](azure-stack-integrate-security.md)
