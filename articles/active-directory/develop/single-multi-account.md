---
title: Offentliga klientappar för ett och flera konton | Azure
description: En översikt över offentliga klientappar för ett och flera konton.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: 89a383aabf3487a0938604bc28ddb06c0541d13e
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881358"
---
# <a name="single-and-multiple-account-public-client-apps"></a>Offentliga klientappar för ett och flera konton

Den här artikeln hjälper dig att förstå vilka typer som används i offentliga klientappar för ett och flera konton, med fokus på offentliga klientappar för ett konto. 

I ADAL -autentiseringsbiblioteket (Azure Active Directory Authentication Library) modelleras servern.  I Microsoft Authentication Library (MSAL) modelleras klientprogrammet i stället.  Majoriteten av Android-appar betraktas som offentliga klienter. En offentlig klient är en app som inte kan hålla en hemlighet på ett säkert sätt.  

MSAL specialiserar API-ytan på `PublicClientApplication` för att förenkla och förtydliga utvecklingsupplevelsen för appar som tillåter att endast ett konto används åt gången. `PublicClientApplication`är underklassad `SingleAccountPublicClientApplication` `MultipleAccountPublicClientApplication`av och .  Följande diagram visar förhållandet mellan dessa klasser.

![Klassdiagram för SingleAccountPublicClientApplication UML](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>Offentligt klientprogram för ett konto

Med `SingleAccountPublicClientApplication` klassen kan du skapa en MSAL-baserad app som bara tillåter att ett enda konto loggas in åt gången. `SingleAccountPublicClientApplication`skiljer sig `PublicClientApplication` från på följande sätt:

- MSAL spårar det inloggade kontot.
  - Om din app använder en mäklare (standard under Azure portal app registrering) och är installerad på en enhet där en mäklare är närvarande, kommer MSAL kontrollera att kontot fortfarande är tillgängligt på enheten.
- `signIn`kan du logga in på ett konto uttryckligen och separat från att begära scope.
- `acquireTokenSilent`kräver ingen kontoparameter.  Om du tillhandahåller ett konto och kontot du anger inte matchar det aktuella `MsalClientException` kontot som spåras av MSAL, genereras ett.
- `acquireToken`tillåter inte att användaren byter konto. Om användaren försöker växla till ett annat konto genereras ett undantag.
- `getCurrentAccount`returnerar ett resultatobjekt som ger följande:
  - En boolesk som anger om kontot har ändrats. Ett konto kan till exempel ändras till följd av att det tas bort från enheten.
  - Det tidigare kontot. Detta är användbart om du behöver göra någon lokal datarensning när kontot tas bort från enheten eller när ett nytt konto är inloggad.
  - Aktuelltkonto.
- `signOut`tar bort alla token som är associerade med din klient från enheten.  

När en Android-autentiseringsmäklare som Microsoft Authenticator eller Intune Company Portal är installerad `signOut` på enheten och appen är konfigurerad för att använda mäklaren tas inte kontot bort från enheten.

## <a name="single-account-scenario"></a>Scenario med ett enda konto

Följande pseudokod illustrerar `SingleAccountPublicClientApplication`användningen av .

```java
// Construct Single Account Public Client Application
ISingleAccountPublicClientApplication app = PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will get a UI prompt before getting the token.
app.signIn(getActivity(), scopes, new AuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
    }
);

// Load Account Specific Data
getDataForAccount(account);

// Get Current Account
ICurrentAccountResult currentAccountResult = app.getCurrentAccount();
if (currentAccountResult.didAccountChange())
{
    // Account Changed Clear existing account data
    clearDataForAccount(currentAccountResult.getPriorAccount());
    mAccount = currentAccountResult.getCurrentAccount();
    if (account != null)
    {
        //load data for new account
        getDataForAccount(account);
    }
}

// Sign out
if (app.signOut())
{
    clearDataForAccount(mAccount);
    mAccount = null;
}
```

## <a name="multiple-account-public-client-application"></a>Offentligt klientprogram för flera konton

Klassen `MultipleAccountPublicClientApplication` används för att skapa MSAL-baserade appar som tillåter att flera konton loggas in samtidigt. Det låter dig hämta, lägga till och ta bort konton enligt följande:

### <a name="add-an-account"></a>Lägga till ett konto

Använd ett eller flera konton `acquireToken` i programmet genom att ringa en eller flera gånger.  

### <a name="get-accounts"></a>Hämta konton

- Ring `getAccount` för att få ett specifikt konto.
- Ring `getAccounts`för att få en lista över konton som för närvarande är kända för appen.

Din app kan inte räkna upp alla Microsoft-identitetsplattformskonton på den enhet som mäklaren har känt till. Den kan bara räkna upp konton som har använts av din app.  Konton som har tagits bort från enheten returneras inte av dessa funktioner.

### <a name="remove-an-account"></a>Ta bort ett konto

Ta bort ett `removeAccount` konto genom att ringa med en kontoidentifierare.

Om din app är konfigurerad för att använda en mäklare och en mäklare är installerad på `removeAccount`enheten tas kontot inte bort från mäklaren när du ringer .  Endast token som är associerade med klienten tas bort.

## <a name="multiple-account-scenario"></a>Scenario för flera konton

Följande pseudokod visar hur du skapar en app med flera konton, listar konton på enheten och hämtar token.

```java
// Construct Multiple Account Public Client Application
IMultipleAccountPublicClientApplication app = PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will be required to interact with a UI to obtain a token
app.acquireToken(getActivity(), scopes, new AuthenticationCallback()
 {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
 });

...

// Get the default authority
String authority = app.getConfiguration().getDefaultAuthority().getAuthorityURL().toString();

// Get a list of accounts on the device
List<IAccount> accounts = app.getAccounts();

// Pick an account to obtain a token from without prompting the user to sign in
IAccount selectedAccount = accounts.get(0);

// Get a token without prompting the user
app.acquireTokenSilentAsync(scopes, selectedAccount, authority, new SilentAuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }
});
```
