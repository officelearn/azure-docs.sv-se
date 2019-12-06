---
title: Inbyggda roller för Azure-resurser | Microsoft Docs
description: Beskriver de inbyggda rollerna för rollbaserad åtkomst kontroll (RBAC) och Azure-resurser. Visar en lista över åtgärder, NotActions, DataActions och NotDataActions.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: 00d4487abed97084976d81ee1ec78316fc688193
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851391"
---
# <a name="built-in-roles-for-azure-resources"></a>Inbyggda roller för Azure-resurser

[Rollbaserad åtkomst kontroll (RBAC)](overview.md) har flera inbyggda roller för Azure-resurser som du kan tilldela till användare, grupper, tjänstens huvud namn och hanterade identiteter. Roll tilldelningar är hur du styr åtkomsten till Azure-resurser. Om de inbyggda rollerna inte uppfyller organisationens specifika krav, kan du skapa egna [anpassade roller för Azure-resurser](custom-roles.md).

Den här artikeln innehåller de inbyggda rollerna för Azure-resurser, som alltid utvecklas. För att få de senaste rollerna använder du [AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) eller [AZ roll definitions lista](/cli/azure/role/definition#az-role-definition-list). Om du letar efter administratörs roller för Azure Active Directory, se [Administratörs roll behörigheter i Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="built-in-role-descriptions"></a>Inbyggda roll beskrivningar

Följande tabell innehåller en kort beskrivning av varje inbyggd roll. Klicka på roll namnet om du vill se en lista över `Actions`, `NotActions`, `DataActions`och `NotDataActions` för varje roll. Information om vad dessa åtgärder betyder och hur de tillämpas på hanterings-och data planen finns i [förstå roll definitioner för Azure-resurser](role-definitions.md).


> [!div class="mx-tableFixed"]
> | Inbyggd roll | Beskrivning | Id |
> | --- | --- | --- |
> | [Ägare](#owner) | Låter dig hantera allt, inklusive åtkomst till resurser. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Deltagare](#contributor) | Låter dig hantera allt, förutom att bevilja åtkomst till resurser. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Läsare](#reader) | Gör att du kan visa allt, men inte göra några ändringar. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [AcrDelete](#acrdelete) | ta bort ACR | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | ACR-bildsignerare | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull](#acrpull) | ACR pull | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush](#acrpush) | ACR-push | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineReader](#acrquarantinereader) | ACR Quarantine data Reader | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | ACR karantän data skrivare | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [API Management Service Contributor](#api-management-service-contributor) | Kan hantera tjänster och API: er | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [Rollen API Management tjänst operatör](#api-management-service-operator-role) | Kan hantera tjänsten men inte API: erna | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [Rollen API Management tjänst läsare](#api-management-service-reader-role) | Skrivskyddad åtkomst till tjänster och API: er | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [Application Insights komponent deltagare](#application-insights-component-contributor) | Kan hantera Application Insights-komponenter | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Ger användaren behörighet att visa och hämta fel söknings ögonblicks bilder som samlats in med Application Insights Snapshot Debugger. Observera att dessa behörigheter inte ingår i [ägaren](#owner) eller [deltagar](#contributor) rollerna. | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Automatiserings jobb operatör](#automation-job-operator) | Skapa och hantera jobb med hjälp av Automation-runbooks. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Automation-operatör](#automation-operator) | Automation-operatörer kan starta, stoppa, pausa och återuppta jobb | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Automation Runbook-operator](#automation-runbook-operator) | Läs Runbook-egenskaperna – för att kunna skapa jobb för runbooken. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Aver deltagare](#avere-contributor) | Kan skapa och hantera ett AVERT vFXT-kluster. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Aver operator](#avere-operator) | Används av det Avera vFXT-klustret för att hantera klustret | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Azure Event Hubs data ägare](#azure-event-hubs-data-owner) | Ger fullständig åtkomst till Azure Event Hubs-resurser. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Azure Event Hubs data mottagare](#azure-event-hubs-data-receiver) | Tillåter åtkomst till Azure Event Hubs-resurser. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Azure Event Hubs data avsändare](#azure-event-hubs-data-sender) | Tillåter skicka åtkomst till Azure Event Hubs-resurser. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Administratörs roll för Azure Kubernetes service Cluster](#azure-kubernetes-service-cluster-admin-role) | Visa lista med autentiseringsuppgifter för kluster administratör. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Användar roll för Azure Kubernetes service-kluster](#azure-kubernetes-service-cluster-user-role) | Visa lista över autentiseringsuppgifter för kluster användare. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | [Azure Maps data läsare (förhands granskning)](#azure-maps-data-reader-preview) | Beviljar åtkomst till läsa kartdata relaterade data från ett Azure Maps-konto. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Azure Sentinel-deltagare](#azure-sentinel-contributor) | Azure Sentinel-deltagare | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Azure Sentinel-läsare](#azure-sentinel-reader) | Azure Sentinel-läsare | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Azure Sentinel-svarare](#azure-sentinel-responder) | Azure Sentinel-svarare | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Azure Service Bus data ägare](#azure-service-bus-data-owner) | Ger fullständig åtkomst till Azure Service Bus resurser. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Azure Service Bus data mottagare](#azure-service-bus-data-receiver) | Ger åtkomst till Azure Service Bus resurser. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Azure Service Bus data avsändare](#azure-service-bus-data-sender) | Tillåter att åtkomst till Azure Service Bus-resurser skickas. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Azure Stack registrerings ägare](#azure-stack-registration-owner) | Låter dig hantera Azure Stack-registreringar. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [Säkerhets kopierings deltagare](#backup-contributor) | Låter dig hantera säkerhets kopierings tjänsten, men kan inte skapa valv och ge åtkomst till andra | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Ansvarig för säkerhets kopiering](#backup-operator) | Låter dig hantera säkerhets kopierings tjänster, förutom att ta bort säkerhets kopiering, skapa valv och ge till gång till andra | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Säkerhets kopierings läsare](#backup-reader) | Kan visa säkerhets kopierings tjänster, men kan inte göra ändringar | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Fakturerings läsare](#billing-reader) | Tillåter Läs åtkomst till fakturerings data | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [BizTalk-deltagare](#biztalk-contributor) | Gör att du kan hantera BizTalk Services, men inte till gång till dem. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Blockchain för medlems Node (för hands version)](#blockchain-member-node-access-preview) | Tillåter åtkomst till blockchain-medlems noder | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | [Skiss deltagare](#blueprint-contributor) | Kan hantera skiss definitioner, men tilldela dem inte. | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [Skiss operator](#blueprint-operator) | Kan tilldela befintliga publicerade ritningar, men kan inte skapa nya ritningar. OBS! detta fungerar endast om tilldelningen görs med en tilldelad hanterad identitet. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [CDN-slutpunkts deltagare](#cdn-endpoint-contributor) | Kan hantera CDN-slutpunkter, men kan inte bevilja åtkomst till andra användare. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [CDN-slutpunkt läsare](#cdn-endpoint-reader) | Kan visa CDN-slutpunkter, men kan inte göra ändringar. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [CDN-profil deltagare](#cdn-profile-contributor) | Kan hantera CDN-profiler och deras slut punkter, men kan inte bevilja åtkomst till andra användare. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [CDN profil läsare](#cdn-profile-reader) | Kan visa CDN-profiler och deras slut punkter, men kan inte göra ändringar. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Klassisk nätverksdeltagare](#classic-network-contributor) | Gör att du kan hantera klassiska nätverk, men inte till gång till dem. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [Klassisk lagrings konto deltagare](#classic-storage-account-contributor) | Gör att du kan hantera klassiska lagrings konton, men inte till gång till dem. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [Klassisk lagrings kontots nyckel operatörs tjänst roll](#classic-storage-account-key-operator-service-role) | Klassiska lagrings konto nyckel operatörer får lista och återskapa nycklar på klassiska lagrings konton | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Klassisk virtuell dator deltagare](#classic-virtual-machine-contributor) | Låter dig hantera klassiska virtuella datorer, men inte åtkomst till dem, inte det virtuella nätverk eller lagrings konto som de är anslutna till. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Cognitive Services deltagare](#cognitive-services-contributor) | Gör att du kan skapa, läsa, uppdatera, ta bort och hantera nycklar för Cognitive Services. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Cognitive Services data läsare (förhands granskning)](#cognitive-services-data-reader-preview) | Gör att du kan läsa Cognitive Services data. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Cognitive Services användare](#cognitive-services-user) | Gör att du kan läsa och Visa nycklar för Cognitive Services. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | [Cosmos DB konto läsar roll](#cosmos-db-account-reader-role) | Kan läsa Azure Cosmos DB konto data. Se [DocumentDB Account Contributor](#documentdb-account-contributor) för att hantera Azure Cosmos DB-konton. | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Cosmos DB operatör](#cosmos-db-operator) | Låter dig hantera Azure Cosmos DB konton, men inte komma åt data i dem. Förhindrar åtkomst till konto nycklar och anslutnings strängar. | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Kan skicka en Restore-begäran för en Cosmos DB databas eller en behållare för ett konto | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [Cost Management deltagare](#cost-management-contributor) | Kan visa kostnader och hantera kostnads konfiguration (t. ex. budgetar, exporter) | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Cost Management läsare](#cost-management-reader) | Kan visa kostnads data och konfiguration (t. ex. budgetar, exporter) | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Data Box-enhet deltagare](#data-box-contributor) | Låter dig hantera allt under Data Box-enhet tjänst, förutom att ge till gång till andra. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Data Box-enhet läsare](#data-box-reader) | Låter dig hantera Data Box-enhet tjänst, förutom att skapa order-eller redigerings beställnings detaljer och ge åtkomst till andra. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Data Factory deltagare](#data-factory-contributor) | Skapa och hantera data fabriker, samt underordnade resurser i dem. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Data Lake Analytics utvecklare](#data-lake-analytics-developer) | Låter dig skicka, övervaka och hantera dina egna jobb, men inte skapa eller ta bort Data Lake Analytics konton. | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [Data rensning](#data-purger) | Kan rensa analys data | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [DevTest Labs-användare](#devtest-labs-user) | Låter dig ansluta, starta, starta om och stänga av dina virtuella datorer i din Azure DevTest Labs. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [DNS-zon deltagare](#dns-zone-contributor) | Gör att du kan hantera DNS-zoner och post uppsättningar i Azure DNS, men du kan inte styra vem som har åtkomst till dem. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [DocumentDB-konto deltagare](#documentdb-account-contributor) | Kan hantera Azure Cosmos DB-konton. Azure Cosmos DB är tidigare känt som DocumentDB. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [EventGrid EventSubscription-deltagare](#eventgrid-eventsubscription-contributor) | Låter dig hantera EventGrid händelse prenumerations åtgärder. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [EventGrid EventSubscription-läsare](#eventgrid-eventsubscription-reader) | Låter dig läsa EventGrid händelse prenumerationer. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [HDInsight-kluster operator](#hdinsight-cluster-operator) | Gör att du kan läsa och ändra HDInsight-klusterkonfigurationer. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [HDInsight Domain Services-deltagare](#hdinsight-domain-services-contributor) | Kan läsa, skapa, ändra och ta bort åtgärder för domän tjänster som krävs för HDInsight-Enterprise Security Package | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Konto deltagare i Intelligent Systems](#intelligent-systems-account-contributor) | Gör att du kan hantera intelligenta system konton, men inte åtkomst till dem. | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | [Key Vault deltagare](#key-vault-contributor) | Låter dig hantera nyckel valv, men inte åtkomst till dem. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Labb skapare](#lab-creator) | Gör att du kan skapa, hantera och ta bort dina hanterade labb under dina Azure Lab-konton. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | [Log Analytics deltagare](#log-analytics-contributor) | Log Analytics deltagare kan läsa alla övervaknings data och redigera övervaknings inställningar. Genom att redigera övervaknings inställningarna lägger du till VM-tillägget till virtuella datorer. läsning av lagrings konto nycklar för att kunna konfigurera samling av loggar från Azure Storage. Skapa och konfigurera Automation-konton. lägga till lösningar. och konfigurera Azure Diagnostics på alla Azure-resurser. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Log Analytics läsare](#log-analytics-reader) | Log Analytics läsaren kan visa och söka i alla övervaknings data samt Visa övervaknings inställningar, inklusive Visa konfigurationen av Azure Diagnostics på alla Azure-resurser. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | [Logic app-deltagare](#logic-app-contributor) | Låter dig hantera Logi Kap par, men ändra inte åtkomsten till dem. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Logic app-operatör](#logic-app-operator) | Låter dig läsa, aktivera och inaktivera Logi Kap par, men inte redigera eller uppdatera dem. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | [Rollen hanterad program operatör](#managed-application-operator-role) | Gör att du kan läsa och utföra åtgärder på hanterade program resurser | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Läsare för hanterade program](#managed-applications-reader) | Låter dig läsa resurser i en hanterad app och begära JIT-åtkomst. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Hanterad identitets deltagare](#managed-identity-contributor) | Skapa, läsa, uppdatera och ta bort användare tilldelad identitet | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Hanterad identitets operator](#managed-identity-operator) | Läs och tilldela en tilldelad identitet | f1a07417-d97a-45cb-824c-7a7467783830 |
> | [Borttagnings roll för registrering av hanterade tjänster](#managed-services-registration-assignment-delete-role) | Ta bort roll för registrering av hanterade tjänster för att hantera klient organisations användare kan ta bort den registrerings tilldelning som tilldelats till klienten. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Deltagare i hanterings grupp](#management-group-contributor) | Rollen hanterings grupp deltagare | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Hanterings grupp läsare](#management-group-reader) | Rollen hanterings grupp läsare | ac63b705-f282-497d-ac71-919bf39d939d |
> | [Övervaknings deltagare](#monitoring-contributor) | Kan läsa alla övervaknings data och redigera övervaknings inställningar. Se även [komma igång med roller, behörigheter och säkerhet med Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Övervaknings mått utgivare](#monitoring-metrics-publisher) | Möjliggör publicering av mått mot Azure-resurser | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Övervaknings läsare](#monitoring-reader) | Kan läsa alla övervaknings data (mått, loggar osv.). Se även [komma igång med roller, behörigheter och säkerhet med Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Nätverksdeltagare](#network-contributor) | Gör att du kan hantera nätverk, men inte till gång till dem. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Ny Relic APM-konto deltagare](#new-relic-apm-account-contributor) | Låter dig hantera New Relic Application Performance Management konton och program, men inte till gång till dem. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Data skrivare för princip insikter (för hands version)](#policy-insights-data-writer-preview) | Tillåter Läs åtkomst till resurs principer och Skriv behörighet till resurs komponent princip händelser. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Läsare och data åtkomst](#reader-and-data-access) | Gör att du kan visa allting men du kan inte ta bort eller skapa ett lagrings konto eller en resurs som saknas. Den kommer också att tillåta Läs-/skriv åtkomst till alla data som finns i ett lagrings konto via åtkomst till lagrings konto nycklar. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Redis Cache deltagare](#redis-cache-contributor) | Låter dig hantera Redis-cacheer, men inte till gång till dem. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [Resurs princip deltagare](#resource-policy-contributor) | Användare med behörighet att skapa/ändra resurs principer, skapa support ärende och läsa resurser/hierarki. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Jobb samlings deltagare i Scheduler](#scheduler-job-collections-contributor) | Gör att du kan hantera jobb samlingar i Scheduler, men inte till gång till dem. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | [Search Service deltagare](#search-service-contributor) | Låter dig hantera Sök tjänster, men inte till gång till dem. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [Säkerhets administratör](#security-admin) | Endast i Security Center: kan visa säkerhets principer, Visa säkerhets tillstånd, redigera säkerhets principer, Visa aviseringar och rekommendationer, ignorera aviseringar och rekommendationer | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Säkerhets hanterare (bakåtkompatibelt)](#security-manager-legacy) | Detta är en äldre roll. Använd säkerhets administratör istället | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Säkerhets läsare](#security-reader) | Endast i Security Center: kan visa rekommendationer och aviseringar, Visa säkerhets principer, Visa säkerhets tillstånd, men kan inte göra ändringar | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | [Site Recovery deltagare](#site-recovery-contributor) | Låter dig hantera Site Recovery tjänst förutom att skapa valv och roll tilldelning | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Site Recovery operatör](#site-recovery-operator) | Låter dig redundansväxla och failback men inte utföra andra Site Recovery hanterings åtgärder | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Site Recovery läsare](#site-recovery-reader) | Låter dig Visa Site Recovery status men inte utföra andra hanterings åtgärder | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Konto deltagare för spatiala ankare](#spatial-anchors-account-contributor) | Låter dig hantera spatiala ankare i ditt konto, men ta inte bort dem | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Konto ägare för spatiala ankare](#spatial-anchors-account-owner) | Låter dig hantera spatialdata i ditt konto, inklusive att ta bort dem | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Konto läsare för spatiala ankare](#spatial-anchors-account-reader) | Gör att du kan hitta och läsa egenskaper för spatiala ankare i ditt konto | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | [SQL DB-deltagare](#sql-db-contributor) | Gör att du kan hantera SQL-databaser, men inte åtkomst till dem. Du kan inte heller hantera säkerhets relaterade principer eller överordnade SQL-servrar. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [SQL-hanterad instans deltagare](#sql-managed-instance-contributor) | Låter dig hantera SQL-hanterade instanser och nödvändig nätverks konfiguration, men kan inte ge åtkomst till andra. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [SQL Security Manager](#sql-security-manager) | Gör att du kan hantera säkerhetsrelaterade principer för SQL-servrar och databaser, men inte åtkomst till dem. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [SQL Server deltagare](#sql-server-contributor) | Gör att du kan hantera SQL-servrar och databaser, men inte åtkomst till dem och inte deras säkerhetsrelaterade principer. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | [Lagringskontodeltagare](#storage-account-contributor) | Tillåter hantering av lagrings konton. Ger åtkomst till konto nyckeln, som kan användas för att få åtkomst till data via autentisering med delad nyckel. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [Lagrings kontots nyckel operatörs tjänst roll](#storage-account-key-operator-service-role) | Tillåter att du visar och återskapar åtkomst nycklar för lagrings kontot. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Storage BLOB data-deltagare](#storage-blob-data-contributor) | Läsa, skriva och ta bort Azure Storage behållare och blobbar. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Storage BLOB data-ägare](#storage-blob-data-owner) | Ger fullständig åtkomst till Azure Storage BLOB-behållare och data, inklusive att tilldela POSIX-åtkomstkontroll. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Storage BLOB data Reader](#storage-blob-data-reader) | Läs och Visa Azure Storage behållare och blobbar. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Storage BLOB-delegerare](#storage-blob-delegator) | Hämta en användar Delegerings nyckel som sedan kan användas för att skapa en signatur för delad åtkomst för en behållare eller BLOB som är signerad med Azure AD-autentiseringsuppgifter. Mer information finns i [skapa en användar Delegerings-SAS](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas). | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [Lagrings fil data SMB-resurs deltagare](#storage-file-data-smb-share-contributor) | Tillåter Läs-, skriv-och borttagnings åtkomst i Azure Storage fil resurser över SMB | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Lagrings fil data SMB-resurs upphöjt bidrags givare](#storage-file-data-smb-share-elevated-contributor) | Tillåter behörighet att läsa, skriva, ta bort och ändra NTFS-behörighet i Azure Storage fil resurser över SMB | a7264617-510b-434b-a828-9731dc254ea7 |
> | [Storage File data SMB Share Reader](#storage-file-data-smb-share-reader) | Tillåter Läs åtkomst till Azure-filresurs via SMB | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Data deltagare i Storage Queue](#storage-queue-data-contributor) | Läsa, skriva och ta bort Azure Storage köer och köa meddelanden. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Processor för data meddelande i lagrings kön](#storage-queue-data-message-processor) | Granska, hämta och ta bort ett meddelande från en Azure Storage kö. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Avsändare av data meddelande i lagrings köer](#storage-queue-data-message-sender) | Lägg till meddelanden i en Azure Storage-kö. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Data läsare för lagrings kön](#storage-queue-data-reader) | Läs och Visa Azure Storage köer och köa meddelanden. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 19e7f393-937e-4f77-808e-94535e297925 |
> | [Support förfrågan deltagare](#support-request-contributor) | Gör att du kan skapa och hantera support förfrågningar | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Traffic Manager deltagare](#traffic-manager-contributor) | Låter dig hantera Traffic Manager profiler, men låter dig inte kontrol lera vem som har åtkomst till dem. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | [Administratör för användaråtkomst](#user-access-administrator) | Gör att du kan hantera användar åtkomst till Azure-resurser. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | [Administratörs inloggning för virtuell dator](#virtual-machine-administrator-login) | Visa Virtual Machines i portalen och logga in som administratör | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Virtuell datordeltagare](#virtual-machine-contributor) | Låter dig hantera virtuella datorer, men inte åtkomst till dem, inte det virtuella nätverk eller lagrings konto som de är anslutna till. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Användar inloggning för virtuell dator](#virtual-machine-user-login) | Visa Virtual Machines i portalen och logga in som en vanlig användare. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | [Webb Plans deltagare](#web-plan-contributor) | Gör att du kan hantera webb planer för webbplatser, men inte till gång till dem. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Webbplats deltagare](#website-contributor) | Gör att du kan hantera webbplatser (inte webb planer), men inte till gång till dem. | de139f84-1756-47ae-9be6-808fbbe84772 |


## <a name="owner"></a>Ägare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera allt, inklusive åtkomst till resurser. |
> | **Id** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **Åtgärder** |  |
> | * | Skapa och hantera resurser av alla typer |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="contributor"></a>Deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera allt, förutom att bevilja åtkomst till resurser. |
> | **Id** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **Åtgärder** |  |
> | * | Skapa och hantera resurser av alla typer |
> | **NotActions** |  |
> | Microsoft. Authorization/*/Delete | Ta bort roller, princip tilldelningar, princip definitioner och princip uppsättnings definitioner |
> | Microsoft. Authorization/*/Write | Skapa roller, roll tilldelningar, princip tilldelningar, princip definitioner och princip uppsättnings definitioner |
> | Microsoft.Authorization/elevateAccess/Action | Beviljar anroparen Användaråtkomst Administratörsåtkomst för klientomfånget |
> | Microsoft. skiss/blueprintAssignments/Write | Skapa eller uppdatera alla skiss uppgifter |
> | Microsoft. skiss/blueprintAssignments/Delete | Ta bort alla skiss uppgifter |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="reader"></a>Läsare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Gör att du kan visa allt, men inte göra några ändringar. |
> | **Id** | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | **Åtgärder** |  |
> | */read | Läs resurser av alla typer, förutom hemligheter. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="acrdelete"></a>AcrDelete
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | ta bort ACR |
> | **Id** | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | **Åtgärder** |  |
> | Microsoft. ContainerRegistry/register/artefakter/ta bort | Ta bort artefakt i ett behållar register. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="acrimagesigner"></a>AcrImageSigner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | ACR-bildsignerare |
> | **Id** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **Åtgärder** |  |
> | Microsoft.ContainerRegistry/registries/sign/write | Push/pull-metadata för innehålls förtroende för ett behållar register. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="acrpull"></a>AcrPull
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | ACR pull |
> | **Id** | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | **Åtgärder** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Hämta eller hämta avbildningar från ett behållar register. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="acrpush"></a>AcrPush
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | ACR-push |
> | **Id** | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | **Åtgärder** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Hämta eller hämta avbildningar från ett behållar register. |
> | Microsoft.ContainerRegistry/registries/push/write | Push-överför eller Skriv avbildningar till ett behållar register. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="acrquarantinereader"></a>AcrQuarantineReader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | ACR Quarantine data Reader |
> | **Id** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **Åtgärder** |  |
> | Microsoft. ContainerRegistry/register/karantän/läsa | Hämta eller hämta bilder i karantän från container Registry |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="acrquarantinewriter"></a>AcrQuarantineWriter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | ACR karantän data skrivare |
> | **Id** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **Åtgärder** |  |
> | Microsoft. ContainerRegistry/register/karantän/läsa | Hämta eller hämta bilder i karantän från container Registry |
> | Microsoft. ContainerRegistry/register/karantän/skrivning | Skriv/ändra karantän tillstånd för karantän avbildningar |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="api-management-service-contributor"></a>API Management-tjänstdeltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan hantera tjänster och API: er |
> | **Id** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Åtgärder** |  |
> | Microsoft.ApiManagement/service/* | Skapa och hantera API Management-tjänst |
> | Microsoft.Authorization/*/read | Läs behörighet |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviserings regler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="api-management-service-operator-role"></a>API Management-tjänstoperatorroll
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan hantera tjänsten men inte API: erna |
> | **Id** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | **Åtgärder** |  |
> | Microsoft.ApiManagement/service/*/read | Läs API Management tjänst instanser |
> | Microsoft.ApiManagement/service/backup/action | Säkerhetskopiera API Management tjänst till den angivna behållaren i ett användardefinierat lagrings konto |
> | Microsoft.ApiManagement/service/delete | Ta bort API Management tjänst instans |
> | Microsoft.ApiManagement/service/managedeployments/action | Ändra SKU/enheter, Lägg till/ta bort regionala distributioner av API Managements tjänsten |
> | Microsoft.ApiManagement/service/read | Läs metadata för en API Management tjänst instans |
> | Microsoft.ApiManagement/service/restore/action | Återställa API Management tjänst från den angivna behållaren i ett användardefinierat lagrings konto |
> | Microsoft.ApiManagement/service/updatecertificate/action | Ladda upp SSL-certifikatet för en API Management-tjänst |
> | Microsoft.ApiManagement/service/updatehostname/action | Konfigurera, uppdatera eller ta bort anpassade domän namn för en API Management-tjänst |
> | Microsoft.ApiManagement/service/write | Skapa en ny instans av API Management-tjänsten |
> | Microsoft.Authorization/*/read | Läs behörighet |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviserings regler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Hämta nycklar kopplade till användaren |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="api-management-service-reader-role"></a>Rollen API Management tjänst läsare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Skrivskyddad åtkomst till tjänster och API: er |
> | **Id** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Åtgärder** |  |
> | Microsoft.ApiManagement/service/*/read | Läs API Management tjänst instanser |
> | Microsoft.ApiManagement/service/read | Läs metadata för en API Management tjänst instans |
> | Microsoft.Authorization/*/read | Läs behörighet |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviserings regler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Hämta nycklar kopplade till användaren |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="application-insights-component-contributor"></a>Application Insights komponent deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan hantera Application Insights-komponenter |
> | **Id** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviserings regler |
> | Microsoft.Insights/components/* | Skapa och hantera Insights-komponenter |
> | Microsoft.Insights/webtests/* | Skapa och hantera webbtester |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="application-insights-snapshot-debugger"></a>Application Insights Snapshot Debugger
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Ger användaren behörighet att visa och hämta fel söknings ögonblicks bilder som samlats in med Application Insights Snapshot Debugger. Observera att dessa behörigheter inte ingår i [ägaren](#owner) eller [deltagar](#contributor) rollerna. |
> | **Id** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="automation-job-operator"></a>Automatiserings jobb operatör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Skapa och hantera jobb med hjälp av Automation-runbooks. |
> | **Id** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Läser Hybrid Runbook Worker resurser |
> | Microsoft.Automation/automationAccounts/jobs/read | Hämtar ett Azure Automation jobb |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Återupptar ett Azure Automation jobb |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Stoppar ett Azure Automation jobb |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Hämtar en Azure Automation jobb ström |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Pausar ett Azure Automation jobb |
> | Microsoft.Automation/automationAccounts/jobs/write | Skapar ett Azure Automation jobb |
> | Microsoft. Automation/automationAccounts/Jobs/utdata/Read | Hämtar utdata för ett jobb |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="automation-operator"></a>Automation-operatör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Automation-operatörer kan starta, stoppa, pausa och återuppta jobb |
> | **Id** | d3881f73-407a-4167-8283-e981cbba0404 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Läser Hybrid Runbook Worker resurser |
> | Microsoft.Automation/automationAccounts/jobs/read | Hämtar ett Azure Automation jobb |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Återupptar ett Azure Automation jobb |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Stoppar ett Azure Automation jobb |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Hämtar en Azure Automation jobb ström |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Pausar ett Azure Automation jobb |
> | Microsoft.Automation/automationAccounts/jobs/write | Skapar ett Azure Automation jobb |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Hämtar ett Azure Automation jobb schema |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Skapar ett Azure Automation jobb schema |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Hämtar arbets ytan som är länkad till Automation-kontot |
> | Microsoft.Automation/automationAccounts/read | Hämtar ett Azure Automation konto |
> | Microsoft.Automation/automationAccounts/runbooks/read | Hämtar en Azure Automation Runbook |
> | Microsoft.Automation/automationAccounts/schedules/read | Hämtar en Azure Automation schema till gång |
> | Microsoft.Automation/automationAccounts/schedules/write | Skapar eller uppdaterar en Azure Automation schema till gång |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft. Automation/automationAccounts/Jobs/utdata/Read | Hämtar utdata för ett jobb |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="automation-runbook-operator"></a>Automation Runbook-operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Läs Runbook-egenskaperna – för att kunna skapa jobb för runbooken. |
> | **Id** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Automation/automationAccounts/runbooks/read | Hämtar en Azure Automation Runbook |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="avere-contributor"></a>Aver deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan skapa och hantera ett AVERT vFXT-kluster. |
> | **Id** | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft. Compute/*/Read |  |
> | Microsoft. Compute/availabilitySets/* |  |
> | Microsoft.Compute/virtualMachines/* |  |
> | Microsoft. Compute/disks/* |  |
> | Microsoft. Network/*/Read |  |
> | Microsoft.Network/networkInterfaces/* |  |
> | Microsoft.Network/virtualNetworks/read | Hämta definition av virtuellt nätverk |
> | Microsoft. Network/virtualNetworks/subnets/Read | Hämtar en under näts definition för virtuellt nätverk |
> | Microsoft. Network/virtualNetworks/subnets/Join/Action | Ansluter till ett virtuellt nätverk. Det går inte att avisera. |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Ansluter till en resurs som lagrings konto eller SQL-databas till ett undernät. Det går inte att avisera. |
> | Microsoft.Network/networkSecurityGroups/join/action | Ansluter till en nätverks säkerhets grupp. Det går inte att avisera. |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft. Storage/*/Read |  |
> | Microsoft.Storage/storageAccounts/* |  |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | Microsoft. Resources/Subscriptions/resourceGroups/Resources/Read | Hämtar resurser för resurs gruppen. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Returnerar resultatet av att ta bort en BLOB |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Returnerar en BLOB eller en lista över blobbar |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Returnerar resultatet av att skriva en BLOB |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="avere-operator"></a>Aver operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Används av det Avera vFXT-klustret för att hantera klustret |
> | **Id** | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | **Åtgärder** |  |
> | Microsoft.Compute/virtualMachines/read | Hämta egenskaperna för en virtuell dator |
> | Microsoft.Network/networkInterfaces/read | Hämtar en definition för nätverks gränssnitt.  |
> | Microsoft.Network/networkInterfaces/write | Skapar ett nätverks gränssnitt eller uppdaterar ett befintligt nätverks gränssnitt.  |
> | Microsoft.Network/virtualNetworks/read | Hämta definition av virtuellt nätverk |
> | Microsoft. Network/virtualNetworks/subnets/Read | Hämtar en under näts definition för virtuellt nätverk |
> | Microsoft. Network/virtualNetworks/subnets/Join/Action | Ansluter till ett virtuellt nätverk. Det går inte att avisera. |
> | Microsoft.Network/networkSecurityGroups/join/action | Ansluter till en nätverks säkerhets grupp. Det går inte att avisera. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Returnerar resultatet av att ta bort en behållare |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Returnerar lista över behållare |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Returnerar resultatet av att skicka BLOB-behållare |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Returnerar resultatet av att ta bort en BLOB |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Returnerar en BLOB eller en lista över blobbar |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Returnerar resultatet av att skriva en BLOB |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="azure-event-hubs-data-owner"></a>Azure Event Hubs data ägare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Ger fullständig åtkomst till Azure Event Hubs-resurser. |
> | **Id** | f526a384-b230-433a-b45c-95f59c4a2dec |
> | **Åtgärder** |  |
> | Microsoft.EventHub/* |  |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft.EventHub/* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="azure-event-hubs-data-receiver"></a>Azure Event Hubs data mottagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Tillåter åtkomst till Azure Event Hubs-resurser. |
> | **Id** | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | **Åtgärder** |  |
> | Microsoft.EventHub/*/eventhubs/consumergroups/read |  |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft.EventHub/*/receive/action |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="azure-event-hubs-data-sender"></a>Azure Event Hubs data avsändare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Tillåter skicka åtkomst till Azure Event Hubs-resurser. |
> | **Id** | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | **Åtgärder** |  |
> | Microsoft. EventHub/*/eventhubs/Read |  |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft. EventHub/*/Send/Action |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="azure-kubernetes-service-cluster-admin-role"></a>Administratörs roll för Azure Kubernetes service Cluster
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Visa lista med autentiseringsuppgifter för kluster administratör. |
> | **Id** | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | **Åtgärder** |  |
> | Microsoft. container service/managedClusters/listClusterAdminCredential/Action | Visa en lista över clusterAdmin-autentiseringsuppgiften för ett hanterat kluster |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="azure-kubernetes-service-cluster-user-role"></a>Användar roll för Azure Kubernetes service-kluster
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Visa lista över autentiseringsuppgifter för kluster användare. |
> | **Id** | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Åtgärder** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Visa en lista över clusterUser-autentiseringsuppgiften för ett hanterat kluster |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="azure-maps-data-reader-preview"></a>Azure Maps data läsare (förhands granskning)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Beviljar åtkomst till läsa kartdata relaterade data från ett Azure Maps-konto. |
> | **Id** | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | **Åtgärder** |  |
> | *alternativet* |  |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft. Maps/Accounts/data/Read | Beviljar data Läs behörighet till ett Maps-konto. |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="azure-sentinel-contributor"></a>Azure Sentinel-deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Azure Sentinel-deltagare |
> | **Id** | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | **Åtgärder** |  |
> | Microsoft. SecurityInsights/* |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Sök med ny motor. |
> | Microsoft.OperationalInsights/workspaces/read | Hämtar en befintlig arbets yta |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* |  |
> | Microsoft.OperationsManagement/solutions/read | Ta slut på OMS-lösning |
> | Microsoft. OperationalInsights/arbets ytor/fråga/läsa | Köra frågor över data i arbets ytan |
> | Microsoft. OperationalInsights/arbets ytor/fråga/*/Read |  |
> | Microsoft. OperationalInsights/arbets ytor/data källor/läsa | Hämta data källor under en arbets yta. |
> | Microsoft. Insights/arbets böcker/* |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="azure-sentinel-reader"></a>Azure Sentinel-läsare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Azure Sentinel-läsare |
> | **Id** | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | **Åtgärder** |  |
> | Microsoft. SecurityInsights/*/Read |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Sök med ny motor. |
> | Microsoft.OperationalInsights/workspaces/read | Hämtar en befintlig arbets yta |
> | Microsoft. OperationalInsights/arbets ytor/savedSearches/Read | Hämtar en sparad Sök fråga |
> | Microsoft.OperationsManagement/solutions/read | Ta slut på OMS-lösning |
> | Microsoft. OperationalInsights/arbets ytor/fråga/läsa | Köra frågor över data i arbets ytan |
> | Microsoft. OperationalInsights/arbets ytor/fråga/*/Read |  |
> | Microsoft. OperationalInsights/arbets ytor/data källor/läsa | Hämta data källor under en arbets yta. |
> | Microsoft. Insights/arbets böcker/läsa | Läs en arbets bok |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="azure-sentinel-responder"></a>Azure Sentinel-svarare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Azure Sentinel-svarare |
> | **Id** | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | **Åtgärder** |  |
> | Microsoft. SecurityInsights/*/Read |  |
> | Microsoft. SecurityInsights/Cases/* |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Sök med ny motor. |
> | Microsoft.OperationalInsights/workspaces/read | Hämtar en befintlig arbets yta |
> | Microsoft. OperationalInsights/arbets ytor/data källor/läsa | Hämta data källor under en arbets yta. |
> | Microsoft. OperationalInsights/arbets ytor/savedSearches/Read | Hämtar en sparad Sök fråga |
> | Microsoft.OperationsManagement/solutions/read | Ta slut på OMS-lösning |
> | Microsoft. OperationalInsights/arbets ytor/fråga/läsa | Köra frågor över data i arbets ytan |
> | Microsoft. OperationalInsights/arbets ytor/fråga/*/Read |  |
> | Microsoft. OperationalInsights/arbets ytor/data källor/läsa | Hämta data källor under en arbets yta. |
> | Microsoft. Insights/arbets böcker/läsa | Läs en arbets bok |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="azure-service-bus-data-owner"></a>Azure Service Bus data ägare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Ger fullständig åtkomst till Azure Service Bus resurser. |
> | **Id** | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | **Åtgärder** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="azure-service-bus-data-receiver"></a>Azure Service Bus data mottagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Ger åtkomst till Azure Service Bus resurser. |
> | **Id** | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | **Åtgärder** |  |
> | Microsoft.ServiceBus/*/queues/read |  |
> | Microsoft. Service Bus/*/topics/Read |  |
> | Microsoft. Service Bus/*/topics/Subscriptions/Read |  |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft. Service Bus/*/Receive/Action |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="azure-service-bus-data-sender"></a>Azure Service Bus data avsändare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Tillåter att åtkomst till Azure Service Bus-resurser skickas. |
> | **Id** | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | **Åtgärder** |  |
> | Microsoft.ServiceBus/*/queues/read |  |
> | Microsoft. Service Bus/*/topics/Read |  |
> | Microsoft. Service Bus/*/topics/Subscriptions/Read |  |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft. Service Bus/*/Send/Action |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="azure-stack-registration-owner"></a>Azure Stack registrerings ägare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera Azure Stack-registreringar. |
> | **Id** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **Åtgärder** |  |
> | Microsoft. AzureStack/-registreringar/produkter/*/Action |  |
> | Microsoft.AzureStack/registrations/products/read | Hämtar egenskaperna för en Azure Stack Marketplace-produkt |
> | Microsoft.AzureStack/registrations/read | Hämtar egenskaperna för en Azure Stack registrering |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="backup-contributor"></a>Säkerhets kopierings deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera säkerhets kopierings tjänsten, men kan inte skapa valv och ge åtkomst till andra |
> | **Id** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Network/virtualNetworks/read | Hämta definition av virtuellt nätverk |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Hantera åtgärds resultat för säkerhets kopierings hantering |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Skapa och hantera säkerhets kopierings behållare i säkerhets kopierings infrastrukturer i Recovery Services Vault |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Uppdaterar behållar listan |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Skapa och hantera säkerhets kopierings jobb |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportera jobb |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Skapa och hantera metadata relaterade till säkerhets kopierings hantering |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Skapa och hantera resultat av säkerhets kopierings hanterings åtgärder |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | Skapa och hantera säkerhets kopierings principer |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Skapa och hantera objekt som kan säkerhets kopie ras |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Skapa och hantera säkerhetskopierade objekt |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Skapa och hantera behållare som innehåller säkerhets kopierings objekt |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Returnerar sammanfattningar för skyddade objekt och skyddade servrar för en Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Skapa och hantera certifikat som rör säkerhets kopiering i Recovery Services Vault |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Skapa och hantera utökad information som rör valvet |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Hämtar aviseringarna för Recovery Services-valvet. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | Med åtgärden Hämta valv hämtas ett objekt som representerar Azure-resursen av typen valv |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Skapa och hantera registrerade identiteter |
> | Microsoft.RecoveryServices/Vaults/usages/* | Skapa och hantera användning av Recovery Services valv |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Storage/storageAccounts/read | Returnerar listan över lagrings konton eller hämtar egenskaperna för det angivna lagrings kontot. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Verifiera åtgärd på skyddat objekt |
> | Microsoft.RecoveryServices/Vaults/write | Skapa valv-åtgärd skapar en Azure-resurs av typen valv |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Returnerar säkerhets kopierings åtgärdens status för Recovery Services valv. |
> | Microsoft. RecoveryServices/valv/backupEngines/läsa | Returnerar alla säkerhets kopierings hanterings servrar registrerade i valvet. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Hämta alla skydds bara behållare |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Kontrol lera säkerhets kopierings status för Recovery Services valv |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Validera funktioner |
> | Microsoft. RecoveryServices/valv/monitoringAlerts/Write | Löser aviseringen. |
> | Microsoft. RecoveryServices/Operations/Read | Åtgärd returnerar listan över åtgärder för en resurs leverantör |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Hämtar åtgärds status för en specifik åtgärd |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Lista alla säkerhets avsikter för säkerhets kopiering |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="backup-operator"></a>Säkerhetskopieringsoperator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera säkerhets kopierings tjänster, förutom att ta bort säkerhets kopiering, skapa valv och ge till gång till andra |
> | **Id** | 00c29273-979b-4161-815c-10b084fb9324 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Network/virtualNetworks/read | Hämta definition av virtuellt nätverk |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Returnerar status för åtgärden |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Hämtar resultat från utförd åtgärd på skydds behållare. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Utför säkerhets kopiering för skyddat objekt. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Hämtar resultat från utförd åtgärd på skyddade objekt. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Returnerar statusen för den åtgärd som utförts på skyddade objekt. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Returnerar objekt information om det skyddade objektet |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Etablera snabb objekts återställning för skyddat objekt |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Hämta återställnings punkter för skyddade objekt. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Återställa återställnings punkter för skyddade objekt. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Återkalla direkt objekt återställning för skyddat objekt |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Skapa ett säkerhetskopierat skyddat objekt |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Returnerar alla registrerade behållare |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Uppdaterar behållar listan |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Skapa och hantera säkerhets kopierings jobb |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportera jobb |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Skapa och hantera resultat av säkerhets kopierings hanterings åtgärder |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Hämta resultat från princip åtgärd. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Returnerar alla skydds principer |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Skapa och hantera objekt som kan säkerhets kopie ras |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Returnerar listan över alla skyddade objekt. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Returnerar alla behållare som hör till prenumerationen |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Returnerar sammanfattningar för skyddade objekt och skyddade servrar för en Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Med åtgärden Uppdatera resurs certifikat uppdateras certifikatet för resurs/valv-autentiseringsuppgifter. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Med åtgärden Hämta utökad information får du ett objekts utökade information som representerar Azure-resursen av typen? valv? |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Med åtgärden Hämta utökad information får du ett objekts utökade information som representerar Azure-resursen av typen? valv? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Hämtar aviseringarna för Recovery Services-valvet. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | Med åtgärden Hämta valv hämtas ett objekt som representerar Azure-resursen av typen valv |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Åtgärden hämta åtgärds resultat kan användas för att hämta åtgärds statusen och resultatet för åtgärden som skickats asynkront |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Med åtgärden Hämta behållare kan du hämta de behållare som är registrerade för en resurs. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Du kan använda åtgärden registrera tjänst behållare för att registrera en behållare med återställnings tjänsten. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Returnerar användnings information för ett Recovery Services-valv. |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Storage/storageAccounts/read | Returnerar listan över lagrings konton eller hämtar egenskaperna för det angivna lagrings kontot. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Verifiera åtgärd på skyddat objekt |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Returnerar säkerhets kopierings åtgärdens status för Recovery Services valv. |
> | Microsoft. RecoveryServices/valv/backupPolicies/åtgärder/Läs | Hämta status för princip åtgärd. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Skapar en registrerad behållare |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Gör förfrågan om arbets belastningar inom en behållare |
> | Microsoft. RecoveryServices/valv/backupEngines/läsa | Returnerar alla säkerhets kopierings hanterings servrar registrerade i valvet. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Skapa en säkerhets kopia av skydds avsikt |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Få en säkerhets kopie rad skydds avsikt |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Hämta alla skydds bara behållare |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Hämta alla objekt i en behållare |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Kontrol lera säkerhets kopierings status för Recovery Services valv |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Validera funktioner |
> | Microsoft. RecoveryServices/valv/monitoringAlerts/Write | Löser aviseringen. |
> | Microsoft. RecoveryServices/Operations/Read | Åtgärd returnerar listan över åtgärder för en resurs leverantör |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Hämtar åtgärds status för en specifik åtgärd |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Lista alla säkerhets avsikter för säkerhets kopiering |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="backup-reader"></a>Säkerhets kopierings läsare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan visa säkerhets kopierings tjänster, men kan inte göra ändringar |
> | **Id** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp är en intern åtgärd som används av tjänsten |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Returnerar status för åtgärden |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Hämtar resultat från utförd åtgärd på skydds behållare. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Hämtar resultat från utförd åtgärd på skyddade objekt. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Returnerar statusen för den åtgärd som utförts på skyddade objekt. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Returnerar objekt information om det skyddade objektet |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Hämta återställnings punkter för skyddade objekt. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Returnerar alla registrerade behållare |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Returnerar resultatet av jobb åtgärden. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Returnerar alla jobb objekt |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Exportera jobb |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Returnerar resultatet av säkerhets kopierings åtgärden för Recovery Services valvet. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Hämta resultat från princip åtgärd. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Returnerar alla skydds principer |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Returnerar listan över alla skyddade objekt. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Returnerar alla behållare som hör till prenumerationen |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Returnerar sammanfattningar för skyddade objekt och skyddade servrar för en Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Med åtgärden Hämta utökad information får du ett objekts utökade information som representerar Azure-resursen av typen? valv? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Hämtar aviseringarna för Recovery Services-valvet. |
> | Microsoft.RecoveryServices/Vaults/read | Med åtgärden Hämta valv hämtas ett objekt som representerar Azure-resursen av typen valv |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Åtgärden hämta åtgärds resultat kan användas för att hämta åtgärds statusen och resultatet för åtgärden som skickats asynkront |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Med åtgärden Hämta behållare kan du hämta de behållare som är registrerade för en resurs. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Returnerar lagrings konfiguration för Recovery Services valv. |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | Returnerar konfiguration för Recovery Services valv. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Returnerar säkerhets kopierings åtgärdens status för Recovery Services valv. |
> | Microsoft. RecoveryServices/valv/backupPolicies/åtgärder/Läs | Hämta status för princip åtgärd. |
> | Microsoft. RecoveryServices/valv/backupEngines/läsa | Returnerar alla säkerhets kopierings hanterings servrar registrerade i valvet. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Få en säkerhets kopie rad skydds avsikt |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Hämta alla objekt i en behållare |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Kontrol lera säkerhets kopierings status för Recovery Services valv |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft. RecoveryServices/valv/monitoringAlerts/Write | Löser aviseringen. |
> | Microsoft. RecoveryServices/Operations/Read | Åtgärd returnerar listan över åtgärder för en resurs leverantör |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Hämtar åtgärds status för en specifik åtgärd |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Lista alla säkerhets avsikter för säkerhets kopiering |
> | Microsoft.RecoveryServices/Vaults/usages/read | Returnerar användnings information för ett Recovery Services-valv. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="billing-reader"></a>Faktureringsläsare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Tillåter Läs åtkomst till fakturerings data |
> | **Id** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Billing/*/read | Läs fakturerings information |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Management/managementGroups/read | Visa en lista med hanterings grupper för den autentiserade användaren. |
> | Microsoft. CostManagement/*/Read |  |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="biztalk-contributor"></a>BizTalk-deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Gör att du kan hantera BizTalk Services, men inte till gång till dem. |
> | **Id** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.BizTalkServices/BizTalk/* | Skapa och hantera BizTalk Services |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviserings regler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="blockchain-member-node-access-preview"></a>Blockchain för medlems Node (för hands version)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Tillåter åtkomst till blockchain-medlems noder |
> | **Id** | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **Åtgärder** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Hämtar eller visar befintliga blockchain för medlems transaktioner. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action | Ansluter till en blockchain. |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="blueprint-contributor"></a>Skiss deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan hantera skiss definitioner, men tilldela dem inte. |
> | **Id** | 41077137-e803-4205-871c-5a86e6a753b4 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft. skiss/skiss/* | Skapa och hantera skiss definitioner eller skiss artefakter. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="blueprint-operator"></a>Skiss operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan tilldela befintliga publicerade ritningar, men kan inte skapa nya ritningar. OBS! detta fungerar endast om tilldelningen görs med en tilldelad hanterad identitet. |
> | **Id** | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft. skiss/blueprintAssignments/* | Skapa och hantera skiss tilldelningar. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="cdn-endpoint-contributor"></a>CDN-slutpunkts deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan hantera CDN-slutpunkter, men kan inte bevilja åtkomst till andra användare. |
> | **Id** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="cdn-endpoint-reader"></a>CDN-slutpunkt läsare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan visa CDN-slutpunkter, men kan inte göra ändringar. |
> | **Id** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft. CDN/profiler/slut punkter/*/Read |  |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="cdn-profile-contributor"></a>CDN-profil deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan hantera CDN-profiler och deras slut punkter, men kan inte bevilja åtkomst till andra användare. |
> | **Id** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft. CDN/profiler/* |  |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="cdn-profile-reader"></a>CDN profil läsare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan visa CDN-profiler och deras slut punkter, men kan inte göra ändringar. |
> | **Id** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="classic-network-contributor"></a>Klassisk nätverks deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Gör att du kan hantera klassiska nätverk, men inte till gång till dem. |
> | **Id** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs behörighet |
> | Microsoft.ClassicNetwork/* | Skapa och hantera klassiska nätverk |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="classic-storage-account-contributor"></a>Klassisk lagrings konto deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Gör att du kan hantera klassiska lagrings konton, men inte till gång till dem. |
> | **Id** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs behörighet |
> | Microsoft.ClassicStorage/storageAccounts/* | Skapa och hantera lagringskonton |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="classic-storage-account-key-operator-service-role"></a>Klassisk lagrings kontots nyckel operatörs tjänst roll
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Klassiska lagrings konto nyckel operatörer får lista och återskapa nycklar på klassiska lagrings konton |
> | **Id** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **Åtgärder** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Visar åtkomst nycklar för lagrings kontona. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Återskapar befintliga åtkomst nycklar för lagrings kontot. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="classic-virtual-machine-contributor"></a>Klassisk virtuell dator deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera klassiska virtuella datorer, men inte åtkomst till dem, inte det virtuella nätverk eller lagrings konto som de är anslutna till. |
> | **Id** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs behörighet |
> | Microsoft. ClassicCompute/domän namn/* | Skapa och hantera klassiska beräknings domän namn |
> | Microsoft. ClassicCompute/virtualMachines/* | Skapa och hantera virtuella datorer |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Länka en reserverad IP |
> | Microsoft.ClassicNetwork/reservedIps/read | Hämtar de reserverade IP-adresserna |
> | Microsoft. ClassicNetwork/virtualNetworks/JOIN/åtgärd | Ansluter till det virtuella nätverket. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Hämta det virtuella nätverket. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | Returnerar lagrings konto disken. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | Returnerar lagrings konto avbildningen. Föråldrad. Använd Microsoft. ClassicStorage/storageAccounts/vmImages) |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Visar åtkomst nycklar för lagrings kontona. |
> | Microsoft.ClassicStorage/storageAccounts/read | Returnera lagrings kontot med det aktuella kontot. |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="cognitive-services-contributor"></a>Cognitive Services deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Gör att du kan skapa, läsa, uppdatera, ta bort och hantera nycklar för Cognitive Services. |
> | **Id** | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.CognitiveServices/* |  |
> | Microsoft. features/features/Read | Hämtar funktionerna i en prenumeration. |
> | Microsoft. features/providers/features/Read | Hämtar funktionen för en prenumeration i en specifik resurs leverantör. |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft.Insights/diagnosticSettings/* | Skapar, uppdaterar eller läser in diagnostikinställningar för Analysis Server |
> | Microsoft.Insights/logDefinitions/read | Läs logg definitioner |
> | Microsoft.Insights/metricdefinitions/read | Läs mått definitioner |
> | Microsoft. Insights/Metrics/Read | Läs mått |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/deployments/operations/read | Hämtar eller visar distributions åtgärder. |
> | Microsoft.Resources/subscriptions/operationresults/read | Hämta prenumerations åtgärds resultatet. |
> | Microsoft. Resources/Subscriptions/Read | Hämtar listan över prenumerationer. |
> | Microsoft. Resources/Subscriptions/ResourceGroups/distributions/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="cognitive-services-data-reader-preview"></a>Cognitive Services data läsare (förhands granskning)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Gör att du kan läsa Cognitive Services data. |
> | **Id** | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | **Åtgärder** |  |
> | *alternativet* |  |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft. CognitiveServices/*/Read |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="cognitive-services-user"></a>Cognitive Services användare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Gör att du kan läsa och Visa nycklar för Cognitive Services. |
> | **Id** | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Åtgärder** |  |
> | Microsoft. CognitiveServices/*/Read |  |
> | Microsoft. CognitiveServices/Accounts/listnycklar/Action | Lista nycklar |
> | Microsoft.Insights/alertRules/read | Läs en klassisk måtta avisering |
> | Microsoft. Insights/diagnosticSettings/Read | Läs en inställning för resurs diagnostik |
> | Microsoft.Insights/logDefinitions/read | Läs logg definitioner |
> | Microsoft.Insights/metricdefinitions/read | Läs mått definitioner |
> | Microsoft. Insights/Metrics/Read | Läs mått |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft.Resources/deployments/operations/read | Hämtar eller visar distributions åtgärder. |
> | Microsoft.Resources/subscriptions/operationresults/read | Hämta prenumerations åtgärds resultatet. |
> | Microsoft. Resources/Subscriptions/Read | Hämtar listan över prenumerationer. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft.CognitiveServices/* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="cosmos-db-account-reader-role"></a>Cosmos DB konto läsar roll
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan läsa Azure Cosmos DB konto data. Se [DocumentDB Account Contributor](#documentdb-account-contributor) för att hantera Azure Cosmos DB-konton. |
> | **Id** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar kan läsa behörigheter för varje användare |
> | Microsoft.DocumentDB/*/read | Läs valfri samling |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Läser databas kontots ReadOnly-nycklar. |
> | Microsoft.Insights/MetricDefinitions/read | Läs mått definitioner |
> | Microsoft.Insights/Metrics/read | Läs mått |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="cosmos-db-operator"></a>Cosmos DB operatör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera Azure Cosmos DB konton, men inte komma åt data i dem. Förhindrar åtkomst till konto nycklar och anslutnings strängar. |
> | **Id** | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | **Åtgärder** |  |
> | Microsoft.DocumentDb/databaseAccounts/* |  |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Ansluter till en resurs som lagrings konto eller SQL-databas till ett undernät. Det går inte att avisera. |
> | **NotActions** |  |
> | Microsoft. DocumentDB/databaseAccounts/readonlyKeys/* |  |
> | Microsoft. DocumentDB/databaseAccounts/regenerateKey/* |  |
> | Microsoft. DocumentDB/databaseAccounts/Listnycklar/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="cosmosbackupoperator"></a>CosmosBackupOperator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan skicka en Restore-begäran för en Cosmos DB databas eller en behållare för ett konto |
> | **Id** | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | **Åtgärder** |  |
> | Microsoft. DocumentDB/databaseAccounts/säkerhets kopiering/åtgärd | Skicka en begäran om att konfigurera säkerhets kopiering |
> | Microsoft. DocumentDB/databaseAccounts/Restore/Action | Skicka en begäran om återställning |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="cost-management-contributor"></a>Cost Management deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan visa kostnader och hantera kostnads konfiguration (t. ex. budgetar, exporter) |
> | **Id** | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | **Åtgärder** |  |
> | Microsoft. förbrukning/* |  |
> | Microsoft.CostManagement/* |  |
> | Microsoft. fakturering/billingPeriods/Läs |  |
> | Microsoft. Resources/Subscriptions/Read | Hämtar listan över prenumerationer. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | Microsoft.Advisor/configurations/read | Hämta konfigurationer |
> | Microsoft. Advisor/rekommendationer/läsa | Läser rekommendationer |
> | Microsoft.Management/managementGroups/read | Visa en lista med hanterings grupper för den autentiserade användaren. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="cost-management-reader"></a>Cost Management läsare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan visa kostnads data och konfiguration (t. ex. budgetar, exporter) |
> | **Id** | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | **Åtgärder** |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft. CostManagement/*/Read |  |
> | Microsoft. fakturering/billingPeriods/Läs |  |
> | Microsoft. Resources/Subscriptions/Read | Hämtar listan över prenumerationer. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | Microsoft.Advisor/configurations/read | Hämta konfigurationer |
> | Microsoft. Advisor/rekommendationer/läsa | Läser rekommendationer |
> | Microsoft.Management/managementGroups/read | Visa en lista med hanterings grupper för den autentiserade användaren. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="data-box-contributor"></a>Data Box-enhet deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera allt under Data Box-enhet tjänst, förutom att ge till gång till andra. |
> | **Id** | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | Microsoft. data-/* |  |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="data-box-reader"></a>Data Box-enhet läsare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera Data Box-enhet tjänst, förutom att skapa order-eller redigerings beställnings detaljer och ge åtkomst till andra. |
> | **Id** | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft. data-och/Read |  |
> | Microsoft. data-/jobb/listsecrets/åtgärd |  |
> | Microsoft. data-/jobb/listcredentials/åtgärd | Visar en lista med okrypterade autentiseringsuppgifter relaterade till beställningen. |
> | Microsoft. data-/plats/availableSkus/åtgärd | Den här metoden returnerar listan över tillgängliga SKU: er. |
> | Microsoft. data-/plats/validateAddress/åtgärd | Verifierar leverans adressen och ger alternativa adresser om det finns några. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="data-factory-contributor"></a>Data Factory deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Skapa och hantera data fabriker, samt underordnade resurser i dem. |
> | **Id** | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.DataFactory/dataFactories/* | Skapa och hantera data fabriker och underordnade resurser i dem. |
> | Microsoft. DataFactory/factors/* | Skapa och hantera data fabriker och underordnade resurser i dem. |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviserings regler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="data-lake-analytics-developer"></a>Data Lake Analytics utvecklare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig skicka, övervaka och hantera dina egna jobb, men inte skapa eller ta bort Data Lake Analytics konton. |
> | **Id** | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft. BigAnalytics/Accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft. BigAnalytics/Accounts/TakeOwnership/Action |  |
> | Microsoft. BigAnalytics/konton/Skriv |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | Ta bort ett DataLakeAnalytics-konto. |
> | Microsoft. DataLakeAnalytics/Accounts/TakeOwnership/Action | Bevilja behörighet att avbryta jobb som skickats av andra användare. |
> | Microsoft.DataLakeAnalytics/accounts/Write | Skapa eller uppdatera ett DataLakeAnalytics-konto. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Skapa eller uppdatera ett länkat DataLakeStore-konto för ett DataLakeAnalytics-konto. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Ta bort länken mellan ett DataLakeStore-konto och ett DataLakeAnalytics-konto. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Skapa eller uppdatera ett länkat lagrings konto för ett DataLakeAnalytics-konto. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Ta bort länken till ett lagrings konto från ett DataLakeAnalytics-konto. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Skapa eller uppdatera en brand Väggs regel. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Ta bort en brand Väggs regel. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Skapa eller uppdatera en beräknings princip. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Ta bort en beräknings princip. |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="data-purger"></a>Data rensning
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan rensa analys data |
> | **Id** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **Åtgärder** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft. Insights/komponenter/rensning/åtgärd | Rensar data från Application Insights |
> | Microsoft. OperationalInsights/arbets ytor/*/Read |  |
> | Microsoft. OperationalInsights/arbets ytor/rensning/åtgärd | Ta bort angivna data från arbets ytan |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="devtest-labs-user"></a>DevTest Labs-användare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig ansluta, starta, starta om och stänga av dina virtuella datorer i din Azure DevTest Labs. |
> | **Id** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Compute/availabilitySets/read | Hämta egenskaperna för en tillgänglighets uppsättning |
> | Microsoft.Compute/virtualMachines/*/read | Läsa egenskaperna för en virtuell dator (VM-storlekar, körnings status, VM-tillägg osv.) |
> | Microsoft.Compute/virtualMachines/deallocate/action | Stänger av den virtuella datorn och frigör beräknings resurserna |
> | Microsoft.Compute/virtualMachines/read | Hämta egenskaperna för en virtuell dator |
> | Microsoft.Compute/virtualMachines/restart/action | Startar om den virtuella datorn |
> | Microsoft.Compute/virtualMachines/start/action | Startar den virtuella datorn |
> | Microsoft.DevTestLab/*/read | Läsa egenskaperna för ett labb |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Anspråk på en slumpmässig virtuell dator i labbet. |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Skapa virtuella datorer i ett labb. |
> | Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action | Se till att den aktuella användaren har en giltig profil i labbet. |
> | Microsoft.DevTestLab/labs/formulas/delete | Ta bort formler. |
> | Microsoft.DevTestLab/labs/formulas/read | Läs formler. |
> | Microsoft.DevTestLab/labs/formulas/write | Lägg till eller ändra formler. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Utvärderar labb princip. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Bli ägare till en befintlig virtuell dator |
> | Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action | Visar en lista över tillämpliga start-/stopp scheman, om det finns några. |
> | Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action | Hämtar en sträng som representerar innehållet i RDP-filen för den virtuella datorn |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Ansluter till en backend-adresspool för belastnings utjämning. Det går inte att avisera. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Ansluter en inkommande NAT-regel för belastningsutjämnare. Det går inte att avisera. |
> | Microsoft.Network/networkInterfaces/*/read | Läs egenskaperna för ett nätverks gränssnitt (till exempel alla belastningsutjämnare som nätverks gränssnittet är en del av) |
> | Microsoft.Network/networkInterfaces/join/action | Ansluter en virtuell dator till ett nätverks gränssnitt. Det går inte att avisera. |
> | Microsoft.Network/networkInterfaces/read | Hämtar en definition för nätverks gränssnitt.  |
> | Microsoft.Network/networkInterfaces/write | Skapar ett nätverks gränssnitt eller uppdaterar ett befintligt nätverks gränssnitt.  |
> | Microsoft.Network/publicIPAddresses/*/read | Läsa egenskaperna för en offentlig IP-adress |
> | Microsoft.Network/publicIPAddresses/join/action | Ansluter till en offentlig IP-adress. Det går inte att avisera. |
> | Microsoft.Network/publicIPAddresses/read | Hämtar en offentlig IP-adress definition. |
> | Microsoft. Network/virtualNetworks/subnets/Join/Action | Ansluter till ett virtuellt nätverk. Det går inte att avisera. |
> | Microsoft.Resources/deployments/operations/read | Hämtar eller visar distributions åtgärder. |
> | Microsoft. Resources/Deployments/Read | Hämtar eller visar distributioner. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft. Storage/storageAccounts/Listnycklar/åtgärd | Returnerar åtkomst nycklar för det angivna lagrings kontot. |
> | **NotActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | Visar en lista över tillgängliga storlekar som den virtuella datorn kan uppdateras till |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="dns-zone-contributor"></a>DNS-zon deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Gör att du kan hantera DNS-zoner och post uppsättningar i Azure DNS, men du kan inte styra vem som har åtkomst till dem. |
> | **Id** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviserings regler |
> | Microsoft.Network/dnsZones/* | Skapa och hantera DNS-zoner och-poster |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="documentdb-account-contributor"></a>DocumentDB-konto deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan hantera Azure Cosmos DB-konton. Azure Cosmos DB är tidigare känt som DocumentDB. |
> | **Id** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.DocumentDb/databaseAccounts/* | Skapa och hantera Azure Cosmos DB-konton |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviserings regler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Ansluter till en resurs som lagrings konto eller SQL-databas till ett undernät. Det går inte att avisera. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="eventgrid-eventsubscription-contributor"></a>EventGrid EventSubscription-deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera EventGrid händelse prenumerations åtgärder. |
> | **Id** | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft. EventGrid/eventSubscriptions/* |  |
> | Microsoft. EventGrid/topicTypes/eventSubscriptions/Read | Lista globala händelse prenumerationer efter typ av ämne |
> | Microsoft. EventGrid/locations/eventSubscriptions/Read | Lista regionala händelse prenumerationer |
> | Microsoft. EventGrid/locations/topicTypes/eventSubscriptions/Read | Lista regionala händelse prenumerationer efter TopicType |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription-läsare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig läsa EventGrid händelse prenumerationer. |
> | **Id** | 2414bbcf-6497-4faf-8c65-045460748405 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft. EventGrid/eventSubscriptions/Read | Läs en eventSubscription |
> | Microsoft. EventGrid/topicTypes/eventSubscriptions/Read | Lista globala händelse prenumerationer efter typ av ämne |
> | Microsoft. EventGrid/locations/eventSubscriptions/Read | Lista regionala händelse prenumerationer |
> | Microsoft. EventGrid/locations/topicTypes/eventSubscriptions/Read | Lista regionala händelse prenumerationer efter TopicType |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="hdinsight-cluster-operator"></a>HDInsight-kluster operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Gör att du kan läsa och ändra HDInsight-klusterkonfigurationer. |
> | **Id** | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | **Åtgärder** |  |
> | Microsoft. HDInsight/*/Read |  |
> | Microsoft. HDInsight/kluster/getGatewaySettings/åtgärd | Hämta Gateway-inställningar för HDInsight-kluster |
> | Microsoft. HDInsight/kluster/updateGatewaySettings/åtgärd | Uppdatera Gateway-inställningar för HDInsight-kluster |
> | Microsoft. HDInsight/kluster/konfigurationer/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Resources/deployments/operations/read | Hämtar eller visar distributions åtgärder. |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="hdinsight-domain-services-contributor"></a>HDInsight Domain Services-deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan läsa, skapa, ändra och ta bort åtgärder för domän tjänster som krävs för HDInsight-Enterprise Security Package |
> | **Id** | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | **Åtgärder** |  |
> | Microsoft. AAD/*-/Read |  |
> | Microsoft. AAD/domainServices/*/Read |  |
> | Microsoft. AAD/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="intelligent-systems-account-contributor"></a>Konto deltagare i Intelligent Systems
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Gör att du kan hantera intelligenta system konton, men inte åtkomst till dem. |
> | **Id** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviserings regler |
> | Microsoft.IntelligentSystems/accounts/* | Skapa och hantera intelligenta system konton |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="key-vault-contributor"></a>Key Vault deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera nyckel valv, men inte åtkomst till dem. |
> | **Id** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft.KeyVault/* |  |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Rensa ett ej permanent borttaget nyckel valv |
> | Microsoft.KeyVault/hsmPools/* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="lab-creator"></a>Labb skapare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Gör att du kan skapa, hantera och ta bort dina hanterade labb under dina Azure Lab-konton. |
> | **Id** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Skapa ett labb i ett labb konto. |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | Hämta regional tillgänglighets information för varje storleks kategori som kon figurer ATS under ett labb konto |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="log-analytics-contributor"></a>Log Analytics Contributor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Log Analytics deltagare kan läsa alla övervaknings data och redigera övervaknings inställningar. Genom att redigera övervaknings inställningarna lägger du till VM-tillägget till virtuella datorer. läsning av lagrings konto nycklar för att kunna konfigurera samling av loggar från Azure Storage. Skapa och konfigurera Automation-konton. lägga till lösningar. och konfigurera Azure Diagnostics på alla Azure-resurser. |
> | **Id** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | **Åtgärder** |  |
> | */read | Läs resurser av alla typer, förutom hemligheter. |
> | Microsoft. Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Visar åtkomst nycklar för lagrings kontona. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft.Insights/diagnosticSettings/* | Skapar, uppdaterar eller läser in diagnostikinställningar för Analysis Server |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft. Resources/Subscriptions/ResourceGroups/distributions/* |  |
> | Microsoft. Storage/storageAccounts/Listnycklar/åtgärd | Returnerar åtkomst nycklar för det angivna lagrings kontot. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="log-analytics-reader"></a>Log Analytics Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Log Analytics läsaren kan visa och söka i alla övervaknings data samt Visa övervaknings inställningar, inklusive Visa konfigurationen av Azure Diagnostics på alla Azure-resurser. |
> | **Id** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Åtgärder** |  |
> | */read | Läs resurser av alla typer, förutom hemligheter. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Sök med ny motor. |
> | Microsoft.OperationalInsights/workspaces/search/action | Kör en Sök fråga |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Hämtar de delade nycklarna för arbets ytan. Dessa nycklar används för att ansluta Microsoft Operational Insights-agenter till arbets ytan. |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="logic-app-contributor"></a>Logic app-deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera Logi Kap par, men ändra inte åtkomsten till dem. |
> | **Id** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Visar åtkomst nycklar för lagrings kontona. |
> | Microsoft.ClassicStorage/storageAccounts/read | Returnera lagrings kontot med det aktuella kontot. |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft.Insights/metricAlerts/* |  |
> | Microsoft.Insights/diagnosticSettings/* | Skapar, uppdaterar eller läser in diagnostikinställningar för Analysis Server |
> | Microsoft.Insights/logdefinitions/* | Den här behörigheten krävs för användare som behöver åtkomst till aktivitets loggar via portalen. Lista logg kategorier i aktivitets loggen. |
> | Microsoft.Insights/metricDefinitions/* | Läs mått definitioner (lista över tillgängliga mått typer för en resurs). |
> | Microsoft.Logic/* | Hanterar Logic Apps resurser. |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/operationresults/read | Hämta prenumerations åtgärds resultatet. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Storage/storageAccounts/listkeys/action | Returnerar åtkomst nycklar för det angivna lagrings kontot. |
> | Microsoft.Storage/storageAccounts/read | Returnerar listan över lagrings konton eller hämtar egenskaperna för det angivna lagrings kontot. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | Microsoft.Web/connectionGateways/* | Skapa och hantera en anslutnings-Gateway. |
> | Microsoft.Web/connections/* | Skapa och hantera en anslutning. |
> | Microsoft.Web/customApis/* | Skapar och hanterar en anpassad API. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Hämta egenskaperna för en App Service plan |
> | Microsoft.Web/sites/functions/listSecrets/action | Visa en lista över funktions hemligheter. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="logic-app-operator"></a>Logic app-operatör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig läsa, aktivera och inaktivera Logi Kap par, men inte redigera eller uppdatera dem. |
> | **Id** | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Insights/alertRules/*/read | Läs Insights-aviserings regler |
> | Microsoft. Insights/metricAlerts/*/Read |  |
> | Microsoft.Insights/diagnosticSettings/*/read | Hämtar diagnostikinställningar för Logic Apps |
> | Microsoft.Insights/metricDefinitions/*/read | Hämtar tillgängliga mått för Logic Apps. |
> | Microsoft.Logic/*/read | Läser Logic Apps-resurser. |
> | Microsoft. Logic/-arbets flöden/inaktivera/åtgärd | Inaktiverar arbets flödet. |
> | Microsoft. Logic/-arbets flöden/aktivera/åtgärd | Aktiverar arbetsflödet |
> | Microsoft.Logic/workflows/validate/action | Verifierar arbets flödet. |
> | Microsoft.Resources/deployments/operations/read | Hämtar eller visar distributions åtgärder. |
> | Microsoft.Resources/subscriptions/operationresults/read | Hämta prenumerations åtgärds resultatet. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | Microsoft.Web/connectionGateways/*/read | Läsa anslutnings-gatewayer. |
> | Microsoft.Web/connections/*/read | Läsa anslutningar. |
> | Microsoft.Web/customApis/*/read | Läs anpassat API. |
> | Microsoft.Web/serverFarms/read | Hämta egenskaperna för en App Service plan |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="managed-application-operator-role"></a>Rollen hanterad program operatör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Gör att du kan läsa och utföra åtgärder på hanterade program resurser |
> | **Id** | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | **Åtgärder** |  |
> | */read | Läs resurser av alla typer, förutom hemligheter. |
> | Microsoft. Solutions/program/läsa | Hämtar en lista över program. |
> | Microsoft. Solutions/*/Action |  |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="managed-applications-reader"></a>Läsare för hanterade program
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig läsa resurser i en hanterad app och begära JIT-åtkomst. |
> | **Id** | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | **Åtgärder** |  |
> | */read | Läs resurser av alla typer, förutom hemligheter. |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Solutions/jitRequests/* |  |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="managed-identity-contributor"></a>Hanterad identitets deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Skapa, läsa, uppdatera och ta bort användare tilldelad identitet |
> | **Id** | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | **Åtgärder** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/read | Hämtar en befintlig användare tilldelad identitet |
> | Microsoft.ManagedIdentity/userAssignedIdentities/write | Skapar en ny tilldelad identitet eller uppdaterar de taggar som är associerade med en befintlig användare som tilldelats identiteten |
> | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Tar bort en befintlig användare tilldelad identitet |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="managed-identity-operator"></a>Hanterad identitets operator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Läs och tilldela en tilldelad identitet |
> | **Id** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Åtgärder** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="managed-services-registration-assignment-delete-role"></a>Borttagnings roll för registrering av hanterade tjänster
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Ta bort roll för registrering av hanterade tjänster för att hantera klient organisations användare kan ta bort den registrerings tilldelning som tilldelats till klienten. |
> | **Id** | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | **Åtgärder** |  |
> | Microsoft. ManagedServices/registrationAssignments/Read | Hämtar en lista över uppgifter för registrering av hanterade tjänster. |
> | Microsoft. ManagedServices/registrationAssignments/Delete | Tar bort registrering av hanterade tjänster. |
> | Microsoft. ManagedServices/operationStatuses/Read | Läser åtgärds statusen för resursen. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="management-group-contributor"></a>Deltagare i hanterings grupp
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Rollen hanterings grupp deltagare |
> | **Id** | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | **Åtgärder** |  |
> | Microsoft.Management/managementGroups/delete | Ta bort hanterings grupp. |
> | Microsoft.Management/managementGroups/read | Visa en lista med hanterings grupper för den autentiserade användaren. |
> | Microsoft.Management/managementGroups/subscriptions/delete | Ta bort prenumerationen från hanterings gruppen. |
> | Microsoft.Management/managementGroups/subscriptions/write | Kopplar en befintlig prenumeration till hanterings gruppen. |
> | Microsoft.Management/managementGroups/write | Skapa eller uppdatera en hanterings grupp. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="management-group-reader"></a>Hanterings grupp läsare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Rollen hanterings grupp läsare |
> | **Id** | ac63b705-f282-497d-ac71-919bf39d939d |
> | **Åtgärder** |  |
> | Microsoft.Management/managementGroups/read | Visa en lista med hanterings grupper för den autentiserade användaren. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="monitoring-contributor"></a>Övervaknings deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan läsa alla övervaknings data och redigera övervaknings inställningar. Se även [komma igång med roller, behörigheter och säkerhet med Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Åtgärder** |  |
> | */read | Läs resurser av alla typer, förutom hemligheter. |
> | Microsoft. AlertsManagement/Alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft. Insights/actiongroups/* |  |
> | Microsoft.Insights/activityLogAlerts/* |  |
> | Microsoft.Insights/AlertRules/* | Läsa/skriva/ta bort aviserings regler. |
> | Microsoft.Insights/components/* | Läsa/skriva/ta bort Application Insights-komponenter. |
> | Microsoft.Insights/DiagnosticSettings/* | Läsa/skriva/ta bort diagnostikinställningar. |
> | Microsoft.Insights/eventtypes/* | Visa lista över aktivitets logg händelser (hanterings händelser) i en prenumeration. Den här behörigheten gäller för både program mässig och Portal åtkomst till aktivitets loggen. |
> | Microsoft.Insights/LogDefinitions/* | Den här behörigheten krävs för användare som behöver åtkomst till aktivitets loggar via portalen. Lista logg kategorier i aktivitets loggen. |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | Läs mått definitioner (lista över tillgängliga mått typer för en resurs). |
> | Microsoft.Insights/Metrics/* | Läs mått för en resurs. |
> | Microsoft.Insights/Register/Action | Registrera Microsoft Insights-providern |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Läs/skriv/ta bort Application Insights webbtester. |
> | Microsoft. Insights/arbets böcker/* |  |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Läsa/skriva/ta bort lösnings paket för Log Analytics. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Läs/skriv/ta bort sparade Log Analytics-sökningar. |
> | Microsoft.OperationalInsights/workspaces/search/action | Kör en Sök fråga |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Hämtar de delade nycklarna för arbets ytan. Dessa nycklar används för att ansluta Microsoft Operational Insights-agenter till arbets ytan. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Läsa/skriva/ta bort insikter för Log Analytics-lagring. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | Microsoft. WorkloadMonitor/monitors/* |  |
> | Microsoft.WorkloadMonitor/notificationSettings/* |  |
> | Microsoft. AlertsManagement/smartDetectorAlertRules/* |  |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="monitoring-metrics-publisher"></a>Övervaknings mått utgivare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Möjliggör publicering av mått mot Azure-resurser |
> | **Id** | 3913510d-42f4-4e42-8a64-420c390055eb |
> | **Åtgärder** |  |
> | Microsoft.Insights/Register/Action | Registrera Microsoft Insights-providern |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft. Insights/Metrics/Write | Skriv mått |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="monitoring-reader"></a>Övervaknings läsare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Kan läsa alla övervaknings data (mått, loggar osv.). Se även [komma igång med roller, behörigheter och säkerhet med Azure Monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Id** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **Åtgärder** |  |
> | */read | Läs resurser av alla typer, förutom hemligheter. |
> | Microsoft.OperationalInsights/workspaces/search/action | Kör en Sök fråga |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="network-contributor"></a>Nätverks deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Gör att du kan hantera nätverk, men inte till gång till dem. |
> | **Id** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviserings regler |
> | Microsoft.Network/* | Skapa och hantera nätverk |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="new-relic-apm-account-contributor"></a>Ny Relic APM-konto deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera New Relic Application Performance Management konton och program, men inte till gång till dem. |
> | **Id** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | NewRelic. APM/Accounts/* |  |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="policy-insights-data-writer-preview"></a>Data skrivare för princip insikter (för hands version)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Tillåter Läs åtkomst till resurs principer och Skriv behörighet till resurs komponent princip händelser. |
> | **Id** | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | **Åtgärder** |  |
> | Microsoft. Authorization/policyassignments/Read | Hämta information om en princip tilldelning. |
> | Microsoft. Authorization/policydefinitions/Read | Hämta information om en princip definition. |
> | Microsoft. Authorization/policysetdefinitions/Read | Hämta information om en princip uppsättnings definition. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft. PolicyInsights/checkDataPolicyCompliance/Action | Kontrol lera status för efterlevnad för en specifik komponent mot data policys. |
> | Microsoft. PolicyInsights/policyEvents/logDataEvents/Action | Logga händelser för resurs komponents princip. |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="reader-and-data-access"></a>Läsare och data åtkomst
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Gör att du kan visa allting men du kan inte ta bort eller skapa ett lagrings konto eller en resurs som saknas. Den kommer också att tillåta Läs-/skriv åtkomst till alla data som finns i ett lagrings konto via åtkomst till lagrings konto nycklar. |
> | **Id** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **Åtgärder** |  |
> | Microsoft. Storage/storageAccounts/Listnycklar/åtgärd | Returnerar åtkomst nycklar för det angivna lagrings kontot. |
> | Microsoft.Storage/storageAccounts/ListAccountSas/action | Returnerar kontots SAS-token för det angivna lagrings kontot. |
> | Microsoft.Storage/storageAccounts/read | Returnerar listan över lagrings konton eller hämtar egenskaperna för det angivna lagrings kontot. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="redis-cache-contributor"></a>Redis Cache deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera Redis-cacheer, men inte till gång till dem. |
> | **Id** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Cache/redis/* | Skapa och hantera Redis-cache |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviserings regler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="resource-policy-contributor"></a>Deltagare för resursprincip
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Användare med behörighet att skapa/ändra resurs principer, skapa support ärende och läsa resurser/hierarki. |
> | **Id** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | **Åtgärder** |  |
> | */read | Läs resurser av alla typer, förutom hemligheter. |
> | Microsoft. Authorization/policyassignments/* | Skapa och hantera princip tilldelningar |
> | Microsoft. Authorization/policydefinitions/* | Skapa och hantera princip definitioner |
> | Microsoft.Authorization/policysetdefinitions/* | Skapa och hantera princip uppsättningar |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="scheduler-job-collections-contributor"></a>Jobb samlings deltagare i Scheduler
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Gör att du kan hantera jobb samlingar i Scheduler, men inte till gång till dem. |
> | **Id** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviserings regler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Scheduler/jobcollections/* | Skapa och hantera jobb samlingar |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="search-service-contributor"></a>Search Service deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera Sök tjänster, men inte till gång till dem. |
> | **Id** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviserings regler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Search/searchServices/* | Skapa och hantera Sök tjänster |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="security-admin"></a>Säkerhetsadministratör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Endast i Security Center: kan visa säkerhets principer, Visa säkerhets tillstånd, redigera säkerhets principer, Visa aviseringar och rekommendationer, ignorera aviseringar och rekommendationer |
> | **Id** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft. Authorization/policyAssignments/* | Skapa och hantera princip tilldelningar |
> | Microsoft. Authorization/policyDefinitions/* | Skapa och hantera princip definitioner |
> | Microsoft.Authorization/policySetDefinitions/* | Skapa och hantera princip uppsättningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviserings regler |
> | Microsoft.Management/managementGroups/read | Visa en lista med hanterings grupper för den autentiserade användaren. |
> | Microsoft.operationalInsights/workspaces/*/read | Visa Log Analytics-data |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Security/* |  |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="security-manager-legacy"></a>Säkerhets hanterare (bakåtkompatibelt)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Detta är en äldre roll. Använd säkerhets administratör istället |
> | **Id** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.ClassicCompute/*/read | Läs konfigurations information klassiska virtuella datorer |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Skriv konfiguration för klassiska virtuella datorer |
> | Microsoft.ClassicNetwork/*/read | Läs konfigurations information om klassiskt nätverk |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviserings regler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Security/* | Skapa och hantera säkerhets komponenter och principer |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="security-reader"></a>Säkerhetsläsare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Endast i Security Center: kan visa rekommendationer och aviseringar, Visa säkerhets principer, Visa säkerhets tillstånd, men kan inte göra ändringar |
> | **Id** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviserings regler |
> | Microsoft.operationalInsights/workspaces/*/read | Visa Log Analytics-data |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Security/*/read | Läsa säkerhets komponenter och principer |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | Microsoft.Management/managementGroups/read | Visa en lista med hanterings grupper för den autentiserade användaren. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="site-recovery-contributor"></a>Site Recovery deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera Site Recovery tjänst förutom att skapa valv och roll tilldelning |
> | **Id** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviserings regler |
> | Microsoft.Network/virtualNetworks/read | Hämta definition av virtuellt nätverk |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp är en intern åtgärd som används av tjänsten |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | Allocatedstamp är en intern åtgärd som används av tjänsten |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Med åtgärden Uppdatera resurs certifikat uppdateras certifikatet för resurs/valv-autentiseringsuppgifter. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Skapa och hantera utökad information som rör valvet |
> | Microsoft.RecoveryServices/Vaults/read | Med åtgärden Hämta valv hämtas ett objekt som representerar Azure-resursen av typen valv |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Skapa och hantera registrerade identiteter |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Skapa eller uppdatera aviserings inställningar för replikering |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Läs eventuella händelser |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | Skapa och hantera infrastruktur resurser |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Skapa och hantera jobb för replikering |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | Skapa och hantera principer för replikering |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Skapa och hantera återställnings planer |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Skapa och hantera lagrings konfiguration för Recovery Services valv |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Returnerar användnings information för ett Recovery Services-valv. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Valv-token kan användas för att hämta valv-token för Server dels åtgärder på valvnivå. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Läs aviseringar för Recovery Services-valvet |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Storage/storageAccounts/read | Returnerar listan över lagrings konton eller hämtar egenskaperna för det angivna lagrings kontot. |
> | Microsoft. RecoveryServices/valv/replicationOperationStatus/läsa | Läs eventuell åtgärds status för valvets replikering |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="site-recovery-operator"></a>Site Recovery operatör
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig redundansväxla och failback men inte utföra andra Site Recovery hanterings åtgärder |
> | **Id** | 494ae006-db33-4328-bf46-533a6560a3ca |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviserings regler |
> | Microsoft.Network/virtualNetworks/read | Hämta definition av virtuellt nätverk |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp är en intern åtgärd som används av tjänsten |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | Allocatedstamp är en intern åtgärd som används av tjänsten |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Med åtgärden Hämta utökad information får du ett objekts utökade information som representerar Azure-resursen av typen? valv? |
> | Microsoft.RecoveryServices/Vaults/read | Med åtgärden Hämta valv hämtas ett objekt som representerar Azure-resursen av typen valv |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Åtgärden hämta åtgärds resultat kan användas för att hämta åtgärds statusen och resultatet för åtgärden som skickats asynkront |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Med åtgärden Hämta behållare kan du hämta de behållare som är registrerade för en resurs. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Läs eventuella aviserings inställningar |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Läs eventuella händelser |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Kontrollerar konsekvensen för infrastrukturen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Läs eventuella infrastruktur resurser |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Associera gatewayen igen |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Förnya certifikat för infrastruktur resurser |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Läs alla nätverk |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Läs eventuella nätverks mappningar |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Läs eventuella skydds behållare |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Läs alla objekt som ska skyddas |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Tillämpa återställnings punkt |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Genomför redundans |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Planerad redundans |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Läs alla skyddade objekt |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Läs alla återställnings punkter för replikering |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Reparera replikering |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Återaktivera skydd för skyddat objekt |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Växla skydds behållare |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Testa redundans |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Rensning av redundanstest |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | växling vid fel |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Uppdatera mobilitets tjänsten |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Läs alla skydds behållar mappningar |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Läs eventuella Recovery Services-leverantörer |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Uppdatera Provider |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Läs alla lagrings klassificeringar |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Läs alla mappningar för lagrings klassificering |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Läs eventuella vCenter |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Skapa och hantera jobb för replikering |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Läs eventuella principer |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Återställnings plan för redundans |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Återställnings plan för planerad redundansväxling |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Läs eventuella återställnings planer |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Skydda återställnings plan |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Återställnings plan för redundanstest |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Återställnings plan för rensning av redundanstest |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Återställnings plan för redundans |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Läs aviseringar för Recovery Services-valvet |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Returnerar användnings information för ett Recovery Services-valv. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Valv-token kan användas för att hämta valv-token för Server dels åtgärder på valvnivå. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Storage/storageAccounts/read | Returnerar listan över lagrings konton eller hämtar egenskaperna för det angivna lagrings kontot. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="site-recovery-reader"></a>Site Recovery läsare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig Visa Site Recovery status men inte utföra andra hanterings åtgärder |
> | **Id** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | GetAllocatedStamp är en intern åtgärd som används av tjänsten |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Med åtgärden Hämta utökad information får du ett objekts utökade information som representerar Azure-resursen av typen? valv? |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Hämtar aviseringarna för Recovery Services-valvet. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | Med åtgärden Hämta valv hämtas ett objekt som representerar Azure-resursen av typen valv |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Åtgärden hämta åtgärds resultat kan användas för att hämta åtgärds statusen och resultatet för åtgärden som skickats asynkront |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Med åtgärden Hämta behållare kan du hämta de behållare som är registrerade för en resurs. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Läs eventuella aviserings inställningar |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Läs eventuella händelser |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Läs eventuella infrastruktur resurser |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Läs alla nätverk |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Läs eventuella nätverks mappningar |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Läs eventuella skydds behållare |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Läs alla objekt som ska skyddas |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Läs alla skyddade objekt |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Läs alla återställnings punkter för replikering |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Läs alla skydds behållar mappningar |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Läs eventuella Recovery Services-leverantörer |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Läs alla lagrings klassificeringar |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Läs alla mappningar för lagrings klassificering |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Läs eventuella vCenter |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | Läs alla jobb |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Läs eventuella principer |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Läs eventuella återställnings planer |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Returnerar användnings information för ett Recovery Services-valv. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Valv-token kan användas för att hämta valv-token för Server dels åtgärder på valvnivå. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="spatial-anchors-account-contributor"></a>Konto deltagare för spatiala ankare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera spatiala ankare i ditt konto, men ta inte bort dem |
> | **Id** | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | **Åtgärder** |  |
> | *alternativet* |  |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Create/Action | Skapa spatiala ankare |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Discovery/Read | Identifiera närliggande spatiala ankare |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Properties/Read | Hämta egenskaper för spatiala ankare |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Query/Read | Hitta spatiala ankare |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Skicka diagnostikdata för att hjälpa till att förbättra kvaliteten på tjänsten Azure spatiala ankare |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Write | Uppdatera egenskaper för spatiala ankare |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="spatial-anchors-account-owner"></a>Konto ägare för spatiala ankare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera spatialdata i ditt konto, inklusive att ta bort dem |
> | **Id** | 70bbe301-9835-447d-afdd-19eb3167307c |
> | **Åtgärder** |  |
> | *alternativet* |  |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Create/Action | Skapa spatiala ankare |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Delete | Ta bort avstånds ankare |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Discovery/Read | Identifiera närliggande spatiala ankare |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Properties/Read | Hämta egenskaper för spatiala ankare |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Query/Read | Hitta spatiala ankare |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Skicka diagnostikdata för att hjälpa till att förbättra kvaliteten på tjänsten Azure spatiala ankare |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Write | Uppdatera egenskaper för spatiala ankare |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="spatial-anchors-account-reader"></a>Konto läsare för spatiala ankare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Gör att du kan hitta och läsa egenskaper för spatiala ankare i ditt konto |
> | **Id** | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Åtgärder** |  |
> | *alternativet* |  |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Discovery/Read | Identifiera närliggande spatiala ankare |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Properties/Read | Hämta egenskaper för spatiala ankare |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Query/Read | Hitta spatiala ankare |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read | Skicka diagnostikdata för att hjälpa till att förbättra kvaliteten på tjänsten Azure spatiala ankare |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="sql-db-contributor"></a>SQL DB-deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Gör att du kan hantera SQL-databaser, men inte åtkomst till dem. Du kan inte heller hantera säkerhets relaterade principer eller överordnade SQL-servrar. |
> | **Id** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera aviserings regler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | Skapa och hantera SQL-databaser |
> | Microsoft.Sql/servers/read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | Microsoft. Insights/Metrics/Read | Läs mått |
> | Microsoft.Insights/metricDefinitions/read | Läs mått definitioner |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/databaser/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/databaser/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/managedInstances/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Redigera gransknings principer |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Redigera gransknings inställningar |
> | Microsoft.Sql/servers/databases/auditRecords/read | Hämta databasens BLOB audit-poster |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Redigera anslutnings principer |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Redigera data masknings principer |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Redigera säkerhets aviserings principer |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Redigera säkerhets mått |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft. SQL/Servers/databaser/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft. SQL/Servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="sql-managed-instance-contributor"></a>SQL-hanterad instans deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera SQL-hanterade instanser och nödvändig nätverks konfiguration, men kan inte ge åtkomst till andra. |
> | **Id** | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | **Åtgärder** |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Network/networkSecurityGroups/* |  |
> | Microsoft. Network/routeTables/* |  |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft. SQL/managedInstances/* |  |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | Microsoft.Network/virtualNetworks/subnets/* |  |
> | Microsoft.Network/virtualNetworks/* |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft. Insights/Metrics/Read | Läs mått |
> | Microsoft.Insights/metricDefinitions/read | Läs mått definitioner |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="sql-security-manager"></a>SQL Security Manager
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Gör att du kan hantera säkerhetsrelaterade principer för SQL-servrar och databaser, men inte åtkomst till dem. |
> | **Id** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs Microsoft-auktorisering |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Ansluter till en resurs som lagrings konto eller SQL-databas till ett undernät. Det går inte att avisera. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/databaser/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/databaser/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/managedInstances/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/databaser/transparentDataEncryption/* |  |
> | Microsoft. SQL/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Skapa och hantera principer för SQL Server-granskning |
> | Microsoft.Sql/servers/auditingSettings/* | Skapa och hantera gransknings inställning för SQL Server |
> | Microsoft.Sql/servers/extendedAuditingSettings/read | Hämta information om den utökade gransknings principen för Server-blob som kon figurer ATS på en viss server |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Skapa och hantera gransknings principer för SQL Server-databaser |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Skapa och hantera gransknings inställningar för SQL Server-databasen |
> | Microsoft.Sql/servers/databases/auditRecords/read | Läs gransknings poster |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Skapa och hantera anslutnings principer för SQL Server-databaser |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Skapa och hantera data masknings principer för SQL Server-databas |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Hämta information om den utökade blobb gransknings principen som kon figurer ATS för en viss databas |
> | Microsoft.Sql/servers/databases/read | Returnera listan över databaser eller hämta egenskaperna för den angivna databasen. |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/read | Hämta ett databas schema. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Hämta en databas kolumn. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | Hämta en databas tabell. |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Skapa och hantera säkerhets aviserings principer för SQL Server-databas |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Skapa och hantera säkerhets mått för SQL Server-databasen |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft. SQL/Servers/databaser/transparentDataEncryption/* |  |
> | Microsoft. SQL/Servers/databaser/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | Returnera listan över servrar eller hämtar egenskaperna för den angivna servern. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Skapa och hantera SQL Server-principer för säkerhets avisering |
> | Microsoft. SQL/Servers/vulnerabilityAssessments/* |  |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="sql-server-contributor"></a>SQL Server deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Gör att du kan hantera SQL-servrar och databaser, men inte åtkomst till dem och inte deras säkerhetsrelaterade principer. |
> | **Id** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | Skapa och hantera SQL-servrar |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | Microsoft. Insights/Metrics/Read | Läs mått |
> | Microsoft.Insights/metricDefinitions/read | Läs mått definitioner |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/databaser/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/sensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/databaser/vulnerabilityAssessments/* |  |
> | Microsoft. SQL/managedInstances/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Redigera principer för SQL Server-granskning |
> | Microsoft.Sql/servers/auditingSettings/* | Redigera gransknings inställningar för SQL Server |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Redigera gransknings principer för SQL Server-databasen |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Redigera gransknings inställningar för SQL Server-databasen |
> | Microsoft.Sql/servers/databases/auditRecords/read | Läs gransknings poster |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Redigera anslutnings principer för SQL Server-databas |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Redigera data masknings principer för SQL Server-databas |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Redigera säkerhets aviserings principer för SQL Server-databas |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Redigera säkerhets mått för SQL Server-databas |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft. SQL/Servers/databaser/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Redigera säkerhets aviserings principer för SQL Server |
> | Microsoft. SQL/Servers/vulnerabilityAssessments/* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="storage-account-contributor"></a>Lagrings konto deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Tillåter hantering av lagrings konton. Ger åtkomst till konto nyckeln, som kan användas för att få åtkomst till data via autentisering med delad nyckel. |
> | **Id** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs all auktorisering |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft.Insights/diagnosticSettings/* | Hantera diagnostikinställningar |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Ansluter till en resurs som lagrings konto eller SQL-databas till ett undernät. Det går inte att avisera. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Storage/storageAccounts/* | Skapa och hantera lagringskonton |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="storage-account-key-operator-service-role"></a>Lagrings kontots nyckel operatörs tjänst roll
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Tillåter att du visar och återskapar åtkomst nycklar för lagrings kontot. |
> | **Id** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **Åtgärder** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | Returnerar åtkomst nycklar för det angivna lagrings kontot. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Återskapar åtkomst nycklarna för det angivna lagrings kontot. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="storage-blob-data-contributor"></a>Storage BLOB data-deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Läsa, skriva och ta bort Azure Storage behållare och blobbar. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | **Åtgärder** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Ta bort en behållare. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Returnera en behållare eller en lista över behållare. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Ändra en behållares metadata eller egenskaper. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Returnerar en användar Delegerings nyckel för Blob Service. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Ta bort en blob. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Returnera en BLOB eller en lista över blobbar. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Skriv till en blob. |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="storage-blob-data-owner"></a>Storage BLOB data-ägare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Ger fullständig åtkomst till Azure Storage BLOB-behållare och data, inklusive att tilldela POSIX-åtkomstkontroll. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | **Åtgärder** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/* | Fullständiga behörigheter för behållare. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Returnerar en användar Delegerings nyckel för Blob Service. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/* | Fullständiga behörigheter för blobbar. |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="storage-blob-data-reader"></a>Storage BLOB data Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Läs och Visa Azure Storage behållare och blobbar. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **Åtgärder** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Returnera en behållare eller en lista över behållare. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Returnerar en användar Delegerings nyckel för Blob Service. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Returnera en BLOB eller en lista över blobbar. |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="storage-blob-delegator"></a>Storage BLOB-delegerare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Hämta en användar Delegerings nyckel som sedan kan användas för att skapa en signatur för delad åtkomst för en behållare eller BLOB som är signerad med Azure AD-autentiseringsuppgifter. Mer information finns i [skapa en användar Delegerings-SAS](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas). |
> | **Id** | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | **Åtgärder** |  |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Returnerar en användar Delegerings nyckel för Blob Service. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="storage-file-data-smb-share-contributor"></a>Lagrings fil data SMB-resurs deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Tillåter Läs-, skriv-och borttagnings åtkomst i Azure Storage fil resurser över SMB |
> | **Id** | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | **Åtgärder** |  |
> | *alternativet* |  |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Returnerar en fil/mapp eller en lista över filer/mappar. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Returnerar resultatet av att skriva en fil eller skapa en mapp. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Returnerar resultatet av att ta bort en fil/mapp. |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="storage-file-data-smb-share-elevated-contributor"></a>Lagrings fil data SMB-resurs upphöjt bidrags givare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Tillåter behörighet att läsa, skriva, ta bort och ändra NTFS-behörighet i Azure Storage fil resurser över SMB |
> | **Id** | a7264617-510b-434b-a828-9731dc254ea7 |
> | **Åtgärder** |  |
> | *alternativet* |  |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Returnerar en fil/mapp eller en lista över filer/mappar. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Returnerar resultatet av att skriva en fil eller skapa en mapp. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete | Returnerar resultatet av att ta bort en fil/mapp. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action | Returnerar resultatet av att ändra behörighet för en fil/mapp. |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="storage-file-data-smb-share-reader"></a>Storage File data SMB Share Reader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Tillåter Läs åtkomst till Azure-filresurs via SMB |
> | **Id** | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | **Åtgärder** |  |
> | *alternativet* |  |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Returnerar en fil/mapp eller en lista över filer/mappar. |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="storage-queue-data-contributor"></a>Data deltagare i Storage Queue
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Läsa, skriva och ta bort Azure Storage köer och köa meddelanden. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | **Åtgärder** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Ta bort en kö. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Returnera en kö eller en lista över köer. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | Ändra metadata eller egenskaper för kö. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Ta bort ett eller flera meddelanden från en kö. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Granska eller hämta ett eller flera meddelanden från en kö. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Lägg till ett meddelande i en kö. |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="storage-queue-data-message-processor"></a>Processor för data meddelande i lagrings kön
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Granska, hämta och ta bort ett meddelande från en Azure Storage kö. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | **Åtgärder** |  |
> | *alternativet* |  |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Titta på ett meddelande. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action | Hämta och ta bort ett meddelande. |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="storage-queue-data-message-sender"></a>Avsändare av data meddelande i lagrings köer
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Lägg till meddelanden i en Azure Storage-kö. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | **Åtgärder** |  |
> | *alternativet* |  |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action | Lägg till ett meddelande i en kö. |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="storage-queue-data-reader"></a>Data läsare för lagrings kön
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Läs och Visa Azure Storage köer och köa meddelanden. Information om vilka åtgärder som krävs för en specifik data åtgärd finns i [behörigheter för att anropa blob-och Queue data-åtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Id** | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Åtgärder** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Returnerar en kö eller en lista över köer. |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Granska eller hämta ett eller flera meddelanden från en kö. |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="support-request-contributor"></a>Support förfrågan deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Gör att du kan skapa och hantera support förfrågningar |
> | **Id** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs behörighet |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="traffic-manager-contributor"></a>Traffic Manager deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera Traffic Manager profiler, men låter dig inte kontrol lera vem som har åtkomst till dem. |
> | **Id** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs roller och roll tilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="user-access-administrator"></a>Administratör för användaråtkomst
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Gör att du kan hantera användar åtkomst till Azure-resurser. |
> | **Id** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Åtgärder** |  |
> | */read | Läs resurser av alla typer, förutom hemligheter. |
> | Microsoft. Authorization/* | Hantera auktorisering |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="virtual-machine-administrator-login"></a>Administratörs inloggning för virtuell dator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Visa Virtual Machines i portalen och logga in som administratör |
> | **Id** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | **Åtgärder** |  |
> | Microsoft.Network/publicIPAddresses/read | Hämtar en offentlig IP-adress definition. |
> | Microsoft.Network/virtualNetworks/read | Hämta definition av virtuellt nätverk |
> | Microsoft.Network/loadBalancers/read | Hämtar en belastnings Utjämnings definition |
> | Microsoft.Network/networkInterfaces/read | Hämtar en definition för nätverks gränssnitt.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Logga in på en virtuell dator som en vanlig användare |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Logga in på en virtuell dator med Windows-administratör eller Linux rot användar privilegier |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="virtual-machine-contributor"></a>Virtuell datordeltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Låter dig hantera virtuella datorer, men inte åtkomst till dem, inte det virtuella nätverk eller lagrings konto som de är anslutna till. |
> | **Id** | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs behörighet |
> | Microsoft. Compute/availabilitySets/* | Skapa och hantera beräknings tillgänglighets uppsättningar |
> | Microsoft. Compute/locations/* | Skapa och hantera beräknings platser |
> | Microsoft.Compute/virtualMachines/* | Skapa och hantera virtuella datorer |
> | Microsoft.Compute/virtualMachineScaleSets/* | Skapa och hantera VM-skalningsuppsättningar |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Ansluter till en programgateways backend-adresspool. Det går inte att avisera. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Ansluter till en backend-adresspool för belastnings utjämning. Det går inte att avisera. |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Ansluter en inkommande NAT-pool för belastningsutjämnaren. Det går inte att avisera. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Ansluter en inkommande NAT-regel för belastningsutjämnare. Det går inte att avisera. |
> | Microsoft.Network/loadBalancers/probes/join/action | Tillåter att avsökningar av en belastningsutjämnare används. Till exempel kan den här behörighetens egenskap healthProbe-egenskap för VM Scale set referera till avsökningen. Det går inte att avisera. |
> | Microsoft.Network/loadBalancers/read | Hämtar en belastnings Utjämnings definition |
> | Microsoft. Network/locations/* | Skapa och hantera nätverks platser |
> | Microsoft.Network/networkInterfaces/* | Skapa och hantera nätverks gränssnitt |
> | Microsoft.Network/networkSecurityGroups/join/action | Ansluter till en nätverks säkerhets grupp. Det går inte att avisera. |
> | Microsoft.Network/networkSecurityGroups/read | Hämtar en definition för nätverks säkerhets grupp |
> | Microsoft.Network/publicIPAddresses/join/action | Ansluter till en offentlig IP-adress. Det går inte att avisera. |
> | Microsoft.Network/publicIPAddresses/read | Hämtar en offentlig IP-adress definition. |
> | Microsoft.Network/virtualNetworks/read | Hämta definition av virtuellt nätverk |
> | Microsoft. Network/virtualNetworks/subnets/Join/Action | Ansluter till ett virtuellt nätverk. Det går inte att avisera. |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Skapa en säkerhets kopia av skydds avsikt |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Returnerar objekt information om det skyddade objektet |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Skapa ett säkerhetskopierat skyddat objekt |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Returnerar alla skydds principer |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Skapar skydds princip |
> | Microsoft.RecoveryServices/Vaults/read | Med åtgärden Hämta valv hämtas ett objekt som representerar Azure-resursen av typen valv |
> | Microsoft.RecoveryServices/Vaults/usages/read | Returnerar användnings information för ett Recovery Services-valv. |
> | Microsoft.RecoveryServices/Vaults/write | Skapa valv-åtgärd skapar en Azure-resurs av typen valv |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.SqlVirtualMachine/* |  |
> | Microsoft. Storage/storageAccounts/Listnycklar/åtgärd | Returnerar åtkomst nycklar för det angivna lagrings kontot. |
> | Microsoft.Storage/storageAccounts/read | Returnerar listan över lagrings konton eller hämtar egenskaperna för det angivna lagrings kontot. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="virtual-machine-user-login"></a>Användar inloggning för virtuell dator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Visa Virtual Machines i portalen och logga in som en vanlig användare. |
> | **Id** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Åtgärder** |  |
> | Microsoft.Network/publicIPAddresses/read | Hämtar en offentlig IP-adress definition. |
> | Microsoft.Network/virtualNetworks/read | Hämta definition av virtuellt nätverk |
> | Microsoft.Network/loadBalancers/read | Hämtar en belastnings Utjämnings definition |
> | Microsoft.Network/networkInterfaces/read | Hämtar en definition för nätverks gränssnitt.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Logga in på en virtuell dator som en vanlig användare |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="web-plan-contributor"></a>Webb Plans deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Gör att du kan hantera webb planer för webbplatser, men inte till gång till dem. |
> | **Id** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs behörighet |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | Microsoft.Web/serverFarms/* | Skapa och hantera Server grupper |
> | Microsoft. Web/hostingEnvironments/JOIN/åtgärd | Ansluter till en App Service-miljön |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="website-contributor"></a>Webbplats deltagare
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Beskrivning** | Gör att du kan hantera webbplatser (inte webb planer), men inte till gång till dem. |
> | **Id** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Åtgärder** |  |
> | Microsoft.Authorization/*/read | Läs behörighet |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insikter aviserings regler |
> | Microsoft.Insights/components/* | Skapa och hantera Insights-komponenter |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighets status för alla resurser i det angivna omfånget |
> | Microsoft. Resources/Deployments/* | Skapa och hantera distributioner av resurs grupper |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Hämtar eller listar resurs grupper. |
> | Microsoft.Support/* | Skapa och hantera support biljetter |
> | Microsoft.Web/certificates/* | Skapa och hantera webbplats certifikat |
> | Microsoft.Web/listSitesAssignedToHostName/read | Hämta namn på platser tilldelade till värdnamn. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Hämta egenskaperna för en App Service plan |
> | Microsoft.Web/sites/* | Skapa och hantera webbplatser (webbplats skapande kräver även Skriv behörighet till den associerade App Service planen) |
> | **NotActions** |  |
> | *alternativet* |  |
> | **DataActions** |  |
> | *alternativet* |  |
> | **NotDataActions** |  |
> | *alternativet* |  |

## <a name="next-steps"></a>Nästa steg

- [Matcha Resource Provider till tjänst](../azure-resource-manager/azure-services-resource-providers.md)
- [Anpassade roller för Azure-resurser](custom-roles.md)
- [Behörigheter i Azure Security Center](../security-center/security-center-permissions.md)
