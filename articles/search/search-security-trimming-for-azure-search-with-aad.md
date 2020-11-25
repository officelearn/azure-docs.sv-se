---
title: Säkerhets filter för att trimma resultat med Active Directory
titleSuffix: Azure Cognitive Search
description: Säkerhets behörigheter på dokument nivå för Azure Kognitiv sökning Sök resultat, med säkerhets filter och Azure Active Directory-identiteter (AAD).
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/04/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 87337cf22bdb388c5873a2811bb9913c3e7f4d4e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994969"
---
# <a name="security-filters-for-trimming-azure-cognitive-search-results-using-active-directory-identities"></a>Säkerhets filter för att trimma Azure-Kognitiv sökning resultat med hjälp av Active Directory identiteter

Den här artikeln visar hur du använder säkerhets identiteter för Azure Active Directory (AAD) tillsammans med filter i Azure Kognitiv sökning för att trimma Sök resultat baserat på användar grupp medlemskap.

Den här artikeln beskriver följande uppgifter:
> [!div class="checklist"]
> - Skapa AAD-grupper och användare
> - Koppla användaren till den grupp som du har skapat
> - Cachelagra de nya grupperna
> - Indexera dokument med tillhör ande grupper
> - Utfärda en Sök förfrågan med ett filter för grupp identifierare
> 
> [!NOTE]
> Exempel kod avsnitt i den här artikeln är skrivna i C#. Du hittar den fullständiga källkoden [på GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started). 

## <a name="prerequisites"></a>Förutsättningar

Ditt index i Azure Kognitiv sökning måste ha ett [säkerhets fält](search-security-trimming-for-azure-search.md) för att lagra listan över grupp identiteter som har Läs behörighet till dokumentet. Det här användnings fallet förutsätter en en-till-en-korrespondens mellan ett skydds Bart objekt (till exempel en enskild persons skolprogram) och ett säkerhets fält som anger vem som har åtkomst till det objektet (anställnings personal).

Du måste ha AAD-administratörs behörighet, som krävs i den här genom gången för att skapa användare, grupper och associationer i AAD.

Ditt program måste också vara registrerat i AAD, enligt beskrivningen i följande procedur.

### <a name="register-your-application-with-aad"></a>Registrera ditt program med AAD

I det här steget integreras ditt program med AAD i syfte att godkänna inloggningar av användar-och grupp konton. Om du inte är AAD-administratör i din organisation kan du behöva [skapa en ny klient](../active-directory/develop/quickstart-create-new-tenant.md) för att utföra följande steg.

