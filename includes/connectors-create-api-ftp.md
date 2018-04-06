### <a name="prerequisites"></a>Förutsättningar
* En [FTP](https://wikipedia.org/wiki/File_Transfer_Protocol) konto  

Innan du kan använda din FTP-konto i en logikapp, måste du godkänna logik för att ansluta till din FTP-konto. Lyckligtvis kan du göra detta direkt i din logikapp på Azure Portal.  

Här följer stegen för att verifiera din logikapp för att ansluta till din FTP-konto:  

1. Om du vill skapa en anslutning till FTP, i logik app designer **visa Microsoft hanterade API: er** i nedrullningsbara listan anger *FTP* i sökrutan. Välj utlösaren eller åtgärd du vill använda:  
   ![FTP-anslutning, skapa steg](./media/connectors-create-api-ftp/ftp-1.png)  
2. Om du inte har skapat alla anslutningar till FTP innan du kan hämta uppmanas du att ange autentiseringsuppgifter för FTP. Dessa autentiseringsuppgifter används för att auktorisera din logikapp för att ansluta till och komma åt data i din FTP-konto:  
   ![FTP-anslutning, skapa steg](./media/connectors-create-api-ftp/ftp-2.png)  
3. Observera att anslutningen har skapats och du kan nu välja att fortsätta med andra steg i din logikapp:  
   ![FTP-anslutning, skapa steg](./media/connectors-create-api-ftp/ftp-3.png)  

