---
title: Säker åtkomst till ett nyckel valv – Azure Key Vault | Microsoft Docs
description: Hantera åtkomst behörigheter för Azure Key Vault, nycklar och hemligheter. Beskriver autentiserings-och auktoriserings modellen för Key Vault och hur du skyddar nyckel valvet.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: sudbalas
ms.openlocfilehash: 26dba14baa95a91c12e9ccd277731b91207b4a4c
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533265"
---
# <a name="secure-access-to-a-key-vault"></a>Säker åtkomst till ett nyckel valv

Azure Key Vault är en moln tjänst som skyddar krypterings nycklar och hemligheter som certifikat, anslutnings strängar och lösen ord. Eftersom dessa data är känsliga och affärs kritiska måste du skydda åtkomsten till dina nyckel valv genom att bara tillåta behöriga program och användare. Den här artikeln ger en översikt över Key Vault åtkomst modell. Den förklarar autentisering och auktorisering och beskriver hur du skyddar åtkomsten till dina nyckel valv.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="access-model-overview"></a>Översikt över åtkomst modell

Åtkomst till ett nyckel valv styrs via två gränssnitt: **hanterings planet** och **data planet**. Hanterings planet är den plats där du hanterar Key Vault sig själv. Åtgärder i det här planet innefattar att skapa och ta bort nyckel valv, Hämta Key Vault egenskaper och uppdatera åtkomst principer. Data planet är den plats där du arbetar med de data som lagras i ett nyckel valv. Du kan lägga till, ta bort och ändra nycklar, hemligheter och certifikat.

För att få åtkomst till ett nyckel valv i något av planerna måste alla anropare (användare eller program) ha korrekt autentisering och auktorisering. Autentisering upprättar identiteten för anroparen. Auktorisering avgör vilka åtgärder som anroparen kan köra.

Båda planerna använder Azure Active Directory (Azure AD) för autentisering. För auktorisering använder hanterings planet rollbaserad åtkomst kontroll (RBAC) och data planet använder en Key Vault åtkomst princip.

## <a name="active-directory-authentication"></a>Active Directory autentisering

När du skapar ett nyckel valv i en Azure-prenumeration associeras det automatiskt med Azure AD-klienten för prenumerationen. Alla anropare i båda planerna måste registreras i den här klienten och autentiseras för åtkomst till nyckel valvet. I båda fallen kan program komma åt Key Vault på två sätt:

- **Användare plus åtkomst till program**: programmet får åtkomst till Key Vault åt en inloggad användare. Exempel på den här typen av åtkomst är Azure PowerShell och Azure Portal. Användar åtkomst beviljas på två sätt. Användare kan komma åt Key Vault från ett program, eller de måste använda ett enskilt program (kallas _sammansatt identitet_).
- **Endast program åtkomst**: programmet körs som en daemon-tjänst eller ett bakgrunds jobb. Program identiteten beviljas åtkomst till nyckel valvet.

För båda typerna av åtkomst autentiseras programmet med Azure AD. Programmet använder en [autentiseringsmetod som stöds](../../active-directory/develop/authentication-scenarios.md) baserat på program typen. Programmet hämtar en token för en resurs i planet för att ge åtkomst. Resursen är en slut punkt i hanterings-eller data planet, baserat på Azure-miljön. Programmet använder token och skickar en REST API begäran till Key Vault. Läs mer i [hela autentiserings flödet](../../active-directory/develop/v2-oauth2-auth-code-flow.md).

Modellen för en enda mekanism för autentisering till båda planerna har flera fördelar:

- Organisationer kan styra åtkomsten centralt till alla nyckel valv i organisationen.
- Om en användare lämnar, förlorar de direkt åtkomst till alla nyckel valv i organisationen.
- Organisationer kan anpassa autentiseringen med hjälp av alternativen i Azure AD, till exempel för att aktivera Multi-Factor Authentication för extra säkerhet.

## <a name="resource-endpoints"></a>Resurs slut punkter

