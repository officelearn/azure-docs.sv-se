

## <a name="use-the-portal-to-move-a-vm-to-a-different-subscription"></a>Använda portalen för att flytta en virtuell dator till en annan prenumeration
Du kan flytta en virtuell dator och dess tillhörande resurser till en annan prenumeration med hjälp av portalen.

1. Öppna [Azure-portalen](https://portal.azure.com).
2. Klicka på **Bläddra** > **virtuella datorer** och välj den virtuella datorn som du vill flytta från listan.
   
    ![Skärmbild av avsnittet Essentials där du kan klicka på pennikonen att öppna bladet flytta resurser.](./media/virtual-machines-common-move-vm/move-button.png)
3. I den **Essentials** klickar du på den **ändra prenumerationen** pennikonen bredvid prenumerationsnamn. Den **flyttar resurser** öppnas bladet.
   
    ![Skärmbild av bladet flytta resurser.](./media/virtual-machines-common-move-vm/move.png)
4. Välj var och en av resurserna som ska flyttas. I de flesta fall bör du flytta alla valfria resurser.
5. Välj den **prenumeration** där du vill att den virtuella datorn flyttas.
6. Välj en befintlig **resursgruppen** eller Skriv ett namn som har en ny resursgrupp som har skapats.
7. När du är klar väljer du att du förstår att nya resursen med ID: N skapas och de måste användas med den virtuella datorn när den flyttas, sedan klickar du på **OK**.

## <a name="use-the-portal-to-move-a-vm-to-another-resource-group"></a>Använda portalen för att flytta en virtuell dator till en annan resursgrupp
Du kan flytta en virtuell dator och dess tillhörande resurser till en annan resursgrupp med hjälp av portalen.

1. Öppna [Azure-portalen](https://portal.azure.com).
2. Klicka på **Bläddra** > **resursgrupper** och markera den resursgrupp som innehåller den virtuella datorn.
3. I den **resursgruppen** bladet väljer **flytta** på menyn.
   
    ![Skärmbild av knappen Flytta på menyn resurs grupper.](./media/virtual-machines-common-move-vm/move-rg.png)
4. I den **flyttar resurser** bladet väljer resurserna som ska flyttas och sedan skriva en befintlig resurs gruppnamn eller välj att skapa en ny resursgrupp. När du är klar väljer du att du förstår att nya resursen med ID: N skapas och de måste användas med den virtuella datorn när den flyttas, sedan klickar du på **OK**
   
    ![Skärmbild av bladet flytta resurser.](./media/virtual-machines-common-move-vm/move-rg-list.png)

