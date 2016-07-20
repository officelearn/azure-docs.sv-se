## Skapa ett VNet i Azure-portalen

Följ stegen nedan för att skapa ett VNet baserat på scenariot ovan med hjälp av Azure Preview Portal.

1. Från en webbläsare, navigerar du till http://portal.azure.com och loggar, vid behov, in med ditt Azure-konto.
2. Klicka på **NY** > **Nätverk** > **Virtuellt nätverk** och välj sedan **Resource Manager** från listan **Välj en distributionsmodell**. Klicka sedan på **Skapa** enligt bilden nedan.

    ![Skapa VNet i Azure-portalen](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure1.gif)

3. På bladet **Skapa virtuellt nätverk**, konfigurerar du VNet-inställningarna som det visas i bilden nedan.

    ![Skapa ett virtuellt nätverksblad](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure2.png)

4. Klicka på **Resursgrupp** och välj en resursgrupp att lägga till VNet till, eller klicka på **Skapa ny** för att lägga till VNet till en ny resursgrupp. Bilden nedan visar resursgruppsinställningarna för en ny resursgrupp med namnet **TestRG**. Mer information om resursgrupper finns i [Översikt över Azure Resource Manager](../articles/resource-group-overview.md/#resource-groups).

    ![Resursgrupp](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure3.png)

5. Ändra vid behov inställningarna för **Prenumeration** och **Plats** för din VNet. 

6. Om du inte vill se VNet som en ikon på **Startsidan**, inaktiverar du **Fäst på startsidan**. 

7. Klicka på **Skapa** och notera ikonen som heter **Skapar virtuellt nätverk** som visas i bilden nedan.

    ![Skapa en virtuell nätverksikon](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure4.png)

8. Vänta tills VNet har skapats och i bladet **Virtuellt nätverk** klickar du sedan på **Alla inställningar** > **Undernät** > **Lägg till** enligt nedan.

    ![Lägg till undernät i Azure-portalen](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure5.gif)

9. Ange undernätsinställningar för *BackEnd*-undernätet, enligt nedan och klicka sedan på **OK**. 

    ![Undernätsinställningar](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure6.png)

10. Lägg märke till listan med undernät, som visas i bilden nedan.

    ![Lista med undernät i VNet](./media/virtual-networks-create-vnet-arm-pportal-include/vnet-create-arm-pportal-figure7.png)


<!--HONumber=Jun16_HO2-->


