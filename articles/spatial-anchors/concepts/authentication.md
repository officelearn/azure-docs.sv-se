---
title: Autentisering och auktorisering till Azure Spatial fästpunkter | Microsoft Docs
description: Läs mer om de olika sätt en app eller tjänst kan autentisera till Azure Spatial ankare och nivåer av kontroll som du behöver förhindrar åtkomst till Azure Spatial fästpunkter.
author: julianparismorgan
manager: vriveras
services: azure-spatial-anchors
ms.author: pmorgan
ms.date: 05/28/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 2b3f4cf4099459b655fc0e370935ddc8079de810
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67073945"
---
# <a name="authentication-and-authorization-to-azure-spatial-anchors"></a>Autentisering och auktorisering till Azure Spatial fästpunkter

I det här avsnittet täcker vi de olika sätt som du kan autentisera till Azure Spatial fästpunkter från din app eller en webbtjänst och på sätt som du kan använda rollbaserad åtkomstkontroll i Azure Directory (Azure AD) att styra åtkomsten till dina Spatial ankare-konton.  

## <a name="overview"></a>Översikt

![En översikt över autentisering till Azure Spatial fästpunkter](./media/spatial-anchors-authentication-overview.png)

Klienter måste först hämta en åtkomsttoken från Azure Mixad verklighet Security säkerhetstokentjänst (STS) för att komma åt en viss Spatial fästpunkter för Azure-konto. Token hämtas från STS live i 24 timmar och innehåller information för Spatial ankare tjänster att fatta auktoriseringsbeslut för kontot och se till att endast auktoriserade säkerhetsobjekt kan komma åt det kontot. 

Åtkomsttoken kan hämtas i exchange från antingen nycklar eller från Azure AD-utfärdade token. 

Nycklar kan du snabbt komma igång med tjänsten Azure Spatial ankare; men innan du distribuerar ditt program till produktion är det bäst att du uppdaterar din app för att använda Azure AD-baserad autentisering. 

Azure AD-autentiseringstoken kan hämtas på två sätt:

- Om du skapar ett företagsprogram och ditt företag använder Azure AD som dess ID-system, kan du använda användarbaserade Azure AD-autentisering i din app och bevilja åtkomst till dina spatial ankare konton med hjälp av din befintliga Azure AD-säkerhetsgrupper, eller direkt till användare i din organisation. 
- I annat fall rekommenderar vi att du hämtar Azure AD-token från en webbtjänst som stöder din app. Med hjälp av en stödjande webbtjänst är den rekommenderade metoden för autentisering för program i produktion, eftersom det förhindrar att bädda in autentiseringsuppgifter för åtkomst till Azure Spatial ankare i klientprogrammet. 

## <a name="account-keys"></a>Kontonycklar

Med hjälp av nycklar för åtkomst till ditt Spatial fästpunkter för Azure-konto är det enklaste sättet att komma igång. Du hittar dina kontonycklar på Azure portal. Navigera till ditt konto och välj fliken ”nycklar”.

![En översikt över autentisering till Azure Spatial fästpunkter](../../../includes/media/spatial-anchors-get-started-create-resource/view-account-key.png)


Två nycklar blir tillgängliga, som är både samtidigt giltigt för åtkomst till kontot Spatial fästpunkter. Vi rekommenderar att du uppdaterar nyckeln för att komma åt kontot med två separata giltiga nycklar aktivera, till exempel uppdaterar utan driftavbrott; Du behöver bara uppdatera också primärnyckeln och sekundärnyckeln. 

SDK: N har inbyggt stöd för att autentisera med nycklar; Du behöver bara ange egenskapen AccountKey cloudSession-objektet. 

```csharp
this.cloudSession.Configuration.AccountKey = @"MyAccountKey";
```

När det är klart, hanterar SDK: N utbyte av kontonyckeln för en åtkomsttoken och nödvändiga cachelagring av token för din app. 

