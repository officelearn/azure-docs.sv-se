---
title: Snabbstart:Skapa en profil för HA-program - Azure CLI - Azure Traffic Manager
description: I den här snabbstartsartikeln beskrivs hur du skapar en Traffic Manager-profil för att skapa ett webbprogram med hög tillgänglig tillgång.
services: traffic-manager
author: rohinkoul
mnager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2019
ms.author: rohink
ms.openlocfilehash: e19850243498fc24c9a726f4603590df15f3a046
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79531523"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-cli"></a>Snabbstart: Skapa en Traffic Manager-profil för ett webbprogram med högtillgängliga med Azure CLI

I den här snabbstarten beskrivs hur du skapar en Traffic Manager-profil som ger hög tillgänglighet för din webbapp.

I den här snabbstarten ska du skapa två instanser av ett webbprogram. Var och en av dem körs i olika Azure-regioner. Du skapar Traffic Manager-profil baserat på [slutpunktsprioritet](traffic-manager-routing-methods.md#priority-traffic-routing-method). Profilen dirigerar användartrafik till den primära plats som kör webbappen. Traffic Manager övervakar kontinuerligt webbappen. Om den primära platsen inte är tillgänglig ger den automatisk redundans till säkerhetskopieringsplatsen.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) nu.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt så kräver den här självstudiekursen att du kör Azure CLI version 2.0.28 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp
Skapa en resursgrupp med [az group create](https://docs.microsoft.com/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på *eastus-platsen:*

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

## <a name="create-a-traffic-manager-profile"></a>Skapa en Traffic Manager-profil

Skapa en Traffic Manager-profil med hjälp av [az network traffic-manager-profil skapa](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-create) som dirigerar användartrafik baserat på slutpunktsprioritet.

I följande exempel ersätter **du<profile_name>** med ett unikt Traffic Manager-profilnamn.

```azurecli-interactive

az network traffic-manager profile create \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --routing-method Priority \
    --path "/" \
    --protocol HTTP \
    --unique-dns-name <profile_name> \
    --ttl 30 \
    --port 80

```

## <a name="create-web-apps"></a>Skapa webbappar

För den här snabbstarten behöver du två instanser av en webbapp som distribuerats i två olika Azure-regioner (*USA, östra* och *Europa, västra*). Var och en av dessa kommer att fungera som primär- och redundansslutpunkter för Traffic Manager.

### <a name="create-web-app-service-plans"></a>Skapa serviceplaner för webbappar
Skapa webbapptjänstplaner med [az appservice-planen skapa](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) för de två instanserna av webbprogrammet som du ska distribuera i två olika Azure-regioner.

I följande exempel ersätter **du<appspname_eastus>** och **<appspname_westeurope>** med ett unikt namn på apptjänstplan

```azurecli-interactive

az appservice plan create \
    --name <appspname_eastus> \
    --resource-group myResourceGroup \
    --location eastus \
    --sku S1

az appservice plan create \
    --name <appspname_westeurope> \
    --resource-group myResourceGroup \
    --location westeurope \
    --sku S1

```

### <a name="create-a-web-app-in-the-app-service-plan"></a>Skapa en webbapp i apptjänstplanen
Skapa två instanser som webbprogrammet med [az webapp skapar](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) i App Service-planerna i *Azure-regionerna i östra USA* och *Västeuropa.*

I följande exempel ersätter **du<app1name_eastus>** och<app2name_westeurope **>** med ett unikt appnamn och ersätter<**appspname_eastus>** och<**appspname_westeurope>** med namnet som används för att skapa App Service-planerna i föregående avsnitt.

```azurecli-interactive

az webapp create \
    --name <app1name_eastus> \
    --plan <appspname_eastus> \
    --resource-group myResourceGroup

az webapp create \
    --name <app2name_westeurope> \
    --plan <appspname_westeurope> \
    --resource-group myResourceGroup

```

## <a name="add-traffic-manager-endpoints"></a>Lägga till Traffic Manager-slutpunkter
Lägg till de två Web Apps som Traffic Manager-slutpunkter med [az-nätverkstrafikhanterarens slutpunkt skapa](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-create) i Traffic Manager-profilen enligt följande:

- Bestäm webbapp-ID och lägg till webappen som finns i *regionen Östra USA* Azure som den primära slutpunkten för att dirigera all användartrafik. 
- Bestäm webbapp-ID och lägg till webbappen som finns i Regionen Azure i *Västeuropa* som redundansslutpunkt. 

När den primära slutpunkten inte är tillgänglig dirigeras trafiken automatiskt till redundansslutpunkten.

I följande exempel ersätter **du<app1name_eastus>** och<app2name_westeurope **>** med appnamn som skapats för varje region i föregående avsnitt, ersätter<**appspname_eastus>** och<appspname_westeurope **>** med namnet som används för att skapa App Service-planerna i föregående avsnitt och **ersätter<profile_name>** med profilnamnet som användes i föregående avsnitt. 

**Slutpunkten för östra USA**

```azurecli-interactive

az webapp show \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --query id

```

Anteckna id som visas i utdata och använd i följande kommando för att lägga till slutpunkten:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 1 \
    --endpoint-status Enabled
```

**Slutpunkt för Västeuropa**

```azurecli-interactive

az webapp show \
    --name <app2name_westeurope> \
    --resource-group myResourceGroup \
    --query id

```

Anteckna id som visas i utdata och använd i följande kommando för att lägga till slutpunkten:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_westeurope> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 2 \
    --endpoint-status Enabled

```

## <a name="test-your-traffic-manager-profile"></a>Testa din Traffic Manager-profil

I det här avsnittet kontrollerar domännamnet för Traffic Manager-profilen. Du kan även konfigurera den primära slutpunkten till att inte vara tillgänglig. Slutligen ser du att webbappen fortfarande är tillgänglig. Det beror på att Traffic Manager skickar trafiken till redundansslutpunkten.

I följande exempel ersätter **du<app1name_eastus>** och<app2name_westeurope **>** med appnamn som skapats för varje region i föregående avsnitt, ersätter<**appspname_eastus>** och<appspname_westeurope **>** med namnet som används för att skapa App Service-planerna i föregående avsnitt och **ersätter<profile_name>** med profilnamnet som användes i föregående avsnitt.

### <a name="determine-the-dns-name"></a>Bestämma DNS-namnet

Bestäm DNS-namnet för Traffic Manager-profilen med hjälp av [az network traffic-manager-profil visar](https://docs.microsoft.com/cli/azure/network/traffic-manager/profile?view=azure-cli-latest#az-network-traffic-manager-profile-show).

```azurecli-interactive

az network traffic-manager profile show \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --query dnsConfig.fqdn

```

Kopiera **relativednsName-värdet.** DNS-namnet på traffic manager-profilen är *http://<* relativednsname *>.trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>Se hur Traffic Manager fungerar i praktiken
1. I en webbläsare anger du DNS-namnet på Traffic Manager-profilen (*http://<* relativednsname *>.trafficmanager.net)* för att visa webbappens standardwebbplats.

    > [!NOTE]
    > I det här snabbstartsscenariot dirigeras alla begäranden till den primära slutpunkten. Den är inställd på **Prioritet 1**.
2. Om du vill visa Failover i Traffic Manager-redundans inaktiverar du den primära platsen med hjälp av [slutpunktsuppdatering för az-nätverkstrafikhanteraren](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update).

   ```azurecli-interactive

    az network traffic-manager endpoint update \
        --name <app1name_eastus> \
        --resource-group myResourceGroup \
        --profile-name <profile_name> \
        --type azureEndpoints \
        --endpoint-status Disabled
    
   ```

3. Kopiera DNS-namnet på Traffic Manager-profilen (*http://<* relativednsname *>.trafficmanager.net*) för att visa webbplatsen i en ny webbläsarsession.
4. Kontrollera att webbappen fortfarande är tillgänglig.

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar tar du bort resursgrupper, webbprogram och alla relaterade resurser med [az-gruppborttagning](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete).

```azurecli-interactive

az group delete \
    --resource-group myResourceGroup

```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat en Traffic Manager-profil som ger hög tillgänglighet för webbprogrammet. Om du vill veta mer om att dirigera trafik kan du gå vidare till Traffic Manager-självstudierna.

> [!div class="nextstepaction"]
> [Självstudier för Traffic Manager](tutorial-traffic-manager-improve-website-response.md)
