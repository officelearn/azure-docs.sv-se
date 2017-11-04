När du har [distribuerat ett Azure Container Service-kluster](../articles/container-service/dcos-swarm/container-service-deployment.md) kanske du behöver ändra antalet agentnoder. Till exempel kan du behöva fler agenter så att du kan köra flera behållarprogram eller instanser. 

Du kan ändra antalet agentnoder i ett DC/OS-, Docker Swarm- eller Kubernetes-kluster med Azure Portal eller Azure CLI 2.0. 

## <a name="scale-with-the-azure-portal"></a>Skala med Azure Portal

1. I [Azure Portal](https://portal.azure.com) bläddrar du till **Behållartjänster** och klickar sedan på den behållartjänst du vill ändra.
2. På bladet **Behållartjänst** klickar du på **Agenter**.
3. In **Antal virtuella datorer** anger du önskat antal agentnoder.

    ![Skala en pool i portalen](./media/container-service-scale/container-service-scale-portal.png)

4. Spara konfigurationen genom att klicka på **Spara**.

## <a name="scale-with-the-azure-cli-20"></a>Skala med Azure CLI 2.0

Kontrollera att den senaste versionen av Azure CLI 2.0 är [installerad](/cli/azure/install-az-cli2) och att du är inloggad på ett Azure-konto (`az login`).

### <a name="see-the-current-agent-count"></a>Visa det aktuella antalet agenter
För att se antalet agenter i klustret kör du kommandot `az acs show`. Detta visar klusterkonfigurationen. Följande kommando visar till exempel konfigurationen av behållartjänsten som heter `containerservice-myACSName` i resursgruppen `myResourceGroup`:

```azurecli
az acs show -g myResourceGroup -n containerservice-myACSName
```

Kommandot returnerar antalet agenter i `Count`-värdet under `AgentPoolProfiles`.

### <a name="use-the-az-acs-scale-command"></a>Använda kommandot az acs scale
Om du vill ändra antalet agentnoder kör du kommandot `az acs scale` och anger **resursgrupp**, **namnet på behållartjänsten** och önskat **nytt agentantal**. Om du använder ett lägre eller högre antal kan du skala ned eller upp.

Om du till exempel vill ändra antalet agenter i föregående kluster till 10 skriver du följande kommando:

```azurecli
az acs scale -g myResourceGroup -n containerservice-myACSName --new-agent-count 10
```

Azure CLI 2.0 returnerar en JSON-sträng som representerar den nya konfigurationen av behållartjänsten, inklusive det nya agentantalet.

Om du vill ha fler kommandoalternativ kör du `az acs scale --help`.

## <a name="scaling-considerations"></a>Att tänka på vid skalning

* Antalet agentnoder måste vara mellan 1 och 100. 

* Din kärnkvot kan begränsa antalet agentnoder i ett kluster.

* Skalningsåtgärder för agentnoder tillämpas på en skalningsuppsättning för en virtuell Azure-dator som innehåller agentpoolen. Endast agentnoder i den privata poolen skalas i ett DC/OS-kluster med åtgärder som visas i den här artikeln.

* Beroende på vilken initierare du distribuerar i ditt kluster kan du separat skala antalet instanser i en behållare som körs på klustret. I exempelvis ett DC/OS-kluster kan du använda [gränssnittet i Marathon](../articles/container-service/dcos-swarm/container-service-mesos-marathon-ui.md) för att ändra antalet instanser i ett behållarprogram.

* För närvarande går det inte att autoskala agentnoder i ett behållartjänstkluster.

## <a name="next-steps"></a>Nästa steg
* Se [fler exempel](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) på att använda kommandon i Azure CLI 2.0 med Azure Container Service.
* Läs mer om [DC/OS-agentpooler](../articles/container-service/dcos-swarm/container-service-dcos-agents.md) i Azure Container Service.

