---
title: Självstudie – installera program på en skalningsuppsättning med Azure-mallar | Microsoft Docs
description: Läs hur du använder Azure Resource Manager-mallar för att installera program på VM-skalningsuppsättningar med det anpassade skripttillägget
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: b388e0aaec29c5b9a01e0b0a306f5486f6215092
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
ms.locfileid: "30246652"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-an-azure-template"></a>Självstudie: Installera program i VM-skalningsuppsättningar med en Azure-mall
Om du vill köra program på virtuella datorinstanser i en skalningsuppsättning, måste du först installera programkomponenter och nödvändiga filer. I en föregående självstudie fick du lära dig hur du skapar och använder en anpassad virtuell datoravbildning för att distribuera dina virtuella datorinstanser. Den här anpassade avbildningen inkluderade programinstallationer och konfigurationer. Du kan också automatisera installationen av programmen till en skalningsuppsättning efter att varje virtuell datorinstans distribueras, eller uppdatera ett program som redan körs på en skalningsuppsättning. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Installerar automatiskt program till din skalningsuppsättning
> * Använd det anpassade Azure-skripttillägget
> * Uppdatera ett program som körs på en skalningsuppsättning

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt, måste du köra Azure CLI version 2.0.29 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli).


## <a name="what-is-the-azure-custom-script-extension"></a>Vad är det anpassade Azure-skripttillägget?
Det anpassade skripttillägget laddar ner och kör skript på virtuella Azure-datorer. Det här tillägget är användbart för konfiguration efter distribution, programvaruinstallation eller andra konfigurerings-/hanteringsuppgifter. Skript kan laddas ned från Azure Storage eller GitHub, eller tillhandahållas via Azure Portal vid tilläggskörning.

Det anpassade skripttillägget integreras med Azure Resource Manager-mallar och kan också användas med Azure CLI 2.0, Azure PowerShell, Azure Portal eller REST API:t. Mer information finns i [översikten över tillägget för anpassat skript](../virtual-machines/linux/extensions-customscript.md).

Om du vill se hur det anpassade skripttillägget, skapar du en skalningsuppsättning som installerar NGINX-webbservern och matar ut värddatornamnet för skalningsuppsättningens virtuella datorinstans. Följande anpassade skripttilläggsdefinition laddar ner ett exempelskript från GitHub, installerar nödvändiga paket och skriver sedan den virtuella datorinstansens värddatornamn till en grundläggande HTML-sida.


## <a name="create-custom-script-extension-definition"></a>Skapa en anpassad skripttilläggsdefinition
När du definierar en VM-skalningsuppsättning med en Azure-mall, kan resursprovidern *Microsoft.Compute/virtualMachineScaleSets* inkludera ett avsnitt om tillägg. *extensionsProfile* går igenom vad som tillämpas till de virtuella datorinstanserna i en skalningsuppsättning. Om du vill använda det anpassade skripttillägget så anger du en utgivare för *Microsoft.Azure.Extensions* och en typ av *CustomScript*.

Egenskapen *fileUris* används för att definiera källan för installationsskripten eller paketen. Om du vill starta installationsprocessen, definieras de nödvändiga skripten i *commandToExecute*. Följande exempel definierar ett exempelskript från GitHub som installerar och konfigurerar NGINX-webbservern:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"
          ],
          "commandToExecute": "bash automate_nginx.sh"
        }
      }
    }
  ]
}
```

En komplett exempel på en Azure-mall som distribuerar en skalningsuppsättning och det anpassade skripttillägget finns i [https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json). Den här exempelmallen används i nästa avsnitt.


## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning
Vi använder exempelmallen för att skapa en skalningsuppsättning och tillämpa det anpassade skripttillägget. Skapa först en resursgrupp med [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med namnet *myResourceGroup* på platsen *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Skapa nu en VM-skalningsuppsättning med [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create). När du uppmanas, anger du ditt eget användarnamn och lösenord som används som autentiseringsuppgifter för varje virtuell datorinstans:

```azurecli-interactive
az group deployment create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy.json
```

Det tar några minuter att skapa och konfigurera alla skalningsuppsättningsresurser och virtuella datorer.

Varje virtuell datorinstans i skalningsuppsättningen laddar ned och kör skriptet från GitHub. I ett mer avancerat exempel, kan flera programkomponenter och filer installeras. Om skalningsuppsättningen skalas upp, använder de nya virtuella datorinstanserna automatiskt samma definition för anpassade skripttillägg och installerar det program som krävs.


## <a name="test-your-scale-set"></a>Testa din skalningsuppsättning
Om du vill testa webbservern hämtar du den offentliga IP-adressen för belastningsutjämnaren med [az network public-ip show](/cli/azure/network/public-ip#show). I följande exempel hämtas IP-adressen för *myScaleSetLBPublicIP* som skapas som en del av skalningsuppsättningen:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetPublicIP \
  --query [ipAddress] \
  --output tsv
```

