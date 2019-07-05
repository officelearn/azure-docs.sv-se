---
title: Aktivera säkerhetsgranskningar för Azure AD Domain Services | Microsoft Docs
description: Aktivera säkerhetsgranskningar för Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: iainfou
ms.openlocfilehash: 3105296b3c670d3d44789c93878fa1fc6076973b
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566500"
---
# <a name="enable-security-audits-for-azure-ad-domain-services-preview"></a>Aktivera säkerhetsgranskningar för Azure AD Domain Services (förhandsversion)
Azure AD Domain Service säkerhetsgranskningar gör att kunderna kan använda portalen för Azure AD Domain-tjänsten att stream säkerhetsgranskningshändelser till målresurser. Resurser som kan ta emot de här händelserna inkluderar Azure Storage, Azure Log Analytics-arbetsytor och Azure Event Hub. Strax efter att aktivera säkerhetsgranskningshändelser, skickar Azure AD Domain-tjänsten de granskade händelserna för den valda kategorin till resurs. Säkerhets-granskningshändelser kan kunder Arkiv granskade händelser till Azure storage. Kunder kan dessutom strömma händelser till säkerhet information och händelsen hanteringsprogramvara (SIEM) (eller motsvarande) med event hubs eller gör egna analyser och insikter med hjälp av Azure Log Analytics från Azure-portalen. 

> [!IMPORTANT]
> Azure AD Domain Services-säkerhet och granskning finns endast på Azure Resource Manager-baserade instanser för Azure AD Domain Services.
>
>

## <a name="auditing-event-categories"></a>Händelsekategorier
Azure AD Domain Services-säkerhet och granskning överensstämmer med traditionella granskning för Active Directory DS-domänkontrollanter. Återanvända befintliga audit mönster garanterar samma logik som kan användas när du analyserar händelserna. Azure AD Domain Services-säkerhet och granskning innehåller följande händelsekategorier.

