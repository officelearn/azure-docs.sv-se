---
title: Skydda PaaS-program med hjälp av Azure Storage | Microsoft Docs
description: " Läs mer om Azure Storage Säkerhet Metodtips för att skydda din PaaS webb- och mobilprogram. "
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomShinder
ms.openlocfilehash: 9d4251e61b60d8da6ce5072ba66aeaedb60cb33a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
ms.locfileid: "31418228"
---
# <a name="securing-paas-web-and-mobile-applications-using-azure-storage"></a>Att säkra PaaS-webb- och mobila program med Azure Storage
I den här artikeln tar vi upp en samling Azure Storage säkerhetsmetoder för att skydda din PaaS webb- och mobilprogram. Följande rekommendationer härleds från våra erfarenhet av Azure och upplevelser för kunder som själv.

Den [säkerhetsguiden för Azure Storage](../storage/common/storage-security-guide.md) hittar du mer detaljerad information om Azure Storage och säkerhet.  Den här artikeln tar på en hög nivå vissa av de begrepp som hittades i säkerhetsguiden och länkar till säkerhetsguiden för, samt andra källor, mer information.

## <a name="azure-storage"></a>Azure Storage
Azure gör det möjligt att distribuera och använda lagring på sätt inte enkelt kan ske lokalt. Du kan nå hög skalbarhet och tillgänglighet relativt visningsläge med Azure storage. Azure storage är inte bara grunden för Windows och Linux virtuella Azure-datorer stöder även stora distribuerade program.

Azure Storage tillhandahåller följande fyra tjänster: Blob Storage, Table Storage, Queue Storage och File Storage. Läs mer i [introduktion till Microsoft Azure Storage](../storage/storage-introduction.md).

## <a name="best-practices"></a>Bästa praxis
Den här artikeln tar följande metodtips:

- NAP:
   - Signaturer för delad åtkomst (SAS)
   - Hanterade diskar
   - Rollbaserad åtkomstkontroll (RBAC)

- Lagringskryptering:
   - Klient-kryptering för data med högt värde på serversidan
   - Azure Disk Encryption för virtuella datorer (VM)
   - Kryptering av lagringstjänst

## <a name="access-protection"></a>NAP
### <a name="use-shared-access-signature-instead-of-a-storage-account-key"></a>Använda signatur för delad åtkomst i stället för en lagringskontonyckel

