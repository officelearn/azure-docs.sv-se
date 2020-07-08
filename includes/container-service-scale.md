---
author: dlepow
ms.service: container-service
ms.topic: include
ms.date: 11/09/2018
ms.author: danlep
ms.openlocfilehash: a4af53e035929a44f74a95b8e9897cb1dc0c6d8e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "66814743"
---
[!INCLUDE [ACS deprecation](container-service-deprecation.md)]

När du har [distribuerat ett Azure Container Service-kluster](../articles/container-service/dcos-swarm/container-service-deployment.md) kanske du behöver ändra antalet agentnoder. Till exempel kan du behöva fler agenter så att du kan köra flera containerprogram eller instanser. 

Du kan ändra antalet Gent-noder i ett DC/OS-, Docker Swarm-eller Kubernetes-kluster med hjälp av Azure Portal eller Azure CLI. 

## <a name="scale-with-the-azure-portal"></a>Skala med Azure Portal

1. I [Azure Portal](https://portal.azure.com) bläddrar du till **Containertjänster** och klickar sedan på den containertjänst du vill ändra.
2. På bladet **Containertjänst** klickar du på **Agenter**.
3. In **Antal virtuella datorer** anger du önskat antal agentnoder.

    ![Skala en pool i portalen](./media/container-service-scale/container-service-scale-portal.png)

4. Spara konfigurationen genom att klicka på **Spara**.

## <a name="scale-with-the-azure-cli"></a>Skala med Azure CLI

[Installera Azure CLI](/cli/azure/install-azure-cli) och logga in på ett Azure-konto med `az login` .

### <a name="see-the-current-agent-count"></a>Visa det aktuella antalet agenter
För att se antalet agenter i klustret kör du kommandot `az acs show`. Detta visar klusterkonfigurationen. Följande kommando visar till exempel konfigurationen av containertjänsten som heter `containerservice-myACSName` i resursgruppen `myResourceGroup`:

```azurecli
az acs show -g myResourceGroup -n containerservice-myACSName
```

Kommandot returnerar antalet agenter i `Count`-värdet under `AgentPoolProfiles`.

### <a name="use-the-az-acs-scale-command"></a>Använda kommandot az acs scale
Om du vill ändra antalet agentnoder kör du kommandot `az acs scale` och anger **resursgrupp**, **namnet på containertjänsten** och önskat **nytt agentantal**. Om du använder ett lägre eller högre antal kan du skala ned eller upp.

Om du till exempel vill ändra antalet agenter i föregående kluster till 10 skriver du följande kommando:

```azurecli
az acs scale -g myResourceGroup -n containerservice-myACSName --new-agent-count 10
```

Azure CLI returnerar en JSON-sträng som representerar den nya konfigurationen av behållar tjänsten, inklusive det nya antalet agenter.

Om du vill ha fler kommandoalternativ kör du `az acs scale --help`.

## <a name="scaling-considerations"></a>Att tänka på vid skalning

* Antalet agentnoder måste vara mellan 1 och 100. 

* Din kärnkvot kan begränsa antalet agentnoder i ett kluster.

* Skalningsåtgärder för agentnoder tillämpas på en skalningsuppsättning för en virtuell Azure-dator som innehåller agentpoolen. Endast agentnoder i den privata poolen skalas i ett DC/OS-kluster med åtgärder som visas i den här artikeln.

* Beroende på vilken initierare du distribuerar i ditt kluster kan du separat skala antalet instanser i en container som körs på klustret. I exempelvis ett DC/OS-kluster kan du använda [gränssnittet i Marathon](../articles/container-service/dcos-swarm/container-service-mesos-marathon-ui.md) för att ändra antalet instanser i ett containerprogram.


## <a name="next-steps"></a>Nästa steg
* Se [fler exempel](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) på hur du använder Azure CLI-kommandon med Azure Container Service.
* Läs mer om [DC/OS-agentpooler](../articles/container-service/dcos-swarm/container-service-dcos-agents.md) i Azure Container Service.

