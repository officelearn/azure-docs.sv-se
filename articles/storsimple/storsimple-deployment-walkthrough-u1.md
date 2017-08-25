---
title: Distribuera din StorSimple-enhet (Uppdatering 1) | Microsoft Docs
description: "Beskriver stegen och bästa praxis för att distribuera StorSimple-enheten och tjänsten för uppdatering 1."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: ac631d3c-3c53-4c9e-9e4a-5c61c0cd8167
ms.service: storsimple
ms.devlang: NA
ms.topic: hero-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/17/2016
ms.author: alkohli
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 4d568fb2eca418ca939f7a76ac24197a0457fe47
ms.contentlocale: sv-se
ms.lasthandoff: 08/21/2017

---
# <a name="deploy-your-on-premises-storsimple-device-update-1"></a>Distribuera din lokala StorSimple-enhet (uppdatering 1)
> [!div class="op_single_selector"]
> * [Uppdatering 2](storsimple-deployment-walkthrough-u2.md)
> * [Uppdatering 1](storsimple-deployment-walkthrough-u1.md)
> * [GA-version](storsimple-deployment-walkthrough.md)
> 
> 

## <a name="overview"></a>Översikt
Välkommen till distribution av Microsoft Azure StorSimple-enheten. De här kurserna om distribution gäller StorSimple 8000-serien uppdatering 1.0. Den här kursserien beskriver hur du konfigurerar din StorSimple-enhet och innehåller en checklista för konfiguration, konfigurationskrav och detaljerade konfigurationssteg.

Informationen i de här kurserna förutsätter att du har vidtagit säkerhetsåtgärder, packat upp, rackmonterat och kabelanslutit din StorSimple-enhet. Om du fortfarande inte har utförd ovanstående bör du börja med att vidta [säkerhetsåtgärderna](storsimple-safety.md). Beroende på din enhetsmodell kan du sedan packa upp, rackmontera och kabelansluta genom att följa instruktionerna i:

* [Packa upp, rackmontera och kabelanslut din 8100](storsimple-8100-hardware-installation.md)
* [Packa upp, rackmontera och kabelanslut din 8600](storsimple-8600-hardware-installation.md)

Du måste ha administratörsbehörighet för att slutföra installationen och konfigurationen. Vi rekommenderar att du läser checklistan för konfiguration innan du börjar. Processen för distribution och konfiguration kan ta lite tid att slutföra.

