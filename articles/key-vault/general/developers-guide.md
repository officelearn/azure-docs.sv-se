---
title: Utvecklarguide för Azure Key Vault
description: Utvecklare kan använda Azure Key Vault för att hantera kryptografiska nycklar i Microsoft Azure-miljön.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mbaldwin
ms.openlocfilehash: 18e1ab1d01394d585cfb06ced8c5fbac04114177
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431961"
---
# <a name="azure-key-vault-developers-guide"></a>Utvecklarguide för Azure Key Vault

Med Key Vault kan du komma åt känslig information på ett säkert sätt inifrån dina program:

- Nycklar och hemligheter är skyddade utan att behöva skriva koden själv och du är lätt kunna använda dem från dina program.
- Du kan få dina kunder egna och hantera sina egna nycklar så att du kan koncentrera dig på att tillhandahålla de grundläggande mjukvarufunktionerna. På så sätt kommer dina program inte att äga ansvaret eller det potentiella ansvaret för dina kunders klientnycklar och hemligheter.
- Ditt program kan använda nycklar för signering och kryptering men ändå håller nyckelhanteringen extern från ditt program, så att din lösning kan vara lämplig som en geografiskt distribuerad app.
- Hantera Key Vault-certifikat. Mer information finns i [Certifikat](../certificates/about-certificates.md)

Mer allmän information om Azure Key Vault finns i [Vad är Nyckelvalv](overview.md)).

## <a name="public-previews"></a>Offentliga förhandsvisningar

Med jämna mellanrum släpper vi en offentlig förhandsversion av en ny Key Vault-funktion. Prova dessa och låt oss veta azurekeyvault@microsoft.comvad du tycker via , vår feedback e-postadress.

## <a name="creating-and-managing-key-vaults"></a>Skapa och hantera nyckelvalv

Azure Key Vault är ett sätt att lagra autentiseringsuppgifter samt andra nycklar och hemligheter på ett säkert sätt, men din kod måste autentiseras till Key Vault för att kunna hämta dem. Hanterade identiteter för Azure-resurser gör det enklare att lösa problemet genom att ge Azure-tjänster en automatiskt hanterad identitet i Azure Active Directory (Azure AD). Du kan använda den här identiteten för att autentisera till alla tjänster som stöder Azure AD-autentisering, inklusive Key Vault, utan att behöva ha några autentiseringsuppgifter i koden. 

Mer information om hanterade identiteter för Azure-resurser finns [i översikten över hanterade identiteter](../../active-directory/managed-identities-azure-resources/overview.md). Mer information om hur du arbetar med Azure AD finns i [Integrera program med Azure Active Directory](../../active-directory/develop/active-directory-integrating-applications.md).

Innan du arbetar med nycklar, hemligheter eller certifikat i nyckelvalvet skapar och hanterar du nyckelvalvet via CLI, PowerShell, Resource Manager-mallar eller REST, enligt beskrivningen i följande artiklar:

- [Skapa och hantera nyckelvalv med CLI](../secrets/quick-create-cli.md)
- [Skapa och hantera nyckelvalv med PowerShell](../secrets/quick-create-powershell.md)
- [Skapa och hantera nyckelvalv med Azure-porten](../secrets/quick-create-portal.md)
- [Skapa och hantera nyckelvalv med Python](../secrets/quick-create-python.md)
- [Skapa och hantera nyckelvalv med Java](../secrets/quick-create-java.md)
- [Skapa och hantera nyckelvalv med Node.js](../secrets/quick-create-node.md)
- [Skapa och hantera nyckelvalv med .NET (v4 SDK)](../secrets/quick-create-net.md)
- [Skapa ett nyckelvalv och lägg till en hemlighet via en Azure Resource Manager-mall](../secrets/quick-create-template.md)
- [Skapa och hantera nyckelvalv med REST](/rest/api/keyvault/)


## <a name="coding-with-key-vault"></a>Kodning med Nyckelvalv

Key Vault-hanteringssystemet för programmerare består av flera gränssnitt. Det här avsnittet innehåller länkar till alla språk samt några kodexempel. 

### <a name="supported-programming-and-scripting-languages"></a>Programmerings- och skriptspråk som stöds

