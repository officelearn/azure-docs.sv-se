---
title: Azure Log Integration vanliga frågor och svar | Microsoft Docs
description: Den här artikeln ger svar på frågor om Azure Log Integration.
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TerryLanfear
ms.assetid: d06d1ac5-5c3b-49de-800e-4d54b3064c64
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload8: na
ms.date: 05/28/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 7ed63364a9dc4b96470a23af3a4bff832d42621b
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727613"
---
# <a name="azure-log-integration-faq"></a>Azure Log Integration vanliga frågor och svar

I den här artikeln besvaras vanliga frågor och svar om Azure Log Integration.

>[!IMPORTANT]
> Funktionen Azure logg integrering kommer att föråldras med 06/15/2019. AzLog hämtningar inaktiverades den 27 juni 2018. Information om vad du kan göra när du flyttar framåt finns i [använda Azure Monitor för att integrera med SIEM-verktyg](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Azure Log Integration är en Windows-tjänst för operativ system som du kan använda för att integrera obehandlade loggar från dina Azure-resurser i dina lokala säkerhets informations-och händelse hanterings system (SIEM). Den här integrationen tillhandahåller en enhetlig instrument panel för alla dina till gångar, lokalt eller i molnet. Du kan sedan aggregera, korrelera, analysera och varna för säkerhets händelser som är kopplade till dina program.

Den bästa metoden för att integrera Azure-loggar är att använda din SIEM-leverantörs Azure Monitor-anslutning och följa dessa [anvisningar](/azure/azure-monitor/platform/stream-monitoring-data-event-hubs). Men om din SIEM-leverantör inte tillhandahåller någon koppling till Azure Monitor kan du kanske använda Azure Log Integration som en tillfällig lösning (om din SIEM stöds av Azure Log Integration) tills en sådan koppling är tillgänglig.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="is-the-azure-log-integration-software-free"></a>Är Azure Log Integration Software gratis?

Ja. Det kostar inget att Azure Log Integration program varan.

## <a name="where-is-azure-log-integration-available"></a>Var finns Azure Log Integration?

Den är för närvarande tillgänglig i Azures kommersiella och Azure Government och är inte tillgänglig i Kina eller Tyskland.

## <a name="how-can-i-see-the-storage-accounts-from-which-azure-log-integration-is-pulling-azure-vm-logs"></a>Hur kan jag se de lagrings konton som Azure Log Integration hämtar Azure VM-loggar från?

Kör kommandot **AzLog source List**.

## <a name="how-can-i-tell-which-subscription-the-azure-log-integration-logs-are-from"></a>Hur kan jag se vilken prenumeration Azure Log Integration loggar är från?

I händelse av gransknings loggar som placeras i **AzureResourcemanagerJson** -katalogerna finns prenumerations-ID: t i logg filens namn. Detta gäller även för loggar i mappen **AzureSecurityCenterJson** Exempel:

20170407T070805_2768037.0000000023.**1111e5ee-1111-111b-a11e-1e111e1111dc**.json

Azure Active Directory gransknings loggar innehåller klient-ID som en del av namnet.

Diagnostikloggar som läses från en Event Hub innehåller inte prenumerations-ID som en del av namnet. De innehåller i stället det egna namnet som anges som en del av att skapa Event Hub-källan. 

## <a name="how-can-i-update-the-proxy-configuration"></a>Hur kan jag uppdatera proxykonfigurationen?

Om proxyinställningarna inte tillåter åtkomst till Azure-lagring direkt öppnar du **AZLOG. EXE. CONFIG** -fil i **c:\Program Files\Microsoft Azure log integration**. Uppdatera filen så att den inkluderar **defaultProxy** -avsnittet med din organisations proxyadress. När uppdateringen är färdig stoppar du och startar tjänsten med hjälp av kommandona **net stop AzLog** och **net start AzLog**.

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

## <a name="how-can-i-see-the-subscription-information-in-windows-events"></a>Hur kan jag se prenumerations informationen i Windows-händelser?

Lägg till prenumerations-ID: t i det egna namnet när du lägger till källan:

    Azlog source add <sourcefriendlyname>.<subscription id> <StorageName> <StorageKey>  
XML-händelsen har följande metadata, inklusive prenumerations-ID:

![Händelse-XML](media/azure-log-integration-faq/event-xml.png)

## <a name="error-messages"></a>Felmeddelanden
### <a name="when-i-run-the-command-azlog-createazureid-why-do-i-get-the-following-error"></a>Varför får jag följande fel ```AzLog createazureid```när jag kör kommandot?

Fel:

  *Det gick inte att skapa AAD-program-Tenant 72f988bf-86f1-41AF-91ab-2d7cd011db37-orsak = ' förbud '-Message = ' otillräcklig behörighet för att slutföra åtgärden. '*

Kommandot **azlog createazureid** försöker skapa ett huvud namn för tjänsten i alla Azure AD-klienter för de prenumerationer som Azure-inloggningen har åtkomst till. Om din Azure-inloggning bara är en gäst användare i den Azure AD-klienten, Miss lyckas kommandot med "otillräcklig behörighet att slutföra åtgärden". Be klient organisations administratören att lägga till ditt konto som en användare i klient organisationen.

### <a name="when-i-run-the-command-azlog-authorize-why-do-i-get-the-following-error"></a>Varför får jag följande fel när jag kör kommandot **azlog**?

Fel:

  *Varning när roll tilldelningen skapas – AuthorizationFailed: Janedo\@-Microsoft.com med objekt-ID: t fe9e03e4-4dad-4328-910F-fd24a9660bd2 har inte behörighet att utföra åtgärden Microsoft. Authorization/roleAssignments/Write över omfattning/subscriptions/ 70d95299-d689-4c97-b971-0d8ff0000000'.*

Kommandot **azlog auktorisering** tilldelar läsaren rollen till Azure AD-tjänstens huvud namn (som skapats med **azlog createazureid**) till de angivna prenumerationerna. Om Azure-inloggningen inte är en medadministratör eller en ägare till prenumerationen Miss lyckas fel meddelandet "auktorisering misslyckades". Azure Role-baserade Access Control (RBAC) för medadministratör eller ägare krävs för att slutföra den här åtgärden.

## <a name="where-can-i-find-the-definition-of-the-properties-in-the-audit-log"></a>Var hittar jag definitionen av egenskaperna i gransknings loggen?

Se:

* [Gransknings åtgärder med Azure Resource Manager](/azure/azure-resource-manager/resource-group-audit)
* [Visa en lista med hanterings händelser i en prenumeration i Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931934.aspx)

## <a name="where-can-i-find-details-on-azure-security-center-alerts"></a>Var hittar jag information om Azure Security Center aviseringar?

Se [Hantera och svara på säkerhets aviseringar i Azure Security Center](/azure/security-center/security-center-managing-and-responding-alerts).

## <a name="how-can-i-modify-what-is-collected-with-vm-diagnostics"></a>Hur kan jag ändra vad som samlas in med VM-diagnostik?

Mer information om hur du hämtar, ändrar och anger Azure-diagnostik konfiguration finns i [använda PowerShell för att aktivera Azure-diagnostik på en virtuell dator som kör Windows](/azure/virtual-machines/windows/ps-extensions-diagnostics?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

I följande exempel hämtas Azure-diagnostik-konfigurationen:

    Get-AzVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient
    $publicsettings = (Get-AzVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient).PublicSettings
    $encodedconfig = (ConvertFrom-Json -InputObject $publicsettings).xmlCfg
    $xmlconfig = [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($encodedconfig))
    Write-Host $xmlconfig

    $xmlconfig | Out-File -Encoding utf8 -FilePath "d:\WADConfig.xml"

I följande exempel ändras Azure-diagnostik-konfigurationen. I den här konfigurationen samlas endast händelse-ID 4624 och händelse-ID 4625 in från säkerhets händelse loggen. Microsoft Antimalware för Azure-händelser samlas in från systemets händelse logg. Mer information om hur du använder XPath-uttryck finns i använda [händelser](https://msdn.microsoft.com/library/windows/desktop/dd996910(v=vs.85)).

    <WindowsEventLog scheduledTransferPeriod="PT1M">
        <DataSource name="Security!*[System[(EventID=4624 or EventID=4625)]]" />
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>
    </WindowsEventLog>

I följande exempel anges Azure-diagnostik-konfigurationen:

    $diagnosticsconfig_path = "d:\WADConfig.xml"
    Set-AzVMDiagnosticsExtension -ResourceGroupName AzLog-Integration -VMName AzlogClient -DiagnosticsConfigurationPath $diagnosticsconfig_path -StorageAccountName log3121 -StorageAccountKey <storage key>

När du har gjort ändringarna kontrollerar du lagrings kontot för att se till att rätt händelser samlas in.

Om du har problem under installationen och konfigurationen kan du öppna en [support förfrågan](/azure/azure-supportability/how-to-create-azure-support-request). Välj **logg integrering** som den tjänst som du begär support för.

## <a name="can-i-use-azure-log-integration-to-integrate-network-watcher-logs-into-my-siem"></a>Kan jag använda Azure Log Integration för att integrera Network Watcher loggar i min SIEM?

Azure Network Watcher genererar stora mängder loggnings information. Dessa loggar är inte avsedda att skickas till en SIEM. Det enda mål som stöds för Network Watcher loggar är ett lagrings konto. Azure Log Integration har inte stöd för att läsa dessa loggar och göra dem tillgängliga för en SIEM.


