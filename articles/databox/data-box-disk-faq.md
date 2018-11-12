---
title: Microsoft Azure Data Box Disk – vanliga frågor | Microsoft Docs i data
description: Innehåller vanliga frågor och svar kring Azure Data Box Disk, en molnlösning som gör det möjligt att överföra stora mängder data till Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: overview
ms.date: 10/30/2018
ms.author: alkohli
ms.openlocfilehash: 5e4461a910f13c5047c4a2aadd7326c4e12c533f
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238139"
---
# <a name="what-is-azure-data-box-disk-preview"></a>Vad är Azure Data Box Disk? (Förhandsversion)

Med Microsoft Azure Data Box Disk-lösningen kan du skicka terabyte med lokala data till Azure på ett snabbt, kostnadseffektivt och tillförlitligt sätt. Här hittar du vanliga frågor och svar om att använda Data Box-diskar i Azure-portalen. 

Frågor och svar är ordnade i följande kategorier:

- Om tjänsten
- Konfigurera och ansluta 
- Spåra status
- Migrera data 
- Verifiera och ladda upp data 

> [!IMPORTANT]
> Data Box Disk är tillgängligt som förhandsversion. Granska [Azures användningsvillkor för förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innan du distribuerar den här lösningen.

## <a name="about-the-service"></a>Om tjänsten

### <a name="q-what-is-azure-data-box-service"></a>F. Vad är Azure Data Box-tjänsten? 
A.  Azure Data Box-tjänsten är utformad för offlineinmatning av data. Tjänsten hanterar en rad produkter som alla har skräddarsytts för dataöverföring för olika lagringskapaciteter. 

### <a name="q-what-are-azure-data-box-disks"></a>F. Vad är Azure Data Box-diskar?
A. Azure Data Box-diskar möjliggör snabb, prisvärd och säker överföring av terabytes data till och från Azure. Microsoft skickar 1 till 5 diskar till dig, med en maximal lagringskapacitet på 35 TB. Du kan enkelt konfigurera, ansluta och låsa upp diskarna via Data Box-tjänsten i Azure-portalen.  

Diskarna krypteras med hjälp av Microsoft BitLocker-enhetskryptering, och dina krypteringsnycklar hanteras på Azure-portalen. Du kan sedan kopiera data från kundens servrar. I datacentret migrerar Microsoft dina data från enheten till molnet via en snabb, privat nätverksöverföringslänk. De överförs till Azure.

### <a name="q-when-should-i-use-data-box-disks"></a>F. När ska jag använda Data Box-diskar?
A. Om du har högst 40 TB data som du vill överföra till Azure kan du använda Data Box-diskar.

### <a name="q-what-is-the-price-of-data-box-disks"></a>F. Hur mycket kostar Data Box-diskarna?
A. Information om priset för Data Box-diskar finns på [sidan med priser för förhandsversion](https://azure.microsoft.com/pricing/details/storage/databox/disk/).

### <a name="q-how-do-i-get-data-box-disks"></a>F. Hur får jag Data Box-diskarna? 
A.  Om du vill ha Azure Data Box-diskar registrerar du dig för [Data Box-diskförhandsversionen](https://aka.ms/AzureDataBox). Logga sedan in på Azure-portalen och skapa en Data Box-diskbeställning. Ange din kontakt- och aviseringsinformation. Beroende på tillgängligheten skickas diskarna till dig inom tio dagar.   

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>F. Vad är den maximala mängden data kan överföra med Data Box-diskar?
A. Om du får fem diskar om vardera 8 TB (7 TB användbar kapacitet) är den maximala användbara kapaciteten 35 TB. Du kan då överföra 35 TB data.  Om du vill överföra en större datamängd måste du beställa fler diskar.

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>F. Hur ser jag om Data Box-diskar finns tillgängliga i min region? 
A.  Data Box Disks är tillgängligt i USA, Kanada, Australien och i alla EU-länder under förhandsversionsfasen.  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>F. I vilka regioner kan jag lagra data med Data Box-diskar?
A. Data Box Disk kan användas i alla regioner i USA, Kanada, Australien, Europa västra och Europa norra under förhandsversionen. Endast regionerna med offentligt Azure-moln stöds. Azure Government och andra begränsade moln stöds inte.

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>F. Vem kontaktar jag om jag får problem med Data Box-diskarna?
A. Om du får problem med Data Box-diskarna kan du kontakta [Data Box-supporten](mailto:expresspodsupport@microsoft.com).

## <a name="configure-and-connect"></a>Konfigurera och ansluta
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>F. Kan jag ange antal Data Box-diskar i beställningen?
A.  Nej. Du får diskar på 8 TB (högst fem diskar) beroende på datastorlek och tillgänglighet.  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>F. Hur låser jag upp Data Box-diskarna? 
A.  Öppna Azure-portalen och öppna din Data Box-diskbeställning. Navigera till **Enhetsinformation**. Kopiera supportnyckeln. Ladda ned och extrahera upplåsningsverktyget för Data Box Disk från Azure-portalen för ditt operativsystem. Kör verktyget på den dator som har de data du vill kopiera till diskarna. Ange nyckeln för att låsa upp diskarna. Samma nyckel låser upp alla diskar. 

Stegvisa instruktioner finns i [Låsa upp diskar på en Windows-klient](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) eller [Låsa upp diskar på en Linux-klient](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>F. Kan jag använda en Linux-dator som värd och ansluta och kopiera data till Data Box-diskar?
A.  Ja. Både Linux-klienten och Windows-klienten kan användas till att ansluta och kopiera data på Data Box-diskarna. Mer information finns i listan över [operativsystem som stöds](data-box-disk-system-requirements.md) för värddatorn.

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>F. Mina diskar har skickats men jag vill avbryta beställningen. Varför kan jag inte trycka på knappen och avbryta?
A.  Det går bara att avbryta redan beställda diskar innan de skickas till dig. När diskarna har skickats iväg går det inte att avbryta beställningen. Under förhandsversionen kan du skicka tillbaka diskarna utan kostnad. När lösningen i framtiden blir allmänt tillgängligt läggs förmodligen en avgift på. 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>F. Kan jag ansluta flera Data Box-diskar samtidigt till värddatorn och överföra data?
A. Ja. Du kan ansluta flera Data Box-diskar till samma värddator och överföra data, och du kan köra flera kopieringsjobb parallellt.

## <a name="track-status"></a>Spåra status

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>F. Hur gör jag för att spåra diskarna, från beställningen till det att jag skickar tillbaka dem? 
A.  Du kan spåra statusen för Data Box Disk-beställningen i Azure-portalen. När du skapar beställningen blir du ombedd att ange en e-postadress dit aviseringar kan skickas. Om du angav en sådan e-postadress meddelas du om alla statusändringar för beställningen. Mer information om hur du [konfigurerar e-postaviseringar](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-disks"></a>F. Hur gör jag för att skicka tillbaka diskarna? 
A.  Microsoft skickar med en adressetikett i paketet med Data Box-diskarna. Fäst etiketten på leveranskartongen och lämna in det förseglade paketet hos transportföretaget. Om etiketten är skadad eller har försvunnit kan du hämta en ny via **Översikt > Ladda ned adressetikett**.

## <a name="migrate-data"></a>Migrera data

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>F. Hur stor mängd data får plats på Data Box-diskarna?  
A.  Du kan få upp till 5 diskar med en användningsbar kapacitet på totalt 35 TB. Diskarna är 8 TB (användbart utrymme 7 TB). 

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>F. Vilken är den största blockblob- och sidblobstorleken som stöds av Data Box Disks? 
A.  De maximala storlekarna regleras av begränsningarna för Azure Storage. Den maximala blockbloben är ungefär 4,768 TiB och den maximala sidblobstorleken är 8 TiB. Mer information finns i [Skalbarhets- och prestandamål i Azure Storage](../storage/common/storage-scalability-targets.md). 

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>F. Vilken är dataöverföringshastigheten för Data Box-diskar?
A. Vid tester med diskar ansluta via USB 3.0 var diskprestandan upp till 430 MB/s. De faktiska hastigheterna varierar beroende på filstorleken. För mindre filer kan du se lägre prestanda.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>F. Hur vet jag att mina data är säkra under överföringen? 
A.  Data Box-diskarna krypteras med BitLocker-AES-128-bitarskryptering och nyckeln är endast tillgänglig i Azure-portalen. Logga in på Azure-portalen med autentiseringsuppgifterna och hämta nyckeln. Ange den här nyckeln när du kör upplåsningsverktyget för Data Box Disk.

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>F. Hur gör jag för att kopiera data till Data Box-diskarna? 
A.  Använd ett SMB-verktyg som Robocopy eller Diskboss. Du kan till och med dra och släppa i Windows för att kopiera data till diskarna. 

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>F. Finns det några tips för att påskynda datakopieringen?
A.  Påskynda kopieringsprocessen:

- Använd flera dataströmmar. Med till exempel Robocopy kan du använda flertrådsalternativet. Mer information om de exakta kommandon som används finns i [Självstudie: Kopiera data till en Azure Data Box-disk och verifiera](data-box-disk-deploy-copy-data.md#copy-data-to-disks).
- Använda flera sessioner.
- Kontrollera att dina data finns lokalt på datorn som diskarna är ansluta till, i stället för att kopiera via nätverk (där du kan begränsas av nätverkshastigheterna).
- Se till att du använder USB 3.0 eller senare under kopieringen. Ladda ned och använda [USBView-verktyget](https://docs.microsoft.com/windows-hardware/drivers/debugger/usbview) för att identifiera USB-kontrollanterna och USB-enheterna som är anslutna till datorn.
- Analysera prestanda för den dator som används för att kopiera data. Ladda ned och använd [Bluestop FIO-verktyget](https://bluestop.org/fio/) för att analysera servermaskinvarans prestanda.

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>F. Hur skyndar jag på dataöverföringen om källdata består av små filer (KB eller få MB)?
A.  Påskynda kopieringsprocessen:

- Skapa en lokal VHDx på snabb lagring eller skapa en tom virtuell hårddisk på HDD/SSD (långsammare).
- Montera på en virtuell dator.
- Kopiera filer till den virtuella datorns disk.


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>F. Kan jag använda flera lagringskonton med Data Box-diskar?
A.  Nej. För närvarande går det endast att använda ett lagringskonto, allmänt eller klassiskt, med Data Box-diskar. Det går att använda både högfrekvent och lågfrekvent blob. I förhandsversionen stöds endast lagringskonton det offentliga Azure-molnet i USA och i västra och norra Europa.

## <a name="verify-and-upload"></a>Verifiera och ladda upp

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>F. Hur snart kommer jag åt mina data i Azure när jag har skickat tillbaka diskarna? 
A.  När orderstatusen för datakopieringen visas som slutförd bör du komma åt dina data direkt.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>F. Var i Azure finns mina data efter uppladdningen?
A.  När du kopierar data under mapparna *BlockBlob* och *PageBlob* på disken skapas en container i Azure-lagringskontot för varje undermapp under mappen *BlockBlob* och *PageBlob*. Om du kopierade filerna under mapparna *BlockBlob* och *PageBlob* direkt kopieras de till en standardcontainer *$root* under Azure Storage-kontot. 

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>F. Jag har precis märkt att jag inte följde namngivningskraven för min container. Misslyckas dataöverföringen till Azure?
A. Versaler omvandlas automatiskt till gemener. Om namn inte efterlever kraven på andra sätt (specialtecken, andra språk och så vidare) misslyckas uppladdningen. Mer information finns i [Azure-namngivningskonventioner](data-box-disk-limits.md#azure-block-blob-and-page-blob-naming-conventions).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>F. Hur verifierar jag de data jag kopierade till flera Data Box-diskar?
A.  När kopieringen är klar kan du köra `DataBoxDiskValidation.cmd` i mappen *DataBoxDiskImport* för att generera kontrollsummor för verifiering. Om du har flera diskar behöver du öppna ett kommandofönster per disk och kör det här kommandot. Tänk på att den här åtgärden kan ta lång tid (~ timmar) beroende på storleken på dina data.

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>F. Vad händer med mina data när jag har skickat tillbaka diskarna?
A.  När datakopieringen till Azure är klar raderas alla data på disken på ett säkert sätt enligt riktlinjerna NIST SP 800-88 Revision 1.  

### <a name="q-how-is-my-data-protected-during-transit"></a>F. Hur skyddade är mina data under transporten? 
A.  Data Box-diskarna krypteras med AES-128 Microsoft BitLocker-kryptering. Alla diskar och data låses upp med en unik nyckel.

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>F. Måste jag köra validering av kontrollsumma om jag lägger till mer data på Data Box-diskarna?
A. Ja. Om du vill verifiera dina data (vi rekommenderar att du gör det!), måste du köra verifieringen om du har lagt till ytterligare data på diskarna.

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>F. Jag har använt samtliga diskar och behöver beställa fler diskar. Går det att beställa dem snabbt?
A. Du kan klona din tidigare beställning. En kloning skapar samma ordning som tidigare och du kan redigera beställningsinformationen utan att behöva ange adress, kontaktuppgifter och aviseringsadress. 



## <a name="next-steps"></a>Nästa steg

- Titta på [systemkraven för Data Box](data-box-disk-system-requirements.md).
- Förstå [Data Box-begränsningarna](data-box-disk-limits.md).
- Distribuera snabbt [Azure Data Box Disk](data-box-disk-quickstart-portal.md) på Azure-portalen.
