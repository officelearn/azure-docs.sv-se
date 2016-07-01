<properties 
   pageTitle="Distribuera din StorSimple-enhet (Uppdatering 2) | Microsoft Azure"
   description="Beskriver stegen och bästa praxis för att distribuera StorSimple-enheten och tjänsten för Uppdatering 2."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/20/2016"
   ms.author="alkohli" />

# Distribuera din lokala StorSimple-enhet (Uppdatering 2)

> [AZURE.SELECTOR]
- [Uppdatering 2](../articles/storsimple/storsimple-deployment-walkthrough-u2.md)
- [Uppdatering 1](../articles/storsimple/storsimple-deployment-walkthrough-u1.md)
- [GA-version](../articles/storsimple/storsimple-deployment-walkthrough.md)

## Översikt

Välkommen till distribution av Microsoft Azure StorSimple-enheten. Dessa självstudiekurser för distribution gäller StorSimple-serien 8000 Uppdatering 2. Denna serie självstudier innehåller en checklista för konfiguration, konfigurationskrav och detaljerade konfigurationssteg för din StorSimple-enhet.

Informationen i dessa självstudiekurser förutsätter att du har granskat säkerhetsåtgärder, packat upp, rackmonterat och kabelanslutit din StorSimple-enhet. Om du fortfarande behöver utföra dessa uppgifter ska du börja med att granska [säkerhetsåtgärderna](storsimple-safety.md). Följ de specifika instruktionerna för enheten för att packa upp, rackmontera och kabelansluta den.

- [Packa upp, rackmontera och dra kabel till din 8100](storsimple-8100-hardware-installation.md)
- [Packa upp, rackmontera och dra kabel till din 8600](storsimple-8600-hardware-installation.md)

Du måste ha administratörsbehörighet för att slutföra installationen och konfigurationen. Vi rekommenderar att du läser checklistan för konfiguration  innan du börjar. Processen för distribution och konfiguration kan ta lite tid att slutföra.

