Plats-till-plats-anslutningar till ett lokalt nätverk kräver en VPN-enhet. Vi tillhandahåller inte konfigurationssteg för alla VPN-enheter men informationen i följande länkar kan vara användbar:

- Se [VPN-enheter](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) för mer information om kompatibla VPN-enheter. 
- Mer information om länkar till konfigurationsinställningar för enheter, finns i [Verifierade VPN-enheter](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable). Länkarna till konfigurationsanvisningarna tillhandahålls i mån av möjlighet. Det är alltid bäst att kontrollera med enhetstillverkaren för att få den senaste konfigurationsinformationen.
- Mer information om att redigera enhetens konfigurationsexempel finns i [Redigera exempel](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#editing).
- Mer information om IPsec-/IKE-parametrar finns i [Parametrar](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec).
- Innan du konfigurerar VPN-enheten kontrollerar du om det finns några [kända kompatibilitetsproblem](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#known) med den VPN-enhet som du vill använda.

När du konfigurerar VPN-enheten behöver du följande objekt:

- Den offentliga IP-adressen för din virtuella nätverksgateway.

    -  Om du använder Azure Portal hittar du den offentliga IP-adressen genom att gå till **Virtuella nätverksgatewayer** och klicka på namnet på din gateway. 
    - Om du använder PowerShell hittar du den offentliga IP-adressen för din virtuella nätverksgateway genom att ersätta värdena med dina egna värden i följande exempel.

            Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG
- En delad nyckel. Det här är samma delade nyckel som du anger när du skapar VPN-anslutningen för plats-till-plats. I vårt exempel använder vi en enkel delad nyckel. Du bör skapa och använda en mer komplex nyckel.




