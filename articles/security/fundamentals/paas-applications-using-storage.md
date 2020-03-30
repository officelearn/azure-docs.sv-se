---
title: Skydda PaaS-program med Azure Storage | Microsoft-dokument
description: Lär dig mer om metodtips för Azure Storage-säkerhet för att skydda dina PaaS-webb- och mobilappar.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: tomsh
ms.openlocfilehash: 675e10101d01d831aad7652c70cbfcf320085a3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70999172"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-storage"></a>Metodtips för att skydda PaaS webb- och mobilappar med Azure Storage
I den här artikeln diskuterar vi en samling metodtips för Azure Storage-säkerhet för att skydda din PaaS-webb- och mobilappar (Platform-as-a-Service). Dessa metodtips härleds från vår erfarenhet av Azure och erfarenheter från kunder som du själv.

Azure gör det möjligt att distribuera och använda lagring på ett sätt som inte är lätt att uppnå lokalt. Med Azure-lagring kan du nå höga nivåer av skalbarhet och tillgänglighet med relativt liten ansträngning. Azure Storage är inte bara grunden för Windows och Virtuella Linux Azure-datorer, det kan också stödja stora distribuerade program.

Azure Storage tillhandahåller följande fyra tjänster: Blob storage, Table storage, Queue storage och File storage. Mer information finns i [Introduktion till Microsoft Azure Storage](/azure/storage/common/storage-introduction).

[Azure Storage-säkerhetsguiden](/azure/storage/common/storage-security-guide) är en bra källa för detaljerad information om Azure Storage och säkerhet. Den här artikeln om metodtips behandlar några av de begrepp som finns i säkerhetsguiden och länkar till säkerhetsguiden, liksom andra källor, på en hög nivå för mer information.

I den här artikeln beskrivs följande metodtips:

- Signaturer för delad åtkomst (SAS)
- Rollbaserad åtkomstkontroll (RBAC)
- Kryptering på klientsidan för data med högt värde
- Kryptering för lagringstjänst


## <a name="use-a-shared-access-signature-instead-of-a-storage-account-key"></a>Använda en signatur för delad åtkomst i stället för en lagringskontonyckel
Åtkomstkontrollen är kritisk. Azure genererar två 512-bitars lagringskontonycklar (SAK: er) när du skapar ett lagringskonto för att hjälpa dig att kontrollera åtkomsten till Azure Storage. Nivån på den viktiga redundansen gör det möjligt för dig att undvika avbrott i tjänsten under rutinmässig nyckelrotation. 

Åtkomstnycklar för lagring är hemligheter med hög prioritet och bör endast vara tillgängliga för dem som ansvarar för åtkomstkontroll för lagring. Om fel personer får tillgång till dessa nycklar har de fullständig kontroll över lagring och kan ersätta, ta bort eller lägga till filer i lagring. Detta inkluderar skadlig kod och andra typer av innehåll som kan äventyra din organisation eller dina kunder.

Du behöver fortfarande ett sätt att ge åtkomst till objekt i lagring. För att ge mer detaljerad åtkomst kan du dra nytta av signaturen för delad åtkomst (SAS). SAS gör det möjligt för dig att dela specifika objekt i lagring för ett fördefinierat tidsintervall och med specifika behörigheter. Med en signatur för delad åtkomst kan du definiera:

- Det intervall som SAS är giltigt över, inklusive starttid och utgångstid.
- De behörigheter som beviljas av SAS. En SAS på en blob kan till exempel ge en användare läs- och skrivbehörighet till den blobben, men inte ta bort behörigheter.
- En valfri IP-adress eller ett intervall med IP-adresser från vilka Azure Storage accepterar SAS. Du kan till exempel ange ett intervall med IP-adresser som tillhör din organisation. Detta ger ytterligare ett mått på säkerhet för din SAS.
- Det protokoll som Azure Storage accepterar SAS. Du kan använda den här valfria parametern för att begränsa åtkomsten till klienter som använder HTTPS.

MED SAS kan du dela innehåll på det sätt du vill dela det utan att ge bort dina lagringskontonycklar. Att alltid använda SAS i ditt program är ett säkert sätt att dela dina lagringsresurser utan att kompromissa med dina lagringskontonycklar.

Mer information om signatur med delad åtkomst finns i [Använda signaturer för delad åtkomst](/azure/storage/common/storage-dotnet-shared-access-signature-part-1). 

