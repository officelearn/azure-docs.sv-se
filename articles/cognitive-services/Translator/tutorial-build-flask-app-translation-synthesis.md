---
title: 'Självstudier: Skapa en Flask-app för att översätta syntetisera och analysera text: Translator Text API'
titleSuffix: Azure Cognitive Services
description: I den här självstudien skapar du en Flask-baserad webbapp som använder Azure Cognitive Services för att översätta text, analysera sentiment och syntetisera översatt text till tal. Vår fokus ligger på Python-kod och Flask-vägar som gör att våra program. Vi kommer inte lägger mycket tid på att Javascript som styr appen, men ger alla filer som du kan granska.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 04/02/2019
ms.author: erhopf
ms.openlocfilehash: 69e6797e91fc645e3bd3e3b300cea6852a662214
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/04/2019
ms.locfileid: "59007392"
---
# <a name="tutorial-build-a-flask-app-with-azure-cognitive-services"></a>Självstudier: Skapa en Flask-app med Azure Cognitive Services

I den här självstudien skapar du en Flask-webbapp som använder Azure Cognitive Services för att översätta text, analysera sentiment och syntetisera översatt text till tal. Vår fokus ligger på Python-kod och Flask-vägar som gör att våra program, men vi kommer hjälper dig med HTML och Javascript som sammanställer appen. Om du stöter på problem kan vi vet du att använda feedback-knappen nedan.

Här är den här självstudien tar upp:

> [!div class="checklist"]
> * Hämta nycklar för Azure-prenumeration
> * Konfigurera utvecklingsmiljön och installera beroenden
> * Skapa en Flask-app
> * Använd Translator Text API för att översätta text
> * Använda textanalys för att analysera positiv/negativ känsla av indata-text och översättningar
> * Använd Speech Services om du vill konvertera översatt text till syntetiskt tal
> * Kör Flask-app lokalt

