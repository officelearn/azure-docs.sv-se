## <a name="create-a-storage-account-by-using-the-azure-portal"></a>Skapa ett lagringskonto med hjälp av Azure portal

Skapa först ett allmänt lagringskonto som du kan använda i denna snabbstart. 

1. Gå till den [Azure-portalen](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM) och logga in med ditt Azure-konto. 
2. Ange ett unikt namn för lagringskontot. Tänk på dessa regler när du namnger lagringskontot:
    - Namnet måste vara 3 till 24 tecken.
    - Namnet får innehålla siffror och gemener.
3. Kontrollera att följande standardvärden konfigureras: 
    - **Distributionsmodell** är inställd på **Resource Manager**.
    - **Typ av konto** är inställt på **Allmänt**.
    - **Prestanda** är inställt på **Standard**.
    - **Replikering** är inställt på **Lokalt redundant lagring (LRS)**.
4. Välj din prenumeration. 
5. Skapa en ny **Resursgrupp** och ge den ett unikt namn. 
6. Välj platsen som ska användas för ditt lagringskonto.
7. Välj **fäst på instrumentpanelen** och välj **skapa** att skapa ditt lagringskonto. 

När ditt lagringskonto har skapats kan den är fäst på instrumentpanelen. Välj den så att den öppnas. Under **inställningar**väljer **åtkomstnycklar**. Välj den primära nyckeln och kopiera associerade anslutningssträngen till Urklipp. Klistra in strängen i en textredigerare för senare användning.
