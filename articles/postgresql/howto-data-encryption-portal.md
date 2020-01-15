---
title: Data kryptering för Azure Database for PostgreSQL enskild server med hjälp av portalen
description: Lär dig hur du konfigurerar och hanterar data kryptering för Azure Database for PostgreSQL enskild server med Azure Portal.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: c836139047b83220e571842b6f365ab568f81e93
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941627"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-using-portal"></a>Data kryptering för Azure Database for PostgreSQL enskild server med hjälp av portalen

I den här artikeln får du lära dig hur du konfigurerar och hanterar för att använda Azure Portal för att konfigurera data kryptering för Azure Database for PostgreSQL enskild server.

## <a name="prerequisites-for-cli"></a>Krav för CLI

* Du måste ha en Azure-prenumeration och vara administratör för den prenumerationen.
* Skapa en Azure Key Vault och nyckel som ska användas för kundhanterad nyckel.
* Nyckel valvet måste ha följande egenskap för att kunna användas som kundhanterad nyckel:
  * [Mjuk borttagning](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Rensa skyddad](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Nyckeln måste ha följande attribut för att kunna användas med kundhanterad nyckel.
  * Inget förfallo datum
  * Inte inaktiverat
  * Kan utföra _Get_-, _wrap_-och _unwrap Key_ -åtgärder

## <a name="setting-the-right-permissions-for-key-operations"></a>Ange rätt behörigheter för viktiga åtgärder

1. Välj **åtkomst principer**på Azure Key Vault och **Lägg sedan till åtkomst princip**.

   ![Översikt över åtkomst princip](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Under **nyckel behörigheter**och välj **Hämta**, **Radbryt**, **packa** upp och **huvudobjektet**, vilket är namnet på postgresql-servern. Om ditt Server huvud namn inte finns i listan över befintliga huvud konton, måste du registrera det genom att försöka konfigurera data kryptering för första gången. det kommer att Miss lyckas.  

   ![Översikt över åtkomst princip](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. **Spara** inställningarna.

## <a name="setting-data-encryption-for-azure-database-for-postgresql-single-server"></a>Ange data kryptering för Azure Database for PostgreSQL enskild server

1. På **Azure Database for PostgreSQL**väljer du **data kryptering** för att ställa in kundhanterad nyckel konfiguration.

   ![Ange data kryptering](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Du kan antingen välja en **Key Vault** och **nyckel** par eller skicka en **nyckel identifierare**.

   ![Ange Key Vault](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. **Spara** inställningarna.

4. För att se till att alla filer (inklusive **temporära filer**) är fullständigt **krypterade måste du** **starta om** servern.

## <a name="restoring-or-creating-replica-of-the-server-which-has-data-encryption-enabled"></a>Återställa eller skapa en replik av servern, som har data kryptering aktiverat

När en Azure Database for PostgreSQL enskild server är krypterad med kundens hanterade nyckel lagrad i Key Vault, kommer alla nyligen skapade kopior av servern antingen med lokal eller geo-återställning eller en replik (lokal/över region) åtgärd. För en krypterad PostgreSQL-Server kan du följa stegen nedan för att skapa en krypterad återställd Server.

1. Välj **Översikt**på servern och välj sedan **Återställ**.

   ![Starta-återställning](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Om du använder en aktive rad Server, väljer du **replikering**under rubriken **Inställningar** , som du ser här:

   ![Initiera-replik](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. När återställningen har slutförts är den nya servern som skapas data krypterad med den primära serverns nyckel. Funktionerna och alternativen på-servern är dock inaktiverade och servern har marker ATS i ett **otillgängligt** tillstånd. Det här beteendet är utformat för att förhindra all data manipulering eftersom den nya serverns identitet fortfarande har fått behörighet att komma åt Key Vault.

   ![Markera servern otillgänglig](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. För att kunna åtgärda otillgängligt läge måste du verifiera om nyckeln på den återställda servern. Välj fönstret **data kryptering** och sedan knappen verifiera om **nyckel** .

   > [!NOTE]
   > Det första försöket att validera kommer att Miss lyckas eftersom den nya serverns tjänst huvud namn måste ges åtkomst till nyckel valvet. Om du vill generera tjänstens huvud namn väljer du **revalidate Key**, vilket ger upphov till fel men genererar tjänstens huvud namn. Därefter läser du stegen [i avsnitt 2](#setting-the-right-permissions-for-key-operations) ovan.

   ![verifiera Server](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Du måste ge åtkomst till den nya servern till Key Vault.

4. När du har registrerat tjänstens huvud namn måste du verifiera om nyckeln igen och servern återupptar sin normala funktion.

   ![Den normala servern har återställts](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>Nästa steg

 Mer information om data kryptering finns i [Vad är Azure Data Encryption](concepts-data-encryption-postgresql.md).
