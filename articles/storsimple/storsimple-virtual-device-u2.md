---
title: StorSimple virtuell enhet Uppdatering 2| Microsoft Docs
description: "Lär dig mer om att skapa, distribuera och hantera en virtuell StorSimple-enhet i ett virtuellt Microsoft Azure-nätverk. (Gäller StorSimple uppdatering 2)."
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: f37752a5-cd0c-479b-bef2-ac2c724bcc37
ms.service: storsimple
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/22/2017
ms.author: alkohli
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 48d9d8ae97eb763932dd6a59a7df01ae92c92eff
ms.lasthandoff: 03/24/2017


---
# <a name="deploy-and-manage-a-storsimple-virtual-device-in-azure"></a>Distribuera och hantera en virtuell StorSimple-enhet i Azure
## <a name="overview"></a>Översikt
Den virtuella StorSimple-enheten i 8000-serien är en ytterligare funktion som ingår i din Microsoft Azure StorSimple-lösning. Den virtuella enheten StorSimple körs på en virtuell dator i ett virtuellt Microsoft Azure-nätverk och du kan använda den för att säkerhetskopiera och klona data från värdar. Den här självstudien beskriver hur du distribuerar och hanterar virtuella enheter i Azure och är tillämplig på alla virtuella enheter som kör programvaruversion Uppdatering 2 och lägre.

#### <a name="virtual-device-model-comparison"></a>Jämförelse av virtuella enhetsmodeller
Den virtuella enheten StorSimple finns i två modeller, en Standardmodell, 8010 (kallades tidigare 1100), och Premiummodellen 8020 (introducerad i Uppdatering 2). En jämförelse av de två modellerna visas i tabellen nedan