| Granska kategorinamn | Beskrivning |
|:---|:---|
| Kontoinloggning|Granskar försök att autentisera kontodata på en domänkontrollant eller på en lokala konton Manager (SAM).</p>Inloggning och utloggning principinställningar och händelser spåra försöker komma åt en viss dator. Inställningar och händelser i den här kategorin kan du fokusera på den databasen som används. Den här kategorin omfattar följande underkategorier:<ul><li>[Granska verifiering av autentiseringsuppgifter](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Granska Kerberos-autentiseringstjänst](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Granska Kerberos-Tjänstbiljettåtgärder](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Granska andra händelser för inloggning](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Kontohantering|Granskar förändringar av användar- och datorkonton och grupper. Den här kategorin omfattar följande underkategorier:<ul><li>[Hantering av granskning programgrupp](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Granska hantering av datorkonto](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Hantering av granskning distributionsgrupp](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Granska andra kontohantering](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Hantering av säkerhetsgrupper för granskning](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Granska hantering av användarkonto](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| Detaljerad spårning|Granskningar aktiviteter för enskilda program och användare på datorn och att förstå hur en dator används. Den här kategorin omfattar följande underkategorier:<ul><li>[Granska DPAPI-aktivitet](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[PNP aktivitetsrapporter](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[Granska Processgenerering](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[Granska processavslut](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[Granska RPC-händelser](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Directory Services Access|Granskningar försöker komma åt och ändra objekt i Active Directory Domain Services (AD DS). Dessa granska händelser som loggas endast på domänkontrollanter. Den här kategorin omfattar följande underkategorier:<ul><li>[Granska detaljerad replikering av katalogtjänst](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Granska katalogtjänståtkomst](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Granska ändringar av katalogtjänst](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Granska replikering av katalogtjänst](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Logon-Logoff|Granskar försöker logga in på en dator interaktivt eller över ett nätverk. Dessa händelser är användbara för att spåra användaraktivitet och identifiera potentiella attacker på nätverksresurser. Den här kategorin omfattar följande underkategorier:<ul><li>[Granska kontoutelåsning](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Granska anspråk för användare/enhet](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[Granska utökat IPsec-läge](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Granska gruppmedlemskap](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[Granska IPsec-huvudläge](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[Granska IPsec-snabbläge](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Granska utloggning](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Granska inloggning](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logon)</li><li>[Granska Nätverksprincipserver](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Granska andra händelser för inloggning](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Granska Specialinloggning](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Åtkomst till objekt| Granskar försöker få åtkomst till specifika objekt eller typer av objekt på ett nätverk eller dator. Den här kategorin omfattar följande underkategorier:<ul><li>[Granska genererat program](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Granska Certifikattjänster](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Granska detaljerad filresurs](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Granska filresurs](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-share)</li><li>[Granska filsystem](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Granska anslutning för filterplattform](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Granska filtrering paket för filterplattform](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Granska hantering av manipulering](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Granska Kernel-objekt](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Granska andra objektåtkomsthändelser](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Granska register](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-registry)</li><li>[Granska flyttbara lagringsmedia](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[Granska SAM](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sam)</li><li>[Granska mellanlagring för princip för Central åtkomst](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|Ändring av|Granskar förändringar av viktiga säkerhetsprinciper i ett lokalt system eller nätverk. Principer som vanligtvis upprättas av administratörer för att säkra nätverksresurser. Övervaka ändringar eller försök att ändra de här principerna kan vara en viktig aspekt av säkerhetshantering för ett nätverk. Den här kategorin omfattar följande underkategorier:<ul><li>[Granska ändring av granskningsprincip](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Granska ändring av autentiseringsprincip](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Granska ändring av auktoriseringsprincip](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Granska ändring av Filtreringsplattformsprincip](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[Granska ändring av MPSSVC regel på databasnivå princip](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Granska ändring av andra](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Privilegierad användning| Granskar användning av vissa behörigheter för ett eller flera system. Den här kategorin omfattar följande underkategorier:<ul><li>[Granska användning av icke-känsliga privilegier](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Granska användning av känsliga privilegier](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Granska andra privilegierad användning](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|System| Granskningar på systemnivå ändringar till en dator som inte ingår i kategorier och som påverkar potentiella säkerheten. Den här kategorin omfattar följande underkategorier:<ul><li>[Granska IPsec-drivrutin](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Granska andra systemhändelser](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Ändring av säkerhetsstatus för granskning](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Granska säkerhetssystem](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Granska Systemintegritet](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>Händelse-ID per kategori
 Azure AD Domain Services-säkerhet och granskning registrerar följande händelse-ID när åtgärden utlöser en granskningsbar händelse.

| Händelsen kategorinamn | Händelse-ID |
|:---|:---|
|Konto inloggningssäkerhet|4767, 4774, 4775, 4776, 4777|
|Hantering av kontosäkerhet|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|Detaljerad spårning säkerhet|Ingen|
|DS åtkomstsäkerhet|5136, 5137, 5138, 5139, 5141|
|In-utloggning säkerhet|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Objektsäkerhet för åtkomst|Ingen|
|Säkerhet för ändring av princip|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|Säkerhet för användning av privilegier|4985|
|Systemsäkerhet|4612, 4621|

## <a name="enable-security-audit-events"></a>Aktivera säkerhets-granskningshändelser
Följande riktlinjer hjälper dig att har prenumerera på Azure AD Domain Services säkerhetsgranskningshändelser.

> [!IMPORTANT]
> Azure AD Domain Services-säkerhetsgranskningar är inte retroaktivt. Det går inte att hämta händelser från tidigare eller att spela upp händelser från tidigare. Tjänsten kan bara skicka händelser som inträffar när den är aktiverad.
>

### <a name="choose-the-target-resource"></a>Välj målresurs
Du kan använda valfri kombination av Azure Storage, Azure Event Hubs eller Azure Log Analytics-arbetsytor som en målresurs för din säkerhetsgranskningar. Titta på följande tabell för den bästa resursen för ditt användningsområde.

> [!IMPORTANT]
> Du behöver skapa målresursen innan du aktiverar Azure AD Domain Services säkerhetsgranskningar.
>

| Målresurs | Scenario |
|:---|:---|
|Azure Storage|Överväg att använda det här målet när din primära behovet att lagra säkerhetsgranskningshändelser för arkivering. Andra mål kan användas för arkivering; dessa mål ger dock möjligheter utöver primära behöver för att arkivera. Så här skapar du ett Azure Storage-konto [skapa ett lagringskonto.](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal#create-a-storage-account-1)|
|Azure Event Hubs|Överväg att använda det här målet när din primära behovet att dela säkerhetsgranskningshändelser med ytterligare programvara, till exempel data programvara eller information & event management (SIEM) säkerhetsprogram. Så här skapar du en event hub [Snabbstart: Skapa en event hub med Azure-portalen.](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)|
|Azure Log Analytics Workspace|Överväg att använda det här målet när din primära behovet att analysera och granska säker granskningar från Azure portal direkt.  Så här skapar du en Log Analytics-arbetsyta [skapa en Log Analytics-arbetsyta i Azure-portalen.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)|

## <a name="using-the-azure-portal-to-enable-security-audit-events"></a>Använda Azure-portalen för att aktivera säkerhets-granskningshändelser 
1. Logga in på Azure Portal på https://portal.azure.com.  Klicka på alla tjänster i Azure-portalen. I listan över resurser skriver **domän**. När du börjar skriva filtreras listan baserat på det du skriver. Klicka på **Azure AD Domain Services**.
2. Klicka på den Azure AD Domain Services-instansen i listan.
3. Klicka på **diagnostikinställningar (förhandsversion)** från listan över åtgärder till vänster.</p>
![åtgärd för diagnostikinställning](./media/security-audit-events/diagnostic-settings-action.png)
4. Skriv namnet på diagnostikkonfiguration (**aadds granskning** som exempel).</p>
![diagnostikinställningar för sidan](./media/security-audit-events/diagnostic-settings-page.png)
5. Markerar du motsvarande kryssruta bredvid målresurser som du ska använda med säkerhetsgranskningshändelser.
    > [!NOTE]
    > Du kan inte skapa målresurser från den här sidan.
    >
    
    **Azure storage:**</p>
    Välj **arkivet till ett lagringskonto**. Klicka på **Konfigurera**. Välj den **prenumeration** och **lagringskonto** du vill använda för att arkivera händelser för säkerhetsgranskning. Klicka på **OK**.</p>
    
    ![inställningarna för diagnostik](./media/security-audit-events/diag-settings-storage.png)
    
    **Azure event hubs:**</p>
    Välj **Stream till en händelsehubb**. Klicka på **Konfigurera**. I den **sidan Välj händelsehubb**väljer den **prenumeration** används för att skapa händelsehubben. Välj sedan den **händelsehubbnamnområde**, **händelsehubbnamn**, och **Principnamn för event hub**. Klicka på **OK**.</p>
    ![diagnostiska event hub-inställningar](./media/security-audit-events/diag-settings-eventhub.png)
    
    **Azure Log Analytics-arbetsytor:**</p>
    Välj **skicka till Log Analytics**. Välj den **prenumeration** och **Log Analytics-arbetsytan** används för att lagra säkerhetsgranskningshändelser.</p>
    ![inställningar för diagnostik arbetsyta](./media/security-audit-events/diag-settings-log-analytics.png)

6. Välj loggkategorier som du vill inkludera för viss målresursen. Om du använder storage-konton, kan du konfigurera principer för kvarhållning.

    > [!NOTE]
    > Du kan välja olika loggkategorier för varje resurs inom en enda konfiguration. På så sätt kan du välja vilket loggar kategorier som du vill behålla för Log Analytics och som loggar kategorier du vill arkivera.
    >

7. Klicka på **spara** att genomföra ändringarna. Resurser för target får Azure AD Domain Services säkerhetsgranskningshändelser strax efter att du sparar din konfiguration.

## <a name="using-azure-powershell-to-enable-security-audit-events"></a>Använda Azure PowerShell för att aktivera säkerhets-granskningshändelser
 
### <a name="prerequisites"></a>Förutsättningar

Följ instruktionerna i artikeln om du vill [installera Azure PowerShell-modulen och ansluta till din Azure-prenumeration](https://docs.microsoft.com/powershell/azure/install-az-ps?toc=%2fazure%2factive-directory-domain-services%2ftoc.json).

### <a name="enable-security-audits"></a>Aktivera säkerhetsgranskningar

1. Autentisera till Azure Resource Manager för rätt klient- och prenumeration med hjälp av den **Connect AzAccount** Azure PowerShell-cmdleten.
2. Skapa målresursen för att skydda granskningshändelser.</p>
    **Azure storage:**</p>
    Följ [skapa ett lagringskonto](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-powershell) att skapa ditt lagringskonto.</p>
    **Azure event hubs:**</p>
    Följ [Snabbstart: Skapa en händelsehubb med hjälp av Azure PowerShell](https://docs.microsoft.com/azure/event-hubs/event-hubs-quickstart-powershell) att skapa din event hub. Du kan också behöva använda den [New-AzEventHubAuthorizationRule](https://docs.microsoft.com/powershell/module/az.eventhub/new-azeventhubauthorizationrule?view=azps-2.3.2) Azure PowerShell-cmdlet för att skapa en auktoriseringsregel för att tillåta Active Directory AD Domain Services-behörigheter till event hub **namnområde**. Auktoriseringsregeln måste innehålla den **hantera**, **lyssna**, och **skicka** rättigheter.
    > [!IMPORTANT]
    > Se till att du anger auktoriseringsregeln för händelsehubbens namnområde och inte event hub.
       
    </p>
    
    **Azure Log Analytics-arbetsytor:**</p>
    Följ [skapar en Log Analytics-arbetsyta med Azure PowerShell](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace-posh) att skapa din arbetsyta.
3. Hämta resurs-ID för din Azure AD Domain Services-instans. Skriv följande kommando i en öppen, autentiserad Windows PowerShell-konsolen. Använd den **$aadds. ResourceId** variabeln som en parameter för Azure AD Domain Services-resurs-ID för framtida cmdletar.
    ```powershell
    $aadds = Get-AzResource -name aaddsDomainName
    ``` 
4. Använd den **Set-AzDiagnosticSetting** cmdlet för att konfigurera Azure Diagnostics-inställningar för att använda målresursen för Azure AD Domain Services säkerhetsgranskningshändelser. I exemplen nedan, variabeln $aadds. Resurs-ID representerar resurs-ID för din Azure AD Domain Services-instans (se steg3).</p>
    **Azure storage:**
    ```powershell
    Set-AzDiagnosticSetting `
    -ResourceId $aadds.ResourceId` 
    -StorageAccountId storageAccountId `
    -Enabled $true
    ```
    Ersätt *storageAccountId* med ditt storage-konto-ID.</p>
    
    **Azure event hubs:**
    ```powershell
    Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId ` 
    -EventHubName eventHubName `
    -EventHubAuthorizationRuleId eventHubRuleId `
    -Enabled $true
    ```
    Ersätt *eventHubName* med namnet på din event hub. Ersätt *eventHubRuleId* med din auktorisering regel-ID som du skapade tidigare.</p>
    
    **Azure Log Analytics-arbetsytor:**
    ```powershell
    Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId ` 
    -WorkspaceID workspaceId `
    -Enabled $true
    ```
    Ersätt *workspaceId* med ID för Log Analytics-arbetsytan som du skapade tidigare. 

## <a name="view-security-audit-events-using-azure-monitor"></a>Visa säkerhetsgranskningshändelser med Azure Monitor
Log Analytics-arbetsytor kan du visa och analysera säkerhetsgranskningshändelser med hjälp av Azure Monitor och Kusto-frågespråket. Frågespråket är avsedd för skrivskyddade som har power analysfunktioner med ett enkelt att läsa-syntax.
Här är några resurser som hjälper dig att komma igång med Kusto-frågespråk.
* [Dokumentation om Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
* [Kom igång med Log Analytics i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)
* [Kom igång med loggfrågor i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)
* [Skapa och dela instrumentpaneler för Log Analytics-data](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-logs-dashboards)

## <a name="sample-queries"></a>Exempelfrågor

### <a name="sample-query-1"></a>Exempelfråga 1
Alla konto kontoutelåsning händelser under de senaste sju dagarna.
```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>Exempelfråga 2
Alla konto kontoutelåsning händelser (4740) mellan den 26 juni 2019 kl 9. och den 1 juli 2019 midnatt sorterade stigande efter datum och tid.
```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2019-06-26 09:00) and TimeGenerated <= datetime(2019-07-01) 
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>Exempelfråga 3
Kontot log på händelser sju dagar sedan (från och med nu) för kontot med namnet användare.
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>Exempelfråga 4
Kontoinloggningar sju dagar sedan från nu för kontot med namnet användaren försökte logga in med ett felaktigt lösenord (0xC0000006a).
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>Exempelfråga 5
Kontoinloggningar sju dagar sedan från nu för kontot med namnet användare som försökte logga in när kontot har låsts ute (0xC0000234).
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>Exempelfråga 6
Antalet kontoinloggningar sju dagar sedan från nu för alla loggar du in försök som uppstod för alla låsts ute användare.
```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="related-content"></a>Relaterat innehåll
* [Översikt över](https://docs.microsoft.com/azure/kusto/query/) av Kusto-frågespråket.
* [Kusto-självstudier](https://docs.microsoft.com/azure/kusto/query/tutorial) du bekantar dig med grunderna för frågan.
* [Exempel på frågor](https://docs.microsoft.com/azure/kusto/query/samples) som hjälper dig att du lära dig nya sätt att visa dina data.
* Kusto [bästa praxis](https://docs.microsoft.com/azure/kusto/query/best-practices) – optimera frågor för att lyckas.














 
