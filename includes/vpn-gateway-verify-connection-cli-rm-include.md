Du kan kontrollera att anslutningen har utförts med hjälp av följande kommandot [az network vpn-connection show](/cli/azure/network/vpn-connection#show). I exemplet avser '-name' namnet på den anslutning du vill testa. När anslutningen håller på att upprättas visas status Ansluter. När anslutningen har upprättats ändras status till ”Ansluten”.

```azurecli
az network vpn-connection show --name VNet1toSite2 --resource-group TestRG1
```