1. Gå till [**program registrerings portalen**](https://apps.dev.microsoft.com)  >   **konvergerad app**  >  **Lägg till en app**.
2. Ange ett namn för ditt program och klicka sedan på **skapa**. 
3. Välj ditt nyligen registrerade program på sidan mina program.
4. På sidan program registrering > **plattformar**  >  **Lägg till plattform** väljer du **webb-API**.
5. Fortfarande på sidan program registrering går du till > **Microsoft Graph behörigheter**  >  **Lägg till**.
6. I Välj behörigheter, lägger du till följande delegerade behörigheter och klickar sedan på **OK**:

   + **Directory. ReadWrite. all**
   + **Group.ReadWrite.All**
   + **User. ReadWrite. all**

Microsoft Graph tillhandahåller ett API som ger program mässig åtkomst till AAD via en REST API. Kod exemplet för den här genom gången använder behörigheterna för att anropa Microsoft Graph-API: et för att skapa grupper, användare och associationer. API: erna används också för att cachelagra grupp identifierare för snabbare filtrering.

## <a name="create-users-and-groups"></a>Skapa användare och grupper

Om du lägger till en sökning i ett etablerat program kan du ha befintliga användar-och grupp identifierare i AAD. I så fall kan du hoppa över nästa tre steg. 

Men om du inte har befintliga användare kan du använda Microsoft Graph-API: er för att skapa säkerhets objekt. Följande kodfragment visar hur du genererar identifierare, som blir datavärdena för säkerhets fältet i Azure Kognitiv sökning indexet. I vårt hypotetiska universitets ansöknings program är detta säkerhets identifierarna för anställda.

Användar-och grupp medlemskap kan vara mycket flytande, särskilt i stora organisationer. Kod som skapar användar-och grupp identiteter bör köras tillräckligt ofta för att hämta ändringar i organisationens medlemskap. På samma sätt kräver ditt Azure Kognitiv sökning-index ett liknande uppdaterings schema för att återspegla den aktuella statusen för tillåtna användare och resurser.

### <a name="step-1-create-aad-group"></a>Steg 1: skapa [AAD-grupp](/graph/api/group-post-groups?view=graph-rest-1.0) 
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
   
### <a name="step-2-create-aad-user"></a>Steg 2: skapa [AAD-användare](/graph/api/user-post-users?view=graph-rest-1.0)
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

### <a name="step-3-associate-user-and-group"></a>Steg 3: koppla användare och grupp
```csharp
await graph.Groups[newGroup.Id].Members.References.Request().AddAsync(newUser);
```

### <a name="step-4-cache-the-groups-identifiers"></a>Steg 4: cachelagra grupp-ID: n
Om du vill minska nätverks fördröjningen kan du cachelagra användar grupps kopplingarna så att när en sökbegäran utfärdas, returneras grupper från cachen och sparar en tur och retur till AAD. Du kan använda [AAD batch API](/graph/json-batching) för att skicka en enda http-begäran med flera användare och bygga cacheminnet.

Microsoft Graph har utformats för att hantera en stor mängd begär Anden. Om det uppstår ett överbelastat antal begär Anden, Miss lyckas begäran med HTTP-statuskod 429 i Microsoft Graph. Mer information finns i [Microsoft Graph begränsning](/graph/throttling).

## <a name="index-document-with-their-permitted-groups"></a>Indexera dokument med deras tillåtna grupper

Fråga-åtgärder i Azure-Kognitiv sökning körs över ett Azure Kognitiv sökning-index. I det här steget importerar en indexerings åtgärd sökbara data till ett index, inklusive de identifierare som används som säkerhets filter. 

Azure Kognitiv sökning autentiserar inte användar identiteter eller ger logik för att fastställa vilket innehåll en användare har behörighet att visa. Användnings fallet för säkerhets trimning förutsätter att du anger associationen mellan ett känsligt dokument och grupp-ID: t som har åtkomst till dokumentet, som importeras intakt i ett sökindex. 

I det hypotetiska exemplet skulle bröd texten i begäran i ett Azure Kognitiv sökning-index innehålla en sökandes skol uppsats eller avskrift tillsammans med grupp-ID: t som har behörighet att visa innehållet. 

I det allmänna exemplet som används i kod exemplet för den här genom gången kan index åtgärden se ut så här:

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

## <a name="issue-a-search-request"></a>Utfärda en Sök förfrågan

För säkerhets putsning är värdena i säkerhets fältet i indexet statiska värden som används för att inkludera eller exkludera dokument i Sök resultaten. Om grupp identifieraren för-inhämtare till exempel är "A11B22C33D44-E55F66G77-H88I99JKK" inkluderas alla dokument i ett Azure Kognitiv sökning-index som har identifieraren i den arkiverade säkerhets kopian (eller exkluderas) i Sök resultaten som skickas tillbaka till begär Anden.

Granska följande steg för att filtrera dokument som returneras i Sök resultat baserat på grupper av användaren som utfärdade begäran.

### <a name="step-1-retrieve-users-group-identifiers"></a>Steg 1: Hämta användarens grupp identifierare

Om användarens grupper inte redan har cachelagrats, eller om cachen har upphört att gälla, utfärdar du [grupp](/graph/api/directoryobject-getmembergroups?view=graph-rest-1.0) förfrågan
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

### <a name="step-2-compose-the-search-request"></a>Steg 2: Skriv Sök förfrågan

Förutsatt att du har användarens grupp medlemskap kan du utfärda Sök förfrågan med lämpliga filter värden.

```csharp
string filter = String.Format("groupIds/any(p:search.in(p, '{0}'))", string.Join(",", groups.Select(g => g.ToString())));
SearchParameters parameters = new SearchParameters()
             {
                 Filter = filter,
                 Select = new[] { "application essays" }
             };

DocumentSearchResult<SecuredFiles> results = _indexClient.Documents.Search<SecuredFiles>("*", parameters);
```
### <a name="step-3-handle-the-results"></a>Steg 3: hantera resultaten

Svaret innehåller en filtrerad lista med dokument, som består av de som användaren har behörighet att visa. Beroende på hur du skapar Sök Resultat sidan kanske du vill inkludera visuella tips som visar den filtrerade resultat uppsättningen.

## <a name="conclusion"></a>Slutsats

I den här genom gången har du lärt dig hur du använder AAD-inloggningar för att filtrera dokument i Azure Kognitiv sökning resultat, vilket innebär att dokument som inte matchar det filter som anges på begäran rensas.

## <a name="see-also"></a>Se även

+ [Identitets-baserad åtkomst kontroll med Azure Kognitiv sökning filter](search-security-trimming-for-azure-search.md)
+ [Filter i Azure Kognitiv sökning](search-filters.md)
+ [Data säkerhet och åtkomst kontroll i Azure Kognitiv sökning åtgärder](search-security-overview.md)