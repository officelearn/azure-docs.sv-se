<properties
    pageTitle="Förhandsgranskning, Azure Active Directory B2C: Anropa ett webb-API från ett iOS-program | Microsoft Azure"
    description="Den här artikeln beskriver hur du skapar en ”att göra-app” för iOS som anropar en Node.js-webb-API med OAuth 2.0-ägar-token. Både iOS-appen och webb-API:et hanterar användaridentiteter och autentiserar användare med hjälp av Azure Active Directory B2C."
    services="active-directory-b2c"
    documentationCenter="ios"
    authors="brandwe"
    manager="mbaldwin"
    editor=""/>

<tags ms.service="active-directory-b2c" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="objectivec" ms.topic="hero-article"

    ms.date="05/31/2016"
    ms.author="brandwe"/>

# Förhandsgranskning, Azure AD B2C: Anropa ett webb-API från ett iOS-program

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-web-switcher](../../includes/active-directory-b2c-devquickstarts-web-switcher.md)]-->

Med Azure Active Directory (AD Azure) B2C kan du lägga till kraftfulla självbetjäningsfunktioner för identitetshantering i dina iOS-appar och webb-API:er i bara några korta steg. Den här artikeln beskriver hur du skapar en ”att göra-app” som anropar en Node.js-webb-API med OAuth 2.0-ägar-token. Både iOS-appen och webb-API:et hanterar användaridentiteter och autentiserar användare med hjälp av Azure AD B2C.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]
    Denna snabbstart kräver att du redan har ett webb-API som skyddas av Azure AD B2C för att fungera helt. Vi har skapat ett för både .NET och Node.js som du kan använda. Den här genomgången förutsätter att exemplet med Node.js-webb-API:et är konfigurerat. [Exemplet Azure Active Directory-webb-API för Node.js](active-directory-b2c-devquickstarts-api-node.md) innehåller mer information.
).

> [AZURE.NOTE]
    Den här artikeln innehåller inte information om hur du implementerar inloggning, registrering och profilhantering med hjälp av Azure AD B2C. Den fokuserar på att anropa webb-API:er när användaren har autentiserats. Börja med [självstudierna för att komma igång med webbappen .NET](active-directory-b2c-devquickstarts-web-dotnet.md) om du vill lära dig grunderna i Azure AD B2C.

## Hämta en Azure AD B2C-katalog

Innan du kan använda Azure AD B2C, måste du skapa en katalog eller klient. En katalog är en behållare för alla användare, appar, grupper och mer. Om du inte redan har en [skapar du en B2C-katalog](active-directory-b2c-get-started.md) innan du fortsätter.

## Skapa ett program

Därefter måste du skapa en app i B2C-katalogen. Det ger Azure AD den information som det behöver för att kommunicera säkert med din app. I det här fallet representeras både appen och webb-API:et av ett enda **program-ID** eftersom de omfattar en logisk app. Skapa en app genom att följa [anvisningarna](active-directory-b2c-app-registration.md). Se till att:

- Inkludera en **webbapp/ett webb-API** i programmet.
- Ange `http://localhost:3000/auth/openid/return` som en **Reply URL**. Den är standard-URL för det här kodexemplet.
- Skapa en **programhemlighet** för programmet och kopiera den. Du behöver den senare.
- Kopiera det **Program-ID** som har tilldelats din app. Du behöver även detta senare.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Skapa principer

I Azure AD B2C definieras varje användarupplevelse av en [princip](active-directory-b2c-reference-policies.md). Den här appen innehåller tre identitetsupplevelser: registrera, logga in och logga ut med Facebook. Du måste skapa en princip av varje typ, enligt beskrivningen i [artikeln om principreferens](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy). Tänk på följande när du skapar de tre principerna:

- Välj **Visningsnamn** och registreringsattribut i principen för registrering.
- Välj det **visningsnamn** och **objekt-ID** som programmet gör anspråk på i varje princip. Du kan också välja andra anspråk.
- Kopiera **namnet** på varje princip när du har skapat den. Det bör ha prefixet `b2c_1_`.  Du behöver dem senare.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

