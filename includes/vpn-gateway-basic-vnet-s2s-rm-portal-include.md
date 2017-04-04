Följ stegen nedan för att skapa en VNet i Resource Manager-distributionsmodellen med hjälp av Azure Portal. Skärmbilderna anges som exempel. Glöm inte att byta ut värdena mot dina egna. Mer information om hur du arbetar med virtuella nätverk finns i [Översikt över virtuella nätverk](../articles/virtual-network/virtual-networks-overview.md).

1. Öppna en webbläsare, navigera till [Azure Portal](http://portal.azure.com) och logga in med ditt Azure-konto.
2. Klicka på **Ny**. Skriv ”Virtuella nätverk” i fältet **Sök på marketplace**. Leta upp **Virtuellt nätverk** bland sökresultaten och klicka för att öppna bladet **Virtuellt nätverk**.
3. Nästan längst ned på Virtuellt nätverk-bladet, från **Välj en distributionsmodell**-listan, väljer du **Resource Manager** och klickar sedan på **Skapa**.
4. Konfigurera VNet-inställningarna på bladet **Skapa virtuellt nätverk**. När du fyller i fälten blir det röda utropstecknet en grön bock om tecknen som anges i fältet är giltiga.
5. Bladet **Skapa virtuellt nätverk** liknar följande exempel. Det kan finnas värden som fylls i automatiskt. Om detta är fallet ska du ersätta dessa värden med dina egna.
   
    ![Bladet Skapa virtuellt nätverk](./media/vpn-gateway-basic-vnet-s2s-rm-portal-include/createvnet.png "Bladet Skapa virtuellt nätverk")
6. **Namn**: Namnge ditt virtuella nätverk.
7. **Adressutrymme**: Ange adressutrymmet. Om du vill lägga till flera adressutrymme anger du ditt första adressutrymme. Du kan lägga till ytterligare adressutrymmen senare när du har skapat det virtuella nätverket. Kontrollera att adressutrymmet som du anger inte överlappar adressutrymmet för din lokala plats.
8. **Undernätsnamn**: Lägg till undernätsnamn och adressintervall i undernätet. Du kan lägga till ytterligare undernät senare när du har skapat det virtuella nätverket.
9. **Prenumeration**: Verifiera att prenumerationen som visas är korrekt. Du kan ändra prenumerationer i listrutan.
10. **Resursgrupp**: Välj en befintlig resursgrupp eller skapa en ny genom att skriva ett namn för en ny resursgrupp. Om du skapar en ny grupp, bör du kalla resursgruppen efter dina planerade konfigurationsvärden. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
11. **Plats**: Välj plats för ditt VNet. Platsen avgör var resurserna som du distribuerar till detta VNet kommer att placeras.
12. Välj **Fäst vid instrumentpanelen** om du vill kunna hitta ditt VNet på ett enkelt sätt på instrumentpanelen och klicka sedan på **Skapa**.
13. När du klickar på **Skapa** visas en panel i din instrumentpanel som visar förloppet för VNet. Panelen ändras när VNet skapas.