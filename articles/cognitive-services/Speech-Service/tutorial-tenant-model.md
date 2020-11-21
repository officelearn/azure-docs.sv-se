---
title: Skapa en klient modell (för hands version) – tal tjänst
titleSuffix: Azure Cognitive Services
description: Generera automatiskt en säker, kompatibel klient modell (Custom Speech med Microsoft 365 data) som använder dina Microsoft 365 data för att leverera optimal tal igenkänning för företagsspecifika villkor.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 06/25/2020
ms.author: erhopf
ms.custom: devx-track-csharp
ms.openlocfilehash: 48cde51ee9941f705aa848d121c419a8f0c9ad1a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2020
ms.locfileid: "95013704"
---
# <a name="tutorial-create-a-tenant-model-preview"></a>Självstudie: skapa en klient modell (förhands granskning)

Klient modell (Custom Speech med Microsoft 365 data) är en valbar tjänst för Microsoft 365 företags kunder som automatiskt genererar en anpassad tal igenkännings modell från din organisations Microsoft 365 data. Modellen är optimerad för tekniska termer, jargong och personers namn, allt på ett säkert och kompatibelt sätt.

> [!IMPORTANT]
> Om din organisation registrerar sig genom att använda klient modell tjänsten kan tal tjänsten komma åt din organisations språk modell. Modellen genereras från Microsoft 365 offentliga grupp-e-postmeddelanden och dokument som kan ses av alla i din organisation. Din organisations administratör kan aktivera eller inaktivera användningen av språk modellen för hela organisationen från administrations portalen.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Registrera i klient organisations modellen med hjälp av Microsoft 365 administrations Center
> * Hämta en nyckel för tal prenumeration
> * Skapa en klient modell
> * Distribuera en klient modell
> * Använd din klient modell med talet SDK

## <a name="enroll-in-the-tenant-model-service"></a>Registrera i klient modell tjänsten

Innan du kan distribuera din klient modell måste du vara registrerad i klient modell tjänsten. Registreringen har slutförts i Microsoft 365 administrations Center och kan bara utföras av administratören.

