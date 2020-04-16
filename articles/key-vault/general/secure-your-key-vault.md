---
title: Säker åtkomst till ett nyckelvalv – Azure Key Vault | Microsoft-dokument
description: Hantera åtkomstbehörigheter för Azure Key Vault, nycklar och hemligheter. Täcker autentiserings- och auktoriseringsmodellen för Key Vault och hur du skyddar nyckelvalvet.
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 0ae1b26bb2e01d388f3f91d94134bb9723a5a305
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432026"
---
# <a name="secure-access-to-a-key-vault"></a>Säker åtkomst till ett nyckelvalv

Azure Key Vault är en molntjänst som skyddar krypteringsnycklar och hemligheter som certifikat, anslutningssträngar och lösenord. Eftersom dessa data är känsliga och affärskritiska måste du skydda åtkomsten till dina nyckelvalv genom att endast tillåta auktoriserade program och användare. Den här artikeln innehåller en översikt över åtkomstmodellen för Key Vault. Den förklarar autentisering och auktorisering och beskriver hur du skyddar åtkomsten till dina nyckelvalv.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="access-model-overview"></a>Översikt över Åtkomstmodell

Åtkomsten till ett nyckelvalv styrs via två gränssnitt: **hanteringsplanet** och **dataplanet**. Hanteringsplanet är där du hanterar Key Vault själv. Åtgärder i det här planet omfattar att skapa och ta bort nyckelvalv, hämta egenskaper för Nyckelvalv och uppdatera åtkomstprinciper. Dataplanet är där du arbetar med data som lagras i ett nyckelvalv. Du kan lägga till, ta bort och ändra nycklar, hemligheter och certifikat.

För att komma åt ett nyckelvalv i något av plan måste alla anropare (användare eller program) ha korrekt autentisering och auktorisering. Autentisering upprättar identiteten på den som ringer. Auktoriseringen avgör vilka åtgärder anroparen kan utföra.

Båda planen använder Azure Active Directory (Azure AD) för autentisering. För auktorisering använder hanteringsplanet rollbaserad åtkomstkontroll (RBAC) och dataplanet använder en åtkomstprincip för Key Vault.

## <a name="active-directory-authentication"></a>Active Directory-autentisering

När du skapar ett nyckelvalv i en Azure-prenumeration associeras det automatiskt med Azure AD-klienten för prenumerationen. Alla anropare i båda planen måste registrera sig i den här klienten och autentisera för att komma åt nyckelvalvet. I båda fallen kan program komma åt Key Vault på två sätt:

- **Åtkomst till användare plus program:** Programmet kommer åt Key Vault för en inloggad användares räkning. Exempel på den här typen av åtkomst är Azure PowerShell och Azure-portalen. Användaråtkomst beviljas på två sätt. Användare kan komma åt Key Vault från alla program, eller så måste de använda ett visst program (kallas _sammansatt identitet)._
- **Åtkomst endast till program**: Programmet körs som en daemontjänst eller bakgrundsjobb. Programidentiteten beviljas åtkomst till nyckelvalvet.

