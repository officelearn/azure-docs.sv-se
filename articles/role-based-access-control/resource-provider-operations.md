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
ms.openlocfilehash: 80724a24fe7cf2565334a5212a0877589eb1eecf
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
# <a name="azure-resource-manager-resource-provider-operations"></a>Åtgärder i Azure Resource Manager-Resursprovidern

Det här dokumentet innehåller åtgärder som är tillgängliga för varje resursprovider för Microsoft Azure Resource Manager. Dessa kan användas i anpassade roller för att tillhandahålla detaljerade rollbaserad åtkomstkontroll (RBAC) behörigheter till resurser i Azure. Lägg märke till detta är inte en omfattande lista och åtgärder kan läggas till eller tas bort under varje provider är uppdaterad. Åtgärden strängar följa formatet för `Microsoft.<ProviderName>/<ChildResourceType>/<action>`. 

> [!NOTE]
> Använd för en omfattande och aktuella lista `Get-AzureRmProviderOperation` (i PowerShell) eller `az provider operation list` (i Azure CLI v2) till Liståtgärder över providers som Azure-resurs.

## <a name="microsoftaad"></a>Microsoft.AAD

| Åtgärd | Beskrivning |
|---|---|
|/domainServices/delete|Tar bort Domain Services.|
|/domainServices/read|Läser Domain Services.|
|/domainServices/write|Skriva Domain Services|
|/locations/operationresults/read|Läsa status för en asynkron åtgärd.|
|/Operations/read|Lokaliserad beskrivning för åtgärden, som det ska visas för användaren.|

## <a name="microsoftaadiam"></a>microsoft.aadiam

| Åtgärd | Beskrivning |
|---|---|
|/diagnosticsettings/delete|Om du tar bort en diagnostikinställningen|
|/diagnosticsettings/read|Läser en diagnostikinställningen|
|/diagnosticsettings/write|Skriva ett diagnostikinställningen|
|/diagnosticsettingscategories/read|Läser en diagnostikinställningen kategorier|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/tenants/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/tenants/providers/Microsoft.Insights/logDefinitions/read|Hämtar de tillgängliga loggarna för klienter|

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

| Åtgärd | Beskrivning |
|---|---|
|/configuration/action|Klientkonfiguration för uppdateringar.|
|/configuration/read|Läser konfigurationen av klienten.|
|/configuration/write|Skapar en klientkonfiguration.|
|/services/action|Uppdaterar en tjänstinstans i klienten.|
|/services/alerts/read|Läser aviseringar för en tjänst.|
|/services/alerts/read|Läser aviseringar för en tjänst.|
|/services/delete|Tar bort en instans av tjänsten på klienten.|
|/services/read|Läser tjänstinstanser i klienten.|
|/services/servicemembers/action|Skapar en instans av tjänsten medlem i tjänsten.|
|/services/servicemembers/alerts/read|Läser aviseringar för en medlem i tjänsten.|
|/services/servicemembers/delete|Tar bort en instans av tjänsten medlem i tjänsten.|
|/services/servicemembers/read|Läser tjänstinstans för medlem i tjänsten.|
|/services/write|Skapar en instans av tjänsten på klienten.|

## <a name="microsoftadvisor"></a>Microsoft.Advisor

| Åtgärd | Beskrivning |
|---|---|
|/configurations/read|Hämta konfigurationer|
|/configurations/write|Konfiguration av skapar/uppdateringar|
|/generateRecommendations/action|Skapar rekommendationer|
|/generateRecommendations/read|Hämtar generera rekommendationer status|
|/operations/read|Hämtar åtgärder för Microsoft Advisor|
|/recommendations/read|Läser rekommendationer|
|/recommendations/suppressions/delete|Tar bort Undertryckning|
|/recommendations/suppressions/read|Hämtar suppressions|
|/recommendations/suppressions/write|Skapat/uppdaterat suppressions|
|/register/action|Registrerar prenumerationen för Microsoft Advisor|
|/suppressions/delete|Tar bort Undertryckning|
|/suppressions/read|Hämtar suppressions|
|/suppressions/write|Skapat/uppdaterat suppressions|
|/unregister/action|Avregistrerar prenumeration för Microsoft Advisor|

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

| Åtgärd | Beskrivning |
|---|---|
|/alerts/read|Hämta alla aviseringar för inkommande trafik.|
|/alerts/resolve/action|Ändra tillstånd för aviseringen till 'Åtgärda'|
|/alertsSummary/read|Få en översikt över aviseringar|
|/Operations/read|Läser de åtgärder som anges|

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

| Åtgärd | Beskrivning |
|---|---|
|/locations/checkNameAvailability/action|Kontrollerar att namnet angivna Analysis Server är giltigt och inte i användning.|
|/locations/operationresults/read|Hämtar information om resultatet för den angivna åtgärden.|
|/locations/operationstatuses/read|Hämtar information om statusen för den angivna åtgärden.|
|/operations/read|Hämtar information av åtgärder|
|/register/action|Registrerar Analysis Services-resursprovidern.|
|/servers/delete|Tar bort analysserver.|
|/servers/listGatewayStatus/action|Visa status för gatewayen som associeras med servern.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar diagnostikinställningen för Analysis Server|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningen för Analysis Server|
|/servers/providers/Microsoft.Insights/logDefinitions/read|Hämtar de tillgängliga loggarna för servrar|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för Analysis Server|
|/Servers/Read|Hämtar information för den angivna Analysis Server.|
|/servers/resume/action|Återupptar analysserver.|
|/Servers/skus/Read|Hämta information om tillgängliga SKU för servern|
|/Servers/suspend/Action|Pausar analysserver.|
|/servers/write|Skapar eller uppdaterar den angivna Analysis-servern.|
|/skus/read|Hämtar information av SKU: er|

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

| Åtgärd | Beskrivning |
|---|---|
|/checkNameAvailability/read|Kontrollerar om tjänstens namn är tillgängligt|
|/operations/read|Läsa alla API åtgärder som är tillgängliga för Microsoft.ApiManagement resurs|
|/register/action|Registrera prenumerationen för Microsoft.ApiManagement resursprovidern|
|/reports/read|Hämta rapporter sammanställs efter tidsperioder, geografisk region, utvecklare, produkter, API: er, åtgärder, prenumeration och byRequest.|
|/service/apis/delete|Ta bort befintliga API|
|/service/apis/diagnostics/delete|Ta bort befintliga diagnostik|
|/service/apis/diagnostics/loggers/delete|Ta bort mappning av en logg med en diagnostikinställningen|
|/service/apis/diagnostics/loggers/read|Hämta lista över befintliga diagnostiska loggar tangenttryckningar|
|/service/apis/diagnostics/loggers/write|Mappa loggaren till en diagnostikinställningen|
|/service/apis/diagnostics/read|Hämta lista över diagnostik eller hämta information för diagnostik|
|/service/apis/diagnostics/write|Lägger till nya diagnostiska eller uppdaterar befintliga diagnosinformation|
|/service/apis/operations/delete|Ta bort befintliga API-åtgärd|
|/service/apis/operations/policies/delete|Ta bort principkonfigurationen från principer för API-åtgärd|
|/service/apis/operations/policies/read|Hämta principer för API-åtgärd eller Get princip konfigurationsinformation för API-åtgärd|
|/service/apis/operations/policies/write|Ange information om principen för API-åtgärd|
|/service/apis/operations/policy/delete|Ta bort principkonfigurationen från åtgärden|
|/service/apis/operations/policy/read|Hämta information om principen för åtgärden|
|/service/apis/operations/policy/write|Ange information om konfiguration av principen för åtgärden|
|/service/apis/operations/read|Hämta listan över befintliga API: et eller hämta information om API-åtgärd|
|/service/apis/operations/tags/delete|Ta bort associationen mellan befintlig tagg med befintliga åtgärden|
|/service/apis/operations/tags/read|Hämta taggarna associerade med åtgärden eller hämta taggen information|
|/service/apis/operations/tags/write|Associera befintlig tagg med befintliga åtgärden|
|/service/apis/operations/write|Skapa nya API-åtgärden eller uppdatera befintliga API-åtgärd|
|/service/apis/operationsByTags/read|Hämta lista över kopplingar åtgärden-tagg|
|/service/apis/policies/delete|Ta bort principkonfigurationen från API-principer|
|/service/apis/policies/read|Hämta principer för API: et eller Get princip konfigurationsinformation för API|
|/service/apis/policies/write|Ange information om principen för API|
|/service/apis/policy/delete|Ta bort principkonfigurationen från API: et|
|/service/apis/policy/read|Hämta information om principen för API|
|/service/apis/policy/write|Ange information om principen för API|
|/service/apis/products/read|Hämta alla produkter som API: et är en del av|
|/service/apis/read|Hämta lista över alla registrerade API: er eller hämta information om API|
|/service/apis/releases/delete|Tar bort alla versioner av API eller ta bort API-versionen|
|/service/apis/releases/read|Hämta versioner för en API eller hämta information om API reelase|
|/service/apis/releases/write|Skapa ny API-version eller uppdatera befintliga API-versionen|
|/service/apis/revisions/delete|Tar bort alla revisioner av API|
|/service/apis/revisions/read|Hämta revisioner som hör till en API|
|/service/apis/schemas/delete|Tar bort befintliga schemat|
|/service/apis/schemas/document/read|Hämta det dokument som beskriver schemat|
|/service/apis/schemas/document/write|Uppdatera dokumentet som beskriver schemat|
|/service/apis/schemas/read|Hämtar de scheman som används av API: et eller hämtar alla scheman för en given API|
|/service/apis/schemas/write|Anger de scheman som används av API: et|
|/service/apis/tagDescriptions/delete|Ta bort taggen beskrivning från API: et|
|/service/apis/tagDescriptions/read|Hämta taggar beskrivningar i omfånget för API: et eller hämta taggen beskrivning i omfånget för API|
|/service/apis/tagDescriptions/write|Skapa/ändra taggen beskrivning i omfattningen av API|
|/service/apis/tags/delete|Ta bort den befintliga API-tagg kopplingen|
|/service/apis/tags/read|Hämta alla API-tagg associationen för API: et eller hämta information om API-tagg association|
|/service/apis/tags/write|Lägga till en ny koppling för API-tagg|
|/service/apis/write|Skapa nya API eller uppdatera befintliga API-information|
|/service/apisByTags/read|Hämta lista över API-tagg kopplingar|
|/service/api-version-sets/delete|Ta bort befintliga VersionSet|
|/service/api-version-sets/read|Hämta lista över version grupp entiteter eller hämtar information om en VersionSet|
|/service/api-version-sets/versions/read|Hämta lista över version entiteter|
|/service/api-version-sets/write|Skapa nya VersionSet eller uppdatera den befintliga VersionSet information|
|/service/applynetworkconfigurationupdates/action|Uppdaterar de Microsoft.ApiManagement resurser som körs i virtuella nätverk för att hämta uppdaterade nätverksinställningar.|
|/service/authorizationServers/delete|Ta bort den befintliga auktorisering server|
|/service/authorizationServers/read|Hämta listan över servrar för auktorisering eller hämta information om auktorisering server|
|/service/authorizationServers/write|Skapa en ny auktoriserings-server eller uppdatera information om en befintlig server auktorisering|
|/service/backends/delete|Ta bort befintlig serverdel|
|/service/backends/read|Hämta listan över serverdelar eller hämta information om backend|
|/service/backends/reconnect/action|Skapa en begäran om återanslutningen|
|/service/backends/write|Lägg till en ny serverdel eller uppdatera befintliga backend-information|
|/Service/Backup/Action|Säkerhetskopiering API-tjänsten till den angivna behållaren i en användare som storage-konto|
|/service/certificates/delete|Ta bort befintligt certifikat|
|/service/certificates/read|Hämta listan över certifikat eller hämta information om certifikat|
|/service/certificates/write|Lägg till nytt certifikat|
|/service/delete|Ta bort en instans för API Management-tjänsten|
|/service/diagnostics/delete|Ta bort befintliga diagnostik|
|/Service/Diagnostics/loggers/delete|Ta bort mappning av en logg med en diagnostikinställningen|
|/Service/Diagnostics/loggers/Read|Hämta lista över befintliga diagnostiska loggar tangenttryckningar|
|/Service/Diagnostics/loggers/Write|Mappa loggaren till en diagnostikinställningen|
|/service/diagnostics/read|Hämta lista över diagnostik eller hämta information för diagnostik|
|/service/diagnostics/write|Lägger till nya diagnostiska eller uppdaterar befintliga diagnosinformation|
|/service/getssotoken/action|Hämtar SSO-token som kan användas för att logga in i API Management-tjänsten äldre portal som administratör|
|/service/groups/delete|Ta bort en befintlig grupp|
|/service/groups/read|Hämta listan över grupper eller hämtar information om en grupp|
|/service/groups/users/delete|Ta bort befintliga användare från en befintlig grupp|
|/service/groups/users/read|Hämta lista över gruppanvändare|
|/service/groups/users/write|Lägg till befintliga användare i en befintlig grupp|
|/service/groups/write|Skapa ny grupp eller uppdatera befintliga gruppinformation|
|/service/identityProviders/delete|Ta bort den befintliga identitetsleverantören.|
|/service/identityProviders/read|Hämta lista över identitetsleverantörer eller hämta information av identitetsleverantören.|
|/service/identityProviders/write|Skapa en ny identitetsleverantör eller uppdatera information om en befintlig identitetsleverantör|
|/service/locations/networkstatus/read|Hämtar nätverksstatus för åtkomst av resurser som tjänsten är beroende på plats.|
|/Service/loggers/delete|Ta bort befintliga transaktionsloggfiler|
|/Service/loggers/Read|Hämta listan över loggar tangenttryckningar eller hämta information om loggning|
|/Service/loggers/Write|Lägg till ny loggare eller uppdatera information om befintliga transaktionsloggfiler|
|/service/managedeployments/action|Ändra SKU/enheter, Lägg till/ta bort regionala distributioner av API Management-tjänsten|
|/service/networkstatus/read|Hämtar nätverksstatus för åtkomst av resurser som tjänsten är beroende av.|
|/service/notifications/action|Skickar meddelandet till en angiven användare|
|/service/notifications/read|Hämtar alla API Management publisher meddelanden eller hämta API Management publisher detaljer|
|/service/notifications/recipientEmails/delete|Tar bort befintlig e-postadress som är associerad med ett meddelande|
|/service/notifications/recipientEmails/read|Hämta e-postmottagare som är associerade med API Management Publisher meddelande|
|/service/notifications/recipientEmails/write|Skapa ny e-postmottagare för meddelandet|
|/service/notifications/recipientUsers/delete|Tar bort användare som är kopplad till den meddelandemottagare|
|/service/notifications/recipientUsers/read|Hämta mottagaren användare som associeras med meddelandet|
|/service/notifications/recipientUsers/write|Lägg till användare i meddelandemottagare|
|/service/notifications/write|Skapa eller API uppdateringshantering publisher meddelande|
|/service/openidConnectProviders/delete|Ta bort befintliga OpenID Connect-providern|
|/service/openidConnectProviders/read|Hämta lista över OpenID Connect providers eller hämta information för OpenID Connect Provider|
|/service/openidConnectProviders/write|Skapa en ny information för OpenID Connect Provider eller uppdatering av en befintlig OpenID Connect-Provider|
|/service/operationresults/read|Hämtar aktuell status för långvarig åtgärd|
|/service/policies/delete|Ta bort principkonfigurationen från innehavaren principer|
|/service/policies/read|Hämta principer för klient- eller Get princip konfigurationsinformation för klient|
|/service/policies/write|Ange information om principen för klienten|
|/service/policySnippets/read|Hämta alla princip kodavsnitt|
|/service/portalsettings/read|Hämta logga in inställningar för portalen eller Get logga In inställningarna för portalen eller hämta delegeringsinställningar för portalen|
|/service/portalsettings/write|Uppdatera registrera dig inställningar eller uppdatering registrera dig inställningar eller uppdatering-logga In inställningar eller uppdatering-logga In inställningar eller delegeringsinställningar för uppdatering eller uppdatering delegeringsinställningar|
|/service/products/apis/delete|Ta bort befintliga API från befintliga produkten|
|/service/products/apis/read|Hämta lista över API: er som lagts till i befintliga produkten|
|/service/products/apis/write|Lägga till befintliga API för befintliga produkt|
|/service/products/delete|Ta bort befintliga produkten|
|/service/products/groups/delete|Ta bort associationen av befintlig developer grupp med befintliga produkten|
|/service/products/groups/read|Hämta lista över developer grupper som är associerade med produkt|
|/service/products/groups/write|Associera befintlig developer grupp med befintliga produkt|
|/service/products/policies/delete|Ta bort principkonfigurationen från produkt-principer|
|/service/products/policies/read|Hämta principer för produkt eller Get princip konfigurationsinformation för produkten|
|/service/products/policies/write|Ange information om principen för produkten|
|/Service/products/policy/delete|Ta bort principkonfigurationen från befintliga produkten|
|/service/products/policy/read|Hämta konfiguration av befintlig produkt|
|/service/products/policy/write|Konfigurera principen för befintliga produkten|
|/service/products/read|Hämta lista över produkter eller hämta information om produkt|
|/service/products/subscriptions/read|Hämta listan över prenumerationer för produkten|
|/service/products/tags/delete|Ta bort associationen mellan befintlig tagg med befintlig produkt|
|/service/products/tags/read|Hämta taggarna associerade med produkt eller hämta taggen information|
|/service/products/tags/write|Associera befintlig tagg med befintlig produkt|
|/Service/products/Write|Skapa en ny produkt eller uppdatera befintliga produktinformation|
|/service/properties/delete|Tar bort befintlig egenskap|
|/service/properties/read|Hämtar information om den angivna egenskapen eller hämtar en lista över alla egenskaper|
|/service/properties/write|Skapar en ny egenskap eller uppdaterar värdet för den angivna egenskapen|
|/service/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar diagnostikinställningen för API Management-tjänsten|
|/service/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningen för API Management-tjänsten|
|/service/providers/Microsoft.Insights/logDefinitions/read|Hämtar de tillgängliga loggarna för API Management-tjänsten|
|/service/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för API Management-tjänsten|
|/service/quotas/periods/read|Hämta räknarvärdet kvoten för perioden|
|/service/quotas/periods/write|Ange kvoten aktuella räknarvärdet|
|/service/quotas/read|Hämta värden för kvot|
|/service/quotas/write|Ange kvoten aktuella räknarvärdet|
|/service/read|Läsa in metadata för en instans för API Management-tjänsten|
|/service/reports/read|Hämta rapport sammanställs efter tidsperioder eller Get-rapport som sammanställs efter geografisk region eller Get-rapport som aggregeras av utvecklare. eller hämta rapport sammanställs efter produkter. eller hämta rapport som har aggregerats med API: er eller Get rapporten sammanställs efter operations eller Get-rapport som aggregeras av prenumerationen. eller hämta begäranden rapportdata|
|/Service/Restore/Action|Återställa API Management-tjänsten från den angivna behållaren i en användare har angett storage-konto|
|/service/subscriptions/delete|Ta bort prenumerationen. Den här åtgärden kan användas för att ta bort prenumerationen|
|/service/subscriptions/read|Hämta en lista över produktprenumeration eller hämta information om produkten prenumeration|
|/service/subscriptions/regeneratePrimaryKey/action|Återskapa primärnyckeln för prenumeration|
|/service/subscriptions/regenerateSecondaryKey/action|Återskapa en sekundär nyckel för prenumeration|
|/service/subscriptions/write|Prenumerera på en befintlig användare till en befintlig produkt eller uppdatera befintliga prenumerationsinformation. Den här åtgärden kan användas för att förnya prenumerationen|
|/service/tagResources/read|Hämta lista över taggar med associerade resurser|
|/service/tags/delete|Ta bort befintlig tagg|
|/service/tags/read|Hämta lista över taggar eller hämta information om tagg|
|/service/tags/write|Lägg till ny tagg eller uppdatera information om befintliga tagg|
|/service/templates/delete|Återställ standardmall för API Management-e-post|
|/service/templates/read|Hämtar alla postmallar för e-eller hämtar API Management e-mallinformation|
|/service/templates/write|Skapa eller uppdatera API-hantering e-post eller uppdaterar API Management e-mallen|
|/Service/tenant/delete|Ta bort konfiguration för klienten|
|/service/tenant/deploy/action|Kör en åtgärd för distribution för att tillämpa ändringarna från den angivna git-grenen i konfigurationen i databasen.|
|/service/tenant/operationResults/read|Hämta listan över Åtgärdsresultat eller hämta resultatet av en specifik åtgärd|
|/service/tenant/read|Hämta konfiguration för den eller Get-klient access informationsdetaljer|
|/service/tenant/regeneratePrimaryKey/action|Återskapa primärnyckeln|
|/service/tenant/regenerateSecondaryKey/action|Återskapa den sekundära åtkomstnyckeln|
|/Service/tenant/Save/Action|Skapar commit med ögonblicksbild av konfigurationen för den angivna grenen i databasen|
|/service/tenant/syncState/read|Hämta status för senaste git-synkronisering|
|/service/tenant/validate/action|Verifierar ändringar från den angivna git-grenen|
|/service/tenant/write|Konfigurera principen för klient eller information uppdateringsdetaljer klient åtkomst|
|/service/updatecertificate/action|Ladda upp SSL-certifikat för en API Management-tjänsten|
|/service/updatehostname/action|Konfigurera, uppdatera eller ta bort anpassade domännamn för en API Management-tjänsten|
|/service/users/action|Registrera en ny användare|
|/service/users/applications/attachments/delete|Tar bort en bilaga|
|/service/users/applications/attachments/read|Hämtar programmet bifogade filer eller hämtar bifogade filer|
|/service/users/applications/attachments/write|Lägga till bilaga till programmet|
|/service/users/applications/delete|Tar bort befintliga program|
|/service/users/applications/read|Hämta lista över alla program eller hämtar API Management programinformation|
|/service/users/applications/write|Registrerar ett program till API-hantering eller uppdateringar programinformation|
|/service/users/delete|Ta bort användarkonto|
|/service/users/generateSsoUrl/action|Generera en URL för enkel inloggning. URL: en kan användas för åtkomst till administrationsportal|
|/service/users/groups/read|Hämta lista över användargrupper|
|/service/users/keys/read|Hämta lista över användarnycklar|
|/service/users/read|Hämta en lista över registrerade användare eller hämta kontoinformation för en användare|
|/service/users/subscriptions/read|Hämta listan över användare prenumerationer|
|/service/users/token/action|Hämta token åtkomsttoken för en användare|
|/service/users/write|Registrera en ny användare eller uppdatera kontoinformation för en befintlig användare|
|/service/write|Skapa en ny instans av API Management-tjänsten|
|/unregister/action|Avregistrera prenumerationen för Microsoft.ApiManagement resursprovidern|

## <a name="microsoftauthorization"></a>Microsoft.Authorization

| Åtgärd | Beskrivning |
|---|---|
|/checkAccess/action|Kontrollerar att anroparen har behörighet att utföra en viss åtgärd|
|/classicAdministrators/delete|Tar bort administratören från prenumerationen.|
|/classicAdministrators/read|Läser in prenumerationens administratörer.|
|/classicAdministrators/write|Lägg till eller ta bort en administratör för en prenumeration.|
|/elevateAccess/action|Ger anroparen åtkomst till administratör för användaråtkomst i klientomfattningen|
|/locks/delete|Ta bort lås i det specificerade omfånget.|
|/locks/read|Hämtar lås i det specificerade omfånget.|
|/locks/write|Lägg till lås i det specificerade omfånget.|
|/permissions/Read|Listar alla behörigheter som anroparen har i ett givet omfång.|
|/policyAssignments/delete|Ta bort tilldelning av principer i det definierade området.|
|/policyAssignments/read|Hämta information om en tilldelning av principer.|
|/policyAssignments/write|Skapa en principtilldelning i det definierade området.|
|/policyDefinitions/delete|Ta bort en definition av principen.|
|/policyDefinitions/read|Hämta information om en principdefinition.|
|/policyDefinitions/write|Skapa en anpassad principdefinition.|
|/policySetDefinitions/delete|Ta bort en principuppsättningsdefinition.|
|/policySetDefinitions/read|Hämta information om en principuppsättningsdefinition.|
|/policySetDefinitions/write|Skapa en anpassad principuppsättningsdefinition.|
|/providerOperations/read|Hämta åtgärder för alla providrar som kan användas i rolldefinitioner.|
|/roleAssignments/delete|Ta bort en rolltilldelning i det specificerade omfånget.|
|/roleAssignments/read|Hämta information om en rolltilldelning.|
|/roleAssignments/write|Skapa en rolltilldelning i det specificerade omfånget.|
|/roleDefinitions/delete|Ta bort den angivna anpassade rolldefinitionen.|
|/roleDefinitions/read|Hämta information om en rolldefinition.|
|/roleDefinitions/write|Skapa eller uppdatera en anpassad rolldefinition med angivna behörigheter och tilldelningsbara scope.|

## <a name="microsoftautomation"></a>Microsoft.Automation

| Åtgärd | Beskrivning |
|---|---|
|/automationAccounts/agentRegistrationInformation/read|Läs en Azure Automation DSC-registreringsinformation|
|/automationAccounts/agentRegistrationInformation/regenerateKey/action|Skriver en begäran om att återskapa nycklar för Azure Automation DSC|
|/automationAccounts/certificates/delete|Tar bort en Azure Automation-certifikattillgång|
|/automationAccounts/certificates/read|Hämtar en Azure Automation-certifikattillgång|
|/automationAccounts/certificates/write|Skapar eller uppdaterar en Azure Automation-certifikattillgång|
|/automationAccounts/compilationjobs/read|Läser en Azure Automation DSC-kompilering|
|/automationAccounts/compilationjobs/write|Skriver en Azure Automation DSC-kompilering|
|/automationAccounts/configurations/delete|Tar bort en Azure Automation DSC-innehåll|
|/automationAccounts/configurations/getCount/action|Läser antal Azure Automation DSC-innehåll|
|/automationAccounts/configurations/read|Hämtar en Azure Automation DSC-innehåll|
|/automationAccounts/configurations/write|Skriver en Azure Automation DSC-innehåll|
|/automationAccounts/connections/delete|Tar bort en Azure Automation-anslutningstillgång|
|/automationAccounts/connections/read|Hämtar en Azure Automation-anslutningstillgång|
|/automationAccounts/connections/write|Skapar eller uppdaterar en Azure Automation-anslutningstillgång|
|/automationAccounts/connectionTypes/delete|Tar bort en Azure Automation-anslutningstyptillgång|
|/automationAccounts/connectionTypes/read|Hämtar en Azure Automation-anslutningstyptillgång|
|/automationAccounts/connectionTypes/write|Skapar en Azure Automation-anslutningstyptillgång|
|/automationAccounts/credentials/delete|Tar bort en Azure Automation-autentiseringsuppgiftstillgång|
|/automationAccounts/credentials/read|Hämtar en Azure Automation-autentiseringsuppgiftstillgång|
|/automationAccounts/credentials/write|Skapar eller uppdaterar en Azure Automation-autentiseringsuppgiftstillgång|
|/automationAccounts/delete|Tar bort en Azure Automation-konto|
|/automationAccounts/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/automationAccounts/diagnosticSettings/write|Anger diagnostikinställningen för resursen|
|/automationAccounts/hybridRunbookWorkerGroups/delete|Tar bort Hybrid Runbook Worker-resurser|
|/automationAccounts/hybridRunbookWorkerGroups/read|Läser Hybrid Runbook Worker-resurser|
|/automationAccounts/jobs/output/action|Hämtar utdata för ett jobb|
|/automationAccounts/jobs/output/action|Hämtar utdata för ett jobb|
|/automationAccounts/jobs/read|Hämtar en Azure Automation-jobb|
|/automationAccounts/jobs/read|Hämtar en Azure Automation-jobb|
|/automationAccounts/jobs/resume/action|Återupptar ett Azure Automation-jobb|
|/automationAccounts/jobs/resume/action|Återupptar ett Azure Automation-jobb|
|/automationAccounts/jobs/runbookContent/action|Hämtar innehållet i Azure Automation-runbook vid tidpunkten för jobbkörningen|
|/automationAccounts/jobs/runbookContent/action|Hämtar innehållet i Azure Automation-runbook vid tidpunkten för jobbkörningen|
|/automationAccounts/jobs/stop/action|Stoppar ett Azure Automation-jobb|
|/automationAccounts/jobs/stop/action|Stoppar ett Azure Automation-jobb|
|/automationAccounts/jobs/streams/read|Hämtar en Azure Automation-jobbström|
|/automationAccounts/jobs/streams/read|Hämtar en Azure Automation-jobbström|
|/automationAccounts/jobs/suspend/action|Pausar ett Azure Automation-jobb|
|/automationAccounts/jobs/suspend/action|Pausar ett Azure Automation-jobb|
|/automationAccounts/jobs/write|Skapar ett Azure Automation-jobb|
|/automationAccounts/jobs/write|Skapar ett Azure Automation-jobb|
|/automationAccounts/jobSchedules/delete|Tar bort ett Azure Automation-Jobbschema|
|/automationAccounts/jobSchedules/read|Hämtar ett Azure Automation-Jobbschema|
|/automationAccounts/jobSchedules/write|Skapar ett Azure Automation-Jobbschema|
|/automationAccounts/linkedWorkspace/read|Hämtar arbetsytan kopplad till automation-kontot|
|/automationAccounts/logDefinitions/read|Hämtar de tillgängliga loggarna för automation-kontot|
|/automationAccounts/modules/activities/read|Hämtar Azure Automation-aktiviteter|
|/automationAccounts/modules/delete|Tar bort en Azure Automation-modul|
|/automationAccounts/modules/read|Hämtar en Azure Automation-modul|
|/automationAccounts/modules/write|Skapar eller uppdaterar en Azure Automation-modul|
|/automationAccounts/nodeConfigurations/delete|Tar bort en Azure Automation DSC-nodkonfiguration|
|/automationAccounts/nodeConfigurations/read|Läser en Azure Automation DSC-nodkonfiguration|
|/automationAccounts/nodeConfigurations/readContent/action|Läser en Azure Automation DSC-nod configuration innehåll|
|/automationAccounts/nodeConfigurations/write|Skriver en Azure Automation DSC-nodkonfiguration|
|/automationAccounts/nodes/delete|Tar bort Azure Automation DSC-noder|
|/automationAccounts/nodes/read|Läser en Azure Automation DSC-noder|
|/automationAccounts/nodes/reports/read|Läser en Azure Automation DSC rapporten contentss|
|/automationAccounts/nodes/reports/read|Läser en Azure Automation DSC-rapporter|
|/automationAccounts/objectDataTypes/fields/read|Hämtar Azure Automation-TypeFields|
|/automationAccounts/providers/Microsoft.Insights/metricDefinitions/read|Hämtar Måttdefinitionerna för Automation|
|/automationAccounts/read|Hämtar ett Azure Automation-konto|
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
|/automationAccounts/runbooks/read|Hämtar en Azure Automation-runbook|
|/automationAccounts/runbooks/readContent/action|Hämtar innehållet i en Azure Automation-runbook|
|/automationAccounts/runbooks/write|Skapar eller uppdaterar en Azure Automation-runbook|
|/automationAccounts/schedules/delete|Tar bort en Azure Automation-schematillgång|
|/automationAccounts/schedules/read|Hämtar en Azure Automation-schematillgång|
|/automationAccounts/schedules/write|Skapar eller uppdaterar en Azure Automation-schematillgång|
|/automationAccounts/statistics/read|Hämtar Azure Automation-statistik|
|/automationAccounts/usages/read|Hämtar Azure Automation-användning|
|/automationAccounts/variables/delete|Tar bort en Azure Automation-variabeltillgång|
|/automationAccounts/variables/read|Läser en Azure Automation-variabeltillgång|
|/automationAccounts/variables/write|Skapar eller uppdaterar en Azure Automation-variabeltillgång|
|/automationAccounts/watchers/streams/read|Hämtar en Azure Automation watcher jobbström|
|/automationAccounts/webhooks/delete|Tar bort en Azure Automation-webhook |
|/automationAccounts/webhooks/generateUri/action|Genererar en URI för en Azure Automation-webhook|
|/automationAccounts/webhooks/read|Läser en Azure Automation-webhook|
|/automationAccounts/webhooks/write|Skapar eller uppdaterar en Azure Automation-webhook|
|/automationAccounts/write|Skapar eller uppdaterar en Azure Automation-konto|
|/automationAccounts/write|Skapar eller uppdaterar en Azure Automation-konto|

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