Program får åtkomst till planen via slut punkter. Åtkomst kontrollerna för de två planerna fungerar oberoende av varandra. För att ge en program åtkomst till att använda nycklar i ett nyckel valv beviljar du data Plans åtkomst med hjälp av en Key Vault åtkomst princip. För att ge en användare Läs behörighet till Key Vault egenskaper och taggar, men inte åtkomst till data (nycklar, hemligheter eller certifikat) ger du åtkomst till hanterings planet med RBAC.

I följande tabell visas slut punkterna för hanterings-och data planen.

| Åtkomst &nbsp; plan | Slutpunkter för åtkomst | Operations | Mekanism för åtkomst &nbsp; kontroll |
| --- | --- | --- | --- |
| Hanteringsplanet | **EAN**<br> management.azure.com:443<br><br> **Azure Kina 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure amerikanska myndigheter:**<br> management.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> management.microsoftazure.de:443 | Skapa, läsa, uppdatera och ta bort nyckel valv<br><br>Ange Key Vault åtkomst principer<br><br>Ange Key Vault Taggar | Azure RBAC |
| Dataplanet | **EAN**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure Kina 21Vianet:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure amerikanska myndigheter:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure Germany:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 | Nycklar: dekryptera, kryptera,<br> Packa upp, radbryta, verifiera, signera,<br> Hämta, Visa, uppdatera, skapa,<br> Importera, ta bort, säkerhetskopiera, återställa<br><br> Hemligheter: Hämta, lista, ange, ta bort | Key Vault åtkomst princip |

## <a name="management-plane-and-rbac"></a>Hanterings plan och RBAC

