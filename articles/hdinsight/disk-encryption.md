---
title: Kund hanterad nyckel disk kryptering för Azure HDInsight
description: Den här artikeln beskriver hur du använder din egen krypterings nyckel från Azure Key Vault för att kryptera data som lagras på hanterade diskar i Azure HDInsight-kluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/06/2019
ms.openlocfilehash: b452cb986e6f662aeb33c2a475f18695ebc75745
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846119"
---
# <a name="customer-managed-key-disk-encryption"></a>Kundhanterad nyckeldiskkryptering

Azure HDInsight stöder Kundhanterade nycklar, även kallat Bring Your Own Key-kryptering (BYOK) för data på hanterade diskar och resurs diskar som är anslutna till virtuella HDInsight-kluster. Med den här funktionen kan du använda Azure Key Vault för att hantera krypterings nycklar som skyddar data i andra i HDInsight-klustren. Dina kluster kan ha ett eller flera kopplade Azure Storage konton där krypterings nycklarna också kan hanteras av Microsoft eller kund, men krypterings tjänsten skiljer sig åt.

Det här dokumentet hanterar inte data som lagras i ditt Azure Storage-konto. Mer information om ASE finns i [Azure Storage kryptering för data i vila](../storage/common/storage-service-encryption.md).

Alla hanterade diskar i HDInsight skyddas med Azure Storage Service Encryption (SSE). Som standard krypteras data på dessa diskar med hjälp av Microsoft-hanterade nycklar. Om du aktiverar Kundhanterade nycklar för HDInsight, anger du krypterings nycklarna för HDInsight för att använda och hantera dessa nycklar med hjälp av Azure Key Vault.

Kundhanterad nyckel kryptering är en enda stegs process som hanteras när klustret skapas utan extra kostnad. Allt du behöver göra är att registrera HDInsight som en hanterad identitet med Azure Key Vault och lägga till krypterings nyckeln när du skapar klustret.

Både resurs diskar och hanterade diskar på varje nod i klustret krypteras med en symmetrisk data krypterings nyckel (DEK). DEK skyddas med nyckel krypterings nyckeln (KEK) från ditt nyckel valv. Krypterings-och dekrypterings processerna hanteras helt av Azure HDInsight.

Du kan använda Azure Portal eller Azure CLI för att på ett säkert sätt rotera nycklar i nyckel valvet. När en nyckel roterar börjar HDInsight-klustret med den nya nyckeln inom några minuter. Aktivera nyckel skydds funktionerna "mjuk borttagning" för att skydda dig mot utpressnings scenarier och oavsiktlig borttagning. Nyckel valv utan denna skydds funktion stöds inte.

## <a name="get-started-with-customer-managed-keys"></a>Kom igång med Kundhanterade nycklar

Vi går igenom följande steg för att skapa ett kundhanterat nyckel aktiverat HDInsight-kluster:

1. Skapa hanterade identiteter för Azure-resurser
2. Installations Azure Key Vault och nycklar
3. Skapa HDInsight-kluster med kundhanterad nyckel aktive rad
4. Rotera krypterings nyckeln

## <a name="create-managed-identities-for-azure-resources"></a>Skapa hanterade identiteter för Azure-resurser

