---
title: Skapa en klient modell (för hands version) – tal tjänst
titleSuffix: Azure Cognitive Services
description: Generera en klient modell automatiskt (Custom Speech med Office 365-data) som utnyttjar dina Office 365-data för att leverera optimal tal igenkänning för organisatoriska villkor som är både säkra och kompatibla.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: tutorial
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: 8ca31dcadebf2dc47d5a4b4db715f26fb38e204e
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816375"
---
# <a name="create-a-tenant-model-preview"></a>Skapa en klient modell (förhands granskning)

Klient organisations modell (Custom Speech med Office 365-data) är en valbar tjänst för Office 365 Enterprise-kunder som automatiskt genererar en anpassad tal igenkännings modell från din organisations Office365-data. Modellen som skapas är optimerad för tekniska termer, jargong och personers namn, allt på ett säkert och kompatibelt sätt.

> [!IMPORTANT]
> Om din organisation registrerar med klient organisations modellen kan tal tjänsten komma åt din organisations språk modell, som genereras från e-post och dokument i Office 365 offentliga grupper som kan ses av alla i din organisation. Din organisations Office 365-administratör kan aktivera/inaktivera användningen av den organisatoriska språk modellen med hjälp av administrations portalen för Office 365.

I den här kursen får du lära du dig att:

> [!div class="checklist"]
> * Registrera dig för att använda en klient modell i Microsoft 365 administrations Center
> * Hämta en nyckel för tal prenumeration
> * Skapa en klient modell
> * Distribuera en klient modell
> * Använda en klient modell med tal-SDK

## <a name="enroll-using-the-microsoft-365-admin-center"></a>Registrera dig med hjälp av Microsoft 365 administrations centret

Innan du kan distribuera din klient modell måste du först registrera dig med hjälp av Microsoft 365 administrations Center. Den här uppgiften kan bara utföras av Microsoft 365 administratören.

1. Logga in på [Microsoft 365 administrations Center](https://admin.microsoft.com ).
2. I den vänstra panelen väljer du **Inställningar** och sedan **appar**.

   ![Registrering av klient organisations modell](media/tenant-language-model/tenant-language-model-enrollment.png)

3. Leta upp och välj **Azure Speech Services**.

   ![Registrering av klient modell 2](media/tenant-language-model/tenant-language-model-enrollment-2.png)

4. Klicka på kryss rutan och spara.

Om du behöver inaktivera klient organisations modellen går du tillbaka till den här skärmen, avmarkerar kryss rutan och sparar.

## <a name="get-a-speech-subscription-key"></a>Hämta en nyckel för tal prenumeration

För att kunna använda en klient modell med tal-SDK behöver du en tal resurs och dess tillhör ande prenumerations nyckel.

1. Logga in på [Azure-portalen](https://aka.ms/azureportal).
2. Välj **Skapa en resurs**.
3. I Sök fältet skriver du: **tal**.
4. Välj **tal**och klicka sedan på **skapa**.
5. Följ anvisningarna på skärmen för att skapa din resurs. Förvissa sig:
   * **Platsen** anges till antingen **öster** eller **väst**.
   * **Pris nivån** är inställd på **S0**.
6. Klicka på **Skapa**.
7. Efter några minuter skapas din resurs. Prenumerations nyckeln är tillgänglig i **översikts** avsnittet för din resurs.

## <a name="create-a-model"></a>Skapa en modell

När din administratör har aktiverat klient organisations modell för din organisation kan du skapa en språk modell som baseras på dina Office365-data.

1. Logga in på [tal Studio](https://speech.microsoft.com/).
2. Leta upp och klicka på kugg hjuls ikonen (inställningar) i det övre högra hörnet och välj sedan **Inställningar för klient organisations modell**.

   ![Menyn Inställningar](media/tenant-language-model/tenant-language-settings.png)

3. Nu visas ett meddelande där du vet om du är kvalificerad att skapa en klient modell.
   > [!NOTE]
   > Office 365 företags kunder i Nordamerika är berättigade att skapa en klient modell (engelska). Om du är Customer Lockbox (CLB), kund nyckel (CK) eller Office 365 myndighets kund är den här funktionen inte tillgänglig. Följ dessa anvisningar för att avgöra om du är Customer Lockbox-eller kund nyckel kund:
   > * [Customer Lockbox](https://docs.microsoft.com/office365/securitycompliance/controlling-your-data-using-customer-key#FastTrack)
   > * [Kund nyckel](https://docs.microsoft.com/microsoft-365/compliance/customer-lockbox-requests)
   > * [Office 365-myndigheter](https://www.microsoft.com/microsoft-365/government)

4. Välj sedan Välj **för**att välja. Du får ett e-postmeddelande med anvisningar när din klient modell är klar.

## <a name="deploy-your-model"></a>Distribuera din modell

När din klient modell är klar följer du de här stegen för att distribuera din modell:

1. Klicka på knappen **Visa modell** i bekräftelse meddelandet som du fick eller logga in på [tal Studio](https://speech.microsoft.com/).
2. Leta upp och klicka på kugg hjuls ikonen (inställningar) i det övre högra hörnet och välj sedan **Inställningar för klient organisations modell**.

   ![Menyn Inställningar](media/tenant-language-model/tenant-language-settings.png)

3. Klicka på **Distribuera**.
4. När modellen har distribuerats ändras statusen till **distribuerad**.

## <a name="use-your-model-with-the-speech-sdk"></a>Använd din modell med talet SDK

Nu när du har distribuerat din modell kan du använda den med talet SDK. I det här avsnittet ska du använda exempel koden som tillhandahölls för att anropa tal tjänsten med Azure AD-autentisering.

Nu ska vi titta på koden som du ska använda för att anropa talet SDK C#i. I det här exemplet ska du utföra tal igenkänning med en klient modell. Den här guiden förutsätter att din plattform redan har kon figurer ATS. Om du behöver hjälp med att konfigurera, se [snabb start: identifiera tal C# , (.net Core)](quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnetcore).

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

    // Note: ServiceApplicationId is a fixed value.  No need to change.

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

Därefter måste du återskapa och köra projektet från kommando raden. Det finns några parametrar du behöver uppdatera innan du kör kommandot.

1. Ersätt `<Username>` och `<Password>` med värdena för en giltig klient användare.
2. Ersätt `<Subscription-Key>` med prenumerations nyckeln för din tal resurs. Det här värdet är tillgängligt i **översikts** avsnittet för din tal resurs i [Azure Portal](https://aka.ms/azureportal).
3. Ersätt `<Endpoint-Uri>` med slut punkten nedan. Se till att du ersätter `{your-region}` med den region där din tal resurs skapades. Dessa regioner stöds: `westus`, `westus2`och `eastus`. Din regions information finns i **översikts** avsnittet för din tal resurs i [Azure Portal](https://aka.ms/azureportal).
   ```
   "wss://{your region}.online.princeton.customspeech.ai/msgraphcustomspeech/conversation/v1".
   ```
4. Kör kommandot:
   ```bash
   dotnet TenantLMSample.dll --Username=<Username> --Password=<Password> --SubscriptionKey=<Subscription-Key> --EndpointUri=<Endpoint-Uri>
   ```

## <a name="next-steps"></a>Nästa steg

* [Tal Studio](https://speech.microsoft.com/)
* [Speech SDK](speech-sdk.md)
