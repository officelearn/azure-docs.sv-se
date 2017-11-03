---
title: "Hantera resurser för Batch-kontot med klientbiblioteket för .NET - Azure | Microsoft Docs"
description: "Skapa, ta bort och ändra resurser för Azure Batch-kontot med Batch Management .NET-biblioteket."
services: batch
documentationcenter: .net
author: tamram
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 04/24/2017
ms.author: tamram
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: eafde9258222a2ab09ade2e366f9cc595a303dec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Hantera Batch-konton och kvoter med Batch Management klientbiblioteket för .NET

> [!div class="op_single_selector"]
> * [Azure Portal](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
> 
> 

Du kan sänka Underhåll omkostnader i Azure Batch-program med hjälp av den [Batch Management .NET] [ api_mgmt_net] biblioteket för att automatisera skapandet av Batch-konto, borttagning, hantering av nycklar och kvot identifiering.

* **Skapa och ta bort Batch-konton** inom varje region. Som oberoende programvaruleverantör (ISV) till exempel erbjuder en tjänst för klienter där varje tilldelas en separat Batch-kontot för fakturering, kan du lägga till kontot skapas eller tas bort funktioner till kundportalen för.
* **Hämta och återskapa nycklar** programmässigt för någon av Batch-konton. Detta kan hjälpa dig att följa säkerhetsprinciper som framtvingar periodiska förnyelse eller utgången av nycklar. När du har flera Batch-konton i olika Azure-regioner ökar automatisering av den här processen för förnyelse effektiviteten för din lösning.
* **Kontrollera konto kvoter** och sluta utvärderingsversion och fel gissa dig att fastställa vilka Batch-konton som har vilka gränser. Skapa pooler, eller lägga till compute-noder, justerar du proaktivt var genom att kontrollera ditt konto kvoter innan du startar jobb eller när dessa beräkna resurser skapas. Du kan bestämma vilka konton som kräver kvoten ökar innan du ytterligare resurser i dessa konton.
* **Kombinera funktionerna i andra Azure-tjänster** för en komplett hanteringsupplevelse--med Batch Management .NET [Azure Active Directory][aad_about], och [Azure Hanteraren för filserverresurser] [ resman_overview] tillsammans i samma program. Du kan ange ett friktionsfritt autentiseringsupplevelse möjlighet att skapa och ta bort resursgrupper och de funktioner som beskrivs ovan för en hanteringslösning för slutpunkt till slutpunkt med hjälp av dessa funktioner och deras API: er.

> [!NOTE]
> Den här artikeln fokuserar på programmässig hantering av Batch-konton, nycklar och kvoter, kan du utföra många av dessa aktiviteter med hjälp av den [Azure-portalen][azure_portal]. Mer information finns i [skapa ett Azure Batch-konto med hjälp av Azure portal](batch-account-create-portal.md) och [kvoter och gränser för Azure Batch-tjänsten](batch-quota-limit.md).
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Skapa och ta bort Batch-konton
Som tidigare nämnts kan är en av huvudfunktionerna i Batch Management API att skapa och ta bort Batch-konton i en Azure-region. Det gör du genom att använda [BatchManagementClient.Account.CreateAsync] [ net_create] och [DeleteAsync][net_delete], och motsvarigheterna synkront.

Följande kodavsnitt skapar ett konto, får det nya kontot från Batch-tjänsten och tar bort den. I den här fragment och andra i den här artikeln `batchManagementClient` är en helt initierad instans av [BatchManagementClient][net_mgmt_client].

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
> Program som använder Batch Management .NET-bibliotek och dess BatchManagementClient klass kräver **tjänstadministratör** eller **coadministrator** åtkomst till den prenumeration som äger Batch konto som ska hanteras. Mer information finns i [Azure Active Directory](#azure-active-directory) avsnitt och [AccountManagement] [ acct_mgmt_sample] kodexempel.
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>Hämta och återskapa nycklar
Hämta primära och sekundära nycklar från alla Batch-kontot i din prenumeration med hjälp av [ListKeysAsync][net_list_keys]. Du kan återskapa dessa nycklar med hjälp av [RegenerateKeyAsync][net_regenerate_keys].

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
> Du kan skapa en effektiv anslutning arbetsflödet för av hanteringsprogram. Skaffa först en kontonyckel för Batch-kontot som du vill hantera med [ListKeysAsync][net_list_keys]. Använd sedan denna nyckel vid initieringen Batch .NET-bibliotek [BatchSharedKeyCredentials] [ net_sharedkeycred] klass som används vid initieringen [BatchClient] [ net_batch_client].
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Kontrollera Azure-prenumeration och kvoter för Batch-konto
Azure-prenumerationer och enskilda Azure-tjänster som Batch alla har standard kvoterna som begränsar antalet vissa enheter i dem. Standard kvoterna för Azure-prenumerationer, finns i [Azure-prenumeration och tjänsten gränser, kvoter och begränsningar](../azure-subscription-service-limits.md). Standardkvoter för Batch-tjänsten finns [kvoter och gränser för Azure Batch-tjänsten](batch-quota-limit.md). Du kan kontrollera dessa kvoter i dina program med Batch Management .NET-biblioteket. På så sätt kan du att fatta beslut om tilldelning innan du lägger till konton eller beräkningsresurser som pooler och compute-noder.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Kontrollera en Azure-prenumeration för kvoter för Batch-konto
Du kan kontrollera din Azure-prenumeration för att se om du ska kunna lägga till ett konto i den regionen innan du skapar ett Batch-konto i en region.

I kodfragmentet nedan vi först använda [BatchManagementClient.Account.ListAsync] [ net_mgmt_listaccounts] att hämta en samling med alla Batch-konton som är inom en prenumeration. När vi har köpt den här samlingen kan avgöra hur många konton finns i mål-region. Vi använder [BatchManagementClient.Subscriptions] [ net_mgmt_subscriptions] hämta batchkonton och avgöra hur många konton (eventuella) kan skapas i den regionen.

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

Ovanstående kodutdrag `creds` är en instans av [TokenCloudCredentials][azure_tokencreds]. Om du vill se ett exempel på det här objektet skapas, finns det [AccountManagement] [ acct_mgmt_sample] kodexempel på GitHub.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Kontrollera en Batch-kontot för beräkning resurskvoter
Du kan kontrollera för att säkerställa de resurser du vill allokera inte överstiga kontots kvoter innan du ökar beräkningsresurser i Batch-lösning. Vi skriver ut kvotinformation för Batch-kontot med namnet i kodfragmentet nedan `mybatchaccount`. I ditt eget program kunde du använda denna information för att avgöra om kontot kan hantera ytterligare resurser som ska skapas.

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
> Det finns standard kvoterna för Azure-prenumerationer och tjänster, många av dessa begränsningar kan ökas genom att utfärda en begäran i den [Azure-portalen][azure_portal]. Se exempelvis [kvoter och gränser för Azure Batch-tjänsten](batch-quota-limit.md) anvisningar för att öka din kvoter för Batch-kontot.
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Använda Azure AD med Batch Management .NET

Batch Management .NET-biblioteket är en Azure resource provider-klient och används tillsammans med [Azure Resource Manager] [ resman_overview] hantera kontot programmässigt. Azure AD för att kunna autentisera begäranden som görs via alla Azure-resurs providern klienter, inklusive Batch Management .NET-bibliotek och via [Azure Resource Manager][resman_overview]. Information om hur du använder Azure AD med Batch Management .NET-bibliotek finns [Använd Azure Active Directory för att autentisera Batch lösningar](batch-aad-auth.md). 

## <a name="sample-project-on-github"></a>Exempelprojektet på GitHub

Om du vill se Batch Management .NET fungerar, ta en titt på [AccountManagment] [ acct_mgmt_sample] exempelprojektet på GitHub. Exempelprogrammet AccountManagment visas följande åtgärder:

1. Hämta en säkerhetstoken från Azure AD med hjälp av [ADAL][aad_adal]. Om användaren inte redan har signerats uppmanas de för sina autentiseringsuppgifter för Azure.
2. Med den säkerhetstoken som erhållits från Azure AD, skapa en [SubscriptionClient] [ resman_subclient] till Azure-fråga för en lista över prenumerationer som är associerat med kontot. Användaren kan välja en prenumeration i listan om den innehåller mer än en prenumeration.
3. Hämta autentiseringsuppgifterna som associeras med den valda prenumerationen.
4. Skapa en [ResourceManagementClient] [ resman_client] objekt med hjälp av autentiseringsuppgifterna.
5. Använd en [ResourceManagementClient] [ resman_client] objekt för att skapa en resursgrupp.
6. Använd en [BatchManagementClient] [ net_mgmt_client] objekt som ska utföra flera åtgärder för Batch-kontot:
   * Skapa ett Batch-konto i den nya resursgruppen.
   * Hämta det nyligen skapade kontot från Batch-tjänsten.
   * Skriv ut nycklar för det nya kontot.
   * Skapa en ny primär nyckel för kontot.
   * Skriv ut kvotinformation för kontot.
   * Skriv ut kvotinformation för prenumerationen.
   * Skriva ut alla konton i prenumerationen.
   * Nyligen skapade konto bort.
7. Ta bort resursgruppen.

Innan du tar bort den nyligen skapade Batch konto- och -gruppen, kan du visa dem i den [Azure-portalen][azure_portal]:

Om du vill köra exempelprogrammet måste först registrera den med Azure AD-klienten i Azure-portalen och ge behörighet till Azure Resource Manager API. Följ stegen i [autentiserar Batch hanteringslösningar med Active Directory](batch-aad-auth-management.md).


[aad_about]: ../active-directory/active-directory-whatis.md "Vad är Azure Active Directory?"
[aad_adal]: ../active-directory/active-directory-authentication-libraries.md
[aad_auth_scenarios]: ../active-directory/active-directory-authentication-scenarios.md "Autentiseringsscenarier för Azure AD"
[aad_integrate]: ../active-directory/active-directory-integrating-applications.md "Integrera program med Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[api_mgmt_net]: https://msdn.microsoft.com/library/azure/mt463120.aspx
[azure_portal]: http://portal.azure.com
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
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
