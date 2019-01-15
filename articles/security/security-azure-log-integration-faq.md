---
title: Azure-Loggintegrering vanliga frågor och svar | Microsoft Docs
description: Den här artikeln får du svar på frågor om Azure Log Integration.
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: d06d1ac5-5c3b-49de-800e-4d54b3064c64
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload8: na
ms.date: 01/14/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 928c7921ce184f5f17916c3b9e182ce3754b0115
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2019
ms.locfileid: "54306275"
---
# <a name="azure-log-integration-faq"></a>Azure-Loggintegrering vanliga frågor och svar

Den här artikeln får du svar på vanliga frågor och svar (FAQ) om Azure Log Integration.

>[!IMPORTANT]
> Funktionen Azure Log integration upphör att gälla genom 06/01/2019. AzLog hämtningar har inaktiverats på den 27 juni 2018. För information om vad du gör Flytta framåt granska inlägget [Använd Azure monitor för att integrera med SIEM-verktyg](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Azure Log Integration är en tjänst i Windows-operativsystemet som du kan använda för att integrera dina lokala säkerheten och händelsehantering (SIEM) hanteringssystem loggarna från dina Azure-resurser. Den här integreringen ger en enhetlig instrumentpanel för alla dina tillgångar, lokalt eller i molnet. Du kan sedan aggregera, korrelera, analysera och varna för säkerhetshändelser som är associerade med dina program.

Den bästa metoden för att integrera Azure loggar är genom att använda din SIEM-leverantör Azure Monitor-koppling och följa de här [instruktioner](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). Men om din SIEM-leverantör inte tillhandahåller en anslutning till Azure Monitor, du kan använda Azure Log Integration som en tillfällig lösning (om din siem-server som stöds av Azure Log Integration) tills en sådan anslutning är tillgänglig.

## <a name="is-the-azure-log-integration-software-free"></a>Azure Log Integration-programvara är kostnadsfritt?

Ja. Det är gratis för Azure Log Integration-programvara.

## <a name="where-is-azure-log-integration-available"></a>Var finns Azure Log Integration?

Det är för närvarande tillgängligt i Azure Commercial och Azure Government och är inte tillgänglig i Kina och Tyskland.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs"></a>Hur kan jag se storage-konton som Azure Log Integration hämta loggar för virtuella Azure-datorer?

Kör kommandot **AzLog källistan**.

## <a name="how-can-i-tell-which-subscription-the-azure-log-integration-logs-are-from"></a>Hur vet jag vilken prenumeration som Azure Log Integration loggarna är från?

När det gäller granskningsloggar som placeras i den **AzureResourcemanagerJson** kataloger, prenumerations-ID är i loggfilens namn. Detta gäller även för loggar i den **AzureSecurityCenterJson** mapp. Exempel:

20170407T070805_2768037.0000000023.**1111e5ee-1111-111b-a11e-1e111e1111dc**.json

Azure Active Directory med granskningsloggar innehåller klient-ID som en del av namnet.

Diagnostikloggar som läses från en event hub omfattar inte prenumerations-ID som en del av namnet. I stället inkluderar de det egna namnet som angetts som en del av skapandet av hub-händelsekälla. 

## <a name="how-can-i-update-the-proxy-configuration"></a>Hur kan jag uppdatera proxykonfigurationen?

Om din proxyinställning inte tillåter åtkomst till Azure storage direkt, öppna den **AZLOG. EXE. CONFIG** fil i **c:\Program Files\Microsoft Azure Log Integration**. Uppdatera filen att inkludera den **defaultProxy** avsnittet med proxyadress i din organisation. När uppdateringen är klar, stoppa och starta tjänsten med hjälp av kommandona **net stoppa AzLog** och **net start AzLog**.

    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
      <system.net>
        <connectionManagement>
          <add address="*" maxconnection="400" />
        </connectionManagement>
        <defaultProxy>
          <proxy usesystemdefault="true"
          proxyaddress="http://127.0.0.1:8888"
          bypassonlocal="true" />
        </defaultProxy>
      </system.net>
      <system.diagnostics>
        <performanceCounters filemappingsize="20971520" />
      </system.diagnostics>   

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Hur kan jag se information om prenumerationen i Windows-händelser?

Lägg till prenumerations-ID till det egna namnet när du lägger till källan:

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  
Händelsen XML har följande metadata, inklusive prenumerations-ID:

![Händelsen XML][1]

## <a name="error-messages"></a>Felmeddelanden
### <a name="when-i-run-the-command-azlog-createazureid-why-do-i-get-the-following-error"></a>När jag kör kommandot ```AzLog createazureid```, varför kan jag få följande fel?

Fel:

  *Det gick inte att skapa program för AAD - klient 72f988bf-86f1-41af-91ab-2d7cd011db37-orsak = ”förbjuden” - meddelande = 'Inte tillräcklig behörighet för att slutföra åtgärden ”.*

Den **azlog createazureid** kommando görs ett försök att skapa ett huvudnamn för tjänsten i alla Azure AD-klienter för prenumerationer som Azure inloggningen har åtkomst till. Om Azure-autentiseringsuppgifter är bara en gästanvändare i den Azure AD-klienten kan misslyckas kommandot ”otillräckliga behörigheter för att slutföra åtgärden”. Be en administratör att lägga till ditt konto som en användare i klienten.

### <a name="when-i-run-the-command-azlog-authorize-why-do-i-get-the-following-error"></a>När jag kör kommandot **azlog auktorisera**, varför kan jag få följande fel?

Fel:

  *Skapa rolltilldelning - AuthorizationFailed varning Klienten janedo@microsoft.com' med objekt id 'fe9e03e4-4dad-4328-910f-fd24a9660bd2' har inte behörighet att utföra åtgärden ”Microsoft.Authorization/roleAssignments/write' över område '/ subscriptions/70 d 95299-d689-4 c 97-b971-0d8ff0000000”.*

Den **azlog auktorisera** kommandot tilldelar rollen läsare till AD-tjänstens huvudnamn (skapad med **azlog createazureid**) till de angivna prenumerationerna. Om Azure-inloggning inte är en delad administratör eller ägare till prenumerationen kan misslyckas med felmeddelandet ”auktorisering misslyckades”. Azure rollbaserad åtkomstkontroll (RBAC) för delad administratör eller ägare krävs för att slutföra åtgärden.

## <a name="where-can-i-find-the-definition-of-the-properties-in-the-audit-log"></a>Var hittar jag definitionen av egenskaperna i granskningsloggen?

Se:

* [Granska åtgärder med Azure Resource Manager](../azure-resource-manager/resource-group-audit.md)
* [Lista händelserna i en prenumeration i Azure Monitor REST API management](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Var hittar jag information om Azure Security Center-aviseringar

Se [hantera och åtgärda säkerhetsaviseringar i Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md).

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>Hur kan jag ändra vad som samlas in med VM-diagnostik?

Information om hur du hämtar, ändra och ställer in Azure Diagnostics-konfigurationen finns i [Använd PowerShell för att aktivera Azure Diagnostics i en virtuell dator med Windows](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

I följande exempel hämtas Azure Diagnostics-konfigurationen:

    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

I följande exempel ändrar Azure Diagnostics-konfigurationen. I den här konfigurationen samlas endast händelse-ID 4624 och händelse-ID 4625 in från säkerhetshändelseloggen. Microsoft Antimalware för Azure-händelser samlas in från systemets händelselogg. Mer information om användning av XPath-uttryck, finns i [förbruka händelser](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85)).

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

I följande exempel anger konfigurationen av Azure Diagnostics:

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

När du gör ändringar, kontrollerar du storage-konto för att säkerställa att rätt händelserna ska samlas in.

Om du har problem under installationen och konfigurationen, öppnar du en [supportförfrågan](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Välj **Loggintegrering** som tjänsten som du begär support.

## <a name="can-i-use-azure-log-integration-to-integrate-network-watcher-logs-into-my-siem"></a>Kan jag använda Azure Log Integration för att integrera Network Watcher loggar i mitt SIEM?

Azure Network Watcher genererar stora mängder loggningsinformation. Dessa loggar är inte avsedda att skickas till en SIEM. Den enda mål som stöds för Network Watcher loggar är ett lagringskonto. Azure Log Integration stöder inte läsa de här loggarna och gör dem tillgängliga för en SIEM.

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png
