---
title: Konfigurera en privat slut punkt (för hands version)
titleSuffix: Azure Machine Learning
description: Använd Azures privata länk för att få säker åtkomst till din Azure Machine Learning-arbetsyta från ett virtuellt nätverk.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 07/28/2020
ms.openlocfilehash: 9ce139131e2c6cbfd73f9160b986d9886ae4844b
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181960"
---
# <a name="configure-azure-private-link-for-an-azure-machine-learning-workspace-preview"></a>Konfigurera en privat Azure-länk för en Azure Machine Learning arbets yta (förhands granskning)

I det här dokumentet får du lära dig hur du använder en privat Azure-länk med din Azure Machine Learning-arbetsyta. 

> [!IMPORTANT]
> Att använda Azures privata länk med Azure Machine Learning-arbetsytan är för närvarande en offentlig för hands version. Den här funktionen är endast tillgänglig i regionerna **USA, östra**, **södra centrala** USA och **västra USA 2** . Den här för hands versionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med Azures privata länk kan du ansluta till din arbets yta med en privat slut punkt. Den privata slut punkten är en uppsättning privata IP-adresser i det virtuella nätverket. Du kan sedan begränsa åtkomsten till din arbets yta så att den bara sker över de privata IP-adresserna. Privat länk hjälper till att minska risken för data exfiltrering. Mer information om privata slut punkter finns i artikeln [Azure Private Link](/azure/private-link/private-link-overview) .

> [!IMPORTANT]
> Azures privata länk påverkar inte Azures kontroll plan (hanterings åtgärder) som att ta bort arbets ytan eller hantera beräknings resurser. Till exempel skapa, uppdatera eller ta bort ett beräknings mål. De här åtgärderna utförs via det offentliga Internet som normalt.
>
> Du kan stöta på problem vid försök att komma åt den privata slut punkten för din arbets yta om du använder Mozilla Firefox. Det här problemet kan vara relaterat till DNS via HTTPS i Mozilla. Vi rekommenderar att du använder Microsoft Edge av Google Chrome som en lösning.

> [!TIP]
> Azure Machine Learning beräknings instans kan användas med en arbets yta och en privat slut punkt. Den här funktionen är för närvarande en offentlig för hands version i regionerna **USA, östra**, **södra centrala** USA och **västra USA 2** .

## <a name="create-a-workspace-that-uses-a-private-endpoint"></a>Skapa en arbets yta som använder en privat slut punkt

> [!IMPORTANT]
> För närvarande stöder vi bara aktivering av en privat slut punkt när du skapar en ny Azure Machine Learning-arbetsyta.

Mallen i [https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) kan användas för att skapa en arbets yta med en privat slut punkt.

Information om hur du använder den här mallen, inklusive privata slut punkter, finns i [använda en Azure Resource Manager mall för att skapa en arbets yta för Azure Machine Learning](how-to-create-workspace-template.md).

## <a name="using-a-workspace-over-a-private-endpoint"></a>Använda en arbets yta över en privat slut punkt

Eftersom kommunikation till arbets ytan endast tillåts från det virtuella nätverket måste alla utvecklings miljöer som använder arbets ytan vara medlemmar i det virtuella nätverket. Till exempel en virtuell dator i det virtuella nätverket.

> [!IMPORTANT]
> För att undvika tillfälligt avbrott i anslutningen rekommenderar Microsoft att tömma DNS-cachen på datorer som ansluter till arbets ytan när du har aktiverat privat länk. 

Information om Azure Virtual Machines finns i Virtual Machines- [dokumentationen](/azure/virtual-machines/).


## <a name="using-azure-storage"></a>Använda Azure Storage

Om du vill skydda det Azure Storage kontot som används av din arbets yta, så Lägg det i det virtuella nätverket.

Information om hur du placerar lagrings kontot i det virtuella nätverket finns i [använda ett lagrings konto för din arbets yta](how-to-enable-virtual-network.md#use-a-storage-account-for-your-workspace).

> [!WARNING]
> Azure Machine Learning har inte stöd för att använda ett Azure Storage konto som har privat länk aktiverat.

## <a name="using-azure-key-vault"></a>Använda Azure Key Vault

Om du vill skydda Azure Key Vault som används av din arbets yta kan du antingen flytta den i det virtuella nätverket eller aktivera en privat länk.

Information om hur du placerar nyckel valvet i det virtuella nätverket finns i [använda en nyckel valv instans med din arbets yta](how-to-enable-virtual-network.md#key-vault-instance).

Information om hur du aktiverar privat länk för nyckel valvet finns i [integrera Key Vault med Azure privat länk](/azure/key-vault/private-link-service).

## <a name="using-azure-kubernetes-services"></a>Använda Azure Kubernetes Services

Om du vill skydda de Azure Kubernetes-tjänster som används av din arbets yta, så Lägg till den i ett virtuellt nätverk. Mer information finns i [använda Azure Kubernetes Services med din arbets yta](how-to-enable-virtual-network.md#aksvnet).

Azure Machine Learning har nu stöd för användning av en Azure Kubernetes-tjänst som har privat länk aktive rad.
Så här skapar du ett privat AKS-kluster Följ dokumenten [här](https://docs.microsoft.com/azure/aks/private-clusters)

## <a name="azure-container-registry"></a>Azure Container Registry

Information om hur du skyddar Azure Container Registry i det virtuella nätverket finns i [använd Azure Container Registry](how-to-enable-virtual-network.md#azure-container-registry).

> [!IMPORTANT]
> Om du använder en privat länk för din Azure Machine Learning arbets yta och lägger Azure Container Registry för din arbets yta i ett virtuellt nätverk, måste du också använda följande Azure Resource Manager-mall. Med den här mallen kan din arbets yta kommunicera med ACR via den privata länken.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "keyVaultArmId": {
      "type": "string"
      },
      "workspaceName": {
      "type": "string"
      },
      "containerRegistryArmId": {
      "type": "string"
      },
      "applicationInsightsArmId": {
      "type": "string"
      },
      "storageAccountArmId": {
      "type": "string"
      },
      "location": {
      "type": "string"
      }
  },
  "resources": [
      {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2019-11-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "identity": {
          "type": "SystemAssigned"
      },
      "sku": {
          "tier": "enterprise",
          "name": "enterprise"
      },
      "properties": {
          "sharedPrivateLinkResources":
  [{"Name":"Acr","Properties":{"PrivateLinkResourceId":"[concat(parameters('containerRegistryArmId'), '/privateLinkResources/registry')]","GroupId":"registry","RequestMessage":"Approve","Status":"Pending"}}],
          "keyVault": "[parameters('keyVaultArmId')]",
          "containerRegistry": "[parameters('containerRegistryArmId')]",
          "applicationInsights": "[parameters('applicationInsightsArmId')]",
          "storageAccount": "[parameters('storageAccountArmId')]"
      }
      }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du skyddar Azure Machine Learning-arbetsytan finns i artikeln [företags säkerhet](concept-enterprise-security.md) .