I en IaaS-lösning som kör Windows Server- eller Linux virtuella datorer vanligtvis, skyddas filer från avslöjande och manipulering hot med hjälp av mekanismer för kontrollen. På Windows använder du [åtkomstkontrollistor (ACL)](../virtual-network/virtual-networks-acl.md) och på Linux du förmodligen använda [chmod](https://en.wikipedia.org/wiki/Chmod). I princip, detta är exakt vad du skulle göra om du idag skydda filer på en server i ditt eget datacenter.

PaaS är olika. Ett av de vanligaste sätten att lagra filer i Microsoft Azure är att använda [Azure Blob storage](../storage/storage-dotnet-how-to-use-blobs.md). Skillnad mellan Blob storage och andra fillagring är fil-i/o och skyddsmetod som följer med i/o.

Åtkomstkontroll är viktigt. Som hjälper dig att styra åtkomsten till Azure storage, systemet skapar två 512-bitars lagringskontonycklar (SAKs) när du [skapa ett lagringskonto](../storage/common/storage-create-storage-account.md). Nivån av nycklar redundans gör det möjligt för dig att undvika avbrott i tjänsten under rutinunderhåll viktiga rotation.

Åtkomstnycklar för lagring är hemligheter med hög prioritet och bör enbart vara åtkomliga för ansvariga för åtkomstkontroll för lagring. Om fel personer får tillgång till nycklarna kan de kommer har fullständig kontroll av lagring och kan ersätta, ta bort eller lägga till filer till lagring. Detta inkluderar skadlig kod och andra typer av innehåll som potentiellt kan påverka din organisation eller dina kunder.

Du behöver ett sätt att ge åtkomst till objekt i lagringen. För att ge mer detaljerade åtkomst kan du dra nytta av [signatur för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md) (SAS). SAS gör det möjligt att dela specifika objekt i lagring för en fördefinierad tidsintervall och särskilda behörigheter. En signatur för delad åtkomst kan du definiera:

- Tidsintervall som SAS är giltiga, inklusive starttid och förfallotiden.
- De behörigheter som utfärdats av SAS. Till exempel kan en SAS för en blob ge en användare läsa och skrivbehörighet till blobben, men inte att ta bort behörigheter.
- En valfri IP-adress eller intervall av IP-adresser som Azure Storage accepterar SAS. Du kan till exempel ange ett intervall med IP-adresser som tillhör din organisation. Detta ger ett annat mått av säkerhet för din SAS.
- Det protokoll som accepterar Azure Storage SAS. Du kan använda den här valfria parametern för att begränsa åtkomsten till klienter som använder HTTPS.

SAS kan du dela innehåll på sätt som du vill dela utan att ge bort din Lagringskontonycklar. Alltid med hjälp av SAS i ditt program är ett säkert sätt att dela dina lagringsresurser utan att kompromissa med dina nycklar för lagringskonto.

Läs mer i [använder signaturer för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md) (SAS). Läs mer om potentiella risker och rekommendationer för att minska riskerna i [metodtips när du använder SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

### <a name="use-managed-disks-for-vms"></a>Använda hanterade diskar för virtuella datorer

När du väljer [Azure hanterade diskar](../storage/storage-managed-disks-overview.md), hanteras av Azure storage-konton som du använder för din Virtuella diskar. Allt du behöver göra är att välja vilken typ av disk (Standard eller Premium) och diskstorleken; Azure storage gör resten. Du behöver inte bry dig om skalbarhetsbegränsningar som annars kan ha krävt du till flera lagringskonton.

Läs mer i [vanliga frågor och svar om hanterade och ohanterade premiumdiskar](../storage/storage-faq-for-disks.md).

### <a name="use-role-based-access-control"></a>Använda rollbaserad åtkomstkontroll

Vi diskuterade tidigare med hjälp av delade signatur åtkomst (SAS) för att bevilja begränsad åtkomst till objekt i ditt lagringskonto till andra klienter, utan att utsätta din lagringskontonyckel för kontot. Ibland uppväger risker med en viss åtgärd mot ditt lagringskonto fördelarna med SAS. Ibland är det enklare att hantera åtkomst på annat sätt.

Ett annat sätt att hantera åtkomst är att använda [rollbaserad åtkomstkontroll i](../role-based-access-control/overview.md) (RBAC). Baserat på behöver känna till och minsta privilegium säkerhetsprinciper med RBAC du fokusera på att ge anställda behörigheterna exakt som de behöver. För många behörigheter kan exponera ett konto för attacker. För få behörigheter innebär att anställda kan få arbetet gjort effektivt. RBAC kan du lösa det här problemet genom att erbjuda detaljerad åtkomsthantering för Azure. Det här är viktigt för organisationer som vill tillämpa säkerhetsprinciper för dataåtkomst.

Du kan utnyttja inbyggda RBAC-roller i Azure för att tilldela behörigheter till användare. Överväg att använda Storage-konto deltagare för molnoperatörer som behöver kunna hantera storage-konton och klassiska Storage-konto deltagarrollen till klassiska lagringskonton hantera. För molnoperatörer som behöver för att hantera virtuella datorer men inte den virtuella nätverks- eller konto som de är anslutna, Överväg att lägga till dem till rollen Virtual Machine-deltagare.

Organisationer som inte behöver använda data åtkomstkontroll genom att utnyttja funktioner, till exempel RBAC kan ger fler behörigheter än vad som krävs för sina användare. Detta kan leda till röjande av data genom att tillåta att vissa användare åtkomst till data som de inte borde ha i första hand.

Om du vill veta se mer om RBAC:

- [Rollbaserad åtkomstkontroll i Azure](../role-based-access-control/role-assignments-portal.md)
- [Inbyggda roller för rollbaserad åtkomstkontroll i Azure](../role-based-access-control/built-in-roles.md)
- [Azure Storage-säkerhetsguiden](../storage/common/storage-security-guide.md) information om hur du skyddar ditt lagringskonto med RBAC

## <a name="storage-encryption"></a>Lagringskryptering
### <a name="use-client-side-encryption-for-high-value-data"></a>Använd klientsidans kryptering för data med högt värde

Kryptering på klientsidan kan du programmässigt kryptera data under överföring innan du laddar upp till Azure Storage och dekryptera data programmässigt när hämtas från lagringsplatsen.  Detta tillhandahåller kryptering av data under överföring, men det ger också kryptering av vilande data.  Klientsidan kryptering är den säkraste metoden för att kryptera dina data, men det kräver att du ändrar ditt program programmässiga och gjorda nyckelhantering processer.

Kryptering på klientsidan kan du ha ensam kontroll över dina krypteringsnycklar.  Du kan skapa och hantera egna krypteringsnycklar.  Kryptering på klientsidan använder en kuvert teknik där Azure storage-klientbiblioteket genererar en innehåll krypteringsnyckel (CEK) som sedan omslutna (krypterade) med hjälp av krypteringsnyckel (KEK). KEK identifieras med en nyckelidentifierare och kan vara ett asymmetriskt nyckelpar eller en symmetrisk nyckel och kan hanteras lokalt eller lagras i [Azure Key Vault](../key-vault/key-vault-whatis.md).

Kryptering på klientsidan är inbyggd i Java och storage-klientbibliotek för .NET.  Se [kryptering på klientsidan och Azure Key Vault för Microsoft Azure Storage](../storage/storage-client-side-encryption.md) information om kryptering av data i klientprogram och generera och hantera egna krypteringsnycklar.

### <a name="azure-disk-encryption-for-vms"></a>Azure Disk Encryption för virtuella datorer
Azure Disk Encryption är en funktion som hjälper dig att kryptera din Windows- och Linux IaaS virtuella diskar. Azure Disk Encryption använder funktionen industry standard BitLocker i Windows och funktionen DM-Crypt i Linux att tillhandahålla volymkryptering för Operativsystemet och datadiskar. Lösningen är integrerad med Azure Key Vault som hjälper dig att styra och hantera disk krypteringsnycklar och hemligheter i nyckelvalvet-prenumeration. Lösningen betyder också att krypteras alla data på virtuella diskar i vila i ditt Azure storage.

Se [Azure Disk Encryption för Windows och Linux-IaaS-VM](azure-security-disk-encryption.md).

### <a name="storage-service-encryption"></a>Kryptering av lagringstjänst
När [Lagringstjänstens kryptering](../storage/storage-service-encryption.md) för File storage är aktiverat data krypteras automatiskt med hjälp av AES 256-kryptering. Microsoft hanterar alla kryptering, dekryptering och hantering av nycklar. Den här funktionen är tillgänglig för LRS- och GRS redundans-typer.

## <a name="next-steps"></a>Nästa steg
Den här artikeln introduceras du till en samling Azure Storage säkerhetsmetoder för att skydda din PaaS webb- och mobilprogram. Mer information om hur du skyddar dina PaaS-distributioner finns:

- [Skydda PaaS-distributioner](security-paas-deployments.md)
- [Att säkra PaaS-webb- och mobila program med Azure App Service](security-paas-applications-using-app-services.md)
- [Att säkra PaaS-databaser i Azure](security-paas-applications-using-sql.md)
