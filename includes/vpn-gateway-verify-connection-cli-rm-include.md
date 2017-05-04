Du kan kontrollera att anslutningen har utförts med hjälp av följande CLI-kommando. Konfigurera värdena så att de stämmer överens med dina värden. I exemplet refererar  -n till namnet på den anslutning som du skapade och vill testa.

```azurecli
az network vpn-connection show -n VNet1toSite2 -g TestRG1
```

När anslutningen håller på att upprättas visas status Ansluter. När anslutningen har upprättats ändras status till Ansluten och du kan se ingående och utgående byte.