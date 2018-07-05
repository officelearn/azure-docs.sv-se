---
title: Konfigurera resursägarens lösenord autentiseringsuppgifter flöde i Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du konfigurerar resursägarens lösenord autentiseringsuppgifter flöde i Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/24/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5d68f8fe28b7f029d19a0ed0c03e5324c32f29c0
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446817"
---
# <a name="configure-the-resource-owner-password-credentials-flow-in-azure-ad-b2c"></a>Konfigurera resursägarens lösenord autentiseringsuppgifter flöde i Azure AD B2C

Resursägarens lösenord autentiseringsuppgifter (ROPC) flöde är en flöde för OAuth-standardautentisering där programmet, även kallat den förlitande parten, utbyter giltiga autentiseringsuppgifter, till exempel användar-ID och lösenord för ett ID-token, åtkomst-token och en uppdateringstoken. 

> [!NOTE]
> Den här funktionen är en förhandsversion.

I Azure Active Directory (Azure AD) B2C stöds följande alternativ:

- **Native Client**: interaktion från användaren under autentiseringen sker när koden körs på en enhet för användaren sida. Enheten kan vara ett mobilt program som körs i ett operativsystem, till exempel Android, eller körs i en webbläsare, till exempel JavaScript.
- **Offentliga klientflödet**: endast användarens autentiseringsuppgifter, samlas in av ett program, skickas i API-anrop. Autentiseringsuppgifterna för programmet skickas inte.
- **Lägga till nya anspråk**: ID-token innehållet kan ändras för att lägga till nya anspråk. 

Följande flöden stöds inte:

- **Server-till-server**: identity protection systemet behöver en tillförlitlig IP-adress som samlats in från anroparen (intern klient) som en del av interaktionen. I ett API-anrop för serversidan används bara serverns IP-adress. Om ett dynamiskt tröskelvärde för misslyckade autentiseringar överskrids kan identity protection systemet identifiera en upprepad IP-adress som en angripare.
- **Konfidentiellt klientflödet**: programmets klient-ID har verifierats, men programhemlighet har inte verifierats.

##  <a name="create-a-resource-owner-policy"></a>Skapa en princip för resurs-ägare

1. Logga in på Azure Portal som global administratör för din Azure AD B2C-klient.
2. Om du vill växla till din Azure AD B2C-klient, väljer du B2C-katalogen i det övre högra hörnet i portalen.
3. Under **principer**väljer **Resursägaren principer**.
4. Ange ett namn för principen, till exempel *ROPC_Auth*, och välj sedan **Programanspråk**.
5. Välj Programanspråk som du behöver för ditt program, till exempel *visningsnamn*, *e-postadress*, och *identitetsprovider*.
6. Välj **OK** och sedan **Skapa**.

   Du ser därefter en slutpunkt som det här exemplet:

   `https://login.microsoftonline.com/yourtenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1A_ROPC_Auth`


## <a name="register-an-application"></a>Registrera ett program

1. I B2C-inställningar, väljer **program**, och välj sedan **Lägg till**.
2. Ange ett namn för programmet, till exempel *ROPC_Auth_app*.
3. Välj **nr** för **Web App/Web API**, och välj sedan **Ja** för **Native client**.
4. Lämna övriga värden som de är och välj sedan **skapa**.
5. Välj det nya programmet och notera program-ID för senare användning.

## <a name="test-the-policy"></a>Testa principen

Använd din favorit-API-program för utveckling för att generera ett API-anrop och granska svaret för att felsöka din princip. Skapa ett anrop som det här med informationen i följande tabell som en del av POST-begäran:
- Ersätt  *\<yourtenant.onmicrosoft.com >* med namnet på din B2C-klient.
- Ersätt  *\<B2C_1A_ROPC_Auth >* med det fullständiga namnet på din resurs ägare autentiseringsuppgifter lösenordsprincip.
- Ersätt  *\<bef2222d56-552f-4a5b-b90a-1988a7d634c3 >* med program-ID från din registrering.

`https://login.microsoftonline.com/<yourtenant.onmicrosoft.com>/<B2C_1A_ROPC_Auth>/oauth2/v2.0/token`

