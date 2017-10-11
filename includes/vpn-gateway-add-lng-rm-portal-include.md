1. Klicka på **+Lägg till** från **Alla resurser** i portalen. I den **allt** bladet sökrutan, Skriv **lokal nätverksgateway**, och sedan klicka om du vill returnera en lista över resurser. Klicka på **Lokal nätverksgateway** för att öppna bladet och klicka sedan på **Skapa** för att öppna bladet**Skapa lokal nätverksgateway**.
   
    ![skapa lokal nätverksgateway](./media/vpn-gateway-add-lng-rm-portal-include/lng.png)

2. På **Skapa lokal nätverksgateway-bladet**, anger du ett **Namn** för ditt lokala gateway-objekt. Använd om möjligt något intuitiva som **ClassicVNetLocal** eller **TestVNet1Local**. På så sätt blir det lättare för dig att identifiera den lokala nätverksgatewayen i portalen.
3. Ange en giltig offentlig **IP-adress** för VPN-enhet eller virtuell nätverksgateway som du vill ansluta till.<br>**Om den här lokala nätverket representerar en lokal plats:** ange den offentliga IP-adressen för VPN-enhet som du vill ansluta till. Den får inte vara bakom en NAT och måste kunna nås av Azure.<br>**Om den här lokala nätverket motsvarar ett annat VNet:** ange den offentliga IP-adressen som har tilldelats den virtuella nätverksgatewayen för det virtuella nätverket.<br>**Om du ännu inte har IP-adressen:** du utgör en giltig platshållare IP-adress och sedan gå tillbaka och ändra den här inställningen innan du ansluter.
4. **Adressutrymme** refererar till adressintervallen för det nätverk som detta lokala nätverk representerar. Du kan lägga till flera adressintervall. Kontrollera att de intervall som du anger här inte överlappar intervallen för andra nätverk som du vill ansluta till.
5. För **Prenumeration** verifierar du att korrekt prenumeration visas.
6. För **resursgruppen** väljer du den resursgrupp som du vill använda. Du kan antingen skapa en ny resursgrupp eller välja en som du redan har skapat.
7. För **plats**, Välj den plats där den här resursen kommer att skapas. Du kanske vill välja samma plats som din VNet finns i, men du behöver inte göra det.
8. Klicka på **Skapa** för att skapa den lokala nätverksgatewayen.

