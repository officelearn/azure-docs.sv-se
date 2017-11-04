#### <a name="prerequisites"></a>Krav
* Ett Azure-konto; Du kan skapa en [kostnadsfritt konto](https://azure.microsoft.com/free)
* En [Dynamics CRM Online](https://www.microsoft.com/en-us/dynamics/crm-free-trial-overview.aspx) konto 

Innan du använder Dynamics-konto i en logikapp, auktorisera logik för att ansluta till din CRM Online-konto. Du kan göra detta enkelt i din logikapp på Azure-portalen. 

Verifiera din logikapp för att ansluta till din CRM Online-konto med följande steg:

1. Skapa en logikapp. Välj i Logic Apps-designer **visa Microsoft hanterade API: er** i nedrullningsbara listrutan, och ange sedan ”dynamics” i sökrutan. Välj något av utlösare eller åtgärder:  
   ![](./media/connectors-create-api-crmonline/dynamics-triggers.png)
2. Om du inte tidigare har skapat alla anslutningar till Dynamics, uppmanas du att logga in med dina inloggningsuppgifter för Dynamics:  
   ![](./media/connectors-create-api-crmonline/dynamics-signin.png)
3. Välj **logga in**, och ange ditt användarnamn och lösenord. Välj **logga in**. 
   
    Dessa autentiseringsuppgifter används för att auktorisera din logikapp för att ansluta till och komma åt data i din Dynamics-konto. 
4. Observera att anslutningen har skapats. Nu kan fortsätta med andra steg i din logikapp:  
   ![](./media/connectors-create-api-crmonline/dynamics-properties.png)

