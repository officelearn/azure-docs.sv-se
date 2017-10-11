## <a name="connect-to-outlookcom"></a>Ansluta till Outlook.com
### <a name="prerequisites"></a>Krav
* En Outlook.com-konto

Innan du kan använda din Outlook.com-konto i en logikapp, måste du godkänna logik för att ansluta till din Outlook.com-konto. Lyckligtvis kan du göra detta direkt i din logikapp på Azure Portal. 

Här följer stegen för att verifiera din logikapp för att ansluta till din Outlook.com-konto:

1. Alla Logic apps behöver startas av en utlösare så när du skapar din logikapp, designer öppnas och visar en lista över utlöser som du kan använda för att starta din logikapp:
   
   ![](./media/connectors-create-api-outlook/office365-outlook-0.png)
2. Ange ”outlook” i sökrutan. Lägg märke till listan filtreras för att visa en lista med alla utlösare med ”Outlook” i namnet:![](./media/connectors-create-api-outlook/office365-outlook-0-5.png)
3. Välj **Office 365 Outlook - på nytt e-postmeddelande**.   
   Om du inte har skapat alla anslutningar till Outlook innan du kan hämta uppmanas du att ange autentiseringsuppgifter för Outlook.com. Dessa autentiseringsuppgifter används för att auktorisera din logikapp för att ansluta till och komma åt data i din Outlook.com-konto:![](./media/connectors-create-api-outlook/office365-outlook-1.png)
4. Ange dina autentiseringsuppgifter för Outlook och logga in:![](./media/connectors-create-api-outlook/office365-outlook-2.png)  
   Det var allt. Du har nu skapat en anslutning till Outlook. Den här anslutningen ska vara tillgängligt för användning i andra logikappen som du skapar.

