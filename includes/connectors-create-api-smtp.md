### <a name="prerequisites"></a>Krav
* En [SMTP](https://wikipedia.org/wiki/Simple_Mail_Transfer_Protocol) konto  

Innan du kan använda SMTP-kontot i en logikapp, måste du godkänna logik för att ansluta till SMTP-kontot. Lyckligtvis kan du göra detta direkt i din logikapp på Azure Portal.  

Här följer stegen för att verifiera din logikapp för att ansluta till SMTP-kontot:  

1. Om du vill skapa en anslutning till SMTP i logik app designer **visa Microsoft hanterade API: er** i nedrullningsbara listan anger *SMTP* i sökrutan. Välj utlösaren eller åtgärd du vill använda:  
   ![](./media/connectors-create-api-smtp/smtp-1.png)  
2. Om du inte har skapat alla anslutningar till SMTP innan du kan hämta uppmanas du att ange SMTP-autentiseringsuppgifter. Dessa autentiseringsuppgifter används för att auktorisera din logikapp för att ansluta till och få åtkomst till SMTP-kontot data:  
   ![](./media/connectors-create-api-smtp/smtp-2.png)  
3. Observera att anslutningen har skapats och du kan nu välja att fortsätta med andra steg i din logikapp:  
   ![](./media/connectors-create-api-smtp/smtp-3.png)  

