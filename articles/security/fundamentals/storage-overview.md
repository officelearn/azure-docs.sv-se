---
title: Säkerhetsfunktioner som kan användas med Azure Storage | Microsoft Docs
description: Den här artikeln innehåller en översikt över de centrala Azure-säkerhetsfunktioner som kan användas med Azure Storage.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: 249380b5bd9d95e969a9c7a812102b694b9d1e3b
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726548"
---
# <a name="azure-storage-security-overview"></a>Översikt över Azure Storage säkerhet

Den här artikeln innehåller en översikt över de Azure-säkerhetsfunktioner som du kan använda med Azure Storage. Azure Storage är molnlagringslösningen för moderna program som kräver hållbarhet, tillgänglighet och skalbarhet för att uppfylla kundernas behov. Azure Storage innehåller en omfattande uppsättning säkerhetsfunktioner. Du kan:

* Skydda lagrings kontot med hjälp av rollbaserad Access Control (RBAC) och Azure Active Directory.
* Skydda data i överföring mellan ett program och Azure med hjälp av kryptering på klient sidan, HTTPS eller SMB 3,0.
* Ange att data ska krypteras automatiskt när de skrivs till Azure Storage med hjälp av Kryptering för lagringstjänst.
* Ange operativ system och data diskar som används av virtuella datorer (VM) som ska krypteras med hjälp av Azure Disk Encryption.
* Bevilja delegerad åtkomst till data objekt i Azure Storage med hjälp av signaturer för delad åtkomst (SASs).
* Använd Analytics för att spåra autentiseringsmetoden som någon använder vid åtkomst till lagring.

En mer detaljerad översikt över säkerheten i Azure Storage finns i [säkerhets guiden för Azure Storage](../../storage/common/storage-security-guide.md). Den här guiden ger en detaljerad genom gång av säkerhetsfunktionerna i Azure Storage. Dessa funktioner omfattar lagrings konto nycklar, data kryptering under överföring och i vila och lagrings analys.

## <a name="role-based-access-control"></a>Rollbaserad Access Control

