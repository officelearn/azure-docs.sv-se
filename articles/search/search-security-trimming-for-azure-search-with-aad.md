---
title: Säkerhetsfilter för trimning Azure sökresultat med hjälp av Active Directory-identiteter | Microsoft Docs
description: Åtkomstkontroll på Azure Search-innehåll med säkerhetsfilter och Active Directory-identiteter.
author: revitalbarletz
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 11/07/2017
ms.author: revitalb
ms.openlocfilehash: 75017a1a3a400ca5390210225f26a6c5f3bb7c47
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856172"
---
# <a name="security-filters-for-trimming-azure-search-results-using-active-directory-identities"></a>Säkerhetsfilter för att trimma Azure-sökresultaten med hjälp av Active Directory-identiteter

Den här artikeln visar hur du använder Azure Active Directory (AAD) security identiteter tillsammans med filter i Azure Search för att trimma sökresultat baserat på användarens gruppmedlemskap.

Den här artikeln beskriver följande uppgifter:
> [!div class="checklist"]
- Skapa AAD-grupper och användare
- Koppla användaren till gruppen som du har skapat
- Cachelagra de nya grupperna
- Indexera dokument med associerade grupper
- Utfärda en sökbegäran med grupp-ID: n-filter

>[!NOTE]
> Exemplet kodfragment i den här artikeln är skrivna i C#. Du hittar den fullständiga källkoden [på GitHub](http://aka.ms/search-dotnet-howto). 

## <a name="prerequisites"></a>Förutsättningar

Ditt Azure Search-index måste ha en [security fältet](search-security-trimming-for-azure-search.md) lagra listan grupp identiteter har läsbehörighet till dokumentet. Det här användningsfallet förutsätter en överensstämmelsen mellan ett skyddbara objekt (till exempel en enskild persons college program) och ett fält för säkerhet som anger vem som har åtkomst till det objektet (sjukhusvistelse personal).

Du måste ha administratörsbehörighet för AAD krävs i den här genomgången för att skapa användare, grupper och associationer i AAD.

Programmet måste också vara registrerad med AAD, enligt beskrivningen i följande procedur.

### <a name="register-your-application-with-aad"></a>Registrera din app med AAD

Det här steget integrerar dina program med AAD i syfte att godkänna inloggningar för användarkonton och gruppkonton. Om du inte är en AAD-administratör i din organisation kan du behöva [skapar en ny klient](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) att utföra följande steg.

