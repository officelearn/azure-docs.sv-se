> [!NOTE]
> Den offentliga IP-adressen för VPN-gatewayen ändras när du migrerar från en gammal SKU till en ny SKU.
> 

Du kan inte ändra storlek på Azure VPN-gatewayer direkt mellan gamla SKU: er och nya SKU-familjer. Om du har VPN-gatewayer i Resource Manager-distributionsmodellen som använder den äldre versionen av de SKU: er kan migrera du till nya SKU: er. För att migrera måste du ta bort den befintliga VPN-gatewayen för det virtuella nätverket och sedan skapa en ny.

Arbetsflöde för migrering:

1. Ta bort flera anslutningar till en virtuell nätverksgateway.
2. Ta bort VPN-gatewayen.
3. Skapa den nya VPN-gatewayen.
4. Uppdatera din lokala VPN-enhet med den nya IP-adressen för VPN-gatewayen (för plats-till-plats-anslutningar).
5. Uppdatera gateway IP-adressvärdet för alla lokala nätverket VNet-till-VNet-gateways som ska ansluta till den här gatewayen.
6. Hämta den nya klientpaket för VPN-konfiguration för P2S-klienter som ansluter till det virtuella nätverket via den här VPN-gatewayen.
7. Skapa om flera anslutningar till en virtuell nätverksgateway.