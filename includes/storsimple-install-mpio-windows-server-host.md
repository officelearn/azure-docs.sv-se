#### <a name="to-install-mpio-on-the-host"></a>Du installerar MPIO på värden
1. Öppna Serverhanteraren på Windows Server-värd. Som standard startar Server Manager när en medlem i administratörsgruppern loggar in på en dator som kör Windows Server 2012 R2 eller Windows Server 2012. Om Serverhanteraren inte redan är öppen klickar du på **starta > Serverhanteraren**.
   
    ![Serverhanteraren](./media/storsimple-install-mpio-windows-server/IC740997.png)
2. Klicka på **Server Manager > instrumentpanelen > Lägg till roller och funktioner**. Detta startar den **Lägg till roller och funktioner** guiden.
   
    ![Lägg till roller och funktioner som guiden 1](./media/storsimple-install-mpio-windows-server/IC740998.png)
3. I den **Lägg till roller och funktioner** guiden gör följande:
   
   * På den **innan du börjar** klickar du på **nästa**.
   * På den **Välj installationstyp** godkänner du standardinställningen **rollbaserad eller funktionsbaserad** installation. Klicka på **Nästa**.
     
       ![Lägg till roller och funktioner som guiden 2](./media/storsimple-install-mpio-windows-server/IC740999.png)
   * På den **väljer målservern** väljer **Välj en server från serverpoolen**. Värdservern bör identifieras automatiskt. Klicka på **Nästa**.
   * På den **Välj serverroller** klickar du på **nästa**.
   * På den **Välj vilka funktioner du** väljer **Multipath I/O**, och klicka på **nästa**.
     
       ![Lägg till roller och funktioner som guiden 5](./media/storsimple-install-mpio-windows-server/IC741000.png)
   * På den **Bekräfta installationsinställningarna** bekräftar valet och välj sedan **starta om målservern automatiskt om det behövs**, enligt nedan. Klicka på **Installera**.
     
       ![Lägg till roller och funktioner som guiden 8](./media/storsimple-install-mpio-windows-server/IC741001.png)
   * Du meddelas när installationen är klar. Stäng guiden genom att klicka på **Stäng**.
     
       ![Lägg till roller och funktioner som guiden 9](./media/storsimple-install-mpio-windows-server/IC741002.png)

