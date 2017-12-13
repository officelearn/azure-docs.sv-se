## <a name="create-a-storage-account-using-the-azure-portal"></a>Skapa ett lagringskonto med Azure Portal

Skapa först ett allmänt lagringskonto som du kan använda i denna snabbstart. 

1. Gå till [Azure-portalen](https://portal.azure.com) och logga in med ditt Azure-konto. 
2. På navmenyn väljer du **Nytt** > **Lagring** > **Lagringskonto – blob, fil, tabell, kö**. 
3. Ange ett unikt namn för lagringskontot. Tänk på dessa regler när du namnger lagringskontot:
    - Namnet måste innehålla mellan 3 och 24 tecken.
    - Namnet får endast innehålla siffror och gemener.
4. Kontrollera att följande standardvärden konfigureras: 
    - **Distributionsmodell** är inställt på **Resource Manager**.
    - **Typ av konto** är inställt på **Allmänt**.
    - **Prestanda** är inställt på **Standard**.
    - **Replikering** är inställt på **Lokalt redundant lagring (LRS)**.
5. Välj din prenumeration. 
6. Skapa en ny **Resursgrupp** och ge den ett unikt namn. 
7. Välj **Plats** som ska användas för lagringskontot.
8. Markera **Fäst på instrumentpanelen** och klicka på **Skapa** för att skapa lagringskontot. 

När lagringskontot har skapats fästs det på instrumentpanelen. Klicka på det för att öppna det. Under **Inställningar** klickar du på **Åtkomstnycklar**. Välj den primära nyckeln och kopiera associerad **Anslutningssträng** till Urklipp och klistra sedan in den i en textredigerare så att du kan använda den senare.