| Nyckel | Värde |
| --- | ----- |
| användarnamn | leadiocl@outlook.com |
| lösenord | Passxword1 |
| _typ av beviljande | lösenord |
| omfång | openid \<bef2222d56-552f-4a5b-b90a-1988a7d634c3 > offline_access |
| client_id | \<bef2222d56-552f-4a5b-b90a-1988a7d634c3 > |
| response_type | token id_token |

*Client_id* är det värde som du antecknade tidigare som program-ID *Offline_access* är valfritt om du vill få en uppdateringstoken. 

Den faktiska POST-begäran som ser ut som följande:

```
POST /yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token HTTP/1.1
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

username=leadiocl%40trashmail.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```


Ett lyckat svar med offlineåtkomst ser ut som i följande exempel:

```
{ 
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJpc3MiOiJodHRwczovL3RlLmNwaW0ud2luZG93cy5uZXQvZjA2YzJmZTgtNzA5Zi00MDMwLTg1ZGMtMzhhNGJmZDllODJkL3YyLjAvIiwiZXhwIjoxNTEzMTMwMDc4LCJuYmYiOjE1MTMxMjY0NzgsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsIm9pZCI6IjNjM2I5NjljLThjNDktNGUxMS1hNGVmLWZkYjJmMzkyZjA0OSIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF6cCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsInZlciI6IjEuMCIsImlhdCI6MTUxMzEyNjQ3OH0.MSEThYZxCS4SevBw3-3ecnVLUkucFkehH-gH-P7SFcJ-MhsBeQEpMF1Rzu_R9kUqV3qEWKAPYCNdZ3_P4Dd3a63iG6m9TnO1Vt5SKTETuhVx3Xl5LYeA1i3Slt9Y7LIicn59hGKRZ8ddrQzkqj69j723ooy01amrXvF6zNOudh0acseszt7fbzzofyagKPerxaeTH0NgyOinLwXu0eNj_6RtF9gBfgwVidRy9OzXUJnqm1GdrS61XUqiIUtv4H04jYxDem7ek6E4jsH809uSXT0iD5_4C5bDHrpO1N6pXSasmVR9GM1XgfXA_IRLFU4Nd26CzGl1NjbhLnvli2qY4A", 
    "token_type": "Bearer", 
    "expires_in": "3600", 
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6IjEuMCJ9.aJ_2UW14dh4saWTQ0jLJ7ByQs5JzIeW_AU9Q_RVFgrrnYiPhikEc68ilvWWo8B20KTRB_s7oy_Eoh5LACsqU6Oz0Mjnh0-DxgrMblUOTAQ9dbfAT5WoLZiCBJIz4YT5OUA_RAGjhBUkqGwdWEumDExQnXIjRSeaUBmWCQHPPguV1_5wSj8aW2zIzYIMbofvpjwIATlbIZwJ7ufnLypRuq_MDbZhJkegDw10KI4MHJlJ40Ip8mCOe0XeJIDpfefiJ6WQpUq4zl06NO7j8kvDoVq9WALJIao7LYk_x9UIT-3d0W0eDBHGSRcNgtMYpymaN9ltx6djcEesXNn4CFnWG3g.y6KKeA9EcsW9zW-g.TrTSgn4WBt18gezegxihBla9SLSTC3YfDROQsL9K4yX4400FKlTlf-2l9CnpGTEdWXVi7sIMHCl8S4oUiXd-rvY2mn_NfDrbbVJfgKp1j7Nnq9FFyeJEFcP_FtUXgsNTG9iwfzWox04B1d845qNRWiS9N8BhAAAIdz5N0ChHuOxsVOC0Y_Ly3DNe-JQyXcq964M6-jp3cgi4UqMxT837L6pLY5Ih_iPsSfyHzstsFeqyUIktnzt1MpTlyW-_GDyFK1S-SyV8PPQ7phgFouw2jho1iboHX70RlDGYyVmP1CfQzKE_zWxj3rgaCZvYMWN8fUenoiatzhvWkUM7dhqKGjofPeL8rOMkhl6afLLjObzhUg3PZFcMR6guLjQdEwQFufWxGjfpvaHycZSKeWu6-7dF8Hy_nyMLLdBpUkdrXPob_5gRiaH72KvncSIFvJLqhY3NgXO05Fy87PORjggXwYkhWh4FgQZBIYD6h0CSk2nfFjR9uD9EKiBBWSBZj814S_Jdw6HESFtn91thpvU3hi3qNOi1m41gg1vt5Kh35A5AyDY1J7a9i_lN4B7e_pknXlVX6Z-Z2BYZvwAU7KLKsy5a99p9FX0lg6QweDzhukXrB4wgfKvVRTo.mjk92wMk-zUSrzuuuXPVeg", 
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlkifQ.eyJleHAiOjE1MTMxMzAwNzgsIm5iZiI6MTUxMzEyNjQ3OCwidmVyIjoiMS4wIiwiaXNzIjoiaHR0cHM6Ly90ZS5jcGltLndpbmRvd3MubmV0L2YwNmMyZmU4LTcwOWYtNDAzMC04NWRjLTM4YTRiZmQ5ZTgyZC92Mi4wLyIsInN1YiI6Ik5vdCBzdXBwb3J0ZWQgY3VycmVudGx5LiBVc2Ugb2lkIGNsYWltLiIsImF1ZCI6ImJlZjIyZDU2LTU1MmYtNGE1Yi1iOTBhLTE5ODhhN2Q2MzRjZSIsImFjciI6ImIyY18xYV9yZXNvdXJjZW93bmVydjIiLCJpYXQiOjE1MTMxMjY0NzgsImF1dGhfdGltZSI6MTUxMzEyNjQ3OCwib2lkIjoiM2MzYjk2OWMtOGM0OS00ZTExLWE0ZWYtZmRiMmYzOTJmMDQ5IiwiYXRfaGFzaCI6Ikd6QUNCTVJtcklwYm9OdkFtNHhMWEEifQ.iAJg13cgySsdH3cmoEPGZB_g-4O8KWvGr6W5VzRXtkrlLoKB1pl4hL6f_0xOrxnQwj2sUgW-wjsCVzMc_dkHSwd9QFZ4EYJEJbi1LMGk2lW-PgjsbwHPDU1mz-SR1PeqqJgvOqrzXo0YHXr-e07M4v4Tko-i_OYcrdJzj4Bkv7ZZilsSj62lNig4HkxTIWi5Ec2gD79bPKzgCtIww1KRnwmrlnCOrMFYNj-0T3lTDcXAQog63MOacf7OuRVUC5k_IdseigeMSscrYrNrH28s3r0JoqDhNUTewuw1jx0X6gdqQWZKOLJ7OF_EJMP-BkRTixBGK5eW2YeUUEVQxsFlUg" 
} 
```

