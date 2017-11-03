## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Så här skapar du ett klassiskt virtuellt nätverk i Azure-portalen
Följ stegen nedan om du vill skapa ett klassiskt virtuellt nätverk baserat på scenariot ovan.

1. Från en webbläsare, navigerar du till http://portal.azure.com och loggar, vid behov, in med ditt Azure-konto.
2. Klicka på **ny** > **nätverk** > **för virtuella nätverk**, Lägg märke till att den **Välj en distributionsmodell** Visa en lista över redan visar **klassiska**, och klicka sedan på **skapa**, som visas i bilden nedan.
   
    ![Skapa VNet i Azure-portalen](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
3. På den **för virtuella nätverk** bladet, skriver den **namn** VNet och klicka sedan på **adressutrymmet**. Konfigurera inställningar för det virtuella nätverket och det första undernätet din adressutrymme och klicka sedan på **OK**. Bilden nedan visar CIDR block-inställningarna för vårt scenario.
   
    ![Adressutrymme-bladet](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
4. Klicka på **resursgruppen** och välja en resursgrupp att lägga till VNet eller klicka på **Skapa ny resursgrupp** att lägga till det virtuella nätverket till en ny resursgrupp. Bilden nedan visar resursgruppsinställningarna för en ny resursgrupp med namnet **TestRG**. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
   
    ![Skapa blad för resursgrupp](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
5. Ändra vid behov inställningarna för **Prenumeration** och **Plats** för din VNet. 
6. Om du inte vill se VNet som en ikon på **Startsidan**, inaktiverar du **Fäst på startsidan**. 
7. Klicka på **Skapa** och notera ikonen som heter **Skapar virtuellt nätverk** som visas i bilden nedan.
   
    ![Skapa VNet i portalen](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
8. Vänta tills VNet som ska skapas och när du ser nedan sida vid sida, klickar du på den för att lägga till flera undernät.
   
    ![Skapa VNet i portalen](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
9. Du bör se den **Configuration** för din VNet som visas nedan. 
   
    ![Skapa VNet i portalen](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
10. Klicka på **undernät** > **Lägg till**, Skriv en **namn** och ange en **adressintervall (CIDR-block)** din undernät, och sedan Klicka på **OK**. Bilden nedan visar inställningarna för våra aktuella scenariot.
    
    ![Skapa VNet i Azure-portalen](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

