---
title: Skicka push-meddelanden till iOS med Azure Notification Hubs | Microsoft Docs
description: "I den här självstudiekursen beskrivs hur du använder Azure Notification Hubs för att skicka push-meddelanden till en iOS-app."
services: notification-hubs
documentationcenter: ios
keywords: push-meddelande, push-meddelanden, push-meddelanden i ios
author: ysxu
manager: erikre
editor: 
ms.assetid: b7fcd916-8db8-41a6-ae88-fc02d57cb914
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: hero-article
ms.date: 10/03/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 968e24b0441575be7ef17aac8ffaddb8fd16d3c6


---
# <a name="sending-push-notifications-to-ios-with-azure-notification-hubs"></a>Skicka push-meddelanden till iOS med Azure Notification Hubs
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Översikt
> [!NOTE]
> Du måste ha ett aktivt Azure-konto för att slutföra den här kursen. Om du inte har något konto kan skapa du ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started).
> 
> 

I den här självstudiekursen kommer du att få lära dig hur du använder Azure Notification Hubs för att skicka push-meddelanden till en iOS-app. Du skapar en tom iOS-app som tar emot push-meddelanden med hjälp av [Apple Push Notification Service (APNS)](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html). 

När du är klar kan du använda meddelandehubben för att sända push-meddelanden till alla enheter som kör appen.

