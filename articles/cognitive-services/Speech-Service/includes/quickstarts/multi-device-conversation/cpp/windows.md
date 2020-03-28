---
services: cognitive-services
author: ralphe
manager: cpoulain
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 01/15/2020
ms.author: ralphe
ms.openlocfilehash: e2c4530dbd5fa8b65e149e7467ce0f27adc15782
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "78925049"
---
## <a name="prerequisites"></a>Krav

Innan du börjar måste du:

> [!div class="checklist"]
> * [Skapa en Azure Speech-resurs](../../../../get-started.md)
> * [Konfigurera utvecklingsmiljön och skapa ett tomt projekt](../../../../quickstarts/setup-platform.md?tabs=windows)

## <a name="add-sample-code"></a>Lägg till exempelkod

1. Från Visual Studio, öppna källfilen **helloworld.cpp**.

1. Ersätt all kod med följande utdrag:

    ```cpp
    #include "pch.h"
    
    #define WIN32_LEAN_AND_MEAN
    #include <Windows.h>
    
    #include <iostream>
    #include <thread>
    #include <future>
    #include <string>
    #include <speechapi_cxx.h>
    
    using namespace std::chrono_literals;
    using namespace Microsoft::CognitiveServices::Speech;
    using namespace Microsoft::CognitiveServices::Speech::Audio;
    using namespace Microsoft::CognitiveServices::Speech::Transcription;
    
    // Create a promise we will set when the user presses Ctrl-C
    std::promise<bool> promise;
    // Create the future we will use to wait for the promise to be fulfilled
    std::future<bool> future = promise.get_future();
    
    void StartNewConversation()
    {
        // Set these
        std::string subscriptionKey("YourSubscriptionKey");

        // Replace below with your own region identifier from here: https://aka.ms/speech/sdkregion
        std::string region("YourServiceRegion");
        
        std::string speechLanguage("en-US");
    
        // Create the conversation object you'll need to manage the conversation
        auto speechConfig = SpeechConfig::FromSubscription(subscriptionKey, region);
        speechConfig->SetSpeechRecognitionLanguage(speechLanguage);
        auto conversation = Conversation::CreateConversationAsync(speechConfig).get();
    
        // Start the conversation so you and others can join
        conversation->StartConversationAsync().get();
    
        // Get the conversation ID. It will be up to your scenario to determine how this is shared with other participants.
        std::cout << "Created conversation: " << conversation->GetConversationId() << std::endl;
    
        // You can now call various commands to manage the room. For example:
        conversation->MuteAllParticipantsAsync().get();
    
        // Create the conversation translator you'll need to send audio, send IMs, and receive conversation events
        auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
        auto conversationTranslator = ConversationTranslator::FromConfig(audioConfig);
    
        // Add any event handlers
        conversationTranslator->SessionStarted += [](const SessionEventArgs& args)
        {
            std::cout << "Session started " << args.SessionId << std::endl;
        };
        conversationTranslator->SessionStopped += [](const SessionEventArgs& args)
        {
            std::cout << "Session stopped " << args.SessionId << std::endl;
        };
        conversationTranslator->Canceled += [](const ConversationTranslationCanceledEventArgs& args)
        {
            switch (args.Reason)
            {
                case CancellationReason::EndOfStream:
                    std::cout << "End of audio reached" << std::endl;
                    break;
    
                case CancellationReason::Error:
                    std::cout << "Canceled due to error. " << (long)args.ErrorCode << ": " << args.ErrorDetails << std::endl;
                    break;
            }
        };
        conversationTranslator->ConversationExpiration += [](const ConversationExpirationEventArgs& args)
        {
            std::cout << "Conversation will expire in " << args.ExpirationTime.count() << " minutes" << std::endl;
        };
        conversationTranslator->ParticipantsChanged += [](const ConversationParticipantsChangedEventArgs& args)
        {
            std::cout << "The following participant(s) have ";
            switch (args.Reason)
            {
                case ParticipantChangedReason::JoinedConversation:
                    std::cout << "joined";
                    break;
    
                case ParticipantChangedReason::LeftConversation:
                    std::cout << "left";
                    break;
    
                case ParticipantChangedReason::Updated:
                    std::cout << "been updated";
                    break;
            }
    
            std::cout << ":" << std::endl;
    
            for(std::shared_ptr<Participant> participant : args.Participants)
            {
                std::cout << "\t" << participant->DisplayName << std::endl;
            }
        };
        conversationTranslator->Transcribing += [](const ConversationTranslationEventArgs& args)
        {
            std::cout << "Received a partial transcription from " << args.Result->ParticipantId << ": " << args.Result->Text << std::endl;
            for (const auto& entry : args.Result->Translations)
            {
                std::cout << "\t" << entry.first << ": " << entry.second << std::endl;
            }
        };
        conversationTranslator->Transcribed += [](const ConversationTranslationEventArgs& args)
        {
            std::cout << "Received a transcription from " << args.Result->ParticipantId << ": " << args.Result->Text << std::endl;
            for (const auto& entry : args.Result->Translations)
            {
                std::cout << "\t" << entry.first << ": " << entry.second << std::endl;
            }
        };
        conversationTranslator->TextMessageReceived += [](const ConversationTranslationEventArgs& args)
        {
            std::cout << "Received an instant message from " << args.Result->ParticipantId << ": " << args.Result->Text << std::endl;
            for (const auto& entry : args.Result->Translations)
            {
                std::cout << "\t" << entry.first << ": " << entry.second << std::endl;
            }
        };
    
        // Join the conversation so you can start receiving events
        conversationTranslator->JoinConversationAsync(conversation, "Test Host").get();
    
        // Send an instant message
        conversationTranslator->SendTextMessageAsync("This is a short test message").get();
    
        // Start sending audio
        conversationTranslator->StartTranscribingAsync().get();
        std::cout << "Started transcribing. Press Ctrl + C to stop" << std::endl;
        future.get(); // wait for Ctrl - C to be pressed
    
        // Stop audio capture
        conversationTranslator->StopTranscribingAsync().get();
    
        // Leave the conversation. You will stop receiving events
        conversationTranslator->LeaveConversationAsync().get();
    
        // Once you are done, remember to delete the conversation.
        conversation->EndConversationAsync().get(); // You will not be able to rejoin after this
        conversation->DeleteConversationAsync().get(); // All participants still in the room will be ejected
    }
    
    int main()
    {
        // Register a handler for the Ctrl - C callback
        SetConsoleCtrlHandler(
            [](DWORD dwCtrlType) -> BOOL
            {
                if (dwCtrlType == CTRL_C_EVENT)
                {
                    // Signal that the user has pressed ctrl + C
                    promise.set_value(true);
                    return TRUE;
                }
    
                return FALSE;
            },
            TRUE);
    
        StartNewConversation();
    }
    ```

