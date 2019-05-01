---
title: Säker åtkomst till key vault – Azure Key Vault | Microsoft Docs
description: Hantera åtkomstbehörigheter för Nyckelvalv, nycklar och hemligheter. Beskriver modellen för autentisering och auktorisering för Key Vault och hur du säkrar ditt nyckelvalv.
services: key-vault
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: a88451403f242c39212c80e3c7425a901c6819cc
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64725280"
---
# <a name="secure-access-to-a-key-vault"></a>Säker åtkomst till ett nyckelvalv

Azure Key Vault är en molnbaserad tjänst som skyddar krypteringsnycklar och hemligheter som certifikat, anslutningssträngar och lösenord. Eftersom dessa data är känsliga och verksamhetskritiska, som du behöver för att skydda åtkomst till dina nyckelvalv genom att tillåta att endast auktoriserade program och användare. Den här artikeln innehåller en översikt över åtkomstmodellen för Nyckelvalvet. Det förklarar autentisering och auktorisering och beskriver hur du skyddar åtkomsten till dina nyckelvalv.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="access-model-overview"></a>Översikt över åtkomst

Åtkomst till ett nyckelvalv styrs via två gränssnitt: den **Hanteringsplanet** och **dataplanet**. Hanteringsplanet är där du hanterar Key Vault själva. Åtgärder i den här plan omfattar skapa och ta bort nyckelvalv, hämta egenskaper för Key Vault och åtkomstprinciperna. Dataplanet är där du arbetar med data som lagras i ett nyckelvalv. Du kan lägga till, ta bort och ändra nycklar, hemligheter och certifikat.

Om du vill komma åt ett nyckelvalv i antingen plan måste måste alla anropare (användare eller program) ha korrekt autentisering och auktorisering. Autentisering etablerar identiteten hos anroparen. Auktoriseringen avgör vilka åtgärder som anroparen kan köra. 

Båda planen använder Azure Active Directory (Azure AD) för autentisering. För auktorisering använder rollbaserad åtkomstkontroll (RBAC) för Hanteringsplanet och dataplanet använder en åtkomstprincip för Nyckelvalvet.

## <a name="active-directory-authentication"></a>Active Directory-autentisering

När du skapar ett nyckelvalv i en Azure-prenumeration associeras den automatiskt med Azure AD-klient för prenumerationen. Alla anropare i båda planen måste registrera i den här klienten och autentisera sig för att få åtkomst till nyckelvalvet. Program i båda fallen kan de komma åt Nyckelvalvet på två sätt:

- **Användar- plus åtkomst**: Programmet har åtkomst till Nyckelvalvet åt en inloggad användare. Exempel på den här typen av åtkomst är Azure PowerShell och Azure-portalen. Åtkomst beviljas på två sätt. Användare kan komma åt Nyckelvalvet från valfritt program eller de måste använda ett visst program (kallas _sammansatt identitet_).
- **Enbart åtkomst**: Programmet körs som en daemon service eller BITS-jobb. Programidentiteten beviljas åtkomst till nyckelvalvet.

För båda typer av åtkomst till programmet som autentiserar med Azure AD. Programmet använder någon [stöds autentiseringsmetod](../active-directory/develop/authentication-scenarios.md) baserat på vilken typ av program. Programmet hämtar en token för en resurs i plan att bevilja åtkomst. Resursen är en slutpunkt i plan management eller data baserat på Azure-miljön. Programmet använder token och skickar en REST API-begäran till Key Vault. Om du vill veta mer kan du granska den [hela autentiseringsflödet](../active-directory/develop/v1-protocols-oauth-code.md).

Modell för en enda mekanism för autentisering på båda planen har flera fördelar:

- Organisationer kan styra åtkomsten centralt till alla nyckelvalv i organisationen.
- Om en användare lämnar förlorar de omedelbart åtkomst till alla nyckelvalv i organisationen.
- Organisationer kan anpassa autentisering via alternativen i Azure AD, som att aktivera Multi-Factor authentication för extra säkerhet.

## <a name="resource-endpoints"></a>Resurs-slutpunkter