## <a name="before-you-begin"></a>Innan du börjar
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Den slutförda koden för den här självstudiekursen hittar du [på GitHub](https://github.com/Azure/azure-notificationhubs-samples/tree/master/iOS/GetStartedNH/GetStarted). 

## <a name="prerequisites"></a>Krav
Följande krävs för den här självstudiekursen:

* [Mobile Services iOS SDK version 1.2.4]
* Den senaste versionen av [Xcode]
* En enhet som är kompatibel med iOS 8 (eller senare versioner)
* Medlemskap i [Apple Developer Program](https://developer.apple.com/programs/).
  
  > [!NOTE]
  > På grund av konfigurationskrav för push-meddelanden måste du distribuera och testa push-meddelanden på en fysisk iOS-enhet (iPhone eller iPad) i stället för på iOS-simulatorn.
  > 
  > 

Du måste slutföra den här självstudiekursen innan du börjar någon annan kurs om Notification Hubs för iOS-appar.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>Konfigurera din Notification Hub för att skicka push-meddelanden till iOS
I det här avsnittet går vi igenom hur du skapar en ny meddelandehubb och konfigurerar autentisering med APNS genom att använda **.p12**-pushcertifikatet som du skapade. Om du vill använda en meddelandehubb som du redan har skapat går du vidare till steg 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li>

<p>Klicka på knappen <b>Notification Services</b> i bladet <b>Inställningar</b> och välj sedan <b>Apple (APNS)</b>. Klicka på <b>Överför certifikat</b> och välj den <b>.p12</b> -fil som du exporterade tidigare. Kontrollera också att du anger rätt lösenord.</p>

<p>Välj <b>Sandbox</b> eftersom det är detta läge som används för utveckling. Använd bara <b>Produktion</b> om du vill skicka push-meddelanden till användare som har köpt din app i butiken.</p>
</li>
</ol>
&emsp;&emsp;![Konfigurera APNS i Azure Portal](./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png)

&emsp;&emsp;![Konfigurera APNS-certifikat i Azure-portalen](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

Din meddelandehubb har nu konfigurerats för att fungera med APNS och du har anslutningssträngar för att registrera din app och skicka push-meddelanden.

## <a name="connect-your-ios-app-to-notification-hubs"></a>Anslut iOS-appen till Notification Hubs
1. Skapa ett nytt iOS-projekt i Xcode och välj mallen **Program enkel vy**.
   
       ![Xcode - Single View Application][8]
2. När du anger alternativ för ditt nya projekt, ska du använda samma **produktnamn** och **organisations-ID** som du använde när du tidigare gjorde inställningarna för ID:t för programpaket på Apple Developer-portalen.
   
    ![Xcode – projektalternativ][11]
3. Under **Mål** klickar du på ditt projektnamn och sedan på fliken **Versionsinställningar**. Expandera fliken och expandera **Identitet för kodsignering** och ställ sedan in din identitet för kodsignering under **Felsökning**. Växla **nivå** från **Grundläggande** till **Alla** och ställ in **Etableringsprofil** till den etableringsprofil som du skapade tidigare.
   
    Om du inte ser den nya etableringsprofil som du skapade i Xcode, kan du försöka uppdatera profilerna för din signeringsidentitet. Klicka på **Xcode** på menyraden, sedan på **Inställningar** och på fliken **Konto**. Därefter klickar du på knappen **Visa detaljer** och sedan på din signeringsidentitet. Slutligen klickar du på uppdateringsknappen i det nedre högra hörnet.
   
       ![Xcode - provisioning profile][9]
4. Hämta [Mobile Services iOS SDK version 1.2.4] och packa upp filen. Högerklicka på ditt projekt i Xcode och klicka sedan på alternativet **Lägg till filer i** för att lägga till mappen **WindowsAzureMessaging.framework** till ditt Xcode-projekt. Välj **Kopiera objekt vid behov** och klicka sedan på **Lägg till**.
   
   > [!NOTE]
   > Notification Hubs SDK stöder för närvarande inte Bitcode på Xcode 7.  Du måste ställa in **Aktivera Bitcode** på **Nej** under **Versionsalternativ** för projektet.
   > 
   > 
   
       ![Unzip Azure SDK][10]
5. Lägg till en ny rubrikfil i projektet med namnet `HubInfo.h`. Den här filen ska innehålla konstanterna för din meddelandehubb.  Lägg till följande definitioner och ersätt platshållarnas textsträngar med ditt *hubbnamn* och den *DefaultListenSharedAccessSignature* som du skrivit ned tidigare.
   
        #ifndef HubInfo_h
        #define HubInfo_h
   
            #define HUBNAME @"<Enter the name of your hub>"
            #define HUBLISTENACCESS @"<Enter your DefaultListenSharedAccess connection string"
   
        #endif /* HubInfo_h */
6. Öppna filen `AppDelegate.h` och lägg till följande importdirektiv:
   
         #import <WindowsAzureMessaging/WindowsAzureMessaging.h> 
         #import "HubInfo.h"
7. I din `AppDelegate.m file` lägger du till följande kod i den `didFinishLaunchingWithOptions`-metod som är baserad på din iOS-version. Den här koden registrerar din enhetshantering med APNS:
   
    För iOS 8:
   
         UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound |
                                                UIUserNotificationTypeAlert | UIUserNotificationTypeBadge categories:nil];
   
        [[UIApplication sharedApplication] registerUserNotificationSettings:settings];
        [[UIApplication sharedApplication] registerForRemoteNotifications];
   
    För iOS-versioner äldre än 8:
   
         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
8. Lägg till följande metoder i samma fil. Den här koden ansluter till meddelandehubben genom att använda den uppkopplingsinformation som du angav i HubInfo.h. Den lämnar sedan över enhetstoken till meddelandehubben för att den ska kunna skicka meddelanden:
   
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
            SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:HUBLISTENACCESS
                                        notificationHubPath:HUBNAME];
   
            [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
                if (error != nil) {
                    NSLog(@"Error registering for notifications: %@", error);
                }
                else {
                    [self MessageBox:@"Registration Status" message:@"Registered"];
                }
            }];
        }
   
        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }
9. Lägg till följande metod i samma fil för att visa en **UIAlert** om meddelandet tas emot medan appen är aktiv:

        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
        }

1. Skapa och kör appen på din enhet för att kontrollera att det inte finns några fel.

