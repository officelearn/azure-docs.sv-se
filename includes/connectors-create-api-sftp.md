### <a name="prerequisites"></a>Krav
* En [SFTP](https://en.wikipedia.org/wiki/SSH_File_Transfer_Protocol) konto  

Innan du kan använda SFTP-konto i en logikapp, måste du godkänna logik för att ansluta till ditt konto SFTP. Lyckligtvis kan du göra detta direkt i din logikapp på Azure Portal.  

Här följer stegen för att verifiera din logikapp för att ansluta till ditt konto SFTP:  

1. Om du vill skapa en anslutning till SFTP i logik app designer **visa Microsoft hanterade API: er** i nedrullningsbara listan anger *SFTP* i sökrutan. Välj den **SFTP - när en fil har lagts till eller ändrats** utlösare:  
   ![SFTP online bild 1](./media/connectors-create-api-sftp/sftp-1.png)  
2. Om du inte har skapat alla anslutningar till SFTP innan du kan hämta uppmanas du att ange autentiseringsuppgifter för SFTP. Dessa autentiseringsuppgifter används för att auktorisera din logikapp för att ansluta till och komma åt ditt konto SFTP data:  
   ![SFTP online bild 2](./media/connectors-create-api-sftp/sftp-2.png)  
3. Observera att anslutningen har skapats och du kan nu välja att fortsätta med andra steg i din logikapp:   
   ![SFTP online bild 3](./media/connectors-create-api-sftp/sftp-3.png) 

