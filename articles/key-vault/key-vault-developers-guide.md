---
title: Utvecklarguide för Azure Key Vault
description: Utvecklare kan använda Azure Key Vault för att hantera kryptografiska nycklar i Microsoft Azure-miljön.
services: key-vault
author: bryanla
manager: mbaldwin
ms.service: key-vault
ms.topic: conceptual
ms.workload: identity
ms.date: 09/05/2018
ms.author: bryanla
ms.openlocfilehash: 9ab399ae50f9b04acad411b225797f89547a2824
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51237544"
---
# <a name="azure-key-vault-developers-guide"></a>Utvecklarguide för Azure Key Vault

Key Vault kan du få säker åtkomst till känslig information i dina program:

- Nycklar och hemligheter skyddas utan att behöva skriva koden själv och du kan enkelt använda dem från dina program.
- Du kan ha dina egna kunder och hantera sina egna nycklar så att du kan koncentrera dig på att erbjuda grundläggande funktioner. På så sätt kommer dina program inte äger det ansvaret för dina kunders klientnycklar och hemligheter.
- Ditt program kan använda nycklar för signering och kryptering än håller nyckelhantering som externa från ditt program, vilket gör att din lösning för att passa som geografiskt distribuerade app.
- Från och med September 2016-versionen av Key Vault kan dina program nu hantera Key Vault-certifikat. Mer information finns i [om nycklar, hemligheter och certifikat](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates).

Mer allmän information om Azure Key Vault finns i [vad är Key Vault](key-vault-whatis.md).

## <a name="public-previews"></a>Offentliga förhandsversioner

Vi släpper regelbundet, en offentlig förhandsversion av en ny funktion för Key Vault. Testa de och berätta vad du tycker azurekeyvault@microsoft.com, vårt feedback e-postadress.

### <a name="storage-account-keys---july-10-2017"></a>Lagringskontonycklar - 10 juli 2017

>[!NOTE]
>För den här uppdateringen av Azure Key Vault den **Lagringskontonycklar** funktionen är en förhandsversion.

