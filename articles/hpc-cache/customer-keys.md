---
title: Använda kundbedömda nycklar för att kryptera data i Azure HPC-cache
description: Så här använder du Azure Key Vault med Azure HPC-cache för att styra åtkomsten till krypteringsnyckel i stället för att använda standardkrypteringsnycklarna för Microsoft
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: v-erkel
ms.openlocfilehash: a31979763dd1ab5d8f289deef0e30cce27bb0df4
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81538874"
---
# <a name="use-customer-managed-encryption-keys-for-azure-hpc-cache"></a>Använda kundhanterade krypteringsnycklar för Azure HPC-cache

Du kan använda Azure Key Vault för att styra ägarskapet av de nycklar som används för att kryptera dina data i Azure HPC-cache. I den här artikeln beskrivs hur du använder kundhanterade nycklar för cachedatakryptering.

> [!NOTE]
> Alla data som lagras i Azure, även på cachediskarna, krypteras i vila med Microsoft-hanterade nycklar som standard. Du behöver bara följa stegen i den här artikeln om du vill hantera nycklarna som används för att kryptera dina data.

Den här funktionen är endast tillgänglig i dessa Azure-regioner:

* USA, östra
* USA, södra centrala
* USA, västra 2

Det finns tre steg för att aktivera kundhanterad nyckelkryptering för Azure HPC-cache:

1. Konfigurera ett Azure Key Vault för att lagra nycklarna.
1. När du skapar Azure HPC-cachen väljer du kundhanterad nyckelkryptering och anger nyckelvalvet och nyckeln som ska användas.
1. När cacheminnet har skapats auktoriserar du den för att komma åt nyckelvalvet.

Kryptering är inte helt konfigurerad förrän du har auktoriserat den från den nyskapade cachen (steg 3). Detta beror på att du måste skicka cachens identitet till nyckelvalvet för att göra den till en behörig användare. Du kan inte göra detta innan du skapar cacheminnet, eftersom identiteten inte finns förrän cacheminnet har skapats.

