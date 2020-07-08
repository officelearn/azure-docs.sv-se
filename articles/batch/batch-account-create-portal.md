---
title: Skapa ett konto i Azure Portal
description: Lär dig hur du skapar ett Azure Batch-konto på Azure-portalen för att köra storskaliga parallella arbetsbelastningar i molnet
ms.topic: how-to
ms.date: 06/10/2020
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1205de2b800588b735aeb20d388ba4b64bc6b078
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84711348"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Skapa ett Batch-konto med Azure Portal

I det här avsnittet visas hur du skapar ett Azure Batch konto i [Azure Portal](https://portal.azure.com)och väljer de konto egenskaper som passar ditt beräknings scenario. Du lär dig också var du hittar viktiga konto egenskaper som åtkomst nycklar och konto-URL: er.

För bakgrund om batch-konton och scenarier, se [batch-tjänstens arbets flöde och resurser](batch-service-workflow-features.md).

## <a name="create-a-batch-account"></a>Skapa ett Batch-konto

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **skapa en resurs**och välj sedan **beräknings** **-och batch-tjänst**.

1. Ange inställningar för **Nytt Batch-konto**. Se följande information.

    ![Skapa ett Batch-konto][account_portal]

    a. **Prenumeration**: Prenumerationen som Batch-kontot skapas i. Om du bara har en prenumeration väljs den som standard.

    b. **Resursgrupp**: Välj en befintlig resursgrupp för ditt nya Batch-konto. Du kan också skapa en ny resursgrupp.

    c. **Kontonamn**: Det namn du väljer måste vara unikt i den Azure-region där kontot skapas (se **Plats** nedan). Kontonamnet får bara innehålla gemener eller siffror och måste vara mellan 3 och 24 tecken långt.

    d. **Plats**: Azure-regionen som Batch-kontot skapas i. Endast de regioner som stöds av din prenumeration och resursgrupp visas som alternativ.

    e. **Lagrings konto**: ett valfritt Azure Storage-konto som du associerar med ditt batch-konto. Ett v2-lagringskonto för generell användning rekommenderas för bästa prestanda. Samtliga alternativ för lagringskonton i batch finns i [översikten över Batch-funktionen](accounts.md#azure-storage-accounts). I portalen väljer du ett befintligt lagringskonto eller skapar ett nytt.

      ![skapar ett lagringskonto][storage_account]

    f. **Poolens fördelnings läge**: på fliken **avancerade** inställningar kan du ange poolens fördelnings läge som **Batch-tjänst** eller **användar prenumeration**. För de flesta scenarier kan du acceptera standardinställningarna för **Batch-tjänsten**.

      ![Batch-poolallokeringsläge][pool_allocation]

1. Skapa kontot genom att klicka på **Skapa**.

## <a name="view-batch-account-properties"></a>Visa egenskaper för ett Batch-konto

När kontot har skapats kan du välja kontot för att komma åt dess inställningar och egenskaper. Du kan komma åt alla kontoinställningar och kontoegenskaper från den vänstra menyn.

> [!NOTE]
> Namnet på batch-kontot är dess ID och kan inte ändras. Om du behöver ändra namnet på ett batch-konto måste du ta bort kontot och skapa ett nytt med det avsedda namnet.

![Sidan för Batch-kontot på Azure Portal][account_blade]

* **Batch-kontonamn, URL och nycklar**: När du utvecklar ett program med [Batch-API:erna](batch-apis-tools.md#azure-accounts-for-batch-development) behöver du en konto-URL och nyckel för att komma åt Batch-resurserna. (Batch har även stöd för Azure Active Directory-autentisering.)

    Om du vill visa åtkomstinformationen för Batch-kontot väljer du **Nycklar**.

    ![Batch-kontonycklar på Azure Portal][account_keys]

* Om du vill visa namnet på och nycklarna för lagringskontot som är associerat med Batch-kontot väljer du **Lagringskonto**.

* Om du vill visa resurskvoterna som gäller för Batch-kontot väljer du **Kvoter**. Mer information finns i [Kvoter och begränsningar för Batch-tjänsten](batch-quota-limit.md).

## <a name="additional-configuration-for-user-subscription-mode"></a>Ytterligare konfiguration för läget Användarprenumeration

Om du väljer att skapa ett Batch-konto i läget Användarprenumeration ska du utföra följande åtgärder innan du skapar kontot.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Tillåt att Azure Batch får åtkomst till prenumerationen (engångsåtgärd)

När du skapar ditt första Batch-konto i användarprenumerationsläge behöver du registrera prenumerationen med Batch. (Om du redan har gjort det kan du gå vidare till nästa avsnitt.)

1. Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **alla tjänster**  >  **prenumerationer**och välj den prenumeration som du vill använda för batch-kontot.

1. På sidan **Prenumeration** väljer du **resursproviders** och sök efter **Microsoft.Batch**. Kontrollera att resursprovidern **Microsoft.Batch** har registrerats i prenumerationen. Om den inte är registrerad väljer du länken **Registrera**.

    ![Registrera providern Microsoft.Batch][register_provider]

1. På sidan **Prenumeration** väljer du **Åtkomstkontroll (IAM)** > **Rolltilldelningar** > **Lägg till rolltilldelning**.

    ![Åtkomstkontroll för prenumeration][subscription_access]

1. På sidan **Lägg till roll tilldelning** väljer du rollen **deltagare** eller **ägare** och söker efter batch-API: et. Sök efter var och en av de här strängarna tills du hittar API:t:
    1. **MicrosoftAzureBatch**.
    1. **Microsoft Azure Batch**. Nyare Azure AD-klientorganisationer kan använda det här namnet.
    1. **ddbf3205-c6bd-46ae-8127-60eb93363864** är id:t för API:t.

1. När du har hittat Batch-API:t markerar du det och klickar på **Spara**.

    ![Lägg till Batch-behörigheter][add_permission]

### <a name="create-a-key-vault"></a>Skapa en Key Vault-lösning

I användar prenumerations läge krävs en [Azure Key Vault](../key-vault/general/overview.md) . Key Vault måste vara i samma prenumeration och region som det batch-konto som ska skapas. 

1. På [Azure Portal](https://portal.azure.com) väljer du **Nytt** > **Säkerhet** > **Key Vault**.

1. På sidan **skapa Key Vault** anger du ett namn för Key Vault och skapar en resurs grupp i den region som du vill använda för ditt batch-konto. Lämna standardvärdena för resten av inställningarna och väljer sedan **Skapa**.

När du skapar batch-kontot i användar prenumerations läge anger du **användar prenumerationen** som poolens fördelnings läge, väljer Key Vault och markerar kryss rutan för att bevilja Azure Batch åtkomst till Key Vault.

Om du vill bevilja åtkomst till Key Vault manuellt går du till avsnittet **åtkomst principer** i Key Vault, väljer **Lägg till åtkomst princip** och söker efter **Microsoft Azure Batch**. När du har valt det här alternativet måste du konfigurera de **hemliga behörigheterna** med hjälp av den nedrullningsbara menyn. Azure Batch måste ges minst behörigheterna **Get**, **list**, **set**och **Delete** .

![Hemliga behörigheter för Azure Batch](./media/batch-account-create-portal/secret-permissions.png)

> [!NOTE]
> Kontrol lera att kryss rutorna **Azure Virtual Machines för distribution** och **Azure Resource Manager för mall distribution** är markerade under **åtkomst principer** för den länkade **Key Vault** resursen.
>
> ![Obligatorisk princip för Key Vault åtkomst](./media/batch-account-create-portal/key-vault-access-policy.png)

### <a name="configure-subscription-quotas"></a>Konfigurera prenumerationskvoter

För batch-konton för användar prenumerationer måste kärn kvoter anges manuellt. Standard Batch kärn kvoter gäller inte för konton i användar prenumerations läge.

1. I [Azure-portalen](https://portal.azure.com) väljer du ditt Batch-konto för läget för användarprenumeration för att visa dess inställningar och egenskaper.
1. På den vänstra menyn väljer du **Kvoter** för att visa och konfigurera de kärnkvoter som är associerade med ditt Batch-konto.

Mer information om kärn kvoter för användar prenumerations läge finns i [batch-tjänstens kvoter och begränsningar](batch-quota-limit.md).

## <a name="other-batch-account-management-options"></a>Andra alternativ för Batch-kontohantering

Förutom att använda Azure Portal kan du skapa och hantera Batch-konton med verktyg som följande:

* [PowerShell-cmdlets för Batch](batch-powershell-cmdlets-get-started.md)
* [Azure CLI](batch-cli-get-started.md)
* [Batch Management .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [batch-tjänstens arbets flöde och primära resurser](batch-service-workflow-features.md) som pooler, noder, jobb och aktiviteter.
* Lär dig hur du utvecklar ett enkelt Batch-aktiverat program med hjälp av [Batch .NET-klientbiblioteket](quick-run-dotnet.md) eller [Python](quick-run-python.md). I de här snabbstarterna beskriver vi ett exempelprogram som använder Batch-tjänsten för att köra en arbetsbelastning på flera beräkningsnoder och förklarar hur du använder Azure Storage för mellanlagring och hämtning av filer i arbetsbelastningar.

[account_blade]: ./media/batch-account-create-portal/batch_blade.png
[account_portal]: ./media/batch-account-create-portal/batch-account-portal.png
[pool_allocation]: ./media/batch-account-create-portal/batch-pool-allocation.png
[account_keys]: ./media/batch-account-create-portal/batch-account-keys.png
[storage_account]: ./media/batch-account-create-portal/storage_account.png
[subscription_access]: ./media/batch-account-create-portal/subscription_iam.png
[add_permission]: ./media/batch-account-create-portal/add_permission.png
[register_provider]: ./media/batch-account-create-portal/register_provider.png
