---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d1ec61bf18248ea56c8ee5e430a671af7f39d732
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458682"
---
Det är viktigt att skydda den virtuella datorn (VM) för de program som du kör. Skydda dina virtuella datorer kan innehålla en eller flera Azure-tjänster och funktioner som täcker säker åtkomst till dina virtuella datorer och säker lagring av dina data. Den här artikeln innehåller information som gör att du kan skydda din virtuella dator och program.

## <a name="antimalware"></a>Antimalware

Det moderna hotlandskapet för molnmiljöer är dynamiskt, vilket ökar trycket att upprätthålla ett effektivt skydd för att uppfylla efterlevnads- och säkerhetskraven. [Microsoft Antimalware för Azure](../articles/security/fundamentals/antimalware.md) är en kostnadsfri realtidsskyddsfunktion som hjälper till att identifiera och ta bort virus, spionprogram och annan skadlig programvara. Aviseringar kan konfigureras för att meddela dig när kända skadliga eller oönskade program försöker installera sig själv eller köras på den virtuella datorn. Det stöds inte på virtuella datorer som kör Linux eller Windows Server 2008.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../articles/security-center/security-center-intro.md) hjälper dig att förebygga, identifiera och svara på hot mot dina virtuella datorer. Security Center tillhandahåller integrerad säkerhetsövervakning och principhantering i dina Azure-prenumerationer, hjälper till att identifiera hot som annars kan gå obemärkt förbi och arbetar med ett brett ekosystem av säkerhetslösningar.

Security Centers just-in-time-åtkomst kan användas i hela vm-distributionen för att låsa inkommande trafik till dina virtuella Azure-datorer, vilket minskar exponeringen för attacker samtidigt som du enkelt kan ansluta till virtuella datorer när det behövs. När just-in-time är aktiverat och en användare begär åtkomst till en virtuell dator kontrollerar Security Center vilka behörigheter användaren har för den virtuella datorn. Om de har rätt behörigheter godkänns begäran och Security Center konfigurerar automatiskt NSG (Network Security Groups) så att inkommande trafik tillåts till de valda portarna under en begränsad tid. När tiden har gått ut återställer Security Center NSG:erna till sina tidigare tillstånd. 

## <a name="encryption"></a>Kryptering

Två krypteringsmetoder erbjuds för hanterade diskar. Kryptering på OS-nivå, som är Azure Disk Encryption, och kryptering på plattformsnivå, vilket är kryptering på serversidan.

### <a name="server-side-encryption"></a>Kryptering på serversidan

Azure-hanterade diskar krypterar automatiskt dina data som standard när de sparas i molnet. Kryptering på serversidan skyddar dina data och hjälper dig att uppfylla dina organisatoriska säkerhets- och efterlevnadsåtaganden. Data i Azure-hanterade diskar krypteras transparent med 256-bitars [AES-kryptering](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), ett av de starkaste blockchiffer som finns och är FIPS 140-2-kompatibel.

Kryptering påverkar inte prestanda för hanterade diskar. Det finns ingen extra kostnad för krypteringen.

Du kan lita på plattformshanterade nycklar för kryptering av den hanterade disken eller hantera kryptering med dina egna nycklar. Om du väljer att hantera kryptering med dina egna nycklar kan du ange en *kundhanterad nyckel* som ska användas för att kryptera och dekryptera alla data i hanterade diskar. 

Mer information om kryptering på serversidan finns i antingen artiklarna för [Windows](../articles/virtual-machines/windows/disk-encryption.md) eller [Linux](../articles/virtual-machines/linux/disk-encryption.md).

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

För förbättrad säkerhet och efterlevnad av [Windows VM](../articles/virtual-machines/windows/disk-encryption-overview.md) och [Linux VM](../articles/virtual-machines/linux/disk-encryption-overview.md) kan virtuella diskar i Azure krypteras. Virtuella diskar på virtuella Windows-datorer krypteras i vila med BitLocker. Virtuella diskar på Linux virtuella datorer krypteras i vila med dm-crypt. 

Det finns ingen avgift för att kryptera virtuella diskar i Azure. Kryptografiska nycklar lagras i Azure Key Vault med programvaruskydd, eller så kan du importera eller generera dina nycklar i HSM (Hardware Security Modules) som är certifierade enligt FIPS 140-2-standarder. Dessa kryptografiska nycklar används för att kryptera och dekryptera virtuella diskar som är anslutna till den virtuella datorn. Du behåller kontrollen över dessa kryptografiska nycklar och kan granska deras användning. Ett Azure Active Directory-tjänsthuvudnamn tillhandahåller en säker mekanism för att utfärda dessa kryptografiska nycklar när virtuella datorer är påslagna och inaktiverade.

