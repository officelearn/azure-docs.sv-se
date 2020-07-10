---
title: Använd batch Management .NET-biblioteket för att hantera konto resurser
description: Skapa, ta bort och ändra Azure Batch konto resurser med batch Management .NET-biblioteket.
ms.topic: how-to
ms.date: 04/24/2017
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 1684a177f139a3acff8a4957c0c1e89f46d8848d
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86145026"
---
# <a name="manage-batch-accounts-and-quotas-with-the-batch-management-client-library-for-net"></a>Hantera batch-konton och kvoter med klient biblioteket för batch-hantering för .NET

> [!div class="op_single_selector"]
> * [Azure-portalen](batch-account-create-portal.md)
> * [Batch Management .NET](batch-management-dotnet.md)
> 
> 

Du kan minska underhålls kostnader i Azure Batch-program genom att använda [batch Management .net][api_mgmt_net] -biblioteket för att automatisera skapandet av batch-konton, borttagning, nyckel hantering och kvot identifiering.

* **Skapa och ta bort batch-konton** inom vilken region som helst. Om du till exempel är en oberoende program varu leverantör (ISV) som till exempel tillhandahåller en tjänst för dina klienter där varje har tilldelats ett separat batch-konto för fakturering kan du lägga till funktioner för att skapa och ta bort konton på kund portalen.
* **Hämta och återskapa konto nycklar** program mässigt för alla batch-konton. Detta kan hjälpa dig att följa säkerhets principer som tvingar regelbunden förnyelse eller förfallo datum för konto nycklar. När du har flera batch-konton i olika Azure-regioner ökar lösningens effektivitet genom automatisering av den här förnyelse processen.
* **Kontrol lera konto kvoter** och ta prov-och fel gissningen att avgöra vilka batch-konton som har vilka gränser. Genom att kontrol lera konto kvoterna innan jobb startas, skapa pooler eller lägga till Compute-noder, kan du proaktivt justera var eller när dessa beräknings resurser skapas. Du kan avgöra vilka konton som kräver kvot ökningar innan du allokerar ytterligare resurser i dessa konton.
* **Kombinera funktioner i andra Azure-tjänster** för en komplett hanterings upplevelse – genom att använda batch Management .net, [Azure Active Directory][aad_about]och [Azure Resource Manager][resman_overview] tillsammans i samma program. Genom att använda dessa funktioner och deras API: er, kan du tillhandahålla en friktions lös autentisering, möjlighet att skapa och ta bort resurs grupper och de funktioner som beskrivs ovan för en heltäckande hanterings lösning.

> [!NOTE]
> Den här artikeln fokuserar på programmerings hanteringen av dina batch-konton, nycklar och kvoter, och du kan utföra många av dessa aktiviteter med hjälp av [Azure Portal][azure_portal]. Mer information finns i [skapa ett Azure Batch konto med hjälp av Azure Portal](batch-account-create-portal.md) och [kvoter och begränsningar för Azure Batchs tjänsten](batch-quota-limit.md).
> 
> 

## <a name="create-and-delete-batch-accounts"></a>Skapa och ta bort batch-konton
Som vi nämnt är en av de viktigaste funktionerna i batch Management-API: et att skapa och ta bort batch-konton i en Azure-region. Det gör du genom att använda [BatchManagementClient. account. CreateAsync][net_create] och [DeleteAsync][net_delete], eller deras synkrona motsvarigheter.

Följande kodfragment skapar ett konto, hämtar det nyligen skapade kontot från batch-tjänsten och tar sedan bort det. I det här kodfragmentet och de andra i den här artikeln `batchManagementClient` är en fullständigt initierad instans av [BatchManagementClient][net_mgmt_client].

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
> Program som använder .NET-biblioteket för batch Management och dess BatchManagementClient-klass kräver **tjänst administratör** eller **Administratörs** åtkomst till den prenumeration som äger det batch-konto som ska hanteras. Mer information finns i avsnittet Azure Active Directory och exemplet på [AccountManagement][acct_mgmt_sample] -kod.
> 
> 

