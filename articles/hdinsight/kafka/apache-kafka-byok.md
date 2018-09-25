---
title: Ta med din egen nyckel för Apache Kafka på Azure HDInsight (förhandsversion)
description: Den här artikeln beskriver hur du använder egna nycklar från Azure Key Vault för att kryptera data som lagras i Apache Kafka på Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 46105ee92a5c98cb8180b2499d0ad295702aac43
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953384"
---
# <a name="bring-your-own-key-for-apache-kafka-on-azure-hdinsight-preview"></a>Ta med din egen nyckel för Apache Kafka på Azure HDInsight (förhandsversion)

Azure HDInsight har stöd för ta med din egen nyckel (BYOK) för Apache Kafka. Den här funktionen kan du samla och hantera nycklar som används för att kryptera vilande data. 

Alla hanterade diskar i HDInsight är skyddade med Azure Storage Service Encryption (SSE). Som standard krypteras data på diskarna med Microsoft-hanterade nycklar. Om du aktiverar BYOK kan ange du krypteringsnyckeln för HDInsight att använda och hantera den med hjälp av Azure Key Vault. 

BYOK kryptering är en autentiseringsprocessen hanteras när klustret skapas utan extra kostnad. Allt du behöver göra är att registrera HDInsight som en hanterad identitet med Azure Key Vault och lägga till krypteringsnyckeln när du skapar klustret.

Alla meddelanden till Kafka-klustret (inklusive repliker som underhålls av Kafka) krypteras med en symmetrisk Data Datakrypteringsnyckeln (DEK). DEK skyddas med hjälp av nyckeln kryptering nyckel (KEK) från ditt nyckelvalv. Kryptering och dekryptering processer hanteras helt av Azure HDInsight. 

Du kan använda Azure portal eller Azure CLI för att på ett säkert sätt rotera nycklar i nyckelvalvet. När en nyckel roteras startar HDInsight Kafka-klustret med den nya nyckeln inom några minuter. Aktivera nyckelskydd-funktioner ”Rensa inte” och ”Mjuk borttagning” för att skydda mot utpressningstrojaner scenarier och oavsiktlig borttagning. Nycklar utan dessa funktioner stöds inte.

## <a name="get-started-with-byok"></a>Kom igång med BYOK

1. Skapa hanterade identiteter för Azure-resurser.

   För att autentisera till Key Vault behöver du skapa en Användartilldelad hanterad identitet med hjälp av den [Azure-portalen](../../active-directory/managed-service-identity/how-to-manage-ua-identity-portal.md), [Azure PowerShell](../../active-directory/managed-service-identity/how-to-manage-ua-identity-powershell.md), [Azure Resource Manager](../../active-directory/managed-service-identity/how-to-manage-ua-identity-arm.md), eller [ Azure CLI](../../active-directory/managed-service-identity/how-to-manage-ua-identity-cli.md). Medan Azure Active directory krävs för hanterade identiteter och BYOK till Kafka, Enterprise Security Package (ESP) är inte ett krav. Glöm inte att spara resurs-ID för hanterad identitet för när du lägger till den åtkomstprincip för Nyckelvalvet.

   ![Skapa användartilldelade hanterad identitet i Azure portal](./media/apache-kafka-byok/user-managed-identity-portal.png)

2. Skapa eller importera Azure Key Vault.

   HDInsight har endast stöd för Azure Key Vault. Om du har ditt eget nyckelvalv kan importera du dina nycklar i Azure Key Vault. Kom ihåg att nycklarna måste ha ”mjuk borttagning” och ”gör inte rensa” aktiverat. ”Mjuk borttagning” och ”rensa inte” funktionerna är tillgängliga via REST, .NET / C#, PowerShell och Azure CLI-gränssnitt.

   Så här skapar du ett nytt nyckelvalv i [Azure Key Vault](../../key-vault/key-vault-get-started.md) Snabbstart. Mer information om att importera befintliga nycklar finns [om nycklar, hemligheter och certifikat](../../key-vault/about-keys-secrets-and-certificates.md).