## <a name="send-test-push-notifications"></a>Skicka test-push-meddelanden
Du kan testa att ta emot meddelanden i appen genom att skicka push-meddelanden i [Azure Portal]. Du går via avsnittet **Felsökning** i hubbladet (använd alternativet *Prova att skicka*).

![Azure Portal – Prova att skicka][30]

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="optional-send-push-notifications-from-the-app"></a>(Valfritt) Skicka push-meddelanden från appen
> [!IMPORTANT]
> Det här exemplet för att skicka meddelanden från klientappen tillhandahålls endast i studiesyfte. Eftersom detta kräver att `DefaultFullSharedAccessSignature` ska finnas i klientappen, utsätter den din meddelandehubb för risken att en användare kan komma åt den och skicka obehöriga meddelanden från den till klienterna.
> 
> 

Om du vill skicka push-meddelanden från en app, hittar du ett exempel på hur du gör detta med hjälp av REST-gränssnittet i detta avsnitt.

1. I Xcode öppnar du `Main.storyboard` och lägger till följande UI-komponenter från objektbiblioteket för att användaren ska kunna skicka push-meddelanden i appen:
   
   * En etikett utan etikettext. Den kommer att användas för att rapportera fel i samband med att meddelanden skickas. Egenskapen **Rader** ska ställas in på **0** så att storleken automatiskt begränsas till de högra och vänstra marginalerna samt vyns övre del.
   * Ett textfält med text för **platshållare** med texten **Ange meddelande**. Begränsa fältet precis under etiketten som visas nedan. Ställ in View Controller som uttagsdelegat.
   * En knapp med titeln **Skicka meddelande**, begränsad precis under textfältet och i mitten ur horisontal synvinkel.
     
     Vyn bör se ut så här:
     
     ![Xcode-designern][32]