> [!WARNING] 
> Användning av nycklar rekommenderas för snabb registreringen, men under utveckling/prototyper endast. Vi rekommenderar inte att skicka ditt program till produktion med en inbäddad kontonyckel i den och använda användar- eller service-baserade Azure AD-autentisering närmar sig listade nästa.

## <a name="azure-ad-user-authentication"></a>Autentisering av Azure AD-användare

För program som riktar in sig på Azure Active Directory-användare, den rekommenderade metoden är att använda en Azure AD-token för användaren, vilket du kan hämta med ADAL-biblioteket enligt beskrivningen i följande dokumentation: [ https://docs.microsoft.com/azure/active-directory/develop/v1-overview ](../../active-directory/develop/v1-overview.md); du Följ stegen som visas under ”snabb startar”, bland annat:

1. Konfigurationen i Azure-portalen
    1.  Registrera ditt program i Azure AD som **programspecifik**. Som en del av registreringen behöver att fastställa om ditt program bör vara flera innehavare eller inte, och ange omdirigerings-URL: er tillåts för ditt program.  
    2.  Ge ditt program eller användare åtkomst till din resurs: 
        1.  Gå till din Spatial ankare resurs i Azure-portalen
        2.  Växla till den **åtkomstkontroll (IAM)** fliken
        3.  Tryck på **Lägg till rolltilldelning**
            1.  [Välj en roll](#role-based-access-control)
            2.  I den **Välj** fältet, anger du namnet på den användare, grupper och/eller program som du vill tilldela åtkomst. 
            3.  Tryck på **spara**.
2. I koden:
    1.  Se till att använda den **program-ID** och **omdirigerings-Uri** för din egen Azure AD-programmet som den **klient-ID** och **RedirectUri** parametrarna i ADAL
    2.  Ange klientinformation:
        1.  Om ditt program stöder **organisationen endast**, Ersätt detta värde med din **klient-ID** eller **klientnamn** (till exempel contoso.microsoft.com)
        2.  Om ditt program stöder **konton i alla organisationskatalog**, Ersätt detta värde med **organisationer**
        3.  Om ditt program stöder **alla Microsoft-kontoanvändare**, Ersätt detta värde med **vanliga**
    3.  På din begäran om token, ange den **resource** till ”https://sts.mixedreality.azure.com”. Den här ”resurser” indikerar till Azure AD att ditt program begär en token för tjänsten Azure Spatial fästpunkter.  

Därmed ska ditt program kunna hämta en Azure AD-token; från ADAL Du kan ange den Azure AD-token som den **authenticationToken** på molnet sessionen config-objektet. 

```csharp
this.cloudSession.Configuration.AuthenticationToken = @"MyAuthenticationToken";
```

## <a name="azure-ad-service-authentication"></a>Azure AD-tjänst-autentisering

Det rekommenderade alternativet för att distribuera appar som utnyttjar Azure Spatial fästpunkter till produktion är att använda en serverdelstjänst som kommer mäkla autentiseringsbegäranden. Det allmänna systemet bör vara enligt beskrivningen i det här diagrammet:

![En översikt över autentisering till Azure Spatial fästpunkter](./media/spatial-anchors-aad-authentication.png)

Här förutsätts att appen använder en egen mekanism (till exempel: Microsoft-konto, PlayFab, Facebook, Google-ID, anpassade användarnamn/lösenord osv.) att autentisera till en backend-tjänst. När användarna autentiseras till serverdelstjänsten, som tjänsten kan hämta en Azure AD-token exchange det för en åtkomsttoken för Azure Spatial ankare och returnera den tillbaka till klientprogrammet.

Azure AD-åtkomsttoken har hämtats med hjälp av ADAL-biblioteket enligt beskrivningen i följande dokumentation: [ https://docs.microsoft.com/azure/active-directory/develop/v1-overview ](../../active-directory/develop/v1-overview.md); du bör följa stegen under ”snabb startar”, bland annat:

1.  Konfiguration i Azure-portalen:
    1.  Registrera ditt program i Azure AD:
        1.  I Azure-portalen går du till **Azure Active Directory**, och välj **appregistreringar**
        2.  Välj **ny programregistrering**
        3.  Ange namnet på ditt program, Välj **webbapp / API** som programtyp, och ange auth URL: en för din tjänst. Tryck sedan på **skapa**.
        4.  På programmet, trycker du på **inställningar**och välj sedan den **nycklar** fliken. Ange namnet på din nyckel, Välj en varaktighet och når **spara**. Se till att spara nyckelvärdet som visas vid den tidpunkten, eftersom du behöver ingår i din webbtjänst kod.
    2.  Ge din program-och/eller användare åtkomst till resursen:
        1.  Gå till din Spatial ankare resurs i Azure-portalen
        2.  Växla till den **åtkomstkontroll (IAM)** fliken
        3.  Tryck på **Lägg till rolltilldelning**
        1.  [Välj en roll](#role-based-access-control)
        2.  I den **Välj** fältet, anger du namnet på de program som du skapade och vill som du vill tilldela åtkomst. Om du vill appens användare har olika roller mot Spatial ankare-konto, ska du registrera flera program i Azure AD och tilldela till var och en separat roll. Sedan implementera auktorisering logik för att använda rätt roll för användarna.  
    3.  Tryck på **spara**.
2.  I koden (Observera: du kan använda tjänstexemplet på GitHub):
    1.  Omdirigerings-Uri för din egen Azure AD-program som klient-ID och se till att använda program-ID, programhemlighet hemlighet och RedirectUri parametrar i ADAL
    2.  Ange klient-ID till dina egna AAAzure Lägg till klient-ID i parametern utfärdare i ADAL
    3.  På din begäran om token, ange den **resource** till ”https://sts.mixedreality.azure.com” 

Därmed kan serverdelstjänsten hämta en Azure AD-token. Det kan sedan byta den för en MR-token som returneras till klienten. Använda en Azure AD-token för att hämta en token för MR görs via ett REST-anrop. Här är ett exempel-anrop:

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

Där auktoriseringsrubriken är formaterat på följande sätt: `Bearer <accoundId>:<accountKey>`

Och svaret innehåller MR token i oformaterad text.
 
Den MR token returneras till klienten. Klientappen kan sedan ange den som dess åtkomsttoken i sessionen molnkonfigurationen.

```csharp
this.cloudSession.Configuration.AccessToken = @"MyAccessToken";
```

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Till att kontrollera beviljas vilken åtkomstnivå program, tjänster eller Azure AD-användare för din tjänst, följande roller har skapats för att tilldela efter behov mot Spatial fästpunkter för Azure-konton:

- **Kontoägaren för rumsliga ankare**: program eller användare som har den här rollen kan skapa spatial fästpunkter, fråga efter dem och ta bort dem. När du autentiserar till ditt konto med nycklar, den **Kontoägaren för rumsliga ankare** rollen tilldelas det autentiserade huvudnamnet. 
- **Spatial ankare-Kontodeltagare**: program eller användare som har den här rollen kan skapa spatial ankare, fråga för dem, men det går inte att ta bort dem. 
- **Spatial ankare konto läsare**: program eller användare som har den här rollen kan bara skicka frågor för rumsliga ankare men det går inte att skapa nya, ta bort befintliga eller uppdatera metadata på spatial fästpunkter. Detta används vanligtvis för program där vissa användare moderera miljön, medan andra kan bara återställa ankare tidigare placerade i den miljön.

## <a name="next-steps"></a>Nästa steg

Skapa din första app med Azure Spatial fästpunkter.

> [!div class="nextstepaction"]
> [Unity](../unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](../quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](../quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](../quickstarts/get-started-hololens.md)