3. Lägg till hanterad identitet i nyckelvalvets åtkomstprincip.

   Skapa en ny åtkomstprincip för Azure Key Vault.

   ![Skapa ny åtkomstprincip för Azure Key Vault](./media/apache-kafka-byok/add-key-vault-access-policy.png)

   Ange **Nyckelbehörigheter** till **hämta**, **nyckelomslutning**, och **omsluta nyckel**.

   ![Ange behörigheter för Azure Key Vault åtkomstprincip](./media/apache-kafka-byok/add-key-vault-access-policy-keys.png)

   Ange **hemliga behörigheter** till **hämta**, **ange**, och **ta bort**.

   ![Ange behörigheter för Azure Key Vault åtkomstprincip](./media/apache-kafka-byok/add-key-vault-access-policy-secrets.png)

   Under **Välj huvudkonto**, Välj det användartilldelade hanterad identitet du skapade.

   ![Ange Välj huvudkonto för Azure Key Vault åtkomstprincip](./media/apache-kafka-byok/add-key-vault-access-policy-select-principal.png)

4. Skapa HDInsight-kluster

   Du är nu redo att skapa ett nytt HDInsight-kluster. BYOK kan endast tillämpas på nya kluster när klustret skapas. Kryptering kan inte tas bort från BYOK-kluster och BYOK går inte att lägga till befintliga kluster.

   ![Kafka-diskkryptering på Azure-portalen](./media/apache-kafka-byok/apache-kafka-byok-portal.png)

   När klustret skapas, ange fullständiga webbadress, inklusive nyckelns version. Till exempel `myakv.azure.com/KEK1/v1`. Du måste också tilldela den hanterade identitet till klustret och ange nyckel-URI.

## <a name="faq-for-byok-to-kafka"></a>Vanliga frågor och svar för BYOK till Kafka

**Hur åt mitt nyckelvalv av Kafka-kluster?**

   Koppla en hanterad identitet till HDInsight Kafka-klustret när klustret skapas. Den här hanterade identiteten kan skapas före eller när klustret skapas. Du måste också ge åtkomst till nyckelvalvet där nyckeln lagras hanterad identitet.

**Är den här funktionen tillgänglig för alla Kafka-kluster i HDInsight?**

   Det går bara att BYOK-kryptering för Kafka 1.1 och senare kluster.

**Kan jag ha olika nycklar för olika ämnen/partitioner?**

   Nej, alla hanterade diskar i klustret har krypterats med samma nyckel.

**Hur kan jag återställa klustret om nycklarna har tagits bort?**

   Eftersom endast ”ej permanent ta bort” aktiverat nycklar stöds om nycklarna har återställts i nyckelvalvet, ska klustret få åtkomst till nycklarna. Om du vill återställa en Azure Key Vault-nyckel, se [Restore-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey).

**Kan jag ha producent/konsument-program som arbetar med ett BYOK-kluster och ett icke-BYOK-kluster samtidigt?**

   Ja. Användning av BYOK är transparent för program med producent/konsument. Kryptering sker i OS-lagret. Inga ändringar behöver göras för befintliga producent/konsument Kafka-program.

**OS-diskar/resurs diskar krypteras också?**

   Nej. OS-diskar och diskar för resursen är inte krypterade.

**Om ett kluster skalas nya asynkrona Meddelandeköer stöder BYOK sömlöst?**

   Ja. Klustret behöver åtkomst till nyckeln i nyckelvalvet under skala upp. Samma nyckel används för att kryptera alla hanterade diskar i klustret.

**Är BYOK tillgänglig i Min plats?**

   Kafka BYOK är tillgängligt i alla offentliga moln.

## <a name="next-steps"></a>Nästa steg

* Läs mer om Azure Key Vault, [vad är Azure Key Vault](../../key-vault/key-vault-whatis.md)?
* Kom igång med Azure Key Vault, se [komma igång med Azure Key Vault](../../key-vault/key-vault-get-started.md).