I hanterings planet använder du rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att auktorisera de åtgärder som en anropare kan utföra. I RBAC-modellen har varje Azure-prenumeration en instans av Azure AD. Du beviljar åtkomst till användare, grupper och program från den här katalogen. Åtkomst beviljas för att hantera resurser i Azure-prenumerationen som använder Azure Resource Manager distributions modell. Om du vill bevilja åtkomst använder du [Azure Portal](https://portal.azure.com/), [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest), [Azure PowerShell](/powershell/azure/)eller [Azure Resource Manager REST-API: er](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Du skapar ett nyckel valv i en resurs grupp och hanterar åtkomst med hjälp av Azure AD. Du beviljar användare eller grupper möjligheten att hantera nyckel valv i en resurs grupp. Du beviljar åtkomst på en bestämd omfattnings nivå genom att tilldela lämpliga Azure-roller. Om du vill bevilja åtkomst till en användare för att hantera nyckel valv tilldelar du en fördefinierad `key vault Contributor` roll till användaren vid en bestämd omfattning. Följande omfattnings nivåer kan tilldelas en Azure-roll:

- **Prenumeration**: en Azure-roll som tilldelas på prenumerations nivån gäller för alla resurs grupper och resurser i prenumerationen.
- **Resurs grupp**: en Azure-roll som tilldelas på resurs grupps nivå gäller för alla resurser i den resurs gruppen.
- **Resurs**: en Azure-roll som är tilldelad en angiven resurs gäller resursen. I det här fallet är resursen ett särskilt nyckel valv.

Det finns flera fördefinierade roller. Om en fördefinierad roll inte passar dina behov kan du definiera en egen roll. Mer information finns i [RBAC: inbyggda roller](../../role-based-access-control/built-in-roles.md).

> [!IMPORTANT]
> Om en användare har `Contributor` behörighet till ett nyckel valv hanterings plan kan användaren ge sig själva åtkomst till data planet genom att ange en Key Vault åtkomst princip. Du bör noggrant kontrol lera vem som har `Contributor` roll åtkomst till dina nyckel valv. Se till att endast behöriga personer kan komma åt och hantera nyckel valv, nycklar, hemligheter och certifikat.
>

<a id="data-plane-access-control"></a>
## <a name="data-plane-and-access-policies"></a>Data plan och åtkomst principer

Du ger åtkomst till data planet genom att ange Key Vault åtkomst principer för ett nyckel valv. Om du vill ange dessa åtkomst principer måste en användare, grupp eller ett program ha `Contributor` behörighet till hanterings planet för nyckel valvet.

Du beviljar en användare, grupp eller program åtkomst för att köra specifika åtgärder för nycklar eller hemligheter i ett nyckel valv. Key Vault stöder upp till 1 024 åtkomst princip poster för ett nyckel valv. Om du vill ge data plan åtkomst till flera användare skapar du en Azure AD-säkerhetsgrupp och lägger till användare i gruppen.

Du kan se en fullständig lista över valv och hemliga åtgärder och förstå de åtgärder som tillåts när du konfigurerar åtkomst principer för nyckel valv genom att visa följande referens. [Referens för Key Vaults åtgärd](https://docs.microsoft.com/rest/api/keyvault/#vault-operations)

<a id="key-vault-access-policies"></a>Key Vault åtkomst principer beviljar behörigheter separat till nycklar, hemligheter och certifikat. Du kan endast ge en användare åtkomst till nycklar och inte till hemligheter. Åtkomst behörigheter för nycklar, hemligheter och certifikat finns på valv nivån. Key Vault åtkomst principer stöder inte detaljerade behörigheter på objekt nivå som en speciell nyckel, hemlighet eller certifikat. Om du vill ange åtkomst principer för ett nyckel valv använder du [Azure Portal](https://portal.azure.com/), [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest), [Azure PowerShell](/powershell/azure/)eller [Key Vault hantering REST-API: er](https://msdn.microsoft.com/library/azure/mt620024.aspx).

> [!IMPORTANT]
> Key Vault åtkomst principer tillämpas på valv nivån. När en användare beviljas behörighet att skapa och ta bort nycklar kan de utföra dessa åtgärder på alla nycklar i nyckel valvet.
>

Du kan begränsa åtkomsten till data planet genom att använda [tjänst slut punkter för virtuella nätverk för Azure Key Vault](overview-vnet-service-endpoints.md)). Du kan konfigurera [brand väggar och virtuella nätverks regler](network-security.md) för ett extra säkerhets lager.

## <a name="example"></a>Exempel

I det här exemplet ska vi utveckla ett program som använder ett certifikat för TLS/SSL, Azure Storage att lagra data och en RSA 2 048-bitars nyckel för signerings åtgärder. Vårt program körs på en virtuell Azure-dator (eller en virtuell dators skalnings uppsättning). Vi kan använda ett nyckel valv för att lagra program hemligheterna. Vi kan lagra start certifikatet som används av programmet för att autentisera med Azure AD.

Vi behöver åtkomst till följande lagrade nycklar och hemligheter:
- **TLS/SSL-certifikat**: används för TLS/SSL.
- **Lagrings nyckel**: används för att komma åt lagrings kontot.
- **RSA 2 048-bitars nyckel**: används för signerings åtgärder.
- **Bootstrap-certifikat**: används för att autentisera med Azure AD. När åtkomst har beviljats kan vi hämta lagrings nyckeln och använda RSA-nyckeln för signering.

Vi måste definiera följande roller för att ange vem som kan hantera, distribuera och granska vårt program:
- **Säkerhets team**: IT-personal från byråns skydds chef (säkerhets tjänsteman) eller liknande bidrags givare. Säkerhets teamet ansvarar för rätt säkerhet. Hemligheterna kan omfatta TLS/SSL-certifikat, RSA-nycklar för signering, anslutnings strängar och lagrings konto nycklar.
- **Utvecklare och operatörer**: personal som utvecklar programmet och distribuerar det i Azure. Medlemmarna i det här teamet är inte en del av säkerhets personalen. De bör inte ha till gång till känsliga data som TLS/SSL-certifikat och RSA-nycklar. Endast det program som de distribuerar bör ha åtkomst till känsliga data.
- **Granskare**: den här rollen är för deltagare som inte är medlemmar i utvecklings-eller allmän IT-personal. De granskar användningen och underhållet av certifikat, nycklar och hemligheter för att säkerställa efterlevnaden av säkerhets standarder.

Det finns en annan roll som ligger utanför omfånget för programmet: prenumerationen (eller resurs gruppens) administratör. Prenumerations administratören konfigurerar inledande åtkomst behörighet för säkerhets teamet. De ger åtkomst till säkerhets teamet genom att använda en resurs grupp som har de resurser som krävs av programmet.

Vi måste auktorisera följande åtgärder för våra roller:

**Säkerhetsteamet**
- Skapa nyckel valv.
- Aktivera Key Vault loggning.
- Lägg till nycklar och hemligheter.
- Skapa säkerhets kopior av nycklar för haveri beredskap.
- Ange Key Vault åtkomst principer för att ge behörigheter till användare och program för vissa åtgärder.
- Lyft nycklar och hemligheter med jämna mellanrum.

**Utvecklare och operatörer**
- Hämta referenser från säkerhets teamet för bootstrap-och TLS/SSL-certifikat (tumavtrycken), lagrings nyckel (hemlig URI) och RSA-nyckel (nyckel-URI) för signering.
- Utveckla och distribuera programmet för att få åtkomst till nycklar och hemligheter program mässigt.

**Granskare**
- Granska Key Vault loggar för att bekräfta korrekt användning av nycklar och hemligheter och efterlevnad av data säkerhets standarder.

I följande tabell sammanfattas åtkomst behörigheterna för våra roller och program.

| Roll | Behörigheter på hanteringsplanet | Behörigheter på dataplanet |
| --- | --- | --- |
| Säkerhetsteamet | Key Vault deltagare | Nycklar: säkerhetskopiering, skapa, ta bort, hämta, importera, lista, återställa<br>Hemligheter: alla åtgärder |
| Utvecklare och &nbsp; operatörer | Key Vault distributions behörighet<br><br> **Obs!** den här behörigheten gör att distribuerade virtuella datorer kan hämta hemligheter från ett nyckel valv. | Ingen |
| Granskare | Ingen | Nycklar: lista<br>Hemligheter: lista<br><br> **Obs!** den här behörigheten gör det möjligt för granskare att inspektera attribut (Taggar, aktiverings datum, förfallo datum) för nycklar och hemligheter som inte genereras i loggarna. |
| Program | Ingen | Nycklar: signera<br>Hemligheter: hämta |

De tre team rollerna behöver åtkomst till andra resurser tillsammans med Key Vault behörigheter. Utvecklare och operatörer behöver ha `Contributor` åtkomst till dessa resurs typer för att distribuera virtuella datorer (eller Web Apps-funktionen i Azure App Service). Granskare behöver Läs behörighet till lagrings kontot där Key Vaults loggarna lagras.

Mer information om hur du distribuerar certifikat, åtkomst nycklar och hemligheter program mässigt finns i följande resurser:
- Lär dig hur du [distribuerar certifikat till virtuella datorer från ett kundhanterat nyckel valv](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (blogg inlägg).
- Visa [Azure Key Vault klient exempel](https://docs.microsoft.com/samples/browse/?term=Key%20Vault). Det här innehållet illustrerar hur du använder ett start certifikat för att autentisera till Azure AD för att få åtkomst till ett nyckel valv.

Du kan bevilja de flesta åtkomst behörigheterna genom att använda Azure Portal. Om du vill bevilja detaljerade behörigheter kan du använda Azure PowerShell eller Azure CLI.

PowerShell-kodfragmenten i det här avsnittet är skapade med följande antaganden:
- Azure AD-administratören har skapat säkerhets grupper som representerar de tre rollerna: contoso Security Team, contoso app DevOps och contoso app Auditers. Administratören har lagt till användare i deras respektive grupper.
- Alla resurser finns i resurs gruppen **ContosoAppRG** .
- Key Vaults loggarna lagras i lagrings kontot för **contosologstorage** .
- **ContosoKeyVault** Key Vault och **contosologstorage** lagrings konto finns på samma Azure-plats.

Prenumerations administratören tilldelar-och- `key vault Contributor` `User Access Administrator` rollerna till säkerhets teamet. Med dessa roller kan säkerhets teamet hantera åtkomst till andra resurser och nyckel valv, som båda finns i resurs gruppen **ContosoAppRG** .

```powershell
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzRoleAssignment -ObjectId (Get-AzADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Säkerhets teamet skapar ett nyckel valv och ställer in loggnings-och åtkomst behörigheter.

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

Våra definierade anpassade roller kan endast tilldelas den prenumeration där resurs gruppen **ContosoAppRG** skapas. Om du vill använda en anpassad roll för andra projekt i andra prenumerationer lägger du till andra prenumerationer i omfånget för rollen.

För vår DevOps-personal är den anpassade roll tilldelningen för Key Vault- `deploy/action` behörigheten begränsad till resurs gruppen. Endast virtuella datorer som har skapats i resurs gruppen **ContosoAppRG** får åtkomst till hemligheter (TLS/SSL och bootstrap-certifikat). Virtuella datorer som skapats i andra resurs grupper av en DevOps-medlem har inte åtkomst till dessa hemligheter, även om den virtuella datorn har de hemliga URI: erna.

Vårt exempel beskriver ett enkelt scenario. Scenarier med real tid kan vara mer komplexa. Du kan justera behörigheterna till ditt nyckel valv baserat på dina behov. Vi förmodade att säkerhets teamet tillhandahåller nyckel-och hemlighet-referenser (URI: er och tumavtrycken), som används av DevOps-Personalen i sina program. Utvecklare och operatörer kräver ingen åtkomst till data planet. Vi fokuserar på hur du skyddar ditt nyckel valv. Ge liknande överväganden när du skyddar [dina virtuella datorer](https://azure.microsoft.com/services/virtual-machines/security/), [lagrings konton](../../storage/blobs/security-recommendations.md)och andra Azure-resurser.

> [!NOTE]
> I det här exemplet visas hur Key Vault åtkomst är låst i produktion. Utvecklare bör ha sin egen prenumeration eller resurs grupp med fullständig behörighet för att hantera sina valv, virtuella datorer och lagrings kontot där de utvecklar programmet.

Vi rekommenderar att du konfigurerar ytterligare säker åtkomst till ditt nyckel valv genom att [konfigurera Key Vault brand väggar och virtuella nätverk](network-security.md).

## <a name="resources"></a>Resurser

* [Azure AD RBAC](../../role-based-access-control/role-assignments-portal.md)

* [RBAC: inbyggda roller](../../role-based-access-control/built-in-roles.md)

* [Förstå Resource Manager-distribution och klassisk distribution](../../azure-resource-manager/management/deployment-models.md)

* [Hantera RBAC med Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)

* [Hantera RBAC med REST API](../../role-based-access-control/role-assignments-rest.md)

* [RBAC för Microsoft Azure](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Den här 2015 Microsoft-inbrännings konferens videon diskuterar åtkomst hantering och rapporterings funktioner i Azure. Den undersöker också metod tips för att skydda åtkomsten till Azure-prenumerationer med hjälp av Azure AD.

* [Auktorisera åtkomst till webb program med hjälp av OAuth 2,0 och Azure AD](../../active-directory/develop/v2-oauth2-auth-code-flow.md)

* [REST-API: er för Key Vault hantering](https://msdn.microsoft.com/library/azure/mt620024.aspx)

    Referensen för REST-API: er för att hantera ditt nyckel valv program mässigt, inklusive att ange Key Vault åtkomst princip.

* [Key Vault REST-API: er](https://msdn.microsoft.com/library/azure/dn903609.aspx)

* [Nyckelåtkomstkontroll](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)

* [Åtkomstkontroll till hemlighet](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)

* [Ange](/powershell/module/az.keyvault/Set-azKeyVaultAccessPolicy) och [ta bort](/powershell/module/az.keyvault/Remove-azKeyVaultAccessPolicy) Key Vault åtkomst princip med hjälp av PowerShell.

## <a name="next-steps"></a>Nästa steg

Konfigurera [Key Vault brand väggar och virtuella nätverk](network-security.md).

En vägledning för att komma igång med en administratör finns i [Vad är Azure Key Vault?](overview.md)).

Mer information om användnings loggning för Key Vault finns i [Azure Key Vault loggning](logging.md)).

Mer information om hur du använder nycklar och hemligheter med Azure Key Vault finns i [om nycklar och hemligheter](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Om du har frågor om Key Vault går du till [sidan Microsoft Q&en fråga](https://docs.microsoft.com/answers/topics/azure-key-vault.html).
