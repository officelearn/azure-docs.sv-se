---
title: Säkra ditt nyckelvalv | Microsoft Docs
description: Hantera åtkomstbehörigheter för nyckelvalv för att hantera valv och nycklar och hemligheter. Autentiserings- och auktoriseringsmodell för nyckelvalvet och hur du säkrar ditt nyckelvalv
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
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: ambapat
ms.openlocfilehash: df577222fb8f9d13bd33c5705e6234362519d351
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2018
ms.locfileid: "41920380"
---
# <a name="secure-your-key-vault"></a>Säkra ditt nyckelvalv
Azure Key Vault är en molntjänst som skyddar krypteringsnycklar och hemligheter (som certifikat, anslutningssträngar, lösenord) för dina molnprogram. Eftersom dessa data är känsliga och verksamhetskritiska, behöver du säkra åtkomsten till dina nyckelvalv så att enbart auktoriserade program och användare har åtkomst. Den här artikeln ger en översikt över åtkomstmodellen för nyckelvalvet, förklarar autentisering och auktorisering och beskriver hur du skyddar åtkomst till nyckelvalvet för dina molnprogram med ett exempel.

## <a name="overview"></a>Översikt
Åtkomst till ett nyckelvalv styrs via två separata gränssnitt: hanteringplanet och dataplanet. För båda planen krävs korrekt autentisering och auktorisering innan en anropare (en användare eller ett program) kan få åtkomst till nyckelvalvet. Autentisering etablerar identiteten hos anroparen, medan auktorisering avgör vilka åtgärder som anroparen får utföra.

Både hanteringsplanet och dataplanet använder sig av Azure Active Directory för autentisering. För auktorisering använder sig hanteringsplanet av rollbaserad åtkomstkontroll (RBAC) medan dataplanet använder sig av nyckelvalvets åtkomstprincip.

Här är en kort översikt över de ämnen som täcks:

