### <a name="prerequisites"></a>Förutsättningar
* Ett Twilio-konto
* Ett verifierade Twilio-telefonnummer som kan ta emot SMS
* Ett verifierade Twilio-telefonnummer som kan skicka SMS

> [!NOTE]
> Om du använder en utvärderingsversion Twilio-konto kan du bara skicka SMS till **verifieras** telefonnummer.  
> 
> 

Innan du kan använda ditt Twilio-konto i en logikapp, måste du godkänna logik för att ansluta till ditt Twilio-konto. Lyckligtvis kan du göra detta direkt i din logikapp på Azure Portal. 

Här följer stegen för att verifiera din logikapp för att ansluta till ditt Twilio-konto:

1. Om du vill skapa en anslutning till Twilio, i logik app designer **visa Microsoft hanterade API: er** i nedrullningsbara listan anger *Twilio* i sökrutan. Välj utlösaren eller åtgärd du vill använda:  
   ![](./media/connectors-create-api-twilio/twilio-0.png)
2. Om du inte har skapat alla anslutningar till Twilio innan du kan hämta uppmanas du att ange Twilio-autentiseringsuppgifter. Dessa autentiseringsuppgifter används för att auktorisera din logikapp för att ansluta till och komma åt data i ditt Twilio-konto:  
   ![](./media/connectors-create-api-twilio/twilio-1.png)  
3. Du behöver den **Twilio-konto-id** och **Twilio åtkomsttoken** från instrumentpanelen i Twilio, så logga in på ditt Twilio-konto nu att hämta dessa två typer av information:  
   ![](./media/connectors-create-api-twilio/twilio-2.png)  
4. Twilio och Logic apps använda olika namn för att identifiera dessa två typer av information. Här är hur måste du mappa dem till dialogrutan Logic apps: ![](./media/connectors-create-api-twilio/twilio-3.png)  
5. Välj den **Skapa anslutning** knappen:  
   ![](./media/connectors-create-api-twilio/twilio-4.png)
6. Observera att anslutningen har skapats och du kan nu välja att fortsätta med andra steg i din logikapp:  
   ![](./media/connectors-create-api-twilio/twilio-5.png)

