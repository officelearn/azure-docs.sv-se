<properties
    pageTitle="Komma igång med Azure Mobile Services och Sencha"
    description="Den här kursen hjälper dig att komma igång med Mobile Services och Sencha (ramverk för mobilappar baserat på HTML 5)."
    services="mobile-services"
    documentationCenter=""
    authors="ggailey777"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-services"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-sencha"
    ms.devlang="multiple"
    ms.topic="get-started-article"
    ms.date="07/21/2016"
    ms.author="glenga"/>

# <a name="getting-started"> </a>Komma igång med Mobile Services och Sencha Touch

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]
&nbsp;

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]

##Översikt

Den här kursen visar hur du kan använda Azure Mobile Services i din Sencha Touch-app. Du får skapa en enkel *To Do List*-app (att göra-lista) med Sencha Touch som använder en mobiltjänst som du definierar via den klassiska Azure-portalen. Den här kursen är avsedd för webbapplikationsutvecklare på medelhög till avancerad nivå som har goda kunskaper i JavaScript och som är bekanta med Sencha Touch-ramverket.

Om du vill kan du titta på videoklippet i stället. Det visar samma steg som kursen. I videon förklarar Arthur Kay hur du skapar en Sencha Touch-app med Azure Mobile Services Backend.

> [AZURE.VIDEO getting-started-with-sencha-touch]


En skärmbild från den färdiga appen:

![][0]

##Krav