För båda typerna av åtkomst autentiserar programmet med Azure AD. Programmet använder alla [autentiseringsmetoder som stöds](../../active-directory/develop/authentication-scenarios.md) baserat på programtypen. Programmet hämtar en token för en resurs i planet för att bevilja åtkomst. Resursen är en slutpunkt i hanterings- eller dataplanet, baserat på Azure-miljön. Programmet använder token och skickar en REST API-begäran till Key Vault. Mer information finns i [hela autentiseringsflödet](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Modellen för en enda mekanism för autentisering till båda planen har flera fördelar:

- Organisationer kan styra åtkomsten centralt till alla viktiga valv i organisationen.
- Om en användare lämnar, förlorar de omedelbart åtkomst till alla viktiga valv i organisationen.
- Organisationer kan anpassa autentiseringen med hjälp av alternativen i Azure AD, till exempel för att aktivera multifaktorautentisering för ökad säkerhet.

## <a name="resource-endpoints"></a>Resursslutpunkter

Program kommer åt planen via slutpunkter. Åtkomstkontrollerna för de två planen fungerar oberoende av dem. Om du vill ge ett program åtkomst till att använda nycklar i ett nyckelvalv beviljar du dataplanåtkomst med hjälp av en åtkomstprincip för Key Vault. Om du vill ge en användare läsbehörighet och taggar för Key Vault, men inte åtkomst till data (nycklar, hemligheter eller certifikat), beviljar du åtkomst till hanteringsplan med RBAC.

I följande tabell visas slutpunkterna för hanterings- och dataplan.

| Tillträde&nbsp;plan | Slutpunkter för åtkomst | Åtgärder | Mekanism&nbsp;för åtkomstkontroll |
| --- | --- | --- | --- |
| Hanteringsplanet | **Globala:**<br> management.azure.com:443<br><br> **Azure China 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure för amerikanska myndigheter:**<br> management.usgovcloudapi.net:443<br><br> **Azure Tyskland:**<br> management.microsoftazure.de:443 | Skapa, läsa, uppdatera och ta bort nyckelvalv<br><br>Ange principer för nyckelvalv<br><br>Ange taggar för Nyckelvalv | Azure Resource Manager RBAC |
| Dataplanet | **Globala:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure China 21Vianet:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure för amerikanska myndigheter:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Tyskland:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | Nycklar: dekryptera, kryptera,<br> packa upp, slå in, verifiera, underteckna,<br> hämta, lista, uppdatera, skapa,<br> importera, ta bort, säkerhetskopiera, återställa<br><br> Hemligheter: få, lista, ställa in, ta bort | Åtkomstprincip för Nyckelvalv |

## <a name="management-plane-and-rbac"></a>Ledningsplan och RBAC

I hanteringsplanet använder du RBAC(rollbaserad åtkomstkontroll) för att auktorisera de åtgärder som en anropare kan utföra. I RBAC-modellen har varje Azure-prenumeration en instans av Azure AD. Du beviljar åtkomst till användare, grupper och program från den här katalogen. Åtkomst beviljas för att hantera resurser i Azure-prenumerationen som använder Distributionsmodellen för Azure Resource Manager. Om du vill bevilja åtkomst använder du [Azure-portalen,](https://portal.azure.com/) [Azure CLI,](/cli/azure/install-azure-cli?view=azure-cli-latest) [Azure PowerShell](/powershell/azureps-cmdlets-docs)eller [AZURE Resource Manager REST API:er](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Du skapar ett nyckelvalv i en resursgrupp och hanterar åtkomst med hjälp av Azure AD. Du ger användare eller grupper möjlighet att hantera nyckelvalven i en resursgrupp. Du beviljar åtkomsten på en viss scopenivå genom att tilldela lämpliga RBAC-roller. Om du vill bevilja åtkomst till en användare för att `key vault Contributor` hantera nyckelvalv tilldelar du en fördefinierad roll till användaren i ett visst omfång. Följande scopenivåer kan tilldelas en RBAC-roll:

- **Prenumeration**: En RBAC-roll som tilldelats på prenumerationsnivå gäller för alla resursgrupper och resurser inom den prenumerationen.
- **Resursgrupp**: En RBAC-roll som tilldelats på resursgruppsnivå gäller för alla resurser i resursgruppen.
- **Specifik resurs**: En RBAC-roll som tilldelats för en viss resurs gäller för den resursen. I det här fallet är resursen ett specifikt nyckelvalv.

Det finns flera fördefinierade roller. Om en fördefinierad roll inte passar dina behov kan du definiera din egen roll. Mer information finns [i RBAC: Inbyggda roller](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Om en `Contributor` användare har behörighet till ett nyckelvalvshanteringsplan kan användaren ge sig själva åtkomst till dataplanet genom att ange en åtkomstprincip för Key Vault. Du bör noga styra `Contributor` vem som har rollåtkomst till dina nyckelvalv. Se till att endast behöriga personer kan komma åt och hantera dina nyckelvalv, nycklar, hemligheter och certifikat.
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>Principer för dataplan och åtkomst

Du beviljar dataplanåtkomst genom att ange åtkomstprinciper för Key Vault för ett nyckelvalv. Om du vill ange dessa åtkomstprinciper måste `Contributor` en användare, grupp eller ett program ha behörighet för hanteringsplanet för nyckelvalvet.

Du ger en användare, grupp eller program åtkomst till att köra specifika åtgärder för nycklar eller hemligheter i ett nyckelvalv. Key Vault stöder upp till 1 024 åtkomstprincipposter för ett nyckelvalv. Om du vill bevilja åtkomst till dataplan till flera användare skapar du en Azure AD-säkerhetsgrupp och lägger till användare i den gruppen.

<a id="key-vault-access-policies"></a>Åtkomstprinciper för Key Vault ger behörigheter separat till nycklar, hemligheter och certifikat. Du kan bara ge en användare åtkomst till nycklar och inte till hemligheter. Åtkomstbehörigheter för nycklar, hemligheter och certifikat finns på arkivnivå. Åtkomstprinciper för Key Vault stöder inte detaljerade behörigheter på objektnivå som en viss nyckel, hemlighet eller ett certifikat. Om du vill ange åtkomstprinciper för ett nyckelvalv använder du [Azure-portalen,](https://portal.azure.com/) [Azure CLI,](/cli/azure/install-azure-cli?view=azure-cli-latest) [Azure PowerShell](/powershell/azureps-cmdlets-docs)eller [REST-API:erna för nyckelvalvshantering](https://msdn.microsoft.com/library/azure/mt620024.aspx).

> [!IMPORTANT]
> Åtkomstprinciper för nyckelvalv gäller på arkivnivå. När en användare beviljas behörighet att skapa och ta bort nycklar kan de utföra dessa åtgärder på alla nycklar i nyckelvalvet.
>

Du kan begränsa åtkomsten till dataplan med hjälp av [slutpunkter för virtuella nätverkstjänster för Azure Key Vault](overview-vnet-service-endpoints.md)). Du kan konfigurera [brandväggar och virtuella nätverksregler](network-security.md) för ytterligare ett säkerhetslager.

## <a name="example"></a>Exempel

I det här exemplet utvecklar vi ett program som använder ett certifikat för TLS/SSL, Azure Storage för att lagra data och en RSA 2 048-bitarsnyckel för signeringsåtgärder. Vårt program körs i en virtuell Azure-dator (VM) (eller en skalningsuppsättning för virtuella datorer). Vi kan använda ett nyckelvalv för att lagra programhemligheterna. Vi kan lagra bootstrap-certifikatet som används av programmet för att autentisera med Azure AD.

Vi behöver tillgång till följande lagrade nycklar och hemligheter:
- **TLS/SSL-certifikat**: Används för TLS/SSL.
- **Lagringsnyckel:** Används för att komma åt lagringskontot.
- **RSA 2,048-bitars nyckel:** Används för teckenoperationer.
- **Bootstrap-certifikat:** Används för att autentisera med Azure AD. När åtkomst har beviljats kan vi hämta lagringsnyckeln och använda RSA-nyckeln för signering.

Vi måste definiera följande roller för att ange vem som kan hantera, distribuera och granska vårt program:
- **Säkerhetsteam:** IT-personal från cso-kontoret (Chief Security Officer) eller liknande bidragsgivare. Säkerhetsteamet ansvarar för att bevara hemligheterna. Hemligheterna kan innehålla TLS/SSL-certifikat, RSA-nycklar för signering, anslutningssträngar och lagringskontonycklar.
- **Utvecklare och operatörer**: Den personal som utvecklar programmet och distribuerar det i Azure. Medlemmarna i det här teamet är inte en del av säkerhetspersonalen. De bör inte ha åtkomst till känsliga data som TLS/SSL-certifikat och RSA-nycklar. Endast det program som de distribuerar bör ha åtkomst till känsliga data.
- **Revisorer**: Den här rollen är för bidragsgivare som inte är medlemmar i utvecklingen eller allmän IT-personal. De granskar användning och underhåll av certifikat, nycklar och hemligheter för att säkerställa efterlevnad av säkerhetsstandarder.

Det finns en annan roll som ligger utanför vårt program: prenumerationsadministratören (eller resursgruppen). Prenumerationsadministratören ställer in inledande åtkomstbehörigheter för säkerhetsteamet. De ger åtkomst till säkerhetsgruppen med hjälp av en resursgrupp som har de resurser som krävs av programmet.

Vi måste godkänna följande operationer för våra roller:

**Säkerhetsteamet**
- Skapa nyckelvalv.
- Aktivera loggning av Key Vault.
- Lägg till nycklar och hemligheter.
- Skapa säkerhetskopior av nycklar för haveriberedskap.
- Ange åtkomstprinciper för Key Vault för att bevilja behörigheter till användare och program för specifika åtgärder.
- Rulla nycklarna och hemligheterna med jämna mellanrum.

**Utvecklare och operatörer**
- Hämta referenser från säkerhetsteamet för bootstrap- och TLS/SSL-certifikat (tumavtryck), lagringsnyckel (hemlig URI) och RSA-nyckel (nyckel-URI) för signering.
- Utveckla och distribuera programmet för att komma åt nycklar och hemligheter programmässigt.

**Granskare**
- Granska Key Vault-loggarna för att bekräfta korrekt användning av nycklar och hemligheter och efterlevnad av datasäkerhetsstandarder.

I följande tabell sammanfattas åtkomstbehörigheterna för våra roller och program.

| Roll | Behörigheter på hanteringsplanet | Behörigheter på dataplanet |
| --- | --- | --- |
| Säkerhetsteamet | Nyckelvalvsdeltagare | Nycklar: säkerhetskopiering, skapa, ta bort, hämta, importera, lista, återställa<br>Hemligheter: alla operationer |
| Utvecklare&nbsp;och operatörer | Behörighet för distribution av Key Vault<br><br> **Den**här behörigheten gör det möjligt för distribuerade virtuella datorer att hämta hemligheter från ett nyckelvalv. | Ingen |
| Granskare | Ingen | Nycklar: lista<br>Hemligheter: lista<br><br> **Den**här behörigheten gör det möjligt för revisorer att inspektera attribut (taggar, aktiveringsdatum, utgångsdatum) för nycklar och hemligheter som inte avges i loggarna. |
| Program | Ingen | Nycklar: signera<br>Hemligheter: hämta |

De tre grupprollerna behöver åtkomst till andra resurser tillsammans med behörigheter för Key Vault. För att distribuera virtuella datorer (eller webbappfunktionen i Azure `Contributor` App Service) behöver utvecklare och operatörer åtkomst till dessa resurstyper. Revisorer behöver läsåtkomst till lagringskontot där Key Vault-loggarna lagras.

Mer information om hur du distribuerar certifikat, åtkomstnycklar och hemligheter programmässigt finns i följande resurser:
- Lär dig hur du [distribuerar certifikat till virtuella datorer från ett kundhanterad nyckelvalv](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (blogginlägg).
- Hämta [klientexemplen på Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45343). Det här innehållet illustrerar hur du använder ett bootstrap-certifikat för att autentisera till Azure AD för att komma åt ett nyckelvalv.

Du kan bevilja de flesta åtkomstbehörigheter med hjälp av Azure-portalen. Om du vill bevilja detaljerade behörigheter kan du använda Azure PowerShell eller Azure CLI.

PowerShell-kodavsnitten i det här avsnittet är byggda med följande antaganden:
- Azure AD-administratören har skapat säkerhetsgrupper för att representera de tre rollerna: Contoso Security Team, Contoso App DevOps och Contoso App Auditors. Administratören har lagt till användare i sina respektive grupper.
- Alla resurser finns i resursgruppen **ContosoAppRG.**
- Key Vault-loggarna lagras i **contosologstorage-lagringskontot.**
- **ContosoKeyVault-nyckelvalvet** och **contosologstorage-lagringskontot** finns på samma Azure-plats.

Prenumerationsadministratören `key vault Contributor` tilldelar `User Access Administrator` säkerhetsteamet och roller. Dessa roller gör det möjligt för säkerhetsteamet att hantera åtkomst till andra resurser och nyckelvalv, som båda finns i resursgruppen **ContosoAppRG.**

```powershell
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Säkerhetsteamet skapar ett nyckelvalv och ställer in loggnings- och åtkomstbehörigheter.

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

Våra definierade anpassade roller kan endast tilldelas prenumerationen där **ContosoAppRG-resursgruppen** skapas. Om du vill använda en anpassad roll för andra projekt i andra prenumerationer lägger du till andra prenumerationer i omfånget för rollen.

För vår DevOps-personal begränsas den anpassade `deploy/action` rolltilldelningen för nyckelvalvets behörighet till resursgruppen. Endast virtuella datorer som skapas i resursgruppen **ContosoAppRG** får åtkomst till hemligheterna (TLS/SSL och bootstrap-certifikat). Virtuella datorer som skapats i andra resursgrupper av en DevOps-medlem kan inte komma åt dessa hemligheter, även om den virtuella datorn har de hemliga URI:erna.

Vårt exempel beskriver ett enkelt scenario. Verkliga scenarier kan vara mer komplexa. Du kan justera behörigheter till ditt nyckelvalv baserat på dina behov. Vi antog att säkerhetsteamet tillhandahåller viktiga och hemliga referenser (URI och tumavtryck), som används av DevOps-personalen i deras applikationer. Utvecklare och operatörer kräver ingen åtkomst till dataplan. Vi fokuserade på hur du säkrar ditt nyckelvalv. Ta liknande hänsyn när du skyddar [dina virtuella datorer,](https://azure.microsoft.com/services/virtual-machines/security/) [lagringskonton](../../storage/blobs/security-recommendations.md)och andra Azure-resurser.

> [!NOTE]
> Det här exemplet visar hur Åtkomst till Key Vault är låst i produktionen. Utvecklare bör ha en egen prenumeration eller resursgrupp med fullständig behörighet att hantera sina valv, virtuella datorer och lagringskontot där de utvecklar programmet.

Vi rekommenderar att du ställer in ytterligare säker åtkomst till nyckelvalvet genom [att konfigurera Key Vault-brandväggar och virtuella nätverk](network-security.md).

## <a name="resources"></a>Resurser

* [Azure AD RBAC](../../role-based-access-control/role-assignments-portal.md)

* [RBAC: Inbyggda roller](../../role-based-access-control/built-in-roles.md)

* [Förstå Resurshanterarens distribution och klassisk distribution](../../azure-resource-manager/management/deployment-models.md)

* [Hantera RBAC med Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)

* [Hantera RBAC med REST API](../../role-based-access-control/role-assignments-rest.md)

* [RBAC för Microsoft Azure](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    I den här microsoft ignite-konferensvideon från 2015 beskrivs funktioner för åtkomsthantering och rapportering i Azure. Den utforskar också metodtips för att skydda åtkomsten till Azure-prenumerationer med hjälp av Azure AD.

* [Auktorisera åtkomst till webbprogram med OAuth 2.0 och Azure AD](../../active-directory/develop/v2-oauth2-auth-code-flow.md)

* [REST-API:er för nyckelvalvshantering](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    Referensen för REST-API:erna för att hantera ditt nyckelvalv programmässigt, inklusive att ange åtkomstprincipen för Key Vault.

* [REST-API:er för nyckelvalv](https://msdn.microsoft.com/library/azure/dn903609.aspx)

* [Nyckelåtkomstkontroll](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)

* [Åtkomstkontroll till hemlighet](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)

* [Ange](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) och [ta bort](/powershell/module/az.keyvault/Remove-azKeyVaultAccessPolicy) åtkomstprincipen för Key Vault med hjälp av PowerShell.

## <a name="next-steps"></a>Nästa steg

Konfigurera [Key Vault-brandväggar och virtuella nätverk](network-security.md).

En självstudiekurs för en administratör finns i [Vad är Azure Key Vault?](overview.md)).

Mer information om användningsloggning för Key Vault finns i [Loggning av Azure Key Vault](logging.md)).

Mer information om hur du använder nycklar och hemligheter med Azure Key Vault finns i [Om nycklar och hemligheter](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Om du har frågor om Key Vault, besök [forumen](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
