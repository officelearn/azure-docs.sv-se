---
title: Skydda PaaS-program med hjälp av Azure Storage | Microsoft Docs
description: Läs mer om Azure Storage security metoder för att skydda din PaaS-webbprogram och mobilappar.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: TomShinder
ms.openlocfilehash: 3ad97c7adb5901c1da1d174d12d5d6a91831cc74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60445425"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-storage"></a>Metodtips för att skydda PaaS-webb- och mobilprogram med Azure Storage
I den här artikeln diskuterar vi en samling med Azure Storage rekommenderade säkerhetsmetoder för att skydda din plattform som tjänst (PaaS) webbprogram och mobilappar. Dessa metodtips härleds från vår erfarenhet av Azure och erfarenheter från kunder som dig själv.

Azure gör det möjligt att distribuera och använda lagring på sätt inte enkelt kan ske på plats. Med Azure storage kan nå du hög skalbarhet och tillgänglighet med relativt besvär. Azure Storage inte bara är grunden för Windows och Linux Azure-datorer, kan den också stödja stora distribuerade program.

Azure Storage tillhandahåller följande fyra tjänster: BLOB storage, Table storage, Queue storage och File storage. Mer information finns i [introduktion till Microsoft Azure Storage](../storage/storage-introduction.md).

Den [säkerhetsguiden för Azure Storage](../storage/common/storage-security-guide.md) är en bra källa för detaljerad information om Azure Storage och säkerhet. Denna bästa praxis-artikel-adresser på hög nivå några av de begrepp som finns i säkerhetshandboken och länkar till säkerhetshandboken till, samt andra källor, mer information.

Den här artikeln tar upp följande metoder:

- Signaturer för delad åtkomst (SAS)
- Rollbaserad åtkomstkontroll (RBAC)
- Kryptering för data med högt värde
- Kryptering av lagringstjänst


## <a name="use-a-shared-access-signature-instead-of-a-storage-account-key"></a>Använda en signatur för delad åtkomst i stället för en lagringskontonyckel
Access control är viktiga. För att hjälpa dig att genererar styra åtkomsten till Azure Storage, Azure två 512-bitars storage-kontonycklar (SAKs) när du skapar ett lagringskonto. Viktiga redundansnivå gör det möjligt för dig att undvika avbrott i tjänsten under rotation av rutinunderhåll. 

Åtkomstnycklar för lagring är hemligheter för hög prioritet och bör endast vara tillgängligt för ansvariga för åtkomstkontroll för lagring. Om fel personer får åtkomst till de här nycklarna kan kommer de har fullständig kontroll över lagringen och kan ersätta, ta bort eller lägga till filer till storage. Detta inkluderar skadlig kod och andra typer av innehåll som potentiellt kan påverka din organisation eller dina kunder.

Du behöver ett sätt att ge åtkomst till objekt i storage. Du kan dra nytta av signatur för delad åtkomst (SAS) för att ge mer detaljerad åtkomst. SAS gör det möjligt för dig att dela specifika objekt i lagring för ett fördefinierat tidsintervall och med särskilda behörigheter. En signatur för delad åtkomst kan du definiera:

- Intervallet över vilket Signaturen är giltig, inklusive starttiden och förfallotiden.
- De behörigheter som beviljats av SAS. Exempelvis kan kan en SAS för en blob ge en användare läsa och skrivbehörighet till blobben, men inte att ta bort behörigheter.
- En valfri IP-adress eller IP-adressintervall som Azure Storage accepterar SAS. Du kan till exempel ange ett intervall med IP-adresser som tillhör din organisation. Detta ger ett annat mått av säkerhet för din SAS.
- Det protokoll som accepterar signaturen för delad åtkomst i Azure Storage. Du kan använda den här valfria parametern för att begränsa åtkomsten till klienter som använder HTTPS.

SAS kan du dela innehåll som du vill dela den utan att ge bort dina lagringskontonycklar. Alltid med hjälp av SAS i ditt program är ett säkert sätt att dela dina lagringsresurser utan att kompromissa med lagringsnycklar lagring.

Läs mer om signatur för delad åtkomst i [använda signaturer för delad åtkomst](../storage/common/storage-dotnet-shared-access-signature-part-1.md). 

