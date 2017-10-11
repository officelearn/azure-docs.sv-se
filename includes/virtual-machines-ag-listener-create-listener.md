I det här steget kan skapa du manuellt tillgänglighetsgruppens lyssnare i hanteraren för redundanskluster och SQL Server Management Studio.

1. Öppna Klusterhanteraren för växling från den nod som värd för den primära repliken.

2. Välj den **nätverk** nod och Observera klustrets nätverksnamn. Det här namnet används i variabeln $ClusterNetworkName i PowerShell-skript.

3. Expandera klusternamnet och klicka sedan på **roller**.

4. I den **roller** fönstret, högerklicka på tillgänglighetsgruppens namn och välj sedan **Lägg till resurs** > **klientåtkomstpunkt**.
   
    ![Lägg till klientåtkomstpunkten för tillgänglighetsgruppen](./media/virtual-machines-sql-server-configure-alwayson-availability-group-listener/IC678769.gif)

5. I den **namn** , skapar du ett namn för den här lyssnaren, klickar du på **nästa** två gånger, och klicka sedan på **Slutför**.  
    Tar inte med lyssnare eller resursen i det här läget.

6. Klicka på den **resurser** fliken och expandera sedan den klientåtkomstpunkt som du nyss skapade. 
    IP-adressresurs för varje klusternätverk i klustret visas. Om detta är en Azure-only-lösning, visas endast en IP-adressresurs.

7. Gör något av följande:
   
   * Så här konfigurerar du en hybridlösning:
     
        a. Högerklicka på IP-adressresurs som motsvarar det lokala undernätet och välj sedan **egenskaper**. Observera namn på IP-adressen och nätverksnamnet.
   
        b. Välj **statisk IP-adress**, tilldela en IP-adress som inte används och klicka sedan på **OK**.
 
   * Så här konfigurerar du en endast Azure-lösning:

        a. Högerklicka på IP-adressresurs som motsvarar din Azure-undernät och välj sedan **egenskaper**.
       
       > [!NOTE]
       > Om lyssnaren senare misslyckas anslutas på grund av en konflikt IP-adress som valts av DHCP kan du konfigurera en statisk IP-adress i egenskapsfönstret.
       > 
       > 

       b. I samma **IP-adress** i fönstret Ändra den **IP-adressnamn**.  
        Det här namnet används i variabeln $IPResourceName på PowerShell-skript. Om din lösning sträcker sig över flera virtuella Azure-nätverk, kan du upprepa det här steget för varje IP-resurs.

