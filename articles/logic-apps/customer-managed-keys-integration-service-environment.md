---
title: Konfigurera kundhanterade nycklar för att kryptera data i vila i ISE
description: Skapa och hantera dina egna krypteringsnycklar för att skydda data i vila för integrationstjänstmiljöer (ISE) i Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: fa39c8f65b00283044ef31dc7577a4668b3e634b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127646"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>Konfigurera kundhanterade nycklar för att kryptera data i vila för integrationstjänstmiljöer (ISE) i Azure Logic Apps

Azure Logic Apps förlitar sig på Azure Storage för att lagra och automatiskt [kryptera data i vila](../storage/common/storage-service-encryption.md). Den här krypteringen skyddar dina data och hjälper dig att uppfylla dina organisatoriska säkerhets- och efterlevnadsåtaganden. Som standard använder Azure Storage Microsoft-hanterade nycklar för att kryptera dina data. Mer information om hur Azure Storage-kryptering fungerar finns i [Azure Storage-kryptering för data i vila](../storage/common/storage-service-encryption.md) och Azure Data [Encryption-at-Rest](../security/fundamentals/encryption-atrest.md).

När du skapar en [integrationstjänstmiljö (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) för att vara värd för dina logikappar och vill ha mer kontroll över krypteringsnycklarna som används av Azure Storage, kan du konfigurera, använda och hantera din egen nyckel med hjälp av [Azure Key Vault](../key-vault/key-vault-overview.md). Den här funktionen kallas även "Bring Your Own Key" (BYOK), och nyckeln kallas en "kundhanterad nyckel".

Det här avsnittet visar hur du konfigurerar och anger din egen krypteringsnyckel som ska användas när du skapar ISE med hjälp av REST-API:et för Logikappar. De allmänna stegen för att skapa ett ISE via REST API för Logic Apps finns i [Skapa en integrationstjänstmiljö (ISE) med hjälp av REST API FÖR Logic Apps](../logic-apps/create-integration-service-environment-rest-api.md).

## <a name="considerations"></a>Överväganden

* För närvarande är kundhanterad nyckelsupport för en ISE endast tillgänglig i dessa Azure-regioner: Västra USA 2, Östra USA och Södra centrala USA

* Du kan bara ange en kundhanterad nyckel *när du skapar ISE*, inte efteråt. Du kan inte inaktivera den här nyckeln när ISE har skapats. För närvarande finns det inget stöd för att rotera en kundhanterad nyckel för en ISE.

* För att stödja kundhanterade nycklar kräver DIN ISE att dess [systemtilldelade hanterade identitet](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) är aktiverad. Med den här identiteten kan ISE autentisera åtkomst till resurser i andra Azure Active Directory-klienter (Azure AD) så att du inte behöver logga in med dina autentiseringsuppgifter.

* För närvarande, för att skapa en ISE som stöder kundhanterade nycklar och har sin systemtilldelade identitet aktiverad, måste du anropa Logic Apps REST API med hjälp av en HTTPS PUT-begäran.

* Inom *30 minuter* efter att du har skickat HTTPS PUT-begäran som skapar DIN ISE måste du [ge nyckelvalvet åtkomst till ISE:s systemtilldelade identitet](#identity-access-to-key-vault). Annars misslyckas ISE-skapandet och genererar ett behörighetsfel.

## <a name="prerequisites"></a>Krav

* Samma [förutsättningar](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) och [krav för att aktivera åtkomst för din ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) som när du skapar en ISE i Azure-portalen

* Ett Azure-nyckelvalv som har egenskaperna **Mjuk borttagning** **och Rensa inte** aktiverat

  Mer information om hur du aktiverar dessa egenskaper finns i [Azure Key Vault soft-delete översikt](../key-vault/key-vault-ovw-soft-delete.md) och Konfigurera [kundhanterade nycklar med Azure Key Vault](../storage/common/storage-encryption-keys-portal.md). Om du inte har använt Azure Key Vault tidigare kan du läsa om hur du [skapar ett nyckelvalv](../key-vault/quick-create-portal.md#create-a-vault) med hjälp av Azure-portalen eller med kommandot Azure PowerShell, [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault).

* I nyckelvalvet har en nyckel som har skapats med dessa egenskapsvärden:

  | Egenskap | Värde |
  |----------|-------|
  | **Nyckeltyp** | RSA |
  | **RSA-nyckelstorlek** | 2048 |
  | **Enabled** | Ja |
  |||

  ![Skapa din kundhanterade krypteringsnyckel](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  Mer information finns i [Konfigurera kundhanterade nycklar med Azure Key Vault](../storage/common/storage-encryption-keys-portal.md) eller Azure PowerShell-kommandot [Add-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/Add-AzKeyVaultKey).

* Ett verktyg som du kan använda för att skapa din ISE genom att anropa Logic Apps REST API med en HTTPS PUT-begäran. Du kan till exempel använda [Postman](https://www.getpostman.com/downloads/)eller skapa en logikapp som utför den här uppgiften.

<a name="enable-support-key-system-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>Skapa ISE med nyckelvalv och hanterat identitetsstöd

Om du vill skapa din ISE genom att anropa LOGIC Apps REST API gör du den här HTTPS PUT-begäran:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Logic Apps REST API 2019-05-01-versionen kräver att du gör din egen HTTP PUT-begäran om ISE-kopplingar.

Distributionen tar vanligtvis inom två timmar att slutföra. Ibland kan distributionen ta upp till fyra timmar. Om du vill kontrollera distributionsstatus väljer du ikonen meddelanden i [Azure-portalen](https://portal.azure.com)i azure-verktygsfältet som öppnar meddelandefönstret.

> [!NOTE]
> Om distributionen misslyckas eller om du tar bort din ISE kan Det ta upp till en timme innan du släpper dina undernät. Den här fördröjningen innebär att du kanske måste vänta innan du återanvänder dessa undernät i en annan ISE.
>
> Om du tar bort det virtuella nätverket tar Azure vanligtvis upp till två timmar innan du släpper upp dina undernät, men den här åtgärden kan ta längre tid. 
> När du tar bort virtuella nätverk kontrollerar du att inga resurser fortfarande är anslutna. 
> Se [Ta bort virtuellt nätverk](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

### <a name="request-header"></a>Begärandehuvud

I begäran huvudet, inkludera dessa egenskaper:

* `Content-type`: Ange egenskapsvärdet till `application/json`.

* `Authorization`: Ange det här egenskapsvärdet till innehavartoken för kunden som har åtkomst till Azure-prenumerationen eller resursgruppen som du vill använda.

### <a name="request-body"></a>Begärandetext

I begärandetexten aktiverar du stöd för dessa ytterligare objekt genom att tillhandahålla deras information i ISE-definitionen:

* Den systemtilldelade hanterade identitet som din ISE använder för att komma åt ditt nyckelvalv
* Nyckelvalvet och den kundhanterade nyckeln som du vill använda

#### <a name="request-body-syntax"></a>Syntax för begäran om brödtext

Här är syntaxen för begärantexten, som beskriver de egenskaper som ska användas när du skapar ISE:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.KeyVault/vaults/{key-vault-name}",
            },
            "keyName": "{customer-managed-key-name}",
            "keyVersion": "{key-version-number}"
         }
      }
   }
}
```

#### <a name="request-body-example"></a>Exempel på begärantext

I det här exemplet visar begäranden exempelvärdena:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "SystemAssigned"
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.KeyVault/vaults/FabrikamKeyVault",
            },
            "keyName": "Fabrikam-Encryption-Key",
            "keyVersion": "********************"
         }
      }
   }
}
```

<a name="identity-access-to-key-vault"></a>

## <a name="grant-access-to-your-key-vault"></a>Bevilja åtkomst till ditt nyckelvalv

Inom *30 minuter* efter att du har skickat HTTP PUT-begäran om att skapa ise måste du lägga till en åtkomstprincip i nyckelvalvet för ISE:s systemtilldelade identitet. Annars misslyckas skapandet för din ISE och du får ett behörighetsfel. 

För den här uppgiften kan du använda kommandot Azure PowerShell [Set-AzKeyVaultAccessPolicy](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) eller så kan du följa dessa steg i Azure-portalen:

1. Öppna ditt Azure-nyckelvalv i [Azure-portalen.](https://portal.azure.com)

1. På nyckelvalvsmenyn väljer du Lägg**till åtkomstprinciper,** **Access policies** > till exempel:

   ![Lägga till åtkomstprincip för systemtilldelade hanterade identitet](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. När fönstret **Lägg till åtkomstprincip** har öppnats gör du så här:

   1. Välj de här alternativen:

      | Inställning | Värden |
      |---------|--------|
      | **Konfigurera från mall (valfri) lista** | Nyckelhantering |
      | **Viktiga behörigheter** | - **Nyckelhantering:** Hämta, Lista <p><p>- **Kryptografiska operationer:** Packa upp nyckel, radbrytnyckel |
      |||

      ![Välj "Nyckelhantering" > "Nyckelbehörigheter"](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. För **Välj huvudnamn**väljer du **Ingen markerad**. När fönstret **Huvudnamn** har öppnats letar du reda på och väljer ISE i sökrutan. När du är klar väljer du **Välj** > **Lägg till**.

      ![Välj din ISE att använda som huvudnamn](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. När du är klar med **fönstret Åtkomstprinciper** väljer du **Spara**.

Mer information finns i [Tillhandahålla nyckelvalvsautentisering med en hanterad identitet](../key-vault/managed-identity.md#grant-your-app-access-to-key-vault).

## <a name="next-steps"></a>Nästa steg

* Läs mer om [Azure Key Vault](../key-vault/key-vault-overview.md)