1. Logga in på [Administrationscenter för Microsoft 365](https://admin.microsoft.com).

1. Välj **Inställningar** i den vänstra rutan och välj sedan **Inställningar** på den kapslade menyn och välj sedan **Azure Speech Services** från huvud fönstret.

   ![Fönstret "tjänster & tillägg"](media/tenant-language-model/tenant-language-model-enrollment.png)

1. Markera kryss rutan **Tillåt att språk modellen för hela organisationen** är markerad och välj sedan **Spara ändringar**.

   ![Fönstret Azure Speech Services](media/tenant-language-model/tenant-language-model-enrollment-2.png)

Så här stänger du av klient modells instansen:
1. Upprepa föregående steg 1 och 2.
1. Avmarkera kryss rutan **Tillåt att språk modellen för hela organisationen** är markerad och välj sedan **Spara ändringar**.

## <a name="get-a-speech-subscription-key"></a>Hämta en nyckel för tal prenumeration

Om du vill använda din klient modell med tal-SDK behöver du en tal resurs och dess tillhör ande prenumerations nyckel.

1. Logga in på [Azure-portalen](https://aka.ms/azureportal).
1. Välj **Skapa en resurs**.
1. Skriv **tal** i rutan **Sök** .
1. Välj **tal** i listan resultat och välj sedan **skapa**.
1. Följ anvisningarna på skärmen för att skapa din resurs. Se till att:
   * **Platsen** anges till antingen **öster** eller **väst**.
   * **Pris nivån** är inställd på **S0**.
1. Välj **Skapa**.

   Efter några minuter skapas din resurs. Prenumerations nyckeln är tillgänglig i **översikts** avsnittet för din resurs.

## <a name="create-a-language-model"></a>Skapa en språk modell

När din administratör har aktiverat klient organisations modell för din organisation kan du skapa en språk modell som baseras på dina Microsoft 365 data.

1. Logga in på [tal Studio](https://speech.microsoft.com/).
1. Längst upp till höger väljer du **Inställningar** (kugg hjuls ikon) och väljer sedan **Inställningar för klient organisations modell**.

   ![Länken "inställningar för klient organisations modell"](media/tenant-language-model/tenant-language-settings.png)

   Tal Studio visar ett meddelande som gör att du vet om du är kvalificerad att skapa en klient organisations modell.

   > [!NOTE]
   > Företags kunder i Nordamerika är berättigade att skapa en klient modell (engelska). Den här funktionen är inte tillgänglig om du är Customer Lockbox, kund nyckel eller Office 365 myndighets kund. För att avgöra om du är en Customer Lockbox-eller kund nyckel kund, se:
   > * [Customer Lockbox](/microsoft-365/compliance/customer-lockbox-requests)
   > * [Kund nyckel](/microsoft-365/compliance/customer-key-overview)
   > * [Office 365-myndigheter](https://www.microsoft.com/microsoft-365/government)

1. Välj **Anmäl dig**.

   När din klient modell är klar får du ett e-postmeddelande med en bekräftelse med ytterligare instruktioner.

## <a name="deploy-your-tenant-model"></a>Distribuera din klient modell

När din klient modell instans är klar distribuerar du den genom att göra följande:

1. I bekräftelse-e-postmeddelandet väljer du knappen **Visa modell** . Eller logga in på [tal Studio](https://speech.microsoft.com/).
1. Längst upp till höger väljer du **Inställningar** (kugg hjuls ikon) och väljer sedan **Inställningar för klient organisations modell**.

   ![Länken "inställningar för klient organisations modell"](media/tenant-language-model/tenant-language-settings.png)

1. Välj **Distribuera**.

   När din modell har distribuerats ändras statusen till *distribuerad*.

## <a name="use-your-tenant-model-with-the-speech-sdk"></a>Använd din klient modell med talet SDK

Nu när du har distribuerat din modell kan du använda den med talet SDK. I det här avsnittet använder du exempel kod för att anropa tal tjänsten med hjälp av Azure Active Directory (Azure AD)-autentisering.

Nu ska vi titta på koden som du ska använda för att anropa talet SDK i C#. I det här exemplet ska du utföra tal igenkänning genom att använda din klient modell. Den här guiden förutsätter att din plattform redan har kon figurer ATS. Om du behöver installations hjälpen, se [snabb start: identifiera tal, C# (.net Core)](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnetcore).

Kopiera den här koden till projektet:

```csharp
namespace PrincetonSROnly.FrontEnd.Samples
{
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Text.RegularExpressions;
    using System.Threading.Tasks;
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json.Linq;

    // ServiceApplicationId is a fixed value. No need to change it.

    public class TenantLMSample
    {
        private const string EndpointUriArgName = "EndpointUri";
        private const string SubscriptionKeyArgName = "SubscriptionKey";
        private const string UsernameArgName = "Username";
        private const string PasswordArgName = "Password";
        private const string ClientApplicationId = "f87bc118-1576-4097-93c9-dbf8f45ef0dd";
        private const string ServiceApplicationId = "18301695-f99d-4cae-9618-6901d4bdc7be";

        public static async Task ContinuousRecognitionWithTenantLMAsync(Uri endpointUri, string subscriptionKey, string audioDirPath, string username, string password)
        {
            var config = SpeechConfig.FromEndpoint(endpointUri, subscriptionKey);

            // Passing client specific information for obtaining LM
            if (string.IsNullOrEmpty(username) || string.IsNullOrEmpty(password))
            {
                config.AuthorizationToken = await AcquireAuthTokenWithInteractiveLoginAsync().ConfigureAwait(false);
            }
            else
            {
                config.AuthorizationToken = await AcquireAuthTokenWithUsernamePasswordAsync(username, password).ConfigureAwait(false);
            }

            var stopRecognition = new TaskCompletionSource<int>();

            // Creates a speech recognizer using file as audio input.
            // Replace with your own audio file name.
            using (var audioInput = AudioConfig.FromWavFileInput(audioDirPath))
            {
                using (var recognizer = new SpeechRecognizer(config, audioInput))
                {
                    // Subscribes to events
                    recognizer.Recognizing += (s, e) =>
                    {
                        Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
                    };

                    recognizer.Recognized += (s, e) =>
                    {
                        if (e.Result.Reason == ResultReason.RecognizedSpeech)
                        {
                            Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
                        }
                        else if (e.Result.Reason == ResultReason.NoMatch)
                        {
                            Console.WriteLine($"NOMATCH: Speech could not be recognized.");
                        }
                    };

                    recognizer.Canceled += (s, e) =>
                    {
                        Console.WriteLine($"CANCELED: Reason={e.Reason}");
                        if (e.Reason == CancellationReason.Error)
                        {
                            Exception exp = new Exception(string.Format("Error Code: {0}\nError Details{1}\nIs your subscription information updated?", e.ErrorCode, e.ErrorDetails));
                            throw exp;
                        }

                        stopRecognition.TrySetResult(0);
                    };

                    recognizer.SessionStarted += (s, e) =>
                    {
                        Console.WriteLine("\n    Session started event.");
                    };

                    recognizer.SessionStopped += (s, e) =>
                    {
                        Console.WriteLine("\n    Session stopped event.");
                        Console.WriteLine("\nStop recognition.");
                        stopRecognition.TrySetResult(0);
                    };

                    // Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
                    await recognizer.StartContinuousRecognitionAsync().ConfigureAwait(false);

                    // Waits for completion.
                    // Use Task.WaitAny to keep the task rooted.
                    Task.WaitAny(new[] { stopRecognition.Task });

                    // Stops recognition.
                    await recognizer.StopContinuousRecognitionAsync().ConfigureAwait(false);
                }
            }
        }

        public static void Main(string[] args)
        {
            var arguments = new Dictionary<string, string>();
            string inputArgNamePattern = "--";
            Regex regex = new Regex(inputArgNamePattern);
            if (args.Length > 0)
            {
                foreach (var arg in args)
                {
                    var userArgs = arg.Split("=");
                    arguments[regex.Replace(userArgs[0], string.Empty)] = userArgs[1];
                }
            }

            var endpointString = arguments.GetValueOrDefault(EndpointUriArgName, $"wss://westus.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1");
            var endpointUri = new Uri(endpointString);

            if (!arguments.ContainsKey(SubscriptionKeyArgName))
            {
                Exception exp = new Exception("Subscription Key missing! Please pass in a Cognitive services subscription Key using --SubscriptionKey=\"your_subscription_key\"" +
                    "Find more information on creating a Cognitive services resource and accessing your Subscription key here: https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account?tabs=multiservice%2Cwindows");
                throw exp;
            }

            var subscriptionKey = arguments[SubscriptionKeyArgName];
            var username = arguments.GetValueOrDefault(UsernameArgName, null);
            var password = arguments.GetValueOrDefault(PasswordArgName, null);

            var audioDirPath = Path.Combine(Path.GetDirectoryName(System.Reflection.Assembly.GetExecutingAssembly().Location), "../../../AudioSamples/DictationBatman.wav");
            if (!File.Exists(audioDirPath))
            {
                Exception exp = new Exception(string.Format("Audio File does not exist at path: {0}", audioDirPath));
                throw exp;
            }

            ContinuousRecognitionWithTenantLMAsync(endpointUri, subscriptionKey, audioDirPath, username, password).GetAwaiter().GetResult();
        }

        private static async Task<string> AcquireAuthTokenWithUsernamePasswordAsync(string username, string password)
        {
            var tokenEndpoint = "https://login.microsoftonline.com/common/oauth2/token";
            var postBody = $"resource={ServiceApplicationId}&client_id={ClientApplicationId}&grant_type=password&username={username}&password={password}";
            var stringContent = new StringContent(postBody, Encoding.UTF8, "application/x-www-form-urlencoded");
            using (HttpClient httpClient = new HttpClient())
            {
                var response = await httpClient.PostAsync(tokenEndpoint, stringContent).ConfigureAwait(false);

                if (response.IsSuccessStatusCode)
                {
                    var result = await response.Content.ReadAsStringAsync().ConfigureAwait(false);

                    JObject jobject = JObject.Parse(result);
                    return jobject["access_token"].Value<string>();
                }
                else
                {
                    throw new Exception($"Requesting token from {tokenEndpoint} failed with status code {response.StatusCode}: {await response.Content.ReadAsStringAsync().ConfigureAwait(false)}");
                }
            }
        }

        private static async Task<string> AcquireAuthTokenWithInteractiveLoginAsync()
        {
            var authContext = new AuthenticationContext("https://login.windows.net/microsoft.onmicrosoft.com");
            var deviceCodeResult = await authContext.AcquireDeviceCodeAsync(ServiceApplicationId, ClientApplicationId).ConfigureAwait(false);

            Console.WriteLine(deviceCodeResult.Message);

            var authResult = await authContext.AcquireTokenByDeviceCodeAsync(deviceCodeResult).ConfigureAwait(false);

            return authResult.AccessToken;
        }
    }
}
```

Sedan måste du återskapa och köra projektet från kommando raden. Innan du kör kommandot uppdaterar du några parametrar genom att göra följande:

1. Ersätt `<Username>` och `<Password>` med värdena för en giltig klient användare.
1. Ersätt `<Subscription-Key>` med prenumerations nyckeln för din tal resurs. Det här värdet är tillgängligt i **översikts** avsnittet för din tal resurs i [Azure Portal](https://aka.ms/azureportal).
1. Ersätt `<Endpoint-Uri>` med följande slut punkt. Se till att du ersätter `{your region}` den region där din tal resurs skapades. Dessa regioner stöds: `westus` , `westus2` och `eastus` . Din regions information finns i avsnittet **Översikt** i din tal resurs i [Azure Portal](https://aka.ms/azureportal).
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
1. Kör följande kommando:

   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

I den här självstudien har du lärt dig hur du använder Microsoft 365 data för att skapa en anpassad tal igenkännings modell, distribuera den och använda den med talet SDK.

## <a name="next-steps"></a>Nästa steg

* [Speech Studio](https://speech.microsoft.com/)
* [Speech SDK](speech-sdk.md)