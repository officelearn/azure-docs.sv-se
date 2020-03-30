---
title: Autentisering och auktorisering
description: Lär dig mer om de olika sätt som en app eller tjänst kan autentisera till Azure Spatial Anchors och de kontrollnivåer som du har för att utfärda åtkomst till Azure Spatial Anchors.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: df27a77c202951a6c789703f12712e75bd8b5906
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77657001"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Autentisering och auktorisering till Azure Spatial Anchors

I det här avsnittet beskriver vi de olika sätt du kan autentisera till Azure Spatial Anchors från din app eller webbtjänst och hur du kan använda rollbaserad åtkomstkontroll i Azure Directory (Azure AD) för att styra åtkomsten till dina spatialankarekonton.

## <a name="overview"></a>Översikt

![En översikt över autentisering till Azure Spatial Anchors](./media/spatial-anchors-authentication-overview.png)

För att komma åt ett visst Azure Spatial Anchors-konto måste klienter först hämta en åtkomsttoken från Azure Mixed Reality Security Token Service (STS). Tokens som erhållits från STS live i 24 timmar och innehåller information för Spatial Anchors-tjänsterna för att fatta auktoriseringsbeslut på kontot och se till att endast behöriga huvudmän kan komma åt det kontot.

Åtkomsttoken kan erhållas i utbyte från antingen kontonycklar eller från Azure AD-utfärdade token.

Med kontonycklar kan du snabbt komma igång med azure spatial anchors-tjänsten. Innan du distribuerar programmet till produktion rekommenderas dock att du uppdaterar din app för att använda Azure AD-baserad autentisering.

Azure AD-autentiseringstoken kan erhållas på två sätt:

- Om du skapar ett företagsprogram och ditt företag använder Azure AD som identitetssystem kan du använda användarbaserad Azure AD-autentisering i din app och bevilja åtkomst till dina rumsliga fästpunkter med hjälp av dina befintliga Azure AD-säkerhetsgrupper, eller direkt till användare i organisationen.
- Annars rekommenderas att du hämtar Azure AD-token från en webbtjänst som stöder din app. Att använda en stödjande webbtjänst är den rekommenderade metoden för autentisering för produktionsprogram, eftersom det undviker att bädda in autentiseringsuppgifterna för åtkomst till Azure Spatial Anchors i klientprogrammet.

## <a name="account-keys"></a>Kontonycklar

Att använda kontonycklar för åtkomst till ditt Azure Spatial Anchors-konto är det enklaste sättet att komma igång. Du hittar dina kontonycklar på Azure-portalen. Navigera till ditt konto och välj fliken "Nycklar".

