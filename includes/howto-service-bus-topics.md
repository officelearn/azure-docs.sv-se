## Vad är Service Bus-ämnen och -prenumerationer?

Service Bus-ämnen och -prenumerationer stöder en *publicera/prenumerera*-modell för meddelandekommunikation. När du använder ämnen och prenumerationer så kommunicerar inte komponenterna i ett distribuerat program direkt med varandra. Istället så utbyter de meddelanden via ett ämne, som agerar mellanhand.

![TopicConcepts](./media/howto-service-bus-topics/sb-topics-01.png)

Till skillnad från Service Bus-köer, där varje meddelande bearbetas av en enskild konsument så ger ämnen och prenumerationer en ”ett till många”-kommunikation med hjälp av ett publicera/prenumerera-mönster. Det är möjligt att registrera flera prenumerationer för ett ämne. När ett meddelande skickas till ett ämne så görs det tillgängligt för varje prenumeration för oberoende hantering/bearbetning.

En prenumeration på ett ämne liknar en virtuell kö som tar emot kopior av meddelanden som har skickats till ämnet. Alternativt kan du registrera filterregler för ett ämne på prenumerationsbasis, vilket låter dig filtrera eller begränsa vilka meddelanden till ett ämne som tas emot av vilka ämnesprenumerationer.

Service Bus-ämnen och -prenumerationer låter dig skala och bearbeta ett stort antal meddelanden för många användare och program.

## Skapa ett namnområde

För att komma igång med Service Bus-ämnen och -prenumerationer så måste du först skapa ett *namnområde för tjänsten*. Ett namnområde innehåller en omfattningsbehållare för adressering av Service Bus-resurser i ditt program.

Så här skapar du ett namnområde:

1. Logga in på [Azure Portal][].

2. I det vänstra navigationsfältet i portalen klickar du på **Nytt**, på **Enterprise Integration** och sedan på **Service Bus**.

4. I dialogrutan **Skapa namnområde** anger du ett namn för namnområdet. Systemet kontrollerar omedelbart om namnet är tillgängligt.

5. När du har kontrollerat att namnet för namnområdet är tillgängligt, väljer du prisnivå (Basic, Standard eller Premium).

7. I fältet **Prenumeration** väljer du en Azure-prenumeration för vilken du vill skapa namnområdet.

9. I fältet **Resursgrupp** väljer du en befintlig resursgrupp där namnområdet ska finnas eller skapar en ny.      

8. I **Plats** väljer du land eller region där namnområdet ska finnas.

    ![Skapa namnområde][create-namespace]

6. Klicka på knappen **Skapa**. Systemet skapar namnområdet och aktiverar det. Du kan behöva vänta några minuter medan systemet tilldelar resurser till ditt konto.
 
### Hämta autentiseringsuppgifterna

1. I listan över namnområden, klickar du på det nyligen skapade namnområdet.
 
3. På bladet **Service Bus-namnområde** klickar du på **Principer för delad åtkomst**.

4. På bladet **Principer för delad åtkomst** klickar du på **RootManageSharedAccessKey**.

    ![connection-info][connection-info]

5. På bladet **Princip: RootManageSharedAccessKey** klickar du på kopieringsknappen bredvid **Anslutningssträng – Primärnyckel** för att kopiera anslutningssträngen till Urklipp för senare användning.

    ![connection-string][connection-string]

[Azure Portal]: https://portal.azure.com
[create-namespace]: ./media/howto-service-bus-topics/create-namespace.png
[connection-info]: ./media/howto-service-bus-topics/connection-info.png
[connection-string]: ./media/howto-service-bus-topics/connection-string.png




<!--HONumber=Sep16_HO3-->


