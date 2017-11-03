#### <a name="prerequisites"></a>Krav
* Ett Azure-konto; Du kan skapa en [kostnadsfritt konto](https://azure.microsoft.com/free)
* En [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) konto 

Innan du kan använda ditt OneDrive-konto i en logikapp, auktorisera logik för att ansluta till ditt OneDrive-konto.  Du kan göra detta enkelt i din logikapp på Azure-portalen. 

Verifiera din logikapp för att ansluta till ditt OneDrive-konto med följande steg:

1. Skapa en logikapp. Välj i Logic Apps-designer **visa Microsoft hanterade API: er** i nedrullningsbara listrutan, och ange sedan ”onedrive” i sökrutan. Välj något av utlösare eller åtgärder:  
   ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Om du inte har skapat alla anslutningar till OneDrive uppmanas du att logga in med dina inloggningsuppgifter för OneDrive:  
   ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Välj **logga in**, och ange ditt användarnamn och lösenord. Välj **logga in**:  
   ![](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Dessa autentiseringsuppgifter används för att verifiera din logikapp för att ansluta till och komma åt data i ditt OneDrive-konto. 
4. Välj **Ja** att auktorisera logikappen att använda OneDrive-konto:  
   ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Observera att anslutningen har skapats. Nu kan fortsätta med andra steg i din logikapp:  
   ![](./media/connectors-create-api-onedrive/onedrive-5.png)

