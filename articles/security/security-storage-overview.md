---
title: Säkerhetsfunktioner som kan användas med Azure Storage | Microsoft Docs
description: Den här artikeln innehåller en översikt över grundläggande Azure säkerhetsfunktioner som kan användas med Azure Storage.
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: 9558f1ec0d8ccd83da764a0967fa83d93e1e6a02
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365395"
---
# <a name="azure-storage-security-overview"></a>Översikt över säkerheten i Azure Storage
Azure Storage är molnlagringslösningen för moderna program som kräver hållbarhet, tillgänglighet och skalbarhet för att uppfylla kundernas behov. Azure Storage innehåller en omfattande uppsättning säkerhetsfunktioner. Du kan:

* Skydda storage-konto med hjälp av rollbaserad åtkomstkontroll (RBAC) och Azure Active Directory.
* Skydda data under överföring mellan en program- och Azure med hjälp av kryptering på klientsidan, HTTPS och SMB 3.0.
* Ange data krypteras automatiskt när de skrivs till Azure Storage med hjälp av Storage Service-kryptering.
* Ange Operativsystemet och datadiskarna används av virtuella datorer (VM) som ska krypteras med hjälp av Azure Disk Encryption.
* Bevilja delegerad åtkomst till dataobjekt i Azure Storage med hjälp av signaturer för delad åtkomst (SASs).
* Använda analytics för att spåra den autentiseringsmetod som någon använder när de har åtkomst till lagring.

En närmare titt på säkerhet i Azure Storage finns i [säkerhetsguiden för Azure Storage](../storage/common/storage-security-guide.md). Den här guiden ger en djupdykning i säkerhetsfunktionerna i Azure Storage. Dessa funktioner inkluderar lagringskontonycklar, datakryptering vid överföring och i vila och storage analytics.

Den här artikeln innehåller en översikt över säkerheten i Azure-funktioner som du kan använda med Azure Storage. Länkar till artiklar ger information om varje funktion så kan du läsa mer.

## <a name="role-based-access-control"></a>Rollbaserad Access Control
Du kan skydda ditt lagringskonto med hjälp av rollbaserad åtkomstkontroll. Begränsa åtkomst baserat på de [behöver](https://en.wikipedia.org/wiki/Need_to_know) och [minsta privilegium](https://en.wikipedia.org/wiki/Principle_of_least_privilege) säkerhetsprinciper är viktigt för organisationer som vill tillämpa säkerhetsprinciper för dataåtkomst. Dessa behörigheter beviljas genom att tilldela rollen RBAC grupper och program för ett visst område. Du kan använda [inbyggda RBAC-roller](../role-based-access-control/built-in-roles.md), till exempel lagring konto deltagare att tilldela behörigheter till användare.

Läs mer:

* [Azure Active Directory-rollbaserad åtkomstkontroll](../role-based-access-control/role-assignments-portal.md)

## <a name="delegated-access-to-storage-objects"></a>Delegerad åtkomst till lagringsobjekt
En signatur för delad åtkomst ger delegerad åtkomst till resurser i ditt lagringskonto. SAS innebär att du ger en klient begränsade behörigheter till objekt i ditt lagringskonto för en angiven period och på en viss uppsättning behörigheter. Du kan bevilja dessa begränsade behörigheter utan att behöva dela åtkomstnycklarna för ditt konto. 

SAS är en URI som omfattar all information som krävs för autentiserad åtkomst till en lagringsresurs i dess Frågeparametrar. För att komma åt lagringsresurser med SAS behöver klienten bara ange SAS till lämplig konstruktor eller metod.

Läs mer:

* [Förstå SAS-modellen](../storage/common/storage-dotnet-shared-access-signature-part-1.md)
* [Skapa och använda en SAS med Blob storage](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md)

## <a name="encryption-in-transit"></a>Kryptering under överföring
Kryptering under överföring är en mekanism för att skydda data när de skickas över nätverk. Du kan skydda data med Azure Storage med hjälp av:

* [Kryptering på transportnivå](../storage/common/storage-security-guide.md#encryption-in-transit), till exempel HTTPS, när du överför data till eller från Azure Storage.
* [Tråd kryptering](../storage/common/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), till exempel SMB 3.0-kryptering för Azure-filresurser.
* [Kryptering på klientsidan](../storage/common/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage), att kryptera data innan det överförs till Lagringskontot och att dekryptera data efter överföringen slut på minne.

Mer information om klientens kryptering:

* [Kryptering på klientsidan för Microsoft Azure Storage](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
* [Molnet säkerhetskontroller serien: kryptera Data under överföring](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>Vilande kryptering
I många organisationer [datakryptering i viloläge](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) är ett obligatoriskt steg mot data sekretess-, efterlevnads- och suveränitet. Tre funktioner som Azure tillhandahåller kryptering av data som är i vila:

* [Lagringstjänstens kryptering](../storage/common/storage-security-guide.md#encryption-at-rest) kan du begära att lagringstjänsten automatiskt kryptera data när du skriver till Azure Storage.
* [Kryptering på klientsidan](../storage/common/storage-security-guide.md#client-side-encryption) innehåller också en funktion till kryptering i vila.
* [Azure Disk Encryption](../storage/common/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) kan du kryptera OS-diskar och datadiskar som använder en virtuell IaaS-dator.

Mer information om Lagringskryptering för tjänsten:

* [Azure Storage Service-kryptering](https://azure.microsoft.com/services/storage/) är tillgänglig för [Azure Blob storage](https://azure.microsoft.com/services/storage/blobs/). Mer information om andra typer av Azure storage finns [Azure Files](https://azure.microsoft.com/services/storage/files/), [(Premium-lagring)](https://azure.microsoft.com/services/storage/premium-storage/), [tabell lagring](https://azure.microsoft.com/services/storage/tables/), och [kö lagring](https://azure.microsoft.com/services/storage/queues/).
* [Azure Storage Service-kryptering av vilande Data](../storage/common/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Azure Disk Encryption
Azure Disk Encryption för virtuella datorer hjälper dig att organisationens säkerhets- och efterlevnadskrav. Virtuella diskar (inklusive start- och datadiskar) krypteras med hjälp av nycklar och principer som du styr i [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Kryptering för virtuella datorer fungerar för Linux och Windows-operativsystem. Dessutom används Key Vault som hjälper dig att skydda, hantera och granska användning av krypteringsnycklarna disk. Alla data i VM-diskar är krypterad vilande genom att använda branschstandardiserad krypteringsteknik i Azure storage-konton. Disk Encryption lösningen för Windows är baserad på [Microsoft BitLocker-diskkryptering](https://technet.microsoft.com/library/cc732774.aspx), och Linux-lösning baserad på [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt).

Läs mer:

* [Azure Disk Encryption för Windows och Linux-IaaS-virtuella datorer](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="azure-key-vault"></a>Azure Key Vault
Azure Disk Encryption använder [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) som hjälper dig att styra och hantera disk krypteringsnycklar och hemligheter i nyckelvalvet-prenumeration. Det innebär också att krypteras alla data i virtuella diskar i vila i Azure Storage. Du bör använda Key Vault granska nycklar och användning av principen.

Läs mer:

* [Vad är Azure Key Vault?](../key-vault/key-vault-whatis.md)
* [Kom igång med Azure Key Vault](../key-vault/key-vault-get-started.md)
