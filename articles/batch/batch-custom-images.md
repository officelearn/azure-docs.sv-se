---
title: Etablera en anpassad pool från en hanterad avbildning – Azure Batch | Microsoft Docs
description: Skapa en batch-pool från en hanterad avbildnings resurs för att etablera Compute-noder med program vara och data för ditt program.
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.date: 09/16/2019
ms.author: labrenne
ms.openlocfilehash: 1ef6be2ba9364203dceba54ab51325c05dbbbe41
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020156"
---
# <a name="use-a-managed-image-to-create-a-pool-of-virtual-machines"></a>Använd en hanterad avbildning för att skapa en pool med virtuella datorer

Om du vill skapa en anpassad avbildning för batch-poolens virtuella datorer kan du antingen använda det [delade avbildnings galleriet](batch-sig-images.md)eller en *hanterad avbildnings* resurs.

> [!TIP]
> I de flesta fall bör du skapa anpassade avbildningar med hjälp av den delade avbildnings galleriet. Genom att använda det delade avbildnings galleriet kan du etablera pooler snabbare, skala större kvantiteter av virtuella datorer och förbättra tillförlitligheten vid etablering av virtuella datorer. Mer information finns i [använda galleriet för delade avbildningar för att skapa en anpassad pool](batch-sig-images.md).

## <a name="prerequisites"></a>Krav

- **En hanterad avbildnings resurs**. Om du vill skapa en pool med virtuella datorer med en anpassad avbildning måste du ha eller skapa en hanterad avbildnings resurs i samma Azure-prenumeration och region som batch-kontot. Avbildningen bör skapas från ögonblicks bilder av den virtuella datorns OS-disk och eventuellt anslutna data diskar. Mer information och steg för att förbereda en hanterad avbildning finns i följande avsnitt.
  - Använd en unik anpassad avbildning för varje pool som du skapar.
  - Om du vill skapa en pool med avbildningen med hjälp av batch-API: erna anger du **resurs-ID** för avbildningen, som har formen `/subscriptions/xxxx-xxxxxx-xxxxx-xxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage`. Använd **namnet** på bilden om du vill använda portalen.  
  - Den hanterade avbildnings resursen måste finnas för att poolens livstid ska kunna skalas och kan tas bort när poolen har tagits bort.

- **Autentisering med Azure Active Directory (AAD)** . Batch-klientens API måste använda AAD-autentisering. Azure Batch stöd för AAD dokumenteras i [autentisera batch service-lösningar med Active Directory](batch-aad-auth.md).

## <a name="prepare-a-custom-image"></a>Förbereda en anpassad avbildning

I Azure kan du förbereda en hanterad avbildning från:

- Ögonblicks bilder av en virtuell Azure-dators operativ system och data diskar
- En generaliserad virtuell Azure-dator med Managed disks
- En generaliserad lokal virtuell hård disk som laddats upp till molnet

För att skala batch-pooler på ett tillförlitligt sätt med en anpassad avbildning rekommenderar vi att du skapar en hanterad avbildning med *bara* den första metoden: använda ögonblicks bilder av den virtuella datorns diskar. Se följande steg för att förbereda en virtuell dator, ta en ögonblicks bild och skapa en avbildning från ögonblicks bilden.

### <a name="prepare-a-vm"></a>Förbered en virtuell dator

Om du skapar en ny virtuell dator för avbildningen använder du en Azure Marketplace-avbildning från första part som stöds av batch som bas avbildning för din hanterade avbildning. Endast första parts bilder kan användas som en bas avbildning. Om du vill få en fullständig lista över referens för Azure Marketplace-avbildningar som stöds av Azure Batch, se [noden List Node agent SKU: er](/java/api/com.microsoft.azure.batch.protocol.accounts.listnodeagentskus) .

> [!NOTE]
> Du kan inte använda en avbildning från tredje part som har ytterligare licens-och inköps villkor som bas avbildning. Information om dessa Marketplace-avbildningar finns i rikt linjerna för virtuella [Linux](../virtual-machines/linux/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) -eller [Windows](../virtual-machines/windows/cli-ps-findimage.md#deploy-an-image-with-marketplace-terms
) -datorer.

- Se till att den virtuella datorn har skapats med en hanterad disk. Det här är standard lagrings inställningen när du skapar en virtuell dator.
- Installera inte Azure-tillägg, till exempel tillägget för anpassat skript, på den virtuella datorn. Om avbildningen innehåller ett förinstallerat tillägg kan Azure drabbas av problem när du distribuerar batch-poolen.
- När du använder anslutna data diskar måste du montera och Formatera diskarna inifrån en virtuell dator för att använda dem.
- Kontrol lera att den grundläggande OS-avbildningen som du anger använder standard-Temp-enheten. Batch Node-agenten förväntar sig för närvarande standard-Temp-enheten.
- När den virtuella datorn är igång ansluter du till den via RDP (för Windows) eller SSH (för Linux). Installera nödvändig program vara eller kopiera önskade data.  

### <a name="create-a-vm-snapshot"></a>Skapa en ögonblicks bild av en virtuell dator

En ögonblicks bild är en fullständig skrivskyddad kopia av en virtuell hård disk. Om du vill skapa en ögonblicks bild av en virtuell dators operativ system eller data diskar kan du använda Azure Portal eller kommando rads verktygen. Steg och alternativ för att skapa en ögonblicks bild finns i rikt linjerna för virtuella [Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md) -eller [Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md) -datorer.

### <a name="create-an-image-from-one-or-more-snapshots"></a>Skapa en avbildning från en eller flera ögonblicks bilder

