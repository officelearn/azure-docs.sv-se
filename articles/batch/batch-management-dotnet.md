---
title: Använda batchhanteringen .NET-bibliotek för att hantera kontoresurser
description: Skapa, ta bort och ändra Azure Batch-kontoresurser med Batch Management .NET-biblioteket.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
tags: azure-resource-manager
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/24/2017
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 79916d769ad8a7228aec8db965c29506ccd78ece
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023692"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Hantera batchkonton och kvoter med batchhanteringsklientbiblioteket för .NET

> [!div class="op_single_selector"]
> * [Azure-portal](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
> 
> 

Du kan sänka underhållskostnaderna i dina Azure Batch-program genom att använda [BATCH Management .NET-biblioteket][api_mgmt_net] för att automatisera batch-kontoskapande, borttagning, nyckelhantering och kvotidentifiering.

* **Skapa och ta bort batchkonton** inom valfri region. Om du som oberoende programvaruleverantör (ISV) till exempel tillhandahåller en tjänst för dina kunder där var och en tilldelas ett separat batchkonto för faktureringsändamål, kan du lägga till funktioner för att skapa och ta bort konton i kundportalen.
* **Hämta och återskapa kontonycklar** programmässigt för något av dina Batch-konton. Detta kan hjälpa dig att följa säkerhetsprinciper som upprätthåller periodisk överrullning eller utgående av kontonycklar. När du har flera Batch-konton i olika Azure-regioner ökar automatiseringen av den här överrullningsprocessen lösningen effektiviteten i lösningen.
* **Kontrollera kontokvoter** och ta bort gissningsjobbet för försök och fel från att avgöra vilka batchkonton som har vilka gränser. Genom att kontrollera dina kontokvoter innan du startar jobb, skapar pooler eller lägger till beräkningsnoder kan du proaktivt justera var eller när dessa beräkningsresurser skapas. Du kan bestämma vilka konton som kräver kvotökningar innan du fördelar ytterligare resurser i dessa konton.
* Kombinera funktioner för **andra Azure-tjänster** för en komplett hanteringsupplevelse – med hjälp av Batch Management .NET, [Azure Active Directory][aad_about]och Azure Resource [Manager][resman_overview] tillsammans i samma program. Genom att använda dessa funktioner och deras API: er kan du tillhandahålla en friktionsfri autentiseringsupplevelse, möjligheten att skapa och ta bort resursgrupper och de funktioner som beskrivs ovan för en heltäckande hanteringslösning.

> [!NOTE]
> Den här artikeln fokuserar på den programmatiska hanteringen av batchkonton, nycklar och kvoter, men du kan utföra många av dessa aktiviteter med hjälp av [Azure-portalen][azure_portal]. Mer information finns i [Skapa ett Azure Batch-konto med Azure-portalen](batch-account-create-portal.md) och [kvoter och gränser för Azure Batch-tjänsten](batch-quota-limit.md).
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Skapa och ta bort batchkonton
Som nämnts är en av de primära funktionerna i batchhanterings-API:et att skapa och ta bort batchkonton i en Azure-region. Det gör du genom att använda [BatchManagementClient.Account.CreateAsync][net_create] och [DeleteAsync][net_delete]eller deras synkrona motsvarigheter.

Följande kodavsnitt skapar ett konto, hämtar det nyskapade kontot från batch-tjänsten och tar sedan bort det. I det här kodavsnittet och `batchManagementClient` de andra i den här artikeln är en helt initierad instans av [BatchManagementClient][net_mgmt_client].

```csharp
// Create a new Batch account
await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount",
    new BatchAccountCreateParameters() { Location = "West US" });

// Get the new account from the Batch service
AccountResource account = await batchManagementClient.Account.GetAsync(
    "MyResourceGroup",
    "mynewaccount");

// Delete the account
await batchManagementClient.Account.DeleteAsync("MyResourceGroup", account.Name);
```

> [!NOTE]
> Program som använder batchhanteringen .NET-biblioteket och klassen BatchManagementClient kräver **tjänstadministratör** eller **coadministratoråtkomst** till prenumerationen som äger batchkontot som ska hanteras. Mer information finns i avsnittet Azure Active Directory och exemplet [på AccountManagement-kod.][acct_mgmt_sample]
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>Hämta och återskapa kontonycklar
Hämta primära och sekundära kontonycklar från alla Batch-konton i din prenumeration med hjälp av [ListKeysAsync][net_list_keys]. Du kan återskapa nycklarna med hjälp av [RegenerateKeyAsync][net_regenerate_keys].

```csharp
// Get and print the primary and secondary keys
BatchAccountListKeyResult accountKeys =
    await batchManagementClient.Account.ListKeysAsync(
        "MyResourceGroup",
        "mybatchaccount");
Console.WriteLine("Primary key:   {0}", accountKeys.Primary);
Console.WriteLine("Secondary key: {0}", accountKeys.Secondary);

// Regenerate the primary key
BatchAccountRegenerateKeyResponse newKeys =
    await batchManagementClient.Account.RegenerateKeyAsync(
        "MyResourceGroup",
        "mybatchaccount",
        new BatchAccountRegenerateKeyParameters() {
            KeyName = AccountKeyType.Primary
            });
```

> [!TIP]
> Du kan skapa ett strömlinjeformat anslutningsarbetsflöde för dina hanteringsprogram. Hämta först en kontonyckel för det Batch-konto som du vill hantera med [ListKeysAsync][net_list_keys]. Använd sedan den här nyckeln när du initierar batch.NET-bibliotekets [batchsharedKeyCredentials-klass,][net_sharedkeycred] som används vid initiering [av BatchClient][net_batch_client].
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Kontrollera Azure-prenumerations- och batchkontokvoter
Azure-prenumerationer och enskilda Azure-tjänster som Batch har alla standardkvoter som begränsar antalet vissa entiteter i dem. Standardkvoter för Azure-prenumerationer finns i [Azure-prenumerations- och tjänstgränser, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md). Standardkvoter för batch-tjänsten finns [i Kvoter och gränser för Azure Batch-tjänsten](batch-quota-limit.md). Genom att använda batchhanteringen .NET-biblioteket kan du kontrollera dessa kvoter i dina program. På så sätt kan du fatta allokeringsbeslut innan du lägger till konton eller beräknar resurser som pooler och beräkningsnoder.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Kontrollera en Azure-prenumeration för batchkontokvoter
Innan du skapar ett batchkonto i en region kan du kontrollera din Azure-prenumeration för att se om du kan lägga till ett konto i den regionen.

I kodavsnittet nedan använder vi först [BatchManagementClient.Account.ListAsync][net_mgmt_listaccounts] för att få en samling av alla Batch-konton som finns i en prenumeration. När vi har fått den här samlingen bestämmer vi hur många konton som finns i målregionen. Sedan använder vi [BatchManagementClient.Subscriptions][net_mgmt_subscriptions] för att hämta batchkontokvoten och bestämma hur många konton (om några) som kan skapas i den regionen.

```csharp
// Get a collection of all Batch accounts within the subscription
BatchAccountListResponse listResponse =
        await batchManagementClient.Account.ListAsync(new AccountListParameters());
IList<AccountResource> accounts = listResponse.Accounts;
Console.WriteLine("Total number of Batch accounts under subscription id {0}:  {1}",
    creds.SubscriptionId,
    accounts.Count);

// Get a count of all accounts within the target region
string region = "westus";
int accountsInRegion = accounts.Count(o => o.Location == region);

// Get the account quota for the specified region
SubscriptionQuotasGetResponse quotaResponse = await batchManagementClient.Subscriptions.GetSubscriptionQuotasAsync(region);
Console.WriteLine("Account quota for {0} region: {1}", region, quotaResponse.AccountQuota);

// Determine how many accounts can be created in the target region
Console.WriteLine("Accounts in {0}: {1}", region, accountsInRegion);
Console.WriteLine("You can create {0} accounts in the {1} region.", quotaResponse.AccountQuota - accountsInRegion, region);
```

I kodavsnittet ovan, `creds` är en instans av [TokenCloudCredentials][azure_tokencreds]. Information om hur du skapar det här objektet finns i exemplet med [AccountManagement-koden][acct_mgmt_sample] på GitHub.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Kontrollera ett batchkonto för beräkningsresurskvoter
Innan du ökar beräkningsresurserna i batch-lösningen kan du kontrollera att de resurser som du vill allokera inte överskrider kontots kvoter. I kodavsnittet nedan skriver vi ut kvotinformationen `mybatchaccount`för batchkontot . I ditt eget program kan du använda sådan information för att avgöra om kontot kan hantera de ytterligare resurser som ska skapas.

```csharp
// First obtain the Batch account
BatchAccountGetResponse getResponse =
    await batchManagementClient.Account.GetAsync("MyResourceGroup", "mybatchaccount");
AccountResource account = getResponse.Resource;

// Now print the compute resource quotas for the account
Console.WriteLine("Core quota: {0}", account.Properties.CoreQuota);
Console.WriteLine("Pool quota: {0}", account.Properties.PoolQuota);
Console.WriteLine("Active job and job schedule quota: {0}", account.Properties.ActiveJobAndJobScheduleQuota);
```

> [!IMPORTANT]
> Det finns standardkvoter för Azure-prenumerationer och -tjänster, men många av dessa gränser kan höjas genom att utfärda en begäran i [Azure-portalen][azure_portal]. Se till exempel [Kvoter och begränsningar för Azure Batch-tjänsten](batch-quota-limit.md) för instruktioner om hur du ökar dina batchkontokvoter.
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Använda Azure AD med Batch Management .NET

Batch Management .NET-biblioteket är en Azure-resursproviderklient och används tillsammans med [Azure Resource Manager][resman_overview] för att hantera kontoresurser programmässigt. Azure AD krävs för att autentisera begäranden som görs via en Azure-resursproviderklient, inklusive Batch Management .NET-biblioteket och via [Azure Resource Manager][resman_overview]. Information om hur du använder Azure AD med batchhantering .NET-biblioteket finns i [Använda Azure Active Directory för att autentisera batchlösningar](batch-aad-auth.md). 

## <a name="sample-project-on-github"></a>Exempel på projekt på GitHub

Om du vill se Batchhantering .NET i aktion kan du kolla in exempelprojektet [AccountManagement][acct_mgmt_sample] på GitHub. Exempelprogrammet AccountManagement visar följande åtgärder:

1. Hämta en säkerhetstoken från Azure AD med hjälp av [ADAL][aad_adal]. Om användaren inte redan är inloggad uppmanas de att ange sina Azure-autentiseringsuppgifter.
2. Med säkerhetstoken som hämtas från Azure AD skapar du en [SubscriptionClient][resman_subclient] för att fråga Azure efter en lista över prenumerationer som är associerade med kontot. Användaren kan välja en prenumeration i listan om den innehåller mer än en prenumeration.
3. Hämta autentiseringsuppgifter som är associerade med den valda prenumerationen.
4. Skapa ett [ResourceManagementClient-objekt][resman_client] med hjälp av autentiseringsuppgifterna.
5. Använd ett [ResourceManagementClient-objekt][resman_client] för att skapa en resursgrupp.
6. Använd ett [BatchManagementClient-objekt][net_mgmt_client] för att utföra flera batchkontoåtgärder:
   * Skapa ett batchkonto i den nya resursgruppen.
   * Hämta det nyskapade kontot från batch-tjänsten.
   * Skriv ut kontonycklarna för det nya kontot.
   * Återskapa en ny primärnyckel för kontot.
   * Skriv ut kvotinformationen för kontot.
   * Skriv ut kvotinformationen för prenumerationen.
   * Skriv ut alla konton i prenumerationen.
   * Ta bort ett nyskapade konto.
7. Ta bort resursgruppen.

Innan du tar bort det nyligen skapade batchkontot och resursgruppen kan du visa dem i [Azure-portalen:][azure_portal]

Om du vill köra exempelprogrammet måste du först registrera det med din Azure AD-klient i Azure-portalen och bevilja behörigheter till Azure Resource Manager API. Följ stegen i Lösningar för [autentisera batchhantering med Active Directory](batch-aad-auth-management.md).


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Vad är Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Autentiseringsscenarier för Azure AD"
[aad_integrate]:../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md "Integrera program med Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: https://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.tokencloudcredentials.aspx
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_list_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.listkeysasync.aspx
[net_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.createasync.aspx
[net_delete]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.deleteasync.aspx
[net_regenerate_keys]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.accountoperationsextensions.regeneratekeyasync.aspx
[net_sharedkeycred]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.auth.batchsharedkeycredentials.aspx
[net_mgmt_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.aspx
[net_mgmt_subscriptions]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.batchmanagementclient.subscriptions.aspx
[net_mgmt_listaccounts]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.batch.iaccountoperations.listasync.aspx
[resman_api]: https://msdn.microsoft.com/library/azure/mt418626.aspx
[resman_client]: https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx
[resman_subclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.subscriptions.subscriptionclient.aspx
[resman_overview]: ../azure-resource-manager/management/overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
