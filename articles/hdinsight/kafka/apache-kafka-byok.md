---
title: Ta med din egen nyckel för Apache Kafka på Azure HDInsight (förhandsversion)
description: Den här artikeln beskriver hur du använder egna nycklar från Azure Key Vault för att kryptera data som lagras i Apache Kafka på Azure HDInsight.
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: ce9df58e9640cab2e6ba50fce772f1e30739dc5a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62115456"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight"></a>Ta med din egen nyckel för Apache Kafka på Azure HDInsight

Azure HDInsight har stöd för ta med din egen nyckel (BYOK) för Apache Kafka. Den här funktionen kan du samla och hantera nycklar som används för att kryptera vilande data. 

Alla hanterade diskar i HDInsight är skyddade med Azure Storage Service Encryption (SSE). Som standard krypteras data på diskarna med Microsoft-hanterade nycklar. Om du aktiverar BYOK kan ange du krypteringsnyckeln för HDInsight att använda och hantera den med hjälp av Azure Key Vault. 

BYOK kryptering är en autentiseringsprocessen hanteras när klustret skapas utan extra kostnad. Allt du behöver göra är att registrera HDInsight som en hanterad identitet med Azure Key Vault och lägga till krypteringsnyckeln när du skapar klustret.

Alla meddelanden till Kafka-klustret (inklusive repliker som underhålls av Kafka) krypteras med en symmetrisk Data Datakrypteringsnyckeln (DEK). DEK skyddas med hjälp av nyckeln kryptering nyckel (KEK) från ditt nyckelvalv. Kryptering och dekryptering processer hanteras helt av Azure HDInsight. 

Du kan använda Azure portal eller Azure CLI för att på ett säkert sätt rotera nycklar i nyckelvalvet. När en nyckel roteras startar HDInsight Kafka-klustret med den nya nyckeln inom några minuter. Aktivera ”mjuk borttagning” nyckelskydd funktioner som skyddar mot utpressningstrojaner scenarier och oavsiktlig borttagning. Nyckelvalv utan den här funktionen för skydd inte stöds.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="get-started-with-byok"></a>Kom igång med BYOK
Skapa en BYOK aktiverad Kafka-kluster, vi går igenom följande steg:
1. Skapa hanterade identiteter för Azure-resurser
2. Konfigurera Azure Key Vault och nycklar
3. Skapa HDInsight Kafka-kluster med BYOK aktiverad
4. Rotera krypteringsnyckeln

