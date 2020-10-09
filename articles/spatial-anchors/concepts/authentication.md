---
title: Autentisering och auktorisering
description: Lär dig mer om de olika sätt som en app eller tjänst kan autentisera till Azure spatiala ankare och de kontroll nivåer som du måste använda för att få åtkomst till Azure spatiala ankare.
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 10/08/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: 133b565bc54feaf49a2fec9dd0056ca8e7ef43f7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2020
ms.locfileid: "91857732"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Autentisering och auktorisering till Azure spatiala ankare

I det här avsnittet tar vi upp de olika sätt som du kan använda för att autentisera till Azure spatiala ankare från din app eller webb tjänst, och hur du kan använda Role-Based Access Control i Azure-katalogen (Azure AD) för att styra åtkomsten till dina konton för spatiala ankare.

## <a name="overview"></a>Översikt

![Diagram som visar en översikt över autentisering till Azure spatiala ankare.](./media/spatial-anchors-authentication-overview.png)

För att få åtkomst till ett specifikt konto för kontot för Azure-spatialdata måste klienterna först skaffa en åtkomsttoken från Azure Mixed Reality Security Token Service (STS). Token erhållna från STS Live i 24 timmar och innehåller information om de spatiala Ankarena för att fatta auktoriseringsbeslut på kontot och se till att endast behöriga huvud konton har åtkomst till kontot.

Åtkomsttoken kan erhållas i Exchange från antingen konto nycklar eller från Azure AD-utfärdade tokens.

Med konto nycklar kan du snabbt komma igång med tjänsten Azure spatial ankare. Innan du distribuerar ditt program till produktion, rekommenderar vi dock att du uppdaterar appen så att den använder Azure AD-baserad autentisering.

Azure AD-autentiseringstoken kan hämtas på två sätt:

- Om du skapar ett företags program och ditt företag använder Azure AD som identitets system, kan du använda en användar baserad Azure AD-autentisering i din app och bevilja åtkomst till dina spatiala ankare konton med hjälp av dina befintliga Azure AD-säkerhetsgrupper eller direkt till användare i din organisation.
- Annars rekommenderar vi att du skaffar Azure AD-tokens från en webb tjänst som stöder din app. Att använda en stöd webb tjänst är den rekommenderade autentiseringsmetoden för produktions program, eftersom den förhindrar inbäddning av autentiseringsuppgifter för åtkomst till Azure-spatialdata i klient programmet.

## <a name="account-keys"></a>Konto nycklar

Det enklaste sättet att komma igång är att använda konto nycklar för åtkomst till ditt Azure-konto med spatialdata. Du hittar dina konto nycklar på Azure Portal. Gå till ditt konto och välj fliken "nycklar".

![Skärm bild som visar sidan "nycklar" med knappen "Kopiera" för "primär nyckel" markerad.](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)

Två nycklar görs tillgängliga, som båda är giltiga för åtkomst till kontot för spatiala ankare. Vi rekommenderar att du regelbundet uppdaterar den nyckel som du använder för att få åtkomst till kontot. att ha två separata giltiga nycklar möjliggör sådana uppdateringar utan drift avbrott. du behöver bara uppdatera den primära nyckeln och den sekundära nyckeln.

SDK: n har inbyggt stöd för autentisering med konto nycklar. du behöver bara ange egenskapen AccountKey för cloudSession-objektet.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

# <a name="objc"></a>[ObjC](#tab/objc)

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

När den är färdig hanterar SDK utbyte av konto nyckeln för en åtkomsttoken och nödvändig cachelagring av token för din app.

> [!WARNING]
> Användning av konto nycklar rekommenderas för snabb registrering, men under utveckling/prototypering. Vi rekommenderar starkt att du inte levererar ditt program till produktion med hjälp av en inbäddad konto nyckel i det, och i stället använder du de användarbaserade eller tjänstebaserade Azure AD-autentiserings metoderna som anges härnäst.

## <a name="azure-ad-user-authentication"></a>Azure AD-användarautentisering

Den rekommenderade metoden är att använda en Azure AD-token för program som är riktade till Azure Active Directory användare, som du kan hämta med hjälp av [MSAL-biblioteket](../../active-directory/develop/msal-overview.md). Följ stegen i listan [Registrera en app snabb start](../../active-directory/develop/quickstart-register-app.md), som inkluderar:

