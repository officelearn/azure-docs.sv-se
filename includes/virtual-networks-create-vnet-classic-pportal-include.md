## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Så här skapar du ett klassiskt virtuellt nätverk i Azure-portalen
Följ dessa steg om du vill skapa ett klassiskt virtuellt nätverk baserat på föregående scenariot.

1. Från en webbläsare, navigerar du till http://portal.azure.com och loggar, vid behov, in med ditt Azure-konto.
2. Klicka på **skapar du en resurs** > **nätverk** > **för virtuella nätverk**. Observera att den **Välj en distributionsmodell** redan lista visas **klassiska**. 3. Klicka på **skapa** som visas i följande bild.
   
    ![Skapa VNet i Azure-portalen](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
4. På den **för virtuella nätverk** rutan, skriver den **namn** VNet och klicka sedan på **adressutrymmet**. Konfigurera inställningar för det virtuella nätverket och det första undernätet din adressutrymme och klicka sedan på **OK**. Följande bild visar CIDR block-inställningarna för vårt scenario.
   
    ![Adressutrymme fönstret](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
5. Klicka på **resursgruppen** och välja en resursgrupp att lägga till VNet eller klicka på **Skapa ny resursgrupp** att lägga till det virtuella nätverket till en ny resursgrupp. Följande bild visar resursen resursgruppsinställningarna för en ny resursgrupp som kallas **TestRG**. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../articles/azure-resource-manager/resource-group-overview.md#resource-groups).
   
    ![Skapa resurs fönstret](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
6. Ändra vid behov inställningarna för **Prenumeration** och **Plats** för din VNet. 
7. Om du inte vill se VNet som en ikon på **Startsidan**, inaktiverar du **Fäst på startsidan**. 
8. Klicka på **skapa** och notera ikonen som heter **skapar virtuellt nätverk** som visas i följande bild.
   
    ![Skapa VNet i portalen](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
9. Vänta tills VNet som ska skapas och när panelen, klickar du på den och lägga till flera undernät.
   
    ![Skapa VNet i portalen](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
10. Du bör se den **Configuration** för din VNet som visas. 
   
    ![Skapa VNet i portalen](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
11. Klicka på **undernät** > **Lägg till**, Skriv en **namn** och ange en **adressintervall (CIDR-block)** din undernät, och sedan Klicka på **OK**. Följande bild visar inställningarna för våra aktuella scenariot.
    
    ![Skapa VNet i Azure-portalen](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)

