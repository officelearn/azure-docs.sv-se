---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 53c9dea83fc6d413d7e82194696ffedabcc8cf7b
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71830182"
---
Det är viktigt att du håller den virtuella datorn säker för de program som du kör. Att skydda dina virtuella datorer kan omfatta en eller flera Azure-tjänster och-funktioner som omfattar säker åtkomst till dina virtuella datorer och säker lagring av dina data. Den här artikeln innehåller information som gör att du kan skydda dina virtuella datorer och program.

## <a name="antimalware"></a>Programvara mot skadlig kod

Modern Threat landskap för moln miljöer är dynamisk, vilket ökar trycket för att upprätthålla det effektiva skyddet för att uppfylla efterlevnads-och säkerhets kraven. [Microsoft Antimalware för Azure](../articles/security/fundamentals/antimalware.md) är en kostnads fri real tids skydds funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig program vara. Aviseringar kan konfigureras för att meddela dig när kända skadlig eller oönskad program vara försöker installera sig själv eller köra på den virtuella datorn. Det finns inte stöd för virtuella datorer som kör Linux eller Windows Server 2008.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../articles/security-center/security-center-intro.md) hjälper dig att förhindra, identifiera och svara på hot mot dina virtuella datorer. Security Center tillhandahåller integrerad säkerhetsövervakning och princip hantering i dina Azure-prenumerationer, hjälper till att identifiera hot som annars kan gå vidare och fungerar med ett brett eko system med säkerhetslösningar.

Security Centers just-in-Time-åtkomst kan användas över din VM-distribution för att låsa inkommande trafik till dina virtuella Azure-datorer, vilket minskar exponeringen för attacker och ger enkel åtkomst till att ansluta till virtuella datorer när det behövs. När just-in-Time är aktiverat och en användare begär åtkomst till en virtuell dator, kontrollerar Security Center vilka behörigheter användaren har för den virtuella datorn. Om de har rätt behörigheter godkänns begäran och Security Center automatiskt konfigurerar nätverks säkerhets grupper (NSG: er) för att tillåta inkommande trafik till de valda portarna under en begränsad tid. När tiden har gått ut Security Center återställer NSG: er till sina tidigare tillstånd. 

## <a name="encryption"></a>Kryptering

Virtuella diskar i Azure kan krypteras för förbättrad [VM](../articles/virtual-machines/linux/disk-encryption-overview.md) -säkerhet och efterlevnad för virtuella [Windows-datorer](../articles/virtual-machines/windows/encrypt-disks.md) . Virtuella diskar på virtuella Windows-datorer krypteras i vila med BitLocker. Virtuella diskar på virtuella Linux-datorer krypteras i vila med hjälp av dm-crypt. 

Det kostar inget att kryptera virtuella diskar i Azure. Krypterings nycklar lagras i Azure Key Vault med hjälp av program varu skydd, eller så kan du importera eller generera nycklar i HSM: er (Hardware Security modules) som är certifierade enligt standarden FIPS 140-2 nivå 2. Dessa kryptografiska nycklar används för att kryptera och dekryptera virtuella diskar som är anslutna till den virtuella datorn. Du behåller kontrollen över dessa kryptografiska nycklar och kan granska deras användning. Ett Azure Active Directory tjänstens huvud namn är en säker mekanism för att utfärda kryptografiska nycklar när virtuella datorer är påslagna.

## <a name="key-vault-and-ssh-keys"></a>Key Vault och SSH-nycklar

Hemligheter och certifikat kan modelleras som resurser och tillhandahålls av [Key Vault](../articles/key-vault/key-vault-whatis.md). Du kan använda Azure PowerShell för att skapa nyckel valv för virtuella [Windows-datorer](../articles/virtual-machines/windows/key-vault-setup.md) och Azure CLI för [virtuella Linux-datorer](../articles/virtual-machines/linux/key-vault-setup.md). Du kan också skapa nycklar för kryptering.

