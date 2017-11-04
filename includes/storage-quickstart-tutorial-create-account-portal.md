## <a name="create-a-storage-account-using-the-azure-portal"></a>Skapa ett lagringskonto med hjälp av Azure portal

Först skapa Allmänt lagringskonto ska användas för denna Snabbstart. 

1. Gå till den [Azure-portalen](https://portal.azure.com) och logga in med ditt Azure-konto. 
2. På navmenyn väljer **ny** > **lagring** > **lagringskonto - blob, fil, tabell, kö**. 
3. Ange ett unikt namn för ditt lagringskonto. Tänk dessa regler för namngivning av ditt lagringskonto:
    - Namnet måste vara mellan 3 och 24 tecken.
    - Namnet får innehålla siffror och gemener.
4. Kontrollera att följande standardvärden anges: 
    - **Distributionsmodell** är inställd på **Resource manager**.
    - **Kontot kind** är inställd på **generella**.
    - **Prestanda** är inställd på **Standard**.
    - **Replikering** är inställd på **lokalt Redundant lagring (LRS)**.
5. Välj din prenumeration. 
6. För **resursgruppen**, skapa en ny och ge den ett unikt namn. 
7. Välj den **plats** ska användas för ditt lagringskonto.
8. Kontrollera **fäst på instrumentpanelen** och på **skapa** att skapa ditt lagringskonto. 

När ditt lagringskonto har skapats kan är den fäst på instrumentpanelen. Klicka på den för att öppna den. Under **inställningar**, klickar du på **åtkomstnycklar**. Välj den primära nyckeln och kopiera den associerade **anslutningssträngen** till Urklipp, klistra in den i en textredigerare för senare användning.