Den här förhandsversionen innehåller vår nya Lagringskontonycklar funktion tillgänglig via dessa gränssnitt; [.NET / C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault/), [REST](https://docs.microsoft.com/rest/api/keyvault/) och [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/). 

Läs mer på den nya funktionen för Lagringskontonycklar [nycklar för Azure Key Vault-lagringskontoöversikten](key-vault-ovw-storage-keys.md).

## <a name="videos"></a>Videoklipp

Den här videon visar hur du skapar ditt eget nyckelvalv och hur du använder den från ”Hello Key Vault-exempelprogrammet.

- [Key Vault-utvecklare – Snabbstartsguide](https://channel9.msdn.com/Blogs/Azure/Azure-Key-Vault-Developer-Quick-Start/player)

Resurser som ingår i ovan video:

- [Azure PowerShell](https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Azure Key Vault-exempelkod](https://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

## <a name="creating-and-managing-key-vaults"></a>Skapa och hantera Nyckelvalv

Azure Key Vault är ett sätt att lagra autentiseringsuppgifter samt andra nycklar och hemligheter på ett säkert sätt, men din kod måste autentiseras till Key Vault för att kunna hämta dem. Hanterade identiteter för Azure-resurser kan lösa problemet enklare genom att ge Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory (AD Azure). Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan att behöva ha några autentiseringsuppgifter i koden. 

Mer information om hanterade identiteter för Azure-resurser finns i [hanterade identiteter översikten](../active-directory/managed-identities-azure-resources/overview.md). Läs mer om hur du arbetar med AAD, [integrera program med Azure Active Directory](/azure/active-directory/develop/active-directory-integrating-applications).

Innan du börjar arbeta med nycklar, hemligheter eller certifikat i ditt nyckelvalv ska du skapa och hantera ditt nyckelvalv via CLI, PowerShell, Resource Manager-mallar eller REST, enligt beskrivningen i följande artiklar:

- [Skapa och hantera Nyckelvalv med CLI](key-vault-manage-with-cli2.md)
- [Skapa och hantera Nyckelvalv med PowerShell](key-vault-get-started.md)
- [Skapa ett nyckelvalv och Lägg till en hemlighet via en Azure Resource Manager-mall](../azure-resource-manager/resource-manager-template-keyvault.md)
- [Skapa och hantera Nyckelvalv med REST](https://docs.microsoft.com/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Kodning med Key Vault

Key Vault-hanteringssystem för programmerare består av flera gränssnitt. Det här avsnittet innehåller länkar till alla språk samt vissa kod exampls. 

### <a name="supported-programming-and-scripting-languages"></a>Programmera och skriptspråk som stöds

#### <a name="rest"></a>REST

Alla dina Key Vault-resurser är tillgängliga via REST-gränssnittet, valv, nycklar, hemligheter, osv. 

[Nyckeln Vault REST API-referens](https://docs.microsoft.com/rest/api/keyvault/). 

#### <a name="net"></a>.NET

[.NET API refence för Key Vault](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault) 

Mer information om den 2.x-versionen av .NET SDK finns i den [viktig](key-vault-dotnet2api-release-notes.md).

#### <a name="java"></a>Java

[Java SDK för Key Vault](https://docs.microsoft.com/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

I Node.js är management-API Key Vault och Key Vault-objektet API separat. I följande översiktsartikel ger dig åtkomst till båda. 

[Azure Key Vault-moduler för Node.js](https://docs.microsoft.com/nodejs/api/overview/azure/key-vault)

#### <a name="python"></a>Python

[Azure Key Vault-bibliotek för Python](https://docs.microsoft.com/python/api/overview/azure/key-vault)

#### <a name="azure-cli-2"></a>Azure CLI 2

[Azure CLI för Key Vault](https://docs.microsoft.com/cli/azure/keyvault)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Azure PowerShell för Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault)

### <a name="quick-start-guides"></a>Snabbstartsguide

- [Skapa Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
- [Komma igång med Key Vault i Node.js](https://github.com/Azure-Samples/key-vault-node-getting-started)

### <a name="code-examples"></a>Kodexempel

För fullständiga exempel med Key Vault med dina program, se:

- [Azure Key Vault-kodexempel](https://azure.microsoft.com/resources/samples/?service=key-vault) -kodexempel för Azure Key Vault. 
- [Använda Azure Key Vault från ett webbprogram](quick-create-net.md) -självstudiekursen för att lära dig hur du använder Azure Key Vault från ett webbprogram i Azure. 

## <a name="how-tos"></a>Instruktioner

Följande artiklar och scenarier tillhandahåller uppgiftsspecifika vägledning för att arbeta med Azure Key Vault:

- [Ändra nyckelvalvsklient-ID efter flytta](key-vault-subscription-move-fix.md) – när du flyttar din Azure-prenumeration från klient A till klient B, ditt befintliga nyckelvalv är otillgängligt för säkerhetsobjekten (användare och program) i klient B. åtgärda det här med den här guiden.
- [Åtkomst till Nyckelvalv bakom brandvägg](key-vault-access-behind-firewall.md) – om du vill komma åt ett nyckelvalv som ditt nyckelvalv-klientprogram behöver åtkomst till flera slutpunkter för olika funktioner.
- [Så här genererar och Transfer HSM-Protected nycklar för Azure Key Vault](key-vault-hsm-protected-keys.md) – det här hjälper dig att planera för, generera och överföra din egen HSM-skyddade nycklar som ska användas med Azure Key Vault.
- [Så här att skicka säkra värden (till exempel lösenord) under distributionen](../azure-resource-manager/resource-manager-keyvault-parameter.md) – när du behöver skicka ett säkert värde (t.ex. ett lösenord) som en parameter under distributionen, du kan lagra värdet som en hemlighet i ett Azure Key Vault och referera till värdet i en annan resurs Manager-mallar.
- [Hur du använder Key Vault för utökningsbar nyckelhantering med SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) – The SQL Server-anslutningen för Azure Key Vault kan SQL Server och SQL i en VM för att dra nytta av Azure Key Vault-tjänsten som en Extensible Key Management (EKM)-provider för att skydda dess krypteringsnycklar för program länk; Transparent datakryptering, kryptering av säkerhetskopiering och kryptering på kolumnen.
- [Distribuera certifikat till virtuella datorer från Key Vault](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) – ett molnprogram som körs på en virtuell dator på Azure-behov ett certifikat. Hur du får det här certifikatet i den här virtuella datorn i dag?
- [Hur du ställer in Key Vault med heltäckande nyckelrotation och granskning](key-vault-key-rotation-log-monitoring.md) – det här visar vi hur du ställer in nyckelrotation och granskning med Azure Key Vault.
- [Distribuera Azure Web App-certifikat via Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) innehåller stegvisa instruktioner för distribution av certifikat som lagras i Key Vault som en del av [App Service Certificate](https://azure.microsoft.com/blog/internals-of-app-service-certificate/) erbjuder.
- [Bevilja behörighet till många program för att komma åt ett nyckelvalv](key-vault-group-permissions-for-apps.md) kontrollprinciper för Key Vault har stöd för upp till 1024 poster. Du kan dock skapa en Azure Active Directory-säkerhetsgrupp. Lägg till alla associerade huvudnamn i den här säkerhetsgruppen och bevilja åtkomst till den här säkerhetsgruppen till Key Vault.
- För att integrera och använda Nyckelvalv med Azure mer uppgiftsspecifika vägledning finns i [Ryan Jones Azure Resource Manager-mallexempel för Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- [Hur du använder Key Vault mjuk borttagning med CLI](key-vault-soft-delete-cli.md) vägleder dig genom användning och livscykeln för ett nyckelvalv och olika nyckelvalvobjekt med aktivera mjuk borttagning.
- [Hur du använder Key Vault mjuk borttagning med PowerShell](key-vault-soft-delete-powershell.md) vägleder dig genom användning och livscykeln för ett nyckelvalv och olika nyckelvalvobjekt med aktivera mjuk borttagning.

## <a name="integrated-with-key-vault"></a>Integrerad med Key Vault

Dessa artiklar handlar om andra scenarier och tjänster som använder eller integrera med Key Vault.

- [Azure Disk Encryption](../security/azure-security-disk-encryption.md) utnyttjar branschstandard [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funktion i Windows och [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funktion i Linux för att kryptera volymer för Operativsystemet och datadiskarna. Lösningen är integrerad med Azure Key Vault för att styra och hantera diskkrypteringsnycklarna och hemligheter i key vault-prenumeration, samtidigt som man säkerställer att alla data på diskar i virtuella datorer krypteras i vila i din Azure-lagring.
- [Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) erbjuder alternativ för kryptering av data som lagras i kontot. För nyckelhantering har Data Lake Store två lägen för att hantera din huvudkrypteringsnycklar (Huvudkrypteringsnycklar), som krävs för att dekryptera data som lagras i Data Lake Store. Du kan antingen låta Data Lake Store hantera Huvudkrypteringsnycklar åt dig, eller väljer att behålla ägarskapet för Huvudkrypteringsnycklar med hjälp av Azure Key Vault-kontot. Du kan ange läget för nyckelhantering när du skapar ett Data Lake Store-konto. 
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) gör det möjligt att manager din egen klientnyckel. I stället för att Microsoft hanterar din klientnyckel (standard), till exempel kan du hantera din egen klientnyckel för att uppfylla specifika krav som gäller för din organisation. Hantera din egen klientnyckel kallas också för att ta med din egen nyckel eller BYOK som.

## <a name="key-vault-overviews-and-concepts"></a>Key Vault översikter och begrepp

- [Key Vault mjuk borttagning beteende](key-vault-ovw-soft-delete.md) beskrivs en funktion som gör att återställning av borttagna objekt om borttagningen har oavsiktligt eller avsiktligt.
- [Key Vault-klienten begränsning](key-vault-ovw-throttling.md) beskriver de grundläggande principerna för begränsning av och erbjuder en metod för din app.
- [Nycklar för Key Vault-lagringskontoöversikten](key-vault-ovw-storage-keys.md) beskriver tangenterna Key Vault-integrering Azure Storage-konton.
- [Key Vault-säkerhetsvärldar](key-vault-ovw-security-worlds.md) beskrivningar av relationerna mellan regioner och säkerhetsområden.

## <a name="social"></a>Sociala medier

- [Key Vault-bloggen](https://aka.ms/kvblog)
- [Key Vault-forumet](https://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Andra bibliotek

- [Kärnbibliotek för Microsoft Azure Key Vault](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) ger **IKey** och **IKeyResolver** gränssnitt för att hitta nycklar från identifierare och utföra åtgärder med nycklar.
- [Microsoft Azure Key Vault-tillägg](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) ger utökade funktioner för Azure Key Vault.


