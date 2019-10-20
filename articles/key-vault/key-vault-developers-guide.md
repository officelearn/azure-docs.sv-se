---
title: Guide för Azure Key Vault utvecklare
description: Utvecklare kan använda Azure Key Vault för att hantera kryptografiska nycklar i Microsoft Azures miljön.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: mbaldwin
ms.openlocfilehash: 1bfd156f9fbdb69766e4588a342a695fbd79ca3a
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/18/2019
ms.locfileid: "72595303"
---
# <a name="azure-key-vault-developers-guide"></a>Guide för Azure Key Vault utvecklare

Med Key Vault kan du på ett säkert sätt komma åt känslig information i dina program:

- Nycklar och hemligheter skyddas utan att du behöver skriva koden själv och du kan enkelt använda dem från dina program.
- Du kan låta dina kunder äga och hantera sina egna nycklar så att du kan koncentrera dig på att tillhandahålla viktiga program varu funktioner. På så sätt kommer dina program inte att äga ansvar eller potentiella ansvar för kundernas klient nycklar och hemligheter.
- Ditt program kan använda nycklar för signering och kryptering, men behåller nyckel hanteringen externt från ditt program, vilket gör att din lösning kan användas som en geografiskt distribuerad app.
- Från och med september 2016-versionen av Key Vault kan dina program nu hantera Key Vault certifikat. Mer information finns i [om nycklar, hemligheter och certifikat](/rest/api/keyvault/about-keys--secrets-and-certificates).

Mer allmän information om Azure Key Vault finns i [Vad är Key Vault](key-vault-overview.md).

## <a name="public-previews"></a>Offentliga för hands versionerna

Med jämna mellanrum släpper vi en offentlig för hands version av en ny Key Vault funktion. Prova dessa och berätta för oss vad du tycker via azurekeyvault@microsoft.com, vår e-postadress för feedback.

## <a name="creating-and-managing-key-vaults"></a>Skapa och hantera nyckel valv

Azure Key Vault är ett sätt att lagra autentiseringsuppgifter samt andra nycklar och hemligheter på ett säkert sätt, men din kod måste autentiseras till Key Vault för att kunna hämta dem. Hanterade identiteter för Azure-resurser gör det enklare att lösa det här problemet genom att ge Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan att behöva ha några autentiseringsuppgifter i koden. 

Mer information om hanterade identiteter för Azure-resurser finns i [Översikt över hanterade identiteter](../active-directory/managed-identities-azure-resources/overview.md). Mer information om hur du arbetar med AAD finns i [integrera program med Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md).

Innan du börjar arbeta med nycklar, hemligheter eller certifikat i ditt nyckel valv, skapar du och hanterar ditt nyckel valv genom CLI, PowerShell, Resource Manager-mallar eller REST, enligt beskrivningen i följande artiklar:

