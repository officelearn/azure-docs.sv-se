Följ stegen nedan för att skapa ett VNet med Azure-portalen. Skärmbilderna anges som exempel. Glöm inte att byta ut värdena mot dina egna. Mer information om hur du arbetar med virtuella nätverk finns i [Översikt över virtuella nätverk](../articles/virtual-network/virtual-networks-overview.md).

1. Navigera till [Azure-portalen](http://portal.azure.com) från en webbläsare och logga in med ditt Azure-konto vid behov.

2. Klicka på **Ny**. I fältet **Sök marknadsplats** skriver du "Virtuella nätverk". Leta upp **Virtuellt nätverk** bland sökresultaten och klicka för att öppna bladet **Virtuellt nätverk**.

    ![Leta upp resursbladet Virtuellt nätverk](./media/vpn-gateway-basic-vnet-rm-portal-include/newvnetportal700.png "Locate virtual network resource blade")

3. Nästan längst ned på Virtuellt nätverk-bladet, från **Välj en distributionsmodell**-listan, väljer du **Resource Manager** och klickar sedan på **Skapa**.


    ![Välj Resource Manager](./media/vpn-gateway-basic-vnet-rm-portal-include/resourcemanager250.png "Select Resource Manager")

4. Konfigurera VNet-inställningarna på bladet **Skapa virtuellt nätverk**. När du fyller i fälten blir det röda utropstecknet en grön bock om tecknen som anges i fältet är giltiga.

    ![Fältvalidering](./media/vpn-gateway-basic-vnet-rm-portal-include/checkmark300.png "Field validation")

5. Bladet **Skapa virtuellt nätverk** liknar följande exempel. Det kan finnas värden som fylls i automatiskt. Om detta är fallet ska du ersätta dessa värden med dina egna.

    ![Skapa ett virtuellt nätverksblad](./media/vpn-gateway-basic-vnet-rm-portal-include/createvnet300.png "Create virtual network blade")

6. **Namn**: Namnge ditt virtuella nätverk.

7. **Adressutrymme**: Ange adressutrymmet. Om du vill lägga till flera adressutrymme anger du ditt första adressutrymme. Du kan lägga till ytterligare adressutrymmen senare när du har skapat det virtuella nätverket.
 
8. **Undernätsnamn**: Lägg till undernätsnamn och adressintervall i undernätet. Du kan lägga till ytterligare undernät senare när du har skapat det virtuella nätverket.

10. **Prenumeration**: Verifiera att prenumerationen som visas är korrekt. Du kan ändra prenumerationer i listrutan.

11. **Resursgrupp**: Välj en befintlig resursgrupp eller skapa en ny genom att skriva ett namn för en ny resursgrupp. Om du skapar en ny grupp, bör du kalla resursgruppen efter dina planerade konfigurationsvärden. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](resource-group-overview.md#resource-groups).

12. **Plats**: Välj plats för ditt VNet. Platsen avgör var resurserna som du distribuerar till detta VNet kommer att placeras.

13. Välj **Fäst vid instrumentpanelen** om du vill kunna hitta ditt VNet på ett enkelt sätt på instrumentpanelen och klicka sedan på **Skapa**.
    
    ![Fäst vid instrumentpanelen](./media/vpn-gateway-basic-vnet-rm-portal-include/pintodashboard150.png "pin to dashboard")

14. När du klickar på **Skapa** visas en panel i din instrumentpanel som visar förloppet för VNet. Panelen ändras när VNet skapas.

    ![Skapa en virtuell nätverksikon](./media/vpn-gateway-basic-vnet-rm-portal-include/deploying150.png "Creating virtual network tile")

<!--HONumber=Oct16_HO1-->


