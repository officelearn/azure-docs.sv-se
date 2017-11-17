### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Steg 1: Gå till den virtuella nätverksgatewayen

1. I den [Azure-portalen](https://portal.azure.com), gå till **alla resurser**. 
2. Navigera till den virtuella nätverksgatewayen som du vill ta bort och klicka på den för att öppna det virtuella nätverksgateway-bladet.

### <a name="step-2-delete-connections"></a>Steg 2: Ta bort anslutningar

1. Klicka på bladet för din virtuella nätverksgateway **anslutningar** att visa alla anslutningar till gatewayen.
2. Klicka på den **”...”** på raden på namnet på anslutningen väljer **ta bort** i listrutan.
3. Klicka på **Ja** bekräfta att du vill ta bort anslutningen. Om du har flera anslutningar kan du ta bort varje anslutning.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Steg 3: Ta bort den virtuella nätverksgatewayen

Tänk på att om du har en P2S-konfiguration till detta virtuella nätverk utöver konfigurationen S2S tar bort virtuell nätverksgateway automatiskt frånkopplas alla P2S-klienter utan varning.

1. Klicka på det virtuella nätverksgateway-bladet, **översikt**.
2. På den **översikt** bladet, klickar du på **ta bort** att ta bort denna gateway.
