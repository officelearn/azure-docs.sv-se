---
title: Data kryptering för Azure Database for PostgreSQL enskild server med hjälp av portalen
description: Lär dig hur du konfigurerar och hanterar data kryptering för din Azure Database for PostgreSQL enskild server med Azure Portal
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: 51c99ca0788900397c922e31e44f121a7ae9caa6
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75904268"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-using-portal"></a>Data kryptering för Azure Database for PostgreSQL enskild server med hjälp av portalen

I den här artikeln får du lära dig hur du konfigurerar och hanterar för att använda Azure Portal för att konfigurera data kryptering för Azure Database for PostgreSQL enskild server.

## <a name="prerequisites-for-powershell"></a>Krav för PowerShell

* Du måste ha en Azure-prenumeration och vara administratör för den prenumerationen.
* Du måste ha Azure PowerShell installerat och igång.
* Skapa en Azure Key Vault och nyckel som ska användas för kundhanterad nyckel.
* Nyckel valvet måste ha följande egenskap för att kunna användas som kundhanterad nyckel
    * [Mjuk borttagning](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)

        ```azurecli-interactive
        az resource update --id $(az keyvault show --name \ <key_valut_name> -test -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
        ```
    
    * [Rensa skyddad](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)

        ```azurecli-interactive
        az keyvault update --name <key_valut_name> --resource-group <resource_group_name>  --enable-purge-protection true
        ```

* Nyckeln måste ha följande attribut för att kunna användas med kundhanterad nyckel.
    * Inget förfallo datum
    * Inte inaktiverat
    * Kan utföra get-, wrap-och unwrap Key-åtgärder

## <a name="setting-the-right-permissions-for-key-operations"></a>Ange rätt behörigheter för viktiga åtgärder

1. På Azure Key Vault väljer du **åtkomst principer** och **lägger till åtkomst princip** 

   ![Översikt över åtkomst princip](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Under **nyckel behörigheter** väljer du **Hämta**, **Radbryt**, **packa** upp och **huvudobjektet** som är namnet på postgresql-servern.

   ![Översikt över åtkomst princip](media/concepts-data-access-and-security-data-encryption/access-policy-warp-unwrap.png)

3. **Spara** inställningarna.

## <a name="setting-data-encryption-for-azure-database-for-postgresql-single-server"></a>Ange data kryptering för Azure Database for PostgreSQL enskild server

1. På **Azure Database for PostgreSQL**väljer du **data krypteringen** för att ställa in den Kundhanterade nyckeln.

   ![Ange data kryptering](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Du kan antingen välja en **Key Vault** och **nyckel** par eller skicka en **nyckel identifierare**.

   ![Ange Key Vault](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. **Spara** inställningarna.

4. För att se till att alla filer (inklusive temporära filer) är fullständigt krypterade måste du starta om servern.

## <a name="restoring-or-creating-replica-of-the-server-which-has-data-encryption-enabled"></a>Återställa eller skapa en replik av servern som har data kryptering aktiverat

När en Azure Database for PostgreSQL enskild server är krypterad med kundens hanterade nyckel lagrad i Key Vault, kommer alla nyligen skapade kopior av servern antingen med lokal eller geo-återställning eller en replik (lokal/över region) åtgärd. För en krypterad PostgreSQL-Server kan du följa stegen nedan för att skapa en krypterad återställd Server.

1. Välj **Översikt**på servern och välj sedan **Återställ**.

   ![Starta-återställning](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Om du har en replik aktive rad server väljer du **replikering** under rubriken **Inställningar**

   ![Initiera-replik](media/concepts-data-access-and-security-data-encryption/postgresql-replica.png)

2. När återställningen har slutförts är den nya servern som skapas data krypterad med den primära serverns nyckel. Funktionerna och alternativen på-servern är dock inaktiverade och servern har marker ATS i ett **otillgängligt** tillstånd. Detta är för att förhindra data manipulation eftersom den nya serverns identitet fortfarande har fått behörighet att komma åt Key Vault.

   ![Markera servern otillgänglig](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)


3. För att kunna åtgärda otillgängligt läge måste du verifiera om nyckeln på den återställda servern.

   ![verifiera Server](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Du måste ge åtkomst till den nya servern till Key Vault. 

4. När du har verifierat nyckeln igen fortsätter servern sin normala funktion.

   ![Den normala servern har återställts](media/concepts-data-access-and-security-data-encryption/restore-successful.png)


## <a name="next-steps"></a>Nästa steg

 Mer information om data kryptering finns i [Vad är Azure Data Encryption](concepts-data-encryption-postgresql.md).