När du har skapat cacheminnet kan du inte ändra mellan kundhanterade nycklar och Microsoft-hanterade nycklar. Men om cacheminnet använder kundhanterade nycklar kan du [ändra](#update-key-settings) krypteringsnyckeln, nyckelversionen och nyckelvalvet efter behov.

## <a name="understand-key-vault-and-key-requirements"></a>Förstå viktiga valv och viktiga krav

Nyckelvalvet och nyckeln måste uppfylla dessa krav för att arbeta med Azure HPC-cache.

Egenskaper för nyckelvalv:

* **Prenumeration** - Använd samma prenumeration som används för cachen.
* **Region** - Nyckelvalvet måste finnas i samma region som Azure HPC-cachen.
* **Prisnivå** - Standardnivån är tillräcklig för användning med Azure HPC-cache.
* **Mjuk borttagning** - Azure HPC-cache aktiverar mjuk borttagning om den inte redan är konfigurerad i nyckelvalvet.
* **Rensa skydd** - Rensningsskydd måste aktiveras.
* **Åtkomstprincip** - Standardinställningarna är tillräckliga.
* **Nätverksanslutning** – Azure HPC-cache måste kunna komma åt nyckelvalvet oavsett vilka slutpunktsinställningar du väljer.

Viktiga egenskaper:

* **Nyckeltyp** - RSA
* **RSA-nyckelstorlek** - 2048
* **Aktiverad** - Ja

Behörigheter för nyckelvalv:

* Användaren som skapar Azure HPC-cachen måste ha behörigheter som motsvarar [rollen Nyckelvalvsdeltagare](../role-based-access-control/built-in-roles.md#key-vault-contributor). Samma behörigheter behövs för att konfigurera och hantera Azure Key Vault.

  Läs [Säker åtkomst till ett nyckelvalv](../key-vault/key-vault-secure-your-key-vault.md) för mer information.

## <a name="1-set-up-azure-key-vault"></a>1. Konfigurera Azure Key Vault

Du kan ställa in ett nyckelvalv och en nyckel innan du skapar cacheminnet eller göra det som en del av skapandet av cacheminnet. Se till att dessa resurser uppfyller de krav som beskrivs [ovan](#understand-key-vault-and-key-requirements).

När du skapar cacheminnet måste du ange ett valv, en nyckel och en nyckelversion som ska användas för cachens kryptering.

Mer information finns i dokumentationen till [Azure Key Vault.](../key-vault/key-vault-overview.md)

> [!NOTE]
> Azure Key Vault måste använda samma prenumeration och vara i samma region som Azure HPC-cachen. Använd en av de regioner som stöds i början av den här artikeln.

## <a name="2-create-the-cache-with-customer-managed-keys-enabled"></a>2. Skapa cacheminnet med kundhanterade nycklar aktiverade

Du måste ange krypteringsnyckelkällan när du skapar din Azure HPC-cache. Följ instruktionerna i [Skapa en Azure HPC-cache](hpc-cache-create.md)och ange nyckelvalvet och nyckeln på sidan **Diskkrypteringsnycklar.** Du kan skapa ett nytt nyckelvalv och nyckel när cache skapas.

> [!TIP]
> Om sidan **Diskkrypteringsnycklar** inte visas kontrollerar du att cachen finns i något av de områden som stöds.

Användaren som skapar cachen måste ha privilegier som är lika med [rollen Nyckelvalvsdeltagare](../role-based-access-control/built-in-roles.md#key-vault-contributor) eller högre.

1. Klicka på knappen om du vill aktivera privathanterade nycklar. När du har ändrat den här inställningen visas inställningarna för nyckelvalvet.

1. Klicka på **Välj ett nyckelvalv** för att öppna nyckelmarkeringssidan. Välj eller skapa nyckelvalvet och nyckeln för att kryptera data på cachens diskar.

   Om ditt Azure Key Vault inte visas i listan kontrollerar du följande krav:

   * Är cachen i samma prenumeration som nyckelvalvet?
   * Är cacheminnet i samma region som nyckelvalvet?
   * Finns det nätverksanslutning mellan Azure-portalen och nyckelvalvet?

1. När du har valt ett valv väljer du den enskilda nyckeln från de tillgängliga alternativen eller skapar en ny nyckel. Nyckeln måste vara en 2048-bitars RSA-nyckel.

1. Ange versionen för den valda nyckeln. Läs mer om versionshantering i [Azure Key Vault-dokumentationen](../key-vault/about-keys-secrets-and-certificates.md#objects-identifiers-and-versioning).

Fortsätt med resten av specifikationerna och skapa cacheminnet enligt beskrivningen i [Skapa en Azure HPC-cache](hpc-cache-create.md).

## <a name="3-authorize-azure-key-vault-encryption-from-the-cache"></a>3. Auktorisera Azure Key Vault-kryptering från cacheminnet
<!-- header is linked from create article, update if changed -->

Efter några minuter visas den nya Azure HPC-cachen i din Azure-portal. Gå till sidan **Översikt** för att auktorisera den för åtkomst till ditt Azure Key Vault och aktivera kundhanterad nyckelkryptering. (Cacheminnet kan visas i resurslistan innan "distributionen pågår"-meddelandena rensas.)

Den här tvåstegsprocessen är nödvändig eftersom Azure HPC-cacheinstansen behöver en identitet för att gå till Azure Key Vault för auktorisering. Cacheidentiteten finns inte förrän stegen för den första skapelsen har slutförts.

> [!NOTE]
> Du måste auktorisera kryptering inom 90 minuter efter att du har skapat cacheminnet. Om du inte slutför det här steget tar cachen time out och misslyckas. En misslyckad cache måste återskapas, den kan inte åtgärdas.

Cachen visar statusen **Väntar på nyckeln**. Klicka på knappen **Aktivera kryptering** högst upp på sidan om du vill att cachen ska få åtkomst till det angivna nyckelvalvet.

![skärmbild av cacheöversiktssidan i portalen, med markering på knappen Aktivera kryptering (översta raden) och Status: Väntar på nyckel](media/waiting-for-key.png)

Klicka på **Aktivera kryptering** och sedan på **ja-knappen** för att auktorisera cachen för att använda krypteringsnyckeln. Den här åtgärden möjliggör också mjuk-borttagning och rensning skydd (om inte redan aktiverat) på nyckeln valv.

![skärmbild av cacheöversiktssidan i portalen, med ett bannermeddelande högst upp som ber användaren att aktivera kryptering genom att klicka på ja](media/enable-keyvault.png)

När cachebegäranden har åtkomst till nyckelvalvet kan den skapa och kryptera diskarna som lagrar cachelagrade data.

När du har auktoriserat kryptering går Azure HPC-cachen igenom ytterligare flera minuter för installation för att skapa krypterade diskar och relaterad infrastruktur.

## <a name="update-key-settings"></a>Uppdatera viktiga inställningar

Du kan ändra nyckelvalvet, nyckeln eller nyckelversionen för cacheminnet från Azure-portalen. Klicka på länken **Krypteringsinställningar** i cacheminnet för att öppna sidan **Inställningar för kundnyckel.** (Du kan inte ändra en cache mellan kundhanterade nycklar och systemhanterade nycklar.)

![skärmbild av sidan "Inställningar för kundnycklar" som nås genom att klicka på Inställningar > Kryptering från cachesidan i Azure-portalen](media/change-key-click.png)

Klicka på länken **Ändra nyckel** och sedan på **Ändra nyckelvalv, nyckel eller version** för att öppna nyckelväljaren.

![skärmbild av sidan "Välj nyckel från Azure Key Vault" med tre listruta för att välja nyckelvalv, nyckel och version](media/select-new-key.png)

Nyckelvalv i samma prenumeration och samma region som den här cachen visas i listan.

När du har valt de nya krypteringsnyckelvärdena klickar du på **Välj**. En bekräftelsesida visas med de nya värdena. Klicka på **Spara** om du vill slutföra markeringen.

![skärmbild av bekräftelsesida med knappen Spara längst upp till vänster](media/save-key-settings.png)

## <a name="read-more-about-customer-managed-keys-in-azure"></a>Läs mer om kundhanterade nycklar i Azure

I de här artiklarna beskrivs mer om hur du använder Azure Key Vault och kundhanterade nycklar för att kryptera data i Azure:

* [Översikt över Azure-lagringskryptering](../storage/common/storage-service-encryption.md)
* [Diskkryptering med kundhanterade nycklar](../virtual-machines/linux/disk-encryption.md#customer-managed-keys) - Dokumentation för användning av Azure Key Vault och hanterade diskar, som liknar den process som används med Azure HPC-cache

## <a name="next-steps"></a>Nästa steg

När du har skapat Azure HPC-cachen och auktoriserad Key Vault-baserad kryptering fortsätter du att konfigurera cacheminnet genom att ge den åtkomst till dina datakällor.

* [Lägga till lagringsmål](hpc-cache-add-storage.md)