## <a name="key-vault-and-ssh-keys"></a>Key Vault och SSH-nycklar

Hemligheter och certifikat kan modelleras som resurser och tillhandahållas av [Key Vault](../articles/key-vault/key-vault-whatis.md). Du kan använda Azure PowerShell för att skapa nyckelvalv för [virtuella Windows-datorer](../articles/virtual-machines/windows/key-vault-setup.md) och virtuella Azure CLI för [virtuella Linux-datorer](../articles/virtual-machines/linux/key-vault-setup.md). Du kan också skapa nycklar för kryptering.

Åtkomstprinciper för nyckelvalv ger behörigheter till nycklar, hemligheter och certifikat separat. Du kan till exempel ge en användare åtkomst till bara nycklar, men inte hemligheter. Behörighet att komma åt nycklar eller hemligheter eller certifikat är dock valvnivå. Med andra ord stöder [princip för nyckelvalvsåtkomst](../articles/key-vault/key-vault-secure-your-key-vault.md) inte behörigheter på objektnivå.

När du ansluter till virtuella datorer bör du använda kryptering med offentliga nycklar för att ge ett säkrare sätt att logga in på dem. Den här processen innebär ett offentligt och privat nyckelutbyte med kommandot Secure Shell (SSH) för att autentisera dig själv i stället för ett användarnamn och lösenord. Lösenord är sårbara för brute-force-attacker, särskilt på internetvända virtuella datorer som webbservrar. Med ett säkert skal (SSH) nyckelpar kan du skapa en [Virtuell Linux-dator](../articles/virtual-machines/linux/mac-create-ssh-keys.md) som använder SSH-nycklar för autentisering, vilket eliminerar behovet av lösenord för att logga in. Du kan också använda SSH-nycklar för att ansluta från en Windows VM till en Virtuell Linux.You can also use SSH keys to connect from a [Windows VM](../articles/virtual-machines/linux/ssh-from-windows.md) to a Linux VM.

## <a name="managed-identities-for-azure-resources"></a>Hanterade identiteter för Azure-resurser

En vanlig utmaning vid utvecklingen av molnprogram är hur man ska hantera autentiseringsuppgifterna i koden som krävs för autentisering mot molntjänsterna. Det är viktigt att dessa autentiseringsuppgifter skyddas. Helst bör autentiseringsuppgifterna aldrig visas på utvecklarnas arbetsstationer eller checkas in i källkontrollen. Azure Key Vault är ett sätt att lagra autentiseringsuppgifter, hemligheter och andra nycklar på ett säkert sätt, men din kod måste autentisera mot Key Vault för att kunna hämta dem. 

Funktionen Hanterade identiteter för Azure-resurser i Azure Active Directory (Azure AD) löser det här problemet. Funktionen förser Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Du kan använda identiteten för att autentisera mot alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan att du behöver lägga in några autentiseringsuppgifter i din kod.  Din kod som körs på en virtuell dator kan begära en token från två slutpunkter som endast är tillgängliga från den virtuella datorn. Mer detaljerad information om den här tjänsten finns på [översiktssidan för hanterade identiteter för Azure-resurser.](../articles/active-directory/managed-identities-azure-resources/overview.md)   

## <a name="policies"></a>Principer

[Azure-principer](../articles/azure-policy/azure-policy-introduction.md) kan användas för att definiera önskat beteende för organisationens [virtuella datorer för Windows](../articles/virtual-machines/windows/policy.md) och virtuella [linux-datorer](../articles/virtual-machines/linux/policy.md). Genom att använda principer kan en organisation tillämpa olika konventioner och regler i hela företaget. Verkställighet av önskat beteende kan bidra till att minska risken och samtidigt bidra till organisationens framgång.

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Med hjälp av [rollbaserad åtkomstkontroll (RBAC)](../articles/role-based-access-control/overview.md)kan du segregera uppgifter inom teamet och endast bevilja åtkomst till användare på den virtuella datorn som de behöver för att utföra sina jobb. I stället för att ge alla obegränsad behörighet på den virtuella datorn kan du bara tillåta vissa åtgärder. Du kan konfigurera åtkomstkontroll för den virtuella datorn i [Azure-portalen](../articles/role-based-access-control/role-assignments-portal.md), med hjälp av [Azure CLI](https://docs.microsoft.com/cli/azure/role)eller[Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).


## <a name="next-steps"></a>Nästa steg
- Gå igenom stegen för att övervaka säkerheten för virtuella datorer med hjälp av Azure Security Center för [Linux](../articles/security/fundamentals/overview.md) eller [Windows](../articles/virtual-machines/windows/tutorial-azure-security.md).
