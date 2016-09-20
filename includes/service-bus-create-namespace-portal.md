1. Logga in på [Azure Portal][].

2. I det vänstra navigationsfältet i portalen klickar du på **Nytt**, på **Enterprise Integration** och sedan på **Service Bus**.

4. I dialogrutan **Skapa namnområde** anger du ett namn för namnområdet. Systemet kontrollerar omedelbart om namnet är tillgängligt.

5. När du har kontrollerat att namnet för namnområdet är tillgängligt, väljer du prisnivå (Basic, Standard eller Premium).

7. I fältet **Prenumeration** väljer du en Azure-prenumeration för vilken du vill skapa namnområdet.

9. I fältet **Resursgrupp** väljer du en befintlig resursgrupp där namnområdet ska finnas eller skapar en ny.      

8. I **Plats** väljer du land eller region där namnområdet ska finnas.

    ![Skapa namnområde][create-namespace]

6. Klicka på knappen **Skapa**. Systemet skapar namnområdet och aktiverar det. Du kan behöva vänta några minuter medan systemet tilldelar resurser till ditt konto.
 
### Hämta autentiseringsuppgifterna för hantering

1. I listan över namnområden, klickar du på det nyligen skapade namnområdet.
 
3. På bladet **Service Bus-namnområde** klickar du på **Principer för delad åtkomst**.

4. På bladet **Principer för delad åtkomst** klickar du på **RootManageSharedAccessKey**.

    ![connection-info][connection-info]

5. På bladet **Princip: RootManageSharedAccessKey** klickar du på kopieringsknappen bredvid **Anslutningssträng – Primärnyckel** för att kopiera anslutningssträngen till Urklipp för senare användning.

    ![connection-string][connection-string]

<!--Image references-->

[create-namespace]: ./media/service-bus-create-namespace-portal/create-namespace.png
[connection-info]: ./media/service-bus-create-namespace-portal/connection-info.png
[connection-string]: ./media/service-bus-create-namespace-portal/connection-string.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[Azure Portal]: https://portal.azure.com


<!--HONumber=sep16_HO1-->


