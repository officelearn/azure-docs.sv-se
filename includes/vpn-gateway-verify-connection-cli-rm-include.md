Du kan kontrollera att anslutningen har utförts med hjälp av följande kommandot [az network vpn-connection show](/cli/azure/network/vpn-connection#show). Konfigurera värdena så att de stämmer överens med dina värden. I exemplet avser --name namnet på den anslutning du skapade och vill testa.

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```

När anslutningen håller på att upprättas visas status Ansluter. När anslutningen har upprättats ändras status till Ansluten och du kan se ingående och utgående byte.