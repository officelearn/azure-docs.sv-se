#### <a name="prerequisites"></a>Krav
* Ett Azure-konto; Du kan skapa en [kostnadsfritt konto](https://azure.microsoft.com/free)
* En [Office 365](https://office365.com) konto  

Innan du använder Office 365-konto i en logikapp, auktorisera logik för att ansluta till Office 365-konto. Du kan göra detta enkelt i din logikapp på Azure-portalen.  

Verifiera din logikapp för att ansluta till ditt Office 365-konto med följande steg:

1. Skapa en logikapp. Välj i Logic Apps-designer **visa Microsoft hanterade API: er** i nedrullningsbara listrutan, och ange sedan ”office 365” i sökrutan. Välj något av utlösare eller åtgärder:  
    ![Office 365 steg i att skapa anslutningen](./media/connectors-create-api-office365-outlook/office365-sendemail.png)  
2. Om du inte tidigare har skapat alla anslutningar till Office 365, uppmanas du att logga in med dina inloggningsuppgifter för Office 365:  
    ![Office 365 steg i att skapa anslutningen](./media/connectors-create-api-office365-outlook/office365-signin.png)  
3. Välj **logga in**, och ange ditt användarnamn och lösenord. Välj **logga in**:  
    ![Office 365 steg i att skapa anslutningen](./media/connectors-create-api-office365-outlook/office365-usernamepassword.png)
   
    Dessa autentiseringsuppgifter används för att verifiera din logikapp för att ansluta till och komma åt ditt Office 365-konto. 
4. Observera att anslutningen har skapats. Nu kan fortsätta med andra steg i din logikapp:   
    ![Office 365 steg i att skapa anslutningen](./media/connectors-create-api-office365-outlook/office365-sendemailproperties.png)  