| Åtgärd | Beskrivning |
|---|---|
|/b2cDirectories/delete|Ta bort B2C-katalogresurs|
|/b2cDirectories/read|Visa B2C-katalogresurs|
|/b2cDirectories/write|Skapa eller uppdatera en B2C-katalogresurs|
|/operations/read|Läs alla API-åtgärder tillgängliga för resursprovidern Microsoft.AzureActiveDirectory|
|/register/action|Registrera prenumeration för resursprovidern Microsoft.AzureActiveDirectory|

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

| Åtgärd | Beskrivning |
|---|---|
|/Operations/read|Hämtar egenskaperna för en resurs provider-åtgärd|
|/register/action|Registrerar prenumeration med Microsoft.AzureStack resursprovidern|
|/registrations/customerSubscriptions/delete|Tar bort en Azure-stacken kund prenumeration|
|/registrations/customerSubscriptions/read|Hämtar egenskaperna för en Azure-stacken kund prenumeration|
|/registrations/customerSubscriptions/write|Skapar eller uppdaterar en Azure-stacken kund prenumeration|
|/registrations/delete|Tar bort en Azure-Stack-registrering|
|/registrations/getActivationKey/action|Hämtar senaste Azure Stack aktiveringsnyckeln|
|/registrations/products/listDetails/action|Hämtar utökad information för en Azure-stacken Marketplace-produkten|
|/registrations/products/read|Hämtar egenskaperna för en Azure-stacken Marketplace-produkt|
|/registrations/read|Hämtar egenskaperna för en Azure-Stack-registrering|
|/registrations/write|Skapar eller uppdaterar en Azure-Stack-registrering|

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
|/batchAccounts/certificates/read|Visar en lista över certifikat på en Batch-kontot eller hämtar egenskaperna för ett certifikat|
|/batchAccounts/certificates/write|Skapar ett nytt certifikat på en Batch-kontot eller uppdaterar ett befintligt certifikat|
|/batchAccounts/delete|Tar bort ett Batch-konto|
|/batchAccounts/listkeys/action|Visar åtkomstnycklar för Batch-kontot|
|/batchAccounts/operationResults/read|Hämtar resultatet av en långvarig åtgärd för Batch-konto|
|/batchAccounts/poolOperationResults/read|Hämtar resultatet av en tidskrävande pool-åtgärd i ett Batch-konto|
|/batchAccounts/pools/delete|Tar bort poolen från ett Batch-konto|
|/batchAccounts/pools/disableAutoscale/action|Inaktiverar automatisk skalning för en pool med Batch-konto|
|/batchAccounts/pools/read|Visar en lista över programpooler på en Batch-kontot eller hämtar egenskaperna för en pool|
|/batchAccounts/pools/stopResize/action|Stoppar ett pågående att ändra storlek på på poolen Batch-konto|
|/batchAccounts/pools/upgradeOs/action|Uppgraderar operativsystemet på poolen Batch-konto|
|/batchAccounts/pools/write|Skapar en ny pool på ett Batch-konto eller uppdaterar en befintlig adresspool|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/batchAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/batchAccounts/providers/Microsoft.Insights/logDefinitions/read|Hämtar de tillgängliga loggarna för Batch-tjänsten|
|/batchAccounts/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för Batch-tjänsten|
|/batchAccounts/read|Visar en lista över Batch-konton eller hämtar egenskaperna för ett Batch-konto|
|/batchAccounts/regeneratekeys/action|Återskapar åtkomstnycklar för Batch-kontot|
|/batchAccounts/syncAutoStorageKeys/action|Synkroniserar åtkomstnycklar för lagringskontot automatiskt konfigurerade för Batch-kontot|
|/batchAccounts/write|Skapar ett nytt batchkonto eller uppdaterar ett befintligt batchkonto|
|/locations/checkNameAvailability/action|Kontrollerar att kontonamnet är giltigt och inte används.|
|/locations/quotas/read|Hämtar Batch-kvoter för den angivna prenumerationen i den angivna Azure-regionen|
|/register/action|Registrerar prenumerationen för Batch-Resursprovidern och kan skapa Batch-konton|
|/unregister/action|Avregistrerar prenumerationen för Batch-Resursprovidern som hindrar skapandet av Batch-konton|

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

| Åtgärd | Beskrivning |
|---|---|
|/clusters/delete|Tar bort ett Batch AI-kluster|
|/clusters/read|Visar en lista över Batch AI-kluster eller hämtar egenskaperna för en Batch AI-kluster|
|/clusters/remoteLoginInformation/action|Visar information om remote inloggningen för ett Batch AI-kluster|
|/clusters/write|Skapar ett nytt Batch AI-kluster eller uppdaterar ett befintligt Batch AI-kluster|
|/fileservers/delete|Tar bort en Batch AI-filserver|
|/fileservers/read|Visar en lista över Batch AI fileservers eller hämtar egenskaperna för en Batch AI-filserver|
|/fileservers/resume/action|Återupptar en Batch AI-filserver|
|/fileservers/suspend/Action|Pausar en Batch AI-filserver|
|/fileservers/write|Skapar en ny Batch AI-filserver eller uppdaterar en befintlig Batch AI-filserver|
|/jobs/delete|Tar bort ett Batch AI-jobb|
|/jobs/read|Visar Batch AI jobb eller hämtar egenskaperna för en Batch AI-jobb|
|/jobs/remoteLoginInformation/action|Visar information om remote inloggningen för ett Batch AI-jobb|
|/jobs/terminate/action|Avslutar en Batch AI-jobb|
|/jobs/write|Skapar ett nytt Batch AI-jobb eller uppdaterar ett befintligt Batch AI-jobb|
|/register/action|Registrerar prenumerationen för Resursprovidern Batch AI och kan skapa Batch AI-resurser|

## <a name="microsoftbilling"></a>Microsoft.Billing

| Åtgärd | Beskrivning |
|---|---|
|/billingPeriods/read|Visar en lista över tillgängliga fakturering punkter|
|/invoices/read|Visar tillgängliga fakturor|

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

| Åtgärd | Beskrivning |
|---|---|
|/mapApis/Delete|Borttagningsåtgärd|
|/mapApis/listSecrets/action|Lista hemligheterna|
|/mapApis/listSingleSignOnToken/action|Läs auktoriseringstoken för enkel inloggning för resursen|
|/mapApis/Read|Läsåtgärd|
|/mapApis/regenerateKey/action|Återskapa nyckeln|
|/mapApis/Write|Skrivåtgärd|
|/Operations/read|Beskrivning av åtgärden.|

## <a name="microsoftcache"></a>Microsoft.Cache

| Åtgärd | Beskrivning |
|---|---|
|/checknameavailability/action|Kontrollerar om ett namn kan användas med nytt Redis Cache|
|/operations/read|Visar en lista över de åtgärder som 'Microsoft.Cache-leverantör har stöd för.|
|/redis/delete|Ta bort hela Redis-cache|
|/redis/export/action|Exportera Redis-data till prefixade lagringsblobbar i angivet format|
|/redis/firewallRules/delete|Ta bort IP-brandväggsregler för Redis Cache|
|/redis/firewallRules/read|Hämta IP-brandväggsregler för Redis Cache|
|/redis/firewallRules/write|Redigera IP-brandväggsregler för Redis Cache|
|/redis/forceReboot/action|Tvinga omstart av en cache-instans, vilket kan medföra dataförlust.|
|/redis/import/action|Importera data av ett angivet format från flera blobbar till Redis|
|/redis/linkedservers/delete|Ta bort länkad server från Redis Cache|
|/redis/linkedservers/read|Hämta länkade servrar som är kopplade till Redis Cache.|
|/redis/linkedservers/write|Lägg till länkad server till Redis Cache|
|/redis/listKeys/action|Visa värdet för åtkomstnycklarna för Redis-cache i hanteringsportalen|
|/redis/listUpgradeNotifications/read|Lista de senaste uppgraderingsmeddelandena för cacheklienten.|
|/redis/locations/operationresults/read|Hämtar resultatet av lång åtgärd som körs som ”plats”-rubrik tidigare returneras till klienten|
|/redis/metricDefinitions/read|Hämtar tillgängliga mått för ett Redis-cache|
|/redis/patchSchedules/delete|Ta bort uppdateringsschemat för Redis Cache|
|/redis/patchSchedules/read|Hämtar uppdateringsschemat för Redis Cache|
|/redis/patchSchedules/write|Ändra uppdateringsschemat för Redis Cache|
|/redis/read|Visa inställningarna och konfigurationen för Redis-cache i hanteringsportalen|
|/redis/regenerateKey/action|Ändra värdet för åtkomstnycklarna för Redis-cache i hanteringsportalen|
|/redis/start/action|Starta en cacheinstans.|
|/redis/stop/action|Stoppa en cacheinstans.|
|/redis/write|Ändra inställningarna och konfigurationen för Redis-cache i hanteringsportalen|
|/register/action|Registrerar Microsoft.Cache-resursprovidern med en prenumeration|
|/unregister/action|Avregistrerar Microsoft.Cache-resursprovidern med en prenumeration|

## <a name="microsoftcapacity"></a>Microsoft.Capacity

| Åtgärd | Beskrivning |
|---|---|
|/register/action|Registrerar resursprovidern kapacitet och kan skapa kapacitet resurser.|
|/reservationorders/action|Uppdatera alla Reservation|
|/reservationorders/delete|Ta bort alla Reservation|
|/reservationorders/read|Läsa alla reservationer|
|/reservationorders/reservations/action|Uppdatera alla Reservation|
|/reservationorders/reservations/delete|Ta bort alla Reservation|
|/reservationorders/reservations/read|Läsa alla reservationer|
|/reservationorders/reservations/revisions/read|Läsa alla reservationer|
|/reservationorders/reservations/write|Skapa en Reservation|
|/reservationorders/write|Skapa en Reservation|

## <a name="microsoftcdn"></a>Microsoft.Cdn

| Åtgärd | Beskrivning |
|---|---|
|/CheckNameAvailability/action||
|/CheckResourceUsage/action||
|/edgenodes/delete||
|/edgenodes/Read||
|/edgenodes/write||
|/operationresults/delete||
|/operationresults/profileresults/CheckResourceUsage/action||
|/operationresults/profileresults/delete||
|/operationresults/profileresults/endpointresults/CheckResourceUsage/action||
|/operationresults/profileresults/endpointresults/customdomainresults/delete||
|/ operationresults/profileresults/endpointresults/customdomainresults/DisableCustomHttps/åtgärd||
|/ operationresults/profileresults/endpointresults/customdomainresults/EnableCustomHttps/åtgärd||
|/operationresults/profileresults/endpointresults/customdomainresults/read||
|/operationresults/profileresults/endpointresults/customdomainresults/write||
|/operationresults/profileresults/endpointresults/delete||
|/operationresults/profileresults/endpointresults/Load/action||
|/operationresults/profileresults/endpointresults/originresults/delete||
|/operationresults/profileresults/endpointresults/originresults/read||
|/operationresults/profileresults/endpointresults/originresults/write||
|/operationresults/profileresults/endpointresults/PURGE/Action||
|/operationresults/profileresults/endpointresults/read||
|/operationresults/profileresults/endpointresults/Start/Action||
|/operationresults/profileresults/endpointresults/Stop/action||
|/operationresults/profileresults/endpointresults/ValidateCustomDomain/action||
|/operationresults/profileresults/endpointresults/write||
|/operationresults/profileresults/GenerateSsoUri/action||
|/operationresults/profileresults/GetSupportedOptimizationTypes/action||
|/operationresults/profileresults/read||
|/operationresults/profileresults/write||
|/operationresults/read||
|/operationresults/write||
|/operations/read||
|/profiles/CheckResourceUsage/action||
|/profiles/delete||
|/profiles/endpoints/CheckResourceUsage/action||
|/profiles/endpoints/customdomains/delete||
|/profiles/endpoints/customdomains/DisableCustomHttps/action||
|/profiles/endpoints/customdomains/EnableCustomHttps/action||
|/profiles/endpoints/customdomains/read||
|/profiles/endpoints/customdomains/write||
|/Profiles/endpoints/delete||
|/Profiles/endpoints/Load/Action||
|/Profiles/endpoints/Origins/delete||
|/profiles/endpoints/origins/read||
|/profiles/endpoints/origins/write||
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar diagnostikinställningar för resursen|
|/profiles/endpoints/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningar för resursen|
|/profiles/endpoints/providers/Microsoft.Insights/logDefinitions/read|Hämtar de tillgängliga loggarna för Microsoft.Cdn|
|/Profiles/endpoints/PURGE/Action||
|/profiles/endpoints/read||
|/Profiles/endpoints/Start/Action||
|/Profiles/endpoints/stop/Action||
|/profiles/endpoints/ValidateCustomDomain/action||
|/Profiles/endpoints/Write||
|/profiles/GenerateSsoUri/action||
|/profiles/GetSupportedOptimizationTypes/action||
|/profiles/read||
|/profiles/write||
|/register/action|Registrerar prenumerationen för CDN-resursprovidern och aktiverar funktionen för att skapa CDN-profiler.|
|/ValidateProbe/action||

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

| Åtgärd | Beskrivning |
|---|---|
|/certificateOrders/certificates/Delete|Ta bort ett befintligt certifikat|
|/certificateOrders/certificates/Read|Hämta listan över certifikat|
|/certificateOrders/certificates/Write|Lägg till ett nytt certifikat eller uppdatera en befintlig|
|/certificateOrders/Delete|Ta bort en befintlig AppServiceCertificate|
|/certificateOrders/operations/Read|Visa en lista med alla åtgärder från certifikatregistreringsplatsen för app service|
|/certificateOrders/Read|Hämta en lista över CertificateOrders|
|/certificateOrders/reissue/Action|Ange en befintlig certificateorder|
|/certificateOrders/renew/Action|Förnya ett befintligt certificateorder|
|/certificateOrders/resendEmail/Action|Skicka e-post med certifikat|
|/certificateOrders/resendRequestEmails/Action|Skicka om begäran e-post till en annan e-postadress|
|/certificateOrders/resendRequestEmails/Action|Hämta platsen förseglingen för ett certifikat i App Service|
|/certificateOrders/retrieveCertificateActions/Action|Hämta listan över certifikat-åtgärder|
|/certificateOrders/retrieveEmailHistory/Action|Hämta certifikat e-historik|
|/certificateOrders/verifyDomainOwnership/Action|Verifiera domänägarskap|
|/certificateOrders/Write|Lägg till en ny certificateOrder eller uppdatera en befintlig|
|/provisionGlobalAppServicePrincipalInUserTenant/Action|Etablera tjänstens huvudnamn för tjänstens huvudnamn för app|
|/register/action|Microsoft Certificates registerresursleverantören för prenumerationen|
|/validateCertificateRegistrationInformation/Action|Verifiera certifikatet köp objekt utan att skicka den|

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

| Åtgärd | Beskrivning |
|---|---|
|/capabilities/read|Visar funktionerna|
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
|/domainNames/write|Lägg till eller ändra resursernas domännamn.|
|/moveSubscriptionResources/action|Flytta alla klassiska resurser till en annan prenumeration.|
|/operatingSystemFamilies/read|Listar de gästoperativsystem som är tillgängliga i Microsoft Azure, och listar även de operativsystemsversioner som är tillgängliga för respektive familj.|
|/operatingSystems/read|Listar de versioner av gästoperativsystemen som för tillfället är tillgängliga i Microsoft Azure.|
|/quotas/read|Hämta prenumerationens kvot.|
|/register/action|Registrera för klassisk beräkning|
|/resourceTypes/skus/read|Hämtar SKU-listan för de resurstyper som stöds.|
|/validateSubscriptionMoveAvailability/action|Verifiera prenumerationens tillgänglighet för klassisk flyttåtgärd.|
|/virtualMachines/associatedNetworkSecurityGroups/delete|Tar bort nätverkssäkerhetsgruppen som är kopplad till den virtuella datorn.|
|/virtualMachines/associatedNetworkSecurityGroups/operationStatuses/read|Läser åtgärdsstatus för nätverkssäkerhetsgrupp kopplad till virtuella datorer.|
|/virtualMachines/associatedNetworkSecurityGroups/read|Hämtar nätverkssäkerhetsgruppen som är kopplad till den virtuella datorn.|
|/virtualMachines/associatedNetworkSecurityGroups/write|Lägger till en nätverkssäkerhetsgrupp som är kopplad till den virtuella datorn.|
|/virtualMachines/asyncOperations/read|Hämta möjliga asynkrona åtgärder|
|/virtualMachines/attachDisk/action|Bifogar en datadisk till den virtuella datorn.|
|/virtualMachines/delete|Tar bort virtuella datorer.|
|/virtualMachines/detachDisk/action|Tar bort en datadisk från den virtuella datorn.|
|/virtualMachines/disks/read|Hämtar en lista över datadiskar|
|/virtualMachines/downloadRemoteDesktopConnectionFile/action|Hämtar RDP-filen för den virtuella datorn.|
|/virtualMachines/extensions/operationStatuses/read|Läser åtgärdsstatus för tillägg för virtuella datorer.|
|/virtualMachines/extensions/read|Hämtar tillägget för virtuell dator.|
|/virtualMachines/extensions/write|Placerar tillägget för virtuell dator.|
|/virtualMachines/metrics/read|Hämtar måtten.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/delete|Tar bort nätverkssäkerhetsgruppen som är kopplad till nätverksgränssnittet.|
|/ associatedNetworkSecurityGroups-virtualMachines/networkInterfaces/operationStatuses/läsning|Läser åtgärdsstatus för nätverkssäkerhetsgrupp kopplad till virtuella datorer.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/read|Hämtar nätverkssäkerhetsgruppen som är kopplad till nätverksgränssnittet.|
|/virtualMachines/networkInterfaces/associatedNetworkSecurityGroups/write|Lägger till en nätverkssäkerhetsgrupp som är kopplad till nätverksgränssnittet.|
|/virtualMachines/operationStatuses/read|Läser åtgärdsstatus för virtuella datorer.|
|/virtualMachines/performMaintenance/action|Utför underhåll av den virtuella datorn.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/read|Hämta diagnostikinställningarna.|
|/virtualMachines/providers/Microsoft.Insights/diagnosticSettings/write|Lägg till eller ändra diagnostikinställningarna.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|Hämtar måttdefinitioner.|
|/virtualMachines/read|Hämtar en lista över virtuella datorer.|
|/virtualMachines/redeploy/action|Distribuerar om den virtuella datorn.|
|/virtualMachines/restart/action|Startar om virtuella datorer.|
|/virtualMachines/shutdown/action|Stänga av den virtuella datorn.|
|/virtualMachines/start/action|Startar den virtuella datorn.|
|/virtualMachines/stop/action|Stoppar den virtuella datorn.|
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
|/quotas/read|Hämta prenumerationens kvot.|
|/register/action|Registrera för klassiskt nätverk|
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
|/virtualNetworks/gateways/downloadDeviceConfigurationScript/action|Hämtar konfigurationsskriptet för enhet.|
|/virtualNetworks/gateways/downloadDiagnostics/action|Hämtar gateway-diagnostiken.|
|/virtualNetworks/gateways/listCircuitServiceKey/action|Hämtar kretstjänstnyckeln.|
|/virtualNetworks/gateways/listPackage/action|Listar gateway-paketet för virtuella nätverk.|
|/virtualNetworks/gateways/operationStatuses/read|Läser åtgärdsstatus för virtuella nätverksgatewayar.|
|/virtualNetworks/gateways/packages/read|Hämtar gateway-paketet för virtuella nätverk.|
|/virtualNetworks/gateways/read|Hämtar de virtuella nätverks-gatewayerna.|
|/virtualNetworks/gateways/startDiagnostics/action|Startar diagnostiken för den virtuella nätverks-gatewayen.|
|/virtualNetworks/gateways/stopDiagnostics/action|Stoppar diagnostiken för den virtuella nätverks-gatewayen.|
|/virtualNetworks/gateways/write|Lägger till en virtuell nätverks-gateway.|
|/virtualNetworks/join/action|Anslut till det virtuella nätverket.|
|/virtualNetworks/operationStatuses/read|Läser åtgärdsstatus för virtuella nätverk.|
|/virtualNetworks/peer/action|Peerar ett virtuellt nätverk med ett annat virtuellt nätverk.|
|/virtualNetworks/read|Hämtar det virtuella nätverket.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/delete|Tar bort nätverkssäkerhetsgruppen som är kopplad till undernätet.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/operationStatuses/read|Läser åtgärdsstatus för nätverkssäkerhetsgrupp kopplad till virtuella nätverksundernät.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/read|Hämtar nätverkssäkerhetsgruppen som är kopplad till undernätet.|
|/virtualNetworks/subnets/associatedNetworkSecurityGroups/write|Lägger till en nätverkssäkerhetsgrupp som är kopplad till undernätet.|
|/virtualNetworks/write|Lägg till ett nytt virtuellt nätverk.|

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

| Åtgärd | Beskrivning |
|---|---|
|/capabilities/read|Visar funktionerna|
|/checkStorageAccountAvailability/action|Kontrollerar tillgängligheten för ett lagringskonto.|
|/disks/read|Returnerar lagringskontodisken.|
|/images/read|Returnerar bilden.|
|/osImages/read|Returnerar operativsystemsavbildningen.|
|/publicImages/read|Hämtar den offentliga avbildningen av den virtuella datorn.|
|/quotas/read|Hämta prenumerationens kvot.|
|/register/action|Registrera för klassisk lagring|
|/storageAccounts/delete|Ta bort lagringskontot.|
|/storageAccounts/disks/delete|Tar bort angiven lagringskontodisk.|
|/storageAccounts/disks/operationStatuses/read|Läser resursens åtgärdsstatus.|
|/storageAccounts/disks/read|Returnerar lagringskontodisken.|
|/storageAccounts/disks/write|Lägger till en lagringskontodisk.|
|/storageAccounts/images/delete|Tar bort en given lagringskontoavbildning.|
|/storageAccounts/images/read|Returnerar lagringskontoavbildningen.|
|/storageAccounts/listKeys/action|Listar lagringskontots åtkomstnycklar.|
|/storageAccounts/operationStatuses/read|Läser resursens åtgärdsstatus.|
|/storageAccounts/osImages/delete|Tar bort en given operativsystemsavbildning för lagringskonto.|
|/storageAccounts/osImages/read|Returnerar operativsystemsavbildningen för lagringskonto.|
|/storageAccounts/read|Returnerar lagringskontot med det givna kontot.|
|/storageAccounts/regenerateKey/action|Återskapar befintliga åtkomstnycklar för lagringskontot.|
|/storageAccounts/services/diagnosticSettings/read|Hämta diagnostikinställningarna.|
|/storageAccounts/services/diagnosticSettings/write|Lägg till eller ändra diagnostikinställningarna.|
|/storageAccounts/services/metricDefinitions/read|Hämtar måttdefinitioner.|
|/storageAccounts/services/metrics/read|Hämtar måtten.|
|/storageAccounts/services/read|Hämta de tjänster som är tillgängliga.|
|/storageAccounts/write|Lägger till ett nytt lagringskonto.|

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

| Åtgärd | Beskrivning |
|---|---|
|/accounts/delete|Tar bort API-konton|
|/accounts/listKeys/action|Visa nyckellista|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Hämta diagnostikinställningen för resursen.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningen för resursen.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för Cognitive Services.|
|/accounts/read|Läser API-konton.|
|/accounts/regenerateKey/action|Återskapar nyckel|
|/accounts/skus/read|Läser tillgängliga SKU:er för en befintlig resurs.|
|/accounts/usages/read|Hämta kvotanvändning för en befintlig resurs.|
|/accounts/write|Skriver API-konton.|
|/locations/checkSkuAvailability/action|Läser tillgängliga SKU:er för en prenumeration.|
|/Operations/read|Visa en lista över alla tillgängliga åtgärder|
|/register/action|Registrerar prenumeration för kognitiva tjänster|
|/skus/read|Läser tillgängliga SKU: er för kognitiva tjänster.|

## <a name="microsoftcommerce"></a>Microsoft.Commerce

| Åtgärd | Beskrivning |
|---|---|
|/RateCard/read|Returnerar erbjuder data, resurs-mätare metadata och priser för den angivna prenumerationen.|
|/UsageAggregates/read|Hämtar Microsoft Azure-förbrukningen av en prenumeration. Resultatet innehåller mängder användningsdata, prenumeration och resurs relaterad information, på ett angivet tidsintervall.|

## <a name="microsoftcompute"></a>Microsoft.Compute

| Åtgärd | Beskrivning |
|---|---|
|/availabilitySets/delete|Tar bort tillgänglighetsuppsättningen|
|/availabilitySets/read|Hämta egenskaperna för en tillgänglighetsuppsättning|
|/availabilitySets/vmSizes/read|Visar en lista över tillgängliga storlekar för att skapa eller uppdatera en virtuell dator i tillgänglighetsuppsättningen|
|/availabilitySets/write|Skapar en ny tillgänglighetsuppsättning eller uppdaterar en befintlig|
|/disks/beginGetAccess/action|Hämta diskens SAS-URI för blobåtkomst|
|/disks/delete|Tar bort disken|
|/disks/endGetAccess/action|Återkalla diskens SAS-URI|
|/disks/read|Hämta egenskaperna för en disk|
|/disks/write|Skapar en ny disk eller uppdaterar en befintlig|
|/images/delete|Avbildningen tas bort|
|/images/read|Hämta egenskaperna för avbildningen|
|/images/write|Skapar en ny avbildning eller uppdaterar en befintlig|
|/locations/capsOperations/read|Hämtar status för en asynkron åtgärd Caps|
|/locations/diskOperations/read|Hämtar status för en asynkron diskåtgärd|
|/locations/operations/read|Hämtar status för en asynkron åtgärd|
|/locations/publishers/artifacttypes/offers/read|Hämta egenskaperna för en plattform avbildningen erbjuder|
|/locations/publishers/artifacttypes/offers/skus/read|Hämta egenskaperna för en plattform avbildningen Sku|
|/Locations/Publishers/artifacttypes/OFFERS/skus/versions/Read|Hämta egenskaperna för en plattform Avbildningsversion|
|/locations/publishers/artifacttypes/types/read|Hämta egenskaperna för en VMExtension-typ.|
|/locations/publishers/artifacttypes/types/versions/read|Hämta egenskaperna för en VMExtension Version|
|/locations/publishers/read|Hämta egenskaperna för en utgivare|
|/locations/runCommands/read|Listar tillgängliga körningskommandon på platsen|
|/locations/usages/read|Hämtar gränser för antalet tjänster och aktuell användning för prenumerationens beräkningsresurser på en plats|
|/locations/vmSizes/read|Visar en lista över tillgängliga storlekar för virtuella datorer på en plats|
|/operations/read|Visar en lista över åtgärder som är tillgängliga på Microsoft.Compute-resursprovidern|
|/register/action|Registrerar prenumeration med Microsoft.Compute-resursprovidern|
|/restorePointCollections/delete|Tar bort samlingen med återställningspunkter och återställningspunkter den innehåller|
|/restorePointCollections/read|Hämta egenskaperna för en samling med återställningspunkter|
|/restorePointCollections/restorePoints/delete|Tar bort återställningspunkten|
|/restorePointCollections/restorePoints/read|Hämta egenskaperna för en återställningspunkt|
|/restorePointCollections/restorePoints/retrieveSasUris/action|Hämta egenskaperna för en återställningspunkt tillsammans med blob-SAS-URI:er|
|/restorePointCollections/restorePoints/write|Skapar en ny återställningspunkt|
|/restorePointCollections/write|Skapar en ny samling med återställningspunkter eller uppdaterar en befintlig|
|/sharedVMImages/delete|Tar bort SharedVMImage|
|/sharedVMImages/read|Hämta egenskaperna för en SharedVMImage|
|/sharedVMImages/versions/delete|Ta bort en SharedVMImageVersion|
|/sharedVMImages/versions/read|Hämta egenskaperna för en SharedVMImageVersion|
|/sharedVMImages/versions/replicate/action|Replikera en SharedVMImageVersion ska målregioner|
|/sharedVMImages/versions/write|Skapa en ny SharedVMImageVersion eller uppdatera en befintlig|
|/sharedVMImages/write|Skapar en ny SharedVMImage eller uppdaterar en befintlig|
|/skus/read|Hämtar listan över Microsoft.Compute SKU: er som tillgänglig för din prenumeration|
|/snapshots/beginGetAccess/action|Hämta ögonblicksbilden SAS-URI för blobåtkomst|
|/snapshots/delete|Ta bort en ögonblicksbild|
|/snapshots/endGetAccess/action|Återkalla ögonblicksbilden SAS-URI|
|/snapshots/read|Hämta egenskaperna för en ögonblicksbild|
|/snapshots/write|Skapa en ny ögonblicksbild eller uppdatera en befintlig|
|/virtualMachines/capture/action|Avbildar den virtuella datorn genom att kopiera hårddiskar, och genererar en mall som kan användas för att skapa liknande virtuella datorer|
|/virtualMachines/convertToManagedDisks/action|Konverterar den virtuella datorns blobbaserade diskar till hanterade diskar|
|/virtualMachines/deallocate/action|Stänger av den virtuella datorn och frigör beräkningsresurser|
|/virtualMachines/delete|Tar bort den virtuella datorn|
|/virtualMachines/extensions/delete|Tar bort tillägget för virtuell dator|
|/virtualMachines/extensions/read|Hämta egenskaperna för ett tillägg för virtuell dator|
|/virtualMachines/extensions/write|Skapar ett nytt tillägg för virtuell dator eller uppdaterar ett befintligt|
|/virtualMachines/generalize/action|Ställer in statusen för den virtuella datorn på Generaliserad och förbereder den virtuella datorn för avbildning|
|/virtualMachines/instanceView/read|Hämtar detaljerad körningsstatus för den virtuella datorn och dess resurser|
|/virtualMachines/performMaintenance/action|Utför underhållsåtgärd på den virtuella datorn.|
|/virtualMachines/powerOff/action|Stänger av den virtuella datorn. Observera att den virtuella datorn kommer att fortsätta debiteras.|
|/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|Läser måttdefinitioner för den virtuella datorn|
|/virtualMachines/read|Hämta egenskaperna för en virtuell dator|
|/virtualMachines/redeploy/action|Distribuerar om virtuell dator|
|/virtualMachines/reimage/action|Avbildningen av den virtuella dator som använder differentieringsdisk återställs.|
|/virtualMachines/restart/action|Startar om den virtuella datorn|
|/virtualMachines/runCommand/action|Kör ett fördefinierat skript på den virtuella datorn|
|/virtualMachines/start/action|Startar den virtuella datorn|
|/virtualMachines/vmSizes/read|Visar en lista över tillgängliga storlekar som de virtuella datorerna kan uppdateras till|
|/virtualMachines/write|Skapar en ny virtuell dator eller uppdaterar en befintlig|
|/virtualMachineScaleSets/deallocate/action|Stänger av och startar om beräkningsresurserna för VM-skalningsuppsättningens instanser |
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
|/virtualMachineScaleSets/redeploy/action|Distribuera instanser av Skalningsuppsättning i virtuell dator|
|/virtualMachineScaleSets/reimage/action|Skapar ny avbildning av VM-skalningsuppsättningens instanser|
|/virtualMachineScaleSets/restart/action|Startar om instanserna för VM-skalningsuppsättningen|
|/virtualMachineScaleSets/rollingUpgrades/cancel/action|Avbryter samlad uppgradering av en VM-skalningsuppsättning|
|/virtualMachineScaleSets/rollingUpgrades/read|Hämta senaste status för den samlade uppgraderingen för VM-skalningsuppsättningen|
|/virtualMachineScaleSets/scale/action|Kontrollera om en befintlig VM-skalningsuppsättning kan skala in/ut till det angivna antalet instanser|
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
|/ virtualMachineScaleSets/virtualMachines/providers/Microsoft.Insights/metricDefinitions/read|Läser måttdefinitioner för den virtuella datorn i skalningsuppsättning|
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
|/budgets/read|Lista över budgetar genom en prenumeration eller en hanteringsgrupp.|
|/ budgetar/skrivning|Skapa, uppdatera och ta bort budgetar genom en prenumeration eller en hanteringsgrupp.|
|/marketplaces/read|Visa användningsinformation för ett omfång för prenumerationer EA och WebDirect marketplace resurs.|
|/operations/read|Lista över de åtgärder som alla stöds av Microsoft.Consumption resursprovidern.|
|/pricesheets/read|Visa en lista över Pricesheets-data för en prenumeration eller en hanteringsgrupp.|
|/reservationDetails/read|Visa information om diskanvändning för reserverade instanser av reservation ordning eller hantering av grupper. Information om data är per instans per dag nivå.|
|/reservationSummaries/read|Lista över belastningen sammanfattning för reserverade instanser av reservation ordning eller hantering av grupper. Sammanfattningsdata är antingen på månatliga eller daglig nivå.|
|/reservationTransactions/read|Visa transaktionshistoriken för reserverade instanser av hanteringsgrupper.|
|/terms/read|Ange villkoren för en prenumeration eller en hanteringsgrupp.|
|/usageDetails/read|Visa användningsinformation för ett omfång för prenumerationer EA och WebDirect.|

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
|/checkNameAvailability/read|Kontrollerar om registret behållarnamn är tillgängliga för användning.|
|/locations/operationResults/read|Hämtar en asynkron resultat|
|/operations/read|Visar en lista över alla tillgängliga Azure Container registret REST API-åtgärder|
|/register/action|Registrerar prenumerationen för resursprovidern behållare registret och kan skapa behållaren register.|
|/registries/delete|Tar bort ett register för behållaren.|
|/registries/eventGridFilters/delete|Tar bort ett rutnät händelsefilter registret är behållare.|
|/registries/eventGridFilters/read|Hämtar egenskaperna för det angivna händelsen rutnätet filtret eller listar alla händelse rutnätet filter för den angivna behållaren registernyckeln.|
|/registries/eventGridFilters/write|Skapar eller uppdaterar ett händelsefilter rutnät för en behållare registret med de angivna parametrarna.|
|/registries/listCredentials/action|Visar inloggningsuppgifterna för det angivna behållaren registret.|
|/registries/listUsages/read|Visar kvoten användningsområden för den angivna behållaren registernyckeln.|
|/registries/operationStatuses/read|Hämtar status registret asynkron åtgärd|
|/registries/read|Hämtar egenskaperna för det angivna behållaren registret eller listar alla behållare register under den angivna resursgrupp eller prenumeration.|
|/registries/regenerateCredential/action|Återskapar en inloggningsuppgifterna för det angivna behållaren registret.|
|/registries/replications/delete|Tar bort en replikering från en behållare registret.|
|/registries/replications/operationStatuses/read|Hämtar en replikeringsstatus asynkron åtgärd|
|/registries/replications/read|Hämtar egenskaperna för den angivna replikeringen eller listar alla replikeringar för den angivna behållaren registernyckeln.|
|/registries/replications/write|Skapar eller uppdaterar en replikering för en behållare registret med de angivna parametrarna.|
|/registries/webhooks/delete|Tar bort en webhook från en behållare registret.|
|/registries/webhooks/getCallbackConfig/action|Hämtar konfigurationen för tjänsten URI och anpassade huvuden för webhooken.|
|/registries/webhooks/listEvents/action|Visar senaste händelser för den angivna webhooken.|
|/registries/webhooks/operationStatuses/read|Hämtar en webhook asynkrona Åtgärdsstatus|
|/registries/webhooks/ping/action|Utlöser en ping-händelse skickas till webhooken.|
|/registries/webhooks/read|Hämtar egenskaperna för den angivna webhooken eller listar alla webhooks för den angivna behållaren registernyckeln.|
|/registries/webhooks/write|Skapar eller uppdaterar en webhook för en behållare registret med de angivna parametrarna.|
|/registries/write|Skapar eller uppdaterar en behållare registret med de angivna parametrarna.|

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

