---
title: Utveckling av webbappar i Python Flask med DocumentDB | Microsoft Docs
description: "Det här är en självstudie om databaser och användning av DocumentDB för lagring av och åtkomst till data från en Python Flask-webbapp på Azure. Hitta apputvecklingslösningar."
keywords: "Programutveckling, självstudier för databas, python flask, python webbprogram, python webbutveckling, documentdb, azure, Microsoft azure"
services: documentdb
documentationcenter: python
author: syamkmsft
manager: jhubbard
editor: cgronlun
ms.assetid: 20ebec18-67c2-4988-a760-be7c30cfb745
ms.service: documentdb
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: hero-article
ms.date: 11/16/2016
ms.author: syamk
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 8dc246755c90686f890d2ac9df05b3a8b2a9341f


---
# <a name="python-flask-web-application-development-with-documentdb"></a>Utveckling av webbappar i Python Flask med DocumentDB
> [!div class="op_single_selector"]
> * [.NET](documentdb-dotnet-application.md)
> * [Node.js](documentdb-nodejs-application.md)
> * [Java](documentdb-java-application.md)
> * [Python](documentdb-python-application.md)
> 
> 

I den här självstudien visar vi hur du använder Azure DocumentDB för att lagra och komma åt data från en Python-webbapp på Azure. Vi förutsätter att du har tidigare erfarenhet av Python och Azure Websites.

I den här självstudien om databaser tar vi upp följande:

1. Skapa och etablera ett DocumentDB-konto.
2. Skapa en Python MVC-app.
3. Ansluta till och använda Azure DocumentDB från din webbapp.
4. Distribuera webbappen till Azure Websites.

Genom att följa den här självstudien skapar du en enkel röstningsapp där du kan delta i en omröstning.

![Skärmdump av den webbapp med att göra-lista som skapats i denna självstudie om databaser](./media/documentdb-python-application/image1.png)

## <a name="database-tutorial-prerequisites"></a>Förutsättningar för självstudien om databaser
Innan du följer anvisningarna i den här artikeln bör du se till att du har följande installerat:

* Ett aktivt Azure-konto. Om du inte har något konto kan du skapa ett kostnadsfritt utvärderingskonto på bara några minuter. Mer information om den [kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/).
 
    ELLER 

    En lokal installation av [Azure DocumentDB-emulatorn](documentdb-nosql-local-emulator.md).
