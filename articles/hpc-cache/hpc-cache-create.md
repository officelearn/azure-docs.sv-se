---
title: Skapa en Azure HPC-cache
description: Så här skapar du en Azure HPC cache-instans
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 06/01/2020
ms.author: v-erkel
ms.openlocfilehash: 894595ee3660532bf046a39e994fa669f7c6b002
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84434094"
---
# <a name="create-an-azure-hpc-cache"></a>Skapa en Azure HPC-cache

Använd Azure Portal för att skapa din cache.

![skärm bild av cache-översikten i Azure Portal med knappen Skapa längst ned](media/hpc-cache-home-page.png)

Klicka på bilden nedan om du vill titta på en [video demonstration](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) av hur du skapar en cache och lägger till ett lagrings mål.

[![video miniatyr: Azure HPC cache: Setup (Klicka för att besöka video sidan)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="define-basic-details"></a>Definiera grundläggande information

![skärm bild av sidan projekt information i Azure Portal](media/hpc-cache-create-basics.png)

I **projekt information**väljer du den prenumeration och resurs grupp som ska vara värd för cachen. Se till att prenumerationen finns på [åtkomst](hpc-cache-prereqs.md#azure-subscription) listan.

I **tjänst information**anger du cache-namn och följande attribut:

* Plats – Välj en av de [regioner som stöds](hpc-cache-overview.md#region-availability).
* Virtuellt nätverk – du kan välja ett befintligt namn eller skapa ett nytt virtuellt nätverk.
* Undernät – Välj eller skapa ett undernät med minst 64 IP-adresser (/24). Det här under nätet får endast användas för den här Azure HPC-instansen.

## <a name="set-cache-capacity"></a>Ange cache-kapacitet
<!-- referenced from GUI - update aka.ms link if you change this header text -->

På sidan **cache** måste du ange kapaciteten för din cache. Värdena som anges här avgör hur mycket data ditt cacheminne kan innehålla och hur snabbt det kan betjäna klient begär Anden.

Kapaciteten påverkar också cachens kostnad.

Välj kapacitet genom att ange följande två värden:

* Maximal data överförings hastighet för cachen (data flöde), i GB/SEK
* Mängden lagrings utrymme som allokerats för cachelagrade data i TB

Välj något av de tillgängliga data flödes värdena och cache Storage-storlekarna.

Tänk på att den faktiska data överförings hastigheten är beroende av arbets belastning, nätverks hastigheter och typen av lagrings mål. De värden du väljer ställer in maximalt data flöde för hela cache-systemet, men vissa av dem används för omkostnader. Om till exempel en klient begär en fil som inte redan finns lagrad i cacheminnet, eller om filen har marker ATS som inaktuell, använder cacheminnet en del av data flödet för att hämta den från backend-lagringen.

Azure HPC cache hanterar vilka filer som cachelagras och förinstalleras för att maximera träffar i cacheträffar. Innehållet i cachen utvärderas kontinuerligt och filer flyttas till långsiktig lagring när de inte används ofta. Välj en lagrings storlek för cachen som kan vara den aktiva uppsättningen arbetsfiler, plus ytterligare utrymme för metadata och andra kostnader.

![skärm bild av sidan cache-storlek](media/hpc-cache-create-capacity.png)

## <a name="enable-azure-key-vault-encryption-optional"></a>Aktivera Azure Key Vault kryptering (valfritt)

Om din cache finns i en region som stöder Kundhanterade krypterings nycklar visas sidan **disk krypterings nycklar** mellan flikarna **cache** och **taggar** . Vid publicerings tillfället stöds det här alternativet i östra USA, södra centrala USA och västra USA 2.

Om du vill hantera krypterings nycklarna som används med Cache-lagringen, anger du Azure Key Vault information på sidan **disk krypterings nycklar** . Nyckel valvet måste finnas i samma region och i samma prenumeration som cachen.

Du kan hoppa över det här avsnittet om du inte behöver Kundhanterade nycklar. Azure krypterar data med Microsoft-hanterade nycklar som standard. Läs mer i [Azure Storage-kryptering](../storage/common/storage-service-encryption.md) .

> [!NOTE]
>
> * Du kan inte ändra mellan Microsoft-hanterade nycklar och Kundhanterade nycklar när du har skapat cacheminnet.
> * När cachen har skapats måste du ge den åtkomst till nyckel valvet. Klicka på knappen **Aktivera kryptering** på sidan för cachens **Översikt** för att aktivera kryptering. Ta det här steget inom 90 minuter när du skapar cacheminnet.
> * Cache-diskar skapas efter den här auktoriseringen. Det innebär att tiden för att skapa första cache är kort, men cachen är inte redo att användas i tio minuter eller mer efter att du har auktoriserat åtkomst.

En fullständig förklaring av den kund hanterade nyckel krypterings processen finns i [använda Kundhanterade krypterings nycklar för Azure HPC cache](customer-keys.md).

![skärm bild av sidan med krypterings nycklar med valt kundhanterat och nyckel valv fält visas](media/create-encryption.png)

Välj **kund hanterat** för att välja kundhanterad nyckel kryptering. Fält för nyckel valvs specifikation visas. Välj den Azure Key Vault som du vill använda och välj sedan den nyckel och version som ska användas för cacheminnet. Nyckeln måste vara en 2048-bitars RSA-nyckel. Du kan skapa ett nytt nyckel valv, nyckel eller nyckel version från den här sidan.

När du har skapat cachen måste du godkänna att den använder Key Vault-tjänsten. Mer information finns [i auktorisera Azure Key Vault kryptering från cachen](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) .

## <a name="add-resource-tags-optional"></a>Lägg till resurs koder (valfritt)

På sidan **taggar** kan du lägga till [resurs Taggar](https://go.microsoft.com/fwlink/?linkid=873112) till Azure HPC-instansen.

## <a name="finish-creating-the-cache"></a>Slutför skapandet av cacheminnet

När du har konfigurerat den nya cachen klickar du på fliken **Granska + skapa** . Portalen verifierar dina val och gör att du kan granska dina val. Om allt stämmer klickar du på **skapa**.

Det tar cirka 10 minuter att skapa cacheminnet. Du kan följa förloppet i Azure Portal meddelande panel.

![skärm bild av sidorna för att skapa "distribution pågår" och "Notifications" i portalen](media/hpc-cache-deploy-status.png)

När en avisering har skapats visas ett meddelande med en länk till den nya Azure HPC cache-instansen och cachen visas i prenumerationens lista över **resurser** .

![skärm bild av Azure HPC cache-instansen i Azure Portal](media/hpc-cache-new-overview.png)

> [!NOTE]
> Om cachen använder Kundhanterade krypterings nycklar kan cachen visas i resurs listan innan distributionens status ändras till slutförd. Så snart cachens status **väntar på nyckel** kan du [ge den](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) behörighet att använda nyckel valvet.

## <a name="next-steps"></a>Nästa steg

När din cache visas i listan **resurser** kan du gå vidare till nästa steg.

* [Definiera lagrings mål](hpc-cache-add-storage.md) för att ge cache åtkomst till dina data källor.
* Om du använder Kundhanterade krypterings nycklar måste du [auktorisera Azure Key Vault kryptering](customer-keys.md#3-authorize-azure-key-vault-encryption-from-the-cache) från sidans översikts sida för att slutföra cache-installationen. Du måste göra det här steget innan du kan lägga till lagrings utrymme. Läs [Använd kund hanterade krypterings nycklar](customer-keys.md) för mer information.
