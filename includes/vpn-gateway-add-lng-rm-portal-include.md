1. Klicka på **+Lägg till** från **Alla resurser** i portalen.
2. I den **allt** sidan sökrutan, Skriv **lokal nätverksgateway**, och sedan klicka om du vill returnera en lista över resurser. Klicka på **Lokal nätverksgateway** för att öppna sidan och klicka sedan på **Skapa** för att öppna sidan **Skapa lokal nätverksgateway**.

  ![skapa lokal nätverksgateway](./media/vpn-gateway-add-lng-rm-portal-include/lng.png)
3. På sidan **Skapa lokal nätverksgateway** anger du värden för din lokala gateway.

  - **Namn:** Ange ett namn för ditt lokala gateway-objekt. Använd om möjligt något intuitiva som **ClassicVNetLocal** eller **TestVNet1Local**. På så sätt blir det lättare för dig att identifiera den lokala nätverksgatewayen i portalen.
  - **IP-adress:** ange en giltig offentlig **IP-adress** för VPN-enhet eller virtuell nätverksgateway som du vill ansluta till.

    * **Om den här lokala nätverket representerar en lokal plats:** ange den offentliga IP-adressen för VPN-enhet som du vill ansluta till. Den får inte vara bakom en NAT och måste kunna nås av Azure.
    * **Om den här lokala nätverket motsvarar ett annat VNet:** ange den offentliga IP-adressen som har tilldelats den virtuella nätverksgatewayen för det virtuella nätverket.
    * **Om du ännu inte har IP-adressen:** du utgör en giltig platshållare IP-adress och sedan gå tillbaka och ändra den här inställningen innan du ansluter.
  - **Adressutrymme** refererar till adressintervallen för det nätverk som detta lokala nätverk representerar. Du kan lägga till flera adressintervall. Kontrollera att de intervall som du anger här inte överlappar intervallen för andra nätverk som du vill ansluta till.
  - **Konfigurera BGP-inställningar:** Använd endast vid konfigurering av BGP. Annars ska detta inte väljas.
  - **Prenumeration:** Kontrollera att korrekt prenumeration visas.
  - **Resursgrupp:** Välj den resursgrupp som du vill använda. Du kan antingen skapa en ny resursgrupp eller välja en som du redan har skapat.
  - **Plats:** Välj den plats som det här objektet kommer att skapas i. Du kanske vill välja samma plats som din VNet finns i, men du behöver inte göra det.
4. Klicka på **Skapa** för att skapa den lokala nätverksgatewayen.