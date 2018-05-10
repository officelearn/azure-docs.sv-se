---
title: Konfigurera resurs ägare lösenord autentiseringsuppgifter flödet i Azure AD B2C | Microsoft Docs
description: Lär dig hur du konfigurerar resurs ägare lösenord autentiseringsuppgifter flödet i Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 04/24/2018
ms.author: davidmu
ms.openlocfilehash: 4a052da4a6a16fde0583b3a1dbecc2a6de5cc0a9
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="configure-the-resource-owner-password-credentials-flow-ropc-in-azure-ad-b2c"></a>Konfigurera resurs ägare lösenord autentiseringsuppgifter flödet (ROPC) i Azure AD B2C

Resursen ägare lösenord autentiseringsuppgifter (ROPC) flödet är en standard autentiseringsflödet för OAUTH där programmet, även kallade den förlitande parten, utbyte giltiga autentiseringsuppgifter, till exempel användar-ID och lösenord för en ID-token, åtkomst-token och en uppdateringstoken. 

> [!NOTE]
> Den här funktionen är i förhandsgranskningen.

Dessa alternativ kan användas i Azure AD B2C:

- **Native Client** – användarinteraktion under autentiseringen sker med hjälp av kod som körs på en enhet för användaren på klientsidan, vilket kan vara ett mobilt program som körs i operativsystemet som t.ex Android eller körs i webbläsaren, till exempel Javascript.
- **Offentliga flödet** – endast användarautentiseringsuppgifter, samlas in av ett program, skickas i API-anrop. Autentiseringsuppgifterna för programmet skickas inte.
- **Lägga till nya anspråk** -ID-token innehållet kan ändras om du vill lägga till nya anspråk. 

De här flödena stöds inte:

- **Server-till-server** skydd identitetssystem (IDPS) måste en tillförlitlig IP-adress som samlats in från anroparen (native client) som en del av interaktionen.  Endast serverns IP-adress används i en API-anrop för serversidan, och IDPS kan identifiera en upprepad IP-adress som en angripare om dynamiskt tröskelvärde för misslyckad har överskridits.
- **Konfidentiell flödet** - klient-ID för programmet har verifierats, men hemligheten som programmet har inte verifierats.

##  <a name="create-a-resource-owner-policy"></a>Skapa en princip för resurs-ägare

1. Logga in på Azure-portalen som global administratör för din Azure AD B2C-klient.
2. Om du vill växla till Azure AD B2C-klientorganisationen väljer du B2C-katalogen längst upp till höger i portalen.
3. Under **principer**väljer **resursägare principer**.
4. Ange ett namn för principen som *ROPC_Auth*, och klicka sedan på **Programanspråk**.
5. Välj programmet anspråk som du behöver för ditt program som *visningsnamn*, *e-postadress*, och *identitetsleverantör*.
6. Klicka på **OK**, och klicka sedan på **skapa**.

En slutpunkt som det här exemplet visas sedan:

`https://login.microsoftonline.com/yourtenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1A_ROPC_Auth`


## <a name="register-an-application"></a>Registrera ett program

1. I B2C-inställningarna klickar du på **Program** och sedan på **+ Lägg till**.
2. Ange ett namn för programmet, t.ex *ROPC_Auth_app*.
3. Klicka på **nr** för **Web App/Web API** och på **Ja** för **Native client**.
4. Lämna alla värden som de är och på **skapa**.
5. Markera det nya programmet och anteckna program-ID.

## <a name="test-the-policy"></a>Testa principen

Använd din favorit-API-utveckling program för att generera ett API-anrop och granska svaret för att felsöka principen. Skapa ett anrop så här med informationen i följande tabell som en del av POST-begäran:
- Ersätt *yourtenant.onmicrosoft.com* med namnet på din B2C-klient
- Ersätt *B2C_1A_ROPC_Auth* med det fullständiga namnet på din ROPC-princip
- Ersätt *bef2222d56 552f-4a5b-b90a-1988a7d634c3* med program-ID från din registrering.

`https://te.cpim.windows.net/yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

| Nyckel | Värde |
| --- | ----- |
| användarnamn | leadiocl@outlook.com |
| lösenord | Passxword1 |
| grant_type | lösenord |
| omfång | openid bef2222d56-552f-4a5b-b90a-1988a7d634c3 offline_access |
| client_id | bef2222d56 552f-4a5b-b90a-1988a7d634c3 |
| response_type | token id_token |

*Client_id* är det värde som du antecknade tidigare som program-ID. *Offline_access* är valfritt om du vill få en uppdateringstoken. 

Den faktiska POST-begäranden som ser ut så här:

```
POST /yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token HTTP/1.1
Host: te.cpim.windows.net
Content-Type: application/x-www-form-urlencoded

username=leadiocl%40trashmail.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```


Ett lyckat svar med offline-åtkomst liknar följande exempel:

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

Skapa en POST anrop så här med informationen i följande tabell som en del av begäran:

`https://te.cpim.windows.net/yourtenant.onmicrosoft.com/B2C_1A_ROPC_Auth/oauth2/v2.0/token`

| Nyckel | Värde |
| --- | ----- |
| grant_type | refresh_token |
| response_type | id_token |
| client_id | bef2222d56 552f-4a5b-b90a-1988a7d634c3 |
| resurs | bef2222d56 552f-4a5b-b90a-1988a7d634c3 |
| refresh_token | eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3... |

*Client_id* och *resurs* är de värden som du antecknade tidigare som program-ID. *Refresh_token* är token som du fick i authentication-samtal som tidigare nämnts.

## <a name="implement-with-your-preferred-native-sdk-or-use-app-auth"></a>Implementera din önskade intern SDK eller använda App-autentisering


Azure AD B2C-implementeringen uppfyller OAuth 2.0-standarder eller offentliga klienten ROPC och ska vara kompatibla med de flesta SDK-klienten.  Vi har testat det här flödet i stor utsträckning, i produktion med AppAuth för iOS och AppAuth för Android.  Se https://appauth.io/ för den senaste informationen.

Du kan ladda ned fungerande prov som har konfigurerats för användning med Azure AD B2C från github på https://aka.ms/aadb2cappauthropc för Android och https://aka.ms/aadb2ciosappauthropc.