1. Ersätt strängen `YourSubscriptionKey` i samma fil med din prenumerationsnyckel.

1. Ersätt strängen `YourServiceRegion` med den [region](~/articles/cognitive-services/Speech-Service/regions.md) som är associerad med din prenumeration (till exempel `westus` för en kostnadsfri provprenumeration).

1. Välj Spara**alla** **på** > menyraden .

## <a name="build-and-run-the-application-to-create-a-new-conversation"></a>Skapa och kör programmet för att skapa en ny konversation

1. Välj **Bygg** > **bygglösning på** menyraden för att skapa programmet. Koden bör nu kompileras utan fel.

1. Välj **Felsökning** > **Starta felsökning** (eller tryck på <kbd>F5)</kbd>för att starta **helloworld-programmet.**

1. När meddelandet `Started transcribing` visas kan du börja tala. Du ser transkriptionerna visas när du talar
    - Om du delar konversationskoden med de andra och de går med i konversationen ser du även deras transkriptioner.

1. När du har talat klart trycker du på <kbd>Ctrl+C</kbd> på tangentbordet för att stoppa ljudinspelningen.

    > [!NOTE]
    > Du kan se ett meddelande från Visual `Exception thrown at 0x76EB90BF (KernelBase.dll) in helloworld.exe: 0x40010005: Control-C.` Studio om ett undantag som liknar: Du kan ignorera detta på ett säkert sätt.
    > <br/> <br/>
    > Tryck på <kbd>F5</kbd> för att fortsätta.

