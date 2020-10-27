---
title: Azure HDInsight-kryptering under överföring
description: Lär dig mer om säkerhetsfunktioner för att tillhandahålla kryptering vid överföring av ditt Azure HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: 85382ecd627ec8afc63a85de0debd98f94a89849
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2020
ms.locfileid: "92544893"
---
# <a name="ipsec-encryption-in-transit-for-azure-hdinsight"></a>IPSec-kryptering i överföring för Azure HDInsight

I den här artikeln beskrivs implementeringen av kryptering i överföring för kommunikation mellan Azure HDInsight-klusternoder.

> [!Note]
> Kryptering under överföring är för närvarande aktiverat för följande regioner: östra USA, södra centrala USA, västra 2; USA. 

## <a name="background"></a>Bakgrund

Azure HDInsight erbjuder en mängd olika säkerhetsfunktioner för att skydda dina företags data. Dessa lösningar är grupperade under pelaren för perimeter säkerhet, autentisering, auktorisering, granskning, kryptering och efterlevnad. Kryptering kan tillämpas på data både i vila och under överföring.

Kryptering i vila omfattas av kryptering på Server sidan på Azure Storage-konton, samt disk kryptering på de virtuella Azure-datorerna som ingår i ditt HDInsight-kluster.

Kryptering av data i överföring i HDInsight uppnås med [Transport Layer Security (TLS)](../transport-layer-security.md) för accssing av kluster-gatewayer och [Internet Protocol säkerhet (IPSec)](https://en.wikipedia.org/wiki/IPsec) mellan klusternoder. IPSec kan aktive ras mellan alla Head-noder, arbetsnoder, Edge-noder och Zookeeper-noder. Den är inte aktive rad för trafik mellan gateway [-eller ID Broker-](./identity-broker.md) noder som är Windows-baserade virtuella datorer och andra Linux-baserade noder i klustret.

## <a name="enable-encryption-in-transit"></a>Aktivera kryptering under överföring

### <a name="azure-portal"></a>Azure Portal

Gör så här om du vill skapa ett nytt kluster med kryptering under överföring aktiverat med Azure Portal:

1. Påbörja processen för att skapa ett vanligt kluster. Se [skapa Linux-baserade kluster i HDInsight med hjälp av Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) för att skapa första kluster.
1. Slutför flikarna **grundläggande** och **lagring** . Fortsätt till fliken **säkerhet + nätverk** .

    :::image type="content" source="media/encryption-in-transit/create-cluster-security-networking-tab.png" alt-text="Fliken Skapa kluster – säkerhet och nätverk.":::

1. På fliken **säkerhet + nätverk** klickar du på kryss rutan **Aktivera kryptering i överföring** .

    :::image type="content" source="media/encryption-in-transit/enable-encryption-in-transit.png" alt-text="Fliken Skapa kluster – säkerhet och nätverk.":::

### <a name="create-a-cluster-with-encryption-in-transit-enabled-through-the-azure-cli"></a>Skapa ett kluster med kryptering under överföring aktiverat via Azure CLI

Kryptering vid överföring aktive ras med `isEncryptionInTransitEnabled` egenskapen.

Du kan [Ladda ned en exempel mall och parameter fil](https://github.com/Azure-Samples/hdinsight-enterprise-security). Innan du använder mallen och kod avsnittet för Azure CLI nedan ersätter du följande plats hållare med rätt värden:

| Platshållare | Beskrivning |
|---|---|
| `<SUBSCRIPTION_ID>` | ID för din Azure-prenumeration |
| `<RESOURCE_GROUP>` | Den resurs grupp där du vill att det nya klustret och lagrings kontot ska skapas. |
| `<STORAGEACCOUNTNAME>` | Det befintliga lagrings kontot som ska användas med klustret. Namnet ska ha formatet `ACCOUNTNAME.blob.core.windows.net` |
| `<CLUSTERNAME>` | Namnet på ditt HDInsight-kluster. |
| `<PASSWORD>` | Ditt lösen ord för att logga in på klustret med SSH och Ambari-instrumentpanelen. |
| `<VNET_NAME>` | Det virtuella nätverk där klustret ska distribueras. |

Kodfragmentet nedan visar följande inledande steg:

1. Loggar in på ditt Azure-konto.
1. Anger den aktiva prenumeration där åtgärderna för att skapa ska utföras.
1. Skapar en ny resurs grupp för de nya distributions aktiviteterna.
1. Distribuera mallen för att skapa ett nytt kluster.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus2

az group deployment create --name HDInsightEnterpriseSecDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-enterprise-security.json \
    --parameters parameters.json
```

## <a name="next-steps"></a>Nästa steg

* [Översikt över företags säkerhet i Azure HDInsight](hdinsight-security-overview.md)
* [Synkronisera Azure Active Directory användare till ett HDInsight-kluster](../disk-encryption.md).