Program för att komma åt plan via slutpunkter. Åtkomstkontroller för de två plan arbeta på egen hand. Om du vill ge ett programåtkomst för att använda nycklar i key vault, tilldela dataplansåtkomst med hjälp av en åtkomstprincip för Nyckelvalvet. Om du vill ge en användare läsåtkomst till Key Vault-egenskaper och taggar, men inte åtkomst till data (nycklar, hemligheter eller certifikat), kan du ge hanteringsplansåtkomst med RBAC.

I följande tabell visar slutpunkterna för hantering och data plan.

| Åtkomst&nbsp;plan | Slutpunkter för åtkomst | Åtgärder | Åtkomst&nbsp;styra mekanism |
| --- | --- | --- | --- |
| Hanteringsplanet | **Globalt:**<br> management.azure.com:443<br><br> **Azure Kina 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure för amerikanska myndigheter:**<br> management.usgovcloudapi.net:443<br><br> **Azure i Tyskland:**<br> management.microsoftazure.de:443 | Skapa, läsa, uppdatera och ta bort nyckelvalv<br><br>Ställa in åtkomstprinciper för Key Vault<br><br>Ställa in etiketter för Key Vault | Azure Resource Manager RBAC |
| Dataplanet | **Globalt:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure Kina 21Vianet:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure för amerikanska myndigheter:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure i Tyskland:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | Nycklar: dekryptera, kryptera,<br> Packa upp, omsluta, verifiera, signera,<br> Hämta, lista, uppdatera, skapa,<br> Importera, ta bort, säkerhetskopiering, återställning<br><br> Hemligheter: hämta, lista, Ställ in, ta bort | Åtkomstprincip för Nyckelvalvet |

## <a name="management-plane-and-rbac"></a>Hanteringsplanet och RBAC

