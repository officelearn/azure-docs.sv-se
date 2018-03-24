---
title: Azure Resource Manager-providern Operations | Microsoft Docs
description: Information om åtgärder som är tillgängliga på Microsoft Azure Resource Manager-resursprovidrar
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/06/2018
ms.author: rolyon
ms.openlocfilehash: 0b8c8823c6d21df96dcfd926db1855169f1570e4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Åtgärder i Azure Resource Manager-Resursprovidern

Det här dokumentet innehåller åtgärder som är tillgängliga för varje resursprovider för Microsoft Azure Resource Manager. Dessa kan användas i anpassade roller för att tillhandahålla detaljerade rollbaserad åtkomstkontroll (RBAC) behörigheter till resurser i Azure. Lägg märke till detta är inte en omfattande lista och åtgärder kan läggas till eller tas bort under varje provider är uppdaterad. Åtgärden strängar följa formatet för `Microsoft.<ProviderName>/<ChildResourceType>/<action>`. 

> [!NOTE]
> Använd för en omfattande och aktuella lista `Get-AzureRmProviderOperation` (i PowerShell) eller `az provider operation list` (i Azure CLI v2) till Liståtgärder över providers som Azure-resurs.

## <a name="microsoftaad"></a>Microsoft.AAD

| Åtgärd | Beskrivning |
|---|---|
|/domainServices/delete|Tar bort Domain Services.|
|/domainServices/read|Reads Domain Services.|
|/domainServices/write|Write Domain Services|
|/Locations/operationresults/Read|Läsa status för en asynkron åtgärd.|
|/ Operations/läsning|Lokaliserad beskrivning för åtgärden, som det ska visas för användaren.|

## <a name="microsoftaadiam"></a>microsoft.aadiam

| Åtgärd | Beskrivning |
|---|---|
|/diagnosticsettings/delete|Om du tar bort en diagnostikinställningen|
|/diagnosticsettings/Read|Läser en diagnostikinställningen|
|/ diagnosticsettings/skrivning|Skriva ett diagnostikinställningen|
|/diagnosticsettingscategories/Read|Läser en diagnostikinställningen kategorier|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/tenants/providers/Microsoft.Insights/logDefinitions/read|Hämtar de tillgängliga loggarna för klienter|

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

| Åtgärd | Beskrivning |
|---|---|
|/ configuration/åtgärd|Klientkonfiguration för uppdateringar.|
|/Configuration/Read|Läser konfigurationen av klienten.|
|/ configuration/skrivning|Skapar en klientkonfiguration.|
|/ services/åtgärd|Uppdaterar en tjänstinstans i klienten.|
|/Services/Alerts/Read|Läser aviseringar för en tjänst.|
|/Services/Alerts/Read|Läser aviseringar för en tjänst.|
|/Services/delete|Tar bort en instans av tjänsten på klienten.|
|/Services/Read|Läser tjänstinstanser i klienten.|
|/Services/servicemembers/Action|Skapar en instans av tjänsten medlem i tjänsten.|
|/Services/servicemembers/Alerts/Read|Läser aviseringar för en medlem i tjänsten.|
|/Services/servicemembers/delete|Tar bort en instans av tjänsten medlem i tjänsten.|
|/Services/servicemembers/Read|Läser tjänstinstans för medlem i tjänsten.|
|/ services/skrivning|Skapar en instans av tjänsten på klienten.|

## <a name="microsoftadvisor"></a>Microsoft.Advisor

| Åtgärd | Beskrivning |
|---|---|
|/Configurations/Read|Hämta konfigurationer|
|/ konfigurationer/skrivning|Konfiguration av skapar/uppdateringar|
|/ generateRecommendations/åtgärd|Skapar rekommendationer|
|/generateRecommendations/Read|Hämtar generera rekommendationer status|
|/Operations/Read|Hämtar åtgärder för Microsoft Advisor|
|/recommendations/Read|Läser rekommendationer|
|/recommendations/suppressions/delete|Tar bort Undertryckning|
|/recommendations/suppressions/Read|Hämtar suppressions|
|/recommendations/suppressions/Write|Skapat/uppdaterat suppressions|
|registrera/åtgärd|Registrerar prenumerationen för Microsoft Advisor|
|/suppressions/delete|Tar bort Undertryckning|
|/suppressions/Read|Hämtar suppressions|
|/ suppressions/skrivning|Skapat/uppdaterat suppressions|
|/unregister/action|Avregistrerar prenumeration för Microsoft Advisor|

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

| Åtgärd | Beskrivning |
|---|---|
|/Alerts/Read|Hämta alla aviseringar för inkommande trafik.|
|/Alerts/Resolve/Action|Ändra tillstånd för aviseringen till 'Åtgärda'|
|/alertsSummary/Read|Få en översikt över aviseringar|
|/ Operations/läsning|Läser de åtgärder som anges|

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

| Åtgärd | Beskrivning |
|---|---|
|/Locations/checkNameAvailability/Action|Kontrollerar att namnet angivna Analysis Server är giltigt och inte i användning.|
|/Locations/operationresults/Read|Hämtar information om resultatet för den angivna åtgärden.|
|/Locations/operationstatuses/Read|Hämtar information om statusen för den angivna åtgärden.|
|/Operations/Read|Hämtar information av åtgärder|
|registrera/åtgärd|Registrerar Analysis Services-resursprovidern.|
|/Servers/delete|Tar bort analysserver.|
|/servers/listGatewayStatus/action|Visa status för gatewayen som associeras med servern.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar diagnostikinställningen för Analysis Server|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningen för Analysis Server|
|/servers/providers/Microsoft.Insights/logDefinitions/read|Hämtar de tillgängliga loggarna för servrar|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för Analysis Server|
|/Servers/Read|Hämtar information för den angivna Analysis Server.|
|/Servers/Resume/Action|Återupptar analysserver.|
|/servers/skus/read|Hämta information om tillgängliga SKU för servern|
|/servers/suspend/action|Pausar analysserver.|
|/ servrar/skrivning|Skapar eller uppdaterar den angivna Analysis-servern.|
|/skus/Read|Hämtar information av SKU: er|

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

| Åtgärd | Beskrivning |
|---|---|
|/checkNameAvailability/Read|Kontrollerar om tjänstens namn är tillgängligt|
|/Operations/Read|Läsa alla API åtgärder som är tillgängliga för Microsoft.ApiManagement resurs|
|registrera/åtgärd|Registrera prenumerationen för Microsoft.ApiManagement resursprovidern|
|/Reports/Read|Hämta rapporter sammanställs efter tidsperioder, geografisk region, utvecklare, produkter, API: er, åtgärder, prenumeration och byRequest.|
|/Service/Apis/delete|Ta bort befintliga API|
|/Service/Apis/Diagnostics/delete|Ta bort befintliga diagnostik|
|/Service/Apis/Diagnostics/loggers/delete|Ta bort mappning av en logg med en diagnostikinställningen|
|/Service/Apis/Diagnostics/loggers/Read|Hämta lista över befintliga diagnostiska loggar tangenttryckningar|
|/Service/Apis/Diagnostics/loggers/Write|Mappa loggaren till en diagnostikinställningen|
|/Service/Apis/Diagnostics/Read|Hämta lista över diagnostik eller hämta information för diagnostik|
|/Service/Apis/Diagnostics/Write|Lägger till nya diagnostiska eller uppdaterar befintliga diagnosinformation|
|/Service/Apis/Operations/delete|Ta bort befintliga API-åtgärd|
|/Service/Apis/Operations/policies/delete|Ta bort principkonfigurationen från principer för API-åtgärd|
|/Service/Apis/Operations/policies/Read|Hämta principer för API-åtgärd eller Get princip konfigurationsinformation för API-åtgärd|
|/Service/Apis/Operations/policies/Write|Ange information om principen för API-åtgärd|
|/Service/Apis/Operations/policy/delete|Ta bort principkonfigurationen från åtgärden|
|/Service/Apis/Operations/policy/Read|Hämta information om principen för åtgärden|
|/Service/Apis/Operations/policy/Write|Ange information om konfiguration av principen för åtgärden|
|/Service/Apis/Operations/Read|Hämta listan över befintliga API: et eller hämta information om API-åtgärd|
|/Service/Apis/Operations/Tags/delete|Ta bort associationen mellan befintlig tagg med befintliga åtgärden|
|/Service/Apis/Operations/Tags/Read|Hämta taggarna associerade med åtgärden eller hämta taggen information|
|/Service/Apis/Operations/Tags/Write|Associera befintlig tagg med befintliga åtgärden|
|/Service/Apis/Operations/Write|Skapa nya API-åtgärden eller uppdatera befintliga API-åtgärd|
|/service/apis/operationsByTags/read|Hämta lista över kopplingar åtgärden-tagg|
|/Service/Apis/policies/delete|Ta bort principkonfigurationen från API-principer|
|/Service/Apis/policies/Read|Hämta principer för API: et eller Get princip konfigurationsinformation för API|
|/Service/Apis/policies/Write|Ange information om principen för API|
|/Service/Apis/policy/delete|Ta bort principkonfigurationen från API: et|
|/Service/Apis/policy/Read|Hämta information om principen för API|
|/Service/Apis/policy/Write|Ange information om principen för API|
|/Service/Apis/products/Read|Hämta alla produkter som API: et är en del av|
|/Service/Apis/Read|Hämta lista över alla registrerade API: er eller hämta information om API|
|/Service/Apis/releases/delete|Tar bort alla versioner av API eller ta bort API-versionen|
|/Service/Apis/releases/Read|Hämta versioner för en API eller hämta information om API reelase|
|/Service/Apis/releases/Write|Skapa ny API-version eller uppdatera befintliga API-versionen|
|/service/apis/revisions/delete|Tar bort alla revisioner av API|
|/Service/Apis/revisions/Read|Hämta revisioner som hör till en API|
|/service/apis/schemas/delete|Tar bort befintliga schemat|
|/Service/Apis/schemas/Document/Read|Hämta det dokument som beskriver schemat|
|/service/apis/schemas/document/write|Uppdatera dokumentet som beskriver schemat|
|/service/apis/schemas/read|Hämtar de scheman som används av API: et eller hämtar alla scheman för en given API|
|/service/apis/schemas/write|Anger de scheman som används av API: et|
|/Service/Apis/tagDescriptions/delete|Ta bort taggen beskrivning från API: et|
|/Service/Apis/tagDescriptions/Read|Hämta taggar beskrivningar i omfånget för API: et eller hämta taggen beskrivning i omfånget för API|
|/Service/Apis/tagDescriptions/Write|Skapa/ändra taggen beskrivning i omfattningen av API|
|/Service/Apis/Tags/delete|Ta bort den befintliga API-tagg kopplingen|
|/Service/Apis/Tags/Read|Hämta alla API-tagg associationen för API: et eller hämta information om API-tagg association|
|/Service/Apis/Tags/Write|Lägga till en ny koppling för API-tagg|
|/Service/Apis/Write|Skapa nya API eller uppdatera befintliga API-information|
|/Service/apisByTags/Read|Hämta lista över API-tagg kopplingar|
|/service/api-version-sets/delete|Ta bort befintliga VersionSet|
|/Service/API-version-Sets/Read|Hämta lista över version grupp entiteter eller hämtar information om en VersionSet|
|/Service/API-version-Sets/versions/Read|Hämta lista över version entiteter|
|/service/api-version-sets/write|Skapa nya VersionSet eller uppdatera den befintliga VersionSet information|
|/service/applynetworkconfigurationupdates/action|Uppdaterar de Microsoft.ApiManagement resurser som körs i virtuella nätverk för att hämta uppdaterade nätverksinställningar.|
|/service/authorizationServers/delete|Ta bort den befintliga auktorisering server|
|/Service/authorizationServers/Read|Hämta listan över servrar för auktorisering eller hämta information om auktorisering server|
|/service/authorizationServers/write|Skapa en ny auktoriserings-server eller uppdatera information om en befintlig server auktorisering|
|/service/backends/delete|Ta bort befintlig serverdel|
|/service/backends/read|Hämta listan över serverdelar eller hämta information om backend|
|/Service/backends/Reconnect/Action|Skapa en begäran om återanslutningen|
|/service/backends/write|Lägg till en ny serverdel eller uppdatera befintliga backend-information|
|/Service/Backup/Action|Säkerhetskopiering API-tjänsten till den angivna behållaren i en användare som storage-konto|
|/Service/Certificates/delete|Ta bort befintligt certifikat|
|/Service/Certificates/Read|Hämta listan över certifikat eller hämta information om certifikat|
|/Service/Certificates/Write|Lägg till nytt certifikat|
|/Service/delete|Ta bort en instans för API Management-tjänsten|
|/Service/Diagnostics/delete|Ta bort befintliga diagnostik|
|/Service/Diagnostics/loggers/delete|Ta bort mappning av en logg med en diagnostikinställningen|
|/Service/Diagnostics/loggers/Read|Hämta lista över befintliga diagnostiska loggar tangenttryckningar|
|/Service/Diagnostics/loggers/Write|Mappa loggaren till en diagnostikinställningen|
|/Service/Diagnostics/Read|Hämta lista över diagnostik eller hämta information för diagnostik|
|/Service/Diagnostics/Write|Lägger till nya diagnostiska eller uppdaterar befintliga diagnosinformation|
|/Service/getssotoken/Action|Hämtar SSO-token som kan användas för att logga in i API Management-tjänsten äldre portal som administratör|
|/Service/groups/delete|Ta bort en befintlig grupp|
|/Service/groups/Read|Hämta listan över grupper eller hämtar information om en grupp|
|/Service/groups/Users/delete|Ta bort befintliga användare från en befintlig grupp|
|/Service/groups/Users/Read|Hämta lista över gruppanvändare|
|/Service/groups/Users/Write|Lägg till befintliga användare i en befintlig grupp|
|/Service/groups/Write|Skapa ny grupp eller uppdatera befintliga gruppinformation|
|/service/identityProviders/delete|Ta bort den befintliga identitetsleverantören.|
|/service/identityProviders/read|Hämta lista över identitetsleverantörer eller hämta information av identitetsleverantören.|
|/service/identityProviders/write|Skapa en ny identitetsleverantör eller uppdatera information om en befintlig identitetsleverantör|
|/Service/Locations/networkstatus/Read|Hämtar nätverksstatus för åtkomst av resurser som tjänsten är beroende på plats.|
|/Service/loggers/delete|Ta bort befintliga transaktionsloggfiler|
|/Service/loggers/Read|Hämta listan över loggar tangenttryckningar eller hämta information om loggning|
|/Service/loggers/Write|Lägg till ny loggare eller uppdatera information om befintliga transaktionsloggfiler|
|/Service/managedeployments/Action|Ändra SKU/enheter, Lägg till/ta bort regionala distributioner av API Management-tjänsten|
|/Service/networkstatus/Read|Hämtar nätverksstatus för åtkomst av resurser som tjänsten är beroende av.|
|/Service/Notifications/Action|Skickar meddelandet till en angiven användare|
|/Service/Notifications/Read|Hämtar alla API Management publisher meddelanden eller hämta API Management publisher detaljer|
|/Service/Notifications/recipientEmails/delete|Tar bort befintlig e-postadress som är associerad med ett meddelande|
|/Service/Notifications/recipientEmails/Read|Hämta e-postmottagare som är associerade med API Management Publisher meddelande|
|/Service/Notifications/recipientEmails/Write|Skapa ny e-postmottagare för meddelandet|
|/Service/Notifications/recipientUsers/delete|Tar bort användare som är kopplad till den meddelandemottagare|
|/Service/Notifications/recipientUsers/Read|Hämta mottagaren användare som associeras med meddelandet|
|/service/notifications/recipientUsers/write|Lägg till användare i meddelandemottagare|
|/Service/Notifications/Write|Skapa eller API uppdateringshantering publisher meddelande|
|/service/openidConnectProviders/delete|Ta bort befintliga OpenID Connect-providern|
|/service/openidConnectProviders/read|Hämta lista över OpenID Connect providers eller hämta information för OpenID Connect Provider|
|/service/openidConnectProviders/write|Skapa en ny information för OpenID Connect Provider eller uppdatering av en befintlig OpenID Connect-Provider|
|/Service/operationresults/Read|Hämtar aktuell status för långvarig åtgärd|
|/Service/policies/delete|Ta bort principkonfigurationen från innehavaren principer|
|/Service/policies/Read|Hämta principer för klient- eller Get princip konfigurationsinformation för klient|
|/Service/policies/Write|Ange information om principen för klienten|
|/service/policySnippets/read|Hämta alla princip kodavsnitt|
|/Service/portalsettings/Read|Hämta logga in inställningar för portalen eller Get logga In inställningarna för portalen eller hämta delegeringsinställningar för portalen|
|/service/portalsettings/write|Uppdatera registrera dig inställningar eller uppdatering registrera dig inställningar eller uppdatering-logga In inställningar eller uppdatering-logga In inställningar eller delegeringsinställningar för uppdatering eller uppdatering delegeringsinställningar|
|/Service/products/Apis/delete|Ta bort befintliga API från befintliga produkten|
|/Service/products/Apis/Read|Hämta lista över API: er som lagts till i befintliga produkten|
|/Service/products/Apis/Write|Lägga till befintliga API för befintliga produkt|
|/Service/products/delete|Ta bort befintliga produkten|
|/Service/products/groups/delete|Ta bort associationen av befintlig developer grupp med befintliga produkten|
|/Service/products/groups/Read|Hämta lista över developer grupper som är associerade med produkt|
|/Service/products/groups/Write|Associera befintlig developer grupp med befintliga produkt|
|/Service/products/policies/delete|Ta bort principkonfigurationen från produkt-principer|
|/Service/products/policies/Read|Hämta principer för produkt eller Get princip konfigurationsinformation för produkten|
|/Service/products/policies/Write|Ange information om principen för produkten|
|/Service/products/policy/delete|Ta bort principkonfigurationen från befintliga produkten|
|/Service/products/policy/Read|Hämta konfiguration av befintlig produkt|
|/Service/products/policy/Write|Konfigurera principen för befintliga produkten|
|/Service/products/Read|Hämta lista över produkter eller hämta information om produkt|
|/Service/products/subscriptions/Read|Hämta listan över prenumerationer för produkten|
|/Service/products/Tags/delete|Ta bort associationen mellan befintlig tagg med befintlig produkt|
|/Service/products/Tags/Read|Hämta taggarna associerade med produkt eller hämta taggen information|
|/Service/products/Tags/Write|Associera befintlig tagg med befintlig produkt|
|/Service/products/Write|Skapa en ny produkt eller uppdatera befintliga produktinformation|
|/Service/Properties/delete|Tar bort befintlig egenskap|
|/Service/Properties/Read|Hämtar information om den angivna egenskapen eller hämtar en lista över alla egenskaper|
|/Service/Properties/Write|Skapar en ny egenskap eller uppdaterar värdet för den angivna egenskapen|
|/service/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar diagnostikinställningen för API Management-tjänsten|
|/service/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningen för API Management-tjänsten|
|/service/providers/Microsoft.Insights/logDefinitions/read|Hämtar de tillgängliga loggarna för API Management-tjänsten|
|/service/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för API Management-tjänsten|
|/Service/Quotas/periods/Read|Hämta räknarvärdet kvoten för perioden|
|/Service/Quotas/periods/Write|Ange kvoten aktuella räknarvärdet|
|/Service/Quotas/Read|Hämta värden för kvot|
|/Service/Quotas/Write|Ange kvoten aktuella räknarvärdet|
|/Service/Read|Läsa in metadata för en instans för API Management-tjänsten|
|/Service/Reports/Read|Hämta rapport sammanställs efter tidsperioder eller Get-rapport som sammanställs efter geografisk region eller Get-rapport som aggregeras av utvecklare. eller hämta rapport sammanställs efter produkter. eller hämta rapport som har aggregerats med API: er eller Get rapporten sammanställs efter operations eller Get-rapport som aggregeras av prenumerationen. eller hämta begäranden rapportdata|
|/Service/Restore/Action|Återställa API Management-tjänsten från den angivna behållaren i en användare har angett storage-konto|
|/Service/subscriptions/delete|Ta bort prenumerationen. Den här åtgärden kan användas för att ta bort prenumerationen|
|/Service/subscriptions/Read|Hämta en lista över produktprenumeration eller hämta information om produkten prenumeration|
|/service/subscriptions/regeneratePrimaryKey/action|Återskapa primärnyckeln för prenumeration|
|/service/subscriptions/regenerateSecondaryKey/action|Återskapa en sekundär nyckel för prenumeration|
|/Service/subscriptions/Write|Prenumerera på en befintlig användare till en befintlig produkt eller uppdatera befintliga prenumerationsinformation. Den här åtgärden kan användas för att förnya prenumerationen|
|/Service/tagResources/Read|Hämta lista över taggar med associerade resurser|
|/Service/Tags/delete|Ta bort befintlig tagg|
|/Service/Tags/Read|Hämta lista över taggar eller hämta information om tagg|
|/Service/Tags/Write|Lägg till ny tagg eller uppdatera information om befintliga tagg|
|/Service/Templates/delete|Återställ standardmall för API Management-e-post|
|/Service/Templates/Read|Hämtar alla postmallar för e-eller hämtar API Management e-mallinformation|
|/Service/Templates/Write|Skapa eller uppdatera API-hantering e-post eller uppdaterar API Management e-mallen|
|/service/tenant/delete|Ta bort konfiguration för klienten|
|/Service/tenant/Deploy/Action|Kör en åtgärd för distribution för att tillämpa ändringarna från den angivna git-grenen i konfigurationen i databasen.|
|/Service/tenant/operationResults/Read|Hämta listan över Åtgärdsresultat eller hämta resultatet av en specifik åtgärd|
|/Service/tenant/Read|Hämta konfiguration för den eller Get-klient access informationsdetaljer|
|/service/tenant/regeneratePrimaryKey/action|Återskapa primärnyckeln|
|/service/tenant/regenerateSecondaryKey/action|Återskapa den sekundära åtkomstnyckeln|
|/Service/tenant/Save/Action|Skapar commit med ögonblicksbild av konfigurationen för den angivna grenen i databasen|
|/service/tenant/syncState/read|Hämta status för senaste git-synkronisering|
|/Service/tenant/Validate/Action|Verifierar ändringar från den angivna git-grenen|
|/Service/tenant/Write|Konfigurera principen för klient eller information uppdateringsdetaljer klient åtkomst|
|/Service/updatecertificate/Action|Ladda upp SSL-certifikat för en API Management-tjänsten|
|/service/updatehostname/action|Konfigurera, uppdatera eller ta bort anpassade domännamn för en API Management-tjänsten|
|/Service/Users/Action|Registrera en ny användare|
|/Service/Users/Applications/Attachments/delete|Tar bort en bilaga|
|/Service/Users/Applications/Attachments/Read|Hämtar programmet bifogade filer eller hämtar bifogade filer|
|/Service/Users/Applications/Attachments/Write|Lägga till bilaga till programmet|
|/Service/Users/Applications/delete|Tar bort befintliga program|
|/Service/Users/Applications/Read|Hämta lista över alla program eller hämtar API Management programinformation|
|/Service/Users/Applications/Write|Registrerar ett program till API-hantering eller uppdateringar programinformation|
|/Service/Users/delete|Ta bort användarkonto|
|/service/users/generateSsoUrl/action|Generera en URL för enkel inloggning. URL: en kan användas för åtkomst till administrationsportal|
|/Service/Users/groups/Read|Hämta lista över användargrupper|
|/Service/Users/keys/Read|Hämta lista över användarnycklar|
|/Service/Users/Read|Hämta en lista över registrerade användare eller hämta kontoinformation för en användare|
|/Service/Users/subscriptions/Read|Hämta listan över användare prenumerationer|
|/Service/Users/token/Action|Hämta token åtkomsttoken för en användare|
|/Service/Users/Write|Registrera en ny användare eller uppdatera kontoinformation för en befintlig användare|
|/ service/skrivning|Skapa en ny instans av API Management-tjänsten|
|/unregister/action|Avregistrera prenumerationen för Microsoft.ApiManagement resursprovidern|

## <a name="microsoftauthorization"></a>Microsoft.Authorization

| Åtgärd | Beskrivning |
|---|---|
|/ checkAccess/åtgärd|Kontrollerar att anroparen har behörighet att utföra en viss åtgärd|
|/classicAdministrators/delete|Tar bort administratören från prenumerationen.|
|/classicAdministrators/Read|Läser in prenumerationens administratörer.|
|/ classicAdministrators/skrivning|Lägg till eller ta bort en administratör för en prenumeration.|
|/ elevateAccess/åtgärd|Ger anroparen åtkomst till administratör för användaråtkomst i klientomfattningen|
|/locks/delete|Ta bort lås i det specificerade omfånget.|
|/locks/Read|Hämtar lås i det specificerade omfånget.|
|/ Lås/skrivning|Lägg till lås i det specificerade omfånget.|
|/permissions/Read|Listar alla behörigheter som anroparen har i ett givet omfång.|
|/policyAssignments/delete|Ta bort tilldelning av principer i det definierade området.|
|/policyAssignments/Read|Hämta information om en tilldelning av principer.|
|/ policyAssignments/skrivning|Skapa en principtilldelning i det definierade området.|
|/policyDefinitions/delete|Ta bort en definition av principen.|
|/policyDefinitions/Read|Hämta information om en principdefinition.|
|/ policyDefinitions/skrivning|Skapa en anpassad principdefinition.|
|/policySetDefinitions/delete|Ta bort en principuppsättningsdefinition.|
|/policySetDefinitions/read|Hämta information om en principuppsättningsdefinition.|
|/policySetDefinitions/write|Skapa en anpassad principuppsättningsdefinition.|
|/providerOperations/Read|Hämta åtgärder för alla providrar som kan användas i rolldefinitioner.|
|/roleAssignments/delete|Ta bort en rolltilldelning i det specificerade omfånget.|
|/roleAssignments/Read|Hämta information om en rolltilldelning.|
|/ roleAssignments/skrivning|Skapa en rolltilldelning i det specificerade omfånget.|
|/roleDefinitions/delete|Ta bort den angivna anpassade rolldefinitionen.|
|/roleDefinitions/Read|Hämta information om en rolldefinition.|
|/ roleDefinitions/skrivning|Skapa eller uppdatera en anpassad rolldefinition med angivna behörigheter och tilldelningsbara scope.|

## <a name="microsoftautomation"></a>Microsoft.Automation

| Åtgärd | Beskrivning |
|---|---|
|/automationAccounts/agentRegistrationInformation/read|Läs en Azure Automation DSC-registreringsinformation|
|/automationAccounts/agentRegistrationInformation/regenerateKey/action|Skriver en begäran om att återskapa nycklar för Azure Automation DSC|
|/automationAccounts/certificates/delete|Tar bort en Azure Automation-certifikattillgång|
|/automationAccounts/Certificates/Read|Hämtar en Azure Automation-certifikattillgång|
|/automationAccounts/Certificates/Write|Skapar eller uppdaterar en Azure Automation-certifikattillgång|
|/automationAccounts/compilationjobs/read|Läser en Azure Automation DSC-kompilering|
|/automationAccounts/compilationjobs/write|Skriver en Azure Automation DSC-kompilering|
|/automationAccounts/configurations/delete|Tar bort en Azure Automation DSC-innehåll|
|/automationAccounts/configurations/getCount/action|Läser antal Azure Automation DSC-innehåll|
|/automationAccounts/configurations/read|Hämtar en Azure Automation DSC-innehåll|
|/automationAccounts/configurations/write|Skriver en Azure Automation DSC-innehåll|
|/automationAccounts/connections/delete|Tar bort en Azure Automation-anslutningstillgång|
|/automationAccounts/Connections/Read|Hämtar en Azure Automation-anslutningstillgång|
|/automationAccounts/Connections/Write|Skapar eller uppdaterar en Azure Automation-anslutningstillgång|
|/automationAccounts/connectionTypes/delete|Tar bort en Azure Automation-anslutningstyptillgång|
|/automationAccounts/connectionTypes/Read|Hämtar en Azure Automation-anslutningstyptillgång|
|/automationAccounts/connectionTypes/Write|Skapar en Azure Automation-anslutningstyptillgång|
|/automationAccounts/credentials/delete|Tar bort en Azure Automation-autentiseringsuppgiftstillgång|
|/automationAccounts/credentials/read|Hämtar en Azure Automation-autentiseringsuppgiftstillgång|
|/automationAccounts/credentials/write|Skapar eller uppdaterar en Azure Automation-autentiseringsuppgiftstillgång|
|/automationAccounts/delete|Tar bort en Azure Automation-konto|
|/automationAccounts/diagnosticSettings/Read|Hämtar den diagnostiska inställningen för resursen|
|/automationAccounts/diagnosticSettings/write|Anger diagnostikinställningen för resursen|
|/automationAccounts/hybridRunbookWorkerGroups/delete|Tar bort Hybrid Runbook Worker-resurser|
|/automationAccounts/hybridRunbookWorkerGroups/read|Läser Hybrid Runbook Worker-resurser|
|/automationAccounts/jobs/Output/Action|Hämtar utdata för ett jobb|
|/automationAccounts/jobs/Output/Action|Hämtar utdata för ett jobb|
|/automationAccounts/jobs/Read|Hämtar en Azure Automation-jobb|
|/automationAccounts/jobs/Read|Hämtar en Azure Automation-jobb|
|/automationAccounts/jobs/Resume/Action|Återupptar ett Azure Automation-jobb|
|/automationAccounts/jobs/Resume/Action|Återupptar ett Azure Automation-jobb|
|/automationAccounts/jobs/runbookContent/action|Hämtar innehållet i Azure Automation-runbook vid tidpunkten för jobbkörningen|
|/automationAccounts/jobs/runbookContent/action|Hämtar innehållet i Azure Automation-runbook vid tidpunkten för jobbkörningen|
|/automationAccounts/jobs/stop/Action|Stoppar ett Azure Automation-jobb|
|/automationAccounts/jobs/stop/Action|Stoppar ett Azure Automation-jobb|
|/automationAccounts/jobs/Streams/Read|Hämtar en Azure Automation-jobbström|
|/automationAccounts/jobs/Streams/Read|Hämtar en Azure Automation-jobbström|
|/automationAccounts/jobs/suspend/action|Pausar ett Azure Automation-jobb|
|/automationAccounts/jobs/suspend/action|Pausar ett Azure Automation-jobb|
|/automationAccounts/jobs/Write|Skapar ett Azure Automation-jobb|
|/automationAccounts/jobs/Write|Skapar ett Azure Automation-jobb|
|/automationAccounts/jobSchedules/delete|Tar bort ett Azure Automation-Jobbschema|
|/automationAccounts/jobSchedules/Read|Hämtar ett Azure Automation-Jobbschema|
|/automationAccounts/jobSchedules/write|Skapar ett Azure Automation-Jobbschema|
|/automationAccounts/linkedWorkspace/Read|Hämtar arbetsytan kopplad till automation-kontot|
|/automationAccounts/logDefinitions/Read|Hämtar de tillgängliga loggarna för automation-kontot|
|/automationAccounts/Modules/Activities/Read|Hämtar Azure Automation-aktiviteter|
|/automationAccounts/Modules/delete|Tar bort en Azure Automation-modul|
|/automationAccounts/Modules/Read|Hämtar en Azure Automation-modul|
|/automationAccounts/Modules/Write|Skapar eller uppdaterar en Azure Automation-modul|
|/automationAccounts/nodeConfigurations/delete|Tar bort en Azure Automation DSC-nodkonfiguration|
|/automationAccounts/nodeConfigurations/read|Läser en Azure Automation DSC-nodkonfiguration|
|/automationAccounts/nodeConfigurations/readContent/action|Läser en Azure Automation DSC-nod configuration innehåll|
|/automationAccounts/nodeConfigurations/write|Skriver en Azure Automation DSC-nodkonfiguration|
|/automationAccounts/nodes/delete|Tar bort Azure Automation DSC-noder|
|/automationAccounts/nodes/read|Läser en Azure Automation DSC-noder|
|/automationAccounts/nodes/Reports/Read|Läser en Azure Automation DSC rapporten contentss|
|/automationAccounts/nodes/Reports/Read|Läser en Azure Automation DSC-rapporter|
|/automationAccounts/objectDataTypes/fields/read|Hämtar Azure Automation-TypeFields|
|/automationAccounts/providers/Microsoft.Insights/metricDefinitions/read|Hämtar Måttdefinitionerna för Automation|
|/automationAccounts/Read|Hämtar ett Azure Automation-konto|
|/automationAccounts/runbooks/delete|Tar bort en Azure Automation-runbook|
|/automationAccounts/runbooks/draft/publish/action|Publicerar ett Azure Automation-runbookutkast|
|/automationAccounts/runbooks/draft/read|Hämtar ett Azure Automation-runbookutkast|
|/automationAccounts/runbooks/draft/readContent/action|Hämtar innehållet i ett Azure Automation-runbookutkast|
|/automationAccounts/runbooks/draft/testJob/read|Hämtar en Azure Automation runbookutkast|
|/automationAccounts/runbooks/draft/testJob/resume/action|Återupptar ett Azure Automation runbookutkast|
|/automationAccounts/runbooks/draft/testJob/stop/action|Stoppar ett Azure Automation runbookutkast|
|/automationAccounts/runbooks/draft/testJob/suspend/action|Pausar ett Azure Automation runbookutkast|
|/automationAccounts/runbooks/draft/testJob/write|Skapar en Azure Automation runbookutkast|
|/automationAccounts/runbooks/draft/undoEdit/action|Ångra redigeringar i ett Azure Automation-runbookutkast|
|/automationAccounts/runbooks/draft/writeContent/action|Skapar innehållet i ett Azure Automation-runbookutkast|
|/automationAccounts/runbooks/Read|Hämtar en Azure Automation-runbook|
|/automationAccounts/runbooks/readContent/action|Hämtar innehållet i en Azure Automation-runbook|
|/automationAccounts/runbooks/write|Skapar eller uppdaterar en Azure Automation-runbook|
|/automationAccounts/schedules/delete|Tar bort en Azure Automation-schematillgång|
|/automationAccounts/Schedules/Read|Hämtar en Azure Automation-schematillgång|
|/automationAccounts/schedules/write|Skapar eller uppdaterar en Azure Automation-schematillgång|
|/automationAccounts/statistics/Read|Hämtar Azure Automation-statistik|
|/automationAccounts/usages/Read|Hämtar Azure Automation-användning|
|/automationAccounts/variables/delete|Tar bort en Azure Automation-variabeltillgång|
|/automationAccounts/variables/Read|Läser en Azure Automation-variabeltillgång|
|/automationAccounts/variables/Write|Skapar eller uppdaterar en Azure Automation-variabeltillgång|
|/automationAccounts/watchers/streams/read|Hämtar en Azure Automation watcher jobbström|
|/automationAccounts/webhooks/delete|Tar bort en Azure Automation-webhook |
|/automationAccounts/webhooks/generateUri/action|Genererar en URI för en Azure Automation-webhook|
|/automationAccounts/webhooks/read|Läser en Azure Automation-webhook|
|/automationAccounts/webhooks/write|Skapar eller uppdaterar en Azure Automation-webhook|
|/ automationAccounts/skrivning|Skapar eller uppdaterar en Azure Automation-konto|
|/ automationAccounts/skrivning|Skapar eller uppdaterar en Azure Automation-konto|

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

| Åtgärd | Beskrivning |
|---|---|
|/b2cDirectories/delete|Ta bort B2C-katalogresurs|
|/b2cDirectories/Read|Visa B2C-katalogresurs|
|/b2cDirectories/write|Skapa eller uppdatera en B2C-katalogresurs|
|/Operations/Read|Läs alla API-åtgärder tillgängliga för resursprovidern Microsoft.AzureActiveDirectory|
|registrera/åtgärd|Registrera prenumeration för resursprovidern Microsoft.AzureActiveDirectory|

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

| Åtgärd | Beskrivning |
|---|---|
|/ Operations/läsning|Hämtar egenskaperna för en resurs provider-åtgärd|
|registrera/åtgärd|Registrerar prenumeration med Microsoft.AzureStack resursprovidern|
|/registrations/customerSubscriptions/delete|Tar bort en Azure-stacken kund prenumeration|
|/Registrations/customerSubscriptions/Read|Hämtar egenskaperna för en Azure-stacken kund prenumeration|
|/registrations/customerSubscriptions/write|Skapar eller uppdaterar en Azure-stacken kund prenumeration|
|/Registrations/delete|Tar bort en Azure-Stack-registrering|
|/registrations/getActivationKey/action|Hämtar senaste Azure Stack aktiveringsnyckeln|
|/Registrations/products/listDetails/Action|Hämtar utökad information för en Azure-stacken Marketplace-produkten|
|/Registrations/products/Read|Hämtar egenskaperna för en Azure-stacken Marketplace-produkt|
|/Registrations/Read|Hämtar egenskaperna för en Azure-Stack-registrering|
|/ registreringar/skrivning|Skapar eller uppdaterar en Azure-Stack-registrering|

## <a name="microsoftbatch"></a>Microsoft.Batch

| Åtgärd | Beskrivning |
|---|---|
|/batchAccounts/applications/delete|Tar bort ett program|
|/batchAccounts/applications/read|Visar program eller hämtar egenskaperna för ett program|
|/batchAccounts/applications/versions/activate/action|Aktiverar ett programpaket|
|/batchAccounts/applications/versions/delete|Tar bort ett programpaket|
|/batchAccounts/applications/versions/read|Hämtar egenskaperna för ett programpaket|
|/batchAccounts/applications/versions/write|Skapar ett nytt programpaket eller uppdaterar ett befintligt programpaket|
|/batchAccounts/applications/write|Skapar ett nytt program eller uppdaterar ett befintligt program|
|/batchAccounts/certificateOperationResults/read|Hämtar resultatet av en tidskrävande certifikatåtgärd i ett Batch-konto|
|/batchAccounts/certificates/cancelDelete/action|Avbryter misslyckad borttagning av ett certifikat på en Batch-kontot|
|/batchAccounts/certificates/delete|Tar bort ett certifikat från en Batch-kontot|
|/batchAccounts/Certificates/Read|Visar en lista över certifikat på en Batch-kontot eller hämtar egenskaperna för ett certifikat|
|/batchAccounts/Certificates/Write|Skapar ett nytt certifikat på en Batch-kontot eller uppdaterar ett befintligt certifikat|
|/batchAccounts/delete|Tar bort ett Batch-konto|
|/batchAccounts/listkeys/action|Visar åtkomstnycklar för Batch-kontot|
|/batchAccounts/operationResults/read|Hämtar resultatet av en långvarig åtgärd för Batch-konto|
|/batchAccounts/poolOperationResults/read|Hämtar resultatet av en tidskrävande pool-åtgärd i ett Batch-konto|
|/batchAccounts/pools/delete|Tar bort poolen från ett Batch-konto|
|/batchAccounts/pools/disableAutoscale/Action|Inaktiverar automatisk skalning för en pool med Batch-konto|
|/batchAccounts/pools/Read|Visar en lista över programpooler på en Batch-kontot eller hämtar egenskaperna för en pool|
|/batchAccounts/pools/stopResize/Action|Stoppar ett pågående att ändra storlek på på poolen Batch-konto|
|/batchAccounts/pools/upgradeOs/Action|Uppgraderar operativsystemet på poolen Batch-konto|
|/batchAccounts/pools/Write|Skapar en ny pool på ett Batch-konto eller uppdaterar en befintlig adresspool|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/batchAccounts/providers/Microsoft.Insights/logDefinitions/read|Hämtar de tillgängliga loggarna för Batch-tjänsten|
|/batchAccounts/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för Batch-tjänsten|
|/batchAccounts/Read|Visar en lista över Batch-konton eller hämtar egenskaperna för ett Batch-konto|
|/batchAccounts/regeneratekeys/action|Återskapar åtkomstnycklar för Batch-kontot|
|/batchAccounts/syncAutoStorageKeys/action|Synkroniserar åtkomstnycklar för lagringskontot automatiskt konfigurerade för Batch-kontot|
|/batchAccounts/write|Skapar ett nytt batchkonto eller uppdaterar ett befintligt batchkonto|
|/Locations/checkNameAvailability/Action|Kontrollerar att kontonamnet är giltigt och inte används.|
|/Locations/Quotas/Read|Hämtar Batch-kvoter för den angivna prenumerationen i den angivna Azure-regionen|
|registrera/åtgärd|Registrerar prenumerationen för Batch-Resursprovidern och kan skapa Batch-konton|
|/unregister/action|Avregistrerar prenumerationen för Batch-Resursprovidern som hindrar skapandet av Batch-konton|

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

| Åtgärd | Beskrivning |
|---|---|
|/Clusters/delete|Tar bort ett Batch AI-kluster|
|/Clusters/Read|Visar en lista över Batch AI-kluster eller hämtar egenskaperna för en Batch AI-kluster|
|/clusters/remoteLoginInformation/action|Visar information om remote inloggningen för ett Batch AI-kluster|
|/ kluster och skrivning|Skapar ett nytt Batch AI-kluster eller uppdaterar ett befintligt Batch AI-kluster|
|/fileservers/delete|Tar bort en Batch AI-filserver|
|/fileservers/read|Visar en lista över Batch AI fileservers eller hämtar egenskaperna för en Batch AI-filserver|
|/fileservers/Resume/Action|Återupptar en Batch AI-filserver|
|/fileservers/suspend/action|Pausar en Batch AI-filserver|
|/fileservers/write|Skapar en ny Batch AI-filserver eller uppdaterar en befintlig Batch AI-filserver|
|/Jobs/delete|Tar bort ett Batch AI-jobb|
|/Jobs/Read|Visar Batch AI jobb eller hämtar egenskaperna för en Batch AI-jobb|
|/jobs/remoteLoginInformation/action|Visar information om remote inloggningen för ett Batch AI-jobb|
|/Jobs/terminate/Action|Avslutar en Batch AI-jobb|
|/ jobb/skrivning|Skapar ett nytt Batch AI-jobb eller uppdaterar ett befintligt Batch AI-jobb|
|registrera/åtgärd|Registrerar prenumerationen för Resursprovidern Batch AI och kan skapa Batch AI-resurser|

## <a name="microsoftbilling"></a>Microsoft.Billing

| Åtgärd | Beskrivning |
|---|---|
|/billingPeriods/Read|Visar en lista över tillgängliga fakturering punkter|
|/invoices/Read|Visar tillgängliga fakturor|

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

| Åtgärd | Beskrivning |
|---|---|
|/mapApis/Delete|Borttagningsåtgärd|
|/mapApis/listSecrets/action|Lista hemligheterna|
|/mapApis/listSingleSignOnToken/action|Läs auktoriseringstoken för enkel inloggning för resursen|
|/mapApis/Read|Läsåtgärd|
|/mapApis/regenerateKey/action|Återskapa nyckeln|
|/mapApis/Write|Skrivåtgärd|
|/ Operations/läsning|Beskrivning av åtgärden.|

## <a name="microsoftcache"></a>Microsoft.Cache

| Åtgärd | Beskrivning |
|---|---|
|/ checknameavailability/åtgärd|Kontrollerar om ett namn kan användas med nytt Redis Cache|
|/Operations/Read|Visar en lista över de åtgärder som 'Microsoft.Cache-leverantör har stöd för.|
|/redis/delete|Ta bort hela Redis-cache|
|/redis/export/Action|Exportera Redis-data till prefixade lagringsblobbar i angivet format|
|/redis/firewallRules/delete|Ta bort IP-brandväggsregler för Redis Cache|
|/redis/firewallRules/Read|Hämta IP-brandväggsregler för Redis Cache|
|/redis/firewallRules/Write|Redigera IP-brandväggsregler för Redis Cache|
|/redis/forceReboot/Action|Tvinga omstart av en cache-instans, vilket kan medföra dataförlust.|
|/redis/import/Action|Importera data av ett angivet format från flera blobbar till Redis|
|/redis/linkedservers/delete|Ta bort länkad server från Redis Cache|
|/redis/linkedservers/Read|Hämta länkade servrar som är kopplade till Redis Cache.|
|/redis/linkedservers/Write|Lägg till länkad server till Redis Cache|
|/redis/listKeys/Action|Visa värdet för åtkomstnycklarna för Redis-cache i hanteringsportalen|
|/redis/listUpgradeNotifications/Read|Lista de senaste uppgraderingsmeddelandena för cacheklienten.|
|/redis/Locations/operationresults/Read|Hämtar resultatet av lång åtgärd som körs som ”plats”-rubrik tidigare returneras till klienten|
|/redis/metricDefinitions/Read|Hämtar tillgängliga mått för ett Redis-cache|
|/redis/patchSchedules/delete|Ta bort uppdateringsschemat för Redis Cache|
|/redis/patchSchedules/Read|Hämtar uppdateringsschemat för Redis Cache|
|/redis/patchSchedules/Write|Ändra uppdateringsschemat för Redis Cache|
|/redis/Read|Visa inställningarna och konfigurationen för Redis-cache i hanteringsportalen|
|/redis/regenerateKey/Action|Ändra värdet för åtkomstnycklarna för Redis-cache i hanteringsportalen|
|/redis/Start/Action|Starta en cacheinstans.|
|/redis/stop/Action|Stoppa en cacheinstans.|
|/ redis/skrivning|Ändra inställningarna och konfigurationen för Redis-cache i hanteringsportalen|
|registrera/åtgärd|Registrerar Microsoft.Cache-resursprovidern med en prenumeration|
|/unregister/action|Avregistrerar Microsoft.Cache-resursprovidern med en prenumeration|

## <a name="microsoftcapacity"></a>Microsoft.Capacity

| Åtgärd | Beskrivning |
|---|---|
|registrera/åtgärd|Registrerar resursprovidern kapacitet och kan skapa kapacitet resurser.|
|/reservationorders/action|Uppdatera alla Reservation|
|/reservationorders/delete|Ta bort alla Reservation|
|/reservationorders/Read|Läsa alla reservationer|
|/reservationorders/reservations/action|Uppdatera alla Reservation|
|/reservationorders/reservations/delete|Ta bort alla Reservation|
|/reservationorders/reservations/read|Läsa alla reservationer|
|/reservationorders/reservations/revisions/read|Läsa alla reservationer|
|/reservationorders/reservations/write|Skapa en Reservation|
|/reservationorders/write|Skapa en Reservation|

## <a name="microsoftcdn"></a>Microsoft.Cdn

| Åtgärd | Beskrivning |
|---|---|
|/ CheckNameAvailability/åtgärd||
|/ CheckResourceUsage/åtgärd||
|/edgenodes/delete||
|/edgenodes/Read||
|/ edgenodes/skrivning||
|/operationresults/delete||
|/operationresults/profileresults/CheckResourceUsage/action||
|/operationresults/profileresults/delete||
|/operationresults/profileresults/endpointresults/CheckResourceUsage/action||
|/operationresults/profileresults/endpointresults/customdomainresults/delete||
|/operationresults/profileresults/endpointresults/customdomainresults/ DisableCustomHttps/action||
|/operationresults/profileresults/endpointresults/customdomainresults/ EnableCustomHttps/action||
|/operationresults/profileresults/endpointresults/customdomainresults/read||
|/operationresults/profileresults/endpointresults/customdomainresults/write||
|/operationresults/profileresults/endpointresults/delete||
|/operationresults/profileresults/endpointresults/Load/Action||
|/operationresults/profileresults/endpointresults/originresults/delete||
|/operationresults/profileresults/endpointresults/originresults/Read||
|/operationresults/profileresults/endpointresults/originresults/write||
|/operationresults/profileresults/endpointresults/PURGE/Action||
|/operationresults/profileresults/endpointresults/Read||
|/operationresults/profileresults/endpointresults/Start/Action||
|/operationresults/profileresults/endpointresults/stop/Action||
|/operationresults/profileresults/endpointresults/ValidateCustomDomain/action||
|/operationresults/profileresults/endpointresults/Write||
|/operationresults/profileresults/GenerateSsoUri/action||
|/operationresults/profileresults/GetSupportedOptimizationTypes/Action||
|/operationresults/profileresults/Read||
|/operationresults/profileresults/Write||
|/operationresults/Read||
|/ operationresults/skrivning||
|/Operations/Read||
|/Profiles/CheckResourceUsage/Action||
|/Profiles/delete||
|/profiles/endpoints/CheckResourceUsage/action||
|/profiles/endpoints/customdomains/delete||
|/profiles/endpoints/customdomains/DisableCustomHttps/action||
|/profiles/endpoints/customdomains/EnableCustomHttps/action||
|/profiles/endpoints/customdomains/read||
|/profiles/endpoints/customdomains/write||
|/Profiles/endpoints/delete||
|/Profiles/endpoints/Load/Action||
|/profiles/endpoints/origins/delete||
|/Profiles/endpoints/Origins/Read||
|/profiles/endpoints/origins/write||
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar diagnostikinställningar för resursen|
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningar för resursen|
|/Profiles/endpoints/providers/Microsoft.Insights/logDefinitions/Read|Hämtar de tillgängliga loggarna för Microsoft.Cdn|
|/Profiles/endpoints/PURGE/Action||
|/Profiles/endpoints/Read||
|/Profiles/endpoints/Start/Action||
|/Profiles/endpoints/stop/Action||
|/profiles/endpoints/ValidateCustomDomain/action||
|/Profiles/endpoints/Write||
|/profiles/GenerateSsoUri/action||
|/Profiles/GetSupportedOptimizationTypes/Action||
|/Profiles/Read||
|/ profiler/skrivning||
|registrera/åtgärd|Registrerar prenumerationen för CDN-resursprovidern och aktiverar funktionen för att skapa CDN-profiler.|
|/ValidateProbe/action||

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

| Åtgärd | Beskrivning |
|---|---|
|/certificateOrders/Certificates/delete|Ta bort ett befintligt certifikat|
|/certificateOrders/Certificates/Read|Hämta listan över certifikat|
|/certificateOrders/Certificates/Write|Lägg till ett nytt certifikat eller uppdatera en befintlig|
|/ certificateOrders/ta bort|Ta bort en befintlig AppServiceCertificate|
|/certificateOrders/Operations/Read|Visa en lista med alla åtgärder från certifikatregistreringsplatsen för app service|
|/ certificateOrders/Läs|Hämta en lista över CertificateOrders|
|/certificateOrders/reissue/Action|Ange en befintlig certificateorder|
|/certificateOrders/renew/Action|Förnya ett befintligt certificateorder|
|/certificateOrders/resendEmail/Action|Skicka e-post med certifikat|
|/certificateOrders/resendRequestEmails/Action|Skicka om begäran e-post till en annan e-postadress|
|/certificateOrders/resendRequestEmails/Action|Hämta platsen förseglingen för ett certifikat i App Service|
|/certificateOrders/retrieveCertificateActions/Action|Hämta listan över certifikat-åtgärder|
|/certificateOrders/retrieveEmailHistory/Action|Hämta certifikat e-historik|
|/certificateOrders/verifyDomainOwnership/Action|Verifiera domänägarskap|
|/ certificateOrders/skrivning|Lägg till en ny certificateOrder eller uppdatera en befintlig|
|/provisionGlobalAppServicePrincipalInUserTenant/Action|Etablera tjänstens huvudnamn för tjänstens huvudnamn för app|
|registrera/åtgärd|Microsoft Certificates registerresursleverantören för prenumerationen|
|/validateCertificateRegistrationInformation/Action|Verifiera certifikatet köp objekt utan att skicka den|

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

| Åtgärd | Beskrivning |
|---|---|
|/Capabilities/Read|Visar funktionerna|
|/checkDomainNameAvailability/action|Kontrollerar tillgängligheten för ett visst domännamn.|
|/domainNames/active/write|Anger det aktiva domännamnet.|
|/domainNames/availabilitySets/read|Visa tillgänglighetsuppsättningen för resursen.|
|/domainNames/capabilities/read|Visar domännamnsfunktionerna|
|/domainNames/delete|Ta bort resursernas domännamn.|
|/domainNames/extensions/delete|Ta bort domännamnstilläggen.|
|/domainNames/extensions/operationStatuses/read|Läser domännamnstilläggens åtgärdsstatus.|
|/domainNames/extensions/read|Returnerar domännamnstilläggen.|
|/domainNames/extensions/write|Lägg till domännamnstilläggen.|
|/domainNames/internalLoadBalancers/delete|Ta bort en ny intern belastningsutjämning.|
|/domainNames/internalLoadBalancers/operationStatuses/read|Läser åtgärdsstatusen för domännamnens interna belastningsutjämnare.|
|/domainNames/internalLoadBalancers/read|Hämtar de interna belastningsutjämnarna.|
|/domainNames/internalLoadBalancers/write|Skapa en ny intern belastningsutjämning.|
|/domainNames/loadBalancedEndpointSets/operationStatuses/read|Läser åtgärdsstatusen för belastningsutjämnade slutpunktsuppsättningar för domännamn.|
|/domainNames/loadBalancedEndpointSets/read|Visar de belastningsutjämnade slutpunktsuppsättningarna|
|/domainNames/read|Returnera resursernas domännamn.|
|/domainNames/serviceCertificates/delete|Ta bort de tjänstcertifikat som används.|
|/domainNames/serviceCertificates/operationStatuses/read|Läser åtgärdsstatus för tjänstcertifikat för domännamn.|
|/domainNames/serviceCertificates/read|Returnerar de tjänstcertifikat som används.|
|/domainNames/serviceCertificates/write|Lägg till eller ändra de tjänstcertifikat som används.|
|/domainNames/slots/delete|Tar bort en given distributionsplats.|
|/domainNames/slots/operationStatuses/read|Läser domännamnsplatsernas åtgärdsstatus.|
|/domainNames/slots/read|Visar distributionsplatserna.|
|/domainNames/slots/roles/extensionReferences/delete|Ta bort tilläggsreferensen för distributionsplatsrollen.|
|/domainNames/slots/roles/extensionReferences/operationStatuses/read|Läser åtgärdsstatus för rolltilläggsreferenser för domännamnsplatser.|
|/domainNames/slots/roles/extensionReferences/read|Returnerar tilläggsreferensen för distributionsplatsrollen.|
|/domainNames/slots/roles/extensionReferences/write|Lägg till eller ändra tilläggsreferensen för distributionsplatsrollen.|
|/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/read|Hämta diagnostikinställningarna.|
|/domainNames/slots/roles/providers/Microsoft.Insights/diagnosticSettings/write|Lägg till eller ändra diagnostikinställningarna.|
|/domainNames/slots/roles/providers/Microsoft.Insights/metricDefinitions/read|Hämtar måttdefinitioner.|
|/domainNames/slots/roles/read|Hämta distributionsplatsens roll.|
|/domainNames/slots/roles/roleInstances/operationStatuses/read|Läser åtgärdsstatus för rollinstanser för domännamnsplatsroller.|
|/domainNames/slots/roles/roleInstances/read|Hämta rollinstansen.|
|/domainNames/slots/roles/roleInstances/rebuild/action|Återskapar rollinstansen.|
|/domainNames/slots/roles/roleInstances/reimage/action|Återställer avbildningen för rollinstansen.|
|/domainNames/slots/roles/roleInstances/restart/action|Uppgradera domän|
|/domainNames/slots/start/action|Startar en distributionsplats.|
|/domainNames/slots/state/start/write|Ändrar distributionsplatsens status till stoppad.|
|/domainNames/slots/state/stop/write|Ändrar distributionsplatsens status till startad.|
|/domainNames/slots/stop/action|Göra uppehåll för distributionsplatserna.|
|/domainNames/slots/upgradeDomain/write|Gå igenom uppgraderingsdomän.|
|/domainNames/slots/write|Skapar eller uppdaterar distributionen.|
|/domainNames/swap/action|Byta mellanlagringsplatsen till produktionsplatsen.|
|/ domainNames/skrivning|Lägg till eller ändra resursernas domännamn.|
|/ moveSubscriptionResources/åtgärd|Flytta alla klassiska resurser till en annan prenumeration.|
|/operatingSystemFamilies/read|Listar de gästoperativsystem som är tillgängliga i Microsoft Azure, och listar även de operativsystemsversioner som är tillgängliga för respektive familj.|
|/operatingSystems/read|Listar de versioner av gästoperativsystemen som för tillfället är tillgängliga i Microsoft Azure.|
|/Quotas/Read|Hämta prenumerationens kvot.|
|registrera/åtgärd|Registrera för klassisk beräkning|
|/resourceTypes/skus/Read|Hämtar SKU-listan för de resurstyper som stöds.|
|/validateSubscriptionMoveAvailability/action|Verifiera prenumerationens tillgänglighet för klassisk flyttåtgärd.|
|/virtualMachines/associatedNetworkSecurityGroups/delete|Tar bort nätverkssäkerhetsgruppen som är kopplad till den virtuella datorn.|
|/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read|Läser åtgärdsstatus för nätverkssäkerhetsgrupp kopplad till virtuella datorer.|
|/virtualMachines/associatedNetworkSecurityGroups/read|Hämtar nätverkssäkerhetsgruppen som är kopplad till den virtuella datorn.|
|/virtualMachines/associatedNetworkSecurityGroups/write|Lägger till en nätverkssäkerhetsgrupp som är kopplad till den virtuella datorn.|
|/virtualMachines/asyncOperations/read|Hämta möjliga asynkrona åtgärder|
|/virtualMachines/attachDisk/Action|Bifogar en datadisk till den virtuella datorn.|
|/virtualMachines/delete|Tar bort virtuella datorer.|
|/virtualMachines/detachDisk/Action|Tar bort en datadisk från den virtuella datorn.|
|/virtualMachines/Disks/Read|Hämtar en lista över datadiskar|
|/virtualMachines/downloadRemoteDesktopConnectionFile/Action|Hämtar RDP-filen för den virtuella datorn.|
|/virtualMachines/extensions/operationStatuses/read|Läser åtgärdsstatus för tillägg för virtuella datorer.|
|/virtualMachines/Extensions/Read|Hämtar tillägget för virtuell dator.|
|/virtualMachines/Extensions/Write|Placerar tillägget för virtuell dator.|
|/virtualMachines/Metrics/Read|Hämtar måtten.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete|Tar bort nätverkssäkerhetsgruppen som är kopplad till nätverksgränssnittet.|
|/ associatedNetworkSecurityGroups-virtualMachines/networkInterfaces/operationStatuses/läsning|Läser åtgärdsstatus för nätverkssäkerhetsgrupp kopplad till virtuella datorer.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read|Hämtar nätverkssäkerhetsgruppen som är kopplad till nätverksgränssnittet.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write|Lägger till en nätverkssäkerhetsgrupp som är kopplad till nätverksgränssnittet.|
|/virtualMachines/operationStatuses/read|Läser åtgärdsstatus för virtuella datorer.|
|/virtualMachines/performMaintenance/Action|Utför underhåll av den virtuella datorn.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read|Hämta diagnostikinställningarna.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write|Lägg till eller ändra diagnostikinställningarna.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|Hämtar måttdefinitioner.|
|/virtualMachines/Read|Hämtar en lista över virtuella datorer.|
|/virtualMachines/redeploy/Action|Distribuerar om den virtuella datorn.|
|/virtualMachines/restart/Action|Startar om virtuella datorer.|
|/virtualMachines/Shutdown/Action|Stänga av den virtuella datorn.|
|/virtualMachines/Start/Action|Startar den virtuella datorn.|
|/virtualMachines/stop/Action|Stoppar den virtuella datorn.|
|/virtualMachines/write|Lägg till eller ändra virtuella datorer.|

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

| Åtgärd | Beskrivning |
|---|---|
|/gatewaySupportedDevices/read|Hämtar listan över enheter som stöds.|
|/networkSecurityGroups/delete|Tar bort nätverkssäkerhetsgruppen.|
|/networkSecurityGroups/operationStatuses/read|Läser åtgärdsstatus för nätverkssäkerhetsgrupp.|
|/networkSecurityGroups/read|Hämtar nätverkssäkerhetsgruppen.|
|/networkSecurityGroups/securityRules/delete|Tar bort säkerhetsregeln.|
|/networkSecurityGroups/securityRules/operationStatuses/read|Läser åtgärdsstatus för säkerhetsroller för nätverkssäkerhetsgrupp.|
|/networkSecurityGroups/securityRules/read|Hämtar säkerhetsregeln.|
|/networkSecurityGroups/securityRules/write|Lägger till eller uppdaterar en säkerhetsregel.|
|/networkSecurityGroups/write|Lägger till en ny nätverkssäkerhetsgrupp.|
|/Quotas/Read|Hämta prenumerationens kvot.|
|registrera/åtgärd|Registrera för klassiskt nätverk|
|/reservedIps/delete|Ta bort en reserverad IP.|
|/reservedIps/join/action|Anslut till en reserverad IP|
|/reservedIps/link/action|Länka en reserverad IP|
|/reservedIps/operationStatuses/read|Läser åtgärdsstatus för reserverade IP:ar.|
|/reservedIps/read|Hämtar reserverade IP-adresser|
|/reservedIps/write|Lägga till en ny reserverad IP|
|/virtualNetworks/capabilities/read|Visar funktionerna|
|/virtualNetworks/checkIPAddressAvailability/action|Kontrollerar tillgängligheten för en given IP-adress i ett virtuellt nätverk.|
|/virtualNetworks/delete|Tar bort det virtuella nätverket.|
|/virtualNetworks/gateways/clientRevokedCertificates/delete|Upphäver återkallningen av ett klientcertifikat.|
|/virtualNetworks/gateways/clientRevokedCertificates/read|Läs de återkallade klientcertifikaten.|
|/virtualNetworks/gateways/clientRevokedCertificates/write|Återkallar ett klientcertifikat.|
|/virtualNetworks/gateways/clientRootCertificates/delete|Tar bort klientcertifikatet för virtuell nätverksgateway.|
|/virtualNetworks/gateways/clientRootCertificates/download/action|Hämtar certifikat efter tumavtryck.|
|/virtualNetworks/gateways/clientRootCertificates/listPackage/action|Listar gatewaycertifikatspaketet för virtuella nätverk.|
|/virtualNetworks/gateways/clientRootCertificates/read|Sök efter klientrotscertifikaten.|
|/virtualNetworks/gateways/clientRootCertificates/write|Laddar upp ett nytt klientrotscertifikat.|
|/virtualNetworks/gateways/connections/connect/action|Ansluter en plats-till-plats-gatewayanslutning.|
|/virtualNetworks/gateways/connections/disconnect/action|Koppla från en plats-till-plats-gatewayanslutning.|
|/virtualNetworks/gateways/connections/read|Hämtar listan över anslutningar.|
|/virtualNetworks/gateways/connections/test/action|Testar en plats-till-plats-gatewayanslutning.|
|/virtualNetworks/gateways/delete|Tar bort den virtuella nätverks-gatewayen.|
|/virtualNetworks/gateways/downloadDeviceConfigurationScript/Action|Hämtar konfigurationsskriptet för enhet.|
|/virtualNetworks/gateways/downloadDiagnostics/Action|Hämtar gateway-diagnostiken.|
|/virtualNetworks/gateways/listCircuitServiceKey/action|Hämtar kretstjänstnyckeln.|
|/virtualNetworks/gateways/listPackage/Action|Listar gateway-paketet för virtuella nätverk.|
|/virtualNetworks/gateways/operationStatuses/read|Läser åtgärdsstatus för virtuella nätverksgatewayar.|
|/virtualNetworks/gateways/packages/read|Hämtar gateway-paketet för virtuella nätverk.|
|/virtualNetworks/gateways/read|Hämtar de virtuella nätverks-gatewayerna.|
|/virtualNetworks/gateways/startDiagnostics/action|Startar diagnostiken för den virtuella nätverks-gatewayen.|
|/virtualNetworks/gateways/stopDiagnostics/action|Stoppar diagnostiken för den virtuella nätverks-gatewayen.|
|/virtualNetworks/gateways/Write|Lägger till en virtuell nätverks-gateway.|
|/virtualNetworks/JOIN/Action|Anslut till det virtuella nätverket.|
|/virtualNetworks/operationStatuses/read|Läser åtgärdsstatus för virtuella nätverk.|
|/virtualNetworks/peer/Action|Peerar ett virtuellt nätverk med ett annat virtuellt nätverk.|
|/virtualNetworks/Read|Hämtar det virtuella nätverket.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete|Tar bort nätverkssäkerhetsgruppen som är kopplad till undernätet.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read|Läser åtgärdsstatus för nätverkssäkerhetsgrupp kopplad till virtuella nätverksundernät.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/read|Hämtar nätverkssäkerhetsgruppen som är kopplad till undernätet.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/write|Lägger till en nätverkssäkerhetsgrupp som är kopplad till undernätet.|
|/ virtualNetworks/skrivning|Lägg till ett nytt virtuellt nätverk.|

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

| Åtgärd | Beskrivning |
|---|---|
|/Capabilities/Read|Visar funktionerna|
|/checkStorageAccountAvailability/action|Kontrollerar tillgängligheten för ett lagringskonto.|
|/Disks/Read|Returnerar lagringskontodisken.|
|/Images/Read|Returnerar bilden.|
|/osImages/read|Returnerar operativsystemsavbildningen.|
|/publicImages/Read|Hämtar den offentliga avbildningen av den virtuella datorn.|
|/Quotas/Read|Hämta prenumerationens kvot.|
|registrera/åtgärd|Registrera för klassisk lagring|
|/storageAccounts/delete|Ta bort lagringskontot.|
|/storageAccounts/disks/delete|Tar bort angiven lagringskontodisk.|
|/storageAccounts/Disks/operationStatuses/Read|Läser resursens åtgärdsstatus.|
|/storageAccounts/Disks/Read|Returnerar lagringskontodisken.|
|/storageAccounts/Disks/Write|Lägger till en lagringskontodisk.|
|/storageAccounts/images/delete|Tar bort en given lagringskontoavbildning.|
|/storageAccounts/images/read|Returnerar lagringskontoavbildningen.|
|/storageAccounts/listKeys/action|Listar lagringskontots åtkomstnycklar.|
|/storageAccounts/operationStatuses/read|Läser resursens åtgärdsstatus.|
|/storageAccounts/osImages/delete|Tar bort en given operativsystemsavbildning för lagringskonto.|
|/storageAccounts/osImages/read|Returnerar operativsystemsavbildningen för lagringskonto.|
|/storageAccounts/Read|Returnerar lagringskontot med det givna kontot.|
|/storageAccounts/regenerateKey/action|Återskapar befintliga åtkomstnycklar för lagringskontot.|
|/storageAccounts/services/diagnosticSettings/read|Hämta diagnostikinställningarna.|
|/storageAccounts/services/diagnosticSettings/write|Lägg till eller ändra diagnostikinställningarna.|
|/storageAccounts/services/metricDefinitions/read|Hämtar måttdefinitioner.|
|/storageAccounts/services/metrics/read|Hämtar måtten.|
|/storageAccounts/services/read|Hämta de tjänster som är tillgängliga.|
|/ storageAccounts/skrivning|Lägger till ett nytt lagringskonto.|

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

| Åtgärd | Beskrivning |
|---|---|
|/Accounts/delete|Tar bort API-konton|
|/Accounts/listKeys/Action|Visa nyckellista|
|/Accounts/providers/Microsoft.Insights/diagnosticSettings/Read|Hämta diagnostikinställningen för resursen.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningen för resursen.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för Cognitive Services.|
|/Accounts/Read|Läser API-konton.|
|/Accounts/regenerateKey/Action|Återskapar nyckel|
|/Accounts/skus/Read|Läser tillgängliga SKU:er för en befintlig resurs.|
|/Accounts/usages/Read|Hämta kvotanvändning för en befintlig resurs.|
|/ konton/skrivning|Skriver API-konton.|
|/Locations/checkSkuAvailability/Action|Läser tillgängliga SKU:er för en prenumeration.|
|/ Operations/läsning|Visa en lista över alla tillgängliga åtgärder|
|registrera/åtgärd|Registrerar prenumeration för kognitiva tjänster|
|/skus/Read|Läser tillgängliga SKU: er för kognitiva tjänster.|

## <a name="microsoftcommerce"></a>Microsoft.Commerce

| Åtgärd | Beskrivning |
|---|---|
|/ RateCard/läsning|Returnerar erbjuder data, resurs-mätare metadata och priser för den angivna prenumerationen.|
|/UsageAggregates/read|Hämtar Microsoft Azure-förbrukningen av en prenumeration. Resultatet innehåller mängder användningsdata, prenumeration och resurs relaterad information, på ett angivet tidsintervall.|

## <a name="microsoftcompute"></a>Microsoft.Compute

| Åtgärd | Beskrivning |
|---|---|
|/availabilitySets/delete|Tar bort tillgänglighetsuppsättningen|
|/availabilitySets/Read|Hämta egenskaperna för en tillgänglighetsuppsättning|
|/availabilitySets/vmSizes/read|Visar en lista över tillgängliga storlekar för att skapa eller uppdatera en virtuell dator i tillgänglighetsuppsättningen|
|/ availabilitySets/skrivning|Skapar en ny tillgänglighetsuppsättning eller uppdaterar en befintlig|
|/disks/beginGetAccess/action|Hämta diskens SAS-URI för blobåtkomst|
|/Disks/delete|Tar bort disken|
|/disks/endGetAccess/action|Återkalla diskens SAS-URI|
|/Disks/Read|Hämta egenskaperna för en disk|
|/ diskar/skrivning|Skapar en ny disk eller uppdaterar en befintlig|
|/images/delete|Avbildningen tas bort|
|/Images/Read|Hämta egenskaperna för avbildningen|
|/ bilder/skrivning|Skapar en ny avbildning eller uppdaterar en befintlig|
|/Locations/capsOperations/Read|Hämtar status för en asynkron åtgärd Caps|
|/Locations/diskOperations/Read|Hämtar status för en asynkron diskåtgärd|
|/Locations/Operations/Read|Hämtar status för en asynkron åtgärd|
|/Locations/Publishers/artifacttypes/OFFERS/Read|Hämta egenskaperna för en plattform avbildningen erbjuder|
|/Locations/Publishers/artifacttypes/OFFERS/skus/Read|Hämta egenskaperna för en plattform avbildningen Sku|
|/Locations/Publishers/artifacttypes/OFFERS/skus/versions/Read|Hämta egenskaperna för en plattform Avbildningsversion|
|/Locations/Publishers/artifacttypes/types/Read|Hämta egenskaperna för en VMExtension-typ.|
|/Locations/Publishers/artifacttypes/types/versions/Read|Hämta egenskaperna för en VMExtension Version|
|/Locations/Publishers/Read|Hämta egenskaperna för en utgivare|
|/Locations/runCommands/Read|Listar tillgängliga körningskommandon på platsen|
|/Locations/usages/Read|Hämtar gränser för antalet tjänster och aktuell användning för prenumerationens beräkningsresurser på en plats|
|/Locations/vmSizes/Read|Visar en lista över tillgängliga storlekar för virtuella datorer på en plats|
|/Operations/Read|Visar en lista över åtgärder som är tillgängliga på Microsoft.Compute-resursprovidern|
|registrera/åtgärd|Registrerar prenumeration med Microsoft.Compute-resursprovidern|
|/restorePointCollections/delete|Tar bort samlingen med återställningspunkter och återställningspunkter den innehåller|
|/restorePointCollections/read|Hämta egenskaperna för en samling med återställningspunkter|
|/restorePointCollections/restorePoints/delete|Tar bort återställningspunkten|
|/restorePointCollections/restorePoints/read|Hämta egenskaperna för en återställningspunkt|
|/restorePointCollections/restorePoints/retrieveSasUris/action|Hämta egenskaperna för en återställningspunkt tillsammans med blob-SAS-URI:er|
|/restorePointCollections/restorePoints/write|Skapar en ny återställningspunkt|
|/restorePointCollections/write|Skapar en ny samling med återställningspunkter eller uppdaterar en befintlig|
|/sharedVMImages/delete|Tar bort SharedVMImage|
|/sharedVMImages/Read|Hämta egenskaperna för en SharedVMImage|
|/sharedVMImages/versions/delete|Ta bort en SharedVMImageVersion|
|/sharedVMImages/versions/read|Hämta egenskaperna för en SharedVMImageVersion|
|/sharedVMImages/versions/replicate/action|Replikera en SharedVMImageVersion ska målregioner|
|/sharedVMImages/versions/write|Skapa en ny SharedVMImageVersion eller uppdatera en befintlig|
|/sharedVMImages/write|Skapar en ny SharedVMImage eller uppdaterar en befintlig|
|/skus/Read|Hämtar listan över Microsoft.Compute SKU: er som tillgänglig för din prenumeration|
|/snapshots/beginGetAccess/action|Hämta ögonblicksbilden SAS-URI för blobåtkomst|
|/snapshots/delete|Ta bort en ögonblicksbild|
|/snapshots/endGetAccess/action|Återkalla ögonblicksbilden SAS-URI|
|/Snapshots/Read|Hämta egenskaperna för en ögonblicksbild|
|/ ögonblicksbilder/skrivning|Skapa en ny ögonblicksbild eller uppdatera en befintlig|
|/virtualMachines/Capture/Action|Avbildar den virtuella datorn genom att kopiera hårddiskar, och genererar en mall som kan användas för att skapa liknande virtuella datorer|
|/virtualMachines/convertToManagedDisks/action|Konverterar den virtuella datorns blobbaserade diskar till hanterade diskar|
|/virtualMachines/deallocate/Action|Stänger av den virtuella datorn och frigör beräkningsresurser|
|/virtualMachines/delete|Tar bort den virtuella datorn|
|/virtualMachines/Extensions/delete|Tar bort tillägget för virtuell dator|
|/virtualMachines/Extensions/Read|Hämta egenskaperna för ett tillägg för virtuell dator|
|/virtualMachines/Extensions/Write|Skapar ett nytt tillägg för virtuell dator eller uppdaterar ett befintligt|
|/virtualMachines/generalize/Action|Ställer in statusen för den virtuella datorn på Generaliserad och förbereder den virtuella datorn för avbildning|
|/virtualMachines/instanceView/Read|Hämtar detaljerad körningsstatus för den virtuella datorn och dess resurser|
|/virtualMachines/performMaintenance/Action|Utför underhållsåtgärd på den virtuella datorn.|
|/virtualMachines/powerOff/Action|Stänger av den virtuella datorn. Observera att den virtuella datorn kommer att fortsätta debiteras.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|Läser måttdefinitioner för den virtuella datorn|
|/virtualMachines/Read|Hämta egenskaperna för en virtuell dator|
|/virtualMachines/redeploy/Action|Distribuerar om virtuell dator|
|/virtualMachines/reimage/action|Avbildningen av den virtuella dator som använder differentieringsdisk återställs.|
|/virtualMachines/restart/Action|Startar om den virtuella datorn|
|/virtualMachines/runCommand/action|Kör ett fördefinierat skript på den virtuella datorn|
|/virtualMachines/Start/Action|Startar den virtuella datorn|
|/virtualMachines/vmSizes/read|Visar en lista över tillgängliga storlekar som de virtuella datorerna kan uppdateras till|
|/virtualMachines/write|Skapar en ny virtuell dator eller uppdaterar en befintlig|
|/virtualMachineScaleSets/deallocate/Action|Stänger av och startar om beräkningsresurserna för VM-skalningsuppsättningens instanser |
|/virtualMachineScaleSets/delete|Tar bort VM-skalningsuppsättningen|
|/virtualMachineScaleSets/delete/action|Tar bort VM-skalningsuppsättningens instanser|
|/virtualMachineScaleSets/extensions/delete|Hämta tillägg för VM-skalningsuppsättningen|
|/virtualMachineScaleSets/extensions/read|Hämtar egenskaperna för ett tillägg till VM-skalningsuppsättningen|
|/virtualMachineScaleSets/extensions/write|Skapar ett nytt tillägg för VM-skalningsuppsättningen eller uppdaterar ett befintligt|
|/virtualMachineScaleSets/forceRecoveryServiceFabricPlatformUpdateDomainWalk/action|Beskriver platform update domäner av ett service fabric Skaluppsättning för virtuell dator att slutföra en väntande uppdatering som har fastnat manuellt|
|/virtualMachineScaleSets/instanceView/read|Hämtar instansvyn för VM-skalningsuppsättningen|
|/virtualMachineScaleSets/manualUpgrade/action|Uppdaterar manuellt instanser av den senaste modellen av VM-skalningsuppsättningen|
|/virtualMachineScaleSets/networkInterfaces/read|Hämta egenskaper för alla nätverksgränssnitt i en Skaluppsättning virtuell dator|
|/virtualMachineScaleSets/osUpgradeHistory/read|Hämtar historiken för OS-uppgraderingar för en virtuell dator Skaluppsättning|
|/virtualMachineScaleSets/performMaintenance/action|Genomför planerat underhåll på instanser av Skalningsuppsättning i virtuell dator|
|/virtualMachineScaleSets/powerOff/action|Stänger av VM-skalningsuppsättningens instanser|
|/virtualMachineScaleSets/providers/Microsoft.Insights/metricDefinitions/read|Läser måttdefinitioner för den virtuella datorns skalningsuppsättning|
|/virtualMachineScaleSets/publicIPAddresses/read|Hämta egenskaper för alla offentliga IP-adresser för en virtuell dator Skaluppsättning|
|/virtualMachineScaleSets/read|Hämta egenskaperna för en VM-skalningsuppsättning|
|/virtualMachineScaleSets/redeploy/Action|Distribuera instanser av Skalningsuppsättning i virtuell dator|
|/virtualMachineScaleSets/reimage/action|Skapar ny avbildning av VM-skalningsuppsättningens instanser|
|/virtualMachineScaleSets/restart/action|Startar om instanserna för VM-skalningsuppsättningen|
|/virtualMachineScaleSets/rollingUpgrades/cancel/action|Avbryter samlad uppgradering av en VM-skalningsuppsättning|
|/virtualMachineScaleSets/rollingUpgrades/read|Hämta senaste status för den samlade uppgraderingen för VM-skalningsuppsättningen|
|/virtualMachineScaleSets/Scale/Action|Kontrollera om en befintlig VM-skalningsuppsättning kan skala in/ut till det angivna antalet instanser|
|/virtualMachineScaleSets/skus/read|Visar en lista över giltiga SKU-koder för en befintlig VM-skalningsuppsättning|
|/virtualMachineScaleSets/start/action|Startar VM-skalningsuppsättningens instanser|
|/virtualMachineScaleSets/virtualMachines/deallocate/action|Stänger av och frigör beräkningsresurserna för en virtuell dator i en skaluppsättning.|
|/virtualMachineScaleSets/virtualMachines/delete|Ta bort en viss virtuell dator i en skaluppsättning.|
|/virtualMachineScaleSets/virtualMachines/instanceView/read|Hämtar instansvyn för en virtuell dator i en skaluppsättning för virtuell dator.|
|/ networkInterfaces-virtualMachineScaleSets/virtualMachines/ipConfigurations/publicIPAddresses/läsning|Hämta egenskaper för offentliga IP-adressen som skapats med hjälp av Skaluppsättning för virtuell dator. Skaluppsättning för virtuell dator kan skapa högst en offentlig IP per ipconfiguration (privat IP)|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/ipConfigurations/read|Hämta egenskaperna för en eller alla IP-konfigurationer för ett nätverksgränssnitt som skapats med hjälp av Skaluppsättning för virtuell dator. IP-konfigurationer representerar privata IP-adresser|
|/virtualMachineScaleSets/virtualMachines/networkInterfaces/read|Hämta egenskaperna för en eller alla nätverksgränssnitt för en virtuell dator som skapats med hjälp av Skaluppsättning för virtuell dator|
|/virtualMachineScaleSets/virtualMachines/performMaintenance/action|Genomför planerat underhåll på en virtuell datorinstans i en virtuell dator Skaluppsättning|
|/virtualMachineScaleSets/virtualMachines/powerOff/action|Stänger av en virtuell datorinstans i en skaluppsättning för virtuell dator.|
|/virtualMachineScaleSets/virtualMachines/providers/ Microsoft.Insights/metricDefinitions/read|Läser måttdefinitioner för den virtuella datorn i skalningsuppsättning|
|/virtualMachineScaleSets/virtualMachines/read|Hämtar egenskaperna för en virtuell dator i en skaluppsättning för virtuell dator|
|/virtualMachineScaleSets/virtualMachines/redeploy/action|Återdistribuerar en virtuell datorinstans i en virtuell dator Skaluppsättning|
|/virtualMachineScaleSets/virtualMachines/reimage/action|Skapar ny avbildning av virtuell datorinstans i en VM-skalningsuppsättning.|
|/virtualMachineScaleSets/virtualMachines/restart/action|Startar om en virtuell datorinstans i en skaluppsättning för virtuell dator.|
|/virtualMachineScaleSets/virtualMachines/start/action|Startar en virtuell datorinstans i en skaluppsättning för virtuell dator.|
|/virtualMachineScaleSets/virtualMachines/write|Uppdaterar egenskaperna för en virtuell dator i en VM-skalningsuppsättning|
|/virtualMachineScaleSets/write|Skapar en ny VM-skalningsuppsättning eller uppdaterar en befintlig|

## <a name="microsoftconsumption"></a>Microsoft.Consumption

| Åtgärd | Beskrivning |
|---|---|
|/balances/Read|Lista över belastningen sammanfattning för faktureringsperioden för en hanteringsgrupp.|
|/budgets/Read|Lista över budgetar genom en prenumeration eller en hanteringsgrupp.|
|/ budgetar/skrivning|Skapa, uppdatera och ta bort budgetar genom en prenumeration eller en hanteringsgrupp.|
|/marketplaces/Read|Visa användningsinformation för ett omfång för prenumerationer EA och WebDirect marketplace resurs.|
|/Operations/Read|Lista över de åtgärder som alla stöds av Microsoft.Consumption resursprovidern.|
|/pricesheets/Read|Visa en lista över Pricesheets-data för en prenumeration eller en hanteringsgrupp.|
|/reservationDetails/Read|Visa information om diskanvändning för reserverade instanser av reservation ordning eller hantering av grupper. Information om data är per instans per dag nivå.|
|/reservationSummaries/read|Lista över belastningen sammanfattning för reserverade instanser av reservation ordning eller hantering av grupper. Sammanfattningsdata är antingen på månatliga eller daglig nivå.|
|/reservationTransactions/read|Visa transaktionshistoriken för reserverade instanser av hanteringsgrupper.|
|/Terms/Read|Ange villkoren för en prenumeration eller en hanteringsgrupp.|
|/usageDetails/Read|Visa användningsinformation för ett omfång för prenumerationer EA och WebDirect.|

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

| Åtgärd | Beskrivning |
|---|---|
|/containerGroups/containers/logs/read|Hämta loggar för en särskild behållare.|
|/containerGroups/delete|Tar bort den specifika behållargruppen.|
|/containerGroups/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar diagnostikinställningen för behållargruppen.|
|/containerGroups/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningen för behållargruppen.|
|/containerGroups/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för behållargruppen.|
|/containerGroups/read|Hämta alla behållargrupper.|
|/containerGroups/write|Skapa eller uppdatera en särskild behållargrupp.|

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

| Åtgärd | Beskrivning |
|---|---|
|/checkNameAvailability/Read|Kontrollerar om registret behållarnamn är tillgängliga för användning.|
|/Locations/operationResults/Read|Hämtar en asynkron resultat|
|/Operations/Read|Visar en lista över alla tillgängliga Azure Container registret REST API-åtgärder|
|registrera/åtgärd|Registrerar prenumerationen för resursprovidern behållare registret och kan skapa behållaren register.|
|/registries/delete|Tar bort ett register för behållaren.|
|/registries/eventGridFilters/delete|Tar bort ett rutnät händelsefilter registret är behållare.|
|/registries/eventGridFilters/read|Hämtar egenskaperna för det angivna händelsen rutnätet filtret eller listar alla händelse rutnätet filter för den angivna behållaren registernyckeln.|
|/registries/eventGridFilters/write|Skapar eller uppdaterar ett händelsefilter rutnät för en behållare registret med de angivna parametrarna.|
|/registries/listCredentials/Action|Visar inloggningsuppgifterna för det angivna behållaren registret.|
|/registries/listUsages/Read|Visar kvoten användningsområden för den angivna behållaren registernyckeln.|
|/registries/operationStatuses/read|Hämtar status registret asynkron åtgärd|
|/registries/Read|Hämtar egenskaperna för det angivna behållaren registret eller listar alla behållare register under den angivna resursgrupp eller prenumeration.|
|/registries/regenerateCredential/action|Återskapar en inloggningsuppgifterna för det angivna behållaren registret.|
|/registries/replications/delete|Tar bort en replikering från en behållare registret.|
|/registries/replications/operationStatuses/read|Hämtar en replikeringsstatus asynkron åtgärd|
|/registries/replications/Read|Hämtar egenskaperna för den angivna replikeringen eller listar alla replikeringar för den angivna behållaren registernyckeln.|
|/registries/replications/Write|Skapar eller uppdaterar en replikering för en behållare registret med de angivna parametrarna.|
|/registries/webhooks/delete|Tar bort en webhook från en behållare registret.|
|/registries/webhooks/getCallbackConfig/action|Hämtar konfigurationen för tjänsten URI och anpassade huvuden för webhooken.|
|/registries/webhooks/listEvents/action|Visar senaste händelser för den angivna webhooken.|
|/registries/webhooks/operationStatuses/read|Hämtar en webhook asynkrona Åtgärdsstatus|
|/registries/webhooks/ping/action|Utlöser en ping-händelse skickas till webhooken.|
|/registries/webhooks/read|Hämtar egenskaperna för den angivna webhooken eller listar alla webhooks för den angivna behållaren registernyckeln.|
|/registries/webhooks/write|Skapar eller uppdaterar en webhook för en behållare registret med de angivna parametrarna.|
|/ register/skrivning|Skapar eller uppdaterar en behållare registret med de angivna parametrarna.|

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

| Åtgärd | Beskrivning |
|---|---|
|/containerServices/delete|Tar bort den angivna Behållartjänsten|
|/containerServices/read|Hämtar den angivna Behållartjänsten|
|/containerServices/write|Placerar eller uppdaterar den angivna Behållartjänsten|

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

| Åtgärd | Beskrivning |
|---|---|
|/Applications/delete|Borttagningsåtgärd|
|/applications/listSecrets/action|Lista hemligheter|
|/applications/listSingleSignOnToken/action|Läsa token för enkel inloggning|
|/Applications/Read|Läsåtgärd|
|/ program/skrivning|Skrivåtgärd|
|/ program/skrivning|Skrivåtgärd|
|/ listCommunicationPreference/åtgärd|Lista över inställningar för kommunikation|
|/Operations/Read|Läs-och skrivåtgärder|
|/updateCommunicationPreference/action|Uppdatera inställningar för kommunikation|

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

| Åtgärd | Beskrivning |
|---|---|
|/hubs/adobemetadata/Action|Skapa eller uppdatera alla Azure-kund insikter Adobe Metadata|
|/hubs/adobemetadata/read|Läsa alla Azure-kund insikter Adobe Metadata|
|/hubs/authorizationPolicies/delete|Ta bort princip för delad åtkomst-signatur alla Azure-kund insikter|
|/hubs/authorizationPolicies/Read|Läsa några Azure kunden insikter delad åtkomstprincip för signatur|
|/hubs/authorizationPolicies/regeneratePrimaryKey/action|Återskapa en primär nyckel för Azure kunden insikter delad signatur åtkomstprincip|
|/hubs/authorizationPolicies/regenerateSecondaryKey/action|Återskapa en sekundär nyckel för Azure kunden insikter delad signatur åtkomstprincip|
|/hubs/authorizationPolicies/Write|Skapa eller uppdatera princip för delad åtkomst-signatur alla Azure-kund insikter|
|/hubs/Connectors/Activate/Action|Aktivera alla Azure kunden Insights Connector|
|/hubs/Connectors/Activate/Action|Aktivera alla Azure kunden Insights Connector|
|/hubs/Connectors/delete|Ta bort alla Azure kunden Insights Connector|
|/hubs/connectors/getruntimestatus/action|Hämta alla Azure kunden Insights Connector Körningsstatus|
|/hubs/Connectors/mappings/Activate/Action|Aktivera alla Azure kunden Insights Connector mappning|
|/hubs/connectors/mappings/delete|Ta bort alla Azure kunden Insights Connector mappning|
|/hubs/connectors/mappings/operations/read|Läs valfritt Åtgärdsresultat Azure kunden Insights Connector mappning|
|/hubs/Connectors/mappings/Read|Läsa alla Azure kunden Insights Connector mappning|
|/hubs/connectors/mappings/write|Skapa eller uppdatera alla Azure kunden Insights Connector mappning|
|/hubs/Connectors/Operations/Read|Läs valfritt Åtgärdsresultat Azure kunden Insights Connector|
|/hubs/Connectors/Read|Läsa alla Azure kunden Insights Connector|
|/hubs/Connectors/saveauthinfo/Action|Skapa eller uppdatera alla Azure kunden Insights Connector autentisering information om Gatewayanslutningen|
|/hubs/Connectors/Update/action|Uppdatera alla Azure kunden Insights Connector|
|/hubs/Connectors/Write|Skapa eller uppdatera alla Azure kunden Insights Connector|
|/hubs/crmmetadata/Action|Skapa eller uppdatera alla Azure-kund insikter Crm Metadata|
|/hubs/crmmetadata/Read|Läsa alla Azure-kund insikter Crm Metadata|
|/hubs/delete|Ta bort alla Azure kunden Insights hubb|
|/hubs/gdpr/delete|Ta bort alla Azure-kund insikter BNPR|
|/hubs/gdpr/Read|Läsa alla Azure-kund insikter BNPR|
|/hubs/gdpr/Write|Skapa eller uppdatera alla Azure-kund insikter BNPR|
|/hubs/getbillingcredits/Read|Hämta Azure kunden Insights hubb fakturering krediter|
|/hubs/getbillinghistory/Read|Hämta Azure kunden Insights hubb fakturering historik|
|/hubs/images/delete|Ta bort alla Azure-kund insikter avbildningen|
|/hubs/images/Read|Läs valfri Azure kunden insikter bild|
|/hubs/images/Write|Skapa eller uppdatera någon Azure kunden insikter bild|
|/hubs/Interactions/delete|Ta bort alla azure kundernas insikter interaktioner|
|/hubs/Interactions/Operations/Read|Läs valfritt Åtgärdsresultat Azure kundinteraktion insikter|
|/hubs/Interactions/Read|Läsa alla Azure kundinteraktion insikter|
|/hubs/interactions/suggestrelationshiplinks/action|Föreslå relationen länkar för alla Azure kundernas insikter interaktioner|
|/hubs/Interactions/Write|Skapa eller uppdatera alla Azure kundinteraktion insikter|
|/hubs/KPI/delete|Ta bort alla Azure-kund insikter Key Performance Indicator|
|/hubs/KPI/Operations/Read|Läs valfritt Åtgärdsresultat Azure kunden insikter prestationsindikatorer|
|/hubs/KPI/Read|Läsa alla Azure-kund insikter Key Performance Indicator|
|/hubs/kpi/reprocess/action|Bearbeta alla Azure-kund insikter KPI: er|
|/hubs/kpi/write|Skapa eller uppdatera en Azure-kund insikter Key Performance Indicator|
|/hubs/Links/delete|Ta bort alla Azure-kund insikter länkar|
|/hubs/Links/Operations/Read|Läs valfritt Åtgärdsresultat Azure kunden insikter länkar|
|/hubs/Links/Read|Läsa alla Azure-kund insikter länkar|
|/hubs/Links/Write|Skapa eller uppdatera Azure kunden länkar|
|/hubs/msemetadata/Action|Skapa eller uppdatera alla Azure-kund insikter mus Metadata|
|/hubs/msemetadata/Read|Läsa alla Azure-kund insikter mus Metadata|
|/hubs/operationresults/Read|Hämta Azure kunden Insights hubb Åtgärdsresultat|
|/hubs/predictions/delete|Ta bort alla Azure-kund insikter förutsägelser|
|/hubs/predictions/Operations/Read|Läs valfritt Åtgärdsresultat Azure kunden insikter förutsägelser|
|/hubs/predictions/Read|Läsa alla Azure-kund insikter förutsägelser|
|/hubs/predictions/Write|Skapa eller uppdatera alla Azure-kund förutsägelser|
|/hubs/predictivematchpolicies/delete|Ta bort alla Azure-kund insikter förutsägande matchar principer|
|/hubs/predictivematchpolicies/Operations/Read|Läs valfritt Åtgärdsresultat Azure kunden insikter förutsägande matchar principer|
|/hubs/predictivematchpolicies/read|Läsa alla Azure-kund insikter förutsägande matchar principer|
|/hubs/predictivematchpolicies/write|Skapa eller uppdatera policyer Azure kunden insikter förutsägande matchning|
|/hubs/Profiles/delete|Ta bort alla Azure-kund insikter profil|
|/hubs/Profiles/Operations/Read|Läs valfritt Åtgärdsresultat Azure kunden insikter profil|
|/hubs/Profiles/Read|Läsa alla Azure-kund insikter profil|
|/hubs/Profiles/Write|Skriv valfri Azure kunden insikter profil|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/hubs/providers/Microsoft.Insights/logDefinitions/read|Hämtar de tillgängliga loggarna för resurs|
|/hubs/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för resurs|
|/hubs/Read|Läsa alla Azure kunden Insights hubb|
|/hubs/relationshiplinks/delete|Ta bort alla Azure-kund insikter relationen länkar|
|/hubs/relationshiplinks/Operations/Read|Läs valfritt Åtgärdsresultat Azure kunden insikter relationen länkar|
|/hubs/relationshiplinks/Read|Läsa alla Azure-kund insikter relationen länkar|
|/hubs/relationshiplinks/Write|Skapa eller uppdatera Azure kunden insikter relationen länkar|
|/hubs/Relationships/delete|Ta bort alla Azure-kund insikter relationer|
|/hubs/Relationships/Operations/Read|Läs valfritt Åtgärdsresultat Azure kunden insikter relationer|
|/hubs/Relationships/Read|Läsa alla Azure-kund insikter relationer|
|/hubs/Relationships/Write|Skapa eller uppdatera Azure kunden insikter relationer|
|/hubs/roleAssignments/delete|Ta bort alla Azure-kund insikter Rbac tilldelning|
|/hubs/roleAssignments/Operations/Read|Läs valfritt Åtgärdsresultat Azure kunden insikter Rbac tilldelning|
|/hubs/roleAssignments/Read|Läs Azure kunden insikter Rbac tilldelningar|
|/hubs/roleAssignments/Write|Skapa eller uppdatera Azure kunden insikter Rbac tilldelningar|
|/hubs/roles/Read|Läsa alla Azure-kund insikter Rbac-roller|
|/hubs/salesforcemetadata/Action|Skapa eller uppdatera alla Azure-kund insikter SalesForce-Metadata|
|/hubs/salesforcemetadata/Read|Läsa alla Azure-kund insikter SalesForce-Metadata|
|/hubs/segments/delete|Ta bort alla Azure kundsegment insikter|
|/hubs/segments/Dynamic/Action|Hantering av alla Azure-kund Insight dynamiska segment|
|/hubs/segments/Read|Läsa alla Azure kundsegment insikter|
|/hubs/segments/static/Action|Hantering av alla Azure-kund Insight statiska segment|
|/hubs/segments/Write|Skapa eller uppdatera alla Azure kundsegment insikter|
|/hubs/sqlconnectionstrings/delete|Ta bort alla Azure-kund insikter SqlConnectionStrings|
|/hubs/sqlconnectionstrings/read|Läsa alla Azure-kund insikter SqlConnectionStrings|
|/hubs/sqlconnectionstrings/write|Skapa eller uppdatera alla Azure-kund insikter SqlConnectionStrings|
|/hubs/suggesttypeschema/Action|Generera föreslår typen Schema från exempeldata|
|/hubs/tenantmanagement/Read|Hantera Azure kunden Insights hubbsinställningar|
|/hubs/Views/delete|Ta bort en Azure-kund insikter App vy|
|/hubs/Views/Read|Läs valfri Azure kunden insikter App vy|
|/hubs/Views/Write|Skapa eller uppdatera Azure kunden insikter App visa|
|/hubs/widgettypes/Read|Läs en Azure kunden insikter App Widget typer|
|/ NAV/skrivning|Skapa eller uppdatera alla Azure kunden Insights hubb|
|/Operations/Read|Läs Azure kunden Insights Api Metadatas|
|registrera/åtgärd|Registrerar prenumerationen för resursprovidern kunden insikter och kan skapa kunden Insights-resurser|
|/unregister/action|Avregistrerar prenumerationen för resursprovidern kunden insikter|

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

| Åtgärd | Beskrivning |
|---|---|
|/Catalogs/delete|Tar bort katalogen.|
|/Catalogs/Read|Visa egenskaper för katalogen eller katalogerna under prenumeration eller resursgrupp.|
|/ kataloger/skrivning|Katalogen skapas eller uppdateras taggar och egenskaper för katalogen.|
|/ checkNameAvailability/åtgärd|Kontrollerar tillgängligheten för katalog-namnet för klienten.|
|/Operations/Read|Visar en lista över åtgärder som är tillgängliga på Microsoft.DataCatalog resursprovidern.|
|registrera/åtgärd|Registrerar prenumeration med Microsoft.DataCatalog resursprovidern.|
|/unregister/action|Avregistrerar prenumeration från Microsoft.DataCatalog resursprovidern.|

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

| Åtgärd | Beskrivning |
|---|---|
|/datafactories/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/datafactories/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/datafactories/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för datafactories|
|/factories/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/factories/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/factories/providers/Microsoft.Insights/logDefinitions/read|Hämtar de tillgängliga loggarna för fabriker|
|/factories/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för fabriker|

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

| Åtgärd | Beskrivning |
|---|---|
|/accounts/computePolicies/delete|Ta bort en princip för beräkning.|
|/accounts/computePolicies/read|Hämta information om en princip för beräkning.|
|/accounts/computePolicies/write|Skapa eller uppdatera en princip för beräkning.|
|/accounts/dataLakeStoreAccounts/delete|Avlänka ett DataLakeStore konto från ett DataLakeAnalytics-konto.|
|/Accounts/dataLakeStoreAccounts/Read|Hämta information om ett länkade DataLakeStore-konto för DataLakeAnalytics-konto.|
|/Accounts/dataLakeStoreAccounts/Write|Skapa eller uppdatera en länkad DataLakeStore konto för DataLakeAnalytics-konto.|
|/Accounts/delete|Ta bort ett DataLakeAnalytics konto.|
|/Accounts/firewallRules/delete|Ta bort en brandväggsregel.|
|/Accounts/firewallRules/Read|Hämta information om en brandväggsregel.|
|/Accounts/firewallRules/Write|Skapa eller uppdatera en brandväggsregel.|
|/Accounts/operationResults/Read|Hämta resultatet av en DataLakeAnalytics konto.|
|/Accounts/providers/Microsoft.Insights/diagnosticSettings/Read|Hämta diagnostikinställningar för DataLakeAnalytics-kontot.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Skapa eller uppdatera diagnostikinställningar för DataLakeAnalytics-kontot.|
|/Accounts/providers/Microsoft.Insights/logDefinitions/Read|Hämta de tillgängliga loggarna för DataLakeAnalytics-konto.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Hämta tillgängliga mått för DataLakeAnalytics-konto.|
|/Accounts/Read|Hämta information om ett befintligt DataLakeAnalytics-konto.|
|/accounts/storageAccounts/Containers/listSasTokens/action|Lista över SAS-token för behållare för lagring av länkade lagringskonton för DataLakeAnalytics-konto.|
|/Accounts/storageAccounts/containers/Read|Hämta behållare av länkade lagringskonton för DataLakeAnalytics-konto.|
|/accounts/storageAccounts/delete|Avlänka ett lagringskonto från en DataLakeAnalytics-konto.|
|/Accounts/storageAccounts/Read|Hämta information om ett länkat lagringskonto för DataLakeAnalytics-konto.|
|/Accounts/storageAccounts/Write|Skapa eller uppdatera ett länkat lagringskonto för DataLakeAnalytics-konto.|
|/Accounts/TakeOwnerShip/Action|Bevilja behörighet att avbryta jobb som skickats av andra användare.|
|/ konton/skrivning|Skapa eller uppdatera ett DataLakeAnalytics konto.|
|/Locations/CAPABILITY/Read|Hämta Funktionsinformation om för en prenumeration angående med DataLakeAnalytics.|
|/Locations/checkNameAvailability/Action|Kontrollera tillgängligheten för ett DataLakeAnalytics kontonamn.|
|/Locations/operationResults/Read|Hämta resultatet av en DataLakeAnalytics konto.|
|/Operations/Read|Hämta tillgängliga åtgärder av DataLakeAnalytics.|
|registrera/åtgärd|Registrera prenumeration på DataLakeAnalytics.|

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

| Åtgärd | Beskrivning |
|---|---|
|/Accounts/delete|Ta bort ett DataLakeStore konto.|
|/Accounts/enableKeyVault/Action|Aktivera KeyVault för en DataLakeStore-konto.|
|/Accounts/firewallRules/delete|Ta bort en brandväggsregel.|
|/Accounts/firewallRules/Read|Hämta information om en brandväggsregel.|
|/Accounts/firewallRules/Write|Skapa eller uppdatera en brandväggsregel.|
|/Accounts/operationResults/Read|Hämta resultatet av en DataLakeStore konto.|
|/Accounts/providers/Microsoft.Insights/diagnosticSettings/Read|Hämta diagnostikinställningar för DataLakeStore-kontot.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Skapa eller uppdatera diagnostikinställningar för DataLakeStore-kontot.|
|/Accounts/providers/Microsoft.Insights/logDefinitions/Read|Hämta de tillgängliga loggarna för DataLakeStore-konto.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Hämta tillgängliga mått för DataLakeStore-konto.|
|/Accounts/Read|Hämta information om ett befintligt DataLakeStore-konto.|
|/Accounts/superuser/Action|Bevilja superanvändare på Data Lake Store när beviljas med Microsoft.Authorization/roleAssignments/write.|
|/accounts/trustedIdProviders/delete|Ta bort en betrodd identitetsleverantör.|
|/Accounts/trustedIdProviders/Read|Hämta information om en betrodd identitetsleverantör.|
|/accounts/trustedIdProviders/write|Skapa eller uppdatera en betrodd identitetsleverantör.|
|/ konton/skrivning|Skapa eller uppdatera ett DataLakeStore konto.|
|/Locations/CAPABILITY/Read|Hämta Funktionsinformation om för en prenumeration angående med DataLakeStore.|
|/Locations/checkNameAvailability/Action|Kontrollera tillgängligheten för ett DataLakeStore kontonamn.|
|/Locations/operationResults/Read|Hämta resultatet av en DataLakeStore konto.|
|/Operations/Read|Hämta tillgängliga åtgärder av DataLakeStore.|
|registrera/åtgärd|Registrera prenumeration på DataLakeStore.|

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

| Åtgärd | Beskrivning |
|---|---|
|/Locations/performanceTiers/Read|Returnerar listan över tillgängliga prestandanivåer.|
|/performanceTiers/Read|Returnerar listan över tillgängliga prestandanivåer.|
|/Servers/delete|Tar bort en befintlig server.|
|/Servers/firewallRules/delete|Tar bort en befintlig brandväggsregel.|
|/Servers/firewallRules/Read|Returnera listan över brandväggen regler för en server eller hämtar egenskaperna för den angivna brandväggsregeln.|
|/Servers/firewallRules/Write|Skapar en brandväggsregel med de angivna parametrarna eller uppdaterar en befintlig regel.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar disagnostic inställningen för resursen|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Returnera typer av mätvärden som är tillgängliga för databaser|
|/Servers/Read|Returnera listan över servrar eller hämtar egenskaperna för den angivna servern.|
|/servers/recoverableServers/read|Returnera återställningsbara MySQL-serverinformation|
|/servers/virtualNetworkRules/delete|Tar bort en befintlig regel för virtuella nätverk|
|/servers/virtualNetworkRules/read|Returnera listan över virtuella nätverk regler eller hämtar egenskaperna för den angivna virtuella nätverk regeln.|
|/Servers/virtualNetworkRules/Write|Skapar ett virtuellt nätverk med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna virtuella nätverk regeln.|
|/ servrar/skrivning|Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern.|

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

| Åtgärd | Beskrivning |
|---|---|
|/Locations/performanceTiers/Read|Returnerar listan över tillgängliga prestandanivåer.|
|/performanceTiers/Read|Returnerar listan över tillgängliga prestandanivåer.|
|/Servers/delete|Tar bort en befintlig server.|
|/Servers/firewallRules/delete|Tar bort en befintlig brandväggsregel.|
|/Servers/firewallRules/Read|Returnera listan över brandväggen regler för en server eller hämtar egenskaperna för den angivna brandväggsregeln.|
|/Servers/firewallRules/Write|Skapar en brandväggsregel med de angivna parametrarna eller uppdaterar en befintlig regel.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar disagnostic inställningen för resursen|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/servers/providers/Microsoft.Insights/logDefinitions/read|Returnera typer av loggar som är tillgängliga för databaser|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Returnera typer av mätvärden som är tillgängliga för databaser|
|/Servers/Read|Returnera listan över servrar eller hämtar egenskaperna för den angivna servern.|
|/servers/recoverableServers/read|Returnera återställningsbara PostgreSQL-serverinformation|
|/servers/virtualNetworkRules/delete|Tar bort en befintlig regel för virtuella nätverk|
|/servers/virtualNetworkRules/read|Returnera listan över virtuella nätverk regler eller hämtar egenskaperna för den angivna virtuella nätverk regeln.|
|/Servers/virtualNetworkRules/Write|Skapar ett virtuellt nätverk med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna virtuella nätverk regeln.|
|/ servrar/skrivning|Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern.|

## <a name="microsoftdevices"></a>Microsoft.Devices

| Åtgärd | Beskrivning |
|---|---|
|/ checkNameAvailability/åtgärd|Kontrollera om IotHub namn är tillgängligt|
|/checkProvisioningServiceNameAvailability/Action|Kontrollera om IotHub namn är tillgängligt|
|/ElasticPools/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/ElasticPools/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/elasticPools/iotHubTenants/Delete|Ta bort resursen IotHub-klient|
|/ElasticPools/IotHubTenants/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/ElasticPools/IotHubTenants/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Delete|Ta bort EventHub konsumentgrupp|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Read|Hämta EventHub konsumenten grupper|
|/elasticPools/iotHubTenants/eventHubEndpoints/consumerGroups/Write|Skapa EventHub konsumentgrupp|
|/elasticPools/iotHubTenants/exportDevices/Action|Exportera enheter|
|/elasticPools/iotHubTenants/getStats/Read|Hämtar IotHub innehavaren stats resurs|
|/elasticPools/iotHubTenants/importDevices/Action|Importera enheter|
|/elasticPools/iotHubTenants/iotHubKeys/listkeys/Action|Hämtar klientnyckeln IotHub|
|/elasticPools/iotHubTenants/jobs/Read|Hämta jobb information som skickats på angivna IotHub|
|/elasticPools/iotHubTenants/listKeys/Action|Hämtar IotHub klientnycklar|
|/ElasticPools/IotHubTenants/logDefinitions/read|Hämtar tillgängliga Loggdefinitioner för IotHub Service|
|/ElasticPools/IotHubTenants/metricDefinitions/read|Hämtar tillgängliga mått för tjänsten IotHub|
|/elasticPools/iotHubTenants/quotaMetrics/Read|Hämta mätvärden kvot|
|/elasticPools/iotHubTenants/Read|Hämtar resursen IotHub-klient|
|/elasticPools/iotHubTenants/routing/routes/$testall/Action|Testmeddelande mot alla befintliga vägar|
|/elasticPools/iotHubTenants/routing/routes/$testnew/Action|Testmeddelande mot ett angivna test väg|
|/elasticPools/iotHubTenants/routingEndpointsHealth/Read|Hämtar hälsotillståndet för alla routning slutpunkter för en IotHub|
|/elasticPools/iotHubTenants/Write|Skapa eller uppdatera resursen IotHub-klient|
|/ ElasticPools/metricDefinitions/läsning|Hämtar tillgängliga mått för tjänsten IotHub|
|/iotHubs/certificates/generateVerificationCode/Action|Generera Verifieringskod|
|/iotHubs/Certificates/verify/Action|Kontrollera resursens certifikat|
|/iotHubs/Delete|Ta bort resursen med IotHub|
|/IotHubs/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/IotHubs/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/iotHubs/eventGridFilters/Delete|Tar bort händelsen rutnätet filter|
|/iotHubs/eventGridFilters/Read|Hämtar händelsen rutnätet filter|
|/iotHubs/eventGridFilters/Write|Skapa en ny eller uppdatera befintliga händelse rutnätet filter|
|/iotHubs/eventHubEndpoints/consumerGroups/Delete|Ta bort EventHub konsumentgrupp|
|/iotHubs/eventHubEndpoints/consumerGroups/Read|Hämta EventHub konsumenten grupper|
|/iotHubs/eventHubEndpoints/consumerGroups/Write|Skapa EventHub konsumentgrupp|
|/iotHubs/exportDevices/Action|Exportera enheter|
|/iotHubs/importDevices/Action|Importera enheter|
|/iotHubs/iotHubKeys/listkeys/Action|Hämta IotHub Key för det angivna namnet|
|/iotHubs/iotHubStats/Read|Få statistik IotHub|
|/iotHubs/jobs/Read|Hämta jobb information som skickats på angivna IotHub|
|/iotHubs/listkeys/Action|Hämta alla IotHub-nycklar|
|/ IotHubs/logDefinitions/läsning|Hämtar tillgängliga Loggdefinitioner för IotHub Service|
|/IotHubs/metricDefinitions/read|Hämtar tillgängliga mått för tjänsten IotHub|
|/iotHubs/quotaMetrics/Read|Hämta mätvärden kvot|
|/iotHubs/Read|Hämtar IotHub-resurser|
|/iotHubs/Routing/$ testall/åtgärd|Testmeddelande mot alla befintliga vägar|
|/iotHubs/Routing/$ testnew/åtgärd|Testmeddelande mot ett angivna test väg|
|/iotHubs/routingEndpointsHealth/Read|Hämtar hälsotillståndet för alla routning slutpunkter för en IotHub|
|/iotHubs/skus/Read|Hämta giltiga IotHub Skus|
|/ iotHubs/skrivning|Skapa eller uppdatera IotHub resurs|
|/ operations/Läs|Hämta alla ResourceProvider-åtgärder|
|/provisioningServices/certificates/generateVerificationCode/Action|Generera Verifieringskod|
|/provisioningServices/Certificates/verify/Action|Kontrollera resursens certifikat|
|/provisioningServices/Delete|Ta bort IotDps resurs|
|/provisioningServices/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/provisioningServices/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/provisioningServices/listkeys/Action|Hämta alla IotDps nycklar|
|/provisioningServices/logDefinitions/Read|Hämtar tillgängliga Loggdefinitioner för tjänsten etablering|
|/provisioningServices/metricDefinitions/Read|Hämtar tillgängliga mått för tjänsten etablering|
|/provisioningServices/ProvisioningServiceKeys/listkeys/Action|Hämta IotDps nycklar för nyckelnamn|
|/ provisioningServices/Läs|Hämta IotDps resurs|
|/provisioningServices/skus/Read|Hämta giltiga IotDps Skus|
|/provisioningServices/Write|Skapa IotDps resurs|
|registrera/åtgärd|Registrera prenumerationen för resursprovidern IotHub och kan skapa IotHub resurser|
|registrera/åtgärd|Registrera prenumerationen för resursprovidern IotHub och kan skapa IotHub resurser|
|/ användningar/Läs|Hämta prenumeration användningsinformation för den här providern.|

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

| Åtgärd | Beskrivning |
|---|---|
|/labCenters/delete|Ta bort lab datacenter.|
|/labCenters/read|Läsa lab datacenter.|
|/labCenters/write|Lägg till eller ändra lab datacenter.|
|/labs/artifactSources/armTemplates/read|Läs azure resource manager-mallar.|
|/labs/artifactSources/artifacts/GenerateArmTemplate/action|Genererar en ARM-mall för den angivna artefakten, överför de nödvändiga filerna till ett lagringskonto och validerar artefakt som skapas.|
|/Labs/artifactSources/artifacts/Read|Läsa artefakter.|
|/Labs/artifactSources/delete|Ta bort artefakt källor.|
|/Labs/artifactSources/Read|Läsa artefakt källor.|
|/Labs/artifactSources/Write|Lägg till eller ändra artefakt källor.|
|/Labs/ClaimAnyVm/Action|Begär en slumpmässig claimable virtuell dator i labbet.|
|/Labs/Costs/Read|Läsa kostnader.|
|/Labs/Costs/Write|Lägg till eller ändra kostnaderna.|
|/Labs/CreateEnvironment/Action|Skapa virtuella datorer i ett labb.|
|/labs/customImages/delete|Ta bort anpassade avbildningar.|
|/labs/customImages/read|Läsa anpassade avbildningar.|
|/labs/customImages/write|Lägg till eller ändra anpassade avbildningar.|
|/Labs/delete|Ta bort övningar.|
|/Labs/ExportResourceUsage/Action|Exporterar resursanvändningen lab till ett lagringskonto|
|/Labs/Formulas/delete|Ta bort formler.|
|/Labs/Formulas/Read|Läsa formler.|
|/Labs/Formulas/Write|Lägg till eller ändra formler.|
|/labs/galleryImages/read|Läsa galleriavbildningar.|
|/labs/GenerateUploadUri/action|Generera en URI för överföring av anpassade diskavbildningar till ett labb.|
|/Labs/ImportVirtualMachine/Action|Importera en virtuell dator till en annan labbet.|
|/labs/ListVhds/action|Lista över tillgängliga för att skapa en anpassad avbildning diskavbildningar.|
|/labs/notificationChannels/delete|Ta bort notificationchannels.|
|/labs/notificationChannels/Notify/action|Skicka meddelande till angivna kanal.|
|/labs/notificationChannels/read|Läsa notificationchannels.|
|/labs/notificationChannels/write|Lägg till eller ändra notificationchannels.|
|/labs/policySets/EvaluatePolicies/action|Utvärderar lab princip.|
|/Labs/policySets/policies/delete|Ta bort principer.|
|/labs/policySets/policies/read|Läsa principer.|
|/labs/policySets/policies/write|Lägg till eller ändra principer.|
|/Labs/Read|Läsa övningar.|
|/Labs/Schedules/delete|Ta bort scheman.|
|/Labs/Schedules/Execute/Action|Köra ett schema.|
|/Labs/Schedules/ListApplicable/Action|Visar en lista över alla tillämpliga scheman|
|/Labs/Schedules/Read|Läsa scheman.|
|/Labs/Schedules/Write|Lägg till eller ändra scheman.|
|/labs/serviceRunners/delete|Ta bort tjänsten utlöpare.|
|/Labs/serviceRunners/Read|Läsa service utlöpare.|
|/labs/serviceRunners/write|Lägg till eller ändra tjänsten utlöpare.|
|/Labs/Users/delete|Ta bort användarprofiler.|
|/Labs/Users/Disks/Attach/Action|Ansluta och skapa lånet på disken för att den virtuella datorn.|
|/Labs/Users/Disks/delete|Ta bort diskar.|
|/Labs/Users/Disks/Detach/Action|Koppla från och ta bort lånet för den disk som är ansluten till den virtuella datorn.|
|/Labs/Users/Disks/Read|Läsa diskar.|
|/Labs/Users/Disks/Write|Lägg till eller ändra diskar.|
|/Labs/Users/Environments/delete|Ta bort miljöer.|
|/Labs/Users/Environments/Read|Läsa miljöer.|
|/Labs/Users/Environments/Write|Lägg till eller ändra miljöer.|
|/Labs/Users/Read|Läsa användarprofiler.|
|/Labs/Users/secrets/delete|Ta bort hemligheter.|
|/Labs/Users/secrets/Read|Läsa hemligheter.|
|/Labs/Users/secrets/Write|Lägg till eller ändra hemligheter.|
|/labs/users/serviceFabrics/delete|Ta bort service fabric.|
|/labs/users/serviceFabrics/ListApplicableSchedules/action|Visar en lista över alla tillämpliga scheman|
|/labs/users/serviceFabrics/read|Läsa service fabric.|
|/labs/users/serviceFabrics/schedules/delete|Ta bort scheman.|
|/labs/users/serviceFabrics/schedules/Execute/action|Köra ett schema.|
|/labs/users/serviceFabrics/schedules/read|Läsa scheman.|
|/labs/users/serviceFabrics/schedules/write|Lägg till eller ändra scheman.|
|/Labs/Users/serviceFabrics/Start/Action|Starta ett service fabric.|
|/labs/users/serviceFabrics/Stop/action|Stoppa ett service fabric|
|/labs/users/serviceFabrics/write|Lägg till eller ändra service fabric.|
|/Labs/Users/Write|Lägg till eller ändra användarprofiler.|
|/labs/virtualMachines/AddDataDisk/action|Ansluta en ny eller befintlig datadisk till den virtuella datorn.|
|/Labs/virtualMachines/ApplyArtifacts/Action|Tillämpa artefakter till den virtuella datorn.|
|/Labs/virtualMachines/Claim/Action|Bli ägare till en befintlig virtuell dator|
|/Labs/virtualMachines/delete|Ta bort virtuella datorer.|
|/Labs/virtualMachines/DetachDataDisk/Action|Koppla bort den angivna disken från den virtuella datorn.|
|/Labs/virtualMachines/ListApplicableSchedules/Action|Visar en lista över alla tillämpliga scheman|
|/Labs/virtualMachines/Read|Läsa virtuella datorer.|
|/Labs/virtualMachines/restart/Action|Starta om en virtuell dator.|
|/labs/virtualMachines/schedules/delete|Ta bort scheman.|
|/Labs/virtualMachines/Schedules/Execute/Action|Köra ett schema.|
|/Labs/virtualMachines/Schedules/Read|Läsa scheman.|
|/labs/virtualMachines/schedules/write|Lägg till eller ändra scheman.|
|/labs/virtualMachines/Start/action|Starta en virtuell dator.|
|/labs/virtualMachines/Stop/action|Stoppa en virtuell dator|
|/Labs/virtualMachines/TransferDisks/Action|Överför ägarskapet för virtuella hårddiskar till dig själv|
|/labs/virtualMachines/UnClaim/action|Släpp ägarskap för en befintlig virtuell dator|
|/labs/virtualMachines/write|Lägg till eller ändra virtuella datorer.|
|/Labs/virtualNetworks/delete|Ta bort virtuella nätverk.|
|/Labs/virtualNetworks/Read|Läsa virtuella nätverk.|
|/Labs/virtualNetworks/Write|Lägg till eller ändra virtuella nätverk.|
|/ labs/skrivning|Lägg till eller ändra övningar.|
|/Locations/Operations/Read|Läs-och skrivåtgärder.|
|registrera/åtgärd|Registrerar prenumerationen|
|/Schedules/delete|Ta bort scheman.|
|/Schedules/Execute/Action|Köra ett schema.|
|/Schedules/Read|Läsa scheman.|
|/Schedules/Retarget/Action|Uppdaterar ett schema målresurs Id.|
|/ scheman/skrivning|Lägg till eller ändra scheman.|

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

| Åtgärd | Beskrivning |
|---|---|
|/databaseAccountNames/read|Söker efter tillgänglighet.|
|/databaseAccounts/changeResourceGroup/action|Ändra resursgruppen för ett databaskonto|
|/databaseAccounts/databases/collections/metricDefinitions/read|Läser samlingen måttdefinitioner.|
|/databaseAccounts/Databases/Collections/Metrics/Read|Läser samling mått.|
|/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read|Läsa databas konto partition nivån nyckelvärden|
|/databaseAccounts/databases/collections/partitions/metrics/read|Läsa databas konto partition nivån måtten|
|/databaseAccounts/databases/collections/partitions/usages/read|Läsa databaskonto partition nivån användningsområden|
|/databaseAccounts/databases/collections/usages/read|Läser samling användningsområden.|
|/databaseAccounts/databases/metricDefinitions/read|Läser databasen måttdefinitioner|
|/databaseAccounts/Databases/Metrics/Read|Läser databasen mått.|
|/databaseAccounts/databases/usages/read|Läser databasen användningsområden.|
|/databaseAccounts/delete|Tar bort databasen konton.|
|/databaseAccounts/failoverPriorityChange/action|Ändra prioriteringarna för växling vid fel för områden i ett databaskonto. Används för att utföra manuell redundans|
|/databaseAccounts/listConnectionStrings/action|Hämta anslutningssträngar för ett databaskonto|
|/databaseAccounts/listKeys/action|Lista nycklar för ett databaskonto|
|/databaseAccounts/metricDefinitions/read|Läser databasen konto mått definitioner.|
|/databaseAccounts/Metrics/Read|Läser databasen konto mått.|
|/databaseAccounts/operationResults/read|Lässtatus för asynkron åtgärd|
|/databaseAccounts/percentile/metrics/read|Läsa måtten för svarstid|
|/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read|Läsa latens måtten för en viss region för källa och mål|
|/databaseAccounts/percentile/targetRegion/metrics/read|Läsa latens måtten för en specifik målsökväg region|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/databaseAccounts/providers/Microsoft.Insights/logDefinitions/read|Hämtar tillgängliga catageries för konto|
|/databaseAccounts/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för databasen konto|
|/databaseAccounts/Read|Läser ett databaskonto.|
|/databaseAccounts/readonlykeys/action|Läser databasen readonly nycklar.|
|/databaseAccounts/readonlykeys/read|Läser databasen readonly nycklar.|
|/databaseAccounts/regenerateKey/action|Rotera ett databaskonto nycklar|
|/databaseAccounts/region/Databases/Collections/Metrics/Read|Läser regionala samling mått.|
|/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read|Läsa regionala databasen konto partition nivån nyckelvärden|
|/databaseAccounts/region/databases/collections/partitions/metrics/read|Läsa regionala databasen konto partition nivån måtten|
|/databaseAccounts/region/databases/collections/partitions/read|Läsa databas konto partitioner i en samling|
|/databaseAccounts/region/metrics/read|Läser konto mätvärdena region och databasen.|
|/databaseAccounts/usages/read|Läser databasen konto användningsområden.|
|/ databaseAccounts/skrivning|Uppdatera en databas-konton.|
|/locations/deleteVirtualNetworkOrSubnets/action|Meddelar Microsoft.DocumentDB att VirtualNetwork eller undernät tas bort|
|/operationResults/Read|Lässtatus för asynkron åtgärd|
|/Operations/Read|Läs-och skrivåtgärder som är tillgängliga för Microsoft DocumentDB |
|registrera/åtgärd| Registerresursleverantören Microsoft DocumentDB för prenumerationen|

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

| Åtgärd | Beskrivning |
|---|---|
|/checkDomainAvailability/Action|Kontrollera om en domän kan köpas|
|/domains/Delete|Ta bort en befintlig domän.|
|/domains/domainownershipidentifiers/Delete|Ta bort ägarskap identifierare|
|/domains/domainownershipidentifiers/Read|Lista över ägarskapet identifierare|
|/domains/domainownershipidentifiers/Read|Hämta ägarskap identifierare|
|/domains/domainownershipidentifiers/Write|Skapa eller uppdatera identifierare|
|/domains/operationresults/Read|En åtgärd som domän|
|/domains/operations/Read|Visa en lista med alla åtgärder från domänregistrering för app service|
|/domains/Read|Hämta listan över domäner|
|/domains/Read|Hämta domän|
|/Domains/renew/Action|Förnya en befintlig domän.|
|/ domäner/skrivning|Lägg till en ny domän eller uppdatera en befintlig|
|/generateSsoRequest/Action|Skapa en begäran för att logga in kontrollcenter för domänen.|
|/listDomainRecommendations/Action|Hämta listan domän rekommendationer baserat på nyckelord|
|registrera/åtgärd|Microsoft Domains registerresursleverantören för prenumerationen|
|/topLevelDomains/listAgreements/Action|Listan avtal åtgärd|
|/topLevelDomains/Read|Hämta toplevel domäner|
|/topLevelDomains/Read|Hämta toplevel domän|
|/validateDomainRegistrationInformation/Action|Verifiera domän köp objekt utan att skicka den|

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

| Åtgärd | Beskrivning |
|---|---|
|/lcsprojects/clouddeployments/Read|Visa Microsoft Dynamics AX 2012 R3 utvärdering distributioner i Microsoft Dynamics livscykel Services-projekt som hör till en användare|
|/lcsprojects/clouddeployments/Write|Skapa Microsoft Dynamics AX 2012 R3 utvärdering distribution i ett Microsoft Dynamics livscykel Services-projekt som hör till en användare. Distributioner kan hanteras från Azure-hanteringsportalen|
|/lcsprojects/Connectors/Read|Läs kopplingar som hör till ett Microsoft Dynamics livscykel Services-projekt|
|/lcsprojects/Connectors/Write|Skapa och uppdatera kopplingar som hör till ett Microsoft Dynamics livscykel Services-projekt|
|/lcsprojects/delete|Ta bort Microsoft Dynamics livscykel Services-projekt som hör till användaren|
|/lcsprojects/Read|Visa Microsoft Dynamics livscykel Services-projekt som hör till en användare|
|/ lcsprojects/skrivning|Skapa och uppdatera Microsoft Dynamics livscykel Services-projekt som tillhör användaren. Endast namn och beskrivning egenskaper kan uppdateras. Prenumeration och plats som är kopplade till projektet kan inte uppdateras när den har skapats|

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

| Åtgärd | Beskrivning |
|---|---|
|/eventSubscriptions/delete|Ta bort en eventSubscription|
|/eventSubscriptions/getFullUrl/action|Hämta fullständiga URL: en för händelseprenumerationen|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar diagnostikinställningen för prenumerationer på händelser|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningen för prenumerationer på händelser|
|/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för eventSubscriptions|
|/eventSubscriptions/Read|Läs en eventSubscription|
|/eventSubscriptions/write|Skapa eller uppdatera en eventSubscription|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar diagnostikinställningen för avsnitt|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningen för avsnitt|
|/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för avsnitt|
|registrera/åtgärd|Registrerar eventSubscription för resursprovidern EventGrid och kan skapa händelse rutnätet prenumerationer.|
|/topics/delete|Ta bort ett ämne|
|/topics/listKeys/Action|Lista nycklar för avsnittet|
|/topics/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar diagnostikinställningen för avsnitt|
|/topics/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningen för avsnitt|
|/topics/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för avsnitt|
|/topics/Read|Läs ett ämne|
|/topics/regenerateKey/action|Generera nyckel för avsnittet|
|/ artiklar/skrivning|Skapa eller uppdatera ett avsnitt|

## <a name="microsofteventhub"></a>Microsoft.EventHub

| Åtgärd | Beskrivning |
|---|---|
|/ checkNameAvailability/åtgärd|Kontrollerar tillgänglighet för namnområden i en viss prenumeration.|
|/ checkNamespaceAvailability/åtgärd|Kontrollerar tillgänglighet för namnområden i en viss prenumeration. Detta API är inaktuell. Använd CheckNameAvailabiltiy i stället.|
|/Namespaces/authorizationRules/Action|Uppdateringar Namespace auktoriseringsregeln. Detta API är depricated. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln Namespace i stället... Den här åtgärden stöds inte på API-version 2017-04-01.|
|/Namespaces/authorizationRules/delete|Ta bort Namespace auktoriseringsregeln. Auktoriseringsregeln standard Namespace kan inte tas bort. |
|/namespaces/authorizationRules/listkeys/action|Hämta anslutningssträngen till namnområdet|
|/Namespaces/authorizationRules/Read|Hämta listan över beskrivning av auktoriseringsregler för namnområden.|
|/namespaces/authorizationRules/regenerateKeys/action|Återskapa den primära eller sekundära nyckeln till resursen|
|/Namespaces/authorizationRules/Write|Skapa en nivå auktoriseringsregler Namespace och uppdatera dess egenskaper. Åtkomstbehörigheter för regler för auktorisering, primära och sekundärnycklar kan uppdateras.|
|namnområden/ta bort|Ta bort namnområdesresurs|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Hämtar tillståndet regler nycklar för katastrofåterställning primära namnområdet|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Hämta Disaster Recovery primära Namespaces auktoriseringsregler|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|Inaktiverar haveriberedskap och stoppar replikering av ändringar från primära till sekundära namnområden.|
|/Namespaces/disasterrecoveryconfigs/checkNameAvailability/Action|Kontrollerar tillgängligheten för namnområdesalias under de angivna prenumerationen.|
|/namespaces/disasterRecoveryConfigs/delete|Tar bort Disaster Recovery-konfiguration som associeras med namnområdet. Den här åtgärden kan bara anropas via primära namnområdet.|
|/namespaces/disasterRecoveryConfigs/failover/action|Anropar GEO DR-redundans och omkonfigurerar namnområdets alias till att peka på det sekundära namnområdet.|
|/namespaces/disasterRecoveryConfigs/read|Hämtar den haveriberedskapskonfiguration som är kopplad till namnområdet.|
|/namespaces/disasterRecoveryConfigs/write|Skapar eller uppdaterar den haveriberedskapskonfiguration som är kopplad till namnområdet.|
|/namespaces/eventhubs/authorizationRules/action|Åtgärden Uppdatera EventHub. Den här åtgärden stöds inte på API-version 2017-04-01. Auktoriseringsregler. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln.|
|/namespaces/eventhubs/authorizationRules/delete|Åtgärden ta bort EventHub-auktoriseringsregler|
|/namespaces/eventhubs/authorizationRules/listkeys/action|Hämta anslutningssträngen till EventHub|
|/namespaces/eventhubs/authorizationRules/read| Hämta en lista över EventHub-auktoriseringsregler|
|/namespaces/eventhubs/authorizationRules/regenerateKeys/action|Återskapa den primära eller sekundära nyckeln till resursen|
|/namespaces/eventhubs/authorizationRules/write|Skapa auktoriseringsregler för EventHub och uppdatera dess egenskaper. Att det går uppdatera åtkomstbehörigheter för regler för auktorisering.|
|/namespaces/eventHubs/consumergroups/Delete|Åtgärden ta bort ConsumerGroup resurs|
|/namespaces/eventHubs/consumergroups/read|Hämta lista över ConsumerGroup resurs beskrivningar|
|/namespaces/eventHubs/consumergroups/write|Skapa eller uppdatera ConsumerGroup egenskaper.|
|/namespaces/eventhubs/Delete|Åtgärden ta bort EventHub-resurs|
|/Namespaces/eventhubs/Read|Hämta lista över EventHub resurs beskrivningar|
|/Namespaces/eventhubs/Write|Skapa eller uppdatera EventHub-egenskaper.|
|/Namespaces/messagingPlan/Read|Hämtar meddelanden planera för ett namnområde. Detta API är inaktuell. Egenskaper som exponeras via MessagingPlan resursen flyttas till (överordnad) Namespace resurs API senare... Den här åtgärden stöds inte på API-version 2017-04-01.|
|/Namespaces/messagingPlan/Write|Uppdaterar Messaging planera för ett namnområde. Detta API är inaktuell. Egenskaper som exponeras via MessagingPlan resursen flyttas till (överordnad) Namespace resurs API senare... Den här åtgärden stöds inte på API-version 2017-04-01.|
|/Namespaces/operationresults/Read|Hämta status för namnområdesåtgärd|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|Hämta lista över Namespace diagnostikinställningar resurs beskrivningar|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|Hämta lista över Namespace diagnostikinställningar resurs beskrivningar|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|Hämta en lista över Namespace loggar resurs beskrivningar|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Hämta lista över Namespace mått resurs beskrivningar|
|/Namespaces/Read|Hämta listan över beskrivningar av namnområdesresurs|
|/ namnområden/skrivning|Skapa en resurs för Namespace och uppdatera dess egenskaper. Taggar och kapacitet för Namespace är egenskaper som kan uppdateras.|
|/Operations/Read|Hämta åtgärder|
|registrera/åtgärd|Registrerar prenumerationen för EventHub-resursprovidern och gör det möjligt att skapa EventHub-resurser|
|/sku/read|Hämta lista över Sku resurs beskrivningar|
|/sku/regions/read|Hämta lista över SkuRegions resurs beskrivningar|
|/unregister/action|Registrerar EventHub-resursprovidern|

## <a name="microsoftfeatures"></a>Microsoft.Features

| Åtgärd | Beskrivning |
|---|---|
|/Features/Read|Hämtar en prenumerations funktioner.|
|/providers/Features/Read|Hämtar en prenumerations funktion hos en given resursprovider.|
|/providers/Features/register/Action|Registrerar funktionen för en prenumerations hos en given resursprovider.|
|/providers/Features/unregister/Action|Avregistrerar funktionen för en prenumeration hos en viss resursprovider.|

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

| Åtgärd | Beskrivning |
|---|---|
|/clusters/changerdpsetting/action|Ändra RDP-inställningen för HDInsight-kluster|
|/Clusters/Configurations/Action|Uppdatera konfigurationen för HDInsight-kluster|
|/Clusters/Configurations/Read|Hämta konfigurationer för HDInsight-kluster|
|/Clusters/delete|Ta bort ett HDInsight-kluster|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar diagnostikinställningen för resursen HDInsight-kluster|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningen för resursen HDInsight-kluster|
|/clusters/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för HDInsight-kluster|
|/Clusters/Read|Få information om HDInsight-kluster|
|/Clusters/roles/Resize/Action|Ändra storlek på ett HDInsight-kluster|
|/ kluster och skrivning|Skapa eller uppdatera HDInsight-kluster|
|/Locations/Capabilities/Read|Hämta prenumerationens funktioner|
|/Locations/checkNameAvailability/Read|Kontrollera namntillgänglighet|

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

| Åtgärd | Beskrivning |
|---|---|
|/Jobs/delete|Tar bort ett befintligt jobb.|
|/jobs/listBitLockerKeys/action|Hämtar BitLocker-nycklar för det angivna jobbet.|
|/Jobs/Read|Hämtar egenskaperna för det angivna jobbet eller returnerar listan över jobb.|
|/ jobb/skrivning|Skapar ett jobb med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för det angivna jobbet.|
|/Locations/Read|Hämtar egenskaperna för den angivna platsen eller returnerar en lista med platser.|
|registrera/åtgärd|Registrerar prenumerationen för resursprovidern import/export- och kan skapa jobb för import och export.|

## <a name="microsoftinsights"></a>Microsoft.Insights

| Åtgärd | Beskrivning |
|---|---|
|/ActionGroups/Delete|Tar bort en åtgärdsgrupp|
|/ActionGroups/Read|Läser en åtgärdsgrupp|
|/ActionGroups/Write|Skriver en åtgärdsgrupp|
|/ActivityLogAlerts/Activated/Action|Aktivitetsloggavisering har utlösts|
|/ActivityLogAlerts/Delete|Tar bort en aktivitetsloggavisering|
|/ActivityLogAlerts/Read|Läser en aktivitetsloggavisering|
|/ ActivityLogAlerts/skrivning|Läser en aktivitetsloggavisering|
|/ AlertRules/aktiverat/åtgärd|Varningsregeln har aktiverats|
|/AlertRules/Delete|Tar bort en varningsregelskonfiguration|
|/AlertRules/Incidents/Read|Läser en konfiguration för varningsregelincidenter|
|/ AlertRules/läsning|Läser en varningsregelskonfiguration|
|/ AlertRules/löst/åtgärd|Varningsregeln har lösts|
|/ AlertRules/begränsas/åtgärd|Varningsregeln begränsas|
|/ AlertRules/skrivning|Skriver till en varningsregelskonfiguration|
|/AutoscaleSettings/Delete|Tar bort en autoskalningskonfiguration|
|/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read|Läs måttdefinitioner|
|/ AutoscaleSettings/läsning|Läser en autoskalningskonfiguration|
|/ AutoscaleSettings/Scaledown/åtgärd|Åtgärd för autoskalning nedåt|
|/AutoscaleSettings/Scaleup/Action|Åtgärd för autoskalning uppåt|
|/ AutoscaleSettings/skrivning|Skriver till en autoskalningskonfiguration|
|/Components/AnalyticsItems/Delete|Ta bort ett objekt för analys av Application Insights|
|/ Komponenter/AnalyticsItems/läsning|Läsa ett Application Insights analytics-objekt|
|Och komponenter/AnalyticsItems/skrivning|Skriva ett Application Insights analytics-objekt|
|/ Komponenter/AnalyticsTables/åtgärd|Application Insights analytics Tabellåtgärd|
|/Components/AnalyticsTables/Delete|Ta bort en Application Insights tabell analytics schema|
|/Components/AnalyticsTables/Read|Läser en Application Insights tabell analytics schema|
|Och komponenter/AnalyticsTables/skrivning|Skriva ett Application Insights tabell analytics schema|
|/ Komponenter/anteckningar/ta bort|Om du tar bort Application Insights anteckningen|
|/ Komponenter/anteckningar/läsning|Läsa anteckningens Application Insights|
|Och komponenter/anteckningar/skrivning|Skriva anteckningens Application Insights|
|/ Komponenter/Api/läsning|Datainläsningen Application Insights component API|
|/ Komponenter/ApiKeys/åtgärd|Generera en Application Insights API-nyckel|
|/Components/ApiKeys/Delete|Om du tar bort en Application Insights API-nyckel|
|/Components/ApiKeys/Read|Läser en Application Insights API-nyckel|
|/Components/BillingPlanForComponent/Read|Läsa ett faktureringsavtal för Application Insights-komponent|
|/ Komponenter/CurrentBillingFeatures/läsning|Läsa fakturering funktionerna för Application Insights-komponent|
|Och komponenter/CurrentBillingFeatures/skrivning|Skrivning fakturering funktionerna för Application Insights-komponent|
|/Components/DefaultWorkItemConfig/Read|Läser en Application Insights standard ALM integration konfiguration|
|/ Komponenter/ta bort|Tar bort konfiguration av Application Insights-komponent|
|/ Komponenter/ExportConfiguration/åtgärd|Application Insights Exportera inställningar för åtgärden|
|/ Komponenter/ExportConfiguration/ta bort|Tar bort Application Insights exporterar du inställningar|
|/ Komponenter/ExportConfiguration/läsning|Läsa Application Insights exporterar du inställningar|
|Och komponenter/ExportConfiguration/skrivning|Skrivning Application Insights exporterar du inställningar|
|/ Komponenter/ExtendQueries/läsning|Läsa Application Insights component utökad frågeresultat|
|/Components/Favorites/Delete|Om du tar bort Application Insights-favoriten|
|/ Komponenter/Favoriter/läsning|Läsa Application Insights-favoriten|
|Och komponenter/Favoriter/skrivning|Skriva ett Application Insights-favoriten|
|/ Komponenter/FeatureCapabilities/läsning|Läsa Application Insights component-funktioner|
|/Components/GetAvailableBillingFeatures/Read|Läsa Application Insights komponenten faktureringsinformation funktioner som är tillgängliga|
|/ Komponenter/GetToken/läsning|Läsa en token för Application Insights-komponent|
|/ Komponenter/ListMigrationDate/åtgärd|Hämta tillbaka prenumeration migrering datum|
|/ Komponenter/ListMigrationDate/läsning|Hämta tillbaka prenumeration migrering datum|
|/ Komponenter/MetricDefinitions/läsning|Läsa måttdefinitionerna för Application Insights-komponent|
|/ Komponenter/mått/läsning|Läsa Application Insights component mått|
|/Components/MigrateToNewpricingModel/Action|Migrera prenumeration till ny prissättningsmodell|
|/ Komponenter/flytta/åtgärd|Flytta en programkomponent insikter till en annan resursgrupp eller prenumeration|
|/Components/MyAnalyticsItems/Delete|Ta bort ett objekt för Application Insights personliga analytics|
|/Components/MyAnalyticsItems/Read|Läsa ett Application Insights personliga analytics-objekt|
|/Components/MyAnalyticsItems/Write|Skriva ett Application Insights personliga analytics-objekt|
|/Components/MyFavorites/Read|Läser en personlig Application Insights-favoriten|
|/Components/PricingPlans/Read|Läsa komponenten Application Insights priser plan|
|Och komponenter/PricingPlans/skrivning|Skriva en Application Insights-komponent priser plan|
|/Components/ProactiveDetectionConfigs/Read|Konfigurera proaktiv identifiering av läsa Application Insights|
|/Components/ProactiveDetectionConfigs/Write|Skrivning Application Insights proaktiv identifiering konfiguration|
|/Components/providers/Microsoft.Insights/MetricDefinitions/Read|Läs måttdefinitioner|
|/ Komponenter/QuotaStatus/läsning|Läsa Application Insights Komponentstatus kvot|
|/ Komponenter/läsning|Läser en konfiguration av Application Insights-komponent|
|/Components/RollbackToLegacyPricingModel/Action|Äldre Prismodell rollback-prenumeration|
|/Components/SyntheticMonitorLocations/Read|Läsa Application Insights webtest platser|
|/Components/WorkItemConfigs/Delete|Om du tar bort en konfiguration för Application Insights ALM-integrering|
|/Components/WorkItemConfigs/Read|Läser en konfiguration för Application Insights ALM-integrering|
|/Components/WorkItemConfigs/Write|Skriva en konfiguration för Application Insights ALM-integrering|
|Och komponenter/skrivning|Skriver till en konfiguration av Application Insights-komponent|
|/DiagnosticSettings/Delete|Tar bort konfigurationen av diagnostikinställningar|
|/ DiagnosticSettings/läsning|Läser en konfiguration av diagnostikinställningar|
|/ DiagnosticSettings/skrivning|Skriver till konfiguration av diagnostikinställningar|
|/EventCategories/Read|Läser en händelsekategori|
|/eventtypes/digestevents/Read|Läs sammandrag av hanteringshändelsetyp|
|/eventtypes/Values/Read|Läs värden för hanteringshändelsetyp|
|/ExtendedDiagnosticSettings/Delete|Tar bort konfigurationen av utökade diagnostikinställningar|
|/ ExtendedDiagnosticSettings/läsning|Läser konfiguration av utökade diagnostikinställningar|
|/ ExtendedDiagnosticSettings/skrivning|Skriver till konfiguration av utökade diagnostikinställningar|
|/ LogDefinitions/läsning|Läs loggdefinitioner|
|/LogProfiles/Delete|Ta bort konfiguration för loggprofiler|
|/ LogProfiles/läsning|Läs loggprofiler|
|/ LogProfiles/skrivning|Skriver till en konfiguration för loggprofil|
|/ MetricAlerts/ta bort|Tar bort en måttavisering|
|/ MetricAlerts/läsning|Läser en måttavisering|
|/ MetricAlerts/skrivning|Skriver en måttavisering|
|/MetricDefinitions/Microsoft.Insights/Read|Läs måttdefinitioner|
|/MetricDefinitions/providers/Microsoft.Insights/Read|Läs måttdefinitioner|
|/ MetricDefinitions/läsning|Läs måttdefinitioner|
|/ Mått/providers/mått/läsning|Läs mått|
|/ Mått/läsning|Läs mått|
|/ Mått/skrivning|Skriva mått|
|/ Operations/läsning|Läser åtgärder|
|/ Register/åtgärd|Registrera Microsoft Insights-providern|
|/ Klienter/Register/åtgärd|Startar microsoft insights-providern|
|/Unregister/Action|Registrera Microsoft Insights-providern|
|/Webtests/Delete|Tar bort en webbtestkonfiguration|
|/Webtests/GetToken/Read|Läsa en token för webbtestet|
|/ Webtests/MetricDefinitions/läsning|Läsa måttdefinitionerna för webbtestet|
|/ Webtests/mått/läsning|Läser en webtest mått|
|/Webtests/Read|Läser en webbtestkonfiguration|
|/ Webtests/skrivning|Skriver till en webbtestkonfiguration|

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

| Åtgärd | Beskrivning |
|---|---|
|/checkNameAvailability/Read|Kontrollera att nyckelvalvsnamnet är giltigt och inte används|
|/deletedVaults/Read|Visa egenskaperna för de ej permanent borttagna nyckelvalven|
|/hsmPools/delete|Ta bort en HSM-pool|
|/hsmPools/joinVault/action|Anslut ett nyckelvalv till en HSM-pool|
|/hsmPools/read|Visa egenskaperna för en HSM-pool|
|/hsmPools/write|Skapa en ny HSM-pool eller uppdatera egenskaperna för en befintlig HSM-pool|
|/Locations/deletedVaults/PURGE/Action|Rensa ett ej permanent borttaget nyckelvalv|
|/Locations/deletedVaults/Read|Visa egenskaperna för ett ej permanent borttaget nyckelvalv|
|/locations/deleteVirtualNetworkOrSubnets/action|Aviserar Microsoft.KeyVault att ett virtuellt nätverk eller undernät tas bort|
|/Locations/operationResults/Read|Kontrollera resultatet för en långkörd åtgärd|
|/Operations/Read|Listar de åtgärder som finns tillgängliga på resursprovidern Microsoft.KeyVault|
|registrera/åtgärd|Registrerar en prenumeration|
|/unregister/action|Avregistrerar en prenumeration|
|/vaults/accessPolicies/write|Uppdatera en befintlig åtkomstprincip genom att sammanfoga, ersätta eller lägga till en ny åtkomstprincip till ett valv.|
|/vaults/delete|Ta bort ett nyckelvalv|
|/vaults/Deploy/Action|Tillåter åtkomst till hemligheter i ett nyckelvalv vid distribution av Azure-resurser|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Read|Hämtar den diagnostiska inställningen för resursen|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/vaults/providers/Microsoft.Insights/logDefinitions/read|Hämtar tillgängliga loggar för ett nyckelvalv|
|/vaults/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för ett nyckelvalv|
|/vaults/Read|Visa egenskaperna för ett nyckelvalv|
|/vaults/secrets/Read|Visa egenskaperna för en hemlighet, men inte dess värde|
|/vaults/secrets/Write|Skapa en ny hemlighet eller uppdatera värdet för en befintlig hemlighet|
|/ valv/skrivning|Skapa ett nytt nyckelvalv eller uppdatera egenskaperna för ett befintligt nyckelvalv|

## <a name="microsoftlabservices"></a>Microsoft.LabServices

| Åtgärd | Beskrivning |
|---|---|
|/labAccounts/CreateLab/action|Skapa ett labb i ett labb-konto.|
|/labAccounts/delete|Ta bort lab konton.|
|/labAccounts/Labs/delete|Ta bort övningar.|
|/labAccounts/Labs/environmentSettings/delete|Ta bort inställningen för miljön.|
|/labAccounts/Labs/environmentSettings/Environments/delete|Ta bort miljöer.|
|/labAccounts/Labs/environmentSettings/Environments/Read|Läsa miljöer.|
|/labAccounts/Labs/environmentSettings/Environments/Write|Lägg till eller ändra miljöer.|
|/labAccounts/Labs/environmentSettings/publish/Action|Tillhandahåller/deprovisions krävs resurser för en miljö som inställningen baserat på aktuell status för inställningen labb-miljö.|
|/labAccounts/Labs/environmentSettings/Read|Läsa miljöinställning.|
|/labAccounts/Labs/environmentSettings/Write|Lägg till eller ändra inställningen för miljön.|
|/labAccounts/Labs/Read|Läsa övningar.|
|/labAccounts/Labs/Users/delete|Ta bort användare.|
|/labAccounts/Labs/Users/Read|Läsa användare.|
|/labAccounts/Labs/Users/Write|Lägg till eller ändra användare.|
|/labAccounts/Labs/Write|Lägg till eller ändra övningar.|
|/labAccounts/read|Läsa lab konton.|
|/ labAccounts/skrivning|Lägg till eller ändra lab konton.|
|/Locations/Operations/Read|Läs-och skrivåtgärder.|
|registrera/åtgärd|Registrerar prenumerationen|

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

| Åtgärd | Beskrivning |
|---|---|
|/Accounts/delete|Ta bort en plats baserat Services-konto.|
|/Accounts/listKeys/Action|Visa en lista över nycklar plats baserat Services-konto|
|/Accounts/providers/Microsoft.Insights/diagnosticSettings/Read|Hämtar den diagnostiska inställningen för resursen|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för tjänstkonton för plats-baserad|
|/Accounts/Read|Hämta en plats baserat Services-konto.|
|/Accounts/regenerateKey/Action|Skapa ny plats baserat Services-konto primär eller sekundär nyckel|
|/ konton/skrivning|Skapa eller uppdatera en plats baserat Services-konto.|
|registrera/åtgärd|Registrera providern|

## <a name="microsoftlogic"></a>Microsoft.Logic

| Åtgärd | Beskrivning |
|---|---|
|/integrationAccounts/agreements/delete|Tar bort avtal i kontot för integrering.|
|/integrationAccounts/agreements/listContentCallbackUrl/action|Hämtar URL: en för återanrop för innehållet i integration konto.|
|/integrationAccounts/agreements/Read|Läser avtal i kontot för integrering.|
|/integrationAccounts/agreements/Write|Skapar eller uppdaterar avtal i kontot för integrering.|
|/integrationAccounts/assemblies/delete|Tar bort sammansättningen i integration konto.|
|/integrationAccounts/assemblies/listContentCallbackUrl/action|Hämtar återanrop URL: en för sammansättningen innehållet i integration konto.|
|/integrationAccounts/assemblies/Read|Läser sammansättningen i integration konto.|
|/integrationAccounts/assemblies/write|Skapar eller uppdaterar sammansättningen i integration konto.|
|/integrationAccounts/batchConfigurations/delete|Tar bort konfigurationen batch i integration konto.|
|/integrationAccounts/batchConfigurations/read|Läser batch-konfigurationen i integration konto.|
|/integrationAccounts/batchConfigurations/write|Skapar eller uppdaterar batch-konfigurationen i integration konto.|
|/integrationAccounts/Certificates/delete|Tar bort certifikatet i integration konto.|
|/integrationAccounts/Certificates/Read|Läser certifikatet i integration konto.|
|/integrationAccounts/Certificates/Write|Skapar eller uppdaterar certifikat i integration konto.|
|/integrationAccounts/delete|Tar bort kontot integrering.|
|/integrationAccounts/listCallbackUrl/action|Hämtar återanrop URL för integrering konto.|
|/integrationAccounts/listKeyVaultKeys/action|Hämtar nycklarna i nyckelvalvet.|
|/integrationAccounts/logTrackingEvents/action|Loggar händelser för spårning i kontot för integrering.|
|/integrationAccounts/Maps/delete|Tar bort kartan i integration konto.|
|/integrationAccounts/maps/listContentCallbackUrl/action|Hämtar återanrop URL för kartinnehåll i integration konto.|
|/integrationAccounts/Maps/Read|Läser kartan i integration konto.|
|/integrationAccounts/Maps/Write|Skapar eller uppdaterar kartan i integration konto.|
|/integrationAccounts/partners/delete|Tar bort partner i integration konto.|
|/integrationAccounts/partners/listContentCallbackUrl/action|Hämtar återanrop URL för partner innehåll i integration konto.|
|/integrationAccounts/partners/Read|Läser det kompanjon i integration konto.|
|/integrationAccounts/partners/write|Skapar eller uppdaterar partner i integration konto.|
|/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read|Läser integrationskontots loggdefinitioner.|
|/integrationAccounts/Read|Läser integration-konto.|
|/integrationAccounts/regenerateAccessKey/action|Återskapar åtkomstnyckelhemligheter.|
|/integrationAccounts/schemas/delete|Tar bort schemat i integration konto.|
|/integrationAccounts/schemas/listContentCallbackUrl/action|Hämtar återanrop URL för schemat innehåll i integration konto.|
|/integrationAccounts/schemas/read|Läser schemat i integration konto.|
|/integrationAccounts/schemas/write|Skapar eller uppdaterar schemat i integration konto.|
|/integrationAccounts/sessions/delete|Tar bort sessionen i integration konto.|
|/integrationAccounts/sessions/Read|Läser batch-konfigurationen i integration konto.|
|/integrationAccounts/sessions/Write|Skapar eller uppdaterar sessionen i integration konto.|
|/ integrationAccounts/skrivning|Skapar eller uppdaterar integration-konto.|
|/Locations/Workflows/Validate/Action|Verifierar arbetsflödet.|
|/Operations/Read|Hämtar åtgärden.|
|registrera/åtgärd|Registrerar Microsoft.Logic-resursprovidern för en viss prenumeration.|
|/workflows/accessKeys/delete|Tar bort åtkomstnyckeln.|
|/workflows/accessKeys/list/action|Radar upp åtkomstnyckelhemligheter.|
|/workflows/accessKeys/read|Läser åtkomstnyckeln.|
|/workflows/accessKeys/regenerate/action|Återskapar åtkomstnyckelhemligheter.|
|/workflows/accessKeys/write|Skapar eller uppdaterar åtkomstnyckeln.|
|/Workflows/delete|Tar bort arbetsflödet.|
|/Workflows/disable/Action|Inaktiverar arbetsflödet.|
|/Workflows/enable/Action|Aktiverar arbetsflödet.|
|/workflows/listCallbackUrl/action|Hämtar arbetsflödets motringnings-URL.|
|/workflows/listSwagger/action|Hämtar arbetsflödets Swagger-definitioner.|
|/Workflows/Move/Action|Flyttar arbetsflödet från befintligt prenumerations-id, resursgrupp och/eller namn till ett annat prenumerations-id, resursgrupp och/eller namn.|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/read|Läser diagnostikinställningar för arbetsflödet.|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställning för arbetsflödet.|
|/workflows/providers/Microsoft.Insights/logDefinitions/read|Läser loggdefinitioner för arbetsflödet.|
|/workflows/providers/Microsoft.Insights/metricDefinitions/read|Läser måttdefinitioner för arbetsflödet.|
|/Workflows/Read|Läser arbetsflödet.|
|/workflows/regenerateAccessKey/action|Återskapar åtkomstnyckelhemligheter.|
|/Workflows/Run/Action|Startar en körning av arbetsflödet.|
|/workflows/runs/actions/listExpressionTraces/action|Hämtar spåren av arbetsflödeskörningens åtgärdsuttryck.|
|/Workflows/Runs/Actions/Read|Läser arbetsflödets köråtgärd.|
|/Workflows/Runs/Actions/repetitions/listExpressionTraces/Action|Hämtar upprepningsspåren av arbetsflödeskörningens åtgärdsuttryck.|
|/Workflows/Runs/Actions/repetitions/Read|Läser upprepningen av arbetsflödets körningsåtgärd.|
|/Workflows/Runs/Actions/scoperepetitions/Read|Läser upprepningen av reservationsomfånget för arbetsflödets körningsåtgärd.|
|/Workflows/Runs/Cancel/Action|Avbryter körningen av ett arbetsflöde.|
|/Workflows/Runs/Operations/Read|Läser åtgärdsstatus för arbetsflödeskörning.|
|-arbetsflöden körs/läsa|Läser arbetsflödeskörningen.|
|/workflows/suspend/action|Gör uppehåll i arbetsflödet.|
|/Workflows/Triggers/histories/Read|Läser utlösarhistorik.|
|/workflows/triggers/histories/resubmit/action|Skickar om arbetsflödets utlösare.|
|/workflows/triggers/listCallbackUrl/action|Hämtar motringnings-URL:en för utlösaren.|
|/Workflows/Triggers/Read|Läser utlösarna.|
|/workflows/triggers/reset/action|Återställer utlösaren.|
|/workflows/triggers/run/action|Utför utlösaren.|
|/workflows/triggers/setState/action|Anger läget för utlösare.|
|/Workflows/Validate/Action|Verifierar arbetsflödet.|
|/Workflows/versions/Read|Läser arbetsflödesversionen.|
|/workflows/versions/triggers/listCallbackUrl/action|Hämtar motringnings-URL:en för utlösaren.|
|/ arbetsflöden och skrivning|Skapar eller uppdaterar arbetsflödet.|

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

| Åtgärd | Beskrivning |
|---|---|
|/commitmentPlans/commitmentAssociations/move/action|Flytta alla Machine Learning åtagande Plan Association|
|/commitmentPlans/commitmentAssociations/read|Läsa alla Machine Learning åtagande Plan Association|
|/commitmentPlans/delete|Ta bort en dator utbildningsplan åtagande|
|/commitmentPlans/JOIN/Action|Ansluta till en dator som utbildningsplan åtagande|
|/commitmentPlans/read|Läs valfri dator utbildningsplan åtagande|
|/commitmentPlans/write|Skapa eller uppdatera alla åtagande utbildningsplan datorn|
|/Locations/operationresults/Read|Hämta resultatet av en Machine Learning-åtgärd|
|/Locations/operationsstatus/Read|Hämta status för en pågående åtgärd i Machine Learning|
|/Operations/Read|Hämta åtgärder för Maskininlärning|
|registrera/åtgärd|Registrerar prenumerationen för maskininlärning web tjänstresursprovider och kan skapa webbtjänster.|
|/skus/Read|Hämta Machine Learning åtagande Plan SKU: er|
|/webServices/action|Skapa regionala egenskaper för webbtjänst för regioner som stöds|
|/webServices/delete|Ta bort alla Machine Learning-webbtjänst|
|/webServices/read|Läsa alla Machine Learning-webbtjänst|
|/webServices/write|Skapa eller uppdatera en Machine Learning-webbtjänst|
|/Workspaces/delete|Ta bort alla Maskininlärning arbetsytan|
|/ Arbetsytor/listworkspacekeys/åtgärd|Lista nycklar för en Machine Learning-arbetsytan|
|/ Arbetsytor/läsning|Läsa alla Machine Learning arbetsytan|
|/Workspaces/resyncstoragekeys/action|Synkronisera nycklar för lagringskonto har konfigurerats för en Machine Learning-arbetsytan|
|Och arbetsytor/skrivning|Skapa eller uppdatera alla Maskininlärning arbetsytan|

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

| Åtgärd | Beskrivning |
|---|---|
|/operationalizationClusters/checkUpdate/action|Kontrollera om det finns uppdateringar för systemtjänster för operationalization klustret|
|/operationalizationClusters/delete|Ta bort alla värd-konto|
|/operationalizationClusters/listKeys/action|Lista nycklarna som associerats med operationalization kluster|
|/operationalizationClusters/Read|Varje värd läskonto|
|/operationalizationClusters/updateSystem/action|Uppdatera systemtjänster i ett operationalization kluster|
|/operationalizationClusters/write|Skapa eller uppdatera värd-konto|
|registrera/åtgärd|Registrerar prenumerations-ID för resursprovidern och kan skapa machine learning beräkningsresurser|

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

| Åtgärd | Beskrivning |
|---|---|
|/Accounts/delete|Ta bort alla värd-konto|
|/Accounts/Read|Varje värd läskonto|
|/ konton/skrivning|Skapa eller uppdatera värd-konto|
|registrera/åtgärd|Registrerar prenumerations-ID för resursprovidern och kan skapa ett konto för värd|

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

| Åtgärd | Beskrivning |
|---|---|
|/userAssignedIdentities/assign/action|RBAC-åtgärden för att tilldela en befintlig användare som tilldelats en resurs identitet|
|/userAssignedIdentities/delete|Tar bort en befintlig användare som tilldelats identitet|
|/userAssignedIdentities/read|Hämtar en befintlig användare som tilldelats identitet|
|/userAssignedIdentities/write|Skapar en ny användare som tilldelats identitet eller uppdaterar taggarna associerade med en befintlig användare som tilldelats identitet|

## <a name="microsoftmanagedlab"></a>Microsoft.ManagedLab

| Åtgärd | Beskrivning |
|---|---|
|/labAccounts/CreateLab/action|Skapa ett labb i ett labb-konto.|
|/labAccounts/delete|Ta bort lab konton.|
|/labAccounts/Labs/delete|Ta bort övningar.|
|/labAccounts/Labs/environmentSettings/delete|Ta bort inställningen för miljön.|
|/labAccounts/Labs/environmentSettings/Environments/delete|Ta bort miljöer.|
|/labAccounts/Labs/environmentSettings/Environments/Read|Läsa miljöer.|
|/labAccounts/Labs/environmentSettings/Environments/Write|Lägg till eller ändra miljöer.|
|/labAccounts/Labs/environmentSettings/Read|Läsa miljöinställning.|
|/labAccounts/Labs/environmentSettings/Write|Lägg till eller ändra inställningen för miljön.|
|/labAccounts/Labs/labVms/delete|Ta bort virtuella datorer för testlabbet.|
|/labAccounts/Labs/labVms/Read|Läsa virtuella datorer för testlabbet.|
|/labAccounts/Labs/labVms/Write|Lägg till eller ändra virtuella datorer för testlabbet.|
|/labAccounts/Labs/Read|Läsa övningar.|
|/labAccounts/Labs/Write|Lägg till eller ändra övningar.|
|/labAccounts/read|Läsa lab konton.|
|/ labAccounts/skrivning|Lägg till eller ändra lab konton.|
|/Locations/Operations/Read|Läs-och skrivåtgärder.|
|registrera/åtgärd|Registrerar prenumerationen|

## <a name="microsoftmanagement"></a>Microsoft.Management

| Åtgärd | Beskrivning |
|---|---|
|/ checkNameAvailability/åtgärd|Kontrollerar om tillgänglighetsgruppens angivna namn är giltigt och unikt.|
|/ getEntities/åtgärd|Lista över alla enheter (Hanteringsgrupper, prenumerationer, etc.) för den autentiserade användaren.|
|/managementGroups/delete|Ta bort hanteringsgruppen.|
|/managementGroups/read|Lista över hanteringsgrupper för den autentiserade användaren.|
|/managementGroups/subscriptions/delete|Frigör associerar prenumeration från hanteringsgruppen.|
|/managementGroups/subscriptions/write|Associerats befintliga prenumeration till hanteringsgruppen.|
|/managementGroups/write|Skapa eller uppdatera en hanteringsgrupp.|

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

| Åtgärd | Beskrivning |
|---|---|
|/ClassicDevServices/delete|Utför en borttagning på en resurs för klassiska dev-tjänst.|
|/ ClassicDevServices/listSecrets/åtgärd|Hämtar en klassiska dev service resurs hantering av nycklar.|
|/ClassicDevServices/listSingleSignOnToken/action|Hämtar enkel inloggning på URL: en för en klassiska dev-tjänst.|
|/ClassicDevServices/read|Matchar en GET-åtgärd på en klassisk dev-tjänst.|
|/ClassicDevServices/regenerateKey/action|Genererar en klassiska dev service resurs hantering av nycklar.|
|/ Operations/läsning|Läs-och skrivåtgärder för alla typer av resurser.|

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

| Åtgärd | Beskrivning |
|---|---|
|/agreements/OFFERS/plans/Cancel/Action|Avbryt ett avtal för ett givet marketplace-objekt|
|/agreements/OFFERS/plans/Read|Returnera ett avtal för ett givet marketplace-objekt|
|/agreements/OFFERS/plans/Sign/Action|Registrera ett avtal för ett givet marketplace-objekt|
|/agreements/Read|Returnera alla avtal enligt angivna prenumerationen|
|/offertypes/Publishers/OFFERS/plans/agreements/Read|Hämta ett avtal för en given marketplace-objektet för virtuell dator|
|/offertypes/Publishers/OFFERS/plans/agreements/Write|Logga in till eller avbryta ett avtal för en given marketplace-objektet för virtuell dator|

## <a name="microsoftmedia"></a>Microsoft.Media

| Åtgärd | Beskrivning |
|---|---|
|/ checknameavailability/åtgärd|Kontrollerar om det finns ett Media Services-kontonamn|
|/mediaservices/delete|Ta bort alla Media Services-konto|
|/mediaservices/listKeys/Action|Visa en lista med ACS-nycklar för Media Services-konto|
|/mediaservices/Read|Läsa alla Media Services-konto|
|/mediaservices/regenerateKey/action|Återskapa ett Media Services ACS-nyckeln|
|/mediaservices/syncStorageKeys/action|Synkronisera Lagringsnycklar för ett anslutna Azure Storage-konto|
|/ mediaservices/skrivning|Skapa eller uppdatera alla Media Services-konto|
|/Operations/Read|Läsa alla Media Services-konto|
|registrera/åtgärd|Registrerar prenumerationen för resursprovidern Media Services och används för att skapa Media Services-konton|

## <a name="microsoftmigrate"></a>Microsoft.Migrate

| Åtgärd | Beskrivning |
|---|---|
|/ Operations/läsning|Läser de exponerade åtgärderna|

## <a name="microsoftnetwork"></a>Microsoft.Network

| Åtgärd | Beskrivning |
|---|---|
|/applicationGatewayAvailableSslOptions/predefinedPolicies/read|Programmet Gateway Ssl fördefinierade princip|
|/applicationGatewayAvailableSslOptions/read|Programmet Gateway Ssl alternativen|
|/applicationGatewayAvailableWafRuleSets/read|Hämtar tillgängliga brandvägg regeluppsättningar Programgateway|
|/applicationGateways/backendAddressPools/join/action|Ansluter en serverdelsadresspool för programmet gateway|
|/applicationGateways/backendhealth/action|Hämtar en programhälsan gateway backend|
|/applicationGateways/delete|Tar bort en Programgateway|
|/applicationGateways/effectiveNetworkSecurityGroups/action|Hämta routningstabell som konfigurerats på Programgateway|
|/applicationGateways/effectiveRouteTable/action|Hämta routningstabell som konfigurerats på Programgateway|
|/applicationGateways/providers/Microsoft.Insights/logDefinitions/read|Hämtar händelserna för Programgateway|
|/applicationGateways/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för Programgateway|
|/applicationGateways/Read|Hämtar en Programgateway|
|/applicationGateways/setSecurityCenterConfiguration/action|Anger programmet Gateway Security Center-konfiguration|
|/applicationGateways/start/action|Startar en Programgateway|
|/applicationGateways/stop/action|Stoppar en Programgateway|
|/applicationGateways/write|Skapar eller uppdaterar en Programgateway|
|/applicationSecurityGroups/delete|Tar bort en säkerhetsgrupp för programmet|
|/applicationSecurityGroups/joinIpConfiguration/action|Ansluter till en IP-konfiguration till säkerhetsgrupper för programmet.|
|/applicationSecurityGroups/joinNetworkSecurityRule/action|Kopplar ihop en säkerhetsregel säkerhetsgrupper för programmet.|
|/applicationSecurityGroups/read|Hämtar en säkerhetsgrupp för program-ID.|
|/applicationSecurityGroups/write|Skapar en säkerhetsgrupp för programmet eller uppdaterar en befintlig säkerhetsgrupp för programmet.|
|/bgpServiceCommunities/read|Hämta Bgp Service Communities|
|/checkTrafficManagerNameAvailability/action|Kontrollerar tillgängligheten för ett Traffic Manager relativt DNS-namn.|
|/Connections/delete|Deletes VirtualNetworkGatewayConnection|
|/Connections/Read|Gets VirtualNetworkGatewayConnection|
|/Connections/sharedkey/Action|Get VirtualNetworkGatewayConnection SharedKey|
|/Connections/sharedKey/Read|Hämtar VirtualNetworkGatewayConnection SharedKey|
|/Connections/sharedKey/Write|Skapar eller uppdaterar en befintlig VirtualNetworkGatewayConnection SharedKey|
|/connections/vpndeviceconfigurationscript/read|Hämtar Vpn-enhet konfigurationen av VirtualNetworkGatewayConnection|
|/ anslutningar/skrivning|Skapar eller uppdaterar en befintlig VirtualNetworkGatewayConnection|
|/ddosProtectionPlans/ddosProtectionPlanProxies/delete|Tar bort en DDoS-skydd Plan Proxy|
|/ddosProtectionPlans/ddosProtectionPlanProxies/read|Hämtar en definition av DDoS-skydd planera Proxy|
|/ddosProtectionPlans/ddosProtectionPlanProxies/write|Skapar en DDoS-skydd planera Proxy eller uppdateringar och befintliga DDoS-skydd planera Proxy|
|/ddosProtectionPlans/delete|Tar bort en Plan för DDoS-skydd|
|/ddosProtectionPlans/join/action|Ansluter till en Plan för DDoS-skydd|
|/ddosProtectionPlans/read|Hämtar en Plan för DDoS-skydd|
|/ddosProtectionPlans/write|Skapar en Plan för DDoS-skydd eller uppdaterar en Plan för DDoS-skydd |
|/dnsoperationresults/read|Hämtar resultat av en DNS-åtgärd|
|/dnsoperationstatuses/read|Hämtar status för en DNS-åtgärd |
|/dnszones/A/delete|Ta bort postuppsättningen för ett angivet namn och typen ”A” från en DNS-zon.|
|/dnszones/A/read|Hämta uppsättningen poster av typen ”A” i JSON-format. Uppsättningen poster innehåller en lista över poster samt TTL-värde, taggar och etag.|
|/dnszones/A/write|Skapa eller uppdatera en uppsättning poster av typen ”A” i en DNS-zon. De angivna posterna ersätter de aktuella posterna i uppsättningen av poster.|
|/dnszones/AAAA/delete|Ta bort postuppsättningen för ett angivet namn och typen AAAA om du från en DNS-zon.|
|/dnszones/AAAA/read|Hämta postuppsättningen av typen AAAA, i JSON-format. Uppsättningen poster innehåller en lista över poster samt TTL-värde, taggar och etag.|
|/dnszones/AAAA/write|Skapa eller uppdatera en uppsättning poster av typen 'AAAA' inom en DNS-zon. De angivna posterna ersätter de aktuella posterna i uppsättningen av poster.|
|/dnszones/all/read|Hämtar DNS-postuppsättningar över typer|
|/dnszones/CAA/delete|Ta bort postuppsättningen för ett angivet namn och typen CAA om du från en DNS-zon.|
|/dnszones/CAA/read|Hämta postuppsättningen av typen CAA, i JSON-format. Uppsättningen poster innehåller TTL-värde, taggar och etag.|
|/dnszones/CAA/write|Skapa eller uppdatera en uppsättning poster av typen 'CAA' inom en DNS-zon. De angivna posterna ersätter de aktuella posterna i uppsättningen av poster.|
|/dnszones/CNAME/delete|Ta bort postuppsättningen för ett angivet namn och typen CNAME om du från en DNS-zon.|
|/dnszones/CNAME/read|Hämta postuppsättningen av typen CNAME, i JSON-format. Uppsättningen poster innehåller TTL-värde, taggar och etag.|
|/dnszones/CNAME/write|Skapa eller uppdatera en uppsättning poster av typen 'CNAME' inom en DNS-zon. De angivna posterna ersätter de aktuella posterna i uppsättningen av poster.|
|/dnszones/delete|Ta bort DNS-zonen i JSON-format. Zonegenskaperna omfattar taggar, etag, numberOfRecordSets och maxNumberOfRecordSets.|
|/dnszones/MX/delete|Ta bort postuppsättningen för ett angivet namn och typen MX om du från en DNS-zon.|
|/dnszones/MX/read|Hämta postuppsättningen av typen MX, i JSON-format. Uppsättningen poster innehåller en lista över poster samt TTL-värde, taggar och etag.|
|/dnszones/MX/write|Skapa eller uppdatera en uppsättning poster av typen 'MX' inom en DNS-zon. De angivna posterna ersätter de aktuella posterna i uppsättningen av poster.|
|/dnszones/NS/delete|Tar bort DNS-uppsättningen av poster av typen NS|
|/dnszones/NS/read|Hämtar uppsättning av DNS-poster av typen NS|
|/dnszones/NS/write|Skapar eller uppdaterar DNS-postuppsättning av typen NS|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar diagnostikinställningar för DNS-zonen|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningar för DNS-zonen|
|/dnszones/providers/Microsoft.Insights/metricDefinitions/read|Hämtar måttdefinitionerna för DNS-zonen|
|/dnszones/PTR/delete|Ta bort postuppsättningen för ett angivet namn och typen PTR om du från en DNS-zon.|
|/dnszones/PTR/read|Hämta postuppsättningen av typen PTR, i JSON-format. Uppsättningen poster innehåller en lista över poster samt TTL-värde, taggar och etag.|
|/dnszones/PTR/write|Skapa eller uppdatera en uppsättning poster av typen 'PTR' inom en DNS-zon. De angivna posterna ersätter de aktuella posterna i uppsättningen av poster.|
|/dnszones/read|Hämta DNS-zonen i JSON-format. Zonegenskaperna omfattar taggar, etag, numberOfRecordSets och maxNumberOfRecordSets. Observera att det här kommandot inte hämtar postuppsättningar i zonen.|
|/dnszones/recordsets/read|Hämtar DNS-postuppsättningar över typer|
|/dnszones/SOA/read|Hämtar uppsättning av DNS-poster av typen SOA|
|/dnszones/SOA/write|Skapar eller uppdaterar DNS-postuppsättning av typen SOA|
|/dnszones/SRV/delete|Ta bort postuppsättningen för ett angivet namn och typen SRV om du från en DNS-zon.|
|/dnszones/SRV/read|Hämta postuppsättningen av typen SRV, i JSON-format. Uppsättningen poster innehåller en lista över poster samt TTL-värde, taggar och etag.|
|/dnszones/SRV/write|Skapa eller uppdatera en postuppsättning av typen SRV|
|/dnszones/TXT/delete|Ta bort postuppsättningen för ett angivet namn och typen TXT om du från en DNS-zon.|
|/dnszones/TXT/read|Hämta postuppsättningen av typen TXT, i JSON-format. Uppsättningen poster innehåller en lista över poster samt TTL-värde, taggar och etag.|
|/dnszones/TXT/write|Skapa eller uppdatera en uppsättning poster av typen 'TXT' inom en DNS-zon. De angivna posterna ersätter de aktuella posterna i uppsättningen av poster.|
|/dnszones/write|Skapa eller uppdatera en DNS-zon i en resursgrupp.  Används för att uppdatera taggarna i en DNS-resursposter i zonen. Observera att det här kommandot inte kan användas för att skapa eller uppdatera postuppsättningar i zonen.|
|/expressRouteCircuits/authorizations/delete|Tar bort ExpressRouteCircuit tillstånd|
|/expressRouteCircuits/authorizations/read|Hämtar ExpressRouteCircuit tillstånd|
|/expressRouteCircuits/authorizations/write|Skapar eller uppdaterar en befintlig ExpressRouteCircuit-auktorisering|
|/expressRouteCircuits/delete|Tar bort en ExpressRouteCircuit|
|/expressRouteCircuits/peerings/arpTables/action|Hämtar en ExpressRouteCircuit Peering ArpTable|
|/expressRouteCircuits/peerings/connections/delete|Tar bort en ExpressRouteCircuit-anslutning|
|/expressRouteCircuits/peerings/Connections/Read|Hämtar en ExpressRouteCircuit-anslutning|
|/expressRouteCircuits/peerings/connections/write|Skapar eller uppdaterar en befintlig ExpressRouteCircuit anslutning resurs|
|/expressRouteCircuits/peerings/delete|Tar bort en ExpressRouteCircuit Peering|
|/expressRouteCircuits/peerings/Read|Hämtar en ExpressRouteCircuit Peering|
|/expressRouteCircuits/peerings/routeTables/action|Hämtar en ExpressRouteCircuit Peering Migreringstillståndet|
|/expressRouteCircuits/peerings/routeTablesSummary/action|Hämtar en ExpressRouteCircuit Peering Migreringstillståndet sammanfattning|
|/expressRouteCircuits/peerings/stats/read|Hämtar en ExpressRouteCircuit Peering Stat|
|/expressRouteCircuits/peerings/write|Skapar eller uppdaterar en befintlig ExpressRouteCircuit Peering|
|/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar diagnostikinställningar för ExpressRoute-kretsar|
|/expressRouteCircuits/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningar för ExpressRoute-kretsar|
|/expressRouteCircuits/providers/Microsoft.Insights/logDefinitions/read|Hämta händelser för ExpressRoute-kretsar|
|/expressRouteCircuits/providers/Microsoft.Insights/metricDefinitions/read|Hämtar mått definitioner för ExpressRoute-kretsar|
|/expressRouteCircuits/read|Hämta en ExpressRouteCircuit|
|/expressRouteCircuits/stats/read|Hämtar en ExpressRouteCircuit Stat|
|/expressRouteCircuits/write|Skapar eller uppdaterar en befintlig ExpressRouteCircuit|
|/expressRouteCrossConnections/delete|Ta bort Expressroute mellan anslutning|
|/expressRouteCrossConnections/join/action|Ansluter till en Express Route mellan anslutning|
|/expressRouteCrossConnections/peerings/arpTables/action|Hämtar en Express Route gränsöverskridande anslutning Peering Arp-tabell|
|/expressRouteCrossConnections/peerings/delete|Tar bort en Express Route gränsöverskridande anslutning Peering|
|/expressRouteCrossConnections/peerings/read|Hämtar en Express Route gränsöverskridande anslutning Peering|
|/expressRouteCrossConnections/peerings/routeTables/action|Hämtar en Express Route gränsöverskridande anslutning Peering routningstabellen|
|/expressRouteCrossConnections/peerings/routeTableSummary/action|Hämtar en Express Route gränsöverskridande anslutning Peering väg tabellsammanfattning|
|/expressRouteCrossConnections/peerings/stats/read|Hämtar en Express Route gränsöverskridande anslutning Peering Stat|
|/expressRouteCrossConnections/peerings/write|Skapar en mellan anslutning Peering i Expressroute eller uppdaterar en befintlig mellan anslutning Peering i Expressroute|
|/expressRouteCrossConnections/read|Hämta Express Route mellan anslutning|
|/expressRouteCrossConnections/write|Skapa eller uppdatera Express Route mellan anslutning|
|/expressRouteServiceProviders/read|Hämtar Express Route-leverantörer|
|/loadBalancers/backendAddressPools/join/action|Ansluter till en belastningsutjämnare adress serverdelspool|
|/loadBalancers/backendAddressPools/read|Hämtar en load balancer definition av serverdelsadresspool|
|/loadBalancers/delete|Tar bort en belastningsutjämnare|
|/loadBalancers/frontendIPConfigurations/read|Hämtar en klientdel IP-konfiguration belastningsutjämnardefinition|
|/loadBalancers/inboundNatPools/join/action|Ansluter till en belastningsutjämnare inkommande nat-pool|
|/loadBalancers/inboundNatPools/read|Hämtar en belastningsutjämnare definition för inkommande nat-pool|
|/loadBalancers/inboundNatRules/delete|Tar bort en inkommande nat-regel för belastningsutjämnare|
|/loadBalancers/inboundNatRules/JOIN/Action|Ansluter en inkommande nat-regel för belastningsutjämnare|
|/loadBalancers/inboundNatRules/Read|Hämtar en belastningsutjämnare definition för inkommande nat-regel|
|/loadBalancers/inboundNatRules/Write|Skapar en inkommande nat-regel för belastningsutjämnare eller uppdaterar en befintlig inkommande nat-regel för belastningsutjämnare|
|/loadBalancers/loadBalancingRules/Read|Hämtar en belastningsutjämnare belastningsutjämnardefinition av belastningsutjämningsregel|
|/loadBalancers/networkInterfaces/read|Hämtar hänvisningar till alla nätverksgränssnitt under en belastningsutjämnare|
|/loadBalancers/outboundNatRules/Read|Hämtar en utgående nat-regel belastningsutjämnardefinition|
|/loadBalancers/probes/JOIN/Action|Kan använda avsökningar av belastningsutjämning. Med den här behörigheten healthProbe egenskapen VM scale kan set referera exempelvis avsökningen.|
|/loadBalancers/probes/Read|Hämtar en belastningsutjämningsavsökning|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar de diagnostiska inställningarna för belastningsutjämnaren|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningar för belastningen belastningsutjämnare|
|/loadBalancers/providers/Microsoft.Insights/logDefinitions/read|Hämtar händelserna för belastningsutjämnare|
|/loadBalancers/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för belastningsutjämnare|
|/loadBalancers/Read|Hämtar en belastningsutjämnardefinition|
|/loadBalancers/virtualMachines/Read|Hämtar hänvisningar till alla virtuella datorer under en belastningsutjämnare|
|/ loadBalancers/skrivning|Skapar en belastningsutjämnare eller uppdaterar en befintlig belastningsutjämnare|
|/localnetworkgateways/delete|Deletes LocalNetworkGateway|
|/localnetworkgateways/read|Gets LocalNetworkGateway|
|/ localnetworkgateways/skrivning|Skapar eller uppdaterar en befintlig LocalNetworkGateway|
|/Locations/checkDnsNameAvailability/Read|Kontrollerar om dns-etikett finns på den angivna platsen|
|/Locations/operationResults/Read|Hämtar resultat av en asynkron post- eller DELETE-åtgärden|
|/Locations/Operations/Read|Hämtar åtgärdsresurs motsvarande status för en asynkron åtgärd|
|/Locations/usages/Read|Hämtar resurserna användningsstatistik|
|/locations/virtualNetworkAvailableEndpointServices/read|Hämtar en lista med tillgängliga virtuella Endpoint nätverkstjänster|
|/networkInterfaces/delete|Tar bort ett nätverksgränssnitt|
|/networkInterfaces/diagnosticIdentity/read|Hämtar diagnostiska identiteten för resursen|
|/networkInterfaces/effectiveNetworkSecurityGroups/action|Hämta Nätverkssäkerhetsgrupp konfigurerade på nätverket gränssnitt för den virtuella datorns|
|/networkInterfaces/effectiveRouteTable/action|Hämta routningstabell som konfigurerats på nätverksgränssnitt för den virtuella datorn|
|/networkInterfaces/ipconfigurations/read|Hämtar en IP-konfigurationsdefinition av nätverksgränssnitt. |
|/networkInterfaces/join/action|Ansluter en virtuell dator till ett nätverksgränssnitt|
|/networkInterfaces/loadBalancers/read|Hämtar alla belastningsutjämnare som nätverksgränssnittet tillhör|
|/networkInterfaces/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för nätverksgränssnittet|
|/networkInterfaces/read|Hämtar en definition av nätverksgränssnitt. |
|/networkInterfaces/write|Skapar ett nätverksgränssnitt eller uppdaterar en befintlig nätverksgränssnitt. |
|/networkSecurityGroups/defaultSecurityRules/read|Hämtar en definition av säkerhetsregel standard|
|/networkSecurityGroups/delete|Tar bort en nätverkssäkerhetsgrupp|
|/networkSecurityGroups/join/action|Ansluter en nätverkssäkerhetsgrupp|
|/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar de nätverket diagnostikinställningar för Nätverkssäkerhetsgrupper|
|/networksecuritygroups/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningar för Nätverkssäkerhetsgrupper åtgärden kompletteras av resursprovidern.|
|/networksecuritygroups/providers/Microsoft.Insights/logDefinitions/read|Hämtar händelserna för nätverkssäkerhetsgrupp|
|/networkSecurityGroups/read|Hämtar en definition av nätverkssäkerhetsgrupp|
|/networkSecurityGroups/securityRules/delete|Tar bort en säkerhetsregel|
|/networkSecurityGroups/securityRules/read|Hämtar en definition av säkerhetsregel|
|/networkSecurityGroups/securityRules/write|Skapar en säkerhetsregel eller uppdaterar en befintlig säkerhetsregel|
|/networkSecurityGroups/write|Skapar en nätverkssäkerhetsgrupp eller uppdaterar en befintlig nätverkssäkerhetsgrupp|
|/networkWatchers/availableProvidersList/action|Returnerar alla tillgängliga internet-leverantörer för angivna Azure-region.|
|/networkWatchers/azureReachabilityReport/action|Returnerar relativa latens poängen för internet-leverantörer från en angiven plats till Azure-regioner.|
|/networkWatchers/configureFlowLog/action|Konfigurerar flöde loggning för en målresurs.|
|/networkWatchers/connectionMonitors/delete|Tar bort en anslutning Övervakare|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ diagnosticSettings/läsning|Hämta diagnostikinställningar för övervakaren anslutning|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ diagnosticSettings/skrivning|Skapar eller uppdaterar diagnostikinställningar för anslutningen Övervakaren|
|/networkWatchers/connectionMonitors/providers/Microsoft.Insights/ metricDefinitions/läsning|Hämtar tillgängliga mått för övervakaren anslutning|
|/networkWatchers/connectionMonitors/query/action|Frågan övervaka anslutningar mellan angivna slutpunkter|
|/networkWatchers/connectionMonitors/read|Hämta information om övervakaren anslutning|
|/networkWatchers/connectionMonitors/start/action|Börja övervaka anslutningen mellan angivna slutpunkter|
|/networkWatchers/connectionMonitors/stop/action|Stoppa och pausa övervaka anslutningar mellan angivna slutpunkter|
|/networkWatchers/connectionMonitors/write|Skapar en anslutning Övervakare|
|/networkWatchers/connectivityCheck/action|Verifierar möjligheten att upprätta en direkt TCP-anslutning från en virtuell dator till en viss slutpunkten, inklusive en annan virtuell dator eller en godtycklig fjärrserver.|
|/networkWatchers/delete|Tar bort en nätverksbevakaren|
|/networkWatchers/ipFlowVerify/action|Returnerar om paketet tillåts eller nekas till eller från en viss destination.|
|/networkWatchers/lenses/delete|Tar bort en lins|
|/networkWatchers/lenses/query/action|Frågan övervaka nätverkstrafik på den angivna slutpunkten|
|/networkWatchers/lenses/read|Hämta lins information|
|/networkWatchers/lenses/start/action|Börja övervaka nätverkstrafik på den angivna slutpunkten|
|/networkWatchers/lenses/stop/action|Stoppa och pausa övervaka nätverkstrafik på den angivna slutpunkten|
|/networkWatchers/lenses/write|Skapar en lins|
|/networkWatchers/nextHop/action|Returnerar nästa hopptyp och nästa hoppas IP-adress för en angiven mål- och mål-IP-adress.|
|/networkWatchers/packetCaptures/delete|Tar bort en paketinsamling|
|/networkWatchers/packetCaptures/queryStatus/action|Hämtar information om egenskaper och status för en resurs för avbildningen av paketet.|
|/networkWatchers/packetCaptures/read|Hämta paket avbilda definition|
|/networkWatchers/packetCaptures/stop/action|Stoppa körs paket avbildningssessionen.|
|/networkWatchers/packetCaptures/write|Skapar en paketinsamling|
|/networkWatchers/queryFlowLogStatus/action|Hämtar status för flöde loggning på en resurs.|
|/networkWatchers/queryTroubleshootResult/Action|Felsökning resultatet från körts tidigare eller för närvarande hämtar kör felsökning igen.|
|/networkWatchers/read|Hämta definition för Övervakare nätverk|
|/networkWatchers/securityGroupView/action|Visa de konfigurerade och effektivt regler för nätverkssäkerhetsgrupper tillämpas på en virtuell dator.|
|/networkWatchers/topology/action|Hämtar en nätverket vy på protokollnivå för resurser och deras relationer i en resursgrupp.|
|/networkWatchers/troubleshoot/action|Startar felsökning på en resurs för nätverk i Azure.|
|/networkWatchers/write|Skapar en nätverksbevakaren eller uppdaterar en befintlig nätverksbevakaren|
|/Operations/Read|Hämta tillgängliga åtgärder|
|/publicIPAddresses/delete|Tar bort en offentlig Ip-adress.|
|/publicIPAddresses/join/action|Ansluter till en offentlig ip-adress|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/read|Hämta diagnostikinställningar för offentlig IP-adress|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/write|Skapa eller uppdatera diagnostikinställningar för offentlig IP-adress|
|/publicIPAddresses/providers/Microsoft.Insights/logDefinitions/read|Hämta Loggdefinitioner för offentlig IP-adress|
|/publicIPAddresses/providers/Microsoft.Insights/metricDefinitions/read|Hämta mätvärden definitionerna av offentlig IP-adress|
|/publicIPAddresses/read|Hämtar en definition av offentlig ip-adress.|
|/publicIPAddresses/write|Skapar en offentlig Ip-adress eller uppdaterar en befintlig offentlig Ip-adress. |
|registrera/åtgärd|Registrerar prenumerationen|
|/routeFilters/delete|Tar bort en väg filterdefinition|
|/routeFilters/JOIN/Action|Ansluter till en vägfilter|
|/routeFilters/Read|Hämtar en definition av flödet filter|
|/routeFilters/routeFilterRules/delete|Tar bort en regel för vägen filterdefinition|
|/routeFilters/routeFilterRules/read|Hämtar en regel för vägen filterdefinition|
|/routeFilters/routeFilterRules/write|Skapar en regel för vägen filter eller uppdaterar en befintlig väg filter regel|
|/routeFilters/write|Skapar ett vägfilter eller uppdaterar ett befintligt rotue filter|
|/routeTables/delete|Tar bort en definition av routningstabell|
|/routeTables/JOIN/Action|Ansluter till en routningstabell|
|/routeTables/Read|Hämtar en definition av routningstabell|
|/routeTables/routes/delete|Tar bort en väg definition|
|/routeTables/routes/Read|Hämtar en definition av väg|
|/routeTables/routes/Write|Skapar en väg eller uppdaterar en befintlig|
|/ routeTables/skrivning|Skapar en routningstabell eller uppdaterar en befintlig tabell rotue|
|/securegateways/applicationRuleCollections/delete|Tar bort en regelsamling för programmet för säker Gateway|
|/securegateways/applicationRuleCollections/read|Hämta en regelsamling för programmet för en viss säker Gateway|
|/securegateways/applicationRuleCollections/write|Skapar eller uppdaterar en regelsamling för programmet för en säker Gateway|
|/securegateways/delete|Ta bort säkra Gateway|
|/securegateways/networkRuleCollections/delete|Tar bort en regelsamling för nätverket för en säker Gateway|
|/securegateways/networkRuleCollections/read|Hämta en regelsamling för nätverket för en given säker Gateway|
|/securegateways/networkRuleCollections/write|Skapar eller uppdaterar en regelsamling för nätverket för en säker Gateway|
|/securegateways/Read|Få säker Gateway|
|/ securegateways/skrivning|Skapar eller uppdaterar en säker Gateway|
|/serviceEndpointPolicies/delete|Tar bort en slutpunkt för för tjänsten|
|/serviceEndpointPolicies/join/action|Ansluter till en slutpunkt för för tjänsten|
|/serviceEndpointPolicies/joinSubnet/action|Ansluter till ett undernät till-slutpunkt-principer|
|/serviceEndpointPolicies/read|Hämtar en tjänstbeskrivning Endpoint princip|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/delete|Tar bort en Principdefinition för Service-slutpunkt|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/read|Hämtar en tjänsten Endpoint princip Definition beskrivning|
|/serviceEndpointPolicies/serviceEndpointPolicyDefinitions/write|Skapar en Principdefinition för tjänsten Endpoint eller uppdaterar en befintlig Principdefinition för tjänsten slutpunkt|
|/serviceEndpointPolicies/write|Skapar en slutpunkt för för tjänsten eller uppdaterar en befintlig Service Endpoint princip|
|/trafficManagerGeographicHierarchies/read|Hämtar Traffic Manager geografiska hierarkin som innehåller områden som kan användas med geografisk trafikroutningsmetod|
|/trafficManagerProfiles/azureEndpoints/delete|Tar bort en Azure-slutpunkt från en befintlig Traffic Manager-profil. Traffic Manager stoppas dirigera trafiken till den Azure slutpunkt har tagits bort.|
|/trafficManagerProfiles/azureEndpoints/read|Hämtar en Azure-slutpunkt som tillhör en Traffic Manager-profilen, inklusive alla egenskaper för den Azure-slutpunkten.|
|/trafficManagerProfiles/azureEndpoints/write|Lägg till en ny Azure-slutpunkt i en befintlig Traffic Manager-profil eller uppdatera egenskaperna för en befintlig Azure-slutpunkt i Traffic Manager profilen.|
|/trafficManagerProfiles/delete|Ta bort Traffic Manager-profilen. Alla inställningar som är kopplade till trafikhanterarprofilen försvinner och profilen kan inte längre användas för att dirigera trafik.|
|/trafficManagerProfiles/externalEndpoints/delete|Tar bort en extern slutpunkt från en befintlig Traffic Manager-profil. Traffic Manager stoppas dirigera trafiken till den externa slutpunkt har tagits bort.|
|/trafficManagerProfiles/externalEndpoints/read|Hämtar en extern slutpunkt som tillhör en Traffic Manager-profilen, inklusive alla egenskaper för den externa slutpunkten.|
|/trafficManagerProfiles/externalEndpoints/write|Lägg till en ny extern slutpunkt i en befintlig Traffic Manager-profil eller uppdatera egenskaperna för en befintlig extern slutpunkt i Traffic Manager profilen.|
|/trafficManagerProfiles/heatMaps/read|Hämtar Traffic Manager termisk karta för den angivna Traffic Manager-profil som innehåller antalet och fördröjning frågedata av plats- och käll-IP.|
|/trafficManagerProfiles/nestedEndpoints/delete|Tar bort en slutpunkt för kapslade från en befintlig Traffic Manager-profil. Traffic Manager stoppas dirigera trafiken till borttagna kapslade slutpunkten.|
|/trafficManagerProfiles/nestedEndpoints/read|Hämtar en kapslad slutpunkt som tillhör en Traffic Manager-profilen, inklusive alla egenskaper för den kapslade slutpunkten.|
|/trafficManagerProfiles/nestedEndpoints/write|Lägg till en ny kapslade slutpunkt i en befintlig Traffic Manager-profil eller uppdatera egenskaperna för en befintlig kapslade slutpunkt i Traffic Manager profilen.|
|/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar inställningar för Traffic Manager-diagnostik|
|/trafficManagerProfiles/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningar för Traffic Manager åtgärden kompletteras av resursprovidern för insights.|
|/trafficManagerProfiles/providers/Microsoft.Insights/logDefinitions/read|Hämtar händelserna för Traffic Manager|
|/trafficManagerProfiles/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för Traffic Manager.|
|/trafficManagerProfiles/read|Hämta profilkonfigurationen för Traffic Manager. Detta inkluderar DNS-inställningar, trafikdirigeringsinställningar, slutpunktsövervakningsinställningar och listan över slutpunkter som dirigeras av den här trafikhanterarprofilen.|
|/trafficManagerProfiles/write|Skapa en trafikhanterarprofil eller ändra konfigurationen för en befintlig Traffic Manager-profil. Detta inkluderar att aktivera eller inaktivera en profil och ändra DNS-inställningar, trafikdirigeringsinställningar och slutpunktsövervakningsinställningar. Slutpunkter som dirigeras av trafikhanterarprofilen kan läggas till, tas bort, aktiveras eller inaktiveras.|
|/trafficManagerUserMetricsKeys/delete|Tar bort en prenumerationsnivån nyckel som används för realtid användaren mått samling.|
|/trafficManagerUserMetricsKeys/read|Hämtar-prenumerationsnivån-nyckel som används för realtid användaren mått samling.|
|/trafficManagerUserMetricsKeys/write|Skapar en ny prenumeration på objektnivå nyckel som ska användas för realtid användaren mått samling.|
|/unregister/action|Avregistrerar prenumerationen|
|/virtualHubs/delete|Tar bort en virtuell-hubb|
|/virtualHubs/hubVirtualNetworkConnections/delete|Tar bort en HubVirtualNetworkConnection|
|/virtualHubs/hubVirtualNetworkConnections/read|Hämta en HubVirtualNetworkConnection|
|/virtualHubs/hubVirtualNetworkConnections/write|Skapa eller uppdatera en HubVirtualNetworkConnection|
|/virtualHubs/Read|Hämta en virtuell-hubb|
|/virtualHubs/write|Skapa eller uppdatera en virtuell-hubb|
|/virtualnetworkgateways/connections/read|Get VirtualNetworkGatewayConnection|
|/virtualNetworkGateways/delete|Tar bort en virtualNetworkGateway|
|/virtualnetworkgateways/generatevpnclientpackage/action|Generera VpnClient paketet för virtualNetworkGateway|
|/virtualnetworkgateways/generatevpnprofile/action|Generera VpnProfile paketet för VirtualNetworkGateway|
|/virtualnetworkgateways/getadvertisedroutes/action|Hämtar virtualNetworkGateway annonserade vägar|
|/virtualnetworkgateways/getbgppeerstatus/Action|Hämtar virtualNetworkGateway bgp peer-status|
|/virtualnetworkgateways/getlearnedroutes/Action|Hämtar virtualnetworkgateway inlärda vägar|
|/virtualnetworkgateways/getvpnclientipsecparameters/action|Hämta Vpnclient Ipsec parametrar för VirtualNetworkGateway P2S-klienten.|
|/virtualnetworkgateways/getvpnprofilepackageurl/action|Hämtar URL för en profil för förgenererade VPN-klientpaketet|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar diagnostikinställningar för virtuellt nätverk Gateway|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningar för virtuell nätverksgateway åtgärden kompletteras av resursprovidern för insights.|
|/virtualNetworkGateways/providers/Microsoft.Insights/logDefinitions/read|Hämtar händelserna för virtuell nätverksgateway|
|/virtualNetworkGateways/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för virtuell nätverksgateway|
|/virtualNetworkGateways/read|Hämtar en VirtualNetworkGateway|
|/virtualnetworkgateways/Reset/Action|Återställer en virtualNetworkGateway|
|/virtualnetworkgateways/setvpnclientipsecparameters/action|Ange Vpnclient Ipsec-parametrarna för VirtualNetworkGateway P2S-klienten.|
|/virtualnetworkgateways/supportedvpndevices/action|Visar VPN-enheter som stöds|
|/virtualNetworkGateways/write|Skapar eller uppdaterar en VirtualNetworkGateway|
|/virtualNetworks/checkIpAddressAvailability/read|Kontrollera om Ip-adress är tillgänglig på det angivna virtuella nätverket|
|/virtualNetworks/customViews/get/action|Hämta en anpassad vy innehåll för virtuellt nätverk|
|/virtualNetworks/customViews/read|Hämta definition av en anpassad vy av virtuellt nätverk|
|/virtualNetworks/delete|Tar bort ett virtuellt nätverk|
|/virtualNetworks/peer/Action|Peers ett virtuellt nätverk med ett annat virtuellt nätverk|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/read|Hämta diagnostikinställningar för virtuellt nätverk|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/write|Skapa eller uppdatera diagnostikinställningar för det virtuella nätverket|
|/virtualNetworks/providers/Microsoft.Insights/logDefinitions/read|Hämta Loggdefinitioner för virtuellt nätverk|
|/virtualNetworks/providers/Microsoft.Insights/metricDefinitions/read|Hämta mått definitionerna av virtuellt nätverk|
|/virtualNetworks/Read|Hämta definitionen av virtuellt nätverk|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete|Tar bort ett virtuellt nätverk peering-proxy|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/read|Hämtar ett virtuellt nätverk peering proxydefinitioner|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/write|Skapar en peering proxy för virtuellt nätverk eller uppdaterar en befintlig peering proxy för virtuellt nätverk|
|/virtualNetworks/subnets/delete|Tar bort ett undernät för virtuellt nätverk|
|/virtualNetworks/Subnets/JOIN/Action|Ansluter till ett virtuellt nätverk|
|/virtualNetworks/subnets/joinViaServiceEndpoint/action|Ansluter till resursen, till exempel lagringskonto eller SQL-databas till ett undernät.|
|/virtualNetworks/subnets/read|Hämtar en definition av undernät för virtuellt nätverk|
|/virtualNetworks/subnets/resourceNavigationLinks/delete|Tar bort en resurs navigeringslänk|
|/virtualNetworks/subnets/resourceNavigationLinks/read|Hämta definitionen för resurslänken för navigering|
|/virtualNetworks/subnets/resourceNavigationLinks/write|Skapar en Resurslänk navigering eller uppdaterar en befintlig resurs navigeringslänk|
|/virtualNetworks/subnets/virtualMachines/read|Hämtar hänvisningar till alla virtuella datorer i ett undernät för virtuellt nätverk|
|/virtualNetworks/Subnets/Write|Skapar ett undernät för virtuellt nätverk eller uppdaterar ett befintligt undernät för virtuellt nätverk|
|/virtualNetworks/taggedTrafficConsumers/delete|Tar bort en konsument taggade trafik|
|/virtualNetworks/taggedTrafficConsumers/read|Hämta märkta trafik konsument-definition|
|/virtualNetworks/taggedTrafficConsumers/validate/action|Verifierar taggade trafik konsumenter|
|/virtualNetworks/taggedTrafficConsumers/write|Skapar taggade trafik konsumenter eller uppdaterar en befintlig märkta trafik konsumenten|
|/virtualNetworks/usages/read|Hämta IP-användningsområden för varje undernät i det virtuella nätverket|
|/virtualNetworks/virtualMachines/read|Hämtar hänvisningar till alla virtuella datorer i ett virtuellt nätverk|
|/virtualNetworks/virtualNetworkPeerings/delete|Tar bort ett virtuellt nätverk som peering|
|/virtualNetworks/virtualNetworkPeerings/Read|Hämtar en definition av peering virtuellt nätverk|
|/virtualNetworks/virtualNetworkPeerings/write|Skapar en peering virtuellt nätverk eller uppdaterar ett befintligt virtuellt nätverk peering|
|/ virtualNetworks/skrivning|Skapar ett virtuellt nätverk eller uppdaterar ett befintligt virtuellt nätverk|
|/virtualNetworkTaps/delete|Ta bort tryck för virtuellt nätverk|
|/virtualNetworkTaps/JOIN/Action|Ansluter till ett virtuellt nätverk tryck|
|/virtualNetworkTaps/read|Hämta tryck för virtuellt nätverk|
|/ virtualNetworkTaps/skrivning|Skapa eller uppdatera tryck för virtuellt nätverk|
|/virtualwans/delete|Tar bort en virtuell Wan|
|/virtualwans/Read|Hämta en virtuell Wan|
|/virtualWans/virtualHubProxies/delete|Tar bort en virtuell Hub-proxy|
|/virtualWans/virtualHubProxies/read|Hämtar en definition av virtuella hubb proxy|
|/virtualWans/virtualHubProxies/write|Skapar en virtuell hubb proxy eller uppdaterar en virtuell Hub-proxy|
|/virtualwans/virtualHubs/read|Hämtar alla virtuella hubbar som är kopplade till virtuella Wan.|
|/virtualwans/vpnconfiguration/read|Hämtar en Vpn-konfiguration|
|/virtualWans/vpnSiteProxies/delete|Tar bort platsen för Vpn-proxy|
|/virtualWans/vpnSiteProxies/read|Hämtar en definition av platsen för Vpn-proxy|
|/virtualWans/vpnSiteProxies/write|Skapar en platsen för Vpn-proxy eller uppdaterar en platsen för Vpn-proxy|
|/virtualwans/vpnSites/read|Hämtar alla VPN-platser som är kopplade till virtuella Wan.|
|/ virtualwans/skrivning|Skapa eller uppdatera virtuella Wan|
|/vpnGateways/read|Hämtar en VpnGateway.|
|/vpnGateways/vpnConnections/read|Hämtar en VpnConnection.|
|/vpnGateways/vpnConnections/write|Placerar en VpnConnection.|
|/vpnGateways/write|Placerar en VpnGateway.|
|/vpnsites/delete|Tar bort en platsen för Vpn-resurs.|
|/vpnsites/Read|Hämtar en platsen för Vpn-resurs.|
|/ vpnsites/skrivning|Skapar eller uppdaterar en platsen för Vpn-resurs.|

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

| Åtgärd | Beskrivning |
|---|---|
|/ CheckNamespaceAvailability/åtgärd|Kontrollerar huruvida ett visst namnområdesresursnamn finns i NotificationHub-tjänsten.|
|/ Namnområden/authorizationRules/åtgärd|Hämta listan över beskrivning av auktoriseringsregler för namnområden.|
|/Namespaces/authorizationRules/delete|Ta bort Namespace auktoriseringsregeln. Auktoriseringsregeln standard Namespace kan inte tas bort. |
|/Namespaces/authorizationRules/listkeys/action|Hämta anslutningssträngen till namnområdet|
|/ Namnområden/authorizationRules/läsning|Hämta listan över beskrivning av auktoriseringsregler för namnområden.|
|/Namespaces/authorizationRules/regenerateKeys/action|Auktoriseringsregel för namnområde: återskapa primär/sekundär nyckel. Ange den nyckel som behöver återskapas|
|/Namespaces/authorizationRules/write|Skapa en nivå auktoriseringsregler Namespace och uppdatera dess egenskaper. Åtkomstbehörigheter för regler för auktorisering, primära och sekundärnycklar kan uppdateras.|
|/Namespaces/CheckNotificationHubAvailability/action|Kontrollerar huruvida ett visst NotificationHub-namn finns i ett namnområde.|
|/Namespaces/Delete|Ta bort namnområdesresurs|
|/Namespaces/NotificationHubs/authorizationRules/action|Hämta listan över auktoriseringsregler för Notification Hub|
|/Namespaces/NotificationHubs/authorizationRules/delete|Ta bort auktoriseringsregler för Notification Hub|
|/Namespaces/NotificationHubs/authorizationRules/listkeys/action|Hämta anslutningssträngen till meddelandehubben|
|/Namespaces/NotificationHubs/authorizationRules/read|Hämta listan över auktoriseringsregler för Notification Hub|
|/Namespaces/NotificationHubs/authorizationRules/regenerateKeys/action|Auktoriseringsregel för Notification Hub: återskapa primär/sekundär nyckel. Ange den nyckel som behöver återskapas|
|/Namespaces/NotificationHubs/authorizationRules/write|Skapa auktoriseringsregler för Notification Hub och uppdatera dess egenskaper. Åtkomstbehörigheter för regler för auktorisering, primära och sekundärnycklar kan uppdateras.|
|/Namespaces/NotificationHubs/debugSend/action|Skicka ett push-testmeddelande.|
|/ Namnområden/NotificationHubs/ta bort|Ta bort Notification Hub-resurs|
|/ Namnområden/NotificationHubs/metricDefinitions/läsning|Hämta lista över Namespace mått resurs beskrivningar|
|/Namespaces/NotificationHubs/pnsCredentials/action|Hämta alla Notification Hub PNS-autentiseringsuppgifter. Detta omfattar WNS, MPNS, APN, GCM och Baidu autentiseringsuppgifter|
|/ Namnområden/NotificationHubs/läsning|Hämta listan över resursbeskrivningar av Notification Hub|
|/Namespaces/NotificationHubs/write|Skapa en Meddelandehubb och uppdatera dess egenskaper. Egenskaperna är främst PNS-autentiseringsuppgifter. Auktoriseringsregler och TTL-värde|
|/ Namnområden/läsning|Hämta listan över beskrivningar av namnområdesresurs|
|Och namnområden/skrivning|Skapa en resurs för Namespace och uppdatera dess egenskaper. Taggar och kapacitet för Namespace är egenskaper som kan uppdateras.|
|registrera/åtgärd|Registrerar prenumerationen för NotificationHubs-resursprovidern och gör det möjligt att skapa namnområden och NotificationHubs|

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

| Åtgärd | Beskrivning |
|---|---|
|/linkTargets/Read|Visar en lista över befintliga konton som inte är associerade med en Azure-prenumeration. Använd en kund-id som returneras av den här åtgärden i kundens id-egenskapen för skapa arbetsytan igen för att länka den här Azure-prenumeration till en arbetsyta.|
|registrera/åtgärd|Registrera en prenumeration på en resursleverantör.|
|/Workspaces/Analytics/Query/Action|Sökningen med nya motorn.|
|/workspaces/analytics/query/schema/read|Hämta schema för sökning V2.|
|/Workspaces/API/Query/Action|Sökningen med nya motorn.|
|/Workspaces/API/Query/schema/Read|Hämta schema för sökning V2.|
|/workspaces/configurationScopes/delete|Ta bort konfigurationsomfång|
|/workspaces/configurationScopes/read|Hämta konfigurationsomfång|
|/workspaces/configurationScopes/write|Ange konfigurationsomfång|
|/Workspaces/DataSources/delete|Ta bort datakällor i en arbetsyta.|
|/Workspaces/DataSources/Read|Hämta datakällor i en arbetsyta.|
|/Workspaces/DataSources/Write|Skapa eller uppdatera datakällor i en arbetsyta.|
|/Workspaces/delete|Tar bort en arbetsyta. Om arbetsytan var kopplad till en befintlig arbetsyta vid skapandet bort arbetsytan den kopplats till inte.|
|/Workspaces/generateregistrationcertificate/Action|Genererar registreringscertifikat för arbetsytan. Det här certifikatet används för att ansluta Microsoft System Center Operation Manager till arbetsytan.|
|/workspaces/intelligencepacks/disable/action|Inaktiverar en informationspaketet för en viss arbetsyta.|
|/Workspaces/intelligencepacks/enable/Action|Gör en informationspaketet för en viss arbetsyta.|
|/workspaces/intelligencepacks/read|Visar en lista över alla intelligence packs som är synliga för en viss arbetsyta och visar också om Packet är aktiverat eller inaktiverat för den arbetsytan.|
|/workspaces/linkedServices/delete|Ta bort länkade tjänster enligt angivna arbetsytan.|
|/workspaces/linkedServices/read|Hämta länkade tjänster enligt angivna arbetsytan.|
|/workspaces/linkedServices/write|Skapa eller uppdatera länkade tjänster enligt angivna arbetsytan.|
|/workspaces/listKeys/action|Hämtar lista nycklarna för arbetsytan. Dessa nycklar används för att ansluta Microsoft åtgärdsinformation agenter till arbetsytan.|
|/Workspaces/listKeys/Read|Hämtar lista nycklarna för arbetsytan. Dessa nycklar används för att ansluta Microsoft åtgärdsinformation agenter till arbetsytan.|
|/workspaces/managementGroups/read|Hämtar namn och metadata för System Center Operations Manager-hanteringsgrupper som är ansluten till den här arbetsytan.|
|/Workspaces/metricDefinitions/Read|Hämta mått definitioner under arbetsytan|
|/workspaces/notificationSettings/delete|Ta bort användarens meddelandeinställningarna för arbetsytan.|
|/workspaces/notificationSettings/read|Hämta användarens meddelandeinställningarna för arbetsytan.|
|/workspaces/notificationSettings/write|Ange användarens meddelandeinställningarna för arbetsytan.|
|/Workspaces/PURGE/Action|Ta bort angivna data från arbetsytan|
|/Workspaces/Read|Hämtar en befintlig arbetsyta|
|/workspaces/savedSearches/delete|Tar bort en sparad sökning|
|/workspaces/savedSearches/read|Hämtar en sparad sökning|
|/workspaces/savedSearches/write|Skapar en sparad sökning|
|/Workspaces/schema/Read|Hämtar Sök schemat för arbetsytan.  Sök-schemat innehåller exponerade fält och deras typer.|
|/Workspaces/Search/Action|Kör en sökfråga|
|/Workspaces/sharedKeys/Action|Hämtar de delade nycklarna för arbetsytan. Dessa nycklar används för att ansluta Microsoft åtgärdsinformation agenter till arbetsytan.|
|/Workspaces/sharedKeys/Read|Hämtar de delade nycklarna för arbetsytan. Dessa nycklar används för att ansluta Microsoft åtgärdsinformation agenter till arbetsytan.|
|/workspaces/storageinsightconfigs/delete|Tar bort en konfiguration för lagring. Microsoft åtgärdsinformation stoppas från att läsa data från lagringskontot.|
|/workspaces/storageinsightconfigs/read|Hämtar en konfiguration för lagring.|
|/workspaces/storageinsightconfigs/write|Skapar en ny konfiguration för lagring. Dessa konfigurationer för att hämta data från en plats i ett befintligt lagringskonto.|
|/Workspaces/usages/Read|Hämtar användningsdata för en arbetsyta inklusive mängden data som läses av arbetsytan.|
|/ arbetsytor/skrivning|Skapar en ny arbetsyta eller länkar till en befintlig arbetsyta genom att tillhandahålla kund-id från befintlig arbetsyta.|

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

| Åtgärd | Beskrivning |
|---|---|
|/managementAssociations/delete|Ta bort befintliga Management-kopplingen|
|/managementAssociations/read|Hämta befintliga för att associera Management|
|/managementAssociations/write|Skapa en ny Management-Association|
|/managementConfigurations/delete|Ta bort den befintliga Management Configuratin|
|/managementConfigurations/read|Hämta befintliga Management-konfiguration|
|/ managementConfigurations/skrivning|Skapa en ny Management-konfiguration|
|registrera/åtgärd|Registrera en prenumeration på en resursleverantör.|
|/Solutions/delete|Ta bort befintliga OMS-lösning|
|/Solutions/Read|Hämta avslutas OMS-lösning|
|/ lösningar/skrivning|Skapa ny OMS-lösning|

## <a name="microsoftportal"></a>Microsoft.Portal

| Åtgärd | Beskrivning |
|---|---|
|/Dashboards/delete|Tar bort instrumentpanelen från prenumerationen.|
|/Dashboards/Read|Läser in instrumentpanelerna för prenumerationen.|
|/ instrumentpaneler och skrivning|Lägg till eller ändra en instrumentpanel för en prenumeration.|

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

| Åtgärd | Beskrivning |
|---|---|
|/capacities/checkNameAvailability/Action|Kontrollerar att namnet angivna Power BI-dedikerade kapacitet är giltigt och inte i användning.|
|/capacities/delete|Tar bort Powerbi reserverad kapacitet.|
|/capacities/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för Power BI dedikerade kapacitet.|
|/capacities/Read|Hämtar information för den angivna Power BI dedikerade kapacitet.|
|/ kapacitet/skrivning|Skapar eller uppdaterar den angivna Power BI dedikerade kapacitet.|

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

| Åtgärd | Beskrivning |
|---|---|
|/Locations/allocatedStamp/Read|GetAllocatedStamp är en intern åtgärd som används av tjänsten|
|/Locations/allocateStamp/Action|AllocateStamp är intern åtgärd som används av tjänsten|
|/locations/backupPreValidateProtection/action||
|/Locations/backupStatus/Action|Kontrollera Status för säkerhetskopiering för Recovery Services-valv|
|/locations/backupValidateFeatures/action|Validera funktioner|
|/Operations/Read|Åtgärd returnerar en lista över åtgärder för en resurs-Provider|
|registrera/åtgärd|Registrerar prenumerationen för angivna Resource Provider|
|/Vaults/backupconfig/read|Returnerar konfigurationen för Recovery Services-valvet.|
|/Vaults/backupconfig/write|Uppdateringar konfigurationen för Recovery Services-valvet.|
|/Vaults/backupEngines/read|Returnerar alla säkerhetskopieringshanteringsservrar registrerade i valvet.|
|/Vaults/backupFabrics/{fabricName}/protectionContainers/{containerName}/items/read|Hämta alla objekt i en behållare|
|/Vaults/backupFabrics/backupProtectionIntent/write|Skapa en säkerhetskopiering skydd avsett|
|/Vaults/backupFabrics/operationResults/read|Returnerar status för åtgärden|
|/Vaults/backupFabrics/protectableContainers/read|Hämta alla behållare som kan skyddas|
|/Vaults/backupFabrics/protectionContainers/inquire/action|Gör en förfrågan för arbetsbelastningar i en behållare|
|/Vaults/backupFabrics/protectionContainers/operationResults/read|Hämtar resultat från utförd åtgärd på skyddsbehållare.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action|Säkerhetskopierar ett skyddat objekt.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/delete|Tar bort skyddade objekt|
|/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read|Hämtar resultat från utförd åtgärd på skyddade objekt.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read|Returnerar status för utförd åtgärd på skyddade objekt.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/read|Returnerar information om objekt för det skyddade objektet|
|/ Valv/backupFabrics/protectionContainers/protectedItems/recoveryPoints-provisionInstantItemRecovery-åtgärden|Etablera omedelbar återställning för skyddade objekt|
|/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read|Hämta återställningspunkter för skyddade objekt.|
|/ Valv/backupFabrics/protectionContainers/protectedItems/recoveryPoints-restore-åtgärden|Återskapa återställningspunkter för skyddade objekt.|
|/ Valv/backupFabrics/protectionContainers/protectedItems/recoveryPoints-revokeInstantItemRecovery-åtgärden|Återkalla omedelbar återställning för skyddade objekt|
|/Vaults/backupFabrics/protectionContainers/protectedItems/write|Skapa en säkerhetskopiera skyddade objekt|
|/Vaults/backupFabrics/protectionContainers/read|Returnerar alla registrerade behållare|
|/Vaults/backupFabrics/protectionContainers/write|Skapar en registrerad behållare|
|/Vaults/backupFabrics/refreshContainers/action|Uppdaterar listan över behållare|
|/Vaults/backupJobs/cancel/action|Avbryts|
|/Vaults/backupJobs/operationResults/read|Returnerar resultat från jobbåtgärd.|
|/Vaults/backupJobs/read|Returnerar alla jobbobjekt|
|/Vaults/backupJobsExport/action|Exportera jobben|
|/Vaults/backupJobsExport/operationResults/read|Returnerar resultatet av jobbet exportåtgärden.|
|/Vaults/backupManagementMetaData/read|Returnerar metadata för hantering av säkerhetskopiering för Recovery Services-valvet.|
|/Vaults/backupOperationResults/read|Returnerar resultat från säkerhetskopiering för Recovery Services-valvet.|
|/Vaults/backupOperations/read|Returnerar Säkerhetskopieringsåtgärden Status för Recovery Services-valvet.|
|/Vaults/backupPolicies/delete|Ta bort en Skyddsprincip för|
|/Vaults/backupPolicies/operationResults/read|Hämtar resultat från principåtgärd.|
|/Vaults/backupPolicies/operations/read|Hämta Status för princip-åtgärd.|
|/Vaults/backupPolicies/read|Returnerar alla Protection-principer|
|/Vaults/backupPolicies/write|Skapar Protection-principen|
|/Vaults/backupProtectableItems/read|Returnerar lista över alla objekt som ska skyddas.|
|/Vaults/backupProtectedItems/read|Returnerar listan över alla skyddade objekt.|
|/Vaults/backupProtectionContainers/read|Returnerar alla behållare som hör till prenumerationen|
|/Vaults/backupSecurityPIN/action|Returnerar säkerhet PIN-kod Information för Recovery Services-valvet.|
|/Vaults/backupstorageconfig/read|Returnerar lagringskonfigurationen för Recovery Services-valvet.|
|/Vaults/backupstorageconfig/write|Konfiguration för lagring av uppdateringar för Recovery Services-valvet.|
|/Vaults/backupUsageSummaries/read|Returnerar sammanfattningar för skyddade objekt och skyddade servrar för en Recovery Services.|
|Och valv/certifikat/skrivning|Uppdatera resurs certifikat åtgärden uppdaterar Autentiseringscertifikatet resurs-valvet.|
|/Vaults/delete|Ta bort valvet åtgärden tar bort angivna Azure-resurs av typen 'valvet'|
|/Vaults/extendedInformation/delete|Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv?|
|/Vaults/extendedInformation/read|Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv?|
|/Vaults/extendedInformation/write|Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv?|
|/ Valv/monitoringAlerts/läsning|Hämtar aviseringar för Recovery services-valvet.|
|/Vaults/monitoringAlerts/write|Löser du aviseringen.|
|/Vaults/monitoringConfigurations/read|Hämtar meddelandekonfigurationen Recovery services-valvet.|
|Och valv/monitoringConfigurations/skrivning|Konfigurerar e-postaviseringar till Recovery services-valvet.|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/read|Azure Backup-diagnostik|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/write|Azure Backup-diagnostik|
|/Vaults/providers/Microsoft.Insights/logDefinitions/read|Azure Backup-loggar|
|/Vaults/providers/Microsoft.Insights/metricDefinitions/read|Azure Backup mått|
|/ Valv/läsning|Åtgärden hämta valvet hämtar ett objekt som representerar Azure-resurs av typen 'valvet'|
|/Vaults/registeredIdentities/delete|Åtgärden för att avregistrera behållaren kan användas för att avregistrera en behållare.|
|/Vaults/registeredIdentities/operationResults/read|Hämta åtgärden resulterar åtgärden kan användas för hämta Åtgärdsstatus och resultat för asynkront skickats igen|
|/Vaults/registeredIdentities/read|Hämta behållarna åtgärden kan användas för hämta behållare som har registrerats för en resurs.|
|/Vaults/registeredIdentities/write|Registrera tjänstbehållaren åtgärden kan användas för att registrera en behållare med återställningstjänsten.|
|/vaults/replicationAlertSettings/Read|Läsa alla aviseringsinställningar|
|/vaults/replicationAlertSettings/write|Skapa eller uppdatera alla aviseringsinställningar|
|/vaults/replicationEvents/Read|Läsa alla händelser|
|/vaults/replicationFabrics/checkConsistency/action|Kontrollerar infrastrukturens enhetlighet|
|/vaults/replicationFabrics/delete|Ta bort alla Infrastrukturresurser|
|/vaults/replicationFabrics/deployProcessServerImage/action|Distribuera Processerveravbildning|
|/vaults/replicationFabrics/Read|Läsa alla Infrastrukturresurser|
|/vaults/replicationFabrics/reassociateGateway/action|Skapa en ny koppling Gateway|
|/vaults/replicationFabrics/Remove/Action|Ta bort Fabric|
|/vaults/replicationFabrics/renewcertificate/Action|Förnya certifikat för infrastruktur|
|/vaults/replicationFabrics/replicationNetworks/read|Läsa alla nätverk|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete|Ta bort alla nätverksmappningar|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read|Läsa alla nätverksmappningar|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write|Skapa eller uppdatera alla nätverksmappningar|
|/ valv/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/åtgärd|Identifiera skyddsobjekt|
|/vaults/replicationFabrics/replicationProtectionContainers/read|Läsa skydd behållare|
|/vaults/replicationFabrics/replicationProtectionContainers/Remove/Action|Ta bort Skyddsbehållaren|
|/ replicationProtectionContainers-valv/replicationFabrics/replicationProtectableItems/läsning|Läsa alla objekt som kan skyddas|
|/ replicationProtectionContainers-valv/replicationFabrics/replicationProtectedItems-applyRecoveryPoint-åtgärden|Tillämpa återställningspunkt|
|/ valv/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ta bort|Ta bort alla skyddade objekt|
|/ replicationProtectionContainers-valv/replicationFabrics/replicationProtectedItems-failoverCommit-åtgärden|Redundansåtgärd|
|/ replicationProtectionContainers-valv/replicationFabrics/replicationProtectedItems-plannedFailover-åtgärden|Planerad redundans|
|/ replicationProtectionContainers-valv/replicationFabrics/replicationProtectedItems/läsning|Läsa alla skyddade objekt|
|/ replicationProtectionContainers-valv/replicationFabrics/replicationProtectedItems/recoveryPoints/läsning|Läsa alla återställningspunkter för replikeringen|
|/ replicationProtectionContainers-valv/replicationFabrics/replicationProtectedItems-remove-åtgärden|Ta bort skyddade objekt|
|/ replicationProtectionContainers-valv/replicationFabrics/replicationProtectedItems-repairReplication-åtgärden|Reparera replikering|
|/ replicationProtectionContainers-valv/replicationFabrics/replicationProtectedItems/skyddar/åtgärd|Skapa nytt skyddat objekt|
|/ replicationProtectionContainers-valv/replicationFabrics/replicationProtectedItems-testFailover-åtgärden|Testa redundans|
|/ replicationProtectionContainers-valv/replicationFabrics/replicationProtectedItems-testFailoverCleanup-åtgärden|Redundanstestningen|
|/ replicationProtectionContainers-valv/replicationFabrics/replicationProtectedItems-unplannedFailover-åtgärden|Redundans|
|/ replicationProtectionContainers-valv/replicationFabrics/replicationProtectedItems-updateMobilityService-åtgärden|Uppdatera Mobilitetstjänsten|
|/ valv/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/skrivning|Skapa eller uppdatera alla skyddade objekt|
|/ valv/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/ta bort|Ta bort alla mappningar för behållaren skydd|
|/ replicationProtectionContainers-valv/replicationFabrics/replicationProtectionContainerMappings/läsning|Läsa alla mappningar för behållaren skydd|
|/ replicationProtectionContainers-valv/replicationFabrics/replicationProtectionContainerMappings-remove-åtgärden|Ta bort skyddsbehållare|
|/ valv/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/skrivning|Skapa eller uppdatera alla mappningar för behållaren skydd|
|/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/Action|Växeln-Skyddsbehållaren|
|/vaults/replicationFabrics/replicationProtectionContainers/write|Skapa eller uppdatera Protection behållare|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/delete|Ta bort alla Recovery Services-Providers|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/read|Läsa alla Recovery Services-Providers|
|/ valv/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/åtgärd|Uppdatera providern|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/remove/action|Ta bort Recovery Services-Provider|
|/vaults/replicationFabrics/replicationRecoveryServicesProviders/write|Skapa eller uppdatera alla Recovery Services-Providers|
|/vaults/replicationFabrics/replicationStorageClassifications/read|Läsa alla Lagringsklassificeringar|
|/ valv/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/ta bort|Ta bort alla mappningar för klassificering av lagring|
|/ replicationStorageClassifications-valv/replicationFabrics/replicationStorageClassificationMappings/läsning|Läsa alla mappningar för klassificering av lagring|
|/ valv/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/skrivning|Skapa eller uppdatera alla mappningar för klassificering av lagring|
|/vaults/replicationFabrics/replicationvCenters/delete|Ta bort alla jobb|
|/vaults/replicationFabrics/replicationvCenters/read|Läsa alla jobb|
|/vaults/replicationFabrics/replicationvCenters/write|Skapa eller uppdatera jobb|
|/vaults/replicationFabrics/Write|Skapa eller uppdatera alla Infrastrukturresurser|
|/vaults/replicationJobs/Cancel/Action|Avbryta jobb|
|/vaults/replicationJobs/Read|Läsa alla jobb|
|/vaults/replicationJobs/restart/Action|Starta om jobbet|
|/vaults/replicationJobs/Resume/Action|Återuppta jobbet|
|/vaults/replicationPolicies/delete|Ta bort alla principer|
|/vaults/replicationPolicies/Read|Läsa alla principer|
|/vaults/replicationPolicies/Write|Skapa eller uppdatera alla principer|
|/vaults/replicationRecoveryPlans/delete|Ta bort alla Återställningsplaner|
|/vaults/replicationRecoveryPlans/failoverCommit/action|Redundansåtgärd återställningsplan|
|/vaults/replicationRecoveryPlans/plannedFailover/action|Planerad redundans återställningsplan|
|/vaults/replicationRecoveryPlans/read|Läsa alla Återställningsplaner|
|/vaults/replicationRecoveryPlans/reProtect/action|Skapa nytt återställningsplan|
|/vaults/replicationRecoveryPlans/testFailover/Action|Testa redundans återställningsplan|
|/vaults/replicationRecoveryPlans/testFailoverCleanup/action|Testa redundans Rensa återställningsplan|
|/vaults/replicationRecoveryPlans/unplannedFailover/action|Plan för växling vid fel|
|/vaults/replicationRecoveryPlans/Write|Skapa eller uppdatera alla Återställningsplaner|
|/Vaults/tokenInfo/read|Returnerar tokeninformation för Recovery Services-valvet.|
|/vaults/usages/Read|Läsa alla valvet användningsområden|
|/Vaults/usages/read|Returnerar användningsinformation om Recovery Services-valvet.|
|/Vaults/vaultTokens/read|Token valvet igen kan användas för att hämta valv Token för valvet nivån backend-åtgärder.|
|/Vaults/write|Med skapa valv så skapas en Azure-resurs av typen valv|

## <a name="microsoftrelay"></a>Microsoft.Relay

| Åtgärd | Beskrivning |
|---|---|
|/ checkNameAvailability/åtgärd|Kontrollerar tillgänglighet för namnområden i en viss prenumeration.|
|/ checkNamespaceAvailability/åtgärd|Kontrollerar tillgänglighet för namnområden i en viss prenumeration. Detta API är inaktuell. Använd CheckNameAvailabiltiy i stället.|
|/Namespaces/authorizationRules/Action|Uppdateringar Namespace auktoriseringsregeln. Detta API är depricated. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln Namespace i stället... Den här åtgärden stöds inte på API-version 2017-04-01.|
|/Namespaces/authorizationRules/delete|Ta bort Namespace auktoriseringsregeln. Auktoriseringsregeln standard Namespace kan inte tas bort. |
|/namespaces/authorizationRules/listkeys/action|Hämta anslutningssträngen till namnområdet|
|/Namespaces/authorizationRules/Read|Hämta listan över beskrivning av auktoriseringsregler för namnområden.|
|/namespaces/authorizationRules/regenerateKeys/action|Återskapa den primära eller sekundära nyckeln till resursen|
|/Namespaces/authorizationRules/Write|Skapa en nivå auktoriseringsregler Namespace och uppdatera dess egenskaper. Åtkomstbehörigheter för regler för auktorisering, primära och sekundärnycklar kan uppdateras.|
|namnområden/ta bort|Ta bort namnområdesresurs|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Hämtar tillståndet regler nycklar för katastrofåterställning primära namnområdet|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Hämta Disaster Recovery primära Namespaces auktoriseringsregler|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|Inaktiverar haveriberedskap och stoppar replikering av ändringar från primära till sekundära namnområden.|
|/Namespaces/disasterrecoveryconfigs/checkNameAvailability/Action|Kontrollerar tillgängligheten för namnområdesalias under de angivna prenumerationen.|
|/namespaces/disasterRecoveryConfigs/delete|Tar bort Disaster Recovery-konfiguration som associeras med namnområdet. Den här åtgärden kan bara anropas via primära namnområdet.|
|/namespaces/disasterRecoveryConfigs/failover/action|Anropar GEO DR-redundans och omkonfigurerar namnområdets alias till att peka på det sekundära namnområdet.|
|/namespaces/disasterRecoveryConfigs/read|Hämtar den haveriberedskapskonfiguration som är kopplad till namnområdet.|
|/namespaces/disasterRecoveryConfigs/write|Skapar eller uppdaterar den haveriberedskapskonfiguration som är kopplad till namnområdet.|
|/namespaces/HybridConnections/authorizationRules/action|Åtgärden Uppdatera HybridConnection. Den här åtgärden stöds inte på API-version 2017-04-01. Auktoriseringsregler. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln.|
|/namespaces/HybridConnections/authorizationRules/delete|Åtgärden ta bort HybridConnection auktoriseringsregler|
|/namespaces/HybridConnections/authorizationRules/listkeys/action|Hämta anslutningssträngen till HybridConnection|
|/Namespaces/HybridConnections/authorizationRules/Read| Hämta en lista över HybridConnection auktoriseringsregler|
|/namespaces/HybridConnections/authorizationRules/regeneratekeys/action|Återskapa den primära eller sekundära nyckeln till resursen|
|/namespaces/HybridConnections/authorizationRules/write|Skapa auktoriseringsregler för HybridConnection och uppdatera dess egenskaper. Att det går uppdatera åtkomstbehörigheter för regler för auktorisering.|
|/Namespaces/HybridConnections/delete|Åtgärden ta bort HybridConnection resurs|
|/Namespaces/HybridConnections/Read|Hämta lista över HybridConnection resurs beskrivningar|
|/namespaces/HybridConnections/write|Skapa eller uppdatera HybridConnection egenskaper.|
|/Namespaces/messagingPlan/Read|Hämtar meddelanden planera för ett namnområde. Detta API är inaktuell. Egenskaper som exponeras via MessagingPlan resursen flyttas till (överordnad) Namespace resurs API senare... Den här åtgärden stöds inte på API-version 2017-04-01.|
|/Namespaces/messagingPlan/Write|Uppdaterar Messaging planera för ett namnområde. Detta API är inaktuell. Egenskaper som exponeras via MessagingPlan resursen flyttas till (överordnad) Namespace resurs API senare... Den här åtgärden stöds inte på API-version 2017-04-01.|
|/Namespaces/operationresults/Read|Hämta status för namnområdesåtgärd|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Hämta lista över Namespace mått resurs beskrivningar|
|/Namespaces/Read|Hämta listan över beskrivningar av namnområdesresurs|
|/Namespaces/WcfRelays/authorizationRules/Action|Åtgärden Uppdatera WcfRelay. Den här åtgärden stöds inte på API-version 2017-04-01. Auktoriseringsregler. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln.|
|/namespaces/WcfRelays/authorizationRules/delete|Åtgärden ta bort WcfRelay auktoriseringsregler|
|/namespaces/WcfRelays/authorizationRules/listkeys/action|Hämta anslutningssträngen till WcfRelay|
|/Namespaces/WcfRelays/authorizationRules/Read| Hämta en lista över WcfRelay auktoriseringsregler|
|/namespaces/WcfRelays/authorizationRules/regeneratekeys/action|Återskapa den primära eller sekundära nyckeln till resursen|
|/namespaces/WcfRelays/authorizationRules/write|Skapa auktoriseringsregler för WcfRelay och uppdatera dess egenskaper. Att det går uppdatera åtkomstbehörigheter för regler för auktorisering.|
|/Namespaces/WcfRelays/delete|Åtgärden ta bort WcfRelay resurs|
|/Namespaces/WcfRelays/Read|Hämta lista över WcfRelay resurs beskrivningar|
|/Namespaces/WcfRelays/Write|Skapa eller uppdatera WcfRelay egenskaper.|
|/ namnområden/skrivning|Skapa en resurs för Namespace och uppdatera dess egenskaper. Taggar och kapacitet för Namespace är egenskaper som kan uppdateras.|
|/Operations/Read|Hämta åtgärder|
|registrera/åtgärd|Registrerar prenumerationen för Relay-resursprovidern och gör det möjligt att skapa Relay-resurser|
|/unregister/action|Registrerar prenumerationen för Relay-resursprovidern och gör det möjligt att skapa Relay-resurser|

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

| Åtgärd | Beskrivning |
|---|---|
|/ AvailabilityStatuses/current/läsning|Hämtar tillgänglighetsstatusen för den angivna resursen|
|/ AvailabilityStatuses/läsning|Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området|
|/ healthevent/åtgärd|Anger ändringen i hälsotillståndet för den angivna resursen|
|/healthevent/activated/Action|Anger ändringen i hälsotillståndet för den angivna resursen|
|/healthevent/InProgress/Action|Anger ändringen i hälsotillståndet för den angivna resursen|
|/healthevent/Pending/Action|Anger ändringen i hälsotillståndet för den angivna resursen|
|/healthevent/Resolved/Action|Anger ändringen i hälsotillståndet för den angivna resursen|
|/healthevent/Updated/Action|Anger ändringen i hälsotillståndet för den angivna resursen|
|registrera/åtgärd|Registrerar prenumerationen för Microsoft ResourceHealth|

## <a name="microsoftresources"></a>Microsoft.Resources

| Åtgärd | Beskrivning |
|---|---|
|/ checkResourceName/åtgärd|Kontrollera resursnamnets giltighet.|
|/Deployments/Cancel/Action|Avbryter en distribution.|
|/Deployments/delete|Tar bort en distribution.|
|/Deployments/Operations/Read|Hämtar eller listar distributionsåtgärder.|
|/Deployments/Read|Hämtar eller listar distributioner.|
|/Deployments/Validate/Action|Verifierar en distribution.|
|/ distributioner/skrivning|Skapar eller uppdaterar en distribution.|
|/Links/delete|Tar bort en resurslänk.|
|/Links/Read|Hämtar eller listar resurslänkar.|
|/ länkar/skrivning|Skapar eller uppdaterar en resurslänk.|
|/Marketplace/Purchase/Action|Köper en resurs på Marketplace.|
|/providers/Read|Hämta listan över leverantörer.|
|/Resources/Read|Hämta listan över resurser baserade på filter.|
|/subscriptions/Locations/Read|Hämtar listan över platser som stöds.|
|/subscriptions/operationresults/Read|Hämtar prenumerationsåtgärdsresultaten.|
|/subscriptions/providers/Read|Hämtar eller listar resursprovidrar.|
|/subscriptions/Read|Hämtar listan över prenumerationer.|
|/subscriptions/resourceGroups/delete|Tar bort resursgruppen och alla dess resurser.|
|/subscriptions/resourcegroups/Deployments/Operations/Read|Hämtar eller listar distributionsåtgärder.|
|/subscriptions/resourcegroups/Deployments/operationstatuses/Read|Hämtar eller listar status för distributionsåtgärder.|
|/subscriptions/resourcegroups/Deployments/Read|Hämtar eller listar distributioner.|
|/subscriptions/resourcegroups/Deployments/Write|Skapar eller uppdaterar en distribution.|
|/subscriptions/resourceGroups/moveResources/Action|Flyttar resurser från en resursgrupp till en annan.|
|/subscriptions/resourceGroups/Read|Hämtar eller listar resursgrupper.|
|/subscriptions/resourcegroups/Resources/Read|Hämtar resurserna för resursgruppen.|
|/subscriptions/resourceGroups/validateMoveResources/action|Verifiera flytt av resurser från en resursgrupp till en annan.|
|/subscriptions/resourceGroups/write|Skapar eller uppdaterar en resursgrupp.|
|/subscriptions/Resources/Read|Hämtar en prenumerations resurser.|
|/subscriptions/tagNames/delete|Tar bort en prenumerationstagg.|
|/subscriptions/tagNames/Read|Hämtar eller listar prenumerationstaggar.|
|/subscriptions/tagNames/tagValues/delete|Tar bort ett prenumerationstaggsvärde.|
|/subscriptions/tagNames/tagValues/read|Hämtar eller listar prenumerationstaggsvärden.|
|/subscriptions/tagNames/tagValues/write|Lägger till ett prenumerationstaggsvärde.|
|/subscriptions/tagNames/Write|Lägger till en prenumerationstagg.|
|/tenants/Read|Hämtar listan över klienter.|

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

| Åtgärd | Beskrivning |
|---|---|
|/jobcollections/delete|Tar bort en jobbsamling.|
|/jobcollections/disable/Action|Inaktiverar en jobbsamling.|
|/jobcollections/enable/Action|Aktiverar en jobbsamling.|
|/jobcollections/jobs/delete|Tar bort ett jobb.|
|/jobcollections/jobs/generateLogicAppDefinition/action|Genererar en logikappsdefinition som baseras på ett Scheduler-jobb.|
|/jobcollections/jobs/jobhistories/Read|Hämtar jobbhistorik.|
|/jobcollections/jobs/Read|Hämtar jobb.|
|/jobcollections/jobs/Run/Action|Kör jobb.|
|/jobcollections/jobs/Write|Skapar eller uppdaterar jobb.|
|/jobcollections/Read|Hämta jobbsamling|
|/ jobbsamlingar/skrivning|Skapar eller uppdaterar en jobbsamling.|

## <a name="microsoftsearch"></a>Microsoft.Search

| Åtgärd | Beskrivning |
|---|---|
|/ checkNameAvailability/åtgärd|Kontrollerar tillgängligheten för tjänstnamnet.|
|registrera/åtgärd|Registrerar prenumerationen för resursen sökleverantör och kan skapa search-tjänster.|
|/searchServices/createQueryKey/Action|Skapar fråga för nyckeln.|
|/searchServices/delete|Tar bort söktjänsten.|
|/searchServices/diagnosticSettings/read|Hämtar diganostic inställningen skrivskyddade för resursen|
|/searchServices/diagnosticSettings/write|Skapar eller uppdaterar inställningen diganostic för resursen|
|/searchServices/listAdminKeys/action|Läser admin-nycklar.|
|/searchServices/logDefinitions/Read|Hämtar de tillgängliga loggarna för söktjänsten|
|/searchServices/metricDefinitions/read|Hämtar tillgängliga mått för söktjänsten|
|/searchServices/queryKey/delete|Tar bort Frågenyckeln.|
|/searchServices/queryKey/read|Läser frågan nycklar.|
|/searchServices/read|Läser search-tjänsten.|
|/searchServices/regenerateAdminKey/action|Återskapar admin-nyckel.|
|/searchServices/start/action|Startar söktjänsten.|
|/searchServices/stop/action|Stoppar tjänsten sökning.|
|/searchServices/write|Skapar eller uppdaterar search-tjänsten.|

## <a name="microsoftsecurity"></a>Microsoft.Security

| Åtgärd | Beskrivning |
|---|---|
|/Alerts/Read|Hämtar alla tillgängliga säkerhetsaviseringar|
|/applicationWhitelistings/read|Hämtar programmet whitelistings|
|/applicationWhitelistings/write|Skapar en ny vitlistning av program eller uppdaterar en befintlig|
|/complianceResults/Read|Hämtar kompatibilitetsresultaten för resursen|
|/Locations/Alerts/Activate/Action|Aktivera en säkerhetsvarning|
|/Locations/Alerts/dismiss/Action|Avvisa en säkerhetsavisering|
|/Locations/Alerts/Read|Hämtar alla tillgängliga säkerhetsaviseringar|
|/locations/jitNetworkAccessPolicies/initiate/action|Initierar en åtkomstprincip för just-in-time-nätverk|
|/locations/jitNetworkAccessPolicies/read|Hämtar just-in-time-åtkomst nätverksprinciper|
|/locations/jitNetworkAccessPolicies/write|Skapar en ny åtkomstprincip för just-in-time-nätverk eller uppdaterar en befintlig|
|/Locations/Read|Hämtar Dataplats säkerhet|
|/Locations/Tasks/Activate/Action|Aktivera en säkerhetsrekommendation|
|/Locations/Tasks/dismiss/Action|Stänga en säkerhetsrekommendation|
|/Locations/Tasks/Read|Hämtar alla tillgängliga säkerhetsrekommendationer|
|/Locations/Tasks/Resolve/Action|Lösa en säkerhetsrekommendation|
|/Locations/Tasks/Start/Action|Starta en säkerhetsrekommendation|
|/policies/Read|Hämtar säkerhetsprincipen|
|/ principer/skrivning|Uppdaterar säkerhetsprincipen|
|/pricings/delete|Tar bort prisnivå inställningar för scope|
|/pricings/Read|Hämtar prisnivå inställningarna för scope|
|/ pricings/skrivning|Uppdaterar inställningarna för omfånget som prisnivå|
|registrera/åtgärd|Registrerar prenumerationen för Azure Security Center|
|/securityContacts/delete|Tar bort kontakten säkerhet|
|/securityContacts/Read|Hämtar säkerhet kontakt|
|/ securityContacts/skrivning|Uppdaterar säkerhet kontakt|
|/securitySolutions/delete|Tar bort en säkerhetslösning|
|/securitySolutions/read|Hämtar säkerhetslösningar|
|/securitySolutions/write|Skapar en ny lösning eller uppdaterar en befintlig|
|/securitySolutionsReferenceData/read|Hämtar säkerhetslösningar referensdata|
|/securityStatuses/Read|Hämtar säkerheten hälsa status för Azure-resurser|
|/securityStatusesSummaries/read|Hämtar säkerheten statusar sammanfattningar för scope|
|/Tasks/Read|Hämtar alla tillgängliga säkerhetsrekommendationer|
|/webApplicationFirewalls/delete|Tar bort en brandvägg för webbaserade program|
|/webApplicationFirewalls/Read|Hämtar webben programmet brandväggar|
|/webApplicationFirewalls/write|Skapar en ny Brandvägg för webbaserade program eller uppdaterar en befintlig|
|/workspaceSettings/connect/action|Ändra arbetsytan inställningar för återanslutning|
|/workspaceSettings/delete|Tar bort arbetsytan inställningar|
|/workspaceSettings/read|Hämtar inställningar för arbetsyta|
|/workspaceSettings/write|Arbetsytan inställningar för uppdateringar|

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

| Åtgärd | Beskrivning |
|---|---|
|/ checkNameAvailability/åtgärd|Kontrollerar tillgänglighet för namnområden i en viss prenumeration.|
|/ checkNamespaceAvailability/åtgärd|Kontrollerar tillgänglighet för namnområden i en viss prenumeration. Detta API är inaktuell. Använd CheckNameAvailabiltiy i stället.|
|/Namespaces/authorizationRules/Action|Uppdateringar Namespace auktoriseringsregeln. Detta API är depricated. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln Namespace i stället... Den här åtgärden stöds inte på API-version 2017-04-01.|
|/Namespaces/authorizationRules/delete|Ta bort Namespace auktoriseringsregeln. Auktoriseringsregeln standard Namespace kan inte tas bort. |
|/namespaces/authorizationRules/listkeys/action|Hämta anslutningssträngen till namnområdet|
|/Namespaces/authorizationRules/Read|Hämta listan över beskrivning av auktoriseringsregler för namnområden.|
|/namespaces/authorizationRules/regenerateKeys/action|Återskapa den primära eller sekundära nyckeln till resursen|
|/Namespaces/authorizationRules/Write|Skapa en nivå auktoriseringsregler Namespace och uppdatera dess egenskaper. Åtkomstbehörigheter för regler för auktorisering, primära och sekundärnycklar kan uppdateras.|
|namnområden/ta bort|Ta bort namnområdesresurs|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Hämtar tillståndet regler nycklar för katastrofåterställning primära namnområdet|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Hämta Disaster Recovery primära Namespaces auktoriseringsregler|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|Inaktiverar haveriberedskap och stoppar replikering av ändringar från primära till sekundära namnområden.|
|/Namespaces/disasterrecoveryconfigs/checkNameAvailability/Action|Kontrollerar tillgängligheten för namnområdesalias under de angivna prenumerationen.|
|/namespaces/disasterRecoveryConfigs/delete|Tar bort Disaster Recovery-konfiguration som associeras med namnområdet. Den här åtgärden kan bara anropas via primära namnområdet.|
|/namespaces/disasterRecoveryConfigs/failover/action|Anropar GEO DR-redundans och omkonfigurerar namnområdets alias till att peka på det sekundära namnområdet.|
|/namespaces/disasterRecoveryConfigs/read|Hämtar den haveriberedskapskonfiguration som är kopplad till namnområdet.|
|/namespaces/disasterRecoveryConfigs/write|Skapar eller uppdaterar den haveriberedskapskonfiguration som är kopplad till namnområdet.|
|/namespaces/eventGridFilters/delete|Tar bort händelsen rutnätet filtret som associeras med namnområdet.|
|/namespaces/eventGridFilters/read|Hämtar händelsen rutnätet filtret som associeras med namnområdet.|
|/namespaces/eventGridFilters/write|Skapar eller uppdaterar händelse rutnätet filtret som associeras med namnområdet.|
|/Namespaces/eventhubs/Read|Hämta lista över EventHub resurs beskrivningar|
|/Namespaces/messagingPlan/Read|Hämtar meddelanden planera för ett namnområde. Detta API är inaktuell. Egenskaper som exponeras via MessagingPlan resursen flyttas till (överordnad) Namespace resurs API senare... Den här åtgärden stöds inte på API-version 2017-04-01.|
|/Namespaces/messagingPlan/Write|Uppdaterar Messaging planera för ett namnområde. Detta API är inaktuell. Egenskaper som exponeras via MessagingPlan resursen flyttas till (överordnad) Namespace resurs API senare... Den här åtgärden stöds inte på API-version 2017-04-01.|
|/Namespaces/migrate/Action|Migreringsåtgärd för namnområde|
|/Namespaces/operationresults/Read|Hämta status för namnområdesåtgärd|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|Hämta lista över Namespace diagnostikinställningar resurs beskrivningar|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|Hämta lista över Namespace diagnostikinställningar resurs beskrivningar|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|Hämta en lista över Namespace loggar resurs beskrivningar|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Hämta lista över Namespace mått resurs beskrivningar|
|/Namespaces/queues/authorizationRules/Action|Åtgärden uppdatera kön. Den här åtgärden stöds inte på API-version 2017-04-01. Auktoriseringsregler. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln.|
|/namespaces/queues/authorizationRules/delete|Åtgärden ta bort kön auktoriseringsregler|
|/namespaces/queues/authorizationRules/listkeys/action|Hämta anslutningssträngen till kön|
|/Namespaces/queues/authorizationRules/Read| Hämta en lista över auktoriseringsregler för kön|
|/namespaces/queues/authorizationRules/regenerateKeys/action|Återskapa den primära eller sekundära nyckeln till resursen|
|/Namespaces/queues/authorizationRules/Write|Skapa auktoriseringsregler för kön och uppdatera dess egenskaper. Att det går uppdatera åtkomstbehörigheter för regler för auktorisering.|
|/namespaces/queues/Delete|Åtgärden ta bort kön resurs|
|/Namespaces/queues/Read|Hämta lista över kön resurs beskrivningar|
|/Namespaces/queues/Write|Skapa eller uppdatera köegenskaper.|
|/Namespaces/Read|Hämta listan över beskrivningar av namnområdesresurs|
|/Namespaces/topics/authorizationRules/Action|Åtgärden Uppdatera avsnittet. Den här åtgärden stöds inte på API-version 2017-04-01. Auktoriseringsregler. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln.|
|/namespaces/topics/authorizationRules/delete|Åtgärden ta bort avsnittet auktoriseringsregler|
|/namespaces/topics/authorizationRules/listkeys/action|Hämta anslutningssträngen till avsnittet|
|/Namespaces/topics/authorizationRules/Read| Hämta en lista över avsnittet auktoriseringsregler|
|/namespaces/topics/authorizationRules/regenerateKeys/action|Återskapa den primära eller sekundära nyckeln till resursen|
|/namespaces/topics/authorizationRules/write|Skapa auktoriseringsregler för avsnittet och uppdatera dess egenskaper. Att det går uppdatera åtkomstbehörigheter för regler för auktorisering.|
|/Namespaces/topics/delete|Åtgärden ta bort avsnittet resurs|
|/Namespaces/topics/Read|Hämta lista över avsnittet resurs beskrivningar|
|/Namespaces/topics/subscriptions/delete|Åtgärden ta bort TopicSubscription resurs|
|/Namespaces/topics/subscriptions/Read|Hämta lista över TopicSubscription resurs beskrivningar|
|/Namespaces/topics/subscriptions/rules/delete|Åtgärden ta bort regeln resurs|
|/Namespaces/topics/subscriptions/rules/Read|Hämta lista över regeln resurs beskrivningar|
|/Namespaces/topics/subscriptions/rules/Write|Skapa eller uppdatera regelegenskaper.|
|/Namespaces/topics/subscriptions/Write|Skapa eller uppdatera TopicSubscription egenskaper.|
|/Namespaces/topics/Write|Skapa eller uppdatera avsnittet Egenskaper.|
|/ namnområden/skrivning|Skapa en resurs för Namespace och uppdatera dess egenskaper. Taggar och kapacitet för Namespace är egenskaper som kan uppdateras.|
|/Operations/Read|Hämta åtgärder|
|registrera/åtgärd|Registrerar prenumerationen för ServiceBus-resursprovidern och gör det möjligt att skapa ServiceBus-resurser|
|/sku/read|Hämta lista över Sku resurs beskrivningar|
|/sku/regions/read|Hämta lista över SkuRegions resurs beskrivningar|
|/unregister/action|Registrerar prenumerationen för ServiceBus-resursprovidern och gör det möjligt att skapa ServiceBus-resurser|

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

| Åtgärd | Beskrivning |
|---|---|
|/Clusters/Applications/delete|Ta bort valfritt program|
|/Clusters/Applications/Read|Läs valfritt program|
|/Clusters/Applications/Services/delete|Ta bort valfri tjänst|
|/Clusters/Applications/Services/partitions/Read|Läs valfri partition|
|/Clusters/Applications/Services/partitions/replicas/Read|Läs valfri replik|
|/Clusters/Applications/Services/Read|Läs valfri tjänst|
|/Clusters/Applications/Services/statuses/Read|Läs valfri tjänststatus|
|/clusters/applications/services/write|Skapa eller uppdatera valfri tjänst|
|/Clusters/Applications/Write|Skapa eller uppdatera valfritt program|
|/Clusters/applicationTypes/delete|Ta bort valfri programtyp|
|/Clusters/applicationTypes/Read|Läs valfri programtyp|
|/clusters/applicationTypes/versions/delete|Ta bort valfri version av programtyp|
|/Clusters/applicationTypes/versions/Read|Läs valfri version av programtyp|
|/clusters/applicationTypes/versions/write|Skapa eller uppdatera valfri version av programtyp|
|/clusters/applicationTypes/write|Skapa eller uppdatera valfri programtyp|
|/Clusters/delete|Ta bort valfritt kluster|
|/Clusters/nodes/Read|Läs valfri nod|
|/Clusters/Read|Läs valfritt kluster|
|/Clusters/statuses/Read|Läs valfri klusterstatus|
|/ kluster och skrivning|Skapa eller uppdatera valfritt kluster|
|/Locations/clusterVersions/Read|Läs valfri klusterversion|
|/Locations/Environments/clusterVersions/Read|Läs valfri klusterversion för en viss miljö|
|/Locations/operationresults/Read|Läs valfritt åtgärdsresultat|
|/Locations/Operations/Read|Läs valfri åtgärd efter plats|
|/Operations/Read|Läs valfri tillgänglig åtgärd|
|registrera/åtgärd|Registrera valfri åtgärd|

## <a name="microsoftsolutions"></a>Microsoft.Solutions

| Åtgärd | Beskrivning |
|---|---|
|/applicationDefinitions/delete|Tar bort en programdefinition.|
|/applicationDefinitions/Read|Hämtar en lista över programdefinitioner.|
|/applicationDefinitions/write|Lägg till eller ändra en programdefinition.|
|/Applications/delete|Tar bort ett program.|
|/Applications/Read|Hämtar en lista över program.|
|/ program/skrivning|Skapar ett program.|
|/Locations/operationStatuses/Read|Läser resursens åtgärdsstatus.|
|registrera/åtgärd|Registrera dig för lösningar.|

## <a name="microsoftsql"></a>Microsoft.Sql

| Åtgärd | Beskrivning |
|---|---|
|/ checkNameAvailability/åtgärd|Kontrollera den angivna servern namn är tillgängligt för att etablera över hela världen för en viss prenumeration.|
|/locations/auditingSettingsAzureAsyncOperation/read|Hämta resultatet av den utökade server blob granskning princip Set-åtgärd|
|/locations/auditingSettingsOperationResults/read|Hämta resultatet av server-blob granskning princip Set-åtgärd|
|/Locations/Capabilities/Read|Hämtar funktionerna för den här prenumerationen i en viss plats|
|/locations/databaseAzureAsyncOperation/read|Hämtar status för en databasåtgärd.|
|/Locations/databaseOperationResults/Read|Hämtar status för en databasåtgärd.|
|/locations/deletedServerAsyncOperation/read|Hämtar pågående åtgärder på servern som tagits bort|
|/locations/deletedServerOperationResults/read|Hämtar pågående åtgärder på servern som tagits bort|
|/Locations/deletedServers/Read|Returnera listan över borttagna servrar eller hämtar egenskaperna för den angivna servern som tagits bort.|
|/locations/deletedServers/recover/action|Återställa en borttagen server|
|/locations/deleteVirtualNetworkOrSubnets/action|Tar bort regler för virtuella nätverk som är kopplat till ett virtuellt nätverk eller undernät|
|/locations/elasticPoolAzureAsyncOperation/read|Hämtar azure async-åtgärden för en asynkron åtgärd elastisk pool|
|/locations/elasticPoolOperationResults/read|Hämtar resultatet av en elastisk pool.|
|/locations/extendedAuditingSettingsAzureAsyncOperation/read|Hämta resultatet av den utökade server blob granskning princip Set-åtgärd|
|/locations/extendedAuditingSettingsOperationResults/read|Hämta resultatet av den utökade server blob granskning princip Set-åtgärd|
|/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action|Slutför hanterade databasåterställning|
|/locations/managedTransparentDataEncryptionAzureAsyncOperation/read|Hämtar pågående åtgärder på hanterade databasen transparent datakryptering|
|/locations/managedTransparentDataEncryptionOperationResults/read|Hämtar pågående åtgärder på hanterade databasen transparent datakryptering|
|/Locations/Read|Hämtar tillgängliga platser för en viss prenumeration|
|/locations/syncAgentOperationResults/read|Hämta resultatet av synkroniseringsåtgärd agent resurs|
|/locations/syncDatabaseIds/read|Hämta ID: n för sync-databasen för en viss region och en prenumeration|
|/locations/syncGroupOperationResults/read|Hämta resultatet av åtgärden för synkronisering av resursen|
|/locations/syncMemberOperationResults/read|Hämta resultatet av synkroniseringsåtgärd medlem resurs|
|/Locations/usages/Read|Hämtar en samling av användningsstatistik för den här prenumerationen på en plats|
|/locations/virtualNetworkRulesAzureAsyncOperation/read|Returnerar information om de angivna virtuella nätverk reglerna azure asynkron åtgärd |
|/locations/virtualNetworkRulesOperationResults/read|Returnerar information om den angivna virtuella nätverket regler åtgärden |
|/managedInstances/Administrators/delete|Tar bort en befintlig administratör för hanterade instansen.|
|/managedInstances/Administrators/Read|Hämtar en lista över hanterade instans administratörer.|
|/managedInstances/administrators/write|Skapar eller uppdaterar hanterade instans administratör med de angivna parametrarna.|
|/managedInstances/Databases/delete|Tar bort en befintlig hanterad databas|
|/managedInstances/Databases/Read|Hämtar befintliga hanterad databas|
|/managedInstances/databases/securityAlertPolicies/read|Hämta information om databasen threat detection principen konfigurerats på en viss hanterad databas|
|/managedInstances/databases/securityAlertPolicies/write|Ändra databasen threat detection principen för en viss hanterad databas|
|/managedInstances/databases/securityEvents/read|Hämtar säkerhetshändelser hanterad databas|
|/managedInstances/Databases/transparentDataEncryption/Read|Hämta information om databasen Transparent datakryptering på en viss hanterad databas|
|/managedInstances/databases/transparentDataEncryption/write|Ändra databasen Transparent datakryptering för en viss hanterad databas|
|/managedInstances/Databases/Write|Skapar en ny databas eller uppdaterar en befintlig databas.|
|/managedInstances/delete|Tar bort en befintlig hanterade instans.|
|/managedInstances/metricDefinitions/read|Hämta måttdefinitionerna för hanterade instans|
|/managedInstances/Metrics/Read|Hämta hanterade instans mätvärden|
|/managedInstances/Read|Returnera listan över hanterade instanser eller hämtar egenskaperna för den angivna hantera instansen.|
|/managedInstances/securityAlertPolicies/read|Hämta information om hanterad server threat detection principen på en viss hanterad server|
|/managedInstances/securityAlertPolicies/write|Ändra hanterad server threat detection principen för en viss hanterad server|
|/ managedInstances/skrivning|Skapar en hanterad instans med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna hantera instansen.|
|/Operations/Read|Hämtar tillgängliga REST-åtgärder|
|registrera/åtgärd|Registrerar prenumerationen för resursprovidern Microsoft SQL-databasen och kan skapa Microsoft SQL-databaser.|
|/servers/administratorOperationResults/read|Hämtar pågående åtgärder på server-administratörer|
|/servers/administrators/delete|Ta bort serveradministratör|
|/servers/administrators/read|Hämta serverinformation för administratör|
|/servers/administrators/write|Skapa eller uppdatera serveradministratör|
|/servers/advisors/read|Returnerar en lista över rådgivare som är tillgängliga för servern|
|/servers/advisors/recommendedActions/read|Returnerar en lista över rekommenderade åtgärder för angiven klassificering för servern|
|/servers/advisors/recommendedActions/write|Tillämpa den rekommenderade åtgärden på servern|
|/servers/advisors/write|Uppdateringar köra automatiskt-status för en advisor på servernivå.|
|/servers/auditingPolicies/read|Hämta information om server Standardtabell granskningsprincip som konfigurerats på en viss server|
|/servers/auditingPolicies/write|Ändra standard server tabellen granskning för en viss server|
|/servers/auditingSettings/operationResults/read|Hämta resultatet av server-blob granskning princip Set-åtgärd|
|/servers/auditingSettings/read|Hämta information om server blob granskningsprincipen konfigurerats på en viss server|
|/servers/auditingSettings/write|Ändra server-blob som granskning för en viss server|
|/servers/automaticTuning/read|Returnerar automatisk justeringsinställningar för servern|
|/servers/automaticTuning/write|Uppdaterar inställningar för automatisk justering för servern och returnerar uppdaterade inställningarna|
|/servers/backupLongTermRetentionVaults/delete|Tar bort en befintlig arkivering säkerhetskopieringsvalvet.|
|/servers/backupLongTermRetentionVaults/read|Den här åtgärden används för att hämta en säkerhetskopiering för långsiktig kvarhållning valvet. Returnerar information om valvet som registrerats för den här servern|
|/servers/backupLongTermRetentionVaults/write|Den här åtgärden används för att registrera en säkerhetskopiering för långsiktig kvarhållning valvet till en server|
|/servers/communicationLinks/delete|Tar bort en befintlig server kommunikationskanal.|
|/servers/communicationLinks/read|Returnera listan över kommunikationslänkar för en server.|
|/servers/communicationLinks/write|Skapa eller uppdatera en server kommunikationslänk.|
|/servers/connectionPolicies/read|Returnera listan med principer för server-anslutning för en server.|
|/servers/connectionPolicies/write|Skapa eller uppdatera en princip för servern.|
|/Servers/Databases/Advisors/Read|Returnerar en lista över rådgivare som är tillgängliga för databasen|
|/servers/databases/advisors/recommendedActions/read|Returnerar en lista över rekommenderade åtgärder för angivna advisor för databasen|
|/servers/databases/advisors/recommendedActions/write|Tillämpa den rekommenderade åtgärden för databasen|
|/Servers/Databases/Advisors/Write|Uppdateringen automatiskt-köra status för en advisor på databasnivå.|
|/Servers/Databases/auditingPolicies/Read|Hämta information om tabellen granskningsprincip konfigureras på en viss databas|
|/servers/databases/auditingPolicies/write|Ändra tabellen granskningsprincipen för en viss databas|
|/servers/databases/auditingSettings/read|Hämta information om blob-granskningsprincip som konfigurerats på en viss databas|
|/servers/databases/auditingSettings/write|Ändra granskningsprincipen blob för en viss databas|
|/servers/databases/auditRecords/read|Hämta databasen blob granskningsposter|
|/Servers/Databases/automaticTuning/Read|Returnerar automatisk justeringsinställningar för en databas|
|/servers/databases/automaticTuning/write|Uppdaterar inställningar för automatisk justering för en databas och returnerar uppdaterade inställningarna|
|/servers/databases/azureAsyncOperation/read|Hämtar status för en databasåtgärd.|
|/servers/databases/backupLongTermRetentionPolicies/read|Returnera listan över säkerhetskopieringsprinciper för arkivering av en angiven databas.|
|/servers/databases/backupLongTermRetentionPolicies/write|Skapa eller uppdatera en databas arkivering säkerhetskopieringsprincip.|
|/Servers/Databases/connectionPolicies/Read|Hämta information om principen konfigurerats på en viss databas|
|/Servers/Databases/connectionPolicies/Write|Ändra princip för en viss databas|
|/servers/databases/dataMaskingPolicies/read|Returnera listan över databasdata maskering av principer.|
|/servers/databases/dataMaskingPolicies/rules/delete|Ta bort datamaskning principregel för en viss databas|
|/servers/databases/dataMaskingPolicies/rules/read|Hämta information om datamaskning principregeln som konfigurerats på en viss databas|
|/servers/databases/dataMaskingPolicies/rules/write|Ändra datamaskning principregel för en viss databas|
|/servers/databases/dataMaskingPolicies/write|Ändra datamaskning princip för en viss databas|
|/Servers/Databases/dataWarehouseQueries/dataWarehouseQuerySteps/Read|Returnerar den distribuerade fråga steg informationen av data warehouse fråga för valda steget-ID|
|/Servers/Databases/dataWarehouseQueries/Read|Returnerar data warehouse distribution läsa information för valda fråge-ID|
|/servers/databases/dataWarehouseUserActivities/read|Hämtar användaraktiviteter på en instans av SQL Data Warehouse som innehåller körs och avbrutna frågor|
|/Servers/Databases/delete|Tar bort en befintlig databas.|
|/Servers/Databases/export/Action|Exportera Azure SQL-databas|
|/servers/databases/extendedAuditingSettings/read|Hämta information om den utökade blobben granskningsprincip som konfigurerats på en viss databas|
|/servers/databases/extendedAuditingSettings/write|Ändra den utökade blobben granskningsprincipen för en viss databas|
|/servers/databases/extensions/read|Hämtar en uppsättning tillägg för databasen.|
|/servers/databases/extensions/write|Ändra filnamnstillägget för en viss databas|
|/Servers/Databases/geoBackupPolicies/Read|Hämta geo principer för säkerhetskopiering för en viss databas|
|/servers/databases/geoBackupPolicies/write|Skapa eller uppdatera en databas geobackup princip|
|/servers/databases/importExportOperationResults/read|Hämtar pågående import/export-åtgärder|
|/Servers/Databases/metricDefinitions/Read|Returnera typer av mätvärden som är tillgängliga för databaser|
|/Servers/Databases/Metrics/Read|Returnera mätvärden för databaser|
|/Servers/Databases/Move/Action|Byt namn på Azure SQL-databas|
|/servers/databases/operationResults/read|Hämtar status för en databasåtgärd.|
|/servers/databases/operations/cancel/action|Avbryter Azure SQL Database väntande asynkron åtgärd inte har slutförts ännu.|
|/Servers/Databases/Operations/Read|Returnera en lista över åtgärder som utförs på databasen|
|/Servers/Databases/pause/Action|Pausa Azure SQL Datawarehouse-databas|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/servers/databases/providers/Microsoft.Insights/logDefinitions/read|Hämtar tillgängliga loggar för databaser|
|/servers/databases/providers/Microsoft.Insights/metricDefinitions/read|Returnera typer av mätvärden som är tillgängliga för databaser|
|/servers/databases/queryStore/queryTexts/read|Returnerar frågan texter som är kopplade till de angivna parametrarna.|
|/servers/databases/queryStore/read|Returnerar aktuella värden för Query Store inställningar för databasen.|
|/servers/databases/queryStore/write|Uppdaterar inställningen för Frågearkivet för databasen|
|/Servers/Databases/Read|Returnera listan över databaser eller hämtar egenskaperna för den angivna databasen.|
|/Servers/Databases/replicationLinks/delete|Avsluta replikeringsrelationen tvång och potentiell dataförlust|
|/Servers/Databases/replicationLinks/failover/Action|Växling vid fel efter synkronisering alla ändras från primärt gör den här databasen till replikering relationship\u0027s primära och göra fjärransluten primär till en sekundär|
|/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action|Redundans omedelbart med potentiell dataförlust, vilket gör den här databasen till replikering relationship\u0027s primära och göra fjärransluten primär till en sekundär|
|/Servers/Databases/replicationLinks/Read|Returnerar information om replikeringslänkar som har upprättats för en viss databas|
|/servers/databases/replicationLinks/unlink/action|Avsluta replikeringsrelationen tvång eller efter synkronisering med partnern|
|/servers/databases/replicationLinks/updateReplicationMode/action|Uppdatera replikeringsläge för länken till synkron eller asynkron läge|
|/servers/databases/restorePoints/action|Skapar en ny återställningspunkt|
|/servers/databases/restorePoints/read|Returnerar återställningspunkter för databasen.|
|/Servers/Databases/Resume/Action|Återuppta Azure SQL Datawarehouse-databas|
|/servers/databases/schemas/read|Hämta listan över scheman för en databas|
|/Servers/Databases/schemas/Tables/columns/Read|Hämta listan över kolumner i en tabell|
|/servers/databases/schemas/tables/columns/sensitivityLabels/delete|Ta bort känslig etikett för en viss kolumn|
|/servers/databases/schemas/tables/columns/sensitivityLabels/read|Hämta känslig etikett för en viss kolumn|
|/servers/databases/schemas/tables/columns/sensitivityLabels/write|Skapa eller uppdatera känslig etikett för en viss kolumn|
|/servers/databases/schemas/tables/read|Hämta listan över tabeller i en databas|
|/servers/databases/schemas/tables/recommendedIndexes/read|Hämta listan över indexrekommendationer för en databas|
|/servers/databases/schemas/tables/recommendedIndexes/write|Tillämpa indexrekommendationen|
|/servers/databases/securityAlertPolicies/read|Hämta information om hot identifiering principen som konfigurerats på en viss databas|
|/servers/databases/securityAlertPolicies/write|Ändra threat detection principen för en viss databas|
|/servers/databases/securityMetrics/read|Hämtar en samling av databasen säkerhet mått|
|/servers/databases/sensitivityLabels/read|Lista känslighet etiketter för en viss databas|
|/servers/databases/serviceTierAdvisors/read|Returnera förslag om att skala databasen uppåt eller nedåt baserat på frågan körning statistik för att förbättra prestanda eller minska kostnaden för|
|/servers/databases/syncGroups/cancelSync/action|Avbryt synkronisering Gruppsynkronisering|
|/servers/databases/syncGroups/delete|Tar bort en befintlig grupp för synkronisering.|
|/servers/databases/syncGroups/hubSchemas/read|Returnera listan över sync hubb databasen scheman|
|/servers/databases/syncGroups/logs/read|Returnera listan över gruppen synkroniseringsloggar|
|/servers/databases/syncGroups/read|Returnera listan över grupper eller hämtar egenskaperna för den angivna sync-gruppen.|
|/servers/databases/syncGroups/refreshHubSchema/action|Uppdatera databasschemat för sync-hubb|
|/servers/databases/syncGroups/refreshHubSchemaOperationResults/read|Hämta resultatet av uppdateringsåtgärden sync hubb schema|
|/servers/databases/syncGroups/syncMembers/delete|Tar bort en befintlig sync-medlem.|
|/servers/databases/syncGroups/syncMembers/read|Returnera listan över sync medlemmar eller hämtar egenskaperna för den angivna sync-medlemmen.|
|/servers/databases/syncGroups/syncMembers/refreshSchema/action|Uppdatera synkroniseringsschema för medlemmen|
|/servers/databases/syncGroups/syncMembers/refreshSchemaOperationResults/read|Hämta resultatet av uppdateringsåtgärden sync medlem schema|
|/servers/databases/syncGroups/syncMembers/schemas/read|Returnera listan över sync medlem databasen scheman|
|/servers/databases/syncGroups/syncMembers/write|Skapar medlem synkronisering med de angivna parametrarna eller uppdaterar egenskaperna för den angivna sync-medlemmen.|
|/servers/databases/syncGroups/triggerSync/action|Utlösaren sync Gruppsynkronisering|
|/servers/databases/syncGroups/write|Skapar en grupp för synkronisering med de angivna parametrarna eller uppdaterar egenskaperna för den angivna sync-gruppen.|
|/Servers/Databases/topQueries/queryText/Action|Returnerar Transact-SQL-text för valda fråge-ID|
|/Servers/Databases/topQueries/Read|Returnerar samman körningsstatistik för den valda frågan i vald tidsperiod|
|/Servers/Databases/topQueries/statistics/Read|Returnerar samman körningsstatistik för den valda frågan i vald tidsperiod|
|/servers/databases/transparentDataEncryption/operationResults/read|Hämtar pågående åtgärder på transparent datakryptering|
|/servers/databases/transparentDataEncryption/read|Hämta status och information om transparent data kryptering säkerhetsfunktion för en viss databas|
|/servers/databases/transparentDataEncryption/write|Ändra krypteringsstatus för transparent data|
|/Servers/Databases/upgradeDataWarehouse/Action|Uppgradera Azure SQL Datawarehouse-databas|
|/Servers/Databases/usages/Read|Hämtar information för Azure SQL Database användningsområden|
|/Servers/Databases/vulnerabilityAssessments/delete|Ta bort vulnerability assessment för en viss databas|
|/Servers/Databases/vulnerabilityAssessments/Read|Hämta information om utvärdering säkerhetsrisker som konfigurerats på en viss databas|
|/Servers/Databases/vulnerabilityAssessments/rules/baselines/delete|Ta bort vulnerability assessment regeln baslinjen för en viss databas|
|/Servers/Databases/vulnerabilityAssessments/rules/baselines/Read|Hämta vulnerability assessment regeln baslinje för en viss databas|
|/Servers/Databases/vulnerabilityAssessments/rules/baselines/Write|Ändra vulnerability assessment regeln baslinjen för en viss databas|
|/Servers/Databases/vulnerabilityAssessments/scans/Action|Köra säkerhetsproblem genomsökning för utvärdering av databasen.|
|/Servers/Databases/vulnerabilityAssessments/scans/export/Action|Konvertera ett befintligt genomsökning resultat till ett format. Om det finns inget redan händer|
|/Servers/Databases/vulnerabilityAssessments/scans/Read|Returnerar listan över databasen vulnerability assessment genomsökning poster eller hämta genomsökning posten för den angivna sökning-ID.|
|/Servers/Databases/vulnerabilityAssessments/Write|Ändra vulnerability assessment för en viss databas|
|/Servers/Databases/vulnerabilityAssessmentScans/Action|Köra säkerhetsproblem genomsökning för utvärdering av databasen.|
|/servers/databases/vulnerabilityAssessmentScans/operationResults/read|Hämta resultatet av databasen vulnerability assessment genomsökning Kör åtgärden|
|/Servers/Databases/vulnerabilityAssessmentSettings/Read|Hämta information om utvärdering säkerhetsrisker som konfigurerats på en viss databas|
|/Servers/Databases/vulnerabilityAssessmentSettings/Write|Ändra vulnerability assessment för en viss databas|
|/Servers/Databases/Write|Skapar en databas med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna databasen.|
|/Servers/delete|Tar bort en befintlig server.|
|/servers/disasterRecoveryConfiguration/delete|Tar bort en befintlig konfigurationer med katastrofåterställning för en given server|
|/servers/disasterRecoveryConfiguration/failover/action|Redundans en DisasterRecoveryConfiguration|
|/servers/disasterRecoveryConfiguration/forceFailoverAllowDataLoss/action|Tvinga redundans en DisasterRecoveryConfiguration|
|/servers/disasterRecoveryConfiguration/read|Hämtar en samling av disaster recovery-konfigurationer som innehåller den här servern|
|/servers/disasterRecoveryConfiguration/write|Ändra serverkonfiguration disaster recovery|
|/servers/elasticPoolEstimates/read|Returnerar en lista över elastisk pool beräknar redan har skapats för den här servern|
|/servers/elasticPoolEstimates/write|Skapar ny elastiska pooluppskattningen lista över databaser som har angetts|
|/servers/elasticPools/advisors/read|Returnerar en lista över rådgivare som är tillgängliga för den elastiska poolen|
|/servers/elasticPools/advisors/recommendedActions/read|Returnerar en lista över rekommenderade åtgärder för angiven klassificering för den elastiska poolen|
|/servers/elasticPools/advisors/recommendedActions/write|Tillämpa den rekommenderade åtgärden på den elastiska poolen|
|/servers/elasticPools/advisors/write|Uppdateringen automatiskt-köra status för en advisor på elastisk pool-nivå.|
|/servers/elasticPools/databases/read|Hämtar en lista över databaser för en elastisk pool|
|/servers/elasticPools/delete|Ta bort befintlig elastisk pool|
|/servers/elasticPools/elasticPoolActivity/read|Hämta aktiviteter och information om en viss elastisk databaspool|
|/servers/elasticPools/elasticPoolDatabaseActivity/read|Hämta aktiviteter och information om en viss databas som ingår i en elastisk databaspool|
|/servers/elasticPools/metricDefinitions/read|Returnera typer av mätvärden som är tillgängliga för elastiska databaspooler|
|/servers/elasticPools/metrics/read|Returnera mått för elastiska databaspooler|
|/servers/elasticPools/operations/cancel/action|Avbryter SQL Azure elastisk pool väntande asynkron åtgärd inte har slutförts ännu.|
|/servers/elasticPools/operations/read|Returnera en lista över åtgärder som utförs på den elastiska poolen|
|/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/servers/elasticPools/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/servers/elasticPools/providers/Microsoft.Insights/metricDefinitions/read|Returnera typer av mätvärden som är tillgängliga för elastiska databaspooler|
|/servers/elasticPools/read|Hämta information om elastisk pool på en viss server|
|/servers/elasticPools/skus/read|Hämtar en samling av SKU: er som är tillgängliga för den här elastiska poolen|
|/servers/elasticPools/write|Skapa en ny eller ändra egenskaper för befintlig elastisk pool|
|/servers/encryptionProtector/read|Returnerar en lista över server kryptering skydd eller hämtar egenskaperna för den angivna servern kryptering skydd.|
|/servers/encryptionProtector/write|Uppdatera egenskaperna för den angivna servern kryptering skydd.|
|/servers/extendedAuditingSettings/read|Hämta information om den utökade server blob granskningsprincip som konfigurerats på en viss server|
|/servers/extendedAuditingSettings/write|Ändra den utökade servern blobbgranskning för en viss server|
|/servers/failoverGroups/delete|Tar bort en befintlig grupp för växling vid fel.|
|/servers/failoverGroups/failover/action|Kör planerad redundans i en befintlig grupp för växling vid fel.|
|/servers/failoverGroups/forceFailoverAllowDataLoss/action|Kör framtvingad växling vid fel i en befintlig grupp för växling vid fel.|
|/servers/failoverGroups/read|Returnerar listan över redundans grupper eller hämtar egenskaperna för den angivna failover-gruppen.|
|/servers/failoverGroups/write|Skapar en redundansväxlingsgrupp med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna failover-gruppen.|
|/Servers/firewallRules/delete|Tar bort en befintlig brandväggsregel för servern.|
|/Servers/firewallRules/Read|Returnera listan över serverbrandvägg regler eller hämtar egenskaperna för den angivna servern brandväggsregel.|
|/Servers/firewallRules/Write|Skapar en brandväggsregel för servern med de angivna parametrarna uppdatera egenskaperna för den angivna regeln eller skriva över alla befintliga regler med nya regler för server-brandväggen.|
|/Servers/import/Action|Skapa en ny databas på servern och distribuera schema och data från ett DacPac-paket|
|/servers/importExportOperationResults/read|Hämtar pågående import/export-åtgärder|
|/servers/keys/delete|Tar bort en befintlig servernyckel.|
|/servers/keys/read|Returnera listan över server nycklar eller hämtar egenskaperna för den angivna servernyckeln.|
|/servers/keys/write|Skapar en nyckel med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servernyckeln.|
|/servers/operationResults/read|Hämtar pågående åtgärder|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Returnera typer av mått som är tillgängliga för servrar|
|/Servers/Read|Returnera listan över servrar eller hämtar egenskaperna för den angivna servern.|
|/servers/recommendedElasticPools/databases/read|Hämta mätvärden för rekommenderade elastiska databaspoolerna för en viss server|
|/servers/recommendedElasticPools/read|Hämta rekommendation för elastiska databaspooler att minska kostnaderna eller förbättra prestandan, utifrån historica resursutnyttjande|
|/servers/recoverableDatabases/read|Den här åtgärden används för katastrofåterställning av live-databasen för att återställa databasen till senast fungerande säkerhetskopieringspunkt. Den returnerar information om den senaste korrekta säkerhetskopian men doesn\u0027t verkligen återställa databasen.|
|/servers/restorableDroppedDatabases/read|Hämta en lista över databaser som har släppts på en viss server som är fortfarande inom bevarandeprincip.|
|/servers/securityAlertPolicies/operationResults/read|Hämta resultaten av Skrivåtgärden server threat detection princip|
|/servers/securityAlertPolicies/read|Hämta information om server threat detection principen konfigurerats på en viss server|
|/servers/securityAlertPolicies/write|Ändra server threat detection principen för en given server|
|/Servers/serviceObjectives/Read|Hämta lista över servicenivåmål (även kallat prestandanivåer) finns på en viss server|
|/servers/syncAgents/delete|Tar bort en befintlig sync-agent.|
|/servers/syncAgents/generateKey/action|Generera sync agent registrering nyckel|
|/servers/syncAgents/linkedDatabases/read|Returnera listan över synkronisera agent länkade databaser|
|/servers/syncAgents/read|Returnera listan över sync agenter eller hämtar egenskaperna för den angivna sync-agenten.|
|/servers/syncAgents/write|Skapar en agent för synkronisering med de angivna parametrarna eller uppdaterar egenskaperna för den angivna sync-agenten.|
|/servers/usages/read|Returnera DTU-kvot för server och den aktuella DTU consuption alla databaser på servern|
|/servers/virtualNetworkRules/delete|Tar bort en befintlig regel för virtuella nätverk|
|/servers/virtualNetworkRules/read|Returnera listan över virtuella nätverk regler eller hämtar egenskaperna för den angivna virtuella nätverk regeln.|
|/Servers/virtualNetworkRules/Write|Skapar ett virtuellt nätverk med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna virtuella nätverk regeln.|
|/ servrar/skrivning|Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern.|
|/unregister/action|Avregistrerar prenumerationen för resursprovidern Microsoft SQL-databasen och kan skapa Microsoft SQL-databaser.|
|/virtualClusters/Read|Returnera listan med virtuella kluster eller hämtar egenskaperna för det angivna virtuella klustret.|
|/ virtualClusters/skrivning|Uppdaterar virtuellt kluster taggar.|

## <a name="microsoftstorage"></a>Microsoft.Storage

| Åtgärd | Beskrivning |
|---|---|
|/checknameavailability/Read|Kontrollerar att kontonamnet är giltigt och används.|
|/locations/deleteVirtualNetworkOrSubnets/action|Aviserar Microsoft.Storage om att det virtuella nätverket eller undernätet tas bort|
|/Operations/Read|Avsöker status för en asynkron åtgärd.|
|registrera/åtgärd|Registrerar prenumerationen för lagringsresursprovidern och gör det möjligt att skapa lagringskonton.|
|/skus/Read|Listar SKU:erna som stöds av Microsoft.Storage.|
|/storageAccounts/blobServices/containers/clearLegalHold/action|Ta bort bevarande av juridiska skäl för blobbehållare|
|/storageAccounts/blobServices/containers/delete|Returnerar resultatet av att ta bort en behållare|
|/storageAccounts/blobServices/containers/immutabilityPolicies/delete|Ta bort oföränderlighetsprincipen för blobbehållaren|
|/storageAccounts/blobServices/containers/immutabilityPolicies/extend/action|Utöka oföränderlighetsprincip för blobbehållare|
|/storageAccounts/blobServices/containers/immutabilityPolicies/lock/action|Lås oföränderlighetsprincip för blobbehållare|
|/storageAccounts/blobServices/containers/immutabilityPolicies/read|Hämta oföränderlighetsprincip för blobbehållare|
|/storageAccounts/blobServices/containers/immutabilityPolicies/write|Placera oföränderlighetsprincip för blobbehållare|
|/storageAccounts/blobServices/containers/read|Returnerar en behållare eller behållarlista|
|/storageAccounts/blobServices/containers/setLegalHold/action|Ställ in bevarande av juridiska skäl för blobbehållare|
|/storageAccounts/blobServices/containers/write|Returnerar resultatet av att placera eller låna blobbehållare|
|/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/read|Hämta diagnostikinställningen för resursen.|
|/storageAccounts/blobServices/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningen för resursen.|
|/storageAccounts/blobServices/providers/Microsoft.Insights/metricDefinitions/read|Hämta lista över Microsofts lagringsmåttdefinitioner.|
|/storageAccounts/blobServices/read|Returnerar egenskaper för Blob Service eller statistik|
|/storageAccounts/blobServices/write|Returnerar resultatet av att placera Blob Service-egenskaper|
|/storageAccounts/delete|Tar bort ett befintligt lagringskonto.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/read|Hämta diagnostikinställningen för resursen.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningen för resursen.|
|/storageAccounts/fileServices/providers/Microsoft.Insights/metricDefinitions/read|Hämta lista över Microsofts lagringsmåttdefinitioner.|
|/storageAccounts/listAccountSas/action|Returnerar SAS-token för konto för det angivna lagringskontot.|
|/storageAccounts/listkeys/action|Returnerar åtkomstnycklarna för det angivna lagringskontot.|
|/storageAccounts/listServiceSas/action|Returnerar SAS-token för tjänst för det angivna lagringskontot.|
|/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Hämta diagnostikinställningen för resursen.|
|/storageAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningen för resursen.|
|/storageAccounts/providers/Microsoft.Insights/metricDefinitions/read|Hämta lista över Microsofts lagringsmåttdefinitioner.|
|/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningen för resursen.|
|/storageAccounts/queueServices/providers/Microsoft.Insights/metricDefinitions/read|Hämta lista över Microsofts lagringsmåttdefinitioner.|
|/storageAccounts/queueServices/queues/delete|Returnerar resultatet av att ta bort en kö|
|/storageAccounts/queueServices/queues/read|Returnerar en kö eller kölista.|
|/storageAccounts/queueServices/queues/write|Returnerar resultatet av att skriva en kö|
|/storageAccounts/queueServices/read|Returnerar egenskaper för kötjänst eller statistik.|
|/storageAccounts/queueServices/write|Returnerar resultatet av att ställa in egenskaper för kötjänst|
|/storageAccounts/Read|Returnerar listan med lagringskonton eller hämtar egenskaperna för det angivna lagringskontot.|
|/storageAccounts/regeneratekey/action|Återskapar åtkomstnycklarna för det angivna lagringskontot.|
|/storageAccounts/services/diagnosticSettings/write|Skapa/uppdatera diagnostikinställningar för lagringskontot.|
|/ storageAccounts/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/read|Hämta diagnostikinställningen för resursen.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read|Hämta diagnostikinställningen för resursen.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningen för resursen.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read|Hämta lista över Microsofts lagringsmåttdefinitioner.|
|/ storageAccounts/skrivning|Skapar ett lagringskonto med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna, eller lägger till anpassad domän för det angivna lagringskontot.|
|/usages/Read|Returnerar gränsen och det aktuella antalet användningar för resurser i den angivna prenumerationen|

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

| Åtgärd | Beskrivning |
|---|---|
|/storageSyncServices/delete|Ta bort alla lagringstjänster för synkronisering|
|/storageSyncServices/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för lagring Sync Services|
|/storageSyncServices/read|Läsa alla lagringstjänster för synkronisering|
|/storageSyncServices/registeredServers/delete|Ta bort alla registrerade servrar|
|/storageSyncServices/registeredServers/read|Läsa alla registrerade servrar|
|/storageSyncServices/registeredServers/write|Skapa eller uppdatera alla registrerade servrar|
|/storageSyncServices/syncGroups/cloudEndpoints/delete|Ta bort alla moln-slutpunkter|
|/storageSyncServices/syncGroups/cloudEndpoints/operationresults/read|Plats-api för säkerhetskopiering async-anrop|
|/storageSyncServices/syncGroups/cloudEndpoints/postbackup/action|Anropa den här åtgärden efter säkerhetskopiering|
|/storageSyncServices/syncGroups/cloudEndpoints/postrestore/action|Anropa den här åtgärden efter återställning|
|/storageSyncServices/syncGroups/cloudEndpoints/prebackup/action|Anropa den här åtgärden före säkerhetskopiering|
|/storageSyncServices/syncGroups/cloudEndpoints/prerestore/action|Anropa den här åtgärden innan återställningen|
|/storageSyncServices/syncGroups/cloudEndpoints/read|Läsa alla moln-slutpunkter|
|/storageSyncServices/syncGroups/cloudEndpoints/restoreheartbeat/action|Återställa pulsslag|
|/storageSyncServices/syncGroups/cloudEndpoints/write|Skapa eller uppdatera alla moln-slutpunkter|
|/storageSyncServices/syncGroups/delete|Ta bort alla synkroniseringsgrupper|
|/storageSyncServices/syncGroups/read|Läsa alla synkroniseringsgrupper|
|/storageSyncServices/syncGroups/serverEndpoints/delete|Ta bort alla Server-slutpunkter|
|/storageSyncServices/syncGroups/serverEndpoints/read|Läsa Server-slutpunkter|
|/storageSyncServices/syncGroups/serverEndpoints/recallAction/action|Anropa den här åtgärden för att återställa filer till en server|
|/storageSyncServices/syncGroups/serverEndpoints/write|Skapa eller uppdatera några Server-slutpunkter|
|/storageSyncServices/syncGroups/write|Skapa eller uppdatera alla synkroniseringsgrupper|
|/storageSyncServices/write|Skapa eller uppdatera alla lagringstjänster för synkronisering|

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

| Åtgärd | Beskrivning |
|---|---|
|/managers/accessControlRecords/delete|Tar bort Access Control-poster|
|/managers/accessControlRecords/read|Visar eller hämtar Access Control-poster|
|/managers/accessControlRecords/write|Skapa eller uppdatera Access Control-poster|
|/Managers/Alerts/Read|Visar eller hämtar aviseringar|
|/Managers/bandwidthSettings/delete|Tar bort en befintlig bandbreddsinställningar (endast 8000-serien)|
|/Managers/bandwidthSettings/Read|Visa en lista med bandbreddsinställningarna (endast 8000-serien)|
|/Managers/bandwidthSettings/Write|Skapar en ny eller uppdaterar bandbreddsinställningar (endast 8000-serien)|
|Och chefer/certifikat/skrivning|Uppdatera resurs certifikat åtgärden uppdaterar Autentiseringscertifikatet resurs-valvet.|
|/Managers/clearAlerts/Action|Avmarkera alla aviseringar som är associerade med Enhetshanteraren.|
|/managers/cloudApplianceConfigurations/read|Lista molntjänster anordningen konfigurationer som stöds|
|/Managers/configureDevice/Action|Konfigurerar en enhet|
|/Managers/delete|Tar bort enheten chefer|
|/ Chefer/ta bort|Ta bort valvet åtgärden tar bort angivna Azure-resurs av typen 'valvet'|
|/Managers/Devices/alertSettings/Read|Visar eller hämtar de aviseringsinställningar|
|/managers/devices/alertSettings/write|Skapa eller uppdatera aviseringsinställningarna|
|/managers/devices/backupPolicies/backup/action|Ta en manuell säkerhetskopiering för att skapa en på-begäran säkerhetskopiering av alla volymer som skyddas av principen.|
|/managers/devices/backupPolicies/delete|Tar bort en befintlig säkerhetskopia principer (endast 8000-serien)|
|/managers/devices/backupPolicies/read|Lista säkerhetskopieringen principer (endast 8000-serien)|
|/managers/devices/backupPolicies/schedules/delete|Tar bort en befintlig scheman|
|/managers/devices/backupPolicies/schedules/read|Visa en lista över scheman|
|/managers/devices/backupPolicies/schedules/write|Skapar en ny eller uppdaterar scheman|
|/managers/devices/backupPolicies/write|Skapar en ny eller uppdaterar principerna för säkerhetskopiering (endast 8000-serien)|
|/managers/devices/backups/delete|Tar bort säkerhetskopian|
|/managers/devices/backups/elements/clone/action|Klona en resurs eller en volym med en säkerhetskopiering element.|
|/managers/devices/backups/read|Visar eller hämtar säkerhetskopia|
|/managers/devices/backups/restore/action|Återställa alla volymer från en säkerhetskopia.|
|/managers/devices/backupScheduleGroups/delete|Tar bort schemat för säkerhetskopiering-grupper|
|/managers/devices/backupScheduleGroups/read|Visar eller hämtar grupperna schema för säkerhetskopiering|
|/managers/devices/backupScheduleGroups/write|Skapa eller uppdatera grupperna schema för säkerhetskopiering|
|/managers/devices/chapSettings/delete|Tar bort Chap-inställningar|
|/managers/devices/chapSettings/read|Visar eller hämtar Chap-inställningar|
|/managers/devices/chapSettings/write|Skapa eller uppdatera Chap-inställningar|
|/Managers/Devices/deactivate/Action|Inaktiverar en enhet.|
|/managers/devices/delete|Tar bort enheter|
|/Managers/Devices/Download/Action|Download uppdateringar för en enhet.|
|/managers/devices/failover/action|Redundans av enheten.|
|/managers/devices/fileservers/backup/action|Ta säkerhetskopior av en filserver.|
|/managers/devices/fileservers/delete|Tar bort filservrar|
|/managers/devices/fileservers/metrics/read|Visar eller hämtar mätvärden|
|/managers/devices/fileservers/metricsDefinitions/read|Visar eller hämtar mått definitioner|
|/managers/devices/fileservers/read|Visar eller hämtar filservrar|
|/managers/devices/fileservers/shares/delete|Tar bort resurserna|
|/managers/devices/fileservers/shares/metrics/read|Visar eller hämtar mätvärden|
|/managers/devices/fileservers/shares/metricsDefinitions/read|Visar eller hämtar mått definitioner|
|/managers/devices/fileservers/shares/read|Visar eller hämtar resurserna|
|/managers/devices/fileservers/shares/write|Skapa eller uppdatera resurser|
|/managers/devices/fileservers/write|Skapa eller uppdatera filservrar|
|/managers/devices/hardwareComponentGroups/changeControllerPowerState/action|Ändra domänkontrollant energisparläge av maskinvara komponentgrupper|
|/managers/devices/hardwareComponentGroups/read|Visa en lista över maskinvara komponentgrupper|
|/managers/devices/install/action|Installera uppdateringar på en enhet.|
|/managers/devices/installUpdates/action|Installerar uppdateringar på enheter|
|/managers/devices/iscsiservers/backup/action|Ta säkerhetskopior av en iSCSI-server.|
|/managers/devices/iscsiservers/delete|Tar bort iSCSI-servrar|
|/managers/devices/iscsiservers/disks/delete|Tar bort diskarna|
|/managers/devices/iscsiservers/disks/metrics/read|Visar eller hämtar mätvärden|
|/managers/devices/iscsiservers/disks/metricsDefinitions/read|Visar eller hämtar mått definitioner|
|/managers/devices/iscsiservers/disks/read|Hämtar diskarna eller visar|
|/managers/devices/iscsiservers/disks/write|Skapa eller uppdatera diskar|
|/managers/devices/iscsiservers/metrics/read|Visar eller hämtar mätvärden|
|/managers/devices/iscsiservers/metricsDefinitions/read|Visar eller hämtar mått definitioner|
|/managers/devices/iscsiservers/read|Visar eller hämtar iSCSI-servrar|
|/managers/devices/iscsiservers/write|Skapa eller uppdatera iSCSI-servrar|
|/managers/devices/jobs/cancel/action|Avbryta ett jobb som körs|
|/managers/devices/jobs/read|Visar eller hämtar jobb|
|/managers/devices/listFailoverSets/action|Lista för växling vid fel anger för en befintlig enhet.|
|/managers/devices/listFailoverTargets/action|Lista redundansmål enheter|
|/Managers/Devices/Metrics/Read|Visar eller hämtar mätvärden|
|/managers/devices/metricsDefinitions/read|Visar eller hämtar mått definitioner|
|/managers/devices/migrationSourceConfigurations/confirmMigration/action|Bekräftar en lyckad migrering och sparar den.|
|/managers/devices/migrationSourceConfigurations/fetchConfirmMigrationStatus/action|Hämta bekräftelsestatus för migreringen.|
|/managers/devices/migrationSourceConfigurations/fetchMigrationEstimate/action|Hämta status för beräkning av migreringsjobb.|
|/managers/devices/migrationSourceConfigurations/fetchMigrationStatus/action|Hämta status för migreringen.|
|/managers/devices/migrationSourceConfigurations/import/action|Importera källa konfigurationer för migrering|
|/managers/devices/migrationSourceConfigurations/startMigration/action|Starta migrering med hjälp av konfigurationer för källa|
|/managers/devices/migrationSourceConfigurations/startMigrationEstimate/action|Starta ett jobb för att beräkna varaktigheten för migreringen.|
|/managers/devices/networkSettings/read|Visar eller hämtar nätverksinställningar|
|/managers/devices/networkSettings/write|Skapar en ny eller uppdaterar nätverksinställningar|
|/managers/devices/publicEncryptionKey/action|Offentlig krypteringsnyckel för listan med Enhetshanteraren|
|/managers/devices/publishSupportPackage/action|Publicera supportpaket för en enhet för felsökning av Microsoft-supporten.|
|/Managers/Devices/Read|Hämtar enheterna eller visar|
|/managers/devices/scanForUpdates/action|Sök efter uppdateringar i en enhet.|
|/managers/devices/securitySettings/read|Visa en lista med säkerhetsinställningar|
|/managers/devices/securitySettings/syncRemoteManagementCertificate/action|Synkronisera certifikat för fjärrhantering för en enhet.|
|/managers/devices/securitySettings/update/action|Uppdatera säkerhetsinställningar.|
|/managers/devices/securitySettings/write|Skapar en ny eller uppdaterar säkerhetsinställningar|
|/managers/devices/sendTestAlertEmail/action|Skicka avisering testmeddelandet till konfigurerade e-postmottagare.|
|/Managers/Devices/timeSettings/Read|Visar eller hämtar inställningarna för tid|
|/managers/devices/timeSettings/write|Skapar en ny eller uppdaterar tidsinställningar|
|/managers/devices/updateSummary/read|Hämtar uppdatering sammanfattningen eller visar|
|/managers/devices/volumeContainers/delete|Tar bort en befintlig Volymbehållare (endast 8000-serien)|
|/managers/devices/volumeContainers/listEncryptionKeys/action|Lista krypteringsnycklar för Volymbehållare|
|/managers/devices/volumeContainers/metrics/read|Visa en lista över mätvärdena|
|/managers/devices/volumeContainers/metricsDefinitions/read|Visa en lista med definitionerna som mått|
|/managers/devices/volumeContainers/read|Visa en lista över Volymbehållarna (endast 8000-serien)|
|/managers/devices/volumeContainers/rolloverEncryptionKey/action|Krypteringsnycklarna för förnyelse av Volymbehållare|
|/managers/devices/volumeContainers/volumes/delete|Tar bort en befintlig volymer|
|/managers/devices/volumeContainers/volumes/metrics/read|Visa en lista över mätvärdena|
|/managers/devices/volumeContainers/volumes/metricsDefinitions/read|Visa en lista med definitionerna som mått|
|/managers/devices/volumeContainers/volumes/read|Visa en lista över volymer|
|/managers/devices/volumeContainers/volumes/write|Skapar en ny eller uppdaterar volymer|
|/managers/devices/volumeContainers/write|Skapar en ny eller uppdaterar Volymbehållare (endast 8000-serien)|
|/Managers/Devices/Write|Skapa eller uppdatera enheterna|
|/Managers/encryptionSettings/Read|Visar eller hämtar krypteringsinställningar|
|/Managers/extendedInformation/delete|Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv?|
|/Managers/extendedInformation/read|Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv?|
|/Managers/extendedInformation/write|Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv?|
|/managers/getActivationKey/action|Hämta aktiveringsnyckeln för Enhetshanteraren.|
|/managers/getEncryptionKey/action|Hämta krypteringsnyckeln för Enhetshanteraren.|
|/managers/listActivationKey/action|Hämtar aktiveringsnyckeln av StorSimple Enhetshanteraren.|
|/managers/listPrivateEncryptionKey/action|Hämtar privata krypteringsnyckel för en StorSimple Device Manager.|
|/managers/listPublicEncryptionKey/action|Lista över offentliga krypteringsnycklar en StorSimple Enhetshanteraren.|
|/Managers/Metrics/Read|Visar eller hämtar mätvärden|
|/Managers/metricsDefinitions/Read|Visar eller hämtar mått definitioner|
|/managers/provisionCloudAppliance/action|Skapa en ny installation av molnet.|
|/Managers/Read|Visar eller hämtar enheten chefer|
|/ Chefer/läsning|Åtgärden hämta valvet hämtar ett objekt som representerar Azure-resurs av typen 'valvet'|
|/managers/regenarateRegistationCertificate/action|Återskapa registreringscertifikat för enhetshanterare.|
|/managers/regenerateActivationKey/action|Återskapa aktiveringsnyckeln för Enhetshanteraren.|
|/managers/storageAccountCredentials/delete|Tar bort autentiseringsuppgifter för Lagringskonto|
|/managers/storageAccountCredentials/listAccessKey/action|Lista över åtkomstnycklar för lagring av autentiseringsuppgifter för konton|
|/managers/storageAccountCredentials/read|Visar eller hämtar autentiseringsuppgifter för Lagringskonto|
|/managers/storageAccountCredentials/write|Skapa eller uppdatera autentiseringsuppgifter för Lagringskonto|
|/managers/storageDomains/delete|Tar bort Storage-domäner|
|/managers/storageDomains/read|Visar eller hämtar lagring-domäner|
|/managers/storageDomains/write|Skapa eller uppdatera lagring-domäner|
|/ chefer och skrivning|Skapa eller uppdatera enheten chefer|
|Och chefer/skrivning|Med skapa valv så skapas en Azure-resurs av typen valv|

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

| Åtgärd | Beskrivning |
|---|---|
|/Locations/Quotas/Read|Läs Stream Analytics prenumerationens kvoter|
|/ operations/Läs|Läs Stream Analytics-åtgärder|
|/ Register/åtgärd|Registrera prenumerationen med Stream Analytics-Resursprovidern|
|/streamingjobs/Delete|Delete Stream Analytics Job|
|/streamingjobs/Functions/delete|Ta bort Stream Analytics-jobbfunktionen|
|/streamingjobs/Functions/operationresults/Read|Läs Åtgärdsresultat för Stream Analytics-jobbfunktion|
|/streamingjobs/Functions/Read|Läs Stream Analytics-jobbfunktion|
|/streamingjobs/Functions/RetrieveDefaultDefinition/Action|Hämta standarddefinitionen för en funktion i Stream Analytics-jobbet|
|/streamingjobs/Functions/test/Action|Stream Analytics-jobbet funktionsnamnet|
|/streamingjobs/Functions/Write|Skriva Stream Analytics-jobbfunktion|
|/streamingjobs/inputs/delete|Ta bort Stream Analytics-jobbet indata|
|/streamingjobs/inputs/operationresults/Read|Läs Åtgärdsresultat för Stream Analytics-jobbet indata|
|/streamingjobs/inputs/Read|Läs Stream Analytics-jobbet indata|
|/streamingjobs/inputs/Sample/Action|Stream Analytics-jobbet Exempelindata|
|/streamingjobs/inputs/test/Action|Testa Stream Analytics-jobbet indata|
|/streamingjobs/inputs/Write|Skriva Stream Analytics-jobbet indata|
|/streamingjobs/metricdefinitions/Read|Läs Måttdefinitionerna|
|/streamingjobs/operationresults/Read|Läs Åtgärdsresultat för Stream Analytics-jobbet|
|/streamingjobs/outputs/Delete|Ta bort Stream Analytics-Jobbutdata|
|/streamingjobs/outputs/operationresults/Read|Läs Åtgärdsresultat för Stream Analytics-Jobbutdata|
|/streamingjobs/outputs/Read|Läs Stream Analytics-Jobbutdata|
|/streamingjobs/outputs/test/Action|Testa Stream Analytics-Jobbutdata|
|/streamingjobs/outputs/Write|Skriva Stream Analytics-Jobbutdata|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read|Läsa diagnostikinställningen.|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write|Skriva diagnostikinställningen.|
|/streamingjobs/providers/Microsoft.Insights/logDefinitions/read|Hämtar de tillgängliga loggarna för streamingjobs|
|/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för streamingjobs|
|/ streamingjobs/Läs|Read Stream Analytics Job|
|/streamingjobs/Start/Action|Start Stream Analytics Job|
|/streamingjobs/stop/Action|Stop Stream Analytics Job|
|/streamingjobs/Transformations/delete|Ta bort Stream Analytics-jobbet omvandling|
|/streamingjobs/Transformations/Read|Läs Stream Analytics-jobbet omvandling|
|/streamingjobs/Transformations/Write|Skriva Stream Analytics-jobbet omvandling|
|/ streamingjobs/skrivning|Write Stream Analytics Job|

## <a name="microsoftsubscription"></a>Microsoft.Subscription

| Åtgärd | Beskrivning |
|---|---|
|/ SubscriptionDefinitions/läsning|Hämta en Azure-Prenumerationsdefinitionen i en hanteringsgrupp.|
|/SubscriptionDefinitions/write|Skapa en definition för Azure-prenumeration|

## <a name="microsoftsupport"></a>Microsoft.Support

| Åtgärd | Beskrivning |
|---|---|
|registrera/åtgärd|Registrerar till supportresursprovidern|
|/supportTickets/Read|Hämtar information om supportärendet (inklusive status, allvarlighetsgrad, kontaktinformation och kommunikation) eller hämtar listan över supportärenden för alla prenumerationer.|
|/ supportTickets/skrivning|Skapar eller uppdaterar ett supportärende. Du kan skapa ett supportärende för tekniska, fakturering, kvoter eller prenumerationshantering relaterade problem. Du kan uppdatera allvarlighetsgrad, kontaktinformation och kommunikation för befintliga supportärenden.|

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

| Åtgärd | Beskrivning |
|---|---|
|/Environments/accesspolicies/delete|Tar bort åtkomstprincipen.|
|/Environments/accesspolicies/Read|Hämta egenskaperna för en åtkomstprincip.|
|/Environments/accesspolicies/Write|Skapar en ny åtkomstprincip för en miljö eller uppdaterar en befintlig åtkomstprincip.|
|/Environments/delete|Tar bort miljön.|
|/Environments/eventsources/delete|Tar bort händelsekällan.|
|/Environments/eventsources/eventsources/providers/Microsoft.Insights/ diagnosticSettings/skrivning|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/environments/eventsources/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/environments/eventsources/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för eventsources|
|/Environments/eventsources/Read|Hämta egenskaperna för en händelsekälla.|
|/Environments/eventsources/Write|Skapar en ny händelsekälla för en miljö eller uppdaterar en befintlig händelsekälla.|
|/environments/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/environments/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/environments/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för miljöer|
|/Environments/Read|Hämta egenskaperna för en miljö.|
|/Environments/referencedatasets/delete|Tar bort datamängden som referens.|
|/Environments/referencedatasets/Read|Hämta egenskaperna för en datauppsättning för referens.|
|/Environments/referencedatasets/Write|Skapar en ny referens datauppsättning för en miljö eller uppdaterar en befintlig datamängd referens.|
|/Environments/status/Read|Hämta status för miljön, tillståndet för dess associerade åtgärder som ingång.|
|/ miljöer/skrivning|Skapar en ny miljö eller uppdaterar en befintlig miljö.|
|registrera/åtgärd|Registrerar prenumerationen för resursprovidern tid serien insikter och kan skapa tid serien insikter miljöer.|

## <a name="microsoftweb"></a>microsoft.web

| Åtgärd | Beskrivning |
|---|---|
|/apimanagementaccounts/apiacls/Read|Hämta Api Management konton Apiacls.|
|/apimanagementaccounts/Apis/apiacls/delete|Ta bort Api Management konton API: er Apiacls.|
|/apimanagementaccounts/Apis/apiacls/Read|Hämta Api Management konton API: er Apiacls.|
|/apimanagementaccounts/Apis/apiacls/Write|Uppdatera Apiacls för Api Management konton API: er.|
|/apimanagementaccounts/Apis/connectionacls/Read|Hämta Api Management konton API: er Connectionacls.|
|/apimanagementaccounts/apis/connections/confirmconsentcode/action|Bekräfta ditt medgivande kod Api Management konton API: er anslutningar.|
|/apimanagementaccounts/apis/connections/connectionacls/delete|Ta bort Api Management konton API: er anslutningar Connectionacls.|
|/apimanagementaccounts/Apis/Connections/connectionacls/Read|Hämta Api Management konton API: er anslutningar Connectionacls.|
|/apimanagementaccounts/Apis/Connections/connectionacls/Write|Uppdatera Api Management konton API: er anslutningar Connectionacls.|
|/apimanagementaccounts/apis/connections/delete|Ta bort Api Management konton API: er anslutningar.|
|/apimanagementaccounts/apis/connections/getconsentlinks/action|Få godkännande länkar för Api Management konton API: er anslutningar.|
|/apimanagementaccounts/Apis/Connections/listconnectionkeys/Action|Lista över anslutning nycklar Api Management konton API: er anslutningar.|
|/apimanagementaccounts/Apis/Connections/listsecrets/Action|Lista hemligheter Api Management konton API: er anslutningar.|
|/apimanagementaccounts/Apis/Connections/Read|Hämta Api Management konton API: er anslutningar.|
|/apimanagementaccounts/Apis/Connections/Write|Uppdatera Api Management konton API: er anslutningar.|
|/apimanagementaccounts/Apis/delete|Ta bort API: er för Api Management-konton.|
|/apimanagementaccounts/apis/localizeddefinitions/delete|Ta bort Api Management API: er för konton lokaliserade definitioner.|
|/apimanagementaccounts/apis/localizeddefinitions/read|Hämta Api-hantering konton API: er lokaliserade definitioner.|
|/apimanagementaccounts/apis/localizeddefinitions/write|Uppdatera Api Management konton API: er lokaliserade definitioner.|
|/apimanagementaccounts/Apis/Read|Hämta API: er för Api Management-konton.|
|/apimanagementaccounts/Apis/Write|Uppdatera API: er för Api Management-konton.|
|/apimanagementaccounts/connectionacls/Read|Hämta Api Management konton Connectionacls.|
|/availablestacks/Read|Hämta tillgängliga grupper.|
|/billingmeters/Read|Hämta lista över fakturering mätare.|
|/ certifikat/ta bort|Ta bort ett befintligt certifikat.|
|/ certifikat/Läs|Hämta listan över certifikat.|
|/ certifikat/skrivning|Lägg till ett nytt certifikat eller uppdatera en befintlig.|
|/checknameavailability/Read|Kontrollera om resursnamnet är tillgängliga.|
|/classicmobileservices/Read|Hämta klassiska Mobile Services.|
|/connectionGateways/Delete|Tar bort en Gateway med anslutning.|
|/connectionGateways/Join/Action|Ansluter till en Gateway med anslutning.|
|/connectiongateways/Liststatus/Action|Visa Status för anslutningen Gateways.|
|/connectionGateways/ListStatus/Action|Visar status för en Gateway med anslutning.|
|/connectionGateways/Move/Action|Flyttar en Gateway med anslutning.|
|/connectionGateways/Read|Hämta listan över anslutningen Gateways.|
|/connectionGateways/Write|Skapar eller uppdaterar en Gateway med anslutning.|
|/connections/confirmconsentcode/action|Bekräfta anslutningar medgivande kod.|
|anslutningar/ta bort|Tar bort en anslutning.|
|/Connections/JOIN/Action|Ansluter till en anslutning.|
|/connections/listconsentlinks/action|Länkar i listan medgivande för anslutningar.|
|/Connections/Move/Action|Flyttar en anslutning.|
|anslutningar/Läs|Hämta listan över anslutningar.|
|anslutningar/skrivning|Skapar eller uppdaterar en anslutning.|
|/customApis/Delete|Tar bort en anpassad API.|
|/customApis/extractApiDefinitionFromWsdl/Action|Extraherar API-definition från WSDL.|
|/customApis/Join/Action|Ansluter till en anpassad API.|
|/customApis/listWsdlInterfaces/Action|Listar WSDL-gränssnitt för en anpassad API.|
|/customApis/Move/Action|Flyttar en anpassad API.|
|/customApis/Read|Hämta listan över anpassade API.|
|/customApis/Write|Skapar eller uppdaterar en anpassad API.|
|/deploymentlocations/Read|Hämta platser för distributionen.|
|/geoRegions/Read|Hämta listan över geografiska regionerna.|
|/hostingenvironments/capacities/Read|Hämta värd miljöer kapacitet.|
|/hostingEnvironments/Delete|Ta bort Apptjänst-miljö|
|/hostingenvironments/Diagnostics/Read|Hämta värd miljöer diagnostik.|
|/hostingenvironments/inboundnetworkdependenciesendpoints/read|Hämta nätverksslutpunkter av alla inkommande beroenden.|
|/hostingenvironments/metricdefinitions/Read|Hämta värd miljöer Måttdefinitioner.|
|/hostingenvironments/multirolepools/metricdefinitions/Read|Hämta värd miljöer mellan pooler Måttdefinitioner.|
|/hostingenvironments/multirolepools/Metrics/Read|Hämta värd miljöer mellan pooler mått.|
|/hostingEnvironments/multiRolePools/providers/Microsoft.Insights/ metricDefinitions/läsning|Hämtar tillgängliga mått för App Service-miljö MultiRole|
|/hostingEnvironments/multiRolePools/Read|Hämta egenskaperna för en FrontEnd-Pool i en Apptjänst-miljö|
|/hostingenvironments/multirolepools/skus/Read|Hämta värd miljöer mellan pooler SKU: er.|
|/hostingenvironments/multirolepools/usages/Read|Hämta värd miljöer mellan pooler användningsområden.|
|/hostingEnvironments/multiRolePools/Write|Skapa en ny FrontEnd-Pool i en Apptjänst-miljö eller uppdatera en befintlig|
|/hostingenvironments/Operations/Read|Hämta värd Operations miljöer.|
|/hostingenvironments/outboundnetworkdependenciesendpoints/read|Hämta nätverksslutpunkter av alla utgående beroenden.|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/ hostingEnvironments/Läs|Hämta egenskaperna för en Apptjänstmiljö|
|/hostingEnvironments/reboot/Action|Starta om alla datorer i en Apptjänst-miljö|
|/hostingenvironments/Resume/Action|Återuppta värdbaserade miljöer.|
|/hostingenvironments/serverfarms/Read|Hämta värd miljöer App Service-planer.|
|/hostingenvironments/Sites/Read|Hämta värd miljöer Web Apps.|
|/hostingenvironments/suspend/Action|Pausa värdbaserade miljöer.|
|/hostingenvironments/usages/Read|Hämta värd miljöer användningsområden.|
|/hostingenvironments/workerpools/metricdefinitions/Read|Hämta värd miljöer Workerpools mått definitioner.|
|/hostingenvironments/workerpools/Metrics/Read|Hämta värd miljöer Workerpools mått.|
|/hostingEnvironments/workerPools/providers/Microsoft.Insights/metricDefinitions/Read|Hämtar tillgängliga mått för App Service-miljö WorkerPool|
|/hostingEnvironments/workerPools/Read|Hämta egenskaperna för en Arbetspool i en Apptjänst-miljö|
|/hostingenvironments/workerpools/skus/Read|Hämta värd miljöer Workerpools SKU: er.|
|/hostingenvironments/workerpools/usages/Read|Hämta värd miljöer Workerpools användningsområden.|
|/hostingEnvironments/workerPools/Write|Skapa en ny Arbetspool i en Apptjänst-miljö eller uppdatera en befintlig|
|/ hostingEnvironments/skrivning|Skapa en ny Apptjänstmiljö eller uppdatera en befintlig|
|/ishostingenvironmentnameavailable/Read|Hämta om värd-Miljönamn är tillgängligt.|
|/ishostnameavailable/Read|Kontrollera om värdnamnet är tillgänglig.|
|/isusernameavailable/Read|Kontrollera om användarnamnet är tillgängliga.|
|/listSitesAssignedToHostName/Read|Hämta namnen på de platser som tilldelats värdnamn.|
|/Locations/apioperations/Read|Hämta platser API-åtgärder.|
|/locations/connectiongatewayinstallations/read|Hämta platser anslutning Gateway installationer.|
|/locations/extractapidefinitionfromwsdl/action|Extrahera Api-Definition från WSDL för platser.|
|/Locations/listwsdlinterfaces/Action|Visa WSDL-gränssnitt för platser.|
|/Locations/managedapis/apioperations/Read|Hämta platser hanterade API: et.|
|/Locations/managedapis/JOIN/Action|Ansluter till en hanterad API.|
|/Locations/managedapis/Read|Hämta platser hanterade API: er.|
|/Operations/Read|Hämta åtgärder.|
|/publishingusers/Read|Hämta publicera användare.|
|/ publishingusers/skrivning|Uppdatera publicering av användare.|
|rekommendationer/Läs|Hämta lista över rekommendationer för prenumerationer.|
|registrera/åtgärd|Registerresursleverantören Microsoft.Web för prenumerationen.|
|/resourcehealthmetadata/Read|Hämta Resource Health Metadata.|
|/serverfarms/Capabilities/Read|Hämta App Service-planer funktioner.|
|/serverfarms/Delete|Ta bort en befintlig App Service-Plan|
|/serverfarms/firstpartyapps/settings/delete|Ta bort Apptjänst-planer första part appar inställningar.|
|/serverfarms/firstpartyapps/Settings/Read|Hämta Apptjänst första part appar inställningar för energischeman.|
|/serverfarms/firstpartyapps/settings/write|Uppdatera Apptjänstplaner första part appar inställningar.|
|/serverfarms/hybridconnectionnamespaces/relays/delete|Ta bort Apptjänst-planer Hybrid anslutning namnområden reläer.|
|/serverfarms/hybridconnectionnamespaces/relays/Read|Hämta App Service-planer Hybrid anslutning namnområden reläer.|
|/serverfarms/hybridconnectionnamespaces/relays/Sites/Read|Hämta App Service-planer Hybrid anslutning namnområden reläer Web Apps.|
|/serverfarms/hybridconnectionplanlimits/read|Hämta Plan för App Service-planer Hybrid anslutningsbegränsningar.|
|/serverfarms/hybridconnectionrelays/read|Hämta App Service-planer Hybrid anslutning reläer.|
|/serverfarms/metricdefinitions/read|Hämta Måttdefinitionerna för App Service-planer.|
|/serverfarms/Metrics/Read|Hämta mätvärden för App Service-planer.|
|/serverfarms/operationresults/Read|Hämta App Service-planer Åtgärdsresultat.|
|/serverfarms/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/serverfarms/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/serverfarms/providers/Microsoft.Insights/metricDefinitions/Read|Hämtar tillgängliga mått för App Service-Plan|
|/serverfarms/Read|Visa egenskaperna för en App Service-Plan|
|/serverfarms/restartSites/Action|Starta om alla webbprogram i en Apptjänstplan|
|/serverfarms/sites/read|Hämta App Service-planer Web Apps.|
|/serverfarms/skus/read|Hämta App Service-planer SKU: er.|
|/serverfarms/usages/read|Hämta App Service-planer användningsområden.|
|/serverfarms/virtualnetworkconnections/gateways/write|Uppdatera Apptjänst planer virtuella anslutningar Nätverksgatewayerna.|
|/serverfarms/virtualnetworkconnections/read|Hämta virtuella nätverksanslutningar för App Service-planer.|
|/serverfarms/virtualnetworkconnections/routes/delete|Ta bort Apptjänst-planer virtuella anslutningar nätverksvägar.|
|/serverfarms/virtualnetworkconnections/routes/read|Hämta App Service-planer virtuella anslutningar nätverksvägar.|
|/serverfarms/virtualnetworkconnections/routes/write|Uppdatera Apptjänst planer virtuellt nätverk anslutningar vägar.|
|/serverfarms/Workers/reboot/Action|Starta om Apptjänst-planer arbetare.|
|/ serverfarms/skrivning|Skapa en ny App Service-Plan eller uppdatera en befintlig|
|/Sites/analyzecustomhostname/Read|Analysera anpassade värdnamnet.|
|/sites/applySlotConfig/Action|Tillämpa web app platskonfigurationen från mål-plats till det aktuella webbprogrammet|
|/Sites/Backup/Action|Skapa en ny säkerhetskopia av web app|
|/ platser/backup/Läs|Hämta Web Apps säkerhetskopiering.|
|/Sites/Backup/Write|Uppdatera Web Apps säkerhetskopiering.|
|/Sites/Backups/delete|Ta bort Web Apps säkerhetskopior.|
|/Sites/Backups/List/Action|Lista över Web Apps säkerhetskopiering.|
|/Sites/Backups/Read|Hämta egenskaperna för ett webbprogram säkerhetskopiering|
|/Sites/Backups/Restore/Action|Återställa Web Apps säkerhetskopior.|
|/sites/config/delete|Ta bort Web Apps Config.|
|/Sites/config/List/Action|Visa en lista med Web App känsliga säkerhetsinställningar, till exempel publicera autentiseringsuppgifter, app-inställningar och anslutningssträngar|
|/sites/config/Read|Hämta konfigurationsinställningar för webbprogram|
|/Sites/config/Write|Uppdatera Web App konfigurationsinställningar|
|/sites/continuouswebjobs/delete|Ta bort Web Apps kontinuerlig Web jobb.|
|/Sites/continuouswebjobs/Read|Hämta Web Apps kontinuerlig Webjobs.|
|/Sites/continuouswebjobs/Start/Action|Starta Web Apps kontinuerlig Web jobb.|
|/sites/continuouswebjobs/stop/action|Stoppa Web Apps kontinuerlig Webjobs.|
|/ webbplatser/ta bort|Ta bort en befintlig Webbapp|
|/Sites/Deployments/delete|Ta bort Web Apps distributioner.|
|/Sites/Deployments/log/Read|Hämta Web Apps distributioner loggen.|
|/Sites/Deployments/Read|Hämta Web Apps distributioner.|
|/Sites/Deployments/Write|Uppdatera Web Apps distributioner.|
|/Sites/Diagnostics/analyses/Execute/Action|Kör Web Apps diagnostik analys.|
|/Sites/Diagnostics/analyses/Read|Hämta Web Apps diagnostik analys.|
|/Sites/Diagnostics/aspnetcore/Read|Hämta Web Apps diagnostik för ASP.NET Core app.|
|/Sites/Diagnostics/autoheal/Read|Hämta Web Apps diagnostik säkert att Autoheal.|
|/Sites/Diagnostics/Deployment/Read|Hämta Web Apps diagnostik-distributionen.|
|/Sites/Diagnostics/Deployments/Read|Hämta Web Apps diagnostik distributioner.|
|/Sites/Diagnostics/detectors/Execute/Action|Kör Web Apps diagnostik detektor.|
|/Sites/Diagnostics/detectors/Read|Hämta Web Apps diagnostik detektor.|
|/Sites/Diagnostics/failedrequestsperuri/Read|Hämta Web Apps diagnostik misslyckade förfrågningar Per Uri.|
|/Sites/Diagnostics/frebanalysis/Read|Hämta Web Apps diagnostik FREB analys.|
|/Sites/Diagnostics/loganalyzer/Read|Hämta Web Apps diagnostik loggen Analyzer.|
|/Sites/Diagnostics/Read|Hämta Web Apps diagnostik kategorier.|
|/Sites/Diagnostics/runtimeavailability/Read|Hämta Web Apps diagnostik Runtime tillgänglighet.|
|/Sites/Diagnostics/servicehealth/Read|Hämta Web Apps diagnostik tjänstens hälsa.|
|/Sites/Diagnostics/sitecpuanalysis/Read|Hämta Web Apps diagnostik plats CPU analys.|
|/Sites/Diagnostics/sitecrashes/Read|Hämta Web Apps diagnostik plats kraschar.|
|/Sites/Diagnostics/sitelatency/Read|Hämta svarstid för Web Apps diagnostik webbplats.|
|/Sites/Diagnostics/sitememoryanalysis/Read|Hämta Web Apps diagnostik plats minnesanalys.|
|/Sites/Diagnostics/siterestartsettingupdate/Read|Hämta Web Apps diagnostik plats omstart uppdateringen.|
|/Sites/Diagnostics/siterestartuserinitiated/Read|Hämta Web Apps diagnostik plats omstart användarinitierad.|
|/Sites/Diagnostics/siteswap/Read|Hämta Web Apps diagnostik plats växlingen.|
|/Sites/Diagnostics/ThreadCount/Read|Hämta antal trådar för Web Apps diagnostik.|
|/Sites/Diagnostics/workeravailability/Read|Hämta Web Apps diagnostik Workeravailability.|
|/Sites/Diagnostics/workerprocessrecycle/Read|Hämta Web Apps diagnostik Worker omarbetning av processen.|
|/Sites/domainownershipidentifiers/Read|Hämta Web Apps-domän ägarskap identifierare.|
|/sites/domainownershipidentifiers/write|Uppdatera Web Apps-domän ägarskap identifierare.|
|/Sites/Functions/Action|Funktioner Web Apps.|
|/Sites/Functions/delete|Ta bort Web Apps funktioner.|
|/Sites/Functions/listsecrets/Action|Lista hemligheter Web Apps funktioner.|
|/Sites/Functions/masterkey/Read|Hämta Web Apps funktioner Masterkey.|
|/Sites/Functions/Read|Hämta Web Apps funktioner.|
|/Sites/Functions/token/Read|Get Web Apps funktioner Token.|
|/Sites/Functions/Write|Uppdatera Web Apps funktioner.|
|/Sites/hostnamebindings/delete|Delete Web Apps Hostname Bindings.|
|/Sites/hostnamebindings/Read|Get Web Apps Hostname Bindings.|
|/Sites/hostnamebindings/Write|Uppdatera Värdnamnsbindningar för Web Apps.|
|/Sites/hybridconnection/delete|Ta bort Hybridanslutningen för Web Apps.|
|/Sites/hybridconnection/Read|Hämta Hybridanslutning för Web Apps.|
|/Sites/hybridconnection/Write|Uppdatera appar Hybrid-anslutning för webbprogram.|
|/Sites/hybridconnectionnamespaces/relays/delete|Ta bort Web Apps Hybrid anslutning namnområden reläer.|
|/Sites/hybridconnectionnamespaces/relays/listkeys/Action|Lista nycklar Web Apps Hybrid anslutning namnområden reläer.|
|/Sites/hybridconnectionnamespaces/relays/Read|Hämta Web Apps Hybrid anslutning namnområden reläer.|
|/Sites/hybridconnectionnamespaces/relays/Write|Uppdatera Web Apps Hybrid anslutning namnområden reläer.|
|/Sites/hybridconnectionrelays/Read|Hämta Web Apps Hybrid anslutning reläer.|
|/Sites/instances/Deployments/delete|Ta bort Web Apps instanser distributioner.|
|/Sites/instances/Deployments/Read|Hämta Web Apps instanser distributioner.|
|/Sites/instances/Extensions/log/Read|Hämta Web Apps instanser tillägg loggen.|
|/Sites/instances/Extensions/Read|Hämta Web Apps instanser tillägg.|
|/sites/instances/processes/delete|Ta bort Web Apps instanser processer.|
|/Sites/instances/processes/Read|Hämta Web Apps instanser processer.|
|/Sites/instances/Read|Hämta Web Apps-instanser.|
|/sites/listsyncfunctiontriggerstatus/action|Lista synkronisering funktionen utlösaren Status Web Apps.|
|/Sites/metricdefinitions/Read|Hämta definitioner för Web Apps mått.|
|/Sites/Metrics/Read|Hämta mätvärden från webben appar.|
|/Sites/metricsdefinitions/Read|Hämta definitioner för Web Apps mått.|
|/Sites/migratemysql/Action|Migrera MySql Web Apps.|
|/Sites/migratemysql/Read|Hämta Web Apps migrera MySql.|
|/Sites/networktrace/Action|Webbprogram för spårning i nätverket.|
|/Sites/NewPassword/Action|Nytt lösenord Web Apps.|
|/Sites/operationresults/Read|Hämta Åtgärdsresultat för Web Apps.|
|/Sites/Operations/Read|Hämta åtgärder för Web Apps.|
|/Sites/perfcounters/Read|Hämta prestandaräknare för Web Apps.|
|/sites/premieraddons/delete|Ta bort Web Apps Premier-tillägg.|
|/sites/premieraddons/read|Hämta Web Apps Premier-tillägg.|
|/sites/premieraddons/write|Uppdatera Web Apps Premier-tillägg.|
|/Sites/processes/Read|Hämta Web Apps processer.|
|/sites/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/sites/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/sites/providers/Microsoft.Insights/metricDefinitions/Read|Hämtar tillgängliga mått för webbprogram|
|/Sites/publiccertificates/delete|Ta bort Web Apps offentliga certifikat.|
|/Sites/publiccertificates/Read|Hämta Web Apps offentliga certifikat.|
|/Sites/publiccertificates/Write|Uppdatera Web Apps offentliga certifikat.|
|/Sites/publish/Action|Publicera ett webbprogram|
|/Sites/publishxml/Action|Hämta Publicera profil-xml för en Webbapp|
|/Sites/publishxml/Read|Hämta publicera Web Apps XML.|
|/ webbplatser/Läs|Hämta egenskaperna för en Webbapp|
|/Sites/recommendationhistory/Read|Hämta webbhistorik appar rekommendation.|
|/Sites/recommendations/disable/Action|Inaktivera Web Apps rekommendationer.|
|/Sites/recommendations/Read|Hämta lista över rekommendationer för webbprogrammet.|
|/Sites/Recover/Action|Recover Web Apps.|
|/Sites/resetSlotConfig/Action|Återställ webbappkonfigurationen|
|/Sites/resourcehealthmetadata/Read|Hämta Web Apps Resource Health Metadata.|
|/Sites/restart/Action|Starta om ett webbprogram|
|/Sites/Restore/Read|Hämta Web Apps återställning.|
|/Sites/Restore/Write|Återställa Web Apps.|
|/Sites/siteextensions/delete|Ta bort Webbplatstillägg för Web Apps.|
|/Sites/siteextensions/Read|Hämta Webbplatstillägg för Web Apps.|
|/Sites/siteextensions/Write|Uppdatera Webbplatstillägg för Web Apps.|
|/Sites/slots/analyzecustomhostname/Read|Hämta Web Apps fack analysera anpassade värdnamnet.|
|/sites/slots/applySlotConfig/Action|Tillämpa web app platskonfigurationen från målet plats till den aktuella platsen.|
|/Sites/slots/Backup/Action|Skapa ny Webbprogramplats säkerhetskopia.|
|/sites/slots/backup/read|Hämta Web Apps fack säkerhetskopiering.|
|/Sites/slots/Backup/Write|Uppdatera Web Apps fack säkerhetskopiering.|
|/sites/slots/backups/delete|Ta bort Web Apps fack säkerhetskopior.|
|/Sites/slots/Backups/List/Action|Lista Web Apps fack säkerhetskopiering.|
|/sites/slots/backups/Read|Hämta egenskaperna för en webbprogramplatser säkerhetskopiering|
|/Sites/slots/Backups/Restore/Action|Återställa Web Apps fack säkerhetskopior.|
|/sites/slots/config/delete|Ta bort Web Apps fack Config.|
|/sites/slots/config/list/Action|Visa en lista med Web App fack känsliga säkerhetsinställningar, t.ex publicering autentiseringsuppgifter, app-inställningar och anslutningssträngar|
|/sites/slots/config/Read|Hämta Webbprogramplatss konfigurationsinställningar|
|/Sites/slots/config/Write|Uppdatera Webbprogramplatss konfigurationsinställningar|
|/sites/slots/continuouswebjobs/delete|Ta bort Web Apps fack kontinuerlig Web jobb.|
|/sites/slots/continuouswebjobs/read|Hämta Web Apps fack kontinuerlig Webjobs.|
|/sites/slots/continuouswebjobs/start/action|Starta Web Apps fack kontinuerlig Web jobb.|
|/sites/slots/continuouswebjobs/stop/action|Stoppa Web Apps fack kontinuerlig Webjobs.|
|/sites/slots/Delete|Ta bort en befintlig Webbprogramplats|
|/Sites/slots/Deployments/delete|Ta bort Web Apps fack distributioner.|
|/Sites/slots/Deployments/log/Read|Hämta Web Apps fack distributioner loggen.|
|/Sites/slots/Deployments/Read|Hämta Web Apps fack distributioner.|
|/Sites/slots/Deployments/Write|Uppdatera Web Apps fack distributioner.|
|/Sites/slots/Diagnostics/analyses/Execute/Action|Kör Web Apps fack diagnostik analys.|
|/Sites/slots/Diagnostics/analyses/Read|Hämta Web Apps fack diagnostik analys.|
|/Sites/slots/Diagnostics/aspnetcore/Read|Hämta Web Apps fack diagnostik för ASP.NET Core app.|
|/sites/slots/diagnostics/autoheal/read|Hämta Web Apps fack diagnostik säkert att Autoheal.|
|/Sites/slots/Diagnostics/Deployment/Read|Hämta Web Apps fack diagnostik distributionen.|
|/Sites/slots/Diagnostics/Deployments/Read|Hämta Web Apps fack diagnostik distributioner.|
|/Sites/slots/Diagnostics/detectors/Execute/Action|Kör Web Apps fack diagnostik detektor.|
|/Sites/slots/Diagnostics/detectors/Read|Hämta Web Apps fack diagnostik detektor.|
|/Sites/slots/Diagnostics/frebanalysis/Read|Hämta Web Apps fack diagnostik FREB analys.|
|/Sites/slots/Diagnostics/loganalyzer/Read|Hämta Web Apps fack diagnostik loggen Analyzer.|
|/Sites/slots/Diagnostics/Read|Hämta Web Apps fack diagnostik.|
|/Sites/slots/Diagnostics/runtimeavailability/Read|Hämta Web Apps fack diagnostik Runtime tillgänglighet.|
|/Sites/slots/Diagnostics/servicehealth/Read|Hämta Web Apps fack diagnostik tjänstens hälsa.|
|/Sites/slots/Diagnostics/sitecpuanalysis/Read|Hämta Web Apps fack diagnostik plats CPU analys.|
|/Sites/slots/Diagnostics/sitecrashes/Read|Hämta Web Apps fack diagnostik plats kraschar.|
|/Sites/slots/Diagnostics/sitelatency/Read|Hämta svarstid för Web Apps fack diagnostik webbplats.|
|/Sites/slots/Diagnostics/sitememoryanalysis/Read|Hämta Web Apps fack diagnostik plats minnesanalys.|
|/sites/slots/diagnostics/siterestartsettingupdate/read|Hämta Web Apps fack diagnostik plats omstart uppdateringen.|
|/Sites/slots/Diagnostics/siterestartuserinitiated/Read|Hämta Web Apps fack diagnostik plats omstart användarinitierad.|
|/Sites/slots/Diagnostics/siteswap/Read|Hämta Web Apps fack diagnostik plats växlingen.|
|/Sites/slots/Diagnostics/ThreadCount/Read|Hämta Trådräkning för Web Apps fack diagnostik.|
|/Sites/slots/Diagnostics/workeravailability/Read|Hämta Web Apps fack diagnostik Workeravailability.|
|/Sites/slots/Diagnostics/workerprocessrecycle/Read|Hämta Web Apps fack diagnostik Worker processåtervinning.|
|/sites/slots/domainownershipidentifiers/read|Hämta Web Apps fack domän ägarskap identifierare.|
|/sites/slots/hostnamebindings/delete|Ta bort Värdnamnsbindningar för Web Apps platser.|
|/sites/slots/hostnamebindings/read|Hämta Värdnamnsbindningar för Web Apps platser.|
|/sites/slots/hostnamebindings/write|Uppdatera Värdnamnsbindningar för Web Apps platser.|
|/Sites/slots/hybridconnection/delete|Ta bort Hybridanslutningen för Web Apps platser.|
|/Sites/slots/hybridconnection/Read|Hämta Hybridanslutning för Web Apps platser.|
|/Sites/slots/hybridconnection/Write|Uppdatera Hybridanslutning för Web Apps platser.|
|/Sites/slots/hybridconnectionnamespaces/relays/delete|Ta bort Web Apps fack Hybrid anslutning namnområden reläer.|
|/sites/slots/hybridconnectionnamespaces/relays/write|Uppdatera Web Apps fack Hybrid anslutning namnområden reläer.|
|/Sites/slots/hybridconnectionrelays/Read|Hämta Web Apps fack Hybrid anslutning reläer.|
|/Sites/slots/instances/Deployments/Read|Hämta Web Apps fack instanser distributioner.|
|/sites/slots/instances/processes/delete|Ta bort Web Apps fack instanser processer.|
|/Sites/slots/instances/processes/Read|Hämta Web Apps fack instanser processer.|
|/Sites/slots/instances/Read|Hämta Web Apps fack instanser.|
|/Sites/slots/metricdefinitions/Read|Hämta Måttdefinitionerna för Web Apps platser.|
|/Sites/slots/Metrics/Read|Hämta Web Apps fack mått.|
|/sites/slots/migratemysql/read|Hämta Web Apps fack migrera MySql.|
|/Sites/slots/networktrace/Action|Nätverket Trace Web Apps platser.|
|/Sites/slots/NewPassword/Action|Nytt lösenord Web Apps platser.|
|/Sites/slots/operationresults/Read|Hämta Web Apps fack Åtgärdsresultat.|
|/sites/slots/operations/read|Hämta Web Apps fack åtgärder.|
|/sites/slots/perfcounters/read|Hämta Web Apps fack prestandaräknare.|
|/Sites/slots/phplogging/Read|Hämta Web Apps fack Phplogging.|
|/sites/slots/premieraddons/delete|Ta bort Web Apps fack Premier-tillägg.|
|/sites/slots/premieraddons/read|Hämta Web Apps fack Premier-tillägg.|
|/sites/slots/premieraddons/write|Uppdatera Web Apps fack Premier-tillägg.|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/sites/slots/providers/Microsoft.Insights/metricDefinitions/Read|Hämtar tillgängliga mått för Webbprogramplats|
|/Sites/slots/publiccertificates/Read|Hämta Web Apps fack offentliga certifikat.|
|/Sites/slots/publiccertificates/Write|Skapa eller uppdatera Web Apps fack offentliga certifikat.|
|/Sites/slots/publish/Action|Publicera en Webbprogramplats|
|/Sites/slots/publishxml/Action|Hämta Publicera profil-xml för Webbprogramplats|
|/sites/slots/Read|Hämta egenskaperna för en distributionsplats för webbprogram|
|/sites/slots/resetSlotConfig/Action|Återställ platskonfigurationen för web app|
|/Sites/slots/resourcehealthmetadata/Read|Hämta Web Apps fack resurs hälsa Metadata.|
|/Sites/slots/restart/Action|Starta om en Webbprogramplats|
|/Sites/slots/Restore/Read|Hämta Web Apps fack återställning.|
|/Sites/slots/Restore/Write|Återställa Web Apps platser.|
|/Sites/slots/siteextensions/delete|Ta bort Webbplatstillägg i Web Apps fack.|
|/Sites/slots/siteextensions/Read|Hämta Webbplatstillägg i Web Apps fack.|
|/Sites/slots/siteextensions/Write|Uppdatera Webbplatstillägg i Web Apps fack.|
|/Sites/slots/slotsdiffs/Action|Hämta konfigurationsavvikelser mellan webbprogrammet och platser|
|/sites/slots/slotsswap/Action|Växla distributionsplatser för webbprogram|
|/Sites/slots/Snapshots/Read|Hämta ögonblicksbilder för Web Apps platser.|
|/Sites/slots/sourcecontrols/delete|Ta bort konfigurationsinställningar för Web App fack källa kontroll|
|/Sites/slots/sourcecontrols/Read|Hämta Webbprogramplatss källkontrollen konfigurationsinställningar|
|/Sites/slots/sourcecontrols/Write|Uppdatera inställningar för Web App fack källkontrollen configuration|
|/Sites/slots/Start/Action|Starta en Webbprogramplats|
|/Sites/slots/stop/Action|Stoppa en Webbprogramplats|
|/Sites/slots/Sync/Action|Sync Web Apps Slots.|
|/sites/slots/triggeredwebjobs/delete|Ta bort Web Apps fack utlösta WebJobs.|
|/sites/slots/triggeredwebjobs/read|Hämta Web Apps fack utlösta WebJobs.|
|/sites/slots/triggeredwebjobs/run/action|Kör Web Apps fack utlösta WebJobs.|
|/Sites/slots/usages/Read|Hämta Web Apps fack användningsområden.|
|/sites/slots/virtualnetworkconnections/delete|Ta bort virtuella nätverksanslutningar för Web Apps-platser.|
|/sites/slots/virtualnetworkconnections/gateways/write|Uppdatera Web Apps fack virtuella anslutningar Nätverksgatewayerna.|
|/sites/slots/virtualnetworkconnections/read|Hämta virtuella nätverksanslutningar för Web Apps-platser.|
|/sites/slots/virtualnetworkconnections/write|Uppdatera virtuella nätverksanslutningar för Web Apps-platser.|
|/sites/slots/webjobs/read|Hämta Web Apps fack WebJobs.|
|/Sites/slots/Write|Skapa en ny Webbprogramplats eller uppdatera en befintlig|
|/Sites/slotsdiffs/Action|Hämta konfigurationsavvikelser mellan webbprogrammet och platser|
|/sites/slotsswap/Action|Växla distributionsplatser för webbprogram|
|/Sites/Snapshots/Read|Hämta ögonblicksbilder för Web Apps.|
|/Sites/sourcecontrols/delete|Ta bort konfigurationsinställningar för Web App käll-kontroll|
|/Sites/sourcecontrols/Read|Hämta Web App källkontrollen konfigurationsinställningar|
|/Sites/sourcecontrols/Write|Uppdatera inställningar för Web App källkontrollen konfiguration|
|/Sites/Start/Action|Starta ett webbprogram|
|/sites/stop/Action|Stoppa ett webbprogram|
|/Sites/Sync/Action|Sync Web Apps.|
|/sites/syncfunctiontriggers/action|Synkronisera funktionen utlösare för Web Apps.|
|/Sites/triggeredwebjobs/delete|Ta bort Web Apps utlösta WebJobs.|
|/Sites/triggeredwebjobs/History/Read|Hämta appar utlösta WebJobs webbhistorik.|
|/Sites/triggeredwebjobs/Read|Hämta Web Apps utlösta WebJobs.|
|/Sites/triggeredwebjobs/Run/Action|Kör Web Apps utlöses WebJobs.|
|/Sites/usages/Read|Hämta Web Apps användningsområden.|
|/sites/virtualnetworkconnections/delete|Ta bort virtuella nätverksanslutningar för Web Apps.|
|/sites/virtualnetworkconnections/gateways/read|Hämta Web Apps virtuella anslutningar Nätverksgatewayerna.|
|/sites/virtualnetworkconnections/gateways/write|Uppdatera Web Apps virtuella anslutningar Nätverksgatewayerna.|
|/sites/virtualnetworkconnections/read|Hämta Web Apps virtuella nätverksanslutningar.|
|/sites/virtualnetworkconnections/write|Uppdatera Web Apps virtuella nätverksanslutningar.|
|/Sites/webjobs/Read|Hämta Web Apps WebJobs.|
|/ webbplatser/skrivning|Skapa ett nytt webbprogram eller uppdatera en befintlig|
|/skus/Read|Hämta SKU: er.|
|/sourcecontrols/Read|Hämta kontroller för datakällor.|
|/ sourcecontrols/skrivning|Uppdatera källa kontroller.|
|/unregister/action|Avregistrera Microsoft.Web resource provider för prenumerationen.|
|verifiera/åtgärd|Validera.|
|/verifyhostingenvironmentvnet/action|Kontrollera värd för virtuella nätverk i miljön.|

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

| Åtgärd | Beskrivning |
|---|---|
|/Components/Read|Läsa operationsresurser|
|/healthInstances/Read|Läsa operationsresurser|
|/ Operations/läsning|Läsa operationsresurser|
|/workloads/delete|Tar bort en resurs för arbetsbelastning|
|/workloads/Read|Läser en arbetsbelastning resurs|
|/ arbetsbelastningar/skrivning|Skriver en arbetsbelastning resurs|

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapa en anpassad roll](role-based-access-control-custom-roles.md).
- Granska de [inbyggda RBAC-roller](role-based-access-built-in-roles.md).
- Lär dig att hantera åtkomst [av användare](role-based-access-control-manage-assignments.md) eller [per resurs](role-based-access-control-configure.md) 
- Lär dig hur du [visa aktivitetsloggar granska åtgärder på resurser](~/articles/azure-resource-manager/resource-group-audit.md)