När du har skapat dina tre principer, är du redo att bygga din app.

Tänk på att den här artikeln inte beskriver hur du använder principerna som du nyss har skapat. Börja med [självstudierna för att komma igång med webbappen för .NET](active-directory-b2c-devquickstarts-web-dotnet.md) om du vill veta mer om hur principer fungerar i Azure AD B2C.

## Ladda ned koden

Koden för dessa självstudier [underhålls på GitHub](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS). Om du vill bygga exemplet allteftersom kan du [ladda ned stommen av ett projekt som en .zip-fil](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/skeleton.zip). Du kan också klona stommen:

```
git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS.git
```

> [AZURE.NOTE] **Du måste ladda ned stommen för att slutföra den här självstudiekursen.** Eftersom det är komplicerat att implementera ett fullt fungerande program i iOS har **stommen** UX-kod som körs när du har slutfört självstudierna. Det är ett sätt att spara tid för utvecklarna. UX-koden är inte relevant för avsnittet om hur du lägger till B2C i  ett iOS-program.

Den slutförda appen finns också [tillgänglig som en .zip-fil](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip) eller på `complete` grenen för samma lagringsplats.

Nu läser du in `podfile` med CocoaPods. Då skapas en ny Xcode-arbetsyta som ska läsas in. Om du inte har CocoaPods [går du till webbplatsen för att konfigurera det](https://cocoapods.org).

```
$ pod install
...
$ open Microsoft Tasks for Consumers.xcworkspace
```

## Konfigurera iOS-programmet för aktivitet.

Se till att iOS-appen kommunicerar med Azure AD B2C genom att ange några vanliga parametrar. I `Microsoft Tasks`-mappen öppnar du `settings.plist`-filen i roten av projektet och ersätter värdena i `<dict>`-avsnittet. Dessa värden används i hela appen.

```
<dict>
    <key>authority</key>
    <string>https://login.microsoftonline.com/<your tenant name>.onmicrosoft.com/</string>
    <key>clientId</key>
    <string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
    <key>scopes</key>
    <array>
        <string><Enter the Application Id assigned to your app by the Azure portal, e.g.580e250c-8f26-49d0-bee8-1c078add1609></string>
    </array>
    <key>additionalScopes</key>
    <array>
    </array>
    <key>redirectUri</key>
    <string>urn:ietf:wg:oauth:2.0:oob</string>
    <key>taskWebAPI</key>
    <string>http://localhost/tasks:3000</string>
    <key>emailSignUpPolicyId</key>
    <string><Enter your sign up policy name, e.g.g b2c_1_sign_up></string>
    <key>faceBookSignInPolicyId</key>
    <string><your sign in policy for FB></string>
    <key>emailSignInPolicyId</key>
    <string><Enter your sign in policy name, e.g. b2c_1_sign_in></string>
    <key>fullScreen</key>
    <false/>
    <key>showClaims</key>
    <true/>
</dict>
</plist>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

## Få åtkomst-token och anropa aktivitetens API

Det här avsnittet beskriver hur du kan utföra ett utbyte av OAuth 2.0-token i en webbapp med hjälp av Microsofts bibliotek och ramverk. Om du inte känner till auktoriseringskoder och åtkomst-token kan du läsa mer i [Protokollreferens för OAuth 2.0](active-directory-b2c-reference-protocols.md).

### Skapa rubrikfiler genom att använda metoder

Du behöver metoder för att hämta en token med den valda principen och sedan anropa aktivitetsservern. Konfigurera dessa nu.

Skapa en fil med namnet `samplesWebAPIConnector.h` under `/Microsoft Tasks` i Xcode-projektet

Definiera vad du behöver göra genom att lägga till följande kod:

```
#import <Foundation/Foundation.h>
#import "samplesTaskItem.h"
#import "samplesPolicyData.h"
#import "ADALiOS/ADAuthenticationContext.h"

@interface samplesWebAPIConnector : NSObject<NSURLConnectionDataDelegate>

+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) doPolicy:(samplesPolicyData*)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock;

