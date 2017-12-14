För att komma igång med Service Bus-meddelandeentiteter i Azure måste du först skapa ett namnområde med ett namn som är unikt i Azure. Ett namnområde innehåller en omfattningsbehållare för adressering av Service Bus-resurser i ditt program.

Så här skapar du ett namnområde:

1. Logga in på [Azure portal][Azure portal].
2. I det vänstra navigeringsfönstret i portalen klickar du på **+ skapa en resurs**, klicka på **Enterprise Integration**, och klicka sedan på **Service Bus**.
3. I dialogrutan **Skapa namnområde** anger du ett namn för namnområdet. Systemet kontrollerar omedelbart om namnet är tillgängligt.
4. När du har kontrollerat att namnet för namnområdet är tillgängligt, väljer du prisnivå (Basic, Standard eller Premium).
5. I fältet **Prenumeration** väljer du en Azure-prenumeration för vilken du vill skapa namnområdet.
6. I fältet **Resursgrupp** väljer du en befintlig resursgrupp där namnområdet ska finnas eller skapar en ny.      
7. I **Plats** väljer du land eller region där namnområdet ska finnas.
   
    ![Skapa namnområde][create-namespace]
8. Klicka på **Skapa**. Systemet skapar namnområdet och aktiverar det. Du kan behöva vänta några minuter medan systemet tilldelar resurser till ditt konto.

### <a name="obtain-the-management-credentials"></a>Hämta autentiseringsuppgifterna för hantering
Skapa ett nytt namnområde automatiskt genererar en inledande regel för delad åtkomst signatur (SAS) med en associerad par av primära och sekundära nycklarna varje bevilja fullständig kontroll över alla aspekter av namnområdet. Se [Service Bus-autentisering och auktorisering](../articles/service-bus-messaging/service-bus-authentication-and-authorization.md) information om hur du skapar ytterligare regler med mer begränsad behörighet för vanliga avsändare och mottagare. Gör så här om du vill kopiera den ursprungliga regeln: 

1. I listan över namnområden, klickar du på det nyligen skapade namnområdet.
2. I fönstret namnområde klickar du på **principer för delad åtkomst**.
3. I den **principer för delad åtkomst** klickar du på **RootManageSharedAccessKey**.
   
    ![connection-info][connection-info]
4. I den **princip: RootManageSharedAccessKey** kan du klicka på Kopiera knappen Nästa för att **sträng – primära anslutningsnyckel**, för att kopiera anslutningssträngen till Urklipp för senare användning. Klistra in det här värdet i Anteckningar eller på en tillfällig plats.
   
    ![connection-string][connection-string]

5. Upprepa föregående steg, kopiera och klistra in värdet för **primärnyckeln** till en tillfällig plats för senare användning.

<!--Image references-->

[create-namespace]: ./media/service-bus-create-namespace-portal/create-namespace.png
[connection-info]: ./media/service-bus-create-namespace-portal/connection-info.png
[connection-string]: ./media/service-bus-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