## <a name="use-role-based-access-control"></a>Använd rollbaserad åtkomstkontroll
Ett annat sätt att hantera åtkomst är att använda [rollbaserad åtkomstkontroll](../role-based-access-control/overview.md) (RBAC). Med RBAC fokuserar på ger anställda de exakta behörigheter som de behöver, utifrån att känna och minsta privilegium säkerhetsprinciper. För många behörigheter kan exponera ett konto för att angripare. För få behörigheter innebär att anställda inte kan utföra sitt arbete effektivt. RBAC kan du lösa det här problemet genom att erbjuda detaljerad åtkomsthantering för Azure. Det här är viktigt för organisationer som vill tillämpa säkerhetsprinciper för dataåtkomst.

Du kan använda inbyggda RBAC-roller i Azure för att tilldela behörigheter till användare. Till exempel använda Lagringskontodeltagare för molnoperatörer som behöver kunna hantera storage-konton och klassiska Lagringskontodeltagare roll att hantera klassiska lagringskonton. För molnoperatörer som behöver kunna hantera virtuella datorer men inte det virtuella nätverk eller lagringskonto som de är anslutna, kan du lägga till dem till rollen virtuell Datordeltagare.

Organisationer som inte behöver använda åtkomstkontroll för data med hjälp av funktioner, till exempel RBAC kan ger fler behörigheter än vad som krävs för sina användare. Detta kan leda till kompromettering av data genom att tillåta att vissa användare åtkomst till data som de inte ska ha i första hand.

Om du vill veta se mer om RBAC:

- [Hantera åtkomst med hjälp av RBAC och Azure-portalen](../role-based-access-control/role-assignments-portal.md)
- [Inbyggda roller för Azure-resurser](../role-based-access-control/built-in-roles.md)
- [Säkerhetsguide för Azure Storage](../storage/common/storage-security-guide.md) 

## <a name="use-client-side-encryption-for-high-value-data"></a>Använd klientside-kryptering för data med högt värde
Client side encryption kan du programmässigt kryptera data under överföring innan du laddar upp till Azure Storage och programmässigt dekryptera data vid hämtning av den. Detta tillhandahåller kryptering av data under överföring, men det innehåller också kryptering av vilande data. Client side encryption är den säkraste metoden för att kryptera dina data, men det kräver att du gör programmässiga ändringar i programmet och placera viktiga processer på plats.

Client side encryption kan man få ensam kontroll över dina krypteringsnycklar. Du kan skapa och hantera dina egna krypteringsnycklar. Den använder en kuvert-teknik där Azure storage-klientbiblioteket genererar en content krypteringsnyckel (CEK) som sedan radbryts (krypterad) med klartextnyckel-nyckel (KEK). KEK identifieras med en nyckelidentifierare och kan vara ett asymmetriskt nyckelpar eller en symmetrisk nyckel och kan hanteras lokalt eller lagras i [Azure Key Vault](../key-vault/key-vault-whatis.md).

Client side encryption är inbyggt i Java och .NET storage-klientbibliotek. Se [Client side encryption och Azure Key Vault för Microsoft Azure Storage](../storage/storage-client-side-encryption.md) information om kryptering av data i klientprogram och generera och hantera dina egna krypteringsnycklar.

## <a name="enable-storage-service-encryption-for-data-at-rest"></a>Aktivera kryptering av lagringstjänst för vilande data
När [Lagringstjänstkryptering](../storage/storage-service-encryption.md) för fillagring är aktiverat krypteras data automatiskt med AES-256-kryptering. Microsoft hanterar all kryptering, dekryptering och nyckelhantering. Den här funktionen är tillgänglig för LRS och GRS typer av redundans.

## <a name="next-steps"></a>Nästa steg

Den här artikeln har du introducerats till en samling med Azure Storage rekommenderade säkerhetsmetoder för att skydda din PaaS-webbprogram och mobilappar. Mer information om hur du skyddar dina PaaS-distributioner finns:

- [Skydda PaaS-distributioner](security-paas-deployments.md)
- [Skydda PaaS-webb- och mobilprogram med Azure App Services](security-paas-applications-using-app-services.md)
- [Skydda PaaS-databaser i Azure](security-paas-applications-using-sql.md)
