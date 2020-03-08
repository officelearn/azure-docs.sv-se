---
title: Kund hanterad nyckel disk kryptering för Azure HDInsight
description: Den här artikeln beskriver hur du använder din egen krypterings nyckel från Azure Key Vault för att kryptera data som lagras på hanterade diskar i Azure HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: fd5308574e84ab6d2e30b9352254683b2d1d6fdd
ms.sourcegitcommit: 05b36f7e0e4ba1a821bacce53a1e3df7e510c53a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78403567"
---
# <a name="customer-managed-key-disk-encryption"></a>Kundhanterad nyckeldiskkryptering

Azure HDInsight stöder kundhanterad nyckel kryptering för data på hanterade diskar och resurs diskar som är anslutna till virtuella HDInsight-kluster datorer. Med den här funktionen kan du använda Azure Key Vault för att hantera krypterings nycklar som skyddar data i andra i HDInsight-klustren. 

Alla hanterade diskar i HDInsight skyddas med Azure Storage Service Encryption (SSE). Som standard krypteras data på dessa diskar med hjälp av Microsoft-hanterade nycklar. Om du aktiverar Kundhanterade nycklar för HDInsight, anger du krypterings nycklarna för HDInsight för att använda och hantera dessa nycklar med hjälp av Azure Key Vault.

Det här dokumentet hanterar inte data som lagras i ditt Azure Storage-konto. Mer information om Azure Storage kryptering finns i [Azure Storage kryptering av data i vila](../storage/common/storage-service-encryption.md). Dina kluster kan ha ett eller flera kopplade Azure Storage konton där krypterings nycklarna också kan hanteras av Microsoft eller kund, men krypterings tjänsten skiljer sig åt.

## <a name="introduction"></a>Introduktion

Kundhanterad nyckel kryptering är en enda stegs process som hanteras när klustret skapas utan extra kostnad. Allt du behöver göra är att registrera HDInsight som en hanterad identitet med Azure Key Vault och lägga till krypterings nyckeln när du skapar klustret.

Både resurs diskar och hanterade diskar på varje nod i klustret krypteras med en symmetrisk data krypterings nyckel (DEK). DEK skyddas med nyckel krypterings nyckeln (KEK) från ditt nyckel valv. Krypterings-och dekrypterings processerna hanteras helt av Azure HDInsight.

Om Key Vault-brandväggen är aktive rad i nyckel valvet där disk krypterings nyckeln lagras, måste den regionala HDInsight-providerns IP-adresser för den region där klustret ska distribueras läggas till i konfiguration av Key Vault-brandväggen. Detta är nödvändigt eftersom HDInsight inte är en betrodd Azure Key Vault-tjänst.

Du kan använda Azure Portal eller Azure CLI för att på ett säkert sätt rotera nycklar i nyckel valvet. När en nyckel roterar börjar HDInsight-klustret med den nya nyckeln inom några minuter. Aktivera funktionen för nyckel skydd för [mjuk borttagning](../key-vault/key-vault-ovw-soft-delete.md) för att skydda dig mot utpressnings scenarier och oavsiktlig borttagning. Nyckel valv utan denna skydds funktion stöds inte.