2. [Lägg till uttag](https://developer.apple.com/library/ios/recipes/xcode_help-IB_connections/chapters/CreatingOutlet.html) för den etikett och det textfält som är anslutna till din vy och uppdatera din `interface`-definition så att den stöder `UITextFieldDelegate` och `NSXMLParserDelegate`. Lägg till de tre egenskapsdeklarationer som visas nedan för att hjälpa till att stödja anropet till REST-API:et och tolka svaret.
   
    Filen ViewController.h bör se ut så här:
   
        #import <UIKit/UIKit.h>
   
        @interface ViewController : UIViewController <UITextFieldDelegate, NSXMLParserDelegate>
        {
            NSXMLParser *xmlParser;
        }
   
        // Make sure these outlets are connected to your UI by ctrl+dragging
        @property (weak, nonatomic) IBOutlet UITextField *notificationMessage;
        @property (weak, nonatomic) IBOutlet UILabel *sendResults;
   
        @property (copy, nonatomic) NSString *statusResult;
        @property (copy, nonatomic) NSString *currentElement;
   
        @end
3. Öppna `HubInfo.h` och lägg till följande konstanter. De ska användas för att skicka meddelanden till hubben. Ersätt platshållarens teckensträng med den faktiska anslutningssträngen *DefaultFullSharedAccessSignature*.
   
        #define API_VERSION @"?api-version=2015-01"
        #define HUBFULLACCESS @"<Enter Your DefaultFullSharedAccess Connection string>"
4. Lägg till följande `#import`-uttryck i `ViewController.h`-filen:
   
        #import <CommonCrypto/CommonHMAC.h>
        #import "HubInfo.h"
5. Lägg till följande kod i gränssnittsimplementeringen i `ViewController.m`. Den här koden kommer att parsa anslutningssträngen *DefaultFullSharedAccessSignature*. Enligt [REST API-referensen](http://msdn.microsoft.com/library/azure/dn495627.aspx) kommer denna parsade information att användas för att generera ett SaS-token för begärandehuvudet **Autentisering**.
   
        NSString *HubEndpoint;
        NSString *HubSasKeyName;
        NSString *HubSasKeyValue;
   
        -(void)ParseConnectionString
        {
            NSArray *parts = [HUBFULLACCESS componentsSeparatedByString:@";"];
            NSString *part;
   
            if ([parts count] != 3)
            {
                NSException* parseException = [NSException exceptionWithName:@"ConnectionStringParseException"
                    reason:@"Invalid full shared access connection string" userInfo:nil];
   
                @throw parseException;
            }
   
            for (part in parts)
            {
                if ([part hasPrefix:@"Endpoint"])
                {
                    HubEndpoint = [NSString stringWithFormat:@"https%@",[part substringFromIndex:11]];
                }
                else if ([part hasPrefix:@"SharedAccessKeyName"])
                {
                    HubSasKeyName = [part substringFromIndex:20];
                }
                else if ([part hasPrefix:@"SharedAccessKey"])
                {
                    HubSasKeyValue = [part substringFromIndex:16];
                }
            }
        }
6. Uppdatera `viewDidLoad`-metoden för att parsa anslutningssträngen när vyn läses in i `ViewController.m`. Lägg även till verktygsmetoderna, som visas nedan, till implementeringsgränssnittet.  

        - (void)viewDidLoad
        {
            [super viewDidLoad];
            [self ParseConnectionString];
            [_notificationMessage setDelegate:self];
        }

        -(NSString *)CF_URLEncodedString:(NSString *)inputString
        {
           return (__bridge NSString *)CFURLCreateStringByAddingPercentEscapes(NULL, (CFStringRef)inputString,
                NULL, (CFStringRef)@"!*'();:@&=+$,/?%#[]", kCFStringEncodingUTF8);
        }

        -(void)MessageBox:(NSString *)title message:(NSString *)messageText
        {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
                cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        }





1. I `ViewController.m` ska du lägga till följande kod i implementeringsgränssnittet för att generera den SaS-autentiseringstoken som ska anges i huvudet **Autentisering**, på samma sätt som anges i [REST-API-referensen](http://msdn.microsoft.com/library/azure/dn495627.aspx).
   
        -(NSString*) generateSasToken:(NSString*)uri
        {
            NSString *targetUri;
            NSString* utf8LowercasedUri = NULL;
            NSString *signature = NULL;
            NSString *token = NULL;
   
            @try
            {
                // Add expiration
                uri = [uri lowercaseString];
                utf8LowercasedUri = [self CF_URLEncodedString:uri];
                targetUri = [utf8LowercasedUri lowercaseString];
                NSTimeInterval expiresOnDate = [[NSDate date] timeIntervalSince1970];
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60;
                UInt64 expires = trunc(expiresOnDate);
                NSString* toSign = [NSString stringWithFormat:@"%@\n%qu", targetUri, expires];
   
                // Get an hmac_sha1 Mac instance and initialize with the signing key
                const char *cKey  = [HubSasKeyValue cStringUsingEncoding:NSUTF8StringEncoding];
                const char *cData = [toSign cStringUsingEncoding:NSUTF8StringEncoding];
                unsigned char cHMAC[CC_SHA256_DIGEST_LENGTH];
                CCHmac(kCCHmacAlgSHA256, cKey, strlen(cKey), cData, strlen(cData), cHMAC);
                NSData *rawHmac = [[NSData alloc] initWithBytes:cHMAC length:sizeof(cHMAC)];
                signature = [self CF_URLEncodedString:[rawHmac base64EncodedStringWithOptions:0]];
   
                // Construct authorization token string
                token = [NSString stringWithFormat:@"SharedAccessSignature sig=%@&se=%qu&skn=%@&sr=%@",
                    signature, expires, HubSasKeyName, targetUri];
            }
            @catch (NSException *exception)
            {
                [self MessageBox:@"Exception Generating SaS Token" message:[exception reason]];
            }
            @finally
            {
                if (utf8LowercasedUri != NULL)
                    CFRelease((CFStringRef)utf8LowercasedUri);
                if (signature != NULL)
                CFRelease((CFStringRef)signature);
            }
   
            return token;
        }
2. CTRL-dra från knappen **Skicka meddelande** till `ViewController.m` för att lägga till en åtgärd med namnet **SendNotificationMessage** för händelsen **Touch Down**. Uppdatera metoden med följande kod för att skicka meddelandet med hjälp av REST-API:et.
   
        - (IBAction)SendNotificationMessage:(id)sender
        {
            self.sendResults.text = @"";
            [self SendNotificationRESTAPI];
        }
   
        - (void)SendNotificationRESTAPI
        {
            NSURLSession* session = [NSURLSession
                             sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration]
                             delegate:nil delegateQueue:nil];
   
            // Apple Notification format of the notification message
            NSString *json = [NSString stringWithFormat:@"{\"aps\":{\"alert\":\"%@\"}}",
                                self.notificationMessage.text];
   
            // Construct the message's REST endpoint
            NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                                HUBNAME, API_VERSION]];
   
            // Generate the token to be used in the authorization header
            NSString* authorizationToken = [self generateSasToken:[url absoluteString]];
   
            //Create the request to add the APNs notification message to the hub
            NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
            [request setHTTPMethod:@"POST"];
            [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];
   
            // Signify Apple notification format
            [request setValue:@"apple" forHTTPHeaderField:@"ServiceBusNotification-Format"];
   
            //Authenticate the notification message POST request with the SaS token
            [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];
   
            //Add the notification message body
            [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
   
            // Send the REST request
            NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
                NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || (httpResponse.statusCode != 200 && httpResponse.statusCode != 201))
                {
                    NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                }
                if (data != NULL)
                {
                    xmlParser = [[NSXMLParser alloc] initWithData:data];
                    [xmlParser setDelegate:self];
                       [xmlParser parse];
                }
            }];
            [dataTask resume];
        }
