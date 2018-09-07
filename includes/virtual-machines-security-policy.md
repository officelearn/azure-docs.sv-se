Det är viktigt för att skydda din virtuella dator (VM) för de program som du kör. Skydda dina virtuella datorer kan innehålla en eller flera Azure-tjänster och funktioner som täcker säker åtkomst till dina virtuella datorer och säker lagring av dina data. Den här artikeln innehåller information som hjälper dig att skydda dina virtuella datorer och program.

## <a name="antimalware"></a>Programvara mot skadlig kod

Moderna hotbilden i grunden för molnmiljöer är dynamiskt öka trycket att upprätthålla effektiv skydd för att uppfylla efterlevnads- och säkerhetskrav. [Microsoft Antimalware för Azure](../articles/security/azure-security-antimalware.md) är en kostnadsfri realtidsskydd-funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig programvara. Aviseringar kan konfigureras för att meddela dig när känd skadlig eller oönskad programvara försöker installera sig själva eller köras på den virtuella datorn.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../articles/security-center/security-center-intro.md) hjälper till att förhindra, upptäcka och svara på hot mot dina virtuella datorer. Security Center tillhandahåller integrerad säkerhet övervaka och hantera principer för alla Azure-prenumerationer, upptäcka hot som kan annars oupptäckta och fungerar med ett vittomfattande ekosystem med säkerhetslösningar.

## <a name="encryption"></a>Kryptering

För förbättrad [Windows VM](../articles/virtual-machines/windows/encrypt-disks.md) och [Linux VM](../articles/virtual-machines/linux/encrypt-disks.md) säkerhet och efterlevnad, virtuella diskar i Azure kan krypteras. Virtuella diskar på Windows virtuella datorer krypteras i vila med Bitlocker. Virtuella diskar på virtuella Linux-datorer krypteras i vila med dm-crypt. 

Det är kostnadsfritt för att kryptera virtuella diskar i Azure. Kryptografiska nycklar lagras i Azure Key Vault med hjälp av software protection eller kan du importera eller generera dina nycklar i Maskinvarusäkerhetsmoduler (HSM) som är certifierade enligt standarderna FIPS 140-2 nivå 2-standarder. Dessa kryptografiska nycklar används för att kryptera och dekryptera virtuella diskar som är anslutna till den virtuella datorn. Du kan behålla kontrollen över dessa kryptografiska nycklar och kan granska deras användning. En Azure Active Directory-tjänstens huvudnamn är en säker mekanism för att utfärda dessa kryptografiska nycklar som virtuella datorer tillhandahålls av och sätts på.

## <a name="key-vault-and-ssh-keys"></a>Key Vault och SSH-nycklar

Hemligheter och certifikat kan modelleras som resurser och tillhandahålls av [Key Vault](../articles/key-vault/key-vault-whatis.md). Du kan använda Azure PowerShell för att skapa nyckelvalv för [Windows VMs](../articles/virtual-machines/windows/key-vault-setup.md) och Azure CLI för [virtuella Linux-datorer](../articles/virtual-machines/linux/key-vault-setup.md). Du kan också skapa nycklar för kryptering.

Åtkomstprinciper för nyckelvalvet ger separata behörigheter till nycklar, hemligheter och certifikat. Du kan till exempel ge en användare åtkomst till bara nycklar, men inte hemligheter. Behörighet att komma åt nycklar eller hemligheter eller certifikat är dock valvnivå. Med andra ord [nyckeln åtkomstprinciper](../articles/key-vault/key-vault-secure-your-key-vault.md) stöder inte behörigheter på objektsnivå.

Du bör använda kryptografi med offentliga nycklar för att tillhandahålla ett säkrare sätt att logga in på dem när du ansluter till virtuella datorer. Den här processen innefattar en offentliga och privata nyckelutbyte som kommandot secure shell (SSH) för att autentisera dig själv i stället för ett användarnamn och lösenord. Lösenord är sårbara för brute force-attacker, särskilt på Internet-riktade virtuella datorer, till exempel webbservrar. Med en secure shell (SSH)-nyckelpar kan du skapa en [Linux VM](../articles/virtual-machines/linux/mac-create-ssh-keys.md) som använder SSH-nycklar för autentisering, vilket eliminerar behovet av lösenord för inloggning. Du kan också använda SSH-nycklar för att ansluta från en [Windows VM](../articles/virtual-machines/linux/ssh-from-windows.md) till en Linux-VM.

## <a name="managed-identities-for-azure-resources"></a>Hanterade identiteter för Azure-resurser

En vanlig utmaning vid utvecklingen av molnprogram är hur man ska hantera autentiseringsuppgifterna i koden som krävs för autentisering mot molntjänsterna. Det är viktigt att dessa autentiseringsuppgifter skyddas. Helst bör autentiseringsuppgifterna aldrig visas på utvecklarnas arbetsstationer eller checkas in i källkontrollen. Azure Key Vault är ett sätt att lagra autentiseringsuppgifter, hemligheter och andra nycklar på ett säkert sätt, men din kod måste autentisera mot Key Vault för att kunna hämta dem. 

Hanterade identiteter för Azure-resurser funktion i Azure Active Directory (Azure AD) löser detta problem. Funktionen förser Azure-tjänster med en automatiskt hanterad identitet i Azure AD. Du kan använda identiteten för att autentisera mot alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan att du behöver lägga in några autentiseringsuppgifter i din kod.  Din kod som körs på en virtuell dator kan begära en token från två slutpunkter som är enbart tillgänglig från den virtuella datorn. Mer detaljerad information om den här tjänsten kan du granska den [hanterade identiteter för Azure-resurser](../articles/active-directory/managed-identities-azure-resources/overview.md) översiktssidan.   

## <a name="policies"></a>Principer

[Azure-principer](../articles/azure-policy/azure-policy-introduction.md) kan användas för att definiera önskat beteende för din organisation [Windows VMs](../articles/virtual-machines/windows/policy.md) och [virtuella Linux-datorer](../articles/virtual-machines/linux/policy.md). Med hjälp av principer kan en organisation tillämpa olika konventioner och regler i hela företaget. Efterlevnad av önskat beteende kan du minimera risken när bidrar till framgång för organisationen.

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Med hjälp av [rollbaserad åtkomstkontroll (RBAC)](../articles/role-based-access-control/overview.md), du kan hålla isär uppgifter i ditt team och bevilja endast mängden åtkomst till användare på din virtuella dator som de behöver för att utföra sitt arbete. Istället för att ge alla obegränsad behörighet på den virtuella datorn kan du tillåta enbart vissa åtgärder. Du kan konfigurera åtkomstkontroll för den virtuella datorn i den [Azure-portalen](../articles/role-based-access-control/role-assignments-portal.md)med hjälp av den [Azure CLI](https://docs.microsoft.com/cli/azure/role), eller[Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).


## <a name="next-steps"></a>Nästa steg
- Gå igenom stegen för att övervaka säkerhet för virtuella datorer med hjälp av Azure Security Center för [Linux](../articles/virtual-machines/linux/tutorial-azure-security.md) eller [Windows](../articles/virtual-machines/windows/tutorial-azure-security.md).