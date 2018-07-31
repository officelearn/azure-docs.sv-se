---
title: 'Självstudier: Skriva en WPF-app för Translator Text med C# | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: I den här självstudiekursen får du lära dig hur du använder Translator Text API till att översätta text, hämta en språkanpassad lista över språk som stöds med mera, genom att skapa en WPF-app med C#.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: tutorial
ms.date: 07/20/2018
ms.author: nolachar
ms.openlocfilehash: 5dc9478516f4e9850543a6ee129fef0f1d3ee4f7
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39214925"
---
# <a name="tutorial-write-a-wpf-application-for-translator-text-using-c35"></a>Självstudier: Skriva en WPF-app för Translator Text med C#&#35;

I den här självstudiekursen skapar du ett översättningsverktyg för interaktiva texter med hjälp av Translator Text API (V3), en del av Microsoft Cognitive Services i Azure. Du lär dig följande:

> [!div class="checklist"]
> * Hämta en lista över språk som stöds av tjänsten
> * Göra en översättning av text som angetts av användaren från ett språk till ett annat

Den här appen integreras även med två andra Microsoft Cognitive Services.

|||
|-|-|
|[Textanalys](https://azure.microsoft.com/services/cognitive-services/text-analytics/)|Används för att automatiskt identifiera källspråket för texten som ska översättas|
|[Stavningskontroll i Bing](https://azure.microsoft.com/services/cognitive-services/spell-check/)|Används för engelsk källtext till att korrigera felstavningar så att översättningen blir mer exakt

![[Självstudieprogrammet körs]](media/translator-text-csharp-session.png)

## <a name="prerequisites"></a>Nödvändiga komponenter

Du behöver [Visual Studio 2017](https://www.visualstudio.com/downloads/) för att köra den här koden på Windows. (Den kostnadsfria Community Edition fungerar.)

Du behöver också prenumerationsnycklar för de tre Azure-tjänster som används i programmet. Du kan hämta en nyckel för Translator Text-tjänsten via Azure-instrumentpanelen. En gratis prisnivå är tillgänglig så att du kan översätta upp till 2 miljoner tecken per månad utan kostnad.

Båda tjänsterna Textanalys och Stavningskontroll i Bing finns som kostnadsfri utvärderingsversion, som du kan registrera dig för på [Prova Cognitive Services](https://azure.microsoft.com/try/cognitive-services/). Du kan också skapa en prenumeration på tjänsterna via Azure-instrumentpanelen. Textanalys har en kostnadsfri nivå.

Källkoden för den här självstudiekursen finns nedan. Dina prenumerationsnycklar måste kopieras till källkoden som variablerna `TEXT_TRANSLATION_API_SUBSCRIPTION_KEY` och så vidare i `MainWindow.xaml.cs`.

> [!IMPORTANT]
> Tjänsten Textanalys är tillgänglig i flera regioner. URI:n i den här självstudiekursens källkod finns i regionen `westus`, som är den region som används för kostnadsfria utvärderingsversioner. Om du har en prenumeration i en annan region ska du även uppdatera den URI:n.

## <a name="source-code"></a>Källkod

Det här är källkoden för Microsoft Translator-API för textöversättning. Kör appen genom att kopiera källkoden till lämplig fil i ett nytt WPF-projekt i Visual Studio.

### <a name="mainwindowxamlcs"></a>MainWindow.xaml.cs

Det här är filen med bakomliggande kod som innehåller appens funktioner.

```csharp
using System;
using System.Windows;
using System.Net;
using System.Net.Http;
using System.IO;
using System.Collections.Generic;
using System.Linq;
using System.Text;

// NOTE: Add assembly references to System.Runtime.Serialization, System.Web, and System.Web.Extensions.

// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace MSTranslatorTextDemo
{
    /// <summary>
    /// This WPF application demonstrates the use of the Microsoft Translator Text API to translate a brief text string from
    /// one language to another. The languages are selected from a drop-down menu. The text of the translation is displayed.
    /// The source language may optionally be automatically detected. English text is spell-checked.
    /// </summary>
    public partial class MainWindow : Window
    {
        // Translator text subscription key from Microsoft Azure dashboard
        const string TEXT_TRANSLATION_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
        const string TEXT_ANALYTICS_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
        const string BING_SPELL_CHECK_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";

        public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
        const string TEXT_ANALYTICS_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/";
        const string BING_SPELL_CHECK_API_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/";

        private string[] languageCodes;     // array of language codes

        // Dictionary to map language code from friendly name (sorted case-insensitively on language name)
        private SortedDictionary<string, string> languageCodesAndTitles =
            new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

        public MainWindow()
        {
            // at least show an error dialog if there's an unexpected error
            AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

            if (TEXT_TRANSLATION_API_SUBSCRIPTION_KEY.Length != 32
                || TEXT_ANALYTICS_API_SUBSCRIPTION_KEY.Length != 32
                || BING_SPELL_CHECK_API_SUBSCRIPTION_KEY.Length != 32)
            {
                MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                    "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                    "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
                System.Windows.Application.Current.Shutdown();
            }
            else
            {
                InitializeComponent();          // start the GUI
                GetLanguagesForTranslate();     // get codes and friendly names of languages that can be translated
                PopulateLanguageMenus();        // fill the drop-down language lists
            }
        }

        // Global exception handler to display error message and exit
        private static void HandleExceptions(object sender, UnhandledExceptionEventArgs args)
        {
            Exception e = (Exception)args.ExceptionObject;
            MessageBox.Show("Caught " + e.Message, "Error", MessageBoxButton.OK, MessageBoxImage.Error);
            System.Windows.Application.Current.Shutdown();
        }

        // ***** POPULATE LANGUAGE MENUS
        private void PopulateLanguageMenus()
        {
            // Add option to automatically detect the source language
            FromLanguageComboBox.Items.Add("Detect");

            int count = languageCodesAndTitles.Count;
            foreach (string menuItem in languageCodesAndTitles.Keys)
            {
                FromLanguageComboBox.Items.Add(menuItem);
                ToLanguageComboBox.Items.Add(menuItem);
            }

            // set default languages
            FromLanguageComboBox.SelectedItem = "Detect";
            ToLanguageComboBox.SelectedItem = "English";
        }

        // ***** DETECT LANGUAGE OF TEXT TO BE TRANSLATED
        private string DetectLanguage(string text)
        {
            string uri = TEXT_ANALYTICS_API_ENDPOINT + "languages?numberOfLanguagesToDetect=1";

            // create request to Text Analytics API
            HttpWebRequest detectLanguageWebRequest = (HttpWebRequest)WebRequest.Create(uri);
            detectLanguageWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_ANALYTICS_API_SUBSCRIPTION_KEY);
            detectLanguageWebRequest.Method = "POST";

            // create and send body of request
            var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
            string jsonText = serializer.Serialize(text);

            string body = "{ \"documents\": [ { \"id\": \"0\", \"text\": " + jsonText + "} ] }";
            byte[] data = Encoding.UTF8.GetBytes(body);
            detectLanguageWebRequest.ContentLength = data.Length;

            using (var requestStream = detectLanguageWebRequest.GetRequestStream())
                requestStream.Write(data, 0, data.Length);

            HttpWebResponse response = (HttpWebResponse)detectLanguageWebRequest.GetResponse();

            // read and parse JSON response
            var responseStream = response.GetResponseStream();
            var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
            dynamic jsonResponse = serializer.DeserializeObject(jsonString);

            // fish out the detected language code
            var languageInfo = jsonResponse["documents"][0]["detectedLanguages"][0];
            if (languageInfo["score"] > (decimal)0.5)
                return languageInfo["iso6391Name"];
            else
                return "";
        }

        // ***** CORRECT SPELLING OF TEXT TO BE TRANSLATED
        private string CorrectSpelling(string text)
        {
            string uri = BING_SPELL_CHECK_API_ENDPOINT + "?mode=spell&mkt=en-US";

            // create request to Bing Spell Check API
            HttpWebRequest spellCheckWebRequest = (HttpWebRequest)WebRequest.Create(uri);
            spellCheckWebRequest.Headers.Add("Ocp-Apim-Subscription-Key", BING_SPELL_CHECK_API_SUBSCRIPTION_KEY);
            spellCheckWebRequest.Method = "POST";
            spellCheckWebRequest.ContentType = "application/x-www-form-urlencoded"; // doesn't work without this

            // create and send body of request
            string body = "text=" + System.Web.HttpUtility.UrlEncode(text);
            byte[] data = Encoding.UTF8.GetBytes(body);
            spellCheckWebRequest.ContentLength = data.Length;
            using (var requestStream = spellCheckWebRequest.GetRequestStream())
                requestStream.Write(data, 0, data.Length);
            HttpWebResponse response = (HttpWebResponse)spellCheckWebRequest.GetResponse();

            // read and parse JSON response and get spelling corrections
            var serializer = new System.Web.Script.Serialization.JavaScriptSerializer();
            var responseStream = response.GetResponseStream();
            var jsonString = new StreamReader(responseStream, Encoding.GetEncoding("utf-8")).ReadToEnd();
            dynamic jsonResponse = serializer.DeserializeObject(jsonString);
            var flaggedTokens = jsonResponse["flaggedTokens"];

            // construct sorted dictionary of corrections in reverse order in string (right to left)
            // so that making a correction can't affect later indexes
            var corrections = new SortedDictionary<int, string[]>(Comparer<int>.Create((a, b) => b.CompareTo(a)));
            for (int i = 0; i < flaggedTokens.Length; i++)
            {
                var correction = flaggedTokens[i];
                var suggestion = correction["suggestions"][0];  // consider only first suggestion
                if (suggestion["score"] > (decimal)0.7)         // take it only if highly confident
                    corrections[(int)correction["offset"]] = new string[]   // dict key   = offset
                        { correction["token"], suggestion["suggestion"] };  // dict value = {error, correction}
            }

            // apply the corrections in order from right to left
            foreach (int i in corrections.Keys)
            {
                var oldtext = corrections[i][0];
                var newtext = corrections[i][1];

                // apply capitalization from original text to correction - all caps or initial caps
                if (text.Substring(i, oldtext.Length).All(char.IsUpper)) newtext = newtext.ToUpper();
                else if (char.IsUpper(text[i])) newtext = newtext[0].ToString().ToUpper() + newtext.Substring(1);

                text = text.Substring(0, i) + newtext + text.Substring(i + oldtext.Length);
            }

            return text;
        }

        // ***** GET TRANSLATABLE LANGUAGE CODES
        private void GetLanguagesForTranslate()
        {
            // send request to get supported language codes
            string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
            WebRequest WebRequest = WebRequest.Create(uri);
            WebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
            WebRequest.Headers.Add("Accept-Language", "en");
            WebResponse response = null;
            // read and parse the JSON response
            response = WebRequest.GetResponse();
            using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
            {
                var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
                var languages = result["translation"];

                languageCodes = languages.Keys.ToArray();
                foreach (var kv in languages)
                {
                    languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
                }
            }
        }

        // ***** PERFORM TRANSLATION ON BUTTON CLICK
        private async void TranslateButton_Click(object sender, EventArgs e)
        {
            string textToTranslate = TextToTranslate.Text.Trim();

            string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
            string fromLanguageCode;

            // auto-detect source language if requested
            if (fromLanguage == "Detect")
            {
                fromLanguageCode = DetectLanguage(textToTranslate);
                if (!languageCodes.Contains(fromLanguageCode))
                {
                    MessageBox.Show("The source language could not be detected automatically " +
                        "or is not supported for translation.", "Language detection failed",
                        MessageBoxButton.OK, MessageBoxImage.Error);
                    return;
                }
            }
            else
                fromLanguageCode = languageCodesAndTitles[fromLanguage];

            string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

            // spell-check the source text if the source language is English
            if (fromLanguageCode == "en")
            {
                if (textToTranslate.StartsWith("-"))    // don't spell check in this case
                    textToTranslate = textToTranslate.Substring(1);
                else
                {
                    textToTranslate = CorrectSpelling(textToTranslate);
                    TextToTranslate.Text = textToTranslate;     // put corrected text into input field
                }
            }

            // handle null operations: no text or same source/target languages
            if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
            {
                TranslatedTextLabel.Content = textToTranslate;
                return;
            }

            // send HTTP request to perform the translation
            string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
            string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

            System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
            var requestBody = JsonConvert.SerializeObject(body);

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
                request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();

                var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
                var translations = result[0]["translations"];
                var translation = translations[0]["text"];

                // Update the translation field
                TranslatedTextLabel.Content = translation;
            }
        }
    }
}
```

### <a name="mainwindowxaml"></a>MainWindow.xaml

Den här filen definierar användargränssnittet för appen, ett WPF-formulär. Om du vill skapa en egen version av formuläret, behöver du inte denna XAML.

```xml
<Window x:Class="MSTranslatorTextDemo.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:MSTranslatorTextDemo"
        mc:Ignorable="d"
        Title="Microsoft Translator" Height="400" Width="700" BorderThickness="0">
    <Grid>
        <Label x:Name="label" Content="Microsoft Translator" HorizontalAlignment="Left" Margin="39,6,0,0" VerticalAlignment="Top" Height="49" FontSize="26.667"/>
        <TextBox x:Name="TextToTranslate" HorizontalAlignment="Left" Height="23" Margin="42,160,0,0" TextWrapping="Wrap" VerticalAlignment="Top" Width="600" FontSize="14" TabIndex="3"/>
        <Label x:Name="EnterTextLabel" Content="Text to translate:" HorizontalAlignment="Left" Margin="40,129,0,0" VerticalAlignment="Top" FontSize="14"/>
        <Label x:Name="toLabel" Content="Translate to:" HorizontalAlignment="Left" Margin="304,58,0,0" VerticalAlignment="Top" FontSize="14"/>

        <Button x:Name="TranslateButton" Content="Translate" HorizontalAlignment="Left" Margin="39,206,0,0" VerticalAlignment="Top" Width="114" Height="31" Click="TranslateButton_Click" FontSize="14" TabIndex="4" IsDefault="True"/>
        <ComboBox x:Name="ToLanguageComboBox"
                HorizontalAlignment="Left"
                Margin="306,88,0,0"
                VerticalAlignment="Top"
                Width="175" FontSize="14" TabIndex="2">

        </ComboBox>
        <Label x:Name="fromLabel" Content="Translate from:" HorizontalAlignment="Left" Margin="40,58,0,0" VerticalAlignment="Top" FontSize="14"/>
        <ComboBox x:Name="FromLanguageComboBox"
            HorizontalAlignment="Left"
            Margin="42,88,0,0"
            VerticalAlignment="Top"
            Width="175" FontSize="14" TabIndex="1"/>
        <Label x:Name="TranslatedTextLabel" Content="Translation appears here" HorizontalAlignment="Left" Margin="39,255,0,0" VerticalAlignment="Top" Width="620" FontSize="14" Height="85" BorderThickness="0"/>
    </Grid>
</Window>
```

## <a name="service-endpoints"></a>Tjänstslutpunkter

Microsoft Translator-tjänsten har ett antal slutpunkter som ger olika delar av översättningsfunktionerna. De som används i den här självstudien är:

|||
|-|-|
|`Languages`|Returnerar uppsättningen språk som stöds av andra åtgärder för Translator Text API.|
|`Translate`|Den angivna källtexten, en källspråkskod och en målspråkskod returnerar en översättning av källtexten till målspråket.|

## <a name="the-translation-app"></a>Översättningsappen

Översättningsappens användargränssnitt skapas med hjälp av Windows Presentation Foundation (WPF). Skapa ett nytt WPF-projekt i Visual Studio genom att följa dessa steg.

* På **Arkiv**-menyn väljer du **Nytt > Projekt**.
* I fönstret Nytt projekt öppnar du **Installerat > Mallar > Visual C#**. En lista över tillgängliga projektmallar visas i mitten av dialogrutan.
* Se till att **.NET Framework 4.5.2** väljs i listrutan ovanför listan med projektmallar.
* Klicka på **WPF-app (.NET Framework)** i listan med projektmallar.
* Med hjälp av fälten längst ned i dialogrutan namnger du det nya projektet och lösningen som innehåller det.
* Klicka på **OK** för att skapa det nya projektet och lösningen.

![[Skapa en ny WPF-app i Visual Studio]](media/translator-text-csharp-new-project.png)

Lägg till referenser i följande .NET Framework-sammansättningar i projektet.

* System.Runtime.Serialization
* System.Web
* System.Web.Extensions

Installera dessutom NuGet-paketet `Newtonsoft.Json` i projektet.

Leta nu reda på filen `MainWindow.xaml` i Solution Explorer och öppna den. Den är tom från början. Så här bör det färdiga användargränssnittet se ut, kommenterat med namnen på kontrollerna i blått. Använd samma namn för kontrollerna i användargränssnittet, eftersom de också förekommer i koden.

![[Kommenterad vy i huvudfönstret i Visual Studio Designer]](media/translator-text-csharp-xaml.png)

> [!NOTE]
> Källkoden för den här självstudien innehåller XAML-källan för det här formuläret. Du kan klistra in den i projektet i stället för att skapa formuläret i Visual Studio.

* `FromLanguageComboBox` *(kombinationsruta)*  – Visar en lista över de språk som stöds av Microsoft Translator för textöversättning. Användaren väljer det språk som översättningen görs från.
* `ToLanguageComboBox` *(kombinationsrutan)*  – Visar samma lista över språk som `FromComboBox` men används för att välja det språk som användaren översätter till.
* `TextToTranslate` *(textruta)*  – Användaren anger vilken text som ska översättas här.
* `TranslateButton` *(knapp)*  – Användaren klickar på knappen (eller trycker på RETUR) för att översätta texten.
* `TranslatedTextLabel` *(etikett)*  – Översättningen av användarens text visas här.

Om du gör en egen version av det här formuläret, behöver inte göra den *exakt* som den som används här. Men se till att listrutorna för språk är tillräckligt breda för att undvika att språkets namn bryts.

## <a name="the-mainwindow-class"></a>MainWindow-klassen

Filen med bakomliggande kod, `MainWindow.xaml.cs`, är där koden placeras och som så att programmet utför sin uppgift. Arbetet sker vid två tillfällen:

* När programmet startar och `MainWindow` instansieras hämtar det språklistan över med hjälp av Translators  och API:er och fyller listmenyerna med dem. Den här uppgiften utförs en gång, i början av varje session.

* När användaren klickar på knappen **Translate** (Översätt) hämtas användarens språkval och den text som användaren har angett. Sedan anropas API:et `Translate` för att utföra översättningen. Andra funktioner kan också anropas för att fastställa språket i texten och för att kontrollera stavningen innan texten översätts.

Ta en titt i början av klassen:

```csharp
public partial class MainWindow : Window
{
    // Translator text subscription key from Microsoft Azure dashboard
    const string TEXT_TRANSLATION_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
    const string TEXT_ANALYTICS_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";
    const string BING_SPELL_CHECK_API_SUBSCRIPTION_KEY = "ENTER KEY HERE";

    public static readonly string TEXT_TRANSLATION_API_ENDPOINT = "https://api.cognitive.microsofttranslator.com/{0}?api-version=3.0";
    const string TEXT_ANALYTICS_API_ENDPOINT = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/";
    const string BING_SPELL_CHECK_API_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/spellcheck/";

    private string[] languageCodes;     // array of language codes

    // Dictionary to map language code from friendly name (sorted case-insensitively on language name)
    private SortedDictionary<string, string> languageCodesAndTitles =
        new SortedDictionary<string, string>(Comparer<string>.Create((a, b) => string.Compare(a, b, true)));

    public MainWindow()
    {
        // at least show an error dialog if there's an unexpected error
        AppDomain.CurrentDomain.UnhandledException += new UnhandledExceptionEventHandler(HandleExceptions);

        if (TEXT_TRANSLATION_API_SUBSCRIPTION_KEY.Length != 32
            || TEXT_ANALYTICS_API_SUBSCRIPTION_KEY.Length != 32
            || BING_SPELL_CHECK_API_SUBSCRIPTION_KEY.Length != 32)
        {
            MessageBox.Show("One or more invalid API subscription keys.\n\n" +
                "Put your keys in the *_API_SUBSCRIPTION_KEY variables in MainWindow.xaml.cs.",
                "Invalid Subscription Key(s)", MessageBoxButton.OK, MessageBoxImage.Error);
            System.Windows.Application.Current.Shutdown();
        }
        else
        {
            InitializeComponent();          // start the GUI
            GetLanguagesForTranslate();     // get codes and friendly names of languages that can be translated
            PopulateLanguageMenus();        // fill the drop-down language lists
        }
    }
// more to come
}
```

Två medlemsvariabler som deklareras här innehåller information om våra språk:

|||
|-|-|
|`languageCodes`<br>strängmatris|Cachelagrar språkkoderna. Translator-tjänsten använder korta koder som `en` för engelska, för att identifiera språk.|
|`languageCodesAndTitles`<br>SortedDictionary|Mappar ”egna” namn i användargränssnittet tillbaka till de korta koderna som används i API:et. Sorteras alfabetiskt utan hänsyn till skiftläge.|

Den första koden som körs av appen är konstruktorn `MainWindow`. Ange först metoden `HandleExceptions` som global felhanterare. På så sätt finns det minst ett felmeddelande om ett undantag inte hanteras.

Därefter kontrollerar du att alla API-prenumerationsnycklar är exakt 32 tecken. Om de inte är det så är den troligaste orsaken att *någon* inte har klistrat in i sina API-nycklar. I det här fallet visar du ett felmeddelande och lämnar. (Godkänt test betyder såklart inte att nycklarna är giltiga.)

Om det finns nycklar som åtminstone har rätt längd sätter anropet `InitializeComponent()` igång användargränssnittet genom att hitta, läsa in och instansiera XAML-beskrivningen av appens huvudfönster.

Ställ slutligen språkmenyerna. Den här uppgiften kräver tre separata metodanrop, som beskrivs i detalj i följande avsnitt.

## <a name="get-supported-languages"></a>Hämta språk som stöds

Tjänsten Microsoft Translator stöder totalt 61 språk när detta skrivs och fler kan läggas till ibland. Så det är bäst att inte hårdkoda språken som stöds i ditt program. Fråga i stället Translator-tjänsten vilka språk som stöds. Alla språk som stöds kan översättas till ett annat språk som stöds.

Anropa API:et `Languages` för att hämta listan över språk som stöds.

API:et `Languages` tar en valfri GET-frågeparameter, *scope*. *scope* kan ha något av tre värden: `translation`, `transliteration`, och `dictionary`. Den här koden använder värdet `translation`.

API:et `Languages` tar också ett valfritt HTTP-huvud, `Accept-Language`. Värdet för det här huvudet bestämmer vilket språk som namnen på språk som stöds returneras. Värdet ska vara en korrekt strukturerad BCP-47-språktagg. Den här koden använder värdet `en` för att hämta språknamnen på engelska.

API:et `Languages` returnerar ett JSON-svar som ser ut så här.

```json
{
  "translation": {
    "af": {
      "name": "Afrikaans",
      "nativeName": "Afrikaans",
      "dir": "ltr"
    },
    "ar": {
      "name": "Arabic",
      "nativeName": "العربية",
      "dir": "rtl"
    },
...
}
```

Så att språkkoder (t.ex. `af`) och språknamn (t.ex. `Afrikaans`) kan extraheras använder den här koden NewtonSoft.Json-metoden [JsonConvert.DeserializeObject](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm).

Med den här bakgrundskunskapen skapar du följande metod för att hämta språkkoderna och deras namn.

```csharp
private void GetLanguagesForTranslate()
{
    // send request to get supported language codes
    string uri = String.Format(TEXT_TRANSLATION_API_ENDPOINT, "languages") + "&scope=translation";
    WebRequest WebRequest = WebRequest.Create(uri);
    WebRequest.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
    WebRequest.Headers.Add("Accept-Language", "en");
    WebResponse response = null;
    // read and parse the JSON response
    response = WebRequest.GetResponse();
    using (var reader = new StreamReader(response.GetResponseStream(), UnicodeEncoding.UTF8))
    {
        var result = JsonConvert.DeserializeObject<Dictionary<string, Dictionary<string, Dictionary<string, string>>>>(reader.ReadToEnd());
        var languages = result["translation"];

        languageCodes = languages.Keys.ToArray();
        foreach (var kv in languages)
        {
            languageCodesAndTitles.Add(kv.Value["name"], kv.Key);
        }
    }
}
```

`GetLanguagesForTranslate()` skapar först HTTP-begäran. Frågesträngsparametern `scope=translation` begär bara de språk som stöds för textöversättning. Prenumerationsnyckeln för API:et för textöversättning läggs till i begärandehuvuden. Huvudet `Accept-Language` med värdet `en` läggs till så att språken som stöds returneras på engelska.

När begäran har slutförts parsas JSON-svaret och konverteras till en ordlista, och sedan läggs språkkoderna till i medlemsvariabeln `languageCodes`. Nyckel/värde-paren som innehåller språkkoderna och de egna språknamnen loppas igenom och läggs till i medlemsvariabeln `languageCodesAndTitles`. (Menyerna i formuläret visar de egna namnen men koderna behövs för att begära översättningen.)

## <a name="populate-the-language-menus"></a>Fylla i språkmenyerna

Det mesta av användargränssnittet definieras i XAML, så du behöver inte göra mycket för att ställa in det, förutom att anropa `InitializeComponent()`. Det enda andra du måste göra är att lägga till de egna namnen i listrutorna **Translate from** (Översätt från) och **Translate to** (Översätt till), som görs i `PopulateLanguageMenus()`.

```csharp
private void PopulateLanguageMenus()
{
    // Add option to automatically detect the source language
    FromLanguageComboBox.Items.Add("Detect");

    int count = languageCodesAndTitles.Count;
    foreach (string menuItem in languageCodesAndTitles.Keys)
    {
        FromLanguageComboBox.Items.Add(menuItem);
        ToLanguageComboBox.Items.Add(menuItem);
    }

    // set default languages
    FromLanguageComboBox.SelectedItem = "Detect";
    ToLanguageComboBox.SelectedItem = "English";
}
```

Att fylla menyerna handlar helt enkelt om att iterera över ordlistan `languageCodesAndTitles` och lägga till varje nyckel, som är det ”egna” namnet, på båda menyerna. När du har fyllt i menyerna anges **Detect** (Identifiera) för de standardinställda till- och från-språken (för att automatiskt identifiera språk) och **English** (Engelska).

> [!TIP]
> Utan ett standardval för menyerna kan användaren klicka på **Translate** (Översätt) utan att först välja ett till- eller från-språk. Med standardinställningarna behöver du inte hantera det här problemet.

Nu när `MainWindow` har initierats och användargränssnittet skapats väntar koden tills användaren klickar på  knappen **Translate** (Översätt).

## <a name="perform-translation"></a>Utföra översättning

När användaren klickar på **Translate** (Översätt) anropar WPF händelsehanteraren `TranslateButton_Click()`, som visas här.

```csharp
private async void TranslateButton_Click(object sender, EventArgs e)
{
    string textToTranslate = TextToTranslate.Text.Trim();

    string fromLanguage = FromLanguageComboBox.SelectedValue.ToString();
    string fromLanguageCode;

    // auto-detect source language if requested
    if (fromLanguage == "Detect")
    {
        fromLanguageCode = DetectLanguage(textToTranslate);
        if (!languageCodes.Contains(fromLanguageCode))
        {
            MessageBox.Show("The source language could not be detected automatically " +
                "or is not supported for translation.", "Language detection failed",
                MessageBoxButton.OK, MessageBoxImage.Error);
            return;
        }
    }
    else
        fromLanguageCode = languageCodesAndTitles[fromLanguage];

    string toLanguageCode = languageCodesAndTitles[ToLanguageComboBox.SelectedValue.ToString()];

    // spell-check the source text if the source language is English
    if (fromLanguageCode == "en")
    {
        if (textToTranslate.StartsWith("-"))    // don't spell check in this case
            textToTranslate = textToTranslate.Substring(1);
        else
        {
            textToTranslate = CorrectSpelling(textToTranslate);
            TextToTranslate.Text = textToTranslate;     // put corrected text into input field
        }
    }

    // handle null operations: no text or same source/target languages
    if (textToTranslate == "" || fromLanguageCode == toLanguageCode)
    {
        TranslatedTextLabel.Content = textToTranslate;
        return;
    }

    // send HTTP request to perform the translation
    string endpoint = string.Format(TEXT_TRANSLATION_API_ENDPOINT, "translate");
    string uri = string.Format(endpoint + "&from={0}&to={1}", fromLanguageCode, toLanguageCode);

    System.Object[] body = new System.Object[] { new { Text = textToTranslate } };
    var requestBody = JsonConvert.SerializeObject(body);

    using (var client = new HttpClient())
    using (var request = new HttpRequestMessage())
    {
        request.Method = HttpMethod.Post;
        request.RequestUri = new Uri(uri);
        request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
        request.Headers.Add("Ocp-Apim-Subscription-Key", TEXT_TRANSLATION_API_SUBSCRIPTION_KEY);
        request.Headers.Add("X-ClientTraceId", Guid.NewGuid().ToString());

        var response = await client.SendAsync(request);
        var responseBody = await response.Content.ReadAsStringAsync();

        var result = JsonConvert.DeserializeObject<List<Dictionary<string, List<Dictionary<string, string>>>>>(responseBody);
        var translations = result[0]["translations"];
        var translation = translations[0]["text"];

        // Update the translation field
        TranslatedTextLabel.Content = translation;
    }
}
```

Det första steget är att hämta till- och från-språken, tillsammans med texten som användaren har angett, från formuläret.

Om ett källspråket är inställt på **Detect** (Identifiera) gör du ett anrop till `DetectLanguage()` för att fastställa språket i texten. Texten kan vara på ett språk som Translator-API:er inte stöder (många fler språk kan identifieras än översättas) eller så kan API:et för textanalys inte identifiera det. I så fall visas ett meddelande som informerar användaren och återgår utan att översätta.

Om källspråket är engelska (oavsett om det anges eller identifieras) kontrollerar du stavningen i texten med `CorrectSpelling()` och rättar eventuella fel. Den korrigerade texten placeras i indatafältet så att användaren vet att rättningen har gjorts. (Användaren kan ange ett bindestreck före texten som översätts för att förhindra stavningskontrollen.)

Om användaren inte har angett någon text eller om till- och från-språken är samma behövs ingen översättning och begäran kan undvikas.

Koden för att utföra översättningsbegäran bör se bekant ut: skapa URI:n, skapa en begäran, skicka den och parsa svaret. Skicka texten till kontrollen `TranslatedTextLabel` för att visa den.

Skicka sedan texten till API:et `Translate` i en serialiserad JSON-matris i texten för en POST-begäran. JSON-matrisen innehålla flera delar text för översättning, men bara en krävs här.

HTTP-huvudet med namnet `X-ClientTraceId` är valfritt. Värdet ska vara ett GUID. Det klientangivna spårnings-ID:t är användbart för att spåra begäranden när saker inte fungerar som förväntat. Men för att vara användbart måste dock värdet för X-ClientTraceID registreras av klienten. Ett klientspårnings-ID och datumet för begäranden kan hjälpa Microsoft att diagnostisera problem som kan uppstå.

> [!NOTE]
> Den här självstudiekursen fokuserar på Microsoft Translator-tjänsten, så metoderna `DetectLanguage()` och `CorrectSpelling()` beskrivs inte i detalj. Tjänsterna Textanalys och Stavningskontroll i Bing ger svar i JSON i stället för XML och Textanalys kräver även att begäran formateras som JSON. Dessa egenskaper utgör de flesta kodskillnaderna i de metoder som beskrivs här.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Referens för Microsoft Translator Text API](http://docs.microsofttranslator.com/text-translate.html)