| Åtgärd | Beskrivning |
|---|---|
|/containerServices/delete|Tar bort den angivna Behållartjänsten|
|/containerServices/read|Hämtar den angivna Behållartjänsten|
|/containerServices/write|Placerar eller uppdaterar den angivna Behållartjänsten|

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

| Åtgärd | Beskrivning |
|---|---|
|/applications/delete|Borttagningsåtgärd|
|/applications/listSecrets/action|Lista hemligheter|
|/applications/listSingleSignOnToken/action|Läsa token för enkel inloggning|
|/applications/read|Läsåtgärd|
|/applications/write|Skrivåtgärd|
|/applications/write|Skrivåtgärd|
|/listCommunicationPreference/action|Lista över inställningar för kommunikation|
|/operations/read|Läs-och skrivåtgärder|
|/updateCommunicationPreference/action|Uppdatera inställningar för kommunikation|

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

| Åtgärd | Beskrivning |
|---|---|
|/hubs/adobemetadata/action|Skapa eller uppdatera alla Azure-kund insikter Adobe Metadata|
|/hubs/adobemetadata/read|Läsa alla Azure-kund insikter Adobe Metadata|
|/hubs/authorizationPolicies/delete|Ta bort princip för delad åtkomst-signatur alla Azure-kund insikter|
|/hubs/authorizationPolicies/read|Läsa några Azure kunden insikter delad åtkomstprincip för signatur|
|/hubs/authorizationPolicies/regeneratePrimaryKey/action|Återskapa en primär nyckel för Azure kunden insikter delad signatur åtkomstprincip|
|/hubs/authorizationPolicies/regenerateSecondaryKey/action|Återskapa en sekundär nyckel för Azure kunden insikter delad signatur åtkomstprincip|
|/hubs/authorizationPolicies/write|Skapa eller uppdatera princip för delad åtkomst-signatur alla Azure-kund insikter|
|/hubs/Connectors/Activate/Action|Aktivera alla Azure kunden Insights Connector|
|/hubs/Connectors/Activate/Action|Aktivera alla Azure kunden Insights Connector|
|/hubs/connectors/delete|Ta bort alla Azure kunden Insights Connector|
|/hubs/connectors/getruntimestatus/action|Hämta alla Azure kunden Insights Connector Körningsstatus|
|/hubs/connectors/mappings/activate/action|Aktivera alla Azure kunden Insights Connector mappning|
|/hubs/connectors/mappings/delete|Ta bort alla Azure kunden Insights Connector mappning|
|/hubs/connectors/mappings/operations/read|Läs valfritt Åtgärdsresultat Azure kunden Insights Connector mappning|
|/hubs/connectors/mappings/read|Läsa alla Azure kunden Insights Connector mappning|
|/hubs/connectors/mappings/write|Skapa eller uppdatera alla Azure kunden Insights Connector mappning|
|/hubs/connectors/operations/read|Läs valfritt Åtgärdsresultat Azure kunden Insights Connector|
|/hubs/Connectors/Read|Läsa alla Azure kunden Insights Connector|
|/hubs/connectors/saveauthinfo/action|Skapa eller uppdatera alla Azure kunden Insights Connector autentisering information om Gatewayanslutningen|
|/hubs/connectors/update/action|Uppdatera alla Azure kunden Insights Connector|
|/hubs/connectors/write|Skapa eller uppdatera alla Azure kunden Insights Connector|
|/hubs/crmmetadata/action|Skapa eller uppdatera alla Azure-kund insikter Crm Metadata|
|/hubs/crmmetadata/read|Läsa alla Azure-kund insikter Crm Metadata|
|/hubs/delete|Ta bort alla Azure kunden Insights hubb|
|/hubs/gdpr/delete|Ta bort alla Azure-kund insikter BNPR|
|/hubs/gdpr/read|Läsa alla Azure-kund insikter BNPR|
|/hubs/gdpr/write|Skapa eller uppdatera alla Azure-kund insikter BNPR|
|/hubs/getbillingcredits/read|Hämta Azure kunden Insights hubb fakturering krediter|
|/hubs/getbillinghistory/read|Hämta Azure kunden Insights hubb fakturering historik|
|/hubs/images/delete|Ta bort alla Azure-kund insikter avbildningen|
|/hubs/images/read|Läs valfri Azure kunden insikter bild|
|/hubs/images/write|Skapa eller uppdatera någon Azure kunden insikter bild|
|/hubs/interactions/delete|Ta bort alla azure kundernas insikter interaktioner|
|/hubs/interactions/operations/read|Läs valfritt Åtgärdsresultat Azure kundinteraktion insikter|
|/hubs/interactions/read|Läsa alla Azure kundinteraktion insikter|
|/hubs/interactions/suggestrelationshiplinks/action|Föreslå relationen länkar för alla Azure kundernas insikter interaktioner|
|/hubs/interactions/write|Skapa eller uppdatera alla Azure kundinteraktion insikter|
|/hubs/kpi/delete|Ta bort alla Azure-kund insikter Key Performance Indicator|
|/hubs/kpi/operations/read|Läs valfritt Åtgärdsresultat Azure kunden insikter prestationsindikatorer|
|/hubs/kpi/read|Läsa alla Azure-kund insikter Key Performance Indicator|
|/hubs/kpi/reprocess/action|Bearbeta alla Azure-kund insikter KPI: er|
|/hubs/kpi/write|Skapa eller uppdatera en Azure-kund insikter Key Performance Indicator|
|/hubs/links/delete|Ta bort alla Azure-kund insikter länkar|
|/hubs/links/operations/read|Läs valfritt Åtgärdsresultat Azure kunden insikter länkar|
|/hubs/links/read|Läsa alla Azure-kund insikter länkar|
|/hubs/links/write|Skapa eller uppdatera Azure kunden länkar|
|/hubs/msemetadata/action|Skapa eller uppdatera alla Azure-kund insikter mus Metadata|
|/hubs/msemetadata/read|Läsa alla Azure-kund insikter mus Metadata|
|/hubs/operationresults/read|Hämta Azure kunden Insights hubb Åtgärdsresultat|
|/hubs/predictions/delete|Ta bort alla Azure-kund insikter förutsägelser|
|/hubs/predictions/operations/read|Läs valfritt Åtgärdsresultat Azure kunden insikter förutsägelser|
|/hubs/predictions/read|Läsa alla Azure-kund insikter förutsägelser|
|/hubs/predictions/write|Skapa eller uppdatera alla Azure-kund förutsägelser|
|/hubs/predictivematchpolicies/delete|Ta bort alla Azure-kund insikter förutsägande matchar principer|
|/hubs/predictivematchpolicies/operations/read|Läs valfritt Åtgärdsresultat Azure kunden insikter förutsägande matchar principer|
|/hubs/predictivematchpolicies/read|Läsa alla Azure-kund insikter förutsägande matchar principer|
|/hubs/predictivematchpolicies/write|Skapa eller uppdatera policyer Azure kunden insikter förutsägande matchning|
|/hubs/profiles/delete|Ta bort alla Azure-kund insikter profil|
|/hubs/profiles/operations/read|Läs valfritt Åtgärdsresultat Azure kunden insikter profil|
|/hubs/profiles/read|Läsa alla Azure-kund insikter profil|
|/hubs/profiles/write|Skriv valfri Azure kunden insikter profil|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/hubs/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/hubs/providers/Microsoft.Insights/logDefinitions/read|Hämtar de tillgängliga loggarna för resurs|
|/hubs/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för resurs|
|/hubs/read|Läsa alla Azure kunden Insights hubb|
|/hubs/relationshiplinks/delete|Ta bort alla Azure-kund insikter relationen länkar|
|/hubs/relationshiplinks/operations/read|Läs valfritt Åtgärdsresultat Azure kunden insikter relationen länkar|
|/hubs/relationshiplinks/read|Läsa alla Azure-kund insikter relationen länkar|
|/hubs/relationshiplinks/write|Skapa eller uppdatera Azure kunden insikter relationen länkar|
|/hubs/relationships/delete|Ta bort alla Azure-kund insikter relationer|
|/hubs/relationships/operations/read|Läs valfritt Åtgärdsresultat Azure kunden insikter relationer|
|/hubs/relationships/read|Läsa alla Azure-kund insikter relationer|
|/hubs/relationships/write|Skapa eller uppdatera Azure kunden insikter relationer|
|/hubs/roleAssignments/delete|Ta bort alla Azure-kund insikter Rbac tilldelning|
|/hubs/roleAssignments/operations/read|Läs valfritt Åtgärdsresultat Azure kunden insikter Rbac tilldelning|
|/hubs/roleAssignments/read|Läs Azure kunden insikter Rbac tilldelningar|
|/hubs/roleAssignments/write|Skapa eller uppdatera Azure kunden insikter Rbac tilldelningar|
|/hubs/roles/read|Läsa alla Azure-kund insikter Rbac-roller|
|/hubs/salesforcemetadata/action|Skapa eller uppdatera alla Azure-kund insikter SalesForce-Metadata|
|/hubs/salesforcemetadata/read|Läsa alla Azure-kund insikter SalesForce-Metadata|
|/hubs/segments/delete|Ta bort alla Azure kundsegment insikter|
|/hubs/segments/dynamic/action|Hantering av alla Azure-kund Insight dynamiska segment|
|/hubs/segments/read|Läsa alla Azure kundsegment insikter|
|/hubs/segments/static/action|Hantering av alla Azure-kund Insight statiska segment|
|/hubs/segments/write|Skapa eller uppdatera alla Azure kundsegment insikter|
|/hubs/sqlconnectionstrings/delete|Ta bort alla Azure-kund insikter SqlConnectionStrings|
|/hubs/sqlconnectionstrings/read|Läsa alla Azure-kund insikter SqlConnectionStrings|
|/hubs/sqlconnectionstrings/write|Skapa eller uppdatera alla Azure-kund insikter SqlConnectionStrings|
|/hubs/suggesttypeschema/Action|Generera föreslår typen Schema från exempeldata|
|/hubs/tenantmanagement/read|Hantera Azure kunden Insights hubbsinställningar|
|/hubs/views/delete|Ta bort en Azure-kund insikter App vy|
|/hubs/views/read|Läs valfri Azure kunden insikter App vy|
|/hubs/views/write|Skapa eller uppdatera Azure kunden insikter App visa|
|/hubs/widgettypes/read|Läs en Azure kunden insikter App Widget typer|
|/hubs/write|Skapa eller uppdatera alla Azure kunden Insights hubb|
|/operations/read|Läs Azure kunden Insights Api Metadatas|
|/register/action|Registrerar prenumerationen för resursprovidern kunden insikter och kan skapa kunden Insights-resurser|
|/unregister/action|Avregistrerar prenumerationen för resursprovidern kunden insikter|

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

| Åtgärd | Beskrivning |
|---|---|
|/catalogs/delete|Tar bort katalogen.|
|/catalogs/read|Visa egenskaper för katalogen eller katalogerna under prenumeration eller resursgrupp.|
|/catalogs/write|Katalogen skapas eller uppdateras taggar och egenskaper för katalogen.|
|/checkNameAvailability/action|Kontrollerar tillgängligheten för katalog-namnet för klienten.|
|/operations/read|Visar en lista över åtgärder som är tillgängliga på Microsoft.DataCatalog resursprovidern.|
|/register/action|Registrerar prenumeration med Microsoft.DataCatalog resursprovidern.|
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
|/accounts/dataLakeStoreAccounts/read|Hämta information om ett länkade DataLakeStore-konto för DataLakeAnalytics-konto.|
|/accounts/dataLakeStoreAccounts/write|Skapa eller uppdatera en länkad DataLakeStore konto för DataLakeAnalytics-konto.|
|/accounts/delete|Ta bort ett DataLakeAnalytics konto.|
|/accounts/firewallRules/delete|Ta bort en brandväggsregel.|
|/accounts/firewallRules/read|Hämta information om en brandväggsregel.|
|/accounts/firewallRules/write|Skapa eller uppdatera en brandväggsregel.|
|/accounts/operationResults/read|Hämta resultatet av en DataLakeAnalytics konto.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Hämta diagnostikinställningar för DataLakeAnalytics-kontot.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Skapa eller uppdatera diagnostikinställningar för DataLakeAnalytics-kontot.|
|/accounts/providers/Microsoft.Insights/logDefinitions/read|Hämta de tillgängliga loggarna för DataLakeAnalytics-konto.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Hämta tillgängliga mått för DataLakeAnalytics-konto.|
|/accounts/read|Hämta information om ett befintligt DataLakeAnalytics-konto.|
|/accounts/storageAccounts/Containers/listSasTokens/action|Lista över SAS-token för behållare för lagring av länkade lagringskonton för DataLakeAnalytics-konto.|
|/accounts/storageAccounts/Containers/read|Hämta behållare av länkade lagringskonton för DataLakeAnalytics-konto.|
|/accounts/storageAccounts/delete|Avlänka ett lagringskonto från en DataLakeAnalytics-konto.|
|/accounts/storageAccounts/read|Hämta information om ett länkat lagringskonto för DataLakeAnalytics-konto.|
|/accounts/storageAccounts/write|Skapa eller uppdatera ett länkat lagringskonto för DataLakeAnalytics-konto.|
|/accounts/TakeOwnership/action|Bevilja behörighet att avbryta jobb som skickats av andra användare.|
|/accounts/write|Skapa eller uppdatera ett DataLakeAnalytics konto.|
|/locations/capability/read|Hämta Funktionsinformation om för en prenumeration angående med DataLakeAnalytics.|
|/locations/checkNameAvailability/action|Kontrollera tillgängligheten för ett DataLakeAnalytics kontonamn.|
|/locations/operationResults/read|Hämta resultatet av en DataLakeAnalytics konto.|
|/operations/read|Hämta tillgängliga åtgärder av DataLakeAnalytics.|
|/register/action|Registrera prenumeration på DataLakeAnalytics.|

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

| Åtgärd | Beskrivning |
|---|---|
|/accounts/delete|Ta bort ett DataLakeStore konto.|
|/accounts/enableKeyVault/action|Aktivera KeyVault för en DataLakeStore-konto.|
|/accounts/firewallRules/delete|Ta bort en brandväggsregel.|
|/accounts/firewallRules/read|Hämta information om en brandväggsregel.|
|/accounts/firewallRules/write|Skapa eller uppdatera en brandväggsregel.|
|/accounts/operationResults/read|Hämta resultatet av en DataLakeStore konto.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Hämta diagnostikinställningar för DataLakeStore-kontot.|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Skapa eller uppdatera diagnostikinställningar för DataLakeStore-kontot.|
|/accounts/providers/Microsoft.Insights/logDefinitions/read|Hämta de tillgängliga loggarna för DataLakeStore-konto.|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Hämta tillgängliga mått för DataLakeStore-konto.|
|/accounts/read|Hämta information om ett befintligt DataLakeStore-konto.|
|/Accounts/superuser/Action|Bevilja superanvändare på Data Lake Store när beviljas med Microsoft.Authorization/roleAssignments/write.|
|/accounts/trustedIdProviders/delete|Ta bort en betrodd identitetsleverantör.|
|/accounts/trustedIdProviders/read|Hämta information om en betrodd identitetsleverantör.|
|/accounts/trustedIdProviders/write|Skapa eller uppdatera en betrodd identitetsleverantör.|
|/accounts/write|Skapa eller uppdatera ett DataLakeStore konto.|
|/locations/capability/read|Hämta Funktionsinformation om för en prenumeration angående med DataLakeStore.|
|/locations/checkNameAvailability/action|Kontrollera tillgängligheten för ett DataLakeStore kontonamn.|
|/locations/operationResults/read|Hämta resultatet av en DataLakeStore konto.|
|/operations/read|Hämta tillgängliga åtgärder av DataLakeStore.|
|/register/action|Registrera prenumeration på DataLakeStore.|

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

| Åtgärd | Beskrivning |
|---|---|
|/locations/performanceTiers/read|Returnerar listan över tillgängliga prestandanivåer.|
|/performanceTiers/read|Returnerar listan över tillgängliga prestandanivåer.|
|/servers/delete|Tar bort en befintlig server.|
|/servers/firewallRules/delete|Tar bort en befintlig brandväggsregel.|
|/servers/firewallRules/read|Returnera listan över brandväggen regler för en server eller hämtar egenskaperna för den angivna brandväggsregeln.|
|/servers/firewallRules/write|Skapar en brandväggsregel med de angivna parametrarna eller uppdaterar en befintlig regel.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar disagnostic inställningen för resursen|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Returnera typer av mätvärden som är tillgängliga för databaser|
|/Servers/Read|Returnera listan över servrar eller hämtar egenskaperna för den angivna servern.|
|/servers/recoverableServers/read|Returnera återställningsbara MySQL-serverinformation|
|/servers/virtualNetworkRules/delete|Tar bort en befintlig regel för virtuella nätverk|
|/servers/virtualNetworkRules/read|Returnera listan över virtuella nätverk regler eller hämtar egenskaperna för den angivna virtuella nätverk regeln.|
|/servers/virtualNetworkRules/write|Skapar ett virtuellt nätverk med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna virtuella nätverk regeln.|
|/servers/write|Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern.|

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

| Åtgärd | Beskrivning |
|---|---|
|/locations/performanceTiers/read|Returnerar listan över tillgängliga prestandanivåer.|
|/performanceTiers/read|Returnerar listan över tillgängliga prestandanivåer.|
|/servers/delete|Tar bort en befintlig server.|
|/servers/firewallRules/delete|Tar bort en befintlig brandväggsregel.|
|/servers/firewallRules/read|Returnera listan över brandväggen regler för en server eller hämtar egenskaperna för den angivna brandväggsregeln.|
|/servers/firewallRules/write|Skapar en brandväggsregel med de angivna parametrarna eller uppdaterar en befintlig regel.|
|/servers/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar disagnostic inställningen för resursen|
|/servers/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/servers/providers/Microsoft.Insights/logDefinitions/read|Returnera typer av loggar som är tillgängliga för databaser|
|/servers/providers/Microsoft.Insights/metricDefinitions/read|Returnera typer av mätvärden som är tillgängliga för databaser|
|/Servers/Read|Returnera listan över servrar eller hämtar egenskaperna för den angivna servern.|
|/servers/recoverableServers/read|Returnera återställningsbara PostgreSQL-serverinformation|
|/servers/virtualNetworkRules/delete|Tar bort en befintlig regel för virtuella nätverk|
|/servers/virtualNetworkRules/read|Returnera listan över virtuella nätverk regler eller hämtar egenskaperna för den angivna virtuella nätverk regeln.|
|/servers/virtualNetworkRules/write|Skapar ett virtuellt nätverk med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna virtuella nätverk regeln.|
|/servers/write|Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern.|

## <a name="microsoftdevices"></a>Microsoft.Devices

| Åtgärd | Beskrivning |
|---|---|
|/checkNameAvailability/Action|Kontrollera om IotHub namn är tillgängligt|
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
|/ElasticPools/metricDefinitions/read|Hämtar tillgängliga mått för tjänsten IotHub|
|/iotHubs/certificates/generateVerificationCode/Action|Generera Verifieringskod|
|/iotHubs/certificates/verify/Action|Kontrollera resursens certifikat|
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
|/IotHubs/logDefinitions/read|Hämtar tillgängliga Loggdefinitioner för IotHub Service|
|/IotHubs/metricDefinitions/read|Hämtar tillgängliga mått för tjänsten IotHub|
|/iotHubs/quotaMetrics/Read|Hämta mätvärden kvot|
|/iotHubs/Read|Hämtar IotHub-resurser|
|/iotHubs/routing/$testall/Action|Testmeddelande mot alla befintliga vägar|
|/iotHubs/routing/$testnew/Action|Testmeddelande mot ett angivna test väg|
|/iotHubs/routingEndpointsHealth/Read|Hämtar hälsotillståndet för alla routning slutpunkter för en IotHub|
|/iotHubs/skus/Read|Hämta giltiga IotHub Skus|
|/iotHubs/Write|Skapa eller uppdatera IotHub resurs|
|/ operations/Läs|Hämta alla ResourceProvider-åtgärder|
|/provisioningServices/certificates/generateVerificationCode/Action|Generera Verifieringskod|
|/provisioningServices/certificates/verify/Action|Kontrollera resursens certifikat|
|/provisioningServices/Delete|Ta bort IotDps resurs|
|/provisioningServices/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/provisioningServices/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/provisioningServices/listkeys/Action|Hämta alla IotDps nycklar|
|/provisioningServices/logDefinitions/read|Hämtar tillgängliga Loggdefinitioner för tjänsten etablering|
|/provisioningServices/metricDefinitions/read|Hämtar tillgängliga mått för tjänsten etablering|
|/provisioningServices/ProvisioningServiceKeys/listkeys/Action|Hämta IotDps nycklar för nyckelnamn|
|/provisioningServices/Read|Hämta IotDps resurs|
|/provisioningServices/skus/Read|Hämta giltiga IotDps Skus|
|/provisioningServices/Write|Skapa IotDps resurs|
|/register/action|Registrera prenumerationen för resursprovidern IotHub och kan skapa IotHub resurser|
|/register/action|Registrera prenumerationen för resursprovidern IotHub och kan skapa IotHub resurser|
|/ användningar/Läs|Hämta prenumeration användningsinformation för den här providern.|

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

| Åtgärd | Beskrivning |
|---|---|
|/labCenters/delete|Ta bort lab datacenter.|
|/labCenters/read|Läsa lab datacenter.|
|/labCenters/write|Lägg till eller ändra lab datacenter.|
|/labs/artifactSources/armTemplates/read|Läs azure resource manager-mallar.|
|/labs/artifactSources/artifacts/GenerateArmTemplate/action|Genererar en ARM-mall för den angivna artefakten, överför de nödvändiga filerna till ett lagringskonto och validerar artefakt som skapas.|
|/labs/artifactSources/artifacts/read|Läsa artefakter.|
|/labs/artifactSources/delete|Ta bort artefakt källor.|
|/labs/artifactSources/read|Läsa artefakt källor.|
|/labs/artifactSources/write|Lägg till eller ändra artefakt källor.|
|/labs/ClaimAnyVm/action|Begär en slumpmässig claimable virtuell dator i labbet.|
|/labs/costs/read|Läsa kostnader.|
|/labs/costs/write|Lägg till eller ändra kostnaderna.|
|/labs/CreateEnvironment/action|Skapa virtuella datorer i ett labb.|
|/labs/customImages/delete|Ta bort anpassade avbildningar.|
|/labs/customImages/read|Läsa anpassade avbildningar.|
|/labs/customImages/write|Lägg till eller ändra anpassade avbildningar.|
|/labs/delete|Ta bort övningar.|
|/labs/ExportResourceUsage/action|Exporterar resursanvändningen lab till ett lagringskonto|
|/labs/formulas/delete|Ta bort formler.|
|/labs/formulas/read|Läsa formler.|
|/labs/formulas/write|Lägg till eller ändra formler.|
|/labs/galleryImages/read|Läsa galleriavbildningar.|
|/labs/GenerateUploadUri/action|Generera en URI för överföring av anpassade diskavbildningar till ett labb.|
|/labs/ImportVirtualMachine/action|Importera en virtuell dator till en annan labbet.|
|/labs/ListVhds/action|Lista över tillgängliga för att skapa en anpassad avbildning diskavbildningar.|
|/labs/notificationChannels/delete|Ta bort notificationchannels.|
|/labs/notificationChannels/Notify/action|Skicka meddelande till angivna kanal.|
|/labs/notificationChannels/read|Läsa notificationchannels.|
|/labs/notificationChannels/write|Lägg till eller ändra notificationchannels.|
|/labs/policySets/EvaluatePolicies/action|Utvärderar lab princip.|
|/labs/policySets/policies/delete|Ta bort principer.|
|/labs/policySets/policies/read|Läsa principer.|
|/labs/policySets/policies/write|Lägg till eller ändra principer.|
|/labs/read|Läsa övningar.|
|/labs/schedules/delete|Ta bort scheman.|
|/labs/schedules/Execute/action|Köra ett schema.|
|/labs/schedules/ListApplicable/action|Visar en lista över alla tillämpliga scheman|
|/labs/schedules/read|Läsa scheman.|
|/labs/schedules/write|Lägg till eller ändra scheman.|
|/labs/serviceRunners/delete|Ta bort tjänsten utlöpare.|
|/labs/serviceRunners/read|Läsa service utlöpare.|
|/labs/serviceRunners/write|Lägg till eller ändra tjänsten utlöpare.|
|/labs/users/delete|Ta bort användarprofiler.|
|/labs/users/disks/Attach/action|Ansluta och skapa lånet på disken för att den virtuella datorn.|
|/labs/users/disks/delete|Ta bort diskar.|
|/labs/users/disks/Detach/action|Koppla från och ta bort lånet för den disk som är ansluten till den virtuella datorn.|
|/labs/users/disks/read|Läsa diskar.|
|/labs/users/disks/write|Lägg till eller ändra diskar.|
|/labs/users/environments/delete|Ta bort miljöer.|
|/labs/users/environments/read|Läsa miljöer.|
|/labs/users/environments/write|Lägg till eller ändra miljöer.|
|/labs/users/read|Läsa användarprofiler.|
|/labs/users/secrets/delete|Ta bort hemligheter.|
|/labs/users/secrets/read|Läsa hemligheter.|
|/labs/users/secrets/write|Lägg till eller ändra hemligheter.|
|/labs/users/serviceFabrics/delete|Ta bort service fabric.|
|/labs/users/serviceFabrics/ListApplicableSchedules/action|Visar en lista över alla tillämpliga scheman|
|/labs/users/serviceFabrics/read|Läsa service fabric.|
|/labs/users/serviceFabrics/schedules/delete|Ta bort scheman.|
|/labs/users/serviceFabrics/schedules/Execute/action|Köra ett schema.|
|/labs/users/serviceFabrics/schedules/read|Läsa scheman.|
|/labs/users/serviceFabrics/schedules/write|Lägg till eller ändra scheman.|
|/labs/users/serviceFabrics/Start/action|Starta ett service fabric.|
|/labs/users/serviceFabrics/Stop/action|Stoppa ett service fabric|
|/labs/users/serviceFabrics/write|Lägg till eller ändra service fabric.|
|/labs/users/write|Lägg till eller ändra användarprofiler.|
|/labs/virtualMachines/AddDataDisk/action|Ansluta en ny eller befintlig datadisk till den virtuella datorn.|
|/labs/virtualMachines/ApplyArtifacts/action|Tillämpa artefakter till den virtuella datorn.|
|/labs/virtualMachines/Claim/action|Bli ägare till en befintlig virtuell dator|
|/labs/virtualMachines/delete|Ta bort virtuella datorer.|
|/labs/virtualMachines/DetachDataDisk/action|Koppla bort den angivna disken från den virtuella datorn.|
|/labs/virtualMachines/ListApplicableSchedules/action|Visar en lista över alla tillämpliga scheman|
|/labs/virtualMachines/read|Läsa virtuella datorer.|
|/labs/virtualMachines/Restart/action|Starta om en virtuell dator.|
|/labs/virtualMachines/schedules/delete|Ta bort scheman.|
|/labs/virtualMachines/schedules/Execute/action|Köra ett schema.|
|/labs/virtualMachines/schedules/read|Läsa scheman.|
|/labs/virtualMachines/schedules/write|Lägg till eller ändra scheman.|
|/labs/virtualMachines/Start/action|Starta en virtuell dator.|
|/labs/virtualMachines/Stop/action|Stoppa en virtuell dator|
|/labs/virtualMachines/TransferDisks/action|Överför ägarskapet för virtuella hårddiskar till dig själv|
|/labs/virtualMachines/UnClaim/action|Släpp ägarskap för en befintlig virtuell dator|
|/labs/virtualMachines/write|Lägg till eller ändra virtuella datorer.|
|/labs/virtualNetworks/delete|Ta bort virtuella nätverk.|
|/labs/virtualNetworks/read|Läsa virtuella nätverk.|
|/labs/virtualNetworks/write|Lägg till eller ändra virtuella nätverk.|
|/labs/write|Lägg till eller ändra övningar.|
|/locations/operations/read|Läs-och skrivåtgärder.|
|/register/action|Registrerar prenumerationen|
|/schedules/delete|Ta bort scheman.|
|/Schedules/Execute/Action|Köra ett schema.|
|/schedules/read|Läsa scheman.|
|/schedules/Retarget/action|Uppdaterar ett schema målresurs Id.|
|/schedules/write|Lägg till eller ändra scheman.|

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

