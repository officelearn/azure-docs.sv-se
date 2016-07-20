1. I Azure Portal, navigerar du till **Ny** **>** **Nätverk** **>** **Lokal nätverksgateway**.

    ![skapa lokal nätverksgateway](./media/vpn-gateway-add-lng-rm-portal-include/addlng250.png)

2. På **Skapa lokal nätverksgateway-bladet**, anger du ett **Namn** för ditt lokala gateway-objekt.
 
3. Ange en **IP-adress** för din gateway. Det här är IP-adressen för den externa VPN-enhet som du vill ansluta till. Den får inte vara bakom en NAT och måste kunna nås av Azure.

4. **Adressutrymmet** är adressintervallen på ditt lokala nätverk (vanligtvis på plats). Du kan lägga till flera adressintervall. Intervall som du anger här får inte överlappa adressutrymmen som du använder för någon av de virtuella nätverk som kommer att kommunicera via gatewayen.  Du kommer att behöva koordinera med din lokala konfiguration samt med adressutrymmena för ditt virtuella Azure-nätverk.
 
5. För **Prenumeration** verifierar du att korrekt prenumeration visas.

6. För **resursgruppen** väljer du den resursgrupp som du vill använda. Du kan antingen skapa en ny resursgrupp eller välja en som du redan har skapat. Om du vill skapa en ny resursgrupp skriver du in namnet i rutan. Om du vill välja en resursgrupp som du redan skapat klickar du på **Resursgrupp** för att öppna **Resursgrupp**-bladet och väljer sedan den resursgrupp som du vill använda.

7. Om du skapar en ny lokal nätverksgateway, kan du använda samma plats som den virtuella nätverksgatewayen för **Plats**. Men det krävs inte. Den lokala nätverksgatewayen kan vara på en annan plats. 

8. Lämna "Fäst på instrumentpanelen" markerad om du vill hitta den här lokala nätverksgatewayen direkt på instrumentpanelen.

9. Klicka på **Skapa** för att skapa den lokala nätverksgatewayen. Du kommer att se "Distribuerar lokal nätverksgateway" på din instrumentpanel.

10. När den lokala nätverksgatewayen har skapats, öppnas den i portalen så du kan se den.

    



<!--HONumber=Jun16_HO2-->


