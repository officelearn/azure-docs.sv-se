
För att konfigurera din VPN-enhet, kommer du att behöva den offentliga IP-adressen för den virtuella nätverksgatewayen för att konfigurera din lokala VPN-enhet. Jobba med din enhetstillverkare för att få specifik konfigurationsinformation och konfigurera din enhet. Referera till [VPN-enheter](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md) för mer information om VPN-enheter som fungerar bra tillsammans med Azure.

Använd följande exempel för att hitta den offentliga IP-adressen för din virtuella nätverksgateway med hjälp av PowerShell:

    Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG

Du kan också se den offentliga IP-adressen för din virtuella nätverksgateway med hjälp av Azure-portalen. Gå till **Virtuella nätverksgatewayer** och klicka på namnet för din gateway.

<!--HONumber=Sep16_HO3-->