| Åtgärd | Beskrivning |
|---|---|
|/databaseAccountNames/read|Söker efter tillgänglighet.|
|/databaseAccounts/changeResourceGroup/action|Ändra resursgruppen för ett databaskonto|
|/databaseAccounts/databases/collections/metricDefinitions/read|Läser samlingen måttdefinitioner.|
|/databaseAccounts/databases/collections/metrics/read|Läser samling mått.|
|/databaseAccounts/databases/collections/partitionKeyRangeId/metrics/read|Läsa databas konto partition nivån nyckelvärden|
|/databaseAccounts/databases/collections/partitions/metrics/read|Läsa databas konto partition nivån måtten|
|/databaseAccounts/databases/collections/partitions/usages/read|Läsa databaskonto partition nivån användningsområden|
|/databaseAccounts/databases/collections/usages/read|Läser samling användningsområden.|
|/databaseAccounts/databases/metricDefinitions/read|Läser databasen måttdefinitioner|
|/databaseAccounts/databases/metrics/read|Läser databasen mått.|
|/databaseAccounts/databases/usages/read|Läser databasen användningsområden.|
|/databaseAccounts/delete|Tar bort databasen konton.|
|/databaseAccounts/failoverPriorityChange/action|Ändra prioriteringarna för växling vid fel för områden i ett databaskonto. Används för att utföra manuell redundans|
|/databaseAccounts/listConnectionStrings/action|Hämta anslutningssträngar för ett databaskonto|
|/databaseAccounts/listKeys/action|Lista nycklar för ett databaskonto|
|/databaseAccounts/metricDefinitions/read|Läser databasen konto mått definitioner.|
|/databaseAccounts/metrics/read|Läser databasen konto mått.|
|/databaseAccounts/operationResults/read|Lässtatus för asynkron åtgärd|
|/databaseAccounts/percentile/metrics/read|Läsa måtten för svarstid|
|/databaseAccounts/percentile/sourceRegion/targetRegion/metrics/read|Läsa latens måtten för en viss region för källa och mål|
|/databaseAccounts/percentile/targetRegion/metrics/read|Läsa latens måtten för en specifik målsökväg region|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/databaseAccounts/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/databaseAccounts/providers/Microsoft.Insights/logDefinitions/read|Hämtar tillgängliga catageries för konto|
|/databaseAccounts/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för databasen konto|
|/databaseAccounts/read|Läser ett databaskonto.|
|/databaseAccounts/readonlykeys/action|Läser databasen readonly nycklar.|
|/databaseAccounts/readonlykeys/read|Läser databasen readonly nycklar.|
|/databaseAccounts/regenerateKey/action|Rotera ett databaskonto nycklar|
|/databaseAccounts/region/databases/collections/metrics/read|Läser regionala samling mått.|
|/databaseAccounts/region/databases/collections/partitionKeyRangeId/metrics/read|Läsa regionala databasen konto partition nivån nyckelvärden|
|/databaseAccounts/region/databases/collections/partitions/metrics/read|Läsa regionala databasen konto partition nivån måtten|
|/databaseAccounts/region/databases/collections/partitions/read|Läsa databas konto partitioner i en samling|
|/databaseAccounts/region/metrics/read|Läser konto mätvärdena region och databasen.|
|/databaseAccounts/usages/read|Läser databasen konto användningsområden.|
|/databaseAccounts/write|Uppdatera en databas-konton.|
|/locations/deleteVirtualNetworkOrSubnets/action|Meddelar Microsoft.DocumentDB att VirtualNetwork eller undernät tas bort|
|/operationResults/read|Lässtatus för asynkron åtgärd|
|/operations/read|Läs-och skrivåtgärder som är tillgängliga för Microsoft DocumentDB |
|/register/action| Registerresursleverantören Microsoft DocumentDB för prenumerationen|

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

| Åtgärd | Beskrivning |
|---|---|
|/checkDomainAvailability/Action|Kontrollera om en domän kan köpas|
|/ domäner/ta bort|Ta bort en befintlig domän.|
|/Domains/domainownershipidentifiers/delete|Ta bort ägarskap identifierare|
|/Domains/domainownershipidentifiers/Read|Lista över ägarskapet identifierare|
|/Domains/domainownershipidentifiers/Read|Hämta ägarskap identifierare|
|/Domains/domainownershipidentifiers/Write|Skapa eller uppdatera identifierare|
|/domains/operationresults/Read|En åtgärd som domän|
|/Domains/Operations/Read|Visa en lista med alla åtgärder från domänregistrering för app service|
|/ domäner/Läs|Hämta listan över domäner|
|/ domäner/Läs|Hämta domän|
|/domains/renew/Action|Förnya en befintlig domän.|
|/ domäner/skrivning|Lägg till en ny domän eller uppdatera en befintlig|
|/generateSsoRequest/Action|Skapa en begäran för att logga in kontrollcenter för domänen.|
|/listDomainRecommendations/Action|Hämta listan domän rekommendationer baserat på nyckelord|
|/register/action|Microsoft Domains registerresursleverantören för prenumerationen|
|/topLevelDomains/listAgreements/Action|Listan avtal åtgärd|
|/topLevelDomains/Read|Hämta toplevel domäner|
|/topLevelDomains/Read|Hämta toplevel domän|
|/validateDomainRegistrationInformation/Action|Verifiera domän köp objekt utan att skicka den|

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

| Åtgärd | Beskrivning |
|---|---|
|/lcsprojects/clouddeployments/read|Visa Microsoft Dynamics AX 2012 R3 utvärdering distributioner i Microsoft Dynamics livscykel Services-projekt som hör till en användare|
|/lcsprojects/clouddeployments/write|Skapa Microsoft Dynamics AX 2012 R3 utvärdering distribution i ett Microsoft Dynamics livscykel Services-projekt som hör till en användare. Distributioner kan hanteras från Azure-hanteringsportalen|
|/lcsprojects/connectors/read|Läs kopplingar som hör till ett Microsoft Dynamics livscykel Services-projekt|
|/lcsprojects/connectors/write|Skapa och uppdatera kopplingar som hör till ett Microsoft Dynamics livscykel Services-projekt|
|/lcsprojects/delete|Ta bort Microsoft Dynamics livscykel Services-projekt som hör till användaren|
|/lcsprojects/read|Visa Microsoft Dynamics livscykel Services-projekt som hör till en användare|
|/lcsprojects/write|Skapa och uppdatera Microsoft Dynamics livscykel Services-projekt som tillhör användaren. Endast namn och beskrivning egenskaper kan uppdateras. Prenumeration och plats som är kopplade till projektet kan inte uppdateras när den har skapats|

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

| Åtgärd | Beskrivning |
|---|---|
|/eventSubscriptions/delete|Ta bort en eventSubscription|
|/eventSubscriptions/getFullUrl/action|Hämta fullständiga URL: en för händelseprenumerationen|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar diagnostikinställningen för prenumerationer på händelser|
|/eventSubscriptions/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningen för prenumerationer på händelser|
|/eventSubscriptions/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för eventSubscriptions|
|/eventSubscriptions/read|Läs en eventSubscription|
|/eventSubscriptions/write|Skapa eller uppdatera en eventSubscription|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar diagnostikinställningen för avsnitt|
|/extensionTopics/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningen för avsnitt|
|/extensionTopics/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för avsnitt|
|/register/action|Registrerar eventSubscription för resursprovidern EventGrid och kan skapa händelse rutnätet prenumerationer.|
|/topics/delete|Ta bort ett ämne|
|/topics/listKeys/action|Lista nycklar för avsnittet|
|/topics/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar diagnostikinställningen för avsnitt|
|/topics/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningen för avsnitt|
|/topics/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för avsnitt|
|/topics/read|Läs ett ämne|
|/topics/regenerateKey/action|Generera nyckel för avsnittet|
|/topics/write|Skapa eller uppdatera ett avsnitt|

## <a name="microsofteventhub"></a>Microsoft.EventHub

| Åtgärd | Beskrivning |
|---|---|
|/checkNameAvailability/action|Kontrollerar tillgänglighet för namnområden i en viss prenumeration.|
|/checkNamespaceAvailability/action|Kontrollerar tillgänglighet för namnområden i en viss prenumeration. Detta API är inaktuell. Använd CheckNameAvailabiltiy i stället.|
|/namespaces/authorizationRules/action|Uppdateringar Namespace auktoriseringsregeln. Detta API är depricated. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln Namespace i stället... Den här åtgärden stöds inte på API-version 2017-04-01.|
|/namespaces/authorizationRules/delete|Ta bort Namespace auktoriseringsregeln. Auktoriseringsregeln standard Namespace kan inte tas bort. |
|/namespaces/authorizationRules/listkeys/action|Hämta anslutningssträngen till namnområdet|
|/namespaces/authorizationRules/read|Hämta listan över beskrivning av auktoriseringsregler för namnområden.|
|/namespaces/authorizationRules/regenerateKeys/action|Återskapa den primära eller sekundära nyckeln till resursen|
|/namespaces/authorizationRules/write|Skapa en nivå auktoriseringsregler Namespace och uppdatera dess egenskaper. Åtkomstbehörigheter för regler för auktorisering, primära och sekundärnycklar kan uppdateras.|
|/namespaces/Delete|Ta bort namnområdesresurs|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Hämtar tillståndet regler nycklar för katastrofåterställning primära namnområdet|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Hämta Disaster Recovery primära Namespaces auktoriseringsregler|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|Inaktiverar haveriberedskap och stoppar replikering av ändringar från primära till sekundära namnområden.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|Kontrollerar tillgängligheten för namnområdesalias under de angivna prenumerationen.|
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
|/namespaces/eventhubs/read|Hämta lista över EventHub resurs beskrivningar|
|/namespaces/eventhubs/write|Skapa eller uppdatera EventHub-egenskaper.|
|/namespaces/messagingPlan/read|Hämtar meddelanden planera för ett namnområde. Detta API är inaktuell. Egenskaper som exponeras via MessagingPlan resursen flyttas till (överordnad) Namespace resurs API senare... Den här åtgärden stöds inte på API-version 2017-04-01.|
|/namespaces/messagingPlan/write|Uppdaterar Messaging planera för ett namnområde. Detta API är inaktuell. Egenskaper som exponeras via MessagingPlan resursen flyttas till (överordnad) Namespace resurs API senare... Den här åtgärden stöds inte på API-version 2017-04-01.|
|/namespaces/operationresults/read|Hämta status för namnområdesåtgärd|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|Hämta lista över Namespace diagnostikinställningar resurs beskrivningar|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|Hämta lista över Namespace diagnostikinställningar resurs beskrivningar|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|Hämta en lista över Namespace loggar resurs beskrivningar|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Hämta lista över Namespace mått resurs beskrivningar|
|/namespaces/read|Hämta listan över beskrivningar av namnområdesresurs|
|/namespaces/write|Skapa en resurs för Namespace och uppdatera dess egenskaper. Taggar och kapacitet för Namespace är egenskaper som kan uppdateras.|
|/operations/read|Hämta åtgärder|
|/register/action|Registrerar prenumerationen för EventHub-resursprovidern och gör det möjligt att skapa EventHub-resurser|
|/sku/read|Hämta lista över Sku resurs beskrivningar|
|/sku/regions/read|Hämta lista över SkuRegions resurs beskrivningar|
|/unregister/action|Registrerar EventHub-resursprovidern|

## <a name="microsoftfeatures"></a>Microsoft.Features

| Åtgärd | Beskrivning |
|---|---|
|/Features/Read|Hämtar en prenumerations funktioner.|
|/providers/features/read|Hämtar en prenumerations funktion hos en given resursprovider.|
|/providers/Features/register/Action|Registrerar funktionen för en prenumerations hos en given resursprovider.|
|/providers/Features/unregister/Action|Avregistrerar funktionen för en prenumeration hos en viss resursprovider.|

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

| Åtgärd | Beskrivning |
|---|---|
|/clusters/changerdpsetting/action|Ändra RDP-inställningen för HDInsight-kluster|
|/clusters/configurations/action|Uppdatera konfigurationen för HDInsight-kluster|
|/clusters/configurations/read|Hämta konfigurationer för HDInsight-kluster|
|/clusters/delete|Ta bort ett HDInsight-kluster|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar diagnostikinställningen för resursen HDInsight-kluster|
|/clusters/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningen för resursen HDInsight-kluster|
|/clusters/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för HDInsight-kluster|
|/clusters/read|Få information om HDInsight-kluster|
|/clusters/roles/resize/action|Ändra storlek på ett HDInsight-kluster|
|/clusters/write|Skapa eller uppdatera HDInsight-kluster|
|/locations/capabilities/read|Hämta prenumerationens funktioner|
|/locations/checkNameAvailability/read|Kontrollera namntillgänglighet|

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

| Åtgärd | Beskrivning |
|---|---|
|/jobs/delete|Tar bort ett befintligt jobb.|
|/jobs/listBitLockerKeys/action|Hämtar BitLocker-nycklar för det angivna jobbet.|
|/jobs/read|Hämtar egenskaperna för det angivna jobbet eller returnerar listan över jobb.|
|/jobs/write|Skapar ett jobb med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för det angivna jobbet.|
|/locations/read|Hämtar egenskaperna för den angivna platsen eller returnerar en lista med platser.|
|/register/action|Registrerar prenumerationen för resursprovidern import/export- och kan skapa jobb för import och export.|

## <a name="microsoftinsights"></a>Microsoft.Insights

| Åtgärd | Beskrivning |
|---|---|
|/ActionGroups/Delete|Tar bort en åtgärdsgrupp|
|/ActionGroups/Read|Läser en åtgärdsgrupp|
|/ActionGroups/Write|Skriver en åtgärdsgrupp|
|/ActivityLogAlerts/Activated/Action|Aktivitetsloggavisering har utlösts|
|/ActivityLogAlerts/Delete|Tar bort en aktivitetsloggavisering|
|/ActivityLogAlerts/Read|Läser en aktivitetsloggavisering|
|/ActivityLogAlerts/Write|Läser en aktivitetsloggavisering|
|/AlertRules/Activated/Action|Varningsregeln har aktiverats|
|/AlertRules/Delete|Tar bort en varningsregelskonfiguration|
|/AlertRules/Incidents/Read|Läser en konfiguration för varningsregelincidenter|
|/AlertRules/Read|Läser en varningsregelskonfiguration|
|/AlertRules/Resolved/Action|Varningsregeln har lösts|
|/AlertRules/Throttled/Action|Varningsregeln begränsas|
|/AlertRules/Write|Skriver till en varningsregelskonfiguration|
|/AutoscaleSettings/Delete|Tar bort en autoskalningskonfiguration|
|/AutoscaleSettings/providers/Microsoft.Insights/MetricDefinitions/Read|Läs måttdefinitioner|
|/AutoscaleSettings/Read|Läser en autoskalningskonfiguration|
|/AutoscaleSettings/Scaledown/Action|Åtgärd för autoskalning nedåt|
|/AutoscaleSettings/Scaleup/Action|Åtgärd för autoskalning uppåt|
|/AutoscaleSettings/Write|Skriver till en autoskalningskonfiguration|
|/Components/AnalyticsItems/Delete|Ta bort ett objekt för analys av Application Insights|
|/Components/AnalyticsItems/Read|Läsa ett Application Insights analytics-objekt|
|/Components/AnalyticsItems/Write|Skriva ett Application Insights analytics-objekt|
|/Components/AnalyticsTables/Action|Application Insights analytics Tabellåtgärd|
|/Components/AnalyticsTables/Delete|Ta bort en Application Insights tabell analytics schema|
|/Components/AnalyticsTables/Read|Läser en Application Insights tabell analytics schema|
|/Components/AnalyticsTables/Write|Skriva ett Application Insights tabell analytics schema|
|/Components/Annotations/Delete|Om du tar bort Application Insights anteckningen|
|/Components/Annotations/Read|Läsa anteckningens Application Insights|
|/Components/Annotations/Write|Skriva anteckningens Application Insights|
|/Components/Api/Read|Datainläsningen Application Insights component API|
|/Components/ApiKeys/Action|Generera en Application Insights API-nyckel|
|/Components/ApiKeys/Delete|Om du tar bort en Application Insights API-nyckel|
|/Components/ApiKeys/Read|Läser en Application Insights API-nyckel|
|/Components/BillingPlanForComponent/Read|Läsa ett faktureringsavtal för Application Insights-komponent|
|/Components/CurrentBillingFeatures/Read|Läsa fakturering funktionerna för Application Insights-komponent|
|/Components/CurrentBillingFeatures/Write|Skrivning fakturering funktionerna för Application Insights-komponent|
|/Components/DefaultWorkItemConfig/Read|Läser en Application Insights standard ALM integration konfiguration|
|/Components/Delete|Tar bort konfiguration av Application Insights-komponent|
|/Components/ExportConfiguration/Action|Application Insights Exportera inställningar för åtgärden|
|/Components/ExportConfiguration/Delete|Tar bort Application Insights exporterar du inställningar|
|/Components/ExportConfiguration/Read|Läsa Application Insights exporterar du inställningar|
|/Components/ExportConfiguration/Write|Skrivning Application Insights exporterar du inställningar|
|/Components/ExtendQueries/Read|Läsa Application Insights component utökad frågeresultat|
|/Components/Favorites/Delete|Om du tar bort Application Insights-favoriten|
|/Components/Favorites/Read|Läsa Application Insights-favoriten|
|/Components/Favorites/Write|Skriva ett Application Insights-favoriten|
|/Components/FeatureCapabilities/Read|Läsa Application Insights component-funktioner|
|/Components/GetAvailableBillingFeatures/Read|Läsa Application Insights komponenten faktureringsinformation funktioner som är tillgängliga|
|/Components/GetToken/Read|Läsa en token för Application Insights-komponent|
|/Components/ListMigrationDate/Action|Hämta tillbaka prenumeration migrering datum|
|/Components/ListMigrationDate/Read|Hämta tillbaka prenumeration migrering datum|
|/Components/MetricDefinitions/Read|Läsa måttdefinitionerna för Application Insights-komponent|
|/Components/Metrics/Read|Läsa Application Insights component mått|
|/Components/MigrateToNewpricingModel/Action|Migrera prenumeration till ny prissättningsmodell|
|/Components/Move/Action|Flytta en programkomponent insikter till en annan resursgrupp eller prenumeration|
|/Components/MyAnalyticsItems/Delete|Ta bort ett objekt för Application Insights personliga analytics|
|/Components/MyAnalyticsItems/Read|Läsa ett Application Insights personliga analytics-objekt|
|/Components/MyAnalyticsItems/Write|Skriva ett Application Insights personliga analytics-objekt|
|/Components/MyFavorites/Read|Läser en personlig Application Insights-favoriten|
|/Components/PricingPlans/Read|Läsa komponenten Application Insights priser plan|
|/Components/PricingPlans/Write|Skriva en Application Insights-komponent priser plan|
|/Components/ProactiveDetectionConfigs/Read|Konfigurera proaktiv identifiering av läsa Application Insights|
|/Components/ProactiveDetectionConfigs/Write|Skrivning Application Insights proaktiv identifiering konfiguration|
|/Components/providers/Microsoft.Insights/MetricDefinitions/Read|Läs måttdefinitioner|
|/Components/QuotaStatus/Read|Läsa Application Insights Komponentstatus kvot|
|/Components/Read|Läser en konfiguration av Application Insights-komponent|
|/Components/RollbackToLegacyPricingModel/Action|Äldre Prismodell rollback-prenumeration|
|/Components/SyntheticMonitorLocations/Read|Läsa Application Insights webtest platser|
|/Components/WorkItemConfigs/Delete|Om du tar bort en konfiguration för Application Insights ALM-integrering|
|/Components/WorkItemConfigs/Read|Läser en konfiguration för Application Insights ALM-integrering|
|/Components/WorkItemConfigs/Write|Skriva en konfiguration för Application Insights ALM-integrering|
|/Components/Write|Skriver till en konfiguration av Application Insights-komponent|
|/DiagnosticSettings/Delete|Tar bort konfigurationen av diagnostikinställningar|
|/DiagnosticSettings/Read|Läser en konfiguration av diagnostikinställningar|
|/DiagnosticSettings/Write|Skriver till konfiguration av diagnostikinställningar|
|/EventCategories/Read|Läser en händelsekategori|
|/eventtypes/digestevents/Read|Läs sammandrag av hanteringshändelsetyp|
|/eventtypes/Values/Read|Läs värden för hanteringshändelsetyp|
|/ExtendedDiagnosticSettings/Delete|Tar bort konfigurationen av utökade diagnostikinställningar|
|/ExtendedDiagnosticSettings/Read|Läser konfiguration av utökade diagnostikinställningar|
|/ExtendedDiagnosticSettings/Write|Skriver till konfiguration av utökade diagnostikinställningar|
|/LogDefinitions/Read|Läs loggdefinitioner|
|/LogProfiles/Delete|Ta bort konfiguration för loggprofiler|
|/LogProfiles/Read|Läs loggprofiler|
|/LogProfiles/Write|Skriver till en konfiguration för loggprofil|
|/MetricAlerts/Delete|Tar bort en måttavisering|
|/MetricAlerts/Read|Läser en måttavisering|
|/MetricAlerts/Write|Skriver en måttavisering|
|/MetricDefinitions/Microsoft.Insights/Read|Läs måttdefinitioner|
|/MetricDefinitions/providers/Microsoft.Insights/Read|Läs måttdefinitioner|
|/MetricDefinitions/Read|Läs måttdefinitioner|
|/Metrics/providers/Metrics/Read|Läs mått|
|/Metrics/Read|Läs mått|
|/Metrics/Write|Skriva mått|
|/Operations/Read|Läser åtgärder|
|/Register/Action|Registrera Microsoft Insights-providern|
|/Tenants/Register/Action|Startar microsoft insights-providern|
|/Unregister/Action|Registrera Microsoft Insights-providern|
|/Webtests/Delete|Tar bort en webbtestkonfiguration|
|/Webtests/GetToken/Read|Läsa en token för webbtestet|
|/Webtests/MetricDefinitions/Read|Läsa måttdefinitionerna för webbtestet|
|/Webtests/Metrics/Read|Läser en webtest mått|
|/Webtests/Read|Läser en webbtestkonfiguration|
|/Webtests/Write|Skriver till en webbtestkonfiguration|

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

| Åtgärd | Beskrivning |
|---|---|
|/checkNameAvailability/read|Kontrollera att nyckelvalvsnamnet är giltigt och inte används|
|/deletedVaults/read|Visa egenskaperna för de ej permanent borttagna nyckelvalven|
|/hsmPools/delete|Ta bort en HSM-pool|
|/hsmPools/joinVault/action|Anslut ett nyckelvalv till en HSM-pool|
|/hsmPools/read|Visa egenskaperna för en HSM-pool|
|/hsmPools/write|Skapa en ny HSM-pool eller uppdatera egenskaperna för en befintlig HSM-pool|
|/locations/deletedVaults/purge/action|Rensa ett ej permanent borttaget nyckelvalv|
|/locations/deletedVaults/read|Visa egenskaperna för ett ej permanent borttaget nyckelvalv|
|/locations/deleteVirtualNetworkOrSubnets/action|Aviserar Microsoft.KeyVault att ett virtuellt nätverk eller undernät tas bort|
|/locations/operationResults/read|Kontrollera resultatet för en långkörd åtgärd|
|/operations/read|Listar de åtgärder som finns tillgängliga på resursprovidern Microsoft.KeyVault|
|/register/action|Registrerar en prenumeration|
|/unregister/action|Avregistrerar en prenumeration|
|/vaults/accessPolicies/write|Uppdatera en befintlig åtkomstprincip genom att sammanfoga, ersätta eller lägga till en ny åtkomstprincip till ett valv.|
|/vaults/delete|Ta bort ett nyckelvalv|
|/vaults/deploy/action|Tillåter åtkomst till hemligheter i ett nyckelvalv vid distribution av Azure-resurser|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Read|Hämtar den diagnostiska inställningen för resursen|
|/vaults/providers/Microsoft.Insights/diagnosticSettings/Write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/vaults/providers/Microsoft.Insights/logDefinitions/read|Hämtar tillgängliga loggar för ett nyckelvalv|
|/vaults/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för ett nyckelvalv|
|/vaults/read|Visa egenskaperna för ett nyckelvalv|
|/vaults/secrets/read|Visa egenskaperna för en hemlighet, men inte dess värde|
|/vaults/secrets/write|Skapa en ny hemlighet eller uppdatera värdet för en befintlig hemlighet|
|/vaults/write|Skapa ett nytt nyckelvalv eller uppdatera egenskaperna för ett befintligt nyckelvalv|

## <a name="microsoftlabservices"></a>Microsoft.LabServices

| Åtgärd | Beskrivning |
|---|---|
|/labAccounts/CreateLab/action|Skapa ett labb i ett labb-konto.|
|/labAccounts/delete|Ta bort lab konton.|
|/labAccounts/labs/delete|Ta bort övningar.|
|/labAccounts/labs/environmentSettings/delete|Ta bort inställningen för miljön.|
|/labAccounts/labs/environmentSettings/environments/delete|Ta bort miljöer.|
|/labAccounts/labs/environmentSettings/environments/read|Läsa miljöer.|
|/labAccounts/labs/environmentSettings/environments/write|Lägg till eller ändra miljöer.|
|/labAccounts/labs/environmentSettings/Publish/action|Tillhandahåller/deprovisions krävs resurser för en miljö som inställningen baserat på aktuell status för inställningen labb-miljö.|
|/labAccounts/labs/environmentSettings/read|Läsa miljöinställning.|
|/labAccounts/labs/environmentSettings/write|Lägg till eller ändra inställningen för miljön.|
|/labAccounts/labs/read|Läsa övningar.|
|/labAccounts/labs/users/delete|Ta bort användare.|
|/labAccounts/labs/users/read|Läsa användare.|
|/labAccounts/labs/users/write|Lägg till eller ändra användare.|
|/labAccounts/labs/write|Lägg till eller ändra övningar.|
|/labAccounts/read|Läsa lab konton.|
|/labAccounts/write|Lägg till eller ändra lab konton.|
|/locations/operations/read|Läs-och skrivåtgärder.|
|/register/action|Registrerar prenumerationen|

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

| Åtgärd | Beskrivning |
|---|---|
|/accounts/delete|Ta bort en plats baserat Services-konto.|
|/accounts/listKeys/action|Visa en lista över nycklar plats baserat Services-konto|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/accounts/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/accounts/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för tjänstkonton för plats-baserad|
|/accounts/read|Hämta en plats baserat Services-konto.|
|/accounts/regenerateKey/action|Skapa ny plats baserat Services-konto primär eller sekundär nyckel|
|/accounts/write|Skapa eller uppdatera en plats baserat Services-konto.|
|/register/action|Registrera providern|

## <a name="microsoftlogic"></a>Microsoft.Logic

| Åtgärd | Beskrivning |
|---|---|
|/integrationAccounts/agreements/delete|Tar bort avtal i kontot för integrering.|
|/integrationAccounts/agreements/listContentCallbackUrl/action|Hämtar URL: en för återanrop för innehållet i integration konto.|
|/integrationAccounts/agreements/read|Läser avtal i kontot för integrering.|
|/integrationAccounts/agreements/write|Skapar eller uppdaterar avtal i kontot för integrering.|
|/integrationAccounts/assemblies/delete|Tar bort sammansättningen i integration konto.|
|/integrationAccounts/assemblies/listContentCallbackUrl/action|Hämtar återanrop URL: en för sammansättningen innehållet i integration konto.|
|/integrationAccounts/assemblies/read|Läser sammansättningen i integration konto.|
|/integrationAccounts/assemblies/write|Skapar eller uppdaterar sammansättningen i integration konto.|
|/integrationAccounts/batchConfigurations/delete|Tar bort konfigurationen batch i integration konto.|
|/integrationAccounts/batchConfigurations/read|Läser batch-konfigurationen i integration konto.|
|/integrationAccounts/batchConfigurations/write|Skapar eller uppdaterar batch-konfigurationen i integration konto.|
|/integrationAccounts/certificates/delete|Tar bort certifikatet i integration konto.|
|/integrationAccounts/certificates/read|Läser certifikatet i integration konto.|
|/integrationAccounts/certificates/write|Skapar eller uppdaterar certifikat i integration konto.|
|/integrationAccounts/delete|Tar bort kontot integrering.|
|/integrationAccounts/listCallbackUrl/action|Hämtar återanrop URL för integrering konto.|
|/integrationAccounts/listKeyVaultKeys/action|Hämtar nycklarna i nyckelvalvet.|
|/integrationAccounts/logTrackingEvents/action|Loggar händelser för spårning i kontot för integrering.|
|/integrationAccounts/maps/delete|Tar bort kartan i integration konto.|
|/integrationAccounts/maps/listContentCallbackUrl/action|Hämtar återanrop URL för kartinnehåll i integration konto.|
|/integrationAccounts/maps/read|Läser kartan i integration konto.|
|/integrationAccounts/maps/write|Skapar eller uppdaterar kartan i integration konto.|
|/integrationAccounts/partners/delete|Tar bort partner i integration konto.|
|/integrationAccounts/partners/listContentCallbackUrl/action|Hämtar återanrop URL för partner innehåll i integration konto.|
|/integrationAccounts/partners/read|Läser det kompanjon i integration konto.|
|/integrationAccounts/partners/write|Skapar eller uppdaterar partner i integration konto.|
|/integrationAccounts/providers/Microsoft.Insights/logDefinitions/read|Läser integrationskontots loggdefinitioner.|
|/integrationAccounts/read|Läser integration-konto.|
|/integrationAccounts/regenerateAccessKey/action|Återskapar åtkomstnyckelhemligheter.|
|/integrationAccounts/schemas/delete|Tar bort schemat i integration konto.|
|/integrationAccounts/schemas/listContentCallbackUrl/action|Hämtar återanrop URL för schemat innehåll i integration konto.|
|/integrationAccounts/schemas/read|Läser schemat i integration konto.|
|/integrationAccounts/schemas/write|Skapar eller uppdaterar schemat i integration konto.|
|/integrationAccounts/sessions/delete|Tar bort sessionen i integration konto.|
|/integrationAccounts/sessions/read|Läser batch-konfigurationen i integration konto.|
|/integrationAccounts/sessions/write|Skapar eller uppdaterar sessionen i integration konto.|
|/integrationAccounts/write|Skapar eller uppdaterar integration-konto.|
|/locations/workflows/validate/action|Verifierar arbetsflödet.|
|/operations/read|Hämtar åtgärden.|
|/register/action|Registrerar Microsoft.Logic-resursprovidern för en viss prenumeration.|
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
|/workflows/move/action|Flyttar arbetsflödet från befintligt prenumerations-id, resursgrupp och/eller namn till ett annat prenumerations-id, resursgrupp och/eller namn.|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/read|Läser diagnostikinställningar för arbetsflödet.|
|/workflows/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställning för arbetsflödet.|
|/workflows/providers/Microsoft.Insights/logDefinitions/read|Läser loggdefinitioner för arbetsflödet.|
|/workflows/providers/Microsoft.Insights/metricDefinitions/read|Läser måttdefinitioner för arbetsflödet.|
|/workflows/read|Läser arbetsflödet.|
|/workflows/regenerateAccessKey/action|Återskapar åtkomstnyckelhemligheter.|
|/Workflows/Run/Action|Startar en körning av arbetsflödet.|
|/workflows/runs/actions/listExpressionTraces/action|Hämtar spåren av arbetsflödeskörningens åtgärdsuttryck.|
|/workflows/runs/actions/read|Läser arbetsflödets köråtgärd.|
|/workflows/runs/actions/repetitions/listExpressionTraces/action|Hämtar upprepningsspåren av arbetsflödeskörningens åtgärdsuttryck.|
|/workflows/runs/actions/repetitions/read|Läser upprepningen av arbetsflödets körningsåtgärd.|
|/workflows/runs/actions/scoperepetitions/read|Läser upprepningen av reservationsomfånget för arbetsflödets körningsåtgärd.|
|/Workflows/Runs/Cancel/Action|Avbryter körningen av ett arbetsflöde.|
|/workflows/runs/operations/read|Läser åtgärdsstatus för arbetsflödeskörning.|
|-arbetsflöden körs/läsa|Läser arbetsflödeskörningen.|
|/Workflows/suspend/Action|Gör uppehåll i arbetsflödet.|
|/workflows/triggers/histories/read|Läser utlösarhistorik.|
|/workflows/triggers/histories/resubmit/action|Skickar om arbetsflödets utlösare.|
|/workflows/triggers/listCallbackUrl/action|Hämtar motringnings-URL:en för utlösaren.|
|/workflows/triggers/read|Läser utlösarna.|
|/workflows/triggers/reset/action|Återställer utlösaren.|
|/workflows/triggers/run/action|Utför utlösaren.|
|/workflows/triggers/setState/action|Anger läget för utlösare.|
|/workflows/validate/action|Verifierar arbetsflödet.|
|/workflows/versions/read|Läser arbetsflödesversionen.|
|/workflows/versions/triggers/listCallbackUrl/action|Hämtar motringnings-URL:en för utlösaren.|
|/workflows/write|Skapar eller uppdaterar arbetsflödet.|

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

