---
title: Hantera resurser i kontot med klientbiblioteket för .NET – Azure Batch | Microsoft Docs
description: Skapa, ta bort och ändra resurser i Azure Batch-kontot med Batch Management .NET-biblioteket.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 16279b23-60ff-4b16-b308-5de000e4c028
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/24/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: c65a71d9e64a1ea3f60abbe2e420652c12ba030e
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2018
ms.locfileid: "53549559"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Hantera Batch-konton och kvoter med Batch Management-klientbiblioteket för .NET

> [!div class="op_single_selector"]
> * [Azure Portal](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
> 
> 

Du kan sänka Underhåll overhead i dina Azure Batch-program med hjälp av den [Batch Management .NET] [ api_mgmt_net] -biblioteket för att automatiskt skapa en Batch-konto, borttagning, nyckelhantering och kvot identifiering.

* **Skapa och ta bort Batch-konton** inom en region. Om som en oberoende programvaruleverantör (ISV) exempelvis kan du tillhandahålla en tjänst för klienter där var och en har tilldelats ett separat Batch-konto för fakturering, du kan lägga till kontot skapas eller tas bort funktioner till din kundportal.
* **Hämta och återskapa nycklar** programmässigt för någon av dina Batch-konton. Detta kan hjälpa dig följa säkerhetsprinciperna som tillämpar periodiska förnya eller upphörande av nycklar. När du har flera Batch-konton i olika Azure-regioner, ökar automatisering av den här processen för förnyelse av effektiviteten för din lösning.
* **Kontrollera kontots kvoter** och ta bort trial and error gissningarna avgör vilken Batch-konton har vilka gränser. Skapar pooler eller att lägga till compute-noder kan du proaktivt kan justera var genom att kontrollera dina kvoter för kontot innan du startar jobb, eller när dessa beräkningsalternativ resurser skapas. Du kan bestämma vilka konton som behövs kvot ökar innan du allokerar ytterligare resurser i dessa konton.
* **Kombinera funktionerna i andra Azure-tjänster** för en komplett hanteringsmiljö – med hjälp av Batch Management .NET [Azure Active Directory][aad_about], och [Azure Resource Manager] [ resman_overview] tillsammans i samma program. Genom att använda dessa funktioner och deras API: er kan ange du en smidig autentiseringsupplevelse möjligheten att skapa och ta bort resursgrupper och de funktioner som beskrivs ovan för en lösning för slutpunkt till slutpunkt.

> [!NOTE]
> Den här artikeln fokuserar på programmässig hantering av dina Batch-konton, nycklar och kvoter, du kan utföra många av dessa aktiviteter med hjälp av den [Azure-portalen][azure_portal]. Mer information finns i [skapar ett Azure Batch-konto med Azure-portalen](batch-account-create-portal.md) och [kvoter och begränsningar för Azure Batch-tjänsten](batch-quota-limit.md).
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Skapa och ta bort Batch-konton
Som tidigare nämnts är en av de primära funktionerna i Batch Management-API att skapa och ta bort Batch-konton i en Azure-region. Du gör detta genom att använda [BatchManagementClient.Account.CreateAsync] [ net_create] och [DeleteAsync][net_delete], eller motsvarigheterna synkron.

Följande kodavsnitt skapar ett konto, hämtar det nya kontot från Batch-tjänsten och tar sedan bort den. I det här kodfragmentet och andra i den här artikeln `batchManagementClient` är en helt initierad instans av [BatchManagementClient][net_mgmt_client].

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
> Program som använder Batch Management .NET-biblioteket och dess BatchManagementClient klass kräver **tjänstadministratör** eller **medadministratör** åtkomst till prenumerationen som äger Batch konto som ska hanteras. Mer information finns i den [Azure Active Directory](#azure-active-directory) avsnittet och [AccountManagement] [ acct_mgmt_sample] kodexempel.
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
> Du kan skapa ett arbetsflöde för smidig anslutning för av hanteringsprogram. Skaffa först en kontonyckel för Batch-kontot som du vill hantera med [ListKeysAsync][net_list_keys]. Använd den här nyckeln när initiering av Batch .NET-biblioteket [BatchSharedKeyCredentials] [ net_sharedkeycred] klass, som används vid initiering av [BatchClient] [ net_batch_client].
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Kontrollera Azure-prenumeration och kvoter för Batch-konto
Azure-prenumerationer och de enskilda Azure-tjänsterna som alla har standardkvoter som begränsar antalet vissa entiteter i dem. Standardkvoter för Azure-prenumerationer, se [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md). Standardkvoter för Batch-tjänsten finns i [kvoter och begränsningar för Azure Batch-tjänsten](batch-quota-limit.md). Du kan kontrollera dessa kvoter i dina program med hjälp av Batch Management .NET-biblioteket. Detta gör att du kan fatta beslut om tilldelning innan du lägger till konton eller beräkna resurser som pooler och beräkningsnoder.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Kontrollera en Azure-prenumeration för kvoter för Batch-konto
Innan du skapar ett Batch-konto i en region, kan du kontrollera din Azure-prenumeration om du vill se om du ska kunna lägga till ett konto i den regionen.

I kodfragmentet nedan först använder vi [BatchManagementClient.Account.ListAsync] [ net_mgmt_listaccounts] att hämta en samling med alla Batch-konton som är inom en prenumeration. När vi har köpt den här samlingen kan fastställa hur många konton är i målregionen. Sedan använder vi [BatchManagementClient.Subscriptions] [ net_mgmt_subscriptions] erhålla kvoten för Batch-konto och fastställa hur många konton (om sådan finns) kan skapas i den regionen.

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

I fragmentet ovan, `creds` är en instans av [TokenCloudCredentials][azure_tokencreds]. Ett exempel på det här objektet skapas finns i den [AccountManagement] [ acct_mgmt_sample] kodexempel på GitHub.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Kontrollera ett Batch-konto för beräkning resurskvoter
Innan du ökar beräkningsresurser i Batch-lösningar, kan du kontrollera för att säkerställa att de resurser som du vill allokera inte överskrida kontots kvoter. I kodfragmentet nedan vi skriva ut kvotinformation för Batch-kontot med namnet `mybatchaccount`. Du kan använda sådan information i ditt eget program för att avgöra om konton som kan hantera de ytterligare resurserna som ska skapas.

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
> Det finns standardkvoter för Azure-prenumerationer och tjänster, många av dessa begränsningar kan höjas genom att utfärda en begäran i den [Azure-portalen][azure_portal]. Se exempelvis [kvoter och begränsningar för Azure Batch-tjänsten](batch-quota-limit.md) anvisningar för att öka dina kvoter för Batch-konto.
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Använd Azure AD med Batch Management .NET

Batch Management .NET-biblioteket är en Azure resource provider-klient och används tillsammans med [Azure Resource Manager] [ resman_overview] att hantera resurser för kontot programmässigt. Azure AD som krävs för att autentisera begäranden som görs via alla Azure resource provider postklienter, inklusive Batch Management .NET-biblioteket och via [Azure Resource Manager][resman_overview]. Information om hur du använder Azure AD med Batch Management .NET-biblioteket finns i [Använd Azure Active Directory att autentisera Batch-lösningar](batch-aad-auth.md). 

## <a name="sample-project-on-github"></a>Exempelprojektet på GitHub

Om du vill se hur Batch Management .NET fungerar, ta en titt på [AccountManagement] [ acct_mgmt_sample] exempelprojektet på GitHub. AccountManagement-exempelprogrammet demonstrerar följande åtgärder:

1. Hämta en säkerhetstoken från Azure AD med hjälp av [ADAL][aad_adal]. Om användaren inte är redan är inloggad uppmanas de att ange sina autentiseringsuppgifter för Azure.
2. Med den säkerhetstoken som hämtats från Azure AD, skapa en [SubscriptionClient] [ resman_subclient] att fråga Azure efter en lista över prenumerationer som är kopplade till kontot. Användaren kan välja en prenumeration i listan om den innehåller mer än en prenumeration.
3. Hämta autentiseringsuppgifter som är associerade med den valda prenumerationen.
4. Skapa en [ResourceManagementClient] [ resman_client] objekt med hjälp av autentiseringsuppgifterna.
5. Använd en [ResourceManagementClient] [ resman_client] objektet för att skapa en resursgrupp.
6. Använd en [BatchManagementClient] [ net_mgmt_client] objektet för att utföra flera åtgärder för Batch-konto:
   * Skapa ett Batch-konto i den nya resursgruppen.
   * Hämta det nya kontot från Batch-tjänsten.
   * Skriva ut kontonycklarna för det nya kontot.
   * Återskapa en ny primär nyckel för kontot.
   * Skriva ut kvotinformation för kontot.
   * Skriva ut kvotinformation för prenumerationen.
   * Skriva ut alla konton inom prenumerationen.
   * Ta bort nyligen skapade kontot.
7. Ta bort resursgruppen.

Innan du tar bort den nyligen skapade konto- och batchgruppen kan du se dem i den [Azure-portalen][azure_portal]:

Om du vill köra exempelprogrammet har du först registrera den med din Azure AD-klient i Azure-portalen och ge behörigheter till Azure Resource Manager-API. Följ stegen i [autentisera med Batch Management-lösningar med Active Directory](batch-aad-auth-management.md).


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
[resman_overview]: ../azure-resource-manager/resource-group-overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
