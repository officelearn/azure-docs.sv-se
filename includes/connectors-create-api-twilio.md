### <a name="prerequisites"></a>Förutsättningar
* Ett Twilio-konto
* En verifierad Twilio-telefonnummer som kan ta emot SMS
* En verifierad Twilio-telefonnummer som kan skicka SMS

> [!NOTE]
> Om du använder ett Twilio konto, kan du endast skicka SMS till **verifierat** telefonnummer.  
> 
> 

Innan du kan använda Twilio-konto i en logikapp, måste du godkänna logikappen som ska ansluta till ditt Twilio-konto. Som tur är kan göra du det enkelt från i logikappen på Azure Portal. 

Här följer stegen för att auktorisera din logikapp för att ansluta till ditt Twilio-konto:

1. Om du vill skapa en anslutning till Twilio, i Logic appdesigner väljer **visa Microsoft hanterade API: er** i nedrullningsbara listan anger *Twilio* i sökrutan. Välj utlösaren eller åtgärden som du kommer att tycka att använda:  
   ![](./media/connectors-create-api-twilio/twilio-0.png)
2. Om du inte skapat några anslutningar till Twilio innan du kan hämta uppmanas du ange dina Twilio-autentiseringsuppgifter. Dessa autentiseringsuppgifter används för att auktorisera din logikapp för att ansluta till och komma åt data i ditt Twilio-konto:  
   ![](./media/connectors-create-api-twilio/twilio-1.png)  
3. Du behöver den **Twilio konto-id** och **Twilio-åtkomsttoken** från instrumentpanelen i Twilio, så logga in på ditt Twilio-konto nu att hämta dessa två typer av information:  
   ![](./media/connectors-create-api-twilio/twilio-2.png)  
4. Twilio och Logic apps använder olika namn för att identifiera dessa två typer av information. Här är hur måste du mappa den till Logic apps dialogrutan: ![](./media/connectors-create-api-twilio/twilio-3.png)  
5. Välj den **Skapa anslutning** knappen:  
   ![](./media/connectors-create-api-twilio/twilio-4.png)
6. Lägg märke till anslutningen har skapats och du kan nu fortsätta med andra steg i logikappen:  
   ![](./media/connectors-create-api-twilio/twilio-5.png)

