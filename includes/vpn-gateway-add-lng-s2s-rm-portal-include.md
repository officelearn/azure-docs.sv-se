1. Klicka på **+Lägg till** från **Alla resurser** i portalen. 
2. I bladsökrutan **Allt** skriver du **Lokal nätverksgateway** och klickar sedan för att söka. Detta gör att en lista returneras. Klicka på **Lokal nätverksgateway** för att öppna bladet och klicka sedan på **Skapa** för att öppna bladet**Skapa lokal nätverksgateway**.

  ![skapa lokal nätverksgateway](./media/vpn-gateway-add-lng-s2s-rm-portal-include/createlng.png)

3. På **Skapa lokal nätverksgateway-bladet**, anger du värden för din lokala gateway.

  - **Namn:** Ange ett namn för ditt lokala gateway-objekt.
  - **IP address:** Det här är den offentliga IP-adressen för den VPN-enhet som du vill att Azure ska ansluta till. Ange en giltig offentlig IP-adress. IP-adressen får inte vara bakom en NAT och måste kunna nås av Azure. Om du inte har IP-adress just nu, du kan använda de värden som visas i skärmbilden, men du behöver gå tillbaka och ersätta platshållar-IP-adressen med den offentliga IP-adressen för VPN-enheten. Azure kommer annars inte kunna ansluta.
  - **Adressutrymme** refererar till adressintervallen för det nätverk som detta lokala nätverk representerar. Du kan lägga till flera adressintervall. Kontrollera att intervallen du anger här inte överlappar intervallen för andra nätverk som du vill ansluta till. Azure vidarebefordrar det adressintervall som du anger till den lokala VPN-enhetens IP-adress. *Använd dina egna värden här, inte värdena som visas i skärmbilden*.
  - **Prenumeration:** Kontrollera att korrekt prenumeration visas.
  - **Resursgrupp:** Välj den resursgrupp som du vill använda. Du kan antingen skapa en ny resursgrupp eller välja en som du redan har skapat.
  - **Plats:** Välj den plats som det här objektet kommer att skapas i. Du kanske vill välja samma plats som din VNet finns i, men du behöver inte göra det.

4. När du är klar med att ange värden klickar du på **Skapa** längst ned på bladet för att skapa den lokala nätverksgatewayen.