---
title: SSO mellan ADAL & MSAL-appar (iOS/macOS) – Microsofts identitetsplattform | Azure
description: ''
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 7a8a1667ba1ca2a99c053c6941e3ba778299fd53
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80880758"
---
# <a name="how-to-sso-between-adal-and-msal-apps-on-macos-and-ios"></a>Så här: SSO mellan ADAL- och MSAL-appar på macOS och iOS

Microsoft Authentication Library (MSAL) för iOS kan dela SSO-tillstånd med [ADAL Objective-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc) mellan program. Du kan migrera dina appar till MSAL i din egen takt, så att användarna fortfarande kan dra nytta av SSO över flera appar – även med en blandning av ADAL- och MSAL-baserade appar.

Om du letar efter vägledning för att konfigurera SSO mellan appar som använder MSAL SDK läser du [Tyst SSO mellan flera appar](single-sign-on-macos-ios.md#silent-sso-between-apps). Den här artikeln fokuserar på SSO mellan ADAL och MSAL.

Vilka detaljer som implementerar SSO beror på ADAL-version som du använder.

## <a name="adal-27x"></a>ADAL 2.7.x

Det här avsnittet behandlar SSO-skillnader mellan MSAL och ADAL 2.7.x

### <a name="cache-format"></a>Cacheformat

ADAL 2.7.x kan läsa MSAL-cacheformatet. Du behöver inte göra något speciellt för cross-app SSO med version ADAL 2.7.x. Du måste dock vara medveten om skillnader i kontoidentifierare som dessa två bibliotek stöder.

### <a name="account-identifier-differences"></a>Skillnader i kontoidentifierare

MSAL och ADAL använder olika kontoidentifierare. ADAL använder UPN som primär kontoidentifierare. MSAL använder en kontoidentifierare som inte kan visas som baseras på ett objekt-ID och ett klient-ID för AAD-konton och ett `sub` anspråk för andra typer av konton.

När du `MSALAccount` får ett objekt i MSAL-resultatet innehåller det `identifier` en kontoidentifierare i egenskapen. Programmet bör använda den här identifieraren för efterföljande tysta begäranden.

Förutom `identifier`innehåller `MSALAccount` objektet en visningsbar identifierare som heter `username`. Det kan `userId` översättas till i ADAL. `username`anses inte vara en unik identifierare och kan ändras när som helst, så den bör endast användas för bakåtkompatibilitetsscenarier med ADAL. MSAL stöder cachefrågor `username` med `identifier`antingen eller `identifier` , där frågor rekommenderas.

I följande tabell sammanfattas skillnader i kontoidentifierare mellan ADAL och MSAL:

| Kontoidentifierare                | MSAL                                                         | ADAL 2.7.x      | Äldre ADAL (före ADAL 2.7.x) |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| displayerbar identifierare            | `username`                                                   | `userId`        | `userId`                       |
| unik identifierare som inte kan visas | `identifier`                                                 | `homeAccountId` | Ej tillämpligt                            |
| Inget konto-ID känt               | Fråga alla `allAccounts:` konton via API i`MSALPublicClientApplication` | Ej tillämpligt             | Ej tillämpligt                            |

Detta är `MSALAccount` gränssnittet som tillhandahåller dessa identifierare:

```objc
@protocol MSALAccount <NSObject>

/*!
 Displayable user identifier. Can be used for UI and backward compatibility with ADAL.
 */
@property (readonly, nullable) NSString *username;

/*!
 Unique identifier for the account.
 Save this for account lookups from cache at a later point.
 */
@property (readonly, nullable) NSString *identifier;

/*!
 Host part of the authority string used for authentication based on the issuer identifier.
 */
@property (readonly, nonnull) NSString *environment;

/*!
 ID token claims for the account.
 Can be used to read additional information about the account, e.g. name
 Will only be returned if there has been an id token issued for the client Id for the account's source tenant.
 */
@property (readonly, nullable) NSDictionary<NSString *, NSString *> *accountClaims;

@end
```

### <a name="sso-from-msal-to-adal"></a>SSO från MSAL till ADAL

Om du har en MSAL-app och en ADAL-app och användaren först loggar in på den MSAL-baserade `MSALAccount` appen kan du få SSO i `userId`ADAL-appen genom att spara `username` från objektet och skicka den till din ADAL-baserade app som . ADAL kan sedan hitta kontoinformationen `acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:` tyst med API:et.

### <a name="sso-from-adal-to-msal"></a>SSO från ADAL till MSAL

Om du har en MSAL-app och en ADAL-app och användaren först loggar in på den ADAL-baserade appen kan du använda ADAL-användaridentifierare för kontosökningar i MSAL. Detta gäller även vid migrering från ADAL till MSAL.

#### <a name="adals-homeaccountid"></a>ADAL hemAccountId

ADAL 2.7.x `homeAccountId` returnerar `ADUserInformation` objektet i objektet via den här egenskapen:

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

`homeAccountId`adals motsvarar `identifier` i MSAL. Du kan spara den här identifieraren som ska användas `accountForIdentifier:error:` i MSAL för kontosökningar med API:et.

#### <a name="adals-userid"></a>ADAL:s`userId`

Om `homeAccountId` det inte är tillgängligt, eller om du bara har den `userId` visningsbara identifieraren, kan du använda ADAL:s för att söka upp kontot i MSAL.

I MSAL slår du först `username` `identifier`upp ett konto med eller . Använd `identifier` alltid för att fråga om du `username` har det och använd den bara som reserv. Om kontot hittas använder du `acquireTokenSilent` kontot i samtalen.

Mål C:

```objc
NSString *msalIdentifier = @"previously.saved.msal.account.id";
MSALAccount *account = nil;
    
if (msalIdentifier)
{
    // If you have MSAL account id returned either from MSAL as identifier or ADAL as homeAccountId, use it
    account = [application accountForIdentifier:@"my.account.id.here" error:nil];
}
else
{
    // Fallback to ADAL userId for migration
    account = [application accountForUsername:@"adal.user.id" error:nil];
}
    
if (!account)
{
  // Account not found.
  return;
}

MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
        
let msalIdentifier: String?
var account: MSALAccount
        
do {
  if let msalIdentifier = msalIdentifier {
    account = try application.account(forIdentifier: msalIdentifier)
  }
  else {
    account = try application.account(forUsername: "adal.user.id") 
  }
             
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)          
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result
  }  
} catch let error as NSError {
  // handle error or account not found
}
```



MSAL-api:er som stöds för kontosökning:

```objc
/*!
 Returns account for the given account identifier (received from an account object returned in a previous acquireToken call)
 
 @param  error      The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
 */
- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier
                                         error:(NSError * _Nullable __autoreleasing * _Nullable)error;
    
/*!
Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)
    
@param  username    The displayable value in UserPrincipleName(UPN) format
@param  error       The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
*/
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

## <a name="adal-2x-266"></a>ADAL 2.x-2.6.6

Det här avsnittet beskriver SSO-skillnader mellan MSAL och ADAL 2.x-2.6.6.

Äldre ADAL-versioner stöder inte MSAL-cacheformatet internt. För att säkerställa en smidig migrering från ADAL till MSAL kan MSAL dock läsa det äldre ADAL-cacheformatet utan att fråga efter användarautentiseringsuppgifter igen.

Eftersom `homeAccountId` det inte finns i äldre ADAL-versioner måste du `username`slå upp konton med hjälp av:

```objc
/*!
 Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)

 @param  username    The displayable value in UserPrincipleName(UPN) format
 @param  error       The error that occurred trying to get the accounts, if any.  If you're not interested in the specific error pass in nil.
 */
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

Ett exempel:

Mål C:


```objc
MSALAccount *account = [application accountForUsername:@"adal.user.id" error:nil];;
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
do {
  let account = try application.account(forUsername: "adal.user.id")          
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)
  application.acquireTokenSilent(with: silentParameters) { 
    (result: MSALResult?, error: Error?) in
    // handle result
  }   
} catch let error as NSError { 
  // handle error or account not found
}
```



Alternativt kan du läsa alla konton, som också kommer att läsa kontoinformation från ADAL:

Mål C:

```objc
NSArray *accounts = [application allAccounts:nil];
    
if ([accounts count] == 0)
{
  // No account found.
  return; 
}
if ([accounts count] > 1)
{
  // You might want to display an account picker to user in actual application
  // For this sample we assume there's only ever one account in cache
  return;
}
    ``
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:accounts[0]];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Swift:

```swift
      
do {
  let accounts = try application.allAccounts()
  if accounts.count == 0 {
    // No account found.
    return
  }
  if accounts.count > 1 {
    // You might want to display an account picker to user in actual application
    // For this sample we assume there's only ever one account in cache
    return
  }
  
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: accounts[0])
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result or error  
  }  
} catch let error as NSError { 
  // handle error
}
```



## <a name="next-steps"></a>Nästa steg

Läs mer om [autentiseringsflöden och programscenarier](authentication-flows-app-scenarios.md)