+(void) signOut;

@end
```

Det här är enkla CRUD-åtgärder för att skapa, läsa, uppdatera och ta bort i ditt API, och det är också en metod `doPolicy`. Genom att använda den här metoden kan du hämta en token med den princip du vill.

Kom ihåg att två andra rubrikfiler måste definieras. De ska innehålla aktivitetsobjektet och principdata. Skapa dem nu:

Skapa filen `samplesTaskItem.h` med följande kod:

```
#import <Foundation/Foundation.h>

@interface samplesTaskItem : NSObject

@property NSString *itemName;
@property NSString *ownerName;
@property BOOL completed;
@property (readonly) NSDate *creationDate;

@end
```

Skapa även filen `samplesPolicyData.h` för dina principdata:

```
#import <Foundation/Foundation.h>

@interface samplesPolicyData : NSObject

@property (strong) NSString* policyName;
@property (strong) NSString* policyID;

+(id) getInstance;

@end
```
### Lägg till en implementering för aktivitets- och principobjekten

Nu när rubrikfilerna är på plats kan du skriva kod för att lagra de data som du ska använda för ditt exempel.

Skapa filen `samplesPolicyData.m` med följande kod:

```
#import <Foundation/Foundation.h>
#import "samplesPolicyData.h"

@implementation samplesPolicyData

+(id) getInstance
{
    static samplesPolicyData *instance = nil;
    static dispatch_once_t onceToken;

    dispatch_once(&onceToken, ^{
        instance = [[self alloc] init];
        NSDictionary *dictionary = [NSDictionary dictionaryWithContentsOfFile:[[NSBundle mainBundle] pathForResource:@"settings" ofType:@"plist"]];
        instance.policyName = [dictionary objectForKey:@"policyName"];
        instance.policyID = [dictionary objectForKey:@"policyID"];


    });

    return instance;
}


@end
```

### Skriv inställningskod för anrop till ADAL för iOS

Snabbkoden för att lagra objekten till användargränssnittet är nu klar. Nu behöver du skriva kod för att komma åt ADAL (Active Directory Authentication Library, autentiseringsbibliotek för Active Directory) för iOS med de parametrar som du lägger till i `settings.plist`. Då hämtas en åtkomst-token för aktivitetsservern.

Allt arbete utförs i `samplesWebAPIConnector.m`.

Börja med att skapa den `doPolicy()`-implementering som du skrev i `samplesWebAPIConnector.h`-rubrikfilen:

```
+(void) doPolicy:(samplesPolicyData *)policy
         parent:(UIViewController*) parent
