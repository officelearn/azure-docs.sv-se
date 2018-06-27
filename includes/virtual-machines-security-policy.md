Det är viktigt att skydda den virtuella datorn (VM) för de program som du kör. Skydda dina virtuella datorer kan innehålla en eller flera Azure-tjänster och funktioner som täcker säker åtkomst till dina virtuella datorer och säker lagring av dina data. Den här artikeln innehåller information som gör att du kan skydda dina VM och program.

## <a name="antimalware"></a>Programvara mot skadlig kod

Moderna hotbilden i grunden för molnmiljöer är dynamisk, öka trycket att upprätthålla effektiv skydd för att uppfylla efterlevnads- och säkerhetskrav. [Microsoft Antimalware för Azure](../articles/security/azure-security-antimalware.md) är en kostnadsfri realtidsskydd funktion som hjälper dig att identifiera och ta bort virus, spionprogram och annan skadlig programvara. Aviseringar kan konfigureras för att meddela dig när känd skadlig eller oönskad programvara försöker installera sig själva eller köras på den virtuella datorn.

## <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../articles/security-center/security-center-intro.md) hjälper till att förebygga, upptäcka och svara på hot mot din virtuella dator. Security Center innehåller integrerad säkerhet övervaka och hantera principer för dina Azure-prenumerationer, och upptäcka hot som annars kanske skulle förbli oupptäckta, fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

## <a name="encryption"></a>Kryptering

För förbättrad [Windows VM](../articles/virtual-machines/windows/encrypt-disks.md) och [Linux VM](../articles/virtual-machines/linux/encrypt-disks.md) säkerhet och godkännande, virtuella diskar i Azure kan krypteras. Virtuella diskar på virtuella Windows-datorer krypteras vilande med Bitlocker. Virtuella diskar på virtuella Linux-datorer krypteras vilande med dm-crypt. 

Det är gratis för kryptering av virtuella diskar i Azure. Kryptografiska nycklar lagras i Azure Key Vault med skydd av programvara eller du kan importera eller generera dina nycklar i Maskinvarusäkerhetsmoduler (HSM) certifierade FIPS 140-2 level 2-standarder. Dessa kryptografiska nycklar används för att kryptera och dekryptera virtuella diskar som är anslutna till den virtuella datorn. Du behålla kontrollen över dessa kryptografiska nycklar och granska deras användning. Ett Azure Active Directory-tjänstens huvudnamn ger en säker mekanism för att utfärda de kryptografiska nycklarna som virtuella datorer är igång på och stänga av.

## <a name="key-vault-and-ssh-keys"></a>Key Vault och SSH-nycklar

Hemligheter och certifikat kan modelleras som resurser och tillhandahålls av [Nyckelvalvet](../articles/key-vault/key-vault-whatis.md). Du kan använda Azure PowerShell för att skapa nyckelvalv för [virtuella Windows-datorer](../articles/virtual-machines/windows/key-vault-setup.md) och Azure CLI för [virtuella Linux-datorer](../articles/virtual-machines/linux/key-vault-setup.md). Du kan också skapa nycklar för kryptering.

Nyckelvalv åtkomstprinciper bevilja behörighet till nycklar och hemligheter certifikat separat. Du kan till exempel ge en användare åtkomst till bara nycklar, men inte hemligheter. Behörighet att komma åt nycklar eller hemligheter eller certifikat är dock valvnivå. Med andra ord [nyckeln valvet åtkomstprincip](../articles/key-vault/key-vault-secure-your-key-vault.md) stöder inte objektbehörighet på användarnivå.

När du ansluter till virtuella datorer, använder du offentliga nycklar för att tillhandahålla ett säkrare sätt att logga in. Den här processen omfattar ett offentliga och privata nyckelutbyte för kommandot secure shell (SSH) för att autentisera dig själv i stället för användarnamn och lösenord. Lösenord är sårbara för brute force-attacker, särskilt på virtuella datorer mot Internet, till exempel webbservrar. Med ett nyckelpar för secure shell (SSH), kan du skapa en [Linux VM](../articles/virtual-machines/linux/mac-create-ssh-keys.md) som använder SSH-nycklar för autentisering, vilket eliminerar behovet av lösenord att logga in. Du kan också använda SSH-nycklar för att ansluta från en [Windows VM](../articles/virtual-machines/linux/ssh-from-windows.md) till en Linux-VM.

## <a name="policies"></a>Principer

[Azure principer](../articles/azure-policy/azure-policy-introduction.md) kan användas för att definiera önskat beteende för din organisation [virtuella Windows-datorer](../articles/virtual-machines/windows/policy.md) och [virtuella Linux-datorer](../articles/virtual-machines/linux/policy.md). En organisation kan tillämpa olika konventioner och regler i hela företaget med hjälp av principer. Tillämpning av önskat beteende kan du minimera risken när bidrar till att organisationen.

## <a name="role-based-access-control"></a>Rollbaserad åtkomstkontroll

Med hjälp av [rollbaserad åtkomstkontroll (RBAC)](../articles/role-based-access-control/overview.md), du kan särskilja uppgifter i din grupp och ge bara mängden åtkomst till användare på den virtuella datorn som de behöver för att utföra sitt arbete. Istället för att ge alla obegränsad behörighet på den virtuella datorn kan du tillåta endast vissa åtgärder. Du kan konfigurera åtkomstkontroll för den virtuella datorn i den [Azure-portalen](../articles/role-based-access-control/role-assignments-portal.md)med hjälp av den [Azure CLI](https://docs.microsoft.com/cli/azure/role), eller[Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).


## <a name="next-steps"></a>Nästa steg
- Gå igenom stegen för att övervaka virtuella säkerhet med hjälp av Azure Security Center för [Linux](../articles/virtual-machines/linux/tutorial-azure-security.md) eller [Windows](../articles/virtual-machines/windows/tutorial-azure-security.md).