| Åtgärd | Beskrivning |
|---|---|
|/commitmentPlans/commitmentAssociations/move/action|Flytta alla Machine Learning åtagande Plan Association|
|/commitmentPlans/commitmentAssociations/read|Läsa alla Machine Learning åtagande Plan Association|
|/commitmentPlans/delete|Ta bort en dator utbildningsplan åtagande|
|/commitmentPlans/join/action|Ansluta till en dator som utbildningsplan åtagande|
|/commitmentPlans/read|Läs valfri dator utbildningsplan åtagande|
|/commitmentPlans/write|Skapa eller uppdatera alla åtagande utbildningsplan datorn|
|/locations/operationresults/read|Hämta resultatet av en Machine Learning-åtgärd|
|/Locations/operationsstatus/Read|Hämta status för en pågående åtgärd i Machine Learning|
|/operations/read|Hämta åtgärder för Maskininlärning|
|/register/action|Registrerar prenumerationen för maskininlärning web tjänstresursprovider och kan skapa webbtjänster.|
|/skus/read|Hämta Machine Learning åtagande Plan SKU: er|
|/webServices/action|Skapa regionala egenskaper för webbtjänst för regioner som stöds|
|/webServices/delete|Ta bort alla Machine Learning-webbtjänst|
|/webServices/read|Läsa alla Machine Learning-webbtjänst|
|/webServices/write|Skapa eller uppdatera en Machine Learning-webbtjänst|
|/Workspaces/delete|Ta bort alla Maskininlärning arbetsytan|
|/Workspaces/listworkspacekeys/action|Lista nycklar för en Machine Learning-arbetsytan|
|/Workspaces/read|Läsa alla Machine Learning arbetsytan|
|/Workspaces/resyncstoragekeys/action|Synkronisera nycklar för lagringskonto har konfigurerats för en Machine Learning-arbetsytan|
|/Workspaces/write|Skapa eller uppdatera alla Maskininlärning arbetsytan|

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

| Åtgärd | Beskrivning |
|---|---|
|/operationalizationClusters/checkUpdate/action|Kontrollera om det finns uppdateringar för systemtjänster för operationalization klustret|
|/operationalizationClusters/delete|Ta bort alla värd-konto|
|/operationalizationClusters/listKeys/action|Lista nycklarna som associerats med operationalization kluster|
|/operationalizationClusters/read|Varje värd läskonto|
|/operationalizationClusters/updateSystem/action|Uppdatera systemtjänster i ett operationalization kluster|
|/operationalizationClusters/write|Skapa eller uppdatera värd-konto|
|/register/action|Registrerar prenumerations-ID för resursprovidern och kan skapa machine learning beräkningsresurser|

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

| Åtgärd | Beskrivning |
|---|---|
|/accounts/delete|Ta bort alla värd-konto|
|/accounts/read|Varje värd läskonto|
|/accounts/write|Skapa eller uppdatera värd-konto|
|/register/action|Registrerar prenumerations-ID för resursprovidern och kan skapa ett konto för värd|

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
|/labAccounts/labs/delete|Ta bort övningar.|
|/labAccounts/labs/environmentSettings/delete|Ta bort inställningen för miljön.|
|/labAccounts/labs/environmentSettings/environments/delete|Ta bort miljöer.|
|/labAccounts/labs/environmentSettings/environments/read|Läsa miljöer.|
|/labAccounts/labs/environmentSettings/environments/write|Lägg till eller ändra miljöer.|
|/labAccounts/labs/environmentSettings/read|Läsa miljöinställning.|
|/labAccounts/labs/environmentSettings/write|Lägg till eller ändra inställningen för miljön.|
|/labAccounts/labs/labVms/delete|Ta bort virtuella datorer för testlabbet.|
|/labAccounts/labs/labVms/read|Läsa virtuella datorer för testlabbet.|
|/labAccounts/labs/labVms/write|Lägg till eller ändra virtuella datorer för testlabbet.|
|/labAccounts/labs/read|Läsa övningar.|
|/labAccounts/labs/write|Lägg till eller ändra övningar.|
|/labAccounts/read|Läsa lab konton.|
|/labAccounts/write|Lägg till eller ändra lab konton.|
|/locations/operations/read|Läs-och skrivåtgärder.|
|/register/action|Registrerar prenumerationen|

## <a name="microsoftmanagement"></a>Microsoft.Management

| Åtgärd | Beskrivning |
|---|---|
|/checkNameAvailability/action|Kontrollerar om tillgänglighetsgruppens angivna namn är giltigt och unikt.|
|/getEntities/action|Lista över alla enheter (Hanteringsgrupper, prenumerationer, etc.) för den autentiserade användaren.|
|/managementGroups/delete|Ta bort hanteringsgruppen.|
|/managementGroups/read|Lista över hanteringsgrupper för den autentiserade användaren.|
|/managementGroups/subscriptions/delete|Frigör associerar prenumeration från hanteringsgruppen.|
|/managementGroups/subscriptions/write|Associerats befintliga prenumeration till hanteringsgruppen.|
|/managementGroups/write|Skapa eller uppdatera en hanteringsgrupp.|

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

| Åtgärd | Beskrivning |
|---|---|
|/ClassicDevServices/delete|Utför en borttagning på en resurs för klassiska dev-tjänst.|
|/ClassicDevServices/listSecrets/action|Hämtar en klassiska dev service resurs hantering av nycklar.|
|/ClassicDevServices/listSingleSignOnToken/action|Hämtar enkel inloggning på URL: en för en klassiska dev-tjänst.|
|/ClassicDevServices/read|Matchar en GET-åtgärd på en klassisk dev-tjänst.|
|/ClassicDevServices/regenerateKey/action|Genererar en klassiska dev service resurs hantering av nycklar.|
|/Operations/read|Läs-och skrivåtgärder för alla typer av resurser.|

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

| Åtgärd | Beskrivning |
|---|---|
|/agreements/OFFERS/plans/Cancel/Action|Avbryt ett avtal för ett givet marketplace-objekt|
|/agreements/offers/plans/read|Returnera ett avtal för ett givet marketplace-objekt|
|/agreements/OFFERS/plans/Sign/Action|Registrera ett avtal för ett givet marketplace-objekt|
|/agreements/read|Returnera alla avtal enligt angivna prenumerationen|
|/offertypes/publishers/offers/plans/agreements/read|Hämta ett avtal för en given marketplace-objektet för virtuell dator|
|/offertypes/Publishers/OFFERS/plans/agreements/Write|Logga in till eller avbryta ett avtal för en given marketplace-objektet för virtuell dator|

## <a name="microsoftmedia"></a>Microsoft.Media

| Åtgärd | Beskrivning |
|---|---|
|/checknameavailability/action|Kontrollerar om det finns ett Media Services-kontonamn|
|/mediaservices/delete|Ta bort alla Media Services-konto|
|/mediaservices/listKeys/action|Visa en lista med ACS-nycklar för Media Services-konto|
|/mediaservices/read|Läsa alla Media Services-konto|
|/mediaservices/regenerateKey/action|Återskapa ett Media Services ACS-nyckeln|
|/mediaservices/syncStorageKeys/action|Synkronisera Lagringsnycklar för ett anslutna Azure Storage-konto|
|/mediaservices/write|Skapa eller uppdatera alla Media Services-konto|
|/operations/read|Läsa alla Media Services-konto|
|/register/action|Registrerar prenumerationen för resursprovidern Media Services och används för att skapa Media Services-konton|

## <a name="microsoftmigrate"></a>Microsoft.Migrate

| Åtgärd | Beskrivning |
|---|---|
|/Operations/read|Läser de exponerade åtgärderna|

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
|/applicationGateways/read|Hämtar en Programgateway|
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
|/connections/delete|Tar bort VirtualNetworkGatewayConnection|
|/connections/read|Hämtar VirtualNetworkGatewayConnection|
|/connections/sharedkey/action|Hämta VirtualNetworkGatewayConnection SharedKey|
|/connections/sharedKey/read|Hämtar VirtualNetworkGatewayConnection SharedKey|
|/connections/sharedKey/write|Skapar eller uppdaterar en befintlig VirtualNetworkGatewayConnection SharedKey|
|/connections/vpndeviceconfigurationscript/read|Hämtar Vpn-enhet konfigurationen av VirtualNetworkGatewayConnection|
|/connections/write|Skapar eller uppdaterar en befintlig VirtualNetworkGatewayConnection|
|/ddosProtectionPlans/ddosProtectionPlanProxies/delete|Tar bort en DDoS-skydd Plan Proxy|
|/ddosProtectionPlans/ddosProtectionPlanProxies/read|Hämtar en definition av DDoS-skydd planera Proxy|
|/ddosProtectionPlans/ddosProtectionPlanProxies/write|Skapar en DDoS-skydd planera Proxy eller uppdateringar och befintliga DDoS-skydd planera Proxy|
|/ddosProtectionPlans/delete|Tar bort en Plan för DDoS-skydd|
|/ddosProtectionPlans/JOIN/Action|Ansluter till en Plan för DDoS-skydd|
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
|/dnszones/all/Read|Hämtar DNS-postuppsättningar över typer|
|/dnszones/CAA/delete|Ta bort postuppsättningen för ett angivet namn och typen CAA om du från en DNS-zon.|
|/dnszones/CAA/read|Hämta postuppsättningen av typen CAA, i JSON-format. Uppsättningen poster innehåller TTL-värde, taggar och etag.|
|/dnszones/CAA/write|Skapa eller uppdatera en uppsättning poster av typen 'CAA' inom en DNS-zon. De angivna posterna ersätter de aktuella posterna i uppsättningen av poster.|
|/dnszones/CNAME/delete|Ta bort postuppsättningen för ett angivet namn och typen CNAME om du från en DNS-zon.|
|/dnszones/CNAME/Read|Hämta postuppsättningen av typen CNAME, i JSON-format. Uppsättningen poster innehåller TTL-värde, taggar och etag.|
|/dnszones/CNAME/write|Skapa eller uppdatera en uppsättning poster av typen 'CNAME' inom en DNS-zon. De angivna posterna ersätter de aktuella posterna i uppsättningen av poster.|
|/dnszones/delete|Ta bort DNS-zonen i JSON-format. Zonegenskaperna omfattar taggar, etag, numberOfRecordSets och maxNumberOfRecordSets.|
|/dnszones/MX/delete|Ta bort postuppsättningen för ett angivet namn och typen MX om du från en DNS-zon.|
|/dnszones/MX/read|Hämta postuppsättningen av typen MX, i JSON-format. Uppsättningen poster innehåller en lista över poster samt TTL-värde, taggar och etag.|
|/dnszones/MX/write|Skapa eller uppdatera en uppsättning poster av typen 'MX' inom en DNS-zon. De angivna posterna ersätter de aktuella posterna i uppsättningen av poster.|
|/dnszones/NS/delete|Tar bort DNS-uppsättningen av poster av typen NS|
|/dnszones/NS/Read|Hämtar uppsättning av DNS-poster av typen NS|
|/dnszones/NS/write|Skapar eller uppdaterar DNS-postuppsättning av typen NS|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar diagnostikinställningar för DNS-zonen|
|/dnszones/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningar för DNS-zonen|
|/dnszones/providers/Microsoft.Insights/metricDefinitions/read|Hämtar måttdefinitionerna för DNS-zonen|
|/dnszones/PTR/delete|Ta bort postuppsättningen för ett angivet namn och typen PTR om du från en DNS-zon.|
|/dnszones/PTR/read|Hämta postuppsättningen av typen PTR, i JSON-format. Uppsättningen poster innehåller en lista över poster samt TTL-värde, taggar och etag.|
|/dnszones/PTR/write|Skapa eller uppdatera en uppsättning poster av typen 'PTR' inom en DNS-zon. De angivna posterna ersätter de aktuella posterna i uppsättningen av poster.|
|/dnszones/Read|Hämta DNS-zonen i JSON-format. Zonegenskaperna omfattar taggar, etag, numberOfRecordSets och maxNumberOfRecordSets. Observera att det här kommandot inte hämtar postuppsättningar i zonen.|
|/dnszones/recordsets/read|Hämtar DNS-postuppsättningar över typer|
|/dnszones/SOA/Read|Hämtar uppsättning av DNS-poster av typen SOA|
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
|/expressRouteCircuits/peerings/connections/read|Hämtar en ExpressRouteCircuit-anslutning|
|/expressRouteCircuits/peerings/connections/write|Skapar eller uppdaterar en befintlig ExpressRouteCircuit anslutning resurs|
|/expressRouteCircuits/peerings/delete|Tar bort en ExpressRouteCircuit Peering|
|/expressRouteCircuits/peerings/read|Hämtar en ExpressRouteCircuit Peering|
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
|/loadBalancers/inboundNatRules/join/action|Ansluter en inkommande nat-regel för belastningsutjämnare|
|/loadBalancers/inboundNatRules/read|Hämtar en belastningsutjämnare definition för inkommande nat-regel|
|/loadBalancers/inboundNatRules/write|Skapar en inkommande nat-regel för belastningsutjämnare eller uppdaterar en befintlig inkommande nat-regel för belastningsutjämnare|
|/loadBalancers/loadBalancingRules/read|Hämtar en belastningsutjämnare belastningsutjämnardefinition av belastningsutjämningsregel|
|/loadBalancers/networkInterfaces/read|Hämtar hänvisningar till alla nätverksgränssnitt under en belastningsutjämnare|
|/loadBalancers/outboundNatRules/read|Hämtar en utgående nat-regel belastningsutjämnardefinition|
|/loadBalancers/probes/join/action|Kan använda avsökningar av belastningsutjämning. Med den här behörigheten healthProbe egenskapen VM scale kan set referera exempelvis avsökningen.|
|/loadBalancers/probes/read|Hämtar en belastningsutjämningsavsökning|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar de diagnostiska inställningarna för belastningsutjämnaren|
|/loadBalancers/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningar för belastningen belastningsutjämnare|
|/loadBalancers/providers/Microsoft.Insights/logDefinitions/read|Hämtar händelserna för belastningsutjämnare|
|/loadBalancers/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för belastningsutjämnare|
|/loadBalancers/read|Hämtar en belastningsutjämnardefinition|
|/loadBalancers/virtualMachines/read|Hämtar hänvisningar till alla virtuella datorer under en belastningsutjämnare|
|/loadBalancers/write|Skapar en belastningsutjämnare eller uppdaterar en befintlig belastningsutjämnare|
|/localnetworkgateways/delete|Tar bort LocalNetworkGateway|
|/localnetworkgateways/read|Hämtar LocalNetworkGateway|
|/localnetworkgateways/write|Skapar eller uppdaterar en befintlig LocalNetworkGateway|
|/locations/checkDnsNameAvailability/read|Kontrollerar om dns-etikett finns på den angivna platsen|
|/locations/operationResults/read|Hämtar resultat av en asynkron post- eller DELETE-åtgärden|
|/locations/operations/read|Hämtar åtgärdsresurs motsvarande status för en asynkron åtgärd|
|/locations/usages/read|Hämtar resurserna användningsstatistik|
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
|/networkWatchers/queryTroubleshootResult/action|Felsökning resultatet från körts tidigare eller för närvarande hämtar kör felsökning igen.|
|/networkWatchers/read|Hämta definition för Övervakare nätverk|
|/networkWatchers/securityGroupView/action|Visa de konfigurerade och effektivt regler för nätverkssäkerhetsgrupper tillämpas på en virtuell dator.|
|/networkWatchers/topology/action|Hämtar en nätverket vy på protokollnivå för resurser och deras relationer i en resursgrupp.|
|/networkWatchers/troubleshoot/action|Startar felsökning på en resurs för nätverk i Azure.|
|/networkWatchers/write|Skapar en nätverksbevakaren eller uppdaterar en befintlig nätverksbevakaren|
|/operations/read|Hämta tillgängliga åtgärder|
|/publicIPAddresses/delete|Tar bort en offentlig Ip-adress.|
|/publicIPAddresses/join/action|Ansluter till en offentlig ip-adress|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/read|Hämta diagnostikinställningar för offentlig IP-adress|
|/publicIPAddresses/providers/Microsoft.Insights/diagnosticSettings/write|Skapa eller uppdatera diagnostikinställningar för offentlig IP-adress|
|/publicIPAddresses/providers/Microsoft.Insights/logDefinitions/read|Hämta Loggdefinitioner för offentlig IP-adress|
|/publicIPAddresses/providers/Microsoft.Insights/metricDefinitions/read|Hämta mätvärden definitionerna av offentlig IP-adress|
|/publicIPAddresses/read|Hämtar en definition av offentlig ip-adress.|
|/publicIPAddresses/write|Skapar en offentlig Ip-adress eller uppdaterar en befintlig offentlig Ip-adress. |
|/register/action|Registrerar prenumerationen|
|/routeFilters/delete|Tar bort en väg filterdefinition|
|/routeFilters/join/action|Ansluter till en vägfilter|
|/routeFilters/read|Hämtar en definition av flödet filter|
|/routeFilters/routeFilterRules/delete|Tar bort en regel för vägen filterdefinition|
|/routeFilters/routeFilterRules/read|Hämtar en regel för vägen filterdefinition|
|/routeFilters/routeFilterRules/write|Skapar en regel för vägen filter eller uppdaterar en befintlig väg filter regel|
|/routeFilters/write|Skapar ett vägfilter eller uppdaterar ett befintligt rotue filter|
|/routeTables/delete|Tar bort en definition av routningstabell|
|/routeTables/join/action|Ansluter till en routningstabell|
|/routeTables/read|Hämtar en definition av routningstabell|
|/routeTables/routes/delete|Tar bort en väg definition|
|/routeTables/routes/read|Hämtar en definition av väg|
|/routeTables/routes/write|Skapar en väg eller uppdaterar en befintlig|
|/routeTables/write|Skapar en routningstabell eller uppdaterar en befintlig tabell rotue|
|/securegateways/applicationRuleCollections/delete|Tar bort en regelsamling för programmet för säker Gateway|
|/securegateways/applicationRuleCollections/read|Hämta en regelsamling för programmet för en viss säker Gateway|
|/securegateways/applicationRuleCollections/write|Skapar eller uppdaterar en regelsamling för programmet för en säker Gateway|
|/securegateways/delete|Ta bort säkra Gateway|
|/securegateways/networkRuleCollections/delete|Tar bort en regelsamling för nätverket för en säker Gateway|
|/securegateways/networkRuleCollections/read|Hämta en regelsamling för nätverket för en given säker Gateway|
|/securegateways/networkRuleCollections/write|Skapar eller uppdaterar en regelsamling för nätverket för en säker Gateway|
|/securegateways/read|Få säker Gateway|
|/securegateways/write|Skapar eller uppdaterar en säker Gateway|
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
|/virtualHubs/read|Hämta en virtuell-hubb|
|/virtualHubs/write|Skapa eller uppdatera en virtuell-hubb|
|/virtualnetworkgateways/connections/read|Hämta VirtualNetworkGatewayConnection|
|/virtualNetworkGateways/delete|Tar bort en virtualNetworkGateway|
|/virtualnetworkgateways/generatevpnclientpackage/action|Generera VpnClient paketet för virtualNetworkGateway|
|/virtualnetworkgateways/generatevpnprofile/action|Generera VpnProfile paketet för VirtualNetworkGateway|
|/virtualnetworkgateways/getadvertisedroutes/action|Hämtar virtualNetworkGateway annonserade vägar|
|/virtualnetworkgateways/getbgppeerstatus/action|Hämtar virtualNetworkGateway bgp peer-status|
|/virtualnetworkgateways/getlearnedroutes/Action|Hämtar virtualnetworkgateway inlärda vägar|
|/virtualnetworkgateways/getvpnclientipsecparameters/action|Hämta Vpnclient Ipsec parametrar för VirtualNetworkGateway P2S-klienten.|
|/virtualnetworkgateways/getvpnprofilepackageurl/action|Hämtar URL för en profil för förgenererade VPN-klientpaketet|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar diagnostikinställningar för virtuellt nätverk Gateway|
|/virtualNetworkGateways/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningar för virtuell nätverksgateway åtgärden kompletteras av resursprovidern för insights.|
|/virtualNetworkGateways/providers/Microsoft.Insights/logDefinitions/read|Hämtar händelserna för virtuell nätverksgateway|
|/virtualNetworkGateways/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för virtuell nätverksgateway|
|/virtualNetworkGateways/read|Hämtar en VirtualNetworkGateway|
|/virtualnetworkgateways/reset/action|Återställer en virtualNetworkGateway|
|/virtualnetworkgateways/setvpnclientipsecparameters/action|Ange Vpnclient Ipsec-parametrarna för VirtualNetworkGateway P2S-klienten.|
|/virtualnetworkgateways/supportedvpndevices/action|Visar VPN-enheter som stöds|
|/virtualNetworkGateways/write|Skapar eller uppdaterar en VirtualNetworkGateway|
|/virtualNetworks/checkIpAddressAvailability/read|Kontrollera om Ip-adress är tillgänglig på det angivna virtuella nätverket|
|/virtualNetworks/customViews/get/action|Hämta en anpassad vy innehåll för virtuellt nätverk|
|/virtualNetworks/customViews/read|Hämta definition av en anpassad vy av virtuellt nätverk|
|/virtualNetworks/delete|Tar bort ett virtuellt nätverk|
|/virtualNetworks/peer/action|Peers ett virtuellt nätverk med ett annat virtuellt nätverk|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/read|Hämta diagnostikinställningar för virtuellt nätverk|
|/virtualNetworks/providers/Microsoft.Insights/diagnosticSettings/write|Skapa eller uppdatera diagnostikinställningar för det virtuella nätverket|
|/virtualNetworks/providers/Microsoft.Insights/logDefinitions/read|Hämta Loggdefinitioner för virtuellt nätverk|
|/virtualNetworks/providers/Microsoft.Insights/metricDefinitions/read|Hämta mått definitionerna av virtuellt nätverk|
|/virtualNetworks/read|Hämta definitionen av virtuellt nätverk|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/delete|Tar bort ett virtuellt nätverk peering-proxy|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/read|Hämtar ett virtuellt nätverk peering proxydefinitioner|
|/virtualNetworks/remoteVirtualNetworkPeeringProxies/write|Skapar en peering proxy för virtuellt nätverk eller uppdaterar en befintlig peering proxy för virtuellt nätverk|
|/virtualNetworks/subnets/delete|Tar bort ett undernät för virtuellt nätverk|
|/virtualNetworks/subnets/join/action|Ansluter till ett virtuellt nätverk|
|/virtualNetworks/subnets/joinViaServiceEndpoint/action|Ansluter till resursen, till exempel lagringskonto eller SQL-databas till ett undernät.|
|/virtualNetworks/subnets/read|Hämtar en definition av undernät för virtuellt nätverk|
|/virtualNetworks/subnets/resourceNavigationLinks/delete|Tar bort en resurs navigeringslänk|
|/virtualNetworks/subnets/resourceNavigationLinks/read|Hämta definitionen för resurslänken för navigering|
|/virtualNetworks/subnets/resourceNavigationLinks/write|Skapar en Resurslänk navigering eller uppdaterar en befintlig resurs navigeringslänk|
|/virtualNetworks/subnets/virtualMachines/read|Hämtar hänvisningar till alla virtuella datorer i ett undernät för virtuellt nätverk|
|/virtualNetworks/subnets/write|Skapar ett undernät för virtuellt nätverk eller uppdaterar ett befintligt undernät för virtuellt nätverk|
|/virtualNetworks/taggedTrafficConsumers/delete|Tar bort en konsument taggade trafik|
|/virtualNetworks/taggedTrafficConsumers/read|Hämta märkta trafik konsument-definition|
|/virtualNetworks/taggedTrafficConsumers/validate/action|Verifierar taggade trafik konsumenter|
|/virtualNetworks/taggedTrafficConsumers/write|Skapar taggade trafik konsumenter eller uppdaterar en befintlig märkta trafik konsumenten|
|/virtualNetworks/usages/read|Hämta IP-användningsområden för varje undernät i det virtuella nätverket|
|/virtualNetworks/virtualMachines/read|Hämtar hänvisningar till alla virtuella datorer i ett virtuellt nätverk|
|/virtualNetworks/virtualNetworkPeerings/delete|Tar bort ett virtuellt nätverk som peering|
|/virtualNetworks/virtualNetworkPeerings/read|Hämtar en definition av peering virtuellt nätverk|
|/virtualNetworks/virtualNetworkPeerings/write|Skapar en peering virtuellt nätverk eller uppdaterar ett befintligt virtuellt nätverk peering|
|/virtualNetworks/write|Skapar ett virtuellt nätverk eller uppdaterar ett befintligt virtuellt nätverk|
|/virtualNetworkTaps/delete|Ta bort tryck för virtuellt nätverk|
|/virtualNetworkTaps/join/action|Ansluter till ett virtuellt nätverk tryck|
|/virtualNetworkTaps/read|Hämta tryck för virtuellt nätverk|
|/virtualNetworkTaps/write|Skapa eller uppdatera tryck för virtuellt nätverk|
|/virtualwans/delete|Tar bort en virtuell Wan|
|/virtualwans/read|Hämta en virtuell Wan|
|/virtualWans/virtualHubProxies/delete|Tar bort en virtuell Hub-proxy|
|/virtualWans/virtualHubProxies/read|Hämtar en definition av virtuella hubb proxy|
|/virtualWans/virtualHubProxies/write|Skapar en virtuell hubb proxy eller uppdaterar en virtuell Hub-proxy|
|/virtualwans/virtualHubs/read|Hämtar alla virtuella hubbar som är kopplade till virtuella Wan.|
|/virtualwans/vpnconfiguration/read|Hämtar en Vpn-konfiguration|
|/virtualWans/vpnSiteProxies/delete|Tar bort platsen för Vpn-proxy|
|/virtualWans/vpnSiteProxies/read|Hämtar en definition av platsen för Vpn-proxy|
|/virtualWans/vpnSiteProxies/write|Skapar en platsen för Vpn-proxy eller uppdaterar en platsen för Vpn-proxy|
|/virtualwans/vpnSites/read|Hämtar alla VPN-platser som är kopplade till virtuella Wan.|
|/virtualwans/write|Skapa eller uppdatera virtuella Wan|
|/vpnGateways/read|Hämtar en VpnGateway.|
|/vpnGateways/vpnConnections/read|Hämtar en VpnConnection.|
|/vpnGateways/vpnConnections/write|Placerar en VpnConnection.|
|/vpnGateways/write|Placerar en VpnGateway.|
|/vpnsites/delete|Tar bort en platsen för Vpn-resurs.|
|/vpnsites/read|Hämtar en platsen för Vpn-resurs.|
|/vpnsites/write|Skapar eller uppdaterar en platsen för Vpn-resurs.|

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

| Åtgärd | Beskrivning |
|---|---|
|/CheckNamespaceAvailability/action|Kontrollerar huruvida ett visst namnområdesresursnamn finns i NotificationHub-tjänsten.|
|/Namespaces/authorizationRules/action|Hämta listan över beskrivning av auktoriseringsregler för namnområden.|
|/Namespaces/authorizationRules/delete|Ta bort Namespace auktoriseringsregeln. Auktoriseringsregeln standard Namespace kan inte tas bort. |
|/Namespaces/authorizationRules/listkeys/action|Hämta anslutningssträngen till namnområdet|
|/Namespaces/authorizationRules/read|Hämta listan över beskrivning av auktoriseringsregler för namnområden.|
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
|/Namespaces/NotificationHubs/Delete|Ta bort Notification Hub-resurs|
|/Namespaces/NotificationHubs/metricDefinitions/read|Hämta lista över Namespace mått resurs beskrivningar|
|/Namespaces/NotificationHubs/pnsCredentials/action|Hämta alla Notification Hub PNS-autentiseringsuppgifter. Detta omfattar WNS, MPNS, APN, GCM och Baidu autentiseringsuppgifter|
|/Namespaces/NotificationHubs/read|Hämta listan över resursbeskrivningar av Notification Hub|
|/Namespaces/NotificationHubs/write|Skapa en Meddelandehubb och uppdatera dess egenskaper. Egenskaperna är främst PNS-autentiseringsuppgifter. Auktoriseringsregler och TTL-värde|
|/Namespaces/read|Hämta listan över beskrivningar av namnområdesresurs|
|/Namespaces/write|Skapa en resurs för Namespace och uppdatera dess egenskaper. Taggar och kapacitet för Namespace är egenskaper som kan uppdateras.|
|/register/action|Registrerar prenumerationen för NotificationHubs-resursprovidern och gör det möjligt att skapa namnområden och NotificationHubs|

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

| Åtgärd | Beskrivning |
|---|---|
|/linkTargets/read|Visar en lista över befintliga konton som inte är associerade med en Azure-prenumeration. Använd en kund-id som returneras av den här åtgärden i kundens id-egenskapen för skapa arbetsytan igen för att länka den här Azure-prenumeration till en arbetsyta.|
|/register/action|Registrera en prenumeration på en resursleverantör.|
|/workspaces/analytics/query/action|Sökningen med nya motorn.|
|/workspaces/analytics/query/schema/read|Hämta schema för sökning V2.|
|/workspaces/api/query/action|Sökningen med nya motorn.|
|/workspaces/api/query/schema/read|Hämta schema för sökning V2.|
|/workspaces/configurationScopes/delete|Ta bort konfigurationsomfång|
|/workspaces/configurationScopes/read|Hämta konfigurationsomfång|
|/workspaces/configurationScopes/write|Ange konfigurationsomfång|
|/workspaces/datasources/delete|Ta bort datakällor i en arbetsyta.|
|/workspaces/datasources/read|Hämta datakällor i en arbetsyta.|
|/workspaces/datasources/write|Skapa eller uppdatera datakällor i en arbetsyta.|
|/workspaces/delete|Tar bort en arbetsyta. Om arbetsytan var kopplad till en befintlig arbetsyta vid skapandet bort arbetsytan den kopplats till inte.|
|/workspaces/generateregistrationcertificate/action|Genererar registreringscertifikat för arbetsytan. Det här certifikatet används för att ansluta Microsoft System Center Operation Manager till arbetsytan.|
|/workspaces/intelligencepacks/disable/action|Inaktiverar en informationspaketet för en viss arbetsyta.|
|/workspaces/intelligencepacks/enable/action|Gör en informationspaketet för en viss arbetsyta.|
|/workspaces/intelligencepacks/read|Visar en lista över alla intelligence packs som är synliga för en viss arbetsyta och visar också om Packet är aktiverat eller inaktiverat för den arbetsytan.|
|/workspaces/linkedServices/delete|Ta bort länkade tjänster enligt angivna arbetsytan.|
|/workspaces/linkedServices/read|Hämta länkade tjänster enligt angivna arbetsytan.|
|/workspaces/linkedServices/write|Skapa eller uppdatera länkade tjänster enligt angivna arbetsytan.|
|/workspaces/listKeys/action|Hämtar lista nycklarna för arbetsytan. Dessa nycklar används för att ansluta Microsoft åtgärdsinformation agenter till arbetsytan.|
|/workspaces/listKeys/read|Hämtar lista nycklarna för arbetsytan. Dessa nycklar används för att ansluta Microsoft åtgärdsinformation agenter till arbetsytan.|
|/workspaces/managementGroups/read|Hämtar namn och metadata för System Center Operations Manager-hanteringsgrupper som är ansluten till den här arbetsytan.|
|/workspaces/metricDefinitions/read|Hämta mått definitioner under arbetsytan|
|/workspaces/notificationSettings/delete|Ta bort användarens meddelandeinställningarna för arbetsytan.|
|/workspaces/notificationSettings/read|Hämta användarens meddelandeinställningarna för arbetsytan.|
|/workspaces/notificationSettings/write|Ange användarens meddelandeinställningarna för arbetsytan.|
|/workspaces/purge/action|Ta bort angivna data från arbetsytan|
|/workspaces/read|Hämtar en befintlig arbetsyta|
|/workspaces/savedSearches/delete|Tar bort en sparad sökning|
|/workspaces/savedSearches/read|Hämtar en sparad sökning|
|/workspaces/savedSearches/write|Skapar en sparad sökning|
|/workspaces/schema/read|Hämtar Sök schemat för arbetsytan.  Sök-schemat innehåller exponerade fält och deras typer.|
|/workspaces/search/action|Kör en sökfråga|
|/workspaces/sharedKeys/action|Hämtar de delade nycklarna för arbetsytan. Dessa nycklar används för att ansluta Microsoft åtgärdsinformation agenter till arbetsytan.|
|/workspaces/sharedKeys/read|Hämtar de delade nycklarna för arbetsytan. Dessa nycklar används för att ansluta Microsoft åtgärdsinformation agenter till arbetsytan.|
|/workspaces/storageinsightconfigs/delete|Tar bort en konfiguration för lagring. Microsoft åtgärdsinformation stoppas från att läsa data från lagringskontot.|
|/workspaces/storageinsightconfigs/read|Hämtar en konfiguration för lagring.|
|/workspaces/storageinsightconfigs/write|Skapar en ny konfiguration för lagring. Dessa konfigurationer för att hämta data från en plats i ett befintligt lagringskonto.|
|/workspaces/usages/read|Hämtar användningsdata för en arbetsyta inklusive mängden data som läses av arbetsytan.|
|/workspaces/write|Skapar en ny arbetsyta eller länkar till en befintlig arbetsyta genom att tillhandahålla kund-id från befintlig arbetsyta.|

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

