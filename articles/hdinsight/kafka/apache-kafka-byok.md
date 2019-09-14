---
title: Ta med din egen nyckel för Apache Kafka på Azure HDInsight
description: Den här artikeln beskriver hur du använder din egen nyckel från Azure Key Vault för att kryptera data som lagras i Apache Kafka på Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 09cca0eda9a82b1605323678d7442700dc1a2292
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2019
ms.locfileid: "70960504"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight"></a>Ta med din egen nyckel för Apache Kafka på Azure HDInsight

Azure HDInsight innehåller Bring Your Own Key (BYOK) stöd för Apache Kafka. Med den här funktionen kan du äga och hantera nycklarna som används för att kryptera data i vila.

Alla hanterade diskar i HDInsight skyddas med Azure Storage Service Encryption (SSE). Som standard krypteras data på dessa diskar med hjälp av Microsoft-hanterade nycklar. Om du aktiverar BYOK anger du krypterings nyckeln för HDInsight för att använda och hantera den med hjälp av Azure Key Vault.

BYOK-kryptering är en enda steg-process som hanteras när klustret skapas utan extra kostnad. Allt du behöver göra är att registrera HDInsight som en hanterad identitet med Azure Key Vault och lägga till krypterings nyckeln när du skapar klustret.

Alla meddelanden till Kafka-klustret (inklusive repliker som underhålls av Kafka) krypteras med en symmetrisk data krypterings nyckel (DEK). DEK skyddas med nyckel krypterings nyckeln (KEK) från ditt nyckel valv. Krypterings-och dekrypterings processerna hanteras helt av Azure HDInsight. 

Du kan använda Azure Portal eller Azure CLI för att på ett säkert sätt rotera nycklar i nyckel valvet. När en nyckel roterar, börjar HDInsight Kafka-klustret att använda den nya nyckeln inom några minuter. Aktivera nyckel skydds funktionerna "mjuk borttagning" för att skydda dig mot utpressnings scenarier och oavsiktlig borttagning. Nyckel valv utan denna skydds funktion stöds inte.

## <a name="get-started-with-byok"></a>Kom igång med BYOK
Vi kommer att gå igenom följande steg för att skapa ett BYOK-aktiverat Kafka-kluster:
1. Skapa hanterade identiteter för Azure-resurser
2. Installations Azure Key Vault och nycklar
3. Skapa HDInsight Kafka-kluster med BYOK aktiverat
4. Rotera krypterings nyckeln

## <a name="create-managed-identities-for-azure-resources"></a>Skapa hanterade identiteter för Azure-resurser

   Om du vill autentisera till Key Vault skapar du en användardefinierad hanterad identitet med hjälp av [Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md), [Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)eller [Azure CLI](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md). Mer information om hur hanterade identiteter fungerar i Azure HDInsight finns i [hanterade identiteter i Azure HDInsight](../hdinsight-managed-identities.md). Även om Azure Active Directory krävs för hanterade identiteter och BYOK till Kafka, är Enterprise Security Package (ESP) inte ett krav. Se till att spara resurs-ID: t för den hanterade identiteten när du lägger till det i Key Vault åtkomst principen.

   ![Skapa användardefinierad hanterad identitet i Azure Portal](./media/apache-kafka-byok/user-managed-identity-portal.png)

