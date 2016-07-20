## Distribuera ARM-mallen med klicka för att distribuera

Du kan återanvända fördefinierade ARM-mallar som finns i en github-databas som hanteras av Microsoft och är öppen för allmänheten. De här mallarna kan distribueras direkt från github, eller hämtas och ändras för att passa dina behov. Följ stegen nedan om du vill distribuera en mall som skapar ett VNet med två undernät.

1. Från en webbläsare, navigerar du till [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates).
2. Bläddra ned i mallistan och klicka på **101-vnet-two-subnets**. Kontrollera **README.md**-filen enligt nedan.

    ![README.md-filen i GitHub](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. Klicka på **Distribuera till Azure**. Ange dina inloggningsuppgifter för Azure vid behov. 
4. I bladet **Parametrar** anger du de värden som du vill använda för att skapa ditt nya VNet och klickar sedan på **OK**. I bilden nedan visas värdena för vårt scenario.

    ![ARM-mallparametrar](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

4. Klicka på **Resursgrupp** och välj en resursgrupp att lägga till VNet till, eller klicka på **Skapa ny** för att lägga till VNet till en ny resursgrupp. Bilden nedan visar resursgruppsinställningarna för en ny resursgrupp med namnet **TestRG**.

    ![Resursgrupp](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

5. Ändra vid behov inställningarna för **Prenumeration** och **Plats** för din VNet.
6. Om du inte vill se VNet som en ikon på **Startsidan**, inaktiverar du **Fäst på startsidan**.
5. Klicka på **Juridiska villkor**, läs igenom villkoren och klicka på **Köp** för att godkänna. 
6. Skapa VNet genom att klicka på **Skapa**.

    ![Ikonen för Skicka in distribution i preview-portalen](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

7. När distributionen är klar, klickar du på **TestVNet** > **Alla inställningar** > **Undernät** för att se undernätsegenskaperna, enligt nedan.

    ![Skapa VNet i preview-portalen](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.gif)


<!--HONumber=Jun16_HO2-->


