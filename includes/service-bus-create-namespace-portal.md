## <a name="create-a-service-namespace"></a>Skapa ett namnområde för tjänsten

För att komma igång med Service Bus-köer i Azure måste du först skapa ett namnområde. Ett namnområde innehåller en omfattningsbehållare för adressering av Service Bus-resurser i ditt program. 

Så här skapar du ett namnområde:

1. Logga in på [Azure portal][Azure portal].
2. I det vänstra navigationsfältet i portalen klickar du på **Nytt**, på **Enterprise Integration** och sedan på **Service Bus**.
3. I dialogrutan **Skapa namnområde** anger du ett namn för namnområdet. Systemet kontrollerar omedelbart om namnet är tillgängligt.
4. När du har kontrollerat att namnet för namnområdet är tillgängligt, väljer du prisnivå (Basic, Standard eller Premium).
5. I fältet **Prenumeration** väljer du en Azure-prenumeration för vilken du vill skapa namnområdet.
6. I fältet **Resursgrupp** väljer du en befintlig resursgrupp där namnområdet ska finnas eller skapar en ny.      
7. I **Plats** väljer du land eller region där namnområdet ska finnas.
   
    ![Skapa namnområde][create-namespace]
8. Klicka på **Skapa**. Systemet skapar namnområdet och aktiverar det. Du kan behöva vänta några minuter medan systemet tilldelar resurser till ditt konto.

### <a name="obtain-the-management-credentials"></a>Hämta autentiseringsuppgifterna för hantering
1. I listan över namnområden, klickar du på det nyligen skapade namnområdet.
2. På namnområdesbladet klickar du på **Principer för delad åtkomst**.
3. På bladet **Principer för delad åtkomst** klickar du på **RootManageSharedAccessKey**.
   
    ![connection-info][connection-info]
4. På bladet **Princip: RootManageSharedAccessKey** klickar du på kopieringsknappen bredvid **Anslutningssträng – Primärnyckel** för att kopiera anslutningssträngen till Urklipp för senare användning. Klistra in det här värdet i Anteckningar eller på en tillfällig plats.
   
    ![connection-string][connection-string]

5. Upprepa föregående steg, kopiera och klistra in värdet för **primärnyckeln** till en tillfällig plats för senare användning.

<!--Image references-->

[create-namespace]: ./media/service-bus-create-namespace-portal/create-namespace.png
[connection-info]: ./media/service-bus-create-namespace-portal/connection-info.png
[connection-string]: ./media/service-bus-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com