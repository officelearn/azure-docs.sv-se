---
title: Skydda din Azure Key Vault | Microsoft Docs
description: Hantera åtkomstbehörigheter för nyckelvalv för att hantera Azure-Nyckelvalv, nycklar och hemligheter. Modell med autentisering och auktorisering för nyckelvalvet och hur du skyddar ditt nyckelvalv.
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e5b4e083-4a39-4410-8e3a-2832ad6db405
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: ambapat
ms.openlocfilehash: 4a1de3c011f1f8cfa1ea9246efad4ebb7f9e3a76
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49364531"
---
# <a name="secure-your-key-vault"></a>Säkra ditt nyckelvalv
Azure Key Vault är en molnbaserad tjänst som skyddar krypteringsnycklar och hemligheter (till exempel certifikat, anslutningssträngar, lösenord). Eftersom dessa data är känsliga och verksamhetskritiska, åtkomst till dina nyckelvalv måste skyddas auktoriserade så att endast program och användare för att få åtkomst. 

Den här artikeln innehåller en översikt över åtkomstmodellen för nyckelvalvet. Den förklarar autentisering och auktorisering och beskriver hur du skyddar åtkomst till nyckelvalvet.

## <a name="overview"></a>Översikt
Åtkomst till ett nyckelvalv styrs via två separata gränssnitt: hanteringplanet och dataplanet. För båda planen krävs korrekt autentisering och auktorisering innan en anropare (en användare eller ett program) kan få åtkomst till nyckelvalvet. Autentisering etablerar identiteten hos anroparen, medan auktoriseringen avgör vilka åtgärder som anroparen får utföra.

Både hanteringsplanet och dataplanet använder sig av Azure Active Directory för autentisering. För auktorisering använder sig hanteringsplanet av rollbaserad åtkomstkontroll (RBAC) medan dataplanet använder sig av nyckelvalvets åtkomstprincip.

Här är en kort översikt över de ämnen som täcks:

