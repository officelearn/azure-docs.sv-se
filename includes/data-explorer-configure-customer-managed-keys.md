---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 7f5c02c6c009e8916ed063454e0ae6049892e95c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297913"
---
Azure Data Explorer krypterar alla data i ett lagringskonto i vila. Som standard krypteras data med Microsoft-hanterade nycklar. Om du vill ha ytterligare kontroll över krypteringsnycklar kan du ange kundhanterade nycklar som ska användas för datakryptering. 

Kundhanterade nycklar måste lagras i ett [Azure Key Vault](/azure/key-vault/key-vault-overview). Du kan skapa egna nycklar och lagra dem i ett nyckelvalv, eller så kan du använda ett Azure Key Vault API för att generera nycklar. Azure Data Explorer-klustret och nyckelvalvet måste finnas i samma region, men de kan finnas i olika prenumerationer. En detaljerad förklaring av kundhanterade nycklar finns i [kundhanterade nycklar med Azure Key Vault](/azure/storage/common/storage-service-encryption). 

I den här artikeln beskrivs hur du konfigurerar kundhanterade nycklar.

## <a name="configure-azure-key-vault"></a>Konfigurera Azure Key Vault

Om du vill konfigurera kundhanterade nycklar med Azure Data Explorer måste du [ange två egenskaper i nyckelvalvet:](/azure/key-vault/key-vault-ovw-soft-delete)Mjuk **borttagning** och **Rensa inte**. Dessa egenskaper är inte aktiverade som standard. Om du vill aktivera dessa egenskaper utför du **Aktivera mjuk borttagning** och **aktivera rensningsskydd** i [PowerShell](/azure/key-vault/key-vault-soft-delete-powershell) eller [Azure CLI](/azure/key-vault/key-vault-soft-delete-cli) på ett nytt eller befintligt nyckelvalv. Endast RSA-nycklar med storlek 2048 stöds. Mer information om nycklar finns i Nycklar till [Key Vault](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

> [!NOTE]
> Datakryptering med kundhanterade nycklar stöds inte på [leader- och follower-kluster](/azure/data-explorer/follower). 

## <a name="assign-an-identity-to-the-cluster"></a>Tilldela en identitet till klustret

Om du vill aktivera kundhanterade nycklar för klustret tilldelar du först en systemtilldelad hanterad identitet till klustret. Du ska använda den här hanterade identiteten för att bevilja klusterbehörigheter för åtkomst till nyckelvalvet. Information om hur du konfigurerar systemtilldelade hanterade identiteter finns i [hanterade identiteter](/azure/data-explorer/managed-identities).