## <a name="setup-the-key-vault-and-keys"></a>Konfigurera Key Vault och nycklar

   HDInsight har endast stöd för Azure Key Vault. Om du har ett eget nyckel valv kan du importera dina nycklar till Azure Key Vault. Kom ihåg att nycklarna måste ha "mjuk borttagning". Funktionen "mjuk borttagning" är tillgänglig i gränssnitten REST, .NET/C#, PowerShell och Azure CLI.

   1. Om du vill skapa ett nytt nyckel valv följer du snabb starten för [Azure Key Vault](../../key-vault/key-vault-overview.md) . Mer information om hur du importerar befintliga nycklar finns på [nycklar, hemligheter och certifikat](../../key-vault/about-keys-secrets-and-certificates.md).

   2. Aktivera "mjuk borttagning" i Key-valvet med hjälp av AZ-kommando för [uppdatering](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) cli.
        ```Azure CLI
        az keyvault update --name <Key Vault Name> --enable-soft-delete
        ```

   3. Skapa nycklar

        a. Om du vill skapa en ny nyckel väljer du **generera/importera** från menyn **nycklar** under **Inställningar**.

        ![Generera en ny nyckel i Azure Key Vault](./media/apache-kafka-byok/kafka-create-new-key.png "Generera en ny nyckel i Azure Key Vault")

        b. Ange **alternativ** för att **generera** och ge nyckeln ett namn.

        ![Generera nyckel namn](./media/apache-kafka-byok/apache-kafka-create-key.png "Generera nyckel namn")

        c. Välj den nyckel som du skapade i listan över nycklar.

        ![Azure Key Vault nyckel lista](./media/apache-kafka-byok/kafka-key-vault-key-list.png)

        d. När du använder din egen nyckel för Kafka-kluster kryptering måste du ange nyckel-URI: n. Kopiera **nyckel identifieraren** och spara den någonstans tills du är redo att skapa klustret.

        ![Kopiera nyckel identifierare](./media/apache-kafka-byok/kafka-get-key-identifier.png)
   
    4. Lägg till hanterad identitet i åtkomst principen för nyckel valvet.

        a. Skapa en ny Azure Key Vault åtkomst princip.

        ![Skapa ny princip för Azure Key Vault åtkomst](./media/apache-kafka-byok/add-key-vault-access-policy.png)

        b. Under **Välj huvud konto**väljer du den användare-tilldelade hanterade identitet som du har skapat.

        ![Ange Välj huvud konto för Azure Key Vault åtkomst princip](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

        c. Ange **nyckel behörigheter** för att **Hämta, ta emot** **, packa upp**och **figursätta**nyckeln.

        ![Ange nyckel behörigheter för Azure Key Vault åtkomst policy1](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png "Ange nyckel behörigheter för Azure Key Vault åtkomst policy1")

        d. Ange **hemliga behörigheter** för att **Hämta**, **Ange**och **ta bort**.

        ![Ange nyckel behörigheter för Azure Key Vault åtkomst policy2](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png "Ange nyckel behörigheter för Azure Key Vault åtkomst policy2")

        e. Klicka på **Spara**. 

        ![Spara Azure Key Vault åtkomst princip](./media/apache-kafka-byok/add-key-vault-access-policy-save.png)

## <a name="create-hdinsight-cluster"></a>Skapa HDInsight-kluster

   Nu är du redo att skapa ett nytt HDInsight-kluster. BYOK kan bara tillämpas på nya kluster när klustret skapas. Det går inte att ta bort kryptering från BYOK-kluster och BYOK kan inte läggas till i befintliga kluster.

   ![Kafka disk kryptering i Azure Portal](./media/apache-kafka-byok/apache-kafka-byok-portal.png)

   Under skapandet av klustret anger du den fullständiga nyckel-URL: en, inklusive nyckel versionen. Till exempel `https://contoso-kv.vault.azure.net/keys/kafkaClusterKey/46ab702136bc4b229f8b10e8c2997fa4`. Du måste också tilldela den hanterade identiteten till klustret och ange nyckel-URI: n.

## <a name="rotating-the-encryption-key"></a>Rotera krypterings nyckeln
   Det kan finnas scenarier där du kanske vill ändra de krypterings nycklar som används av Kafka-klustret när det har skapats. Detta kan enkelt ske via portalen. För den här åtgärden måste klustret ha åtkomst till både den aktuella nyckeln och den avsedda nya nyckeln, annars går det inte att rotera nyckeln.

   Om du vill rotera nyckeln måste du ha den fullständiga URL: en för den nya nyckeln (se steg 3 i [konfigurera Key Vault och nycklar](#setup-the-key-vault-and-keys)). När du har gjort det går du till avsnittet Kafka-kluster egenskaper i portalen och klickar på **ändra nyckel** under **URL för disk krypterings nyckel**. Ange den nya nyckel-URL: en och skicka för att rotera nyckeln.

   ![Kafka rotera disk krypterings nyckel](./media/apache-kafka-byok/apache-kafka-change-key.png)

## <a name="faq-for-byok-to-apache-kafka"></a>Vanliga frågor och svar om BYOK till Apache Kafka

**Hur kommer Kafka-klustret åt mitt nyckel valv?**

   Koppla en hanterad identitet till HDInsight Kafka-klustret när klustret skapas. Den här hanterade identiteten kan skapas innan eller när klustret skapas. Du måste också bevilja åtkomst till hanterad identitet till nyckel valvet där nyckeln lagras.

**Är den här funktionen tillgänglig för alla Kafka-kluster i HDInsight?**

   BYOK-kryptering är bara möjlig för Kafka 1,1 och senare kluster.

**Kan jag ha olika nycklar för olika ämnen/partitioner?**

   Nej, alla hanterade diskar i klustret krypteras med samma nyckel.

**Vad händer om klustret förlorar åtkomst till nyckel valvet eller nyckeln?**
Om klustret förlorar åtkomst till nyckeln, visas varningar i Apache Ambari-portalen. I det här läget går det inte att utföra **ändrings nyckeln** . När nyckel åtkomsten har återställts försvinner Ambari-varningar och åtgärder som nyckel rotation kan utföras.

   ![Kafka Key Access Ambari-avisering](./media/apache-kafka-byok/kafka-byok-ambari-alert.png)

**Hur kan jag återställa klustret om nycklarna tas bort?**

   Eftersom enbart "mjuk borttagning"-aktiverade nycklar stöds, om nycklarna återställs i nyckel valvet, bör klustret återfå åtkomst till nycklarna. Om du vill återställa en Azure Key Vault nyckel, se [Undo-AzKeyVaultKeyRemoval](/powershell/module/az.keyvault/Undo-AzKeyVaultKeyRemoval) eller [AZ-Key Vault-Key-Recover](/cli/azure/keyvault/key?view=azure-cli-latest#az-keyvault-key-recover).

**Kan jag ha producent-/konsument program som arbetar med ett BYOK-kluster och ett icke-BYOK-kluster samtidigt?**

   Ja. Användningen av BYOK är transparent för tillverkare/konsument program. Kryptering sker i operativ system skiktet. Inga ändringar behöver göras i befintliga tillverkare/konsumenter Kafka-program.

**Krypteras även OS-diskar/resurs diskar?**

   Nej. OS-diskar och resurs diskar är inte krypterade.

**Kommer de nya utjämnare att stödja BYOK sömlöst om ett kluster skalas upp?**

   Ja. Klustret behöver åtkomst till nyckeln i nyckel valvet vid skalning. Samma nyckel används för att kryptera alla hanterade diskar i klustret.

**Är BYOK tillgängligt på min plats?**

   Kafka BYOK är tillgängligt i alla offentliga moln.

## <a name="next-steps"></a>Nästa steg

* Mer information om Azure Key Vault finns i [Vad är Azure Key Vault](../../key-vault/key-vault-whatis.md)?
* För att komma igång med Azure Key Vault, se [komma igång med Azure Key Vault](../../key-vault/key-vault-overview.md).
