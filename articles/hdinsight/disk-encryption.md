---
title: Kundhanterad nyckeldiskkryptering för Azure HDInsight
description: I den här artikeln beskrivs hur du använder din egen krypteringsnyckel från Azure Key Vault för att kryptera data som lagras på hanterade diskar i Azure HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: c0521f384a333c3054397fb0ec7c2ab907e54f67
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80411756"
---
# <a name="customer-managed-key-disk-encryption"></a>Kundhanterad nyckeldiskkryptering

Azure HDInsight stöder kundhanterad nyckelkryptering för data på hanterade diskar och resursdiskar som är anslutna till virtuella HDInsight-klusterdatorer. Med den här funktionen kan du använda Azure Key Vault för att hantera krypteringsnycklarna som skyddar data i vila på dina HDInsight-kluster.

Alla hanterade diskar i HDInsight är skyddade med Azure Storage Service Encryption (SSE). Som standard krypteras data på dessa diskar med Microsoft-hanterade nycklar. Om du aktiverar kundhanterade nycklar för HDInsight tillhandahåller du krypteringsnycklarna för HDInsight för att använda och hantera dessa nycklar med Hjälp av Azure Key Vault.

Det här dokumentet behandlar inte data som lagras i ditt Azure Storage-konto. Mer information om Azure Storage-kryptering finns i [Azure Storage-kryptering för data i vila](../storage/common/storage-service-encryption.md). Dina kluster kan ha ett eller flera anslutna Azure Storage-konton där krypteringsnycklarna också kan hanteras av Microsoft eller hanteras av kunder, men krypteringstjänsten är annorlunda.

## <a name="introduction"></a>Introduktion

Kundhanterad nyckelkryptering är en process i ett steg som hanteras när kluster skapas utan extra kostnad. Allt du behöver göra är att registrera HDInsight som en hanterad identitet med Azure Key Vault och lägga till krypteringsnyckeln när du skapar ditt kluster.

Både resursdisk och hanterade diskar på varje nod i klustret krypteras med en symmetrisk datakrypteringsnyckel (DEK). DEK är skyddad med hjälp av nyckelkrypteringsnyckeln (KEK) från nyckelvalvet. Krypterings- och dekrypteringsprocesserna hanteras helt och hållet av Azure HDInsight.

Om nyckelvalvsbrandväggen är aktiverad i nyckelvalvet där diskkrypteringsnyckeln lagras, måste HDInsight regional Resource Provider IP-adresser för den region där klustret ska distribueras läggas till i nyckelvalvets brandväggskonfiguration. Detta är nödvändigt eftersom HDInsight inte är en betrodd Azure-nyckelvalvstjänst.

Du kan använda Azure-portalen eller Azure CLI för att på ett säkert sätt rotera nycklarna i nyckelvalvet. När en tangent roteras börjar HDInsight-klustret använda den nya nyckeln inom några minuter. Aktivera de mjuka borttagningsnyckelskyddsfunktionerna för att skydda mot ransomware-scenarier och oavsiktlig borttagning. [Soft Delete](../key-vault/key-vault-ovw-soft-delete.md) Nyckelvalv utan den här skyddsfunktionen stöds inte.