## <a name="build-and-run-the-application-to-join-an-existing-conversation"></a>Skapa och kör programmet för att delta i en befintlig konversation

1. Kopiera och klistra in följande funktion i din `int main()` **helloworld.cpp** strax före funktionen:

    ```cpp
    void JoinExistingConversation(const std::string& conversationId)
    {
        std::string speechLanguage("en-US");
    
        // You'll now need to create a ConversationTranslator to send audio, send IMs, and receive conversation events
        auto audioConfig = AudioConfig::FromDefaultMicrophoneInput();
        auto conversationTranslator = ConversationTranslator::FromConfig(audioConfig);
    
        // Attach event handlers here. For example:
        conversationTranslator->Transcribing += [](const ConversationTranslationEventArgs& args)
        {
            std::cout << "Received a partial transcription from " << args.Result->ParticipantId << ": " << args.Result->Text << std::endl;
            for (const auto& entry : args.Result->Translations)
            {
                std::cout << "\t" << entry.first << ": " << entry.second << std::endl;
            }
        };
        conversationTranslator->Transcribed += [](const ConversationTranslationEventArgs& args)
        {
            std::cout << "Received a transcription from " << args.Result->ParticipantId << ": " << args.Result->Text << std::endl;
            for (const auto& entry : args.Result->Translations)
            {
                std::cout << "\t" << entry.first << ": " << entry.second << std::endl;
            }
        };
        conversationTranslator->TextMessageReceived += [](const ConversationTranslationEventArgs& args)
        {
            std::cout << "Received an instant message from " << args.Result->ParticipantId << ": " << args.Result->Text << std::endl;
            for (const auto& entry : args.Result->Translations)
            {
                std::cout << "\t" << entry.first << ": " << entry.second << std::endl;
            }
        };
    
        // Join the conversation
        conversationTranslator->JoinConversationAsync(conversationId, "participant", speechLanguage).get();
    
        // Start sending audio
        conversationTranslator->StartTranscribingAsync().get();
        std::cout << "Started transcribing. Press Ctrl + C to stop" << std::endl;
        future.get(); // wait for Ctrl - C to be pressed
    
        // Stop audio capture
        conversationTranslator->StopTranscribingAsync().get();
    
        // Once you are done, leave the conversation
        conversationTranslator->LeaveConversationAsync().get();
    }
    ```

2. Ersätt `StartNewConversation();` i `int main()` din funktion med:

    ```cpp
    // Set this to the conversation you want to join
    JoinExistingConversation("YourConversationId");
    ```

[!INCLUDE [create-from-web](../create-from-web.md)]

4. Gå tillbaka till Visual `YourConversationId` Studio `int main()` och ersätt i din funktion med konversations-ID från föregående steg.

5. Välj **Bygg** > **bygglösning på** menyraden för att skapa programmet. Koden ska kompileras utan fel.

6. Välj **Felsökning** > **Starta felsökning** (eller tryck på <kbd>F5)</kbd>för att starta **helloworld-programmet.**

7. När meddelandet `Started transcribing` visas kan du börja tala. Du kommer att se transkriptionerna visas när du talar.
    - Om du går tillbaka till din webbläsare, bör du se dina transkriptioner visas där när du talar också.

8.  När du har talat klart trycker du på <kbd>Ctrl+C</kbd> för att stoppa ljudinspelningen och avslutar konversationen.

    > [!NOTE]
    > Du kan se ett meddelande från Visual `Exception thrown at 0x76EB90BF (KernelBase.dll) in helloworld.exe: 0x40010005: Control-C.` Studio om ett undantag som liknar: Du kan ignorera detta på ett säkert sätt.
    > <br/> <br/>
    > Tryck på <kbd>F5</kbd> för att fortsätta.

9. Gå tillbaka till webbläsaren och avsluta konversationen med hjälp av stängningsknappen i det övre högra hörnet.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]