I Hanteringsplanet använder du RBAC (rollen baserat Access Control) för att auktorisera de åtgärder som kan köra en anropare. Varje Azure-prenumeration har en instans av Azure AD i RBAC-modellen. Du beviljar åtkomst till användare, grupper och program från den här katalogen. Komma åt för att hantera resurser i Azure-prenumeration som använder Azure Resource Manager-distributionsmodellen. Om du vill bevilja åtkomst, använda den [Azure-portalen](https://portal.azure.com/), [Azure CLI](../cli-install-nodejs.md), [Azure PowerShell](/powershell/azureps-cmdlets-docs), eller [Azure Resource Manager REST API: er](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Du skapar ett nyckelvalv i en resursgrupp och hantera åtkomst med hjälp av Azure AD. Du ger användare eller grupper möjligheten att hantera nyckelvalv i en resursgrupp. Du ger åtkomst på en specifik omfattning genom att tilldela lämpliga RBAC-roller. Om du vill bevilja åtkomst till en användare att hantera nyckelvalven, tilldelar du en fördefinierad `key vault Contributor` roll till användare i ett visst omfång. Följande omfattningar nivåer kan tilldelas till RBAC-roll:

- **Prenumeration**: En RBAC-roll som tilldelats på prenumerationsnivå gäller för alla resursgrupper och resurser inom den prenumerationen.
- **Resursgrupp**: En RBAC-roll som tilldelats på resursgruppsnivå gäller för alla resurser i resursgruppen.
- **Specifik resurs**: En RBAC-roll som tilldelats för en specifik resurs gäller till den resursen. Resursen är i det här fallet ett visst nyckelvalv.

Det finns flera fördefinierade roller. Om den fördefinierade rollen inte passar dina behov, kan du definiera en egen roll. Mer information finns i [RBAC: inbyggda roller](../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Om en användare har `Contributor` behörigheter till ett nyckelvalv Hanteringsplanet användaren kan ge sig själva åtkomst till dataplanet genom att ange en åtkomstprincip för Nyckelvalvet. Du bör begränsa vem som har `Contributor` platssystemrollens åtkomst till dina nyckelvalv. Se till att endast auktoriserade personer kan komma åt och hantera dina nyckelvalv, nycklar, hemligheter och certifikat.
>

<a id="data-plane-access-control"></a> 
## <a name="data-plane-and-access-policies"></a>Principer för plan och åtkomst av data

Du kan bevilja åtkomst till dataplanet genom att ställa in åtkomstprinciper för Key Vault för ett nyckelvalv. Om du vill ange dessa principer för åtkomst, en användare, grupp eller program måste ha `Contributor` behörigheter för Hanteringsplanet för det nyckelvalvet.

Du kan ge en användare, grupp eller åtkomst till ett program för att utföra specifika åtgärder för nycklar eller hemligheter i key vault. Nyckelvalv stöder upp till 1 024 åtkomstprinciper för ett nyckelvalv. Skapa en Azure AD-säkerhetsgrupp för att tilldela dataplansåtkomst till flera användare och lägga till användare i gruppen.

<a id="key-vault-access-policies"></a> Åtkomstprinciper för Nyckelvalvet ger separata behörigheter separat till nycklar, hemligheter och certifikat. Du kan ge en användaråtkomst endast till nycklar och inte till hemligheter. Åtkomstbehörighet för nycklar, hemligheter och certifikat är på valvnivå. Åtkomstprinciper för Key Vault stöder inte detaljerade, på objektnivå behörigheter som en viss nyckel eller hemlighet certifikat. Om du vill ställa in åtkomstprinciper för ett nyckelvalv, använda den [Azure-portalen](https://portal.azure.com/), [Azure CLI](../cli-install-nodejs.md), [Azure PowerShell](/powershell/azureps-cmdlets-docs), eller [Key Vault Management REST API: er](https://msdn.microsoft.com/library/azure/mt620024.aspx).

> [!IMPORTANT]
> Åtkomstprinciper för Nyckelvalv gäller på valvnivå. När en användare beviljas behörighet att skapa och ta bort nycklar, kan de utföra de åtgärderna på alla nycklar i nyckelvalvet.
>

Du kan begränsa åtkomst till dataplanet genom att använda [tjänstslutpunkter i virtuella nätverk för Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). Du kan konfigurera [brandväggar och virtuella Nätverksregler](key-vault-network-security.md) för ett extra säkerhetslager.

## <a name="example"></a>Exempel

I det här exemplet vi håller på att utveckla ett program som använder ett certifikat för SSL, Azure Storage för att lagra data och en RSA 2 048-bitars nyckel för signeringsåtgärder. Vårt program körs i en Azure-dator (VM) (eller en VM-skalningsuppsättning). Vi kan använda ett nyckelvalv för att lagra programmets hemligheter. Vi kan lagra bootstrap-certifikatet som används av programmet för att autentisera med Azure AD.

Vi behöver åtkomst till följande lagrade nycklar och hemligheter:
- **SSL-certifikat**: Används för SSL.
- **Lagringsnyckeln**: Används för att komma åt lagringskontot.
- **2 048-bitars RSA-nyckel**: Används för signeringsåtgärder.
- **Bootstrap-certifikatet**: För att autentisera med Azure AD. När åtkomst beviljas kan vi hämta lagringsnyckeln och använda RSA-nyckel för signering.

Vi måste du definiera följande roller för att ange vem som kan hantera, distribuera och granska vårt program:
- **Säkerhetsteamet**: IT-personal från kontoret för CSO (Chief Security Officer) eller liknande deltagare. Säkerhetsteamet ansvarar för för att hemligheter. Hemligheterna kan bestå av SSL-certifikat, RSA-nycklar för signering, anslutningssträngar och storage-kontonycklar.
- **Utvecklare och operatörer**: Personal som utvecklar programmet och distribuerar det i Azure. Medlemmarna i gruppen är inte en del av säkerhetspersonalen. De får inte ha åtkomst till känsliga data som SSL-certifikat och RSA-nycklar. Endast det program som de distribuerar ska ha åtkomst till känsliga data.
- **Granskare**: Den här rollen är för deltagare som inte är medlemmar i utvecklings- eller allmän IT-personal. De granska användning och underhåll av certifikat, nycklar och hemligheter för att säkerställa att säkerhetsstandarder. 

Det finns en annan roll som inte omfattas av vårt program: prenumerationen (eller resursgrupp) administratör. Administratören för prenumeration ställer in inledande åtkomstbehörigheter för säkerhetsteamet. De ger åtkomst till säkerhetsteamet med hjälp av en resursgrupp som innehåller de resurser som krävs av programmet.

Vi måste du godkänna följande åtgärder för vår roller:

**Säkerhetsteamet**
- Skapa nyckelvalv.
- Aktivera Key Vault-loggning.
- Lägg till nycklar och hemligheter.
- Skapa säkerhetskopior av nycklar för haveriberedskap.
- Ställa in åtkomstprinciper för Key Vault för att ge behörigheter till användare och program för specifika åtgärder.
- Distribuera nycklar och hemligheter med jämna mellanrum.

**Utvecklare och operatorer**
- Hämta referenser från säkerhetsteamet för bootstrap och SSL-certifikat (tumavtryck), lagringsnyckeln (hemlig-URI) och RSA-nyckel (nyckel URI: N) för signering.
- Utveckla och distribuera programmet till programmässig åtkomst nycklar och hemligheter.

**Granskare**
- Granska Key Vault-loggarna för att bekräfta korrekt användning av nycklar och hemligheter och data säkerhetsstandarder efterlevs.

I följande tabell sammanfattas åtkomstbehörigheter för våra roller och programmet. 

| Roll | Behörigheter på hanteringsplanet | Behörigheter på dataplanet |
| --- | --- | --- |
| Säkerhetsteamet | Nyckelvalvsdeltagare | Nycklar: Säkerhetskopiera, skapa, ta bort, hämta, importera, lista, återställa<br>Hemligheter: alla åtgärder för |
| Utvecklare och&nbsp;operatörer | Nyckelvalvet distribueringsbehörighet<br><br> **Obs!** Den här behörigheten kan distribuerade virtuella datorer att hämta hemligheter från key vault. | Ingen |
| Granskare | Ingen | Nycklar: lista<br>Hemligheter: lista<br><br> **Obs!** Den här behörigheten kan granskare att granska attribut (taggar, aktivering datum, förfallodatum) för nycklar och hemligheter som inte släpps i loggarna. |
| Program | Ingen | Nycklar: signera<br>Hemligheter: hämta |

De tre team-rollerna behöver åtkomst till andra resurser tillsammans med Key Vault-behörigheter. Distribuera virtuella datorer (eller funktionen Web Apps i Azure App Service), utvecklare och operatörer behöver `Contributor` åtkomst till de resurstyperna. Granskare behöver läsåtkomst till det lagringskonto där Key Vault-loggar lagras.

Mer information om hur du distribuerar certifikat programmässig åtkomst nycklar och hemligheter, se följande resurser:
- Lär dig hur du [distribuera certifikat till virtuella datorer från en key vault för Kundhanterade](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (blogginlägg).
- Ladda ned den [Azure Key Vault-klienten exempel](https://www.microsoft.com/download/details.aspx?id=45343). Det här innehållet visar hur du använder bootstrap-certifikatet för att autentisera till Azure AD för att komma åt ett nyckelvalv.

Du kan bevilja de flesta åtkomstbehörigheterna med hjälp av Azure portal. Om du vill tilldela detaljerade behörigheter kan du använda Azure PowerShell eller Azure CLI.

PowerShell-kodfragmenten i det här avsnittet skapas med följande antaganden:
- Azure AD-administratör har skapat säkerhetsgrupper som representerar tre roller: Contoso Security Team, Contoso App DevOps och Contoso App granskare. Administratören har lagt till användare till sina respektive grupper.
- Alla resurser som finns i den **ContosoAppRG** resursgrupp.
- Key Vault-loggar lagras i den **contosologstorage** storage-konto. 
- Den **ContosoKeyVault** nyckelvalv och **contosologstorage** storage-konto finns i samma Azure-plats.

Prenumerationen-administratören har tilldelat den `key vault Contributor` och `User Access Administrator` roller till säkerhetsteamet. Dessa roller Tillåt säkerhetsteam för att hantera åtkomst till andra resurser och nyckelvalv, som i den **ContosoAppRG** resursgrupp.

```powershell
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Säkerhetsteamet skapar ett nyckelvalv och ställer in loggning och åtkomstbehörighet. Mer information om principen för Key Vault behörigheter finns i [om Azure Key Vault-nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md).

```powershell
# Create a key vault and enable logging
$sa = Get-AzStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzKeyVault -Name ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent

# Set up data plane permissions for the Contoso Security Team role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Set up management plane permissions for the Contoso App DevOps role
# Create the new role from an existing role
$devopsrole = Get-AzRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App DevOps"
$devopsrole.Description = "Can deploy VMs that need secrets from a key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permissions for the Contoso App DevOps role so members can deploy VMs with secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzRoleDefinition -Role $devopsrole

# Assign the new role to the Contoso App DevOps security group
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Set up data plane permissions for the Contoso App Auditors role
Set-AzKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

Vår definierade anpassade roller är tilldelningsbar endast till prenumerationen där den **ContosoAppRG** resursgruppen har skapats. För att använda en anpassad roll för andra projekt i andra prenumerationer måste du lägga till andra prenumerationer i omfånget för rollen.

För vår DevOps-personal, den anpassade rolltilldelningen för key vault `deploy/action` behörighet begränsas till resursgruppen. Endast virtuella datorer som skapas i den **ContosoAppRG** resursgruppen har åtkomst till hemligheterna (SSL och bootstrap-certifikat). Virtuella datorer som skapas i andra resursgrupper av en DevOps-medlem kan inte komma åt dessa hemligheter, även om den virtuella datorn har hemliga URI: er.

Vårt exempel beskriver ett enkelt scenario. Verkliga scenarier kan vara mer komplex. Du kan justera behörigheterna till ditt nyckelvalv baserat på dina behov. Vi antas säkerhetsteamet ger nyckel och hemliga referenser (URI: er och tumavtryck) som används av DevOps-personal i sina program. Utvecklare och operatörer kräver inte någon dataplansåtkomst. Fokuserar vi på hur du skyddar ditt nyckelvalv. Liknande beakta när du skyddar [dina virtuella datorer](https://azure.microsoft.com/services/virtual-machines/security/), [lagringskonton](../storage/common/storage-security-guide.md), och andra Azure-resurser.

> [!NOTE]
> Det här exemplet visar hur åtkomst för Nyckelvalvet är låsta i produktion. Utvecklare bör ha sin egen prenumeration eller resursgrupp grupp med fullständig behörighet att hantera sina valv, virtuella datorer och storage-konto där de utvecklar programmet.

Vi rekommenderar att du ställer in ytterligare säker åtkomst till nyckelvalvet genom [konfigurera Key Vault-brandväggar och virtuella nätverk](key-vault-network-security.md).

## <a name="resources"></a>Resurser

* [Azure AD RBAC](../role-based-access-control/role-assignments-portal.md)

* [RBAC: Inbyggda roller](../role-based-access-control/built-in-roles.md)

* [Förstå Resource Manager-distribution och klassisk distribution](../azure-resource-manager/resource-manager-deployment-model.md) 

* [Hantera rollbaserad Åtkomstkontroll med Azure PowerShell](../role-based-access-control/role-assignments-powershell.md) 

* [Hantera rollbaserad Åtkomstkontroll med REST API](../role-based-access-control/role-assignments-rest.md)

* [RBAC för Microsoft Azure](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Den här 2015 Microsoft Ignite konferens video beskrivs åt åtkomsthantering och rapporteringsfunktioner i Azure. Det kan också utforskar bästa praxis för att skydda åtkomsten till Azure-prenumerationer med hjälp av Azure AD.

* [Auktorisera åtkomst till webbprogram med hjälp av OAuth 2.0 och Azure AD](../active-directory/develop/v1-protocols-oauth-code.md)

* [Hantering av Nyckelvalv REST API: er](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    Referens för REST-API: er att hantera din nyckel valvet programmässigt, inklusive inställning av åtkomstprincip för Nyckelvalvet.

* [Key Vault REST API: er](https://msdn.microsoft.com/library/azure/dn903609.aspx)

* [Nyckelåtkomstkontroll](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
* [Åtkomstkontroll till hemlighet](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
* [Ange](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) och [ta bort](/powershell/module/az.keyvault/Remove-azKeyVaultAccessPolicy) åtkomstprincip för Key Vault med hjälp av PowerShell.
  
## <a name="next-steps"></a>Nästa steg

Konfigurera [Key Vault-brandväggar och virtuella nätverk](key-vault-network-security.md).

En komma igång-självstudiekurs för administratörer finns i [vad är Azure Key Vault?](key-vault-overview.md).

Mer information om användningsloggning för Key Vault finns i avsnittet om [Azure Key Vault-loggning](key-vault-logging.md).

Mer information om hur du använder nycklar och hemligheter med Azure Key Vault finns i [om nycklar och hemligheter](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Om du har frågor om Key Vault kan du gå till den [forum](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
