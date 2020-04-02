---
title: Azure inbyggda roller för Azure RBAC
description: I den här artikeln beskrivs de inbyggda Azure-rollerna för Azure-rollbaserad åtkomstkontroll (RBAC). Den listar åtgärder, notactions, dataåtgärder och NotDataActions.
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
ms.date: 03/22/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: 2fe3b94463da07304f2c853910ac5d2a6771d070
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80545644"
---
# <a name="azure-built-in-roles"></a>Azure inbyggda roller

[Azure rollbaserad åtkomstkontroll (RBAC)](overview.md) har flera Azure-inbyggda roller som du kan tilldela användare, grupper, tjänsthuvudnamn och hanterade identiteter. Rolltilldelningar är hur du styr åtkomsten till Azure-resurser. Om de inbyggda rollerna inte uppfyller organisationens specifika behov kan du skapa dina egna [anpassade Azure-roller](custom-roles.md).

I den här artikeln visas de inbyggda rollerna för Azure-resurser som alltid utvecklas. Om du vill hämta de senaste rollerna använder du [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) eller [az role definition list](/cli/azure/role/definition#az-role-definition-list). Om du letar efter administratörsroller för Azure Active Directory (Azure AD) läser du [Administratörsrollbehörigheter i Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="all"></a>Alla

Följande tabell innehåller en kort beskrivning och det unika ID:t för varje inbyggd roll. Välj rollnamnet om du `Actions`vill `NotActions` `DataActions`visa `NotDataActions` listan över , , och för varje roll. Information om vad dessa åtgärder innebär och hur de gäller för hanterings- och dataplan finns i [Förstå rolldefinitioner för Azure-resurser](role-definitions.md).


> [!div class="mx-tableFixed"]
> | Inbyggd roll | Beskrivning | ID |
> | --- | --- | --- |
> | **Allmänt** |  |  |
> | [Deltagare](#contributor) | Gör att du kan hantera allt utom att bevilja åtkomst till resurser. | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | [Ägare](#owner) | Gör att du kan hantera allt, inklusive åtkomst till resurser. | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | [Läsare](#reader) | Gör att du kan visa allt, men inte göra några ändringar. | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | [Administratör för användaråtkomst](#user-access-administrator) | Gör att du kan hantera användaråtkomst till Azure-resurser. | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Compute** |  |  |
> | [Klassisk deltagare i virtuell dator](#classic-virtual-machine-contributor) | Gör att du kan hantera klassiska virtuella datorer, men inte åtkomst till dem, och inte det virtuella nätverks- eller lagringskonto som de är anslutna till. | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | [Inloggning av administratör för virtuell dator](#virtual-machine-administrator-login) | Visa virtuella datorer i portalen och logga in som administratör | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | [Virtuell datordeltagare](#virtual-machine-contributor) | Gör att du kan hantera virtuella datorer, men inte åtkomst till dem, och inte det virtuella nätverks- eller lagringskonto som de är anslutna till. | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | [Användarinloggning för virtuell dator](#virtual-machine-user-login) | Visa virtuella datorer i portalen och logga in som en vanlig användare. | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Nätverk** |  |  |
> | [CDN-slutpunktsdeltagare](#cdn-endpoint-contributor) | Kan hantera CDN-slutpunkter, men kan inte bevilja åtkomst till andra användare. | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | [CDN-slutpunktsläsare](#cdn-endpoint-reader) | Kan visa CDN-slutpunkter, men kan inte göra ändringar. | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | [Deltagare i CDN-profil](#cdn-profile-contributor) | Kan hantera CDN-profiler och deras slutpunkter, men kan inte ge åtkomst till andra användare. | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | [CDN-profilläsare](#cdn-profile-reader) | Kan visa CDN-profiler och deras slutpunkter, men kan inte göra ändringar. | 8f96442b-4075-438f-813d-ad51ab4019af |
> | [Klassisk nätverksdeltagare](#classic-network-contributor) | Gör att du kan hantera klassiska nätverk, men inte åtkomst till dem. | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | [DELTAGARE I DNS-zonen](#dns-zone-contributor) | Gör att du kan hantera DNS-zoner och postuppsättningar i Azure DNS, men låter dig inte styra vem som har åtkomst till dem. | befefa01-2a29-4197-83a8-272ff33ce314 |
> | [Nätverksdeltagare](#network-contributor) | Gör att du kan hantera nätverk, men inte åtkomst till dem. | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | [Traffic Manager-deltagare](#traffic-manager-contributor) | Gör att du kan hantera Traffic Manager-profiler, men du kan inte styra vem som har åtkomst till dem. | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Lagring** |  |  |
> | [Avere Bidragsgivare](#avere-contributor) | Kan skapa och hantera ett Avere vFXT-kluster. | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | [Avere-operatör](#avere-operator) | Används av Avere vFXT-klustret för att hantera klustret | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | [Deltagare för säkerhetskopiering](#backup-contributor) | Gör att du kan hantera säkerhetskopieringstjänsten, men kan inte skapa valv och ge åtkomst till andra | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | [Operatör för säkerhetskopiering](#backup-operator) | Gör att du kan hantera säkerhetskopieringstjänster, förutom borttagning av säkerhetskopiering, valvskapande och ge tillgång till andra | 00c29273-979b-4161-815c-10b084fb9324 |
> | [Läsare för säkerhetskopiering](#backup-reader) | Kan visa säkerhetskopieringstjänster, men kan inte göra ändringar | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | [Deltagare i det klassiska lagringskontot](#classic-storage-account-contributor) | Gör att du kan hantera klassiska lagringskonton, men inte komma åt dem. | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | [Rollen för nyckeloperatörstjänsten för klassiskt lagringskonto](#classic-storage-account-key-operator-service-role) | Klassiska nyckelansvariga för lagringskonton tillåts lista och återskapa nycklar på klassiska lagringskonton | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | [Data Box Deltagare](#data-box-contributor) | Gör att du kan hantera allt under Data Box Service förutom att ge åtkomst till andra. | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | [Läsare av dataruta](#data-box-reader) | Gör att du kan hantera Data Box Service förutom att skapa order- eller redigeringsorderinformation och ge åtkomst till andra. | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | [Utvecklare av DataSjöanalys](#data-lake-analytics-developer) | Gör att du kan skicka, övervaka och hantera dina egna jobb men inte skapa eller ta bort DataSjöanalyskonton. | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | [Läsar- och dataåtkomst](#reader-and-data-access) | Gör att du kan visa allt men låter dig inte ta bort eller skapa ett lagringskonto eller en resurs. Det kommer också att tillåta läs-/skrivåtkomst till alla data som finns i ett lagringskonto via åtkomst till lagringskontonycklar. | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | [Lagringskontodeltagare](#storage-account-contributor) | Tillåter hantering av lagringskonton. Ger åtkomst till kontonyckeln, som kan användas för att komma åt data via auktorisering av delade nyckel. | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | [Nyckeloperatörsroll för lagringskonto](#storage-account-key-operator-service-role) | Tillåter listning och återskapande av åtkomstnycklar för lagringskonto. | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | [Storage Blob Data Contributor](#storage-blob-data-contributor) | Läsa, skriva och ta bort Azure Storage-behållare och blobbar. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i [Behörigheter för att anropa blob- och ködataåtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | [Ägare av lagringsblobbdata](#storage-blob-data-owner) | Ger fullständig åtkomst till Azure Storage blob-behållare och data, inklusive tilldelning av POSIX-åtkomstkontroll. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i [Behörigheter för att anropa blob- och ködataåtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | [Dataläsare för lagringsblobb](#storage-blob-data-reader) | Läs och lista Azure Storage-behållare och blobbar. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i [Behörigheter för att anropa blob- och ködataåtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | [Lagring Blob Delegator](#storage-blob-delegator) | Hämta en användardelegeringsnyckel som sedan kan användas för att skapa en signatur för delad åtkomst för en behållare eller blob som är signerad med Azure AD-autentiseringsuppgifter. Mer information finns i [Skapa en SAS för användardelegering](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas). | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | [SMB-delningsdeltagare för lagringsfildata](#storage-file-data-smb-share-contributor) | Möjliggör läs-, skriv- och borttagningsåtkomst på filer/kataloger i Azure-filresurser. Den här rollen har ingen inbyggd motsvarighet på Windows-filservrar. | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | [Lagringsfildata SMB-delning förhöjd deltagare](#storage-file-data-smb-share-elevated-contributor) | Möjliggör läsning, skrivning, borttagning och ändring av ACL:er på filer/kataloger i Azure-filresurser. Den här rollen motsvarar en filresurs ACL för ändring på Windows-filservrar. | a7264617-510b-434b-a828-9731dc254ea7 |
> | [SMB-delningsläsare för lagringsfildata](#storage-file-data-smb-share-reader) | Möjliggör läsåtkomst för filer/kataloger i Azure-filresurser. Den här rollen motsvarar en filresurs ACL för läsning på Windows-filservrar. | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | [Datadeltagare för lagringskö](#storage-queue-data-contributor) | Läsa, skriva och ta bort Azure Storage-köer och kömeddelanden. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i [Behörigheter för att anropa blob- och ködataåtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | [Meddelandebehandlare för lagringskö](#storage-queue-data-message-processor) | Granska, hämta och ta bort ett meddelande från en Azure Storage-kö. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i [Behörigheter för att anropa blob- och ködataåtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | [Skickare av datameddelande för lagringskö](#storage-queue-data-message-sender) | Lägg till meddelanden i en Azure Storage-kö. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i [Behörigheter för att anropa blob- och ködataåtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | [Dataläsare för lagringskö](#storage-queue-data-reader) | Läs och lista Azure Storage-köer och kömeddelanden. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i [Behörigheter för att anropa blob- och ködataåtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Webb** |  |  |
> | [Dataläsare för Azure Maps (förhandsversion)](#azure-maps-data-reader-preview) | Ger åtkomst till läsa kartrelaterade data från ett Azure Maps-konto. | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | [Deltagare i söktjänsten](#search-service-contributor) | Gör att du kan hantera söktjänster, men inte komma åt dem. | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | [Deltagare i webbplanen](#web-plan-contributor) | Gör att du kan hantera webbplanerna för webbplatser, men inte åtkomst till dem. | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | [Webbplatsdeltagare](#website-contributor) | Gör att du kan hantera webbplatser (inte webbplaner), men inte tillgång till dem. | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Behållare** |  |  |
> | [AcrDelete (](#acrdelete) | acr ta bort | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | [AcrImageSigner](#acrimagesigner) | acr bild undertecknare | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | [AcrPull (acrpull)](#acrpull) | acr dra | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | [AcrPush (av en)](#acrpush) | acr push | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | [AcrQuarantineLäsare](#acrquarantinereader) | acr karantän dataläsare | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | [AcrQuarantineWriter](#acrquarantinewriter) | acr karantän dataskrivare | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | [Azure Kubernetes tjänstkluster administratörsroll](#azure-kubernetes-service-cluster-admin-role) | Lista åtgärden för klusteradministratör. | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | [Användarroll för Azure Kubernetes-tjänstkluster](#azure-kubernetes-service-cluster-user-role) | Lista åtgärden för klusteranvändare. | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Databaser** |  |  |
> | [Cosmos DB-kontoläsare roll](#cosmos-db-account-reader-role) | Kan läsa Azure Cosmos DB-kontodata. Se [DocumentDB-kontodeltagare](#documentdb-account-contributor) för hantering av Azure Cosmos DB-konton. | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | [Cosmos DB-operatör](#cosmos-db-operator) | Gör att du kan hantera Azure Cosmos DB-konton, men inte komma åt data i dem. Förhindrar åtkomst till kontonycklar och anslutningssträngar. | 230815da-be43-4aae-9cb4-875f7bd000a |
> | [CosmosBackupOperator](#cosmosbackupoperator) | Kan skicka återställningsbegäran för en Cosmos DB-databas eller en behållare för ett konto | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | [Deltagare på DocumentDB-konto](#documentdb-account-contributor) | Kan hantera Azure Cosmos DB-konton. Azure Cosmos DB är tidigare känt som DocumentDB. | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | [Redis-cachedeltagare](#redis-cache-contributor) | Gör att du kan hantera Redis-cacheminnen, men inte komma åt dem. | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | [SQL DB-deltagare](#sql-db-contributor) | Gör att du kan hantera SQL-databaser, men inte komma åt dem. Du kan inte heller hantera deras säkerhetsrelaterade principer eller deras överordnade SQL-servrar. | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | [SQL-hanterad instansdeltagare](#sql-managed-instance-contributor) | Gör att du kan hantera SQL-hanterade instanser och nödvändig nätverkskonfiguration, men kan inte ge åtkomst till andra. | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | [SQL-säkerhetshanterare](#sql-security-manager) | Gör att du kan hantera säkerhetsrelaterade principer för SQL-servrar och databaser, men inte åtkomst till dem. | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | [SQL Server-deltagare](#sql-server-contributor) | Gör att du kan hantera SQL-servrar och -databaser, men inte åtkomst till dem, och inte deras säkerhetsrelaterade principer. | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Analys** |  |  |
> | [Azure Event Hubs dataägare](#azure-event-hubs-data-owner) | Ger fullständig åtkomst till Azure Event Hubs-resurser. | f526a384-b230-433a-b45c-95f59c4a2dec |
> | [Azure-händelsehubbar datamottagare](#azure-event-hubs-data-receiver) | Tillåter åtkomst till Azure Event Hubs-resurser. | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | [Azure Event Hubs Data Sender](#azure-event-hubs-data-sender) | Tillåter skicka åtkomst till Azure Event Hubs-resurser. | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | [Data Factory Bidragsgivare](#data-factory-contributor) | Skapa och hantera datafabriker samt underordnade resurser i dem. | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | [Datarensare](#data-purger) | Kan rensa analysdata | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | [Klusteroperatör för HDInsight](#hdinsight-cluster-operator) | Gör att du kan läsa och ändra HDInsight-klusterkonfigurationer. | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | [Medarbetare för HDInsight-domäntjänster](#hdinsight-domain-services-contributor) | Kan läsa, skapa, ändra och ta bort domäntjänster relaterade åtgärder som behövs för HDInsight Enterprise Security Package | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | [Log Analytics Contributor](#log-analytics-contributor) | Log Analytics Contributor kan läsa alla övervakningsdata och redigera övervakningsinställningar. Redigering av övervakningsinställningarna inkluderar att lägga till vm-tillägget till virtuella datorer. läsa lagringskontonycklar för att kunna konfigurera insamling av loggar från Azure Storage; skapa och konfigurera Automation-konton; Lägga till lösningar; och konfigurera Azure-diagnostik på alla Azure-resurser. | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | [Log Analytics Reader](#log-analytics-reader) | Log Analytics Reader kan visa och söka efter alla övervakningsdata samt och visa övervakningsinställningar, inklusive visning av konfigurationen av Azure-diagnostik på alla Azure-resurser. | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Blockkedja** |  |  |
> | [Åtkomst till blockchain-medlemnod (förhandsversion)](#blockchain-member-node-access-preview) | Ger tillgång till Blockchain-medlemsnoder | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **AI + maskininlärning** |  |  |
> | [Kognitiva tjänster Bidragsgivare](#cognitive-services-contributor) | Gör att du kan skapa, läsa, uppdatera, ta bort och hantera nycklar i Cognitive Services. | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | [Dataläsare för Cognitive Services (förhandsgranskning)](#cognitive-services-data-reader-preview) | Gör att du kan läsa Cognitive Services-data. | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | [Användare av Cognitive Services](#cognitive-services-user) | Gör att du kan läsa och lista nycklar i Cognitive Services. | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Blandad verklighet** |  |  |
> | [Rapportör för kontot För rumsliga ankare](#spatial-anchors-account-contributor) | Gör att du kan hantera rumsliga ankare i ditt konto, men inte ta bort dem | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | [Ägare av kontot För rumsliga ankare](#spatial-anchors-account-owner) | Gör att du kan hantera rumsliga ankare i ditt konto, inklusive att ta bort dem | 70bbe301-9835-447d-afdd-19eb3167307c |
> | [Rumsliga ankare kontoläsare](#spatial-anchors-account-reader) | Gör att du kan hitta och läsa egenskaper för rumsliga ankare i ditt konto | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Integrering** |  |  |
> | [API-hanteringstjänstdeltagare](#api-management-service-contributor) | Kan hantera tjänsten och API:erna | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | [Rollen Operatör för API-hanteringstjänster](#api-management-service-operator-role) | Kan hantera tjänsten men inte API:erna | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | [Rollen för tjänstläsare för API-hantering](#api-management-service-reader-role) | Skrivskyddad åtkomst till tjänst och API:er | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | [Ägare av appkonfigurationsdata](#app-configuration-data-owner) | Ger fullständig åtkomst till appkonfigurationsdata. | 5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b |
> | [Dataläsare för appkonfiguration](#app-configuration-data-reader) | Tillåter läsåtkomst till appkonfigurationsdata. | 516239f1-63e1-4d78-a4de-a74fb236a071 |
> | [Ägare av Azure Service Bus-data](#azure-service-bus-data-owner) | Ger fullständig åtkomst till Azure Service Bus-resurser. | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | [Azure Service Bus-datamottagare](#azure-service-bus-data-receiver) | Ger åtkomst till Azure Service Bus-resurser. | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | [Azure Service Bus Data Sender](#azure-service-bus-data-sender) | Tillåter skicka åtkomst till Azure Service Bus-resurser. | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | [Ägare av Azure Stack-registrering](#azure-stack-registration-owner) | Gör att du kan hantera Azure Stack-registreringar. | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | [Deltagare i EventGrid EventSubscription](#eventgrid-eventsubscription-contributor) | Gör att du kan hantera EventGrid-händelseprenumerationsåtgärder. | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | [EventGrid EventSubscription Reader](#eventgrid-eventsubscription-reader) | Gör att du kan läsa EventGrid-händelseprenumerationer. | 2414bbcf-6497-4faf-8c65-045460748405 |
> | [Intelligent systemkontobidragsgivare](#intelligent-systems-account-contributor) | Gör att du kan hantera intelligenta systemkonton, men inte åtkomst till dem. | 03a6d094-3444-4b3d-88af-7477090a9e5e5e |
> | [Logic App-deltagare](#logic-app-contributor) | Gör att du kan hantera logikappar, men inte ändra åtkomst till dem. | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | [Logic App-operatör](#logic-app-operator) | Gör att du kan läsa, aktivera och inaktivera logikappar, men inte redigera eller uppdatera dem. | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Identitet** |  |  |
> | [Hanterad identitetsdeltagare](#managed-identity-contributor) | Skapa, läsa, uppdatera och ta bort användartilldelad identitet | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | [Operatör för hanterad identitet](#managed-identity-operator) | Läsa och tilldela användartilldelad identitet | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Säkerhet** |  |  |
> | [Azure Sentinel-deltagare](#azure-sentinel-contributor) | Azure Sentinel-deltagare | ab8e14d6-4a74-4a29-9ba8-549422addade |
> | [Azure Sentinel-läsare](#azure-sentinel-reader) | Azure Sentinel-läsare | 8d289c81-5878-46d4-8554-54e1e3d8b5cb |
> | [Azure Sentinel Responder](#azure-sentinel-responder) | Azure Sentinel Responder | 3e150937-b8fe-4cfb-8069-0eaf05ecd056 |
> | [Nyckelvalvsdeltagare](#key-vault-contributor) | Gör att du kan hantera nyckelvalv, men inte komma åt dem. | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | [Säkerhetsadministratör](#security-admin) | Kan visa säkerhetsprinciper, visa säkerhetstillstånd, redigera säkerhetsprinciper, visa aviseringar och rekommendationer, avvisa aviseringar och rekommendationer. | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | [Rapportör för säkerhetsbedömning](#security-assessment-contributor) | Gör att du kan skicka utvärderingar till Security Center | 612c2aa1-cb24-443b-ac28-3ab7272de6f5 |
> | [Säkerhetshanteraren (äldre)](#security-manager-legacy) | Detta är en arvsroll. Använd säkerhetsadministratör i stället. | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | [Säkerhetsläsare](#security-reader) | Kan visa rekommendationer och aviseringar, visa säkerhetsprinciper, visa säkerhetstillstånd men kan inte göra ändringar. | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **DevOps** |  |  |
> | [DevTest Labs Användare](#devtest-labs-user) | Gör att du kan ansluta, starta om, starta om och stänga av dina virtuella datorer i dina Azure DevTest Labs. | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | [Lab Skapare](#lab-creator) | Gör att du kan skapa, hantera, ta bort dina hanterade labb under dina Azure Lab-konton. | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Övervakare** |  |  |
> | [Deltagare i komponentdeltagare för programinsikter](#application-insights-component-contributor) | Kan hantera Application Insights-komponenter | ae349356-3a1b-4a5e-921d-050484c6347e |
> | [Ögonblicksbildfelsökare för programinsikter](#application-insights-snapshot-debugger) | Ger användaren behörighet att visa och hämta felsökningsögonblicksbilder som samlats in med snapshotfelsökningsfelsökaren för programinsikter. Observera att dessa behörigheter inte ingår i [rollerna Ägare](#owner) eller [Deltagare.](#contributor) | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | [Övervakningsbidragsgivare](#monitoring-contributor) | Kan läsa alla övervakningsdata och redigera övervakningsinställningar. Se även [Komma igång med roller, behörigheter och säkerhet med Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | [Utgivare av övervakningsmått](#monitoring-metrics-publisher) | Aktiverar publiceringsmått mot Azure-resurser | 3913510d-42f4-4e42-8a64-420c390055eb |
> | [Övervakningsläsare](#monitoring-reader) | Kan läsa alla övervakningsdata (mått, loggar, etc.). Se även [Komma igång med roller, behörigheter och säkerhet med Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles). | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | [Arbetsboksdeltagare](#workbook-contributor) | Kan spara delade arbetsböcker. | e8ddcd69-c73f-4f9f-9844-4100522f16ad |
> | [Arbetsboksläsare](#workbook-reader) | Kan läsa arbetsböcker. | b279062a-9be3-42a0-92ae-8b3cf002ec4d |
> | **Förvaltning + styrning** |  |  |
> | [Jobboperator för automatisering](#automation-job-operator) | Skapa och hantera jobb med hjälp av Automation Runbooks. | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | [Automation-operatör](#automation-operator) | Automationsoperatörer kan starta, stoppa, pausa och återuppta jobb | d3881f73-407a-4167-8283-e981cbba0404 |
> | [Automation Runbook Operatör](#automation-runbook-operator) | Läs Runbook-egenskaper - för att kunna skapa jobb i runbooken. | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | [Onboarding-dator för Azure-ansluten dator](#azure-connected-machine-onboarding) | Kan ombord På Azure Connected Machines. | b64e21ea-ac4e-4cdf-9dc9-5b892992bee7 |
> | [Azure-administratör för anslutna datorresurser](#azure-connected-machine-resource-administrator) | Kan läsa, skriva, ta bort och åter in ombord På Azure Connected Machines. | cd570a14-e51a-42ad-bac8-bafd67325302 |
> | [Faktureringsläsare](#billing-reader) | Ger läsåtkomst till faktureringsdata | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | [Medarbetare för skiss](#blueprint-contributor) | Kan hantera skissdefinitioner, men inte tilldela dem. | 41077137-e803-4205-871c-5a86e6a753b4 |
> | [Skissoperatör](#blueprint-operator) | Kan tilldela befintliga publicerade skisser, men kan inte skapa nya ritningar. Observera att detta bara fungerar om tilldelningen görs med en användartilldelad hanterad identitet. | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | [Kostnadshanteringsdeltagare](#cost-management-contributor) | Kan visa kostnader och hantera kostnadskonfiguration (t.ex. budgetar, export) | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | [Läsare av kostnadshantering](#cost-management-reader) | Kan visa kostnadsdata och konfiguration (t.ex. budgetar, export) | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | [Rollen Hanterad programdeltagare](#managed-application-contributor-role) | Gör det möjligt att skapa hanterade programresurser. | 641177b8-a67a-45b9-a033-47bc880bb21e |
> | [Rollen Hanterad programoperator](#managed-application-operator-role) | Gör att du kan läsa och utföra åtgärder på resurser för hanterade program | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | [Läsare av hanterade program](#managed-applications-reader) | Gör att du kan läsa resurser i en hanterad app och begära JIT-åtkomst. | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | [Borttagningsroll för borttagning av hanterade tjänster](#managed-services-registration-assignment-delete-role) | Med rollen borttagning av borttagning av hanterade tjänster kan hantera klientanvändare ta bort registreringstilldelningen som tilldelats klienten. | 91c1777a-f3dc-4fae-b103-61d183457e46 |
> | [Medarbetare i ledningsgruppen](#management-group-contributor) | Rollen som deltagarroll i ledningsgruppen | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | [Läsare av ledningsgrupper](#management-group-reader) | Roll för läsare av hanteringsgrupp | ac63b705-f282-497d-ac71-919bf39d939d |
> | [Ny relik APM-kontodeltagare](#new-relic-apm-account-contributor) | Gör att du kan hantera nya relikprogramprestandahanteringskonton och program, men inte åtkomst till dem. | 5d28c62d-5b37-4476-8438-e587778df237 |
> | [Data writer för policyinsikter (förhandsgranskning)](#policy-insights-data-writer-preview) | Tillåter läsåtkomst till resursprinciper och skrivåtkomst till principhändelser för resurskomponenter. | 66bb4e9e-b016-4a94-8249-4c0511c2be84 |
> | [Deltagare för resursprincip](#resource-policy-contributor) | Användare med behörighet att skapa/ändra resursprincip, skapa supportbiljett och läsa resurser/hierarki. | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | [Deltagare i webbplatsåterställning](#site-recovery-contributor) | Gör att du kan hantera tjänsten Site Recovery utom skapande av valv och rolltilldelning | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | [Operatör för återställning av webbplatser](#site-recovery-operator) | Gör att du kan redundans och redundans men inte utföra andra hanteringsåtgärder för webbplatsåterställning | 494ae006-db33-4328-bf46-533a6560a3ca |
> | [Läsare av återställning av webbplatser](#site-recovery-reader) | Gör att du kan visa status för webbplatsåterställning men inte utföra andra hanteringsåtgärder | dbaaa88c4-0c30-4179-9fb3-46319faa6149 |
> | [Support begäran bidragsgivare](#support-request-contributor) | Gör att du kan skapa och hantera supportförfrågningar | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | [Tagga deltagare](#tag-contributor) | Gör att du kan hantera taggar på entiteter, utan att ge åtkomst till entiteterna själva. | 4a9ae827-6dc8-4573-8ac7-8239d42aa03f |
> | **Andra** |  |  |
> | [BizTalk-medarbetare](#biztalk-contributor) | Gör att du kan hantera BizTalk-tjänster, men inte komma åt dem. | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | [Deltagare i schemaläggarens jobbsamlingar](#scheduler-job-collections-contributor) | Gör att du kan hantera Scheduler-jobbsamlingar, men inte komma åt dem. | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |


## <a name="general"></a>Allmänt


### <a name="contributor"></a>Deltagare

Gör att du kan hantera allt utom att bevilja åtkomst till resurser.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | * | Skapa och hantera resurser av alla typer |
> | **NotActions** |  |
> | Microsoft.Auktorisering/*/Ta bort | Ta bort roller, principtilldelningar, principdefinitioner och principuppsättningsdefinitioner |
> | Microsoft.Auktorisering/*/skrivning | Skapa roller, rolltilldelningar, principtilldelningar, principdefinitioner och principuppsättningsdefinitioner |
> | Microsoft.Authorization/elevateAccess/Åtgärd | Beviljar anroparen Användaråtkomst Administratörsåtkomst för klientomfånget |
> | Microsoft.Blueprint/blueprintTilldelningar/skriva | Skapa eller uppdatera skisstilldelningar |
> | Microsoft.Blueprint/blueprintTilldelningar/borttagning | Ta bort alla skisstilldelningar |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything except access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
  "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [
        "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action",
        "Microsoft.Blueprint/blueprintAssignments/write",
        "Microsoft.Blueprint/blueprintAssignments/delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="owner"></a>Ägare

Gör att du kan hantera allt, inklusive åtkomst till resurser.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | * | Skapa och hantera resurser av alla typer |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything, including access to resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "name": "8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
  "permissions": [
    {
      "actions": [
        "*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader"></a>Läsare

Gör att du kan visa allt, men inte göra några ändringar.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | */läs | Läs resurser av alla slag, utom hemligheter. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything, but not make any changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
  "permissions": [
    {
      "actions": [
        "*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="user-access-administrator"></a>Administratör för användaråtkomst

Gör att du kan hantera användaråtkomst till Azure-resurser.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | */läs | Läs resurser av alla slag, utom hemligheter. |
> | Microsoft.Auktorisering/* | Hantera auktorisering |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage user access to Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "name": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "User Access Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="compute"></a>Compute


### <a name="classic-virtual-machine-contributor"></a>Klassisk deltagare i virtuell dator

Gör att du kan hantera klassiska virtuella datorer, men inte åtkomst till dem, och inte det virtuella nätverks- eller lagringskonto som de är anslutna till.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.ClassicCompute/domainNames/* | Skapa och hantera klassiska beräkningsdomännamn |
> | Microsoft.ClassicCompute/virtualMachines/* | Skapa och hantera virtuella datorer |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Länka en reserverad IP |
> | Microsoft.ClassicNetwork/reservedIps/read Microsoft.ClassicNetwork/reservedIps/read Microsoft.ClassicNetwork/reservedIps/read Microsoft. | Hämtar de reserverade Ips |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Ansluter till det virtuella nätverket. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Hämta det virtuella nätverket. |
> | Microsoft.ClassicStorage/storageAccounts/diskar/läs | Returnerar lagringskontodisken. |
> | Microsoft.ClassicStorage/storageAccounts/images/read Microsoft.ClassicStorage/storageAccounts/images/read Microsoft.ClassicStorage/storageAccounts/images/read Microsoft. | Returnerar avbildningen för lagringskontot. (Föråldrad. Använd 'Microsoft.ClassicStorage/storageAccounts/vmImages') |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Visar en lista över åtkomstnycklarna för lagringskontona. |
> | Microsoft.ClassicStorage/storageAccounts/read Microsoft.ClassicStorage/storageAccounts/read Microsoft.ClassicStorage/storageAccounts/read Microsoft. | Returnera lagringskontot med det angivna kontot. |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "name": "d73bb868-a0df-4d4d-bd69-98a00b01fccb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/domainNames/*",
        "Microsoft.ClassicCompute/virtualMachines/*",
        "Microsoft.ClassicNetwork/networkSecurityGroups/join/action",
        "Microsoft.ClassicNetwork/reservedIps/link/action",
        "Microsoft.ClassicNetwork/reservedIps/read",
        "Microsoft.ClassicNetwork/virtualNetworks/join/action",
        "Microsoft.ClassicNetwork/virtualNetworks/read",
        "Microsoft.ClassicStorage/storageAccounts/disks/read",
        "Microsoft.ClassicStorage/storageAccounts/images/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-administrator-login"></a>Inloggning av administratör för virtuell dator

Visa virtuella datorer i portalen och logga in som administratör

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Network/publicIPAddresses/read | Hämtar en definition av offentlig IP-adress. |
> | Microsoft.Network/virtualNetworks/read | Hämta definitionen för virtuellt nätverk |
> | Microsoft.Network/loadBalancers/read | Hämtar en belastningsutjämnad definition |
> | Microsoft.Network/networkInterfaces/read | Hämtar en definition av nätverksgränssnitt.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.Compute/virtualMachines/login/action | Logga in på en virtuell dator som en vanlig användare |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Logga in på en virtuell dator med Windows-administratör eller Linux-rotanvändarbehörighet |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as administrator",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "name": "1c0163c0-47e6-4577-8991-ea5c82e286e4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action",
        "Microsoft.Compute/virtualMachines/loginAsAdmin/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Administrator Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-contributor"></a>Virtuell datordeltagare

Gör att du kan hantera virtuella datorer, men inte åtkomst till dem, och inte det virtuella nätverks- eller lagringskonto som de är anslutna till.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Compute/availabilitySets/* | Skapa och hantera beräkningstillgänglighetsuppsättningar |
> | Microsoft.Beräkna/platser/* | Skapa och hantera beräkningsplatser |
> | Microsoft.Compute/virtualMachines/* | Skapa och hantera virtuella datorer |
> | Microsoft.Compute/virtualMachineScaleSets/* | Skapa och hantera VM-skalningsuppsättningar |
> | Microsoft.Compute/disks/write | Skapar en ny disk eller uppdaterar en befintlig |
> | Microsoft.Compute/disks/read | Hämta egenskaperna för en disk |
> | Microsoft.Beräkna/diskar/ta bort | Tar bort disken |
> | Microsoft.DevTestLab/scheman/* |  |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Ansluter till en backend-adresspool för programgateway. Inte alert. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Ansluter till en bakåtsträvningsadresspool för belastningsutjämnare. Inte alert. |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Ansluter till en lastutjämnad NAT-pool. Inte alertable. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Ansluter till en ingående nat-regel för lastjämning. Inte alert. |
> | Microsoft.Network/loadBalancers/probes/join/action | Tillåter användning av avsökningar av en belastningsutjämnare. Med den här behörigheten healthProbe-egenskap för VM-skalningsuppsättning kan till exempel referera till avsökningen. Inte alertable. |
> | Microsoft.Network/loadBalancers/read | Hämtar en belastningsutjämnad definition |
> | Microsoft.Network/locations/* | Skapa och hantera nätverksplatser |
> | Microsoft.Network/networkInterfaces/* | Skapa och hantera nätverksgränssnitt |
> | Microsoft.Network/networkSecurityGroups/join/action | Ansluter till en nätverkssäkerhetsgrupp. Inte alert. |
> | Microsoft.Network/networkSecurityGroups/read | Hämtar en definition av nätverkssäkerhetsgrupp |
> | Microsoft.Network/publicIPAddresses/join/action | Ansluter till en offentlig IP-adress. Inte alert. |
> | Microsoft.Network/publicIPAddresses/read | Hämtar en definition av offentlig IP-adress. |
> | Microsoft.Network/virtualNetworks/read | Hämta definitionen för virtuellt nätverk |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Ansluter till ett virtuellt nätverk. Inte alert. |
> | Microsoft.RecoveryServices/platser/* |  |
> | Microsoft.RecoveryServices/Valv/backupFabrics/backupProtectionIntent/write | Skapa en skyddsmetod för säkerhetskopiering |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read Microsoft. | Returnerar objektinformation för det skyddade objektet |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Skapa ett skyddat objekt för säkerhetskopiering |
> | Microsoft.RecoveryServices/Valv/backupPolicies/read | Returnerar alla skyddsprinciper |
> | Microsoft.RecoveryServices/Valv/backupPolicies/write | Skapar skyddsprincip |
> | Microsoft.RecoveryServices/Valv/läsa | Åtgärden Hämta arkiv hämtar ett objekt som representerar Azure-resursen av typen "valv" |
> | Microsoft.RecoveryServices/Vaults/usages/read Microsoft.RecoveryServices/Vaults/usages/read Microsoft.RecoveryServices/Vaults/usages/read Microsoft. | Returnerar användningsinformation för ett Recovery Services Vault. |
> | Microsoft.RecoveryServices/Valv/skrivning | Skapa Arkiv-åtgärden skapar en Azure-resurs av typen "valv" |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.SqlVirtualMachine/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Returnerar åtkomstnycklarna för det angivna lagringskontot. |
> | Microsoft.Storage/storageAccounts/read | Returnerar listan över lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they're connected to.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "name": "9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/locations/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/virtualMachineScaleSets/*",
        "Microsoft.Compute/disks/write",
        "Microsoft.Compute/disks/read",
        "Microsoft.Compute/disks/delete",
        "Microsoft.DevTestLab/schedules/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/loadBalancers/probes/join/action",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/locations/*",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Network/networkSecurityGroups/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/write",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.SqlVirtualMachine/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="virtual-machine-user-login"></a>Användarinloggning för virtuell dator

Visa virtuella datorer i portalen och logga in som en vanlig användare.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Network/publicIPAddresses/read | Hämtar en definition av offentlig IP-adress. |
> | Microsoft.Network/virtualNetworks/read | Hämta definitionen för virtuellt nätverk |
> | Microsoft.Network/loadBalancers/read | Hämtar en belastningsutjämnad definition |
> | Microsoft.Network/networkInterfaces/read | Hämtar en definition av nätverksgränssnitt.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.Compute/virtualMachines/login/action | Logga in på en virtuell dator som en vanlig användare |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "View Virtual Machines in the portal and login as a regular user.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb879df8-f326-4884-b1cf-06f3ad86be52",
  "name": "fb879df8-f326-4884-b1cf-06f3ad86be52",
  "permissions": [
    {
      "actions": [
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/loadBalancers/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Compute/virtualMachines/*/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Compute/virtualMachines/login/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Virtual Machine User Login",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="networking"></a>Nätverk


### <a name="cdn-endpoint-contributor"></a>CDN-slutpunktsdeltagare

Kan hantera CDN-slutpunkter, men kan inte bevilja åtkomst till andra användare.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresultat/* |  |
> | Microsoft.Cdn/profiler/slutpunkter/* |  |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "name": "426e0c7f-0c7e-4658-b36f-ff54d6c29b45",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-endpoint-reader"></a>CDN-slutpunktsläsare

Kan visa CDN-slutpunkter, men kan inte göra ändringar.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresultat/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "name": "871e35f6-b5c1-49cc-a043-bde969a0f2cd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/endpoints/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Endpoint Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-contributor"></a>Deltagare i CDN-profil

Kan hantera CDN-profiler och deras slutpunkter, men kan inte ge åtkomst till andra användare.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresultat/* |  |
> | Microsoft.Cdn/profiler/* |  |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage CDN profiles and their endpoints, but can't grant access to other users.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "name": "ec156ff8-a8d1-4d15-830c-5b80698ca432",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cdn-profile-reader"></a>CDN-profilläsare

Kan visa CDN-profiler och deras slutpunkter, men kan inte göra ändringar.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresultat/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view CDN profiles and their endpoints, but can't make changes.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8f96442b-4075-438f-813d-ad51ab4019af",
  "name": "8f96442b-4075-438f-813d-ad51ab4019af",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cdn/edgenodes/read",
        "Microsoft.Cdn/operationresults/*",
        "Microsoft.Cdn/profiles/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CDN Profile Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-network-contributor"></a>Klassisk nätverksdeltagare

Gör att du kan hantera klassiska nätverk, men inte åtkomst till dem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.ClassicNetwork/* | Skapa och hantera klassiska nätverk |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "name": "b34d265f-36f7-4a0d-a4d4-e158ca92e90f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicNetwork/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="dns-zone-contributor"></a>DELTAGARE I DNS-zonen

Gör att du kan hantera DNS-zoner och postuppsättningar i Azure DNS, men låter dig inte styra vem som har åtkomst till dem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Network/dnsZones/* | Skapa och hantera DNS-zoner och poster |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DNS zones and record sets in Azure DNS, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/befefa01-2a29-4197-83a8-272ff33ce314",
  "name": "befefa01-2a29-4197-83a8-272ff33ce314",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/dnsZones/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DNS Zone Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="network-contributor"></a>Nätverksdeltagare

Gör att du kan hantera nätverk, men inte åtkomst till dem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Network/* | Skapa och hantera nätverk |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage networks, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4d97b98b-1d4f-4787-a291-c67834d212e7",
  "name": "4d97b98b-1d4f-4787-a291-c67834d212e7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Network Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="traffic-manager-contributor"></a>Traffic Manager-deltagare

Gör att du kan hantera Traffic Manager-profiler, men du kan inte styra vem som har åtkomst till dem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Traffic Manager profiles, but does not let you control who has access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "name": "a4b10055-b0c7-44c2-b00f-c7b5b3550cf7",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/trafficManagerProfiles/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Traffic Manager Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="storage"></a>Storage


### <a name="avere-contributor"></a>Avere Bidragsgivare

Kan skapa och hantera ett Avere vFXT-kluster.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Beräkna/*/läsa |  |
> | Microsoft.Compute/availabilitySets/* |  |
> | Microsoft.Compute/virtualMachines/* |  |
> | Microsoft.Compute/diskar/* |  |
> | Microsoft.Network/*/read |  |
> | Microsoft.Network/networkInterfaces/* |  |
> | Microsoft.Network/virtualNetworks/read | Hämta definitionen för virtuellt nätverk |
> | Microsoft.Network/virtualNetworks/subnets/read | Hämtar en definition av virtuellt nätverksundernät |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Ansluter till ett virtuellt nätverk. Inte alert. |
> | Microsoft.Network/virtualNetworks/subnät/joinViaServiceEndpoint/åtgärd | Sammanfogar resurs som lagringskonto eller SQL-databas till ett undernät. Inte alertable. |
> | Microsoft.Network/networkSecurityGroups/join/action | Ansluter till en nätverkssäkerhetsgrupp. Inte alert. |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Storage/*/read |  |
> | Microsoft.Storage/storageAccounts/* | Skapa och hantera lagringskonton |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | Microsoft.Resurser/prenumerationer/resourceGroups/resurser/läsa | Hämtar resurserna för resursgruppen. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobbar/delete | Returnerar resultatet av att ta bort en blob |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobbar/read | Returnerar en blob eller en lista med blobbar |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobbar/write | Returnerar resultatet av att skriva en blob |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can create and manage an Avere vFXT cluster.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "name": "4f8fab4f-1852-4a58-a46a-8eaf358af14a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/*/read",
        "Microsoft.Compute/availabilitySets/*",
        "Microsoft.Compute/virtualMachines/*",
        "Microsoft.Compute/disks/*",
        "Microsoft.Network/*/read",
        "Microsoft.Network/networkInterfaces/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/*/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="avere-operator"></a>Avere-operatör

Används av Avere vFXT-klustret för att hantera klustret

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Compute/virtualMachines/read | Hämta egenskaperna för en virtuell dator |
> | Microsoft.Network/networkInterfaces/read | Hämtar en definition av nätverksgränssnitt.  |
> | Microsoft.Network/networkInterfaces/write | Skapar ett nätverksgränssnitt eller uppdaterar ett befintligt nätverksgränssnitt.  |
> | Microsoft.Network/virtualNetworks/read | Hämta definitionen för virtuellt nätverk |
> | Microsoft.Network/virtualNetworks/subnets/read | Hämtar en definition av virtuellt nätverksundernät |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Ansluter till ett virtuellt nätverk. Inte alert. |
> | Microsoft.Network/networkSecurityGroups/join/action | Ansluter till en nätverkssäkerhetsgrupp. Inte alert. |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Returnerar resultatet av att ta bort en behållare |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Returnerar lista över behållare |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Returnerar resultatet av put blob-behållare |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobbar/delete | Returnerar resultatet av att ta bort en blob |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobbar/read | Returnerar en blob eller en lista med blobbar |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobbar/write | Returnerar resultatet av att skriva en blob |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Used by the Avere vFXT cluster to manage the cluster",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "name": "c025889f-8102-4ebf-b32c-fc0c6f0c6bd9",
  "permissions": [
    {
      "actions": [
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.Network/virtualNetworks/subnets/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Network/networkSecurityGroups/join/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Avere Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-contributor"></a>Deltagare för säkerhetskopiering

Gör att du kan hantera säkerhetskopieringstjänsten, men kan inte skapa valv och ge åtkomst till andra

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Network/virtualNetworks/read | Hämta definitionen för virtuellt nätverk |
> | Microsoft.RecoveryServices/platser/* |  |
> | Microsoft.RecoveryServices/Valv/säkerhetskopieringFabric/operationResultat/* | Hantera resultat av åtgärden vid säkerhetskopieringshantering |
> | Microsoft.RecoveryServices/Valv/backupFabrics/protectionContainers/* | Skapa och hantera reservbehållare inuti säkerhetskopieringstyger i Recovery Services-valvet |
> | Microsoft.RecoveryServices/Valv/säkerhetskopieringFabric/refreshContainers/åtgärd | Uppdaterar behållarlistan |
> | Microsoft.RecoveryServices/Valv/säkerhetskopieringJobs/* | Skapa och hantera säkerhetskopieringsjobb |
> | Microsoft.RecoveryServices/Valv/säkerhetskopieringJobsExport/åtgärd | Exportera jobb |
> | Microsoft.RecoveryServices/Valv/säkerhetskopieringOperationResultat/* | Skapa och hantera resultat för säkerhetskopieringshantering |
> | Microsoft.RecoveryServices/Valv/backupPolicies/* | Skapa och hantera principer för säkerhetskopiering |
> | Microsoft.RecoveryServices/Valv/backupSkyddaobjekt/* | Skapa och hantera artiklar som kan säkerhetskopieras |
> | Microsoft.RecoveryServices/Valv/säkerhetskopieringSkyddadeobjekt/* | Skapa och hantera säkerhetskopierade artiklar |
> | Microsoft.RecoveryServices/Valv/backupSkyddContainers/* | Skapa och hantera behållare som innehåller säkerhetskopior |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Returnerar sammanfattningar för skyddade objekt och skyddade servrar för en återställningstjänster . |
> | Microsoft.RecoveryServices/Valv/certifikat/* | Skapa och hantera certifikat relaterade till säkerhetskopiering i Recovery Services-valvet |
> | Microsoft.RecoveryServices/Valv/extendedInformation/* | Skapa och hantera utökad information relaterad till valv |
> | Microsoft.RecoveryServices/Valv/övervakningAlerts/läsa | Hämtar aviseringarna för valvet för återställningstjänster. |
> | Microsoft.RecoveryServices/Valv/övervakningKonfigurationer/* |  |
> | Microsoft.RecoveryServices/Valv/läsa | Åtgärden Hämta arkiv hämtar ett objekt som representerar Azure-resursen av typen "valv" |
> | Microsoft.RecoveryServices/Vaults/registeredId-enheter/* | Skapa och hantera registrerade identiteter |
> | Microsoft.RecoveryServices/Valv/användningar/* | Skapa och hantera användning av Recovery Services-valv |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Storage/storageAccounts/read | Returnerar listan över lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
> | Microsoft.RecoveryServices/Valv/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Valv/backupconfig/* |  |
> | Microsoft.RecoveryServices/Valv/säkerhetskopieringValidateOperation/åtgärd | Verifiera åtgärd för skyddat objekt |
> | Microsoft.RecoveryServices/Valv/skrivning | Skapa Arkiv-åtgärden skapar en Azure-resurs av typen "valv" |
> | Microsoft.RecoveryServices/Valv/backupOperations/read | Returnerar säkerhetskopieringsåtgärdsstatus för Recovery Services Vault. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read Microsoft.RecoveryServices/Vaults/backupEngines/read Microsoft.RecoveryServices/Vaults/backupEngines/read Microsoft. | Returnerar alla servrar för säkerhetskopieringshantering som är registrerade med valvet. |
> | Microsoft.RecoveryServices/Valv/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Få alla skyddsbara behållare |
> | Microsoft.RecoveryServices/locations/backupStatus/åtgärd | Kontrollera säkerhetskopieringsstatus för recovery services-valv |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/platser/backupValidateFeatures/action | Verifiera funktioner |
> | Microsoft.RecoveryServices/Valv/övervakningAlerts/skrivning | Löser aviseringen. |
> | Microsoft.RecoveryServices/åtgärder/läs | Åtgärden returnerar listan över åtgärder för en resursprovider |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Hämtar åtgärdsstatus för en viss operation |
> | Microsoft.RecoveryServices/Valv/backupSkyddIntents/read | Lista alla säkerhetsavståendsskyddsavsikter |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup service,but can't create vaults and give access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e467623-bb1f-42f4-a55d-6e525e11384b",
  "name": "5e467623-bb1f-42f4-a55d-6e525e11384b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/*",
        "Microsoft.RecoveryServices/Vaults/backupSecurityPIN/*",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/*",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/Vaults/usages/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/write",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/*",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-operator"></a>Operatör för säkerhetskopiering

Gör att du kan hantera säkerhetskopieringstjänster, förutom borttagning av säkerhetskopiering, valvskapande och ge tillgång till andra

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Network/virtualNetworks/read | Hämta definitionen för virtuellt nätverk |
> | Microsoft.RecoveryServices/Valv/backupFabric/operationResultat/läs | Returnerar status för operationen |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResultat/läs | Får resultat av Åtgärden som utförs på skyddsbehållaren. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Utför säkerhetskopiering för skyddade objekt. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResultat/läs | Hämtar resultat av åtgärden utförd på skyddade objekt. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Returnerar status för åtgärden som utförs på skyddade objekt. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read Microsoft. | Returnerar objektinformation för det skyddade objektet |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Återställning av omedelbar artikel för skyddat objekt för skyddat objekt |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read Microsoft. | Hämta återställningspunkter för skyddade objekt. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Återställ återställningspunkter för skyddade objekt. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Återkalla omedelbar objektåterställning för skyddat objekt |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Skapa ett skyddat objekt för säkerhetskopiering |
> | Microsoft.RecoveryServices/Valv/backupFabrics/protectionContainers/read | Returnerar alla registrerade behållare |
> | Microsoft.RecoveryServices/Valv/säkerhetskopieringFabric/refreshContainers/åtgärd | Uppdaterar behållarlistan |
> | Microsoft.RecoveryServices/Valv/säkerhetskopieringJobs/* | Skapa och hantera säkerhetskopieringsjobb |
> | Microsoft.RecoveryServices/Valv/säkerhetskopieringJobsExport/åtgärd | Exportera jobb |
> | Microsoft.RecoveryServices/Valv/säkerhetskopieringOperationResultat/* | Skapa och hantera resultat för säkerhetskopieringshantering |
> | Microsoft.RecoveryServices/Valv/säkerhetskopieringPolicies/operationResultat/läs | Hämta resultat från policyåtgärden. |
> | Microsoft.RecoveryServices/Valv/backupPolicies/read | Returnerar alla skyddsprinciper |
> | Microsoft.RecoveryServices/Valv/backupSkyddaobjekt/* | Skapa och hantera artiklar som kan säkerhetskopieras |
> | Microsoft.RecoveryServices/Vaults/backupSkyddadeobjekt/läsa | Returnerar listan över alla skyddade objekt. |
> | Microsoft.RecoveryServices/Valv/backupSkyddContainers/read | Returnerar alla behållare som tillhör prenumerationen |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Returnerar sammanfattningar för skyddade objekt och skyddade servrar för en återställningstjänster . |
> | Microsoft.RecoveryServices/Vaults/certifikat/skrivning | Åtgärden Uppdatera resurscertifikat uppdaterar certifikatet för resurs-/valvautentiseringsuppgifter. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read Microsoft.RecoveryServices/Vaults/extendedInformation/read Microsoft.RecoveryServices/Vaults/extendedInformation/read Microsoft. | Åtgärden Hämta utökad information hämtar ett objekts utökade information som representerar Azure-resursen av typen ?valv? |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Åtgärden Hämta utökad information hämtar ett objekts utökade information som representerar Azure-resursen av typen ?valv? |
> | Microsoft.RecoveryServices/Valv/övervakningAlerts/läsa | Hämtar aviseringarna för valvet för återställningstjänster. |
> | Microsoft.RecoveryServices/Valv/övervakningKonfigurationer/* |  |
> | Microsoft.RecoveryServices/Valv/läsa | Åtgärden Hämta arkiv hämtar ett objekt som representerar Azure-resursen av typen "valv" |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Åtgärden Hämta operationsresultat kan användas för att hämta operationsstatus och resultat för den asynkront skickade åtgärden |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Åtgärden Hämta behållare kan användas för att hämta behållarna som registrerats för en resurs. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Åtgärden Register Service Container kan användas för att registrera en behållare med återställningstjänst. |
> | Microsoft.RecoveryServices/Vaults/usages/read Microsoft.RecoveryServices/Vaults/usages/read Microsoft.RecoveryServices/Vaults/usages/read Microsoft. | Returnerar användningsinformation för ett Recovery Services Vault. |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Storage/storageAccounts/read | Returnerar listan över lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
> | Microsoft.RecoveryServices/Valv/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Valv/säkerhetskopieringValidateOperation/åtgärd | Verifiera åtgärd för skyddat objekt |
> | Microsoft.RecoveryServices/Valv/backupOperations/read | Returnerar säkerhetskopieringsåtgärdsstatus för Recovery Services Vault. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Hämta status för principåtgärden. |
> | Microsoft.RecoveryServices/Valv/backupFabrics/protectionContainers/write | Skapar en registrerad behållare |
> | Microsoft.RecoveryServices/Valv/säkerhetskopieringFabric/protectionContainers/fråga/åtgärd | Gör förfrågan för arbetsbelastningar i en behållare |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read Microsoft.RecoveryServices/Vaults/backupEngines/read Microsoft.RecoveryServices/Vaults/backupEngines/read Microsoft. | Returnerar alla servrar för säkerhetskopieringshantering som är registrerade med valvet. |
> | Microsoft.RecoveryServices/Valv/backupFabrics/backupProtectionIntent/write | Skapa en skyddsmetod för säkerhetskopiering |
> | Microsoft.RecoveryServices/Valv/backupFabrics/backupProtectionIntent/read | Skaffa en säkerhetsavstjandemetod |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Få alla skyddsbara behållare |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Hämta alla artiklar i en behållare |
> | Microsoft.RecoveryServices/locations/backupStatus/åtgärd | Kontrollera säkerhetskopieringsstatus för recovery services-valv |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/platser/backupValidateFeatures/action | Verifiera funktioner |
> | Microsoft.RecoveryServices/Valv/övervakningAlerts/skrivning | Löser aviseringen. |
> | Microsoft.RecoveryServices/åtgärder/läs | Åtgärden returnerar listan över åtgärder för en resursprovider |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Hämtar åtgärdsstatus för en viss operation |
> | Microsoft.RecoveryServices/Valv/backupSkyddIntents/read | Lista alla säkerhetsavståendsskyddsavsikter |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage backup services, except removal of backup, vault creation and giving access to others",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/00c29273-979b-4161-815c-10b084fb9324",
  "name": "00c29273-979b-4161-815c-10b084fb9324",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action",
        "Microsoft.RecoveryServices/Vaults/backupJobs/*",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/*",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectableItems/*",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/write",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/write",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/*",
        "Microsoft.RecoveryServices/Vaults/backupValidateOperation/action",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/locations/backupPreValidateProtection/action",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="backup-reader"></a>Läsare för säkerhetskopiering

Kan visa säkerhetskopieringstjänster, men kan inte göra ändringar

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read Microsoft.RecoveryServices/locations/allocatedStamp/read Microsoft.RecoveryServices/locations/allocatedStamp/read Microsoft. | GetAllocatedStamp är intern åtgärd som används av tjänsten |
> | Microsoft.RecoveryServices/Valv/backupFabric/operationResultat/läs | Returnerar status för operationen |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResultat/läs | Får resultat av Åtgärden som utförs på skyddsbehållaren. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResultat/läs | Hämtar resultat av åtgärden utförd på skyddade objekt. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Returnerar status för åtgärden som utförs på skyddade objekt. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read Microsoft. | Returnerar objektinformation för det skyddade objektet |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read Microsoft. | Hämta återställningspunkter för skyddade objekt. |
> | Microsoft.RecoveryServices/Valv/backupFabrics/protectionContainers/read | Returnerar alla registrerade behållare |
> | Microsoft.RecoveryServices/Valv/säkerhetskopieringJobs/operationResultat/läs | Returnerar resultatet av projektoperationen. |
> | Microsoft.RecoveryServices/Valv/säkerhetskopieringJobs/läs | Returnerar alla jobbobjekt |
> | Microsoft.RecoveryServices/Valv/säkerhetskopieringJobsExport/åtgärd | Exportera jobb |
> | Microsoft.RecoveryServices/Valv/säkerhetskopieringOperationResultat/läs | Returnerar säkerhetskopieringsåtgärdsresultat för Recovery Services Vault. |
> | Microsoft.RecoveryServices/Valv/säkerhetskopieringPolicies/operationResultat/läs | Hämta resultat från policyåtgärden. |
> | Microsoft.RecoveryServices/Valv/backupPolicies/read | Returnerar alla skyddsprinciper |
> | Microsoft.RecoveryServices/Vaults/backupSkyddadeobjekt/läsa | Returnerar listan över alla skyddade objekt. |
> | Microsoft.RecoveryServices/Valv/backupSkyddContainers/read | Returnerar alla behållare som tillhör prenumerationen |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Returnerar sammanfattningar för skyddade objekt och skyddade servrar för en återställningstjänster . |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read Microsoft.RecoveryServices/Vaults/extendedInformation/read Microsoft.RecoveryServices/Vaults/extendedInformation/read Microsoft. | Åtgärden Hämta utökad information hämtar ett objekts utökade information som representerar Azure-resursen av typen ?valv? |
> | Microsoft.RecoveryServices/Valv/övervakningAlerts/läsa | Hämtar aviseringarna för valvet för återställningstjänster. |
> | Microsoft.RecoveryServices/Valv/läsa | Åtgärden Hämta arkiv hämtar ett objekt som representerar Azure-resursen av typen "valv" |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Åtgärden Hämta operationsresultat kan användas för att hämta operationsstatus och resultat för den asynkront skickade åtgärden |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Åtgärden Hämta behållare kan användas för att hämta behållarna som registrerats för en resurs. |
> | Microsoft.RecoveryServices/Valv/backupstorageconfig/read | Returnerar lagringskonfiguration för Recovery Services Vault. |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read Microsoft.RecoveryServices/Vaults/backupconfig/read Microsoft.RecoveryServices/Vaults/backupconfig/read Microsoft. | Returnerar konfiguration för Recovery Services Vault. |
> | Microsoft.RecoveryServices/Valv/backupOperations/read | Returnerar säkerhetskopieringsåtgärdsstatus för Recovery Services Vault. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Hämta status för principåtgärden. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read Microsoft.RecoveryServices/Vaults/backupEngines/read Microsoft.RecoveryServices/Vaults/backupEngines/read Microsoft. | Returnerar alla servrar för säkerhetskopieringshantering som är registrerade med valvet. |
> | Microsoft.RecoveryServices/Valv/backupFabrics/backupProtectionIntent/read | Skaffa en säkerhetsavstjandemetod |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Hämta alla artiklar i en behållare |
> | Microsoft.RecoveryServices/locations/backupStatus/åtgärd | Kontrollera säkerhetskopieringsstatus för recovery services-valv |
> | Microsoft.RecoveryServices/Valv/övervakningKonfigurationer/* |  |
> | Microsoft.RecoveryServices/Valv/övervakningAlerts/skrivning | Löser aviseringen. |
> | Microsoft.RecoveryServices/åtgärder/läs | Åtgärden returnerar listan över åtgärder för en resursprovider |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Hämtar åtgärdsstatus för en viss operation |
> | Microsoft.RecoveryServices/Valv/backupSkyddIntents/read | Lista alla säkerhetsavståendsskyddsavsikter |
> | Microsoft.RecoveryServices/Vaults/usages/read Microsoft.RecoveryServices/Vaults/usages/read Microsoft.RecoveryServices/Vaults/usages/read Microsoft. | Returnerar användningsinformation för ett Recovery Services Vault. |
> | Microsoft.RecoveryServices/platser/backupValidateFeatures/action | Verifiera funktioner |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view backup services, but can't make changes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "name": "a795c7a0-d4a2-40c1-ae25-d81f01202912",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupJobs/read",
        "Microsoft.RecoveryServices/Vaults/backupJobsExport/action",
        "Microsoft.RecoveryServices/Vaults/backupOperationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectedItems/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read",
        "Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/Vaults/backupstorageconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupconfig/read",
        "Microsoft.RecoveryServices/Vaults/backupOperations/read",
        "Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read",
        "Microsoft.RecoveryServices/Vaults/backupEngines/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read",
        "Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read",
        "Microsoft.RecoveryServices/locations/backupStatus/action",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/*",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/write",
        "Microsoft.RecoveryServices/operations/read",
        "Microsoft.RecoveryServices/locations/operationStatus/read",
        "Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/locations/backupValidateFeatures/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Backup Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-contributor"></a>Deltagare i det klassiska lagringskontot

Gör att du kan hantera klassiska lagringskonton, men inte komma åt dem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.ClassicStorage/storageAccounts/* | Skapa och hantera lagringskonton |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage classic storage accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "name": "86e8f5dc-a6e9-4c67-9d15-de283e8eac25",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="classic-storage-account-key-operator-service-role"></a>Rollen för nyckeloperatörstjänsten för klassiskt lagringskonto

Klassiska nyckelansvariga för lagringskonton tillåts lista och återskapa nycklar på klassiska lagringskonton

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Visar en lista över åtkomstnycklarna för lagringskontona. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Återskapar befintliga åtkomstnycklar för lagringskontot. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Classic Storage Account Key Operators are allowed to list and regenerate keys on Classic Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "name": "985d6b00-f706-48f5-a6fe-d0ca12fb668d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ClassicStorage/storageAccounts/listkeys/action",
        "Microsoft.ClassicStorage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Classic Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-contributor"></a>Data Box Deltagare

Gör att du kan hantera allt under Data Box Service förutom att ge åtkomst till andra.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | Microsoft.Databox/* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage everything under Data Box Service except giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/add466c9-e687-43fc-8d98-dfcf8d720be5",
  "name": "add466c9-e687-43fc-8d98-dfcf8d720be5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Databox/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-box-reader"></a>Läsare av dataruta

Gör att du kan hantera Data Box Service förutom att skapa order- eller redigeringsorderinformation och ge åtkomst till andra.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Databox/*/read |  |
> | Microsoft.Databox/jobb/listsecrets/åtgärd |  |
> | Microsoft.Databox/jobb/listcredentials/åtgärd | Visar en lista över okrypterade autentiseringsuppgifter som är relaterade till ordern. |
> | Microsoft.Databox/platser/availableSkus/action | Den här metoden returnerar listan över tillgängliga skus. |
> | Microsoft.Databox/platser/validateInputs/åtgärd | Den här metoden gör alla typer av valideringar. |
> | Microsoft.Databox/platser/regionKonfigurering/åtgärd | Den här metoden returnerar konfigurationerna för regionen. |
> | Microsoft.Databox/platser/validateAddress/action | Validerar leveransadressen och tillhandahåller eventuella alternativa adresser. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Data Box Service except creating order or editing order details and giving access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "name": "028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Databox/*/read",
        "Microsoft.Databox/jobs/listsecrets/action",
        "Microsoft.Databox/jobs/listcredentials/action",
        "Microsoft.Databox/locations/availableSkus/action",
        "Microsoft.Databox/locations/validateInputs/action",
        "Microsoft.Databox/locations/regionConfiguration/action",
        "Microsoft.Databox/locations/validateAddress/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Box Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-lake-analytics-developer"></a>Utvecklare av DataSjöanalys

Gör att du kan skicka, övervaka och hantera dina egna jobb men inte skapa eller ta bort DataSjöanalyskonton.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete Microsoft.DataLakeAnalytics/accounts/Delete Microsoft.DataLakeAnalytics/accounts/Delete Microsoft. | Ta bort ett DataLakeAnalytics-konto. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Bevilja behörigheter för att avbryta jobb som skickats av andra användare. |
> | Microsoft.DataLakeAnalytics/accounts/Write | Skapa eller uppdatera ett DataLakeAnalytics-konto. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write Microsoft. | Skapa eller uppdatera ett länkat DataLakeStore-konto för ett DataLakeAnalytics-konto. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete Microsoft. | Ta bort länken till ett DataLakeStore-konto från ett DataLakeAnalytics-konto. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Skapa eller uppdatera ett länkat lagringskonto för ett DataLakeAnalytics-konto. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete Microsoft. | Ta bort länken till ett lagringskonto från ett DataLakeAnalytics-konto. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Skapa eller uppdatera en brandväggsregel. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete Microsoft. | Ta bort en brandväggsregel. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Skapa eller uppdatera en beräkningsprincip. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete Microsoft. | Ta bort en beräkningsprincip. |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you submit, monitor, and manage your own jobs but not create or delete Data Lake Analytics accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/47b7735b-770e-4598-a7da-8b91488b4c88",
  "name": "47b7735b-770e-4598-a7da-8b91488b4c88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BigAnalytics/accounts/*",
        "Microsoft.DataLakeAnalytics/accounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.BigAnalytics/accounts/Delete",
        "Microsoft.BigAnalytics/accounts/TakeOwnership/action",
        "Microsoft.BigAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action",
        "Microsoft.DataLakeAnalytics/accounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write",
        "Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Write",
        "Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Write",
        "Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Lake Analytics Developer",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="reader-and-data-access"></a>Läsar- och dataåtkomst

Gör att du kan visa allt men låter dig inte ta bort eller skapa ett lagringskonto eller en resurs. Det kommer också att tillåta läs-/skrivåtkomst till alla data som finns i ett lagringskonto via åtkomst till lagringskontonycklar.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Returnerar åtkomstnycklarna för det angivna lagringskontot. |
> | Microsoft.Storage/storageAccounts/ListAccountSas/åtgärd | Returnerar konto-SAS-token för det angivna lagringskontot. |
> | Microsoft.Storage/storageAccounts/read | Returnerar listan över lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view everything but will not let you delete or create a storage account or contained resource. It will also allow read/write access to all data contained in a storage account via access to storage account keys.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c12c1c16-33a1-487b-954d-41c89c60f349",
  "name": "c12c1c16-33a1-487b-954d-41c89c60f349",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageAccounts/ListAccountSas/action",
        "Microsoft.Storage/storageAccounts/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Reader and Data Access",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-contributor"></a>Lagringskontodeltagare

Tillåter hantering av lagringskonton. Ger åtkomst till kontonyckeln, som kan användas för att komma åt data via auktorisering av delade nyckel.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Insights/diagnosticSettings/* | Skapar, uppdaterar eller läser diagnostikinställningen för Analysis Server |
> | Microsoft.Network/virtualNetworks/subnät/joinViaServiceEndpoint/åtgärd | Sammanfogar resurs som lagringskonto eller SQL-databas till ett undernät. Inte alertable. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Storage/storageAccounts/* | Skapa och hantera lagringskonton |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage storage accounts, including accessing storage account keys which provide full access to storage account data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "name": "17d1049b-9a84-46fb-8f53-869881c3d3ab",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-account-key-operator-service-role"></a>Nyckeloperatörsroll för lagringskonto

Tillåter listning och återskapande av åtkomstnycklar för lagringskonto.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Storage/storageKonto/listnycklar/åtgärd | Returnerar åtkomstnycklarna för det angivna lagringskontot. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Återskapar åtkomstnycklarna för det angivna lagringskontot. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Storage Account Key Operators are allowed to list and regenerate keys on Storage Accounts",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/81a9662b-bebf-436f-a333-f67b29880f12",
  "name": "81a9662b-bebf-436f-a333-f67b29880f12",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/regeneratekey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Account Key Operator Service Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-contributor"></a>Storage Blob Data Contributor

Läsa, skriva och ta bort Azure Storage-behållare och blobbar. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i [Behörigheter för att anropa blob- och ködataåtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Ta bort en behållare. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Returnera en behållare eller en lista med behållare. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Ändra en behållares metadata eller egenskaper. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/åtgärd | Returnerar en användardelegeringsnyckel för Blob-tjänsten. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobbar/delete | Ta bort en blob. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobbar/read | Returnera en blob eller en lista med blobbar. |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobbar/write | Skriv till en blob. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write and delete access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "name": "ba92f5b4-2d11-453d-a403-e96b0029c9fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/write",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-owner"></a>Ägare av lagringsblobbdata

Ger fullständig åtkomst till Azure Storage blob-behållare och data, inklusive tilldelning av POSIX-åtkomstkontroll. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i [Behörigheter för att anropa blob- och ködataåtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/* | Fullständiga behörigheter för behållare. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/åtgärd | Returnerar en användardelegeringsnyckel för Blob-tjänsten. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobbar/* | Fullständig behörighet för blobbar. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "name": "b7e6dc6d-f1e8-4753-8033-0f276bb0955b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/*",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-data-reader"></a>Dataläsare för lagringsblobb

Läs och lista Azure Storage-behållare och blobbar. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i [Behörigheter för att anropa blob- och ködataåtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Returnera en behållare eller en lista med behållare. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/åtgärd | Returnerar en användardelegeringsnyckel för Blob-tjänsten. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobbar/read | Returnera en blob eller en lista med blobbar. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage blob containers and data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "name": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/read",
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-blob-delegator"></a>Lagring Blob Delegator

Hämta en användardelegeringsnyckel som sedan kan användas för att skapa en signatur för delad åtkomst för en behållare eller blob som är signerad med Azure AD-autentiseringsuppgifter. Mer information finns i [Skapa en SAS för användardelegering](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/åtgärd | Returnerar en användardelegeringsnyckel för Blob-tjänsten. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for generation of a user delegation key which can be used to sign SAS tokens",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "name": "db58b8e5-c6ad-4a2a-8342-4190687cbf4a",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Blob Delegator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-contributor"></a>SMB-delningsdeltagare för lagringsfildata

Möjliggör läs-, skriv- och borttagningsåtkomst på filer/kataloger i Azure-filresurser. Den här rollen har ingen inbyggd motsvarighet på Windows-filservrar.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read Microsoft. | Returnerar en fil/mapp eller en lista med filer/mappar. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Returnerar resultatet av att skriva en fil eller skapa en mapp. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete Microsoft. | Returnerar resultatet av att en fil/mapp tas bort. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "name": "0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-elevated-contributor"></a>Lagringsfildata SMB-delning förhöjd deltagare

Möjliggör läsning, skrivning, borttagning och ändring av ACL:er på filer/kataloger i Azure-filresurser. Den här rollen motsvarar en filresurs ACL för ändring på Windows-filservrar.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read Microsoft. | Returnerar en fil/mapp eller en lista med filer/mappar. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write | Returnerar resultatet av att skriva en fil eller skapa en mapp. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete Microsoft. | Returnerar resultatet av att en fil/mapp tas bort. |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action | Returnerar resultatet av ändringsbehörigheten för en fil/mapp. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, delete and modify NTFS permission access in Azure Storage file shares over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a7264617-510b-434b-a828-9731dc254ea7",
  "name": "a7264617-510b-434b-a828-9731dc254ea7",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/write",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/delete",
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/modifypermissions/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Elevated Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-file-data-smb-share-reader"></a>SMB-delningsläsare för lagringsfildata

Möjliggör läsåtkomst för filer/kataloger i Azure-filresurser. Den här rollen motsvarar en filresurs ACL för läsning på Windows-filservrar.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read Microsoft. | Returnerar en fil/mapp eller en lista med filer/mappar. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure File Share over SMB",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/aba4ae5f-2193-4029-9191-0cb91df5e314",
  "name": "aba4ae5f-2193-4029-9191-0cb91df5e314",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage File Data SMB Share Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-contributor"></a>Datadeltagare för lagringskö

Läsa, skriva och ta bort Azure Storage-köer och kömeddelanden. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i [Behörigheter för att anropa blob- och ködataåtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Storage/storageKonto/queueServices/köer/borttagning | Ta bort en kö. |
> | Microsoft.Storage/storageKonto/queueServices/köer/läsa | Returnera en kö eller en lista med köer. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | Ändra kömetadata eller egenskaper. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.Storage/storageKonto/queueServices/queues/messages/delete Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete Microsoft.Storage/ | Ta bort ett eller flera meddelanden från en kö. |
> | Microsoft.Storage/storageKonto/queueServices/queues/messages/read Microsoft.Storage/storageAccounts/queueServices/queues/messages/read Microsoft.Storage/storageAccounts/queueServices/queues/messages/read Microsoft.Storage/ | Granska eller hämta ett eller flera meddelanden från en kö. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write Microsoft.Storage/storageAccounts/queueServices/queues/messages/write Microsoft.Storage/storageAccounts/queueServices/queues/messages/write Microsoft. | Lägg till ett meddelande i en kö. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read, write, and delete access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "name": "974c5e8b-45b9-4653-ba55-5f855dd0fb88",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/write"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-processor"></a>Meddelandebehandlare för lagringskö

Granska, hämta och ta bort ett meddelande från en Azure Storage-kö. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i [Behörigheter för att anropa blob- och ködataåtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.Storage/storageKonto/queueServices/queues/messages/read Microsoft.Storage/storageAccounts/queueServices/queues/messages/read Microsoft.Storage/storageAccounts/queueServices/queues/messages/read Microsoft.Storage/ | Kika på ett meddelande. |
> | Microsoft.Storage/storageKonto/queueServices/queues/messages/process/action | Hämta och ta bort ett meddelande. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for peek, receive, and delete access to Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "name": "8a0f0c08-91a1-4084-bc3d-661d67233fed",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read",
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/process/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Processor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-message-sender"></a>Skickare av datameddelande för lagringskö

Lägg till meddelanden i en Azure Storage-kö. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i [Behörigheter för att anropa blob- och ködataåtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.Storage/storageKonto/queueServices/queues/messages/add/action | Lägg till ett meddelande i en kö. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for sending of Azure Storage queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "name": "c6a89b2d-59bc-44d0-9896-0f6e12d7b80a",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/add/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Message Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="storage-queue-data-reader"></a>Dataläsare för lagringskö

Läs och lista Azure Storage-köer och kömeddelanden. Information om vilka åtgärder som krävs för en viss dataåtgärd finns i [Behörigheter för att anropa blob- och ködataåtgärder](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Storage/storageKonto/queueServices/köer/läsa | Returnerar en kö eller en lista med köer. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.Storage/storageKonto/queueServices/queues/messages/read Microsoft.Storage/storageAccounts/queueServices/queues/messages/read Microsoft.Storage/storageAccounts/queueServices/queues/messages/read Microsoft.Storage/ | Granska eller hämta ett eller flera meddelanden från en kö. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for read access to Azure Storage queues and queue messages",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/19e7f393-937e-4f77-808e-94535e297925",
  "name": "19e7f393-937e-4f77-808e-94535e297925",
  "permissions": [
    {
      "actions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Storage/storageAccounts/queueServices/queues/messages/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Storage Queue Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="web"></a>Webb


### <a name="azure-maps-data-reader-preview"></a>Dataläsare för Azure Maps (förhandsversion)

Ger åtkomst till läsa kartrelaterade data från ett Azure Maps-konto.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.Kartor/konton/data/läsa | Ger dataläsåtkomst till ett kartkonto. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Grants access to read map related data from an Azure maps account.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "name": "423170ca-a8f6-4b0f-8487-9e4eb8f49bfa",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.Maps/accounts/data/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Maps Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="search-service-contributor"></a>Deltagare i söktjänsten

Gör att du kan hantera söktjänster, men inte komma åt dem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Search/searchServices/* | Skapa och hantera söktjänster |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Search services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "name": "7ca78c08-252a-4471-8644-bb5ff32d4ba0",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Search/searchServices/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Search Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="web-plan-contributor"></a>Deltagare i webbplanen

Gör att du kan hantera webbplanerna för webbplatser, men inte åtkomst till dem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | Microsoft.Web/serverFarms/* | Skapa och hantera servergrupper |
> | Microsoft.Web/hostingMiljöer/Gå med/åtgärd | Ansluter till en apptjänstmiljö |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the web plans for websites, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "name": "2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/serverFarms/*",
        "Microsoft.Web/hostingEnvironments/Join/Action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Web Plan Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="website-contributor"></a>Webbplatsdeltagare

Gör att du kan hantera webbplatser (inte webbplaner), men inte tillgång till dem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Insights/komponenter/* | Skapa och hantera Insights-komponenter |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | Microsoft.Web/certifikat/* | Skapa och hantera webbplatscertifikat |
> | Microsoft.Web/listSitesAssignedToHostName/read Microsoft.Web/listSitesAssignedToHostName/read Microsoft.Web/listSitesAssignedToHostName/read Microsoft. | Hämta namn på webbplatser som tilldelats värdnamn. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Hämta egenskaperna i en apptjänstplan |
> | Microsoft.Webbplatser/* | Skapa och hantera webbplatser (webbplatsskapande kräver också skrivbehörighet till den associerade App Service Plan) |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage websites (not web plans), but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/de139f84-1756-47ae-9be6-808fbbe84772",
  "name": "de139f84-1756-47ae-9be6-808fbbe84772",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/certificates/*",
        "Microsoft.Web/listSitesAssignedToHostName/read",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Website Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="containers"></a>Containrar


### <a name="acrdelete"></a>AcrDelete (

acr ta bort

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.ContainerRegistry/register/artefakter/borttagning | Ta bort artefakt i ett behållarregister. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr delete",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "name": "c2f4ef07-c644-48eb-af81-4b1b4947fb11",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/artifacts/delete"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrDelete",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrimagesigner"></a>AcrImageSigner

acr bild undertecknare

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.ContainerRegistry/register/sign/write | Överför/hämta innehållsförtroendemetadata för ett behållarregister. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr image signer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6cef56e8-d556-48e5-a04f-b8e64114680f",
  "name": "6cef56e8-d556-48e5-a04f-b8e64114680f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/sign/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrImageSigner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpull"></a>AcrPull (acrpull)

acr dra

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.ContainerRegistry/register/pull/read | Hämta eller hämta avbildningar från ett behållarregister. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr pull",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "name": "7f951dda-4ed3-4680-a7ca-43fe172d538d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPull",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrpush"></a>AcrPush (av en)

acr push

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.ContainerRegistry/register/pull/read | Hämta eller hämta avbildningar från ett behållarregister. |
> | Microsoft.ContainerRegistry/register/push/write | Skicka eller skriva avbildningar till ett behållarregister. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr push",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8311e382-0749-4cb8-b61a-304f252e45ec",
  "name": "8311e382-0749-4cb8-b61a-304f252e45ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/pull/read",
        "Microsoft.ContainerRegistry/registries/push/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrPush",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinereader"></a>AcrQuarantineLäsare

acr karantän dataläsare

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.ContainerRegistry/register/karantän/läs | Hämta eller hämta karantänavbildningar från behållarregistret |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cdda3590-29a3-44f6-95f2-9f980659eb04",
  "name": "cdda3590-29a3-44f6-95f2-9f980659eb04",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineReader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="acrquarantinewriter"></a>AcrQuarantineWriter

acr karantän dataskrivare

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.ContainerRegistry/register/karantän/läs | Hämta eller hämta karantänavbildningar från behållarregistret |
> | Microsoft.ContainerRegistry/register/karantän/skrivning | Skriva/ändra karantäntillstånd för bilder i karantän |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "acr quarantine data writer",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "name": "c8d4ff99-41c3-41a8-9f60-21dfdad59608",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerRegistry/registries/quarantine/read",
        "Microsoft.ContainerRegistry/registries/quarantine/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "AcrQuarantineWriter",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-admin-role"></a>Azure Kubernetes tjänstkluster administratörsroll

Lista åtgärden för klusteradministratör.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Lista klustretAdmin-referens för ett hanterat kluster |
> | Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action | Hämta en hanterad klusteråtkomstprofil efter rollnamn med hjälp av listautentiseringsuppgifter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster admin credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "name": "0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action",
        "Microsoft.ContainerService/managedClusters/accessProfiles/listCredential/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster Admin Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-kubernetes-service-cluster-user-role"></a>Användarroll för Azure Kubernetes-tjänstkluster

Lista åtgärden för klusteranvändare.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Lista klusterAnvändarautentiseringsuppgifter för ett hanterat kluster |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "List cluster user credential action.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "name": "4abbcc35-e782-43d8-92c5-2d3f1bd2253f",
  "permissions": [
    {
      "actions": [
        "Microsoft.ContainerService/managedClusters/listClusterUserCredential/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Kubernetes Service Cluster User Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="databases"></a>Databaser


### <a name="cosmos-db-account-reader-role"></a>Cosmos DB-kontoläsare roll

Kan läsa Azure Cosmos DB-kontodata. Se [DocumentDB-kontodeltagare](#documentdb-account-contributor) för hantering av Azure Cosmos DB-konton.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.DocumentDB/*/read | Läs en samling |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Läser databaskontot som är lästa. |
> | Microsoft.Insights/MetricDefinitions/read | Läsa måttdefinitioner |
> | Microsoft.Insights/Metrics/read | Läs mått |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read Azure Cosmos DB Accounts data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "name": "fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDB/*/read",
        "Microsoft.DocumentDB/databaseAccounts/readonlykeys/action",
        "Microsoft.Insights/MetricDefinitions/read",
        "Microsoft.Insights/Metrics/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Account Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmos-db-operator"></a>Cosmos DB-operatör

Gör att du kan hantera Azure Cosmos DB-konton, men inte komma åt data i dem. Förhindrar åtkomst till kontonycklar och anslutningssträngar.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.DocumentDb/databaseKonton/* |  |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | Microsoft.Network/virtualNetworks/subnät/joinViaServiceEndpoint/åtgärd | Sammanfogar resurs som lagringskonto eller SQL-databas till ett undernät. Inte alertable. |
> | **NotActions** |  |
> | Microsoft.DocumentDB/databaseAccounts/readonlyKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/regenerateKey/* |  |
> | Microsoft.DocumentDB/databaseKonto/listKeys/* |  |
> | Microsoft.DocumentDB/databaseKonto/listAnslutningar/* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Cosmos DB accounts, but not access data in them. Prevents access to account keys and connection strings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/230815da-be43-4aae-9cb4-875f7bd000aa",
  "name": "230815da-be43-4aae-9cb4-875f7bd000aa",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [
        "Microsoft.DocumentDB/databaseAccounts/readonlyKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/regenerateKey/*",
        "Microsoft.DocumentDB/databaseAccounts/listKeys/*",
        "Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cosmos DB Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cosmosbackupoperator"></a>CosmosBackupOperator

Kan skicka återställningsbegäran för en Cosmos DB-databas eller en behållare för ett konto

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.DocumentDB/databaseAccounts/backup/action | Skicka en begäran om att konfigurera säkerhetskopiering |
> | Microsoft.DocumentDB/databaseAccounts/restore/action | Skicka en återställningsbegäran |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can submit restore request for a Cosmos DB database or a container for an account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "name": "db7b14f2-5adf-42da-9f96-f2ee17bab5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.DocumentDB/databaseAccounts/backup/action",
        "Microsoft.DocumentDB/databaseAccounts/restore/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "CosmosBackupOperator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="documentdb-account-contributor"></a>Deltagare på DocumentDB-konto

Kan hantera Azure Cosmos DB-konton. Azure Cosmos DB är tidigare känt som DocumentDB.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.DocumentDb/databaseKonton/* | Skapa och hantera Azure Cosmos DB-konton |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | Microsoft.Network/virtualNetworks/subnät/joinViaServiceEndpoint/åtgärd | Sammanfogar resurs som lagringskonto eller SQL-databas till ett undernät. Inte alertable. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage DocumentDB accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5bd9cd88-fe45-4216-938b-f97437e15450",
  "name": "5bd9cd88-fe45-4216-938b-f97437e15450",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DocumentDb/databaseAccounts/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DocumentDB Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="redis-cache-contributor"></a>Redis-cachedeltagare

Gör att du kan hantera Redis-cacheminnen, men inte komma åt dem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Cache/redis/* | Skapa och hantera Redis-cacheminnen |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Redis caches, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e0f68234-74aa-48ed-b826-c38b57376e17",
  "name": "e0f68234-74aa-48ed-b826-c38b57376e17",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Cache/redis/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Redis Cache Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-db-contributor"></a>SQL DB-deltagare

Gör att du kan hantera SQL-databaser, men inte komma åt dem. Du kan inte heller hantera deras säkerhetsrelaterade principer eller deras överordnade SQL-servrar.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servrar/databaser/* | Skapa och hantera SQL-databaser |
> | Microsoft.Sql/servrar/läsa | Returnera listan över servrar eller få egenskaperna för den angivna servern. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | Microsoft.Insights/metrics/read | Läs mått |
> | Microsoft.Insights/metricDefinitions/read | Läsa måttdefinitioner |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databaser/känslighetLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/auditPolicies/* | Redigera granskningsprinciper |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Redigera granskningsinställningar |
> | Microsoft.Sql/servers/databases/auditRecords/read Microsoft.Sql/servers/databases/auditRecords/read Microsoft.Sql/servers/databases/auditRecords/read Microsoft. | Hämta granskningsposterna för databasblob |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Redigera anslutningsprinciper |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Redigera principer för datamaskering |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Redigera principer för säkerhetsvarningar |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Redigera säkerhetsmått |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL databases, but not access to them. Also, you can't manage their security-related policies or their parent SQL servers.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "name": "9b7fa17d-e63e-47b0-bb0a-15c516ac86ec",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/databases/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL DB Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-managed-instance-contributor"></a>SQL-hanterad instansdeltagare

Gör att du kan hantera SQL-hanterade instanser och nödvändig nätverkskonfiguration, men kan inte ge åtkomst till andra.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Network/networkSecurityGroups/* |  |
> | Microsoft.Network/routeTables/* |  |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/managedInstances/* |  |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | Microsoft.Network/virtualNetworks/undernät/* |  |
> | Microsoft.Network/virtualNetworks/* |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Insights/metrics/read | Läs mått |
> | Microsoft.Insights/metricDefinitions/read | Läsa måttdefinitioner |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL Managed Instances and required network configuration, but can't give access to others.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "name": "4939a1f6-9ae0-4e48-a1e0-f2cbe897382d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Network/networkSecurityGroups/*",
        "Microsoft.Network/routeTables/*",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/managedInstances/*",
        "Microsoft.Support/*",
        "Microsoft.Network/virtualNetworks/subnets/*",
        "Microsoft.Network/virtualNetworks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Managed Instance Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-security-manager"></a>SQL-säkerhetshanterare

Gör att du kan hantera säkerhetsrelaterade principer för SQL-servrar och databaser, men inte åtkomst till dem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Network/virtualNetworks/subnät/joinViaServiceEndpoint/åtgärd | Sammanfogar resurs som lagringskonto eller SQL-databas till ett undernät. Inte alertable. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databaser/känslighetLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databases/transparentDataEncryption/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servrar/granskningArmocies/* | Skapa och hantera principer för GRANSKNING AV SQL-servrar |
> | Microsoft.Sql/servrar/granskningsinställningar/* | Skapa och hantera sql server-granskningsinställning |
> | Microsoft.Sql/servers/extendedAuditingSettings/read Microsoft.Sql/servers/extendedAuditingSettings/read Microsoft.Sql/servers/extendedAuditingSettings/read Microsoft. | Hämta information om den utökade granskningsprincipen för serverblobb som konfigurerats på en viss server |
> | Microsoft.Sql/servers/databases/auditPolicies/* | Skapa och hantera principer för granskning av SQL-serverdatabaser |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Skapa och hantera granskningsinställningar för SQL-serverdatabaser |
> | Microsoft.Sql/servers/databases/auditRecords/read Microsoft.Sql/servers/databases/auditRecords/read Microsoft.Sql/servers/databases/auditRecords/read Microsoft. | Hämta granskningsposterna för databasblob |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Skapa och hantera anslutningsprinciper för SQL-serverdatabaser |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Skapa och hantera principer för datamaskering av SQL-serverdatabaser |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/read Microsoft.Sql/servers/databases/extendedAuditingSettings/read Microsoft.Sql/servers/databases/extendedAuditingSettings/read Microsoft. | Hämta information om den utökade blob-granskningsprincipen som konfigurerats i en viss databas |
> | Microsoft.Sql/servers/databases/read | Returnera listan över databaser eller hämtar egenskaperna för den angivna databasen. |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/read Microsoft.Sql/servers/databases/schemas/read Microsoft.Sql/servers/databases/schemas/read Microsoft. | Hämta ett databasschema. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read Microsoft.Sql/servers/databases/schemas/tables/columns/read Microsoft.Sql/servers/databases/schemas/tables/columns/read Microsoft. | Hämta en databaskolumn. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read Microsoft.Sql/servers/databases/schemas/tables/read Microsoft.Sql/servers/databases/schemas/tables/read Microsoft. | Skaffa en databastabell. |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Skapa och hantera säkerhetsprinciper för SQL-serverdatabasen |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Skapa och hantera säkerhetsmått för SQL-serverdatabaser |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/transparentDataEncryption/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servrar/läsa | Returnera listan över servrar eller få egenskaperna för den angivna servern. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Skapa och hantera principer för säkerhetsaviseringar för SQL-servrar |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage the security-related policies of SQL servers and databases, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "name": "056cd41c-7e88-42e1-933e-88ba6a50c9c3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/transparentDataEncryption/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingPolicies/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/read",
        "Microsoft.Sql/servers/databases/read",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/read",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/read",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/transparentDataEncryption/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/firewallRules/*",
        "Microsoft.Sql/servers/read",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Security Manager",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="sql-server-contributor"></a>SQL Server-deltagare

Gör att du kan hantera SQL-servrar och -databaser, men inte åtkomst till dem, och inte deras säkerhetsrelaterade principer.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servrar/* | Skapa och hantera SQL-servrar |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | Microsoft.Insights/metrics/read | Läs mått |
> | Microsoft.Insights/metricDefinitions/read | Läsa måttdefinitioner |
> | **NotActions** |  |
> | Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/databaser/känslighetLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servrar/granskningArmocies/* | Redigera principer för granskning av SQL-servrar |
> | Microsoft.Sql/servrar/granskningsinställningar/* | Redigera inställningar för granskning av SQL-servrar |
> | Microsoft.Sql/servers/databases/auditPolicies/* | Redigera granskningsprinciper för SQL-serverdatabaser |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Redigera granskningsinställningar för SQL Server-databas |
> | Microsoft.Sql/servers/databases/auditRecords/read Microsoft.Sql/servers/databases/auditRecords/read Microsoft.Sql/servers/databases/auditRecords/read Microsoft. | Hämta granskningsposterna för databasblob |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Redigera anslutningsprinciper för SQL-serverdatabaser |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Redigera principer för datamaskering av SQL-serverdatabaser |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Redigera säkerhetsprinciper för SQL-serverdatabasen |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Redigera säkerhetsmått för SQL-serverdatabasen |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Redigera principer för säkerhetsvarningar för SQL-servrar |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage SQL servers and databases, but not access to them, and not their security -related policies.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "name": "6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Sql/locations/*/read",
        "Microsoft.Sql/servers/*",
        "Microsoft.Support/*",
        "Microsoft.Insights/metrics/read",
        "Microsoft.Insights/metricDefinitions/read"
      ],
      "notActions": [
        "Microsoft.Sql/managedInstances/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/databases/sensitivityLabels/*",
        "Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/managedInstances/securityAlertPolicies/*",
        "Microsoft.Sql/managedInstances/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/auditingPolicies/*",
        "Microsoft.Sql/servers/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditingPolicies/*",
        "Microsoft.Sql/servers/databases/auditingSettings/*",
        "Microsoft.Sql/servers/databases/auditRecords/read",
        "Microsoft.Sql/servers/databases/connectionPolicies/*",
        "Microsoft.Sql/servers/databases/currentSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/dataMaskingPolicies/*",
        "Microsoft.Sql/servers/databases/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/databases/recommendedSensitivityLabels/*",
        "Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/securityAlertPolicies/*",
        "Microsoft.Sql/servers/databases/securityMetrics/*",
        "Microsoft.Sql/servers/databases/sensitivityLabels/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessments/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/*",
        "Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/*",
        "Microsoft.Sql/servers/extendedAuditingSettings/*",
        "Microsoft.Sql/servers/securityAlertPolicies/*",
        "Microsoft.Sql/servers/vulnerabilityAssessments/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "SQL Server Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="analytics"></a>Analys


### <a name="azure-event-hubs-data-owner"></a>Azure Event Hubs dataägare

Ger fullständig åtkomst till Azure Event Hubs-resurser.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.EventHub/* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.EventHub/* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f526a384-b230-433a-b45c-95f59c4a2dec",
  "name": "f526a384-b230-433a-b45c-95f59c4a2dec",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-receiver"></a>Azure-händelsehubbar datamottagare

Tillåter åtkomst till Azure Event Hubs-resurser.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.EventHub/*/eventhubs/consumergroups/read Microsoft.EventHub/*/eventhubs/consumergroups/read Microsoft.EventHub/*/eventhubs/consumergroups/read Microsoft. |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.EventHub/*/receive/action |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows receive access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "name": "a638d3c7-ab3a-418d-83e6-5f17a39d4fde",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/consumergroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-event-hubs-data-sender"></a>Azure Event Hubs Data Sender

Tillåter skicka åtkomst till Azure Event Hubs-resurser.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.EventHub/*/eventhubs/read |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.EventHub/*/skicka/åtgärd |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows send access to Azure Event Hubs resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2b629674-e913-4c01-ae53-ef4638d8f975",
  "name": "2b629674-e913-4c01-ae53-ef4638d8f975",
  "permissions": [
    {
      "actions": [
        "Microsoft.EventHub/*/eventhubs/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.EventHub/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Event Hubs Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-factory-contributor"></a>Data Factory Bidragsgivare

Skapa och hantera datafabriker samt underordnade resurser i dem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.DataFactory/dataFactories/* | Skapa och hantera datafabriker och underordnade resurser i dem. |
> | Microsoft.DataFactory/fabriker/* | Skapa och hantera datafabriker och underordnade resurser i dem. |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | Microsoft.EventGrid/eventAbonnemang/skrivning | Skapa eller uppdatera en händelseAbonnemang |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and manage data factories, as well as child resources within them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/673868aa-7521-48a0-acc6-0f60742d39f5",
  "name": "673868aa-7521-48a0-acc6-0f60742d39f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.DataFactory/dataFactories/*",
        "Microsoft.DataFactory/factories/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.EventGrid/eventSubscriptions/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Factory Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="data-purger"></a>Datarensare

Kan rensa analysdata

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action | Rensa data från Application Insights |
> | Microsoft.OperationalInsights/workspaces/*/read | Visa logganalysdata |
> | Microsoft.OperationalInsights/workspaces/purge/action | Ta bort angivna data från arbetsytan |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can purge analytics data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "name": "150f5e0c-0603-4f03-8c7f-cf70034c4e90",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/components/*/read",
        "Microsoft.Insights/components/purge/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/purge/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Data Purger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-cluster-operator"></a>Klusteroperatör för HDInsight

Gör att du kan läsa och ändra HDInsight-klusterkonfigurationer.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.HDInsight/*/read |  |
> | Microsoft.HDInsight/kluster/getGatewaySettings/åtgärd | Hämta gatewayinställningar för HDInsight Cluster |
> | Microsoft.HDInsight/kluster/updateGatewaySettings/åtgärd | Uppdatera gatewayinställningar för HDInsight Cluster |
> | Microsoft.HDInsight/kluster/konfigurationer/* |  |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Resurser/distributioner/åtgärder/läsa | Hämtar eller listar distributionsåtgärder. |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and modify HDInsight cluster configurations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/61ed4efc-fab3-44fd-b111-e24485cc132a",
  "name": "61ed4efc-fab3-44fd-b111-e24485cc132a",
  "permissions": [
    {
      "actions": [
        "Microsoft.HDInsight/*/read",
        "Microsoft.HDInsight/clusters/getGatewaySettings/action",
        "Microsoft.HDInsight/clusters/updateGatewaySettings/action",
        "Microsoft.HDInsight/clusters/configurations/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Cluster Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="hdinsight-domain-services-contributor"></a>Medarbetare för HDInsight-domäntjänster

Kan läsa, skapa, ändra och ta bort domäntjänster relaterade åtgärder som behövs för HDInsight Enterprise Security Package

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.AAD/*/read |  |
> | Microsoft.AAD/domainServices/*/read |  |
> | Microsoft.AAD/domainServices/oucontainer/* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can Read, Create, Modify and Delete Domain Services related operations needed for HDInsight Enterprise Security Package",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "name": "8d8d5a11-05d3-4bda-a417-a08778121c7c",
  "permissions": [
    {
      "actions": [
        "Microsoft.AAD/*/read",
        "Microsoft.AAD/domainServices/*/read",
        "Microsoft.AAD/domainServices/oucontainer/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "HDInsight Domain Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-contributor"></a>Log Analytics Contributor

Log Analytics Contributor kan läsa alla övervakningsdata och redigera övervakningsinställningar. Redigering av övervakningsinställningarna inkluderar att lägga till vm-tillägget till virtuella datorer. läsa lagringskontonycklar för att kunna konfigurera insamling av loggar från Azure Storage; skapa och konfigurera Automation-konton; Lägga till lösningar; och konfigurera Azure-diagnostik på alla Azure-resurser.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | */läs | Läs resurser av alla slag, utom hemligheter. |
> | Microsoft.Automation/automationKonto/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Visar en lista över åtkomstnycklarna för lagringskontona. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Insights/diagnosticSettings/* | Skapar, uppdaterar eller läser diagnostikinställningen för Analysis Server |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Returnerar åtkomstnycklarna för det angivna lagringskontot. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Contributor can read all monitoring data and edit monitoring settings. Editing monitoring settings includes adding the VM extension to VMs; reading storage account keys to be able to configure collection of logs from Azure Storage; creating and configuring Automation accounts; adding solutions; and configuring Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "name": "92aaf0da-9dab-42b6-94a3-d43ce8d16293",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Automation/automationAccounts/*",
        "Microsoft.ClassicCompute/virtualMachines/extensions/*",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.Compute/virtualMachines/extensions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.OperationalInsights/*",
        "Microsoft.OperationsManagement/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="log-analytics-reader"></a>Log Analytics Reader

Log Analytics Reader kan visa och söka efter alla övervakningsdata samt och visa övervakningsinställningar, inklusive visning av konfigurationen av Azure-diagnostik på alla Azure-resurser.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | */läs | Läs resurser av alla slag, utom hemligheter. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Sök med ny motor. |
> | Microsoft.OperationalInsights/workspaces/search/action | Kör en sökfråga |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read Microsoft.OperationalInsights/workspaces/sharedKeys/read Microsoft.OperationalInsights/workspaces/sharedKeys/read Microsoft. | Hämtar de delade nycklarna för arbetsytan. Dessa nycklar används för att ansluta Microsoft Operational Insights-agenter till arbetsytan. |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Log Analytics Reader can view and search all monitoring data as well as and view monitoring settings, including viewing the configuration of Azure diagnostics on all Azure resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/73c42c96-874c-492b-b04d-ab87d138a893",
  "name": "73c42c96-874c-492b-b04d-ab87d138a893",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.OperationalInsights/workspaces/sharedKeys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Log Analytics Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="blockchain"></a>Blockkedja


### <a name="blockchain-member-node-access-preview"></a>Åtkomst till blockchain-medlemnod (förhandsversion)

Ger tillgång till Blockchain-medlemsnoder

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Blockchain/blockchainMedlem/transaktionNoder/läs | Hämtar eller visar befintliga blockchain-medlemtransaktionsnoder. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.Blockchain/blockchainMedlem/transaktionNoder/anslut/åtgärd | Ansluter till en transaktionsnod för blockchain-medlemmar. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for access to Blockchain Member nodes",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "name": "31a002a1-acaf-453e-8a5b-297c9ca1ea24",
  "permissions": [
    {
      "actions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Blockchain/blockchainMembers/transactionNodes/connect/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Blockchain Member Node Access (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="ai--machine-learning"></a>AI + maskininlärning


### <a name="cognitive-services-contributor"></a>Kognitiva tjänster Bidragsgivare

Gör att du kan skapa, läsa, uppdatera, ta bort och hantera nycklar i Cognitive Services.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.CognitiveServices/* |  |
> | Microsoft.Funktioner/funktioner/läs | Hämtar funktionerna i en prenumeration. |
> | Microsoft.Funktioner/leverantörer/funktioner/läsa | Hämtar funktionen för en prenumeration i en viss resursleverantör. |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Insights/diagnosticSettings/* | Skapar, uppdaterar eller läser diagnostikinställningen för Analysis Server |
> | Microsoft.Insights/logDefinitioner/läs | Läsa loggdefinitioner |
> | Microsoft.Insights/metricdefinitions/read Microsoft.Insights/metricdefinitions/read Microsoft.Insights/metricdefinitions/read Microsoft. | Läsa måttdefinitioner |
> | Microsoft.Insights/metrics/read | Läs mått |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/distributioner/åtgärder/läsa | Hämtar eller listar distributionsåtgärder. |
> | Microsoft.Resurser/prenumerationer/operationsresultat/läsa | Hämta resultaten för prenumerationsåtgärden. |
> | Microsoft.Resurser/prenumerationer/läsa | Hämtar listan över prenumerationer. |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, read, update, delete and manage keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "name": "25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.CognitiveServices/*",
        "Microsoft.Features/features/read",
        "Microsoft.Features/providers/features/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourcegroups/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-data-reader-preview"></a>Dataläsare för Cognitive Services (förhandsgranskning)

Gör att du kan läsa Cognitive Services-data.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read Cognitive Services data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "name": "b59867f0-fa02-499b-be73-45a86b5b3e1c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services Data Reader (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cognitive-services-user"></a>Användare av Cognitive Services

Gör att du kan läsa och lista nycklar i Cognitive Services.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | Microsoft.CognitiveServices/accounts/listkeys/action | Lista nycklar |
> | Microsoft.Insights/alertRules/read Microsoft.Insights/alertRules/read Microsoft.Insights/alertRules/read Microsoft. | Läs en klassisk måttvarning |
> | Microsoft.Insights/diagnosticSettings/read | Läsa en resursdiagnostikinställning |
> | Microsoft.Insights/logDefinitioner/läs | Läsa loggdefinitioner |
> | Microsoft.Insights/metricdefinitions/read Microsoft.Insights/metricdefinitions/read Microsoft.Insights/metricdefinitions/read Microsoft. | Läsa måttdefinitioner |
> | Microsoft.Insights/metrics/read | Läs mått |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resurser/distributioner/åtgärder/läsa | Hämtar eller listar distributionsåtgärder. |
> | Microsoft.Resurser/prenumerationer/operationsresultat/läsa | Hämta resultaten för prenumerationsåtgärden. |
> | Microsoft.Resurser/prenumerationer/läsa | Hämtar listan över prenumerationer. |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.CognitiveServices/* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and list keys of Cognitive Services.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/a97b65f3-24c7-4388-baec-2e87135dc908",
  "name": "a97b65f3-24c7-4388-baec-2e87135dc908",
  "permissions": [
    {
      "actions": [
        "Microsoft.CognitiveServices/*/read",
        "Microsoft.CognitiveServices/accounts/listkeys/action",
        "Microsoft.Insights/alertRules/read",
        "Microsoft.Insights/diagnosticSettings/read",
        "Microsoft.Insights/logDefinitions/read",
        "Microsoft.Insights/metricdefinitions/read",
        "Microsoft.Insights/metrics/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.CognitiveServices/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Cognitive Services User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="mixed-reality"></a>Mixed Reality


### <a name="spatial-anchors-account-contributor"></a>Rapportör för kontot För rumsliga ankare

Gör att du kan hantera rumsliga ankare i ditt konto, men inte ta bort dem

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action Microsoft.MixedReality/SpatialAnchorsAccounts/create/action Microsoft.MixedReality/SpatialAnchorsAccounts/create/action Microsoft. | Skapa rumsliga ankare |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read Microsoft. | Upptäck närliggande rumsliga ankare |
> | Microsoft.MixedReality/SpatialAnchorsKonto/egenskaper/läs | Hämta egenskaper för rumsliga ankare |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read Microsoft.MixedReality/SpatialAnchorsAccounts/query/read Microsoft.MixedReality/SpatialAnchorsAccounts/query/read Microsoft. | Hitta rumsliga ankare |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read Microsoft. | Skicka diagnostikdata för att förbättra kvaliteten på Azure Spatial Anchors-tjänsten |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write Microsoft.MixedReality/SpatialAnchorsAccounts/write Microsoft.MixedReality/SpatialAnchorsAccounts/write Microsoft. | Uppdatera egenskaper för rumsliga ankare |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, but not delete them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "name": "8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-owner"></a>Ägare av kontot För rumsliga ankare

Gör att du kan hantera rumsliga ankare i ditt konto, inklusive att ta bort dem

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/create/action Microsoft.MixedReality/SpatialAnchorsAccounts/create/action Microsoft.MixedReality/SpatialAnchorsAccounts/create/action Microsoft. | Skapa rumsliga ankare |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/delete Microsoft.MixedReality/SpatialAnchorsAccounts/delete Microsoft.MixedReality/SpatialAnchorsAccounts/delete Microsoft. | Ta bort rumsliga ankare |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read Microsoft. | Upptäck närliggande rumsliga ankare |
> | Microsoft.MixedReality/SpatialAnchorsKonto/egenskaper/läs | Hämta egenskaper för rumsliga ankare |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read Microsoft.MixedReality/SpatialAnchorsAccounts/query/read Microsoft.MixedReality/SpatialAnchorsAccounts/query/read Microsoft. | Hitta rumsliga ankare |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read Microsoft. | Skicka diagnostikdata för att förbättra kvaliteten på Azure Spatial Anchors-tjänsten |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/write Microsoft.MixedReality/SpatialAnchorsAccounts/write Microsoft.MixedReality/SpatialAnchorsAccounts/write Microsoft. | Uppdatera egenskaper för rumsliga ankare |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage spatial anchors in your account, including deleting them",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/70bbe301-9835-447d-afdd-19eb3167307c",
  "name": "70bbe301-9835-447d-afdd-19eb3167307c",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/create/action",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/delete",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="spatial-anchors-account-reader"></a>Rumsliga ankare kontoläsare

Gör att du kan hitta och läsa egenskaper för rumsliga ankare i ditt konto

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read Microsoft. | Upptäck närliggande rumsliga ankare |
> | Microsoft.MixedReality/SpatialAnchorsKonto/egenskaper/läs | Hämta egenskaper för rumsliga ankare |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/query/read Microsoft.MixedReality/SpatialAnchorsAccounts/query/read Microsoft.MixedReality/SpatialAnchorsAccounts/query/read Microsoft. | Hitta rumsliga ankare |
> | Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read Microsoft. | Skicka diagnostikdata för att förbättra kvaliteten på Azure Spatial Anchors-tjänsten |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you locate and read properties of spatial anchors in your account",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "name": "5d51204f-eb77-4b1c-b86a-2ec626c49413",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.MixedReality/SpatialAnchorsAccounts/discovery/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/properties/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/query/read",
        "Microsoft.MixedReality/SpatialAnchorsAccounts/submitdiag/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Spatial Anchors Account Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="integration"></a>Integrering


### <a name="api-management-service-contributor"></a>API-hanteringstjänstdeltagare

Kan hantera tjänsten och API:erna

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.ApiManagement/service/* | Skapa och hantera API Management-tjänsten |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service and the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/312a565d-c81f-4fd8-895a-4e21e48d571c",
  "name": "312a565d-c81f-4fd8-895a-4e21e48d571c",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-operator-role"></a>Rollen Operatör för API-hanteringstjänster

Kan hantera tjänsten men inte API:erna

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.ApiManagement/service/*/read | Läsa API Management Service-instanser |
> | Microsoft.ApiManagement/service/backup/action | Api Management Service för säkerhetskopiering till den angivna behållaren i ett lagringskonto som tillhandahålls av användaren |
> | Microsoft.ApiManagement/service/delete | Ta bort API Management Service-instans |
> | Microsoft.ApiManagement/service/managedeployments/action | Ändra SKU/-enheter, lägga till/ta bort regionala distributioner av API Management Service |
> | Microsoft.ApiManagement/service/read | Läsa metadata för en API Management Service-instans |
> | Microsoft.ApiManagement/service/restore/action | Återställa API Management Service från den angivna behållaren i ett användarkonto som tillhandahålls av användaren |
> | Microsoft.ApiManagement/service/updatecertificate/action | Ladda upp TLS/SSL-certifikat för en API Management Service |
> | Microsoft.ApiManagement/service/updatehostname/åtgärd | Konfigurera, uppdatera eller ta bort anpassade domännamn för en API Management Service |
> | Microsoft.ApiManagement/service/write | Skapa eller uppdatera API Management Service-instans |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read Microsoft.ApiManagement/service/users/keys/read Microsoft.ApiManagement/service/users/keys/read Microsoft. | Hämta nycklar som är associerade med användaren |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage service but not the APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "name": "e022efe7-f5ba-4159-bbe4-b44f577e9b61",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/backup/action",
        "Microsoft.ApiManagement/service/delete",
        "Microsoft.ApiManagement/service/managedeployments/action",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.ApiManagement/service/restore/action",
        "Microsoft.ApiManagement/service/updatecertificate/action",
        "Microsoft.ApiManagement/service/updatehostname/action",
        "Microsoft.ApiManagement/service/write",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="api-management-service-reader-role"></a>Rollen för tjänstläsare för API-hantering

Skrivskyddad åtkomst till tjänst och API:er

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.ApiManagement/service/*/read | Läsa API Management Service-instanser |
> | Microsoft.ApiManagement/service/read | Läsa metadata för en API Management Service-instans |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read Microsoft.ApiManagement/service/users/keys/read Microsoft.ApiManagement/service/users/keys/read Microsoft. | Hämta nycklar som är associerade med användaren |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read-only access to service and APIs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/71522526-b88f-4d52-b57f-d31fc3546d0d",
  "name": "71522526-b88f-4d52-b57f-d31fc3546d0d",
  "permissions": [
    {
      "actions": [
        "Microsoft.ApiManagement/service/*/read",
        "Microsoft.ApiManagement/service/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.ApiManagement/service/users/keys/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "API Management Service Reader Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-owner"></a>Ägare av appkonfigurationsdata

Ger fullständig åtkomst till appkonfigurationsdata.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.AppKonfigurering/configurationStores/*/read |  |
> | Microsoft.AppKonfigurering/configurationStores/*/write |  |
> | Microsoft.AppKonfigurering/configurationStores/*/delete |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows full access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "name": "5ae67dd6-50cb-40e7-96ff-dc2bfa4b606b",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read",
        "Microsoft.AppConfiguration/configurationStores/*/write",
        "Microsoft.AppConfiguration/configurationStores/*/delete"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="app-configuration-data-reader"></a>Dataläsare för appkonfiguration

Tillåter läsåtkomst till appkonfigurationsdata.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | *inget* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.AppKonfigurering/configurationStores/*/read |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to App Configuration data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/516239f1-63e1-4d78-a4de-a74fb236a071",
  "name": "516239f1-63e1-4d78-a4de-a74fb236a071",
  "permissions": [
    {
      "actions": [],
      "notActions": [],
      "dataActions": [
        "Microsoft.AppConfiguration/configurationStores/*/read"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "App Configuration Data Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-owner"></a>Ägare av Azure Service Bus-data

Ger fullständig åtkomst till Azure Service Bus-resurser.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.ServiceBus/* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for full access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/090c5cfd-751d-490a-894a-3ce6f1109419",
  "name": "090c5cfd-751d-490a-894a-3ce6f1109419",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-receiver"></a>Azure Service Bus-datamottagare

Ger åtkomst till Azure Service Bus-resurser.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.ServiceBus/*/köer/läsa |  |
> | Microsoft.ServiceBus/*/topics/read Microsoft.ServiceBus/topics/read Microsoft.ServiceBus/topics/read Microsoft.ServiceBus/topics |  |
> | Microsoft.ServiceBus/*/topics/subscriptions/read Microsoft.ServiceBus/*/topics/subscriptions/read Microsoft.ServiceBus/*/topics/subscriptions/read Microsoft. |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.ServiceBus/*/receive/action |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for receive access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "name": "4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/receive/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Receiver",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-service-bus-data-sender"></a>Azure Service Bus Data Sender

Tillåter skicka åtkomst till Azure Service Bus-resurser.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.ServiceBus/*/köer/läsa |  |
> | Microsoft.ServiceBus/*/topics/read Microsoft.ServiceBus/topics/read Microsoft.ServiceBus/topics/read Microsoft.ServiceBus/topics |  |
> | Microsoft.ServiceBus/*/topics/subscriptions/read Microsoft.ServiceBus/*/topics/subscriptions/read Microsoft.ServiceBus/*/topics/subscriptions/read Microsoft. |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.ServiceBus/*/send/action |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for send access to Azure Service Bus resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "name": "69a216fc-b8fb-44d8-bc22-1f3c2cd27a39",
  "permissions": [
    {
      "actions": [
        "Microsoft.ServiceBus/*/queues/read",
        "Microsoft.ServiceBus/*/topics/read",
        "Microsoft.ServiceBus/*/topics/subscriptions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.ServiceBus/*/send/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Service Bus Data Sender",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-stack-registration-owner"></a>Ägare av Azure Stack-registrering

Gör att du kan hantera Azure Stack-registreringar.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.AzureStack/registrations/products/*/action |  |
> | Microsoft.AzureStack/registrations/products/read Microsoft.AzureStack/registrations/products/read Microsoft.AzureStack/registrations/products/read Microsoft. | Hämtar egenskaperna för en Azure Stack Marketplace-produkt |
> | Microsoft.AzureStack/registreringar/läsa | Hämtar egenskaperna för en Azure Stack-registrering |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Azure Stack registrations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "name": "6f12a6df-dd06-4f3e-bcb1-ce8be600526a",
  "permissions": [
    {
      "actions": [
        "Microsoft.AzureStack/registrations/products/*/action",
        "Microsoft.AzureStack/registrations/products/read",
        "Microsoft.AzureStack/registrations/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Stack Registration Owner",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-contributor"></a>Deltagare i EventGrid EventSubscription

Gör att du kan hantera EventGrid-händelseprenumerationsåtgärder.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.EventGrid/eventAbonnemang/* |  |
> | Microsoft.EventGrid/topicTypes/eventAbonnemang/läsning | Lista globala händelseprenumerationer efter ämnestyp |
> | Microsoft.EventGrid/platser/eventAbonnemang/läsning | Lista regionala evenemangsprenumerationer |
> | Microsoft.EventGrid/platser/topicTypes/eventAbonnemang/läsning | Lista regionala händelseprenumerationer efter ämnestyp |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage EventGrid event subscription operations.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "name": "428e0ff0-5e57-4d9c-a221-2c70d0e0a443",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/*",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="eventgrid-eventsubscription-reader"></a>EventGrid EventSubscription Reader

Gör att du kan läsa EventGrid-händelseprenumerationer.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.EventGrid/eventAbonnemang/läsning | Läsa en händelseAbonnemang |
> | Microsoft.EventGrid/topicTypes/eventAbonnemang/läsning | Lista globala händelseprenumerationer efter ämnestyp |
> | Microsoft.EventGrid/platser/eventAbonnemang/läsning | Lista regionala evenemangsprenumerationer |
> | Microsoft.EventGrid/platser/topicTypes/eventAbonnemang/läsning | Lista regionala händelseprenumerationer efter ämnestyp |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read EventGrid event subscriptions.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2414bbcf-6497-4faf-8c65-045460748405",
  "name": "2414bbcf-6497-4faf-8c65-045460748405",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.EventGrid/eventSubscriptions/read",
        "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/eventSubscriptions/read",
        "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "EventGrid EventSubscription Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="intelligent-systems-account-contributor"></a>Intelligent systemkontobidragsgivare

Gör att du kan hantera intelligenta systemkonton, men inte åtkomst till dem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.IntelligentSystems/konton/* | Skapa och hantera intelligenta systemkonton |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Intelligent Systems accounts, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/03a6d094-3444-4b3d-88af-7477090a9e5e",
  "name": "03a6d094-3444-4b3d-88af-7477090a9e5e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.IntelligentSystems/accounts/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Intelligent Systems Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-contributor"></a>Logic App-deltagare

Gör att du kan hantera logikappar, men inte ändra åtkomst till dem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Visar en lista över åtkomstnycklarna för lagringskontona. |
> | Microsoft.ClassicStorage/storageAccounts/read Microsoft.ClassicStorage/storageAccounts/read Microsoft.ClassicStorage/storageAccounts/read Microsoft. | Returnera lagringskontot med det angivna kontot. |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Insights/metricAlerts/* |  |
> | Microsoft.Insights/diagnosticSettings/* | Skapar, uppdaterar eller läser diagnostikinställningen för Analysis Server |
> | Microsoft.Insights/logdefinitions/* | Den här behörigheten är nödvändig för användare som behöver åtkomst till aktivitetsloggar via portalen. Lista loggkategorier i Aktivitetsloggen. |
> | Microsoft.Insights/metricDefinitions/* | Läs måttdefinitioner (lista över tillgängliga måtttyper för en resurs). |
> | Microsoft.Logic/* | Hanterar Logic Apps-resurser. |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/operationsresultat/läsa | Hämta resultaten för prenumerationsåtgärden. |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Storage/storageKonto/listnycklar/åtgärd | Returnerar åtkomstnycklarna för det angivna lagringskontot. |
> | Microsoft.Storage/storageAccounts/read | Returnerar listan över lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | Microsoft.Web/connectionGateways/* | Skapa och hantera en anslutningsgateway. |
> | Microsoft.Web/anslutningar/* | Skapa och hantera en anslutning. |
> | Microsoft.Web/customApis/* | Skapar och hanterar ett anpassat API. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Hämta egenskaperna i en apptjänstplan |
> | Microsoft.Webbplatser/funktioner/listSecrets/åtgärd | Lista funktion hemligheter. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage logic app, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "name": "87a39d53-fc1b-424a-814c-f7e04687dc9e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicStorage/storageAccounts/listKeys/action",
        "Microsoft.ClassicStorage/storageAccounts/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/diagnosticSettings/*",
        "Microsoft.Insights/logdefinitions/*",
        "Microsoft.Insights/metricDefinitions/*",
        "Microsoft.Logic/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listkeys/action",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*",
        "Microsoft.Web/connections/*",
        "Microsoft.Web/customApis/*",
        "Microsoft.Web/serverFarms/join/action",
        "Microsoft.Web/serverFarms/read",
        "Microsoft.Web/sites/functions/listSecrets/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="logic-app-operator"></a>Logic App-operatör

Gör att du kan läsa, aktivera och inaktivera logikappar, men inte redigera eller uppdatera dem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/*/read | Läs insights-varningsregler |
> | Microsoft.Insights/metricAlerts/*/read |  |
> | Microsoft.Insights/diagnosticSettings/*/read | Hämtar diagnostikinställningar för Logic Apps |
> | Microsoft.Insights/metricDefinitions/*/read | Hämtar tillgängliga mått för Logic Apps. |
> | Microsoft.Logic/*/read | Läser Logic Apps-resurser. |
> | Microsoft.Logic/workflows/disable/action | Inaktiverar arbetsflödet. |
> | Microsoft.Logic/workflows/enable/action | Aktiverar arbetsflödet. |
> | Microsoft.Logic/workflows/validate/action | Validerar arbetsflödet. |
> | Microsoft.Resurser/distributioner/åtgärder/läsa | Hämtar eller listar distributionsåtgärder. |
> | Microsoft.Resurser/prenumerationer/operationsresultat/läsa | Hämta resultaten för prenumerationsåtgärden. |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | Microsoft.Web/connectionGateways/*/read | Läs Anslutningsgateways. |
> | Microsoft.Web/anslutningar/*/läs | Läs Anslutningar. |
> | Microsoft.Web/customApis/*/read | Läs anpassat API. |
> | Microsoft.Web/serverFarms/read | Hämta egenskaperna i en apptjänstplan |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read, enable and disable logic app.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "name": "515c2055-d9d4-4321-b1b9-bd0c9a0f79fe",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*/read",
        "Microsoft.Insights/metricAlerts/*/read",
        "Microsoft.Insights/diagnosticSettings/*/read",
        "Microsoft.Insights/metricDefinitions/*/read",
        "Microsoft.Logic/*/read",
        "Microsoft.Logic/workflows/disable/action",
        "Microsoft.Logic/workflows/enable/action",
        "Microsoft.Logic/workflows/validate/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/subscriptions/operationresults/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Web/connectionGateways/*/read",
        "Microsoft.Web/connections/*/read",
        "Microsoft.Web/customApis/*/read",
        "Microsoft.Web/serverFarms/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Logic App Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="identity"></a>Identitet


### <a name="managed-identity-contributor"></a>Hanterad identitetsdeltagare

Skapa, läsa, uppdatera och ta bort användartilldelad identitet

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.ManagedIdentity/userAssignedId entiteter/läsa | Hämtar en befintlig användarbedelad identitet |
> | Microsoft.ManagedIdentity/userAssignedId entiteter/skriva | Skapar en ny användartilldelad identitet eller uppdaterar taggarna som är associerade med en befintlig användartilldelad identitet |
> | Microsoft.ManagedIdentity/userAssignedId entiteter/borttagning | Tar bort en befintlig användarbedelad identitet |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create, Read, Update, and Delete User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "name": "e40ec5ca-96e0-45a2-b4ff-59039f2c2b59",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/write",
        "Microsoft.ManagedIdentity/userAssignedIdentities/delete",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-identity-operator"></a>Operatör för hanterad identitet

Läsa och tilldela användartilldelad identitet

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read and Assign User Assigned Identity",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f1a07417-d97a-45cb-824c-7a7467783830",
  "name": "f1a07417-d97a-45cb-824c-7a7467783830",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/read",
        "Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Identity Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="security"></a>Säkerhet


### <a name="azure-sentinel-contributor"></a>Azure Sentinel-deltagare

Azure Sentinel-deltagare

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.SecurityInights/* |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Sök med ny motor. |
> | Microsoft.OperationalInsights/workspaces/*/read | Visa logganalysdata |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* |  |
> | Microsoft.OperationsManagement/lösningar/läsa | Hämta spännande OMS-lösning |
> | Microsoft.OperationalInsights/workspaces/query/read Microsoft.OperationalInsights/workspaces/query/read Microsoft.OperationalInsights/workspaces/query/read Microsoft. | Köra frågor över data på arbetsytan |
> | Microsoft.OperationalInsights/workspaces/query/*/read Microsoft.OperationalInsights/workspaces/query/*/read Microsoft.OperationalInsights/workspaces/query/*/read Microsoft. |  |
> | Microsoft.OperationalInsights/workspaces/dataSources/read Microsoft.OperationalInsights/workspaces/dataSources/read Microsoft.OperationalInsights/workspaces/dataSources/read Microsoft. | Hämta datakällor under en arbetsyta. |
> | Microsoft.Insights/arbetsböcker/* |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Contributor",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ab8e14d6-4a74-4a29-9ba8-549422addade",
  "name": "ab8e14d6-4a74-4a29-9ba8-549422addade",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-reader"></a>Azure Sentinel-läsare

Azure Sentinel-läsare

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.SecurityInights/*/read |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Sök med ny motor. |
> | Microsoft.OperationalInsights/workspaces/*/read | Visa logganalysdata |
> | Microsoft.OperationalInsights/workspaces/LinkedServices/read Microsoft.OperationalInsights/workspaces/LinkedServices/read Microsoft.OperationalInsights/workspaces/LinkedServices/read Microsoft. | Få länkade tjänster under angiven arbetsyta. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/read Microsoft.OperationalInsights/workspaces/savedSearches/read Microsoft.OperationalInsights/workspaces/savedSearches/read Microsoft. | Hämtar en sparad sökfråga |
> | Microsoft.OperationsManagement/lösningar/läsa | Hämta spännande OMS-lösning |
> | Microsoft.OperationalInsights/workspaces/query/read Microsoft.OperationalInsights/workspaces/query/read Microsoft.OperationalInsights/workspaces/query/read Microsoft. | Köra frågor över data på arbetsytan |
> | Microsoft.OperationalInsights/workspaces/query/*/read Microsoft.OperationalInsights/workspaces/query/*/read Microsoft.OperationalInsights/workspaces/query/*/read Microsoft. |  |
> | Microsoft.OperationalInsights/workspaces/dataSources/read Microsoft.OperationalInsights/workspaces/dataSources/read Microsoft.OperationalInsights/workspaces/dataSources/read Microsoft. | Hämta datakällor under en arbetsyta. |
> | Microsoft.Insights/arbetsböcker/läsa | Läsa en arbetsbok |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Reader",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "name": "8d289c81-5878-46d4-8554-54e1e3d8b5cb",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/LinkedServices/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-sentinel-responder"></a>Azure Sentinel Responder

Azure Sentinel Responder

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.SecurityInights/*/read |  |
> | Microsoft.SecurityInights/case/* |  |
> | Microsoft.SecurityInights/incidenter/* |  |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Sök med ny motor. |
> | Microsoft.OperationalInsights/workspaces/*/read | Visa logganalysdata |
> | Microsoft.OperationalInsights/workspaces/dataSources/read Microsoft.OperationalInsights/workspaces/dataSources/read Microsoft.OperationalInsights/workspaces/dataSources/read Microsoft. | Hämta datakällor under en arbetsyta. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/read Microsoft.OperationalInsights/workspaces/savedSearches/read Microsoft.OperationalInsights/workspaces/savedSearches/read Microsoft. | Hämtar en sparad sökfråga |
> | Microsoft.OperationsManagement/lösningar/läsa | Hämta spännande OMS-lösning |
> | Microsoft.OperationalInsights/workspaces/query/read Microsoft.OperationalInsights/workspaces/query/read Microsoft.OperationalInsights/workspaces/query/read Microsoft. | Köra frågor över data på arbetsytan |
> | Microsoft.OperationalInsights/workspaces/query/*/read Microsoft.OperationalInsights/workspaces/query/*/read Microsoft.OperationalInsights/workspaces/query/*/read Microsoft. |  |
> | Microsoft.OperationalInsights/workspaces/dataSources/read Microsoft.OperationalInsights/workspaces/dataSources/read Microsoft.OperationalInsights/workspaces/dataSources/read Microsoft. | Hämta datakällor under en arbetsyta. |
> | Microsoft.Insights/arbetsböcker/läsa | Läsa en arbetsbok |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Azure Sentinel Responder",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "name": "3e150937-b8fe-4cfb-8069-0eaf05ecd056",
  "permissions": [
    {
      "actions": [
        "Microsoft.SecurityInsights/*/read",
        "Microsoft.SecurityInsights/cases/*",
        "Microsoft.SecurityInsights/incidents/*",
        "Microsoft.OperationalInsights/workspaces/analytics/query/action",
        "Microsoft.OperationalInsights/workspaces/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.OperationalInsights/workspaces/savedSearches/read",
        "Microsoft.OperationsManagement/solutions/read",
        "Microsoft.OperationalInsights/workspaces/query/read",
        "Microsoft.OperationalInsights/workspaces/query/*/read",
        "Microsoft.OperationalInsights/workspaces/dataSources/read",
        "Microsoft.Insights/workbooks/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Sentinel Responder",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="key-vault-contributor"></a>Nyckelvalvsdeltagare

Gör att du kan hantera nyckelvalv, men inte komma åt dem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Rensa ett mjukt borttaget nyckelvalv |
> | Microsoft.KeyVault/hsmPools/* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage key vaults, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/f25e0fa2-a7c8-4377-a976-54943a77a395",
  "name": "f25e0fa2-a7c8-4377-a976-54943a77a395",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.KeyVault/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [
        "Microsoft.KeyVault/locations/deletedVaults/purge/action",
        "Microsoft.KeyVault/hsmPools/*"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Key Vault Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-admin"></a>Säkerhetsadministratör

Kan visa säkerhetsprinciper, visa säkerhetstillstånd, redigera säkerhetsprinciper, visa aviseringar och rekommendationer, avvisa aviseringar och rekommendationer.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Authorization/policyTilldelningar/* | Skapa och hantera principtilldelningar |
> | Microsoft.Authorization/policyDefinitions/* | Skapa och hantera principdefinitioner |
> | Microsoft.Authorization/policySetDefinitions/* | Skapa och hantera principuppsättningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Management/managementGroups/read | Lista hanteringsgrupper för den autentiserade användaren. |
> | Microsoft.operationalInsights/workspaces/*/read | Visa logganalysdata |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Security/* | Skapa och hantera säkerhetskomponenter och principer |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Admin Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "name": "fb1c8493-542b-48eb-b624-b4c8fea62acd",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Authorization/policyAssignments/*",
        "Microsoft.Authorization/policyDefinitions/*",
        "Microsoft.Authorization/policySetDefinitions/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Admin",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-assessment-contributor"></a>Rapportör för säkerhetsbedömning

Gör att du kan skicka utvärderingar till Security Center

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Säkerhet/utvärdering/skrivning | Skapa eller uppdatera säkerhetsbedömningar på din prenumeration |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you push assessments to Security Center",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "name": "612c2aa1-cb24-443b-ac28-3ab7272de6f5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Security/assessments/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Assessment Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-manager-legacy"></a>Säkerhetshanteraren (äldre)

Detta är en arvsroll. Använd säkerhetsadministratör i stället.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.ClassicCompute/*/read | Läs konfigurationsinformation klassiska virtuella datorer |
> | Microsoft.ClassicCompute/virtualMachines/*/write Microsoft.ClassicCompute/virtualMachines/*/write Microsoft.ClassicCompute/virtualMachines/*/write Microsoft. | Skrivkonfiguration för klassiska virtuella datorer |
> | Microsoft.ClassicNetwork/*/read | Läs konfigurationsinformation om klassiska nätverk |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Security/* | Skapa och hantera säkerhetskomponenter och principer |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "This is a legacy role. Please use Security Administrator instead",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "name": "e3d13bf0-dd5a-482e-ba6b-9b8433878d10",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.ClassicCompute/*/read",
        "Microsoft.ClassicCompute/virtualMachines/*/write",
        "Microsoft.ClassicNetwork/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Manager (Legacy)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="security-reader"></a>Säkerhetsläsare

Kan visa rekommendationer och aviseringar, visa säkerhetsprinciper, visa säkerhetstillstånd men kan inte göra ändringar.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.operationalInsights/workspaces/*/read | Visa logganalysdata |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Security/*/read | Läs säkerhetskomponenter och principer |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | Microsoft.Management/managementGroups/read | Lista hanteringsgrupper för den autentiserade användaren. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Security Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "name": "39bc4728-0917-49c7-9d2c-d95423bc2eb4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.operationalInsights/workspaces/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Security/*/read",
        "Microsoft.Support/*",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Security Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="devops"></a>DevOps


### <a name="devtest-labs-user"></a>DevTest Labs Användare

Gör att du kan ansluta, starta om, starta om och stänga av dina virtuella datorer i dina Azure DevTest Labs.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Compute/availabilitySets/read | Hämta egenskaperna för en tillgänglighetsuppsättning |
> | Microsoft.Compute/virtualMachines/*/read | Läs egenskaperna för en virtuell dator (VM-storlekar, körningsstatus, VM-tillägg osv.) |
> | Microsoft.Compute/virtualMachines/deallocate/action | Stänger av den virtuella datorn och frigör beräkningsresurserna |
> | Microsoft.Compute/virtualMachines/read | Hämta egenskaperna för en virtuell dator |
> | Microsoft.Compute/virtualMachines/restart/action | Startar om den virtuella datorn |
> | Microsoft.Compute/virtualMachines/start/action | Startar den virtuella datorn |
> | Microsoft.DevTestLab/*/read | Läs egenskaperna för ett labb |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Gör anspråk på en slumpmässig anspråksbar virtuell dator i labbet. |
> | Microsoft.DevTestLab/labs/createMiljö/åtgärd | Skapa virtuella datorer i ett labb. |
> | Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action | Kontrollera att den aktuella användaren har en giltig profil i labbet. |
> | Microsoft.DevTestLab/labs/formler/ta bort | Ta bort formler. |
> | Microsoft.DevTestLab/labs/formler/läsa | Läs formler. |
> | Microsoft.DevTestLab/labs/formler/skrivning | Lägg till eller ändra formler. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Utvärderar labbprincipen. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Bli ägare till en befintlig virtuell dator |
> | Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/åtgärd | Visar eventuella start-/stoppscheman. |
> | Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action | Hämtar en sträng som representerar innehållet i RDP-filen för den virtuella datorn |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Ansluter till en bakåtsträvningsadresspool för belastningsutjämnare. Inte alert. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Ansluter till en ingående nat-regel för lastjämning. Inte alert. |
> | Microsoft.Network/networkInterfaces/*/read | Läs egenskaperna för ett nätverksgränssnitt (till exempel alla belastningsutjämnare som nätverksgränssnittet är en del av) |
> | Microsoft.Network/networkInterfaces/join/action | Ansluter en virtuell dator till ett nätverksgränssnitt. Inte alert. |
> | Microsoft.Network/networkInterfaces/read | Hämtar en definition av nätverksgränssnitt.  |
> | Microsoft.Network/networkInterfaces/write | Skapar ett nätverksgränssnitt eller uppdaterar ett befintligt nätverksgränssnitt.  |
> | Microsoft.Network/publicIPAdresser/*/läs | Läs egenskaperna för en offentlig IP-adress |
> | Microsoft.Network/publicIPAddresses/join/action | Ansluter till en offentlig IP-adress. Inte alert. |
> | Microsoft.Network/publicIPAddresses/read | Hämtar en definition av offentlig IP-adress. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Ansluter till ett virtuellt nätverk. Inte alert. |
> | Microsoft.Resurser/distributioner/åtgärder/läsa | Hämtar eller listar distributionsåtgärder. |
> | Microsoft.Resurser/distributioner/läsa | Hämtar eller listar distributioner. |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Storage/storageAccounts/listKeys/action | Returnerar åtkomstnycklarna för det angivna lagringskontot. |
> | **NotActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read Microsoft.Compute/virtualMachines/vmSizes/read Microsoft.Compute/virtualMachines/vmSizes/read Microsoft. | Visar tillgängliga storlekar som den virtuella datorn kan uppdateras till |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you connect, start, restart, and shutdown your virtual machines in your Azure DevTest Labs.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/76283e04-6283-4c54-8f91-bcf1374a3c64",
  "name": "76283e04-6283-4c54-8f91-bcf1374a3c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Compute/availabilitySets/read",
        "Microsoft.Compute/virtualMachines/*/read",
        "Microsoft.Compute/virtualMachines/deallocate/action",
        "Microsoft.Compute/virtualMachines/read",
        "Microsoft.Compute/virtualMachines/restart/action",
        "Microsoft.Compute/virtualMachines/start/action",
        "Microsoft.DevTestLab/*/read",
        "Microsoft.DevTestLab/labs/claimAnyVm/action",
        "Microsoft.DevTestLab/labs/createEnvironment/action",
        "Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action",
        "Microsoft.DevTestLab/labs/formulas/delete",
        "Microsoft.DevTestLab/labs/formulas/read",
        "Microsoft.DevTestLab/labs/formulas/write",
        "Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action",
        "Microsoft.DevTestLab/labs/virtualMachines/claim/action",
        "Microsoft.DevTestLab/labs/virtualmachines/listApplicableSchedules/action",
        "Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action",
        "Microsoft.Network/loadBalancers/backendAddressPools/join/action",
        "Microsoft.Network/loadBalancers/inboundNatRules/join/action",
        "Microsoft.Network/networkInterfaces/*/read",
        "Microsoft.Network/networkInterfaces/join/action",
        "Microsoft.Network/networkInterfaces/read",
        "Microsoft.Network/networkInterfaces/write",
        "Microsoft.Network/publicIPAddresses/*/read",
        "Microsoft.Network/publicIPAddresses/join/action",
        "Microsoft.Network/publicIPAddresses/read",
        "Microsoft.Network/virtualNetworks/subnets/join/action",
        "Microsoft.Resources/deployments/operations/read",
        "Microsoft.Resources/deployments/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/listKeys/action"
      ],
      "notActions": [
        "Microsoft.Compute/virtualMachines/vmSizes/read"
      ],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "DevTest Labs User",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="lab-creator"></a>Lab Skapare

Gör att du kan skapa, hantera, ta bort dina hanterade labb under dina Azure Lab-konton.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Skapa ett labb i ett labbkonto. |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | Hämta information om regional tillgänglighet för varje storlekskategori som konfigurerats under ett labbkonto |
> | Microsoft.LabServices/labAccounts/getPricingAndAvailability/action | Få priser och tillgänglighet för kombinationer av storlekar, geografiska områden och operativsystem för labbkontot. |
> | Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/åtgärd | Få grundläggande begränsningar och användning för den här prenumerationen |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create, manage, delete your managed labs under your Azure Lab Accounts.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "name": "b97fb8bc-a8b2-4522-a38b-dd33c7e65ead",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.LabServices/labAccounts/*/read",
        "Microsoft.LabServices/labAccounts/createLab/action",
        "Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action",
        "Microsoft.LabServices/labAccounts/getRegionalAvailability/action",
        "Microsoft.LabServices/labAccounts/getPricingAndAvailability/action",
        "Microsoft.LabServices/labAccounts/getRestrictionsAndUsage/action",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Lab Creator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="monitor"></a>Övervaka


### <a name="application-insights-component-contributor"></a>Deltagare i komponentdeltagare för programinsikter

Kan hantera Application Insights-komponenter

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera klassiska varningsregler |
> | Microsoft.Insights/metricAlerts/* | Skapa och hantera nya varningsregler |
> | Microsoft.Insights/komponenter/* | Skapa och hantera Insights-komponenter |
> | Microsoft.Insights/webtests/* | Skapa och hantera Insights webbtester |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage Application Insights components",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ae349356-3a1b-4a5e-921d-050484c6347e",
  "name": "ae349356-3a1b-4a5e-921d-050484c6347e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/metricAlerts/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Component Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="application-insights-snapshot-debugger"></a>Ögonblicksbildfelsökare för programinsikter

Ger användaren behörighet att visa och hämta felsökningsögonblicksbilder som samlats in med snapshotfelsökningsfelsökaren för programinsikter. Observera att dessa behörigheter inte ingår i [rollerna Ägare](#owner) eller [Deltagare.](#contributor)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Gives user permission to use Application Insights Snapshot Debugger features",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "name": "08954f03-6346-4c2e-81c0-ec3a5cfae23b",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Insights/components/*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Application Insights Snapshot Debugger",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-contributor"></a>Övervakningsbidragsgivare

Kan läsa alla övervakningsdata och redigera övervakningsinställningar. Se även [Komma igång med roller, behörigheter och säkerhet med Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | */läs | Läs resurser av alla slag, utom hemligheter. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/activityLogAlerts/* |  |
> | Microsoft.Insights/AlertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Insights/komponenter/* | Skapa och hantera Insights-komponenter |
> | Microsoft.Insights/DiagnosticSettings/* | Skapar, uppdaterar eller läser diagnostikinställningen för Analysis Server |
> | Microsoft.Insights/eventtypes/* | Lista aktivitetslogghändelser (hanteringshändelser) i en prenumeration. Den här behörigheten gäller både programmatisk och portalåtkomst till aktivitetsloggen. |
> | Microsoft.Insights/LogDefinitions/* | Den här behörigheten är nödvändig för användare som behöver åtkomst till aktivitetsloggar via portalen. Lista loggkategorier i Aktivitetsloggen. |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | Läs måttdefinitioner (lista över tillgängliga måtttyper för en resurs). |
> | Microsoft.Insights/Metrics/* | Läs mått för en resurs. |
> | Microsoft.Insights/Register/Åtgärd | Registrera Microsoft Insights-leverantören |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Skapa och hantera Insights webbtester |
> | Microsoft.Insights/arbetsböcker/* |  |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Läs/skriv/ta bort logganalyslösningspaket. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Läs/skriv/ta bort logganalys sparade sökningar. |
> | Microsoft.OperationalInsights/workspaces/search/action | Kör en sökfråga |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Hämtar de delade nycklarna för arbetsytan. Dessa nycklar används för att ansluta Microsoft Operational Insights-agenter till arbetsytan. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Läs/skriv/ta bort lagringsstatistikkonfigurationer för logganalys. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | Microsoft.WorkloadMonitor/monitorer/* |  |
> | Microsoft.WorkloadMonitor/notificationSettings/* |  |
> | Microsoft.AlertsManagement/smartDetectorAlertRules/* |  |
> | Microsoft.AlertsManagement/actionRules/* |  |
> | Microsoft.AlertsManagement/smartGroups/* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data and update monitoring settings.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "name": "749f88d5-cbae-40b8-bcfc-e573ddc772fa",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.AlertsManagement/alerts/*",
        "Microsoft.AlertsManagement/alertsSummary/*",
        "Microsoft.Insights/actiongroups/*",
        "Microsoft.Insights/activityLogAlerts/*",
        "Microsoft.Insights/AlertRules/*",
        "Microsoft.Insights/components/*",
        "Microsoft.Insights/DiagnosticSettings/*",
        "Microsoft.Insights/eventtypes/*",
        "Microsoft.Insights/LogDefinitions/*",
        "Microsoft.Insights/metricalerts/*",
        "Microsoft.Insights/MetricDefinitions/*",
        "Microsoft.Insights/Metrics/*",
        "Microsoft.Insights/Register/Action",
        "Microsoft.Insights/scheduledqueryrules/*",
        "Microsoft.Insights/webtests/*",
        "Microsoft.Insights/workbooks/*",
        "Microsoft.OperationalInsights/workspaces/intelligencepacks/*",
        "Microsoft.OperationalInsights/workspaces/savedSearches/*",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.OperationalInsights/workspaces/sharedKeys/action",
        "Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*",
        "Microsoft.Support/*",
        "Microsoft.WorkloadMonitor/monitors/*",
        "Microsoft.WorkloadMonitor/notificationSettings/*",
        "Microsoft.AlertsManagement/smartDetectorAlertRules/*",
        "Microsoft.AlertsManagement/actionRules/*",
        "Microsoft.AlertsManagement/smartGroups/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-metrics-publisher"></a>Utgivare av övervakningsmått

Aktiverar publiceringsmått mot Azure-resurser

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Insights/Register/Åtgärd | Registrera Microsoft Insights-leverantören |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.Insights/Metrics/Write | Skriv mått |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Enables publishing metrics against Azure resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c390055eb",
  "name": "3913510d-42f4-4e42-8a64-420c390055eb",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/Register/Action",
        "Microsoft.Support/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.Insights/Metrics/Write"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Metrics Publisher",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="monitoring-reader"></a>Övervakningsläsare

Kan läsa alla övervakningsdata (mått, loggar, etc.). Se även [Komma igång med roller, behörigheter och säkerhet med Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles).

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | */läs | Läs resurser av alla slag, utom hemligheter. |
> | Microsoft.OperationalInsights/workspaces/search/action | Kör en sökfråga |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read all monitoring data.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "name": "43d0d8ad-25c7-4714-9337-8ba259a9fe05",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.OperationalInsights/workspaces/search/action",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Monitoring Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-contributor"></a>Arbetsboksdeltagare

Kan spara delade arbetsböcker.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Insights/arbetsböcker/skriva | Skapa eller uppdatera en arbetsbok |
> | Microsoft.Insights/arbetsböcker/ta bort | Ta bort en arbetsbok |
> | Microsoft.Insights/arbetsböcker/läsa | Läsa en arbetsbok |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can save shared workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "name": "e8ddcd69-c73f-4f9f-9844-4100522f16ad",
  "permissions": [
    {
      "actions": [
        "Microsoft.Insights/workbooks/write",
        "Microsoft.Insights/workbooks/delete",
        "Microsoft.Insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="workbook-reader"></a>Arbetsboksläsare

Kan läsa arbetsböcker.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | microsoft.insights/workbooks/read | Läsa en arbetsbok |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read workbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "name": "b279062a-9be3-42a0-92ae-8b3cf002ec4d",
  "permissions": [
    {
      "actions": [
        "microsoft.insights/workbooks/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Workbook Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="management--governance"></a>Förvaltning + styrning


### <a name="automation-job-operator"></a>Jobboperator för automatisering

Skapa och hantera jobb med hjälp av Automation Runbooks.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Automation/automationKonto/hybridRunbookWorkerGroups/read | Läser hybridkörningsarbetsresurser |
> | Microsoft.Automation/automationKonto/jobb/läsa | Hämtar ett Azure Automation-jobb |
> | Microsoft.Automation/automationKonto/jobb/återuppta/åtgärd | Återupptar ett Azure Automation-jobb |
> | Microsoft.Automation/automationKonto/jobb/stopp/åtgärd | Stoppar ett Azure Automation-jobb |
> | Microsoft.Automation/automationKonto/jobb/strömmar/läsa | Hämtar en Azure Automation-jobbström |
> | Microsoft.Automation/automationKonto/jobb/pausa/åtgärd | Pausar ett Azure Automation-jobb |
> | Microsoft.Automation/automationKonton/jobb/skrivning | Skapar ett Azure Automation-jobb |
> | Microsoft.Automation/automationKonto/jobb/utdata/läsa | Hämtar utdata för ett jobb |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Create and Manage Jobs using Automation Runbooks.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "name": "4fe576fe-1146-4730-92eb-48519fa6bf9f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Job Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-operator"></a>Automation-operatör

Automationsoperatörer kan starta, stoppa, pausa och återuppta jobb

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Automation/automationKonto/hybridRunbookWorkerGroups/read | Läser hybridkörningsarbetsresurser |
> | Microsoft.Automation/automationKonto/jobb/läsa | Hämtar ett Azure Automation-jobb |
> | Microsoft.Automation/automationKonto/jobb/återuppta/åtgärd | Återupptar ett Azure Automation-jobb |
> | Microsoft.Automation/automationKonto/jobb/stopp/åtgärd | Stoppar ett Azure Automation-jobb |
> | Microsoft.Automation/automationKonto/jobb/strömmar/läsa | Hämtar en Azure Automation-jobbström |
> | Microsoft.Automation/automationKonto/jobb/pausa/åtgärd | Pausar ett Azure Automation-jobb |
> | Microsoft.Automation/automationKonton/jobb/skrivning | Skapar ett Azure Automation-jobb |
> | Microsoft.Automation/automationKonton/jobbPlaner/läsa | Får ett Azure Automation-jobbschema |
> | Microsoft.Automation/automationKonton/jobbSchedules/write | Skapar ett Azure Automation-jobbschema |
> | Microsoft.Automation/automationKonto/linkedWorkspace/read | Hämtar arbetsytan länkad till automationskontot |
> | Microsoft.Automation/automationKonto/läsa | Hämtar ett Azure Automation-konto |
> | Microsoft.Automation/automationKonto/runbooks/read | Hämtar en Azure Automation-runbook |
> | Microsoft.Automation/automationKonto/scheman/läsa | Hämtar en Azure Automation-schematillgång |
> | Microsoft.Automation/automationKonton/scheman/skrivning | Skapar eller uppdaterar en Azure Automation-schematillgång |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Automation/automationKonto/jobb/utdata/läsa | Hämtar utdata för ett jobb |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Automation Operators are able to start, stop, suspend, and resume jobs",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/d3881f73-407a-4167-8283-e981cbba0404",
  "name": "d3881f73-407a-4167-8283-e981cbba0404",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read",
        "Microsoft.Automation/automationAccounts/jobs/read",
        "Microsoft.Automation/automationAccounts/jobs/resume/action",
        "Microsoft.Automation/automationAccounts/jobs/stop/action",
        "Microsoft.Automation/automationAccounts/jobs/streams/read",
        "Microsoft.Automation/automationAccounts/jobs/suspend/action",
        "Microsoft.Automation/automationAccounts/jobs/write",
        "Microsoft.Automation/automationAccounts/jobSchedules/read",
        "Microsoft.Automation/automationAccounts/jobSchedules/write",
        "Microsoft.Automation/automationAccounts/linkedWorkspace/read",
        "Microsoft.Automation/automationAccounts/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Automation/automationAccounts/schedules/read",
        "Microsoft.Automation/automationAccounts/schedules/write",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Automation/automationAccounts/jobs/output/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="automation-runbook-operator"></a>Automation Runbook Operatör

Läs Runbook-egenskaper - för att kunna skapa jobb i runbooken.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Automation/automationKonto/runbooks/read | Hämtar en Azure Automation-runbook |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Read Runbook properties - to be able to create Jobs of the runbook.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "name": "5fb5aef8-1081-4b8e-bb16-9d5d0385bab5",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Automation/automationAccounts/runbooks/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Automation Runbook Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-onboarding"></a>Onboarding-dator för Azure-ansluten dator

Kan ombord På Azure Connected Machines.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.HybridCompute/maskiner/läsa | Läs alla Azure Arc-datorer |
> | Microsoft.HybridCompute/maskiner/skrivning | Skriver en Azure Arc-datorer |
> | Microsoft.GuestConfiguration/guestConfigurationAssignments/read Microsoft.GuestConfiguration/guestConfigurationAssignments/read Microsoft.GuestConfiguration/guestConfigurationAssignments/read Microsoft. | Hämta gästkonfigurationstilldelning. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "name": "b64e21ea-ac4e-4cdf-9dc9-5b892992bee7",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.GuestConfiguration/guestConfigurationAssignments/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Onboarding",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="azure-connected-machine-resource-administrator"></a>Azure-administratör för anslutna datorresurser

Kan läsa, skriva, ta bort och åter in ombord På Azure Connected Machines.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.HybridCompute/maskiner/läsa | Läs alla Azure Arc-datorer |
> | Microsoft.HybridCompute/maskiner/skrivning | Skriver en Azure Arc-datorer |
> | Microsoft.HybridCompute/maskiner/ta bort | Tar bort en Azure Arc-datorer |
> | Microsoft.HybridCompute/maskiner/återansluta/åtgärd | Återansluter en Azure Arc-datorer |
> | Microsoft.HybridCompute/maskiner/tillägg/skrivning | Installerar eller uppdaterar ett Azure Arc-tillägg |
> | Microsoft.HybridCompute/*/read |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can read, write, delete and re-onboard Azure Connected Machines.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cd570a14-e51a-42ad-bac8-bafd67325302",
  "name": "cd570a14-e51a-42ad-bac8-bafd67325302",
  "permissions": [
    {
      "actions": [
        "Microsoft.HybridCompute/machines/read",
        "Microsoft.HybridCompute/machines/write",
        "Microsoft.HybridCompute/machines/delete",
        "Microsoft.HybridCompute/machines/reconnect/action",
        "Microsoft.HybridCompute/machines/extensions/write",
        "Microsoft.HybridCompute/*/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Azure Connected Machine Resource Administrator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="billing-reader"></a>Faktureringsläsare

Ger läsåtkomst till faktureringsdata

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Fakturering/*/läs | Läs faktureringsinformation |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Förbrukning/*/läs |  |
> | Microsoft.Management/managementGroups/read | Lista hanteringsgrupper för den autentiserade användaren. |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to billing data",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "name": "fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Billing/*/read",
        "Microsoft.Commerce/*/read",
        "Microsoft.Consumption/*/read",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Billing Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-contributor"></a>Medarbetare för skiss

Kan hantera skissdefinitioner, men inte tilldela dem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Blueprint/ritningar/* | Skapa och hantera skissdefinitioner eller skissartefakter. |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can manage blueprint definitions, but not assign them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/41077137-e803-4205-871c-5a86e6a753b4",
  "name": "41077137-e803-4205-871c-5a86e6a753b4",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprints/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="blueprint-operator"></a>Skissoperatör

Kan tilldela befintliga publicerade skisser, men kan inte skapa nya ritningar. Observera att detta bara fungerar om tilldelningen görs med en användartilldelad hanterad identitet.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Blueprint/blueprintAssignments/* | Skapa och hantera skisstilldelningar. |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can assign existing published blueprints, but cannot create new blueprints. NOTE: this only works if the assignment is done with a user-assigned managed identity.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/437d2ced-4a38-4302-8479-ed2bcb43d090",
  "name": "437d2ced-4a38-4302-8479-ed2bcb43d090",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Blueprint/blueprintAssignments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Blueprint Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-contributor"></a>Kostnadshanteringsdeltagare

Kan visa kostnader och hantera kostnadskonfiguration (t.ex. budgetar, export)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Förbrukning/* |  |
> | Microsoft.CostManagement/* |  |
> | Microsoft.Fakturering/faktureringPerioder/läsa |  |
> | Microsoft.Resurser/prenumerationer/läsa | Hämtar listan över prenumerationer. |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | Microsoft.Advisor/konfigurationer/läsa | Hämta konfigurationer |
> | Microsoft.Advisor/rekommendationer/läsa | Läser rekommendationer |
> | Microsoft.Management/managementGroups/read | Lista hanteringsgrupper för den autentiserade användaren. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view costs and manage cost configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/434105ed-43f6-45c7-a02f-909b2ba83430",
  "name": "434105ed-43f6-45c7-a02f-909b2ba83430",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*",
        "Microsoft.CostManagement/*",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="cost-management-reader"></a>Läsare av kostnadshantering

Kan visa kostnadsdata och konfiguration (t.ex. budgetar, export)

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Förbrukning/*/läs |  |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Fakturering/faktureringPerioder/läsa |  |
> | Microsoft.Resurser/prenumerationer/läsa | Hämtar listan över prenumerationer. |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | Microsoft.Advisor/konfigurationer/läsa | Hämta konfigurationer |
> | Microsoft.Advisor/rekommendationer/läsa | Läser rekommendationer |
> | Microsoft.Management/managementGroups/read | Lista hanteringsgrupper för den autentiserade användaren. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Can view cost data and configuration (e.g. budgets, exports)",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/72fafb9e-0641-4937-9268-a91bfd8191a3",
  "name": "72fafb9e-0641-4937-9268-a91bfd8191a3",
  "permissions": [
    {
      "actions": [
        "Microsoft.Consumption/*/read",
        "Microsoft.CostManagement/*/read",
        "Microsoft.Billing/billingPeriods/read",
        "Microsoft.Resources/subscriptions/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "Microsoft.Advisor/configurations/read",
        "Microsoft.Advisor/recommendations/read",
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Cost Management Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-contributor-role"></a>Rollen Hanterad programdeltagare

Gör det möjligt att skapa hanterade programresurser.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | */läs | Läs resurser av alla slag, utom hemligheter. |
> | Microsoft.Solutions/program/* |  |
> | Microsoft.Lösningar/registrera/åtgärd | Registrera dig på lösningar. |
> | Microsoft.Resources/subscriptions/resourceGroups/* |  |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows for creating managed application resources.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/641177b8-a67a-45b9-a033-47bc880bb21e",
  "name": "641177b8-a67a-45b9-a033-47bc880bb21e",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/*",
        "Microsoft.Solutions/register/action",
        "Microsoft.Resources/subscriptions/resourceGroups/*",
        "Microsoft.Resources/deployments/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Contributor Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-application-operator-role"></a>Rollen Hanterad programoperator

Gör att du kan läsa och utföra åtgärder på resurser för hanterade program

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | */läs | Läs resurser av alla slag, utom hemligheter. |
> | Microsoft.Solutions/program/läsa | Hämtar en lista över program. |
> | Microsoft.Solutions/*/action |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read and perform actions on Managed Application resources",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "name": "c7393b34-138c-406f-901b-d8cf2b17e6ae",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Solutions/applications/read",
        "Microsoft.Solutions/*/action"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Application Operator Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-applications-reader"></a>Läsare av hanterade program

Gör att du kan läsa resurser i en hanterad app och begära JIT-åtkomst.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | */läs | Läs resurser av alla slag, utom hemligheter. |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Solutions/jitRequests/* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you read resources in a managed app and request JIT access.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "name": "b9331d33-8a36-4f8c-b097-4f54124fdb44",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Solutions/jitRequests/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Applications Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="managed-services-registration-assignment-delete-role"></a>Borttagningsroll för borttagning av hanterade tjänster

Med rollen borttagning av borttagning av hanterade tjänster kan hantera klientanvändare ta bort registreringstilldelningen som tilldelats klienten.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.ManagedServices/registrationAtilldelningar/läsa | Hämtar en lista över registreringstilldelningar för hanterade tjänster. |
> | Microsoft.ManagedServices/registrationAtilldelningar/borttagning | Tar bort registreringstilldelning för hanterade tjänster. |
> | Microsoft.ManagedServices/operationStatuses/read | Läser åtgärdens status för resursen. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Managed Services Registration Assignment Delete Role allows the managing tenant users to delete the registration assignment assigned to their tenant.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/91c1777a-f3dc-4fae-b103-61d183457e46",
  "name": "91c1777a-f3dc-4fae-b103-61d183457e46",
  "permissions": [
    {
      "actions": [
        "Microsoft.ManagedServices/registrationAssignments/read",
        "Microsoft.ManagedServices/registrationAssignments/delete",
        "Microsoft.ManagedServices/operationStatuses/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Managed Services Registration assignment Delete Role",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-contributor"></a>Medarbetare i ledningsgruppen

Rollen som deltagarroll i ledningsgruppen

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Management/managementGroups/delete | Ta bort hanteringsgrupp. |
> | Microsoft.Management/managementGroups/read | Lista hanteringsgrupper för den autentiserade användaren. |
> | Microsoft.Management/managementGroups/subscriptions/delete Microsoft.Management/managementGroups/subscriptions/delete Microsoft.Management/managementGroups/subscriptions/delete Microsoft. | Av-associerar prenumeration från ledningsgruppen. |
> | Microsoft.Management/managementGroups/subscriptions/write Microsoft.Management/managementGroups/subscriptions/write Microsoft.Management/managementGroups/subscriptions/write Microsoft. | Associerar befintlig prenumeration med ledningsgruppen. |
> | Microsoft.Management/managementGroups/write Microsoft.Management/managementGroups/write Microsoft.Management/managementGroups/write Microsoft. | Skapa eller uppdatera en hanteringsgrupp. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Contributor Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "name": "5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/delete",
        "Microsoft.Management/managementGroups/read",
        "Microsoft.Management/managementGroups/subscriptions/delete",
        "Microsoft.Management/managementGroups/subscriptions/write",
        "Microsoft.Management/managementGroups/write"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="management-group-reader"></a>Läsare av ledningsgrupper

Roll för läsare av hanteringsgrupp

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Management/managementGroups/read | Lista hanteringsgrupper för den autentiserade användaren. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Management Group Reader Role",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/ac63b705-f282-497d-ac71-919bf39d939d",
  "name": "ac63b705-f282-497d-ac71-919bf39d939d",
  "permissions": [
    {
      "actions": [
        "Microsoft.Management/managementGroups/read"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Management Group Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="new-relic-apm-account-contributor"></a>Ny relik APM-kontodeltagare

Gör att du kan hantera nya relikprogramprestandahanteringskonton och program, men inte åtkomst till dem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | NewRelic.APM/konton/* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage New Relic Application Performance Management accounts and applications, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5d28c62d-5b37-4476-8438-e587778df237",
  "name": "5d28c62d-5b37-4476-8438-e587778df237",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*",
        "NewRelic.APM/accounts/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "New Relic APM Account Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="policy-insights-data-writer-preview"></a>Data writer för policyinsikter (förhandsgranskning)

Tillåter läsåtkomst till resursprinciper och skrivåtkomst till principhändelser för resurskomponenter.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Authorization/policyassignments/read | Få information om en principtilldelning. |
> | Microsoft.Authorization/policydefinitions/read | Få information om en principdefinition. |
> | Microsoft.Authorization/policysetdefinitioner/read | Få information om en principuppsättningsdefinition. |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | Microsoft.PolicyInsights/checkDataPolicyCompliance/åtgärd | Kontrollera efterlevnadsstatus för en viss komponent mot dataprinciper. |
> | Microsoft.PolicyInsights/policyEvents/logDataEvents/åtgärd | Logga principhändelserna för resurskomponenten. |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Allows read access to resource policies and write access to resource component policy events.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "name": "66bb4e9e-b016-4a94-8249-4c0511c2be84",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/policyassignments/read",
        "Microsoft.Authorization/policydefinitions/read",
        "Microsoft.Authorization/policysetdefinitions/read"
      ],
      "notActions": [],
      "dataActions": [
        "Microsoft.PolicyInsights/checkDataPolicyCompliance/action",
        "Microsoft.PolicyInsights/policyEvents/logDataEvents/action"
      ],
      "notDataActions": []
    }
  ],
  "roleName": "Policy Insights Data Writer (Preview)",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="resource-policy-contributor"></a>Deltagare för resursprincip

Användare med behörighet att skapa/ändra resursprincip, skapa supportbiljett och läsa resurser/hierarki.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | */läs | Läs resurser av alla slag, utom hemligheter. |
> | Microsoft.Authorization/policyassignments/* | Skapa och hantera principtilldelningar |
> | Microsoft.Authorization/policydefinitions/* | Skapa och hantera principdefinitioner |
> | Microsoft.Authorization/policysetdefinitioner/* | Skapa och hantera principuppsättningar |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Users with rights to create/modify resource policy, create support ticket and read resources/hierarchy.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/36243c78-bf99-498c-9df9-86d9f8d28608",
  "name": "36243c78-bf99-498c-9df9-86d9f8d28608",
  "permissions": [
    {
      "actions": [
        "*/read",
        "Microsoft.Authorization/policyassignments/*",
        "Microsoft.Authorization/policydefinitions/*",
        "Microsoft.Authorization/policysetdefinitions/*",
        "Microsoft.PolicyInsights/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Resource Policy Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-contributor"></a>Deltagare i webbplatsåterställning

Gör att du kan hantera tjänsten Site Recovery utom skapande av valv och rolltilldelning

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Network/virtualNetworks/read | Hämta definitionen för virtuellt nätverk |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read Microsoft.RecoveryServices/locations/allocatedStamp/read Microsoft.RecoveryServices/locations/allocatedStamp/read Microsoft. | GetAllocatedStamp är intern åtgärd som används av tjänsten |
> | Microsoft.RecoveryServices/locations/allocateStamp/åtgärd | AllocateStamp är intern drift som används av tjänsten |
> | Microsoft.RecoveryServices/Vaults/certifikat/skrivning | Åtgärden Uppdatera resurscertifikat uppdaterar certifikatet för resurs-/valvautentiseringsuppgifter. |
> | Microsoft.RecoveryServices/Valv/extendedInformation/* | Skapa och hantera utökad information relaterad till valv |
> | Microsoft.RecoveryServices/Valv/läsa | Åtgärden Hämta arkiv hämtar ett objekt som representerar Azure-resursen av typen "valv" |
> | Microsoft.RecoveryServices/Valv/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredId-enheter/* | Skapa och hantera registrerade identiteter |
> | Microsoft.RecoveryServices/valv/replikeringAlertSettings/* | Skapa eller uppdatera inställningar för replikeringsavisering |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Läs alla händelser |
> | Microsoft.RecoveryServices/valv/replikeringFabric/* | Skapa och hantera replikeringsinfrastrukturer |
> | Microsoft.RecoveryServices/valv/replikeringJobs/* | Skapa och hantera replikeringsjobb |
> | Microsoft.RecoveryServices/valv/replikeringPolicies/* | Skapa och hantera replikeringsprinciper |
> | Microsoft.RecoveryServices/valv/replikeringÅterkna planer/* | Skapa och hantera återställningsplaner |
> | Microsoft.RecoveryServices/Valv/storageConfig/* | Skapa och hantera lagringskonfiguration för Recovery Services-valv |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read Microsoft.RecoveryServices/Vaults/tokenInfo/read Microsoft.RecoveryServices/Vaults/tokenInfo/read Microsoft. |  |
> | Microsoft.RecoveryServices/Vaults/usages/read Microsoft.RecoveryServices/Vaults/usages/read Microsoft.RecoveryServices/Vaults/usages/read Microsoft. | Returnerar användningsinformation för ett Recovery Services Vault. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read Microsoft.RecoveryServices/Vaults/vaultTokens/read Microsoft.RecoveryServices/Vaults/vaultTokens/read Microsoft. | Åtgärden Arkiv token kan användas för att hämta Vault Token för backend-åtgärder på valvnivå. |
> | Microsoft.RecoveryServices/Valv/övervakningAlerts/* | Läs aviseringar för valvet för återställningstjänster |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read Microsoft. |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Storage/storageAccounts/read | Returnerar listan över lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
> | Microsoft.RecoveryServices/vaults/replicationOperationStatus/read | Läs alla status för valvreplikeringsåtgärd |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Site Recovery service except vault creation and role assignment",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "name": "6670b86e-a3f7-4917-ac9b-5d6ab1be4567",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/certificates/write",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/*",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/*",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/*",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/*",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/*",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/*",
        "Microsoft.RecoveryServices/Vaults/storageConfig/*",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.RecoveryServices/vaults/replicationOperationStatus/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-operator"></a>Operatör för återställning av webbplatser

Gör att du kan redundans och redundans men inte utföra andra hanteringsåtgärder för webbplatsåterställning

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Network/virtualNetworks/read | Hämta definitionen för virtuellt nätverk |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read Microsoft.RecoveryServices/locations/allocatedStamp/read Microsoft.RecoveryServices/locations/allocatedStamp/read Microsoft. | GetAllocatedStamp är intern åtgärd som används av tjänsten |
> | Microsoft.RecoveryServices/locations/allocateStamp/åtgärd | AllocateStamp är intern drift som används av tjänsten |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read Microsoft.RecoveryServices/Vaults/extendedInformation/read Microsoft.RecoveryServices/Vaults/extendedInformation/read Microsoft. | Åtgärden Hämta utökad information hämtar ett objekts utökade information som representerar Azure-resursen av typen ?valv? |
> | Microsoft.RecoveryServices/Valv/läsa | Åtgärden Hämta arkiv hämtar ett objekt som representerar Azure-resursen av typen "valv" |
> | Microsoft.RecoveryServices/Valv/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Åtgärden Hämta operationsresultat kan användas för att hämta operationsstatus och resultat för den asynkront skickade åtgärden |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Åtgärden Hämta behållare kan användas för att hämta behållarna som registrerats för en resurs. |
> | Microsoft.RecoveryServices/valv/replikeringSinställningar/läsning | Läs alla inställningar för aviseringar |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Läs alla händelser |
> | Microsoft.RecoveryServices/valv/replikeringFabric/checkConsistency/åtgärd | Kontrollerar konsistensen av tyget |
> | Microsoft.RecoveryServices/valv/replikeringFabric/läsning | Läs alla tyger |
> | Microsoft.RecoveryServices/valv/replikeringFabric/reassociateGateway/åtgärd | Associera gatewayen för att återassociera |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Förnya certifikat för tyg |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Läs alla nätverk |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Läs alla nätverksmappningar |
> | Microsoft.RecoveryServices/valv/replikeringFabric/replicationSkyddare/läsning | Läs alla skyddsbehållare |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Läs alla skyddade objekt |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Använd återställningspunkt |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Redundanssseställning |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Planerad redundans |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Läsa alla skyddade objekt |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Läs alla återställningspunkter för replikering |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Reparera replikering |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Skydda skyddat objekt på nytt |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action | Behållare för switchskydd |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Testa redundans |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Testa rensning av redundans |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Redundans |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Uppdatera mobilitetstjänsten |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Läsa alla mappningar av skyddsbehållare |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read Microsoft. | Läs alla leverantörer av återställningstjänster |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/åtgärd | Uppdatera provider |
> | Microsoft.RecoveryServices/valv/replikeringFabric/replicationStoraklassificeringar/läsning | Läs alla lagringsklassificeringar |
> | Microsoft.RecoveryServices/valv/replikeringFabric/replicationStoraklassificeringar/replikeringStorgKlassificationmappningar/läsning | Läs alla mappningar för lagringsklassificering |
> | Microsoft.RecoveryServices/valv/replikeringFabric/replicationvCenters/read | Läs alla vCenters |
> | Microsoft.RecoveryServices/valv/replikeringJobs/* | Skapa och hantera replikeringsjobb |
> | Microsoft.RecoveryServices/valv/replikeringPolicies/read | Läs alla policyer |
> | Microsoft.RecoveryServices/valv/replikeringÅterkna planer/redundansCommit/åtgärd | Återställningsplan för återställningsplan för återställning av redundansssering |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Återhämtningsplan för planerad återställning efter fel |
> | Microsoft.RecoveryServices/valv/replikeringÅterkna planer/läsa | Läs alla återställningsplaner |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Återställningsplan för Återskydd |
> | Microsoft.RecoveryServices/valv/replikeringÅterkna planer/testFailover/åtgärd | Testa återställningsplan för redundans |
> | Microsoft.RecoveryServices/valv/replikeringÅterkna planer/testFailoverCleanup/åtgärd | Återställningsplan för återställning av testrensning |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Återställningsplan för redundans |
> | Microsoft.RecoveryServices/Valv/övervakningAlerts/* | Läs aviseringar för valvet för återställningstjänster |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read Microsoft. |  |
> | Microsoft.RecoveryServices/Valv/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read Microsoft.RecoveryServices/Vaults/tokenInfo/read Microsoft.RecoveryServices/Vaults/tokenInfo/read Microsoft. |  |
> | Microsoft.RecoveryServices/Vaults/usages/read Microsoft.RecoveryServices/Vaults/usages/read Microsoft.RecoveryServices/Vaults/usages/read Microsoft. | Returnerar användningsinformation för ett Recovery Services Vault. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read Microsoft.RecoveryServices/Vaults/vaultTokens/read Microsoft.RecoveryServices/Vaults/vaultTokens/read Microsoft. | Åtgärden Arkiv token kan användas för att hämta Vault Token för backend-åtgärder på valvnivå. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Storage/storageAccounts/read | Returnerar listan över lagringskonton eller hämtar egenskaperna för det angivna lagringskontot. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you failover and failback but not perform other Site Recovery management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/494ae006-db33-4328-bf46-533a6560a3ca",
  "name": "494ae006-db33-4328-bf46-533a6560a3ca",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Network/virtualNetworks/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/locations/allocateStamp/action",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/switchprotection/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/*",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/*",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Operator",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="site-recovery-reader"></a>Läsare av återställning av webbplatser

Gör att du kan visa status för webbplatsåterställning men inte utföra andra hanteringsåtgärder

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read Microsoft.RecoveryServices/locations/allocatedStamp/read Microsoft.RecoveryServices/locations/allocatedStamp/read Microsoft. | GetAllocatedStamp är intern åtgärd som används av tjänsten |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read Microsoft.RecoveryServices/Vaults/extendedInformation/read Microsoft.RecoveryServices/Vaults/extendedInformation/read Microsoft. | Åtgärden Hämta utökad information hämtar ett objekts utökade information som representerar Azure-resursen av typen ?valv? |
> | Microsoft.RecoveryServices/Valv/övervakningAlerts/läsa | Hämtar aviseringarna för valvet för återställningstjänster. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read Microsoft. |  |
> | Microsoft.RecoveryServices/Valv/läsa | Åtgärden Hämta arkiv hämtar ett objekt som representerar Azure-resursen av typen "valv" |
> | Microsoft.RecoveryServices/Valv/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Åtgärden Hämta operationsresultat kan användas för att hämta operationsstatus och resultat för den asynkront skickade åtgärden |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Åtgärden Hämta behållare kan användas för att hämta behållarna som registrerats för en resurs. |
> | Microsoft.RecoveryServices/valv/replikeringSinställningar/läsning | Läs alla inställningar för aviseringar |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Läs alla händelser |
> | Microsoft.RecoveryServices/valv/replikeringFabric/läsning | Läs alla tyger |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Läs alla nätverk |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Läs alla nätverksmappningar |
> | Microsoft.RecoveryServices/valv/replikeringFabric/replicationSkyddare/läsning | Läs alla skyddsbehållare |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Läs alla skyddade objekt |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Läsa alla skyddade objekt |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Läs alla återställningspunkter för replikering |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Läsa alla mappningar av skyddsbehållare |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read Microsoft. | Läs alla leverantörer av återställningstjänster |
> | Microsoft.RecoveryServices/valv/replikeringFabric/replicationStoraklassificeringar/läsning | Läs alla lagringsklassificeringar |
> | Microsoft.RecoveryServices/valv/replikeringFabric/replicationStoraklassificeringar/replikeringStorgKlassificationmappningar/läsning | Läs alla mappningar för lagringsklassificering |
> | Microsoft.RecoveryServices/valv/replikeringFabric/replicationvCenters/read | Läs alla vCenters |
> | Microsoft.RecoveryServices/valv/replikeringJobs/läs | Läs alla jobb |
> | Microsoft.RecoveryServices/valv/replikeringPolicies/read | Läs alla policyer |
> | Microsoft.RecoveryServices/valv/replikeringÅterkna planer/läsa | Läs alla återställningsplaner |
> | Microsoft.RecoveryServices/Valv/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read Microsoft.RecoveryServices/Vaults/tokenInfo/read Microsoft.RecoveryServices/Vaults/tokenInfo/read Microsoft. |  |
> | Microsoft.RecoveryServices/Vaults/usages/read Microsoft.RecoveryServices/Vaults/usages/read Microsoft.RecoveryServices/Vaults/usages/read Microsoft. | Returnerar användningsinformation för ett Recovery Services Vault. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read Microsoft.RecoveryServices/Vaults/vaultTokens/read Microsoft.RecoveryServices/Vaults/vaultTokens/read Microsoft. | Åtgärden Arkiv token kan användas för att hämta Vault Token för backend-åtgärder på valvnivå. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you view Site Recovery status but not perform other management operations",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "name": "dbaa88c4-0c30-4179-9fb3-46319faa6149",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.RecoveryServices/locations/allocatedStamp/read",
        "Microsoft.RecoveryServices/Vaults/extendedInformation/read",
        "Microsoft.RecoveryServices/Vaults/monitoringAlerts/read",
        "Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read",
        "Microsoft.RecoveryServices/Vaults/read",
        "Microsoft.RecoveryServices/Vaults/refreshContainers/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read",
        "Microsoft.RecoveryServices/Vaults/registeredIdentities/read",
        "Microsoft.RecoveryServices/vaults/replicationAlertSettings/read",
        "Microsoft.RecoveryServices/vaults/replicationEvents/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read",
        "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read",
        "Microsoft.RecoveryServices/vaults/replicationJobs/read",
        "Microsoft.RecoveryServices/vaults/replicationPolicies/read",
        "Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read",
        "Microsoft.RecoveryServices/Vaults/storageConfig/read",
        "Microsoft.RecoveryServices/Vaults/tokenInfo/read",
        "Microsoft.RecoveryServices/Vaults/usages/read",
        "Microsoft.RecoveryServices/Vaults/vaultTokens/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Site Recovery Reader",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="support-request-contributor"></a>Support begäran bidragsgivare

Gör att du kan skapa och hantera supportförfrågningar

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you create and manage Support requests",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "name": "cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Support Request Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="tag-contributor"></a>Tagga deltagare

Gör att du kan hantera taggar på entiteter, utan att ge åtkomst till entiteterna själva.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Resurser/prenumerationer/resourceGroups/resurser/läsa | Hämtar resurserna för resursgruppen. |
> | Microsoft.Resurser/prenumerationer/resurser/läsa | Hämtar resurser för en prenumeration. |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | Microsoft.Resources/tags/* |  |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage tags on entities, without providing access to the entities themselves.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "name": "4a9ae827-6dc8-4573-8ac7-8239d42aa03f",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
        "Microsoft.Resources/subscriptions/resources/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.Support/*",
        "Microsoft.Resources/tags/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Tag Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="other"></a>Annat


### <a name="biztalk-contributor"></a>BizTalk-medarbetare

Gör att du kan hantera BizTalk-tjänster, men inte komma åt dem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.BizTalkServices/BizTalk/* | Skapa och hantera BizTalk-tjänster |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage BizTalk services, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "name": "5e3c6656-6cfa-4708-81fe-0de47ac73342",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.BizTalkServices/BizTalk/*",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "BizTalk Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

### <a name="scheduler-job-collections-contributor"></a>Deltagare i schemaläggarens jobbsamlingar

Gör att du kan hantera Scheduler-jobbsamlingar, men inte komma åt dem.

> [!div class="mx-tableFixed"]
> |  |  |
> | --- | --- |
> | **Åtgärder** |  |
> | Microsoft.Auktorisering/*/läs | Läsa roller och rolltilldelningar |
> | Microsoft.Insights/alertRules/* | Skapa och hantera insights-varningsregler |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Hämtar tillgänglighetsstatus för alla resurser i det angivna scopet |
> | Microsoft.Resources/deployments/* | Skapa och hantera resursgruppsdistributioner |
> | Microsoft.Resurser/prenumerationer/resourceGroups/read | Hämtar eller listar resursgrupper. |
> | Microsoft.Scheduler/jobcollections/* | Skapa och hantera jobbsamlingar |
> | Microsoft.Support/* | Skapa och hantera supportbiljetter |
> | **NotActions** |  |
> | *inget* |  |
> | **DataÅtgärder** |  |
> | *inget* |  |
> | **NotDataActions** |  |
> | *inget* |  |

```json
{
  "assignableScopes": [
    "/"
  ],
  "description": "Lets you manage Scheduler job collections, but not access to them.",
  "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "name": "188a0f2f-5c9e-469b-ae67-2aa5ce574b94",
  "permissions": [
    {
      "actions": [
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Scheduler/jobcollections/*",
        "Microsoft.Support/*"
      ],
      "notActions": [],
      "dataActions": [],
      "notDataActions": []
    }
  ],
  "roleName": "Scheduler Job Collections Contributor",
  "roleType": "BuiltInRole",
  "type": "Microsoft.Authorization/roleDefinitions"
}
```

## <a name="next-steps"></a>Nästa steg

- [Matcha resursprovidern till tjänsten](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Anpassade Azure-roller](custom-roles.md)
- [Behörigheter i Azure Security Center](../security-center/security-center-permissions.md)