## <a name="create-managed-identities-for-azure-resources"></a>Skapa hanterade identiteter för Azure-resurser

   För att autentisera till Key Vault behöver du skapa en Användartilldelad hanterad identitet med hjälp av den [Azure-portalen](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md), eller [ Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Mer information om hur hanterade identiteter arbete i Azure HDInsight finns i [hanterade identiteter i Azure HDInsight](../hdinsight-managed-identities.md). Medan Azure Active directory krävs för hanterade identiteter och BYOK till Kafka, Enterprise Security Package (ESP) är inte ett krav. Glöm inte att spara resurs-ID för hanterad identitet för när du lägger till den åtkomstprincip för Nyckelvalvet.

   ![Skapa användartilldelade hanterad identitet i Azure portal](./media/apache-kafka-byok/user-managed-identity-portal.png)

## <a name="setup-the-key-vault-and-keys"></a>Konfigurera Key Vault och nycklar

   HDInsight har endast stöd för Azure Key Vault. Om du har ditt eget nyckelvalv kan importera du dina nycklar i Azure Key Vault. Kom ihåg att nycklarna måste ha ”mjuk borttagning”. ”Mjuk borttagning”-funktionen är tillgänglig via REST, .NET /C#, PowerShell och Azure CLI-gränssnitt.

   1. Så här skapar du ett nytt nyckelvalv i [Azure Key Vault](../../key-vault/key-vault-overview.md) Snabbstart. Mer information om att importera befintliga nycklar finns [om nycklar, hemligheter och certifikat](../../key-vault/about-keys-secrets-and-certificates.md).

   2. Aktivera ”mjuk borttagning” på key vault med hjälp av den [az keyvault update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) cli-kommando.
        ```Azure CLI az keyvault update --name <Key Vault Name> --enable-soft-delete
        ```

   3. Create keys

        a. To create a new key, select **Generate/Import** from the **Keys** menu under **Settings**.

        ![Generate a new key in Azure Key Vault](./media/apache-kafka-byok/kafka-create-new-key.png)

        b. Set **Options** to **Generate** and give the key a name.

        ![Generate a new key in Azure Key Vault](./media/apache-kafka-byok/kafka-create-a-key.png)

        c. Select the key you created from the list of keys.

        ![Azure Key Vault key list](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

        d. When you use your own key for Kafka cluster encryption, you need to provide the key URI. Copy the **Key identifier** and save it somewhere until you're ready to create your cluster.

        ![Copy key identifier](./media/apache-kafka-byok/kafka-get-key-identifier.png)
   
    4. Add managed identity to the key vault access policy.

        a. Create a new Azure Key Vault access policy.

        ![Create new Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy.png)

        b. Under **Select Principal**, choose the user-assigned managed identity you created.

        ![Set Select Principal for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

        c. Set **Key Permissions** to **Get**, **Unwrap Key**, and **Wrap Key**.

        ![Set Key Permissions for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png)

        d. Set **Secret Permissions** to **Get**, **Set**, and **Delete**.

        ![Set Key Permissions for Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png)

        e. Click on **Save**. 

        ![Save Azure Key Vault access policy](./media/apache-kafka-byok/add-key-vault-access-policy-save.png)

## Create HDInsight cluster

   You're now ready to create a new HDInsight cluster. BYOK can only be applied to new clusters during cluster creation. Encryption can't be removed from BYOK clusters, and BYOK can't be added to existing clusters.

   ![Kafka disk encryption in Azure portal](./media/apache-kafka-byok/apache-kafka-byok-portal.png)

   During cluster creation, provide the full key URL, including the key version. For example, `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. You also need to assign the managed identity to the cluster and provide the key URI.

## Rotating the Encryption key
   There might be scenarios where you might want to change the encryption keys used by the Kafka cluster after it has been created. This can be easily via the portal. For this operation, the cluster must have access to both the current key and the intended new key, otherwise the rotate key operation will fail.

   To rotate the key, you must have the full url of the new key (See Step 3 of [Setup the Key Vault and Keys](#setup-the-key-vault-and-keys)). Once you have that, go to the Kafka cluster properties section in the portal and click on **Change Key** under **Disk Encryption Key URL**. Enter in the new key url and submit to rotate the key.

   ![Kafka rotate disk encryption key](./media/apache-kafka-byok/kafka-change-key.png)

## FAQ for BYOK to Apache Kafka

**How does the Kafka cluster access my key vault?**

   Associate a managed identity with the HDInsight Kafka cluster during cluster creation. This managed identity can be created before or during cluster creation. You also need to grant the managed identity access to the key vault where the key is stored.

**Is this feature available for all Kafka clusters on HDInsight?**

   BYOK encryption is only possible for Kafka 1.1 and above clusters.

**Can I have different keys for different topics/partitions?**

   No, all managed disks in the cluster are encrypted by the same key.

**What happens if the cluster loses access to the key vault or the key?**
   If the cluster loses access to the key, warnings will be shown in the Ambari portal. In this state, the **Change Key** operation will fail. Once key access is restored, ambari warnings will go away and operations such as key rotation can be successfully performed.

   ![Kafka key access ambari alert](./media/apache-kafka-byok/kafka-byok-ambari-alert.png)

**How can I recover the cluster if the keys are deleted?**

   Since only “Soft Delete” enabled keys are supported, if the keys are recovered in the key vault, the cluster should regain access to the keys. To recover an Azure Key Vault key, see [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) or [az-keyvault-key-recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Can I have producer/consumer applications working with a BYOK cluster and a non-BYOK cluster simultaneously?**

   Yes. The use of BYOK is transparent to producer/consumer applications. Encryption happens at the OS layer. No changes need to be made to existing producer/consumer Kafka applications.

**Are OS disks/Resource disks also encrypted?**

   No. OS disks and Resource disks are not encrypted.

**If a cluster is scaled up, will the new brokers support BYOK seamlessly?**

   Yes. The cluster needs access to the key in the key vault during scale up. The same key is used to encrypt all managed disks in the cluster.

**Is BYOK available in my location?**

   Kafka BYOK is available in all public clouds.

## Next steps

* For more information about Azure Key Vault, see [What is Azure Key Vault](../../key-vault/key-vault-whatis.md)?
* To get started with Azure Key Vault, see [Getting Started with Azure Key Vault](../../key-vault/key-vault-overview.md).