3. I `ViewController.m` lägger du till följande delegatmetod för att stödja stängning av tangentbordet för textfältet. CTRL-dra från textfältet till ikonen View Controller i gränssnittsdesignern för att ställa in vykontrollanten som uttagsdelegat.
   
        //===[ Implement UITextFieldDelegate methods ]===
   
        -(BOOL)textFieldShouldReturn:(UITextField *)textField
        {
            [textField resignFirstResponder];
            return YES;
        }
4. I `ViewController.m` lägger du till följande delegatmetoder för att stödja parsning av svaret med hjälp av `NSXMLParser`.
   
       //===[ Implement NSXMLParserDelegate methods ]===
   
       -(void)parserDidStartDocument:(NSXMLParser *)parser
       {
           self.statusResult = @"";
       }
   
       -(void)parser:(NSXMLParser *)parser didStartElement:(NSString *)elementName
           namespaceURI:(NSString *)namespaceURI qualifiedName:(NSString *)qName
           attributes:(NSDictionary *)attributeDict
       {
           NSString * element = [elementName lowercaseString];
           NSLog(@"*** New element parsed : %@ ***",element);
   
           if ([element isEqualToString:@"code"] | [element isEqualToString:@"detail"])
           {
               self.currentElement = element;
           }
       }
   
       -(void) parser:(NSXMLParser *)parser foundCharacters:(NSString *)parsedString
       {
           self.statusResult = [self.statusResult stringByAppendingString:
               [NSString stringWithFormat:@"%@ : %@\n", self.currentElement, parsedString]];
       }
   
       -(void)parserDidEndDocument:(NSXMLParser *)parser
       {
           // Set the status label text on the UI thread
           dispatch_async(dispatch_get_main_queue(),
           ^{
               [self.sendResults setText:self.statusResult];
           });
       }
5. Skapa projektet och kontrollera att det inte finns några fel.