#### <a name="rest"></a>REST

Alla dina Key Vault-resurser är tillgängliga via REST-gränssnittet. valv, nycklar, hemligheter, etc. 

[REST-referens för nyckelvalv](/rest/api/keyvault/).

#### <a name="net"></a>.NET

[.NET API-referens för Key Vault](/dotnet/api/overview/azure/key-vault?view=azure-dotnet).

Mer information om 2.x-versionen av .NET SDK finns i [viktig information](dotnet2api-release-notes.md).

#### <a name="java"></a>Java

[Java SDK för Key Vault](/java/api/overview/azure/keyvault)

#### <a name="nodejs"></a>Node.js

I Node.js är Api:et för hantering av Key Vault och Key Vault-objektets API separata. Följande översiktsartikel ger dig tillgång till båda. 

[Azure Key Vault-moduler för Node.js](/javascript/api/overview/azure/key-vault?view=azure-node-latest)

#### <a name="python"></a>Python

[Azure Key Vault-bibliotek för Python](/python/api/overview/azure/key-vault?view=azure-python)

#### <a name="azure-cli"></a>Azure CLI

[Azure CLI för nyckelvalv](/cli/azure/keyvault?view=azure-cli-latest)

#### <a name="azure-powershell"></a>Azure PowerShell 