![En översikt över autentisering till Azure Spatial Anchors](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


Två nycklar görs tillgängliga, som båda samtidigt är giltiga för åtkomst till kontot Spatial Anchors. Vi rekommenderar att du regelbundet uppdaterar nyckeln du använder för att komma åt kontot. med två separata giltiga nycklar möjliggör sådana uppdateringar utan driftstopp. Du behöver bara uppdatera alternativt primärnyckeln och den sekundära nyckeln.

SDK har inbyggt stöd för autentisering med kontonycklar. Du behöver bara ange egenskapen AccountKey på ditt cloudSession-objekt.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objc"></a>[ObjC (olika)](#tab/objc)

```objc
_cloudSession.configuration.accountKey = @"MyAccountKey";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accountKey = "MyAccountKey"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccountKey("MyAccountKey");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccountKey(R"(MyAccountKey)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccountKey(LR"(MyAccountKey)");
```

---

När det är gjort hanterar SDK utbytet av kontonyckeln för en åtkomsttoken och nödvändig cachelagring av token för din app.

> [!WARNING]
> Användning av kontonycklar rekommenderas för snabb ombordstigning, men endast under utveckling/prototyper. Vi rekommenderar starkt att du inte levererar ditt program till produktion med hjälp av en inbäddad kontonyckel i det, och att i stället använda de användarbaserade eller tjänstbaserade Azure AD-autentiseringsmetoderna som anges härnäst.

## <a name="azure-ad-user-authentication"></a>Azure AD-användarautentisering

För program som är inriktade på Azure Active Directory-användare är den rekommenderade metoden att använda en Azure AD-token för användaren, som du kan hämta med hjälp av [MSAL-biblioteket](../../active-directory/develop/msal-overview.md). Du bör följa stegen i listan [en app snabbstart](../../active-directory/develop/quickstart-register-app.md), som omfattar:

1. Konfiguration i Azure-portalen
    1.  Registrera ditt program i Azure AD som **inbyggt program**. Som en del av registreringen måste du avgöra om ditt program ska vara flera innehavare eller inte och ange de omdirigeringsadresser som tillåts för ditt program.
        1.  Växla till fliken **API-behörigheter**
        2.  Välj **Lägg till en behörighet**
            1.  Välj Resursleverantör för **mixad verklighet** under **API:er som min organisation använder på** fliken
            2.  Välj **delegerade behörigheter**
            3.  Markera kryssrutan för **mixedreality.signin** under **mixedreality**
            4.  Välj **Lägg till behörigheter**
        3.  Välj **Bevilja administratörsmedgivande**
    2.  Ge ditt program eller användare åtkomst till din resurs:
        1.  Navigera till din spatialankarresurs i Azure-portalen
        2.  Växla till fliken **Åtkomstkontroll (IAM)**
        3.  Hit **Lägg till rolltilldelning**
            1.  [Välj en roll](#role-based-access-control)
            2.  I fältet **Välj** anger du namnet på de användare, grupper och/eller program som du vill tilldela åtkomst till.
            3.  Tryck på **Save** (Spara).
2. I din kod:
    1.  Se till att använda **program-ID** och **omdirigera Uri** av ditt eget Azure AD-program som **klient-ID** och **RedirectUri** parametrar i ADAL
    2.  Ange klientinformation:
        1.  Om ditt program endast stöder **Min organisation**ersätter du det här värdet med **ditt klient-ID-** eller **klientnamn** (till exempel contoso.microsoft.com)
        2.  Om programmet stöder **konton i en organisationskatalog** **ersätter** du det här värdet med organisationer
        3.  Om programmet stöder **alla Microsoft-kontoanvändare**ersätter du det här värdet med **Common**
    3.  På din tokenbegäran ställer duhttps://sts.mixedreality.azure.comin **resursen** på " ". Den här "resursen" anger för Azure AD att ditt program begär en token för Azure Spatial Anchors-tjänsten.

Med det bör ditt program kunna hämta en Azure AD-token från MSAL. Du kan ange att Azure AD-token som **authenticationToken** på din molnsession config objekt.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objc"></a>[ObjC (olika)](#tab/objc)

```objc
_cloudSession.configuration.authenticationToken = @"MyAuthenticationToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAuthenticationToken("MyAuthenticationToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AuthenticationToken(R"(MyAuthenticationToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AuthenticationToken(LR"(MyAuthenticationToken)");
```

---

## <a name="azure-ad-service-authentication"></a>Autentisering av Azure AD-tjänst

Det rekommenderade alternativet för att distribuera appar som utnyttjar Azure Spatial Anchors till produktion är att utnyttja en serverdtjänst som kommer att förmedla autentiseringsbegäranden. Det allmänna schemat bör beskrivas i detta diagram:

![En översikt över autentisering till Azure Spatial Anchors](./media/spatial-anchors-aad-authentication.png)

Här antas det att din app använder sin egen mekanism (till exempel: Microsoft-konto, PlayFab, Facebook, Google-ID, anpassat användarnamn/lösenord osv.) för att autentisera till sin backend-tjänst. När användarna har autentiserats till din backend-tjänst kan den tjänsten hämta en Azure AD-token, byta ut den mot en åtkomsttoken för Azure Spatial Anchors och returnera den tillbaka till klientprogrammet.

Azure AD-åtkomsttoken hämtas med [MSAL-biblioteket](../../active-directory/develop/msal-overview.md). Du bör följa stegen i listan [en app snabbstart](../../active-directory/develop/quickstart-register-app.md), som omfattar:

1.  Konfiguration i Azure-portalen:
    1.  Registrera ditt program i Azure AD:
        1.  I Azure-portalen navigerar du till **Azure Active Directory**och väljer **appregistreringar**
        2.  Välj **ny programregistrering**
        3.  Ange namnet på ditt program, välj **webbapp /API** som programtyp och ange auth-URL:en för din tjänst. Tryck sedan på **Skapa**.
        4.  På det programmet trycker du på **Inställningar**och väljer sedan fliken **Nycklar.** Ange namnet på nyckeln, välj en varaktighet och tryck på **Spara**. Se till att spara nyckelvärdet som visas vid den tidpunkten, eftersom du måste inkludera det i webbtjänstens kod.
    2.  Ge ditt program och/eller användare åtkomst till din resurs:
        1.  Navigera till din spatialankarresurs i Azure-portalen
        2.  Växla till fliken **Åtkomstkontroll (IAM)**
        3.  Hit **Lägg till rolltilldelning**
        1.  [Välj en roll](#role-based-access-control)
        2.  I fältet **Välj** anger du namnet på de program som du har skapat och som du vill tilldela åtkomst till. Om du vill att appens användare ska ha olika roller mot kontot Spatial Anchors bör du registrera flera program i Azure AD och tilldela var och en en separat roll. Implementera sedan din auktoriseringslogik för att använda rätt roll för användarna.
    3.  Tryck på **Save** (Spara).
2.  I koden (obs: du kan använda tjänstexemplet som ingår i GitHub):
    1.  Se till att använda program-ID, programhemlighet och omdirigera Uri av ditt eget Azure AD-program som klient-ID,hemliga och RedirectUri-parametrar i ADAL
    2.  Ange klient-ID till ditt eget AAAzure ADD-klient-ID i auktoritetsparametern i ADAL
    3.  På din tokenbegäran ställer duhttps://sts.mixedreality.azure.comin **resursen** på " "

Med det kan din backend-tjänst hämta en Azure AD-token. Den kan sedan byta ut den mot en MR-token som den kommer att återgå till klienten. Använda en Azure AD-token för att hämta en MR-token görs via ett REST-anrop. Här är ett exempel samtal:

```
GET https://mrc-auth-prod.trafficmanager.net/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: mrc-auth-prod.trafficmanager.net
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

Om auktoriseringshuvudet formateras på följande sätt:`Bearer <accoundId>:<accountKey>`

Och svaret innehåller MR-token i oformaterad text.

Den MR-token returneras sedan till klienten. Klientappen kan sedan ange den som åtkomsttoken i molnsessionskonfigurationen.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objc"></a>[ObjC (olika)](#tab/objc)

```objc
_cloudSession.configuration.accessToken = @"MyAccessToken";
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
_cloudSession!.configuration.accessToken = "MyAccessToken"
```

# <a name="java"></a>[Java](#tab/java)

```java
mCloudSession.getConfiguration().setAccessToken("MyAccessToken");
```

# <a name="c-ndk"></a>[C++ NDK](#tab/cpp)

```cpp
auto configuration = cloudSession_->Configuration();
configuration->AccessToken(R"(MyAccessToken)");
```

# <a name="c-winrt"></a>[C++ WinRT](#tab/cppwinrt)

```cpp
auto configuration = m_cloudSession.Configuration();
configuration.AccessToken(LR"(MyAccessToken)");
```

---

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

För att kontrollera åtkomstnivån för program, tjänster eller Azure AD-användare av din tjänst har följande roller skapats för att du ska kunna tilldela efter behov mot dina Azure Spatial Anchors-konton:

- **Rumslig ankare kontoägare:** program eller användare som har den här rollen kan skapa rumsliga ankare, fråga efter dem och ta bort dem. När du autentiserar till ditt konto med hjälp av kontonycklar tilldelas rollen Ägare för **spatialankarkonto** till det autentiserade huvudmannen.
- **Spatial Anchors Account Contributor**: program eller användare som har den här rollen kan skapa rumsliga ankare, fråga efter dem, men kan inte ta bort dem.
- **Spatial Anchors Account Reader:** program eller användare som har den här rollen kan bara fråga efter rumsliga ankare, men kan inte skapa nya, ta bort befintliga eller uppdatera metadata på rumsliga fästpunkter. Detta används vanligtvis för program där vissa användare curate miljön, medan andra bara kan återkalla ankare som tidigare placerats i den miljön.

## <a name="next-steps"></a>Nästa steg

Skapa din första app med Azure Spatial Anchors.

> [!div class="nextstepaction"]
> [Enhet (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Enhet (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Enhet (Android)](../quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](../quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](../quickstarts/get-started-xamarin-ios.md)