- Ladda ned och installera [Sencha Touch](http://wwww.sencha.com/products/touch/download" target="_blank").

- Ladda ned och installera [Sencha Cmd Tool](http://www.sencha.com/products/sencha-cmd/download" target="_blank").

- Java Runtime Environment (JRE) eller Java Development Kit (om du skapar Android-appar)
- Ruby- och SASS-gem.

## <a name="create-new-service"> </a>Skapa en ny mobiltjänst

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

##Skapa en TodoItems-tabell

När du har skapat din mobiltjänst finns en snabbguide i den klassiska Azure-portalen om hur du skapar en ny databastabell för mobiltjänsten.

1. Gå till den [Klassisk Azure-portal], klicka på **Mobile Services** och sedan på den mobiltjänst som du nyss skapade.

2. Välj snabbstartsfliken, klicka på **HTML** under **Choose platform** (Välj plattform) och expandera **Create a new HTML app** (Skapa en ny HTML-app).

    ![Snabbstartsguide för HTML-appar](./media/partner-sencha-mobile-services-get-started/mobile-portal-quickstart-html.png)

    Därmed visas tre enkla steg för att skapa och vara värd för en HTML-app som är ansluten till din mobiltjänst.

    ![Snabbstartsguide för HTML-appar](./media/partner-sencha-mobile-services-get-started/mobile-quickstart-steps-html.png)

3. Klicka på **Create TodoItems table** (Skapa TodoItems-tabell) för att skapa en tabell för lagring av appdata.

    > [AZURE.NOTE] Ladda INTE ned HTML-appen från den klassiska Azure-portalen. Vi skapar i stället en Sencha Touch-app manuellt i avsnittet nedan.


1. Notera **appKey** och **appUrl** i den klassiska Azure-portalen. Du behöver dem i andra avsnitt i den här kursen.

    ![appKey](./media/partner-sencha-mobile-services-get-started/mobile-app-key-portal.png)

1. Välj fliken **Configure** och kontrollera att `localhost` redan finns i listan **Allow requests from host names** (Tillåt begäranden från värdnamn) under **Cross-Origin Resource Sharing (CORS)**. Om den inte gör det skriver du `localhost` i fältet **Host name** (Värdnamn) och klickar på **Save** (Spara).

    ![Konfigurera CORS för localhost](./media/partner-sencha-mobile-services-get-started/mobile-services-set-cors-localhost.png)

##Generera Touch-appen

Det är enkelt att generera en Sencha Touch-mallapp med Sencha Cmd. Det är ett bra sätt att snabbt skapa en körbar app.

Kör följande kommando från katalogen där du installerade Touch-ramverket:

    $ sencha generate app Basic /path/to/application

Därmed genereras en Touch-mallapp med namnet Basic. Du startar appen genom att starta webbläsaren och öppna /path/to/application. Därmed visas Touch-exempelappen.

##Installera Sencha Touch-tillägg för Azure

Tillägget för Azure installeras manuellt eller som ett Sencha-paket. Du väljer själv vilken metod du vill använda.

###Manuell installation

I de flesta Touch-appar lägger du till ett externt klassbibliotek genom att ladda ned paketet, packa upp det i programkatalogen och konfigurera Touch-inläsaren med bibliotekets plats.

Du kan lägga till Azure-tillägget manuellt i appen:

1. Ladda ned Azure-tilläggspaketet [här](https://market.sencha.com/extensions/sencha-extensions-for-microsoft-azure). (Använd ditt Sencha Forums ID för att få åtkomst till platsen.)

2. Kopiera Azure-tilläggspaketet från nedladdningskatalogen till den plats där du vill ha paketet och packa upp det:

        $ cd /path/to/application
        $ mv /download-location/azure.zip .
        $ unzip azure.zip

    Därmed skapas en **azure**-katalog som innehåller paketkällan, exempel och dokumentation. Källan placeras i katalogen **azure/src**.


###Installation som ett Sencha-paket

> [AZURE.NOTE] Du kan bara använda den här metoden när du har genererat appen med kommandot <code>sencha generate app</code>.

Alla appar som genereras av Sencha Cmd har en "packages"-mapp i roten. Du kan ändra plats för mappen, men oavsett var packages-mappen är fungerar den som lagringsplats för alla paket som används av appen (eller apparna om du har skapat en Sencha-arbetsyta).

Eftersom Ext.Azure är ett Sencha Cmd-paket kan du enkelt installera och ta med källkoden i appen med hjälp av Sencha Cmd. (Mer information finns i [Sencha Cmd-paket](http://docs.sencha.com/cmd/6.x/cmd_packages/cmd_packages.html).)

Om du vill ladda ned och installera Azure-tilläggspaket från lagringsplatsen för Sencha-paket måste du lägga till paketnamnet i din **app.json**-fil och skapa din app:

1. Så här lägger du till Azure-paketet i filen app.json:

        {
            "name": "Basic",
            "requires": [
                "touch-azure"
            ]
        }

2. Återskapa appen med **sencha cmd** för att hämta och installera paketet:

        $ sencha app build

Både **sencha app build** och **sencha app refresh** utför nu de åtgärder som krävs för att integrera paketet i appen. När du har ändrat paketkraven måste du normalt köra **sencha app refresh** för att uppdatera de metadata som krävs för att stödja utvecklingsläget.

Oavsett vilket kommando du kör laddar Sencha Cmd ned och expanderar paketet i din packages-mapp. När du har gjort detta finns mappen packages/touch-azure i din arbetsyta.

##Ta med och konfigurera Azure

**Filnamn**: app.js

Nu när Azure-tillägget har hämtats och installerats i programkatalogen är nästa steg att berätta var appen hittar källfilerna och att appen ska kräva dessa filer:

1. Konfigurera Sencha-inläsaren med platsen för källkoden:

        Ext.Loader.setConfig({
            enabled : true,
            paths   : {
                'Ext'       : 'touch/src',
                'Ext.azure' : '/path-to/azure-for-touch/azure/src'
            }
        });


2. Ange att Azure-klassfilerna krävs:

        Ext.application({

            requires: [ 'Ext.azure.Azure' ],

            // ...

        });


3. Konfigurera Azure

    Azure-paketet initieras genom att anropa metoden **Ext.Azure.init** i startavsnittet i din app. Ett konfigurationsobjekt som innehåller autentiseringsuppgifter för mobiltjänsten samt andra uppgifter och funktioner som du vill använda skickas till den här metoden.

    Du kan skicka konfigurationsobjektet direkt till init-metoden, men vi rekommenderar att du skapar en konfigurationsegenskap för Sencha-appen med namnet **azure** och placerar all information som behövs där. Du kan sedan överföra egenskapsvärdet till metoden Ext.Azure.init.

    När du skapar en mobiltjänst i Azure (se [Komma igång med Azure](http://senchaazuredocs.azurewebsites.net/#!/guide/getting_started)) tilldelas tjänsten en programnyckel och URL. Den här informationen måste anges i Azure-paket så att det kan ansluta till tjänsten.

    Det här exemplet visar en enkel Azure-konfiguration och initiering som bara tillhandahåller en programnyckel och URL:

        Ext.application({
            name: 'Basic',

            requires: [ 'Ext.azure.Azure' ],

            azure: {
                appKey: 'myazureservice-access-key',
                appUrl: 'myazure-service.azure-mobile.net'
            },

            launch: function() {

                // Call Azure initialization

                Ext.Azure.init(this.config.azure);

           }
        });

    Mer information om konfigurationsalternativen för Azure finns i Ext.Azure API-dokumentationen.


Grattis! Nu har din app åtkomst till din mobiltjänst.

##Skapa ToDo-appen

Nu när vi har konfigurerat din app så att den innehåller Azure-tillägget och tagit med den med dina mobiltjänstuppgifter, kan vi gå vidare med att skapa en Touch-app som använder din mobiltjänst för att visa och redigera ToDo-listdata som lagras i tjänsten.

###Konfigurera Azure dataproxyservern

**Filnamn:** app/model/TodoItem.js

Touch-appen kommer att kommunicera med din mobiltjänst via en dataproxyserver. Proxyservern gör allt arbete med både skicka förfrågningar till och ta emot data från mobiltjänsten. Vid användning i kombination med Touch-datamodell och arkiv (eng. ”store”) hanteras det tunga arbetet med att bearbeta fjärrdata och få in dessa data i appen av Touch.

Med Sencha Touch-modeller får du en definition av de dataposter som du använder i appen. Förutom att du kan definiera datafält med dessa tillhandahåller de även en konfiguration för proxyservern som hanterar kommunikationen mellan appen och Azure-mobiltjänsten.

I koden nedan kan du se att vi definierar fälten (och deras typer) för modellen, och även anger en proxykonfiguration. Du måste ange en typ (i det här fallet ”azure”), mobil tjänst tabellnamn (ToDoItem) och andra valfria parametrar när du konfigurerar proxyservern. I det här exemplet aktiverar vi sidväxling med proxyserver så att vi kan bläddra smidigt framåt och bakåt mellan listobjekt.

Azure-proxyservern ställer automatiskt in alla HTTP-huvuden med lämpliga CRUD-åtgärder som förväntas av Azure API:t (inklusive autentiseringsuppgifter, om sådana finns).

    Ext.define('Basic.model.TodoItem', {
        extend : 'Ext.data.Model',

        requires : [
            'Ext.azure.Proxy'
        ],

        config : {
            idProperty : 'id',
            useCache   : false,

            fields     : [
                {
                    name : 'id',
                    type : 'int'
                },
                {
                    name : 'text',
                    type : 'string'
                },
                {
                    name : 'complete',
                    type : 'boolean'
                }
            ],

            proxy : {
                type               : 'azure',
                tableName          : 'TodoItem',
                enablePagingParams : true
            }
        }
    });


###Lagra att göra-objekten

**Filnamn**: app/store/TodoItems.js

Sencha Touch-arkiv används för att lagra samlingar av dataposter (modeller). Du kan använda dem som källor för Touch-komponenter så att du kan visa posterna på flera olika sätt. Till exempel i rutnät, diagram och listor.

Här definierar vi ett arkiv som används för att lagra alla objekt i att göra-listan som hämtas från din Azure-mobiltjänst. Observera att arkivkonfigurationen innehåller namnet på modelltypen (Basic.model.TodoItem, definieras ovan). Detta definierar strukturen för de poster som ska ingå i arkivet.

Det finns också några ytterligare konfigurationsalternativ för arkivet, t.ex. att ange sidstorlek (8 poster) och att sorteringen av posterna för arkivet sker externt av Azure-mobiltjänsten (ingen sortering utförs lokalt i själva arkivet).

    Ext.define('Basic.store.TodoItems', {
        extend : 'Ext.data.Store',

        requires : [
            'Basic.model.TodoItem'
        ],

        config : {
            model        : 'Basic.model.TodoItem',
            pageSize     : 8,
            remoteSort   : true,
            remoteFilter : true
        }
    });


###Visa och redigera att göra-objekt

**Filnamn**: app/view/DataItem.js

Vi har definierat strukturen för varje att göra-objekt och skapat ett arkiv för att lagra alla poster i och nu är det dags att fundera över hur vi vill visa den här informationen för användaren i appen. Normalt visar vi information för användaren med **vyer**. En vy kan bestå av ett valfritt antal Touch-komponenter, enskilda eller kombinerade med andra.

Vyn nedan består av ett ListItem som definierar hur varje post ska visas och några knappar som ska ha åtgärder för att ta bort respektive objekt.

    Ext.define('Basic.view.DataItem', {
        extend : 'Ext.dataview.component.ListItem',
        xtype  : 'basic-dataitem',

        requires : [
            'Ext.Button',
            'Ext.layout.HBox',
            'Ext.field.Checkbox'
        ],

        config : {
            checkbox : {
                docked     : 'left',
                xtype      : 'checkboxfield',
                width      : 50,
                labelWidth : 0
            },

            text : {
                flex : 1
            },

            button : {
                docked   : 'right',
                xtype    : 'button',
                ui       : 'plain',
                iconMask : true,
                iconCls  : 'delete',
                style    : 'color: red;'
            },

            dataMap : {
                getText : {
                    setHtml : 'text'
                },

                getCheckbox : {
                    setChecked : 'complete'
                }
            },

            layout : {
                type : 'hbox',
                align: 'stretch'
            }
        },

        applyCheckbox : function(config) {
            return Ext.factory(config, Ext.field.Checkbox, this.getCheckbox());
        },

        updateCheckbox : function (cmp) {
            if (cmp) {
                this.add(cmp);
            }
        },

        applyButton : function(config) {
            return Ext.factory(config, Ext.Button, this.getButton());
        },

        updateButton : function (cmp) {
            if (cmp) {
                this.add(cmp);
            }
        }

    });


###Skapa din primära vy

**Filnamn**: app/view/Main.js

Nu när vi har definierat layouten för ett enskilt objekt i att göra-listan (ovan) vill vi skapa ett fullständigt användargränssnitt för listan som definierar den faktiska listan med objekt, en apptitel och en knapp för att lägga till en ny uppgift.

    Ext.define('Basic.view.Main', {
        extend : 'Ext.dataview.List',
        xtype  : 'main',

        requires : [
            'Ext.TitleBar',
            'Ext.dataview.List',
            'Ext.data.Store',
            'Ext.plugin.PullRefresh',
            'Ext.plugin.ListPaging',
            'Basic.view.DataItem'
        ],

        config : {
            store : 'TodoItems',

            useSimpleItems : false,
            defaultType    : 'basic-dataitem',

            plugins : [
                {
                    xclass          : 'Ext.plugin.PullRefresh',
                    pullRefreshText : 'Pull down to refresh!'
                },
                {
                    xclass     : 'Ext.plugin.ListPaging',
                    autoPaging : true
                }
            ],

            scrollable : {
                direction     : 'vertical',
                directionLock : true
            },

            items : [
                {
                    docked : 'top',
                    xtype  : 'titlebar',
                    title  : 'Azure Mobile - Basic Data Example'
                },
                {
                    xtype  : 'toolbar',
                    docked : 'bottom',
                    items  : [
                        {
                            xtype       : 'textfield',
                            placeHolder : 'Enter new task',
                            flex        : 1
                        },
                        {
                            xtype  : 'button',
                            action : 'add',
                            text   : 'Add'
                        }
                    ]
                }
            ]
        }
    });

###Få allt att fungera tillsammans

**Filnamn**: app/controller/Main.js

Det sista steget är att hantera när knappar trycks ned (ta bort, spara, o.s.v.) och att ange logiken bakom alla dessa begäranden. Sencha Touch använder kontroller som lyssnar på dessa händelser och svarar i enlighet därmed.

    Ext.define('Basic.controller.Main', {
        extend : 'Ext.app.Controller',

        config : {
            refs : {
                todoField : 'main toolbar textfield',
                main      : 'main'
            },

            control : {
                'button[action=add]'    : {
                    tap : 'onAddItem'
                },
                'button[action=reload]' : {
                    tap : 'onReload'
                },

                main : {
                    activate      : 'loadInitialData',
                    itemdoubletap : 'onItemEdit'
                },

                'basic-dataitem checkboxfield' : {
                    change : 'onItemCompleteTap'
                },

                'basic-dataitem button' : {
                    tap : 'onItemDeleteTap'
                }
            }
        },

        loadInitialData : function () {
            Ext.getStore('TodoItems').load();
        },

        onItemDeleteTap : function (button, e, eOpts) {
            var store    = Ext.getStore('TodoItems'),
                dataItem = button.up('dataitem'),
                rec      = dataItem.getRecord();

            rec.erase({
                success: function (rec, operation) {
                    store.remove(rec);
                },
                failure: function (rec, operation) {
                    Ext.Msg.alert(
                        'Error',
                        Ext.util.Format.format('There was an error deleting this task.<br/><br/>    Status Code: {0}<br/>Status Text: {1}',
                        operation.error.status,
                        operation.error.statusText)
                    );
                }
            });
        },

        onItemCompleteTap : function (checkbox, newVal, oldVal, eOpts) {
            var dataItem = checkbox.up('dataitem'),
                rec      = dataItem.getRecord(),
                recVal   = rec.get('complete');

            // this check is needed to prevent an issue where multiple creates get triggered from one create
            if (newVal !== recVal) {
                rec.set('complete', newVal);
                rec.save({
                    success: function (rec, operation) {
                        rec.commit();
                    },
                    failure: function (rec, operation) {
                        // since there was a failure doing the update on the server then silently reject the change
                        rec.reject(true);
                        Ext.Msg.alert(
                            'Error',
                            Ext.util.Format.format('There was an error updating this task.<br/><br/>Status Code: {0}<br/>Status Text: {1}',
                            operation.error.status,
                            operation.error.statusText)
                        );
                    }
                });
            }
        },

        onItemEdit : function (list, index, target, record, e, eOpts) {
            var rec = list.getSelection()[0];

            Ext.Msg.prompt('Edit', 'Rename task',
                function (buttonId, value) {
                    if (buttonId === 'ok') {
                        rec.set('text', value);
                        rec.save({
                            success: function (rec, operation) {
                                rec.commit();
                            },
                            failure: function (rec, operation) {
                                // since there was a failure doing the update on the server then reject the change
                                rec.reject();
                                Ext.Msg.alert(
                                    'Error',
                                    Ext.util.Format.format('There was an error updating this task.<br/><br/>Status Code: {0}<br/>Status Text: {1}',
                                    operation.error.status,
                                    operation.error.statusText)
                                );
                            }
                        });
                    }
                },
                null,
                false,
                record.get('text')
            );
        },

        onReload : function () {
            Ext.getStore('TodoItems').load();
        },

        onAddItem : function () {
            var me = this,
                rec,
                store = Ext.getStore('TodoItems'),
                field = me.getTodoField(),
                value = field.getValue();

            if (value === '') {
                Ext.Msg.alert('Error', 'Please enter Task name', Ext.emptyFn);
            }
            else {
                rec = Ext.create('Basic.model.TodoItem', {
                    complete : false,
                    text     : value
                });
                //store.insert(0, rec); //insert at the top
                //store.sync();
                rec.save({
                    success: function (rec, operation) {
                        store.insert(0, rec); //insert at the top
                        field.setValue('');
                    },
                    failure: function (rec, operation) {
                        Ext.Msg.alert(
                            'Error',
                            Ext.util.Format.format('There was an error creating this task.<br/><br/>Status Code: {0}<br/>Status Text: {1}',
                            operation.error.status,
                            operation.error.statusText)
                        );
                    }
                });
            }
        }
    });

###Färdigställa allt

**Filnamn**: app.js

Det sista steget är att slutföra redigeringen av huvudappfilen och ange information om modeller, arkiv, vyer och kontroller som har definierats. Källfilerna för dessa resurser läses in automatiskt i appen. Slutligen anropas startmetoden som skapar och visar den primära appvyn Basic.main.View.


    Ext.Loader.setConfig({
        enabled : true,
        paths   : {
            'Ext'       : 'touch/src',
            'Ext.azure' : 'packages/azure/src'
        }
    });

    Ext.application({
        name : 'Basic',

        requires : [
            'Ext.MessageBox',
            'Ext.azure.Azure'
        ],

        views : [
            'Main'
        ],

        controllers : [
            'Main'
        ],

        stores : [
            'TodoItems'
        ],

        azure : {
            appUrl : 'YOUR_APP_URL.azure-mobile.net',
            appKey : 'YOUR_APP_KEY'
        },

        icon : {
            '57'  : 'resources/icons/Icon.png',
            '72'  : 'resources/icons/Icon~ipad.png',
            '114' : 'resources/icons/Icon@2x.png',
            '144' : 'resources/icons/Icon~ipad@2x.png'
        },

        isIconPrecomposed : true,

        startupImage : {
            '320x460'   : 'resources/startup/320x460.jpg',
            '640x920'   : 'resources/startup/640x920.png',
            '768x1004'  : 'resources/startup/768x1004.png',
            '748x1024'  : 'resources/startup/748x1024.png',
            '1536x2008' : 'resources/startup/1536x2008.png',
            '1496x2048' : 'resources/startup/1496x2048.png'
        },

        launch : function () {
            // Destroy the #appLoadingIndicator element
            Ext.fly('appLoadingIndicator').destroy();

            // Initialize Azure
            Ext.Azure.init(this.config.azure);

            // Initialize the main view
            Ext.Viewport.add(Ext.create('Basic.view.Main'));
        },

        onUpdated : function () {
            Ext.Msg.confirm(
                "Application Update",
                "This application has just successfully been updated to the latest version. Reload now?",
                function (buttonId) {
                    if (buttonId === 'yes') {
                        window.location.reload();
                    }
                }
            );
        }
    });

###Konfigurera värd för och köra Sencha Touch-appen

Det sista steget i den här kursen är att konfigurera datorn som värd och köra den nya appen lokalt på datorn.

  1. I terminalen bläddrar du till platsen där den uppackade appen finns.

  2. Kör följande kommandon med Sencha Cmd:

    * *sencha app refresh*: Detta instruerar Sencha Cmd att hitta alla appberoenden och ladda ned de paket som behövs (till exempel [Sencha Touch-tillägg för Azure](https://market.sencha.com/extensions/sencha-extensions-for-microsoft-azure)).

    * *sencha web start*: Detta startar en lokal webbserver för att testa appen.

    ![sencha web start](./media/partner-sencha-mobile-services-get-started/sencha-web-start.png)

  3. Starta appen genom att öppna URL:en som anges i terminalen i en webbläsare (t.ex. http://localhost:1841).

  4. Ange en beskrivande text i appen, till exempel Slutföra kursen, och klicka sedan på **Add**.

    ![Nytt att göra-objekt](./media/partner-sencha-mobile-services-get-started/new-todo-item.png)

    Därmed skickas en POST-begäran till den nya mobiltjänsten som finns på Azure. Data från begäran infogas i tabellen TodoItem.

  5. Gå till den [Klassisk Azure-portal], välj fliken **Data** och klicka sedan på tabellen TodoItems.

    ![Tabellen Todo Items](./media/partner-sencha-mobile-services-get-started/mobile-data-tab.png)

    Nu kan du bläddra bland de data som infogats i tabellen via appen.

    ![Bläddra i todoitem-tabellen](./media/partner-sencha-mobile-services-get-started/mobile-data-browse.png)

##Nästa steg
Nu när du har slutfört guiden kan du gå vidare och lära dig fler viktiga uppgifter i Mobile Services med Sencha:

[Ladda ned](https://github.com/arthurakay/sencha-touch-azure-example) en fullständig exempelapp med ytterligare design och funktioner och se vad du kan göra mer med Sencha Touch!

Sedan kan du läsa mer om Sencha Touch-tillägg för Azure:

  * [Genomgång](http://docs.sencha.com/touch-azure/1.0.0/#!/guide/data_filters) av exempelapp
  * Få hjälp i [Sencha-forum](http://www.sencha.com/forum)
  * Bläddra i [Sencha-dokumentation](http://docs.sencha.com/)
  * Använda Sencha med Azure Mobile Services: [(Video)](http://channel9.msdn.com/Shows/Cloud+Cover/Episode-126-Using-Sencha-With-Windows-Azure-Mobile-Services)


##Ytterligare resurser

  * [Ladda ned Sencha Touch](http://pages.sencha.com/touch-for-azure.html)
  * [Sencha Touch-tillägg för Azure](https://market.sencha.com/extensions/sencha-extensions-for-microsoft-azure)


##Sammanfattning

Det här exemplet tillhandahålls i Sencha Touch-tillägget för Azure-paketet och finns i katalogen examples som Basic Data Example. Det finns några fler exempel som visar andra funktioner i det här tillägget med detaljerade kommentarer och förklaringar.

Mer information om hur du kommer igång med Sencha Touch finns den fullständiga uppsättningen [guider](http://docs.sencha.com/touch/#!/guide)


[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- images -->
[0]: ./media/partner-sencha-mobile-services-get-started/finished-app.png

[Klassisk Azure-portal]: https://manage.windowsazure.com/


<!--HONumber=sep16_HO1-->