|Klustertyp |OS-disk (hanterad disk) |Datadisk (hanterad disk) |Temp-datadisk (lokal SSD) |
|---|---|---|---|
|Kafka, HBase med accelererade skriver|[SSE-kryptering](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#encryption)|SSE-kryptering + CMK-kryptering (tillval)|CMK-kryptering (tillval)|
|Alla andra kluster (Spark, Interactive, Hadoop, HBase utan accelererade skrivningar)|SSE-kryptering|Ej tillämpligt|CMK-kryptering (tillval)|

## <a name="get-started-with-customer-managed-keys"></a>Komma igång med kundhanterade nycklar

Om du vill skapa ett kundhanterat nyckelaktiverat HDInsight-kluster går vi igenom följande steg:

1. Skapa hanterade identiteter för Azure-resurser
1. Skapa Azure Key Vault
1. Skapa nyckel
1. Skapa åtkomstprincip
1. Skapa HDInsight-kluster med kundhanterad nyckel aktiverad
1. Rotera krypteringsnyckeln

## <a name="create-managed-identities-for-azure-resources"></a>Skapa hanterade identiteter för Azure-resurser

Skapa en användartilldelad hanterad identitet för att autentisera till Key Vault.

Se [Skapa en användartilldelad hanterad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) för specifika steg. Mer information om hur hanterade identiteter fungerar i Azure HDInsight finns [i Hanterade identiteter i Azure HDInsight](hdinsight-managed-identities.md). Var noga med att spara det hanterade identitetsresurs-ID:n när du lägger till det i åtkomstprincipen för Nyckelvalv.

## <a name="create-azure-key-vault"></a>Skapa Azure Key Vault

Skapa ett nyckelvalv. Se [Skapa Azure Key Vault](../key-vault/quick-create-portal.md) för specifika steg.

HDInsight stöder endast Azure Key Vault. Om du har ett eget nyckelvalv kan du importera dina nycklar till Azure Key Vault. Kom ihåg att nyckelvalvet måste ha **Mjuk borttagning** aktiverat. Mer information om hur du importerar befintliga nycklar finns i [Om nycklar, hemligheter och certifikat](../key-vault/about-keys-secrets-and-certificates.md).

## <a name="create-key"></a>Skapa nyckel

1. Från det nya nyckelvalvet navigerar du till **Inställningar** > **Keys** > **+ Generera/Importera**.

    ![Generera en ny nyckel i Azure Key Vault](./media/disk-encryption/create-new-key.png "Generera en ny nyckel i Azure Key Vault")

1. Ange ett namn och välj sedan **Skapa**. Underhåll **standardnyckeltypen** **för RSA**.

    ![genererar nyckelnamn](./media/disk-encryption/create-key.png "Generera nyckelnamn")

1. När du går tillbaka till sidan **Nycklar** väljer du den nyckel du skapade.

    ![nyckelvalvnyckellista](./media/disk-encryption/key-vault-key-list.png)

1. Välj den version som ska öppnas sidan **Nyckelversion.** När du använder din egen nyckel för HDInsight-klusterkryptering måste du ange nyckeln URI. Kopiera **nyckelidentifieraren** och spara den någonstans tills du är redo att skapa klustret.

    ![hämta nyckelidentifierare](./media/disk-encryption/get-key-identifier.png)

## <a name="create-access-policy"></a>Skapa åtkomstprincip

1. Gå till**principer** > för **inställningar** > **+ Lägg till åtkomstprincip från**det nya nyckelvalvet .

    ![Skapa ny azure key vault-åtkomstprincip](./media/disk-encryption/key-vault-access-policy.png)

1. Ange följande information på sidan **Lägg till åtkomstprincip:**

    |Egenskap |Beskrivning|
    |---|---|
    |Viktiga behörigheter|Välj **Hämta,** **Ta bort nyckel**och **radbryts.**|
    |Hemliga behörigheter|Välj **Hämta,** **Ange**och **Ta bort**.|
    |Välj huvudnamn|Välj den användartilldelade hanterade identitet som du skapade tidigare.|

    ![Ange åtkomstprincip för Välj huvudnamn för Azure Key Vault](./media/disk-encryption/azure-portal-add-access-policy.png)

1. Välj **Lägg till**.

1. Välj **Spara**.

    ![Spara åtkomstprincip för Azure Key Vault](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Skapa kluster med kryptering av kundhanterad nyckeldisk

Nu är du redo att skapa ett nytt HDInsight-kluster. Kundhanterad nyckel kan endast tillämpas på nya kluster när klustret skapas. Kryptering kan inte tas bort från kundhanterade nyckelkluster och kundhanterad nyckel kan inte läggas till i befintliga kluster.

### <a name="using-the-azure-portal"></a>Använda Azure Portal

När klustret skapas anger du den fullständiga **nyckelidentifieraren**, inklusive nyckelversionen. Till exempel `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Du måste också tilldela den hanterade identiteten till klustret och ange nyckeln URI.

![Skapa nytt kluster](./media/disk-encryption/create-cluster-portal.png)

### <a name="using-azure-cli"></a>Använda Azure CLI

I följande exempel visas hur du använder Azure CLI för att skapa ett nytt Apache Spark-kluster med diskkryptering aktiverad. Mer information finns i [Azure CLI az hdinsight create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create).

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

## <a name="rotating-the-encryption-key"></a>Rotera krypteringsnyckeln

Det kan finnas scenarier där du kanske vill ändra krypteringsnycklarna som används av HDInsight-klustret när det har skapats. Detta kan enkelt via portalen. För den här åtgärden måste klustret ha åtkomst till både den aktuella nyckeln och den avsedda nya nyckeln, annars misslyckas rotationsnyckelåtgärden.

### <a name="using-the-azure-portal"></a>Använda Azure Portal

För att rotera nyckeln behöver du basnyckelvalvet URI. När du har gjort det går du till avsnittet Egenskaper för HDInsight-klusteregenskaper i portalen och klickar på **Ändra nyckel** under URL **för diskkrypteringsnyckel**. Ange i den nya nyckeln url och skicka för att rotera nyckeln.

![rotera diskkrypteringsnyckel](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>Använda Azure CLI

I följande exempel visas hur du roterar diskkrypteringsnyckeln för ett befintligt HDInsight-kluster. Mer information finns i [Azure CLI az hdinsight rotate-disk-encryption-key](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key).

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar

Om du vill använda kundhanterade nycklar med hjälp av en Resource Manager-mall uppdaterar du mallen med följande ändringar:

1. Lägg till följande egenskap i objektet **Azuredeploy.json:**

    ```json
       "diskEncryptionProperties":
         {
                 "vaultUri": "[parameters('diskEncryptionVaultUri')]",
                  "keyName": "[parameters('diskEncryptionKeyName')]",
                  "keyVersion": "[parameters('diskEncryptionKeyVersion')]",
                   "msiResourceId": "[parameters('diskEncryptionMsiResourceId')]"
         }

1. In the **azuredeploy.parameters.json** file, add the following parameters. You can get the values of these parameters from the Key Vault URI and the managed Identity. For example, if you have the following URI and identity values,
    * Sample key vault URI: https://<KeyVault_Name>.vault.azure.net/keys/clusterkey/<Cluster_Key_Value>
    * Sample user-assigned managed identity: "/subscriptions/<subscriptionID>/resourcegroups/<ResourceGroup_Name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI_Name>

    The parameters in the **azuredeploy.parameters.json** file are:

    ```json
   "diskEncryptionVaultUri": {
            "value": "https://<KeyVault_Name>.vault.azure.net"
        },
        "diskEncryptionKeyName": {
            "value": "clusterkey"
        },
        "diskEncryptionKeyVersion": {
            "value": "<Cluster_Key_Value>"
        },
        "diskEncryptionMsiResourceId": {
            "value": "/subscriptions/<subscriptionID>/resourcegroups/<ResourceGroup_Name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI_Name>"
        }
    ```

## <a name="faq-for-customer-managed-key-encryption"></a>Vanliga frågor och svar om kryptering av kundhanterade nycklar

**Hur kommer HDInsight-klustret åt mitt nyckelvalv?**

HDInsight använder din Azure Key Vault-instans med den hanterade identitet som du associerar med HDInsight-klustret. Den här hanterade identiteten kan skapas före eller under klusterskapande. Du måste också bevilja den hanterade identitetsåtkomsten till nyckelvalvet där nyckeln lagras.

**Är den här funktionen tillgänglig för alla kluster på HDInsight?**

Kundhanterad nyckelkryptering är tillgänglig för alla klustertyper utom Spark 2.1 och 2.2.

**Kan jag använda flera nycklar för att kryptera olika diskar eller mappar?**

Nej, alla hanterade diskar och resursdiskar krypteras med samma nyckel.

**Vad händer om klustret förlorar åtkomsten till nyckelvalvet eller nyckeln?**

Om klustret förlorar åtkomsten till nyckeln visas varningar i Apache Ambari-portalen. I det här läget misslyckas åtgärden **Ändra nyckel.** När nyckelåtkomsten har återställts försvinner Ambari-varningar och åtgärder som nyckelrotation kan utföras.

![nyckelåtkomst Ambari-varning](./media/disk-encryption/ambari-alert.png)

**Hur återställer jag klustret om nycklarna tas bort?**

Eftersom endast "Soft Delete"-aktiverade nycklar stöds, om nycklarna återställs i nyckelvalvet, bör klustret återfå åtkomsten till nycklarna. Information om hur du återställer en Azure Key Vault-nyckel finns i [Ångra-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) eller [az-keyvault-key-recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Vilka disktyper krypteras? Är OS-diskar/resursdiskar också krypterade?**

Resursdiskar och data/hanterade diskar krypteras. OS-diskar är inte krypterade.

**Om ett kluster skalas upp, kommer de nya noderna att stödja kundhanterade nycklar sömlöst?**

Ja. Klustret behöver åtkomst till nyckeln i nyckelvalvet under uppskalningen. Samma nyckel används för att kryptera både hanterade diskar och resursdiskar i klustret.

**Är kundhanterade nycklar tillgängliga på min plats?**

HDInsight kundhanterade nycklar finns i alla offentliga moln och nationella moln.

## <a name="next-steps"></a>Nästa steg

* Mer information om Azure Key Vault finns i [Vad är Azure Key Vault](../key-vault/key-vault-overview.md).
* [Översikt över företagssäkerhet i Azure HDInsight](./domain-joined/hdinsight-security-overview.md).