- [Skapa och hantera nyckel valv med CLI](key-vault-manage-with-cli2.md)
- [Skapa och hantera nyckel valv med PowerShell](key-vault-overview.md)
- [Skapa ett nyckel valv och Lägg till en hemlighet via en Azure Resource Manager mall](../azure-resource-manager/resource-manager-template-keyvault.md)
- [Skapa och hantera nyckel valv med REST](/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Koda med Key Vault

Key Vault hanterings system för programmerare består av flera gränssnitt. Det här avsnittet innehåller länkar till alla språk samt kod exempel. 

### <a name="supported-programming-and-scripting-languages"></a>Programmerings-och skript språk som stöds

#### <a name="rest"></a>REST

Alla Key Vault resurser är tillgängliga via REST-gränssnittet. valv, nycklar, hemligheter osv. 

[Referens för Key Vault REST API](/rest/api/keyvault/).

#### <a name="net"></a>.NET

[.NET API-referens för Key Vault](/dotnet/api/microsoft.azure.keyvault).

Mer information om 2. x-versionen av .NET SDK finns i [viktig](key-vault-dotnet2api-release-notes.md)information.

#### <a name="java"></a>Java

[Java SDK för Key Vault](/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

I Node. js är API för Key Vault hantering och Key Vault objekt-API separata. I följande översikts artikel får du till gång till båda. 

[Azure Key Vault moduler för Node. js](/nodejs/api/overview/azure/key-vault)

#### <a name="python"></a>Python

[Azure Key Vault bibliotek för python](/python/api/overview/azure/key-vault)

#### <a name="azure-cli-2"></a>Azure CLI 2

[Azure CLI för Key Vault](/cli/azure/keyvault)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Azure PowerShell för Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault)

### <a name="quickstart-guides"></a>Snabb starts guider

- [Skapa Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
- [Komma igång med Key Vault i Node. js](https://github.com/Azure-Samples/key-vault-node-getting-started)

### <a name="code-examples"></a>Kod exempel

Fullständiga exempel som använder Key Vault med dina program finns i:

- [Azure Key Vault kod exempel](https://azure.microsoft.com/resources/samples/?service=key-vault) – kod exempel för Azure Key Vault. 
- [Använd Azure Key Vault från ett webb program](quick-create-net.md) – självstudier som hjälper dig att lära dig hur du använder Azure Key Vault från ett webb program i Azure. 

## <a name="how-tos"></a>Instruktioner

Följande artiklar och scenarier innehåller en detaljerad vägledning för att arbeta med Azure Key Vault:

- [Ändra nyckel valvets klient-ID efter prenumerations flytt](key-vault-subscription-move-fix.md) – när du flyttar din Azure-prenumeration från klient A till klient b är dina befintliga nyckel valv otillgängliga för huvud kontona (användare och program) i klient b. åtgärda detta med hjälp av den här guiden.
- [Åtkomst till Key Vault bakom brand väggen](key-vault-access-behind-firewall.md) – för att få åtkomst till ett nyckel valv måste klient programmet för nyckel valv kunna komma åt flera slut punkter för olika funktioner.
- [Så här genererar och överför du HSM-skyddade nycklar för Azure Key Vault](key-vault-hsm-protected-keys.md) – det hjälper dig att planera för, generera och överföra dina egna HSM-skyddade nycklar som du kan använda med Azure Key Vault.
- [Så här skickar du säkra värden (till exempel lösen ord) under distributionen](../azure-resource-manager/resource-manager-keyvault-parameter.md) – när du behöver skicka ett säkert värde (till exempel ett lösen ord) som en parameter under distributionen kan du lagra värdet som en hemlighet i en Azure Key Vault och referera till värdet i annan Resource Manager onlinemallar.
- [Använda Key Vault för utöknings bar nyckel hantering med SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) -SQL Server-anslutning för Azure Key Vault aktiverar SQL Server och SQL-in-a-VM för att utnyttja Azure Key Vault-tjänsten som en EKM-Provider (Extensible Key Management) för att skydda dess krypterings nycklar för program länk; Transparent datakryptering, kryptering av säkerhets kopior och kryptering på kolumn nivå.
- [Hur du distribuerar certifikat till virtuella datorer från Key Vault](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) – ett moln program som körs på en virtuell dator i Azure behöver ett certifikat. Hur får du det här certifikatet till den virtuella datorn idag?
- [Så här konfigurerar du Key Vault med slut punkt till slut punkt för nyckel rotation och granskning](key-vault-key-rotation-log-monitoring.md) – det här hjälper dig att konfigurera nyckel rotation och granskning med Azure Key Vault.
- [Distribuera Azure Web App-certifikat via Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) innehåller steg-för-steg-instruktioner för distribution av certifikat som lagras i Key Vault som en del av [App Service Certificate](https://azure.microsoft.com/blog/internals-of-app-service-certificate/) erbjudandet.
- [Bevilja behörighet till många program för att komma åt ett nyckel valv](key-vault-group-permissions-for-apps.md) Key Vault åtkomst kontroll principen stöder upp till 1024 poster. Du kan dock skapa en Azure Active Directory säkerhets grupp. Lägg till alla associerade tjänst huvud namn i säkerhets gruppen och ge sedan åtkomst till den här säkerhets gruppen till Key Vault.
- Mer detaljerad information om hur du integrerar och använder nyckel valv med Azure finns i [Ryan Jones "Azure Resource Manager Template exempel for Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- [Använda Key Vault mjuk borttagning med CLI](key-vault-soft-delete-cli.md) guidar dig genom användningen och livs cykeln för ett nyckel valv och olika Key Vault-objekt med mjuk borttagning aktiverat.
- [Använda Key Vault mjuk borttagning med PowerShell](key-vault-soft-delete-powershell.md) vägleder dig genom användningen och livs cykeln för ett nyckel valv och olika Key Vault-objekt med mjuk borttagning aktiverat.

## <a name="integrated-with-key-vault"></a>Integrerad med Key Vault

De här artiklarna är till för andra scenarier och tjänster som använder eller integrerar med Key Vault.

- [Azure Disk Encryption](../security/fundamentals/encryption-overview.md) utnyttjar standarden för [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) -funktionen i Windows och funktionen [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) i Linux för att tillhandahålla volym kryptering för operativ systemet och data diskarna. Lösningen är integrerad med Azure Key Vault som hjälper dig att styra och hantera disk krypterings nycklar och hemligheter i Key Vault-prenumerationen, samtidigt som du ser till att alla data på de virtuella dator diskarna krypteras i vila i Azure Storage.
- [Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) tillhandahåller alternativ för kryptering av data som lagras i kontot. För nyckel hantering har Data Lake Store två lägen för att hantera dina huvud krypterings nycklar (MEKs), som krävs för dekryptering av data som lagras i Data Lake Store. Du kan antingen låta Data Lake Store hantera MEKs åt dig eller välja att behålla ägarskapet för MEKs med ditt Azure Key Vault-konto. Du anger läget för nyckel hantering när du skapar ett Data Lake Store-konto.
- Med [Azure information Protection](/azure/information-protection/plan-implement-tenant-key) kan du hantera din egen klient nyckel. I stället för att Microsoft hanterar din klient nyckel (standard) kan du till exempel hantera din egen klient nyckel för att följa särskilda regler som gäller för din organisation. Att hantera din egen klient nyckel kallas även för att ta med din egen nyckel eller BYOK.

## <a name="key-vault-overviews-and-concepts"></a>Key Vault översikter och begrepp

- [Key Vault beteende för mjuk borttagning](key-vault-ovw-soft-delete.md) beskriver en funktion som gör det möjligt att återställa borttagna objekt, om borttagningen var oavsiktligt eller avsiktligt.
- [Key Vault klient begränsningen](key-vault-ovw-throttling.md) orienterar dig till de grundläggande begreppen för begränsning och erbjuder en metod för din app.
- [Översikt över Key Vault lagrings konto nycklar](key-vault-ovw-storage-keys.md) beskriver konto nycklar för Key Vault-integrering Azure Storage konton.
- [Key Vault säkerhets världar](key-vault-ovw-security-worlds.md) beskriver relationerna mellan regioner och säkerhets områden.

## <a name="social"></a>Socialt

- [Key Vault blogg](https://aka.ms/kvblog)
- [Key Vault forum](https://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Stöd bibliotek

- [Microsoft Azure Key Vault kärn biblioteket](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) innehåller **IKey** -och **IKeyResolver** -gränssnitt för att hitta nycklar från identifierare och utföra åtgärder med nycklar.
- [Microsoft Azure Key Vault tillägg](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) tillhandahåller utökade funktioner för Azure Key Vault.
