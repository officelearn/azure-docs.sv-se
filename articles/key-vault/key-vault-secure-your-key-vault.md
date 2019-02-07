---
title: Skydda din Azure Key Vault - Azure Key Vault | Microsoft Docs
description: Hantera åtkomstbehörigheter för Nyckelvalv, nycklar och hemligheter. Beskriver modellen för autentisering och auktorisering för Key Vault och hur du säkrar ditt nyckelvalv.
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 8a0300eeda49d85ffc08db8f285550e217613dcf
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55821625"
---
# <a name="secure-your-key-vault"></a>Säkra ditt nyckelvalv

Azure Key Vault är en molnbaserad tjänst som skyddar krypteringsnycklar och hemligheter (till exempel certifikat, anslutningssträngar och lösenord). Eftersom dessa data är känsliga och verksamhetskritiska, måste du skydda åtkomsten till dina nyckelvalv, auktoriserade så att endast program och användare. Den här artikeln innehåller en översikt över åtkomstmodellen för Nyckelvalvet. Den förklarar autentisering och auktorisering och beskriver hur du skyddar åtkomst.

## <a name="overview"></a>Översikt

Åtkomst till ett nyckelvalv styrs via två separata gränssnitt: hanteringplanet och dataplanet. 
**Hanteringsplanet** behandlar hantera valvet, till exempel – skapa ett valv, uppdatera ett valv, ta bort ett valv. 
**Dataplanet** behandlar hemligheter i ett valv som är att skapa, uppdatera, ta bort och läsa en hemlighet i valvet. För båda planen krävs korrekt autentisering och auktorisering innan en anropare (en användare eller ett program) kan få åtkomst till nyckelvalvet. Autentisering etablerar identiteten hos anroparen, medan auktoriseringen avgör vilka åtgärder som anroparen får utföra.

Både hanteringsplanet och dataplanet använder sig av Azure Active Directory för autentisering. För auktorisering använder sig hanteringsplanet av rollbaserad åtkomstkontroll (RBAC) medan dataplanet använder sig av nyckelvalvets åtkomstprincip.

Här är en kort översikt över de ämnen som täcks:

Autentisering med hjälp av Azure Active Directory – det här avsnittet beskrivs hur en anropare autentiserar sig med Azure Active Directory för att komma åt ett nyckelvalv via Hanteringsplanet och dataplanet. 

För autentisering använder båda planen Azure Active Directory (AD Azure). För auktorisering använder Hanteringsplanet rollbaserad åtkomstkontroll (RBAC) medan dataplanet använder åtkomstprincip för Nyckelvalvet.

## <a name="authenticate-by-using-azure-active-directory"></a>Autentisera med hjälp av Azure Active Directory

När du skapar ett nyckelvalv i en Azure-prenumeration associeras den automatiskt med prenumerationens Azure AD-klient. Alla anropare måste registreras i den här klienten och måste autentiseras för att komma åt nyckelvalvet. Det här kravet gäller för både Hanteringsplanet och dataplanåtkomst. Ett program i båda fallen kan de komma åt Nyckelvalvet på två sätt:

* **användare + appåtkomst**: Använda med program som får åtkomst till Nyckelvalvet åt en inloggad användare. Azure PowerShell och Azure portal är exempel på den här typen av åtkomst. Det finns två sätt att bevilja åtkomst till användare:

  - Åtkomst till Key Vault från alla program.
  - Åtkomst till Key Vault endast när de använder ett visst program (kallas sammansatt identitet).

* **endast appen**: Använda med program som körs som daemon-tjänster eller bakgrundsjobb. Programmets identitet beviljas åtkomst till nyckelvalvet.

I båda typerna av program, autentiserar sig programmet Azure AD med hjälp av någon av de [autentiseringsmetoder som stöds](../active-directory/develop/authentication-scenarios.md), och hämtar en token. Autentiseringsmetoden som används beror på vilken typ av program. Sedan använder sin token för programmet och skickar en REST API-begäran till Key Vault. Hantering av plan begäranden dirigeras via en Azure Resource Manager-slutpunkt. Vid åtkomst till dataplanet, pratar programmet direkt med en Key Vault-slutpunkt. Mer information finns i den [hela autentiseringsflödet](../active-directory/develop/v1-protocols-oauth-code.md). 

