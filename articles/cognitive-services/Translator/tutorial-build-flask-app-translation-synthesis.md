---
title: 'Självstudiekurs: Skapa en flaskapp för att översätta, syntetisera och analysera text - Translator Text API'
titleSuffix: Azure Cognitive Services
description: I den här självstudien ska du skapa en Flask-baserad webbapp för att översätta text, analysera sentiment och syntetisera översatt text till tal.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: tutorial
ms.date: 02/10/2020
ms.author: swmachan
ms.openlocfilehash: 5034dafa015054e9e9d0804088f345929815b974
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397942"
---
# <a name="tutorial-build-a-flask-app-with-azure-cognitive-services"></a>Självstudiekurs: Skapa en flaskapp med Azure Cognitive Services

I den här självstudien ska du skapa en Flask-webbapp som använder Azure Cognitive Services för att översätta text, analysera sentiment och syntetisera översatt text till tal. Vårt fokus ligger på Python-koden och Flask-rutterna som möjliggör vår applikation, men vi hjälper dig med HTML och Javascript som drar ihop appen. Om du stöter på några problem låt oss veta att använda feedback-knappen nedan.

Här är vad den här guiden täcker:

> [!div class="checklist"]
> * Skaffa Azure-prenumerationsnycklar
> * Konfigurera utvecklingsmiljö och installera beroenden
> * Skapa en Flask-app
> * Använda translatortext-API:et för att översätta text
> * Använd Textanalys för att analysera positiva/negativa sentiment för indatatext och översättningar
> * Använda Taltjänster för att konvertera översatt text till syntetiserat tal
> * Kör din Flask-app lokalt

