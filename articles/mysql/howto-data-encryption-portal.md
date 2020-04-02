---
title: Datakryptering - Azure-portal - Azure Database för MySQL
description: Lär dig hur du konfigurerar och hanterar datakryptering för din Azure-databas för MySQL med hjälp av Azure-portalen.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: acf3e6273f98d98d5da55cfb5b044677116c44dc
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80520816"
---
# <a name="data-encryption-for-azure-database-for-mysql-by-using-the-azure-portal"></a>Datakryptering för Azure Database för MySQL med hjälp av Azure-portalen

Lär dig hur du använder Azure-portalen för att konfigurera och hantera datakryptering för din Azure-databas för MySQL.

## <a name="prerequisites-for-azure-cli"></a>Förutsättningar för Azure CLI

* Du måste ha en Azure-prenumeration och vara administratör för den prenumerationen.
* Skapa ett nyckelvalv och en nyckel som ska användas för en kundhanterad nyckel i Azure Key Vault.
* Nyckelvalvet måste ha följande egenskaper som ska användas som kundhanterad nyckel:
  * [Mjuk borttagning](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [Rensa skyddad](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* Nyckeln måste ha följande attribut att använda som kundhanterad nyckel:
  * Inget utgångsdatum
  * Inte inaktiverad
  * Kunna utföra få, wrap nyckel, packa upp viktiga operationer

## <a name="set-the-right-permissions-for-key-operations"></a>Ange rätt behörigheter för nyckelåtgärder

1. I Key Vault väljer du **Åtkomstprincip** > **Lägg till åtkomstprincip**.

   ![Skärmbild av Key Vault, med åtkomstprinciper och Lägg till åtkomstprincip markerad](media/concepts-data-access-and-security-data-encryption/show-access-policy-overview.png)

2. Välj **Nyckelbehörigheter**och välj **Hämta**, **Radbryta,** **Packa upp**och **huvudmannen**, som är namnet på MySQL-servern. Om serverns huvudnamn inte finns i listan över befintliga huvudnamn måste du registrera det. Du uppmanas att registrera serverns huvudnamn när du försöker konfigurera datakryptering för första gången, och det misslyckas.

   ![Översikt över åtkomstprinciper](media/concepts-data-access-and-security-data-encryption/access-policy-wrap-unwrap.png)

3. Välj **Spara**.

## <a name="set-data-encryption-for-azure-database-for-mysql"></a>Ange datakryptering för Azure Database för MySQL

1. I Azure Database for MySQL väljer du **Datakryptering** för att konfigurera den kundhanterade nyckeln.

   ![Skärmbild av Azure Database for MySQL, med datakryptering markerad](media/concepts-data-access-and-security-data-encryption/data-encryption-overview.png)

2. Du kan antingen välja ett nyckelvalv och nyckelpar eller ange en nyckelidentifierare.

   ![Skärmbild av Azure Database for MySQL, med datakrypteringsalternativ markerade](media/concepts-data-access-and-security-data-encryption/setting-data-encryption.png)

3. Välj **Spara**.

4. Starta om servern om du vill vara säkra på att alla filer (inklusive temporära filer) är helt krypterade.

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>Använda datakryptering för återställnings- eller replikservrar

När Azure Database for MySQL har krypterats med en kunds hanterade nyckel lagrad i Key Vault krypteras även alla nyskapade kopior av servern. Du kan göra den nya kopian antingen via en lokal eller geoåterställningsåtgärd eller genom en replik (lokal/regionöverskridande åtgärd). Så för en krypterad MySQL-server kan du använda följande steg för att skapa en krypterad återställd server.

1. Välj > **Översiktsåterställning**på servern . **Overview**

   ![Skärmbild av Azure Database for MySQL, med Översikt och Återställning markerad](media/concepts-data-access-and-security-data-encryption/show-restore.png)

   Eller för en replikeringsaktiverad server väljer du **Replikering**under rubriken **Inställningar** .

   ![Skärmbild av Azure Database for MySQL, med replikering markerad](media/concepts-data-access-and-security-data-encryption/mysql-replica.png)

2. När återställningen är klar krypteras den nya servern som skapas med den primära serverns nyckel. Funktionerna och alternativen på servern är dock inaktiverade och servern är otillgänglig. Detta förhindrar datamanipulering, eftersom den nya serverns identitet ännu inte har fått behörighet att komma åt nyckelvalvet.

   ![Skärmbild av Azure Database for MySQL, med otillgänglig status markerad](media/concepts-data-access-and-security-data-encryption/show-restore-data-encryption.png)

3. Om du vill göra servern tillgänglig aktiverar du nyckeln på den återställda servern. Välj**Återvalidatnyckel** **för datakryptering** > .

   > [!NOTE]
   > Det första försöket att förnya misslyckas, eftersom den nya serverns tjänsthuvudnamn måste få åtkomst till nyckelvalvet. Om du vill generera tjänstens huvudnamn väljer du **Revalidate-nyckel**, som visar ett fel men genererar tjänstens huvudnamn. Därefter hänvisar du till [dessa steg](#set-the-right-permissions-for-key-operations) tidigare i den här artikeln.

   ![Skärmbild av Azure Database for MySQL, med förlängningssteg markerat](media/concepts-data-access-and-security-data-encryption/show-revalidate-data-encryption.png)

   Du måste ge nyckelvalvet åtkomst till den nya servern.

4. När du har registrerat tjänstens huvudnamn, förnya nyckeln igen och servern återupptar sin normala funktionalitet.

   ![Skärmbild av Azure Database for MySQL, med återställda funktioner](media/concepts-data-access-and-security-data-encryption/restore-successful.png)

## <a name="next-steps"></a>Nästa steg

 Mer information om datakryptering finns i [Azure Database for MySQL-datakryptering med kundhanterad nyckel](concepts-data-encryption-mysql.md).