| Åtgärd | Beskrivning |
|---|---|
|/managementAssociations/delete|Ta bort befintliga Management-kopplingen|
|/managementAssociations/read|Hämta befintliga för att associera Management|
|/managementAssociations/write|Skapa en ny Management-Association|
|/managementConfigurations/delete|Ta bort den befintliga Management Configuratin|
|/managementConfigurations/read|Hämta befintliga Management-konfiguration|
|/managementConfigurations/write|Skapa en ny Management-konfiguration|
|/register/action|Registrera en prenumeration på en resursleverantör.|
|/Solutions/delete|Ta bort befintliga OMS-lösning|
|/Solutions/Read|Hämta avslutas OMS-lösning|
|/solutions/write|Skapa ny OMS-lösning|

## <a name="microsoftportal"></a>Microsoft.Portal

| Åtgärd | Beskrivning |
|---|---|
|/Dashboards/delete|Tar bort instrumentpanelen från prenumerationen.|
|/dashboards/read|Läser in instrumentpanelerna för prenumerationen.|
|/dashboards/write|Lägg till eller ändra en instrumentpanel för en prenumeration.|

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

| Åtgärd | Beskrivning |
|---|---|
|/capacities/checkNameAvailability/action|Kontrollerar att namnet angivna Power BI-dedikerade kapacitet är giltigt och inte i användning.|
|/capacities/delete|Tar bort Powerbi reserverad kapacitet.|
|/capacities/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för Power BI dedikerade kapacitet.|
|/capacities/read|Hämtar information för den angivna Power BI dedikerade kapacitet.|
|/capacities/write|Skapar eller uppdaterar den angivna Power BI dedikerade kapacitet.|

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

| Åtgärd | Beskrivning |
|---|---|
|/locations/allocatedStamp/read|GetAllocatedStamp är en intern åtgärd som används av tjänsten|
|/locations/allocateStamp/action|AllocateStamp är intern åtgärd som används av tjänsten|
|/locations/backupPreValidateProtection/action||
|/locations/backupStatus/action|Kontrollera Status för säkerhetskopiering för Recovery Services-valv|
|/locations/backupValidateFeatures/action|Validera funktioner|
|/operations/read|Åtgärd returnerar en lista över åtgärder för en resurs-Provider|
|/register/action|Registrerar prenumerationen för angivna Resource Provider|
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
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/provisionInstantItemRecovery/action|Etablera omedelbar återställning för skyddade objekt|
|/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read|Hämta återställningspunkter för skyddade objekt.|
|/ Valv/backupFabrics/protectionContainers/protectedItems/recoveryPoints-restore-åtgärden|Återskapa återställningspunkter för skyddade objekt.|
|/Vaults/backupFabrics/protectionContainers/protectedItems/ recoveryPoints/revokeInstantItemRecovery/action|Återkalla omedelbar återställning för skyddade objekt|
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
|/Vaults/certificates/write|Uppdatera resurs certifikat åtgärden uppdaterar Autentiseringscertifikatet resurs-valvet.|
|/Vaults/delete|Ta bort valvet åtgärden tar bort angivna Azure-resurs av typen 'valvet'|
|/Vaults/extendedInformation/delete|Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv?|
|/Vaults/extendedInformation/read|Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv?|
|/Vaults/extendedInformation/write|Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv?|
|/Vaults/monitoringAlerts/read|Hämtar aviseringar för Recovery services-valvet.|
|/Vaults/monitoringAlerts/write|Löser du aviseringen.|
|/Vaults/monitoringConfigurations/read|Hämtar meddelandekonfigurationen Recovery services-valvet.|
|/Vaults/monitoringConfigurations/write|Konfigurerar e-postaviseringar till Recovery services-valvet.|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/read|Azure Backup-diagnostik|
|/Vaults/providers/Microsoft.Insights/diagnosticSettings/write|Azure Backup-diagnostik|
|/Vaults/providers/Microsoft.Insights/logDefinitions/read|Azure Backup-loggar|
|/Vaults/providers/Microsoft.Insights/metricDefinitions/read|Azure Backup mått|
|/Vaults/read|Åtgärden hämta valvet hämtar ett objekt som representerar Azure-resurs av typen 'valvet'|
|/Vaults/registeredIdentities/delete|Åtgärden för att avregistrera behållaren kan användas för att avregistrera en behållare.|
|/Vaults/registeredIdentities/operationResults/read|Hämta åtgärden resulterar åtgärden kan användas för hämta Åtgärdsstatus och resultat för asynkront skickats igen|
|/Vaults/registeredIdentities/read|Hämta behållarna åtgärden kan användas för hämta behållare som har registrerats för en resurs.|
|/Vaults/registeredIdentities/write|Registrera tjänstbehållaren åtgärden kan användas för att registrera en behållare med återställningstjänsten.|
|/vaults/replicationAlertSettings/read|Läsa alla aviseringsinställningar|
|/vaults/replicationAlertSettings/write|Skapa eller uppdatera alla aviseringsinställningar|
|/vaults/replicationEvents/read|Läsa alla händelser|
|/vaults/replicationFabrics/checkConsistency/action|Kontrollerar infrastrukturens enhetlighet|
|/vaults/replicationFabrics/delete|Ta bort alla Infrastrukturresurser|
|/vaults/replicationFabrics/deployProcessServerImage/action|Distribuera Processerveravbildning|
|/vaults/replicationFabrics/read|Läsa alla Infrastrukturresurser|
|/vaults/replicationFabrics/reassociateGateway/action|Skapa en ny koppling Gateway|
|/vaults/replicationFabrics/remove/action|Ta bort Fabric|
|/vaults/replicationFabrics/renewcertificate/action|Förnya certifikat för infrastruktur|
|/vaults/replicationFabrics/replicationNetworks/read|Läsa alla nätverk|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/delete|Ta bort alla nätverksmappningar|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read|Läsa alla nätverksmappningar|
|/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/write|Skapa eller uppdatera alla nätverksmappningar|
|/ valv/replicationFabrics/replicationProtectionContainers/discoverProtectableItem/åtgärd|Identifiera skyddsobjekt|
|/vaults/replicationFabrics/replicationProtectionContainers/read|Läsa skydd behållare|
|/vaults/replicationFabrics/replicationProtectionContainers/remove/action|Ta bort Skyddsbehållaren|
|/ replicationProtectionContainers-valv/replicationFabrics/replicationProtectableItems/läsning|Läsa alla objekt som kan skyddas|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/applyRecoveryPoint/action|Tillämpa återställningspunkt|
|/ valv/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/ta bort|Ta bort alla skyddade objekt|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/failoverCommit/action|Redundansåtgärd|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/plannedFailover/action|Planerad redundans|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/read|Läsa alla skyddade objekt|
|/ replicationProtectionContainers-valv/replicationFabrics/replicationProtectedItems/recoveryPoints/läsning|Läsa alla återställningspunkter för replikeringen|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/remove/action|Ta bort skyddade objekt|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/repairReplication/action|Reparera replikering|
|/ replicationProtectionContainers-valv/replicationFabrics/replicationProtectedItems/skyddar/åtgärd|Skapa nytt skyddat objekt|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/testFailover/action|Testa redundans|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/testFailoverCleanup/action|Redundanstestningen|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/unplannedFailover/action|Redundans|
|/vaults/replicationFabrics/replicationProtectionContainers/ replicationProtectedItems/updateMobilityService/action|Uppdatera Mobilitetstjänsten|
|/ valv/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/skrivning|Skapa eller uppdatera alla skyddade objekt|
|/ valv/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/ta bort|Ta bort alla mappningar för behållaren skydd|
|/ replicationProtectionContainers-valv/replicationFabrics/replicationProtectionContainerMappings/läsning|Läsa alla mappningar för behållaren skydd|
|/ replicationProtectionContainers-valv/replicationFabrics/replicationProtectionContainerMappings-remove-åtgärden|Ta bort skyddsbehållare|
|/ valv/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/skrivning|Skapa eller uppdatera alla mappningar för behållaren skydd|
|/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action|Växeln-Skyddsbehållaren|
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
|/vaults/replicationFabrics/write|Skapa eller uppdatera alla Infrastrukturresurser|
|/vaults/replicationJobs/cancel/action|Avbryta jobb|
|/vaults/replicationJobs/read|Läsa alla jobb|
|/vaults/replicationJobs/restart/action|Starta om jobbet|
|/vaults/replicationJobs/resume/action|Återuppta jobbet|
|/vaults/replicationPolicies/delete|Ta bort alla principer|
|/vaults/replicationPolicies/read|Läsa alla principer|
|/vaults/replicationPolicies/write|Skapa eller uppdatera alla principer|
|/vaults/replicationRecoveryPlans/delete|Ta bort alla Återställningsplaner|
|/vaults/replicationRecoveryPlans/failoverCommit/action|Redundansåtgärd återställningsplan|
|/vaults/replicationRecoveryPlans/plannedFailover/action|Planerad redundans återställningsplan|
|/vaults/replicationRecoveryPlans/read|Läsa alla Återställningsplaner|
|/vaults/replicationRecoveryPlans/reProtect/action|Skapa nytt återställningsplan|
|/vaults/replicationRecoveryPlans/testFailover/action|Testa redundans återställningsplan|
|/vaults/replicationRecoveryPlans/testFailoverCleanup/action|Testa redundans Rensa återställningsplan|
|/vaults/replicationRecoveryPlans/unplannedFailover/action|Plan för växling vid fel|
|/vaults/replicationRecoveryPlans/write|Skapa eller uppdatera alla Återställningsplaner|
|/Vaults/tokenInfo/read|Returnerar tokeninformation för Recovery Services-valvet.|
|/vaults/usages/read|Läsa alla valvet användningsområden|
|/Vaults/usages/read|Returnerar användningsinformation om Recovery Services-valvet.|
|/Vaults/vaultTokens/read|Token valvet igen kan användas för att hämta valv Token för valvet nivån backend-åtgärder.|
|/Vaults/write|Med skapa valv så skapas en Azure-resurs av typen valv|

## <a name="microsoftrelay"></a>Microsoft.Relay

| Åtgärd | Beskrivning |
|---|---|
|/checkNameAvailability/action|Kontrollerar tillgänglighet för namnområden i en viss prenumeration.|
|/checkNamespaceAvailability/action|Kontrollerar tillgänglighet för namnområden i en viss prenumeration. Detta API är inaktuell. Använd CheckNameAvailabiltiy i stället.|
|/namespaces/authorizationRules/action|Uppdateringar Namespace auktoriseringsregeln. Detta API är depricated. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln Namespace i stället... Den här åtgärden stöds inte på API-version 2017-04-01.|
|/namespaces/authorizationRules/delete|Ta bort Namespace auktoriseringsregeln. Auktoriseringsregeln standard Namespace kan inte tas bort. |
|/namespaces/authorizationRules/listkeys/action|Hämta anslutningssträngen till namnområdet|
|/namespaces/authorizationRules/read|Hämta listan över beskrivning av auktoriseringsregler för namnområden.|
|/namespaces/authorizationRules/regenerateKeys/action|Återskapa den primära eller sekundära nyckeln till resursen|
|/namespaces/authorizationRules/write|Skapa en nivå auktoriseringsregler Namespace och uppdatera dess egenskaper. Åtkomstbehörigheter för regler för auktorisering, primära och sekundärnycklar kan uppdateras.|
|/namespaces/Delete|Ta bort namnområdesresurs|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Hämtar tillståndet regler nycklar för katastrofåterställning primära namnområdet|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Hämta Disaster Recovery primära Namespaces auktoriseringsregler|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|Inaktiverar haveriberedskap och stoppar replikering av ändringar från primära till sekundära namnområden.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|Kontrollerar tillgängligheten för namnområdesalias under de angivna prenumerationen.|
|/namespaces/disasterRecoveryConfigs/delete|Tar bort Disaster Recovery-konfiguration som associeras med namnområdet. Den här åtgärden kan bara anropas via primära namnområdet.|
|/namespaces/disasterRecoveryConfigs/failover/action|Anropar GEO DR-redundans och omkonfigurerar namnområdets alias till att peka på det sekundära namnområdet.|
|/namespaces/disasterRecoveryConfigs/read|Hämtar den haveriberedskapskonfiguration som är kopplad till namnområdet.|
|/namespaces/disasterRecoveryConfigs/write|Skapar eller uppdaterar den haveriberedskapskonfiguration som är kopplad till namnområdet.|
|/namespaces/HybridConnections/authorizationRules/action|Åtgärden Uppdatera HybridConnection. Den här åtgärden stöds inte på API-version 2017-04-01. Auktoriseringsregler. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln.|
|/namespaces/HybridConnections/authorizationRules/delete|Åtgärden ta bort HybridConnection auktoriseringsregler|
|/namespaces/HybridConnections/authorizationRules/listkeys/action|Hämta anslutningssträngen till HybridConnection|
|/namespaces/HybridConnections/authorizationRules/read| Hämta en lista över HybridConnection auktoriseringsregler|
|/namespaces/HybridConnections/authorizationRules/regeneratekeys/action|Återskapa den primära eller sekundära nyckeln till resursen|
|/namespaces/HybridConnections/authorizationRules/write|Skapa auktoriseringsregler för HybridConnection och uppdatera dess egenskaper. Att det går uppdatera åtkomstbehörigheter för regler för auktorisering.|
|/namespaces/HybridConnections/Delete|Åtgärden ta bort HybridConnection resurs|
|/namespaces/HybridConnections/read|Hämta lista över HybridConnection resurs beskrivningar|
|/namespaces/HybridConnections/write|Skapa eller uppdatera HybridConnection egenskaper.|
|/namespaces/messagingPlan/read|Hämtar meddelanden planera för ett namnområde. Detta API är inaktuell. Egenskaper som exponeras via MessagingPlan resursen flyttas till (överordnad) Namespace resurs API senare... Den här åtgärden stöds inte på API-version 2017-04-01.|
|/namespaces/messagingPlan/write|Uppdaterar Messaging planera för ett namnområde. Detta API är inaktuell. Egenskaper som exponeras via MessagingPlan resursen flyttas till (överordnad) Namespace resurs API senare... Den här åtgärden stöds inte på API-version 2017-04-01.|
|/namespaces/operationresults/read|Hämta status för namnområdesåtgärd|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Hämta lista över Namespace mått resurs beskrivningar|
|/namespaces/read|Hämta listan över beskrivningar av namnområdesresurs|
|/namespaces/WcfRelays/authorizationRules/action|Åtgärden Uppdatera WcfRelay. Den här åtgärden stöds inte på API-version 2017-04-01. Auktoriseringsregler. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln.|
|/namespaces/WcfRelays/authorizationRules/delete|Åtgärden ta bort WcfRelay auktoriseringsregler|
|/namespaces/WcfRelays/authorizationRules/listkeys/action|Hämta anslutningssträngen till WcfRelay|
|/namespaces/WcfRelays/authorizationRules/read| Hämta en lista över WcfRelay auktoriseringsregler|
|/namespaces/WcfRelays/authorizationRules/regeneratekeys/action|Återskapa den primära eller sekundära nyckeln till resursen|
|/namespaces/WcfRelays/authorizationRules/write|Skapa auktoriseringsregler för WcfRelay och uppdatera dess egenskaper. Att det går uppdatera åtkomstbehörigheter för regler för auktorisering.|
|/namespaces/WcfRelays/Delete|Åtgärden ta bort WcfRelay resurs|
|/namespaces/WcfRelays/read|Hämta lista över WcfRelay resurs beskrivningar|
|/namespaces/WcfRelays/write|Skapa eller uppdatera WcfRelay egenskaper.|
|/namespaces/write|Skapa en resurs för Namespace och uppdatera dess egenskaper. Taggar och kapacitet för Namespace är egenskaper som kan uppdateras.|
|/operations/read|Hämta åtgärder|
|/register/action|Registrerar prenumerationen för Relay-resursprovidern och gör det möjligt att skapa Relay-resurser|
|/unregister/action|Registrerar prenumerationen för Relay-resursprovidern och gör det möjligt att skapa Relay-resurser|

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

| Åtgärd | Beskrivning |
|---|---|
|/AvailabilityStatuses/current/read|Hämtar tillgänglighetsstatusen för den angivna resursen|
|/AvailabilityStatuses/read|Hämtar tillgänglighetsstatusarna för alla resurser i det angivna området|
|/healthevent/action|Anger ändringen i hälsotillståndet för den angivna resursen|
|/healthevent/activated/Action|Anger ändringen i hälsotillståndet för den angivna resursen|
|/healthevent/InProgress/action|Anger ändringen i hälsotillståndet för den angivna resursen|
|/healthevent/Pending/action|Anger ändringen i hälsotillståndet för den angivna resursen|
|/healthevent/Resolved/action|Anger ändringen i hälsotillståndet för den angivna resursen|
|/healthevent/Updated/Action|Anger ändringen i hälsotillståndet för den angivna resursen|
|/register/action|Registrerar prenumerationen för Microsoft ResourceHealth|

## <a name="microsoftresources"></a>Microsoft.Resources

| Åtgärd | Beskrivning |
|---|---|
|/checkResourceName/action|Kontrollera resursnamnets giltighet.|
|/Deployments/Cancel/Action|Avbryter en distribution.|
|/Deployments/delete|Tar bort en distribution.|
|/deployments/operations/read|Hämtar eller listar distributionsåtgärder.|
|/deployments/read|Hämtar eller listar distributioner.|
|/deployments/validate/action|Verifierar en distribution.|
|/ distributioner/skrivning|Skapar eller uppdaterar en distribution.|
|/links/delete|Tar bort en resurslänk.|
|/links/read|Hämtar eller listar resurslänkar.|
|/links/write|Skapar eller uppdaterar en resurslänk.|
|/Marketplace/Purchase/Action|Köper en resurs på Marketplace.|
|/providers/read|Hämta listan över leverantörer.|
|/Resources/Read|Hämta listan över resurser baserade på filter.|
|/subscriptions/locations/read|Hämtar listan över platser som stöds.|
|/subscriptions/operationresults/read|Hämtar prenumerationsåtgärdsresultaten.|
|/subscriptions/providers/read|Hämtar eller listar resursprovidrar.|
|/subscriptions/read|Hämtar listan över prenumerationer.|
|/subscriptions/resourceGroups/delete|Tar bort resursgruppen och alla dess resurser.|
|/subscriptions/resourcegroups/deployments/operations/read|Hämtar eller listar distributionsåtgärder.|
|/subscriptions/resourcegroups/deployments/operationstatuses/read|Hämtar eller listar status för distributionsåtgärder.|
|/subscriptions/resourcegroups/deployments/read|Hämtar eller listar distributioner.|
|/subscriptions/resourcegroups/deployments/write|Skapar eller uppdaterar en distribution.|
|/subscriptions/resourceGroups/moveResources/action|Flyttar resurser från en resursgrupp till en annan.|
|/subscriptions/resourceGroups/read|Hämtar eller listar resursgrupper.|
|/subscriptions/resourcegroups/resources/read|Hämtar resurserna för resursgruppen.|
|/subscriptions/resourceGroups/validateMoveResources/action|Verifiera flytt av resurser från en resursgrupp till en annan.|
|/subscriptions/resourceGroups/write|Skapar eller uppdaterar en resursgrupp.|
|/subscriptions/Resources/Read|Hämtar en prenumerations resurser.|
|/subscriptions/tagNames/delete|Tar bort en prenumerationstagg.|
|/subscriptions/tagNames/read|Hämtar eller listar prenumerationstaggar.|
|/subscriptions/tagNames/tagValues/delete|Tar bort ett prenumerationstaggsvärde.|
|/subscriptions/tagNames/tagValues/read|Hämtar eller listar prenumerationstaggsvärden.|
|/subscriptions/tagNames/tagValues/write|Lägger till ett prenumerationstaggsvärde.|
|/subscriptions/tagNames/write|Lägger till en prenumerationstagg.|
|/tenants/read|Hämtar listan över klienter.|

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

| Åtgärd | Beskrivning |
|---|---|
|/jobcollections/delete|Tar bort en jobbsamling.|
|/jobcollections/disable/action|Inaktiverar en jobbsamling.|
|/jobcollections/enable/Action|Aktiverar en jobbsamling.|
|/jobcollections/jobs/delete|Tar bort ett jobb.|
|/jobcollections/jobs/generateLogicAppDefinition/action|Genererar en logikappsdefinition som baseras på ett Scheduler-jobb.|
|/jobcollections/jobs/jobhistories/read|Hämtar jobbhistorik.|
|/jobcollections/jobs/read|Hämtar jobb.|
|/jobcollections/jobs/run/action|Kör jobb.|
|/jobcollections/jobs/write|Skapar eller uppdaterar jobb.|
|/jobcollections/read|Hämta jobbsamling|
|/jobcollections/write|Skapar eller uppdaterar en jobbsamling.|

## <a name="microsoftsearch"></a>Microsoft.Search

| Åtgärd | Beskrivning |
|---|---|
|/checkNameAvailability/action|Kontrollerar tillgängligheten för tjänstnamnet.|
|/register/action|Registrerar prenumerationen för resursen sökleverantör och kan skapa search-tjänster.|
|/searchServices/createQueryKey/action|Skapar fråga för nyckeln.|
|/searchServices/delete|Tar bort söktjänsten.|
|/searchServices/diagnosticSettings/read|Hämtar diganostic inställningen skrivskyddade för resursen|
|/searchServices/diagnosticSettings/write|Skapar eller uppdaterar inställningen diganostic för resursen|
|/searchServices/listAdminKeys/action|Läser admin-nycklar.|
|/searchServices/logDefinitions/read|Hämtar de tillgängliga loggarna för söktjänsten|
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
|/alerts/read|Hämtar alla tillgängliga säkerhetsaviseringar|
|/applicationWhitelistings/read|Hämtar programmet whitelistings|
|/applicationWhitelistings/write|Skapar en ny vitlistning av program eller uppdaterar en befintlig|
|/complianceResults/read|Hämtar kompatibilitetsresultaten för resursen|
|/locations/alerts/activate/action|Aktivera en säkerhetsvarning|
|/locations/alerts/dismiss/action|Avvisa en säkerhetsavisering|
|/locations/alerts/read|Hämtar alla tillgängliga säkerhetsaviseringar|
|/locations/jitNetworkAccessPolicies/initiate/action|Initierar en åtkomstprincip för just-in-time-nätverk|
|/locations/jitNetworkAccessPolicies/read|Hämtar just-in-time-åtkomst nätverksprinciper|
|/locations/jitNetworkAccessPolicies/write|Skapar en ny åtkomstprincip för just-in-time-nätverk eller uppdaterar en befintlig|
|/locations/read|Hämtar Dataplats säkerhet|
|/locations/tasks/activate/action|Aktivera en säkerhetsrekommendation|
|/locations/tasks/dismiss/action|Stänga en säkerhetsrekommendation|
|/locations/tasks/read|Hämtar alla tillgängliga säkerhetsrekommendationer|
|/locations/tasks/resolve/action|Lösa en säkerhetsrekommendation|
|/Locations/Tasks/Start/Action|Starta en säkerhetsrekommendation|
|/policies/read|Hämtar säkerhetsprincipen|
|/policies/write|Uppdaterar säkerhetsprincipen|
|/pricings/delete|Tar bort prisnivå inställningar för scope|
|/pricings/read|Hämtar prisnivå inställningarna för scope|
|/pricings/write|Uppdaterar inställningarna för omfånget som prisnivå|
|/register/action|Registrerar prenumerationen för Azure Security Center|
|/securityContacts/delete|Tar bort kontakten säkerhet|
|/securityContacts/read|Hämtar säkerhet kontakt|
|/securityContacts/write|Uppdaterar säkerhet kontakt|
|/securitySolutions/delete|Tar bort en säkerhetslösning|
|/securitySolutions/read|Hämtar säkerhetslösningar|
|/securitySolutions/write|Skapar en ny lösning eller uppdaterar en befintlig|
|/securitySolutionsReferenceData/read|Hämtar säkerhetslösningar referensdata|
|/securityStatuses/read|Hämtar säkerheten hälsa status för Azure-resurser|
|/securityStatusesSummaries/read|Hämtar säkerheten statusar sammanfattningar för scope|
|/tasks/read|Hämtar alla tillgängliga säkerhetsrekommendationer|
|/webApplicationFirewalls/delete|Tar bort en brandvägg för webbaserade program|
|/webApplicationFirewalls/read|Hämtar webben programmet brandväggar|
|/webApplicationFirewalls/write|Skapar en ny Brandvägg för webbaserade program eller uppdaterar en befintlig|
|/workspaceSettings/connect/action|Ändra arbetsytan inställningar för återanslutning|
|/workspaceSettings/delete|Tar bort arbetsytan inställningar|
|/workspaceSettings/read|Hämtar inställningar för arbetsyta|
|/workspaceSettings/write|Arbetsytan inställningar för uppdateringar|

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

| Åtgärd | Beskrivning |
|---|---|
|/checkNameAvailability/action|Kontrollerar tillgänglighet för namnområden i en viss prenumeration.|
|/checkNamespaceAvailability/action|Kontrollerar tillgänglighet för namnområden i en viss prenumeration. Detta API är inaktuell. Använd CheckNameAvailabiltiy i stället.|
|/namespaces/authorizationRules/action|Uppdateringar Namespace auktoriseringsregeln. Detta API är depricated. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln Namespace i stället... Den här åtgärden stöds inte på API-version 2017-04-01.|
|/namespaces/authorizationRules/delete|Ta bort Namespace auktoriseringsregeln. Auktoriseringsregeln standard Namespace kan inte tas bort. |
|/namespaces/authorizationRules/listkeys/action|Hämta anslutningssträngen till namnområdet|
|/namespaces/authorizationRules/read|Hämta listan över beskrivning av auktoriseringsregler för namnområden.|
|/namespaces/authorizationRules/regenerateKeys/action|Återskapa den primära eller sekundära nyckeln till resursen|
|/namespaces/authorizationRules/write|Skapa en nivå auktoriseringsregler Namespace och uppdatera dess egenskaper. Åtkomstbehörigheter för regler för auktorisering, primära och sekundärnycklar kan uppdateras.|
|/namespaces/Delete|Ta bort namnområdesresurs|
|/namespaces/disasterRecoveryConfigs/authorizationRules/listkeys/action|Hämtar tillståndet regler nycklar för katastrofåterställning primära namnområdet|
|/namespaces/disasterRecoveryConfigs/authorizationRules/read|Hämta Disaster Recovery primära Namespaces auktoriseringsregler|
|/namespaces/disasterRecoveryConfigs/breakPairing/action|Inaktiverar haveriberedskap och stoppar replikering av ändringar från primära till sekundära namnområden.|
|/namespaces/disasterrecoveryconfigs/checkNameAvailability/action|Kontrollerar tillgängligheten för namnområdesalias under de angivna prenumerationen.|
|/namespaces/disasterRecoveryConfigs/delete|Tar bort Disaster Recovery-konfiguration som associeras med namnområdet. Den här åtgärden kan bara anropas via primära namnområdet.|
|/namespaces/disasterRecoveryConfigs/failover/action|Anropar GEO DR-redundans och omkonfigurerar namnområdets alias till att peka på det sekundära namnområdet.|
|/namespaces/disasterRecoveryConfigs/read|Hämtar den haveriberedskapskonfiguration som är kopplad till namnområdet.|
|/namespaces/disasterRecoveryConfigs/write|Skapar eller uppdaterar den haveriberedskapskonfiguration som är kopplad till namnområdet.|
|/namespaces/eventGridFilters/delete|Tar bort händelsen rutnätet filtret som associeras med namnområdet.|
|/namespaces/eventGridFilters/read|Hämtar händelsen rutnätet filtret som associeras med namnområdet.|
|/namespaces/eventGridFilters/write|Skapar eller uppdaterar händelse rutnätet filtret som associeras med namnområdet.|
|/namespaces/eventhubs/read|Hämta lista över EventHub resurs beskrivningar|
|/namespaces/messagingPlan/read|Hämtar meddelanden planera för ett namnområde. Detta API är inaktuell. Egenskaper som exponeras via MessagingPlan resursen flyttas till (överordnad) Namespace resurs API senare... Den här åtgärden stöds inte på API-version 2017-04-01.|
|/namespaces/messagingPlan/write|Uppdaterar Messaging planera för ett namnområde. Detta API är inaktuell. Egenskaper som exponeras via MessagingPlan resursen flyttas till (överordnad) Namespace resurs API senare... Den här åtgärden stöds inte på API-version 2017-04-01.|
|/Namespaces/migrate/Action|Migreringsåtgärd för namnområde|
|/namespaces/operationresults/read|Hämta status för namnområdesåtgärd|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/read|Hämta lista över Namespace diagnostikinställningar resurs beskrivningar|
|/namespaces/providers/Microsoft.Insights/diagnosticSettings/write|Hämta lista över Namespace diagnostikinställningar resurs beskrivningar|
|/namespaces/providers/Microsoft.Insights/logDefinitions/read|Hämta en lista över Namespace loggar resurs beskrivningar|
|/namespaces/providers/Microsoft.Insights/metricDefinitions/read|Hämta lista över Namespace mått resurs beskrivningar|
|/namespaces/queues/authorizationRules/action|Åtgärden uppdatera kön. Den här åtgärden stöds inte på API-version 2017-04-01. Auktoriseringsregler. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln.|
|/namespaces/queues/authorizationRules/delete|Åtgärden ta bort kön auktoriseringsregler|
|/namespaces/queues/authorizationRules/listkeys/action|Hämta anslutningssträngen till kön|
|/namespaces/queues/authorizationRules/read| Hämta en lista över auktoriseringsregler för kön|
|/namespaces/queues/authorizationRules/regenerateKeys/action|Återskapa den primära eller sekundära nyckeln till resursen|
|/namespaces/queues/authorizationRules/write|Skapa auktoriseringsregler för kön och uppdatera dess egenskaper. Att det går uppdatera åtkomstbehörigheter för regler för auktorisering.|
|/namespaces/queues/Delete|Åtgärden ta bort kön resurs|
|/namespaces/queues/read|Hämta lista över kön resurs beskrivningar|
|/namespaces/queues/write|Skapa eller uppdatera köegenskaper.|
|/namespaces/read|Hämta listan över beskrivningar av namnområdesresurs|
|/namespaces/topics/authorizationRules/action|Åtgärden Uppdatera avsnittet. Den här åtgärden stöds inte på API-version 2017-04-01. Auktoriseringsregler. Använd ett PUT-anrop för att uppdatera auktoriseringsregeln.|
|/namespaces/topics/authorizationRules/delete|Åtgärden ta bort avsnittet auktoriseringsregler|
|/namespaces/topics/authorizationRules/listkeys/action|Hämta anslutningssträngen till avsnittet|
|/namespaces/topics/authorizationRules/read| Hämta en lista över avsnittet auktoriseringsregler|
|/namespaces/topics/authorizationRules/regenerateKeys/action|Återskapa den primära eller sekundära nyckeln till resursen|
|/namespaces/topics/authorizationRules/write|Skapa auktoriseringsregler för avsnittet och uppdatera dess egenskaper. Att det går uppdatera åtkomstbehörigheter för regler för auktorisering.|
|/namespaces/topics/Delete|Åtgärden ta bort avsnittet resurs|
|/namespaces/topics/read|Hämta lista över avsnittet resurs beskrivningar|
|/namespaces/topics/subscriptions/Delete|Åtgärden ta bort TopicSubscription resurs|
|/namespaces/topics/subscriptions/read|Hämta lista över TopicSubscription resurs beskrivningar|
|/namespaces/topics/subscriptions/rules/Delete|Åtgärden ta bort regeln resurs|
|/namespaces/topics/subscriptions/rules/read|Hämta lista över regeln resurs beskrivningar|
|/namespaces/topics/subscriptions/rules/write|Skapa eller uppdatera regelegenskaper.|
|/namespaces/topics/subscriptions/write|Skapa eller uppdatera TopicSubscription egenskaper.|
|/namespaces/topics/write|Skapa eller uppdatera avsnittet Egenskaper.|
|/namespaces/write|Skapa en resurs för Namespace och uppdatera dess egenskaper. Taggar och kapacitet för Namespace är egenskaper som kan uppdateras.|
|/operations/read|Hämta åtgärder|
|/register/action|Registrerar prenumerationen för ServiceBus-resursprovidern och gör det möjligt att skapa ServiceBus-resurser|
|/sku/read|Hämta lista över Sku resurs beskrivningar|
|/sku/regions/read|Hämta lista över SkuRegions resurs beskrivningar|
|/unregister/action|Registrerar prenumerationen för ServiceBus-resursprovidern och gör det möjligt att skapa ServiceBus-resurser|

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