Om du vill autentisera till Key Vault skapar du en användardefinierad hanterad identitet med hjälp av [Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)eller [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Mer information om hur hanterade identiteter fungerar i Azure HDInsight finns i [hanterade identiteter i Azure HDInsight](hdinsight-managed-identities.md). Se till att spara resurs-ID: t för den hanterade identiteten när du lägger till det i Key Vault åtkomst principen.

## <a name="set-up-the-key-vault-and-keys"></a>Konfigurera Key Vault och nycklar

HDInsight har endast stöd för Azure Key Vault. Om du har ett eget nyckel valv kan du importera dina nycklar till Azure Key Vault. Kom ihåg att nycklarna måste ha "mjuk borttagning". Funktionen "mjuk borttagning" är tillgänglig i gränssnitten REST, .NET/C#, PowerShell och Azure CLI.

1. Om du vill skapa ett nytt nyckel valv följer du snabb starten för [Azure Key Vault](../key-vault/key-vault-overview.md) . Mer information om hur du importerar befintliga nycklar finns på [nycklar, hemligheter och certifikat](../key-vault/about-keys-secrets-and-certificates.md).

1. Aktivera "mjuk borttagning" i Key-valvet med hjälp av AZ-kommando för [uppdatering](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) cli.

    ```azurecli
    az keyvault update --name <Key Vault Name> --enable-soft-delete
    ```

1. Skapa nycklar.

    a. Om du vill skapa en ny nyckel väljer du **generera/importera** från menyn **nycklar** under **Inställningar**.

    ![Generera en ny nyckel i Azure Key Vault](./media/disk-encryption/create-new-key.png "Generera en ny nyckel i Azure Key Vault")

    b. Ange **alternativ** för att **generera** och ge nyckeln ett namn.

    ![genererar nyckel namn](./media/disk-encryption/create-key.png "Generera nyckel namn")

    c. Välj den nyckel som du skapade i listan över nycklar.

    ![nyckel lista för nyckel valv](./media/disk-encryption/key-vault-key-list.png)

    d. När du använder din egen nyckel för kluster kryptering i HDInsight måste du ange nyckel-URI: n. Kopiera **nyckel identifieraren** och spara den någonstans tills du är redo att skapa klustret.

    ![Hämta nyckel identifierare](./media/disk-encryption/get-key-identifier.png)

1. Lägg till hanterad identitet i åtkomst principen för nyckel valvet.

    a. Skapa en ny Azure Key Vault åtkomst princip.

    ![Skapa ny princip för Azure Key Vault åtkomst](./media/disk-encryption/add-key-vault-access-policy.png)

    b. Under **Välj huvud konto**väljer du den användare-tilldelade hanterade identitet som du har skapat.

    ![Ange Välj huvud konto för Azure Key Vault åtkomst princip](./media/disk-encryption/azure-portal-add-access-policy.png)

    c. Ange **nyckel behörigheter** för att **Hämta, ta emot** **, packa upp**och **figursätta**nyckeln.

    ![Ange nyckel behörigheter för Azure Key Vault åtkomst policy1](./media/disk-encryption/add-key-vault-access-policy-keys.png "Ange nyckel behörigheter för Azure Key Vault åtkomst policy1")

    d. Ange **hemliga behörigheter** för att **Hämta**, **Ange**och **ta bort**.

    ![Ange nyckel behörigheter för Azure Key Vault åtkomst policy2](./media/disk-encryption/add-key-vault-access-policy-secrets.png "Ange nyckel behörigheter för Azure Key Vault åtkomst policy2")

    e. Välj **Spara**.

    ![Spara Azure Key Vault åtkomst princip](./media/disk-encryption/add-key-vault-access-policy-save.png)

## <a name="create-cluster-with-customer-managed-key-disk-encryption"></a>Skapa kluster med kund hanterad nyckel disk kryptering

Nu är du redo att skapa ett nytt HDInsight-kluster. Kundhanterad nyckel kan bara tillämpas på nya kluster när klustret skapas. Det går inte att ta bort kryptering från kund hanterade nyckel kluster och kundhanterad nyckel kan inte läggas till i befintliga kluster.

### <a name="using-the-azure-portal"></a>Använda Azure Portal

Under skapandet av klustret anger du den fullständiga nyckel-URL: en, inklusive nyckel versionen. Till exempel `https://contoso-kv.vault.azure.net/keys/myClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Du måste också tilldela den hanterade identiteten till klustret och ange nyckel-URI: n.

![Skapa ett nytt kluster](./media/disk-encryption/create-cluster-portal.png)

### <a name="using-azure-cli"></a>Använda Azure CLI

I följande exempel visas hur du använder Azure CLI för att skapa ett nytt Apache Spark kluster med disk kryptering aktiverat. Mer information finns i [Azure CLI-AZ HDInsight Create](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) Documentation.

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

Om du vill rotera nyckeln måste du ha den fullständiga URL: en för den nya nyckeln (se steg 3 i [konfigurera Key Vault och nycklar](#set-up-the-key-vault-and-keys)). När du har gjort det går du till avsnittet HDInsight-kluster egenskaper i portalen och klickar på **ändra nyckel** under **URL för disk krypterings nyckel**. Ange den nya nyckel-URL: en och skicka för att rotera nyckeln.

![rotera disk krypterings nyckel](./media/disk-encryption/change-key.png)

### <a name="using-azure-cli"></a>Använda Azure CLI

I följande exempel visas hur du roterar disk krypterings nyckeln för ett befintligt HDInsight-kluster. Se [Azure CLI AZ HDInsight rotation-Disk-Encryption-Key](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-rotate-disk-encryption-key) för mer information.

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

* [Översikt över företags säkerhet i Azure HDInsight](./domain-joined/hdinsight-security-overview.md)
