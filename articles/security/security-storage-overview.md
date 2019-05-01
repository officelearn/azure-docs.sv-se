---
title: Säkerhetsfunktioner som kan användas med Azure Storage | Microsoft Docs
description: Den här artikeln innehåller en översikt över viktigaste Azure säkerhetsfunktioner som kan användas med Azure Storage.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: 262ccc311b2b7ec634183eb4f914902048ba2c3b
ms.sourcegitcommit: 8a681ba0aaba07965a2adba84a8407282b5762b2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2019
ms.locfileid: "64872046"
---
# <a name="azure-storage-security-overview"></a>Säkerhetsöversikt för Azure Storage

Den här artikeln innehåller en översikt över säkerheten i Azure-funktioner som du kan använda med Azure Storage. Azure Storage är molnlagringslösningen för moderna program som kräver hållbarhet, tillgänglighet och skalbarhet för att uppfylla kundernas behov. Azure Storage tillhandahåller en omfattande uppsättning funktioner för säkerhet. Du kan:

* Skydda storage-konto med hjälp av rollbaserad åtkomstkontroll (RBAC) och Azure Active Directory.
* Skydda data i rörelse mellan ett program och Azure med hjälp av client side encryption, HTTPS och SMB 3.0.
* Ange data krypteras automatiskt när de skrivs till Azure Storage med hjälp av kryptering av lagringstjänst.
* Ställ in OS- och diskar som används av virtuella datorer (VM) som ska krypteras med hjälp av Azure Disk Encryption.
* Bevilja begränsad åtkomst till dataobjekt i Azure Storage med hjälp av signaturer för delad åtkomst (Saas).
* Använda analytics för att spåra den autentiseringsmetod som någon använder när de har åtkomst till lagring.

Mer detaljerad information om säkerhet i Azure Storage, finns det [säkerhetsguiden för Azure Storage](../storage/common/storage-security-guide.md). Den här guiden innehåller en djupdykning i säkerhetsfunktionerna i Azure Storage. Dessa funktioner innefattar lagringskontonycklar, datakryptering vid överföring och på rest- och storage analytics.

## <a name="role-based-access-control"></a>Rollbaserad Access Control