[Autentisering med hjälp av Azure Active Directory](#authentication-using-azure-active-directory) – Det här avsnittet beskriver hur en anropare autentiserar sig med Azure Active Directory för att få åtkomst till ett nyckelvalv via hanteringsplanet och dataplanet. 

[Hanteringsplanet och dataplanet](#management-plane-and-data-plane) – Hanteringsplanet och dataplanet är två åtkomstplan som används för åtkomst till ditt nyckelvalv. Varje åtkomstplan har stöd för specifika åtgärder. Det här avsnittet beskriver åtkomst-slutpunkterna, åtgärder som stöds och metoder för åtkomstkontroll som används av varje plan. 

[Åtkomstkontroll för hanteringsplanet](#management-plane-access-control) – Det här avsnittet tittar på hur man ger tillgång till hanteringsplanets åtgärder med hjälp av rollbaserad åtkomstkontroll.

[Åtkomstkontroll för dataplanet](#data-plane-access-control) – Det här avsnittet beskriver hur man använder nyckelvalvets åtkomstprincip för att kontrollera åtkomst för dataplanet.

[Exempel](#example) – Det här exemplet beskriver hur du ställer in åtkomstkontroll för dina nyckelvalv för att låta tre olika team (säkerhetsteamet, utvecklare/operatörer och granskare) utföra specifika uppgifter för att utveckla, hantera och övervaka ett program i Azure.

## <a name="authentication-using-azure-active-directory"></a>Autentisering med Azure Active Directory
När du skapar ett nyckelvalv i en Azure-prenumeration, knyts det automatiskt till prenumerationens Azure Active Directory-klientorganisation. Alla anropare (användare och program) måste registreras på den här klientorganisationen för att ha åtkomst till nyckelvalvet. Ett program eller en användare måste autentisera sig med Azure Active Directory för att få åtkomst till nyckelvalvet. Det gäller både för åtkomst till hanteringsplanet och dataplanet. I bägge fallen, kan ett program få åtkomst till nyckelvalvet på två sätt:

* **användare+appåtkomst** – vanligtvis är det här för program som har åtkomst till nyckelvalvet åt en inloggad användare. Azure PowerShell, Azure Portal är exempel på den här typen av åtkomst. Det finns två sätt att ge åtkomst till användare: ett sätt är att ge åtkomst till användare så att de kan komma åt nyckelvalvet från valfritt program och det andra är att enbart ge en användare åtkomst till nyckelvalvet när de använder ett visst program (kallas sammansatt identitet). 
* **endast appen** – för program som kör daemon-tjänster, bakgrundsjobb osv. Programmets identitet beviljas åtkomst till nyckelvalvet.

I bägge typerna av program, autentiserar sig programmet Azure Active Directory med hjälp av någon av de [autentiseringsmetoder som stöds](../active-directory/develop/authentication-scenarios.md) och hämtar en token. Den autentiseringsmetod som används beror på programtypen. Sedan använder programmet den här token och skickar en REST API-begäran till nyckelvalvet. När det gäller åtkomst på hanteringsplanet, dirigeras begärandena genom Azure Resource Manager-slutpunkten. När det gäller åtkomst till dataplanet, pratar programmet direkt med en nyckelvalvs-slutpunkt. Mer information finns på i [hela autentiseringsflödet](../active-directory/develop/v1-protocols-oauth-code.md). 

Resursnamnet som programmet begär en token för skiljer sig åt beroende på om ett program vill ha åtkomst till hanteringsplanet eller dataplanet. Resursnamnet är därför antingen hanteringsplanets eller dataplanets slutpunkt som det beskrivs i tabellen i ett senare avsnitt, beroende på Azure-miljön.

Att ha en enda mekanism för autentisering för både hanterings- och dataplanet har sina egna fördelar:

* Organisationer kan centralt styra åtkomsten till alla viktiga valv i organisationen
* Om en användare lämnar förlorar de omedelbart åtkomst till alla nyckelvalv i organisationen
* Organisationer kan anpassa autentisering via alternativen i Azure Active Directory (till exempel aktivera multifaktorautentisering för extra säkerhet)

## <a name="management-plane-and-data-plane"></a>Hanteringsplanet och dataplanet
Azure Key Vault är en Azure-tjänst som finns tillgänglig via distributionsmodellen Azure Resouce Manager. När du skapar ett nyckelvalv, får du en virtuell container i vilken du kan skapa andra objekt som nycklar, hemligheter och certifikat. Du kan sedan använda ditt nyckelvalv med hanteringsplanet och dataplanet för att utföra specifika åtgärder. Gränssnittet för hanteringsplanet används för att hantera dina nyckelvärden som att skapa, ta bort, uppdatera nyckelvärdesattribut och ställa in åtkomstprinciper för dataplanet. Gränssnittet för dataplanet används för att lägga till, ta bort, ändra och använda nycklarna, hemligheterna och certifikaten som lagras i ditt nyckelvalv.

Gränssnitten för hanteringplanet och dataplanet kan nås via olika slutpunkter (se tabell). Den andra kolumnen i tabellen beskriver DNS-namn för de här slutpunkterna i olika Azure-miljöer. Den tredje kolumnen beskriver de åtgärder du kan utföra från varje åtkomstplan. Varje åtkomstplan har sin egen åtkomstkontroll: för hanteringsplanet är åtkomstkontrollen inställd med Azure Resource Manager rollbaserad åtkomstkontroll (RBAC), medan dataplanets åtkomstkontroll är inställd via nyckelvalvets åtkomstprinciper.

| Åtkomstplan | Slutpunkter för åtkomst | Åtgärder | Åtkomstkontrollmekanismer |
| --- | --- | --- | --- |
| Hanteringsplanet |**Globalt:**<br> management.azure.com:443<br><br> **Azure i Kina:**<br> management.chinacloudapi.cn:443<br><br> **Azure för amerikanska myndigheter:**<br> management.usgovcloudapi.net:443<br><br> **Azure i Tyskland:**<br> management.microsoftazure.de:443 |Skapa/läsa/Uppdatera/Ta bort nyckelvalv <br> Ställ in åtkomstprinciper för nyckelvalvet<br>Ställ in taggar för nyckelvalvet |Azure Resource Manager rollbaserad åtkomstkontroll (RBAC) |
| Dataplanet |**Globalt:**<br> &lt;vault-name&gt;.vault.azure.net:443<br><br> **Azure i Kina:**<br> &lt;vault-name&gt;.vault.azure.cn:443<br><br> **Azure för amerikanska myndigheter:**<br> &lt;vault-name&gt;.vault.usgovcloudapi.net:443<br><br> **Azure i Tyskland:**<br> &lt;vault-name&gt;.vault.microsoftazure.de:443 |För nycklar: Dekryptera, kryptera, UnwrapKey, WrapKey, verifiera, signera, hämta, lista, uppdatera, skapa, importera, ta bort, säkerhetskopiera, återställa<br><br> För hemligheter: hämta, lista, ställ in, ta bort |Åtkomstprincip för nyckelvalvet |

Hanteringsplanets och dataplanets åtkomstkontroller fungerar oberoende av varandra. Om du exempelvis vill ge ett program åtkomst för att använda nycklar i ett nyckelvalv, behöver du bara ge dataplansåtkomstbehörigheter med nyckelvalvets åtkomstprinciper och ingen hanteringsplansåtkomst behövs för programmet. Om du vill att en användare ska kunna läsa valvegenskaper och taggar, men inte ha åtkomst till nycklar, hemligheter eller certifikat så kan du bevilja den här användaren läsbehörighet med RBAC och ingen åtkomst till dataplanet krävs.

## <a name="management-plane-access-control"></a>Åtkomstkontroll för hanteringsplanet
Hanteringsplanet består av åtgärder som påverkar själva nyckelvalvet. Du kan till exempel skapa eller ta bort ett nyckelvalv. Du kan hämta en lista över valv i en prenumeration. Du kan hämta nyckelvalvsegenskaper (till exempel SKU-taggar) och ange nyckelvalvsprinciper som styr vilka användare och program som har åtkomst till nycklar och hemligheter i nyckelvalvet. Åtkomstkontroll för hanteringsplanet använder sig av RBAC. Se den fullständiga listan med nyckelvalvsåtgärder som kan utföras, via hanteringsplanet i tabellen i det föregående avsnittet. 

### <a name="role-based-access-control-rbac"></a>Rollbaserad åtkomstkontroll (RBAC)
Varje Azure-prenumerationen har en Azure Active Directory. Användare, grupper och program från den här katalogen kan beviljas åtkomst för att hantera resurser i Azure-prenumerationen som använder Azure Resource Manager-distributionsmodellen. Den här typen av åtkomstkontroll kallas rollbaserad åtkomstkontroll (RBAC). För att hantera den här åtkomsten, kan du använda [Azure Portal](https://portal.azure.com/), [Azure CLI-verktygen](../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs) eller [Azure Resource Manager REST API:er](https://msdn.microsoft.com/library/azure/dn906885.aspx).

Med Azure Resource Manager-modellen, skapar du nyckelvalv i en resursgrupp och kontrollerar åtkomst till hanteringsplanet för det här nyckelvalvet med Azure Active Directory. Du kan till exempel ge användare eller en grupp möjlighet att hantera nyckelvalv i en specifik resursgrupp.

Du kan ge åtkomst till användare, grupper och program i en specifik omfattning genom att tilldela lämpliga RBAC-roller. För att exempelvis bevilja åtkomst till en användare för att hantera nyckelvalven, tilldelar du den fördefinierade rollen ”nyckelvalvsbidragare” till användaren i ett visst omfång. Omfånget är i det här fallet en prenumeration, en resursgrupp eller bara ett visst nyckelvalv. En roll som tilldelats på abonnemangsnivå gäller för alla resursgrupper och resurser inom den prenumerationen. En roll som tilldelats på resursgruppnivå gäller för alla resurser inom den resursgruppen. En roll som tilldelats för en specifik resurs gäller bara för den resursen. Det finns flera fördefinierade roller (se [RBAC: inbyggda roller](../role-based-access-control/built-in-roles.md)) och om de fördefinierade rollerna inte motsvarar dina behov kan du också definiera egna roller.

> [!IMPORTANT]
> Observera att om en användare har bidragsgivarbehörigheter (RBAC) till ett nyckelvalv, kan hen ge sig själv åtkomst till dataplanet genom att ställa in den åtkomstprincipen för nyckelvalvet som kontrollerar åtkomst till dataplanet. Vi rekommenderar därför att kraftigt begränsa vem som har "Bidragsgivar"-tillgång till dina nyckelvalv för att se till att enbart auktoriserade personer kan komma åt och hantera dina nyckelvalv, nycklar, hemligheter och certifikat.
> 
> 

## <a name="data-plane-access-control"></a>Åtkomstkontroll för dataplanet
Nyckelvalvets dataplan består av åtgärder som påverkar objekten i ett nyckelvalv, till exempel nycklar, hemligheter och certifikat.  Det omfattar nyckelåtgärder som att skapa, importera, uppdatera, lista, säkerhetskopiera och återställa nycklar, kryptografiska åtgärder som att signera, verifiera, kryptera, avkryptera, wrappa och unwrappa samt ställa in taggar och andra attribut för nycklar. För hemligheter inkluderar det att hämta, ställa in, lista och ta bort.

Åtkomst till dataplanet ges genom att ställa in åtkomstprinciper för ett nyckelvalv. En användare, grupp eller ett program måste ha bidragsgivarbehörighet (RBAC) för hanteringsplanet för ett nyckelvalv för att kunna ställa in åtkomstprinciper för det nyckelvalvet. En användare, grupp eller ett program kan beviljas åtkomst för att utföra specifika åtgärder för nycklar eller hemligheter i nyckelvalvet. Nyckelvalv stöder upp till 1024 åtkomstprinciper för ett nyckelvalv. Skapa en säkerhetsgrupp i Azure Active Directory och lägg till användare i den gruppen för att tilldela dataplansåtkomst till ett nyckelvalv för flera användare.

### <a name="key-vault-access-policies"></a>Åtkomstprinciper för nyckelvalvet
Åtkomstprinciper till nyckelvalvet ger separata behörigheter till nycklar, hemligheter och certifikat. Du kan till exempel ge en användare åtkomst till bara nycklar, men inte hemligheter. Behörighet att komma åt nycklar eller hemligheter eller certifikat är dock valvnivå. Med andra ord stöder inte åtkomstprinciper för nyckelvalvet behörigheter på objektsnivå. Du kan använda [Azure Portal](https://portal.azure.com/), [Azure CLI verktyg](../cli-install-nodejs.md), [PowerShell](/powershell/azureps-cmdlets-docs), eller [REST API:er för nyckelvalvshantering](https://msdn.microsoft.com/library/azure/mt620024.aspx) för att ställa in åtkomstprinciper för ett nyckelvalv.

> [!IMPORTANT]
> Observera att åtkomstprinciper för nyckelvalv gäller på valvnivå. En användare som har beviljats behörighet att skapa och ta bort nycklar, kan utföra de åtgärderna på alla nycklar i nyckelvalvet.
> 
> 

## <a name="example"></a>Exempel
Anta att du utvecklar ett program som använder ett certifikat för SSL, Azure Storage för att lagra data och använder sig av en RSA 2048-bitars nyckel för signeringsåtgärder. Anta att det här programmet körs i en VM (eller en VM-skaluppsättning). Du kan använda ett nyckelvalv för att lagra alla programmets hemligheter och använda nyckelvalvet för att lagra bootstrap-certifikatet som används av programmet för att autentisera med Azure Active Directory.

Så här är en sammanfattning av alla nycklar och hemligheter ska lagras i ett nyckelvalv.

* **SSL-certifikat** – används för SSL
* **Lagringsnyckeln** – används för att få åtkomst till lagringskontot
* **RSA 2048-bitars nyckel** – används för signeringsåtgärder
* **Bootstrap-certifikatet** – används för att autentisera till Azure Active Directory för att få åtkomst till nyckelvalvet för att hämta lagringsnyckeln och använda RSA-nyckeln för signering.

Nu ska vi möta de personer som hanterar, distribuerar och granskar det här programmet. Vi använder oss av tre roller i det här exemplet.

* **Säkerhetsteamet** – Det här är vanligtvis IT-personal från kontoret för CSO:n (Chief Security Officer) eller likvärdigt. De ansvarar för att hemligheter hålls säkra som SSL-certifikat, RSA-nycklar för signering, anslutningssträngar för databaser, lagringskontonycklar.
* **Utvecklare/operatörer** – Det här är personalen som utvecklar programmet och distribuerar det i Azure. Vanligtvis är inte en del av säkerhetsteamet och bör därför inte ha åtkomst till känslig data, till exempel SSL-certifikat och RSA-nycklar men programmet de distribuerar ska ha åtkomst till dem.
* **Granskare** – Det här är vanligtvis en annan uppsättning personer, isolerade från utvecklare och allmän IT-personal. Deras ansvarsområde är att granska användning och underhåll av certifikat, nycklar osv. samt att tillse att datasäkerhetsstandarder efterlevs. 

Det finns en till roll som ligger utanför omfånget för det här programmet, men som tål att nämnas och det är prenumerationens (eller resursgruppens) administratör. Prenumerationsadministratören ställer in inledande åtkomstbehörigheter för säkerhetsteamet. Vi förutsätter här att prenumerationsadministratören har gett åtkomst till säkerhetsteamet till en resursgrupp i vilken alla de resurser som krävs för det här programmet finns.

Nu ska vi se vilka åtgärder varje roll utför i kontexten för det här programmet.

* **Säkerhetsteamet**
  * Skapa nyckelvalv
  * Slå på nyckelvalvs-loggning
  * Lägger till nycklar/hemligheter
  * Skapar säkerhetskopior av nycklar för haveriberedskap
  * Ställer in åtkomstprincip för nyckelvalvet för att ge behörigheter till användare och program att utföra specifika åtgärder
  * Byter periodvis nycklar/hemligheter
* **Utvecklare/operatörer**
  * Hämtar referenser till bootstrap- och SSL-certifikat (tumavtryck), lagringsnyckeln (hemlig-URI) och signeringsnyckeln (nyckel-URI) från säkerhetsteamet
  * Utvecklar och distribuerar programmet som har programmässig åtkomst till nycklar och hemligheter
* **Granskare**
  * Granska användningsloggar för att bekräfta korrekt användning av nycklar/hemligheter samt efterlevnad av datasäkerhetsstandarder

Nu ska vi se vilka åtkomstbehörigheter för åtkomst till nyckelvalvet som behövs för varje roll (och programmet) för att kunna utföra sina tilldelade uppgifter. 

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

Förutom behörigheter till nyckelvalvet, måste alla tre roller också ha åtkomst till andra resurser. Till exempel för att kunna distribuera VM:ar (eller webbappar osv.) Utvecklare/operatörer måste också ha bidragsgivaråtkomst till de resurstyperna. Granskare behöver läsåtkomst till lagringskontot där nyckelvalvsloggarna lagras.

Eftersom fokuset för den här artikeln är att säkra åtkomst till ditt nyckelvalv, visar vi bara de relevanta delarna för det här avsnittet och hoppar över detaljer om distribution av certifikat och programmässig åtkomst till nycklar och certifikat. De uppgifterna täcks av andra avsnitt. Distribuera certifikat som lagras i nyckelvalvet till VM:ar täcks i ett [blogginlägg](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) och det finns tillgänglig [exempelkod](https://www.microsoft.com/download/details.aspx?id=45343) som illustrerar hur du använder bootstrap-certifikat för att autentisera till Azure AD för att få åtkomst till nyckelvalvet.

De flesta åtkomstbehörigheterna kan tilldelas via Azure Portal, men för att tilldela detaljerade behörigheter kan du behöva använda Azure PowerShell (eller Azure CLI) för att uppnå det önskade resultatet. 

Följande PowerShell-kodavsnitt förutsätter:

* Att Azure Active Directory-administratören har skapat säkerhetsgrupper som representerar tre roller, nämligen Contoso Security Team, Contoso App Devops, Contoso Auditors. Administratören har också har lagt till användare i de grupper som de tillhör.
* **ContosoAppRG** är resursgruppen där alla resurser finns. **contosologstorage** är där loggfilerna lagras. 
* Nyckelvalvet **ContosoKeyVault** och lagringskontot som används för nyckelvalvsloggar **contosologstorage** måste vara på samma plats i Azure

Först tilldelar prenumerationsadministratören rollerna nyckelvalvsbidragare och administratör för användaråtkomst till säkerhetsteamet. På så vis kan säkerhetsteamet hantera åtkomst till andra resurser och hantera nyckelvalv i resursgruppen ContosoAppRG.

```
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "key vault Contributor" -ResourceGroupName ContosoAppRG
New-AzureRmRoleAssignment -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -RoleDefinitionName "User Access Administrator" -ResourceGroupName ContosoAppRG
```

Följande skript illustrerar hur säkerhetsteam kan skapa ett nyckelvalv, ställa in loggning och ställa in åtkomstbehörigheter för andra roller och programmet. 

```
# Create key vault and enable logging
$sa = Get-AzureRmStorageAccount -ResourceGroup ContosoAppRG -Name contosologstorage
$kv = New-AzureRmKeyVault -VaultName ContosoKeyVault -ResourceGroup ContosoAppRG -SKU premium -Location 'westus' -EnabledForDeployment
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent

# Data plane permissions for Security team
Set-AzureRmKeyVaultAccessPolicy -VaultName ContosoKeyVault -ObjectId (Get-AzureRmADGroup -SearchString 'Contoso Security Team')[0].Id -PermissionsToKeys backup,create,delete,get,import,list,restore -PermissionsToSecrets all

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

Den definierade anpassade rollen är bara tilldelningsbar till prenumerationen där ContosoAppRG-resursgruppen har skapats. Om samma anpassade roller ska användas för andra projekt i andra prenumerationer, kan dess omfattning ha flera prenumerationer tillagda.

Den anpassade rolltilldelningen för utvecklare/operatörer för behörigheten "distribuera/åtgärd", begränsas till resursgruppen. På så sätt är det bara de VM:ar som skapas i resursgruppen ContosoAppRG som får hemligheterna (SSL-certifikat och bootstrap-certifikat). Alla VM:ar som en medlem i dev/ops-teamet skapar i andra resursgrupper, kommer inte att kunna hämta de här hemligheterna även om de känner till de hemliga URI:erna.

Det här exemplet visar ett enkelt scenario. Scenarier i verkliga livet är vanligtvis mer komplicerade och du kan behöva justera behörigheterna till ditt nyckelvalv baserat på dina behov. I vårt exempel antar vi till exempel att säkerhetsteamet kommer att ge ut de nyckel- och hemliga referenser (URI:er och tumavtryck) som utvecklarna/operatörerna behöver referera i sina program. Därmed behöver de inte ge utvecklarna/operatörna någon dataplansåtkomst. Tänk också på att det här exemplet fokuserar på att skydda ditt nyckelvalv. Liknande överväganden bör göras när det gäller att säkra [dina VM:ar](https://azure.microsoft.com/services/virtual-machines/security/), [lagringskonton](../storage/common/storage-security-guide.md) och andra Azure-resurser.

> [!NOTE]
> Obs: Det här exemplet visar hur åtkomst till nyckelvalvet låses i produktionen. Utvecklarna bör ha sina egna prenumerationer eller resursgrupper där de har fullständiga behörigheter att hantera sina valv, VM:ar och lagringskonton där de utvecklar programmet.
> 
> 

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
  
  Det här är en länk till en video på Channel 9 från 2015 MS Ignite-konferensen. I den här sessionen talar de om åtkomsthantering och rapporteringsfunktioner i Azure och utforskar bästa praxis när det gäller att säkra åtkomst till Azure-prenumerationer med Azure Active Directory.
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
För en komma igång-självstudie för administratörer, kan du se [Kom igång med Azure Key Vault](key-vault-get-started.md).

Mer information om av användningsloggning för Key Vault finns i avsnittet om [Azure Key Vault-loggning](key-vault-logging.md).

Mer information om hur du använder nycklar och hemligheter med Azure Key Vault finns i [Om nycklar och hemligheter](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Om du har några frågor om Nyckelvalv ska du gå till [Azure Key Vault-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault)

