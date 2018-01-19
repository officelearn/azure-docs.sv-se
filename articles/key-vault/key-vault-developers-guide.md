---
title: "Azure Key Vault-Guide för utvecklare"
description: "Utvecklare kan använda Azure Key Vault för att hantera krypteringsnycklar i Microsoft Azure-miljön."
services: key-vault
author: lleonard-msft
manager: mbaldwin
ms.service: key-vault
ms.topic: article
ms.workload: identity
ms.date: 10/12/2017
ms.author: alleonar
ms.openlocfilehash: 68a08f1f6e8036b9ca966e8d9596c09f2b2ccf81
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2018
---
# <a name="azure-key-vault-developers-guide"></a>Azure Key Vault-Guide för utvecklare

Key Vault kan du på ett säkert sätt komma åt känslig information från ditt program:

- Nycklar och hemligheter skyddas utan att behöva skriva koden själv och kan enkelt du använda dem från dina program.
- Du kan ha dina egna kunder och hantera sina egna nycklar så att du kan koncentrera dig på att erbjuda grundläggande funktioner. På så sätt kommer dina program inte äger ansvar eller potentiella ansvar för dina kunders klientnycklar och hemligheter.
- Programmet kan använda nycklar för signering och kryptering ännu fortsätter nyckelhantering externa från ditt program så att din lösning för att användas som en geografiskt distribuerade app.
- Från och med September 2016-versionen av Key Vault kan ditt program nu använda Nyckelvalv [certifikat](https://docs.microsoft.com/rest/api/keyvault/certificate-operations). Mer information finns i [om nycklar och hemligheter certifikat](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates).

Mer allmän information om Azure Key Vault finns [vad är Key Vault](key-vault-whatis.md).

## <a name="public-previews"></a>Offentliga förhandsgranskningar

Med jämna mellanrum, släpp vi en förhandsversion av en ny funktion i Nyckelvalvet. Testa de och berätta för oss vad du tycker azurekeyvault@microsoft.com, våra feedback e-postadress.

### <a name="storage-account-keys---july-10-2017"></a>Lagringskontonycklar - 10 juli 2017

>[!NOTE]
>För den här uppdateringen av Azure Key Vault den **Lagringskontonycklar** funktionen är i förhandsgranskningen.

Den här förhandsgranskningen innehåller vår nya Lagringskontonycklar funktion, tillgängliga via dessa gränssnitt; [.NET / C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault/), [REST](https://docs.microsoft.com/rest/api/keyvault/) och [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/). 

Mer information om funktionen Lagringskontonycklar finns [översikt över Azure Key Vault storage-konto nycklar](key-vault-ovw-storage-keys.md).

## <a name="videos"></a>Videoklipp

Den här videon visar hur du skapar egna nyckelvalvet och hur du använder den från 'Hello Key Vault-exempelprogram.

- [Key Vault-utvecklare - Snabbstartsguide](https://channel9.msdn.com/Blogs/Azure/Azure-Key-Vault-Developer-Quick-Start/player)

Resurser i ovannämnda video:

- [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Azure Key Vault exempelkod](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

## <a name="creating-and-managing-key-vaults"></a>Skapa och hantera Nyckelvalv

Azure Key Vault är ett sätt att lagra autentiseringsuppgifter och andra nycklar och hemligheter, men din kod måste autentisera till Key Vault de. Hanterad Service identitet (MSI) gör att lösa problemet enklare genom att ge en automatiskt hanterade identitet i Azure-tjänster i Azure Active Directory (AD Azure). Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault utan några autentiseringsuppgifter i koden. 

Mer information om MSI finns [hanteras Service identitet (MSI) för Azure-resurser](https://docs.microsoft.com/azure/active-directory/msi-overview).

Läs mer om att arbeta med AAD [integrera program med Azure Active Directory](/active-directory/develop/active-directory-integrating-applications).

Innan du börjar arbeta med nycklar, hemligheter eller certifikat i nyckelvalvet ska du skapa och hantera nyckelvalvet via CLI, PowerShell, Resource Manager-mallar eller övriga, enligt beskrivningen i följande artiklar:

- [Skapa och hantera nyckeln valv med CLI](key-vault-manage-with-cli2.md)
- [Skapa och hantera nyckeln valv med PowerShell](key-vault-get-started.md)
- [Skapa en nyckelvalvet och lägga till en hemlighet via en Azure Resource Manager-mall](../azure-resource-manager/resource-manager-template-keyvault.md)
- [Skapa och hantera nyckeln valv med övriga](https://docs.microsoft.com/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Med Key Vault-kodning

Key Vault hanteringssystemet för programmerare består av flera gränssnitt. Det här avsnittet innehåller länkar till alla språk som vissa koden exampls. 

### <a name="supported-programming-and-scripting-languages"></a>Programmering och skriptspråk som stöds

#### <a name="rest"></a>REST

Alla Key Vault-resurser är tillgängliga via REST-gränssnittet; valv, nycklar, hemligheter, osv. 

[Nyckeln valvet REST API-referens](https://docs.microsoft.com/rest/api/keyvault/). 

#### <a name="net"></a>.NET

[.NET API refence för Key Vault](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault) 

Mer information om 2.x-versionen av .NET SDK finns på [viktig information](key-vault-dotnet2api-release-notes.md).

#### <a name="java"></a>Java

[Java SDK för Key Vault](https://docs.microsoft.com/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

I Node.js är Key Vault management-API och objektet Key Vault API separat. I följande översiktsartikel ger dig tillgång till båda. 

[Azure Key Vault-moduler för Node.js](https://docs.microsoft.com/nodejs/api/overview/azure/key-vault)

#### <a name="python"></a>Python

[Azure Key Vault-bibliotek för Python](https://docs.microsoft.com/python/api/overview/azure/key-vault)

#### <a name="azure-cli-2"></a>Azure CLI 2

[Azure CLI för Key Vault](https://docs.microsoft.com/cli/azure/keyvault)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Azure PowerShell för Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault)

### <a name="quick-start-guides"></a>Snabbstartsguide

- [Skapa Nyckelvalvet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)
- [Komma igång med Key Vault i Node.js](https://azure.microsoft.com/en-us/resources/samples/key-vault-node-getting-started/)

### <a name="code-examples"></a>Kodexempel

Fullständig exempel med dina program Key Vault finns:

- [Azure Key Vault-kodexempel](http://www.microsoft.com/download/details.aspx?id=45343) -.NET exempelprogrammet *HelloKeyVault* och ett Azure web service-exempel. 
- [Använda Azure Key Vault från ett webbprogram](key-vault-use-from-web-application.md) -självstudier för att lära dig hur du använder Azure Key Vault från ett webbprogram i Azure. 

## <a name="how-tos"></a>Instruktioner

I följande artiklar och scenarier ge uppgiftsspecifika vägledning för att arbeta med Azure Key Vault:

- [Ändra nyckelvalv klient-ID när prenumerationen flytta](key-vault-subscription-move-fix.md) – när du flyttar din Azure-prenumeration från klient A till klient B, din befintliga nyckelvalv inte tillgängliga som säkerhetsobjekt (användare och program) i klient B. Korrigera detta med hjälp av den här guiden.
- [Åtkomst till Nyckelvalvet bakom brandväggen](key-vault-access-behind-firewall.md) - åtkomst till ett nyckelvalv som ditt nyckelvalv klientprogram måste kunna få åtkomst till flera slutpunkter för olika funktioner.
- [Generera och Transfer HSM-Protected nycklar för Azure Key Vault](key-vault-hsm-protected-keys.md) -detta hjälper dig att planera för, generera och överföra din egen HSM-skyddade nycklar som ska användas med Azure Key Vault.
- [Hur att skicka säkra värden (till exempel lösenord) under distributionen](../azure-resource-manager/resource-manager-keyvault-parameter.md) – när du behöver för att skicka ett säkert värde (till exempel ett lösenord) som en parameter under distributionen kan du lagra värdet som en hemlighet i en Azure Key Vault och refererar till värdet i andra resurser Manager-mallar.
- [Hur du använder Key Vault för hantering av utökningsbar nyckel med SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) -av SQL Server-anslutningen för Azure Key Vault kan SQL Server och SQL i en VM dra nytta av Azure Key Vault-tjänsten som en Extensible Key Management (EKM)-provider för att skydda dess krypteringsnycklar för program länk; Transparent datakryptering säkerhetskopiering kryptering och kryptering på kolumnen.
- [Distribuera certifikat till virtuella datorer från Nyckelvalvet](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) - ett moln-program som körs på en virtuell dator på Azure måste ett certifikat. Hur du får det här certifikatet i den här virtuella datorn idag?
- [Hur du ställer in Key Vault slutpunkt till slutpunkt viktiga rotation och granskning](key-vault-key-rotation-log-monitoring.md) – denna går igenom hur du ställer in viktiga rotation och granskning med Azure Key Vault.
- [Distribuera Azure-certifikat för webbprogram via Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) innehåller stegvisa instruktioner för att distribuera certifikat som lagras i Nyckelvalvet som en del av [App tjänstcertifikat](https://azure.microsoft.com/blog/internals-of-app-service-certificate/) erbjudande.
- [Bevilja behörighet till många program för att komma åt ett nyckelvalv](key-vault-group-permissions-for-apps.md) Key Vault principer för åtkomstkontroll stöder endast 16 poster. Du kan dock skapa en Azure Active Directory-säkerhetsgrupp. Lägg till alla associerade tjänster säkerhetsobjekt i den här säkerhetsgruppen och sedan ge åtkomst till den här säkerhetsgruppen att Nyckelvalvet.
- Flera uppgiftsspecifika vägledning för att integrera och med Azure Key valv finns [Ryan Jones Azure Resource Manager-mall exempel för Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- [Hur du använder Key Vault mjuk borttagning med CLI](key-vault-soft-delete-cli.md) vägleder dig genom användning och livscykeln för ett nyckelvalv och olika nyckelvalv objekt med aktiverad mjuk borttagning.
- [Hur du använder Key Vault mjuk borttagning med PowerShell](key-vault-soft-delete-powershell.md) vägleder dig genom användning och livscykeln för ett nyckelvalv och olika nyckelvalv objekt med aktiverad mjuk borttagning.

## <a name="integrated-with-key-vault"></a>Integrerad med Key Vault

Dessa artiklar som handlar om andra scenarier och tjänster som använder eller integrera med Key Vault.

- [Azure Disk Encryption](../security/azure-security-disk-encryption.md) utnyttjar branschstandarden [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) funktion i Windows och [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) funktion i Linux att tillhandahålla volymkryptering för Operativsystemet och datadiskar. Lösningen är integrerad med Azure Key Vault som hjälper dig att styra och hantera disk krypteringsnycklar och hemligheter i prenumerationen nyckelvalv samtidigt som du säkerställer att krypteras alla data i virtuella diskar i vila i ditt Azure storage.
- [Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md) innehåller alternativ för kryptering av data som lagras på kontot. Data Lake Store ger två lägen för hantering, av nycklar för att hantera master krypteringsnycklarna (MEKs) som krävs för att dekryptera data som lagras i Data Lake Store. Du kan antingen låta Data Lake Store hantera MEKs för dig, eller välja att behålla ägarskap för MEKs med Azure Key Vault-konto. Du kan ange läget för hantering av nycklar när du skapar ett Data Lake Store-konto. 
- [Azure Information Protection](/information-protection/plan-design/plan-implement-tenant-key) kan du konfigurera din egen klientnyckel. I stället för Microsoft hanterar din klientnyckel (standard), till exempel kan du hantera din egen klientnyckel för att uppfylla specifika föreskrifter som gäller för din organisation. Hantera din egen klientnyckel kallas även för att ta med din egen nyckel eller BYOK.

## <a name="key-vault-overviews-and-concepts"></a>Key Vault översikter och begrepp

- [Key Vault mjuk borttagning beteende](key-vault-ovw-soft-delete.md) beskrivs en funktion som gör att återställning av borttagna objekt om borttagningen har oavsiktligt eller avsiktligt.
- [Key Vault klienten begränsning](key-vault-ovw-throttling.md) beskriver de grundläggande principerna för begränsning av och erbjuder en metod för din app.
- [Översikt över Key Vault storage-konto nycklar](key-vault-ovw-storage-keys.md) beskriver tangenterna Key Vault-integrering Azure Storage-konton.
- [Key Vault säkerhetsvärldar](key-vault-ovw-security-worlds.md) beskrivningar av relationerna mellan regioner och säkerhetsområden.

## <a name="social"></a>Sociala medier

- [Key Vault-blogg](http://aka.ms/kvblog)
- [Key Vault-Forum](http://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Andra bibliotek

- [Microsoft Azure Key Vault Core Library](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) ger **IKey** och **IKeyResolver** gränssnitt för att hitta nycklar från identifierare och utföra åtgärder med nycklar.
- [Tillägg för Microsoft Azure Key Vault](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) ger utökade funktioner för Azure Key Vault.


