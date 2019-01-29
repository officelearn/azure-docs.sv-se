---
title: Vanliga frågor och svar om Microsoft Azure Data Box | Microsoft Docs i data
description: Innehåller vanliga frågor och svar om Azure Data Box, en molnlösning som gör det möjligt att överföra stora mängder data till Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 01/16/2019
ms.author: alkohli
ms.openlocfilehash: ad52427263c4bbd2effb373b00ef8ff37b8f5b67
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54449579"
---
# <a name="azure-data-box-frequently-asked-questions"></a>Azure Data Box: Vanliga frågor och svar

Med Microsoft Azure Data Box-hybridlösningen kan du skicka flera terabyte data till Azure på ett snabbt, prisvärt och tillförlitligt sätt med hjälp av en överföringsenhet. Här hittar du vanliga frågor och svar om hur du använder Data Box på Azure-portalen. 

Frågor och svar är ordnade i följande kategorier:

- Om tjänsten
- Beställa en enhet
- Konfigurera och ansluta 
- Spåra status
- Kopiera data 
- Skicka en enhet
- Verifiera och ladda upp data 
- Spårbarhetssupport

## <a name="about-the-service"></a>Om tjänsten

### <a name="q-what-is-azure-data-box-service"></a>F. Vad är Azure Data Box-tjänsten? 
A.  Azure Data Box-tjänsten är utformad för offlineinmatning av data. Den här tjänsten hanterar en rad produkter som alla har skräddarsytts för dataöverföring för olika lagringskapaciteter. 

### <a name="q-what-is-azure-data-box"></a>F. Vad är Azure Data Box?
A. Azure Data Box möjliggör snabb, prisvärd och säker överföring av flera terabytes data till och från Azure. Du beställer Data Box-enheten via Azure-portalen. Microsoft levererar en lagringsenhet med en användbar kapacitet på 80 TB via ett regionalt transportföretag. 

När enheten har tagits emot konfigurerar du den snabbt med hjälp av det lokala webbgränssnittet. Kopiera data från dina servrar till enheten och skicka sedan tillbaka enheten till Azure. I Azure-datacentret överförs dina data automatiskt från enheten till Azure. Hela processen spåras från slutpunkt till slutpunkt av Data Box-tjänsten på Azure-portalen.

### <a name="q-when-should-i-use-data-box"></a>F. När ska jag använda Data Box?
A. Om du har mellan 40 och 500 TB data som du vill överföra till Azure kan du använda Data Box. För datastorlekar som är mindre än 40 TB använder du Data Box Disk och för datastorlekar över 500 TB använder du Data Box Heavy.