| Enhetsmodell | 8010<sup>1</sup> | 8020 |
| --- | --- | --- |
| **Maximal kapacitet** |30 TB |64 TB |
| **Virtuell Azure-dator** |Standard_A3 (4 kärnor, 7 GB minne) |Standard_DS3 (4 kärnor, 14 GB minne) |
| **Versionskompatibilitet** |Versioner som körs före Uppdatering 2 eller senare |Versioner som körs med Uppdatering 2 eller senare |
| **Regional tillgänglighet** |Alla Azure-regioner |Alla Azure-regioner som har stöd för Premium Storage<br></br> Premium Storage-regioner är regioner som motsvarar raden för *disklagring* i listan över [Azure-tjänster efter region](https://azure.microsoft.com/en-us/regions/services). |
| **Lagringstyp** |Använder Azure Standardlagring för lokala diskar<br></br> Lär dig hur du [skapar ett Standardlagringskonto](../storage/storage-create-storage-account.md) |Använder Azure Premium Storage för lokala diskar<sup>2</sup> <br></br>Lär dig hur du [skapar ett Premiumkonto för lagring](../storage/storage-premium-storage.md) |
| **Riktlinjer för arbetsbelastning** |Hämtning av filer från säkerhetskopior på objektnivå |Scenarier för utveckling och test av molnet, låg latens, arbetsbelastningar med hög prestanda <br></br>Sekundär enhet för katastrofåterställning |

<sup>1</sup> *Kallades tidigare 1100*.

<sup>2</sup> *Både 8010 och 8020 använder Azure Standardlagring för molnnivån. Skillnaden finns endast på den lokala nivån i enheten*.

Den här artikeln beskriver stegvis processen för att distribuera en virtuell StorSimple-enhet i Azure. När du har läst den här artikeln, kommer du att:

* Förstå hur den virtuella enheten skiljer sig från den fysiska enheten.
* Kunna skapa och konfigurera den virtuella enheten.
* Ansluta till den virtuella enheten.
* Lära dig hur du arbetar med den virtuella enheten.

Den här självstudien gäller för alla virtuella StorSimple-enheter med Uppdatering 2 och senare.

## <a name="how-the-virtual-device-differs-from-the-physical-device"></a>Så skiljer sig den virtuella enheten från den fysiska enheten.
Den virtuella StorSimple-enheten är en version av StorSimple som endast är programvarubaserad och som körs på en enskild nod på en virtuell Microsoft Azure-dator. Den virtuella enheten har stöd för katastrofåterställning i situationer när den fysiska enheten inte är tillgänglig och är lämpad för att användas för att hämta säkerhetskopior på objektsnivå, för lokal katastrofåterställning samt molnbaserade utvecklings- och testscenarier.

#### <a name="differences-from-the-physical-device"></a>Hur den skiljer sig från den fysiska enheten
I följande tabell visas några huvudsakliga skillnader mellan den virtuella StorSimple-enheten och den fysiska StorSimple-enheten.

|  | Fysisk enhet | Virtuell enhet |
| --- | --- | --- |
| **Plats** |Finns i datacentret. |Körs i Azure. |
| **Nätverksgränssnitt** |Har sex nätverksgränssnitt: DATA 0 till DATA 5. |Har bara ett nätverksgränssnitt: DATA 0. |
| **Registrering** |Registrering under konfigurationssteget. |Registreringen är en separat åtgärd. |
| **Krypteringsnyckel för tjänstdata** |Återskapas på den fysiska enheten och sedan uppdateras den virtuella enheten med den nya nyckeln. |Kan inte att återskapas från den virtuella enheten. |

## <a name="prerequisites-for-the-virtual-device"></a>Krav för den virtuella enheten
I följande avsnitt beskrivs konfigurationskraven för din virtuella StorSimple-enhet. Innan du distribuerar en virtuell enhet, ska du granska [säkerhetsöverväganden för att använda en virtuell enhet](storsimple-security.md#storsimple-virtual-device-security).

#### <a name="azure-requirements"></a>Krav för Azure
Innan du etablerar den virtuella enheten måste du göra följande förberedelser i Azure-miljön:

* [Konfigurera ett virtuellt nätverk på Azure](../virtual-network/virtual-networks-create-vnet-classic-portal.md) för den virtuella enheten. Om du använder Premiumlagring, måste du skapa ett virtuellt nätverk i en Azure-region som har stöd för Premiumlagring. Premium Storage-regioner är regioner som motsvarar raden för *disklagring* i listan över [Azure-tjänster efter region](https://azure.microsoft.com/en-us/regions/services).
* Du rekommenderas att använda standard-DNS-servern från Azure istället för att ange ett eget DNS-servernamn. Om DNS-servernamnet inte är giltigt eller om DNS-servern inte kan matcha IP-adresser korrekt går det inte att skapa den virtuella enheten.
* Punkt-till-plats och plats-till-plats går att välja, men är inget krav. Om du vill kan du konfigurera dessa alternativ för mer avancerade scenarier.
* Du kan skapa [virtuella datorer i Azure](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (värdservrar) i det virtuella nätverket som kan använda de volymer som exponeras av den virtuella enheten. Servrarna måste uppfylla följande krav:                             

  * Vara virtuella Windows eller Linux-datorer som har installerad programvara med iSCSI-initierare.
  * Köras i samma virtuella nätverk som den virtuella enheten.
  * Kunna anslutas till iSCSI-målet på den virtuella enheten via den virtuella enhetens interna IP-adress.
* Kontrollera att du har konfigurerat stöd för iSCSI och molntrafik på samma virtuella nätverk.

#### <a name="storsimple-requirements"></a>Krav för StorSimple
Gör följande uppdateringar i din Azure StorSimple-tjänst innan du skapar en virtuell enhet:

* Lägg till [åtkomstkontrollposter](storsimple-manage-acrs.md) för de virtuella datorer som ska vara värdservrar för den virtuella enheten.
* Använd ett [lagringskonto](storsimple-manage-storage-accounts.md#add-a-storage-account) i samma region som den virtuella enheten. Lagringskonton i olika regioner kan resultera i sämre prestanda. Du kan använda ett Standard- eller Premiumlagringskonto med den virtuella enheten. Mer information om hur du skapar ett [Standardlagringskonto](../storage/storage-create-storage-account.md) eller ett [Premiumlagringskonto](../storage/storage-premium-storage.md)
* Använd ett annat lagringskonto för att skapa en virtuell enhet än det som används för dina data. Om samma lagringskonto används kan det resultera i sämre prestanda.

Kontrollera att du har följande information innan du börjar:

* Ditt klassiska Azure-portalkonto med autentiseringsuppgifter.
* En kopia av krypteringsnyckeln för dina tjänstdata från den fysiska enheten.

## <a name="create-and-configure-the-virtual-device"></a>Skapa och konfigurera den virtuella enheten
Innan du genomför de här procedurerna ska du säkerställa att du har uppfyllt [kraven för den virtuella enheten](#prerequisites-for-the-virtual-device).

När du har skapat ett virtuellt nätverk, konfigurerat en StorSimple Manager-tjänst och registrerat din fysiska StorSimple-enhet med tjänsten, kan du använda följande steg för att skapa och konfigurera en virtuell StorSimple-enhet.

### <a name="step-1-create-a-virtual-device"></a>Steg 1: Skapa en virtuell enhet
Utför följande steg för att skapa den virtuella StorSimple-enheten.

[!INCLUDE [Create a virtual device](../../includes/storsimple-create-virtual-device-u2.md)]

Om det inte går att skapa den virtuella enheten i det här steget kan det bero på att du saknar uppkoppling till Internet. Gå till [Felsöka problem med Internetanslutning](#troubleshoot-internet-connectivity-errors) om du behöver mer information när du skapar en virtuell enhet.

### <a name="step-2-configure-and-register-the-virtual-device"></a>Steg 2: Konfigurera och registrera den virtuella enheten
Kontrollera innan du påbörjar den här proceduren att du har en kopia av krypteringsnyckeln för tjänstdata. Krypteringsnyckeln för tjänstdata skapades när du konfigurerade din första StorSimple-enhet och du instruerades att spara den på en säker plats. Om du inte har en kopia av krypteringsnyckeln för tjänstdata ska du kontakta Microsoft Support för att få hjälp.

Utför följande steg för att konfigurera och skapa den virtuella StorSimple-enheten.

[!INCLUDE [Configure and register a virtual device](../../includes/storsimple-configure-register-virtual-device.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>Steg 3: (Valfritt) ändra konfigurationsinställningarna för enheten
I följande avsnitt beskrivs de konfigurationsinställningar som krävs för den virtuella StorSimple-enheten om du vill använda CHAP, StorSimple Snapshot Manager eller ändra administratörslösenordet för enheten.

#### <a name="configure-the-chap-initiator"></a>Konfigurera CHAP-initieraren
Den här parametern innehåller de autentiseringsuppgifter som din virtuella enhet (målet) förväntar sig från initierarna (servrarna) som försöker få åtkomst till volymerna. Initierarna skapar ett CHAP-användarnamn och ett CHAP-lösenord för att identifiera sig själva på din enhet under den här autentiseringen. Detaljerade anvisningar finns i [Konfigurera CHAP för din enhet](storsimple-configure-chap.md#unidirectional-or-one-way-authentication).

#### <a name="configure-the-chap-target"></a>Konfigurera CHAP-målet
Den här parametern innehåller de autentiseringsuppgifter som din virtuella enhet använder när en CHAP-aktiverad initierare begär ömsesidig eller dubbelriktad autentisering. Din virtuella enhet använder ett omvänt CHAP-användarnamn och lösenord för att identifiera sig för initieraren under den här autentiseringsprocessen. Observera att inställningarna för CHAP-målet är globala inställningar. När dessa tillämpas använder alla volymer som är anslutna till den virtuella lagringsenheten CHAP-autentisering. Detaljerade anvisningar finns i [Konfigurera CHAP för din enhet](storsimple-configure-chap.md#bidirectional-or-mutual-authentication).

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>Konfigurera lösenordet för StorSimple Snapshot Manager
Programvaran StorSimple Manager finns på din Windows-värd och ger administratörer möjlighet att hantera säkerhetskopiering av din StorSimple-enhet i form av lokala och molnbaserade ögonblicksbilder.

> [!NOTE]
> För den virtuella enheten är din Windows-värd en virtuell Azure-dator.
>
>

När du konfigurerar en enhet i StorSimple Snapshot Manager uppmanas du att ange StorSimple-enhetens IP-adress och lösenord för att autentisera lagringsenheten. Detaljerade anvisningar finns i [Konfigurera lösenordet för StorSimple Snapshot Manager](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password).

#### <a name="change-the-device-administrator-password"></a>Ändra enhetens administratörslösenord
När du använder Windows PowerShell-gränssnittet för att komma åt den virtuella enheten uppmanas du att ange ett administratörslösenord för enheten. För att upprätthålla säkerheten för dina data ska det här lösenordet ändras innan den virtuella enheten kan användas. Detaljerade anvisningar finns i [Konfigurera administratörslösenord för enheten](storsimple-change-passwords.md#change-the-device-administrator-password).

## <a name="connect-remotely-to-the-virtual-device"></a>Fjärransluta till den virtuella enheten
Fjärråtkomst till din virtuella enhet via Windows PowerShell-gränssnittet är inte aktiverat som standard. Du måste aktivera fjärrhantering på den virtuella enheten först och sedan aktivera det på klienten som ska användas för åtkomst till virtuella enheten.

Tvåstegsprocessen för att fjärransluta beskrivs nedan.

### <a name="step-1-configure-remote-management"></a>Steg 1: Konfigurera fjärrhantering
Utför följande steg för att konfigurera fjärrhantering för din virtuella StorSimple-enhet.

[!INCLUDE [Configure remote management via HTTP for virtual device](../../includes/storsimple-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-virtual-device"></a>Steg 2: Fjärranslut till den virtuella enheten
När du har aktiverat fjärrhantering på konfigurationssidan för StorSimple-enheten kan du använda Windows PowerShell fjärrkommunikation för att ansluta till den virtuella enheten från en annan virtuell dator i samma virtuella nätverket. Du kan till exempel ansluta från den virtuella värddatorn som du har konfigurerat och använt för iSCSI-anslutning. I de flesta distributioner har du redan öppnat en offentlig slutpunkt för att komma åt din virtuella värddator, som du nu även kan använda för att komma åt den virtuella enheten.

> [!WARNING]
> **För ökad säkerhet rekommenderar vi starkt att du använder HTTPS för att ansluta till slutpunkterna och sedan tar bort slutpunkterna när din PowerShell-fjärrsession är slutförd.**
>
>

Du bör följa procedurerna i [Fjärransluta till din StorSimple-enhet](storsimple-remote-connect.md) för att ställa in fjärrstyrning för din virtuella enhet.

## <a name="connect-directly-to-the-virtual-device"></a>Ansluta direkt till den virtuella enheten
Du kan även ansluta direkt till den virtuella enheten. Om du vill ansluta direkt till den virtuella enheten från en annan dator utanför det virtuella nätverket eller utanför Microsoft Azure-miljön måste du skapa ytterligare slutpunkter som beskrivs i följande procedur.

Utför följande steg för att skapa en offentlig slutpunkt på den virtuella enheten.

[!INCLUDE [Create public endpoints on a virtual device](../../includes/storsimple-create-public-endpoints-virtual-device.md)]

Vi rekommenderar att du ansluter från en annan virtuell dator i samma virtuella nätverk eftersom den här metoden minimerar antalet offentliga slutpunkter i ditt virtuella nätverk. När du använder den här metoden kan du enkelt ansluta till den virtuella datorn via en fjärrskrivbordssession och sedan konfigurera den virtuella datorn för användning på samma sätt som alla andra Windows-klienter i ett lokalt nätverk. Du behöver inte lägga till det offentliga portnumret eftersom porten redan är känt.

## <a name="work-with-the-storsimple-virtual-device"></a>Arbeta med den virtuella StorSimple-enheten
Nu när du har skapat och konfigurerat den virtuella StorSimple-enheten, är du redo att börja arbeta med den. Du kan arbeta med volymbehållare, volymer och principer för säkerhetskopiering på en virtuell enhet precis som på en fysisk enhet. Den enda skillnaden är att du behöver säkerställa att du har valt den virtuella enheten från din enhetslista. Det finns stegvisa anvisningar för olika hanteringsuppgifter för den virtuella enheten i  [Använda StorSimple Manager-tjänsten för att hantera en virtuell enhet](storsimple-manager-service-administration.md).

I följande avsnitt beskrivs några av skillnaderna du kommer märka när du arbetar med den virtuella enheten.

### <a name="maintain-a-storsimple-virtual-device"></a>Underhålla en virtuell StorSimple-enhet
Tack vare att den endast är programvarubaserad kräver den virtuella enheten minimalt underhåll jämfört med den fysiska enheten. Du har följande alternativ:

* **Programuppdateringar** – du kan visa det datum då programmet senast uppdaterades, tillsammans med eventuella statusmeddelanden för uppdateringen. Du kan använda knappen **Skanna uppdateringar** längst ned på sidan för att utföra en manuell genomsökning om du vill söka efter nya uppdateringar. Om det finns uppdateringar, klickar du på **Installera uppdateringar** för att installera. Eftersom det finns bara ett enda gränssnitt på den virtuella enheten innebär det att en viss driftstörning uppstår när uppdateringarna tillämpas. Den virtuella enheten avslutas och startas om (vid behov) för att tillämpa uppdateringar som har lanserats. Stegvisa anvisningar finns i [Uppdatera din enhet](storsimple-update-device.md#install-regular-updates-via-the-azure-classic-portal).
* **Stödpaket** – du kan skapa och ladda upp ett supportpaket för att hjälpa Microsoft-supporten felsöka problem i din virtuella enhet. Stegvisa anvisningar finns i [Skapa och hantera ett stödpaket](storsimple-create-manage-support-package.md).

### <a name="storage-accounts-for-a-virtual-device"></a>Lagringskonton för en virtuell enhet
Lagringskonton skapas för användning av StorSimple Manager-tjänsten, den virtuella enheten och den fysiska enheten. När du skapar dina lagringskonton rekommenderar vi att du använder en regionsidentifierare i det egna namnet för att garantera att regionen är konsekvent på alla systemkomponenter. För virtuella enheter är det viktigt att alla komponenter är i samma region för att förhindra prestandaproblem.

Stegvisa anvisningar finns i [Lägg till ett lagringskonto](storsimple-manage-storage-accounts.md#add-a-storage-account).

### <a name="deactivate-a-storsimple-virtual-device"></a>Inaktivera en virtuell StorSimple-enhet
Vid inaktiveringen av en virtuell enhet tas den virtuella datorn bort samt de resurser som skapades när den etablerades. När den virtuella enheten inaktiveras kan den inte återställas till sitt tidigare tillstånd. Se till att stoppa eller ta bort klienter och värdar som är beroende av den innan du inaktiverar den virtuella enheten.

Inaktivering av virtuella enheter resulterar i följande åtgärder:

* Den virtuella enheten tas bort.
* Operativsystemdisken och datadiskar som har skapats för den virtuella enheten tas bort.
* Den värdbaserade tjänsten och virtuella nätverk som skapades under etableringen bevaras. Om du inte använder dem, bör du ta bort dem manuellt.
* Ögonblicksbilder som har skapats i molnet för den virtuella enheten bevaras.

Stegvisa anvisningar finns i [Inaktivera och ta bort din virtuella StorSimple-enhet](storsimple-deactivate-and-delete-device.md).

Så snart den virtuella enheten visas som inaktiverad på tjänstsidan StorSimple Manager kan du ta bort den virtuella enheten från listan över enheter på sidan **Enheter**.

### <a name="start-stop-and-restart-a-virtual-device"></a>Starta, stoppa och starta om en virtuell enhet
Till skillnad från den fysiska StorSimple-enheten finns det ingen strömbrytare på en virtuell StorSimple-enhet. Det kan dock finnas tillfällen när du behöver stoppa och starta om den virtuella enheten. Vissa uppdateringar kan exempelvis kräva att den virtuella datorn startas om för att en uppdateringsprocess ska kunna slutföras. Det enklaste sättet att starta, stoppa och starta om en virtuell enhet är att använda konsolen för hantering av virtuella datorer.

När du tittar på hanteringskonsolen är den virtuella enhetens status **Körs** eftersom den startas som standard när den har skapats. Du kan starta, stoppa och starta om en virtuell dator när som helst.

[!INCLUDE [Stop and restart virtual device](../../includes/storsimple-stop-restart-virtual-device.md)]

### <a name="reset-to-factory-defaults"></a>Återställa till fabriksinställningarna
Om du vill börja om med din virtuella enhet ska du inaktivera och ta bort den och sedan skapa en ny. Precis som vid återställning av den fysiska enheten kommer inte den nya virtuella enheten ha några eventuella uppdateringar installerade. Kontrollera därför om det finns uppdateringar innan du använder den.

## <a name="fail-over-to-the-virtual-device"></a>Växling till den virtuella enheten
Katastrofåterställning är ett av de scenarier som den virtuella StorSimple-enheten har designats för. Under dessa scenarier kan det hända att den fysiska StorSimple-enheten eller hela datacentret inte tillgängliga. Som tur är kan du använda en virtuell enhet för återställa driften på en annan plats. Under katastrofåterställning byter volymbehållarna från källan ägarskap och överförs till den virtuella enheten. Kraven för katastrofåterställning är att den virtuella enheten har skapats och konfigurerats, att alla volymer i volymbehållaren är i offlineläge och att volymbehållaren är kopplad till en ögonblicksbild i molnet.

> [!NOTE]
> * När du använder en virtuell enhet som en sekundär enhet för katastrofåterställning är det viktigt att komma ihåg att 8010 har 30 TB Standardlagring och 8020 har 64 TB Premiumlagring.  Den virtuella enheten 8020 som har högre kapacitet kan vara mer lämpad för användning vid katastrofåterställning.
> * Det går inte att växla eller klona från en enhet som kör Uppdatering 2 till en enhet som kör program av tidigare versioner än Uppdatering 1. Du kan dock växla en enhet som kör Uppdatering 2 till en enhet som kör Uppdatering 1 (1.1 eller 1.2)
>
>

Stegvisa anvisningar finns i [växling till en virtuell enhet](storsimple-device-failover-disaster-recovery.md#fail-over-to-a-storsimple-virtual-device).

## <a name="shut-down-or-delete-the-virtual-device"></a>Stänga av eller ta bort den virtuella enheten
Om du tidigare har konfigurerat och använt en virtuell StorSimple-enhet men nu vill att debiteringarna för den ska avbrytas, kan du stänga av den virtuella enheten. Avstängning av den virtuella enheten medför inte att dess operativsystem eller lagringar i datadiskar tas bort. Debiteringen för din prenumeration upphör, men lagringskostnaderna för operativsystemet och datadiskarna fortsätter.

Om du tar bort eller stänger av den virtuella enheten visas den som **Offline** på sidan med enheter på StorSimple Manager-tjänsten. Du kan välja att inaktivera eller ta bort enheten om du även vill ta bort de säkerhetskopior som har skapats av den virtuella enheten. Mer information finns i [Inaktivera och ta bort en StorSimple-enhet](storsimple-deactivate-and-delete-device.md).

[!INCLUDE [Shut down a virtual device](../../includes/storsimple-shutdown-virtual-device.md)]

[!INCLUDE [Delete a virtual device](../../includes/storsimple-delete-virtual-device.md)]

## <a name="troubleshoot-internet-connectivity-errors"></a>Felsöka problem med Internetanslutning
Om du inte har någon anslutning till Internet när du skapar en virtuell enhet kommer det inte att fungera. Du kan kontrollera om problemet orsakas av Internetanslutningen genom att utföra följande steg i den klassiska Azure-portalen:

1. Skapa en virtuell dator med Windows Server 2012 i Azure. Den virtuella datorn ska ha samma lagringskonto, VNet och undernät som används av din virtuella enhet. Om du redan har en befintlig Windows Server-värd i Azure som använder samma lagringskonto, VNET och undernät, kan du även använda den för att felsöka Internetanslutningen.
2. Logga in via fjärrinloggning på den virtuella datorn som skapades i föregående steg.
3. Öppna ett kommandofönster i den virtuella datorn (Win + R och skriv sedan `cmd`).
4. Kör följande cmd i prompten.

    `nslookup windows.net`
5. Om `nslookup` misslyckas hindrar problemet med Internetanslutningen den virtuella enheten från att registrera sig för StorSimple Manager-tjänsten.
6. Gör nödvändiga ändringar i ditt virtuella nätverk så att den virtuella enheten kan komma åt Azure-webbplatser, till exempel windows.net.

## <a name="next-steps"></a>Nästa steg
* Lär dig att [använda StorSimple Manager-tjänsten för att hantera en virtuell enhet](storsimple-manager-service-administration.md).
* Förstå hur du [återställer en StorSimple-volym från en säkerhetskopia](storsimple-restore-from-backup-set.md).