Resursnamnet som programmet begär en token som beror på vilken plan programmet ansluter till. Resursnamnet är antingen en hanteringsslutpunkt plan eller en slutpunkt för data plan, beroende på Azure-miljön. Mer information finns i tabellen nedan.

Med en enda mekanism för autentisering på båda planen har vissa fördelar:

* Organisationer kan centralt styra åtkomsten till alla nyckelvalv i organisationen.
* Om en användare lämnar förlorar han eller hon direkt åtkomst till alla nyckelvalv i organisationen.
* Organisationer kan anpassa autentisering via alternativen i Azure AD (till exempel aktivera multifaktorautentisering för extra säkerhet).

## <a name="the-management-plane-and-the-data-plane"></a>Hanteringsplanet och dataplanet

Använda Hanteringsplanet för att hantera Nyckelvalv själva. Detta omfattar åtgärder, till exempel hantera attribut och ställa in åtkomstprinciper för data-plan. Använd dataplanet för att lägga till, ta bort, ändra och använda nycklar, hemligheter och certifikat som lagras i Key Vault.

Komma åt de Hanteringsplanet och dataplanet hanteringsgränssnitt via olika slutpunkter som visas i följande tabell. Den andra kolumnen i tabellen beskriver DNS-namn för de här slutpunkterna i olika Azure-miljöer. Den tredje kolumnen beskriver de åtgärder du kan utföra från varje åtkomstplan. Varje åtkomstplan har sin egen åtkomstkontroll. Åtkomstkontroll för Hanteringsplanet anges med hjälp av Azure Resource Manager rollbaserad åtkomstkontroll (RBAC). Åtkomstkontroll för dataplanet anges med hjälp av åtkomstprincip för Nyckelvalvet.

| Åtkomstplan | Slutpunkter för åtkomst | Åtgärder | Åtkomstkontrollmekanismer |
| --- | --- | --- | --- |
| Hanteringsplanet |**Globalt:**<br> management.azure.com:443<br><br> **Azure Kina 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure för amerikanska myndigheter:**<br> management.usgovcloudapi.net:443<br><br> **Azure i Tyskland:**<br> management.microsoftazure.de:443 |Create/Read/Update/Delete Key Vault <br> Ställa in åtkomstprinciper för Key Vault<br>Ställ in taggar för Key Vault |Azure Resource Manager RBAC |
| Dataplanet |**Globalt:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure Kina 21Vianet:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure för amerikanska myndigheter:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure i Tyskland:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 |För nycklar: Dekryptera, kryptera, UnwrapKey, WrapKey, verifiera, logga, hämta, lista, uppdatera, skapa, importera, ta bort, säkerhetskopiera, återställa<br><br> För hemligheter: Hämta, lista, Ställ in, ta bort |Åtkomstprincip för Nyckelvalvet |

Hantering av hanteringsplanets och dataplanets åtkomstkontroller fungerar oberoende av varandra. Till exempel om du vill ge ett programåtkomst för att använda nycklar i key vault behöver du bara att bevilja åtkomst till dataplanet. Du beviljar åtkomst via åtkomstprinciper för Key Vault. En användare som behöver läsa egenskaper för Key Vault och taggar, men inte åtkomst till data (nycklar, hemligheter eller certifikat) behöver däremot bara hanteringsplansåtkomst. Du beviljar åtkomst genom att tilldela läsåtkomst till användaren med RBAC.

## <a name="management-plane-access-control"></a>Åtkomstkontroll för hanteringsplanet

Hanteringsplanet består av åtgärder som påverkar själva, nyckelvalvet som:

- Skapa eller ta bort ett nyckelvalv.
- Hämta en lista över valv i en prenumeration.
- Hämtning av Key Vault-egenskaper (till exempel SKU och taggar).
- Ställa in åtkomstprinciper för Nyckelvalvet som kontrollen användar- och åtkomst till nycklar och hemligheter.

Åtkomstkontroll för hanteringsplanet använder sig av RBAC.  

### <a name="role-based-access-control-rbac"></a>Rollbaserad åtkomstkontroll (RBAC)

