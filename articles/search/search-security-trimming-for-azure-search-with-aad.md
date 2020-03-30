---
title: Säkerhetsfilter för att trimma resultat med Active Directory
titleSuffix: Azure Cognitive Search
description: Åtkomstkontroll för Azure Cognitive Search-innehåll med hjälp av säkerhetsfilter och Azure Active Directory -identiteter (AAD).
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 01280b6ee9dda15af3c0fc707a385501580c624c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72794309"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>Säkerhetsfilter för trimning av Azure Cognitive Search-resultat med Active Directory-identiteter

Den här artikeln visar hur du använder Azure Active Directory (AAD) säkerhetsidentiteter tillsammans med filter i Azure Cognitive Search för att trimma sökresultat baserat på medlemskap i användargrupper.

Den här artikeln beskriver följande uppgifter:
> [!div class="checklist"]
> - Skapa AAD-grupper och användare
> - Associera användaren med den grupp som du har skapat
> - Cachelagra de nya grupperna
> - Indexera dokument med associerade grupper
> - Utfärda en sökbegäran med gruppidentifierarfilter
> 
> [!NOTE]
> Exempelkodavsnitt i den här artikeln är skrivna i C#. Du hittar den fullständiga källkoden [på GitHub](https://aka.ms/search-dotnet-howto). 

## <a name="prerequisites"></a>Krav

Ditt index i Azure Cognitive Search måste ha ett [säkerhetsfält](search-security-trimming-for-azure-search.md) för att lagra listan över gruppidentiteter som har läsbehörighet till dokumentet. Detta användningsfall förutsätter en 1:1-korrespondens mellan en säkerhetsåtgärd (till exempel en persons collegeansökan) och ett säkerhetsfält som anger vem som har åtkomst till den artikeln (antagningspersonal).

Du måste ha AAD-administratörsbehörigheter som krävs i den här genomgången för att skapa användare, grupper och associationer i AAD.

Din ansökan måste också vara registrerad hos AAD, enligt beskrivningen i följande förfarande.

### <a name="register-your-application-with-aad"></a>Registrera din ansökan hos AAD

