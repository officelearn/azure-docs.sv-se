---
title: "Säkerhetsfilter för Azure Search resultat med hjälp av Active Directory identiteter trimning | Microsoft Docs"
description: "Åtkomstkontroll på Azure Search-innehåll med hjälp av säkerhetsfilter och identiteter i Active Directory."
services: search
author: revitalbarletz
manager: jlembicz
ms.service: search
ms.topic: article
ms.date: 11/07/2017
ms.author: revitalb
ms.openlocfilehash: 2113b59d6fec15067acbef8b4d4c1fc34c141e62
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2017
---
# <a name="security-filters-for-trimming-azure-search-results-using-active-directory-identities"></a>Säkerhetsfilter för att reducera Azure sökresultat med hjälp av Active Directory identiteter

Den här artikeln visar hur du använder Azure Active Directory (AAD) säkerhet identiteter tillsammans med filter i Azure Search för att rensa sökresultat baserat på användarens gruppmedlemskap.

Den här artikeln omfattar följande aktiviteter:
> [!div class="checklist"]
- Skapa AAD grupper och användare
- Koppla användaren till gruppen som du har skapat
- Cachelagra nya grupper
- Indexera dokument med associerade grupper
- Skicka en begäran om sökningen med filter för grupp-ID: n

>[!NOTE]
> Exempel kodfragment i den här artikeln är skrivna i C#. Du hittar den fullständiga källkoden [på GitHub](http://aka.ms/search-dotnet-howto). 

## <a name="prerequisites"></a>Krav

Ditt index i Azure Search måste ha en [informationssäkerhet](search-security-trimming-for-azure-search.md) till lagrar listan över gruppidentiteter har läsbehörighet till dokumentet. Den här användningsfall förutsätter en överensstämmelsen mellan ett skyddbara objekt (till exempel en persons universitet program) och ett fält för säkerhet som anger vem som har åtkomst till objektet (upptagande personal).

Du måste ha administratörsbehörighet för AAD krävs i den här genomgången för att skapa användare, grupper och kopplingarna i AAD.

Programmet måste också ha registrerats med AAD, enligt beskrivningen i följande procedur.

### <a name="register-your-application-with-aad"></a>Registrera ditt program i AAD

Det här steget integreras ditt program med AAD för att acceptera inloggningar för användarkonton och gruppkonton. Om du inte är en AAD-administratör i din organisation kan du behöva [skapa en ny klient](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) att utföra följande steg.