## <a name="retrieve-and-regenerate-account-keys"></a>Hämta och återskapa konto nycklar
Hämta primära och sekundära konto nycklar från alla batch-konton i prenumerationen med hjälp av [ListKeysAsync][net_list_keys]. Du kan återskapa dessa nycklar med hjälp av [RegenerateKeyAsync][net_regenerate_keys].

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
> Du kan skapa ett effektiviserat anslutnings arbets flöde för dina hanterings program. Börja med att skaffa en konto nyckel för det batch-konto som du vill hantera med [ListKeysAsync][net_list_keys]. Använd sedan den här nyckeln när du initierade batch .NET-bibliotekets [BatchSharedKeyCredentials][net_sharedkeycred] -klass, som används vid initiering av [metoden batchclient][net_batch_client].
> 
> 

## <a name="check-azure-subscription-and-batch-account-quotas"></a>Kontrol lera kvoter för Azure-prenumeration och batch-konto
Azure-prenumerationer och enskilda Azure-tjänster som batch alla har standard kvoter som begränsar antalet vissa entiteter i dem. Standard kvoter för Azure-prenumerationer finns i [Azure-prenumerationer, tjänst gränser, kvoter och begränsningar](../azure-resource-manager/management/azure-subscription-service-limits.md). Standard kvoterna för batch-tjänsten finns i [kvoter och begränsningar för tjänsten Azure Batch](batch-quota-limit.md). Med hjälp av .NET-biblioteket för batch Management kan du kontrol lera kvoterna i dina program. På så sätt kan du fatta beslut om tilldelning innan du lägger till konton eller beräknings resurser som pooler och datornoder.

### <a name="check-an-azure-subscription-for-batch-account-quotas"></a>Kontrol lera en Azure-prenumeration för batch-konto kvoter
Innan du skapar ett batch-konto i en region kan du kontrol lera din Azure-prenumeration för att se om du kan lägga till ett konto i den regionen.

I kodfragmentet nedan använder vi först [BatchManagementClient. account. ListAsync][net_mgmt_listaccounts] för att hämta en samling av alla batch-konton som ingår i en prenumeration. När vi har skaffat den här samlingen fastställer vi hur många konton som finns i mål regionen. Sedan använder vi [BatchManagementClient. Subscriptions][net_mgmt_subscriptions] för att hämta kvoten för batch-kontot och bestämma hur många konton (om det finns några) som kan skapas i den regionen.

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

I kodfragmentet ovan `creds` är en instans av [TokenCloudCredentials][azure_tokencreds]. Om du vill se ett exempel på hur du skapar objektet, se kod exemplet [AccountManagement][acct_mgmt_sample] på GitHub.

### <a name="check-a-batch-account-for-compute-resource-quotas"></a>Kontrol lera ett batch-konto för beräknings resurs kvoter
Innan du ökar beräknings resurserna i din batch-lösning kan du kontrol lera att de resurser som du vill allokera inte överskrider kontots kvoter. I kodfragmentet nedan skriver vi ut kvot informationen för batch-kontot med namnet `mybatchaccount` . I ditt eget program kan du använda sådan information för att avgöra om kontot kan hantera ytterligare resurser som ska skapas.

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
> Även om det finns standard kvoter för Azure-prenumerationer och-tjänster kan många av dessa gränser höjas genom att en begäran utfärdas i [Azure Portal][azure_portal]. Se till exempel [kvoter och begränsningar för Azure Batchs tjänsten](batch-quota-limit.md) för instruktioner om hur du ökar dina kvoter för batch-kontot.
> 
> 

## <a name="use-azure-ad-with-batch-management-net"></a>Använda Azure AD med batch Management .NET

Batch Management .NET-biblioteket är en Azure Resource Provider-klient och används tillsammans med [Azure Resource Manager][resman_overview] för att hantera konto resurser program mässigt. Azure AD krävs för att autentisera begär Anden som görs via en Azure Resource Provider-klient, inklusive batch Management .NET-biblioteket och via [Azure Resource Manager][resman_overview]. Information om hur du använder Azure AD med batch Management .NET-biblioteket finns i [använda Azure Active Directory för att autentisera batch-lösningar](batch-aad-auth.md). 

## <a name="sample-project-on-github"></a>Exempel projekt på GitHub

