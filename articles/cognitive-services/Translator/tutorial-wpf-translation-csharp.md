---
title: Hur du skriver en Microsoft översättare WPF-program med C# kognitiva Azure-tjänster | Microsoft Docs
description: Lär dig hur du använder tjänsten översättare text att översätta text, hämta en lokaliserad lista över språk som stöds och mycket mer.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.devlang: csharp
ms.topic: article
ms.date: 10/25/2017
ms.author: v-jansko
ms.openlocfilehash: fb58fd087de09561a0ea930748562e595d3dde1c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35352602"
---
# <a name="how-to-write-a-microsoft-translator-wpf-application-in-c"></a>Hur du skriver ett Microsoft översättare WPF-program i C#

I den här kursen ska vi skapa ett verktyg för översättning av interaktiva text med Microsoft översättare Text-API (V3), en del av kognitiva Microsoft-tjänster i Azure. Du lär dig hur du:

> [!div class="checklist"]
> * Begära en lista över korta koder för de språk som stöds av tjänsten
> * Hämta en lista över översatta namn motsvarar dessa språkkoder
> * Skaffa en översatt version av användarens text från ett språk till en annan

Det här programmet har också integrering med två Microsoft kognitiva tjänster.

|||
|-|-|
|[Textanalys](https://azure.microsoft.com/services/cognitive-services/text-analytics/)|Används för att du kan också automatiskt identifiera källan språket för texten som ska räknas|
|[Bing stavningskontroll](https://azure.microsoft.com/services/cognitive-services/spell-check/)|För engelska källtext som används för att korrigera felstavade så översättningen är mer exakt

![[Självstudiekursen kör]](media/translator-text-csharp-session.png)

## <a name="prerequisites"></a>Förutsättningar

Om du vill göra den här kursen behöver du en utgåva av Visual Studio-2017, inklusive Community Edition.

Du måste också prenumeration nycklar för tre Azure-tjänster används i programmet. Du kan hämta en nyckel för tjänsten översättare Text från Azure instrumentpanelen. Kostnadsfria prisnivån är tillgänglig som gör det möjligt att översätta upp till två miljoner tecken per månad utan kostnad.

Både Text Analytics och Bing stavningskontroll services erbjudande gratisutvärderingar som du kan registrera dig för på [försök kognitiva Services](https://azure.microsoft.com/try/cognitive-services/). Du kan också skapa en prenumeration för antingen service via Azure instrumentpanelen. Textanalys har en kostnadsfri nivå.

Källkoden för den här kursen finns nedan. Din prenumeration nycklar måste kopieras till källkoden som variablerna `TEXT_TRANSLATION_API_SUBSCRIPTION_KEY`och så vidare i `MainWindow.xaml.cs`.

> [!IMPORTANT]
> Text Analytics-tjänsten är tillgänglig i flera områden. URI: N i våra självstudier källkoden finns i den `westus` region, vilket är regionen används kostnadsfritt försök. Om du har en prenumeration i en annan region måste URI: N uppdateras också.

## <a name="source-code"></a>Källkod

Detta är källkoden för Microsoft Translator texten API. Kör appen genom att kopiera källkoden till filen i ett nytt WPF-projekt i Visual Studio.

### <a name="mainwindowxamlcs"></a>MainWindow.xaml.cs

Det här är den bakomliggande kod-fil som innehåller programmets funktioner.

```cs
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
    /// This WPF application demonstrates the use of the Microsoft Translator Text API to translate a brief text string
    /// one language to another. The langauges are selected from a drop-down menu. The text of the translation is displayed.
    /// The source language may optionally be automatically detected.  English text is spell-checked.
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
            // at least show an error dialog when we get an unexpected error
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

            // read and and parse JSON response
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

Den här filen definierar användargränssnittet för programmet, WPF-formulär. Om du vill skapa en egen version av formuläret, behöver du inte den här XAML.

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

Tjänsten Microsoft Translator har flera slutpunkter som tillhandahåller olika delar av funktionerna för översättning. De som vi använder i den här kursen är:

|||
|-|-|
|`Languages`|Returnerar antal språk som stöds för närvarande av andra åtgärder av översättare Text API.|
|`Translate`|Källtext får språkkod för datakällans och en målspråk kod returnerar en översättning av källtexten till mål-språk.|

## <a name="the-translation-app"></a>Översättning av appen

Vår översättare programmets användargränssnitt skapas med Windows Presentation Foundation (WPF). Skapa ett nytt WPF-projekt i Visual Studio genom att följa dessa steg.

* Från den **filen** -menyn väljer du **nytt > projekt**.
* Öppna i fönstret nytt projekt **installerad > Mallar > Visual C#**. En lista över tillgängliga projektmallar visas i mitten av dialogrutan.
* Kontrollera att **.NET Framework 4.5.2** väljs i listrutan ovan projektlista för mallen.
* Klicka på **WPF-program (.NET Framework)** i mallistan projektet.
* Med hjälp av fälten längst ned i dialogrutan namnge det nya projektet och lösningen som innehåller den.
* Klicka på **OK** att skapa det nya projektet och lösningen.

![[Skapa en ny WPF-app i Visual Studio]](media/translator-text-csharp-new-project.png)

Lägg till referenser till följande .NET framework-sammansättningar i projektet.

* Avsnittsgruppen
* System.Web
* System.Web.Extensions

Dessutom installera NuGet-paketet `Newtonsoft.Json` i projektet.

Nu finns det `MainWindow.xaml` filen i Solution Explorer och öppna den. Den är tom från början. Det här är vad klar användargränssnittet bör se ut kommenterade med namnen på kontrollerna i blått. Använd samma namn för kontroller i användargränssnittet, eftersom de också visas i koden.

![[Kommenterade vyn i huvudfönstret i Visual Studio designer]](media/translator-text-csharp-xaml.png)

> [!NOTE]
> Källkoden för den här självstudiekursen innehåller XAML-källan för det här formuläret. Du kan klistra in den i projektet i stället för att skapa formulär i Visual Studio.

* `FromLanguageComboBox` *(ComboBox)*  -Visar en lista över de språk som stöds av Microsoft Translator för textöversättning. Användaren väljer språk som de översätter från.
* `ToLanguageComboBox` *(ComboBox)*  -Visar samma lista med språk som `FromComboBox`, men används för att välja det språk som användaren översattes till.
* `TextToTranslate` *(Textruta)*  -Användaren anger texten som ska översättas här.
* `TranslateButton` *(Knapp)*  -Användaren klickar på knappen (eller trycker på RETUR) att översätta text.
* `TranslatedTextLabel` *(Etikett)*  -Översättning för användarens text som visas här.

Om du skapar en egen version av det här formuläret, det är inte nödvändigt att göra det *exakt* som du valde. Men Kontrollera språk listrutorna är litet för att undvika att ta bort ett språknamn.

## <a name="the-mainwindow-class"></a>Klassen MainWindow

Filen bakomliggande kod `MainWindow.xaml.cs` är där koden går som gör programmet gör syfte. Vad som händer på två gånger:

* När programmet startas. När `MainWindow` är instansiera, vi hämtar språklistan med hjälp av deras `GetLanguagesForTranslate` och `GetLanguageNames` API: er och Fyll i vår nedrullningsbara menyer med dem. Den här uppgiften utförs en gång, i början av varje session.

* När användaren klickar på **Översätt** knappen, vi hämtar användarens språk och de har angetts. Vi anropa den `Translate` API för att utföra översättningen. Vi kan också kontakta andra funktioner för att fastställa språket i texten och stavningskontroll innan översättning.

Nu ska vi titta på hur vi börja vår klass:

```cs
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
        // at least show an error dialog when we get an unexpected error
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

Två Medlemsvariabler deklarerats innehåller här information om våra språk:

|||
|-|-|
|`languageCodes`<br>strängmatris|Cachelagrar språkkoder. Tjänsten översättare som använder korta koder `en` för engelska att identifiera språk.|
|`languageCodesAndTitles`<br>SortedDictionary|Maps ”vänligt” namnen i användargränssnittet tillbaka till de korta koder som används i API: et. Hållas sorteras alfabetiskt utan hänsyn till fall.|

Den första koden körs av vårt program är det `MainWindow` konstruktor. Först måste vi konfigurerar metoden `HandleExceptions` som globala felhanterare. På så sätt kan vi minst får ett felmeddelande om alla undantag som inte hanteras.

Nu ska kontrollera vi att API prenumeration nycklar är alla exakt 32 tecken. Om de inte är den troligaste orsaken är att *någon* har inte klistras in i sina API-nycklar (tsk). I det här fallet vi visas ett felmeddelande och borgen. (Skicka det här testet innebär inte nycklar är giltiga, förstås.)

Om vi har nycklar som är minst rätt längden på `InitializeComponent()` anropet hämtar rullande genom att söka efter inläsning och instansieringen XAML-beskrivning av fönstret programmets användargränssnitt.

Slutligen, vi ställa in språket nedrullningsbara menyer. Den här aktiviteten kräver tre separata metodanrop. Vi gå igenom dessa metoder i detalj i följande avsnitt.

## <a name="get-supported-languages"></a>Hämta de språk som stöds

Tjänsten Microsoft Translator stöder totalt 61 språk när detta skrivs och mer kan läggas till med jämna mellanrum. Så bäst det inte att hårdkoda språk som stöds i ditt program. Fråga i stället tjänsten översättare vilka språk som stöds. Alla språk som stöds kan översättas till ett annat språk som stöds.

Anropa den `Languages` API för att hämta en lista över språk som stöds.

Den `Languages` API tar en valfri GET-frågeparameter *omfång*. *scope* kan ha ett av tre värden: `translation`, `transliteration`, och `dictionary`. Vi kommer att använda värdet `translation`.

Den `Languages` API tar också ett valfritt HTTP-huvud `Accept-Language`. Värdet för det här huvudet anger det språk som returneras namnen på språk som stöds. Värdet ska vara en giltig BCP 47 språk tagg. Vi kommer att använda värdet `en` få Språknamnen på engelska.

Den `Languages` API: N returnerar en JSON-svar som liknar följande.

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

Vi vill extrahera språkkoder (till exempel `af`) och språk-namn (till exempel `Afrikaans`). Vi använder metoden NewtonSoft.Json [JsonConvert.DeserializeObject](https://www.newtonsoft.com/json/help/html/M_Newtonsoft_Json_JsonConvert_DeserializeObject__1.htm) att göra detta.

Med den kunskapen bakgrund kan skapa vi följande metod för att hämta språkkoder och deras namn.

```cs
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

`GetLanguagesForTranslate()` först skapar HTTP-begäran. Den `scope=translation` fråga strängparameter anger vi vill de språk som stöds för textöversättning. Vi lägga till Text översättning API-prenumeration nyckeln till vår huvuden för begäran. Vi också lägga till den `Accept-Language` huvud med värdet `en` att ange vi vill att de språk som stöds och som returneras i engelska.

När begäran har slutförts kan vi parsa JSON-svar och konvertera det till en ordlista. Vi lägga till språkkoder till den `languageCodes` medlemsvariabel. Vi sedan gå igenom nyckel/värde-par som innehåller språkkoder och språk som eget namn och lägger du till dem i `languageCodesAndTitles` medlemsvariabel. (Nedrullningsbara menyerna i formuläret visas de egna namn, men vi behöver koder för att begära översättningen.)

## <a name="populate-the-language-menus"></a>Fyll i menyerna språk

De flesta av våra användargränssnittet är definierad i XAML, så vi inte behöver göra mycket för att konfigurera förutom anropet `InitializeComponent()`. Endast klassificeringarna vi behöver göra är att lägga till egna språk namn till till och från listrutorna, vilket görs i `PopulateLanguageMenus()`.

```cs
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

Fylla menyerna är en enkel fråga för att gå över i `languageCodesAndTitles` ordlista och lägga till varje nyckel som heter ”vänligt”, på både menyer. När du har fyllt menyerna vi standardvärdet till och från språk ska identifiera (för automatisk identifiering språk) och engelska.

> [!TIP]
> Om vi inte anger en standardvalet för våra menyer, användaren kan välja **Översätt** utan att välja en till eller från språk. Standardvärdena eliminerar behovet av att åtgärda problemet.

Nu `MainWindow` har initierats, skapa användargränssnittet. Vi inte hämta kontrollen igen förrän användaren klickar på den **Översätt** knappen.

## <a name="perform-translation"></a>Utför konvertering

När användaren klickar på **Översätt**, WPF anropar den `TranslateButton_Click()` händelsehanteraren som visas här.

```cs
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

Här kan vi hämta först till och från språk, tillsammans med texten som användaren har angett, från formuläret.

Om käll-språket är identifiera vi kallar `DetectLanguage()` att fastställa språket i texten. Texten kan finnas på ett språk som översättare-API: er inte support (många fler språk kan identifieras än vad som kan översättas) eller Text Analytics API kanske inte kan identifiera den. I så fall kan visar vi ett meddelande att informera användaren och återgå utan översattes.

Om käll-språk är engelska (om det angetts eller identifiera), vi stavningen text med `CorrectSpelling()` och tillämpa eventuella ändringar. Korrigerade texten är fyllda tillbaka till inmatningsfält så att användaren vet korrigeringen görs. (Användaren komma före den text som översätts med ett bindestreck för att undertrycka stavningskontrollen.)

Om användaren inte har angett någon text, eller om till och är desamma, krävs ingen konvertering från språk. I det här fallet undvika vi att göra begäran.

Kod för att utföra begäran om översättning borde se bekant ut. Vi skapa URI: N, skapa en förfrågan, skicka och tolka svaret. Om du vill visa texten vi lagra den i den `TranslatedTextLabel` kontroll.

Vi skickar text till den `Translate` API i en serialiserad JSON-matris i brödtexten för en POST-begäran. JSON-matris kan innehålla flera texter för att översätta, men här vi innehåller bara ett.

HTTP-huvud med namnet `X-ClientTraceId` är valfritt. Värdet ska vara ett GUID. Klienten har angett trace-ID är användbart för trace-begäranden när saker inte fungerar som förväntat. Om du vill vara användbara skulle registreras värdet för X-ClientTraceID av klienten. En klient trace-ID och datumet för begäranden kan hjälpa Microsoft att diagnosticera problem som kan uppstå.

> [!NOTE]
> Den här självstudiekursen fokuserar på tjänsten Microsoft Translator så att vi inte tar upp den `DetectLanguage()` och `CorrectSpelling()` metoder i detalj. Tjänsterna Text Analytics och Bing stavningskontroll ange svar i JSON i stället för XML och Text Analytics kräver att begäran samt formateras som JSON. Dessa egenskaper konto för de flesta skillnader i kod från de metoder som vi har sett.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Microsoft översättare Text API-referens](http://docs.microsofttranslator.com/text-translate.html)