1. Gå till den [ **Programregistreringsportalen**](https://apps.dev.microsoft.com) >  **konvergerade app** > **lägga till en app**.
2. Ange ett namn för ditt program och klicka sedan på **skapa**. 
3. Välj ditt nyligen registrerade program på sidan Mina program.
4. På registreringssidan program > **plattformar** > **Lägg till plattform**, Välj **webb-API**.
5. Fortfarande på registreringssidan programmet går du till > **Microsoft Graph-behörigheter** > **Lägg till**.
6. Lägg till följande delegerade behörigheter i Select-behörigheter och klicka sedan på **OK**:

   + **Directory.ReadWrite.All**
   + **Group.ReadWrite.All**
   + **User.ReadWrite.All**

Microsoft Graph tillhandahåller ett API som ger programmatisk åtkomst till AAD via ett REST-API. Kodexemplet i den här genomgången använder behörigheter för att anropa Microsoft Graph API för att skapa grupper, användare och associationer. API: er används också för att cache grupp-ID: n för snabbare filtrering.

## <a name="create-users-and-groups"></a>Skapa användare och grupper

Om du lägger till sökning till en etablerad program kanske befintliga användare och grupp-ID i AAD. I det här fallet kan du hoppa över följande tre steg. 

Om du inte har befintliga användare kan använda du Microsoft Graph API: er för att skapa säkerhet huvudnamn. I följande kodavsnitt visar hur du skapar identifierare som blir datavärden för fältet säkerhet i Azure Search-index. Detta kan vara säkerhetsidentifierare för sjukhusvistelse personal i vår hypotetiska college sjukhusvistelse program.

Användar- och gruppmedlemskap kan vara mycket flytande, särskilt i stora organisationer. Kod som skapar identiteter för användare och grupper bör köras tillräckligt ofta för att hämta ändringar i medlemskap i organisationen. På samma sätt kan kräver din Azure Search-index ett liknande uppdateringsschema för att återspegla aktuell status för tillåtna användare och resurser.

### <a name="step-1-create-aad-grouphttpsdevelopermicrosoftcomgraphdocsapi-referencev10apigrouppostgroups"></a>Steg 1: Skapa [AAD-grupp](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/group_post_groups) 
```csharp
// Instantiate graph client 
GraphServiceClient graph = new GraphServiceClient(new DelegateAuthenticationProvider(...));
Group group = new Group()
{
    DisplayName = "My First Prog Group",
    SecurityEnabled = true,
    MailEnabled = false,
    MailNickname = "group1"
}; 
Group newGroup = await graph.Groups.Request().AddAsync(group);
```
   
### <a name="step-2-create-aad-userhttpsdevelopermicrosoftcomgraphdocsapi-referencev10apiuserpostusers"></a>Steg 2: Skapa [AAD-användare](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_post_users) 
```csharp
User user = new User()
{
    GivenName = "First User",
    Surname = "User1",
    MailNickname = "User1",
    DisplayName = "First User",
    UserPrincipalName = "User1@FirstUser.com",
    PasswordProfile = new PasswordProfile() { Password = "********" },
    AccountEnabled = true
};
User newUser = await graph.Users.Request().AddAsync(user);
```

### <a name="step-3-associate-user-and-group"></a>Steg 3: Koppla användare och grupper
```csharp
await graph.Groups[newGroup.Id].Members.References.Request().AddAsync(newUser);
```

### <a name="step-4-cache-the-groups-identifiers"></a>Steg 4: Cachelagra grupper-identifierare
Du kan också för att minska Nätverksfördröjningen, kan du Cachelagra användargrupp kopplingarna så att när en sökbegäran grupper returneras från cachen sparar en tur och RETUR till AAD. Du kan använda (AAD Batch API) [https://developer.microsoft.com/graph/docs/concepts/json_batching] att skicka en Http-begäran med flera användare och skapa cachen.

Microsoft Graph är utformad för att hantera ett stort antal begäranden. Om en överväldigande antalet begäranden sker, inte Microsoft Graph på begäran med HTTP-statuskod 429. Mer information finns i [Microsoft Graph-begränsning](https://developer.microsoft.com/graph/docs/concepts/throttling).

## <a name="index-document-with-their-permitted-groups"></a>Index-dokument med sina tillåtna grupper

Frågeåtgärder i Azure Search körs via ett Azure Search-index. I det här steget importerar en indexeringsåtgärd sökbara data till ett index, inklusive de identifierare som används som säkerhetsfilter. 

Azure Search inte autentisera användaridentiteter eller tillhandahåller logik för att fastställa vilket innehåll en användare har behörighet att visa. Användningsfall för säkerhetsoptimering förutsätter att du anger associationen mellan ett känsliga dokument och grupp-ID att ha åtkomst till dokument, intakta importeras till ett search-index. 

I det hypotetiska exemplet skulle brödtexten i PUT-begäran för ett Azure Search-index med en sökande college uppsats eller avskrift tillsammans med grupp-ID som har behörighet att visa innehållet. 

I exemplet som används i kodexemplet i den här genomgången, index-åtgärd kan se ut på följande sätt:

```csharp
var actions = new IndexAction<SecuredFiles>[]
              {
                  IndexAction.Upload(
                  new SecuredFiles()
                  {
                      FileId = "1",
                      Name = "secured_file_a",
                      GroupIds = new[] { groups[0] }
                  }),
              ...
             };

var batch = IndexBatch.New(actions);

_indexClient.Documents.Index(batch);  
```

## <a name="issue-a-search-request"></a>Utfärda en sökbegäran

Av säkerhetsskäl hur är värdena i din säkerhet fält i indexet statiska värden som används för att inkludera eller exkludera dokument i sökresultaten. Till exempel om gruppidentifierare för sjukhusvistelse ”A11B22C33D44-E55F66G77-H88I99JKK”, är alla dokument i ett Azure Search-index med den identifieraren i security arkiverat ingår (eller undantagna) i sökresultatet som skickas tillbaka till begäranden.

Gå igenom följande steg för att filtrera dokument som returneras i sökresultaten baserat på grupper av användare utfärda begäran.

### <a name="step-1-retrieve-users-group-identifiers"></a>Steg 1: Hämta användarens grupp-ID: n

Om användarens grupper inte har cachelagrade eller cacheminnet har upphört att gälla, utfärdar den [grupper](https://developer.microsoft.com/en-us/graph/docs/api-reference/v1.0/api/directoryobject_getmembergroups) begäran
```csharp
private static void RefreshCacheIfRequired(string user)
{
    if (!_groupsCache.ContainsKey(user))
    {
        var groups = GetGroupIdsForUser(user).Result;
        _groupsCache[user] = groups;
    }
}

private static async Task<List<string>> GetGroupIdsForUser(string userPrincipalName)
{
    List<string> groups = new List<string>();
    var allUserGroupsRequest = graph.Users[userPrincipalName].GetMemberGroups(true).Request();

    while (allUserGroupsRequest != null) 
    {
        IDirectoryObjectGetMemberGroupsRequestBuilder allUserGroups = await allUserGroupsRequest.PostAsync();
        groups = allUserGroups.ToList();
        allUserGroupsRequest = allUserGroups.NextPageRequest;
    }
    return groups;
}
``` 

### <a name="step-2-compose-the-search-request"></a>Steg 2: Skapa sökbegäran

Anta att du har användarens gruppmedlemskap och kan du utfärda sökbegäran med filtervärden.

```csharp
string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", groups.Select(g => g.ToString())));
SearchParameters parameters = new SearchParameters()
             {
                 Filter = filter,
                 Select = new[] { "application essays" }
             };

DocumentSearchResult<SecuredFiles> results = _indexClient.Documents.Search<SecuredFiles>("*", parameters);
```
### <a name="step-3-handle-the-results"></a>Steg 3: Hantera resultaten

Svaret innehåller en filtrerad lista över dokument, som består av de som användaren har behörighet att visa. Beroende på hur du konstruerar sökresultatsidan kanske du vill inkludera visuella tips för att återspegla filtrerad resultatuppsättning.

## <a name="conclusion"></a>Sammanfattning

I den här genomgången ska du lärt dig tekniker för att filtrera dokument i Azure Search-resultaten med AAD-inloggningar trimmar resultatet av dokument som inte matchar det filter som tillhandahålls på begäran.

## <a name="see-also"></a>Se också

+ [Identitetsbaserad åtkomstkontroll med hjälp av Azure Search filter](search-security-trimming-for-azure-search.md)
+ [Filter i Azure Search](search-filters.md)
+ [Säkerhet och åtkomstkontroll i Azure Search-åtgärder](search-security-overview.md)
