## <a name="what-are-service-bus-topics-and-subscriptions"></a>Vad är Service Bus-ämnen och -prenumerationer?
Service Bus-ämnen och -prenumerationer stöder en *publicera/prenumerera*-modell för meddelandekommunikation. När du använder ämnen och prenumerationer så kommunicerar inte komponenterna i ett distribuerat program direkt med varandra. Istället så utbyter de meddelanden via ett ämne, som agerar mellanhand.

![TopicConcepts](./media/howto-service-bus-topics/sb-topics-01.png)

Till skillnad från Service Bus-köer, där varje meddelande bearbetas av en enskild konsument ger ämnen och prenumerationer en ”en-till-många”-kommunikation med hjälp av ett Publicera/prenumerera-mönster. Det är möjligt att registrera flera prenumerationer för ett ämne. När ett meddelande skickas till ett ämne så görs det tillgängligt för varje prenumeration för oberoende hantering/bearbetning.

En prenumeration på ett ämne liknar en virtuell kö som tar emot kopior av meddelanden som har skickats till ämnet. Alternativt kan du registrera filterregler för ett ämne på grundval av per prenumeration. Filterregler kan du filtrera eller begränsa vilka meddelanden till ett ämne tas emot av vilka ämnesprenumerationer.

Service Bus-ämnen och prenumerationer kan du skala och bearbetar ett stort antal meddelanden för många användare och program.

## <a name="create-a-namespace"></a>Skapa ett namnområde
För att komma igång med Service Bus-ämnen och -prenumerationer så måste du först skapa ett *namnområde för tjänsten*. Ett namnområde innehåller en omfattningsbehållare för adressering av Service Bus-resurser i ditt program.

Så här skapar du ett namnområde:

1. Logga in på [Azure Portal][Azure portal].
2. I det vänstra navigeringsfönstret i portalen klickar du på **skapar du en resurs**, klicka på **Enterprise Integration**, och klicka sedan på **Service Bus**.
3. I dialogrutan **Skapa namnområde** anger du ett namn för namnområdet. Systemet kontrollerar omedelbart om namnet är tillgängligt.
4. När du har kontrollerat att namnet för namnområdet är tillgängligt, väljer du prisnivå (Basic, Standard eller Premium).
5. I fältet **Prenumeration** väljer du en Azure-prenumeration för vilken du vill skapa namnområdet.
6. I den **resursgruppen** fält, Välj en befintlig resursgrupp där namnområdet finns eller skapa en ny.      
7. I **Plats** väljer du land eller region där namnområdet ska finnas.
   
    ![Skapa namnområde][create-namespace]
8. Klicka på knappen **Skapa**. Systemet skapar namnområdet och aktiverar det. Du kan behöva vänta några minuter medan systemet tilldelar resurser till ditt konto.

### <a name="obtain-the-credentials"></a>Hämta autentiseringsuppgifterna
1. I listan över namnområden, klickar du på det nyligen skapade namnområdet.
2. I den **Service Bus-namnrymd** rutan klickar du på **principer för delad åtkomst**.
3. I den **principer för delad åtkomst** rutan klickar du på **RootManageSharedAccessKey**.
   
    ![connection-info][connection-info]
4. I den **princip: RootManageSharedAccessKey** klickar du på kopian knappen Nästa för att **sträng – primära anslutningsnyckel**, för att kopiera anslutningssträngen till Urklipp för senare användning.
   
    ![connection-string][connection-string]

[Azure portal]: https://portal.azure.com
[create-namespace]: ./media/howto-service-bus-topics/create-namespace.png
[connection-info]: ./media/howto-service-bus-topics/connection-info.png
[connection-string]: ./media/howto-service-bus-topics/connection-string.png


