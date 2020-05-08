---
title: Konfigurera brand Väggs regler för tjänsten
description: Konfigurera IP-regler för att aktivera åtkomst till ett Azure Container Registry från valda offentliga IP-adresser eller adress intervall.
ms.topic: article
ms.date: 05/04/2020
ms.openlocfilehash: f6459061ca486b4bf229409e6ec1ed1bd808a474
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984621"
---
# <a name="configure-public-ip-network-rules"></a>Konfigurera regler för offentliga IP-nätverk

Ett Azure Container Registry som standard accepterar anslutningar via Internet från värdar i ett nätverk. Den här artikeln visar hur du konfigurerar behållar registret för att tillåta åtkomst från enbart vissa offentliga IP-adresser eller adress intervall. Likvärdiga steg som använder Azure CLI och Azure Portal tillhandahålls.

IP-nätverks regler konfigureras i den offentliga slut punkten för registret. IP-nätverks regler gäller inte för privata slut punkter som kon figurer ATS med [privat länk](container-registry-private-link.md)

Det finns en konfigurering av reglerna för IP-åtkomst i tjänst nivån **Premium** container Registry. Information om nivåer och gränser för register tjänster finns i [Azure Container Registry-nivåer](container-registry-skus.md).

## <a name="access-from-selected-public-network---cli"></a>Åtkomst från valt offentligt nätverk – CLI

### <a name="change-default-network-access-to-registry"></a>Ändra standard nätverks åtkomst till registret

Om du vill begränsa åtkomsten till ett valt offentligt nätverk måste du först ändra standard åtgärden för att neka åtkomst. Ersätt namnet på registret i följande [AZ ACR Update][az-acr-update] -kommando:

```azurecli
az acr update --name myContainerRegistry --default-action Deny
```

### <a name="add-network-rule-to-registry"></a>Lägg till nätverks regel i registret

Använd kommandot [AZ ACR Network-Rule Add][az-acr-network-rule-add] för att lägga till en nätverks regel i registret som tillåter åtkomst från en offentlig IP-adress eller ett intervall. Ersätt till exempel namnet på behållar registret och den offentliga IP-adressen för en virtuell dator i ett virtuellt nätverk.

```azurecli
az acr network-rule add \
  --name mycontainerregistry \
  --ip-address <public-IP-address>
```

> [!NOTE]
> När du har lagt till en regel tar det några minuter innan regeln börjar gälla.

## <a name="access-from-selected-public-network---portal"></a>Åtkomst från valt offentligt nätverk – Portal

1. I portalen navigerar du till behållar registret.
1. Under **Inställningar**väljer du **nätverk**.
1. På fliken **offentlig åtkomst** väljer du om du vill tillåta offentlig åtkomst från **valda nätverk**.
1. Under **brand vägg**anger du en offentlig IP-adress, till exempel den offentliga IP-adressen för en virtuell dator i ett virtuellt nätverk. Eller ange ett adress intervall i CIDR-notation som innehåller den virtuella datorns IP-adress.
1. Välj **Spara**.

![Konfigurera brand Väggs regel för container Registry][acr-access-selected-networks]

> [!NOTE]
> När du har lagt till en regel tar det några minuter innan regeln börjar gälla.

> [!TIP]
> Du kan också aktivera register åtkomst från en lokal klient dator eller ett IP-adressintervall. För att tillåta den här åtkomsten behöver du datorns offentliga IPv4-adress. Du hittar den här adressen genom att söka i "Vad är min IP-adress" i en webbläsare. Den aktuella klientens IPv4-adress visas också automatiskt när du konfigurerar brand Väggs inställningar på sidan **nätverk** i portalen.

## <a name="disable-public-network-access"></a>Inaktivera offentlig nätverks åtkomst

Om du vill begränsa trafiken till virtuella nätverk med hjälp av en [privat länk](container-registry-private-link.md)inaktiverar du den offentliga slut punkten i registret. Om du inaktiverar den offentliga slut punkten åsidosätts alla brand Väggs konfigurationer.

### <a name="disable-public-access---portal"></a>Inaktivera offentlig åtkomst – Portal

1. I portalen navigerar du till ditt behållar register och väljer **inställningar > nätverk**.
1. På fliken **offentlig åtkomst** , i **Tillåt offentlig åtkomst**, väljer du **inaktive rad**. Välj sedan **Spara**.

![Inaktivera offentlig åtkomst][acr-access-disabled]

## <a name="restore-default-registry-access"></a>Återställ standard register åtkomst

Uppdatera standard åtgärden om du vill återställa registret för att tillåta åtkomst som standard. 

### <a name="restore-default-registry-access---portal"></a>Återställa standard åtkomst till registret – portalen

1. I portalen navigerar du till ditt behållar register och väljer **inställningar > nätverk**.
1. Under **brand vägg**väljer du varje adress intervall och väljer sedan ikonen Ta bort.
1. På fliken **offentlig åtkomst** , i **Tillåt offentlig åtkomst**, väljer du **alla nätverk**. Välj sedan **Spara**.

![Offentlig åtkomst från alla nätverk][acr-access-all-networks]

## <a name="next-steps"></a>Nästa steg

* Information om hur du begränsar åtkomsten till ett register med en privat slut punkt i ett virtuellt nätverk finns i [Konfigurera Azures privata länk för ett Azure Container Registry](container-registry-private-link.md).
* Om du behöver konfigurera åtkomst regler för registret bakom en klient brand vägg, se [Konfigurera regler för åtkomst till ett Azure Container Registry bakom en brand vägg](container-registry-firewall-access-rules.md).

[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-network-rule-add]: /cli/azure/acr/network-rule/#az-acr-network-rule-add
[az-acr-network-rule-remove]: /cli/azure/acr/network-rule/#az-acr-network-rule-remove
[az-acr-network-rule-list]: /cli/azure/acr/network-rule/#az-acr-network-rule-list
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-update]: /cli/azure/acr#az-acr-update
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com

[acr-access-selected-networks]: ./media/container-registry-access-selected-networks/acr-access-selected-networks.png
[acr-access-disabled]: ./media/container-registry-access-selected-networks/acr-access-disabled.png
[acr-access-all-networks]: ./media/container-registry-access-selected-networks/acr-access-all-networks.png
