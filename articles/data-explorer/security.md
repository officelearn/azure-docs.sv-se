---
title: Skydda Azure Datautforskaren kluster i Azure
description: Lär dig mer om hur du skyddar kluster i Azure Datautforskaren.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: cc16a40b0ea53d433a5a6c592f3b9ea364ef9089
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2020
ms.locfileid: "75725837"
---
# <a name="secure-azure-data-explorer-clusters-in-azure"></a>Skydda Azure Datautforskaren kluster i Azure

Den här artikeln innehåller en introduktion till säkerhet i Azure Datautforskaren som hjälper dig att skydda dina data och resurser i molnet och uppfylla företagets säkerhets behov. Det är viktigt att hålla dina kluster säkra. Att säkra klustren innehåller en eller flera Azure-funktioner som innehåller säker åtkomst och lagring. Den här artikeln innehåller information som hjälper dig att skydda klustret.

## <a name="managed-identities-for-azure-resources"></a>Hanterade identiteter för Azure-resurser

En vanlig utmaning när du bygger moln program är hantering av autentiseringsuppgifter i din kod för att autentisera till moln tjänster. Det är viktigt att dessa autentiseringsuppgifter skyddas. Autentiseringsuppgifterna lagras inte i Developer-arbetsstationer eller checkas in i käll kontrollen. Azure Key Vault är ett sätt att lagra autentiseringsuppgifter, hemligheter och andra nycklar på ett säkert sätt, men din kod måste autentisera mot Key Vault för att kunna hämta dem.

Den Azure Active Directory (Azure AD) Managed identiteter för Azure-resurser löser det här problemet. Funktionen förser Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Du kan använda identiteten för att autentisera mot alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan att du behöver lägga in några autentiseringsuppgifter i din kod. Mer information om den här tjänsten finns [på sidan hanterade identiteter för](/azure/active-directory/managed-identities-azure-resources/overview) översikt över Azure-resurser.

## <a name="data-encryption"></a>Datakryptering

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) skyddar och skyddar dina data så att de uppfyller organisationens säkerhets-och efterlevnads åtaganden. Den tillhandahåller volym kryptering för operativ system-och data diskar för klustrets virtuella datorer. Azure Disk Encryption integreras också med [Azure Key Vault](/azure/key-vault/), vilket gör att vi kan styra och hantera disk krypterings nycklar och hemligheter och se till att alla data på de virtuella dator diskarna krypteras. 

### <a name="customer-managed-keys-with-azure-key-vault"></a>Kundhanterade nycklar med Azure Key Vault

Som standard krypteras data med Microsoft-hanterade nycklar. Om du vill ha ytterligare kontroll över krypterings nycklar kan du ange Kundhanterade nycklar som ska användas för data kryptering. Du kan hantera kryptering av dina data på lagrings nivå med dina egna nycklar. En kundhanterad nyckel används för att skydda och kontrol lera åtkomsten till rot krypterings nyckeln, som används för att kryptera och dekryptera alla data. Kundhanterade nycklar ger större flexibilitet för att skapa, rotera, inaktivera och återkalla åtkomst kontroller. Du kan också granska de krypterings nycklar som används för att skydda dina data.

Använd Azure Key Vault för att lagra dina Kundhanterade nycklar. Du kan skapa egna nycklar och lagra dem i ett nyckel valv, eller så kan du använda ett Azure Key Vault API för att generera nycklar. Azure Datautforskaren-klustret och Azure Key Vault måste vara i samma region, men de kan finnas i olika prenumerationer. Mer information om Azure Key Vault finns i [Azure Key Vault?](/azure/key-vault/key-vault-overview). En detaljerad förklaring om Kundhanterade nycklar finns i [Kundhanterade nycklar med Azure Key Vault](/azure/storage/common/storage-service-encryption)

> [!Note]
> Kundhanterade nycklar är beroende av hanterade identiteter för Azure-resurser, en funktion i Azure Active Directory (Azure AD). Om du vill konfigurera Kundhanterade nycklar i Azure Portal måste du konfigurera en **SystemAssigned** -hanterad identitet för klustret.

#### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Lagra Kundhanterade nycklar i Azure Key Vault

Om du vill aktivera Kundhanterade nycklar i ett kluster använder du en Azure Key Vault för att lagra dina nycklar. Du måste aktivera både den **mjuka borttagningen** och **Rensa inte** egenskaperna i nyckel valvet. Nyckel valvet måste finnas i samma prenumeration som klustret. Azure Datautforskaren använder hanterade identiteter för Azure-resurser för att autentisera till nyckel valvet för kryptering och dekryptering. Hanterade identiteter stöder inte scenarier mellan kataloger.

#### <a name="rotate-customer-managed-keys"></a>Rotera Kundhanterade nycklar

Du kan rotera en kundhanterad nyckel i Azure Key Vault enligt efterlevnadsprinciper. När nyckeln roteras måste du uppdatera klustret så att det använder den nya nyckel-URI: n. Rotation av nyckeln utlöser inte Omkryptering av data i klustret. 

#### <a name="revoke-access-to-customer-managed-keys"></a>Återkalla åtkomst till Kundhanterade nycklar

Om du vill återkalla åtkomsten till Kundhanterade nycklar använder du PowerShell eller Azure CLI. Mer information finns i [Azure Key Vault PowerShell](/powershell/module/az.keyvault/) eller [Azure Key Vault CLI](/cli/azure/keyvault). Återkallar åtkomst blockerar åtkomst till alla data i klustrets lagrings nivå, eftersom krypterings nyckeln därför inte är tillgänglig för Azure Datautforskaren.

> [!Note]
> När Azure Datautforskaren identifierar att åtkomst till en kundhanterad nyckel återkallas, kommer den automatiskt att tillfälligt pausa klustret för att ta bort cachelagrade data. När åtkomst till nyckeln returneras måste klustret återupptas manuellt.

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Med hjälp av [rollbaserad åtkomst kontroll (RBAC)](/azure/role-based-access-control/overview)kan du åtskilja uppgifter i teamet och endast bevilja nödvändig åtkomst till kluster användare. I stället för att ge alla obegränsade behörigheter för klustret kan du bara tillåta vissa åtgärder. Du kan konfigurera [åtkomst kontroll för databaserna](/azure/data-explorer/manage-database-permissions) i [Azure Portal](/azure/role-based-access-control/role-assignments-portal), med hjälp av [Azure CLI](/azure/role-based-access-control/role-assignments-cli)eller [Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell).

## <a name="next-steps"></a>Nästa steg

* [Konfigurera hanterade identiteter för ditt Azure Datautforskaren-kluster](managed-identities.md)
* [Skydda ditt kluster i Azure datautforskaren-Portal](manage-cluster-security.md) genom att aktivera kryptering i vila.
* [Konfigurera Kundhanterade nycklar med hjälp av Azure Resource Manager mall](customer-managed-keys-resource-manager.md)
* [Konfigurera Kundhanterade nycklar medC#](customer-managed-keys-csharp.md)

