1. Gå till och öppna bladet för den virtuella nätverksgatewayen. Det finns flera sätt att göra detta på. I vårt exempel gick vi till gatewayen 'VNet1GW' genom att gå till **TestVNet1 -> Översikt -> Anslutna enheter -> VNet1GW**.
2. Klicka på **Anslutningar** på bladet för VNet1GW. Klicka på **+Lägg till** överst på bladet Anslutningar för att öppna bladet **Lägg till anslutning**.

    ![Skapa en plats-till-plats-anslutning](./media/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include/connection1.png)

3. På bladet **Lägg till anslutning** fyller du i värden för att skapa anslutningen.

  - **Namn:** Namnge din anslutning. Vi använder **VNet1toSite2** i vårt exempel.
  - **Anslutningstyp:** Välj du **Plats-till-plats(IPSec)**.
  - **Virtuell nätverksgateway:** Värdet är låst eftersom du ansluter från den här gatewayen.
  - **Lokal nätverksgateway:** Klicka på **Välj en lokal nätverksgateway** och väljer den lokala nätverksgateway som du vill använda. I vårt exempel använder vi **Webbplats2**.
  - **Delad nyckel:** Värdet måste matcha det värde som du använder för din lokala VPN-enhet. I det här exemplet använder vi ”abc123”, men du kan (och bör) använda ett mer komplext värde. Det är viktigt att värdet du anger här är samma värde som du angav när du konfigurerade VPN-enheten.
  - De återstående värdena för **Prenumeration**, **Resursgrupp** och **Plats** är låsta.

4. Klicka på **OK** för att skapa din anslutning. Du kommer att se *Skapar anslutningen* blinka fram på skärmen.
5. Anslutningen visas på bladet **Anslutningar** för den virtuella nätverksgatewayen. Statusen kommer att gå från *Okänd* till *Ansluter* och sedan till *Klar*.