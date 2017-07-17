Följ stegen nedan för att skapa en VNet i Resource Manager-distributionsmodellen med hjälp av Azure Portal. Använd [exempelvärden](#values) om du använder de här stegen som en vägledning. Om du inte genomför de här stegen som en vägledning, bör du ersätter värdena med dina egna. Mer information om hur du arbetar med virtuella nätverk finns i [Översikt över virtuella nätverk](../articles/virtual-network/virtual-networks-overview.md).

1. Öppna en webbläsare, navigera till [Azure Portal](http://portal.azure.com) och logga in med ditt Azure-konto.
2. Klicka på **Ny**. Skriv ”Virtuella nätverk” i fältet **Sök på marketplace**. Leta upp **Virtuellt nätverk** bland sökresultaten och klicka för att öppna bladet **Virtuellt nätverk**.
3. Nästan längst ned på Virtuellt nätverk-bladet, från **Välj en distributionsmodell**-listan, väljer du **Resource Manager** och klickar sedan på **Skapa**. Detta öppnar bladet ”Skapa virtuell nätverksgateway”.

    ![Bladet Skapa virtuellt nätverk](./media/vpn-gateway-basic-vnet-s2s-rm-portal-include/createvnet.png "Bladet Skapa virtuellt nätverk")
4. Konfigurera VNet-inställningarna på bladet **Skapa virtuellt nätverk**. När du fyller i fälten blir det röda utropstecknet en grön bock om tecknen som anges i fältet är giltiga.

  - **Namn**: Namnge ditt virtuella nätverk. I det här exemplet använder vi TestVNet1.
  - **Adressutrymme**: Ange adressutrymmet. Om du vill lägga till flera adressutrymme anger du ditt första adressutrymme. Du kan lägga till ytterligare adressutrymmen senare när du har skapat det virtuella nätverket. Kontrollera att adressutrymmet som du anger inte överlappar adressutrymmet för din lokala plats.
  - **Undernätsnamn**: Lägg till det första undernätsnamnet och adressintervall för undernätet. Du kan lägga till ytterligare undernät och gateway-undernätet senare när du har skapat detta virtuella nätverk. 
  - **Prenumeration**: Verifiera att prenumerationen som visas är korrekt. Du kan ändra prenumerationer i listrutan.
  - **Resursgrupp**: Välj en befintlig resursgrupp eller skapa en ny genom att skriva ett namn för en ny resursgrupp. Om du skapar en ny grupp, bör du kalla resursgruppen efter dina planerade konfigurationsvärden. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
  - **Plats**: Välj plats för ditt VNet. Platsen avgör var resurserna som du distribuerar till detta VNet kommer att placeras.

5. Välj **Fäst vid instrumentpanelen** om du vill kunna hitta ditt VNet på ett enkelt sätt på instrumentpanelen och klicka sedan på **Skapa**. När du klickar på **Skapa** visas en panel i din instrumentpanel som visar förloppet för VNet. Panelen ändras när VNet skapas.