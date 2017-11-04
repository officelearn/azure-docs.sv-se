### <a name="prerequisites"></a>Krav
* En [SendGrid](https://www.SendGrid.com/) konto 

Innan du kan använda ditt konto SendGrid i en logikapp, måste du godkänna logik för att ansluta till SendGrid-konto. Lyckligtvis kan du göra detta direkt i din logikapp på Azure Portal. 

Här följer stegen för att auktorisera din logikapp för att ansluta till SendGrid-konto:

1. Om du vill skapa en anslutning till SendGrid, i logik app designer **visa Microsoft hanterade API: er** i nedrullningsbara listan anger *SendGrid* i sökrutan. Välj utlösaren eller åtgärd du vill använda:  
   ![SendGrid steg 1](./media/connectors-create-api-sendgrid/sendgrid-1.png)
2. Om du inte har skapat alla anslutningar till SendGrid innan du kan hämta uppmanas du att ange dina inloggningsuppgifter. Dessa autentiseringsuppgifter används för att auktorisera din logikapp för att ansluta till och komma åt ditt konto SendGrid data:  
   ![SendGrid steg 2](./media/connectors-create-api-sendgrid/sendgrid-2.png)
3. Observera att anslutningen har skapats och du kan nu välja att fortsätta med andra steg i din logikapp:  
   ![SendGrid steg 3](./media/connectors-create-api-sendgrid/sendgrid-3.png)   