Om du vill skapa en hanterad avbildning från en ögonblicks bild använder du kommando rads verktyg i Azure, till exempel kommandot [AZ image Create](/cli/azure/image) . Du kan skapa en avbildning genom att ange en ögonblicks bild av en OS-disk och eventuellt en eller flera ögonblicks bilder av data diskar.

## <a name="create-a-pool-from-a-custom-image-in-the-portal"></a>Skapa en pool från en anpassad avbildning i portalen

När du har sparat din anpassade avbildning och du känner till resurs-ID eller namn skapar du en batch-pool från avbildningen. Följande steg visar hur du skapar en pool från Azure Portal.

> [!NOTE]
> Om du skapar poolen med någon av batch-API: erna kontrollerar du att identiteten som du använder för AAD-autentisering har behörighet till avbildnings resursen. Se [autentisera batch service-lösningar med Active Directory](batch-aad-auth.md).
>
> Resursen för den hanterade avbildningen måste finnas i poolens livstid. Om den underliggande resursen tas bort kan poolen inte skalas.

1. Navigera till ditt Batch-konto i Azure Portal. Det här kontot måste finnas i samma prenumeration och region som den resurs grupp som innehåller den anpassade avbildningen.
2. I fönstret **Inställningar** till vänster väljer du meny alternativet **pooler** .
3. I fönstret **pooler** väljer du kommandot **Lägg till** .
4. I fönstret **Lägg till pool** väljer du **anpassad avbildning (Linux/Windows)** från List rutan **avbildnings typ** . Välj avbildnings namnet (kort form för resurs-ID) i list rutan **anpassad VM-avbildning** .
5. Välj rätt **utgivare/erbjudande/SKU** för din anpassade avbildning.
6. Ange de återstående nödvändiga inställningarna, inklusive **nodens storlek**, **dedikerade noder**och **noder med låg prioritet**samt önskade valfria inställningar.

    Till exempel för en anpassad avbildning av Microsoft Windows Server Data Center 2016 visas fönstret **Lägg till pool** enligt nedan:

    ![Lägg till pool från anpassad Windows-avbildning](media/batch-custom-images/add-pool-custom-image.png)
  
Om du vill kontrol lera om en befintlig pool är baserad på en anpassad avbildning, se egenskapen **operativ system** i avsnittet resurs Sammanfattning i fönstret **pool** . Om poolen skapades från en anpassad avbildning, anges den som **anpassad VM-avbildning**.

Alla anpassade avbildningar som är associerade med en pool visas i poolens **egenskaps** fönster.

## <a name="considerations-for-large-pools"></a>Att tänka på för stora pooler

Om du planerar att skapa en pool med hundratals virtuella datorer eller mer med en anpassad avbildning, är det viktigt att följa föregående vägledning för att använda en avbildning som skapats från en ögonblicks bild av en virtuell dator.

Tänk också på följande:

- **Storleks gränser** – batch begränsar Poolens storlek till 2500 dedikerade datornoder, eller 1000 med låg prioritet när du använder en anpassad avbildning.

  Om du använder samma bild (eller flera avbildningar baserade på samma underliggande ögonblicks bild) för att skapa flera pooler får inte den totala beräknings noderna i poolerna överskrida de tidigare gränserna. Vi rekommenderar inte att du använder en bild eller dess underliggande ögonblicks bild för mer än en pool.

  Gränserna kan minskas om du konfigurerar poolen med [inkommande NAT-pooler](pool-endpoint-configuration.md).

- **Tids gräns för storleks ändring** – om poolen innehåller ett fast antal noder (inte Autoskala) ökar du resizeTimeout-egenskapen för poolen till ett värde som 20-30 minuter. Om din pool inte når mål storleken inom den angivna tids gränsen, kan du utföra en annan [ändrings åtgärd](/rest/api/batchservice/pool/resize).

  Om du planerar en pool med fler än 300 Compute-noder kan du behöva ändra storlek på poolen flera gånger för att nå mål storleken.
  
Genom att använda det [delade avbildnings galleriet](batch-sig-images.md)kan du skapa större pooler med dina anpassade avbildningar tillsammans med fler delade avbildnings repliker. Med hjälp av delade avbildningar kan den tid det tar för poolen att uppnå stabilt tillstånd vara upp till 25% snabbare och svars tiden för den virtuella datorn är upp till 30% kortare.

## <a name="considerations-for-using-packer"></a>Att tänka på när du använder Packer

Att skapa en hanterad avbildnings resurs direkt med Packer kan bara göras med batch-konton för användar prenumerations läge. För batch service mode-konton måste du först skapa en virtuell hård disk och sedan importera den virtuella hård disken till en hanterad avbildnings resurs. Dina steg för att skapa en hanterad avbildnings resurs varierar beroende på poolens tilldelnings läge (användar prenumeration eller batch-tjänst).

Se till att resursen som används för att skapa den hanterade avbildningen finns för livs längden för alla pooler som refererar till den anpassade avbildningen. Om du inte gör det kan det leda till allokeringsfel och/eller ändra storlek på felen.

Om avbildningen eller den underliggande resursen tas bort kan det hända att du får ett fel som liknar: `There was an error encountered while performing the last resize on the pool. Please try resizing the pool again. Code: AllocationFailed`. Om du får det här felet kontrollerar du att den underliggande resursen inte har tagits bort.

Mer information om hur du använder Packer för att skapa en virtuell dator finns i [skapa en Linux-avbildning med Packer](../virtual-machines/linux/build-image-with-packer.md) eller [skapa en Windows-avbildning med Packer](../virtual-machines/windows/build-image-with-packer.md).

## <a name="next-steps"></a>Nästa steg

En djupgående översikt över batch finns i [utveckla storskaliga parallella beräknings lösningar med batch](batch-api-basics.md).