> [!NOTE]
> Distributionsinformationen för StorSimple som publiceras på webbplatsen Microsoft Azure gäller endast StorSimple-enheter i 8000-serien. Fullständig information om enheterna i serierna 5000 och 7000 finns här: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Distributionsinformation för 5000 och 7000-serien finns i [Snabbstartsguide för StorSimple-system](http://onlinehelp.storsimple.com/111_Appliance/).
> 
> 

## <a name="deployment-steps"></a>Distributionssteg
Utför de här obligatoriska stegen för att konfigurera din StorSimple-enhet och ansluta den till din StorSimple Manager-tjänst. Förutom de obligatoriska stegen finns valfria steg och procedurer du kan behöva under distributionen. Steg för steg-instruktionerna för distribution anger när du ska utföra de olika valfria stegen.

| Steg | Beskrivning |
| --- | --- |
| **Förutsättningar** |De här instruktionerna måste slutföras som del av förberedelserna inför den distributionen. |
| Checklista för distributionskonfiguration. |Använd den här checklistan för att samla in och registrera information före och under distributionen. |
| Distributionskrav. |Om de här kraven uppfylls är miljön är klar för distribution. |
|  | |
| **Distribution steg för steg** |De här stegen måste utföras för att distribuera StorSimple-enheten i produktionen. |
| Steg 1: Skapa en ny tjänst. |Ställ in hantering och lagring i molnet för din StorSimple-enhet. Hoppa över det här steget om du har en befintlig tjänst för andra StorSimple-enheter. |
| Steg 2: Hämta nyckeln för tjänstregistrering. |Använd nyckeln för att registrera och ansluta din StorSimple-enhet till hanteringstjänsten. |
| Steg 3: Konfigurera och registrera enheten via Windows PowerShell för StorSimple. |Anslut enheten till nätverket och registrera den med Azure för att slutföra installationen med hjälp av hanteringstjänsten. |
| Steg 4: Slutför de inställningar som krävs som minimum</br>Valfritt: Uppdatera din StorSimple-enhet. |Använd hanteringstjänsten för att slutföra installationen av enheten och aktivera den för att tillhandahålla lagring. |
| Steg 5: Skapa en volymbehållare. |Skapa en behållare för att etablera volymer. En volymbehållare har lagringskonto, bandbredd och krypteringsinställningar för de volymer som finns i den. |
| Steg 6: Skapa en volym. |Etablera lagringsvolymer på StorSimple-enheten för dina servrar. |
| Steg 7: Montera, initiera och formatera en volym.</br>Valfritt: Konfigurera MPIO. |Anslut dina servrar till den iSCSI-lagring som ingår i enheten. Du kan även välja att konfigurera MPIO för att säkerställa att dina servrar kan tolerera fel på länkar, nätverk och gränssnitt. |
| Steg 8: Gör en säkerhetskopia. |Konfigurera din säkerhetskopieringsprincip för att skydda dina data |
|  | |
| **Andra procedurer** |Du kan behöva använda de här procedurerna när du distribuerar din lösning. |
| Konfigurera ett nytt lagringskonto för tjänsten. | |
| Använd PuTTY för att ansluta till enhetens seriekonsol. | |
| Hämta IQN för en Windows Server-värd. | |
| Skapa en manuell säkerhetskopia. | |

## <a name="deployment-configuration-checklist"></a>Checklista för distributionskonfiguration
Följande checklista för distributionskonfiguration anger den information du behöver samla in innan och under konfigurationen av programvaran på din StorSimple-enhet. Processen för att distribuera StorSimple-enheten i din miljö blir effektivare om en del av den här informationen förbereds i förväg. Använd även den här checklistan för att notera konfigurationsinformationen när du distribuerar din enhet.

| Fas | Parameter | Detaljer | Värden |
| --- | --- | --- | --- |
| **Kabelanslut din enhet** |Seriell åtkomst |Första enhetskonfigurationen |Ja/nej |
|  | | | |
| **Konfigurera och registrera enheten** |Data 0 nätverksinställningar |Data 0 IP-adress:</br>Nätmask:</br>Gateway:</br>Primär DNS-server:</br>Primär NTP-server:</br>Webbproxyserver IP/FQDN (valfritt):</br>Webbproxyport: | |
| &nbsp; |Enhetens administratörslösenord |Lösenordet ska vara mellan 8 och 15 tecken och innehålla gemener, versaler, siffror och specialtecken. | |
| &nbsp; |Lösenordet för StorSimple Snapshot Manager |Lösenordet ska vara 14 eller 15 tecken och innehålla gemener, versaler, siffror och specialtecken. | |
| &nbsp; |Nyckel för tjänstregistrering |Den här nyckeln skapas från den klassiska Azure-portalen. | |
| &nbsp; |Krypteringsnyckel för tjänstdata |Den här nyckeln skapas när enheten registreras med hanteringstjänsten via Windows PowerShell för StorSimple. Kopiera den här nyckeln och spara den på säker plats. | |
|  | | | |
| **Slutför de inställningar som krävs som minimum** |Eget namn på din enhet |Det här är ett beskrivande namn för enheten. | |
| &nbsp; |Tidszon |Enheten använder den här tidszonen för alla schemalagda åtgärder. | |
| &nbsp; |Sekundär DNS-server |Det här måste konfigureras. | |
| &nbsp; |Nätverksgränssnitt: Data 0-kontrollant med statiska IP-adresser |De här IP-adresserna ska vara dirigerbara till internet.</br>Kontrollant 0, statisk IP-adress:</br>Kontrollant 1, statisk IP-adress: | |
|  | | | |
| **Ytterligare inställningar för nätverksgränssnittet** |Nätverksgränssnitt: Data 1</br>Konfigurera inte gatewayen om iSCSI är aktiverad. |Syfte: Molnet/iSCSI/används inte</br>IP-adress:</br>Nätmask:</br>Gateway: | |
| &nbsp; |Nätverksgränssnitt: Data 2</br>Konfigurera inte gatewayen om iSCSI är aktiverad. |Syfte: Molnet/iSCSI/används inte</br>IP-adress:</br>Nätmask:</br>Gateway: | |
| &nbsp; |Nätverksgränssnitt: Data 3</br>Konfigurera inte gatewayen om iSCSI är aktiverad. |Syfte: Molnet/iSCSI/används inte</br>IP-adress:</br>Nätmask:</br>Gateway: | |
| &nbsp; |Nätverksgränssnitt: Data 4</br>Konfigurera inte gatewayen om iSCSI är aktiverad. |Syfte: Molnet/iSCSI/används inte</br>IP-adress:</br>Nätmask:</br>Gateway: | |
| &nbsp; |Nätverksgränssnitt: Data 5</br>Konfigurera inte gatewayen om iSCSI är aktiverad. |Syfte: Molnet/iSCSI/används inte</br>IP-adress:</br>Nätmask:</br>Gateway: | |
|  | | | |
| **Skapa en volymcontainer** |Volymbehållarens namn: |Namn på behållaren | |
| &nbsp; |Azure lagringskonto: |Lagringskontonamn och åtkomstnyckel som ska associeras med volymbehållaren | |
| &nbsp; |Krypteringsnyckel för molnlagring: |Krypteringsnyckeln för lagring i varje behållare | |
|  | | | |
| **Skapa en volym** |Information för varje volym |Volymnamn: | |
| &nbsp; |&nbsp; |Storlek: | |
| &nbsp; |&nbsp; |Användningstyp: | |
| &nbsp; |&nbsp; |ACR-namn: | |
| &nbsp; |&nbsp; |Standardprincip för säkerhetskopiering: | |
|  | | | |
| **Montera, initiera och formatera en volym** |Information om varje värdserver som ansluter till lagringen |Windows Server namn: | |
| &nbsp; |&nbsp; |Windows Server IQN: | |
| &nbsp; |&nbsp; |Windows Server volymnamn: | |
| &nbsp; |&nbsp; |NTFS monteringspunkt/enhetsbeteckning: | |

## <a name="deployment-prerequisites"></a>Distributionskrav
I följande avsnitt beskrivs förutsättningarna för konfiguration av din StorSimple Manager-tjänst och StorSimple-enheten.

### <a name="for-the-storsimple-manager-service"></a>För StorSimple Manager-tjänsten
Innan du börjar bör du kontrollera att:

* Du har ditt Microsoft-konto med autentiseringsuppgifter.
* Du har ditt Microsoft Azure lagringskonto med autentiseringsuppgifter.
* Microsoft Azure-prenumerationen har aktiverats för StorSimple Manager-tjänsten. Din prenumeration ska köpas via [Enterprise-avtalet](https://azure.microsoft.com/pricing/enterprise-agreement/).
* Du har åtkomst till programvara för terminalemulering, till exempel PuTTY.

### <a name="for-the-device-in-the-datacenter"></a>För enheten i datacentret
Innan du konfigurerar enheten bör du kontrollera att:

* Din enhet är helt uppackad, rackmonterad och helt kabelansluten till ström, nätverk och serieåtkomst enligt beskrivningen i:
  
  * [Packa upp, rackmontera och kabelanslut din 8100-enhet](storsimple-8100-hardware-installation.md)
  * [Packa upp, rackmontera och kabelanslut din 8600-enhet](storsimple-8600-hardware-installation.md)

### <a name="for-the-network-in-the-datacenter"></a>För nätverket i datacentret
Innan du börjar ska du kontrollera att:

* Portarna i ditt datacenters brandvägg är öppna för att möjliggöra iSCSI- och molntrafik enligt beskrivningen i [Nätverkskrav för din StorSimple-enhet](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).

## <a name="step-by-step-deployment"></a>Steg för steg-distribution
Utför nedanstående steg för steg-instruktioner för att distribuera StorSimple-enheten i datacentret.

## <a name="step-1-create-a-new-service"></a>Steg 1: Skapa en ny tjänst
En StorSimple Manager-tjänst kan hantera flera StorSimple-enheter. Utför följande steg för att skapa en ny instans av StorSimple Manager-tjänsten.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [!IMPORTANT]
> Om du inte har aktiverat automatiskt skapande av lagringskonton med din tjänst måste du skapa minst ett lagringskonto efter att du har skapat en tjänst. Det här lagringskontot används när du skapar en volymbehållare.
> 
> * Om du inte har skapat ett lagringskonto automatiskt går du till [Konfigurera ett nytt lagringskonto för tjänsten](#configure-a-new-storage-account-for-the-service) för detaljerade anvisningar.
> * Om du har aktiverat automatiskt skapande av ett lagringskonto går du till [steg 2: hämta nyckel för tjänstregistrering](#step-2-get-the-service-registration-key).
> 
> 

## <a name="step-2-get-the-service-registration-key"></a>Steg 2: Hämta nyckel för tjänstregistrering
När StorSimple Manager-tjänsten är igång hämtar du nyckeln för tjänstregistrering. Den här nyckeln används för att registrera och ansluta din StorSimple-enhet till tjänsten.

Utför nedanstående steg i den klassiska Azure-portalen.

[!INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Steg 3: Konfigurera och registrera enheten via Windows PowerShell för StorSimple
Använd Windows PowerShell för StorSimple för att slutföra den förberedande inställningen av StorSimple-enheten enligt nedanstående procedur. Du måste använda programvara för terminalemulering för att slutföra det här steget. Mer information finns i [Använda PuTTY för att ansluta till enhetens seriekonsol](#use-putty-to-connect-to-the-device-serial-console).

[!INCLUDE [storsimple-configure-and-register-device-u1](../../includes/storsimple-configure-and-register-device-u1.md)]

## <a name="step-4-complete-minimum-device-setup"></a>Steg 4: Slutför de inställningar som krävs som minimum
Som minimikrav för konfigurationen av din StorSimple-enhet måste du:

* Ställa in den sekundära DNS-servern.
* Aktivera iSCSI i minst ett nätverksgränssnitt.
* Tilldela båda domänkontrollanterna statiska IP-adresser.

Utför nedanstående steg i den klassiska Azure-portalen för att uppfylla minimikraven för konfiguration av enheten.

[!INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## <a name="step-5-create-a-volume-container"></a>Steg 5: Skapa en volymbehållare
En volymbehållare har lagringskonto, bandbredd och krypteringsinställningar för de volymer som finns i den. Du måste skapa en volymbehållare innan du kan börja etablera volymer på StorSimple-enheten.

Utför följande steg i den klassiska Azure-portalen för att skapa en volymbehållare.

[!INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>Steg 6: Skapa en volym
När du har skapat en volymbehållare kan du etablera en lagringsvolym på StorSimple-enheten för dina servrar. Utför följande steg i den klassiska Azure-portalen för att skapa en volym.

> [!IMPORTANT]
> StorSimple Manager kan endast skapa tunt allokerade volymer. Du kan inte skapa helt eller delvis etablerade volymer.
> 
> 

[!INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>Steg 7: Montera, initiera och formatera en volym
Följande steg utförs på din Windows Server-värd.

> [!IMPORTANT]
> * För att få hög tillgänglighet i StorSimple-lösningen rekommenderar vi att du konfigurerar MPIO på dina värdservrar (valfritt) innan du konfigurerar iSCSI. MPIO-konfiguration på värdservrar säkerställer att servrarna kan tolerera fel på en länk, ett nätverk eller gränssnitt.
> * Anvisningar för installation och konfiguration av MPIO och iSCSI på Windows Server-värden finns i [Konfigurera MPIO för din StorSimple-enhet](storsimple-configure-mpio-windows-server.md). Här finns även steg för att montera, initiera och formatera StorSimple-volymer.
> * Anvisningar för installation och konfiguration av MPIO och iSCSI på Linux-värdar finns i [Konfigurera MPIO för din StorSimple Linux-värd](storsimple-configure-mpio-on-linux.md)
> 
> 

Utför nedanstående steg för att montera, initiera och formatera dina StorSimple-volymer på en Windows Server-värd om du väljer att inte konfigurera MPIO.

[!INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>Steg 8: Gör en säkerhetskopia
Säkerhetskopieringar ger tidpunktsskydd för volymer och förbättrar återställningsmöjligheterna samtidigt som återställningstiderna minimeras. Du kan utföra två typer av säkerhetskopiering på StorSimple-enheten: lokala ögonblicksbilder och molnögonblicksbilder. Båda av de här säkerhetskopieringstyperna kan vara antingen **schemalagda** eller **manuella**.

Utför nedanstående steg i den klassiska Azure-portalen för att skapa en schemalagd säkerhetskopiering.

[!INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

Du kan utföra en manuell säkerhetskopiering när som helst. Gå till [Skapa en manuell säkerhetskopiering](#create-a-manual-backup) för anvisningar.

## <a name="configure-a-new-storage-account-for-the-service"></a>Konfigurera ett nytt lagringskonto för tjänsten
Det här är ett valfritt steg som du endast måste utföra om du inte har aktiverat automatiskt skapande av lagringskonton med din tjänst. Ett Microsoft Azure-lagringskonto krävs för att skapa en behållare för StorSimple-volymer.

Om du behöver skapa ett Azure-lagringskonto i en annan region finns stegvisa instruktioner i [Om Azure lagringskonton](../storage/common/storage-create-storage-account.md).

Utför följande steg i den klassiska Azure-portalen på sidan för **StorSimple Manager-tjänsten**.

[!INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]

## <a name="use-putty-to-connect-to-the-device-serial-console"></a>Använd PuTTY för att ansluta till enhetens seriekonsol
För att ansluta till Windows PowerShell för StorSimple behövs en programvara för terminalemulering, till exempel PuTTY. Du kan använda PuTTY när åtkomst till enheten sker direkt via seriekonsolen eller genom att öppna en Telnet-session från en fjärrdator.

[!INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>Söka efter och installera uppdateringar
Det kan ta flera timmar att uppdatera din enhet. Utför stegen nedan för att söka efter och installera uppdateringar på din enhet.
<!--can take 1-4 hours-->

<!--If you have a gateway configured on a network interface other than Data 0, you will need to disable Data 2 and Data 3 network interfaces before installing the update. Go to **Devices > Configure** and disable Data 2 and Data 3 interfaces. You should re-enable these interfaces after the device is updated.-->

#### <a name="to-update-your-device"></a>För att uppdatera din enhet
1. På enhetens sida **Snabbstart** klickar du på **Enheter**. Välj den fysiska enheten, klicka på **Underhåll** och klicka sedan på **Sök efter uppdateringar**.  
2. Det skapas ett jobb för att söka efter uppdateringar. Om uppdateringar är tillgängliga ändras **Sök efter uppdateringar** till **Installera uppdateringar**. Klicka på **Installera uppdateringar**.
3. Ett uppdateringsjobb skapas. Övervaka statusen för uppdateringen genom att navigera till **Jobb**.
   
   > [!NOTE]
   > När uppdateringsjobbet startar visas omedelbart statusen som 50 procent. Statusen ändras till 100 procent först när uppdateringsjobbet har slutförts. Det finns ingen realtidsstatus för uppdateringsprocessen.
   > 
   > 
4. När enheten har uppdaterats aktiverar du nätverksgränssnitten Data 2 och Data 3 om de inaktiverades.

<!-- In step 2, you may be requested to disable Data 2 and Data 3 prior to installing the updates. You must disable these network interfaces or the updates may fail.-->

## <a name="get-the-iqn-of-a-windows-server-host"></a>Hämta IQN för en Windows Server-värd
Utför stegen nedan för att få det kvalificerade iSCSI-namnet (IQN) för en Windows-värd som kör Windows Server® 2012.

[!INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Skapa en manuell säkerhetskopia
Utför stegen nedan i den klassiska Azure-portalen för att skapa en manuell säkerhetskopiering för en enskild volym på din StorSimple-enhet som kan utföras vid behov.

[!INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup.md)]

## <a name="configure-mpio"></a>Konfigurera MPIO
MPIO (Multipath I/O) är en valfri funktion som inte är installerad på Windows Server som standard. Den installeras som en funktion via Server Manager. Anvisningar för MPIO-installation finns i [Konfigurera MPIO för din StorSimple-enhet](storsimple-configure-mpio-windows-server.md).

Anvisningar för MPIO-installation på en StorSimple-enhet som är ansluten till en Linux-värd finns i [Konfigurera MPIO för din StorSimple Linux-värd](storsimple-configure-mpio-on-linux.md)

> [!NOTE]
> MPIO stöds inte på virtuella StorSimple-enheter.
> 
> 

## <a name="next-steps"></a>Nästa steg
* Konfigurera en [virtuell enhet](storsimple-virtual-device-u2.md).
* Använd [StorSimple Manager-tjänsten](storsimple-manager-service-administration.md) för att hantera en virtuell enhet.