Du kan skydda ditt lagrings konto med hjälp av rollbaserad Access Control. Att begränsa åtkomsten baserat på [behovet av att känna till](https://en.wikipedia.org/wiki/Need_to_know) och [minsta behörighets](https://en.wikipedia.org/wiki/Principle_of_least_privilege) säkerhets principer är att det är absolut nödvändigt för organisationer som vill tillämpa säkerhets principer för data åtkomst. Dessa behörigheter beviljas genom att tilldela lämplig RBAC-roll till grupper och program i ett visst omfång. Du kan använda [Inbyggda RBAC-roller](/azure/role-based-access-control/built-in-roles), till exempel lagrings konto deltagare, för att tilldela behörigheter till användare.

Läs mer:

* [Azure Active Directory rollbaserad Access Control](/azure/role-based-access-control/role-assignments-portal)

## <a name="delegated-access-to-storage-objects"></a>Delegerad åtkomst till lagrings objekt

En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt storage-konto. SAS innebär att du kan bevilja en klient begränsad behörighet till objekt i ditt lagrings konto under en angiven period och med en angiven uppsättning behörigheter. Du kan bevilja dessa begränsade behörigheter utan att behöva dela dina konto åtkomst nycklar.

SAS är en URI som omfattar den information som krävs för autentiserad åtkomst till en lagrings resurs i dess frågeparametrar. För att få åtkomst till lagrings resurser med SAS behöver klienten bara ange SAS för lämplig konstruktor eller metod.

Läs mer:

* [Förstå SAS-modellen](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)
* [Skapa och använda en SAS med Blob Storage](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)

## <a name="encryption-in-transit"></a>Kryptering under överföring

Kryptering under överföring är en mekanism för att skydda data när de överförs mellan nätverk. Med Azure Storage kan du skydda data med hjälp av:

* [Kryptering på transport nivå](/azure/storage/common/storage-security-guide#encryption-in-transit), till exempel https, när du överför data till eller från Azure Storage.
* [Kryptering av kablar](/azure/storage/common/storage-security-guide#using-encryption-during-transit-with-azure-file-shares), till exempel SMB 3,0-kryptering, för Azure-filresurser.
* [Kryptering på klient sidan](/azure/storage/common/storage-security-guide#using-client-side-encryption-to-secure-data-that-you-send-to-storage), för att kryptera data innan de överförs till lagrings utrymmet och för att dekryptera data när de har överförts från lagrings utrymmet.

Läs mer om kryptering på klient sidan:

* [Kryptering på klient sidan för Microsoft Azure Storage](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
* [Serie med moln säkerhets kontroller: Kryptera data under överföring](https://cloudblogs.microsoft.com/microsoftsecure/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>Vilande kryptering

För många organisationer är [data kryptering i vila](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) ett obligatoriskt steg mot data sekretess, efterlevnad och data suveränitet. Tre Azure-funktioner ger kryptering av data som är i vila:

* [Kryptering för lagringstjänst](/azure/storage/common/storage-security-guide#encryption-at-rest) är alltid aktiverat och krypterar automatiskt lagrings tjänst data när de skrivs till Azure Storage.
* [Kryptering på klient sidan](/azure/storage/common/storage-security-guide#client-side-encryption) tillhandahåller även funktionen för kryptering i vila.
* Med [Azure Disk Encryption](/azure/storage/common/storage-security-guide#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) kan du kryptera de OS-diskar och data diskar som en virtuell IaaS-dator använder.

Läs mer om Kryptering för lagringstjänst:

* [Azure Storage tjänst kryptering](https://azure.microsoft.com/services/storage/) är tillgänglig för [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/). Mer information om andra typer av Azure-lagring finns i [Azure Files](https://azure.microsoft.com/services/storage/files/), [Table Storage](https://azure.microsoft.com/services/storage/tables/)och [Queue Storage](https://azure.microsoft.com/services/storage/queues/).
* [Azure Storage tjänst kryptering för vilande data](/azure/storage/common/storage-service-encryption)

## <a name="azure-disk-encryption"></a>Azure Disk Encryption

Azure Disk Encryption för virtuella datorer hjälper dig att lösa organisationens krav på säkerhet och efterlevnad. Den krypterar dina virtuella dator diskar (inklusive start-och data diskar) med hjälp av nycklar och principer som du styr i [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Disk kryptering för virtuella datorer fungerar för Linux-och Windows-operativsystem. Den använder också Key Vault för att skydda, hantera och granska användningen av disk krypterings nycklarna. Alla data på de virtuella dator diskarna krypteras i vila med hjälp av krypterings teknik bransch standard i dina Azure Storage-konton. Disk krypterings lösningen för Windows är baserad på [Microsoft BitLocker-diskkryptering](https://technet.microsoft.com/library/cc732774.aspx)och Linux-lösningen baseras på [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Lär dig mer

* [Azure Disk Encryption för Windows-och Linux-IaaS Virtual Machines](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="firewalls-and-virtual-networks"></a>Brand väggar och virtuella nätverk

Med Azure Storage kan du aktivera brand Väggs regler för dina lagrings konton. När den är aktive rad blockeras inkommande begär Anden för data, inklusive förfrågningar från andra Azure-tjänster. Du kan konfigurera undantag för att tillåta trafik. Brand Väggs regler kan aktive ras på befintliga lagrings konton eller under skapande tid.

Du bör använda den här funktionen för att skydda dina lagrings konton till en angiven uppsättning tillåtna nätverk.

För mer information om Azure Storage-brandväggar och virtuella nätverk, se artikeln [konfigurera Azure Storage brand väggar och virtuella nätverk](/azure/storage/common/storage-network-security)

## <a name="azure-data-box"></a>Azure Data Box

Med Data Box, Data Box Disk och Data Box – tung kan du överföra stora mängder data till Azure när nätverket inte är ett alternativ. De här data överförings enheterna i frånkopplat läge levereras mellan din organisation och Azure-datacenter. De använder AES-kryptering för att skydda dina data i transit, och de genomgår en noggrann sanering efter uppladdning för att ta bort dina data från enheten.

Data Box Edge och Data Box Gateway är onlineprodukter för dataöverföring som fungerar som gateways för nätverkslagring som hanterar data mellan din plats och Azure. Data Box Edge, en lokal nätverksenhet, överför data till och från Azure och använder AI-aktiverad (artificiell intelligens) gränsdatabearbetning för att bearbeta data. Data Box Gateway är en virtuell installation med lagringsgatewayfunktioner.

Läs mer:

* [Azure Data Box](https://azure.microsoft.com/services/storage/databox/)
* [Azure Data Box Edge](/azure/databox-online/data-box-edge-overview)
* [Azure Data Box Gateway](/azure/databox-online/data-box-gateway-overview)

## <a name="advanced-threat-protection"></a>Avancerat skydd

Azure Storage tillhandahåller Avancerat skydd för ytterligare ett lager med säkerhets information som identifierar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja ditt lagrings konto. Avancerat skydd övervakar Azure Storage diagnostikloggar för misstänkta Läs-, Skriv-eller borttagnings begär anden till Blob Storage.

Aviseringar om avancerade hot skydd kan visas från [Azure Security Center](https://azure.microsoft.com/services/security-center/). Azure Security Center innehåller information om misstänkt aktivitet som identifieras och rekommenderar åtgärder för att undersöka och åtgärda potentiella hot.

Läs mer:

* [Översikt över Azure Storage Advanced Threat Protection](/azure/storage/common/storage-advanced-threat-protection)

## <a name="azure-key-vault"></a>Azure Key Vault

Azure Disk Encryption använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) som hjälp för att kontrol lera och hantera disk krypterings nycklar och hemligheter i Key Vault-prenumerationen. Det säkerställer också att alla data på de virtuella dator diskarna krypteras i vila i Azure Storage. Du bör använda Key Vault för att granska nycklar och princip användning.

Lär dig mer

* [Vad är Azure Key Vault?](/azure/key-vault/key-vault-overview)