1. Gå till den [ **Programregistreringsportalen**](https://apps.dev.microsoft.com) >  **konvergerade app** > **Lägg till en app**.
2. Ange ett namn för ditt program och klicka sedan på **skapa**. 
3. Välj nyligen registrerade programmet på sidan Mina program.
4. På registreringssidan program > **plattformar** > **lägga till plattformen**, Välj **Web API**.
5. Gå till fortfarande på registreringssidan program > **Microsoft Graph behörigheter** > **Lägg till**.
6. Lägg till följande delegerade behörigheter i Select-behörigheter och klicka sedan på **OK**:

   + **Directory.ReadWrite.All**
   + **Group.ReadWrite.All**
   + **User.ReadWrite.All**

Microsoft Graph tillhandahåller ett API som ger programmatisk åtkomst till AAD via ett REST-API. Kodexempel för den här genomgången använder behörigheter för att anropa Microsoft Graph API för att skapa grupper, användare och kopplingar. API: er används också för cache gruppidentifierare för snabbare filtrering.

## <a name="create-users-and-groups"></a>Skapa användare och grupper

Om sökningen läggs till i ett etablerat program, kanske du har befintliga användare och grupp-ID i AAD. I det här fallet kan du hoppa över följande tre steg. 

Om du inte har befintliga användare kan använda du Microsoft Graph API: er för att skapa säkerheten säkerhetsobjekt. Följande kodavsnitt visar hur du skapar identifierare som blir datavärden för fältet säkerhet i ditt Azure Search-index. I vår hypotetiska upptagande ansökan, skulle detta vara säkerhetsidentifierare för upptagande personal.

Användar- och gruppmedlemskap kan vara mycket flytande, särskilt i stora organisationer. Kod som skapar användar- och identiteter bör köra tillräckligt ofta för att hämta ändringar i medlemskap i organisationen. På samma sätt kräver ditt Azure Search index ett liknande uppdateringsschema för att återspegla den aktuella statusen för behöriga användare och resurser.

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

### <a name="step-4-cache-the-groups-identifiers"></a>Steg 4: Cachelagra grupper identifierare
Du kan också för att minska nätverkslatensen, kan du Cachelagra användargrupp kopplingarna så att när en sökbegäran grupper returneras från cache, spara en tur och RETUR till AAD. Du kan använda AAD Batch API () [https://developer.microsoft.com/graph/docs/concepts/json_batching] för att skicka en enskild Http-begäran med flera användare och skapa cachen.

Microsoft Graph är utformat för att hantera ett stort antal begäranden. Microsoft Graph misslyckas om en överväldigande antal begäranden som görs på begäran med HTTP-statuskod 429. Mer information finns i [Microsoft Graph begränsning](https://developer.microsoft.com/graph/docs/concepts/throttling).

## <a name="index-document-with-their-permitted-groups"></a>Index dokument med sina tillåtna grupper

Frågan utförs i Azure Search via ett Azure Search-index. I det här steget importerar en indexering åtgärd sökbara data till ett index, inklusive de identifierare som används som säkerhetsfilter. 

Azure Search inte autentisera användaridentiteter eller ange logik för att fastställa vilket innehåll en användare har behörighet att visa. Användningsfall för säkerhet trimning förutsätter att du anger associationen mellan ett känsliga dokument och grupp-ID som har åtkomst till dokument, intakta importeras till en sökindex. 

I det hypotetiska exemplet skulle brödtexten i PUT-förfrågan för en Azure Search-index med en av de universitet uppsats eller betyg tillsammans med grupp-ID som har behörighet att visa innehållet. 

I exemplet används i kodexemplet för den här genomgången, index-åtgärden kan se ut så här:

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

Av säkerhetsskäl trimning är värdena i fältet säkerhet i indexet statiska värden som används för att inkludera eller exkludera dokument i sökresultaten. Till exempel om gruppidentifierare för tillträde ”A11B22C33D44-E55F66G77-H88I99JKK” är alla dokument i ett Azure Search-index med identifierare i säkerheten arkiverats ingår (eller exkluderade) i sökresultaten skickas tillbaka till begäranden.

Gå igenom följande steg för att filtrera dokument som returneras i sökresultaten baserat på grupper av användare om begäran.

### <a name="step-1-retrieve-users-group-identifiers"></a>Steg 1: Hämta användarens gruppidentifierare

Om användarens grupper inte har cachelagrade eller cacheminnet har upphört att gälla, utfärda den [grupper](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/directoryobject_getmembergroups) begäran
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

Anta att du har användarens gruppmedlemskap och kan du utfärda sökbegäran filtervärden.

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

Svaret innehåller en filtrerad lista över dokument som består av att användaren har behörighet att visa. Beroende på hur du skapar sökresultatsidan, kanske du vill inkludera visuella tips för att återspegla filtrerade resultatmängden.

## <a name="conclusion"></a>Slutsats

I den här genomgången du lärt dig tekniker för att filtrera dokument i Azure Search-resultaten med AAD inloggningar trimning resultaten av dokument som inte matchar det filter som tillhandahålls på begäran.

## <a name="see-also"></a>Se även

+ [Identity-baserad åtkomstkontroll med Azure Search filter](search-security-trimming-for-azure-search.md)
+ [Filter i Azure Search](search-filters.md)
+ [Data säkerhet och åtkomstkontroll i Azure-sökningar](search-security-overview.md)