> [!TIP]
> Om du vill hoppa framåt och se all kod på en gång, hela exemplet, tillsammans med bygginstruktioner finns på [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="what-is-flask"></a>Vad är Flask?

Kolv är en mikroram för att skapa webbapplikationer. Det innebär att Flask förser dig med verktyg, bibliotek och tekniker som gör att du kan skapa ett webbprogram. Denna webbapplikation kan vara några webbsidor, en blogg, en wiki eller gå så innehållsrik som en webbaserad kalender ansökan eller en kommersiell webbplats.

För er som vill djupdykning efter den här guiden här är några användbara länkar:

* [Dokumentation av kolv](http://flask.pocoo.org/)
* [Kolv för dummies - En nybörjarguide till kolv](https://codeburst.io/flask-for-dummies-a-beginners-guide-to-flask-part-uno-53aec6afc5b1)

## <a name="prerequisites"></a>Krav

Låt oss granska de program- och prenumerationsnycklar som du behöver för den här självstudien.

* [Python 3.5.2 eller senare](https://www.python.org/downloads/)
* [Git-verktyg](https://git-scm.com/downloads)
* En IDE- eller textredigerare, till exempel [Visual Studio-kod](https://code.visualstudio.com/) eller [Atom](https://atom.io/)  
* [Chrome](https://www.google.com/chrome/browser/) eller [Firefox](https://www.mozilla.org/firefox)
* En **prenumerationsnyckel** för översättaretext (Observera att du inte behöver välja en region.)
* En **textanalysnyckel** i regionen **Västra USA.**
* En **Taltjänster-prenumerationsnyckel** i regionen Västra **USA.**

## <a name="create-an-account-and-subscribe-to-resources"></a>Skapa ett konto och prenumerera på resurser

Som tidigare nämnts behöver du tre prenumerationsnycklar för den här självstudien. Det innebär att du måste skapa en resurs i ditt Azure-konto för:
* Translator Text
* Textanalys
* Speech Services

Använd [Skapa ett Cognitive Services-konto i Azure-portalen](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) för steg-för-steg-instruktioner för att skapa resurser.

> [!IMPORTANT]
> Skapa dina resurser i regionen västra USA för den här självstudien. Om du använder en annan region måste du justera bas-URL:en i var och en av dina Python-filer.

## <a name="set-up-your-dev-environment"></a>Konfigurera din utvecklingsmiljö

Innan du skapar din Flask-webbapp måste du skapa en arbetskatalog för projektet och installera några Python-paket.

### <a name="create-a-working-directory"></a>Skapa en arbetskatalog

1. Öppna kommandoraden (Windows) eller terminalen (macOS/Linux). Skapa sedan en arbetskatalog och underkataloger för projektet:  

   ```
   mkdir -p flask-cog-services/static/scripts && mkdir flask-cog-services/templates
   ```
2. Ändra till projektets arbetskatalog:  

   ```
   cd flask-cog-services
   ```

### <a name="create-and-activate-your-virtual-environment-with-virtualenv"></a>Skapa och aktivera din virtuella miljö med`virtualenv`

Låt oss skapa en virtuell miljö för `virtualenv`vår Flask app med . Med hjälp av en virtuell miljö säkerställer du att du har en ren miljö att arbeta från.

1. I arbetskatalogen kör du det här kommandot för att skapa en virtuell miljö: **macOS/Linux:**
   ```
   virtualenv venv --python=python3
   ```
   Vi har uttryckligen deklarerat att den virtuella miljön ska använda Python 3. Detta säkerställer att användare med flera Python-installationer använder rätt version.

   **Windows CMD / Windows Bash:**
   ```
   virtualenv venv
   ```
   För att hålla det enkelt, vi namnge din virtuella miljö venv.

2. Kommandona för att aktivera din virtuella miljö varierar beroende på din plattform/skal:   

   | Plattform | Gränssnitt | Kommando |
   |----------|-------|---------|
   | macOS/Linux | bash/ zsh | `source venv/bin/activate` |
   | Windows | bash | `source venv/Scripts/activate` |
   | | Kommandorad | `venv\Scripts\activate.bat` |
   | | PowerShell | `venv\Scripts\Activate.ps1` |

   När du har kört det här kommandot bör kommandoraden eller terminalsessionen föregås av `venv`.

3. Du kan inaktivera sessionen när som helst genom att skriva in `deactivate`den i kommandoraden eller terminalen: .

> [!NOTE]
> Python har omfattande dokumentation för att skapa och hantera virtuella miljöer, se [virtualenv](https://virtualenv.pypa.io/en/latest/).

### <a name="install-requests"></a>Installera begäranden

Begäranden är en populär modul som används för att skicka HTTP 1.1-begäranden. Du behöver inte lägga till frågesträngar manuellt i webbadresserna eller att koda POST-data manuellt.

1. Om du vill installera begäranden kör du:

   ```
   pip install requests
   ```

> [!NOTE]
> Om du vill veta mer om begäranden läser du [Begäranden: HTTP för människor](https://2.python-requests.org/en/master/).

### <a name="install-and-configure-flask"></a>Installera och konfigurera Flask

Nästa vi behöver för att installera Kolv. Flask hanterar routningen för vår webbapp och gör det möjligt för oss att ringa server-till-server-samtal som döljer våra prenumerationsnycklar från slutanvändaren.

1. Så här installerar du Kolv:
   ```
   pip install Flask
   ```
   Låt oss se till att Flask installerades. Kör:
   ```
   flask --version
   ```
   Versionen ska skrivas ut på terminalen. Allt annat betyder att något gick fel.

2. För att köra Flask-appen kan du antingen använda kolvkommandot eller Pythons -m-switch med Flask. Innan du kan göra det måste du tala om för `FLASK_APP` terminalen vilken app du ska arbeta med genom att exportera miljövariabeln:

   **macOS/Linux**:
   ```
   export FLASK_APP=app.py
   ```

   **Windows**:
   ```
   set FLASK_APP=app.py
   ```

## <a name="create-your-flask-app"></a>Skapa din Flask-app

I det här avsnittet ska du skapa en barebones Flask-app som returnerar en HTML-fil när användare träffar roten på din app. Spendera inte för mycket tid på att försöka plocka isär koden, kommer vi tillbaka för att uppdatera den här filen senare.

### <a name="what-is-a-flask-route"></a>Vad är en Flask rutt?

Låt oss ta en minut att prata om "[rutter](http://flask.pocoo.org/docs/1.0/api/#flask.Flask.route)". Routning används för att binda en URL till en viss funktion. Kolv använder ruttdekoratörer för att registrera funktioner till specifika webbadresser. Till exempel när en användare navigerar`/`till roten `index.html` ( ) i vår webbapp, återges.  

```python
@app.route('/')
def index():
    return render_template('index.html')
```

Låt oss ta en titt på ytterligare ett exempel för att hamra detta hem.

```python
@app.route('/about')
def about():
    return render_template('about.html')
```

Den här koden säkerställer att `http://your-web-app.com/about` när `about.html` en användare navigerar till att filen återges.

Även om dessa exempel illustrerar hur du renderar html-sidor för en användare, kan rutter också användas för att anropa API:er när en knapp trycks in, eller vidta valfritt antal åtgärder utan att behöva navigera bort från startsidan. Det visas i praktiken när du skapar vägar för översättning, sentiment och talsyntes.

### <a name="get-started"></a>Kom igång

1. Öppna projektet i IDE:et och `app.py` skapa sedan en fil som namnges i roten till arbetskatalogen. Kopiera sedan den `app.py` här koden till och spara:

   ```python
   from flask import Flask, render_template, url_for, jsonify, request

   app = Flask(__name__)
   app.config['JSON_AS_ASCII'] = False

   @app.route('/')
   def index():
       return render_template('index.html')
   ```

   Det här kodblocket `index.html` talar om för appen att visas när`/`en användare navigerar till webbappens rot ( ).

2. Låt oss sedan skapa frontend för vår webbapp. Skapa en `index.html` fil `templates` som heter i katalogen. Kopiera sedan den `templates/index.html`här koden till .

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

3. Låt oss testa Flask-appen. Från terminalen, kör:

   ```
   flask run
   ```

4. Öppna en webbläsare och navigera till webbadressen. Du bör se din ensidiga app. Tryck på **Ctrl + C** för att döda appen.

## <a name="translate-text"></a>Översätt text

Nu när du har en uppfattning om hur en enkel Flask app fungerar, låt oss:

* Skriv en del Python för att anropa Translator Text API och returnera ett svar
* Skapa en flaskrutt för att anropa din Python-kod
* Uppdatera HTML-koden med ett område för textinmatning och översättning, en språkväljare och knappen Översätt
* Skriv Javascript som tillåter användare att interagera med din Flask-app från HTML

### <a name="call-the-translator-text-api"></a>Anropa translator-text-API:et

Det första du behöver göra är att skriva en funktion för att ringa Översättaren Text API. Den här funktionen tar `text_input` `language_output`två argument: och . Den här funktionen anropas när en användare trycker på översättsknappen i appen. Textområdet i HTML-koden skickas `text_input`som , och språkvalsvärdet `language_output`i HTML-koden skickas som .

1. Låt oss börja med att `translate.py` skapa en fil som kallas i roten till din arbetskatalog.
2. Lägg sedan till `translate.py`den här koden i . Den här funktionen `text_input` tar `language_output`två argument: och .
   ```python
   import os, requests, uuid, json

   # Don't forget to replace with your Cog Services subscription key!
   # If you prefer to use environment variables, see Extra Credit for more info.
   subscription_key = 'YOUR_TRANSLATOR_TEXT_SUBSCRIPTION_KEY'
   
   # Don't forget to replace with your Cog Services location!
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
           'Ocp-Apim-Subscription-Region': 'location',
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
3. Lägg till prenumerationsnyckeln translator text och spara.

### <a name="add-a-route-to-apppy"></a>Lägga till en rutt till`app.py`

Därefter måste du skapa en rutt i din Flask-app som anropar. `translate.py` Den här vägen anropas varje gång en användare trycker på översättsknappen i appen.

För den här appen kommer `POST` din rutt att acceptera förfrågningar. Detta beror på att funktionen förväntar sig att texten ska översättas och ett utdataspråk för översättningen.

Flask ger hjälpfunktioner som hjälper dig att tolka och hantera varje begäran. I den angivna `get_json()` koden returnerar `POST` du data från begäran som JSON. Sedan `data['text']` använda `data['to']`och , text och utdata språkvärden skickas till `get_translation()` funktionen tillgänglig från `translate.py`. Det sista steget är att returnera svaret som JSON, eftersom du måste visa dessa data i webbappen.

I följande avsnitt upprepar du den här processen när du skapar vägar för sentimentanalys och talsyntes.

1. Öppna `app.py` och leta upp importsatsen `app.py` högst upp på och lägg till följande rad:

   ```python
   import translate
   ```
   Nu kan vår Flask-app `translate.py`använda den metod som finns tillgänglig via .

2. Kopiera den här koden `app.py` till slutet av och spara:

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

Nu när du har en funktion för att översätta text och en rutt i din Flask-app för att anropa den, är nästa steg att börja bygga HTML för din app. HTML nedan gör några saker:

* Tillhandahåller ett textområde där användarna kan mata in text att översätta.
* Innehåller en språkväljare.
* Innehåller HTML-element för att återge det identifierade språket och konfidenspoäng som returneras under översättningen.
* Tillhandahåller ett skrivskyddat textområde där översättningsutdata visas.
* Innehåller platshållare för sentimentanalys och talsynteskod som du lägger till i den här filen senare i självstudien.

Låt oss `index.html`uppdatera .

1. Öppna `index.html` och hitta dessa kodkommentarer:
   ```html
   <!-- HTML provided in the following sections goes here. -->

   <!-- End -->
   ```

2. Ersätt kodkommentarerna med det här HTML-blocket:
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

Nästa steg är att skriva några Javascript. Detta är bryggan mellan din HTML och Flask rutt.

### <a name="create-mainjs"></a>Skapa`main.js`  

Filen `main.js` är bryggan mellan html- och flaskrutten. Din app använder en kombination av jQuery, Ajax och XMLHttpRequest för att återge innehåll och göra `POST` förfrågningar till dina Flask-rutter.

I koden nedan används innehåll från HTML för att skapa en begäran till din Flask-rutt. Innehållet i textområdet och språkväljaren tilldelas variabler och skickas sedan vidare i begäran `translate-text`till .

Koden itererar sedan igenom svaret och uppdaterar HTML-koden med översättningen, det identifierade språket och förtroendepoängen.

1. Skapa en fil som heter `main.js` i `static/scripts` katalogen från IDE.From your IDE, create a file named in the directory.
2. Kopiera den `static/scripts/main.js`här koden till:
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

Låt oss testa översättningen i appen.

```
flask run
```

Navigera till den angivna serveradressen. Skriv text i inmatningsområdet, välj ett språk och tryck på översätt. Du borde skaffa en översättning. Om det inte fungerar kontrollerar du att du har lagt till din prenumerationsnyckel.

> [!TIP]
> Om ändringarna du har gjort inte visas eller om appen inte fungerar som du förväntar dig kan du prova att rensa cacheminnet eller öppna ett privat/inkognitofönster.

Tryck på **CTRL + c** för att döda appen och gå sedan till nästa avsnitt.

## <a name="analyze-sentiment"></a>Analysera sentiment

[Api:et](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) för textanalys kan användas för att utföra sentimentanalys, extrahera nyckelfraser från text eller identifiera källspråket. I den här appen kommer vi att använda sentimentanalys för att avgöra om den angivna texten är positiv, neutral eller negativ. API:n returnerar en numerisk poäng mellan 0 och 1. Poäng nära 1 indikerar positivt sentiment och poäng nära 0 indikerar negativt sentiment.

I det här avsnittet ska du göra några saker:

* Skriv en del Python för att anropa API:et för textanalys för att utföra sentimentanalys och returnera ett svar
* Skapa en flaskrutt för att anropa din Python-kod
* Uppdatera HTML-koden med ett område för sentimentpoäng och en knapp för att utföra analyser
* Skriv Javascript som tillåter användare att interagera med din Flask-app från HTML

### <a name="call-the-text-analytics-api"></a>Anropa API:t för textanalys

Låt oss skriva en funktion för att anropa API:et för textanalys. Den här funktionen tar `input_text` `input_language`fyra `output_text`argument: , , och `output_language`. Den här funktionen anropas när en användare trycker på knappen för analys av körnings sentiment i appen. Data från textområdet och språkväljaren samt det identifierade språket och översättningsutdata tillhandahålls med varje begäran. Svarsobjektet innehåller sentimentpoäng för källan och översättningen. I följande avsnitt ska du skriva javascript för att tolka svaret och använda det i appen. För nu, låt oss fokusera på att ringa Text Analytics API.

1. Nu ska vi skapa `sentiment.py` en fil som anropas i roten till arbetskatalogen.
2. Lägg sedan till `sentiment.py`den här koden i .
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
3. Lägg till din prenumerationsnyckel för Text Analytics och spara.

### <a name="add-a-route-to-apppy"></a>Lägga till en rutt till`app.py`

Låt oss skapa en rutt i din `sentiment.py`Flask-app som anropar . Den här vägen anropas varje gång en användare trycker på knappen för analys av körnings sentiment i appen. Precis som vägen för översättning kommer `POST` den här vägen att acceptera begäranden eftersom funktionen förväntar sig argument.

1. Öppna `app.py` och leta upp importsatsen `app.py` högst upp på och uppdatera den:

   ```python
   import translate, sentiment
   ```
   Nu kan vår Flask-app `sentiment.py`använda den metod som finns tillgänglig via .

2. Kopiera den här koden `app.py` till slutet av och spara:
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

Nu när du har en funktion för att köra sentimentanalys och en väg i flaskappen för att anropa den, är nästa steg att börja skriva HTML-koden för din app. HTML nedan gör några saker:

* Lägger till en knapp i appen för att köra sentimentanalys
* Lägger till ett element som förklarar sentimentbedömning
* Lägger till ett element för att visa sentimentpoängen

1. Öppna `index.html` och hitta dessa kodkommentarer:
   ```html
   <!-- Start sentiment code-->

   <!-- End sentiment code -->
   ```

2. Ersätt kodkommentarerna med det här HTML-blocket:
   ```html
   <button type="submit" class="btn btn-primary mb-2" id="sentiment-analysis">Run sentiment analysis</button></br>
   <div id="sentiment" style="display: none">
      <p>Sentiment scores are provided on a 1 point scale. The closer the sentiment score is to 1, indicates positive sentiment. The closer it is to 0, indicates negative sentiment.</p>
      <strong>Sentiment score for input:</strong> <span id="input-sentiment"></span><br />
      <strong>Sentiment score for translation:</strong> <span id="translation-sentiment"></span>
   </div>
   ```

### <a name="update-mainjs"></a>Uppdatera `main.js`

I koden nedan används innehåll från HTML för att skapa en begäran till din Flask-rutt. Närmare bestämt tilldelas innehållet i textområdet och språkväljaren variabler och skickas sedan vidare `sentiment-analysis` i begäran till rutten.

Koden itererar sedan igenom svaret och uppdaterar HTML med sentimentpoängen.

1. Skapa en fil som heter `main.js` i `static` katalogen från IDE.From your IDE, create a file named in the directory.

2. Kopiera den `static/scripts/main.js`här koden till:
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

### <a name="test-sentiment-analysis"></a>Analys av testsentiment

Låt oss testa sentimentanalys i appen.

```
flask run
```

Navigera till den angivna serveradressen. Skriv text i inmatningsområdet, välj ett språk och tryck på översätt. Du borde skaffa en översättning. Tryck sedan på knappen analys av körnings sentiment. Du skulle se två poäng. Om det inte fungerar kontrollerar du att du har lagt till din prenumerationsnyckel.

> [!TIP]
> Om ändringarna du har gjort inte visas eller om appen inte fungerar som du förväntar dig kan du prova att rensa cacheminnet eller öppna ett privat/inkognitofönster.

Tryck på **CTRL + c** för att döda appen och gå sedan till nästa avsnitt.

## <a name="convert-text-to-speech"></a>Konvertera text-till-tal

[Med TEXT-till-tal-API:et](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech) kan appen konvertera text till naturligt människoliknande syntetiserat tal. Tjänsten stöder standard, neurala och anpassade röster. Vår exempelapp använder en handfull av de tillgängliga rösterna, för en fullständig lista, se [språk som stöds.](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)

I det här avsnittet ska du göra några saker:

* Skriva en del Python för att konvertera text-till-tal med API:et för text-till-tal
* Skapa en flaskrutt för att anropa din Python-kod
* Uppdatera HTML-koden med en knapp för att konvertera text till tal och ett element för ljuduppspelning
* Skriv Javascript som tillåter användare att interagera med din Flask-app

### <a name="call-the-text-to-speech-api"></a>Anropa API:et för text-till-tal

Låt oss skriva en funktion för att konvertera text-till-tal. Den här funktionen tar `input_text` `voice_font`två argument: och . Den här funktionen anropas när en användare trycker på knappen konvertera text till tal i appen. `input_text`är översättningsutdata som returneras av `voice_font` anropet för att översätta text, är värdet från röstteckensnittsväljaren i HTML-koden.

1. Nu ska vi skapa `synthesize.py` en fil som anropas i roten till arbetskatalogen.

2. Lägg sedan till `synthesize.py`den här koden i .
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
3. Lägg till prenumerationsnyckeln för Taltjänster och spara.

### <a name="add-a-route-to-apppy"></a>Lägga till en rutt till`app.py`

Låt oss skapa en rutt i din `synthesize.py`Flask-app som anropar . Den här vägen anropas varje gång en användare trycker på knappen konvertera text till tal i appen. Liksom rutterna för översättnings- och sentimentanalys `POST` kommer den här vägen att acceptera begäranden eftersom funktionen förväntar sig två argument: texten ska syntetiseras och röstteckensnittet för uppspelning.

1. Öppna `app.py` och leta upp importsatsen `app.py` högst upp på och uppdatera den:

   ```python
   import translate, sentiment, synthesize
   ```
   Nu kan vår Flask-app `synthesize.py`använda den metod som finns tillgänglig via .

2. Kopiera den här koden `app.py` till slutet av och spara:

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

Nu när du har en funktion för att konvertera text-till-tal, och en väg i din Flask-app för att kalla det, är nästa steg att börja skriva HTML för din app. HTML nedan gör några saker:

* Ger en listruta för röstmarkering
* Lägger till en knapp för att konvertera text till tal
* Lägger till ett ljudelement som används för att spela upp det syntetiserade talet

1. Öppna `index.html` och hitta dessa kodkommentarer:
   ```html
   <!-- Start voice font selection code -->

   <!-- End voice font selection code -->
   ```

2. Ersätt kodkommentarerna med det här HTML-blocket:
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
       <option value="(en-US, AriaRUS)">English (US) | Female | AriaRUS</option>
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

3. Leta sedan upp dessa kodkommentarer:
   ```html
   <!-- Add Speech Synthesis button and audio element -->

   <!-- End Speech Synthesis button -->
   ```

4. Ersätt kodkommentarerna med det här HTML-blocket:

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

I koden nedan används innehåll från HTML för att skapa en begäran till din Flask-rutt. Översättningen och röstteckensnittet tilldelas variabler och skickas sedan vidare i `text-to-speech` begäran till rutten.

Koden itererar sedan igenom svaret och uppdaterar HTML med sentimentpoängen.

1. Skapa en fil som heter `main.js` i `static` katalogen från IDE.From your IDE, create a file named in the directory.
2. Kopiera den `static/scripts/main.js`här koden till:
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
3. Nästan klart. Det sista du ska göra är att `main.js` lägga till lite kod för att automatiskt välja ett röstteckensnitt baserat på det språk som valts för översättning. Lägg till det `main.js`här kodblocket i:
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

Låt oss testa talsyntes i appen.

```
flask run
```

Navigera till den angivna serveradressen. Skriv text i inmatningsområdet, välj ett språk och tryck på översätt. Du borde skaffa en översättning. Markera sedan en röst och tryck sedan på knappen Konvertera text till tal. översättningen ska spelas upp som syntetiserat tal. Om det inte fungerar kontrollerar du att du har lagt till din prenumerationsnyckel.

> [!TIP]
> Om ändringarna du har gjort inte visas eller om appen inte fungerar som du förväntar dig kan du prova att rensa cacheminnet eller öppna ett privat/inkognitofönster.

Det är det, du har en fungerande app som utför översättningar, analyserar känslor och syntetiserade tal. Tryck på **CTRL + c** för att döda appen. Var noga med att kolla in de andra [Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/).

## <a name="get-the-source-code"></a>Hämta källkoden

Källkoden för det här projektet är tillgänglig på [GitHub](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-Flask-App-Tutorial).

## <a name="next-steps"></a>Nästa steg

* [Referens för Translator Text API](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)
* [API för textanalys – referens](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)
* [Referens för text till tal-API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech)