[Azure PowerShell för nyckelvalv](/powershell/module/az.keyvault/?view=azps-3.6.1#key_vault)

### <a name="code-examples"></a>Kodexempel

Fullständiga exempel med Key Vault med dina program finns i:

- [Azure Key Vault-kodexempel](https://azure.microsoft.com/resources/samples/?service=key-vault) - Kodexempel för Azure Key Vault. 
- [Använd Azure Key Vault från ett webbprogram](../secrets/quick-create-net.md) – självstudiekurs för att lära dig hur du använder Azure Key Vault från ett webbprogram i Azure. 

## <a name="how-tos"></a>Instruktioner

Följande artiklar och scenarier innehåller uppgiftsspecifika riktlinjer för hur du arbetar med Azure Key Vault:

- [Ändra klient-ID för nyckelvalv efter prenumerationsflyttning](subscription-move-fix.md) – När du flyttar din Azure-prenumeration från klient A till klient ab.a till klientnyckeln är dina befintliga nyckelvalv otillgängliga för huvudmännen (användare och program) i klient B. Åtgärda detta med den här guiden.
- [Åtkomst till Key Vault bakom brandvägg](access-behind-firewall.md) - För att komma åt ett nyckelvalv måste ditt nyckelvalvklientprogram kunna komma åt flera slutpunkter för olika funktioner.
- [Så här genererar och överför HSM-skyddade nycklar för Azure Key Vault](../keys/hsm-protected-keys.md) – Det här hjälper dig att planera, generera och sedan överföra dina egna HSM-skyddade nycklar som ska användas med Azure Key Vault.
- Så här skickar du [säkra värden (till exempel lösenord) under distributionen](../../azure-resource-manager/templates/key-vault-parameter.md) – När du behöver skicka ett säkert värde (som ett lösenord) som en parameter under distributionen kan du lagra det värdet som en hemlighet i ett Azure Key Vault och referera till värdet i andra Resource Manager-mallar.
- [Så här använder du Key Vault för utökningsbar nyckelhantering med SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) – SQL Server Connector för Azure Key Vault gör det möjligt för SQL Server och SQL-in-a-VM att använda Azure Key Vault-tjänsten som ekm-provider (Extensible Key Management) för att skydda krypteringsnycklarna för programlänken. Transparent datakryptering, kryptering av säkerhetskopiering och kryptering på kolumnnivå.
- [Hur man distribuerar certifikat till virtuella datorer från Key Vault](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) - Ett molnprogram som körs i en virtuell dator på Azure behöver ett certifikat. Hur får du in det här certifikatet i den här virtuella datorn idag?
- [Konfigurera Key Vault med end to end-tangentrotation och granskning](../secrets/key-rotation-log-monitoring.md) – Det här går igenom hur du konfigurerar nyckelrotation och granskning med Azure Key Vault.
- [Distribuera Azure Web App-certifikat via Key Vault]( https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/) innehåller steg-för-steg-instruktioner för distribution av certifikat som lagras i Key Vault som en del av [Erbjudandet om App-tjänstcertifikat.](https://azure.microsoft.com/blog/internals-of-app-service-certificate/)
- [Bevilja behörighet till många program att komma åt ett nyckelvalv](group-permissions-for-apps.md) Åtkomstkontrollprincipen för Key Vault stöder upp till 1024 poster. Du kan dock skapa en Azure Active Directory-säkerhetsgrupp. Lägg till alla associerade tjänsthuvudnamn i den här säkerhetsgruppen och bevilja sedan åtkomst till den här säkerhetsgruppen till Key Vault.
- Mer uppgiftsspecifik vägledning om hur du integrerar och använder Nyckelvalv med Azure finns i [exempel på Ryan Jones Azure Resource Manager-mall för Key Vault](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
- [Så här använder du Soft-delete för Key Vault med CLI-guider](soft-delete-cli.md) som du använder och livscykeln för ett nyckelvalv och olika nyckelvalvsobjekt med mjuk borttagning aktiverat.
- [Så här använder du soft-delete för Key Vault med PowerShell-guider](soft-delete-powershell.md) som du använder och livscykeln för ett nyckelvalv och olika nyckelvalvsobjekt med mjuk borttagning aktiverat.

## <a name="integrated-with-key-vault"></a>Integrerad med Key Vault

Dessa artiklar handlar om andra scenarier och tjänster som använder eller integreras med Key Vault.

- [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md) utnyttjar [bitlockerfunktionen i](https://technet.microsoft.com/library/cc732774.aspx) Windows och [DM-Crypt-funktionen](https://en.wikipedia.org/wiki/Dm-crypt) i Linux för att tillhandahålla volymkryptering för operativsystemet och datadiskarna. Lösningen är integrerad med Azure Key Vault för att hjälpa dig att styra och hantera diskkrypteringsnycklarna och hemligheterna i din nyckelvalvsprenumeration, samtidigt som du ser till att alla data i diskarna på den virtuella datorn krypteras i vila i din Azure-lagring.
- [Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md) ger alternativ för kryptering av data som lagras i kontot. För nyckelhantering innehåller Data Lake Store två lägen för hantering av dina huvudkrypteringsnycklar (MEK), som krävs för att dekryptera data som lagras i DataSjöarkivet. Du kan antingen låta Data Lake Store hantera MEK:erna åt dig eller välja att behålla äganderätten till MEK:erna med ditt Azure Key Vault-konto. Du anger läget för nyckelhantering när du skapar ett DataSjölagringskonto.
- [Med Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) kan du visa din egen klientnyckel. I stället för att Microsoft hanterar din klientnyckel (standard) kan du hantera din egen klientnyckel för att följa specifika regler som gäller för din organisation. Att hantera sin egen klientnyckel kallas också för att ta med sin egen nyckel eller BYOK.

## <a name="key-vault-overviews-and-concepts"></a>Översikter och koncept för Key Vault

- [Soft-delete-beteende för Key Vault](overview-soft-delete.md)) beskriver en funktion som gör det möjligt att återställa borttagna objekt, oavsett om borttagningen var oavsiktlig eller avsiktlig.
- [Key Vault-klientbegränsningen](overview-throttling.md) orienterar dig till de grundläggande begreppen begränsning och erbjuder en metod för din app.
- [Översikt över key vault-lagringskontonycklar](../secrets/overview-storage-keys.md)) beskriver key vault-integreringen av Azure Storage Accounts-nycklar.
- [Key Vault-säkerhetsvärldar](overview-security-worlds.md) beskriver relationerna mellan regioner och säkerhetsområden.

## <a name="social"></a>Sociala medier

- [Key Vault Blogg](https://aka.ms/kvblog)
- [Viktiga Vault Forum](https://aka.ms/kvforum)

## <a name="supporting-libraries"></a>Stödbibliotek

- [Microsoft Azure Key Vault Core Library](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) tillhandahåller **IKey-** och **IKeyResolver-gränssnitt** för att hitta nycklar från identifierare och utföra åtgärder med nycklar.
- [Microsoft Azure Key Vault-tillägg](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) ger utökade funktioner för Azure Key Vault.
