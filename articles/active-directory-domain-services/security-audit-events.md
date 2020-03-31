---
title: Aktivera säkerhetsgranskningar för Azure AD Domain Services | Microsoft-dokument
description: Lär dig hur du aktiverar säkerhetsgranskningar för att centralisera loggning av händelser för analys och aviseringar i Azure AD Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 662362c3-1a5e-4e94-ae09-8e4254443697
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: b2138818a9092999dd54b14664f7146f087c4fed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78328654"
---
# <a name="enable-security-audits-for-azure-active-directory-domain-services"></a>Aktivera säkerhetsgranskningar för Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) säkerhetsgranskningar gör att Azure kan strömma säkerhetshändelser till riktade resurser. Dessa resurser inkluderar Azure Storage, Azure Log Analytics-arbetsytor eller Azure Event Hub. När du har aktiverat säkerhetsgranskningshändelser skickar Azure AD DS alla granskade händelser för den valda kategorin till den avsedda resursen.

Du kan arkivera händelser i Azure-lagring och strömma händelser till siem-programvara (eller motsvarande) säkerhetsinformation och händelsehantering (eller motsvarande) med Hjälp av Azure Event Hubs, eller göra din egen analys och använda Azure Log Analytics-arbetsytor från Azure-portalen.

> [!IMPORTANT]
> Azure AD DS-säkerhetsgranskningar är endast tillgängliga för Azure Resource Manager-baserade instanser. Information om hur du migrerar finns i [Migrera Azure AD DS från den klassiska virtuella nätverksmodellen till Resource Manager][migrate-azure-adds].

## <a name="audit-event-categories"></a>Granska händelsekategorier

Azure AD DS-säkerhetsgranskningar anpassas till traditionell granskning för traditionella AD DS-domänkontrollanter. I hybridmiljöer kan du återanvända befintliga granskningsmönster så att samma logik kan användas när du analyserar händelserna. Beroende på vilket scenario du behöver felsöka eller analysera måste de olika granskningshändelsekategorierna vara inriktade.

Följande kategorier för granskningshändelsen är tillgängliga:

| Namn på revisionskategori | Beskrivning |
|:---|:---|
| Inloggning för konto|Granskningar försöker autentisera kontodata på en domänkontrollant eller på en lokal Säkerhetskontohanterare (SAM).</p>Inställningar för inloggning och utloggningsprincip och händelser spårar försök att komma åt en viss dator. Inställningar och händelser i den här kategorin fokuserar på den kontodatabas som används. Denna kategori innehåller följande underkategorier:<ul><li>[Granska verifiering av autentiseringsuppgifter](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-credential-validation)</li><li>[Granska Kerberos-autentiseringstjänst](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-authentication-service)</li><li>[Granska Kerberos-tjänstbiljettåtgärder](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kerberos-service-ticket-operations)</li><li>[Granska andra inloggnings-/utloggningshändelser](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li></ul>|
| Kontohantering|Granskar ändringar i användar- och datorkonton och grupper. Denna kategori innehåller följande underkategorier:<ul><li>[Granska hantering av programgrupp](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-group-management)</li><li>[Granska hantering av datorkonto](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-computer-account-management)</li><li>[Granska hantering av distributionsgrupp](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-distribution-group-management)</li><li>[Granska annan kontohantering](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-account-management-events)</li><li>[Granska hantering av säkerhetsgrupp](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-group-management)</li><li>[Granska hantering av användarkonto](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-account-management)</li></ul>|
| Detaljerad spårning|Granskar enskilda programs och användares aktiviteter på den datorn och för att förstå hur en dator används. Denna kategori innehåller följande underkategorier:<ul><li>[Granska DPAPI-aktivitet](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-dpapi-activity)</li><li>[Granska PNP-aktivitet](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-pnp-activity)</li><li>[Granska processgenerering](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-creation)</li><li>[Granska processavslut](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-process-termination)</li><li>[Granska RPC-händelser](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-rpc-events)</li></ul>|
| Åtkomst till katalogtjänster|Granskningar försöker komma åt och ändra objekt i Active Directory Domain Services (AD DS). Dessa granskningshändelser loggas endast på domänkontrollanter. Denna kategori innehåller följande underkategorier:<ul><li>[Granska detaljerad replikering av katalogtjänst](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-directory-service-replication)</li><li>[Granska katalogtjänståtkomst](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-access)</li><li>[Granska ändringar av katalogtjänst](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-changes)</li><li>[Granska replikering av katalogtjänst](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-directory-service-replication)</li></ul>|
| Inloggning-utloggning|Granskningar försöker logga in på en dator interaktivt eller via ett nätverk. Dessa händelser är användbara för att spåra användaraktivitet och identifiera potentiella attacker på nätverksresurser. Denna kategori innehåller följande underkategorier:<ul><li>[Granska kontoutelåsning](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-account-lockout)</li><li>[Granska anspråk på användare/enhet](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-user-device-claims)</li><li>[Granska utökat IPsec-läge](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-extended-mode)</li><li>[Medlemskap i revisionsgruppen](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-group-membership)</li><li>[Granska IPSec-huvudläge](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-main-mode)</li><li>[Granska IPSec-snabbläge](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-quick-mode)</li><li>[Granska utloggning](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logoff)</li><li>[Granska inloggning](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-logon)</li><li>[Granska nätverksprincipserver](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-network-policy-server)</li><li>[Granska andra inloggnings-/utloggningshändelser](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-logonlogoff-events)</li><li>[Granska specialinloggning](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-special-logon)</li></ul>|
|Åtkomst till objekt| Granskningar försöker komma åt specifika objekt eller typer av objekt i ett nätverk eller en dator. Denna kategori innehåller följande underkategorier:<ul><li>[Granska genererat program](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-application-generated)</li><li>[Granska certifikattjänster](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-certification-services)</li><li>[Granska detaljerad filresurs](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-detailed-file-share)</li><li>[Granska filresurs](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-share)</li><li>[Granskningsfilsystem](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-file-system)</li><li>[Granska anslutning för filterplattform](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection)</li><li>[Granska ignorerat paket för filterplattform](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-packet-drop)</li><li>[Manipulering av granskningshandtag](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-handle-manipulation)</li><li>[Kernel-objekt för granskning](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-kernel-object)</li><li>[Granska andra objektåtkomsthändelser](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-object-access-events)</li><li>[Granskningsregistret](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-registry)</li><li>[Re granskning flyttbar lagringsmedia](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-removable-storage)</li><li>[Granska SAM](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sam)</li><li>[Förproduktion av centrala åtkomstprinciper för granskning](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-central-access-policy-staging)</li></ul>|
|Ändring av policy|Granskar ändringar i viktiga säkerhetsprinciper i ett lokalt system eller nätverk. Principer upprättas vanligtvis av administratörer för att skydda nätverksresurser. Övervakning av ändringar eller försök att ändra dessa principer kan vara en viktig aspekt av säkerhetshantering för ett nätverk. Denna kategori innehåller följande underkategorier:<ul><li>[Granska ändring av granskningsprincip](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-audit-policy-change)</li><li>[Granska ändring av autentiseringsprincip](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authentication-policy-change)</li><li>[Granska ändring av auktoriseringsprincip](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-authorization-policy-change)</li><li>[Granska ändring av filtreringsplattformsprincip](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-policy-change)</li><li>[Granska ändring av MPSSVC-regelnivåprincip](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-mpssvc-rule-level-policy-change)</li><li>[Granska annan principändring](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-policy-change-events)</li></ul>|
|Användning av privilegier| Granskar användningen av vissa behörigheter på ett eller flera system. Denna kategori innehåller följande underkategorier:<ul><li>[Granska användning av icke-känsliga privilegier](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-non-sensitive-privilege-use)</li><li>[Granska användning av känsliga privilegier](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)</li><li>[Granska andra händelser som rör privilegierad användning](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-privilege-use-events)</li></ul>|
|System| Granskar ändringar på systemnivå till en dator som inte ingår i andra kategorier och som har potentiella säkerhetskonsekvenser. Denna kategori innehåller följande underkategorier:<ul><li>[Granska IPsec-drivrutin](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-ipsec-driver)</li><li>[Granska andra systemhändelser](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-other-system-events)</li><li>[Granska ändring av säkerhetsstatus](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-state-change)</li><li>[Granska utökning av säkerhetssystem](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-security-system-extension)</li><li>[Granska systemintegritet](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-system-integrity)</li></ul>|

## <a name="event-ids-per-category"></a>Händelse-ID per kategori

 Azure AD DS-säkerhetsgranskningar registrerar följande händelse-ID:n när den specifika åtgärden utlöser en granskningsbar händelse:

| Namn på händelsekategori | Händelse-ID:er |
|:---|:---|
|Säkerhet för kontoinloggning|4767, 4774, 4775, 4776, 4777|
|Säkerhet för kontohantering|4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4730, 4731, 4732, 4733, 4734, 4735, 4737, 4738, 4740, 4741, 4742, 4743, 4754, 4755, 4756, 4757, 4758, 4764, 4765, 4766, 4780, 4781, 4782, 4793, 4798, 4799, 5376, 5377|
|Säkerhet för detaljspårning|Inget|
|DS-åtkomstsäkerhet|5136, 5137, 5138, 5139, 5141|
|Inloggning-Utloggning säkerhet|4624, 4625, 4634, 4647, 4648, 4672, 4675, 4964|
|Säkerhet för objektåtkomst|Inget|
|Säkerhet för principändring|4670, 4703, 4704, 4705, 4706, 4707, 4713, 4715, 4716, 4717, 4718, 4719, 4739, 4864, 4865, 4866, 4867, 4904, 4906, 4911, 4912|
|Privilegium Använd säkerhet|4985|
|Systemsäkerhet|4612, 4621|

## <a name="security-audit-destinations"></a>Destinationer för säkerhetsgranskning

Du kan använda Azure Storage, Azure Event Hubs eller Azure Log Analytics-arbetsytor som en målresurs för Azure AD DS-säkerhetsgranskningar. Dessa destinationer kan kombineras. Du kan till exempel använda Azure Storage för arkivering av säkerhetsgranskningshändelser, men en Azure Log Analytics-arbetsyta för att analysera och rapportera om informationen på kort sikt.

I följande tabell beskrivs scenarier för varje målresurstyp.

> [!IMPORTANT]
> Du måste skapa målresursen innan du aktiverar Azure AD DS-säkerhetsgranskningar. Du kan skapa dessa resurser med Azure-portalen, Azure PowerShell eller Azure CLI.

| Målresurs | Scenario |
|:---|:---|
|Azure Storage| Det här målet bör användas när ditt primära behov är att lagra säkerhetsgranskningshändelser för arkivering. Andra mål kan användas för arkivering, men dessa mål ger funktioner utöver det primära behovet av arkivering. <br /><br />Innan du aktiverar Azure AD DS-säkerhetsgranskningshändelser skapar du först [ett Azure Storage-konto](../storage/common/storage-account-create.md).|
|Azure Event Hubs| Det här målet bör användas när ditt primära behov är att dela säkerhetsgranskningshändelser med ytterligare programvara som dataanalysprogramvara eller säkerhetsinformation & IMEM-programvara (Event Management).<br /><br />Innan du aktiverar Azure AD DS-säkerhetsgranskningshändelser [skapar du en händelsehubb med Azure-portalen](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)|
|Arbetsyta för Azure Log Analytics| Det här målet bör användas när ditt primära behov är att analysera och granska säkra granskningar från Azure-portalen direkt.<br /><br />Innan du aktiverar Azure AD DS-säkerhetsgranskningshändelser [skapar du en log analytics-arbetsyta i Azure-portalen.](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)|

## <a name="enable-security-audit-events-using-the-azure-portal"></a>Aktivera säkerhetsgranskningshändelser med Azure-portalen

Så här aktiverar du Azure AD DS-säkerhetsgranskningshändelser med Azure-portalen.

> [!IMPORTANT]
> Azure AD DS-säkerhetsgranskningar är inte retroaktiva. Du kan inte hämta eller spela upp händelser från det förflutna. Azure AD DS kan bara skicka händelser som inträffar efter att säkerhetsgranskningar har aktiverats.

1. Logga in på Azure Portal på https://portal.azure.com.
1. Sök efter och välj Azure AD Domain Services högst upp i **Azure-portalen**. Välj din hanterade domän, till exempel *aaddscontoso.com*.
1. I Azure AD DS-fönstret väljer du **Diagnostikinställningar** till vänster.
1. Ingen diagnostik är konfigurerad som standard. Kom igång genom att välja **Lägg till diagnostikinställning**.

    ![Lägga till en diagnostikinställning för Azure AD Domain Services](./media/security-audit-events/add-diagnostic-settings.png)

1. Ange ett namn för diagnostikkonfigurationen, till exempel *aadds-auditing*.

    Markera kryssrutan för önskat säkerhetsgranskningsmål. Du kan välja mellan ett Azure Storage-konto, en Azure-händelsehubb eller en Log Analytics-arbetsyta. Dessa målresurser måste redan finnas i din Azure-prenumeration. Du kan inte skapa målresurserna i den här guiden.

    ![Aktivera den mål och typ av granskningshändelser som krävs för att samla in](./media/security-audit-events/diagnostic-settings-page.png)

    * **Azure Storage**
        * Välj **Arkiv till ett lagringskonto**och välj sedan **Konfigurera**.
        * Välj det **prenumerationskonto** och **det lagringskonto** som du vill använda för att arkivera säkerhetsgranskningshändelser.
        * När du är klar väljer du **OK**.
    * **Azure-händelsehubbar**
        * Välj **Strömma till en händelsehubb**och välj sedan **Konfigurera**.
        * Välj **namnområdet Prenumeration** och **händelsehubben**. Om det behövs väljer du också ett **namn på händelsenav** och sedan **händelsenavprincipnamn**.
        * När du är klar väljer du **OK**.
    * **Azure Log analytiska arbetsytor**
        * Välj **Skicka till Logganalys**och välj sedan den **prenumerations-** och **logganalysarbetsyta** som du vill använda för att lagra säkerhetsgranskningshändelser.

1. Välj de loggkategorier som du vill ska inkludera för den aktuella målresursen. Om du skickar granskningshändelserna till ett Azure Storage-konto kan du också konfigurera en bevarandeprincip som definierar antalet dagar för att lagra data. En standardinställning *på 0* behåller alla data och roterar inte händelser efter en viss tid.

    Du kan välja olika loggkategorier för varje riktad resurs i en enda konfiguration. Med den här möjligheten kan du välja vilka loggkategorier du vill behålla för Logganalys och vilka loggar kategorier du vill arkivera, till exempel.

1. När du är klar väljer du **Spara** för att genomföra ändringarna. Målresurserna börjar ta emot Azure AD DS-säkerhetsgranskningshändelser strax efter att konfigurationen har sparats.

## <a name="enable-security-audit-events-using-azure-powershell"></a>Aktivera säkerhetsgranskningshändelser med Azure PowerShell

Så här aktiverar du Azure AD DS-säkerhetsgranskningshändelser med Azure PowerShell: Om det behövs installerar du först [Azure PowerShell-modulen och ansluter till din Azure-prenumeration](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Azure AD DS-säkerhetsgranskningar är inte retroaktiva. Du kan inte hämta eller spela upp händelser från det förflutna. Azure AD DS kan bara skicka händelser som inträffar efter att säkerhetsgranskningar har aktiverats.

1. Autentisera till din [Azure-prenumeration med cmdleten Connect-AzAccount.](/powershell/module/Az.Accounts/Connect-AzAccount) Ange dina kontouppgifter när du uppmanas att göra det.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Skapa målresursen för säkerhetsgranskningshändelserna.

    * **Azure storage** - [Skapa ett lagringskonto med Azure PowerShell](../storage/common/storage-account-create.md?tabs=azure-powershell)
    * **Azure-händelsehubbar** - [Skapa en händelsehubb med Azure PowerShell](../event-hubs/event-hubs-quickstart-powershell.md). Du kan också behöva använda [cmdleten New-AzEventHubAuthorizationRule](/powershell/module/az.eventhub/new-azeventhubauthorizationrule) för att skapa en auktoriseringsregel som ger Azure AD DS-behörigheter till *namnområdet*för händelsehubben . Auktoriseringsregeln måste innehålla behörigheterna **Hantera,** **Lyssna**och **Skicka.**

        > [!IMPORTANT]
        > Se till att du anger auktoriseringsregeln för namnområdet för händelsehubben och inte själva händelsehubben.

    * **Azure Log Analytic arbetsytor** - [Skapa en log Analytics-arbetsyta med Azure PowerShell](../azure-monitor/learn/quick-create-workspace-posh.md).

1. Hämta resurs-ID:n för din Azure AD DS-hanterade domän med hjälp av [cmdleten Get-AzResource.](/powershell/module/Az.Resources/Get-AzResource) Skapa en variabel med namnet *$aadds. ResourceId* att hålla värdet:

    ```azurepowershell
    $aadds = Get-AzResource -name aaddsDomainName
    ```

1. Konfigurera Azure Diagnostic-inställningarna med cmdleten [Set-AzDiagnosticSetting](/powershell/module/Az.Monitor/Set-AzDiagnosticSetting) för att använda målresursen för säkerhetsgranskningshändelser för Azure AD Domain Services. I följande exempel *$aadds variabeln. ResourceId* används från föregående steg.

    * **Azure storage** - Ersätt *storageAccountId* med ditt lagringskontonamn:

        ```powershell
        Set-AzDiagnosticSetting `
            -ResourceId $aadds.ResourceId `
            -StorageAccountId storageAccountId `
            -Enabled $true
        ```

    * **Azure-händelsehubbar** - Ersätt *eventHubName* med namnet på din händelsehubb och *eventHubRuleId* med ditt auktoriseringsregel-ID:

        ```powershell
        Set-AzDiagnosticSetting -ResourceId $aadds.ResourceId `
            -EventHubName eventHubName `
            -EventHubAuthorizationRuleId eventHubRuleId `
            -Enabled $true
        ```

    * **Azure Log Analytic workspaces** - Ersätt *workspaceId* med ID för Log Analytics-arbetsytan:

        ```powershell
        Set-AzureRmDiagnosticSetting -ResourceId $aadds.ResourceId `
            -WorkspaceID workspaceId `
            -Enabled $true
        ```

## <a name="query-and-view-security-audit-events-using-azure-monitor"></a>Fråga och visa säkerhetsgranskningshändelser med Azure Monitor

Med analytiska arbetsytor för logg kan du visa och analysera säkerhetsgranskningshändelserna med hjälp av Azure Monitor och Kusto-frågespråket. Det här frågespråket är utformat för skrivskyddad användning som har energifunktioner med en lättläst syntax. Mer information om hur du kommer igång med Kusto-frågespråk finns i följande artiklar:

* [Dokumentation om Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/)
* [Kom igång med Log Analytics i Azure Monitor](../azure-monitor/log-query/get-started-portal.md)
* [Komma igång med loggfrågor i Azure Monitor](../azure-monitor/log-query/get-started-queries.md)
* [Skapa och dela instrumentpaneler för Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md)

Följande exempelfrågor kan användas för att börja analysera säkerhetsgranskningshändelser från Azure AD DS.

### <a name="sample-query-1"></a>Exempel på fråga 1

Visa alla kontoutelåsningshändelser de senaste sju dagarna:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

### <a name="sample-query-2"></a>Exempel på fråga 2

Visa alla kontoutelåsningshändelser (*4740*) mellan den 3 februari 2020 klockan 9.00. och 10 februari 2020 midnatt, sorterade stigande efter datum och tid:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-02-03 09:00) and TimeGenerated <= datetime(2020-02-10)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

### <a name="sample-query-3"></a>Exempel på fråga 3

Visa inloggningshändelser för konto för sju dagar sedan (från och med nu) för kontot som heter användaren:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-4"></a>Exempel på fråga 4

Visa inloggningshändelser för konto för sju dagar sedan för kontot som heter användaren och som försökte logga in med ett felaktigt lösenord (*0xC0000006a*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc000006a" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-5"></a>Exempel på fråga 5

Visa inloggningshändelser för konto sju dagar sedan från och med nu för kontot med namnet användaren som försökte logga in medan kontot var utelåst (*0xC0000234*):

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "user" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

### <a name="sample-query-6"></a>Exempel på fråga 6

Visa antalet konto inloggningshändelser för sju dagar sedan från och med nu för alla inloggningsförsök som inträffade för alla utelåst användare:

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where "0xc0000234" == tolower(extract("Error Code:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
| summarize count()
```

## <a name="next-steps"></a>Nästa steg

För specifik information om Kusto, se följande artiklar:

* [Översikt över](/azure/kusto/query/) Kusto-frågespråket.
* [Kusto handledning](/azure/kusto/query/tutorial) för att bekanta dig med frågan grunderna.
* [Exempel på frågor](/azure/kusto/query/samples) som hjälper dig att lära dig nya sätt att se dina data.
* Kusto [bästa praxis](/azure/kusto/query/best-practices) för att optimera dina frågor för framgång.

<!-- LINKS - Internal -->
[migrate-azure-adds]: migrate-from-classic-vnet.md
