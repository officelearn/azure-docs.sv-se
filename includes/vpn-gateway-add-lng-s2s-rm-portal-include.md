1. Klicka på **+Lägg till** från **Alla resurser** i portalen. I bladsökrutan **Allt** skriver du **Lokal nätverksgateway** och klickar sedan för att söka. Detta gör att en lista returneras. Klicka på **Lokal nätverksgateway** för att öppna bladet och klicka sedan på **Skapa** för att öppna bladet**Skapa lokal nätverksgateway**.

      ![skapa lokal nätverksgateway](./media/vpn-gateway-add-lng-s2s-rm-portal-include/createlng.png)
2. På **Skapa lokal nätverksgateway-bladet**, anger du ett **Namn** för ditt lokala gateway-objekt.
3. Ange en giltig offentlig **IP-adress** för VPN-enheten eller virtuell nätverksgateway som du vill ansluta till.<br>Det här är den offentliga IP-adressen för den VPN-enhet som du vill ansluta till. Den får inte vara bakom en NAT och måste kunna nås av Azure. *Använd dina egna värden, inte värdena som visas i skärmbilden*.
4. **Adressutrymme** refererar till adressintervallen för det nätverk som detta lokala nätverk representerar. Du kan lägga till flera adressintervall. Kontrollera att intervallen du anger här inte överlappar intervallen för andra nätverk som du vill ansluta till. Azure vidarebefordrar det adressintervall som du anger till den lokala VPN-enhetens IP-adress. *Använd dina egna värden här, inte värdena som visas i skärmbilden*.
5. För **Prenumeration** verifierar du att korrekt prenumeration visas.
6. För **resursgruppen** väljer du den resursgrupp som du vill använda. Du kan antingen skapa en ny resursgrupp eller välja en som du redan har skapat.
7. För **Plats**, välj den plats som det här objektet kommer att skapas i. Du kanske vill välja samma plats som din VNet finns i, men du behöver inte göra det.
8. Klicka på **Skapa** för att skapa den lokala nätverksgatewayen.