### <a name="q-what-is-the-price-of-data-box"></a>F. Hur mycket kostar Data Box?
A. Data Box-tjänsten är tillgänglig mot en nominell kostnad i tio dagar. Kostnaden för enheten visas när du väljer produktmodellen när du skapar din beställning på Azure-portalen. Leveransen kostar inte heller någonting, men vi tar ut avgifter för Azure-lagringen. Mer information finns på [prissidan för Azure Data Box](https://azure.microsoft.com/pricing/details/storage/databox/). 

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-in-one-instance"></a>F. Vad är den maximala mängden data som jag kan överföra samtidigt med Data Box?
A. Data Box har en råkapacitet på 100 TB och en användbar kapacitet på 80 TB. Du kan överföra upp till 80 TB data med Data Box. Om du vill överföra en större datamängd måste du beställa fler enheter.

### <a name="q-how-can-i-check-if-data-box-is-available-in-my-region"></a>F. Hur kan jag kontrollera om Data Box är tillgänglig i min region? 
A.  Information om vilka länder Data Box-tjänsten är tillgänglig i finns på sidan för [regionstillgänglighet](data-box-overview.md#region-availability).  

### <a name="q-which-regions-can-i-store-data-in-with-data-box"></a>F. I vilka regioner kan jag lagra data med Data Box?
A. Data Box stöds i alla regioner inom USA, Västeuropa, Nordeuropa, Frankrike och Storbritannien. Endast regionerna med offentligt Azure-moln stöds. Azure Government och andra begränsade moln stöds inte. Mer information finns i [Regional tillgänglighet](data-box-overview.md#region-availability).

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues-with-data-box"></a>F. Vem kontaktar jag om jag får problem med Data Box?
A. Om du får problem med Data Box [kontaktar du Microsoft Support](data-box-disk-contact-microsoft-support.md).


## <a name="order-device"></a>Beställa en enhet

### <a name="q-how-do-i-get-data-box"></a>F. Hur skaffar jag Data Box? 
A.  Om du vill skaffa Azure Data Box loggar du in på Azure-portalen och skapar en Data Box-beställning. Ange din kontakt- och aviseringsinformation. När du har gjort beställningen skickas Data Box till dig inom tio dagar, beroende på tillgängligheten. Mer information finns på sidan [Beställa en Data Box](data-box-deploy-ordered.md).

### <a name="q-i-was-not-able-to-create-a-data-box-order-in-the-azure-portal-why-would-this-be"></a>F. Jag kunde inte skapa en Data Box-beställning på Azure-portalen. Vad kan det bero på?
A. Om du inte kunde skapa en Data Box-beställning beror det på ett problem med din prenumerationstyp eller åtkomst. 

Kontrollera din prenumeration. Data Box är endast tillgänglig för Enterprise Agreement (EA), leverantörer av molnlösningar (CSP) eller prenumerationer med användningsbaserad betalning. Om din prenumeration inte hör till någon av ovanstående typer kontaktar du Microsoft Support om du vill uppgradera din prenumeration.

Om du har en prenumerationstyp som stöds kontrollerar du din åtkomstnivå för prenumerationen. Du måste vara en Deltagare eller Ägare i prenumerationen för att kunna skapa en order.

### <a name="q-i-ordered-a-couple-of-data-box-devices-i-am-not-able-to-create-any-additional-orders-why-would-this-be"></a>F. Jag har beställt ett par Data Box-enheter. Jag kan inte skapa fler beställningar. Vad kan det bero på?
A. Vi tillåter högst fem aktiva beställningar per prenumeration och handelsområde (kombination av valt land och vald region). Om du vill beställa ytterligare en enhet kontaktar du Microsoft Support för att öka gränsen för din prenumeration.

### <a name="q-when-i-try-to-create-an-order-i-receive-a-notification-that-the-data-box-service-is-not-available-what-does-this-mean"></a>F. När jag försöker skapa en beställning får jag ett meddelande om att Data Box-tjänsten inte är tillgänglig. Vad betyder detta?
A. Det innebär att Data Box-tjänsten inte är tillgänglig för den kombination av land och region som du har valt. Om du ändrar den här kombinationen får du antagligen tillgång till Data Box-tjänsten. En lista över de regioner där tjänsten är tillgänglig finns på sidan för [regionstillgänglighet för Data Box](data-box-overview.md#region-availability).

### <a name="q-i-placed-my-data-box-order-few-days-back-when-will-i-receive-my-data-box"></a>F. Jag skickade min Data Box-beställning för några dagar sedan. När får jag min Data Box?
A. När du gör en beställning kontrollerar vi om det finns någon tillgänglig enhet för din beställning. Om det finns en tillgänglig enhet skickar vi den inom tio dagar. Ibland kan det uppstå perioder med hög efterfrågan. I så fall kan din beställning placeras i kö. Du kan spåra statusförändringen på Azure-portalen. Om din beställning inte har slutförts på 90 dagar avbryts ordern automatiskt. 

### <a name="q-i-have-filled-up-my-data-box-with-data-and-need-to-order-another-one-is-there-a-way-to-quickly-place-the-order"></a>F. Jag har fyllt min Data Box med data och behöver beställa en till. Går det att beställa dem snabbt?
A. Du kan klona din tidigare beställning. En kloning skapar samma ordning som tidigare och du kan redigera beställningsinformationen utan att behöva ange adress, kontaktuppgifter och aviseringsadress. 

## <a name="configure-and-connect"></a>Konfigurera och ansluta

### <a name="q-how-do-i-unlock-the-data-box"></a>F. Hur låser jag upp min Data Box? 
A.  Gå till din Data Box-beställning på Azure-portalen och navigera till **Enhetsinformation**. Kopiera lösenordet för upplåsning. Använd det här lösenordet för att logga in i det lokala webbgränssnittet på din Data Box. Mer information finns i [Självstudie: Packa upp, kabelanslut och anslut till Azure Data Box](data-box-deploy-set-up.md).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box"></a>F. Kan jag använda en Linux-värddator för att ansluta och kopiera data till Data Box?
A.  Ja. Du kan använda Data Box för att ansluta till SMB- och NFS-klienter. Mer information finns i listan över [operativsystem som stöds](data-box-system-requirements.md) för värddatorn.

### <a name="q-my-data-box-is-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>F. Min Data Box har skickats men jag vill avbryta beställningen. Varför kan jag inte trycka på knappen och avbryta?
A.  Du kan bara avbryta en Data Box-beställning innan ordern har bearbetats. När Data Box-beställningen har bearbetats går det inte att avbryta ordern. 

### <a name="q-can-i-connect-a-data-box-at-the-same-to-multiple-host-computers-to-transfer-data"></a>F. Kan jag ansluta en Data Box samtidigt till flera värddatorer för att överföra data?
A. Ja. Flera värddatorer kan ansluta till Data Box för att överföra data, och flera kopieringsjobb kan köras parallellt. Mer information finns i [Självstudie: Kopiera data till Azure Data Box](data-box-deploy-copy-data.md).

<!--### Q. The network interface on my Data Box is not working. What should I do? 
A. 

### Q. I could not set up Data Box using a Dynamic (DHCP) IP address. Why would this be?
A.

### Q. I could not set up Data Box using a Static IP address. Why would this be?
A.

### Q. I could not set up Data Box on a private network. Why would this be?
A.-->

### <a name="q-the-system-fault-indicator-led-on-the-front-operating-panel-is-on-what-should-i-do"></a>F. Indikatorn för systemfel lyser på framsidan av datorn. Vad ska jag göra?
A. Om indikatorn för systemfel lyser betyder det att datorn inte är felfri. [Kontakta Microsoft Support](data-box-disk-contact-microsoft-support.md) om du vill ha hjälp.

### <a name="q-i-cant-access-the-data-box-unlock-password-in-the-azure-portal-why-would-this-be"></a>F. Jag kan inte komma åt Data Box-upplåsningslösenordet på Azure-portalen. Vad kan det bero på?
A. Om du inte kan komma åt upplåsningslösenordet på Azure-portalen kontrollerar du behörigheterna för din prenumeration och ditt lagringskonto. Kontrollera att du har behörighet som deltagare eller ägare på resursgruppsnivå. Om inte behöver du minst ha rollbehörigheten som Data Box-operator för att kunna se autentiseringsuppgifterna.

### <a name="q-is-port-channel-configuration-supported-on-data-box-how-about-mpio"></a>F. Stöder Data Box portkonfiguration för kanalen? Vad gäller MPIO?
A. Vi stöder inte portkonfiguration för kanalen, Multipath IO (MPIO)-konfiguration eller vLAN-konfiguration på Data Box.

## <a name="track-status"></a>Spåra status

### <a name="q-how-do-i-track-the-data-box-from-when-i-placed-the-order-to-shipping-the-device-back"></a>F. Hur spårar jag Data Box från tidpunkten då jag gjorde beställningen till returen av enheten? 
A.  Du kan spåra statusen för Data Box Disk-beställningen på Azure-portalen. När du skapar beställningen blir du ombedd att ange en e-postadress dit aviseringar kan skickas. Om du angav en sådan e-postadress meddelas du om alla statusändringar för beställningen. Mer information om hur du [konfigurerar e-postaviseringar](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-device"></a>F. Hur gör jag för att skicka tillbaka enheten? 
A.  Microsoft visar en adressetikett på e-bläckskärmen. Om adressetiketten inte visas på e-bläckskärmen går du till **Översikt > Ladda ned adressetikett**. Ladda ned och skriv ut etiketten, sätt in etiketten i plastfickan på enheten och lämna enheten hos transportföretaget. 

### <a name="q-i-received-an-email-notification-that-my-device-has-reached-the-azure-datacenter-how-do-i-find-out-if-the-data-upload-is-in-progress"></a>F. Jag har fått ett e-postmeddelande om att enheten har kommit fram till Azure-datacentret. Hur tar jag reda på om dataöverföringen har startat?
A. Du kan gå till din Data Box-beställning på Azure-portalen och sedan till **Översikt**. Om dataöverföringen till Azure har börjat visas kopieringsprocessen i den högra rutan. 

## <a name="migrate-data"></a>Migrera data

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box"></a>F. Hur stor mängd data får plats på Data Box?  
A.  Data Box har en användbar lagringskapacitet på 80 TB. Du kan använda en enskild Data Box-enhet för datastorlekar från 40 TB till 80 TB. För större datastorlekar på upp till 500 TB kan du beställa flera Data Box-enheter. För datastorlekar över 500 TB registrerar du dig för Data Box Heavy.  

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box"></a>F. Vilken är den största blockblob- och sidblobstorleken som stöds av Data Box? 
A.  De maximala storlekarna regleras av begränsningarna för Azure Storage. Den maximala blockbloben är ungefär 4,768 TiB och den maximala sidblobstorleken är 8 TiB. Mer information finns i [Skalbarhets- och prestandamål i Azure Storage](../storage/common/storage-scalability-targets.md). 

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>F. Hur vet jag att mina data är säkra under överföringen? 
A. Flera säkerhetsfunktioner har implementerats för att säkerställa att din Data Box är säker under överföringen. Några exempel är säkra förslutningar, identifiering av maskin- och programvarumanipulering, lösenord för upplåsning av enheten. Mer information finns i [Säkerhet och dataskydd i Azure Data Box](data-box-security.md).

### <a name="q-how-do-i-copy-the-data-to-the-data-box"></a>F. Hur gör jag för att kopiera data till Data Box? 
A.  Om du använder en SMB-klient kan du använda ett SMB-kopieringsverktyg som Robocopy, Diskboss eller till och med dra-och-släpp-funktionen i Utforskaren i Windows för att kopiera data till enheten. 

Om du använder en NFS-klient kan du använda [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) eller [Ultracopier](https://ultracopier.first-world.info/). 

Mer information finns i [Självstudie: Kopiera data till Azure Data Box](data-box-deploy-copy-data.md).

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>F. Finns det några tips för att påskynda datakopieringen?
A.  Påskynda kopieringsprocessen:

- Använd flera dataströmmar. Med till exempel Robocopy kan du använda flertrådsalternativet. Mer information om exakt vilket kommando som används finns i [Självstudie: Kopiera data till Azure Data Box och verifiera](data-box-deploy-copy-data.md).
- Använda flera sessioner.
- Kontrollera att dina data finns lokalt på datorn som Data Box är ansluten till, i stället för att kopiera via nätverk (där du kan begränsas av nätverkshastigheterna).
- Analysera prestanda för den dator som används för att kopiera data. Ladda ned och använd [Bluestop FIO-verktyget](https://bluestop.org/fio/) för att analysera servermaskinvarans prestanda.

<!--### Q. How to speed up the data copy if the source data has small files (KBs or few MBs)?
A.  To speed up the copy process:

- Create a local VHDx on fast storage or create an empty VHD on the HDD/SSD (slower).
- Mount it to a VM.
- Copy files to the VM’s disk.-->


### <a name="q-can-i-use-multiple-storage-accounts-with-data-box"></a>F. Kan jag använda flera lagringskonton med Data Box?
A.  Ja. Högst tio lagringskonton, GA-version (generell användning), klassisk eller bloblagring stöds med Data Box. Det går att använda både högfrekvent och lågfrekvent blob. När versionen för generell användning är tillgänglig stöds lagringskontona i alla regioner inom USA, Västeuropa, Nordeuropa, Frankrike och Storbritannien i det offentliga Azure-molnet.


## <a name="ship-device"></a>Skicka en enhet

<!--### Q. How do I schedule a pickup for my Data Box?--> 

### <a name="q-my-device-was-delivered-but-the-device-seems-to-be-damaged-what-should-i-do"></a>F. Min enhet har levererats men enheten verkar vara skadad. Vad ska jag göra?
A. Om enheten är skadad eller om det finns tecken på att den har manipulerats ska du inte använda enheten. [Kontakta Microsoft Support](data-box-disk-contact-microsoft-support.md) och returnera enheten så snart som möjligt. Du kan också skapa en ny Data Box-beställning för en ersättningsenhet. I så fall debiteras du inte för ersättningsenheten.

### <a name="q-can-i-use-my-own-shipping-carrier-to-ship-data-box"></a>F. Kan jag använda mitt eget transportföretag för att skicka Data Box?
A. För Data Box-tjänsten hanterar Microsoft leveransen till och från Azure-datacentret. Om du vill använda ditt eget transportföretag kan du använda tjänsten Azure Import/Export. Mer information finns i [Vad är Azure Import/Export-tjänsten?](../storage/common/storage-import-export-service.md)

### <a name="q-e-ink-display-is-not-showing-the-return-shipment-label-what-should-i-do"></a>F. Returadressetiketten visas inte på e-bläckskärmen. Vad ska jag göra?
A. Om returadressetiketten inte visas på e-bläckskärmen följer du dessa steg:
- Ta bort den gamla adressetiketten och eventuella klistermärken från den tidigare leveransen.
- Gå till din beställning på Azure-portalen. Gå till **Översikt** och **Ladda ned adressetikett**. Mer information finns i [Ladda ned adressetikett](data-box-portal-admin.md#download-shipping-label).
- Skriv ut adressetiketten och sätt in den i plastfickan som är fäst på enheten. 
- Kontrollera att adressetiketten syns tydligt. 

### <a name="q-how-is-my-data-protected-during-transit"></a>F. Hur skyddade är mina data under transporten? 
A.  Under överföringen hjälper följande Data Box-funktioner till att skydda data.
 - Data Box-diskarna krypteras med AES 256-bitarskryptering. 
 - Enheten är låst och ett upplåsningslösenord krävs för att mata in eller komma åt data.
Mer information finns i avsnittet om [Data Box-säkerhetsfunktioner](data-box-security.md).  

### <a name="q-i-have-finished-prepare-to-ship-and-shut-down-the-device-can-i-still-add-more-data-to-data-box"></a>F. Jag har slutfört anvisningarna i Förbered för att skicka och enheten är avstängd. Kan jag fortfarande lägga till mer data till Data Box?
A. Ja. Du kan aktivera enheten och lägga till mer data. Du måste köra **Förbered för att skicka** igen när du har slutfört kopieringen.

### <a name="q-i-received-my-device-and-it-is-not-booting-up-how-do-i-ship-the-device-back"></a>F. Jag fick enheten, men den startas inte. Hur skickar jag tillbaka enheten?
A. Om enheten inte startar går du till din beställning i Azure-portalen. Ladda ned en adressetikett och fäst den på enheten. Mer information finns i [Ladda ned adressetikett](data-box-portal-admin.md#download-shipping-label).

## <a name="verify-and-upload"></a>Verifiera och ladda upp

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-data-box-back"></a>F. Hur snart kommer jag åt mina data i Azure när jag har skickat tillbaka Data Box? 
A.  När orderstatusen för **datakopieringen** visas som **Slutförd** bör du komma åt dina data direkt.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>F. Var i Azure finns mina data efter uppladdningen?
A.  När du kopierar data till Data Box laddas data upp någon av följande sökvägar i ditt Azure Storage-konto, beroende på om de utgör en blockblob, sidblob eller Azure-filer.
 - `https://<storage_account_name>.blob.core.windows.net/<containername>` 
 -  `https://<storage_account_name>.file.core.windows.net/<sharename>`
 
 Du kan också gå till ditt Azure-lagringskonto i Azure-portalen och navigera därifrån.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>F. Jag har precis märkt att jag inte följde namngivningskraven för min container. Misslyckas dataöverföringen till Azure?
A.  Versaler omvandlas automatiskt till gemener. Om namnen inte uppfyller kraven på andra sätt (specialtecken, andra språk och så vidare) misslyckas uppladdningen. Mer information om metodtips för namngivning av resurser, containrar och filer finns här: 
- [Namnge och referera till resurser](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Konventioner för blockblobar och sidblobar](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-data-box"></a>F. Hur verifierar jag de data som jag har kopierat till Data Box?
A.  Dina data verifieras när datakopieringen är klar och du kör **Förbered för att skicka**. Data Box genererar en lista över filer och kontrollsummor för data under verifieringsprocessen. Du kan ladda ned listan över filer och verifiera den mot filer i källdata. Om du vill ha mer information går du till [Förbered för att skicka](data-box-deploy-copy-data.md#prepare-to-ship).

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-data-box"></a>F. Vad händer med mina data när jag har skickat tillbaka Data Box?
A.  När datakopieringen till Azure är klar raderas alla data på Data Box på ett säkert sätt enligt riktlinjerna i NIST SP 800-88 Revision 1. Mer information finns i avsnittet om [radering av data från Data Box](data-box-deploy-picked-up.md#erasure-of-data-from-data-box).

## <a name="audit-report"></a>Granskningsrapport

### <a name="how-does-azure-data-box-service-help-support-customers-chain-of-custody-procedure"></a>Hur hjälper Azure Data Box-tjänsten kunder att spåra och övervaka processen?
A.  Azure Data Box-tjänsten tillhandahåller rapporter som du kan använda för att spåra och dokumentera processen. Gransknings- och kopieringsloggarna är tillgängliga i ditt lagringskonto i Azure, och [du kan ladda ned orderhistoriken](data-box-portal-admin.md#download-order-history) på Azure-portalen när ordern har slutförts.


### <a name="what-type-of-reporting-is-available-to-support-chain-of-custody"></a>Vilken typ av rapportering är tillgänglig för spårbarhet och övervakning?
A.  Följande rapportering är tillgänglig för spårbarhet och övervakning:

- Transportlogistik från UPS.
- Loggning om start och resursåtkomst av användaren.
- Manifestfil med en 64-bitars CRC (Cyclic Redundancy Check 64) eller kontrollsumma för varje fil som matas in i Data Box.
- Rapportering av filer som inte kunde laddas upp till Azure Storage-kontot.
- Sanering av Data Box-enheten (enligt NIST 800 88R1-standarder) efter att alla data har kopierats till ditt Azure Storage-konto.

### <a name="are-the-carrier-tracking-logs-from-ups-available"></a>Är transportföretagets spårningsloggar (från UPS) tillgängliga? 
A.  Transportföretagets spårningsloggar samlas in Data Box-orderhistoriken. Den här rapporten är tillgänglig när enheten har returnerats till Azure-datacentret och data på enhetsdiskarna har rensats. Du kan också gå direkt till transportföretagets webbplats och ange spårningsnumret för ordern för att visa spårningsinformationen.

### <a name="can-i-transport-the-data-box-to-azure-datacenter"></a>Kan jag själv transportera Data Box till Azures datacenter? 
A.  Nej. Azures datacenter accepterar för närvarande inte leverans av Data Box från kunder eller andra transportföretag än UPS.


## <a name="next-steps"></a>Nästa steg

- Titta på [systemkraven för Data Box](data-box-system-requirements.md).
- Förstå [Data Box-begränsningarna](data-box-limits.md).
- Distribuera snabbt [Azure Data Box Disk](data-box-quickstart-portal.md) på Azure-portalen.