[Autentisering med hjälp av Azure Active Directory](#authentication-using-azure-active-directory) – Det här avsnittet beskriver hur en anropare autentiserar sig med Azure Active Directory för att få åtkomst till ett nyckelvalv via hanteringsplanet och dataplanet. 

[Hanteringsplanet och dataplanet](#management-plane-and-data-plane) – Hanteringsplanet och dataplanet är två åtkomstplan som används för åtkomst till ditt nyckelvalv. Varje åtkomstplan har stöd för specifika åtgärder. Det här avsnittet beskriver åtkomst-slutpunkterna, åtgärder som stöds och metoder för åtkomstkontroll som används av varje plan. 

[Åtkomstkontroll för hanteringsplanet](#management-plane-access-control) – Det här avsnittet tittar på hur man ger tillgång till hanteringsplanets åtgärder med hjälp av rollbaserad åtkomstkontroll.

[Åtkomstkontroll för dataplanet](#data-plane-access-control) – Det här avsnittet beskriver hur man använder nyckelvalvets åtkomstprincip för att kontrollera åtkomst för dataplanet.

[Exempel](#example) – det här exemplet beskriver hur du ställer in åtkomstkontroll för ditt nyckelvalv och låta tre olika team (säkerhetsteamet, utvecklare/operatörer och granskare) för att utföra specifika uppgifter för att utveckla, hantera och övervaka ett program i Azure.

## <a name="authentication-using-azure-active-directory"></a>Autentisering med Azure Active Directory
När du skapar ett nyckelvalv i en Azure-prenumeration associeras den automatiskt till prenumerationens Azure Active Directory-klient. Alla anropare (användare och program) måste registreras i den här klienten och måste autentiseras för att komma åt nyckelvalvet. Det här kravet gäller för både Hanteringsplanet och dataplanåtkomst. I bägge fallen, kan ett program få åtkomst till nyckelvalvet på två sätt:

* **användare + appåtkomst** – används med program som har åtkomst till nyckelvalvet åt en inloggad användare. Azure PowerShell och Azure portal är exempel på den här typen av åtkomst. Det finns två sätt att bevilja åtkomst till användare: 
- Bevilja åtkomst till användare så att de kan komma åt nyckelvalvet från valfritt program.
- Ge en användaråtkomst till nyckelvalvet endast när de använder ett visst program (kallas sammansatt identitet).

* **endast appen** – används med program som körs som daemon-tjänster eller bakgrundsjobb. Programmets identitet beviljas åtkomst till nyckelvalvet.

I bägge typerna av program, autentiserar sig programmet Azure Active Directory med hjälp av någon av de [autentiseringsmetoder som stöds](../active-directory/develop/authentication-scenarios.md) och hämtar en token. Den autentiseringsmetod som används beror på programtypen. Sedan använder programmet den här token och skickar en REST API-begäran till nyckelvalvet. Hantering av plan begäranden dirigeras via en Azure Resource Manager-slutpunkt. När det gäller åtkomst till dataplanet, pratar programmet direkt med en nyckelvalvs-slutpunkt. Mer information finns på i [hela autentiseringsflödet](../active-directory/develop/v1-protocols-oauth-code.md). 

Resursnamnet som programmet begär en token för skiljer sig åt beroende på om ett program vill ha åtkomst till hanteringsplanet eller dataplanet. Resursnamnet är därför antingen hanteringsplanets eller dataplanets slutpunkt som det beskrivs i tabellen i ett senare avsnitt, beroende på Azure-miljön.

Att ha en enda mekanism för autentisering för både hanterings- och dataplanet har sina egna fördelar:

* Organisationer kan centralt styra åtkomsten till alla viktiga valv i organisationen
* Om en användare lämnar förlorar de omedelbart åtkomst till alla nyckelvalv i organisationen
* Organisationer kan anpassa autentisering via alternativen i Azure Active Directory (till exempel aktivera multifaktorautentisering för extra säkerhet)

## <a name="management-plane-and-data-plane"></a>Hanteringsplanet och dataplanet
Azure Key Vault är en Azure-tjänst tillgängliga via Azure Resource Manager-distributionsmodellen. När du skapar ett nyckelvalv, får du en virtuell behållare för att lagra känsliga objekt, till exempel nycklar, hemligheter och certifikat. Specifika åtgärder som utförs på ett nyckelvalv med Hanteringsplanet och dataplanet hanteringsgränssnitt. Hanteringsplanet används för att hantera själva nyckelvalvet. Detta omfattar åtgärder, till exempel hantera attribut och ställa in åtkomstprinciper för data-plan. Gränssnittet för dataplanet används för att lägga till, ta bort, ändra och använda nycklar, hemligheter och certifikat som lagras i key vault.

Gränssnitt för hantering av Hanteringsplanet och dataplanet kan nås via olika slutpunkter som anges nedan. Den andra kolumnen beskriver DNS-namn för de här slutpunkterna i olika Azure-miljöer. Den tredje kolumnen beskriver de åtgärder du kan utföra från varje åtkomstplan. Varje åtkomstplan har sin egen åtkomstkontroll. Åtkomstkontroll för Hanteringsplanet är inställd via nyckelvalvets åtkomstkontroll (RBAC). Åtkomstkontroll för dataplanet är inställd via nyckelvalvets åtkomstprincip.

| Åtkomstplan | Slutpunkter för åtkomst | Åtgärder | Åtkomstkontrollmekanismer |
| --- | --- | --- | --- |
| Hanteringsplanet |**Globalt:**<br> management.azure.com:443<br><br> **Azure Kina 21Vianet:**<br> management.chinacloudapi.cn:443<br><br> **Azure för amerikanska myndigheter:**<br> management.usgovcloudapi.net:443<br><br> **Azure i Tyskland:**<br> management.microsoftazure.de:443 |Skapa/läsa/Uppdatera/Ta bort nyckelvalv <br> Ställ in åtkomstprinciper för nyckelvalvet<br>Ställ in taggar för nyckelvalvet |Azure Resource Manager rollbaserad åtkomstkontroll (RBAC) |
| Dataplanet |**Globalt:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure Kina 21Vianet:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure för amerikanska myndigheter:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure i Tyskland:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 |För nycklar: Dekryptera, kryptera, UnwrapKey, WrapKey, verifiera, signera, hämta, lista, uppdatera, skapa, importera, ta bort, säkerhetskopiera, återställa<br><br> För hemligheter: hämta, lista, ställ in, ta bort |Åtkomstprincip för nyckelvalvet |

Hantering av hanteringsplanets och dataplanets åtkomstkontroller fungerar oberoende av varandra. Till exempel om du vill ge ett programåtkomst för att använda nycklar i key vault behöver du bara att bevilja åtkomst till dataplanet. Komma åt via nyckelvalvets åtkomstprinciper. En användare som behöver läsa valvegenskaper och taggar, men inte åtkomst till data (nycklar, hemligheter eller certifikat) behöver däremot bara åtkomst till dataplanet kontroll. Komma åt genom att tilldela ”läsa” åtkomst till den användaren, med hjälp av RBAC.

## <a name="management-plane-access-control"></a>Åtkomstkontroll för hanteringsplanet
Hanteringsplanet består av åtgärder som påverkar själva, nyckelvalvet som:

- Skapa eller ta bort ett nyckelvalv.
- Hämta en lista över valv i en prenumeration.
- Hämta nyckelvalvsegenskaper (till exempel SKU-taggar).
- Ställa in åtkomstprinciper för nyckelvalvet som kontrollen användar- och åtkomst till nycklar och hemligheter.

Åtkomstkontroll för hanteringsplanet använder sig av RBAC. Se den fullständiga listan med nyckelvalvsåtgärder som kan utföras via Hanteringsplanet i tabellen i föregående avsnitt. 

### <a name="role-based-access-control-rbac"></a>Rollbaserad åtkomstkontroll (RBAC)
Varje Azure-prenumerationen har en Azure Active Directory. Användare, grupper och program från den här katalogen kan beviljas åtkomst för att hantera resurser i Azure-prenumerationen som använder Azure Resource Manager-distributionsmodellen. Den här typen av åtkomstkontroll kallas rollbaserad åtkomstkontroll (RBAC). För att hantera den här åtkomsten, kan du använda [Azure Portal](https://portal.azure.com/), [Azure CLI-verktygen](../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs) eller [Azure Resource Manager REST API:er](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Du skapar ett nyckelvalv i en resurs och kontrollerar åtkomst till Hanteringsplanet med Azure Active Directory. Du kan till exempel ge användare eller en grupp möjlighet att hantera nyckelvalv i en resursgrupp.

Du kan ge åtkomst till användare, grupper och program i en specifik omfattning genom att tilldela lämpliga RBAC-roller. Till exempel för att bevilja åtkomst till en användare att hantera nyckelvalv tilldela du den fördefinierade rollen för Key Vault-bidragsgivare till den här användaren i ett visst omfång. Omfånget är i det här fallet en prenumeration, en resursgrupp eller ett visst nyckelvalv. En roll som tilldelats på abonnemangsnivå gäller för alla resursgrupper och resurser inom den prenumerationen. En roll som tilldelats på resursgruppnivå gäller för alla resurser inom den resursgruppen. En roll som tilldelats för en specifik resurs gäller för den här resursen. Det finns flera fördefinierade roller (se [RBAC: inbyggda roller](../role-based-access-control/built-in-roles.md)). Om den fördefinierade rollen inte passar dina behov kan du definiera en egen roll.

> [!IMPORTANT]
> Observera att om en användare har bidragsgivarbehörigheter (RBAC) till ett nyckelvalv, kan hen ge sig själv åtkomst till dataplanet genom att ställa in den åtkomstprincipen för nyckelvalvet som kontrollerar åtkomst till dataplanet. Vi rekommenderar därför att kraftigt begränsa vem som har "Bidragsgivar"-tillgång till dina nyckelvalv för att se till att enbart auktoriserade personer kan komma åt och hantera dina nyckelvalv, nycklar, hemligheter och certifikat.
> 
> 

## <a name="data-plane-access-control"></a>Åtkomstkontroll för dataplanet
Åtgärder för nyckelvalv plan gäller lagrade objekt, till exempel nycklar, hemligheter och certifikat. Viktiga åtgärder är bland annat skapa, importera, uppdatera, lista, säkerhetskopiera och återställa nycklar. Kryptografiska åtgärder omfattar inloggning, verifiera, kryptera, dekryptera, omsluta, packa upp, ställa in taggar och andra attribut för nycklar. På samma sätt åtgärder för hemligheter inkluderar get, set, lista, ta bort.

Åtkomst till dataplanet ges genom att ställa in åtkomstprinciper för ett nyckelvalv. En användare, grupp eller ett program måste ha bidragsgivarbehörighet (RBAC) för hanteringsplanet för ett nyckelvalv för att kunna ställa in åtkomstprinciper för det nyckelvalvet. En användare, grupp eller ett program kan beviljas åtkomst för att utföra specifika åtgärder för nycklar eller hemligheter i nyckelvalvet. Nyckelvalv stöder upp till 1024 åtkomstprinciper för ett nyckelvalv. Skapa en säkerhetsgrupp i Azure Active Directory och lägg till användare i den gruppen för att tilldela dataplansåtkomst till ett nyckelvalv för flera användare.

### <a name="key-vault-access-policies"></a>Åtkomstprinciper för nyckelvalvet
Åtkomstprinciper för nyckelvalvet ger separata behörigheter till nycklar, hemligheter och certifikat. Du kan till exempel ge en användaråtkomst till bara nycklar, och ingen behörighet för hemligheter. Behörighet att komma åt nycklar eller hemligheter eller certifikat är på valvnivå. Nyckelvalvets åtkomstprincip stöder inte detaljerade behörigheter på objektsnivå, till exempel en specifik/hemlighet/certifikatet. Du kan använda [Azure Portal](https://portal.azure.com/), [Azure CLI verktyg](../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs), eller [REST API:er för nyckelvalvshantering](https://msdn.microsoft.com/library/azure/mt620024.aspx) för att ställa in åtkomstprinciper för ett nyckelvalv.

> [!IMPORTANT]
> Observera att åtkomstprinciper för nyckelvalv gäller på valvnivå. En användare som har beviljats behörighet att skapa och ta bort nycklar, kan utföra de åtgärderna på alla nycklar i nyckelvalvet.

Utöver åtkomstprinciper kan även dataplansåtkomst vara begränsad med [tjänstslutpunkter för virtuellt nätverk för Azure Key Vault](key-vault-overview-vnet-service-endpoints.md) genom att konfigurera [brandväggar och regler för virtuella nätverk](key-vault-network-security.md) för ett extra säkerhetslager.

## <a name="example"></a>Exempel
Vi antar att du utvecklar ett program som använder ett certifikat för SSL, Azure storage för att lagra data och en RSA 2048-bitars nyckel för signeringsåtgärder. Vi antar att det här programmet körs i en Azure virtuell dator (eller en Virtual Machine Scale Sets). Du kan använda ett nyckelvalv för att lagra alla programmets hemligheter och lagra bootstrap-certifikatet används av programmet för att autentisera med Azure Active Directory.

Här är en sammanfattning av typerna av nycklar och hemligheter som lagras i key vault:

* **SSL-Cert** – används för SSL
* **Lagringsnyckeln** – används för att få åtkomst till lagringskontot
* **RSA 2048-bitars nyckel** – används för signeringsåtgärder
* **Bootstrap-certifikatet** – används för att autentisera med Azure Active Directory. Du kan hämta lagringsnyckeln och använda RSA-nyckel för signering när åtkomst beviljas.

Nu ska vi möta de personer som hanterar, distribuerar och granskar det här programmet. Vi använder oss av tre roller i det här exemplet.

* **Säkerhetsteamet** -vanligtvis IT-personal från kontoret för CSO (Chief Security Officer) eller motsvarande. Den här team ansvarar för för att hemligheter. Till exempel SSL-certifikat, RSA-nycklar för signering, anslutningssträngar, och storage-kontonycklar.
* **Utvecklare/operatörer** -personalen som utvecklar programmet och sedan distribuera det i Azure. Normalt kan de inte är en del av säkerhetsteamet och kan därför de får inte ha åtkomst till känsliga data, till exempel SSL-certifikat och RSA-nycklar. Programmet de distribuerar ska ha åtkomst till dessa objekt.
* **Granskare** -vanligtvis en annan uppsättning personer, isolerade från utvecklare och allmän IT-personal. Deras ansvarsområde är att granska användning och underhåll av certifikat, nycklar och hemligheter för att säkerställa att säkerhetsstandarder. 

Det finns en till roll som inte omfattas av det här programmet, men relevanta här anges. Rollen är en prenumeration (eller resursgrupp) administratör. Prenumerationsadministratören ställer in inledande åtkomstbehörigheter för säkerhetsteamet. Administratör för prenumerationen ger åtkomst till säkerhetsteamet med hjälp av en resursgrupp som innehåller de resurser som krävs av programmet.

Nu ska vi se vilka åtgärder varje roll utför i kontexten för det här programmet.

* **Säkerhetsteamet**
  * Skapa nyckelvalv
  * Slå på nyckelvalvs-loggning
  * Lägger till nycklar/hemligheter
  * Skapar säkerhetskopior av nycklar för haveriberedskap
  * Ställer in åtkomstprincip för nyckelvalvet för att ge behörigheter till användare och program att utföra specifika åtgärder
  * Byter periodvis nycklar/hemligheter
* **Utvecklare/operatörer**
  * Hämta referenser till bootstrap och SSL-certifikat (tumavtryck), lagringsnyckeln (hemlig-URI) och signeringsnyckeln (nyckel-URI) från säkerhetsteamet
  * Utvecklar och distribuerar programmet som har programmässig åtkomst till nycklar och hemligheter
* **Granskare**
  * Granska användningsloggar för att bekräfta korrekt användning av nycklar/hemligheter samt efterlevnad av datasäkerhetsstandarder

Nu ska vi se vilka behörigheter som krävs för varje roll och program att utföra sina tilldelade uppgifter. 

| Användarroll | Behörigheter på hanteringsplanet | Behörigheter på dataplanet |
| --- | --- | --- |
| Säkerhetsteamet |nyckelvalvsbidragare |Nycklar: säkerhetskopiering, skapa, ta bort, hämta, importera, lista, återställa <br> Hemligheter: alla |
| Utvecklare/operatör |nyckelvalvet distribueringsbehörighet så att de VM:ar de distribuerar kan hämta hemligheter från nyckelvalvet |Ingen |
| Granskare |Ingen |Nycklar: lista<br>Hemligheter: lista |
| Program |Ingen |Nycklar: signera<br>Hemligheter: hämta |

> [!NOTE]
> Granskare behöver ha listbehörighet för nycklar och hemligheter så att de kan granska attribut för nycklar och hemligheter som inte släpps i loggarna, som taggar, aktiverings och utgångsdatum.
> 
> 

Utöver de behörigheter som nyckelvalvet måste alla tre roller också åtkomst till andra resurser. Till exempel för att kunna distribuera VM:ar (eller webbappar osv.) Utvecklare/operatörer måste också ha bidragsgivaråtkomst till de resurstyperna. Granskare behöver ”läsa” åtkomst till lagringskontot där nyckelvalvsloggarna lagras.

Eftersom fokuset för den här artikeln är att säkra åtkomst till ditt nyckelvalv, visar vi bara begrepp för det här avsnittet. Information om distribution av certifikat, åtkomst till nycklar och hemligheter programmässigt och andra, omfattas någon annanstans. Exempel:

- Distribuera certifikat som lagras i key vault till virtuella datorer ingår i en [blogginlägget: Distribuera certifikat till virtuella datorer från kundhanterad Key Vault](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/)
- Den [exempel för Azure Key Vault-klienten ladda ned](https://www.microsoft.com/download/details.aspx?id=45343) illustrerar hur du använder en bootstrap-certifikatet för att autentisera till Azure AD för att komma åt ett nyckelvalv.

De flesta åtkomstbehörigheterna kan tilldelas via Azure-portalen. Om du vill tilldela detaljerade behörigheter kan du behöva använda Azure PowerShell eller CLI för att uppnå det önskade resultatet. 

Följande PowerShell-kodavsnitt förutsätter:

* Azure Active Directory-administratören har skapat säkerhetsgrupper som representerar tre roller (Contoso Security Team, Contoso App Devops, Contoso App Auditors). Administratören har också har lagt till användare i de grupper som de tillhör.
* **ContosoAppRG** är resursgruppen där alla resurser finns. **contosologstorage** är där loggfilerna lagras. 
* Nyckelvalvet **ContosoKeyVault** och lagringskontot som används för nyckelvalvsloggar **contosologstorage** måste vara på samma plats i Azure

Först tilldelar prenumerationsadministratören rollerna nyckelvalvsbidragare och administratör för användaråtkomst till säkerhetsteamet. Dessa roller kan säkerhetsteamet hantera åtkomst till andra resurser och hantera nyckelvalv i resursgruppen ContosoAppRG.

```
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Följande skript visar hur security-teamet kan skapa ett nyckelvalv och ställa in loggning och åtkomstbehörighet. Se [om Azure Key Vault-nycklar, hemligheter och certifikat](about-keys-secrets-and-certificates.md) mer information om hur Key Vault åtkomstbehörigheter principen.

```
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

Den definierade anpassade rollen är bara tilldelningsbar till prenumerationen där ContosoAppRG-resursgruppen har skapats. Om samma anpassade roller ska användas för andra projekt i andra prenumerationer, ha dess omfattning flera prenumerationer tillagda.

Den anpassade rolltilldelningen för utvecklare/operatörer ”distribuera/åtgärd” behörigheten är begränsad till resursgruppen. På så sätt kan endast virtuella datorer som skapas i resursgruppen ContosoAppRG om du ska ha åtkomst till hemligheterna (SSL-certifikat och bootstrap-certifikat). Virtuella datorer som skapas i en annan resursgrupp genom att en dev/ops-gruppmedlem har inte åtkomst till dessa hemligheter, även om de har de hemliga URI: er.

Det här exemplet visar ett enkelt scenario. Verkliga livet är vanligtvis scenarier kan vara mer komplexa och du kan behöva justera behörigheterna till ditt nyckelvalv baserat på dina behov. I vårt exempel antar vi att säkerhetsteamet kommer att ge nyckel och hemliga referenser (URI: er och tumavtryck), som utvecklare/operatörer behöver referera i sina program. Utvecklare/operatörer kräver inte någon dataplansåtkomst. Det här exemplet fokuserar på att skydda ditt nyckelvalv. Liknande bör övervägas att skydda [dina virtuella datorer](https://azure.microsoft.com/services/virtual-machines/security/), [lagringskonton](../storage/common/storage-security-guide.md), och andra Azure-resurser.

> [!NOTE]
> Obs: Det här exemplet visar hur åtkomst till nyckelvalvet låses i produktionen. Utvecklarna bör ha sina egna prenumerationer eller resursgrupper där de har fullständiga behörigheter att hantera sina valv, VM:ar och lagringskonton där de utvecklar programmet.

Vi rekommenderar starkt att skydda åtkomsten till ditt nyckelvalv ytterligare av [konfigurera Key Vault-brandväggar och virtuella nätverk](key-vault-network-security.md).

## <a name="resources"></a>Resurser
* [Azure Active Directory rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md)
  
  Den här artikeln förklarar Azure Active Directorys rollbaserade åtkomstkontroll och hur den fungerar.
* [RBAC: inbyggda roller](../role-based-access-control/built-in-roles.md)
  
  Den här artikeln beskriver alla inbyggda roller som är tillgängliga i RBAC.
* [Förstå Resource Manager-distribution och klassisk distribution](../azure-resource-manager/resource-manager-deployment-model.md)
  
  Den här artikeln beskriver Resource Manager-distributionsmodellen och den klassiska distributionsmodellen och förklarar fördelarna med att använda Resource Manager och resursgrupper
* [Hantera rollbaserad åtkomstkontroll med Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
  
  Den här artikeln förklarar hur du hanterar rollbaserad åtkomstkontroll med Azure PowerShell
* [Hantera rollbaserad åtkomstkontroll med REST API](../role-based-access-control/role-assignments-rest.md)
  
  Den här artikeln visar hur du använder REST API för att hantera RBAC.
* [Rollbaserad åtkomstkontroll i Microsoft Azure från Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)
  
  Den här 2015 Microsoft Ignite konferens video beskrivs åt åtkomsthantering och rapporteringsfunktioner i Azure. Det kan också utforskar bästa praxis för att skydda åtkomsten till Azure-prenumerationer med Azure Active Directory.
* [Auktorisera åtkomst till webbprogram med OAuth 2.0 och Azure Active Directory](../active-directory/develop/v1-protocols-oauth-code.md)
  
  Den här artikeln beskriver det fullständiga OAuth 2.0-flödet för att autentisera med Azure Active Directory.
* [REST API:er för nyckelvalvshantering](https://msdn.microsoft.com/library/azure/mt620024.aspx)
  
  Det här dokumentet är referensen för REST-API:erna att hantera ditt nyckelvalv programmässigt, inklusive att ställa in åtkomstprincip för nyckelvalvet.
* [REST API:er för nyckelvalvet](https://msdn.microsoft.com/library/azure/dn903609.aspx)
  
  Länk till referensdokumentation för REST API:er för nyckelvalvet.
* [Nyckelåtkomstkontroll](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_KeyAccessControl)
  
  Länk till referensdokumentation för åtkomstkontroll till hemlighet.
* [Åtkomstkontroll till hemlighet](https://msdn.microsoft.com/library/azure/dn903623.aspx#BKMK_SecretAccessControl)
  
  Länk till referensdokumentation för nyckelåtkomstkontroll.
* [Ange](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Set-AzureRmKeyVaultAccessPolicy) och [ta bort](https://docs.microsoft.com/powershell/module/azurerm.keyvault/Remove-AzureRmKeyVaultAccessPolicy) åtkomstprincip för nyckelvalvet med PowerShell
  
  Länkar till referensdokumentation för PowerShell-cmdletar för att hantera åtkomstprinciper för nyckelvalvet.

## <a name="next-steps"></a>Nästa steg
[Konfigurera Key Vault-brandväggar och virtuella nätverk](key-vault-network-security.md)

För en komma igång-självstudie för administratörer, kan du se [Kom igång med Azure Key Vault](key-vault-get-started.md).

Mer information om av användningsloggning för Key Vault finns i avsnittet om [Azure Key Vault-loggning](key-vault-logging.md).

Mer information om hur du använder nycklar och hemligheter med Azure Key Vault finns i [Om nycklar och hemligheter](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Om du har några frågor om Nyckelvalv ska du gå till [Azure Key Vault-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)