Om du vill se batch Management .NET i praktiken kan du kolla [AccountManagement][acct_mgmt_sample] -exempelprojektet på GitHub. Exempel programmet AccountManagement demonstrerar följande åtgärder:

1. Hämta en säkerhetstoken från Azure AD med hjälp av [ADAL][aad_adal]. Om användaren inte redan är inloggad uppmanas de att ange sina autentiseringsuppgifter för Azure.
2. Med säkerhetstoken som hämtats från Azure AD skapar du en [SubscriptionClient][resman_subclient] för att fråga Azure efter en lista över prenumerationer som är kopplade till kontot. Användaren kan välja en prenumeration i listan om den innehåller fler än en prenumeration.
3. Hämta autentiseringsuppgifter som är associerade med den valda prenumerationen.
4. Skapa ett [ResourceManagementClient][resman_client] -objekt med hjälp av autentiseringsuppgifterna.
5. Använd ett [ResourceManagementClient][resman_client] -objekt för att skapa en resurs grupp.
6. Använd ett [BatchManagementClient][net_mgmt_client] -objekt för att utföra flera batch-konto-åtgärder:
   * Skapa ett batch-konto i den nya resurs gruppen.
   * Hämta det nyligen skapade kontot från batch-tjänsten.
   * Skriv ut konto nycklarna för det nya kontot.
   * Återskapa en ny primär nyckel för kontot.
   * Skriv ut kvot informationen för kontot.
   * Skriv ut kvot informationen för prenumerationen.
   * Skriv ut alla konton i prenumerationen.
   * Ta bort nyligen skapade konto.
7. Ta bort resurs gruppen.

Innan du tar bort det nyligen skapade batch-kontot och resurs gruppen kan du visa dem i [Azure Portal][azure_portal]:

För att köra exempel programmet måste du först registrera det med din Azure AD-klient i Azure Portal och bevilja behörighet till Azure Resource Manager API. Följ de steg som beskrivs i [autentisera hanterings lösningar för batch med Active Directory](batch-aad-auth-management.md).


[aad_about]:../active-directory/fundamentals/active-directory-whatis.md "Vad är Azure Active Directory?"
[aad_adal]: ../active-directory/azuread-dev/active-directory-authentication-libraries.md
[aad_auth_scenarios]:../active-directory/develop/authentication-scenarios.md "Autentiserings scenarier för Azure AD"
[aad_integrate]:../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md "Integrera program med Azure Active Directory"
[acct_mgmt_sample]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement
[api_net]: /dotnet/api/microsoft.azure.batch
[api_mgmt_net]: /dotnet/api/overview/azure/batch
[azure_portal]: https://portal.azure.com
[azure_storage]: https://azure.microsoft.com/services/storage/
[azure_tokencreds]: /previous-versions/azure/reference/mt167728(v=azure.100)
[batch_explorer_project]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[net_batch_client]: /dotnet/api/microsoft.azure.batch.batchclient
[net_list_keys]: /previous-versions/azure/mt463199(v=azure.100)
[net_create]: /previous-versions/azure/mt463210(v=azure.100)
[net_delete]: /previous-versions/azure/mt463128(v=azure.100)
[net_regenerate_keys]: /previous-versions/azure/mt463213(v=azure.100)
[net_sharedkeycred]: /dotnet/api/microsoft.azure.batch.auth.batchsharedkeycredentials
[net_mgmt_client]: /dotnet/api/microsoft.azure.management.batch.batchmanagementclient
[net_mgmt_subscriptions]: /previous-versions/azure/mt592937(v=azure.100)
[net_mgmt_listaccounts]: /previous-versions/azure/mt463134(v=azure.100)
[resman_api]: /previous-versions/azure/mt463134(v=azure.100)
[resman_client]: /dotnet/api/microsoft.azure.management.resourcemanager
[resman_subclient]: /dotnet/api/microsoft.azure.management.resourcemanager
[resman_overview]: ../azure-resource-manager/management/overview.md

[1]: ./media/batch-management-dotnet/portal-01.png
[2]: ./media/batch-management-dotnet/portal-02.png
[3]: ./media/batch-management-dotnet/portal-03.png