Åtkomst principer för nyckel valv beviljar behörigheter till nycklar, hemligheter och certifikat separat. Du kan till exempel ge en användare åtkomst till bara nycklar, men inte hemligheter. Behörighet att komma åt nycklar eller hemligheter eller certifikat är dock valvnivå. [Åtkomst principen för nyckel valvet](../articles/key-vault/key-vault-secure-your-key-vault.md) stöder med andra ord inte behörigheter på objekt nivå.

När du ansluter till virtuella datorer bör du använda kryptering med offentliga nycklar för att skapa ett säkrare sätt att logga in på dem. Den här processen omfattar ett offentligt och privat nyckel utbyte med hjälp av SSH-kommandot (Secure Shell) för att autentisera sig själv i stället för ett användar namn och lösen ord. Lösen ord är utsatta för angrepp som är utsatta för intrång, särskilt på Internetbaserade virtuella datorer, till exempel webb servrar. Med ett SSH-nyckelpar (Secure Shell) kan du skapa en [virtuell Linux-dator](../articles/virtual-machines/linux/mac-create-ssh-keys.md) som använder SSH-nycklar för autentisering, vilket eliminerar behovet av lösen ord för inloggning. Du kan också använda SSH-nycklar för att ansluta från en [virtuell Windows-dator](../articles/virtual-machines/linux/ssh-from-windows.md) till en virtuell Linux-dator.

## <a name="managed-identities-for-azure-resources"></a>Hanterade identiteter för Azure-resurser

En vanlig utmaning vid utvecklingen av molnprogram är hur man ska hantera autentiseringsuppgifterna i koden som krävs för autentisering mot molntjänsterna. Det är viktigt att dessa autentiseringsuppgifter skyddas. Helst bör autentiseringsuppgifterna aldrig visas på utvecklarnas arbetsstationer eller checkas in i källkontrollen. Azure Key Vault är ett sätt att lagra autentiseringsuppgifter, hemligheter och andra nycklar på ett säkert sätt, men din kod måste autentisera mot Key Vault för att kunna hämta dem. 

Funktionen Hanterade identiteter för Azure-resurser i Azure Active Directory (Azure AD) löser det här problemet. Funktionen förser Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Du kan använda identiteten för att autentisera mot alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan att du behöver lägga in några autentiseringsuppgifter i din kod.  Din kod som körs på en virtuell dator kan begära en token från två slut punkter som endast är tillgängliga från den virtuella datorn. Mer detaljerad information om den här tjänsten finns på översikts sidan [hanterade identiteter för Azure-resurser](../articles/active-directory/managed-identities-azure-resources/overview.md) .   

## <a name="policies"></a>Principer

[Azure-principer](../articles/azure-policy/azure-policy-introduction.md) kan användas för att definiera det önskade beteendet för din organisations virtuella [Windows-datorer](../articles/virtual-machines/windows/policy.md) och [virtuella Linux-datorer](../articles/virtual-machines/linux/policy.md). Med hjälp av principer kan en organisation tillämpa olika konventioner och regler i hela företaget. Verk ställandet av det önskade beteendet kan hjälpa till att minska risken och bidra till organisationens framgång.

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Med hjälp av [rollbaserad åtkomst kontroll (RBAC)](../articles/role-based-access-control/overview.md)kan du åtskilja uppgifter i teamet och bara ge åtkomst till användare på den virtuella datorn som de behöver för att utföra sina jobb. I stället för att ge alla obegränsade behörigheter på den virtuella datorn kan du bara tillåta vissa åtgärder. Du kan konfigurera åtkomst kontroll för den virtuella datorn i [Azure Portal](../articles/role-based-access-control/role-assignments-portal.md), med hjälp av [Azure CLI](https://docs.microsoft.com/cli/azure/role)eller[Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).


## <a name="next-steps"></a>Nästa steg
- Gå igenom stegen för att övervaka säkerheten för virtuella datorer med hjälp av Azure Security Center för [Linux](../articles/security/fundamentals/overview.md) eller [Windows](../articles/virtual-machines/windows/tutorial-azure-security.md).