> [AZURE.NOTE] Distributionsinformationen för StorSimple som publiceras på webbplatsen Microsoft Azure gäller endast StorSimple-enheter i 8000-serien. Fullständig information om enheterna i 7000-serien finns i: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Distributionsinformation för 7000-serien finns i [Snabbstartsguide för StorSimple-system](http://onlinehelp.storsimple.com/111_Appliance/).

## Distributionssteg

Genomför dessa steg för att konfigurera din StorSimple-enhet och ansluta den till din StorSimple Manager-tjänst. Förutom de steg som krävs finns valfria steg och procedurer du kan behöva under distributionen. Steg för steg-instruktionerna för distribution anger när du bör utföra varje valfritt steg.


| Steg                                                                                   | Beskrivning                                                                                                                                                   |
|----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **KRAV**                                                                      | Dessa måste slutföras som del av förberedelserna inför den kommande distributionen.                                                                                        |
| [Checklista för distributionskonfiguration](#deployment-configuration-checklist)                                                     | Använd den här checklistan för att samla in och registrera information före och under distributionen.                                                                       |
| [Distributionskrav](#deployment-prerequisites)                                                               | Dessa verifierar att miljön är klar för distribution.                                                                                                     |
|                                                                                        |                                                                                                                                                               |
| **STEG FÖR STEG-DISTRIBUTION**                                                                   | Dessa steg krävs för att distribuera StorSimple-enheten i produktionen.                                                                                      |
| [Steg 1: Skapa en ny tjänst](#step-1-create-a-new-service)                                                         | Ställ in hantering och lagring i molnet för din StorSimple-enhet. *Hoppa över det här steget om du har en befintlig tjänst för andra StorSimple-enheter*.                |
| [Steg 2: Hämta nyckel för tjänstregistrering](#step-2-get-the-service-registration-key)                                               | Använd nyckeln för att registrera och ansluta din StorSimple-enhet till hanteringstjänsten.                                                                         |
| [Steg 3: Konfigurera och registrera enheten via Windows PowerShell för StorSimple](#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)    | Anslut enheten till nätverket och registrera den med Azure för att slutföra installationen med hjälp av hanteringstjänsten.                                            |
| [Steg 4: Slutför de inställningar som krävs som minimum](#step-4-complete-minimum-device-setupd)</br>[Valfritt: Uppdatera din StorSimple-enhet](#scan-for-and-apply-updates)      | Använd hanteringstjänsten för att slutföra installationen av enheten och aktivera den för att tillhandahålla lagring.                                                                      |
| [Steg 5: Skapa en volymbehållare](#step-5-create-a-volume-container)                                                      | Skapa en behållare för att etablera volymer. En volymbehållare har lagringskonto, bandbredd och krypteringsinställningar för de volymer som finns i den.    |
| [Steg 6: Skapa en volym](#step-6-create-a-volume)                                                                | Etablera lagringsvolymer på StorSimple-enheten för dina servrar.                                                                                        |
| [Steg 7: Montera, initiera och formatera en volym](#step-7-mount-initialize-and-format-a-volume)</br>[Valfritt: Konfigurera MPIO](storsimple-configure-mpio-windows-server.md)            | Anslut dina servrar till den iSCSI-lagring som ingår i enheten. Du kan även välja att konfigurera MPIO för att säkerställa att dina servrar kan tolerera fel på länkar, nätverk och gränssnitt.                                                                                                                                                              |
| [Steg 8: Gör en säkerhetskopia](#step-8-take-a-backup)                                                                  | Konfigurera din säkerhetskopieringsprincip för att skydda dina data                                                                                                                 |
|                                                                                        |                                                                                                                                                               |
| **ANDRA PROCEDURER**                                                                   | Du kan behöva hänvisa till dessa procedurer när du distribuerar din lösning.                                                                                        |
| [Konfigurera ett nytt lagringskonto för tjänsten](#configure-a-new-storage-account-for-the-service)                                      |                                                                                                                                                               |
| [Använd PuTTY för att ansluta till enhetens seriekonsol](#use-putty-to-connect-to-the-device-serial-console)                                    |                                                                                                                                                               |
| [Hämta IQN för en Windows Server-värd](#get-the-iqn-of-a-windows-server-host)                                                   |                                                                                                                                                               |
| [Skapa en manuell säkerhetskopia](#create-a-manual-backup)                                                                 | 


## Checklista för distributionskonfiguration

Innan du distribuerar din enhet måste du samla in information för att konfigurera programvaran på din StorSimple-enhet. Processen för att distribuera StorSimple-enheten i din miljö blir effektivare om en del av den här informationen förbereds i förväg. Hämta och använd den här checklistan för att notera konfigurationsinformationen när du distribuerar din enhet.

- [Hämta checklistan för distributionskonfiguration för StorSimple](http://www.microsoft.com/download/details.aspx?id=49159)


## Distributionskrav

I följande avsnitt beskrivs kraven för konfiguration av din StorSimple Manager-tjänst och StorSimple-enheten.

### För StorSimple Manager-tjänsten

Innan du börjar ska du kontrollera att:

- Du har ditt Microsoft-konto med autentiseringsuppgifter.

- Du har ditt Microsoft Azure lagringskonto med autentiseringsuppgifter.

- Microsoft Azure-prenumerationen har aktiverats för StorSimple Manager-tjänsten. Din prenumeration ska köpas via [Enterprise-avtalet](https://azure.microsoft.com/pricing/enterprise-agreement/).

- Du har åtkomst till programvara för terminalemulering, till exempel PuTTY.

### För enheten i datacentret

Innan enheten konfigureras ska du säkerställa att den är helt uppackad, rackmonterad och helt kabelansluten till ström, nätverk och serieåtkomst som beskrivs i:

-  [Packa upp, rackmontera och kabelanslut din 8100-enhet](storsimple-8100-hardware-installation.md)
-  [Packa upp, rackmontera och kabelanslut din 8600-enhet](storsimple-8600-hardware-installation.md)


### För nätverket i datacentret

Innan du börjar ska du kontrollera att:

- Portarna i ditt datacenters brandvägg är öppna för att möjliggöra iSCSI- och molntrafik som beskrivs i [Nätverkskrav för din StorSimple-enhet](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).


## Steg för steg-distribution

Använd följande steg för steg-instruktioner för att distribuera StorSimple-enheten i datacentret.

## Steg 1: Skapa en ny tjänst

En StorSimple Manager-tjänst kan hantera flera StorSimple-enheter. Utför följande steg för att skapa en ny instans av StorSimple Manager-tjänsten.

[AZURE.INCLUDE [storsimple-create-new-service](../../includes/storsimple-create-new-service.md)]

> [AZURE.IMPORTANT] Om du inte har aktiverat automatiskt skapande av ett lagringskonto med din tjänst, behöver du skapa minst ett lagringskonto efter att du har skapat en tjänst. Det här lagringskontot används när du skapar en volymbehållare. 
>
> * Om du inte har skapat ett lagringskonto automatiskt, ska du gå till [Konfigurera ett nytt lagringskonto för tjänsten](#configure-a-new-storage-account-for-the-service) för detaljerade anvisningar. 
> * Om du har aktiverat automatiskt skapande av ett lagringskonto, gå till [Steg 2: Hämta nyckel för tjänstregistrering](#step-2-get-the-service-registration-key).

## Steg 2: Hämta nyckel för tjänstregistrering

När StorSimple Manager-tjänsten är igång kan behöver du hämta nyckeln för tjänstregistrering. Den här nyckeln används för att registrera och ansluta din StorSimple-enhet till tjänsten.

Utför följande steg i hanteringsportalen.

[AZURE.INCLUDE [storsimple-get-service-registration-key](../../includes/storsimple-get-service-registration-key.md)]


## Steg 3: Konfigurera och registrera enheten via Windows PowerShell för StorSimple

Använd Windows PowerShell för StorSimple för att slutföra installationen av StorSimple-enheten som beskrivs i följande procedur. Du måste använda programvara för terminalemulering för att slutföra det här steget. Mer information finns i [Använda PuTTY för att ansluta till enhetens seriekonsol](#use-putty-to-connect-to-the-device-serial-console).

[AZURE.INCLUDE [storsimple-configure-and-register-device-u1](../../includes/storsimple-configure-and-register-device-u1.md)]

## Steg 4: Slutför de inställningar som krävs som minimum

Som minimikrav för konfigurationen av din StorSimple-enhet måste du: 

- Gör inställningar för den sekundära DNS-servern.
- Aktivera iSCSI för minst ett gränssnitt.
- Tilldela båda domänkontrollanterna statiska IP-adresser.

Utför följande steg i hanteringsportalen för att uppfylla enhetens minimikrav för konfiguration.

[AZURE.INCLUDE [storsimple-complete-minimum-device-setup](../../includes/storsimple-complete-minimum-device-setup-u1.md)]

## Steg 5: Skapa en volymbehållare

En volymbehållare har lagringskonto, bandbredd och krypteringsinställningar för de volymer som finns i den. Du måste skapa en volymbehållare innan du kan börja etablera volymer på StorSimple-enheten. 

Utför följande steg i hanteringsportalen för att skapa en volymbehållare.

[AZURE.INCLUDE [storsimple-create-volume-container](../../includes/storsimple-create-volume-container.md)]

## Steg 6: Skapa en volym

När du skapar en volymbehållare kan du etablera du en lagringsvolym på StorSimple-enheten för dina servrar. Utför följande steg i hanteringsportalen för att skapa en volym.

> [AZURE.IMPORTANT] StorSimple Manager kan både skapa tunt eller helt allokerade volymer. Du kan dock inte skapa delvis allokerade volymer. 

[AZURE.INCLUDE [storsimple-create-volume](../../includes/storsimple-create-volume-u2.md)]

## Steg 7: Montera, initiera och formatera en volym

Följande steg utförs på din Windows Server-värd. 


> [AZURE.IMPORTANT]

> - För att få hög tillgänglighet i StorSimple-lösningen rekommenderar vi att du konfigurerar MPIO på dina värdservrar (valfritt) innan du konfigurerar iSCSI. MPIO-konfiguration på värdservrar säkerställer att servrarna kan tolerera fel på en länk, ett nätverk eller gränssnitt.

> - Anvisningar för MPIO och iSCSI-installation och konfiguration på Windows Server-värden finns i [Konfigurera MPIO för din StorSimple-enhet](storsimple-configure-mpio-windows-server.md). Dessa inkluderar även steg för att montera, initiera och formatera StorSimple-volymer.

> - Anvisningar för MPIO och iSCSI-installation och konfiguration på Linux-värden finns i [Konfigurera MPIO för din StorSimple Linux-värd](storsimple-configure-mpio-on-linux.md)

Utför följande steg för att montera, initiera och formatera dina StorSimple-volymer på en Windows Server-värd om du väljer att inte konfigurera MPIO.

[AZURE.INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## Steg 8: Gör en säkerhetskopia

Säkerhetskopieringar ger tidpunktsskydd för volymer och förbättrar återställningen samtidigt som återställningstiderna minimeras. Du kan utföra två typer av säkerhetskopiering på StorSimple-enheten: lokala ögonblicksbilder och molnögonblicksbilder. Båda av dessa säkerhetskopieringstyper kan vara **schemalagda** eller **manuella**. 

Utför följande steg i hanteringsportalen för att skapa en schemalagd säkerhetskopiering.

[AZURE.INCLUDE [storsimple-take-backup](../../includes/storsimple-take-backup.md)]

Du kan utföra en manuell säkerhetskopiering när som helst. Gå till [Skapa en manuell säkerhetskopiering](#create-a-manual-backup) för anvisningar. 

## Konfigurera ett nytt lagringskonto för tjänsten

Det här är ett valfritt steg som du endast måste utföra om du inte har aktiverat automatiskt skapande av ett lagringskonto med din tjänst. Ett Microsoft Azure lagringskonto krävs för att skapa en behållare för StorSimple- volymer.

Om du behöver skapa ett Azure lagringskonto i en annan region finns stegvisa instruktioner i [Om Azure lagringskonton](../storage/storage-create-storage-account.md).

Utför följande steg i hanteringsportalen på sidan för **StorSimple Manager-tjänsten**.

[AZURE.INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-configure-new-storage-account-u1.md)]


## Använd PuTTY för att ansluta till enhetens seriekonsol

För att ansluta till Windows PowerShell för StorSimple behöver du använda en programvara för terminalemulering, till exempel PuTTY. Du kan använda PuTTY när åtkomst till enheten sker direkt via seriekonsolen eller genom att öppna en Telnet-session från en fjärrdator.

[AZURE.INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]


## Söka efter och installera uppdateringar

Det kan ta flera timmar att uppdatera din enhet. Utför följande steg för att söka efter och installera uppdateringar på din enhet.
<!--can take 1-4 hours--> 

<!--If you have a gateway configured on a network interface other than Data 0, you will need to disable Data 2 and Data 3 network interfaces before installing the update. Go to **Devices > Configure** and disable Data 2 and Data 3 interfaces. You should re-enable these interfaces after the device is updated.-->

#### För att uppdatera din enhet

1.  På enheten **Snabbstart** klickar du på **Enheter**. Välj den fysiska enheten, klicka på **Underhåll** och klicka sedan på **Sök efter uppdateringar**.  

2.  Det skapas ett jobb för att söka efter uppdateringar. Om uppdateringar är tillgängliga ändras **Sök efter uppdateringar** till **Installera uppdateringar**. Klicka på **Installera uppdateringar**. 

3.  Ett uppdateringsjobb skapas. Övervaka statusen för uppdateringen genom att navigera till **Jobb**.

    > [AZURE.NOTE] När uppdateringsjobbet startar visas omedelbart statusen som 50 procent. Statusen ändras till 100 procent först när uppdateringsjobbet har slutförts. Det finns ingen realtidsstatus för uppdateringsprocessen.

4.  När enheten har uppdaterats, ska du aktivera nätverksgränssnitten Data 2 och Data 3 om dessa inaktiverades.

<!-- In step 2, you may be requested to disable Data 2 and Data 3 prior to installing the updates. You must disable these network interfaces or the updates may fail.-->

## Hämta IQN för en Windows Server-värd

Utför följande steg för att få det kvalificerade iSCSI-namnet (IQN) för en Windows-värd som kör Windows Server® 2012.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-get-iqn.md)]

## Skapa en manuell säkerhetskopia

Utför följande steg i hanteringsportalen för att på begäran kunna skapa en manuell säkerhetskopiering för en enskild volym på din StorSimple-enhet.

[AZURE.INCLUDE [Create a manual backup](../../includes/storsimple-create-manual-backup.md)]


## Nästa steg

- Konfigurera en [virtuell enhet](storsimple-virtual-device-u2.md).

- Använd [StorSimple Manager-tjänsten](storsimple-manager-service-administration.md) för att hantera en virtuell enhet.
 



<!--HONumber=Jun16_HO2-->


