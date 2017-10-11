1. I hanteraren för redundanskluster, expandera **roller**, och sedan markera tillgänglighetsgruppen.  

2. På den **resurser** fliken, högerklicka på grupplyssnarens namn och klicka sedan på **egenskaper**.

3. Klicka på den **beroenden** fliken. Om flera resurser visas, kontrollera att IP-adresser har eller inte och beroenden.  

4. Klicka på **OK**.

5. Högerklicka på grupplyssnarens namn och klicka sedan på **Anslut**.

6. När lyssnaren är online på den **resurser** fliken, högerklicka på tillgänglighetsgruppen och klicka sedan på **egenskaper**.
   
    ![Konfigurera tillgänglighetsgruppresursen](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678772.gif)

7. Skapa ett beroende på resursen lyssnare namn (inte IP-adress resurser namnet) och klicka sedan på **OK**.
   
    ![Lägg till beroende på grupplyssnarens namn](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678773.gif)

8. Starta SQL Server Management Studio och Anslut till den primära repliken.

9. Gå till **AlwaysOn hög tillgänglighet** > **Tillgänglighetsgrupper** > **\<AvailabilityGroupName\>**   >  **Tillgänglighetsgruppens lyssnare**.  
    Lyssnare namnet som du skapade i hanteraren för redundanskluster ska visas.

10. Högerklicka på grupplyssnarens namn och klicka sedan på **egenskaper**.

11. I den **Port** anger du portnumret för tillgänglighetsgruppens lyssnare med hjälp av $EndpointPort som du använde tidigare (1433 var standard i den här självstudiekursen), och klicka sedan på **OK**.