Skriv den offentliga IP-adressen för belastningsutjämnaren i en webbläsare. Belastningsutjämnaren distribuerar trafik till en av dina VM-instanser enligt följande exempel:

![Standardwebbsida i NGINX](media/tutorial-install-apps-template/running-nginx.png)

Lämna webbläsaren öppen så att du kan se en uppdaterad version i nästa steg.


## <a name="update-app-deployment"></a>Uppdatera appdistributionen
Under livscykeln för en skalningsuppsättning, kan du behöva distribuera en uppdaterad version av ditt program. Med det anpassade skripttillägget, kan du referera till ett uppdaterat distributionsskript och sedan tillämpa tillägget till din skalningsuppsättning igen. När skalningsuppsättningen skapades i ett föregående steg, angavs *upgradePolicy som *automatisk*. Den här inställningen låter virtuella datorinstanser i skalningsuppsättningen att automatiskt uppdatera och tillämpa den senaste versionen av ditt program.

Om du vill uppdatera definitionen för ditt anpassade skripttillägg, redigerar du din mall för att referera till ett nytt installationsskript. Det nya filnamnet måste användas för att det anpassade skripttillägget ska identifiera ändringen. Det anpassade skripttillägget utvärderar inte innehållet i skriptet för att avgöra om ändringar gjorts. Följande definition använder ett uppdaterat installationsskript med *_v2* i slutet av namnet:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx_v2.sh"
          ],
          "commandToExecute": "bash automate_nginx_v2.sh"
        }
      }
    }
  ]
}
```

Tillämpa konfigurationen det anpassade skripttillägget till de virtuella datorinstanser i din skalningsuppsättning med [az group deployment create](/cli/azure/group/deployment#az_group_deployment_create). Den här *azuredeployv2.json*-mallen används för att tillämpa den uppdaterade versionen av programmet. I praktiken, redigerar du den befintliga *azuredeploy.json*-mallen så den refererar till det uppdaterade installationsskriptet, som det visas i föregående avsnitt. När du uppmanas, anger du samma användarnamn och lösenord som används när du först skapade skalningsuppsättningen:

```azurecli-interactive
az group deployment create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy_v2.json
```

Alla virtuella datorinstanser i skalningsuppsättningen uppdateras automatiskt med den senaste versionen av exempelwebbsidan. Uppdatera webbplatsen i webbläsaren om du vill se den uppdaterade versionen:

![Uppdaterad webbsida i NGINX](media/tutorial-install-apps-template/running-nginx-updated.png)


## <a name="clean-up-resources"></a>Rensa resurser
Om du vill ta bort din skalningsuppsättning och ytterligare resurser så tar du bort resursgruppen och alla dess resurser med [az group delete](/cli/azure/group#az_group_delete). Parametern `--no-wait` återför kontrollen till kommandotolken utan att vänta på att uppgiften slutförs. Parametern `--yes` bekräftar att du vill ta bort resurserna utan att tillfrågas ytterligare en gång.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Nästa steg
I den här självstudien, läste du hur du automatiskt kan installera och uppdatera program på din skalningsuppsättning med Azure-mallar:

> [!div class="checklist"]
> * Installerar automatiskt program till din skalningsuppsättning
> * Använd det anpassade Azure-skripttillägget
> * Uppdatera ett program som körs på en skalningsuppsättning

Gå vidare till nästa självstudie för att lära dig hur du automatiskt skalar din skalningsuppsättning.

> [!div class="nextstepaction"]
> [Skala dina skalningsuppsättningar automatiskt](tutorial-autoscale-template.md)
