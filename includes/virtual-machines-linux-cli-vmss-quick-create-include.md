Om du inte redan har gjort det så kan du skaffa en [kostnadsfri utvärderingsprenumeration på Azure](https://azure.microsoft.com/pricing/free-trial/) och få [Azure CLI](../articles/xplat-cli-install.md) [anslutet till ditt Azure-konto](../articles/xplat-cli-connect.md). Kontrollera att Azure CLI är i Resource Manager-läge på följande sätt:

```azurecli
azure config mode arm
```

Skapa skalningsuppsättningen med hjälp av `azure vmss quick-create`-kommandot. I följande exempel skapas en Linux-skalningsuppsättning med namnet `myVMSS` med fem VM-instanser i resursgruppen med namnet `myResourceGroup`:

```azurecli
azure vmss quick-create -n myVMSS -g myResourceGroup -l westus \
    -u ops -p P@ssw0rd! \
    -C 5 -Q Canonical:UbuntuServer:16.04.0-LTS:latest
```

Följande exempel skapar en Windows-skalningsuppsättning med samma konfiguration:

```azurecli
azure vmss quick-create -n myVMSS -g myResourceGroup -l westus \
    -u ops -p P@ssw0rd! \
    -C 5 -Q MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest
```

Om du vill anpassa plats- eller bild-urn:en så kan du se över kommandona `azure location list` och `azure vm image {list-publishers|list-offers|list-skus|list|show}`.

När det här kommandot har returnerats så har skalningsuppsättningen skapats. Den här skalningsuppsättningen kommer att ha en belastningsutjämnare med NAT-regler som mappar port 50 000+i på belastningsutjämnaren till port 22 på VM i. Så när vi räknat ut FQDN för belastningsutjämnaren så kommer vi att kunna ansluta via ssh till våra VM:ar:

```bash
# (if you decide to run this as a script, please invoke using bash)

# list load balancers in the resource group we created
#
# generic syntax:
# azure network lb list -g RESOURCE-GROUP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb list -g negatvmssrg | grep negatvmssrg)
split_line=( $line )
lb_name=${split_line[1]}

# now that we have the name of the load balancer, we can show the details to find which Public IP (PIP) is 
# associated to it
#
# generic syntax:
# azure network lb show -g RESOURCE-GROUP-NAME -n LOAD-BALANCER-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb show -g negatvmssrg -n $lb_name | grep loadBalancerFrontEnd)
split_line=( $line )
pip_name=${split_line[4]}

# now that we have the name of the public IP address, we can show the details to find the FQDN
#
# generic syntax:
# azure network public-ip show -g RESOURCE-GROUP-NAME -n PIP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network public-ip show -g negatvmssrg -n $pip_name | grep FQDN)
split_line=( $line )
FQDN=${split_line[3]}

# now that we have the FQDN, we can use ssh on port 50,000+i to connect to VM i (where i is 0-indexed)
#
# example to connct via ssh into VM "0":
ssh -p 50000 negat@$FQDN
```

<!--HONumber=Dec16_HO1-->