completionBlock:(void (^) (ADProfileInfo* userInfo, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    [self getClaimsWithPolicyClearingCache:NO policy:policy params:nil parent:parent completionHandler:^(ADProfileInfo* userInfo, NSError* error) {

        if (userInfo == nil)
        {
            completionBlock(nil, error);
        }

        else {

            completionBlock(userInfo, nil);
        }
    }];

}


```

Metoden är enkel. Den använder de `samplesPolicyData`-objekt du har skapat, den överordnade `ViewController` och en motringning som indata. Motringning är intressant och vi ska titta närmare på det.

- Observera att `completionBlock` har `ADProfileInfo` som en typ som returneras med ett `userInfo`-objekt. `ADProfileInfo` är den typ som innehåller alla svar från servern, inklusive anspråk.
- Observera även `readApplicationSettings`. Det läser de data som du angett i `settings.plist`.
- Och slutligen har du en stor `getClaimsWithPolicyClearingCache` metod. Det här är det faktiska anropet till ADAL för iOS som du behöver skriva. Vi återkommer till det senare.

Skriv sedan den stora metoden `getClaimsWithPolicyClearingCache`. Det här är tillräckligt stor för att ha ett eget avsnitt.

### Skapa anrop till ADAL för iOS

När du har hämtat stommen från GitHub ser du att vi har flera av dessa anrop som hjälp till exempelprogrammet. Alla följer mönstret för `get(Claims|Token)With<verb>ClearningCache`. Genom att använda mål C-konventioner kan de läsas ungefär som engelska. Till exempel ”Hämta en token med extraparametrar som jag ger dig och rensa cachen” är `getTokenWithExtraParamsClearingCache()`.

Du skriver ”Hämta anspråk och en token med den princip som jag ger dig och rensa inte cachen” eller `getClaimsWithPolicyClearingCache`. Du får alltid en token tillbaka från ADAL, så du behöver inte specificera ”anspråk och en token” i metoden. Men ibland vill du bara ha en token utan den tillhörande informationen för att tolka anspråken, så vi tillhandahåller även en metod utan anspråk som `getTokenWithPolicyClearingCache` i stommen.

Skriv koden nu:

```
+(void) getClaimsWithPolicyClearingCache  : (BOOL) clearCache
                           policy:(samplesPolicyData *)policy
                           params:(NSDictionary*) params
                           parent:(UIViewController*) parent
                completionHandler:(void (^) (ADProfileInfo*, NSError*))completionBlock;
{
    SamplesApplicationData* data = [SamplesApplicationData getInstance];


    ADAuthenticationError *error;
    authContext = [ADAuthenticationContext authenticationContextWithAuthority:data.authority error:&error];
    authContext.parentController = parent;
    NSURL *redirectUri = [[NSURL alloc]initWithString:data.redirectUriString];

    if(!data.correlationId ||
       [[data.correlationId stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]] length] == 0)
    {
        authContext.correlationId = [[NSUUID alloc] initWithUUIDString:data.correlationId];
    }

    [ADAuthenticationSettings sharedInstance].enableFullScreen = data.fullScreen;
    [authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {

                           if (result.status != AD_SUCCEEDED)
                           {
                               completionBlock(nil, result.error);
                           }                              else
                              {
                                  data.userItem = result.tokenCacheStoreItem;
                                  completionBlock(result.tokenCacheStoreItem.profileInfo, nil);
                              }
                          }];
}