## <a name="use-role-based-access-control"></a>Använd rollbaserad åtkomstkontroll
Ett annat sätt att hantera åtkomst är att använda [rollbaserad åtkomstkontroll](/azure/role-based-access-control/overview) (RBAC). Med RBAC fokuserar du på att ge medarbetarna de exakta behörigheter de behöver, baserat på behovet av att känna till och minst privilegier säkerhetsprinciper. För många behörigheter kan exponera ett konto för angripare. För få behörigheter innebär att anställda inte kan få sitt arbete gjort effektivt. RBAC hjälper till att lösa det här problemet genom att erbjuda hantering av detaljerad åtkomst för Azure. Detta är absolut nödvändigt för organisationer som vill tillämpa säkerhetsprinciper för dataåtkomst.

Du kan använda inbyggda RBAC-roller i Azure för att tilldela privilegier till användare. Använd till exempel Storage Account Contributor för molnoperatörer som behöver hantera lagringskonton och rollen Klassisk lagringskontodeltagare för att hantera klassiska lagringskonton. För molnoperatörer som behöver hantera virtuella datorer men inte det virtuella nätverks- eller lagringskonto som de är anslutna till kan du lägga till dem i rollen Deltagare i virtuell dator.

Organisationer som inte tillämpar dataåtkomstkontroll med hjälp av funktioner som RBAC kan ge fler privilegier än nödvändigt för sina användare. Detta kan leda till datakompromettering genom att tillåta vissa användare åtkomst till data som de inte bör ha i första hand.

Mer information om RBAC finns i:

- [Hantera åtkomst med hjälp av RBAC och Azure-portalen](/azure/role-based-access-control/role-assignments-portal)
- [Inbyggda roller för Azure-resurser](/azure/role-based-access-control/built-in-roles)
- [Säkerhetsguide för Azure Storage](/azure/storage/common/storage-security-guide) 

## <a name="use-client-side-encryption-for-high-value-data"></a>Använda kryptering på klientsidan för data med högt värde
Kryptering på klientsidan gör att du kan kryptera data på ett programmatiskt sätt innan du överför till Azure Storage och programmässigt dekryptera data när du hämtar dem. Detta ger kryptering av data under överföring, men det ger också kryptering av data i vila. Kryptering på klientsidan är den säkraste metoden för att kryptera dina data, men det kräver att du gör programmatiska ändringar i ditt program och sätter viktiga hanteringsprocesser på plats.

Kryptering på klientsidan gör det också möjligt för dig att ha ensam kontroll över dina krypteringsnycklar. Du kan generera och hantera dina egna krypteringsnycklar. Den använder en kuvertteknik där Azure storage-klientbiblioteket genererar en innehållskrypteringsnyckel (CEK) som sedan slås in (krypterad) med hjälp av nyckelkrypteringsnyckeln (KEK). KEK identifieras av en nyckelidentifierare och kan vara ett asymmetriskt nyckelpar eller en symmetrisk nyckel och kan hanteras lokalt eller lagras i [Azure Key Vault](/azure/key-vault/key-vault-overview).

Kryptering på klientsidan är inbyggd i Java- och .NET-lagringsklientbiblioteken. Se [Kryptering på klientsidan och Azure Key Vault för Microsoft Azure Storage](/azure/storage/common/storage-client-side-encryption) för information om hur du krypterar data i klientprogram och genererar och hanterar dina egna krypteringsnycklar.

## <a name="enable-storage-service-encryption-for-data-at-rest"></a>Aktivera kryptering av lagringstjänster för data i vila
När [Storage Service Encryption](/azure/storage/common/storage-service-encryption) for File storage är aktiverat krypteras data automatiskt med AES-256-kryptering. Microsoft hanterar all kryptering, dekryptering och nyckelhantering. Den här funktionen är tillgänglig för LRS- och GRS-redundanstyper.

## <a name="next-steps"></a>Nästa steg

I den här artikeln introducerades du en samling metodtips för Azure Storage-säkerhet för att skydda dina PaaS-webb- och mobilappar. Mer information om hur du skyddar dina PaaS-distributioner finns i:

- [Skydda PaaS-distributioner](paas-deployments.md)
- [Skydda PaaS-webb- och mobilappar med Azure App Services](paas-applications-using-app-services.md)
- [Skydda PaaS-databaser i Azure](paas-applications-using-sql.md)