1. Konfiguration i Azure Portal
    1.    Registrera ditt program i Azure AD som ett **internt program**. Som en del av registreringen måste du bestämma om ditt program ska vara flera innehavare eller inte och ange de omdirigerings-URL: er som tillåts för ditt program.
        1.  Växla till fliken **API-behörigheter**
        2.  Välj **Lägg till en behörighet**
            1.  Välj **resurs leverantör för Mixad verklighet** under **API: er min organisation använder** flik
            2.  Välj **delegerade behörigheter**
            3.  Markera kryss rutan för **mixedreality. signin** under **mixedreality**
            4.  Välj **Lägg till behörigheter**
        3.  Välj **bevilja administratörs medgivande**
    2.    Ge ditt program eller dina användare åtkomst till din resurs:
        1.    Navigera till resurser för spatial ankare i Azure Portal
        2.    Växla till fliken **åtkomst kontroll (IAM)**
        3.    Tryck på **Lägg till roll tilldelning**
            1.    [Välj en roll](#role-based-access-control)
            2.    I **Välj** -fältet anger du namnet på de användare, grupper och/eller program som du vill tilldela åtkomst till.
            3.    Tryck på **Save** (Spara).
2. I din kod:
    1.    Se till att använda **program-ID** och **omdirigerings-URI** för ditt eget Azure AD **-program som klient-ID** och **RedirectUri** -parametrar i MSAL
    2.    Ange klient information:
        1.    Om ditt program **endast stöder min organisation**ersätter du värdet med **klient-ID** eller **klient namn** (till exempel contoso.Microsoft.com)
        2.    Om ditt program har stöd **för konton i en organisations katalog**ersätter du värdet med **organisationer**
        3.    Om programmet har stöd för **alla Microsoft-konto användare ersätter du**värdet med **common**
    3.    Ange **omfånget** på din Tokenbegäran https://sts.mixedreality.azure.com//.default . Det här omfånget indikerar till Azure AD att ditt program begär en token för tjänsten Mixed Reality säkerhetstokentjänst (STS).

Med detta bör ditt program kunna hämta från MSAL till en Azure AD-token. Du kan ange att Azure AD-token som **authenticationToken** i konfigurations objekt för Cloud session.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

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

## <a name="azure-ad-service-authentication"></a>Azure AD-tjänsteautentisering

Det rekommenderade alternativet för att distribuera appar som utnyttjar Azures spatialdata till produktion är att utnyttja en server dels tjänst som kommer att betjäna autentiseringsbegäranden. Det allmänna schemat bör vara så som beskrivs i det här diagrammet:

![En översikt över autentisering till Azure spatial ankare](./media/spatial-anchors-aad-authentication.png)

Här förutsätts att appen använder sin egen mekanism (till exempel: Microsoft-konto, PlayFab, Facebook, Google ID, anpassad användar namn/lösen ord osv.) för att autentisera till backend-tjänsten. När dina användare har autentiserats för Server dels tjänsten kan tjänsten hämta en Azure AD-token, byta ut den för en åtkomsttoken för Azure spatiala ankare och återställa den till klient programmet.

Azure AD-åtkomsttoken hämtas med [MSAL-biblioteket](../../active-directory/develop/msal-overview.md). Följ stegen i listan [Registrera en app snabb start](../../active-directory/develop/quickstart-register-app.md), som inkluderar:

1.    Konfiguration i Azure Portal:
    1.    Registrera ditt program i Azure AD:
        1.    I Azure Portal navigerar du till **Azure Active Directory**och väljer **registrerade appar**
        2.    Välj **ny program registrering**
        3.    Ange namnet på programmet, Välj **webbapp/API** som program typ och ange autentiserings-URL: en för din tjänst. Tryck sedan på **skapa**.
        4.    I programmet klickar du på **Inställningar**och väljer sedan fliken **certifikat och hemligheter** . Skapa en ny klient hemlighet, Välj en varaktighet och tryck på **Lägg till**. Se till att spara det hemliga värdet eftersom du måste ta med det i din webb tjänst kod.
    2.    Ge ditt program och/eller användare åtkomst till din resurs:
        1.    Navigera till resurser för spatial ankare i Azure Portal
        2.    Växla till fliken **åtkomst kontroll (IAM)**
        3.    Tryck på **Lägg till roll tilldelning**
        1.    [Välj en roll](#role-based-access-control)
        2.    I fältet **Välj** anger du namnet på de program som du skapade och till vilka du vill tilldela åtkomst. Om du vill att appens användare ska ha olika roller mot kontot för spatiala ankare bör du registrera flera program i Azure AD och tilldela varje enskild roll. Implementera sedan din auktoriserings logik för att använda rätt roll för dina användare.
        3.    Obs! i avsnittet **Lägg till roll tilldelning** väljer du att **tilldela åtkomst** till Azure AD-användare, grupp eller tjänstens huvud namn.
    3.    Tryck på **Save** (Spara).
2.    I din kod (Obs: du kan använda tjänst exemplet som ingår i GitHub):
    1.    Se till att använda program-ID, program hemlighet och omdirigerings-URI för ditt eget Azure AD-program som klient-ID, hemligheter och RedirectUri-parametrar i MSAL
    2.    Ange klient-ID: t till ditt eget Azure-tillägg i parametern Authority i MSAL.
    3.    Ange **omfånget** på din Tokenbegäran https://sts.mixedreality.azure.com//.default

Med detta kan Server dels tjänsten hämta en Azure AD-token. Den kan sedan utväxla den för en MR-token som den kommer tillbaka till klienten. Att använda en Azure AD-token för att hämta en MR-token görs via ett REST-anrop. Här är ett exempel på ett anrop:

```
GET https://sts.mixedreality.azure.com/Accounts/35d830cb-f062-4062-9792-d6316039df56/token HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1Ni<truncated>FL8Hq5aaOqZQnJr1koaQ
Host: sts.mixedreality.azure.com
Connection: Keep-Alive

HTTP/1.1 200 OK
Date: Sun, 24 Feb 2019 08:00:00 GMT
Content-Type: application/json; charset=utf-8
Content-Length: 1153
Accept: application/json
MS-CV: 05JLqWeKFkWpbdY944yl7A.0
{"AccessToken":"eyJhbGciOiJSUzI1NiIsImtpZCI6IjI2MzYyMTk5ZTI2NjQxOGU4ZjE3MThlM2IyMThjZTIxIiwidHlwIjoiSldUIn0.eyJqdGkiOiJmMGFiNWIyMy0wMmUxLTQ1MTQtOWEzNC0xNzkzMTA1NTc4NzAiLCJjYWkiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJ0aWQiOiIwMDAwMDAwMC0wMDAwLTAwMDAtMDAwMC0wMDAwMDAwMDAwMDAiLCJhaWQiOiIzNWQ4MzBjYi1mMDYyLTQwNjItOTc5Mi1kNjMxNjAzOWRmNTYiLCJhYW8iOi0xLCJhcHIiOiJlYXN0dXMyIiwicmlkIjoiL3N1YnNjcmlwdGlvbnMvNzIzOTdlN2EtNzA4NC00ODJhLTg3MzktNjM5Y2RmNTMxNTI0L3Jlc291cmNlR3JvdXBzL3NhbXBsZV9yZXNvdXJjZV9ncm91cC9wcm92aWRlcnMvTWljcm9zb2Z0Lk1peGVkUmVhbGl0eS9TcGF0aWFsQW5jaG9yc0FjY291bnRzL2RlbW9fYWNjb3VudCIsIm5iZiI6MTU0NDU0NzkwMywiZXhwIjoxNTQ0NjM0MzAzLCJpYXQiOjE1NDQ1NDc5MDMsImlzcyI6Imh0dHBzOi8vbXJjLWF1dGgtcHJvZC50cmFmZmljbWFuYWdlci5uZXQvIiwiYXVkIjoiaHR0cHM6Ly9tcmMtYW5jaG9yLXByb2QudHJhZmZpY21hbmFnZXIubmV0LyJ9.BFdyCX9UJj0i4W3OudmNUiuaGgVrlPasNM-5VqXdNAExD8acFJnHdvSf6uLiVvPiQwY1atYyPbOnLYhEbIcxNX-YAfZ-xyxCKYb3g_dbxU2w8nX3zDz_X3XqLL8Uha-rkapKbnNgxq4GjM-EBMCill2Svluf9crDmO-SmJbxqIaWzLmlUufQMWg_r8JG7RLseK6ntUDRyDgkF4ex515l2RWqQx7cw874raKgUO4qlx0cpBAB8cRtGHC-3fA7rZPM7UQQpm-BC3suXqRgROTzrKqfn_g-qTW4jAKBIXYG7iDefV2rGMRgem06YH_bDnpkgUa1UgJRRTckkBuLkO2FvA"}
```

Där Authorization-huvudet är formaterat på följande sätt: `Bearer <Azure_AD_token>`

Och svaret innehåller MR-token som oformaterad text.

Den MR-token returneras sedan till klienten. Klient programmet kan sedan ange den som åtkomsttoken i Cloud session config.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

# <a name="objc"></a>[ObjC](#tab/objc)

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

För att hjälpa till att kontrol lera åtkomst nivån för program, tjänster eller Azure AD-användare av tjänsten, har följande roller skapats så att du kan tilldela vid behov till dina konton för moln lån:

- **Konto ägare för spatiala ankare**: program eller användare som har den här rollen kan skapa spatiala ankare, fråga efter dem och ta bort dem. När du autentiserar dig på ditt konto med hjälp av konto nycklar tilldelas **kontot ägare** till det autentiserade huvudobjektet.
- **Konto deltagare för spatiala ankare**: program eller användare som har den här rollen kan skapa spatiala ankare, fråga efter dem, men inte ta bort dem.
- **Konto läsare för avstånds ankare**: program eller användare som har den här rollen kan bara fråga efter avstånds ankare, men de kan inte skapa nya, ta bort befintliga eller uppdatera metadata på spatiala ankare. Detta används vanligt vis för program där vissa användare kan granska miljön, medan andra bara kan återkalla ankare som tidigare placerats i miljön.

## <a name="next-steps"></a>Nästa steg

Skapa din första app med de spatiala Ankarena i Azure.

> [!div class="nextstepaction"]
> [Unity (HoloLens)](../quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](../quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](../quickstarts/get-started-unity-android.md)

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