```

Den första delen borde se bekant ut.

- Läs in de inställningar som angavs i `settings.plist` och tilldela dem till `data`.
- Ställ in `ADAuthenticationError`, som tar alla fel som kommer från ADAL för iOS.
- Skapa `authContext`, som konfigurerar anropet till ADAL. Du auktoriserar det så att allt kommer igång.
- Ge `authContext` en referens till den överordnade styrenheten så att du kan återgå till den.
- Konvertera `redirectURI`, som var en sträng i `settings.plist`, till den NSURL-typ som ADAL förväntar sig.
- Ställ in `correlationId`. Det här är en UUID som kan följa anropet genom klienten till servern och tillbaka. Det här är användbart för felsökning.

Sedan kommer du till det faktiska anropet till ADAL. Det är där anropet förändras från vad du förväntar dig att se i tidigare användningar av ADAL för iOS:

```
[authContext acquireTokenWithScopes:data.scopes
                      additionalScopes: data.additionalScopes
                              clientId:data.clientId
                           redirectUri:redirectUri
                            identifier:[ADUserIdentifier identifierWithId:data.userItem.profileInfo.username type:RequiredDisplayableId]
                            promptBehavior:AD_PROMPT_ALWAYS
                  extraQueryParameters: params.urlEncodedString
                                policy: policy.policyID
                       completionBlock:^(ADAuthenticationResult *result) {

```

Du kan se att anropet är ganska enkelt.

`scopes`: De omfattningar du skickar till servern och som du vill begära från den för en användare som loggar in. För B2C-förhandsgranskning skickar du `client_id`. Men det kommer troligtvis att förändras om du vill läsa omfattningar i framtiden. Vi planerar att uppdatera det här dokumentet då.
`additionalScopes`: Det här är ytterligare omfattningar som du kanske vill använda för ditt program. De förväntas användas i framtiden.
`clientId`: Det program-ID som du har fått från portalen.
`redirectURI`: Den omdirigering där du förväntar dig att token ska publiceras igen.
`identifier`: Ett sätt att identifiera en användare så att du kan se om det finns en användbar token i cacheminnet. Det gör att du inte alltid behöver be servern om en token. Det utförs i en typ som kallas `ADUserIdentifier`, och du kan ange vad du vill använda som ett ID. Du bör använda `username`.
`promptBehavior`: Det är inaktuellt. Det bör vara `AD_PROMPT_ALWAYS`.
`extraQueryParameters`: Allt extra du vill skicka till servern i URL-kodat format.
`policy`: Principen du anropar. Det här är den viktigaste delen för den här genomgången.

Du kan se i `completionBlock` att du skickar `ADAuthenticationResult`. Det innehåller din token- och profilinformation (om anropet lyckades).

Med hjälp av koden ovan kan du hämta en token för den princip du anger. Du kan sedan använda denna token för att anropa API:et.

### Skapa aktivitetsanrop till servern

Nu när du har en token måste du ange den i ditt API för auktorisering.

Du behöver implementera tre metoder:

```
+(void) getTaskList:(void (^) (NSArray*, NSError* error))completionBlock
             parent:(UIViewController*) parent;

+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock;

+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock;
```

`getTasksList` innehåller en matris som representerar aktiviteterna i din server. `addTask` och `deleteTask` gör de efterföljande åtgärderna och returnerar `true` eller `false` om de lyckas.

Skriv `getTaskList` först:

```

+(void) getTaskList:(void (^) (NSArray*, NSError*))completionBlock
             parent:(UIViewController*) parent;
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];

    [self craftRequest:[self.class trimString:data.taskWebApiUrlString]
                parent:parent
     completionHandler:^(NSMutableURLRequest *request, NSError *error) {

        if (error != nil)
        {
            completionBlock(nil, error);
        }
        else
        {

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                if (error == nil && data != nil){

                    NSArray *tasks = [NSJSONSerialization JSONObjectWithData:data options:0 error:nil];

                    //each object is a key value pair
                    NSDictionary *keyValuePairs;
                    NSMutableArray* sampleTaskItems = [[NSMutableArray alloc]init];

                    for(int i =0; i < tasks.count; i++)
                    {
                        keyValuePairs = [tasks objectAtIndex:i];

                        samplesTaskItem *s = [[samplesTaskItem alloc]init];
                        s.itemName = [keyValuePairs valueForKey:@"task"];

                        [sampleTaskItems addObject:s];
                    }

                    completionBlock(sampleTaskItems, nil);
                }
                else
                {
                    completionBlock(nil, error);
                }

            }];
        }
    }];

}

```

En diskussion om uppgiftskoden ligger utanför den här genomgångens omfattning. Men du kanske har lagt märke till något intressant: en `craftRequest`-metod som använder aktivitetens URL. Använd den här metoden om du vill skapa serverbegäran med hjälp av den åtkomst-token som du har fått. Skriv det nu.

Lägg till följande kod i  `samplesWebAPIConnector.m`-filen:

```
+(void) craftRequest : (NSString*)webApiUrlString
               parent:(UIViewController*) parent
    completionHandler:(void (^)(NSMutableURLRequest*, NSError* error))completionBlock
{
    [self getClaimsWithPolicyClearingCache:NO parent:parent completionHandler:^(NSString* accessToken, NSError* error){

        if (accessToken == nil)
        {
            completionBlock(nil,error);
        }
        else
        {
            NSURL *webApiURL = [[NSURL alloc]initWithString:webApiUrlString];

            NSMutableURLRequest *request = [[NSMutableURLRequest alloc]initWithURL:webApiURL];

            NSString *authHeader = [NSString stringWithFormat:@"Bearer %@", accessToken];

            [request addValue:authHeader forHTTPHeaderField:@"Authorization"];

            completionBlock(request, nil);
        }
    }];
}
```

Detta tar emot en webb-URI (Uniform Resource Identifier) och lägger till token i den med hjälp av  `Bearer`-rubriken i HTTP. Den returneras sedan till dig. Du anropar `getTokenClearingCache`-API:et. Det kan verka underligt, men du använder bara det här anropet för att hämta en token från cacheminnet och säkerställa att den fortfarande är giltig. (`getToken`-anropen gör det åt dig genom att fråga ADAL.) Du använder den här koden i varje anrop. Därefter gör du dina andra aktivitetsmetoder.

Skriv `addTask`:

```
+(void) addTask:(samplesTaskItem*)task
         parent:(UIViewController*) parent
completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"POST"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSString *myString = [[NSString alloc] initWithData:requestBody encoding:NSUTF8StringEncoding];

            NSLog(@"Request was: %@", request);
            NSLog(@"Request body was: %@", myString);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

Det följer samma mönster, men introducerar även den sista metoden som du behöver implementera: `convertTaskToDictionary`. Detta tar din matris och gör den till ett katalogobjekt. Det här objektet muteras lättare till de frågeparametrar du behöver för att skicka till servern. Koden är enkel:

```
// Here we have some conversation helpers that allow us to parse passed items into dictionaries for URLEncoding later.

+(NSDictionary*) convertTaskToDictionary:(samplesTaskItem*)task
{
    NSMutableDictionary* dictionary = [[NSMutableDictionary alloc]init];

    if (task.itemName){
        [dictionary setValue:task.itemName forKey:@"task"];
    }

    return dictionary;
}

```

Skriv sedan `deleteTask`:

```
+(void) deleteTask:(samplesTaskItem*)task
            parent:(UIViewController*) parent
   completionBlock:(void (^) (bool, NSError* error)) completionBlock
{
    if (!loadedApplicationSettings)
    {
        [self readApplicationSettings];
    }

    SamplesApplicationData* data = [SamplesApplicationData getInstance];
    [self craftRequest:data.taskWebApiUrlString parent:parent completionHandler:^(NSMutableURLRequest* request, NSError* error){

        if (error != nil)
        {
            completionBlock(NO, error);
        }
        else
        {
            NSDictionary* taskInDictionaryFormat = [self convertTaskToDictionary:task];

            NSData* requestBody = [NSJSONSerialization dataWithJSONObject:taskInDictionaryFormat options:0 error:nil];

            [request setHTTPMethod:@"DELETE"];
            [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
            [request setHTTPBody:requestBody];

            NSLog(@"%@", request);

            NSOperationQueue *queue = [[NSOperationQueue alloc]init];

            [NSURLConnection sendAsynchronousRequest:request queue:queue completionHandler:^(NSURLResponse *response, NSData *data, NSError *error) {

                NSString* content = [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding];
                NSLog(@"%@", content);

                if (error == nil){

                    completionBlock(true, nil);
                }
                else
                {
                    completionBlock(false, error);
                }
            }];
        }
    }];
}
```

### Lägg till utloggning i programmet

Det sista du behöver göra är att implementera utloggning för programmet. Det är enkelt. Inuti `sampleWebApiConnector.m`-filen:

```
+(void) signOut
{
    [authContext.tokenCacheStore removeAll:nil];

    NSHTTPCookie *cookie;

    NSHTTPCookieStorage *storage = [NSHTTPCookieStorage sharedHTTPCookieStorage];
    for (cookie in [storage cookies])
    {
        [storage deleteCookie:cookie];
    }
}
```

## Kör exempelappen

Slutligen skapar och kör du appen i Xcode. Registrera dig eller logga in i appen och skapa aktiviteter för en inloggad användare. Logga ut och logga in igen som en annan användare, och skapa aktiviteter för den användaren.

Tänk på att uppgifterna lagras per användare i API:et eftersom det extraherar användarens identitet från den åtkomst-token som det tar emot.

Hela exemplet [tillhandahålls som en .zip-fil](https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS/archive/complete.zip) som du kan använda som referens. Du kan också klona den från GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-NativeClient-iOS```

## Nästa steg

Du kan nu gå vidare till mer avancerade B2C-ämnen. Du kan prova följande:

[Anropa ett webb-API för Node.js från en Node.js-webbapp]()

[Anpassa UX för en B2C-app]()



<!--HONumber=Jun16_HO2-->


