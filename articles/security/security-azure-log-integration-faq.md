---
title: Azure logganalys Integration vanliga frågor och svar | Microsoft Docs
description: Den här artikeln svar på frågor om Azure Log-integrering.
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
ms.date: 05/25/2018
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 9f270daec40d4b395588c491a7ff88ef6ca45649
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34802450"
---
# <a name="azure-log-integration-faq"></a>Azure logganalys Integration vanliga frågor och svar

Den här artikeln innehåller svar på vanliga frågor och svar (FAQ) om Azure Log-integrering.

Azure Log-integrering är en tjänst för Windows operativsystem som du kan använda för att integrera loggarna från Azure-resurser i din lokala säkerhet information och händelse (SIEM) hanteringssystem. Den här integreringen ger en enhetlig instrumentpanel för alla dina tillgångar, lokalt eller i molnet. Du kan sedan sammanställa, korrelera, analysera och varna för säkerhetshändelser som är kopplade till dina program.

Den bästa metoden för att integrera Azure loggar är med hjälp av leverantören SIEM Azure-Monitor koppling och följande [instruktioner](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). Men om din SIEM-leverantör inte ger en koppling till Azure-Monitor, du kan använda Azure Log-integrering som en tillfällig lösning (om din SIEM stöds av Azure Log-integrering) tills en sådan anslutning är tillgänglig.

## <a name="is-the-azure-log-integration-software-free"></a>Är programmet Azure Log-integrering gratis?

Ja. Det är gratis för programmet Azure Log-integrering.

## <a name="where-is-azure-log-integration-available"></a>Var finns Azure Log-integrering?

Det finns för närvarande i kommersiella Azure och Azure Government och är inte tillgänglig i Kina eller Tyskland.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs"></a>Hur kan jag se storage-konton som Azure Log-integrering dra Azure VM loggar?

Kör kommandot **AzLog källistan**.

## <a name="how-can-i-tell-which-subscription-the-azure-log-integration-logs-are-from"></a>Hur vet vilken prenumeration som Azure Log-integrering loggarna är från?

När det gäller granskningsloggar som placeras i den **AzureResourcemanagerJson** kataloger, prenumerations-ID är i loggfilens namn. Detta gäller även för loggar i den **AzureSecurityCenterJson** mapp. Exempel:

20170407T070805_2768037.0000000023. **1111e5ee-1111-111b-a11e-1e111e1111dc**JSON

Azure Active Directory-granskningsloggar innehåller klient-ID som en del av namnet.

Diagnostikloggar som läses från en händelsehubb innehåller inte prenumerations-ID som en del av namnet. I stället inkluderar de det egna namnet som angetts som en del av skapandet av hubb händelsekälla. 

## <a name="how-can-i-update-the-proxy-configuration"></a>Hur kan jag uppdatera proxykonfigurationen?

Om proxyserverns inställningar inte tillåter åtkomst till Azure lagring direkt, öppna den **AZLOG. EXE. CONFIG** filen i **c:\Program Files\Microsoft Azure Log integrering**. Uppdatera filen för att inkludera den **defaultProxy** avsnitt med proxyadress i din organisation. När uppdateringen är klar, stoppa och starta tjänsten med hjälp av kommandona **net stop AzLog** och **net start AzLog**.

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

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Hur hittar jag information om prenumerationen i Windows-händelser?

Lägg till prenumerations-ID till ett eget namn när du lägger till källan:

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  
Händelsen XML har följande metadata, inklusive prenumerations-ID:

![Händelsen XML][1]

## <a name="error-messages"></a>Felmeddelanden
### <a name="when-i-run-the-command-azlog-createazureid-why-do-i-get-the-following-error"></a>När jag kör kommandot ```AzLog createazureid```, varför visas följande fel?

Fel:

  *Det gick inte att skapa program i AAD - klient 72f988bf-86f1-41af-91ab-2d7cd011db37-orsak = förbjuden - meddelandet = 'Har inte tillräcklig behörighet för att slutföra åtgärden ”.*

Den **azlog createazureid** kommando görs ett försök att skapa ett huvudnamn för tjänsten i alla Azure AD-klienter för prenumerationer som Azure inloggningen har åtkomst till. Om din Azure-inloggningen är endast gästanvändaren i den Azure AD-klienten, misslyckas kommer kommandot med ”inte tillräcklig behörighet för att slutföra åtgärden”. Be klientadministratör att lägga till ditt konto som en användare i klienten.

### <a name="when-i-run-the-command-azlog-authorize-why-do-i-get-the-following-error"></a>När jag kör kommandot **azlog auktorisera**, varför visas följande fel?

Fel:

  *Varning om att skapa rolltilldelning - AuthorizationFailed: klienten janedo@microsoft.com' med objekt id 'fe9e03e4-4dad-4328-910f-fd24a9660bd2' har inte behörighet att utföra åtgärden 'Microsoft.Authorization/roleAssignments/write' över område ' / prenumerationer / 70d 95299 d689 4c 97-b971-0d8ff0000000'.*

Den **azlog auktorisera** kommandot tilldelar rollen läsare till Azure AD-tjänstens huvudnamn (skapats med **azlog createazureid**) till de angivna prenumerationerna. Om Azure-inloggningen inte är en delad administratör eller en ägare till prenumerationen, misslyckas med felmeddelandet ”auktorisering misslyckades”. Azure rollbaserad åtkomstkontroll (RBAC) för en medadministratör eller ägare krävs för att slutföra åtgärden.

## <a name="where-can-i-find-the-definition-of-the-properties-in-the-audit-log"></a>Var hittar jag definitionen av egenskaperna i granskningsloggen

Se:

* [Granskningsåtgärder med Azure Resource Manager](../azure-resource-manager/resource-group-audit.md)
* [Management-händelser i en prenumeration i Azure-Monitor REST-API](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Var hittar jag information om Azure Security Center-aviseringar

Se [hantera och åtgärda säkerhetsaviseringar i Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md).

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>Hur kan jag ändra vad samlas in med diagnostik för Virtuella datorer?

Information om hur du hämtar, ändra och ange Azure-diagnostik-konfigurationen finns [Använd PowerShell för att aktivera Azure-diagnostik i en virtuell dator som kör Windows](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

I följande exempel hämtar Azure-diagnostik-konfiguration:

    -AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

I följande exempel ändrar konfigurationen för Azure-diagnostik. I den här konfigurationen endast händelse-ID 4624 och händelse-ID 4625 samlas in från säkerhetshändelseloggen. Microsoft Antimalware för Azure händelser som samlas in från systemets händelselogg. Mer information om användning av XPath-uttryck finns [förbrukar händelser](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85)).

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

I följande exempel anger Azure-diagnostik-konfiguration:

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

När du har ändrat Kontrollera storage-konto för att säkerställa att rätt händelser har samlats in.

Om du har problem under installationen och konfigurationen, öppnar du en [supportbegäran](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Välj **loggen Integration** som tjänsten som du begär support.

## <a name="can-i-use-azure-log-integration-to-integrate-network-watcher-logs-into-my-siem"></a>Kan jag använda Azure Log-integrering för att integrera Nätverksbevakaren loggar i min SIEM?

Azure Nätverksbevakaren genererar stora mängder loggningsinformation. Dessa loggar är inte avsedda att skickas till en SIEM. Endast stöds mål för Nätverksbevakaren loggar är ett lagringskonto. Azure Log-Integration stöder inte läsning dessa loggar och gör dem tillgängliga för en SIEM.

<!--Image references-->
[1]: ./media/security-azure-log-integration-faq/event-xml.png
