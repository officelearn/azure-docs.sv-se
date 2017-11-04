### <a name="gwipnoconnection"></a>Att ändra lokala nätverkets gateway-IP-adress - ingen gateway-anslutningen

Använd exemplet för att ändra en lokal nätverksgateway som inte har någon gatewayanslutning. När du ändrar det här värdet kan du också ändra adressprefixen på samma gång.

1. På lokala nätverkets Gateway-resursen i den **inställningar** klickar du på **Configuration**.
2. I den **IP-adress** ändrar IP-adress.
3. Klicka på **spara** spara inställningarna.

### <a name="gwipwithconnection"></a>Att ändra lokala nätverkets gateway gateway IP-adress - befintlig gateway-anslutningen

Om du vill ändra en lokal nätverksgateway som har en anslutning måste du först ta bort anslutningen. När anslutningen har tagits bort kan du ändra IP-adressen till gatewayen och skapa en ny anslutning. Du kan också ändra adressprefixen på samma gång. Det medför en del avbrott för din VPN-anslutning. När du ändrar IP-adressen för gatewayen behöver du inte ta bort VPN-gatewayen. Du måste bara ta bort anslutningen.
 
#### <a name="1-remove-the-connection"></a>1. Ta bort anslutningen.

1. På lokala nätverkets Gateway-resursen i den **inställningar** klickar du på **anslutningar**.
2. Klicka på den **...**  på raden för anslutningen, klicka sedan på **ta bort**.
3. Klicka på **spara** att spara dina inställningar.

#### <a name="2-modify-the-ip-address"></a>2. Ändra IP-adressen.

Du kan också ändra adressprefixen på samma gång.

1. I den **IP-adress** ändrar IP-adress.
2. Klicka på **spara** spara inställningarna.

#### <a name="3-recreate-the-connection"></a>3. Skapa anslutningen.

1. Navigera till den virtuella Nätverksgatewayen för din VNet. (Inte den lokala Nätverksgatewayen.)
2. På den virtuella Nätverksgatewayen i den **inställningar** klickar du på **anslutningar**.
3. Klicka på den **+ Lägg till** att öppna den **Lägg till anslutning** bladet.
4. Återskapa din anslutning.
5. Klicka på **OK** att skapa anslutningen.