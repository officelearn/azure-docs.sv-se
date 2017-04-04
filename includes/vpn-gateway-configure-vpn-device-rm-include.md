Plats-till-plats-anslutningar till ett lokalt nätverk kräver en VPN-enhet. Det finns många olika VPN-enheter som fungerar med Azure. Mer information om VPN-enheter och konfigurationsinställningar finns i avsnittet om [VPN-enheter](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md). Innan du konfigurerar VPN-enheten kontrollerar du om det finns några [kända kompatibilitetsproblem](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#known) med den VPN-enhet som du vill använda. Kontakta enhetstillverkaren för specifik konfigurationsinformation för VPN-enheten.

När du konfigurerar VPN-enheten behöver du följande objekt:

- **Den offentliga IP-adressen** för din virtuella nätverksgateway.

    -  Om du använder Azure Portal hittar du den offentliga IP-adressen genom att gå till **Virtuella nätverksgatewayer** och klicka på namnet på din gateway. 

    - Om du använder PowerShell hittar du den offentliga IP-adressen för din virtuella nätverksgateway genom att ersätta värdena med dina egna värden i följande exempel.

            Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG
- **En delad nyckel**. Det här är samma delade nyckel som du anger när du skapar VPN-anslutningen för plats-till-plats. I vårt exempel använder vi en enkel delad nyckel. Du bör skapa och använda en mer komplex nyckel.