| Åtgärd | Beskrivning |
|---|---|
|/clusters/applications/delete|Ta bort valfritt program|
|/clusters/applications/read|Läs valfritt program|
|/clusters/applications/services/delete|Ta bort valfri tjänst|
|/clusters/applications/services/partitions/read|Läs valfri partition|
|/clusters/applications/services/partitions/replicas/read|Läs valfri replik|
|/clusters/applications/services/read|Läs valfri tjänst|
|/clusters/applications/services/statuses/read|Läs valfri tjänststatus|
|/clusters/applications/services/write|Skapa eller uppdatera valfri tjänst|
|/clusters/applications/write|Skapa eller uppdatera valfritt program|
|/clusters/applicationTypes/delete|Ta bort valfri programtyp|
|/clusters/applicationTypes/read|Läs valfri programtyp|
|/clusters/applicationTypes/versions/delete|Ta bort valfri version av programtyp|
|/clusters/applicationTypes/versions/read|Läs valfri version av programtyp|
|/clusters/applicationTypes/versions/write|Skapa eller uppdatera valfri version av programtyp|
|/clusters/applicationTypes/write|Skapa eller uppdatera valfri programtyp|
|/clusters/delete|Ta bort valfritt kluster|
|/clusters/nodes/read|Läs valfri nod|
|/clusters/read|Läs valfritt kluster|
|/clusters/statuses/read|Läs valfri klusterstatus|
|/clusters/write|Skapa eller uppdatera valfritt kluster|
|/locations/clusterVersions/read|Läs valfri klusterversion|
|/locations/environments/clusterVersions/read|Läs valfri klusterversion för en viss miljö|
|/locations/operationresults/read|Läs valfritt åtgärdsresultat|
|/locations/operations/read|Läs valfri åtgärd efter plats|
|/operations/read|Läs valfri tillgänglig åtgärd|
|/register/action|Registrera valfri åtgärd|

## <a name="microsoftsolutions"></a>Microsoft.Solutions

| Åtgärd | Beskrivning |
|---|---|
|/applicationDefinitions/delete|Tar bort en programdefinition.|
|/applicationDefinitions/read|Hämtar en lista över programdefinitioner.|
|/applicationDefinitions/write|Lägg till eller ändra en programdefinition.|
|/applications/delete|Tar bort ett program.|
|/applications/read|Hämtar en lista över program.|
|/applications/write|Skapar ett program.|
|/locations/operationStatuses/read|Läser resursens åtgärdsstatus.|
|/register/action|Registrera dig för lösningar.|

## <a name="microsoftsql"></a>Microsoft.Sql

| Åtgärd | Beskrivning |
|---|---|
|/checkNameAvailability/action|Kontrollera den angivna servern namn är tillgängligt för att etablera över hela världen för en viss prenumeration.|
|/locations/auditingSettingsAzureAsyncOperation/read|Hämta resultatet av den utökade server blob granskning princip Set-åtgärd|
|/locations/auditingSettingsOperationResults/read|Hämta resultatet av server-blob granskning princip Set-åtgärd|
|/locations/capabilities/read|Hämtar funktionerna för den här prenumerationen i en viss plats|
|/locations/databaseAzureAsyncOperation/read|Hämtar status för en databasåtgärd.|
|/locations/databaseOperationResults/read|Hämtar status för en databasåtgärd.|
|/locations/deletedServerAsyncOperation/read|Hämtar pågående åtgärder på servern som tagits bort|
|/locations/deletedServerOperationResults/read|Hämtar pågående åtgärder på servern som tagits bort|
|/locations/deletedServers/read|Returnera listan över borttagna servrar eller hämtar egenskaperna för den angivna servern som tagits bort.|
|/locations/deletedServers/recover/action|Återställa en borttagen server|
|/locations/deleteVirtualNetworkOrSubnets/action|Tar bort regler för virtuella nätverk som är kopplat till ett virtuellt nätverk eller undernät|
|/locations/elasticPoolAzureAsyncOperation/read|Hämtar azure async-åtgärden för en asynkron åtgärd elastisk pool|
|/locations/elasticPoolOperationResults/read|Hämtar resultatet av en elastisk pool.|
|/locations/extendedAuditingSettingsAzureAsyncOperation/read|Hämta resultatet av den utökade server blob granskning princip Set-åtgärd|
|/locations/extendedAuditingSettingsOperationResults/read|Hämta resultatet av den utökade server blob granskning princip Set-åtgärd|
|/locations/managedDatabaseRestoreAzureAsyncOperation/completeRestore/action|Slutför hanterade databasåterställning|
|/locations/managedTransparentDataEncryptionAzureAsyncOperation/read|Hämtar pågående åtgärder på hanterade databasen transparent datakryptering|
|/locations/managedTransparentDataEncryptionOperationResults/read|Hämtar pågående åtgärder på hanterade databasen transparent datakryptering|
|/locations/read|Hämtar tillgängliga platser för en viss prenumeration|
|/locations/syncAgentOperationResults/read|Hämta resultatet av synkroniseringsåtgärd agent resurs|
|/locations/syncDatabaseIds/read|Hämta ID: n för sync-databasen för en viss region och en prenumeration|
|/locations/syncGroupOperationResults/read|Hämta resultatet av åtgärden för synkronisering av resursen|
|/locations/syncMemberOperationResults/read|Hämta resultatet av synkroniseringsåtgärd medlem resurs|
|/locations/usages/read|Hämtar en samling av användningsstatistik för den här prenumerationen på en plats|
|/locations/virtualNetworkRulesAzureAsyncOperation/read|Returnerar information om de angivna virtuella nätverk reglerna azure asynkron åtgärd |
|/locations/virtualNetworkRulesOperationResults/read|Returnerar information om den angivna virtuella nätverket regler åtgärden |
|/managedInstances/administrators/delete|Tar bort en befintlig administratör för hanterade instansen.|
|/managedInstances/administrators/read|Hämtar en lista över hanterade instans administratörer.|
|/managedInstances/administrators/write|Skapar eller uppdaterar hanterade instans administratör med de angivna parametrarna.|
|/managedInstances/databases/delete|Tar bort en befintlig hanterad databas|
|/managedInstances/databases/read|Hämtar befintliga hanterad databas|
|/managedInstances/databases/securityAlertPolicies/read|Hämta information om databasen threat detection principen konfigurerats på en viss hanterad databas|
|/managedInstances/databases/securityAlertPolicies/write|Ändra databasen threat detection principen för en viss hanterad databas|
|/managedInstances/databases/securityEvents/read|Hämtar säkerhetshändelser hanterad databas|
|/managedInstances/databases/transparentDataEncryption/read|Hämta information om databasen Transparent datakryptering på en viss hanterad databas|
|/managedInstances/databases/transparentDataEncryption/write|Ändra databasen Transparent datakryptering för en viss hanterad databas|
|/managedInstances/databases/write|Skapar en ny databas eller uppdaterar en befintlig databas.|
|/managedInstances/delete|Tar bort en befintlig hanterade instans.|
|/managedInstances/metricDefinitions/read|Hämta måttdefinitionerna för hanterade instans|
|/managedInstances/metrics/read|Hämta hanterade instans mätvärden|
|/managedInstances/read|Returnera listan över hanterade instanser eller hämtar egenskaperna för den angivna hantera instansen.|
|/managedInstances/securityAlertPolicies/read|Hämta information om hanterad server threat detection principen på en viss hanterad server|
|/managedInstances/securityAlertPolicies/write|Ändra hanterad server threat detection principen för en viss hanterad server|
|/managedInstances/write|Skapar en hanterad instans med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna hantera instansen.|
|/operations/read|Hämtar tillgängliga REST-åtgärder|
|/register/action|Registrerar prenumerationen för resursprovidern Microsoft SQL-databasen och kan skapa Microsoft SQL-databaser.|
|/servers/administratorOperationResults/read|Hämtar pågående åtgärder på server-administratörer|
|/servers/administrators/delete|Ta bort serveradministratör|
|/Servers/Administrators/Read|Hämta serverinformation för administratör|
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
|/servers/databases/advisors/read|Returnerar en lista över rådgivare som är tillgängliga för databasen|
|/servers/databases/advisors/recommendedActions/read|Returnerar en lista över rekommenderade åtgärder för angivna advisor för databasen|
|/servers/databases/advisors/recommendedActions/write|Tillämpa den rekommenderade åtgärden för databasen|
|/servers/databases/advisors/write|Uppdateringen automatiskt-köra status för en advisor på databasnivå.|
|/servers/databases/auditingPolicies/read|Hämta information om tabellen granskningsprincip konfigureras på en viss databas|
|/servers/databases/auditingPolicies/write|Ändra tabellen granskningsprincipen för en viss databas|
|/servers/databases/auditingSettings/read|Hämta information om blob-granskningsprincip som konfigurerats på en viss databas|
|/servers/databases/auditingSettings/write|Ändra granskningsprincipen blob för en viss databas|
|/servers/databases/auditRecords/read|Hämta databasen blob granskningsposter|
|/servers/databases/automaticTuning/read|Returnerar automatisk justeringsinställningar för en databas|
|/servers/databases/automaticTuning/write|Uppdaterar inställningar för automatisk justering för en databas och returnerar uppdaterade inställningarna|
|/servers/databases/azureAsyncOperation/read|Hämtar status för en databasåtgärd.|
|/servers/databases/backupLongTermRetentionPolicies/read|Returnera listan över säkerhetskopieringsprinciper för arkivering av en angiven databas.|
|/servers/databases/backupLongTermRetentionPolicies/write|Skapa eller uppdatera en databas arkivering säkerhetskopieringsprincip.|
|/servers/databases/connectionPolicies/read|Hämta information om principen konfigurerats på en viss databas|
|/servers/databases/connectionPolicies/write|Ändra princip för en viss databas|
|/servers/databases/dataMaskingPolicies/read|Returnera listan över databasdata maskering av principer.|
|/servers/databases/dataMaskingPolicies/rules/delete|Ta bort datamaskning principregel för en viss databas|
|/servers/databases/dataMaskingPolicies/rules/read|Hämta information om datamaskning principregeln som konfigurerats på en viss databas|
|/servers/databases/dataMaskingPolicies/rules/write|Ändra datamaskning principregel för en viss databas|
|/servers/databases/dataMaskingPolicies/write|Ändra datamaskning princip för en viss databas|
|/servers/databases/dataWarehouseQueries/dataWarehouseQuerySteps/read|Returnerar den distribuerade fråga steg informationen av data warehouse fråga för valda steget-ID|
|/servers/databases/dataWarehouseQueries/read|Returnerar data warehouse distribution läsa information för valda fråge-ID|
|/servers/databases/dataWarehouseUserActivities/read|Hämtar användaraktiviteter på en instans av SQL Data Warehouse som innehåller körs och avbrutna frågor|
|/servers/databases/delete|Tar bort en befintlig databas.|
|/servers/databases/export/action|Exportera Azure SQL-databas|
|/servers/databases/extendedAuditingSettings/read|Hämta information om den utökade blobben granskningsprincip som konfigurerats på en viss databas|
|/servers/databases/extendedAuditingSettings/write|Ändra den utökade blobben granskningsprincipen för en viss databas|
|/servers/databases/extensions/read|Hämtar en uppsättning tillägg för databasen.|
|/servers/databases/extensions/write|Ändra filnamnstillägget för en viss databas|
|/servers/databases/geoBackupPolicies/read|Hämta geo principer för säkerhetskopiering för en viss databas|
|/servers/databases/geoBackupPolicies/write|Skapa eller uppdatera en databas geobackup princip|
|/servers/databases/importExportOperationResults/read|Hämtar pågående import/export-åtgärder|
|/servers/databases/metricDefinitions/read|Returnera typer av mätvärden som är tillgängliga för databaser|
|/servers/databases/metrics/read|Returnera mätvärden för databaser|
|/servers/databases/move/action|Byt namn på Azure SQL-databas|
|/servers/databases/operationResults/read|Hämtar status för en databasåtgärd.|
|/Servers/Databases/Operations/Cancel/Action|Avbryter Azure SQL Database väntande asynkron åtgärd inte har slutförts ännu.|
|/servers/databases/operations/read|Returnera en lista över åtgärder som utförs på databasen|
|/servers/databases/pause/action|Pausa Azure SQL Datawarehouse-databas|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/servers/databases/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/servers/databases/providers/Microsoft.Insights/logDefinitions/read|Hämtar tillgängliga loggar för databaser|
|/servers/databases/providers/Microsoft.Insights/metricDefinitions/read|Returnera typer av mätvärden som är tillgängliga för databaser|
|/servers/databases/queryStore/queryTexts/read|Returnerar frågan texter som är kopplade till de angivna parametrarna.|
|/servers/databases/queryStore/read|Returnerar aktuella värden för Query Store inställningar för databasen.|
|/servers/databases/queryStore/write|Uppdaterar inställningen för Frågearkivet för databasen|
|/servers/databases/read|Returnera listan över databaser eller hämtar egenskaperna för den angivna databasen.|
|/servers/databases/replicationLinks/delete|Avsluta replikeringsrelationen tvång och potentiell dataförlust|
|/servers/databases/replicationLinks/failover/action|Växling vid fel efter synkronisering alla ändras från primärt gör den här databasen till replikering relationship\u0027s primära och göra fjärransluten primär till en sekundär|
|/servers/databases/replicationLinks/forceFailoverAllowDataLoss/action|Redundans omedelbart med potentiell dataförlust, vilket gör den här databasen till replikering relationship\u0027s primära och göra fjärransluten primär till en sekundär|
|/servers/databases/replicationLinks/read|Returnerar information om replikeringslänkar som har upprättats för en viss databas|
|/servers/databases/replicationLinks/unlink/action|Avsluta replikeringsrelationen tvång eller efter synkronisering med partnern|
|/servers/databases/replicationLinks/updateReplicationMode/action|Uppdatera replikeringsläge för länken till synkron eller asynkron läge|
|/servers/databases/restorePoints/action|Skapar en ny återställningspunkt|
|/servers/databases/restorePoints/read|Returnerar återställningspunkter för databasen.|
|/servers/databases/resume/action|Återuppta Azure SQL Datawarehouse-databas|
|/servers/databases/schemas/read|Hämta listan över scheman för en databas|
|/servers/databases/schemas/tables/columns/read|Hämta listan över kolumner i en tabell|
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
|/servers/databases/topQueries/queryText/action|Returnerar Transact-SQL-text för valda fråge-ID|
|/servers/databases/topQueries/read|Returnerar samman körningsstatistik för den valda frågan i vald tidsperiod|
|/servers/databases/topQueries/statistics/read|Returnerar samman körningsstatistik för den valda frågan i vald tidsperiod|
|/servers/databases/transparentDataEncryption/operationResults/read|Hämtar pågående åtgärder på transparent datakryptering|
|/servers/databases/transparentDataEncryption/read|Hämta status och information om transparent data kryptering säkerhetsfunktion för en viss databas|
|/servers/databases/transparentDataEncryption/write|Ändra krypteringsstatus för transparent data|
|/servers/databases/upgradeDataWarehouse/action|Uppgradera Azure SQL Datawarehouse-databas|
|/servers/databases/usages/read|Hämtar information för Azure SQL Database användningsområden|
|/servers/databases/vulnerabilityAssessments/delete|Ta bort vulnerability assessment för en viss databas|
|/servers/databases/vulnerabilityAssessments/read|Hämta information om utvärdering säkerhetsrisker som konfigurerats på en viss databas|
|/servers/databases/vulnerabilityAssessments/rules/baselines/delete|Ta bort vulnerability assessment regeln baslinjen för en viss databas|
|/servers/databases/vulnerabilityAssessments/rules/baselines/read|Hämta vulnerability assessment regeln baslinje för en viss databas|
|/servers/databases/vulnerabilityAssessments/rules/baselines/write|Ändra vulnerability assessment regeln baslinjen för en viss databas|
|/servers/databases/vulnerabilityAssessments/scans/action|Köra säkerhetsproblem genomsökning för utvärdering av databasen.|
|/servers/databases/vulnerabilityAssessments/scans/export/action|Konvertera ett befintligt genomsökning resultat till ett format. Om det finns inget redan händer|
|/servers/databases/vulnerabilityAssessments/scans/read|Returnerar listan över databasen vulnerability assessment genomsökning poster eller hämta genomsökning posten för den angivna sökning-ID.|
|/servers/databases/vulnerabilityAssessments/write|Ändra vulnerability assessment för en viss databas|
|/servers/databases/vulnerabilityAssessmentScans/action|Köra säkerhetsproblem genomsökning för utvärdering av databasen.|
|/servers/databases/vulnerabilityAssessmentScans/operationResults/read|Hämta resultatet av databasen vulnerability assessment genomsökning Kör åtgärden|
|/servers/databases/vulnerabilityAssessmentSettings/read|Hämta information om utvärdering säkerhetsrisker som konfigurerats på en viss databas|
|/servers/databases/vulnerabilityAssessmentSettings/write|Ändra vulnerability assessment för en viss databas|
|/servers/databases/write|Skapar en databas med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna databasen.|
|/servers/delete|Tar bort en befintlig server.|
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
|/servers/firewallRules/delete|Tar bort en befintlig brandväggsregel för servern.|
|/servers/firewallRules/read|Returnera listan över serverbrandvägg regler eller hämtar egenskaperna för den angivna servern brandväggsregel.|
|/servers/firewallRules/write|Skapar en brandväggsregel för servern med de angivna parametrarna uppdatera egenskaperna för den angivna regeln eller skriva över alla befintliga regler med nya regler för server-brandväggen.|
|/servers/import/action|Skapa en ny databas på servern och distribuera schema och data från ett DacPac-paket|
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
|/servers/serviceObjectives/read|Hämta lista över servicenivåmål (även kallat prestandanivåer) finns på en viss server|
|/servers/syncAgents/delete|Tar bort en befintlig sync-agent.|
|/servers/syncAgents/generateKey/action|Generera sync agent registrering nyckel|
|/servers/syncAgents/linkedDatabases/read|Returnera listan över synkronisera agent länkade databaser|
|/servers/syncAgents/read|Returnera listan över sync agenter eller hämtar egenskaperna för den angivna sync-agenten.|
|/servers/syncAgents/write|Skapar en agent för synkronisering med de angivna parametrarna eller uppdaterar egenskaperna för den angivna sync-agenten.|
|/servers/usages/read|Returnera DTU-kvot för server och den aktuella DTU consuption alla databaser på servern|
|/servers/virtualNetworkRules/delete|Tar bort en befintlig regel för virtuella nätverk|
|/servers/virtualNetworkRules/read|Returnera listan över virtuella nätverk regler eller hämtar egenskaperna för den angivna virtuella nätverk regeln.|
|/servers/virtualNetworkRules/write|Skapar ett virtuellt nätverk med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna virtuella nätverk regeln.|
|/servers/write|Skapar en server med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna för den angivna servern.|
|/unregister/action|Avregistrerar prenumerationen för resursprovidern Microsoft SQL-databasen och kan skapa Microsoft SQL-databaser.|
|/virtualClusters/read|Returnera listan med virtuella kluster eller hämtar egenskaperna för det angivna virtuella klustret.|
|/virtualClusters/write|Uppdaterar virtuellt kluster taggar.|

## <a name="microsoftstorage"></a>Microsoft.Storage

| Åtgärd | Beskrivning |
|---|---|
|/checknameavailability/read|Kontrollerar att kontonamnet är giltigt och används.|
|/locations/deleteVirtualNetworkOrSubnets/action|Aviserar Microsoft.Storage om att det virtuella nätverket eller undernätet tas bort|
|/operations/read|Avsöker status för en asynkron åtgärd.|
|/register/action|Registrerar prenumerationen för lagringsresursprovidern och gör det möjligt att skapa lagringskonton.|
|/skus/read|Listar SKU:erna som stöds av Microsoft.Storage.|
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
|/storageAccounts/read|Returnerar listan med lagringskonton eller hämtar egenskaperna för det angivna lagringskontot.|
|/storageAccounts/regeneratekey/action|Återskapar åtkomstnycklarna för det angivna lagringskontot.|
|/storageAccounts/services/diagnosticSettings/write|Skapa/uppdatera diagnostikinställningar för lagringskontot.|
|/ storageAccounts/storageAccounts/queueServices/providers/Microsoft.Insights/diagnosticSettings/read|Hämta diagnostikinställningen för resursen.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/read|Hämta diagnostikinställningen för resursen.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar diagnostikinställningen för resursen.|
|/storageAccounts/tableServices/providers/Microsoft.Insights/metricDefinitions/read|Hämta lista över Microsofts lagringsmåttdefinitioner.|
|/storageAccounts/write|Skapar ett lagringskonto med de angivna parametrarna eller uppdaterar egenskaperna eller taggarna, eller lägger till anpassad domän för det angivna lagringskontot.|
|/usages/read|Returnerar gränsen och det aktuella antalet användningar för resurser i den angivna prenumerationen|

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
|/managers/alerts/read|Visar eller hämtar aviseringar|
|/managers/bandwidthSettings/delete|Tar bort en befintlig bandbreddsinställningar (endast 8000-serien)|
|/managers/bandwidthSettings/read|Visa en lista med bandbreddsinställningarna (endast 8000-serien)|
|/managers/bandwidthSettings/write|Skapar en ny eller uppdaterar bandbreddsinställningar (endast 8000-serien)|
|/Managers/certificates/write|Uppdatera resurs certifikat åtgärden uppdaterar Autentiseringscertifikatet resurs-valvet.|
|/managers/clearAlerts/action|Avmarkera alla aviseringar som är associerade med Enhetshanteraren.|
|/managers/cloudApplianceConfigurations/read|Lista molntjänster anordningen konfigurationer som stöds|
|/managers/configureDevice/action|Konfigurerar en enhet|
|/managers/delete|Tar bort enheten chefer|
|/Managers/delete|Ta bort valvet åtgärden tar bort angivna Azure-resurs av typen 'valvet'|
|/managers/devices/alertSettings/read|Visar eller hämtar de aviseringsinställningar|
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
|/Managers/Devices/delete|Tar bort enheter|
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
|/Managers/Devices/jobs/Cancel/Action|Avbryta ett jobb som körs|
|/managers/devices/jobs/read|Visar eller hämtar jobb|
|/managers/devices/listFailoverSets/action|Lista för växling vid fel anger för en befintlig enhet.|
|/managers/devices/listFailoverTargets/action|Lista redundansmål enheter|
|/managers/devices/metrics/read|Visar eller hämtar mätvärden|
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
|/managers/devices/read|Hämtar enheterna eller visar|
|/managers/devices/scanForUpdates/action|Sök efter uppdateringar i en enhet.|
|/managers/devices/securitySettings/read|Visa en lista med säkerhetsinställningar|
|/managers/devices/securitySettings/syncRemoteManagementCertificate/action|Synkronisera certifikat för fjärrhantering för en enhet.|
|/managers/devices/securitySettings/update/action|Uppdatera säkerhetsinställningar.|
|/managers/devices/securitySettings/write|Skapar en ny eller uppdaterar säkerhetsinställningar|
|/managers/devices/sendTestAlertEmail/action|Skicka avisering testmeddelandet till konfigurerade e-postmottagare.|
|/managers/devices/timeSettings/read|Visar eller hämtar inställningarna för tid|
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
|/managers/devices/write|Skapa eller uppdatera enheterna|
|/managers/encryptionSettings/read|Visar eller hämtar krypteringsinställningar|
|/Managers/extendedInformation/delete|Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv?|
|/Managers/extendedInformation/read|Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv?|
|/Managers/extendedInformation/write|Med Hämta utökad information hämtas utökad information för ett objekt som representerar Azure-resursen av typen ?valv?|
|/managers/getActivationKey/action|Hämta aktiveringsnyckeln för Enhetshanteraren.|
|/managers/getEncryptionKey/action|Hämta krypteringsnyckeln för Enhetshanteraren.|
|/managers/listActivationKey/action|Hämtar aktiveringsnyckeln av StorSimple Enhetshanteraren.|
|/managers/listPrivateEncryptionKey/action|Hämtar privata krypteringsnyckel för en StorSimple Device Manager.|
|/managers/listPublicEncryptionKey/action|Lista över offentliga krypteringsnycklar en StorSimple Enhetshanteraren.|
|/managers/metrics/read|Visar eller hämtar mätvärden|
|/managers/metricsDefinitions/read|Visar eller hämtar mått definitioner|
|/managers/provisionCloudAppliance/action|Skapa en ny installation av molnet.|
|/managers/read|Visar eller hämtar enheten chefer|
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
|/managers/write|Skapa eller uppdatera enheten chefer|
|/Managers/write|Med skapa valv så skapas en Azure-resurs av typen valv|

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

| Åtgärd | Beskrivning |
|---|---|
|/locations/quotas/Read|Läs Stream Analytics prenumerationens kvoter|
|/ operations/Läs|Läs Stream Analytics-åtgärder|
|/Register/action|Registrera prenumerationen med Stream Analytics-Resursprovidern|
|/streamingjobs/Delete|Ta bort Stream Analytics-jobbet|
|/streamingjobs/functions/Delete|Ta bort Stream Analytics-jobbfunktionen|
|/streamingjobs/functions/operationresults/Read|Läs Åtgärdsresultat för Stream Analytics-jobbfunktion|
|/streamingjobs/functions/Read|Läs Stream Analytics-jobbfunktion|
|/streamingjobs/functions/RetrieveDefaultDefinition/action|Hämta standarddefinitionen för en funktion i Stream Analytics-jobbet|
|/streamingjobs/functions/Test/action|Stream Analytics-jobbet funktionsnamnet|
|/streamingjobs/functions/Write|Skriva Stream Analytics-jobbfunktion|
|/streamingjobs/inputs/Delete|Ta bort Stream Analytics-jobbet indata|
|/streamingjobs/inputs/operationresults/Read|Läs Åtgärdsresultat för Stream Analytics-jobbet indata|
|/streamingjobs/inputs/Read|Läs Stream Analytics-jobbet indata|
|/streamingjobs/inputs/Sample/action|Stream Analytics-jobbet Exempelindata|
|/streamingjobs/inputs/Test/action|Testa Stream Analytics-jobbet indata|
|/streamingjobs/inputs/Write|Skriva Stream Analytics-jobbet indata|
|/streamingjobs/metricdefinitions/Read|Läs Måttdefinitionerna|
|/streamingjobs/operationresults/Read|Läs Åtgärdsresultat för Stream Analytics-jobbet|
|/streamingjobs/outputs/Delete|Ta bort Stream Analytics-Jobbutdata|
|/streamingjobs/outputs/operationresults/Read|Läs Åtgärdsresultat för Stream Analytics-Jobbutdata|
|/streamingjobs/outputs/Read|Läs Stream Analytics-Jobbutdata|
|/streamingjobs/outputs/Test/action|Testa Stream Analytics-Jobbutdata|
|/streamingjobs/outputs/Write|Skriva Stream Analytics-Jobbutdata|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/read|Läsa diagnostikinställningen.|
|/streamingjobs/providers/Microsoft.Insights/diagnosticSettings/write|Skriva diagnostikinställningen.|
|/streamingjobs/providers/Microsoft.Insights/logDefinitions/read|Hämtar de tillgängliga loggarna för streamingjobs|
|/streamingjobs/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för streamingjobs|
|/streamingjobs/Read|Läs Stream Analytics-jobbet|
|/streamingjobs/Start/Action|Starta Stream Analytics-jobbet|
|/streamingjobs/stop/Action|Stoppa Stream Analytics-jobbet|
|/streamingjobs/transformations/Delete|Ta bort Stream Analytics-jobbet omvandling|
|/streamingjobs/transformations/Read|Läs Stream Analytics-jobbet omvandling|
|/streamingjobs/transformations/Write|Skriva Stream Analytics-jobbet omvandling|
|/streamingjobs/Write|Skriva Stream Analytics-jobbet|

## <a name="microsoftsubscription"></a>Microsoft.Subscription

| Åtgärd | Beskrivning |
|---|---|
|/SubscriptionDefinitions/read|Hämta en Azure-Prenumerationsdefinitionen i en hanteringsgrupp.|
|/SubscriptionDefinitions/write|Skapa en definition för Azure-prenumeration|

## <a name="microsoftsupport"></a>Microsoft.Support

| Åtgärd | Beskrivning |
|---|---|
|/register/action|Registrerar till supportresursprovidern|
|/supportTickets/read|Hämtar information om supportärendet (inklusive status, allvarlighetsgrad, kontaktinformation och kommunikation) eller hämtar listan över supportärenden för alla prenumerationer.|
|/supportTickets/write|Skapar eller uppdaterar ett supportärende. Du kan skapa ett supportärende för tekniska, fakturering, kvoter eller prenumerationshantering relaterade problem. Du kan uppdatera allvarlighetsgrad, kontaktinformation och kommunikation för befintliga supportärenden.|

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

| Åtgärd | Beskrivning |
|---|---|
|/environments/accesspolicies/delete|Tar bort åtkomstprincipen.|
|/environments/accesspolicies/read|Hämta egenskaperna för en åtkomstprincip.|
|/environments/accesspolicies/write|Skapar en ny åtkomstprincip för en miljö eller uppdaterar en befintlig åtkomstprincip.|
|/environments/delete|Tar bort miljön.|
|/environments/eventsources/delete|Tar bort händelsekällan.|
|/Environments/eventsources/eventsources/providers/Microsoft.Insights/ diagnosticSettings/skrivning|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/environments/eventsources/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/environments/eventsources/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för eventsources|
|/Environments/eventsources/Read|Hämta egenskaperna för en händelsekälla.|
|/Environments/eventsources/Write|Skapar en ny händelsekälla för en miljö eller uppdaterar en befintlig händelsekälla.|
|/environments/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/environments/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/environments/providers/Microsoft.Insights/metricDefinitions/read|Hämtar tillgängliga mått för miljöer|
|/environments/read|Hämta egenskaperna för en miljö.|
|/environments/referencedatasets/delete|Tar bort datamängden som referens.|
|/environments/referencedatasets/read|Hämta egenskaperna för en datauppsättning för referens.|
|/environments/referencedatasets/write|Skapar en ny referens datauppsättning för en miljö eller uppdaterar en befintlig datamängd referens.|
|/Environments/status/Read|Hämta status för miljön, tillståndet för dess associerade åtgärder som ingång.|
|/environments/write|Skapar en ny miljö eller uppdaterar en befintlig miljö.|
|/register/action|Registrerar prenumerationen för resursprovidern tid serien insikter och kan skapa tid serien insikter miljöer.|

## <a name="microsoftweb"></a>microsoft.web

