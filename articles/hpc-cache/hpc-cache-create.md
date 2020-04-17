---
title: Skapa en Azure HPC-cache
description: Skapa en Azure HPC-cacheinstans
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: befbe2435a518b82cf5a3ab12e6129aa3ce5c22b
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537990"
---
# <a name="create-an-azure-hpc-cache"></a>Skapa en Azure HPC-cache

Använd Azure-portalen för att skapa din cache.

![skärmbild av cacheöversikt i Azure-portalen, med knappen Skapa längst ned](media/hpc-cache-home-page.png)

## <a name="define-basic-details"></a>Definiera grundläggande information

![skärmbild av sidan projektinformation i Azure-portalen](media/hpc-cache-create-basics.png)

Välj den prenumeration och resursgrupp som ska vara värd för cacheminnet i **Project Details.**

I **Serviceinformation**anger du cachenamnet och dessa andra attribut:

* Plats - Välj en av de [regioner som stöds](hpc-cache-overview.md#region-availability).
* Virtuellt nätverk - Du kan välja ett befintligt eller skapa ett nytt virtuellt nätverk.
* Undernät - Välj eller skapa ett undernät med minst 64 IP-adresser (/24) som endast ska användas för den här Azure HPC-cacheinstansen.

## <a name="set-cache-capacity"></a>Ange cachekapacitet
<!-- referenced from GUI - update aka.ms link if you change this header text -->

På **sidan Cache** måste du ange cachens kapacitet. De värden som anges här avgör hur mycket data cachen kan innehålla och hur snabbt den kan betjäna klientbegäranden.

Kapaciteten påverkar också cachens kostnad.

Välj kapacitet genom att ange dessa två värden:

* Den maximala dataöverföringshastigheten för cacheminnet (dataflödet), i GB/sekund
* Mängden lagringsutrymme som allokerats för cachelagrade data, i TB

Välj ett av de tillgängliga dataflödesvärdena och cachelagringsstorlekarna.

Tänk på att den faktiska dataöverföringshastigheten beror på arbetsbelastning, nätverkshastigheter och typ av lagringsmål. De värden du väljer anger maximalt dataflöde för hela cachesystemet, men en del av detta används för omkostnader. Om en klient till exempel begär en fil som inte redan lagras i cacheminnet, eller om filen är markerad som inaktuell, använder cachen en del av dataflödet för att hämta den från backend-lagring.

Azure HPC Cache hanterar vilka filer som cachelagras och förinstalleras för att maximera cache träfffrekvensen. Cacheinnehållet utvärderas kontinuerligt och filer flyttas till långsiktig lagring när de används mindre ofta. Välj en lagringsstorlek för cacheminnet som bekvämt kan innehålla den aktiva uppsättningen arbetsfiler med extra utrymme för metadata och andra omkostnader.

![skärmbild av cachestorlekssidan](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Aktivera Kryptering av Azure Key Vault (valfritt)

Om cacheminnet finns i en region som stöder kundhanterade krypteringsnycklar visas sidan **Diskkrypteringsnycklar** mellan flikarna **Cache** och **Taggar.** Från och med publiceringstiden stöds det här alternativet i östra USA, södra centrala USA och västra US 2.

Om du vill hantera krypteringsnycklarna som används med cachelagringen anger du informationen om Azure Key Vault på **diskkrypteringsnyckelsidan.** Nyckelvalvet måste finnas i samma region och i samma prenumeration som cacheminnet.

Du kan hoppa över det här avsnittet om du inte behöver kundhanterade nycklar. Azure krypterar data med Microsoft-hanterade nycklar som standard. Läs [Azure-lagringskryptering](../storage/common/storage-service-encryption.md) om du vill veta mer.

> [!NOTE]
>
> * Du kan inte ändra mellan Microsoft-hanterade nycklar och kundhanterade nycklar när du har skapat cacheminnet.
> * När cacheminnet har skapats måste du auktorisera den för att komma åt nyckelvalvet. Klicka på knappen **Aktivera kryptering** på sidan **Översikt** för cacheminnet för att aktivera kryptering. Ta det här steget inom 90 minuter efter att du har skapat cacheminnet.
> * Cachediskar skapas efter den här auktoriseringen. Det innebär att den första cachetiden är kort, men cacheminnet är inte redo att användas på tio minuter eller mer efter att du har auktoriserat åtkomst.

En fullständig förklaring av krypteringsprocessen för kundhanterad nyckel finns i [Använd kundhanterade krypteringsnycklar för Azure HPC-cache](customer-keys.md).

![skärmbild av klientnycklar med "kundhanterade" valda och nyckelvalvsfält som visar](media/create-encryption.png)

Välj **Kundhanterad** för att välja kundhanterad nyckelkryptering. De viktigaste valvspecifikationsfälten visas. Välj det Azure Key Vault som ska användas och välj sedan den nyckel och version som ska användas för den här cachen. Nyckeln måste vara en 2048-bitars RSA-nyckel. Du kan skapa ett nytt nyckelvalv, nyckel eller nyckelversion från den här sidan.

När du har skapat cacheminnet måste du auktorisera den för att använda nyckelvalvstjänsten. Läs [Auktorisera Azure Key Vault-kryptering från cacheminnet](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) för mer information.

## <a name="add-resource-tags-optional"></a>Lägga till resurstaggar (valfritt)

På sidan **Taggar** kan du lägga till [resurstaggar](https://go.microsoft.com/fwlink/?linkid=873112) i din Azure HPC-cacheinstans.

## <a name="finish-creating-the-cache"></a>Slutför skapandet av cacheminnet

När du har konfigurerat den nya cachen klickar du på fliken **Granska + skapa.** Portalen validerar dina val och låter dig granska dina val. Om allt är korrekt klickar du på **Skapa**.

Cache skapande tar ca 10 minuter. Du kan spåra förloppet i Azure-portalens meddelandepanel.

![skärmbild av cacheskapande "distribution pågår" och "meddelanden" sidor i portalen](media/hpc-cache-deploy-status.png)

När skapandet är klart visas ett meddelande med en länk till den nya Azure HPC-cacheinstansen och cachen visas i prenumerationens **resurslista.**

![skärmbild av Azure HPC Cache-instans i Azure-portalen](media/hpc-cache-new-overview.png)

> [!NOTE]
> Om cachen använder kundhanterade krypteringsnycklar kan cachen visas i resurslistan innan distributionsstatusen ändras för att slutföras. Så fort cachens status väntar **på nyckel** kan du [auktorisera](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) den för att använda nyckelvalvet.

## <a name="next-steps"></a>Nästa steg

När cacheminnet har visas i listan **Resurser** kan du gå vidare till nästa steg.

* [Definiera lagringsmål](hpc-cache-add-storage.md) för att ge cacheåtkomst till dina datakällor.
* Om du använder kundhanterade krypteringsnycklar måste du [auktorisera Azure Key Vault-kryptering](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) från cachens översiktssida för att slutföra cachekonfigurationen. Du måste göra det här steget innan du kan lägga till lagringsutrymme. Läs [Använd kundhanterade krypteringsnycklar](customer-keys.md) för mer information.