Det här steget integrerar ditt program med AAD i syfte att acceptera inloggningar av användar- och gruppkonton. Om du inte är AAD-administratör i organisationen kan du behöva [skapa en ny klient för](https://docs.microsoft.com/azure/active-directory/develop/active-directory-howto-tenant) att kunna utföra följande steg.

1. Gå till**appen Konvergerad programregistreringsportal** >  [**Application Registration Portal**](https://apps.dev.microsoft.com) >  Lägg till en**app**.
2. Ange ett namn på programmet och klicka sedan på **Skapa**. 
3. Välj ditt nyregistrerade program på sidan Mina program.
4. Välj **Webb-API**på sidan för programregistrering > **plattformar** > **lägg till plattform**.
5. På sidan för programregistrering går du till > **Microsoft Graph-behörigheter** > **Lägg till**.
6. Lägg till följande delegerade behörigheter i Välj behörigheter och klicka sedan på **OK:**

   + **Directory.ReadWrite.All**
   + **Group.ReadWrite.All**
   + **User.ReadWrite.All**

Microsoft Graph tillhandahåller ett API som tillåter programmatisk åtkomst till AAD via ett REST API. Kodexemplet för den här genomgången använder behörigheterna för att anropa Microsoft Graph-API:et för att skapa grupper, användare och associationer. API:erna används också för att cachelagra gruppidentifierare för snabbare filtrering.

## <a name="create-users-and-groups"></a>Skapa användare och grupper

Om du lägger till sökning i ett etablerat program kan du ha befintliga användar- och gruppidentifierare i AAD. I det här fallet kan du hoppa över de följande tre stegen. 

Om du inte har befintliga användare kan du dock använda Microsoft Graph API:er för att skapa säkerhetsobjekten. Följande kodavsnitt visar hur du genererar identifierare, som blir datavärden för säkerhetsfältet i ditt Azure Cognitive Search-index. I vår hypotetiska college antagning ansökan, skulle detta vara säkerhetsidentifierare för antagning personal.

Användar- och gruppmedlemskap kan vara mycket flytande, särskilt i stora organisationer. Kod som skapar användar- och gruppidentiteter bör köras tillräckligt ofta för att hämta ändringar i organisationens medlemskap. På samma sätt kräver ditt Azure Cognitive Search-index ett liknande uppdateringsschema för att återspegla den aktuella statusen för tillåtna användare och resurser.

### <a name="step-1-create-aad-group"></a>Steg 1: Skapa [AAD-grupp](https://docs.microsoft.com/graph/api/group-post-groups?view=graph-rest-1.0) 
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
   
### <a name="step-2-create-aad-user"></a>Steg 2: Skapa [AAD-användare](https://docs.microsoft.com/graph/api/user-post-users?view=graph-rest-1.0)
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

### <a name="step-3-associate-user-and-group"></a>Steg 3: Associera användare och grupp
```csharp
await graph.Groups[newGroup.Id].Members.References.Request().AddAsync(newUser);
```

### <a name="step-4-cache-the-groups-identifiers"></a>Steg 4: Cachelagra gruppidentifierare
Om du vill minska nätverksfördröjningen kan du cachelagra användargruppsassociationerna så att grupper returneras från cacheminnet när en sökbegäran utfärdas och sparar en rundtur i AAD. Du kan använda [AAD Batch API](https://developer.microsoft.com/graph/docs/concepts/json_batching) för att skicka en enda Http-begäran med flera användare och skapa cachen.

Microsoft Graph är utformat för att hantera en stor mängd begäranden. Om ett överväldigande antal begäranden inträffar misslyckas begäran med HTTP-statuskod 429. Mer information finns i [Microsoft Graph begränsning](https://developer.microsoft.com/graph/docs/concepts/throttling).

## <a name="index-document-with-their-permitted-groups"></a>Indexera dokument med tillåtna grupper

Frågeåtgärder i Azure Cognitive Search körs över ett Azure Cognitive Search-index. I det här steget importerar en indexeringsåtgärd sökbara data till ett index, inklusive identifierare som används som säkerhetsfilter. 

Azure Cognitive Search autentiserar inte användaridentiteter eller ger logik för att fastställa vilket innehåll en användare har behörighet att visa. Användningsfallet för säkerhetstrimning förutsätter att du anger kopplingen mellan ett känsligt dokument och gruppidentifieraren som har åtkomst till dokumentet, som importeras intakt till ett sökindex. 

I det hypotetiska exemplet skulle brödtexten i PUT-begäran på ett Azure Cognitive Search-index innehålla en sökandes college-uppsats eller avskrift tillsammans med gruppidentifieraren som har behörighet att visa innehållet. 

I det allmänna exemplet som används i kodexemplet för den här genomgången kan indexåtgärden se ut på följande sätt:

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

För säkerhetstrimning är värdena i säkerhetsfältet i indexet statiska värden som används för att inkludera eller utesluta dokument i sökresultaten. Om gruppidentifieraren för antagning till exempel är "A11B22C33D44-E55F66G77-H88I99JKK", inkluderas alla dokument i ett Azure Cognitive Search-index som har den identifieraren i den arkiverade säkerheten (eller utesluts) i sökresultaten som skickas tillbaka till beställaren.

Om du vill filtrera dokument som returneras i sökresultat baserat på grupper av användaren som utfärdar begäran läser du följande steg.

### <a name="step-1-retrieve-users-group-identifiers"></a>Steg 1: Hämta användarens gruppidentifierare

Om användarens grupper inte redan har cachelagrats eller om cacheminnet har upphört att gälla utfärdar du [gruppbegäran](https://docs.microsoft.com/graph/api/directoryobject-getmembergroups?view=graph-rest-1.0)
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

### <a name="step-2-compose-the-search-request"></a>Steg 2: Skriv sökbegäran

Förutsatt att du har användarens grupper medlemskap, kan du utfärda sökbegäran med lämpliga filtervärden.

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

Svaret innehåller en filtrerad lista över dokument som består av dokument som användaren har behörighet att visa. Beroende på hur du skapar sökresultatsidan kanske du vill inkludera visuella tips för att återspegla den filtrerade resultatuppsättningen.

## <a name="conclusion"></a>Slutsats

I den här genomgången har du lärt dig tekniker för att använda AAD-inloggningar för att filtrera dokument i Azure Cognitive Search-resultat, vilket trimmar resultaten av dokument som inte matchar filtret som finns på begäran.

## <a name="see-also"></a>Se även

+ [Identitetsbaserad åtkomstkontroll med hjälp av Azure Cognitive Search-filter](search-security-trimming-for-azure-search.md)
+ [Filter i Azure Cognitive Search](search-filters.md)
+ [Datasäkerhet och åtkomstkontroll i Azure Cognitive Search-åtgärder](search-security-overview.md)
