#### <a name="to-install-mpio-on-the-host"></a>Installera MPIO på värden
1. Öppna Serverhanteraren på Windows Server-värd. Serverhanteraren startar som standard när en medlem i gruppen Administratörer loggar in på en dator som kör Windows Server 2012 R2 eller Windows Server 2012. Om Serverhanteraren inte redan är öppen klickar du på **Start > Serverhanteraren**.
   
    ![Serverhanteraren](./media/storsimple-install-mpio-windows-server/IC740997.png)
2. Klicka på **Serverhanteraren > instrumentpanelen > Lägg till roller och funktioner**. Detta startar den **Lägg till roller och funktioner** guiden.
   
    ![Guiden Lägg till roller och funktioner 1](./media/storsimple-install-mpio-windows-server/IC740998.png)
3. I den **Lägg till roller och funktioner** guiden gör du följande:
   
   * På den **innan du börjar** klickar du på **nästa**.
   * På den **Välj installationstyp** godkänner du standardinställningen **rollbaserad eller funktionsbaserad** installation. Klicka på **Nästa**.
     
       ![Guiden Lägg till roller och funktioner 2](./media/storsimple-install-mpio-windows-server/IC740999.png)
   * På den **väljer målservern** väljer **Välj en server från serverpoolen**. Värdservern bör identifieras automatiskt. Klicka på **Nästa**.
   * På den **Välj serverroller** klickar du på **nästa**.
   * På den **Välj funktioner** väljer **Multipath i/o**, och klicka på **nästa**.
     
       ![Guiden Lägg till roller och funktioner 5](./media/storsimple-install-mpio-windows-server/IC741000.png)
   * På den **Bekräfta installationsinställningarna** bekräftar valet och välj sedan **starta om målservern automatiskt om det behövs**, enligt nedan. Klicka på **Installera**.
     
       ![Guiden Lägg till roller och funktioner 8](./media/storsimple-install-mpio-windows-server/IC741001.png)
   * Du meddelas när installationen är klar. Stäng guiden genom att klicka på **Stäng**.
     
       ![Guiden Lägg till roller och funktioner 9](./media/storsimple-install-mpio-windows-server/IC741002.png)

