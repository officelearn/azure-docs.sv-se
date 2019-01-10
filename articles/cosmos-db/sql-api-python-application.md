---
title: Utveckling av webbprogram i Python Flask med Azure Cosmos DB
description: Den här självstudiekursen om databaser beskriver hur du använder Azure Cosmos DB för att lagra och komma åt data från ett Python Flask-webbprogram i Azure. Hitta apputvecklingslösningar.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: tutorial
ms.date: 02/23/2017
ms.author: sngun
ms.openlocfilehash: a59de5fad7f457fffcc36ed55fd5862bc9329a1d
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54037165"
---
# <a name="build-a-python-flask-web-application-using-azure-cosmos-db"></a>Utveckla ett webbprogram i Python Flask med Azure Cosmos DB

> [!div class="op_single_selector"]
> * [NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

Den här självstudien beskriver hur du kan använda Azure Cosmos DB för lagring av och åtkomst till data från en Python Flask-webbapp på Azure App Service. Den här självstudien förutsätter att du har viss tidigare erfarenhet av Python och Azure-webbplatser.

I den här självstudien om databaser tar vi upp följande:

1. Skapa och etablera ett Microsoft Azure Cosmos DB-konto.
2. Skapa ett Python Flask-program.
3. Ansluta till och använda Azure Cosmos DB från webbappen.
4. Distribuera webbappen till Azure App Service.

Genom att följa den här självstudien skapar du en enkel röstningsapp där du kan delta i en omröstning.

![Skärmdump av det omröstningsprogram som skapats i denna självstudie om databaser](./media/sql-api-python-application/cosmos-db-pythonr-run-application.png)

## <a name="database-tutorial-prerequisites"></a>Förutsättningar för självstudien om databaser
Innan du följer anvisningarna i den här artikeln bör du se till att du har följande installerat:

* [En Azure-prenumeration](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Visual Studio-2017](https://www.visualstudio.com/downloads/) med **Azure-utveckling** och **Python-utveckling** aktiverat. Du kan kontrollera om dessa krav är installerade, och installera dem, genom att öppna **Visual Studio Installer** lokalt.   
* [Microsoft Azure SDK för Python 2.7](https://azure.microsoft.com/downloads/). 
* [Python 2.7](https://www.python.org/downloads/windows/). Du kan använda 32-bitars eller 64-bitars installation.

> [!IMPORTANT]
> Om du installerar Python 2.7 för första gången ska du på skärmen Customize Python 2.7.13 välja **Add python.exe to Path** (Lägg till python.exe till sökväg).
> 
> ![Skärmdump av skärmen Customize Python 2.7.11 där du ska välja Lägg till python.exe i sökväg](./media/sql-api-python-application/cosmos-db-python-install.png)
> 
> 

* [Microsoft Visual C++-kompilatorn för Python 2.7](https://www.microsoft.com/en-us/download/details.aspx?id=44266).

## <a name="step-1-create-an-azure-cosmos-db-database-account"></a>Steg 1: Skapa ett Azure Cosmos DB-databaskonto
Vi ska börja med att skapa ett Azure Cosmos DB-konto. Om du redan har ett konto eller om du använder Azure Cosmos DB-emulatorn för den här självstudien kan du gå vidare till [Steg 2: Skapa en ny webbapp i Python Flask](#step-2-create-a-new-python-flask-web-application).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

<br/>
Nu går vi igenom hur du skapar en ny Python Flask-webbapp från grunden.

## <a name="step-2-create-a-new-python-flask-web-application"></a>Steg 2: Skapa en ny webbapp i Python Flask
1. I Visual Studio klickar du på menyn **Arkiv**, pekar på **Nytt** och klickar sedan på **Projekt**.
   
    Dialogrutan **Nytt projekt** visas.
2. I den vänstra panelen expanderar du **Mallar** och **Python**. Klicka sedan på **Webb**. 
3. Välj **Flask-webbprojekt** i den mittersta panelen. I rutan **Namn** skriver du sedan **tutorial** och klickar på **OK**. Kom ihåg att paketnamn i Python ska skrivas med gemener, enligt beskrivningen i [Stilguide för Python-kod](https://www.python.org/dev/peps/pep-0008/#package-and-module-names).
   
    Har du inte använt Python Flask tidigare? Det är ett utvecklingsramverk för webbappar som hjälper dig att skapa webbappar i Python snabbare.
   
    ![Skärmdump av fönstret Nytt projekt i Visual Studio med Python markerat till vänster, Python Flask-webbprojekt valt i mitten och namnet tutorial i rutan Namn.](./media/sql-api-python-application/image9.png)
4. I fönstret **Python-verktyg för Visual Studio** klickar du på **Installera i virtuell miljö**. 
   
    ![Skärmdump av databasen tutorial – fönstret Python-verktyg för Visual Studio](./media/sql-api-python-application/python-install-virtual-environment.png)
5. I fönstret **Add Virtual Environment** (Lägg till virtuell miljö) väljer du Python 2.7 eller Python 3.5 i rutan Select an interpreter (Välj en interpretator), godkänner de andra standardinställningarna och klickar sedan på **Create** (Skapa). Då konfigureras den nödvändiga virtuella Python-miljön för projektet.
   
    ![Skärmdump av databasen tutorial – fönstret Python-verktyg för Visual Studio](./media/sql-api-python-application/image10_A.png)
   
    Utdatafönstret visar `Successfully installed Flask-0.10.1 Jinja2-2.8 MarkupSafe-0.23 Werkzeug-0.11.5 itsdangerous-0.24 'requirements.txt' was installed successfully.` när miljön har installerats.

## <a name="step-3-modify-the-python-flask-web-application"></a>Steg 3: Ändra Python Flask-webbappen
### <a name="add-the-python-flask-packages-to-your-project"></a>Lägga till Python Flask-paket i ditt projekt
När projektet har konfigurerats måste du lägga till nödvändiga Flask-paket till ditt projekt, inklusive pydocumentdb, Python-paketet för Azure Cosmos DB SQL API.

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
   
    ![Skärmdump som visar env (Python 2.7) vald med Install från requirements.txt markerat i listan](./media/sql-api-python-application/cosmos-db-python-install-from-requirements.png)
   
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
   
    ![Det tomma Python Flask-webbutvecklingsprojektet visas i en webbläsare](./media/sql-api-python-application/image12.png)
3. Stoppa felsökningen av webbplatsen genom att trycka på **SKIFT**+**F5** i Visual Studio.

### <a name="create-database-collection-and-document-definitions"></a>Skapa databas, samling och dokumentdefinitioner
Nu skapar vi röstningsappen genom att lägga till nya filer och uppdatera andra.

1. Högerklicka på projektet **tutorial** i Solution Explorer. Klicka på **Lägg till** och sedan på **Nytt objekt**. Välj **Tom Python-fil** och namnge filen **forms.py**.  
2. Lägg till nedanstående kod i filen forms.fy och spara filen.

```python
from flask_wtf import Form
from wtforms import RadioField

class VoteForm(Form):
    deploy_preference  = RadioField('Deployment Preference', choices=[
        ('Web Site', 'Web Site'),
        ('Cloud Service', 'Cloud Service'),
        ('Virtual Machine', 'Virtual Machine')], default='Web Site')
```


### <a name="add-the-required-imports-to-viewspy"></a>Lägg till nödvändiga importer i views.py
1. Expandera mappen **tutorial** i Solution Explorer och öppna filen **views.py**. 
2. Lägg till nedanstående importuttryck överst i filen **views.py** och spara sedan filen. Dessa importerar Azure Python SDK för Cosmos DB samt Flask-paketen.
   
    ```python
    from forms import VoteForm
    import config_cosmos
    import pydocumentdb.document_client as document_client
    ```

### <a name="create-database-collection-and-document"></a>Skapa databas, samling och dokument
* Lägg till nedanstående kod i slutet av filen, fortfarande i **views.py**. Med den skapas databasen som formuläret använder. Ta inte bort den befintliga koden i **views.py**. Lägg bara till det här i slutet.

```python
@app.route('/create')
def create():
    """Renders the contact page."""
    client = document_client.DocumentClient(config_cosmos.COSMOSDB_HOST, {'masterKey': config_cosmos.COSMOSDB_KEY})

    # Attempt to delete the database.  This allows this to be used to recreate as well as create
    try:
        db = next((data for data in client.ReadDatabases() if data['id'] == config_cosmos.COSMOSDB_DATABASE))
        client.DeleteDatabase(db['_self'])
    except:
        pass

    # Create database
    db = client.CreateDatabase({ 'id': config_cosmos.COSMOSDB_DATABASE })

    # Create collection
    collection = client.CreateCollection(db['_self'],{ 'id': config_cosmos.COSMOSDB_COLLECTION })

    # Create document
    document = client.CreateDocument(collection['_self'],
        { 'id': config_cosmos.COSMOSDB_DOCUMENT,
          'Web Site': 0,
          'Cloud Service': 0,
          'Virtual Machine': 0,
          'name': config_cosmos.COSMOSDB_DOCUMENT 
        })

    return render_template(
       'create.html',
        title='Create Page',
        year=datetime.now().year,
        message='You just created a new database, collection, and document.  Your old votes have been deleted')
```


### <a name="read-database-collection-document-and-submit-form"></a>Läsa databas, samling och dokument samt skicka formulär
* Lägg till nedanstående kod i slutet av filen, fortfarande i **views.py**. Med den kan formuläret konfigureras samt databas, samling och dokument läsas. Ta inte bort den befintliga koden i **views.py**. Lägg bara till det här i slutet.

```python
@app.route('/vote', methods=['GET', 'POST'])
def vote(): 
    form = VoteForm()
    replaced_document ={}
    if form.validate_on_submit(): # is user submitted vote  
        client = document_client.DocumentClient(config_cosmos.COSMOSDB_HOST, {'masterKey': config_cosmos.COSMOSDB_KEY})

        # Read databases and take first since id should not be duplicated.
        db = next((data for data in client.ReadDatabases() if data['id'] == config_cosmos.COSMOSDB_DATABASE))

        # Read collections and take first since id should not be duplicated.
        coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config_cosmos.COSMOSDB_COLLECTION))

        # Read documents and take first since id should not be duplicated.
        doc = next((doc for doc in client.ReadDocuments(coll['_self']) if doc['id'] == config_cosmos.COSMOSDB_DOCUMENT))

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
1. I Solution Explorer, i mappen **tutorial**, högerklickar du på mappen **templates**, klickar på **Add** (Lägg till) och klickar sedan på **New Item** (Nytt objekt). 
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
6. Lägg till nedanstående kod i **vote.html** i elementet `<body`>. Den visar omröstningen och godkänner rösterna. När rösterna registreras skickas kontrollen till views.py där Microsoft Azure Cosmos DB bekräftar den lagda rösten och lägger till den i dokumentet.
   
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
    <h2>Python + Azure Cosmos DB Voting Application.</h2>
    <h3>This is a sample Cosmos DB voting application using PyDocumentDB</h3>
    <p><a href="{{ url_for('create') }}" class="btn btn-primary btn-large">Create/Clear the Voting Database &raquo;</a></p>
    <p><a href="{{ url_for('vote') }}" class="btn btn-primary btn-large">Vote &raquo;</a></p>
    {% endblock %}
    ```

### <a name="add-a-configuration-file-and-change-the-initpy"></a>Lägg till en konfigurationsfil och ändra \_\_init\_\_.py
1. Högerklicka på projektet **tutorial** i Solution Explorer. Klicka på **Add** (Lägg till) och **New Item** (Nytt objekt), välj **Empty Python File** (Tom Python-fil) och namnge filen **config_cosmos.py**. Den här konfigurationsfilen krävs av formulär i Flask. Du kan även använda den för att tillhandahålla en hemlig nyckel. Nyckeln behövs inte i den här självstudien.
2. Lägg till nedanstående config_cosmos.py. I nästa steg behöver du ändra värdena för **COSMOSDB\_HOST** och **COSMOSDB\_KEY**.
   
    ```python
    CSRF_ENABLED = True
    SECRET_KEY = 'you-will-never-guess'
   
    COSMOSDB_HOST = 'https://YOUR_COSMOSDB_NAME.documents.azure.com:443/'
    COSMOSDB_KEY = 'YOUR_SECRET_KEY_ENDING_IN_=='
   
    COSMOSDB_DATABASE = 'voting database'
    COSMOSDB_COLLECTION = 'voting collection'
    COSMOSDB_DOCUMENT = 'voting document'
    ```
3. På [Azure-portalen](https://portal.azure.com/) går du till sidan **Keys** (Nycklar) genom att klicka på **Browse** (Bläddra) och **Azure Cosmos DB Accounts** (Azure Cosmos DB-konton). Dubbelklicka på namnet på det konto som du vill använda och klicka sedan på knappen **Keys** (Nycklar) i området **Essentials**. På sidan **Keys** (Nycklar) kopierar du **URI**-värdet och klistrar in det i filen **config.py** som värdet för egenskapen **COSMOSDB\_HOST**. 
4. Tillbaka i Azure Portal, på sidan **Keys** (Nycklar), kopierar du värdet för **Primary Key** (primärnyckel) eller **Secondary Key** (sekundärnyckel) och klistrar in det i filen **config_cosmos.py** som värdet för egenskapen **COSMOSDB\_KEY**.
5. I filen **\_\_init\_\_.py** lägger du till följande rader för att inkludera config-filläsningen och viss grundläggande loggning: 
   
        app.config.from_object('config_cosmos')
        logging.basicConfig(level=logging.INFO,format='%(asctime)s - %(name)s - %(levelname)s - %(message)s')
        logger = logging.getLogger(__name__)
   
    så att innehållet i datafilen är:
   
    ```python
    import logging
    from flask import Flask
    app = Flask(__name__)
    app.config.from_pyfile('config_cosmos')
    logging.basicConfig(level=logging.INFO,format='%(asctime)s - %(name)s - %(levelname)s - %(message)s')
    logger = logging.getLogger(__name__)

    import tutorial.views
    ```
6. När du har lagt till alla filer bör Solution Explorer se ut så här:
   
    ![Skärmdump av Visual Studio Solution Explorer-fönstret](./media/sql-api-python-application/cosmos-db-python-solution-explorer.png)

## <a name="step-4-run-your-web-application-locally"></a>Steg 4: Kör webbappen lokalt
1. Bygg lösningen genom att trycka på **Ctrl**+**Skift**+**B**.
2. När den har byggts startar du webbplatsen genom att trycka på **F5**. Du bör se nedanstående på skärmen.
   
    ![Skärmbild av Python + Azure Cosmos DB-röstninsgprogrammet i en webbläsare](./media/sql-api-python-application/cosmos-db-pythonr-run-application.png)
3. Skapa databasen genom att klicka på **Skapa/rensa röstningsdatabasen**.
   
    ![Skärmdump av sidan Skapa i webbappen – utvecklingsdetaljer](./media/sql-api-python-application/cosmos-db-python-run-create-page.png)
4. Klicka sedan på **Rösta** och välj ett alternativ.
   
    ![Skärmdump av webbappen med en röstningsfråga](./media/sql-api-python-application/cosmos-db-vote.png)
5. För varje röst du lägger ökas den aktuella räknaren.
   
    ![Skärmdump som visar sidan Röstningsresultat](./media/sql-api-python-application/cosmos-db-voting-results.png)
6. Stoppa felsökningen av projektet genom att trycka på SKIFT + F5.

## <a name="step-5-deploy-the-web-application-to-azure"></a>Steg 5: Distribuera webbappen till Azure
Nu när hela programmet fungerar korrekt mot Azure Cosmos DB lokalt är det dags att skapa en web.config-fil, uppdatera filerna på servern för att matcha den lokala miljön och sedan visa det färdiga programmet på Azure. Den här proceduren är specifik för Visual Studio 2017. Om du använder en annan version av Visual Studio kan du läsa artikeln om att [publicera till Azure App Service](/visualstudio/python/publishing-to-azure).

1. I Visual Studio **Solution Explorer** högerklickar du på projektet och väljer **Add > New Item...** (Lägg till > Nytt objekt...). I den dialogruta som visas väljer du mallen **Azure web.config (Fast CGI)** och sedan **OK**. Då skapas en `web.config`-fil i rotmappen för projektet. 

2. Ändra `<system.webServer>`-avsnittet i `web.config` så att sökvägen matchar Python-installationen. Till exempel ska posten för Python 2.7 x64 se ut på följande sätt:
    
    ```xml
    <system.webServer>
        <handlers>
            <add name="PythonHandler" path="*" verb="*" modules="FastCgiModule" scriptProcessor="D:\home\Python27\python.exe|D:\home\Python27\wfastcgi.py" resourceType="Unspecified" requireAccess="Script"/>
        </handlers>
    </system.webServer>
    ```

3. Ange `WSGI_HANDLER`-posten i `web.config` till `tutorial.app` för att matcha projektnamnet. 

    ```xml
    <!-- Flask apps only: change the project name to match your app -->
    <add key="WSGI_HANDLER" value="tutorial.app"/>
    ```

4. I Visual Studio **Solution Explorer** expanderar du mappen **tutorial** och högerklickar på `static`-mappen. Välj **Add > New Item...** (Lägg till > Nytt objekt...), mallen ”Azure static files web.config” och sedan **OK**. Den här åtgärden skapar en till `web.config` i `static`-mappen som inaktiverar Python-bearbetning för den mappen. Den här konfigurationen skickar förfrågningar efter statiska filer till standardwebbservern i stället för att använda Python-programmet.

5. Spara filerna och högerklicka sedan på projektet i Solution Explorer (kontrollera att det inte fortfarande körs lokalt). Välj **Publicera**.  
   
     ![Skärmdump som visar tutorial markerad i Solution Explorer med alternativet Publicera markerat](./media/sql-api-python-application/image20.png)
6. I dialogrutan **Publish** (Publicera) väljer du **Microsoft Azure App Service** och sedan **Create New** (Skapa nytt). Klicka sedan på **Publish** (publicera).
   
    ![Skärmbild som visar fönstret Publicera till webben med Microsoft Azure App Service markerat](./media/sql-api-python-application/cosmos-db-python-publish.png)
7. I dialogrutan **Create App Service** (Skapa apptjänst) anger du namnet på webbappen och **Subscription** (Prenumeration), **Resource Group** (Resursgrupp) samt **App Service Plan** och klickar sedan på **Create** (Skapa).
   
    ![Skärmdump av fönstret Microsoft Azure-webbappar](./media/sql-api-python-application/cosmos-db-python-create-app-service.png)
8. Efter några sekunder kopierar Visual Studio klart filerna till servern och visar ”The page cannot be displayed because an internal server error has occurred.” (Sidan kan inte visas eftersom ett internt serverfel har inträffat) på sidan `http://<your app service>.azurewebsites.net/`.

9. I Azure-portalen öppnar du ditt nya App Service-konto. På navigeringsmenyn rullar du ned till avsnittet **Development Tools** (Utvecklingsverktyg), väljer **Extensions** (Tillägg) och klickar sedan på **+ Add** (+ Lägg till).

10. På sidan **Choose extension** (Välj tillägg) rullar du ned till den senaste Python 2.7-installationen, väljer alternativet x86-bitars eller x64-bitars och klickar sedan på **OK** för att godkänna de juridiska villkoren.  
   
11. Använd Kudu-konsolen, som du kan bläddra till på `https://<your app service name>.scm.azurewebsites.net/DebugConsole`, för att installera de paket som listas i appens `requirements.txt`-fil. I Kudu Diagnostic Console (Kudu diagnostisk konsol) gör du det här genom att navigera till Python-mappen `D:\home\Python27` och sedan köra följande kommando som beskrivs i avsnittet om [Kudu-konsolen](/visualstudio/python/managing-python-on-azure-app-service#azure-app-service-kudu-console):

    ```
    D:\home\Python27>python -m pip install --upgrade -r /home/site/wwwroot/requirements.txt
    ```          

12. Starta om App Service i Azure-portalen när du har installerat de nya paketen genom att trycka på knappen **Restart** (Starta om). 

    > [!Tip] 
    > Om du gör några ändringar i appens `requirements.txt`-fil ska du se till att återigen använda Kudu-konsolen för att installera paket som nu visas i den filen. 

13. När du har konfigurerat klart servermiljön uppdaterar du sidan i webbläsaren, så bör webbappen visas.

    ![Resultatet av publicering av apparna Bottle, Flask och Django till App Service](./media/sql-api-python-application/python-published-app-services.png)

    > [!Tip] 
    > Om sidan inte visas, eller om du fortfarande får meddelandet ”The page cannot be displayed because an internal server error has occurred” (sidan kan inte visas eftersom ett internt serverfel har inträffat), öppnar du filen web.config i Kudo och lägger till ` <httpErrors errorMode="Detailed"></httpErrors>` i avsnittet system.webServer och uppdaterar sedan sidan. Detta skickar detaljerade felutdata till webbläsaren. 

## <a name="troubleshooting"></a>Felsökning
Om det här är den första Python-appen du kör på datorn kontrollerar du att följande mappar (eller motsvarande installationsplatser) ingår i PATH-variabeln:

    C:\Python27\site-packages;C:\Python27\;C:\Python27\Scripts;

Om du får ett felmeddelande på din röstningssida och du har gett ditt projekt ett annat namn än **tutorial**, kontrollerar du att **\_\_init\_\_.py** refererar rätt projektnamn i raden: `import tutorial.view`.

## <a name="next-steps"></a>Nästa steg
Grattis! Du har skapat din första Python-webbapp med hjälp av Azure Cosmos DB och publicerat den på Azure.

Om du vill lägga till ytterligare funktioner i webbappen kan du ta en titt på tillgängliga API:er i [Azure Cosmos DB Python SDK](sql-api-sdk-python.md).

Mer information om Azure, Visual Studio och Python finns i [Python Developer Center](https://azure.microsoft.com/develop/python/). 

Ytterligare självstudier om Python Flask finns i [Ingående självstudie om Flask, del I: Hello world!](https://blog.miguelgrinberg.com/post/the-flask-mega-tutorial-part-i-hello-world). 