* [Visual Studio 2013](http://www.visualstudio.com/) eller senare, eller [Visual Studio Express](), vilket är den kostnadsfria versionen. Anvisningarna i den här självstudien är skrivna specifikt för Visual Studio 2015. 
* Python Tools för Visual Studio från [GitHub](http://microsoft.github.io/PTVS/). I den här självstudien används Python Tools för VS 2015. 
* Azure Python SDK för Visual Studio, version 2.4 eller senare från [azure.com](https://azure.microsoft.com/downloads/). Vi använde Microsoft Azure SDK för Python 2.7.
* Python 2.7 från [python.org][2]. Vi använde Python 2.7.11. 

> [!IMPORTANT]
> Om du installerar Python 2.7 för första gången ska du vara noga med att välja **Lägg till python.exe i sökväg**på skärmen Anpassa Python 2.7.11.
> 
> ![Skärmdump av skärmen Customize Python 2.7.11 där du ska välja Lägg till python.exe i sökväg](./media/documentdb-python-application/image2.png)
> 
> 

* Microsoft Visual C++ Compiler for Python 2.7 från [Microsoft Download Center][3].

## <a name="step-1-create-a-documentdb-database-account"></a>Steg 1: Skapa ett DocumentDB-databaskonto
Börja med att skapa ett DocumentDB-konto. Om du redan har ett konto eller om du använder DocumentDB-emulatorn för den här kursen kan du gå vidare till [Steg 2: Skapa en ny webbapp i Python Flask](#step-2:-create-a-new-python-flask-web-application).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

<br/>
Nu kommer vi att gå igenom hur du skapar en ny Python Flask-webbapp från grunden.

## <a name="step-2-create-a-new-python-flask-web-application"></a>Steg 2: Skapa en ny webbapp i Python Flask
1. I Visual Studio klickar du på menyn **Arkiv**, pekar på **Nytt** och klickar sedan på **Projekt**.
   
    Dialogrutan **Nytt projekt** visas.
2. I den vänstra panelen expanderar du **Mallar** och **Python**. Klicka sedan på **Webb**. 
3. Välj **Flask-webbprojekt** i den mittersta panelen. I rutan **Namn** skriver du sedan **tutorial** och klickar på **OK**. Kom ihåg att paketnamn i Python ska skrivas med gemener, enligt beskrivningen i [Stilguide för Python-kod](https://www.python.org/dev/peps/pep-0008/#package-and-module-names).
   
    Har du inte använt Python Flask tidigare? Det är ett utvecklingsramverk för webbappar som hjälper dig att skapa webbappar i Python snabbare.
   
    ![Skärmdump av fönstret Nytt projekt i Visual Studio med Python markerat till vänster, Python Flask-webbprojekt valt i mitten och namnet tutorial i rutan Namn.](./media/documentdb-python-application/image9.png)
4. I fönstret **Python-verktyg för Visual Studio** klickar du på **Installera i virtuell miljö**. 
   
    ![Skärmdump av databasen tutorial – fönstret Python-verktyg för Visual Studio](./media/documentdb-python-application/image10.png)
5. I fönstret **Lägg till virtuell miljö** kan du lämna standardinställningarna och använda Python 2.7 som basmiljö eftersom PyDocumentDB för närvarande inte stöder Python 3.x. Klicka sedan på **Skapa**. Då konfigureras den nödvändiga virtuella Python-miljön för projektet.
   
    ![Skärmdump av databasen tutorial – fönstret Python-verktyg för Visual Studio](./media/documentdb-python-application/image10_A.png)
   
    Utdatafönstret visar `Successfully installed Flask-0.10.1 Jinja2-2.8 MarkupSafe-0.23 Werkzeug-0.11.5 itsdangerous-0.24 'requirements.txt' was installed successfully.` när miljön har installerats.

## <a name="step-3-modify-the-python-flask-web-application"></a>Steg 3: Ändra Python Flask-webbappen
### <a name="add-the-python-flask-packages-to-your-project"></a>Lägga till Python Flask-paket i ditt projekt
När projektet har konfigurerats måste du lägga till nödvändiga Flask-paket till ditt projekt, inklusive pydocumentdb, Python-paketet för DocumentDB.

1. Öppna filen **requirements.txt** i Solution Explorer och ersätt innehållet med följande:
   
        flask==0.9
        flask-mail==0.7.6
        sqlalchemy==0.7.9
        flask-sqlalchemy==0.16
        sqlalchemy-migrate==0.7.2
        flask-whooshalchemy==0.55a
        flask-wtf==0.8.4
        pytz==2013b
        flask-babel==0.8
        flup
        pydocumentdb>=1.0.0
2. Spara filen **requirements.txt**. 
3. Högerklicka på **env** i Solution Explorer och klicka på **Install from requirements.txt**.
   
    ![Skärmdump som visar env (Python 2.7) vald med Install från requirements.txt markerat i listan](./media/documentdb-python-application/image11.png)
   
    Efter installationen visar utdatafönstret följande:
   
        Successfully installed Babel-2.3.2 Tempita-0.5.2 WTForms-2.1 Whoosh-2.7.4 blinker-1.4 decorator-4.0.9 flask-0.9 flask-babel-0.8 flask-mail-0.7.6 flask-sqlalchemy-0.16 flask-whooshalchemy-0.55a0 flask-wtf-0.8.4 flup-1.0.2 pydocumentdb-1.6.1 pytz-2013b0 speaklater-1.3 sqlalchemy-0.7.9 sqlalchemy-migrate-0.7.2
   
   > [!NOTE]
   > I sällsynta fall kan det visas ett fel i utdatafönstret. Om det händer kan du kontrollera om felet är relaterat till rensning. Rensningen misslyckas ibland, men installationen slutförs ändå (rulla uppåt i utdatafönstret för att kontrollera). Du kan kontrollera installationen genom att [verifiera den virtuella miljön](#verify-the-virtual-environment). Om installationen misslyckades men verifieringen lyckas kan du fortsätta.
   > 
   > 

### <a name="verify-the-virtual-environment"></a>Verifiera den virtuella miljön
Nu ska vi kontrollera att allt är korrekt installerat.

1. Bygg lösningen genom att trycka på **Ctrl**+**Skift**+**B**.
2. När den har byggts startar du webbplatsen genom att trycka på **F5**. Då startas Flask-utvecklingsservern och din webbläsare. Du bör se nedanstående sida.
   
    ![Det tomma Python Flask-webbutvecklingsprojektet visas i en webbläsare](./media/documentdb-python-application/image12.png)
3. Stoppa felsökningen av webbplatsen genom att trycka på **SKIFT**+**F5** i Visual Studio.

### <a name="create-database-collection-and-document-definitions"></a>Skapa databas, samling och dokumentdefinitioner
Nu skapar vi röstningsappen genom att lägga till nya filer och uppdatera andra.

1. Högerklicka på projektet **tutorial** i Solution Explorer. Klicka på **Lägg till** och sedan på **Nytt objekt**. Välj **Tom Python-fil** och namnge filen **forms.py**.  
2. Lägg till nedanstående kod i filen forms.fy och spara filen.

```python
from flask.ext.wtf import Form
from wtforms import RadioField

class VoteForm(Form):
    deploy_preference  = RadioField('Deployment Preference', choices=[
        ('Web Site', 'Web Site'),
        ('Cloud Service', 'Cloud Service'),
        ('Virtual Machine', 'Virtual Machine')], default='Web Site')
```


### <a name="add-the-required-imports-to-viewspy"></a>Lägg till nödvändiga importer i views.py
1. Expandera mappen **tutorial** i Solution Explorer och öppna filen **views.py**. 
2. Lägg till nedanstående importuttryck överst i filen **views.py** och spara sedan filen. De importerar DocumentDB:s PythonSDK och Flask-paketen.
   
    ```python
    from forms import VoteForm
    import config
    import pydocumentdb.document_client as document_client
    ```

### <a name="create-database-collection-and-document"></a>Skapa databas, samling och dokument
* Lägg till nedanstående kod i slutet av filen, fortfarande i **views.py**. Med den skapas databasen som formuläret använder. Ta inte bort den befintliga koden i **views.py**. Lägg bara till det här i slutet.

```python
@app.route('/create')
def create():
    """Renders the contact page."""
    client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})

    # Attempt to delete the database.  This allows this to be used to recreate as well as create
    try:
        db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))
        client.DeleteDatabase(db['_self'])
    except:
        pass

    # Create database
    db = client.CreateDatabase({ 'id': config.DOCUMENTDB_DATABASE })

    # Create collection
    collection = client.CreateCollection(db['_self'],{ 'id': config.DOCUMENTDB_COLLECTION })

    # Create document
    document = client.CreateDocument(collection['_self'],
        { 'id': config.DOCUMENTDB_DOCUMENT,
          'Web Site': 0,
          'Cloud Service': 0,
          'Virtual Machine': 0,
          'name': config.DOCUMENTDB_DOCUMENT 
        })

    return render_template(
       'create.html',
        title='Create Page',
        year=datetime.now().year,
        message='You just created a new database, collection, and document.  Your old votes have been deleted')
```

> [!TIP]
> Metoden **CreateCollection** tar en valfri **RequestOptions** som tredje parameter. Det kan användas för att ange erbjudandetyp för samlingen. Om inget värde anges för offerType skapas samlingen med standardtypen för erbjudande. Mer information om erbjudandetyper i DocumentDB finns i [Prestandanivåer för DocumentDB](documentdb-performance-levels.md)
> 
> 

### <a name="read-database-collection-document-and-submit-form"></a>Läsa databas, samling och dokument samt skicka formulär
* Lägg till nedanstående kod i slutet av filen, fortfarande i **views.py**. Med den kan formuläret konfigureras samt databas, samling och dokument läsas. Ta inte bort den befintliga koden i **views.py**. Lägg bara till det här i slutet.

```python
@app.route('/vote', methods=['GET', 'POST'])
def vote(): 
    form = VoteForm()
    replaced_document ={}
    if form.validate_on_submit(): # is user submitted vote  
        client = document_client.DocumentClient(config.DOCUMENTDB_HOST, {'masterKey': config.DOCUMENTDB_KEY})

        # Read databases and take first since id should not be duplicated.
        db = next((data for data in client.ReadDatabases() if data['id'] == config.DOCUMENTDB_DATABASE))

        # Read collections and take first since id should not be duplicated.
        coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config.DOCUMENTDB_COLLECTION))

        # Read documents and take first since id should not be duplicated.
        doc = next((doc for doc in client.ReadDocuments(coll['_self']) if doc['id'] == config.DOCUMENTDB_DOCUMENT))

        # Take the data from the deploy_preference and increment our database
        doc[form.deploy_preference.data] = doc[form.deploy_preference.data] + 1
        replaced_document = client.ReplaceDocument(doc['_self'], doc)

        # Create a model to pass to results.html
        class VoteObject:
            choices = dict()
            total_votes = 0

        vote_object = VoteObject()
        vote_object.choices = {
            "Web Site" : doc['Web Site'],
            "Cloud Service" : doc['Cloud Service'],
            "Virtual Machine" : doc['Virtual Machine']
        }
        vote_object.total_votes = sum(vote_object.choices.values())

        return render_template(
            'results.html', 
            year=datetime.now().year, 
            vote_object = vote_object)

    else :
        return render_template(
            'vote.html', 
            title = 'Vote',
            year=datetime.now().year,
            form = form)
```


### <a name="create-the-html-files"></a>Skapa HTML-filerna
1. I Solution Explorer, i mappen **tutorial**, högerklickar du på mappen **templates**, klickar på **Lägg till** och klickar sedan på **Nytt objekt**. 
2. Välj **HTML-sida** och skriv **create.html** i namnrutan. 
3. Upprepa steg 1 och 2 för att skapa två ytterligare HTML-filer: results.html och vote.html.
4. Lägg till nedanstående kod i **create.html** i elementet `<body>`. Ett meddelande visas om att vi har skapat en ny databas, en samling och ett dokument.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>{{ title }}.</h2>
    <h3>{{ message }}</h3>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}
    ```
5. Lägg till nedanstående kod i **results.html** i elementet `<body`>. Den visar resultatet av omröstningen.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>Results of the vote</h2>
        <br />
   
    {% for choice in vote_object.choices %}
    <div class="row">
        <div class="col-sm-5">{{choice}}</div>
            <div class="col-sm-5">
                <div class="progress">
                    <div class="progress-bar" role="progressbar" aria-valuenow="{{vote_object.choices[choice]}}" aria-valuemin="0" aria-valuemax="{{vote_object.total_votes}}" style="width: {{(vote_object.choices[choice]/vote_object.total_votes)*100}}%;">
                                {{vote_object.choices[choice]}}
                </div>
            </div>
            </div>
    </div>
    {% endfor %}
   
    <br />
    <a class="btn btn-primary" href="{{ url_for('vote') }}">Vote again?</a>
    {% endblock %}
    ```
6. Lägg till nedanstående kod i **vote.html** i elementet `<body`>. Den visar omröstningen och godkänner rösterna. När rösterna registreras skickas kontrollen till views.py där vi bekräftar den lagda rösten och lägger till den i dokumentet.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>What is your favorite way to host an application on Azure?</h2>
    <form action="" method="post" name="vote">
        {{form.hidden_tag()}}
            {{form.deploy_preference}}
            <button class="btn btn-primary" type="submit">Vote</button>
    </form>
    {% endblock %}
    ```
7. I mappen **templates** ersätter du innehållet i **index.html** med nedanstående. Det fungerar som landningssida för din app.
   
    ```html
    {% extends "layout.html" %}
    {% block content %}
    <h2>Python + DocumentDB Voting Application.</h2>
    <h3>This is a sample DocumentDB voting application using PyDocumentDB</h3>
    <p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}
    ```

### <a name="add-a-configuration-file-and-change-the-initpy"></a>Lägg till en konfigurationsfil och ändra \_\_init\_\_.py
1. Högerklicka på projektet **tutorial** i Solution Explorer. Klicka på **Lägg till** och **Nytt objekt**, välj **Tom Python-fil** och namnge filen **config.py**. Den här konfigurationsfilen krävs av formulär i Flask. Du kan även använda den för att tillhandahålla en hemlig nyckel. Nyckeln behövs inte i den här självstudien.
2. Lägg till nedanstående kod i config.py. I nästa steg behöver du ändra värden för **DOCUMENTDB\_HOST** och **DOCUMENTDB\_KEY**.
   
    ```python
    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
   
    DOCUMENTDB_HOST = 'https://YOUR_DOCUMENTDB_NAME.documents.azure.com:443/'
    DOCUMENTDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='
   
    DOCUMENTDB_DATABASE = 'voting database'
    DOCUMENTDB_COLLECTION = 'voting collection'
    DOCUMENTDB_DOCUMENT = 'voting document'
    ```
3. I [Azure Portal](https://portal.azure.com/) navigerar du till bladet **Nycklar** genom att klicka på **Bläddra** och **DocumentDB-konton**. Dubbelklicka på namnet på det konto som ska användas och klicka sedan på knappen **Nycklar** i området **Essentials**. I bladet **Nycklar** kopierar du **URI**-värdet och klistrar in det i filen **config.py** som värdet för egenskapen **DOCUMENTDB\_HOST**. 
4. Tillbaka i Azure Portal, i bladet **Nycklar**, kopierar du värdet för **primärnyckel** eller **sekundärnyckel** och klistrar in det i filen **config.py** som värdet för egenskapen **DOCUMENTDB\_KEY**.
5. I **\_\_init\_\_.py**-filen, lägger du till följande rad. 
   
        app.config.from_object('config')
   
    så att innehållet i datafilen är:
   
    ```python
    from flask import Flask
    app = Flask(__name__)
    app.config.from_object('config')
    import tutorial.views
    ```
6. När du har lagt till alla filer bör Solution Explorer se ut så här:
   
    ![Skärmdump av Visual Studio Solution Explorer-fönstret](./media/documentdb-python-application/image15.png)

## <a name="step-4-run-your-web-application-locally"></a>Steg 4: Kör webbappen lokalt
1. Bygg lösningen genom att trycka på **Ctrl**+**Skift**+**B**.
2. När den har byggts startar du webbplatsen genom att trycka på **F5**. Du bör se nedanstående på skärmen.
   
    ![Skärmdump av Python- och DocumentDB-röstningsappen visad i en webbläsare](./media/documentdb-python-application/image16.png)
3. Skapa databasen genom att klicka på **Skapa/rensa röstningsdatabasen**.
   
    ![Skärmdump av sidan Skapa i webbappen – utvecklingsdetaljer](./media/documentdb-python-application/image17.png)
4. Klicka sedan på **Rösta** och välj ett alternativ.
   
    ![Skärmdump av webbappen med en röstningsfråga](./media/documentdb-python-application/image18.png)
5. För varje röst du lägger ökas den aktuella räknaren.
   
    ![Skärmdump som visar sidan Röstningsresultat](./media/documentdb-python-application/image19.png)
6. Stoppa felsökningen av projektet genom att trycka på SKIFT + F5.

## <a name="step-5-deploy-the-web-application-to-azure-websites"></a>Steg 5: Distribuera webbappen till Azure Websites
När hela appen fungerar som den ska mot DocumentDB är det dags att distribuera den till Azure Websites.

1. Högerklicka på projektet i Solution Explorer (kontrollera att det inte fortfarande körs lokalt) och välj **Publicera**.  
   
     ![Skärmdump som visar tutorial markerad i Solution Explorer med alternativet Publicera markerat](./media/documentdb-python-application/image20.png)
2. I fönstret **Publicera till webben** väljer du **Microsoft Azure-webbappar** och klickar sedan på **Nästa**.
   
    ![Skärmdump som visar fönstret Publicera till webben med Microsoft Azure-webbappar markerat](./media/documentdb-python-application/image21.png)
3. I fönstret **Microsoft Azure-webbappar** klickar du på **Ny**.
   
    ![Skärmdump av fönstret Microsoft Azure-webbappar](./media/documentdb-python-application/select-existing-website.png)
4. I fönstret **Skapa webbplats på Microsoft Azure** fyller du i **Webbappens namn**, **Apptjänstplan**, **Resursgrupp** och **Region** och klickar sedan på **Skapa**.
   
    ![Skärmdump som visar fönstret Skapa webbplats på Microsoft Azure](./media/documentdb-python-application/create-site-on-microsoft-azure.png)
5. I fönstret **Publicera till webben** klickar du på **Publicera**.
   
    ![Skärmdump som visar fönstret Skapa webbplats på Microsoft Azure](./media/documentdb-python-application/publish-web.png)
6. Efter några sekunder har Visual Studio publicerat din webbapp och öppnar en webbläsare där du kan se ditt arbete köras i Azure!

## <a name="troubleshooting"></a>Felsökning
Om det här är den första Python-appen du kör på datorn kontrollerar du att följande mappar (eller motsvarande installationsplatser) ingår i PATH-variabeln:

    C:\Python27\site-packages;C:\Python27\;C:\Python27\Scripts;

Om du får ett felmeddelande på din röstningssida och du har gett ditt projekt ett annat namn än **tutorial**, kontrollerar du att **\_\_init\_\_.py** refererar rätt projektnamn i raden: `import tutorial.view`.

## <a name="next-steps"></a>Nästa steg
Grattis! Du har precis skapat din första Python-webbapp med Azure DocumentDB och publicerat den på Azure Websites.

Vi uppdaterar och förbättrar ofta den här artikeln baserat på dina synpunkter.  När du har slutfört självstudien blir vi glada om du använder röstningsknapparna högst upp och längst ned på den här sidan. Ge även feedback på hur vi kan göra den bättre. Om du vill att vi ska kontakta dig direkt kan du skriva din e-postadress i kommentaren.

Om du vill lägga till ytterligare funktioner i webbappen kan du ta en titt på tillgängliga API:er i [DocumentDB Python SDK](documentdb-sdk-python.md).

Mer information om Azure, Visual Studio och Python finns i [Python Developer Center](https://azure.microsoft.com/develop/python/). 

Ytterligare självstudier om Python Flask finns i [Ingående självstudie om Flask, del I: Hello, World!](http://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world). 

[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[2]: https://www.python.org/downloads/windows/
[3]: https://www.microsoft.com/download/details.aspx?id=44266
[Microsoft Web Platform Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[Azure portal]: http://portal.azure.com



<!--HONumber=Dec16_HO1-->


