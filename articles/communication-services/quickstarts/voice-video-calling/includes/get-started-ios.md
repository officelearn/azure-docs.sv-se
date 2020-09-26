---
title: Snabb start – Lägg till samtal till en iOS-app med Azure Communication Services
description: I den här snabb starten får du lära dig hur du använder Azure Communication-tjänsterna som anropar klient biblioteket för iOS.
author: matthewrobertson
ms.author: marobert
ms.date: 07/24/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: bb0af58c9abc4fad701b1d0927f4c13e1fdcca49
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377008"
---
I den här snabb starten får du lära dig hur du startar ett samtal med Azure Communication Services som anropar klient bibliotek för iOS.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här självstudien behöver du följande förutsättningar:

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- En Mac som kör [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), tillsammans med ett giltigt certifikat för utvecklare som installerats i din nyckel Ring.
- En distribuerad kommunikations tjänst resurs. [Skapa en kommunikations tjänst resurs](../../create-communication-resource.md).
- En [token för användar åtkomst](../../access-tokens.md) för Azure Communication Service.

## <a name="setting-up"></a>Konfigurera

### <a name="creating-the-xcode-project"></a>Skapa Xcode-projektet

I Xcode skapar du ett nytt iOS-projekt och väljer app-mallen för **enskild vy** . I den här självstudien används [SwiftUI-ramverket](https://developer.apple.com/xcode/swiftui/), så du bör ange **språket** till **Swift** och **användar gränssnittet** för **SwiftUI**. Du kommer inte att skapa tester under den här snabb starten. Avmarkera **ta med tester**.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Skärm bild som visar det nya projekt fönstret i Xcode.":::

### <a name="install-the-package"></a>Installera paketet

Lägg till Azure Communication Services som anropar klient biblioteket och dess beroenden (AzureCore. Framework och AzureCommunication. Framework) i projektet.

> [!NOTE]
> Med versionen av AzureCommunicationCalling SDK hittar du ett bash-skript `BuildAzurePackages.sh` . Skriptet vid körning `sh ./BuildAzurePackages.sh` ger dig sökvägen till de skapade Ramverks paket som måste importeras i exempel programmet i nästa steg. Observera att du måste konfigurera Xcode kommando rads verktyg om du inte har gjort det innan du kör skriptet: starta Xcode, välj "inställningar-> platser". Välj din Xcode-version för kommando rads verktygen. **BuildAzurePackages.sh-skript fungerar bara med Xcode 11,5 och senare**

1. [Ladda ned](https://github.com/Azure/Communication/releases) Azure Communication Services som anropar klient biblioteket för iOS.
2. I Xcode klickar du på projekt filen till och väljer Bygg målet för att öppna redigeraren för projekt inställningar.
3. Under fliken **Allmänt** bläddrar du till avsnittet **ramverk, bibliotek och inbäddat innehåll** och klickar på ikonen **"+"** .
4. Längst ned till vänster i dialog rutan använder du List rutan Välj **Lägg till filer**, navigera till katalogen **AzureCommunicationCalling. Framework** i det zippade klient biblioteks paketet.
    1. Upprepa det sista steget för att lägga till **AzureCore. Framework** och **AzureCommunication. Framework**.
5. Öppna fliken **versions inställningar** i redigeraren för projekt inställningar och rulla till avsnittet **Sök sökvägar** . Lägg till en ny **sökväg för Sök vägar för ramverk** för katalogen som innehåller **AzureCommunicationCalling. Framework**.
    1. Lägg till en annan sökväg Sök vägar i Framework som pekar på den mapp som innehåller beroenden.

:::image type="content" source="../media/ios/xcode-framework-search-paths.png" alt-text="Skärm bild som visar uppdatering av Ramverks Sök vägar i XCode.":::

### <a name="request-access-to-the-microphone"></a>Begär åtkomst till mikrofonen

För att få åtkomst till enhetens mikrofon måste du uppdatera appens informations egenskaps lista med en `NSMicrophoneUsageDescription` . Du anger det associerade värdet till en `string` som ska ingå i dialog rutan som systemet använder för att begära åtkomst från användaren.

Högerklicka på `Info.plist` posten för projekt trädet och välj **öppna som**  >  **källkod**. Lägg till följande rader i avsnittet på den översta nivån `<dict>` och spara sedan filen.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Konfigurera app Framework

Öppna projektets **ContentView. SWIFT** -fil och Lägg till en `import` deklaration överst i filen för att importera `AzureCommunicationCalling library` . Dessutom `AVFoundation` behöver vi detta för att få en begäran om ljud behörighet i koden.

```swift
import AzureCommunicationCalling
import AVFoundation
```

Ersätt implementeringen av `ContentView` struct med vissa enkla UI-kontroller som gör det möjligt för en användare att initiera och avsluta ett samtal. Vi kommer att koppla affärs logik till dessa kontroller i den här snabb starten.

```swift
struct ContentView: View {
    @State var callee: String = ""
    @State var callClient: ACSCallClient?
    @State var callAgent: ACSCallAgent?
    @State var call: ACSCall?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Who would you like to call?", text: $callee)
                    Button(action: startCall) {
                        Text("Start Call")
                    }.disabled(callAgent == nil)
                    Button(action: endCall) {
                        Text("End Call")
                    }.disabled(call == nil)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
        }
    }

    func startCall() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                // Add start call logic
            }
        }
    }

    func endCall() {
        // Add end call logic
    }
}
```

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i Azure Communication Services som anropar klient biblioteket:

| Name                                  | Beskrivning                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ACSCallClient | CallClient är den huvudsakliga start punkten för det anropande klient biblioteket.|
| ACSCallAgent | CallAgent används för att starta och hantera samtal. |
| CommunicationUserCredential | CommunicationUserCredential används som token-autentiseringsuppgifter för att instansiera CallAgent.| 
| CommunicationIdentifier | CommunicationIdentifier används för att representera identiteten för användaren som kan vara något av följande: CommunicationUser/telefonnummer/CallingApplication. |

## <a name="authenticate-the-client"></a>Autentisera klienten

Initiera en `CallAgent` instans med en åtkomsttoken för användare som gör att vi kan ringa och ta emot samtal. Lägg till följande kod i `onAppear` återanropet i **ContentView. SWIFT**:

```swift
var userCredential: CommunicationUserCredential?
do {
    userCredential = try CommunicationUserCredential(token: "<USER ACCESS TOKEN>")
} catch {
    print("ERROR: It was not possible to create user credential.")
    return
}

self.callClient = ACSCallClient()

// Creates the call agent
self.callClient?.createCallAgent(userCredential) { (agent, error) in
    if error != nil {
        print("ERROR: It was not possible to create a call agent.")
    }

    if let agent = agent {
        self.callAgent = agent
        print("Call agent successfully created.")
    }
}
```

Du måste ersätta `<USER ACCESS TOKEN>` med en giltig åtkomsttoken för din resurs. Se dokumentationen för [användar åtkomst-token](../../access-tokens.md) om du inte redan har en tillgänglig token.

## <a name="start-a-call"></a>Starta ett anrop

`startCall`Metoden anges som den åtgärd som ska utföras när knappen *starta samtal* är knackad. Uppdatera implementeringen för att starta ett samtal med `ASACallAgent` :

```swift
func startCall()
{
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            // start call logic
            let callees:[CommunicationIdentifier] = [CommunicationUser(identifier: self.callee)]
            self.call = self.callAgent?.call(callees, options: ACSStartCallOptions())
        }
    }
}
```

Du kan också använda egenskaperna i `ACSStartCallOptions` för att ange inledande alternativ för anropet (d.v.s. det tillåter att anropet startar med mikrofonen avstängd).

## <a name="end-a-call"></a>Avsluta ett anrop

Implementera `endCall` metoden för att avsluta det aktuella anropet när knappen *Avsluta samtal* är knackad.

```swift
func endCall()
{    
    self.call!.hangup(ACSHangupOptions()) { (error) in
        if (error != nil) {
            print("ERROR: It was not possible to hangup the call.")
        }
    }
}
```

## <a name="run-the-code"></a>Kör koden

Du kan skapa och köra din app på iOS-simulatorn genom att välja **produkt**  >  **körning** eller genom att använda kortkommandot (&#8984;-R).

:::image type="content" source="../media/ios/quick-start-make-call.png" alt-text="Det slutliga utseendet och känslan av snabb starts appen":::

Du kan göra ett utgående VOIP-anrop genom att ange ett användar-ID i fältet text och sedan trycka på knappen **starta samtal** . `8:echo123`Genom att anropa ansluter du med en eko robot är det bra för att komma igång och kontrol lera att ljud enheterna fungerar. 

> [!NOTE]
> Första gången du gör ett samtal uppmanas du att få åtkomst till mikrofonen. I ett produktions program bör du använda API- `AVAudioSession` [kontrollen för att kontrol lera behörighetens status och på](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) ett smidigt sätt uppdatera programmets beteende när behörighet inte beviljas.

## <a name="sample-code"></a>Exempelkod

Du kan ladda ned exempel appen från [GitHub](https://github.com/Azure/Communication/tree/master/samples/Add%20Voice%20Calling/iOS/Swift)