> [!TIP]
> Om du vill gå vidare och att all kod på samma gång hela exemplet, tillsammans med build instruktioner finns på [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="what-is-flask"></a>Vad är Flask?

Flask är en microframework för att skapa webbprogram. Det innebär att Flask ger dig verktyg, bibliotek och tekniker som gör att du kan skapa ett webbprogram. Det här webbprogrammet kan vara en del webbsidor, en blogg, en wiki eller go som sak som en webbaserad kalender-program eller en extern webbplats.

Här följer några användbara länkar för dig som vill djupdykning efter den här självstudien:

* [Flask-dokumentation](http://flask.pocoo.org/)
* [Flask för provdockor som placerats – en Nybörjarguide till Flask](https://codeburst.io/flask-for-dummies-a-beginners-guide-to-flask-part-uno-53aec6afc5b1)

## <a name="prerequisites"></a>Förutsättningar

Nu ska vi se tangenterna programvara och prenumeration som du behöver för den här självstudien.

* [Python 3.5.2 eller senare](https://www.python.org/downloads/)
* [Git-verktyg](https://git-scm.com/downloads)
* En IDE eller textredigerare, till exempel [Visual Studio Code](https://code.visualstudio.com/) eller [Atom](https://atom.io/)  
* [Chrome](https://www.google.com/chrome/browser/) eller [Firefox](https://www.mozilla.org/firefox)
* En **textöversättning** prenumerationsnyckel (Observera att du inte behöver välja en region.)
* En **textanalys** prenumerationsnyckel i den **västra USA** region.
* En **Taltjänster** prenumerationsnyckel i den **västra USA** region.

## <a name="create-an-account-and-subscribe-to-resources"></a>Skapa ett konto och prenumererar på resurser

Som tidigare nämnts bör kommer du att behöva tre prenumerationsnycklar för den här självstudiekursen. Det innebär att du behöver skapa en resurs i ditt Azure-konto för:
* Translator Text
* Textanalys
* Taltjänster

Använd [skapa ett Cognitive Services-konto i Azure-portalen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för stegvisa instruktioner för att skapa resurser.

> [!IMPORTANT]
> Den här självstudien skapar du dina resurser i regionen USA, västra. Om du använder en annan region, kommer du behöva justera den grundläggande Webbadressen i var och en av dina Python-filer.

## <a name="set-up-your-dev-environment"></a>Konfigurera din utvecklingsmiljö

Innan du skapar din Flask-webbapp måste du skapa en arbetskatalog för ditt projekt och installera några Python-paket.

### <a name="create-a-working-directory"></a>Skapa en arbetskatalog

1. Öppna kommandoraden (Windows) eller terminal (Mac OS-/ Linux). Skapa sedan en arbetskatalog och underkataloger för ditt projekt:  

   ```
   mkdir -p flask-cog-services/static/scripts && mkdir flask-cog-services/templates
   ```
2. Ändra till ditt projekt arbetskatalog:  

   ```
   cd flask-cog-services
   ```

### <a name="create-and-activate-your-virtual-environment-with-virtualenv"></a>Skapa och aktivera den virtuella miljön med `virtualenv`

Nu ska vi skapa en virtuell miljö för vår Flask-app med `virtualenv`. Med hjälp av en virtuell miljö säkerställer att du har en ren miljö att arbeta från.

1. Kör detta kommando för att skapa en virtuell miljö i din arbetskatalog: **macOS-/ Linux:**
   ```
   virtualenv venv --python=python3
   ```
   Vi har uttryckligen deklarera att den virtuella miljön ska använda Python 3. Detta säkerställer att användare med flera Python installationer använder rätt version.

   **Windows CMD / Bash-Windows:**
   ```
   virtualenv venv
   ```
   För att göra det enkelt, vi namnger din venv virtuell miljö.

2. Kommandon för att aktivera den virtuella miljön varierar beroende på plattform/gränssnittet:   

   | Plattform | Shell | Kommando |
   |----------|-------|---------|
   | macOS/Linux | Bash/zsh | `source venv/bin/activate` |
   | Windows | Bash | `source venv/Scripts/activate` |
   | | Kommandorad | `venv\Scripts\activate.bat` |
   | | PowerShell | `venv\Scripts\Activate.ps1` |

   När du har kört det här kommandot din kommandorad eller en terminalsession ska föregås av `venv`.

3. Du kan inaktivera sessionen när som helst genom att skriva detta i kommandoraden eller terminal: `deactivate`.

> [!NOTE]
> Python har omfattande dokumentation för att skapa och hantera virtuella miljöer kan du se [virtuell miljö](https://virtualenv.pypa.io/en/latest/).

### <a name="install-requests"></a>Installera begäranden

Begäranden är en populär modul som används för att skicka HTTP 1.1-begäranden. Det finns inget behov att manuellt lägga till frågesträngar till din URL: er eller koda din postdata formuläret.

1. Om du vill installera begäranden, kör du:

   ```
   pip install requests
   ```

> [!NOTE]
> Om du vill ha mer information om förfrågningar, se [begäranden: HTTP för människor](http://docs.python-requests.org/en/master/).

### <a name="install-and-configure-flask"></a>Installera och konfigurera Flask

Därefter måste vi installera Flask. Flask hanterar routning för webbapp och gör att vi kan göra server-till-server-anrop som döljer våra prenumerationsnycklar från användaren.

1. Om du vill installera Flask, kör du:
   ```
   pip install Flask
   ```
   Kontrollera Flask installerades. Kör:
   ```
   flask --version
   ```
   Versionen ska skrivas till terminal. Allt annat innebär att något gick fel.

2. För att köra Flask-app, kan du antingen använda kommandot flask eller Python's -m-växel med Flask. Innan du kan göra det måste du berätta din terminal vilken app du arbetar med genom att exportera den `FLASK_APP` miljövariabeln:

   **macOS/Linux**:
   ```
   export FLASK_APP=app.py
   ```

   **Windows**:
   ```
   set FLASK_APP=app.py
   ```

## <a name="create-your-flask-app"></a>Skapa en Flask-app

I det här avsnittet ska du skapa en app i Flask barebones som returnerar en HTML-fil när användare når roten av din app. Utgifter inte för mycket tid på att välja isär koden, vi återkommer att uppdatera den här filen senare.

### <a name="what-is-a-flask-route"></a>Vad är en Flask-väg?

Låt oss ta en minut att tala om ”[vägar](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.route)”. Routing används för att binda en URL till en specifik funktion. Flask använder väg dekoratörer för att registrera funktioner till specifika URL: er. Till exempel när användaren navigerar till roten (`/`) för vår webbapp `index.html` återges.  

```python
@app.route('/')
def index():
    return render_template('index.html')
```

Låt oss ta en titt på ytterligare ett exempel till tång den här startsidan.

```python
@app.route('/about')
def about():
    return render_template('about.html')
```

Den här koden säkerställer att när en användare navigerar till `http://your-web-app.com/about` som den `about.html` filen återges.

Även om de här exemplen visar hur du kan visa html-sidor för en användare, kan vägar också användas för att anropa API: er när en knapp trycks eller ta valfritt antal åtgärder utan att behöva navigera bort från startsidan. Visas det i praktiken när du skapar vägar för översättning, känsla och talsyntes.

### <a name="get-started"></a>Kom igång

1. Öppna projektet i din IDE och sedan skapa en fil med namnet `app.py` i roten för din arbetskatalog. Kopiera den här koden i `app.py` och spara:

   ```python
   from flask import Flask, render_template, url_for, jsonify, request

   app = Flask(__name__)
   app.config['JSON_AS_ASCII'] = False

   @app.route('/')
   def index():
       return render_template('index.html')
   ```

   Den här kodblocket anger hur appen att visa `index.html` när användaren navigerar till roten för din webbapp (`/`).

2. Nu ska vi skapa klientdelen för vår webbapp. Skapa en fil med namnet `index.html` i den `templates` directory. Kopiera den här koden i `templates/index.html`.

   ```html
   <!doctype html>
   <html lang="en">
     <head>
       <!-- Required metadata tags -->
       <meta charset="utf-8">
       <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
       <meta name="description" content="Translate and analyze text with Azure Cognitive Services.">
       <!-- Bootstrap CSS -->
       <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" integrity="sha384-Gn5384xqQ1aoWXA+058RXPxPg6fy4IWvTNh0E263XmFcJlSAwiGgFAW/dAiS6JXm" crossorigin="anonymous">
       <title>Translate and analyze text with Azure Cognitive Services</title>
     </head>
     <body>
       <div class="container">
         <h1>Translate, synthesize, and analyze text with Azure</h1>
         <p>This simple web app uses Azure for text translation, text-to-speech conversion, and sentiment analysis of input text and translations. Learn more about <a href="https://docs.microsoft.com/azure/cognitive-services/">Azure Cognitive Services</a>.
        </p>
        <!-- HTML provided in the following sections goes here. -->

        <!-- End -->
       </div>

       <!-- Required Javascript for this tutorial -->
       <script src="https://code.jquery.com/jquery-3.2.1.slim.min.js" integrity="sha384-KJ3o2DKtIkvYIK3UENzmM7KCkRr/rE9/Qpg6aAZGJwFDMVNA/GpGFF93hXpG5KkN" crossorigin="anonymous"></script>
       <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.12.9/umd/popper.min.js" integrity="sha384-ApNbgh9B+Y1QKtv3Rn7W3mgPxhU9K/ScQsAP7hUibX39j7fakFPskvXusvfa0b4Q" crossorigin="anonymous"></script>
       <script src="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/js/bootstrap.min.js" integrity="sha384-JZR6Spejh4U02d8jOt6vLEHfe/JQGiRRSQQxSfFWpi1MquVdAyjUar5+76PVCmYl" crossorigin="anonymous"></script>
       <script type = "text/javascript" src ="static/scripts/main.js"></script>
     </body>
   </html>
   ```

3. Nu ska vi testa Flask-app. Från terminalen kör du:

   ```
   flask run
   ```

4. Öppna en webbläsare och gå till den URL som anges. Du bör se din ensidesapp. Tryck på **Ctrl + c** att avsluta appen.

## <a name="translate-text"></a>Översätt text

Nu när du har en uppfattning om hur en enkel app i Flask fungerar, låt oss:

* Skriva några Python för att anropa Translator Text API och returnera ett svar
* Skapa en Flask-väg för att anropa Python-kod
* Uppdatera HTML med ett område för textinmatning och översättning, en språkväljare och översätta knappen
* Skriva Javascript som tillåter användare att interagera med en Flask-app från HTML

### <a name="call-the-translator-text-api"></a>Anropa API för textöversättning

Det första du behöver göra är att skriva en funktion för att anropa Translator Text API. Den här funktionen tar två argument: `text_input` och `language_output`. Den här funktionen anropas när användaren trycker på knappen Översätt i din app. Textområde i HTML-koden skickas som den `text_input`, och värdet på val av språk i HTML-koden skickas som `language_output`.

1. Låt oss börja med att skapa en fil med namnet `translate.py` i roten för din arbetskatalog.
2. Lägg sedan till den här koden till `translate.py`. Den här funktionen tar två argument: `text_input` och `language_output`.
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   # If you prefer to use environment variables, see Extra Credit for more info.
   subscription_key = 'YOUR_TRANSLATOR_TEXT_SUBSCRIPTION_KEY'

   # Our Flask route will supply two arguments: text_input and language_output.
   # When the translate text button is pressed in our Flask app, the Ajax request
   # will grab these values from our web app, and use them in the request.
   # See main.js for Ajax calls.
   def get_translation(text_input, language_output):
       base_url = 'https://api.cognitive.microsofttranslator.com'
       path = '/translate?api-version=3.0'
       params = '&to=' + language_output
       constructed_url = base_url + path + params

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = [{
           'text' : text_input
       }]
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Lägg till din prenumerationsnyckel för Translator Text och spara.

### <a name="add-a-route-to-apppy"></a>Lägg till en väg till `app.py`

Därefter måste du skapa en väg i din Flask-app som anropar `translate.py`. Den här vägen anropas varje gång en användare trycker på knappen Översätt i din app.

För den här appen färdvägen kommer att acceptera `POST` begäranden. Det beror på att funktionen förväntar sig text för översättning och en utdata-språk för översättning.

Flask innehåller hjälpfunktioner hjälper dig att analysera och hantera varje begäran. I koden, `get_json()` returnerar data från den `POST` begäran som JSON. Sedan använder `data['text']` och `data['to']`, språk-värdena text- och utdata skickas till `get_translation()` funktionen som är tillgängliga från `translate.py`. Det sista steget är att returnera svaret som JSON, eftersom du behöver att visa dessa data i din webbapp.

I följande avsnitt kommer du upprepa den här processen när du skapar vägar för sentiment analys- och taligenkänning syntes.

1. Öppna `app.py` och leta upp importuttryck överst i `app.py` och Lägg till följande rad:

   ```python
   import translate
   ```
   Vår Flask-app kan nu använda metoden som är tillgänglig via `translate.py`.

2. Kopiera den här koden i slutet av `app.py` och spara:

   ```python
   @app.route('/translate-text', methods=['POST'])
   def translate_text():
       data = request.get_json()
       text_input = data['text']
       translation_output = data['to']
       response = translate.get_translation(text_input, translation_output)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>Uppdatera `index.html`

Nu när du har en funktion för att översätta text och en väg i Flask-app för att anropa den är nästa steg att börja bygga HTML för din app. I HTML-koden nedan gör några saker:

* Innehåller ett textområde där användare kan ange text för översättning.
* Innehåller en språkväljare.
* Innehåller HTML-element för att rendera identifierat språk och förtroende resultat returnerades vid översättning.
* Innehåller en skrivskyddad textområde där translation utdata visas.
* Innehåller platshållare för sentiment analys- och taligenkänning syntes kod som du lägger till i den här filen senare under kursen.

Nu ska vi uppdatera `index.html`.

1. Öppna `index.html` och leta upp kommentarerna kod:
   ```html
   <!-- HTML provided in the following sections goes here. -->

   <!-- End -->
   ```

2. Ersätt Koden kommentarer med det här HTML-blocket:
   ```html
   <div class="row">
     <div class="col">
       <form>
         <!-- Enter text to translate. -->
         <div class="form-group">
           <label for="text-to-translate"><strong>Enter the text you'd like to translate:</strong></label>
           <textarea class="form-control" id="text-to-translate" rows="5"></textarea>
         </div>
         <!-- Select output language. -->
         <div class="form-group">
           <label for="select-language"><strong>Translate to:</strong></label>
           <select class="form-control" id="select-language">
             <option value="ar">Arabic</option>
             <option value="ca">Catalan</option>
             <option value="zh-Hans">Chinese (Simplified)</option>
             <option value="zh-Hant">Chinese (Traditional)</option>
             <option value="hr">Croatian</option>
             <option value="en">English</option>
             <option value="fr">French</option>
             <option value="de">German</option>
             <option value="el">Greek</option>
             <option value="he">Hebrew</option>
             <option value="hi">Hindi</option>
             <option value="it">Italian</option>
             <option value="ja">Japanese</option>
             <option value="ko">Korean</option>
             <option value="pt">Portuguese</option>
             <option value="ru">Russian</option>
             <option value="es">Spanish</option>
             <option value="th">Thai</option>
             <option value="tr">Turkish</option>
             <option value="vi">Vietnamese</option>
           </select>
         </div>
         <button type="submit" class="btn btn-primary mb-2" id="translate">Translate text</button></br>
         <div id="detected-language" style="display: none">
           <strong>Detected language:</strong> <span id="detected-language-result"></span><br />
           <strong>Detection confidence:</strong> <span id="confidence"></span><br /><br />
         </div>

         <!-- Start sentiment code-->

         <!-- End sentiment code -->

       </form>
     </div>
     <div class="col">
       <!-- Translated text returned by the Translate API is rendered here. -->
       <form>
         <div class="form-group" id="translator-text-response">
           <label for="translation-result"><strong>Translated text:</strong></label>
           <textarea readonly class="form-control" id="translation-result" rows="5"></textarea>
         </div>

         <!-- Start voice font selection code -->

         <!-- End voice font selection code -->

       </form>

       <!-- Add Speech Synthesis button and audio element -->

       <!-- End Speech Synthesis button -->

     </div>
   </div>
   ```

Nästa steg är att skriva några Javascript. Det här är bron mellan HTML och Flask rutten.

### <a name="create-mainjs"></a>Skapa `main.js`  

Den `main.js` filen är bron mellan HTML och Flask rutten. Din app ska använda en kombination av jQuery, Ajax och XMLHttpRequest att återge innehåll och göra `POST` begäranden till Flask-vägar.

I följande kodexempel används innehållet från HTML att konstruera en begäran om att Flask-vägen. Mer specifikt innehållet i textområdet för och språkväljaren tilldelas till variabler, och sedan skickas med i begäran om att `translate-text`.

Koden går igenom svaret och uppdaterar HTML med translation, identifierat språk och förtroendepoäng.

1. Skapa en fil med namnet från IDE, `main.js` i den `static/scripts` directory.
2. Kopiera den här koden i `static/scripts/main.js`:
   ```javascript
   //Initiate jQuery on load.
   $(function() {
     //Translate text with flask route
     $("#translate").on("click", function(e) {
       e.preventDefault();
       var translateVal = document.getElementById("text-to-translate").value;
       var languageVal = document.getElementById("select-language").value;
       var translateRequest = { 'text': translateVal, 'to': languageVal }

       if (translateVal !== "") {
         $.ajax({
           url: '/translate-text',
           method: 'POST',
           headers: {
               'Content-Type':'application/json'
           },
           dataType: 'json',
           data: JSON.stringify(translateRequest),
           success: function(data) {
             for (var i = 0; i < data.length; i++) {
               document.getElementById("translation-result").textContent = data[i].translations[0].text;
               document.getElementById("detected-language-result").textContent = data[i].detectedLanguage.language;
               if (document.getElementById("detected-language-result").textContent !== ""){
                 document.getElementById("detected-language").style.display = "block";
               }
               document.getElementById("confidence").textContent = data[i].detectedLanguage.score;
             }
           }
         });
       };
     });
     // In the following sections, you'll add code for sentiment analysis and
     // speech synthesis here.
   })
   ```

### <a name="test-translation"></a>Testa översättning

Nu ska vi testa translation i appen.

```
flask run
```

Navigera till den angivna serveradressen. Ange text i indata området, Välj ett språk och tryck på översätta. Du bör få en översättning. Om det inte fungerar kontrollerar du att du har lagt till din prenumerationsnyckel.

> [!TIP]
> Om du har gjort ändringarna visas inte, eller om appen fungerar inte som förväntat, försök rensa cacheminnet eller genom att öppna ett fönster för privat/inkognito.

Tryck på **CTRL + c** att avsluta appen och sedan gå till nästa avsnitt.

## <a name="analyze-sentiment"></a>Analysera sentiment

Den [API för textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) kan användas för att utföra känsloanalys, extrahera nyckelfraser i texten eller identifiera källspråket. I den här appen kan vi använda attitydanalys för att avgöra om den angivna texten är neutral, positivt eller negativt. API:t returnerar en poäng mellan 0 och 1. Poäng nära 1 anger positiv känsla och poäng nära 0 anger negativ känsla.

I det här avsnittet ska du göra några saker:

* Skriva några Python för att anropa API för textanalys för att utföra känsloanalys och returnera ett svar
* Skapa en Flask-väg för att anropa Python-kod
* Uppdatera HTML med ett sentimentpoäng och en knapp för att utföra analyser
* Skriva Javascript som tillåter användare att interagera med en Flask-app från HTML

### <a name="call-the-text-analytics-api"></a>Anropa API:t för textanalys

Nu ska vi skriva en funktion för att anropa API för textanalys. Den här funktionen tar fyra argument: `input_text`, `input_language`, `output_text`, och `output_language`. Den här funktionen anropas när användaren trycker på knappen Kör sentiment analys i din app. Data som anges av användaren från text yt- och språk Väljaren, samt identifierade språk och translation utdata tillhandahålls med varje begäran. Objektet response innehåller sentimentpoäng för käll- och översättning. I följande avsnitt ska du skriva några Javascript för att parsa svaret och använda den i din app. För tillfället fokuserar vi på anrop API för textanalys.

1. Nu ska vi skapa en fil med namnet `sentiment.py` i roten för din arbetskatalog.
2. Lägg sedan till den här koden till `sentiment.py`.
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   subscription_key = 'YOUR_TEXT_ANALYTICS_SUBSCRIPTION_KEY'

   # Our Flask route will supply four arguments: input_text, input_language,
   # output_text, output_language.
   # When the run sentiment analysis button is pressed in our Flask app,
   # the Ajax request will grab these values from our web app, and use them
   # in the request. See main.js for Ajax calls.

   def get_sentiment(input_text, input_language, output_text, output_language):
       base_url = 'https://westus.api.cognitive.microsoft.com/text/analytics'
       path = '/v2.0/sentiment'
       constructed_url = base_url + path

       headers = {
           'Ocp-Apim-Subscription-Key': subscription_key,
           'Content-type': 'application/json',
           'X-ClientTraceId': str(uuid.uuid4())
       }

       # You can pass more than one object in body.
       body = {
           'documents': [
               {
                   'language': input_language,
                   'id': '1',
                   'text': input_text
               },
               {
                   'language': output_language,
                   'id': '2',
                   'text': output_text
               }
           ]
       }
       response = requests.post(constructed_url, headers=headers, json=body)
       return response.json()
   ```
3. Lägg till din prenumerationsnyckel för textanalys och spara.

### <a name="add-a-route-to-apppy"></a>Lägg till en väg till `app.py`

Nu ska vi skapa en väg i din Flask-app som anropar `sentiment.py`. Den här vägen anropas varje gång en användare trycker på knappen Kör sentiment analys i din app. Som vägen för den här vägen kommer att acceptera `POST` begäranden eftersom funktionen förväntar sig argument.

1. Öppna `app.py` och leta upp importuttryck överst i `app.py` och uppdatera den:

   ```python
   import translate, sentiment
   ```
   Vår Flask-app kan nu använda metoden som är tillgänglig via `sentiment.py`.

2. Kopiera den här koden i slutet av `app.py` och spara:
   ```python
   @app.route('/sentiment-analysis', methods=['POST'])
   def sentiment_analysis():
       data = request.get_json()
       input_text = data['inputText']
       input_lang = data['inputLanguage']
       output_text = data['outputText']
       output_lang =  data['outputLanguage']
       response = sentiment.get_sentiment(input_text, input_lang, output_text, output_lang)
       return jsonify(response)
   ```

### <a name="update-indexhtml"></a>Uppdatera `index.html`

Nu när du har en funktion för att köra attitydanalys och en väg i Flask-app för att anropa det, är nästa steg att börja skriva HTML för din app. I HTML-koden nedan gör några saker:

* Lägger till en knapp i din app för att köra attitydanalys
* Lägger till ett element som förklarar sentiment bedömning
* Lägger till ett element för att visa sentimentpoäng

1. Öppna `index.html` och leta upp kommentarerna kod:
   ```html
   <!-- Start sentiment code-->

   <!-- End sentiment code -->
   ```

2. Ersätt Koden kommentarer med det här HTML-blocket:
   ```html
   <button type="submit" class="btn btn-primary mb-2" id="sentiment-analysis">Run sentiment analysis</button></br>
   <div id="sentiment" style="display: none">
      <p>Sentiment scores are provided on a 1 point scale. The closer the sentiment score is to 1, indicates positive sentiment. The closer it is to 0, indicates negative sentiment.</p>
      <strong>Sentiment score for input:</strong> <span id="input-sentiment"></span><br />
      <strong>Sentiment score for translation:</strong> <span id="translation-sentiment"></span>
   </div>
   ```

### <a name="update-mainjs"></a>Uppdatera `main.js`

I följande kodexempel används innehållet från HTML att konstruera en begäran om att Flask-vägen. Mer specifikt innehållet i textområdet för och språkväljaren tilldelas till variabler, och sedan skickas med i begäran till den `sentiment-analysis` väg.

Koden går igenom svaret och uppdaterar HTML med sentimentpoäng.

1. Skapa en fil med namnet från IDE, `main.js` i den `static` directory.

2. Kopiera den här koden i `static/scripts/main.js`:
   ```javascript
   //Run sentinment analysis on input and translation.
   $("#sentiment-analysis").on("click", function(e) {
     e.preventDefault();
     var inputText = document.getElementById("text-to-translate").value;
     var inputLanguage = document.getElementById("detected-language-result").innerHTML;
     var outputText = document.getElementById("translation-result").value;
     var outputLanguage = document.getElementById("select-language").value;

     var sentimentRequest = { "inputText": inputText, "inputLanguage": inputLanguage, "outputText": outputText,  "outputLanguage": outputLanguage };

     if (inputText !== "") {
       $.ajax({
         url: "/sentiment-analysis",
         method: "POST",
         headers: {
             "Content-Type":"application/json"
         },
         dataType: "json",
         data: JSON.stringify(sentimentRequest),
         success: function(data) {
           for (var i = 0; i < data.documents.length; i++) {
             if (typeof data.documents[i] !== "undefined"){
               if (data.documents[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.documents[i].score;
               }
               if (data.documents[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.documents[i].score;
               }
             }
           }
           for (var i = 0; i < data.errors.length; i++) {
             if (typeof data.errors[i] !== "undefined"){
               if (data.errors[i].id === "1") {
                 document.getElementById("input-sentiment").textContent = data.errors[i].message;
               }
               if (data.errors[i].id === "2") {
                 document.getElementById("translation-sentiment").textContent = data.errors[i].message;
               }
             }
           }
           if (document.getElementById("input-sentiment").textContent !== '' && document.getElementById("translation-sentiment").textContent !== ""){
             document.getElementById("sentiment").style.display = "block";
           }
         }
       });
     }
   });
   // In the next section, you'll add code for speech synthesis here.
   ```

### <a name="test-sentiment-analysis"></a>Testa attitydanalys

Nu ska vi testa attitydanalys i appen.

```
flask run
```

Navigera till den angivna serveradressen. Ange text i indata området, Välj ett språk och tryck på översätta. Du bör få en översättning. Tryck sedan på knappen Kör sentiment analys. Du bör se två poäng. Om det inte fungerar kontrollerar du att du har lagt till din prenumerationsnyckel.

> [!TIP]
> Om du har gjort ändringarna visas inte, eller om appen fungerar inte som förväntat, försök rensa cacheminnet eller genom att öppna ett fönster för privat/inkognito.

Tryck på **CTRL + c** att avsluta appen och sedan gå till nästa avsnitt.

## <a name="convert-text-to-speech"></a>Konvertera text-till-tal

Den [text till tal-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech) gör att din app för att omvandla text till naturlig människoliknande syntetiskt tal. Tjänsten stöder standard neural och anpassade röster. Vår exempelapp använder en handfull tillgängliga röster för en fullständig lista, se [språk som stöds](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech).

I det här avsnittet ska du göra några saker:

* Skriva några Python för att konvertera text till tal med text till tal-API
* Skapa en Flask-väg för att anropa Python-kod
* Uppdatera HTML med en knapp för att omvandla text till tal och ett element för ljuduppspelning
* Skriva Javascript som tillåter användare att interagera med din Flask-app

### <a name="call-the-text-to-speech-api"></a>Anropa text till tal-API

Nu ska vi skriva en funktion för att konvertera text till tal. Den här funktionen tar två argument: `input_text` och `voice_font`. Den här funktionen anropas när användaren trycker på knappen Konvertera text till tal i din app. `input_text` är translation utdata som returneras av anropet att översätta text, `voice_font` är värdet från väljaren för röst-teckensnitt i HTML-koden.

1. Nu ska vi skapa en fil med namnet `synthesize.py` i roten för din arbetskatalog.

2. Lägg sedan till den här koden till `synthesize.py`.
   ```Python
   import os, requests, time
   from xml.etree import ElementTree

   class TextToSpeech(object):
       def __init__(self, input_text, voice_font):
           subscription_key = 'YOUR_SPEECH_SERVICES_SUBSCRIPTION_KEY'
           self.subscription_key = subscription_key
           self.input_text = input_text
           self.voice_font = voice_font
           self.timestr = time.strftime('%Y%m%d-%H%M')
           self.access_token = None

       # This function performs the token exchange.
       def get_token(self):
           fetch_token_url = 'https://westus.api.cognitive.microsoft.com/sts/v1.0/issueToken'
           headers = {
               'Ocp-Apim-Subscription-Key': self.subscription_key
           }
           response = requests.post(fetch_token_url, headers=headers)
           self.access_token = str(response.text)

       # This function calls the TTS endpoint with the access token.
       def save_audio(self):
           base_url = 'https://westus.tts.speech.microsoft.com/'
           path = 'cognitiveservices/v1'
           constructed_url = base_url + path
           headers = {
               'Authorization': 'Bearer ' + self.access_token,
               'Content-Type': 'application/ssml+xml',
               'X-Microsoft-OutputFormat': 'riff-24khz-16bit-mono-pcm',
               'User-Agent': 'YOUR_RESOURCE_NAME',
           }
           # Build the SSML request with ElementTree
           xml_body = ElementTree.Element('speak', version='1.0')
           xml_body.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-us')
           voice = ElementTree.SubElement(xml_body, 'voice')
           voice.set('{http://www.w3.org/XML/1998/namespace}lang', 'en-US')
           voice.set('name', 'Microsoft Server Speech Text to Speech Voice {}'.format(self.voice_font))
           voice.text = self.input_text
           # The body must be encoded as UTF-8 to handle non-ascii characters.
           body = ElementTree.tostring(xml_body, encoding="utf-8")

           #Send the request
           response = requests.post(constructed_url, headers=headers, data=body)

           # Write the response as a wav file for playback. The file is located
           # in the same directory where this sample is run.
           return response.content
   ```
3. Lägg till din prenumerationsnyckel för Speech Services och spara.

### <a name="add-a-route-to-apppy"></a>Lägg till en väg till `app.py`

Nu ska vi skapa en väg i din Flask-app som anropar `synthesize.py`. Den här vägen anropas varje gång en användare trycker på knappen Konvertera text till tal i din app. Som vägar för översättning och attitydanalys, kommer den här vägen att acceptera `POST` begäranden eftersom funktionen förväntar sig två argument: texten som ska syntetisera och rösttyp för uppspelning.

1. Öppna `app.py` och leta upp importuttryck överst i `app.py` och uppdatera den:

   ```python
   import translate, sentiment, synthesize
   ```
   Vår Flask-app kan nu använda metoden som är tillgänglig via `synthesize.py`.

2. Kopiera den här koden i slutet av `app.py` och spara:

   ```Python
   @app.route('/text-to-speech', methods=['POST'])
   def text_to_speech():
       data = request.get_json()
       text_input = data['text']
       voice_font = data['voice']
       tts = synthesize.TextToSpeech(text_input, voice_font)
       tts.get_token()
       audio_response = tts.save_audio()
       return audio_response
   ```

### <a name="update-indexhtml"></a>Uppdatera `index.html`

Nu när du har en funktion för att omvandla text till tal och en väg i Flask-app för att anropa det, är nästa steg att börja skriva HTML för din app. I HTML-koden nedan gör några saker:

* Ger en röst-markering listrutan
* Lägger till en knapp för att omvandla text till tal
* Lägger till ett ljud-element som används för att spela upp syntetiskt tal

1. Öppna `index.html` och leta upp kommentarerna kod:
   ```html
   <!-- Start voice font selection code -->

   <!-- End voice font selection code -->
   ```

2. Ersätt Koden kommentarer med det här HTML-blocket:
   ```html
   <div class="form-group">
     <label for="select-voice"><strong>Select voice font:</strong></label>
     <select class="form-control" id="select-voice">
       <option value="(ar-SA, Naayf)">Arabic | Male | Naayf</option>
       <option value="(ca-ES, HerenaRUS)">Catalan | Female | HerenaRUS</option>
       <option value="(zh-CN, HuihuiRUS)">Chinese (Mainland) | Female | HuihuiRUS</option>
       <option value="(zh-CN, Kangkang, Apollo)">Chinese (Mainland) | Male | Kangkang, Apollo</option>
       <option value="(zh-HK, Tracy, Apollo)">Chinese (Hong Kong)| Female | Tracy, Apollo</option>
       <option value="(zh-HK, Danny, Apollo)">Chinese (Hong Kong) | Male | Danny, Apollo</option>
       <option value="(zh-TW, Yating, Apollo)">Chinese (Taiwan)| Female | Yaiting, Apollo</option>
       <option value="(zh-TW, Zhiwei, Apollo)">Chinese (Taiwan) | Male | Zhiwei, Apollo</option>
       <option value="(hr-HR, Matej)">Croatian | Male | Matej</option>
       <option value="(en-US, Jessa24kRUS)">English (US) | Female | Jessa24kRUS</option>
       <option value="(en-US, Guy24kRUS)">English (US) | Male | Guy24kRUS</option>
       <option value="(en-IE, Sean)">English (IE) | Male | Sean</option>
       <option value="(fr-FR, Julie, Apollo)">French | Female | Julie, Apollo</option>
       <option value="(fr-FR, HortenseRUS)">French | Female | Julie, HortenseRUS</option>
       <option value="(fr-FR, Paul, Apollo)">French | Male | Paul, Apollo</option>
       <option value="(de-DE, Hedda)">German | Female | Hedda</option>
       <option value="(de-DE, HeddaRUS)">German | Female | HeddaRUS</option>
       <option value="(de-DE, Stefan, Apollo)">German | Male | Apollo</option>
       <option value="(el-GR, Stefanos)">Greek | Male | Stefanos</option>
       <option value="(he-IL, Asaf)">Hebrew (Isreal) | Male | Asaf</option>
       <option value="(hi-IN, Kalpana, Apollo)">Hindi | Female | Kalpana, Apollo</option>
       <option value="(hi-IN, Hemant)">Hindi | Male | Hemant</option>
       <option value="(it-IT, LuciaRUS)">Italian | Female | LuciaRUS</option>
       <option value="(it-IT, Cosimo, Apollo)">Italian | Male | Cosimo, Apollo</option>
       <option value="(ja-JP, Ichiro, Apollo)">Japanese | Male | Ichiro</option>
       <option value="(ja-JP, HarukaRUS)">Japanese | Female | HarukaRUS</option>
       <option value="(ko-KR, HeamiRUS)">Korean | Female | Haemi</option>
       <option value="(pt-BR, HeloisaRUS)">Portuguese (Brazil) | Female | HeloisaRUS</option>
       <option value="(pt-BR, Daniel, Apollo)">Portuguese (Brazil) | Male | Daniel, Apollo</option>
       <option value="(pt-PT, HeliaRUS)">Portuguese (Portugal) | Female | HeliaRUS</option>
       <option value="(ru-RU, Irina, Apollo)">Russian | Female | Irina, Apollo</option>
       <option value="(ru-RU, Pavel, Apollo)">Russian | Male | Pavel, Apollo</option>
       <option value="(ru-RU, EkaterinaRUS)">Russian | Female | EkaterinaRUS</option>
       <option value="(es-ES, Laura, Apollo)">Spanish | Female | Laura, Apollo</option>
       <option value="(es-ES, HelenaRUS)">Spanish | Female | HelenaRUS</option>
       <option value="(es-ES, Pablo, Apollo)">Spanish | Male | Pablo, Apollo</option>
       <option value="(th-TH, Pattara)">Thai | Male | Pattara</option>
       <option value="(tr-TR, SedaRUS)">Turkish | Female | SedaRUS</option>
       <option value="(vi-VN, An)">Vietnamese | Male | An</option>
     </select>
   </div>
   ```

3. Leta sedan upp kommentarerna kod:
   ```html
   <!-- Add Speech Synthesis button and audio element -->

   <!-- End Speech Synthesis button -->
   ```

4. Ersätt Koden kommentarer med det här HTML-blocket:

```html
<button type="submit" class="btn btn-primary mb-2" id="text-to-speech">Convert text-to-speech</button>
<div id="audio-playback">
  <audio id="audio" controls>
    <source id="audio-source" type="audio/mpeg" />
  </audio>
</div>
```

5. Se till att spara ditt arbete.

### <a name="update-mainjs"></a>Uppdatera `main.js`

I följande kodexempel används innehållet från HTML att konstruera en begäran om att Flask-vägen. Mer specifikt översättningen och rösttyp är tilldelade till variabler och sedan skickas med i begäran till den `text-to-speech` väg.

Koden går igenom svaret och uppdaterar HTML med sentimentpoäng.

1. Skapa en fil med namnet från IDE, `main.js` i den `static` directory.
2. Kopiera den här koden i `static/scripts/main.js`:
   ```javascript
   // Convert text-to-speech
   $("#text-to-speech").on("click", function(e) {
     e.preventDefault();
     var ttsInput = document.getElementById("translation-result").value;
     var ttsVoice = document.getElementById("select-voice").value;
     var ttsRequest = { 'text': ttsInput, 'voice': ttsVoice }

     var xhr = new XMLHttpRequest();
     xhr.open("post", "/text-to-speech", true);
     xhr.setRequestHeader("Content-Type", "application/json");
     xhr.responseType = "blob";
     xhr.onload = function(evt){
       if (xhr.status === 200) {
         audioBlob = new Blob([xhr.response], {type: "audio/mpeg"});
         audioURL = URL.createObjectURL(audioBlob);
         if (audioURL.length > 5){
           var audio = document.getElementById("audio");
           var source = document.getElementById("audio-source");
           source.src = audioURL;
           audio.load();
           audio.play();
         }else{
           console.log("An error occurred getting and playing the audio.")
         }
       }
     }
     xhr.send(JSON.stringify(ttsRequest));
   });
   // Code for automatic language selection goes here.
   ```
3. Nästan klart. Det sista du ska göra är att lägga till kod till `main.js` att automatiskt välja en rösttyp baserat på det språk som valts för översättning. Lägg till den här kodblocket till `main.js`:
   ```javascript
   // Automatic voice font selection based on translation output.
   $('select[id="select-language"]').change(function(e) {
     if ($(this).val() == "ar"){
       document.getElementById("select-voice").value = "(ar-SA, Naayf)";
     }
     if ($(this).val() == "ca"){
       document.getElementById("select-voice").value = "(ca-ES, HerenaRUS)";
     }
     if ($(this).val() == "zh-Hans"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "zh-Hant"){
       document.getElementById("select-voice").value = "(zh-HK, Tracy, Apollo)";
     }
     if ($(this).val() == "hr"){
       document.getElementById("select-voice").value = "(hr-HR, Matej)";
     }
     if ($(this).val() == "en"){
       document.getElementById("select-voice").value = "(en-US, Jessa24kRUS)";
     }
     if ($(this).val() == "fr"){
       document.getElementById("select-voice").value = "(fr-FR, HortenseRUS)";
     }
     if ($(this).val() == "de"){
       document.getElementById("select-voice").value = "(de-DE, HeddaRUS)";
     }
     if ($(this).val() == "el"){
       document.getElementById("select-voice").value = "(el-GR, Stefanos)";
     }
     if ($(this).val() == "he"){
       document.getElementById("select-voice").value = "(he-IL, Asaf)";
     }
     if ($(this).val() == "hi"){
       document.getElementById("select-voice").value = "(hi-IN, Kalpana, Apollo)";
     }
     if ($(this).val() == "it"){
       document.getElementById("select-voice").value = "(it-IT, LuciaRUS)";
     }
     if ($(this).val() == "ja"){
       document.getElementById("select-voice").value = "(ja-JP, HarukaRUS)";
     }
     if ($(this).val() == "ko"){
       document.getElementById("select-voice").value = "(ko-KR, HeamiRUS)";
     }
     if ($(this).val() == "pt"){
       document.getElementById("select-voice").value = "(pt-BR, HeloisaRUS)";
     }
     if ($(this).val() == "ru"){
       document.getElementById("select-voice").value = "(ru-RU, EkaterinaRUS)";
     }
     if ($(this).val() == "es"){
       document.getElementById("select-voice").value = "(es-ES, HelenaRUS)";
     }
     if ($(this).val() == "th"){
       document.getElementById("select-voice").value = "(th-TH, Pattara)";
     }
     if ($(this).val() == "tr"){
       document.getElementById("select-voice").value = "(tr-TR, SedaRUS)";
     }
     if ($(this).val() == "vi"){
       document.getElementById("select-voice").value = "(vi-VN, An)";
     }
   });
   ```

### <a name="test-your-app"></a>Testa din app

Nu ska vi testa talsyntes i appen.

```
flask run
```

Navigera till den angivna serveradressen. Ange text i indata området, Välj ett språk och tryck på översätta. Du bör få en översättning. Därefter väljer en röst och tryck på knappen Konvertera text till tal. översättningen ska spelas upp som syntetiskt tal. Om det inte fungerar kontrollerar du att du har lagt till din prenumerationsnyckel.

> [!TIP]
> Om du har gjort ändringarna visas inte, eller om appen fungerar inte som förväntat, försök rensa cacheminnet eller genom att öppna ett fönster för privat/inkognito.

Det var allt – du har en fungerande app som utför översättningar, analyserar känsla och syntetiskt tal. Tryck på **CTRL + c** att avsluta appen. Se till att Kolla in den andra [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/).

## <a name="get-the-source-code"></a>Hämta källkoden

Källkoden för det här projektet är tillgängligt på [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="next-steps"></a>Nästa steg

* [Referens för Translator Text API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
* [Referens för API för textanalys](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)
* [Referens för text till tal-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)