| Åtgärd | Beskrivning |
|---|---|
|/apimanagementaccounts/apiacls/read|Hämta Api Management konton Apiacls.|
|/apimanagementaccounts/apis/apiacls/delete|Ta bort Api Management konton API: er Apiacls.|
|/apimanagementaccounts/apis/apiacls/read|Hämta Api Management konton API: er Apiacls.|
|/apimanagementaccounts/apis/apiacls/write|Uppdatera Apiacls för Api Management konton API: er.|
|/apimanagementaccounts/apis/connectionacls/read|Hämta Api Management konton API: er Connectionacls.|
|/apimanagementaccounts/apis/connections/confirmconsentcode/action|Bekräfta ditt medgivande kod Api Management konton API: er anslutningar.|
|/apimanagementaccounts/apis/connections/connectionacls/delete|Ta bort Api Management konton API: er anslutningar Connectionacls.|
|/apimanagementaccounts/apis/connections/connectionacls/read|Hämta Api Management konton API: er anslutningar Connectionacls.|
|/apimanagementaccounts/apis/connections/connectionacls/write|Uppdatera Api Management konton API: er anslutningar Connectionacls.|
|/apimanagementaccounts/apis/connections/delete|Ta bort Api Management konton API: er anslutningar.|
|/apimanagementaccounts/apis/connections/getconsentlinks/action|Få godkännande länkar för Api Management konton API: er anslutningar.|
|/apimanagementaccounts/apis/connections/listconnectionkeys/action|Lista över anslutning nycklar Api Management konton API: er anslutningar.|
|/apimanagementaccounts/apis/connections/listsecrets/action|Lista hemligheter Api Management konton API: er anslutningar.|
|/apimanagementaccounts/apis/connections/read|Hämta Api Management konton API: er anslutningar.|
|/apimanagementaccounts/apis/connections/write|Uppdatera Api Management konton API: er anslutningar.|
|/apimanagementaccounts/apis/delete|Ta bort API: er för Api Management-konton.|
|/apimanagementaccounts/apis/localizeddefinitions/delete|Ta bort Api Management API: er för konton lokaliserade definitioner.|
|/apimanagementaccounts/apis/localizeddefinitions/read|Hämta Api-hantering konton API: er lokaliserade definitioner.|
|/apimanagementaccounts/apis/localizeddefinitions/write|Uppdatera Api Management konton API: er lokaliserade definitioner.|
|/apimanagementaccounts/apis/read|Hämta API: er för Api Management-konton.|
|/apimanagementaccounts/apis/write|Uppdatera API: er för Api Management-konton.|
|/apimanagementaccounts/connectionacls/read|Hämta Api Management konton Connectionacls.|
|/availablestacks/read|Hämta tillgängliga grupper.|
|/billingmeters/read|Hämta lista över fakturering mätare.|
|/certificates/Delete|Ta bort ett befintligt certifikat.|
|/certificates/Read|Hämta listan över certifikat.|
|/certificates/Write|Lägg till ett nytt certifikat eller uppdatera en befintlig.|
|/checknameavailability/read|Kontrollera om resursnamnet är tillgängliga.|
|/classicmobileservices/read|Hämta klassiska Mobile Services.|
|/connectionGateways/Delete|Tar bort en Gateway med anslutning.|
|/connectionGateways/Join/Action|Ansluter till en Gateway med anslutning.|
|/connectiongateways/Liststatus/Action|Visa Status för anslutningen Gateways.|
|/connectionGateways/ListStatus/Action|Visar status för en Gateway med anslutning.|
|/connectionGateways/Move/Action|Flyttar en Gateway med anslutning.|
|/connectionGateways/Read|Hämta listan över anslutningen Gateways.|
|/connectionGateways/Write|Skapar eller uppdaterar en Gateway med anslutning.|
|/connections/confirmconsentcode/action|Bekräfta anslutningar medgivande kod.|
|/connections/Delete|Tar bort en anslutning.|
|/connections/Join/Action|Ansluter till en anslutning.|
|/connections/listconsentlinks/action|Länkar i listan medgivande för anslutningar.|
|/connections/Move/Action|Flyttar en anslutning.|
|/connections/Read|Hämta listan över anslutningar.|
|/connections/Write|Skapar eller uppdaterar en anslutning.|
|/customApis/Delete|Tar bort en anpassad API.|
|/customApis/extractApiDefinitionFromWsdl/Action|Extraherar API-definition från WSDL.|
|/customApis/Join/Action|Ansluter till en anpassad API.|
|/customApis/listWsdlInterfaces/Action|Listar WSDL-gränssnitt för en anpassad API.|
|/customApis/Move/Action|Flyttar en anpassad API.|
|/customApis/Read|Hämta listan över anpassade API.|
|/customApis/Write|Skapar eller uppdaterar en anpassad API.|
|/deploymentlocations/Read|Hämta platser för distributionen.|
|/geoRegions/Read|Hämta listan över geografiska regionerna.|
|/hostingenvironments/capacities/read|Hämta värd miljöer kapacitet.|
|/hostingEnvironments/Delete|Ta bort Apptjänst-miljö|
|/hostingenvironments/diagnostics/read|Hämta värd miljöer diagnostik.|
|/hostingenvironments/inboundnetworkdependenciesendpoints/read|Hämta nätverksslutpunkter av alla inkommande beroenden.|
|/hostingenvironments/metricdefinitions/read|Hämta värd miljöer Måttdefinitioner.|
|/hostingenvironments/multirolepools/metricdefinitions/read|Hämta värd miljöer mellan pooler Måttdefinitioner.|
|/hostingenvironments/multirolepools/metrics/read|Hämta värd miljöer mellan pooler mått.|
|/hostingEnvironments/multiRolePools/providers/Microsoft.Insights/ metricDefinitions/Read|Hämtar tillgängliga mått för App Service-miljö MultiRole|
|/hostingEnvironments/multiRolePools/Read|Hämta egenskaperna för en FrontEnd-Pool i en Apptjänst-miljö|
|/hostingenvironments/multirolepools/skus/read|Hämta värd miljöer mellan pooler SKU: er.|
|/hostingenvironments/multirolepools/usages/read|Hämta värd miljöer mellan pooler användningsområden.|
|/hostingEnvironments/multiRolePools/Write|Skapa en ny FrontEnd-Pool i en Apptjänst-miljö eller uppdatera en befintlig|
|/hostingenvironments/operations/read|Hämta värd Operations miljöer.|
|/hostingenvironments/outboundnetworkdependenciesendpoints/read|Hämta nätverksslutpunkter av alla utgående beroenden.|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/hostingenvironments/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/hostingEnvironments/Read|Hämta egenskaperna för en Apptjänstmiljö|
|/hostingEnvironments/reboot/Action|Starta om alla datorer i en Apptjänst-miljö|
|/hostingenvironments/resume/action|Återuppta värdbaserade miljöer.|
|/hostingenvironments/serverfarms/read|Hämta värd miljöer App Service-planer.|
|/hostingenvironments/sites/read|Hämta värd miljöer Web Apps.|
|/hostingenvironments/suspend/action|Pausa värdbaserade miljöer.|
|/hostingenvironments/usages/read|Hämta värd miljöer användningsområden.|
|/hostingenvironments/workerpools/metricdefinitions/read|Hämta värd miljöer Workerpools mått definitioner.|
|/hostingenvironments/workerpools/metrics/read|Hämta värd miljöer Workerpools mått.|
|/hostingEnvironments/workerPools/providers/Microsoft.Insights/metricDefinitions/Read|Hämtar tillgängliga mått för App Service-miljö WorkerPool|
|/hostingEnvironments/workerPools/Read|Hämta egenskaperna för en Arbetspool i en Apptjänst-miljö|
|/hostingenvironments/workerpools/skus/Read|Hämta värd miljöer Workerpools SKU: er.|
|/hostingenvironments/workerpools/usages/Read|Hämta värd miljöer Workerpools användningsområden.|
|/hostingEnvironments/workerPools/Write|Skapa en ny Arbetspool i en Apptjänst-miljö eller uppdatera en befintlig|
|/hostingEnvironments/Write|Skapa en ny Apptjänstmiljö eller uppdatera en befintlig|
|/ishostingenvironmentnameavailable/Read|Hämta om värd-Miljönamn är tillgängligt.|
|/ishostnameavailable/Read|Kontrollera om värdnamnet är tillgänglig.|
|/isusernameavailable/Read|Kontrollera om användarnamnet är tillgängliga.|
|/listSitesAssignedToHostName/Read|Hämta namnen på de platser som tilldelats värdnamn.|
|/locations/apioperations/read|Hämta platser API-åtgärder.|
|/locations/connectiongatewayinstallations/read|Hämta platser anslutning Gateway installationer.|
|/locations/extractapidefinitionfromwsdl/action|Extrahera Api-Definition från WSDL för platser.|
|/Locations/listwsdlinterfaces/Action|Visa WSDL-gränssnitt för platser.|
|/locations/managedapis/apioperations/read|Hämta platser hanterade API: et.|
|/locations/managedapis/Join/Action|Ansluter till en hanterad API.|
|/locations/managedapis/read|Hämta platser hanterade API: er.|
|/operations/read|Hämta åtgärder.|
|/publishingusers/Read|Hämta publicera användare.|
|/ publishingusers/skrivning|Uppdatera publicering av användare.|
|/recommendations/Read|Hämta lista över rekommendationer för prenumerationer.|
|/register/action|Registerresursleverantören Microsoft.Web för prenumerationen.|
|/resourcehealthmetadata/Read|Hämta Resource Health Metadata.|
|/serverfarms/capabilities/read|Hämta App Service-planer funktioner.|
|/serverfarms/Delete|Ta bort en befintlig App Service-Plan|
|/serverfarms/firstpartyapps/settings/delete|Ta bort Apptjänst-planer första part appar inställningar.|
|/serverfarms/firstpartyapps/settings/read|Hämta Apptjänst första part appar inställningar för energischeman.|
|/serverfarms/firstpartyapps/settings/write|Uppdatera Apptjänstplaner första part appar inställningar.|
|/serverfarms/hybridconnectionnamespaces/relays/delete|Ta bort Apptjänst-planer Hybrid anslutning namnområden reläer.|
|/serverfarms/hybridconnectionnamespaces/relays/read|Hämta App Service-planer Hybrid anslutning namnområden reläer.|
|/serverfarms/hybridconnectionnamespaces/relays/sites/read|Hämta App Service-planer Hybrid anslutning namnområden reläer Web Apps.|
|/serverfarms/hybridconnectionplanlimits/read|Hämta Plan för App Service-planer Hybrid anslutningsbegränsningar.|
|/serverfarms/hybridconnectionrelays/read|Hämta App Service-planer Hybrid anslutning reläer.|
|/serverfarms/metricdefinitions/read|Hämta Måttdefinitionerna för App Service-planer.|
|/serverfarms/metrics/read|Hämta mätvärden för App Service-planer.|
|/serverfarms/operationresults/read|Hämta App Service-planer Åtgärdsresultat.|
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
|/serverfarms/workers/reboot/action|Starta om Apptjänst-planer arbetare.|
|/serverfarms/Write|Skapa en ny App Service-Plan eller uppdatera en befintlig|
|/Sites/analyzecustomhostname/Read|Analysera anpassade värdnamnet.|
|/sites/applySlotConfig/Action|Tillämpa web app platskonfigurationen från mål-plats till det aktuella webbprogrammet|
|/Sites/Backup/Action|Skapa en ny säkerhetskopia av web app|
|/sites/backup/read|Hämta Web Apps säkerhetskopiering.|
|/sites/backup/write|Uppdatera Web Apps säkerhetskopiering.|
|/sites/backups/delete|Ta bort Web Apps säkerhetskopior.|
|/sites/backups/list/action|Lista över Web Apps säkerhetskopiering.|
|/sites/backups/Read|Hämta egenskaperna för ett webbprogram säkerhetskopiering|
|/sites/backups/restore/action|Återställa Web Apps säkerhetskopior.|
|/sites/config/delete|Ta bort Web Apps Config.|
|/sites/config/list/Action|Visa en lista med Web App känsliga säkerhetsinställningar, till exempel publicera autentiseringsuppgifter, app-inställningar och anslutningssträngar|
|/sites/config/Read|Hämta konfigurationsinställningar för webbprogram|
|/sites/config/Write|Uppdatera Web App konfigurationsinställningar|
|/sites/continuouswebjobs/delete|Ta bort Web Apps kontinuerlig Web jobb.|
|/sites/continuouswebjobs/read|Hämta Web Apps kontinuerlig Webjobs.|
|/sites/continuouswebjobs/start/action|Starta Web Apps kontinuerlig Web jobb.|
|/sites/continuouswebjobs/stop/action|Stoppa Web Apps kontinuerlig Webjobs.|
|/sites/Delete|Ta bort en befintlig Webbapp|
|/sites/deployments/delete|Ta bort Web Apps distributioner.|
|/sites/deployments/log/read|Hämta Web Apps distributioner loggen.|
|/sites/deployments/read|Hämta Web Apps distributioner.|
|/sites/deployments/write|Uppdatera Web Apps distributioner.|
|/Sites/Diagnostics/analyses/Execute/Action|Kör Web Apps diagnostik analys.|
|/sites/diagnostics/analyses/read|Hämta Web Apps diagnostik analys.|
|/sites/diagnostics/aspnetcore/read|Hämta Web Apps diagnostik för ASP.NET Core app.|
|/sites/diagnostics/autoheal/read|Hämta Web Apps diagnostik säkert att Autoheal.|
|/sites/diagnostics/deployment/read|Hämta Web Apps diagnostik-distributionen.|
|/sites/diagnostics/deployments/read|Hämta Web Apps diagnostik distributioner.|
|/Sites/Diagnostics/detectors/Execute/Action|Kör Web Apps diagnostik detektor.|
|/sites/diagnostics/detectors/read|Hämta Web Apps diagnostik detektor.|
|/sites/diagnostics/failedrequestsperuri/read|Hämta Web Apps diagnostik misslyckade förfrågningar Per Uri.|
|/sites/diagnostics/frebanalysis/read|Hämta Web Apps diagnostik FREB analys.|
|/sites/diagnostics/loganalyzer/read|Hämta Web Apps diagnostik loggen Analyzer.|
|/sites/diagnostics/read|Hämta Web Apps diagnostik kategorier.|
|/sites/diagnostics/runtimeavailability/read|Hämta Web Apps diagnostik Runtime tillgänglighet.|
|/sites/diagnostics/servicehealth/read|Hämta Web Apps diagnostik tjänstens hälsa.|
|/sites/diagnostics/sitecpuanalysis/read|Hämta Web Apps diagnostik plats CPU analys.|
|/sites/diagnostics/sitecrashes/read|Hämta Web Apps diagnostik plats kraschar.|
|/sites/diagnostics/sitelatency/read|Hämta svarstid för Web Apps diagnostik webbplats.|
|/sites/diagnostics/sitememoryanalysis/read|Hämta Web Apps diagnostik plats minnesanalys.|
|/sites/diagnostics/siterestartsettingupdate/read|Hämta Web Apps diagnostik plats omstart uppdateringen.|
|/sites/diagnostics/siterestartuserinitiated/read|Hämta Web Apps diagnostik plats omstart användarinitierad.|
|/sites/diagnostics/siteswap/read|Hämta Web Apps diagnostik plats växlingen.|
|/sites/diagnostics/threadcount/read|Hämta antal trådar för Web Apps diagnostik.|
|/sites/diagnostics/workeravailability/read|Hämta Web Apps diagnostik Workeravailability.|
|/sites/diagnostics/workerprocessrecycle/read|Hämta Web Apps diagnostik Worker omarbetning av processen.|
|/sites/domainownershipidentifiers/read|Hämta Web Apps-domän ägarskap identifierare.|
|/sites/domainownershipidentifiers/write|Uppdatera Web Apps-domän ägarskap identifierare.|
|/sites/functions/action|Funktioner Web Apps.|
|/sites/functions/delete|Ta bort Web Apps funktioner.|
|/sites/functions/listsecrets/action|Lista hemligheter Web Apps funktioner.|
|/sites/functions/masterkey/read|Hämta Web Apps funktioner Masterkey.|
|/sites/functions/read|Hämta Web Apps funktioner.|
|/sites/functions/token/read|Get Web Apps funktioner Token.|
|/sites/functions/write|Uppdatera Web Apps funktioner.|
|/sites/hostnamebindings/delete|Ta bort Värdnamnsbindningar för Web Apps.|
|/sites/hostnamebindings/read|Hämta Värdnamnsbindningar för Web Apps.|
|/sites/hostnamebindings/write|Uppdatera Värdnamnsbindningar för Web Apps.|
|/Sites/hybridconnection/delete|Ta bort Hybridanslutningen för Web Apps.|
|/sites/hybridconnection/read|Hämta Hybridanslutning för Web Apps.|
|/Sites/hybridconnection/Write|Uppdatera appar Hybrid-anslutning för webbprogram.|
|/sites/hybridconnectionnamespaces/relays/delete|Ta bort Web Apps Hybrid anslutning namnområden reläer.|
|/sites/hybridconnectionnamespaces/relays/listkeys/action|Lista nycklar Web Apps Hybrid anslutning namnområden reläer.|
|/sites/hybridconnectionnamespaces/relays/read|Hämta Web Apps Hybrid anslutning namnområden reläer.|
|/sites/hybridconnectionnamespaces/relays/write|Uppdatera Web Apps Hybrid anslutning namnområden reläer.|
|/sites/hybridconnectionrelays/read|Hämta Web Apps Hybrid anslutning reläer.|
|/sites/instances/deployments/delete|Ta bort Web Apps instanser distributioner.|
|/sites/instances/deployments/read|Hämta Web Apps instanser distributioner.|
|/sites/instances/extensions/log/read|Hämta Web Apps instanser tillägg loggen.|
|/sites/instances/extensions/read|Hämta Web Apps instanser tillägg.|
|/sites/instances/processes/delete|Ta bort Web Apps instanser processer.|
|/sites/instances/processes/read|Hämta Web Apps instanser processer.|
|/sites/instances/read|Hämta Web Apps-instanser.|
|/Sites/listsyncfunctiontriggerstatus/Action|Lista synkronisering funktionen utlösaren Status Web Apps.|
|/sites/metricdefinitions/read|Hämta definitioner för Web Apps mått.|
|/sites/metrics/read|Hämta mätvärden från webben appar.|
|/sites/metricsdefinitions/read|Hämta definitioner för Web Apps mått.|
|/Sites/migratemysql/Action|Migrera MySql Web Apps.|
|/sites/migratemysql/read|Hämta Web Apps migrera MySql.|
|/sites/networktrace/action|Webbprogram för spårning i nätverket.|
|/Sites/NewPassword/Action|Nytt lösenord Web Apps.|
|/sites/operationresults/read|Hämta Åtgärdsresultat för Web Apps.|
|/sites/operations/read|Hämta åtgärder för Web Apps.|
|/sites/perfcounters/read|Hämta prestandaräknare för Web Apps.|
|/sites/premieraddons/delete|Ta bort Web Apps Premier-tillägg.|
|/sites/premieraddons/read|Hämta Web Apps Premier-tillägg.|
|/sites/premieraddons/write|Uppdatera Web Apps Premier-tillägg.|
|/sites/processes/read|Hämta Web Apps processer.|
|/sites/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/sites/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/sites/providers/Microsoft.Insights/metricDefinitions/Read|Hämtar tillgängliga mått för webbprogram|
|/sites/publiccertificates/delete|Ta bort Web Apps offentliga certifikat.|
|/sites/publiccertificates/read|Hämta Web Apps offentliga certifikat.|
|/sites/publiccertificates/write|Uppdatera Web Apps offentliga certifikat.|
|/sites/publish/Action|Publicera ett webbprogram|
|/sites/publishxml/Action|Hämta Publicera profil-xml för en Webbapp|
|/sites/publishxml/read|Hämta publicera Web Apps XML.|
|/sites/Read|Hämta egenskaperna för en Webbapp|
|/sites/recommendationhistory/read|Hämta webbhistorik appar rekommendation.|
|/sites/recommendations/disable/action|Inaktivera Web Apps rekommendationer.|
|/sites/recommendations/Read|Hämta lista över rekommendationer för webbprogrammet.|
|/sites/recover/action|Återställa Web Apps.|
|/sites/resetSlotConfig/Action|Återställ webbappkonfigurationen|
|/Sites/resourcehealthmetadata/Read|Hämta Web Apps Resource Health Metadata.|
|/sites/restart/Action|Starta om ett webbprogram|
|/sites/restore/read|Hämta Web Apps återställning.|
|/sites/restore/write|Återställa Web Apps.|
|/sites/siteextensions/delete|Ta bort Webbplatstillägg för Web Apps.|
|/sites/siteextensions/read|Hämta Webbplatstillägg för Web Apps.|
|/sites/siteextensions/write|Uppdatera Webbplatstillägg för Web Apps.|
|/Sites/slots/analyzecustomhostname/Read|Hämta Web Apps fack analysera anpassade värdnamnet.|
|/sites/slots/applySlotConfig/Action|Tillämpa web app platskonfigurationen från målet plats till den aktuella platsen.|
|/sites/slots/backup/Action|Skapa ny Webbprogramplats säkerhetskopia.|
|/sites/slots/backup/read|Hämta Web Apps fack säkerhetskopiering.|
|/sites/slots/backup/write|Uppdatera Web Apps fack säkerhetskopiering.|
|/sites/slots/backups/delete|Ta bort Web Apps fack säkerhetskopior.|
|/sites/slots/backups/list/action|Lista Web Apps fack säkerhetskopiering.|
|/sites/slots/backups/Read|Hämta egenskaperna för en webbprogramplatser säkerhetskopiering|
|/sites/slots/backups/restore/action|Återställa Web Apps fack säkerhetskopior.|
|/sites/slots/config/delete|Ta bort Web Apps fack Config.|
|/sites/slots/config/list/Action|Visa en lista med Web App fack känsliga säkerhetsinställningar, t.ex publicering autentiseringsuppgifter, app-inställningar och anslutningssträngar|
|/sites/slots/config/Read|Hämta Webbprogramplatss konfigurationsinställningar|
|/sites/slots/config/Write|Uppdatera Webbprogramplatss konfigurationsinställningar|
|/sites/slots/continuouswebjobs/delete|Ta bort Web Apps fack kontinuerlig Web jobb.|
|/sites/slots/continuouswebjobs/read|Hämta Web Apps fack kontinuerlig Webjobs.|
|/sites/slots/continuouswebjobs/start/action|Starta Web Apps fack kontinuerlig Web jobb.|
|/sites/slots/continuouswebjobs/stop/action|Stoppa Web Apps fack kontinuerlig Webjobs.|
|/sites/slots/Delete|Ta bort en befintlig Webbprogramplats|
|/sites/slots/deployments/delete|Ta bort Web Apps fack distributioner.|
|/sites/slots/deployments/log/read|Hämta Web Apps fack distributioner loggen.|
|/sites/slots/deployments/read|Hämta Web Apps fack distributioner.|
|/sites/slots/deployments/write|Uppdatera Web Apps fack distributioner.|
|/sites/slots/diagnostics/analyses/execute/Action|Kör Web Apps fack diagnostik analys.|
|/sites/slots/diagnostics/analyses/read|Hämta Web Apps fack diagnostik analys.|
|/sites/slots/diagnostics/aspnetcore/read|Hämta Web Apps fack diagnostik för ASP.NET Core app.|
|/sites/slots/diagnostics/autoheal/read|Hämta Web Apps fack diagnostik säkert att Autoheal.|
|/sites/slots/diagnostics/deployment/read|Hämta Web Apps fack diagnostik distributionen.|
|/sites/slots/diagnostics/deployments/read|Hämta Web Apps fack diagnostik distributioner.|
|/Sites/slots/Diagnostics/detectors/Execute/Action|Kör Web Apps fack diagnostik detektor.|
|/sites/slots/diagnostics/detectors/read|Hämta Web Apps fack diagnostik detektor.|
|/sites/slots/diagnostics/frebanalysis/read|Hämta Web Apps fack diagnostik FREB analys.|
|/sites/slots/diagnostics/loganalyzer/read|Hämta Web Apps fack diagnostik loggen Analyzer.|
|/sites/slots/diagnostics/read|Hämta Web Apps fack diagnostik.|
|/sites/slots/diagnostics/runtimeavailability/read|Hämta Web Apps fack diagnostik Runtime tillgänglighet.|
|/sites/slots/diagnostics/servicehealth/read|Hämta Web Apps fack diagnostik tjänstens hälsa.|
|/sites/slots/diagnostics/sitecpuanalysis/read|Hämta Web Apps fack diagnostik plats CPU analys.|
|/sites/slots/diagnostics/sitecrashes/read|Hämta Web Apps fack diagnostik plats kraschar.|
|/sites/slots/diagnostics/sitelatency/read|Hämta svarstid för Web Apps fack diagnostik webbplats.|
|/sites/slots/diagnostics/sitememoryanalysis/read|Hämta Web Apps fack diagnostik plats minnesanalys.|
|/sites/slots/diagnostics/siterestartsettingupdate/read|Hämta Web Apps fack diagnostik plats omstart uppdateringen.|
|/sites/slots/diagnostics/siterestartuserinitiated/read|Hämta Web Apps fack diagnostik plats omstart användarinitierad.|
|/sites/slots/diagnostics/siteswap/read|Hämta Web Apps fack diagnostik plats växlingen.|
|/sites/slots/diagnostics/threadcount/read|Hämta Trådräkning för Web Apps fack diagnostik.|
|/sites/slots/diagnostics/workeravailability/read|Hämta Web Apps fack diagnostik Workeravailability.|
|/sites/slots/diagnostics/workerprocessrecycle/read|Hämta Web Apps fack diagnostik Worker processåtervinning.|
|/sites/slots/domainownershipidentifiers/read|Hämta Web Apps fack domän ägarskap identifierare.|
|/sites/slots/hostnamebindings/delete|Ta bort Värdnamnsbindningar för Web Apps platser.|
|/sites/slots/hostnamebindings/read|Hämta Värdnamnsbindningar för Web Apps platser.|
|/sites/slots/hostnamebindings/write|Uppdatera Värdnamnsbindningar för Web Apps platser.|
|/sites/slots/hybridconnection/delete|Ta bort Hybridanslutningen för Web Apps platser.|
|/sites/slots/hybridconnection/read|Hämta Hybridanslutning för Web Apps platser.|
|/sites/slots/hybridconnection/write|Uppdatera Hybridanslutning för Web Apps platser.|
|/sites/slots/hybridconnectionnamespaces/relays/delete|Ta bort Web Apps fack Hybrid anslutning namnområden reläer.|
|/sites/slots/hybridconnectionnamespaces/relays/write|Uppdatera Web Apps fack Hybrid anslutning namnområden reläer.|
|/sites/slots/hybridconnectionrelays/read|Hämta Web Apps fack Hybrid anslutning reläer.|
|/sites/slots/instances/deployments/read|Hämta Web Apps fack instanser distributioner.|
|/sites/slots/instances/processes/delete|Ta bort Web Apps fack instanser processer.|
|/sites/slots/instances/processes/read|Hämta Web Apps fack instanser processer.|
|/sites/slots/instances/read|Hämta Web Apps fack instanser.|
|/sites/slots/metricdefinitions/read|Hämta Måttdefinitionerna för Web Apps platser.|
|/sites/slots/metrics/read|Hämta Web Apps fack mått.|
|/sites/slots/migratemysql/read|Hämta Web Apps fack migrera MySql.|
|/sites/slots/networktrace/action|Nätverket Trace Web Apps platser.|
|/sites/slots/newpassword/action|Nytt lösenord Web Apps platser.|
|/sites/slots/operationresults/read|Hämta Web Apps fack Åtgärdsresultat.|
|/sites/slots/operations/read|Hämta Web Apps fack åtgärder.|
|/sites/slots/perfcounters/read|Hämta Web Apps fack prestandaräknare.|
|/sites/slots/phplogging/read|Hämta Web Apps fack Phplogging.|
|/sites/slots/premieraddons/delete|Ta bort Web Apps fack Premier-tillägg.|
|/sites/slots/premieraddons/read|Hämta Web Apps fack Premier-tillägg.|
|/sites/slots/premieraddons/write|Uppdatera Web Apps fack Premier-tillägg.|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/read|Hämtar den diagnostiska inställningen för resursen|
|/sites/slots/providers/Microsoft.Insights/diagnosticSettings/write|Skapar eller uppdaterar den diagnostiska inställningen för resursen|
|/sites/slots/providers/Microsoft.Insights/metricDefinitions/Read|Hämtar tillgängliga mått för Webbprogramplats|
|/sites/slots/publiccertificates/read|Hämta Web Apps fack offentliga certifikat.|
|/sites/slots/publiccertificates/write|Skapa eller uppdatera Web Apps fack offentliga certifikat.|
|/sites/slots/publish/Action|Publicera en Webbprogramplats|
|/sites/slots/publishxml/Action|Hämta Publicera profil-xml för Webbprogramplats|
|/sites/slots/Read|Hämta egenskaperna för en distributionsplats för webbprogram|
|/sites/slots/resetSlotConfig/Action|Återställ platskonfigurationen för web app|
|/sites/slots/resourcehealthmetadata/read|Hämta Web Apps fack resurs hälsa Metadata.|
|/sites/slots/restart/Action|Starta om en Webbprogramplats|
|/sites/slots/restore/read|Hämta Web Apps fack återställning.|
|/sites/slots/restore/write|Återställa Web Apps platser.|
|/sites/slots/siteextensions/delete|Ta bort Webbplatstillägg i Web Apps fack.|
|/sites/slots/siteextensions/read|Hämta Webbplatstillägg i Web Apps fack.|
|/sites/slots/siteextensions/write|Uppdatera Webbplatstillägg i Web Apps fack.|
|/sites/slots/slotsdiffs/Action|Hämta konfigurationsavvikelser mellan webbprogrammet och platser|
|/sites/slots/slotsswap/Action|Växla distributionsplatser för webbprogram|
|/sites/slots/snapshots/read|Hämta ögonblicksbilder för Web Apps platser.|
|/sites/slots/sourcecontrols/Delete|Ta bort konfigurationsinställningar för Web App fack källa kontroll|
|/sites/slots/sourcecontrols/Read|Hämta Webbprogramplatss källkontrollen konfigurationsinställningar|
|/sites/slots/sourcecontrols/Write|Uppdatera inställningar för Web App fack källkontrollen configuration|
|/sites/slots/start/Action|Starta en Webbprogramplats|
|/sites/slots/stop/Action|Stoppa en Webbprogramplats|
|/sites/slots/sync/action|Synkronisera Web Apps platser.|
|/sites/slots/triggeredwebjobs/delete|Ta bort Web Apps fack utlösta WebJobs.|
|/sites/slots/triggeredwebjobs/read|Hämta Web Apps fack utlösta WebJobs.|
|/sites/slots/triggeredwebjobs/run/action|Kör Web Apps fack utlösta WebJobs.|
|/sites/slots/usages/read|Hämta Web Apps fack användningsområden.|
|/sites/slots/virtualnetworkconnections/delete|Ta bort virtuella nätverksanslutningar för Web Apps-platser.|
|/sites/slots/virtualnetworkconnections/gateways/write|Uppdatera Web Apps fack virtuella anslutningar Nätverksgatewayerna.|
|/sites/slots/virtualnetworkconnections/read|Hämta virtuella nätverksanslutningar för Web Apps-platser.|
|/sites/slots/virtualnetworkconnections/write|Uppdatera virtuella nätverksanslutningar för Web Apps-platser.|
|/sites/slots/webjobs/read|Hämta Web Apps fack WebJobs.|
|/sites/slots/Write|Skapa en ny Webbprogramplats eller uppdatera en befintlig|
|/sites/slotsdiffs/Action|Hämta konfigurationsavvikelser mellan webbprogrammet och platser|
|/sites/slotsswap/Action|Växla distributionsplatser för webbprogram|
|/sites/snapshots/read|Hämta ögonblicksbilder för Web Apps.|
|/sites/sourcecontrols/Delete|Ta bort konfigurationsinställningar för Web App käll-kontroll|
|/sites/sourcecontrols/Read|Hämta Web App källkontrollen konfigurationsinställningar|
|/sites/sourcecontrols/Write|Uppdatera inställningar för Web App källkontrollen konfiguration|
|/sites/start/Action|Starta ett webbprogram|
|/sites/stop/Action|Stoppa ett webbprogram|
|/sites/sync/action|Synkronisera Web Apps.|
|/sites/syncfunctiontriggers/action|Synkronisera funktionen utlösare för Web Apps.|
|/sites/triggeredwebjobs/delete|Ta bort Web Apps utlösta WebJobs.|
|/sites/triggeredwebjobs/history/read|Hämta appar utlösta WebJobs webbhistorik.|
|/sites/triggeredwebjobs/read|Hämta Web Apps utlösta WebJobs.|
|/sites/triggeredwebjobs/run/action|Kör Web Apps utlöses WebJobs.|
|/sites/usages/read|Hämta Web Apps användningsområden.|
|/sites/virtualnetworkconnections/delete|Ta bort virtuella nätverksanslutningar för Web Apps.|
|/sites/virtualnetworkconnections/gateways/read|Hämta Web Apps virtuella anslutningar Nätverksgatewayerna.|
|/sites/virtualnetworkconnections/gateways/write|Uppdatera Web Apps virtuella anslutningar Nätverksgatewayerna.|
|/sites/virtualnetworkconnections/read|Hämta Web Apps virtuella nätverksanslutningar.|
|/sites/virtualnetworkconnections/write|Uppdatera Web Apps virtuella nätverksanslutningar.|
|/sites/webjobs/read|Hämta Web Apps WebJobs.|
|/ webbplatser/skrivning|Skapa ett nytt webbprogram eller uppdatera en befintlig|
|/skus/read|Hämta SKU: er.|
|/sourcecontrols/read|Hämta kontroller för datakällor.|
|/sourcecontrols/write|Uppdatera källa kontroller.|
|/unregister/action|Avregistrera Microsoft.Web resource provider för prenumerationen.|
|/validate/action|Validera.|
|/verifyhostingenvironmentvnet/action|Kontrollera värd för virtuella nätverk i miljön.|

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

| Åtgärd | Beskrivning |
|---|---|
|/components/read|Läsa operationsresurser|
|/healthInstances/read|Läsa operationsresurser|
|/Operations/read|Läsa operationsresurser|
|/workloads/delete|Tar bort en resurs för arbetsbelastning|
|/workloads/read|Läser en arbetsbelastning resurs|
|/workloads/write|Skriver en arbetsbelastning resurs|

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapa en anpassad roll](custom-roles.md).
- Granska de [inbyggda RBAC-roller](built-in-roles.md).
- Lär dig att hantera åtkomst [av användare](role-assignments-users.md) eller [per resurs](role-assignments-portal.md) 
- Lär dig hur du [visa aktivitetsloggar granska åtgärder på resurser](~/articles/azure-resource-manager/resource-group-audit.md)
