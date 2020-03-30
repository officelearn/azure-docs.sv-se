---
title: Säkra Azure Data Explorer-kluster i Azure
description: Lär dig mer om hur du skyddar kluster i Azure Data Explorer.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 786950011f10e25d6bcb72061212c1878e79d45a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77373365"
---
# <a name="secure-azure-data-explorer-clusters-in-azure"></a>Säkra Azure Data Explorer-kluster i Azure

Den här artikeln innehåller en introduktion till säkerhet i Azure Data Explorer som hjälper dig att skydda dina data och resurser i molnet och uppfylla ditt företags säkerhetsbehov. Det är viktigt att hålla klustren säkra. Skydda dina kluster innehåller en eller flera Azure-funktioner som innehåller säker åtkomst och lagring. Den här artikeln innehåller information som hjälper dig att skydda klustret.

## <a name="managed-identities-for-azure-resources"></a>Hanterade identiteter för Azure-resurser

En vanlig utmaning när du skapar molnprogram är hantering av autentiseringsuppgifter i din kod för autentisering till molntjänster. Det är viktigt att dessa autentiseringsuppgifter skyddas. Autentiseringsuppgifterna ska inte lagras på utvecklararbetsstationer eller checkas in i källkontrollen. Azure Key Vault är ett sätt att lagra autentiseringsuppgifter, hemligheter och andra nycklar på ett säkert sätt, men din kod måste autentisera mot Key Vault för att kunna hämta dem.

Azure Active Directory (Azure AD) hanterade identiteter för Azure-resurser funktionen löser detta problem. Funktionen förser Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Du kan använda identiteten för att autentisera mot alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan att du behöver lägga in några autentiseringsuppgifter i din kod. Mer information om den här tjänsten finns på [hanterad identitet för Azure-resursöversiktssida.](/azure/active-directory/managed-identities-azure-resources/overview)

## <a name="data-encryption"></a>Datakryptering

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) hjälper till att skydda och skydda dina data för att uppfylla dina organisatoriska säkerhets- och efterlevnadsåtaganden. Det ger volymkryptering för operativsystem och datadiskar i klustrets virtuella datorer. Azure Disk Encryption integreras också med [Azure Key Vault](/azure/key-vault/), vilket gör att vi kan styra och hantera diskkrypteringsnycklar och hemligheter och se till att alla data på VM-diskarna är krypterade. 

### <a name="customer-managed-keys-with-azure-key-vault"></a>Kundhanterade nycklar med Azure Key Vault

Som standard krypteras data med Microsoft-hanterade nycklar. Om du vill ha ytterligare kontroll över krypteringsnycklar kan du ange kundhanterade nycklar som ska användas för datakryptering. Du kan hantera kryptering av dina data på lagringsnivå med dina egna nycklar. En kundhanterad nyckel används för att skydda och kontrollera åtkomsten till rotkrypteringsnyckeln, som används för att kryptera och dekryptera alla data. Kundhanterade nycklar ger större flexibilitet att skapa, rotera, inaktivera och återkalla åtkomstkontroller. Du kan också granska krypteringsnycklarna som används för att skydda dina data.

Använd Azure Key Vault för att lagra dina kundhanterade nycklar. Du kan skapa egna nycklar och lagra dem i ett nyckelvalv, eller så kan du använda ett Azure Key Vault API för att generera nycklar. Azure Data Explorer-klustret och Azure Key Vault måste finnas i samma region, men de kan finnas i olika prenumerationer. Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](/azure/key-vault/key-vault-overview). En detaljerad förklaring av kundhanterade nycklar finns i [Kundhanterade nycklar med Azure Key Vault](/azure/storage/common/storage-service-encryption). Konfigurera kundhanterade nycklar i Azure Data Explorer-klustret med [C#](/azure/data-explorer/customer-managed-keys-csharp) eller [Azure Resource Manager-mallen](/azure/data-explorer/customer-managed-keys-resource-manager)

> [!Note]
> Kundhanterade nycklar är beroende av hanterade identiteter för Azure-resurser, en funktion i Azure Active Directory (Azure AD). Om du vill konfigurera kundhanterade nycklar i Azure-portalen måste du konfigurera en **SystemAssigned-hanterad** identitet till klustret som beskrivs i [Konfigurera hanterade identiteter för ditt Azure Data Explorer-kluster](/azure/data-explorer/managed-identities).

#### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Lagra kundhanterade nycklar i Azure Key Vault

Om du vill aktivera kundhanterade nycklar i ett kluster använder du ett Azure Key Vault för att lagra dina nycklar. Du måste aktivera egenskaperna **Mjuk borttagning** **och Rensa inte** på nyckelvalvet. Nyckelvalvet måste finnas i samma prenumeration som klustret. Azure Data Explorer använder hanterade identiteter för Azure-resurser för att autentisera till nyckelvalvet för kryptering och dekrypteringsåtgärder. Hanterade identiteter stöder inte scenarier över kataloger.

#### <a name="rotate-customer-managed-keys"></a>Rotera kundhanterade nycklar

Du kan rotera en kundhanterad nyckel i Azure Key Vault enligt dina efterlevnadsprinciper. När nyckeln roteras måste du uppdatera klustret för att använda den nya nyckeln URI. Om du roterar nyckeln kan du inte återskapa data i klustret. 

#### <a name="revoke-access-to-customer-managed-keys"></a>Återkalla åtkomst till kundhanterade nycklar

Om du vill återkalla åtkomsten till kundhanterade nycklar använder du PowerShell eller Azure CLI. Mer information finns i [Azure Key Vault PowerShell](/powershell/module/az.keyvault/) eller [Azure Key Vault CLI](/cli/azure/keyvault). Återkalla åtkomst blockerar åtkomst till alla data i klustrets lagringsnivå, eftersom krypteringsnyckeln därför inte är tillgänglig för Azure Data Explorer.

> [!Note]
> När Azure Data Explorer identifierar att åtkomsten till en kundhanterad nyckel återkallas, pausas klustret automatiskt för att ta bort cachelagrade data. När åtkomsten till nyckeln har returnerats måste klustret återupptas manuellt.

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Med hjälp av [rollbaserad åtkomstkontroll (RBAC)](/azure/role-based-access-control/overview)kan du segregera uppgifter inom teamet och endast bevilja nödvändig åtkomst till klusteranvändare. I stället för att ge alla obegränsad behörighet i klustret kan du bara tillåta vissa åtgärder. Du kan konfigurera [åtkomstkontroll för databaserna](/azure/data-explorer/manage-database-permissions) i [Azure-portalen](/azure/role-based-access-control/role-assignments-portal), med hjälp av [Azure CLI](/azure/role-based-access-control/role-assignments-cli)eller [Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell).

## <a name="next-steps"></a>Nästa steg

* [Skydda klustret i Azure Data Explorer - Portal](manage-cluster-security.md) genom att aktivera kryptering i vila.
* [Konfigurera hanterade identiteter för ditt Azure Data Explorer-kluster](managed-identities.md)
* [Konfigurera kundhanterade nycklar med azure Resource Manager-mallen](customer-managed-keys-resource-manager.md)
* [Konfigurera kundhanterade nycklar med C #](customer-managed-keys-csharp.md)