> [!NOTE]
> Om du får ett build-fel i Xcode7 om Bitcode-support, ska du ändra **Versionsinställningar** > **Aktivera Bitcode (ENABLE_BITCODE)** till **NEJ** i Xcode. Notification Hubs SDK stöder för närvarande inte Bitcode. 
> 
> 

Du hittar alla möjliga nyttolaster för meddelanden i Apples [Programmeringsguide för lokala meddelanden och push-meddelanden].

## <a name="checking-if-your-app-can-receive-push-notifications"></a>Kontrollera om din app kan ta emot push-meddelanden
Om du vill testa push-meddelanden på iOS måste du distribuera appen till en fysisk iOS-enhet. Du kan inte skicka push-meddelanden för Apple genom att använda iOS-simulatorn.

1. Kör appen och verifiera att registreringen kan genomföras. Tryck sedan på **OK**.
   
    ![Registreringstest för push-meddelanden i iOS-appar][33]
2. Du kan skicka ett test-push-meddelande från [Azure Portal], enligt beskrivningen ovan. Om du har lagt till kod för att skicka push-meddelanden i appen, pekar du i textfältet för att ange ett meddelande. Tryck sedan på knappen **Skicka** på tangentbordet eller på knappen **Skicka meddelande** i vyn för att skicka meddelandet.
   
    ![Test av att skicka push-meddelanden i iOS-appar][34]
3. Push-meddelandena skickas till alla enheter som registrerats för att ta emot meddelanden från en viss Notification Hub.
   
    ![Test av att ta emot push-meddelanden i iOS-appar][35]

## <a name="next-steps"></a>Nästa steg
I det här enkla exemplet skickade du push-meddelanden till alla dina registrerade iOS-enheter. Vi rekommenderar att du som ett nästa steg i din utbildning går vidare till självstudiekursen [Meddela användare för iOS med .NET-serverdel i Azure Notification Hubs]. Den kursen vägleder dig genom proceduren för att skapa en serverdel och skicka push-meddelanden med taggar. 

Om du vill dela in användarna efter intressegrupper, kan du även gå vidare till självstudiekursen [Använda Notification Hubs för att skicka de senaste nyheterna]. 

Allmän information om Notification Hubs finns i [Riktlinjer om Notification Hubs].

<!-- Images. -->

[6]: ./media/notification-hubs-ios-get-started/notification-hubs-configure-ios.png
[8]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app.png
[9]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app2.png
[10]: ./media/notification-hubs-ios-get-started/notification-hubs-create-ios-app3.png
[11]: ./media/notification-hubs-ios-get-started/notification-hubs-xcode-product-name.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png

[31]: ./media/notification-hubs-ios-get-started/notification-hubs-ios-ui.png
[32]: ./media/notification-hubs-ios-get-started/notification-hubs-storyboard-view.png
[33]: ./media/notification-hubs-ios-get-started/notification-hubs-test1.png
[34]: ./media/notification-hubs-ios-get-started/notification-hubs-test2.png
[35]: ./media/notification-hubs-ios-get-started/notification-hubs-test3.png



<!-- URLs. -->
[Mobile Services iOS SDK version 1.2.4]: http://aka.ms/kymw2g
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Skicka en app-sida]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mina program]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK för Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Komma igång med Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Klassisk Azure-portal]: https://manage.windowsazure.com/
[Riktlinjer om Notification Hubs]: http://msdn.microsoft.com/library/jj927170.aspx
[Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Etableringsportal för iOS]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Komma igång med push-meddelanden i Mobile Services]: ../mobile-services-javascript-backend-ios-get-started-push.md
[Meddela användare för iOS med .NET-serverdel i Azure Notification Hubs]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Använda Notification Hubs för att skicka de senaste nyheterna]: notification-hubs-ios-xplat-segmented-apns-push-notification.md

[Programmeringsguide för lokala meddelanden och push-meddelanden]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Azure Portal]: https://portal.azure.com



<!--HONumber=Nov16_HO2-->


