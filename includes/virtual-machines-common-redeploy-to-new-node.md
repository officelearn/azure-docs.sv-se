## <a name="use-the-azure-portal"></a>Använda Azure-portalen
1. Välj den virtuella datorn som du vill distribuera om, och välj sedan den *omdistribuera* knappen i den *inställningar* bladet. Du kan behöva rulla finns i **Support och felsökning** avsnitt som innehåller knappen 'Omdistribuera' som i följande exempel:
   
    ![Azure VM-bladet](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)
2. Bekräfta åtgärden genom att välja den *omdistribuera* knappen:
   
    ![Distribuera en VM-bladet](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)
3. Den **Status** ändras till den virtuella datorns *uppdatering* som den virtuella datorn förbereds för att distribuera, som visas i följande exempel:
   
    ![Uppdatering av virtuell dator](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)
4. Den **Status** ändras till *Start* som den virtuella datorn startar på en ny Azure-värd som visas i följande exempel:
   
    ![Starta VM](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)
5. När den virtuella datorn har slutförts startprocessen den **Status** återgår sedan till *kör*, som anger den virtuella datorn har varit distribuerades:
   
    ![VM körs](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