|Kluster typ |OS-disk (hanterad disk) |Data disk (hanterad disk) |Temporär data disk (lokal SSD) |
|---|---|---|---|
|Kafka, HBase med accelererade skrivningar|[SSE-kryptering](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#encryption)|SSE-kryptering + valfri CMK-kryptering|Valfri CMK-kryptering|
|Alla andra kluster (Spark, Interactive, Hadoop, HBase utan accelererade skrivningar)|SSE-kryptering|Ej tillämpligt|Valfri CMK-kryptering|

## <a name="get-started-with-customer-managed-keys"></a>Kom igång med Kundhanterade nycklar

Vi går igenom följande steg för att skapa ett kundhanterat nyckel aktiverat HDInsight-kluster:

1. Skapa hanterade identiteter för Azure-resurser
1. Skapa Azure Key Vault
1. Skapa nyckel
1. Skapa åtkomst princip
1. Skapa HDInsight-kluster med kundhanterad nyckel aktive rad
1. Rotera krypterings nyckeln

## <a name="create-managed-identities-for-azure-resources"></a>Skapa hanterade identiteter för Azure-resurser

Skapa en användardefinierad hanterad identitet för att autentisera till Key Vault.

Se [skapa en användardefinierad hanterad identitet](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) för vissa steg. Mer information om hur hanterade identiteter fungerar i Azure HDInsight finns i [hanterade identiteter i Azure HDInsight](hdinsight-managed-identities.md). Se till att spara resurs-ID: t för den hanterade identiteten när du lägger till det i Key Vault åtkomst principen.

## <a name="create-azure-key-vault"></a>Skapa Azure Key Vault

Skapa ett nyckelvalv. Se [skapa Azure Key Vault](../key-vault/quick-create-portal.md) för vissa steg.

HDInsight har endast stöd för Azure Key Vault. Om du har ett eget nyckel valv kan du importera dina nycklar till Azure Key Vault. Kom ihåg att nyckel valvet måste ha **mjuk borttagning** aktiverat. Mer information om hur du importerar befintliga nycklar finns på [nycklar, hemligheter och certifikat](../key-vault/about-keys-secrets-and-certificates.md).

## <a name="create-key"></a>Skapa nyckel

1. Från det nya nyckel valvet navigerar du till **inställningar** > **nycklar** >  **+ generera/importera**.

    ![Generera en ny nyckel i Azure Key Vault](./media/disk-encryption/create-new-key.png "Generera en ny nyckel i Azure Key Vault")

1. Ange ett namn och välj sedan **skapa**. Behåll standard **nyckel typen** för **RSA**.

    ![genererar nyckel namn](./media/disk-encryption/create-key.png "Generera nyckel namn")

1. När du kommer tillbaka till sidan **nycklar** väljer du den nyckel som du skapade.

    ![nyckel lista för nyckel valv](./media/disk-encryption/key-vault-key-list.png)

1. Välj version för att öppna sidan med **nyckel versionen** . När du använder din egen nyckel för kluster kryptering i HDInsight måste du ange nyckel-URI: n. Kopiera **nyckel identifieraren** och spara den någonstans tills du är redo att skapa klustret.

    ![Hämta nyckel identifierare](./media/disk-encryption/get-key-identifier.png)

## <a name="create-access-policy"></a>Skapa åtkomst princip

1. Från det nya nyckel valvet navigerar du till **inställningar** > **åtkomst principer** >  **+ Lägg till åtkomst princip**.

    ![Skapa ny princip för Azure Key Vault åtkomst](./media/disk-encryption/key-vault-access-policy.png)

1. På sidan **Lägg till åtkomst princip** anger du följande information:

    |Egenskap |Beskrivning|
    |---|---|
    |Nyckel behörigheter|Välj **Hämta**, **packa upp nyckel**och **Radbryt nyckel**.|
    |Hemliga behörigheter|Välj **Hämta**, **Ange**och **ta bort**.|
    |Välj huvud konto|Välj den användare som tilldelats den hanterade identitet som du skapade tidigare.|

    ![Ange Välj huvud konto för Azure Key Vault åtkomst princip](./media/disk-encryption/azure-portal-add-access-policy.png)

1. Välj **Lägg till**.

1. Välj **Spara**.

    ![Spara Azure Key Vault åtkomst princip](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Skapa kluster med kund hanterad nyckel disk kryptering

Nu är du redo att skapa ett nytt HDInsight-kluster. Kundhanterad nyckel kan bara tillämpas på nya kluster när klustret skapas. Det går inte att ta bort kryptering från kund hanterade nyckel kluster och kundhanterad nyckel kan inte läggas till i befintliga kluster.

### <a name="using-the-azure-portal"></a>Använda Azure Portal

Ange den fullständiga **nyckel identifieraren**, inklusive nyckel versionen, när klustret skapas. Till exempel `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Du måste också tilldela den hanterade identiteten till klustret och ange nyckel-URI: n.

![Skapa nytt kluster](./media/disk-encryption/create-cluster-portal.png)

### <a name="using-azure-cli"></a>Använda Azure CLI

I följande exempel visas hur du använder Azure CLI för att skapa ett nytt Apache Spark kluster med disk kryptering aktiverat. Mer information finns i [Azure CLI AZ HDInsight Create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create).

```azurecli
az hdinsight create -t spark -g MyResourceGroup -n MyCluster \
-p "HttpPassword1234!" --workernode-data-disks-per-node 2 \
--storage-account MyStorageAccount \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--assign-identity MyMSI
```

## <a name="rotating-the-encryption-key"></a>Rotera krypterings nyckeln

Det kan finnas scenarier där du kanske vill ändra de krypterings nycklar som används av HDInsight-klustret när det har skapats. Detta kan enkelt ske via portalen. För den här åtgärden måste klustret ha åtkomst till både den aktuella nyckeln och den avsedda nya nyckeln, annars går det inte att rotera nyckeln.

### <a name="using-the-azure-portal"></a>Använda Azure Portal

Om du vill rotera nyckeln behöver du URI för bas nyckel valvet. När du har gjort det går du till avsnittet HDInsight-kluster egenskaper i portalen och klickar på **ändra nyckel** under **URL för disk krypterings nyckel**. Ange den nya nyckel-URL: en och skicka för att rotera nyckeln.

![rotera disk krypterings nyckel](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>Använda Azure CLI

I följande exempel visas hur du roterar disk krypterings nyckeln för ett befintligt HDInsight-kluster. Mer information finns i [Azure CLI AZ HDInsight rotation-Disk-Encryption-Key](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key).

```azurecli
az hdinsight rotate-disk-encryption-key \
--encryption-key-name SparkClusterKey \
--encryption-key-version 00000000000000000000000000000000 \
--encryption-vault-uri https://MyKeyVault.vault.azure.net \
--name MyCluster \
--resource-group MyResourceGroup
```

## <a name="faq-for-customer-managed-key-encryption"></a>Vanliga frågor och svar om kund hanterad nyckel kryptering

**Hur kommer HDInsight-klustret åt mitt nyckel valv?**

HDInsight ansluter Azure Key Vault-instansen med hjälp av den hanterade identitet som du associerar med HDInsight-klustret. Den här hanterade identiteten kan skapas innan eller när klustret skapas. Du måste också bevilja åtkomst till hanterad identitet till nyckel valvet där nyckeln lagras.

**Är den här funktionen tillgänglig för alla kluster i HDInsight?**

Kundhanterad nyckel kryptering är tillgänglig för alla kluster typer förutom Spark 2,1 och 2,2.

**Kan jag använda flera nycklar för att kryptera olika diskar eller mappar?**

Nej, alla hanterade diskar och resurs diskar krypteras med samma nyckel.

**Vad händer om klustret förlorar åtkomst till nyckel valvet eller nyckeln?**

Om klustret förlorar åtkomst till nyckeln, visas varningar i Apache Ambari-portalen. I det här läget går det inte att utföra **ändrings nyckeln** . När nyckel åtkomsten har återställts försvinner Ambari-varningar och åtgärder som nyckel rotation kan utföras.

![avisering om nyckel åtkomst Ambari](./media/disk-encryption/ambari-alert.png)

**Hur kan jag återställa klustret om nycklarna tas bort?**

Eftersom enbart "mjuk borttagning"-aktiverade nycklar stöds, om nycklarna återställs i nyckel valvet, bör klustret återfå åtkomst till nycklarna. Om du vill återställa en Azure Key Vault nyckel, se [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) eller [AZ-Key Vault-Key-Recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Vilka disk typer är krypterade? Krypteras även OS-diskar/resurs diskar?**

Resurs diskar och data/hanterade diskar är krypterade. OS-diskar är inte krypterade.

**Kommer de nya noderna att stödja Kundhanterade nycklar sömlöst om ett kluster skalas upp?**

Ja. Klustret behöver åtkomst till nyckeln i nyckel valvet vid skalning. Samma nyckel används för att kryptera både hanterade diskar och resurs diskar i klustret.

**Finns kundens Kundhanterade nycklar på min plats?**

HDInsight Kundhanterade nycklar är tillgängliga i alla offentliga moln och i nationella moln.

## <a name="next-steps"></a>Nästa steg

* Mer information om Azure Key Vault finns i [Vad är Azure Key Vault](../key-vault/key-vault-overview.md).
* [Översikt över företags säkerhet i Azure HDInsight](./domain-joined/hdinsight-security-overview.md).