## <a name="redeem-a-refresh-token"></a>Lösa in en uppdateringstoken

Skapa en POST-anrop som den som visas här med informationen i följande tabell som en del av begäran:

`https://login.microsoftonline.com/<yourtenant.onmicrosoft.com>/<B2C_1A_ROPC_Auth>/oauth2/v2.0/token`

| Nyckel | Värde |
| --- | ----- |
| _typ av beviljande | refresh_token |
| response_type | id_token |
| client_id | \<bef2222d56-552f-4a5b-b90a-1988a7d634c3 > |
| resurs | \<bef2222d56-552f-4a5b-b90a-1988a7d634c3 > |
| refresh_token | eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3... |

*Client_id* och *resource* är de värden som du antecknade tidigare som program-ID *Refresh_token* är token som du fick i authentication-samtal som tidigare nämnts.

## <a name="implement-with-your-preferred-native-sdk-or-use-app-auth"></a>Implementera med dina önskade intern SDK eller använda App Auth

Azure AD B2C-implementeringen uppfyller OAuth 2.0-standarder för lösenord-autentiseringsuppgifterna för offentlig klient-ägare och bör vara kompatibla med de flesta klient-SDK: er. Vi har testat det här flödet i stor utsträckning, i produktion med AppAuth för iOS och AppAuth för Android. Den senaste informationen finns i [inbyggd App-SDK för OAuth 2.0 och OpenID Connect implementera moderna metodtips](https://appauth.io/).

Ladda ned fungerande exempel som har konfigurerats för användning med Azure AD B2C från GitHub, [för Android](https://aka.ms/aadb2cappauthropc) och [för iOS](https://aka.ms/aadb2ciosappauthropc).




