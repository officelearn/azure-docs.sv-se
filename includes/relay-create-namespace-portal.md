1. Logga in på [Azure portal][Azure portal].
2. I det vänstra navigeringsfältet i portalen klickar du på **+Skapa en resurs**, sedan **Enterprise-integration** och sedan på **Relay**.
3. I dialogrutan **Skapa namnområde** anger du ett namn för namnområdet. Systemet kontrollerar omedelbart om namnet är tillgängligt.
4. I fältet **Prenumeration** väljer du en Azure-prenumeration för vilken du vill skapa namnområdet.
5. I fältet **[Resursgrupp](../articles/azure-resource-manager/resource-group-portal.md)** väljer du en befintlig resursgrupp där namnområdet ska finnas eller skapar en ny.      
6. I **Plats** väljer du land eller region där namnområdet ska finnas.
   
    ![Skapa namnområde][create-namespace]
7. Klicka på **Skapa**. Systemet skapar namnområdet och aktiverar det. Efter ett par minuter etableras resurser för ditt konto i systemet.

### <a name="obtain-the-management-credentials"></a>Hämta autentiseringsuppgifterna för hantering

1. Klicka på **Alla resurser**, sedan klickar du på den nyligen skapade namnrymden.
2. I Relay-namnrymdsfönstret klickar du på **Principer för delad åtkomst**.
3. I fönstret **Principer för delad åtkomst** klickar du på **RootManageSharedAccessKey**.
   
    ![connection-info][connection-info]
4. På skärmen **Princip: RootManageSharedAccessKey** klickar du på knappen **Kopiera** bredvid **Anslutningssträng – Primärnyckel** för att kopiera anslutningssträngen till Urklipp för senare användning. Klistra in det här värdet i Anteckningar eller på en tillfällig plats.
   
    ![connection-string][connection-string]

5. Upprepa föregående steg, kopiera och klistra in värdet för **primärnyckeln** till en tillfällig plats för senare användning.  

<!--Image references-->

[create-namespace]: ./media/relay-create-namespace-portal/create-namespace.png
[connection-info]: ./media/relay-create-namespace-portal/connection-info.png
[connection-string]: ./media/relay-create-namespace-portal/connection-string.png
[Azure portal]: https://portal.azure.com