Du kan skydda ditt lagringskonto med hjälp av rollbaserad åtkomstkontroll. Begränsa åtkomst baserat på den [behöver veta](https://en.wikipedia.org/wiki/Need_to_know) och [lägsta behörighet](https://en.wikipedia.org/wiki/Principle_of_least_privilege) säkerhetsprinciper är mycket viktigt för organisationer som vill tillämpa säkerhetsprinciper för dataåtkomst. Dessa behörigheter beviljas genom att tilldela lämpliga RBAC-roll till grupper och program för ett visst omfång. Du kan använda [inbyggda RBAC-roller](../role-based-access-control/built-in-roles.md), till exempel Lagringskontodeltagare att tilldela behörigheter till användare.

Läs mer:

* [Azure Active Directory rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md)

## <a name="delegated-access-to-storage-objects"></a>Delegerad åtkomst till storage-objekt

En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt storage-konto. SAS innebär att du kan ge en klient begränsad behörighet till objekt i ditt storage-konto för en angiven tidsperiod och med en angiven uppsättning behörigheter. Du kan bevilja dessa begränsade behörigheter utan att behöva dela åtkomstnycklarna för kontot.

Signaturen för delad åtkomst är en URI som omfattar all information som behövs för autentiserad åtkomst till en lagringsresurs i dess Frågeparametrar. Om du vill få åtkomst till lagringsresurser med SAS, behöver klienten bara ange SAS till lämplig konstruktor nebo metodu.

Läs mer:

* [Förstå SAS-modellen](../storage/common/storage-dotnet-shared-access-signature-part-1.md)
* [Skapa och använda en SAS med Blob storage](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md)

## <a name="encryption-in-transit"></a>Kryptering under överföring

Kryptering under överföring är en mekanism för att skydda data när de skickas över nätverk. Du kan använda Azure Storage, för att skydda data med hjälp av:

* [Kryptering på transportnivå](../storage/common/storage-security-guide.md#encryption-in-transit), till exempel HTTPS, när du överför data till eller från Azure Storage.
* [Koppla kryptering](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), till exempel SMB 3.0-kryptering för Azure-filresurser.
* [Client side encryption](../storage/common/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage), att kryptera data innan den överförs till Storage och att dekryptera data efter överföringen slut på minne.

Läs mer om client side encryption:

* [Client Side Encryption för Microsoft Azure Storage](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
* [Molnsäkerhet styr serien: Krypterar Data under överföring](https://cloudblogs.microsoft.com/microsoftsecure/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>Vilande kryptering

Många organisationer [datakryptering i viloläge](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) är ett obligatoriskt steg mot att sekretess, efterlevnad och datasuveränitet. Tre funktioner i Azure tillhandahåller kryptering av data som är vilande:

* [Kryptering av lagringstjänst](../storage/common/storage-security-guide.md#encryption-at-rest) är alltid aktiverat och krypterar automatiskt data för storage-tjänsten när du skriver den till Azure Storage.
* [Client side encryption](../storage/common/storage-security-guide.md#client-side-encryption) tillhandahåller även funktionen för kryptering i vila.
* [Azure Disk Encryption](../storage/common/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) kan du kryptera OS-diskar och datadiskar som använder en IaaS-dator.

Lär dig mer om Storage Service Encryption:

* [Azure Storage Service Encryption](https://azure.microsoft.com/services/storage/) är tillgänglig för [Azure Blob storage](https://azure.microsoft.com/services/storage/blobs/). Mer information om andra typer av Azure storage finns [Azure Files](https://azure.microsoft.com/services/storage/files/), [tabellagring](https://azure.microsoft.com/services/storage/tables/), och [Queue storage](https://azure.microsoft.com/services/storage/queues/).
* [Azure Storage Service Encryption för vilande Data](../storage/common/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Azure Disk Encryption

Azure Disk Encryption för virtuella datorer hjälper dig att uppfylla organisationens säkerhet och krav på efterlevnad. Dina VM-diskar (inklusive start- och datadiskar) krypteras med hjälp av nycklar och principer som du styr i [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Diskkryptering för virtuella datorer fungerar för Linux och Windows-operativsystem. Key Vault används också för att skydda, hantera och granska användning av diskkrypteringsnycklar. Alla data på dina VM-diskar krypteras i viloläge med hjälp av branschstandard krypteringsteknik på dina Azure storage-konton. Disk Encryption-lösningen för Windows är baserad på [Microsoft BitLocker-diskkryptering](https://technet.microsoft.com/library/cc732774.aspx), och Linux-lösningen baseras på [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Läs mer

* [Azure Disk Encryption för Windows och Linux IaaS-datorer](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="firewalls-and-virtual-networks"></a>Brandväggar och virtuella nätverk

Azure storage kan du aktivera brandväggsregler för dina lagringskonton. När aktiverad de blockerar inkommande förfrågningar för data, inklusive förfrågningar från andra Azure-tjänster. Du kan konfigurera undantag för att tillåta trafik. Brandväggsregler kan aktiveras på befintliga lagringskonton eller under skapandeprocessen.

Du bör använda den här funktionen för att skydda dina storage-konton till en specifik uppsättning tillåtna nätverk.

Mer information om Azure storage brandväggar och virtuella nätverk finns i artikeln [konfigurera Azure Storage-brandväggar och virtuella nätverk](../storage/common/storage-network-security.md)

## <a name="azure-data-box"></a>Azure Data Box

Med Data Box, Data Box Disk och Data Box – tung kan du överföra stora mängder data till Azure när nätverket inte är ett alternativ. Dessa enheter för överföring av data offline levereras mellan din organisation och Azure-datacentret. De använder AES-kryptering för att skydda dina data i transit, och de genomgår en noggrann sanering efter uppladdning för att ta bort dina data från enheten.

Data Box Edge och Data Box Gateway är onlineprodukter för dataöverföring som fungerar som gateways för nätverkslagring som hanterar data mellan din plats och Azure. Data Box Edge, en lokal nätverksenhet, överför data till och från Azure och använder AI-aktiverad (artificiell intelligens) gränsdatabearbetning för att bearbeta data. Data Box Gateway är en virtuell installation med lagringsgatewayfunktioner.

Läs mer:

* [Azure Data Box](https://azure.microsoft.com/services/storage/databox/)
* [Azure Data Box Edge](../databox-online/data-box-edge-overview.md)
* [Azure Data Box Gateway](..//databox-online/data-box-gateway-overview.md)

## <a name="advanced-threat-protection"></a>Advanced Threat Protection

Azure Storage tillhandahåller Avancerat skydd för ett extra lager säkerhetsintelligens som identifierar onormala och potentiellt skadliga försök att komma åt eller utnyttja ditt storage-konto. Advanced Threat Protection övervakar diagnostikloggar för Azure Storage för misstänkt Läs, skriva eller ta bort begäranden till bloblagring.

Avancerade Threat Protection-aviseringar kan visas från [Azure Security Center](https://azure.microsoft.com/services/security-center/). Azure Security Center innehåller information för varje misstänkt aktivitet har identifierats och rekommenderar åtgärder för att undersöka och åtgärda eventuella hot.

Läs mer:

* [Azure Storage Advanced Threat Protection-översikt](../storage/common/storage-advanced-threat-protection.md)

## <a name="azure-key-vault"></a>Azure Key Vault

Azure Disk Encryption används [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) för att styra och hantera diskkrypteringsnycklarna och hemligheter i key vault-prenumeration. Det innebär också att alla data på diskar i virtuella datorer krypteras i vila i Azure Storage. Du bör använda Key Vault för att granska nycklar och användning av principen.

Läs mer

* [Vad är Azure Key Vault?](../key-vault/key-vault-overview.md)