Varje Azure-prenumeration har en Azure AD-instans. Du beviljar åtkomst till användare, grupper och program från den här katalogen för att hantera resurser i Azure-prenumeration som använder Azure Resource Manager-distributionsmodellen. Den här typen av åtkomstkontroll kallas RBAC. För att hantera den här åtkomsten, kan du använda [Azure Portal](https://portal.azure.com/), [Azure CLI-verktygen](../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs) eller [Azure Resource Manager REST API:er](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Du skapar ett nyckelvalv i en resursgrupp och styra åtkomst till Hanteringsplanet med hjälp av Azure AD. Du kan till exempel ge användare eller en grupp möjlighet att hantera nyckelvalv i en resursgrupp.

Du kan ge åtkomst till användare, grupper och program i en specifik omfattning genom att tilldela lämpliga RBAC-roller. Till exempel för att bevilja åtkomst till en användare att hantera nyckelvalv tilldela du en fördefinierad deltagarrollen för Key Vault till den här användaren i ett visst omfång. Omfånget är i det här fallet en prenumeration, en resursgrupp eller ett visst nyckelvalv. En roll som tilldelats på prenumerationsnivå gäller för alla resursgrupper och resurser inom den prenumerationen. En roll som tilldelats på resursgruppsnivå gäller för alla resurser i resursgruppen. En roll som tilldelats för en specifik resurs gäller för den här resursen. Det finns flera fördefinierade roller (se [RBAC: Inbyggda roller](../role-based-access-control/built-in-roles.md)). Om den fördefinierade rollen inte passar dina behov, kan du definiera en egen roll.

> [!IMPORTANT]
> Observera att om en användare har deltagare som har behörighet att Hanteringsplanet för ett Nyckelvalv, kan HEN ge sig själv åtkomst till dataplanet genom att ställa in åtkomstprincip för Nyckelvalvet. Därför bör du noggrant kontrollera vem som har deltagaråtkomst till dina nyckelvalv. Se till att endast auktoriserade personer kan komma åt och hantera dina nyckelvalv, nycklar, hemligheter och certifikat.
>

## <a name="data-plane-access-control"></a>Åtkomstkontroll för dataplanet

Åtgärder för Key Vault plan gäller lagrade objekt, till exempel nycklar, hemligheter och certifikat. Viktiga åtgärder är bland annat skapa, importera, uppdatera, lista, säkerhetskopiera och återställa nycklar. Kryptografiska åtgärder omfattar inloggning, verifiera, kryptera, dekryptera, omsluta, packa upp, ställa in etiketter och ange andra attribut för nycklar. På samma sätt åtgärder för hemligheter inkluderar get, set, lista, ta bort.

Du kan bevilja åtkomst till dataplanet genom att ställa in åtkomstprinciper för ett nyckelvalv. En användare, grupp eller program måste ha bidragsgivarbehörighet för Hanteringsplanet för ett nyckelvalv för att kunna ställa in åtkomstprinciper för nyckelvalvet. Du kan ge en användare, grupp eller åtkomst till ett program för att utföra specifika åtgärder för nycklar eller hemligheter i key vault. Nyckelvalv stöder upp till 1024 åtkomstprinciper för ett nyckelvalv. Skapa en Azure AD-säkerhetsgrupp för att tilldela dataplansåtkomst till flera användare och lägga till användare i gruppen.

### <a name="key-vault-access-policies"></a>Åtkomstprinciper för Key Vault

Åtkomstprinciper för Nyckelvalvet ger separata behörigheter till nycklar, hemligheter och certifikat. Du kan till exempel ge en användaråtkomst till bara nycklar, och ingen behörighet för hemligheter. Behörighet att komma åt nycklar, hemligheter eller certifikat är på valvnivå. Åtkomstprincip för Key Vault stöder inte detaljerade, på objektnivå behörigheter, till exempel en viss nyckel eller hemlighet certifikat. Du kan använda den [Azure-portalen](https://portal.azure.com/), [Azure CLI-verktygen](../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs), eller [Key Vault Management REST API: er](https://msdn.microsoft.com/library/azure/mt620024.aspx) att ställa in åtkomstprinciper för ett nyckelvalv.

> [!IMPORTANT]
> Åtkomstprinciper för Nyckelvalv gäller på valvnivå. En användare som har beviljats behörighet att skapa och ta bort nycklar, kan utföra de åtgärderna på alla nycklar i nyckelvalvet.

Förutom att använda åtkomstprinciper kan du kan också begränsa åtkomst till dataplanet genom att använda [tjänstslutpunkter i virtuella nätverk för Azure Key Vault](key-vault-overview-vnet-service-endpoints.md). Du konfigurerar [brandväggar och virtuella Nätverksregler](key-vault-network-security.md) för ett extra säkerhetslager.

## <a name="example"></a>Exempel

Vi antar att du utvecklar ett program som använder ett certifikat för SSL, Azure Storage för att lagra data och en RSA 2048-bitars nyckel för signeringsåtgärder. Vi antar att det här programmet körs i Azure-datorer (eller en VM-skalningsuppsättning). Du kan använda ett nyckelvalv för att lagra alla programmets hemligheter och lagra bootstrap-certifikatet används av programmet för att autentisera med Azure AD.

Här är en sammanfattning av typerna av nycklar och hemligheter som lagras:

* **SSL-Cert**: Används för SSL.
* **Lagringsnyckeln**: Används för att få åtkomst till lagringskontot.
* **RSA 2048-bitars nyckel**: Används för signeringsåtgärder.
* **Bootstrap-certifikatet**: För att autentisera med Azure AD. När åtkomst beviljas kan du hämta lagringsnyckeln och använda RSA-nyckel för signering.

Nu ska vi möta de personer som hanterar, distribuerar och granskar det här programmet. Vi använder oss av tre roller i det här exemplet.

* **Säkerhetsteamet**: Vanligtvis IT-personal från kontoret för CSO (Chief Security Officer), eller motsvarande. Den här team ansvarar för för att hemligheter. Exempel: SSL-certifikat, RSA-nycklar för signering, anslutningssträngar, och storage-kontonycklar.
* **Utvecklare/operatörer**: Personalen som utvecklar programmet och sedan distribuera det i Azure. Normalt kan de inte är en del av säkerhetsteamet och kan därför de får inte ha åtkomst till känsliga data, till exempel SSL-certifikat och RSA-nycklar. Programmet de distribuerar ska ha åtkomst till dessa objekt.
* **Granskare**: Vanligtvis en annan uppsättning personer, isolerade från utvecklare och allmän IT-personal. Deras ansvarsområde är att granska användning och underhåll av certifikat, nycklar och hemligheter för att säkerställa att säkerhetsstandarder. 

Det finns en till roll som inte omfattas av det här programmet, men relevanta här anges. Rollen är en prenumeration (eller resursgrupp) administratör. Prenumerationsadministratören ställer in inledande åtkomstbehörigheter för säkerhetsteamet. Administratör för prenumerationen ger åtkomst till säkerhetsteamet med hjälp av en resursgrupp som innehåller de resurser som krävs av programmet.

Nu ska vi se vilka åtgärder varje roll utför i kontexten för det här programmet.

* **Säkerhetsteamet**
  * Skapar nyckelvalv.
  * Aktiverar loggning för Key Vault.
  * Lägger till nycklar/hemligheter.
  * Skapar säkerhetskopior av nycklar för haveriberedskap.
  * Anger principen för Key Vault-åtkomst att bevilja behörigheter till användare och program att utföra specifika åtgärder.
  * Med jämna mellanrum samlar nycklar/hemligheter.
* **Utvecklare/operatörer**
  * Hämta referenser till bootstrap och SSL-certifikat (tumavtryck), lagringsnyckeln (hemlig-URI) och signeringsnyckeln (nyckel URI) från säkerhetsteamet.
  * Utveckla och distribuera program som har åtkomst till nycklar och hemligheter programmässigt.
* **Granskare**
  * Granska användningsloggar för att bekräfta korrekt användning av nycklar/hemligheter och data säkerhetsstandarder efterlevs.

Nu ska vi se vilka behörigheter som krävs för varje roll och program att utföra sina tilldelade uppgifter. 

| Användarroll | Behörigheter på hanteringsplanet | Behörigheter på dataplanet |
| --- | --- | --- |
| Säkerhetsteamet |Nyckelvalvsdeltagare |Nycklar: säkerhetskopiering, skapa, ta bort, hämta, importera, lista, återställa <br> Hemligheter: alla |
| Utvecklare/operatörer |Nyckelvalvet distribueringsbehörighet, så att de virtuella datorerna som de distribuerar kan hämta hemligheter från nyckelvalvet. |Ingen |
| Granskare |Ingen |Nycklar: lista<br>Hemligheter: lista |
| Program |Ingen |Nycklar: signera<br>Hemligheter: hämta |

> [!NOTE]
> Granskare behöver listan behörighet för nycklar och hemligheter så att de kan granska attribut för nycklar och hemligheter som inte släpps i loggarna. Dessa attribut är taggar, aktivering och förfallodatum.
> 
> 

Utöver de behörigheter som Key Vault måste alla tre roller också åtkomst till andra resurser. Till exempel för att kunna distribuera virtuella datorer (eller funktionen Web Apps i Azure App Service), måste utvecklare och operatörer också deltagaråtkomst till de resurstyperna. Granskare behöver läsåtkomst till det lagringskonto där Key Vault-loggar lagras.

Eftersom fokuset för den här artikeln är att säkra åtkomst till ditt nyckelvalv, visar vi bara begrepp för det här avsnittet. Information om distribution av certifikat och åtkomst till nycklar och hemligheter programmässigt omfattas någon annanstans. Exempel:

- Distribuera certifikat som lagras i Key Vault till virtuella datorer behandlas i [distribuera certifikat till virtuella datorer från kundhanterad Key Vault](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) (blogginlägg).
- Den [exempel för Azure Key Vault-klienten ladda ned](https://www.microsoft.com/download/details.aspx?id=45343) illustrerar hur du använder bootstrap-certifikatet för att autentisera till Azure AD för att komma åt ett nyckelvalv.

Du kan bevilja de flesta åtkomstbehörigheterna med hjälp av Azure portal. Om du vill tilldela detaljerade behörigheter kan du behöva använda Azure PowerShell eller CLI för att uppnå det önskade resultatet. 

Följande PowerShell-kodavsnitt förutsätter:

* Azure AD-administratör har skapat säkerhetsgrupper som representerar tre roller (Contoso Security Team, Contoso App Devops och Contoso App Auditors). Administratören har också lagt till användare i de grupper som de tillhör.
* **ContosoAppRG** är resursgruppen där alla resurser finns. **contosologstorage** är där loggfilerna lagras. 
* Nyckelvalvet **ContosoKeyVault**, och storage-kontot som används för Key Vault-loggar **contosologstorage**, måste vara i samma Azure-plats.

Först tilldelar prenumerationsadministratören `key vault Contributor` och `User Access Administrator` roller till säkerhetsteamet. Dessa roller kan säkerhetsteamet hantera åtkomst till andra resurser och hantera nyckelvalv i resursgruppen ContosoAppRG.

```PowerShell
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Följande skript visar hur security-teamet kan skapa ett nyckelvalv och ställa in loggning och åtkomstbehörighet. Mer information om principen för Key Vault behörigheter finns i [om Azure Key Vault-nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md).

```PowerShell
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -Name ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge

# Management plane permissions for Dev/ops
# Create a new role from an existing role
$devopsrole = Get-AzureRmRoleDefinition -Name "Virtual Machine Contributor"
$devopsrole.Id = $null
$devopsrole.Name = "Contoso App Devops"
$devopsrole.Description = "Can deploy VMs that need secrets from key vault"
$devopsrole.AssignableScopes = @("/subscriptions/<SUBSCRIPTION-GUID>")

# Add permission for dev/ops so they can deploy VMs that have secrets deployed from key vaults
$devopsrole.Actions.Add("Microsoft.KeyVault/vaults/deploy/action")
New-AzureRmRoleDefinition -Role $devopsrole

# Assign this newly defined role to Dev ops security group
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Devops')[0].Id -RoleDefinitionName "Contoso App Devops" -ResourceGroupName ContosoAppRG

# Data plane permissions for Auditors
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso App Auditors')[0].Id -PermissionsToKeys list -PermissionsToSecrets list
```

Den definierade anpassade rollen är bara tilldelningsbar till prenumerationen där den `ContosoAppRG` resursgruppen har skapats. Om samma anpassade roller ska användas för andra projekt i andra prenumerationer, kan dess omfattning ha flera prenumerationer tillagda.

Den anpassade rolltilldelningen för behörigheten ”distribuera/åtgärd” för utvecklare och operatörer begränsas till resursgruppen. På så sätt kan endast virtuella datorer som skapas i resursgruppen `ContosoAppRG` ska ha åtkomst till hemligheterna (SSL-certifikat och bootstrap-certifikat). Virtuella datorer skapas i en annan resursgrupp av en utvecklare och operatörer teammedlem har inte åtkomst till dessa hemligheter, även om de har de hemliga URI: er.

Det här exemplet visar ett enkelt scenario. Verkliga livet är vanligtvis scenarier kan vara mer komplexa och du kan justera behörigheterna till ditt nyckelvalv baserat på dina behov. I vårt exempel antar vi säkerhetsteamet ger nyckel och hemliga referenser (URI: er och tumavtryck) som utvecklare och operatörer behöver referera i sina program. Utvecklare och operatörer kräver inte någon dataplansåtkomst. Det här exemplet fokuserar på att skydda ditt nyckelvalv. Du bör beakta liknande secure [dina virtuella datorer](https://azure.microsoft.com/services/virtual-machines/security/), [lagringskonton](../storage/common/storage-security-guide.md), och andra Azure-resurser.

> [!NOTE]
> Det här exemplet visar hur åtkomst för Nyckelvalvet låses i produktionen. Utvecklarna bör ha sin egen prenumeration eller resursgrupp där de har att fullständiga behörigheter att hantera sina valv, virtuella datorer och storage-konto där de utvecklar programmet.

Vi rekommenderar starkt att du ytterligare säker åtkomst till nyckelvalvet genom [konfigurera Key Vault-brandväggar och virtuella nätverk](key-vault-network-security.md).

## <a name="resources"></a>Resurser

* [Azure Active Directory rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md)
  
* [RBAC: Inbyggda roller](../role-based-access-control/built-in-roles.md)
  
* [Förstå Resource Manager-distribution och klassisk distribution](../azure-resource-manager/resource-manager-deployment-model.md)
  
* [Hantera rollbaserad åtkomstkontroll med Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
  
* [Hantera rollbaserad åtkomstkontroll med REST API](../role-based-access-control/role-assignments-rest.md)
  
* [Rollbaserad åtkomstkontroll i Microsoft Azure från Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)
  
  Den här 2015 Microsoft Ignite konferens video beskrivs åt åtkomsthantering och rapporteringsfunktioner i Azure. Det kan också utforskar bästa praxis för att skydda åtkomsten till Azure-prenumerationer med hjälp av Azure AD.
* [Bevilja åtkomst till webbprogram med hjälp av OAuth 2.0 och Azure AD](../active-directory/develop/v1-protocols-oauth-code.md)
  
* [Hantering av Nyckelvalv REST API: er](https://msdn.microsoft.com/library/azure/mt620024.aspx)
  
  Det här dokumentet är referensen för REST-API: er att hantera ditt nyckelvalv programmässigt, inklusive inställning av åtkomstprincip för Nyckelvalvet.
* [Key Vault REST API: er](https://msdn.microsoft.com/library/azure/dn903609.aspx)
  
* [Nyckelåtkomstkontroll](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
* [Åtkomstkontroll till hemlighet](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
* [Ange](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Set-AzureRmKeyVaultAccessPolicy) och [ta bort](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Remove-AzureRmKeyVaultAccessPolicy) åtkomstprincip för Key Vault med hjälp av PowerShell
  
## <a name="next-steps"></a>Nästa steg

[Konfigurera Key Vault-brandväggar och virtuella nätverk](key-vault-network-security.md)

En Komma igång-självstudiekurs för administratörer finns i [Komma igång med Azure Key Vault](key-vault-get-started.md).

Mer information om användningsloggning för Key Vault finns i avsnittet om [Azure Key Vault-loggning](key-vault-logging.md).

Mer information om hur du använder nycklar och hemligheter med Azure Key Vault finns i [om nycklar och hemligheter](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Om du har frågor om Key Vault kan du gå till den [forum](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).

