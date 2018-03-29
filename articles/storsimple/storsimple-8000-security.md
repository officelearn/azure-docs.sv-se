---
title: Säkerhet för StorSimple 8000-serien | Microsoft Docs
description: Beskriver de funktioner för säkerhet och sekretess som skyddar din StorSimple-tjänsten, enheter och data lokalt och i molnet.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/27/2018
ms.author: alkohli
ms.openlocfilehash: ee0534ab8bc3400854ddf550df9ca2f395f8d80d
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
# <a name="storsimple-security-and-data-protection"></a>StorSimple-säkerhet och dataskydd

## <a name="overview"></a>Översikt

Säkerhet är en stor utmaning för alla som inför en ny teknik, särskilt när tekniken som används med konfidentiella eller skyddade data. Medan du utvärderar olika tekniker måste du ökade risker och kostnader för dataskydd. Microsoft Azure StorSimple ger både säkerhet och sekretess lösning för dataskydd, vilket hjälper dig för att kontrollera:

* **Sekretess** – endast entiteter auktoriserade kan visa dina data.
* **Integritet** – endast behöriga entiteter kan ändra eller ta bort dina data.

Microsoft Azure StorSimple-lösningen består av fyra huvudsakliga komponenter som samverkar med varandra:

* **Enhetshanteraren för StorSimple-tjänst som finns i Microsoft Azure** – hanteringstjänsten som används för att konfigurera och etablera StorSimple-enhet.
* **StorSimple-enhet** – en fysisk enhet som är installerad i ditt datacenter. Alla värdar och -klienter som genererar data ansluta till StorSimple-enheten och enheten hanterar data och flyttar det till Azure-molnet vid behov.
* **Klienter/värdar som är anslutna till enheten** – klienter i din infrastruktur som ansluter till StorSimple-enhet och generera data som måste skyddas.
* **Molnlagring** – platsen i Azure-molnet var data lagras.

I följande avsnitt beskrivs StorSimple-säkerhetsfunktioner som skyddar var och en av dessa komponenter och de data som lagras på dem. Den innehåller också en lista med frågor som kan uppstå om Microsoft Azure StorSimple-säkerhet och motsvarande svar.

## <a name="storsimple-device-manager-service-protection"></a>StorSimple Enhetshanteraren service skydd

StorSimple Device Manager-tjänsten är en management-tjänst finns i Microsoft Azure och används för att hantera alla StorSimple-enheter som din organisation har skaffat. Du kan komma åt StorSimple enheten Manager-tjänsten med hjälp av din organisations autentiseringsuppgifter för inloggning till Azure-portalen via en webbläsare.

Åtkomst till StorSimple Device Manager-tjänsten kräver att din organisation har en Azure-prenumeration som innehåller StorSimple. Din prenumeration styr funktioner som du kan komma åt i Azure-portalen. Om din organisation inte har en Azure-prenumeration och du vill veta mer om dem, se [registrera dig för Azure som en organisation](../active-directory/sign-up-organization.md).

Eftersom StorSimple enheten Manager-tjänsten finns i Azure, skyddas den av säkerhetsfunktionerna i Azure. Läs mer om säkerhetsfunktioner som tillhandahålls av Microsoft Azure, gå till den [Microsoft Azure Trust Center](https://azure.microsoft.com/support/trust-center/security/).

## <a name="storsimple-device-protection"></a>Skydd för StorSimple-enhet

StorSimple-enhet är en lokal hybrid-lagringsenhet som innehåller solid-state-hårddiskar (SSD) och hårddiskenheter (HDD) tillsammans med funktioner för automatisk redundans och redundanta styrenheter. Domänkontrollanterna hantera lagring skiktning, placera för närvarande används (eller varm) data på lokal lagring (i StorSimple-enhet eller lokala servrar), när mindre ofta använda data till molnet.

Enbart behöriga StorSimple enheter tillåts att ansluta till tjänsten StorSimple Enhetshanteraren som du skapade i Azure-prenumerationen. För att auktorisera en enhet, måste du registrera den med StorSimple Device Manager-tjänsten genom att tillhandahålla nyckel för tjänstregistrering. Nyckeln för tjänstregistrering är en 128-bitars slumpmässig nyckel genereras i Azure-portalen.

![Nyckel för tjänstregistrering](./media/storsimple-security/ServiceRegistrationKey.png)

Mer information hur få en nyckel för tjänstregistrering, gå till [steg 2: Hämta nyckel för tjänstregistrering](storsimple-8000-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key).

Nyckeln för tjänstregistrering är en lång nyckel som innehåller 100 tecken. Du kan kopiera nyckeln och spara den i en textfil på en säker plats så att du kan använda för att godkänna ytterligare enheter efter behov. Om nyckeln för tjänstregistrering försvinner när du har registrerat din första enhet, kan du generera en ny nyckel från StorSimple enheten Manager-tjänsten. Detta påverkar inte användningen av befintliga enheter.

När en enhet har registrerats använder token för att kommunicera med Microsoft Azure. Nyckeln för tjänstregistrering används inte när registrering av enheten.

> [!NOTE]
> Vi rekommenderar att du återskapar nyckeln för tjänstregistrering efter varje användning.


## <a name="protect-your-storsimple-solution-via-passwords"></a>Skydda din StorSimple-lösning via lösenord

Lösenord är en viktig del av datorsäkerhet och används överallt i StorSimple-lösningen för att säkerställa att dina data är tillgängliga för auktoriserade användare. StorSimple kan du konfigurera följande lösenord:

* StorSimple-enhetens administratörslösenord
* Challenge Handshake Authentication Protocol (CHAP) initierare och mål-lösenord
* Lösenordet för StorSimple Snapshot Manager

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Windows PowerShell för StorSimple och StorSimple-enhetens administratörslösenord

Windows PowerShell för StorSimple är ett kommandoradsgränssnitt som du kan använda för att hantera StorSimple-enhet. Windows PowerShell för StorSimple har funktioner som gör att du kan registrera din enhet, konfigurera nätverksgränssnittet på din enhet, installera vissa typer av uppdateringar, felsöka enheten genom att öppna supportsessionen och ändra enhetens tillstånd. Du kan komma åt Windows PowerShell för StorSimple genom att ansluta till seriekonsolen på enheten eller genom att använda Windows PowerShell-fjärrkommunikation.

PowerShell-fjärrkommunikation kan ske över HTTPS eller HTTP. Om fjärrhantering över HTTPS är aktiverat måste hämta certifikat för fjärrhantering från enheten och installera den på den fjärranslutna klienten. Mer information om PowerShell-fjärrkommunikation går du till [Anslut till din StorSimple-enhet via fjärranslutning](storsimple-8000-remote-connect.md).

När du använder Windows PowerShell för StorSimple för att ansluta till enheten, måste du ange enhetens administratörslösenord att logga in på enheten.

![Enhetens administratörslösenord](./media/storsimple-security/DeviceAdminPW.png)

Tänk på följande metodtips:

* Fjärrhantering är inaktiverat som standard. Du kan använda Enhetshanteraren för StorSimple-tjänsten för att aktivera den. Som en säkerhetsåtgärd fjärråtkomst ska aktiveras endast under den tidsperiod som faktiskt krävs.
* Om du ändrar lösenordet måste du meddela alla fjärranslutna användare så att de inte har ett oväntat anslutningen inte bryts.
* StorSimple enheten Manager-tjänsten kan inte hämta befintliga lösenord: den kan bara återställa dem. Vi rekommenderar att du lagrar alla lösenord på en säker plats så att du inte behöver återställa ett lösenord om det har glömt. Om du behöver återställa ett lösenord, måste du meddela alla användare innan du återställa den.

Du kan komma åt Windows PowerShell-gränssnittet med hjälp av en seriell anslutning till enheten. Du kan också komma åt den via fjärranslutning via HTTP eller HTTPS, som ger ökad säkerhet. HTTPS ger högre säkerhet än en serie- eller HTTP-anslutning. Om du vill använda HTTPS måste du dock först installera ett certifikat på den klientdator som ska få åtkomst till enheten. Du kan hämta fjärråtkomstcertifikatet från konfigurationssidan för enheten i Enhetshanteraren för StorSimple-tjänsten. Om certifikatet för fjärråtkomst tappas bort, måste du hämta ett nytt certifikat och sprids till alla klienter som har behörighet att använda remote management.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Challenge Handshake Authentication Protocol (CHAP) initierare och mål-lösenord

CHAP är ett autentiseringsschema som används av StorSimple-enhet för att verifiera identiteten för fjärrklienter. Verifieringen är baserad på ett delat lösenord. CHAP kan vara enkelriktade (enkelriktad) eller ömsesidig (dubbelriktad). Enkelriktade CHAP målet (StorSimple-enhet) som autentiserar en initierare (värd). Ömsesidig eller omvänd CHAP kräver att målet autentisera initieraren och sedan initieraren autentisera målet. Din StorSimple kan konfigureras för att använda någon av metoderna.

Tänk på följande när du konfigurerar CHAP:

* CHAP-användarnamn måste innehålla färre än 233 tecken.
* CHAP-lösenord måste vara mellan 12 och 16 tecken. Försök att använda ett längre användarnamn eller lösenord resulterar i ett autentiseringsfel på Windows-värd.
* Du kan inte använda samma lösenord för både CHAP-initieraren och CHAP-målet.
* När du har angett lösenordet kan ändras, men det går inte att hämta. Om lösenordet har ändrats, måste du meddela alla fjärranslutna användare så att de kan ansluta till StorSimple-enhet.

Mer information om CHAP och hur du konfigurerar för din StorSimple-lösning, gå till [konfigurera CHAP för din StorSimple-enhet](storsimple-8000-configure-chap.md).

### <a name="storsimple-snapshot-manager-password"></a>Lösenordet för StorSimple Snapshot Manager

StorSimple Snapshot Manager är en snapin-modul i Microsoft Management Console (MMC) som använder volymen grupper och tjänsten Windows Volume Shadow Copy för att generera programkonsekvent säkerhetskopiering. Du kan dessutom använda StorSimple Snapshot Manager för att skapa scheman för säkerhetskopiering och klona eller återställa volymer.

När du konfigurerar en enhet för att använda StorSimple Snapshot Manager uppmanas du att ange lösenordet för StorSimple Snapshot Manager. Lösenordet anges först i Windows PowerShell för StorSimple under registreringen. Lösenordet kan också ställa in och ändras från StorSimple enheten Manager-tjänsten. Det här lösenordet autentiserar enheten med StorSimple Snapshot Manager.

![Lösenordet för StorSimple Snapshot Manager](./media/storsimple-security/SnapshotMgrPassword.png)

Lösenordet för StorSimple Snapshot Manager måste vara 14 och 15 tecken och måste innehålla minst 3 av en kombination av versaler, gemener, siffror och särskilda tecken. När du har angett lösenordet för StorSimple Snapshot Manager kan ändras, men det går inte att hämta. Om du ändrar lösenordet måste du meddela alla fjärranvändare.

Mer information om StorSimple Snapshot Manager går du till [vad är StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Metodtips för lösenord

Vi rekommenderar att du använder följande riktlinjer för att säkerställa att StorSimple-lösenord är starkt och väl skyddade:

* Ändra ditt lösenord var tredje månad. Ändra lösenord tillämpas per år.
* Använd starka lösenord. Mer information finns på [skapa starkare lösenord och skydda dem](http://blogs.microsoft.com/cybertrust/2014/08/25/create-stronger-passwords-and-protect-them/).
* Alltid använda olika lösenord för olika mekanismer; var och en av de lösenord som du anger måste vara unika.
* Dela inte lösenord med personer som inte har behörighet att komma åt StorSimple-enhet.
* Tala om ett lösenord framför andra eller inte tipset på formatet på ett lösenord.
* Anmäl händelsen till säkerhetsavdelningen information om du misstänker att ett konto eller lösenord har komprometterats.
* Hantera alla lösenord som känslig och konfidentiell information. 

## <a name="storsimple-data-protection"></a>Dataskydd för StorSimple

Det här avsnittet beskriver StorSimple-säkerhetsfunktioner som skyddar data under överföring och lagrade data.

Enligt beskrivningen i andra avsnitt används lösenord för att auktorisera och autentiserar användare innan de kan få åtkomst till din StorSimple-lösning. Ett annat övervägande för säkerhet skyddar data från obehöriga användare när det överförs mellan lagringssystem och samtidigt som det lagras. I följande avsnitt beskrivs Dataskyddsfunktioner som medföljer StorSimple.

> [!NOTE]
> Deduplicering ger ytterligare skydd för data som lagras på StorSimple-enheten och i Microsoft Azure storage. När data är deduplicerad objekten data lagras separat från de metadata som används för att mappa och komma åt dem: det finns ingen tillgänglig lagringsnivå kontext till rekonstruera data baserat på volymstruktur, filsystemet eller filnamn.


## <a name="protect-data-flowing-through-the-service"></a>Skydda data som skickas via tjänsten

Huvudsyftet med StorSimple enheten Manager-tjänsten är att hantera och konfigurera StorSimple-enhet. StorSimple Device Manager-tjänsten körs i Microsoft Azure. Du använder Azure portal anger konfigurationsdata för enheten och använder sedan för Microsoft Azure StorSimple enheten Manager-tjänsten för att skicka data till enheten. StorSimple använder ett system asymmetriska nyckelpar för att säkerställa att en kompromettering av Azure-tjänsten inte resulterar i en kompromettering av lagrad information.

![Kryptering av data som rör sig](./media/storsimple-security/DataEncryption.png)

Asymmetrisk nyckel systemet skyddar de data som skickas via tjänsten enligt följande:

1. Ett certifikat för kryptering som använder en asymmetrisk offentliga och privata nyckel nyckelpar genereras på enheten och används för att skydda data. Genereras när den första enheten registreras.
2. Certifikat för datakrypteringsnycklar exporteras till en Personal Information Exchange (.pfx)-fil som skyddas av den krypteringsnyckel för tjänstdata, vilket är en stark 128-bitars nyckel som genereras slumpmässigt av den första enheten under registreringen.
3. Den offentliga nyckeln för certifikatet på ett säkert sätt få tillgång till StorSimple Device Manager-tjänsten och den privata nyckeln sparas med enheten.
4. Ange tjänsten data krypteras med offentlig nyckel och dekryptera med den privata nyckeln lagras på enheten, säkerställer att Azure-tjänsten inte kan dekryptera data som flödar till enheten.

Krypteringsnyckel för tjänstdata ska genereras på endast den första enheten som registrerats för tjänsten. Alla efterföljande enheter som registreras med tjänsten måste använda samma service datakrypteringsnyckeln.

> [!IMPORTANT]
> Det är mycket viktigt att göra en kopia av krypteringsnyckeln för tjänstdata och spara den på en säker plats. En kopia av krypteringsnyckeln för tjänstdata ska lagras på ett sådant sätt att den kan användas av en behörig person och lätt kan överföras till enhetsadministratören.
> 
> Om krypteringsnyckeln för tjänstdata tappas bort, kan en Microsoft-supporten hjälpa dig att hämta den förutsatt att du har minst en enhet i ett onlinetillstånd. Vi rekommenderar att du ändrar krypteringsnyckeln för tjänstdata när den har hämtats.

Om du vill ändra krypteringsnyckeln för tjänstdata och motsvarande certifikat för kryptering av data, följer du stegen i [ändra krypteringsnyckeln för tjänstdata för din StorSimple Enhetshanteraren tjänst](storsimple-8000-manage-service.md#change-the-service-data-encryption-key). Ändra krypteringsnycklarna kräver att alla enheter kan uppdateras med den nya nyckeln. Därför rekommenderar vi att du ändrar nyckeln när alla enheter som är online. Om enheter som är offline, kan deras nycklar ändras vid en annan tid. Enheter med inaktuella nycklar kommer fortfarande att kunna köra säkerhetskopieringar, men de kommer inte att kunna återställa data förrän nyckeln har uppdaterats.

Krypteringsnyckel för tjänstdata och krypteringscertifikat data ut inte. Vi rekommenderar dock att du ändrar krypteringsnyckeln för tjänstdata årligen för att förhindra att nyckeln har komprometterats.

## <a name="protect-data-at-rest"></a>Skydda data i vila

StorSimple-enheten hanterar data genom att lagra det i nivåerna lokalt och i molnet, beroende på användning. Alla värden datorer som är anslutna till enheten skickar data till enheten, vilket flyttar data till molnet, efter behov. Data överförs från enheten till molnet på ett säkert sätt via Internet. Varje enhet har en iSCSI-mål som hämtar alla volymer på den enheten. Alla data krypteras innan det skickas till molnlagring. 

![Krypteringsnyckel för molnlagring](./media/storsimple-security/CloudStorageEncryption.png)

För att säkerställa säkerheten och integriteten hos data flyttas till molnet, kan StorSimple du definiera molnet krypteringsnycklar för lagring på följande sätt:

* Du kan ange krypteringsnyckeln för molnlagring när du skapar en volymbehållare. Nyckeln kan inte ändras eller läggs till senare.
* Alla volymer i en volymbehållare delar samma krypteringsnyckel. Om du vill att en annan form av kryptering för en viss volym, rekommenderar vi att du skapar en ny volymbehållare som värd för den volymen.
* När du anger krypteringsnyckeln för molnlagring i tjänsten StorSimple Enhetshanteraren är nyckeln krypterad med den offentliga delen av krypteringsnyckeln för tjänstdata och skickas sedan till enheten.
* Krypteringsnyckeln för molnlagring lagras inte någonstans i tjänsten och känner till enheten.
* Ange en krypteringsnyckel för molnlagring är valfritt. Du kan skicka data som har krypterats med värden till enheten.

### <a name="additional-security-best-practices"></a>Metodtips för ökad säkerhet

* Dela trafik: isolera din iSCSI SAN-nätverk från användartrafik i ett Företagsnätverk genom att distribuera ett helt separata nätverk och använda VLAN, där fysisk isolering inte är ett alternativ. Ett dedikerat nätverk för iSCSI-lagring kommer att garantera säkerhet och prestanda för affärskritiska data. Blanda lagrings- och trafik via en LAN rekommenderas inte och ökar svarstiden och orsaka nätverksfel.
* Använda nätverksgränssnitt som stöder TCP/IP-avlastning Engine (TOE) för värden på klientsidan nätverkssäkerhet. TOE minskar CPU-belastningen genom att behandla TCP på nätverkskortet.

## <a name="protect-data-via-storage-accounts"></a>Skydda data via storage-konton

Varje Microsoft Azure-prenumeration kan skapa en eller flera lagringskonton. (Ett lagringskonto ger ett unikt namnområde för att arbeta med data som lagras i Azure-molnet.) Åtkomst till ett lagringskonto styrs av tangenterna prenumeration och åtkomst som är associerade med detta lagringskonto.

När du skapar ett lagringskonto genererar Microsoft Azure två 512-bitars lagring åtkomstnycklar, varav används för autentisering när StorSimple-enheten får åtkomst till lagringskontot. Observera att endast en av dessa nycklar används. Den andra nyckeln lagras i reserv, så att du kan rotera nycklarna med jämna mellanrum. För att rotera nycklar, aktivera den sekundära nyckeln och ta sedan bort den primära nyckeln. Du kan sedan skapa en ny nyckel som ska användas under nästa rotation. (Av säkerhetsskäl kräver många Datacenter viktiga rotation.)

Vi rekommenderar att du följer dessa bästa praxis för viktiga rotation:

* Du ska rotera lagringskontonycklar regelbundet för att säkerställa att ditt lagringskonto inte kan nås av obehöriga användare.
* Administratören Azure bör med jämna mellanrum, ändra eller återskapa den primära eller sekundära nyckeln med hjälp av avsnittet lagring i Azure-portalen för direkt åtkomst till lagringskontot.

## <a name="protect-data-via-encryption"></a>Skydda data via kryptering

StorSimple använder följande krypteringsalgoritmer att skydda data som lagras i eller reser mellan komponenter i din StorSimple-lösning.

| Algoritmen | Nyckellängd | Kommentarer-protokoll/program |
| --- | --- | --- |
| RSA |2048 |1 för RSA-PKCS-v1.5 används av Azure-portalen för att kryptera konfigurationsdata som skickas till enheten: till exempel lagring kontoautentiseringsuppgifter, StorSimple enhetskonfiguration och cloud storage krypteringsnycklar. |
| AES |256 |AES med CBC används för att kryptera den offentliga delen av krypteringsnyckeln för tjänstdata innan det skickas till Azure-portalen från StorSimple-enhet. Det används också av StorSimple-enhet för att kryptera data innan data skickas till molnet storage-konto. |

## <a name="storsimple-cloud-appliance-security"></a>Säkerhet för StorSimple-enhet för molnet

[!INCLUDE [storsimple Cloud Appliance security](../../includes/storsimple-virtual-device-security.md)]

## <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ)

Följande är några frågor och svar om säkerhet och Microsoft Azure StorSimple.

**F:** min tjänst har komprometterats. Vad bör vara nästa steg om jag?

**S:** omedelbart bör du ändra krypteringsnyckeln för tjänstdata och lagringskontonycklar för storage-konto som används för lagringsnivåer data. Anvisningar finns:

* [Ändra krypteringsnyckeln för tjänstdata](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Viktiga rotation av storage-konton](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**F:** jag har en ny StorSimple-enhet som frågar efter nyckel för tjänstregistrering. Hur hämtar den?

**S:** den här nyckeln skapas när du först skapade StorSimple enheten Manager-tjänsten. När du använder tjänsten StorSimple Device Manager för att ansluta till enheten, använder du sidan Snabbstart för att visa eller återskapa nyckeln för tjänstregistrering. Generera en ny nyckel för tjänstregistrering påverkar inte befintliga registrerade enheter. Anvisningar finns:

* [Visa eller återskapa nyckeln för tjänstregistrering](storsimple-8000-manage-service.md##regenerate-the-service-registration-key)

**F:** försvann min krypteringsnyckel för tjänstdata. Vad gör jag nu?

**S:** kontaktar Microsoft Support. De kan logga in på en supportsession på enheten och hjälpen du hämta nyckeln (förutsatt att minst en enhet är online). Omedelbart efter att du har fått krypteringsnyckeln för tjänstdata, bör du ändra den för att kontrollera att den nya nyckeln är känt för dig. Anvisningar finns:

* [Ändra krypteringsnyckeln för tjänstdata](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)

**F:** jag behörighet en enhet för tjänsten data encryption key ändras, men inte startade processen ändringen. Vad ska jag göra?

**S:** om tidsgränsen har gått ut, behöver du omauktorisera enheten för tjänsten data encryption key ändras och startar processen igen.

**F:** jag ändrade krypteringsnyckel för tjänstdata, men det gick inte att uppdatera andra enheter inom fyra timmar. Måste jag börja igen?

**S:** 4 timmar tidsperioden är endast för initiering av ändringen. När du startar uppdateringen på behöriga StorSimple-enheten får tillståndet tills alla enheter har uppdaterats.

**F:** vår StorSimple-administratören har lämnat företaget. Vad ska jag göra?

**S:** ändrings- och återställning av lösenord som tillåter åtkomst till StorSimple-enhet och ändra datakryptering service nyckeln så att den nya informationen inte känner till auktoriserad personal. Anvisningar finns:

* [Använda Enhetshanteraren för StorSimple-tjänsten för att ändra din storsimple-lösenord](storsimple-8000-change-passwords.md)
* [Ändra krypteringsnyckeln för tjänstdata](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Konfigurera CHAP för din StorSimple-enhet](storsimple-8000-configure-chap.md)

**F:** jag vill du ange lösenordet för StorSimple Snapshot Manager till en värd som ansluter till StorSimple-enhet, men lösenordet är inte tillgänglig. Vad kan jag göra?

**S:** om du har glömt lösenordet kan du skapa en ny. Sedan måste du informera alla befintliga användare att lösenordet har ändrats och att de ska uppdatera sina klienter för att använda det nya lösenordet. Anvisningar finns:

* [Ändra lösenordet för StorSimple Snapshot Manager](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)
* [Autentisera en enhet](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**F:** certifikatet för fjärråtkomst till Windows PowerShell för StorSimple har ändrats på enheten. Hur uppdaterar jag min fjärråtkomstklienter?

**S:** du kan hämta det nya certifikatet från StorSimple Device Manager-tjänsten och sedan tillhandahålla den installeras i certifikatarkivet för fjärranslutna klienter. Anvisningar finns:

* [Importera certifikat cmdlet](https://technet.microsoft.com/library/hh848630.aspx)

**F:** är Mina data skyddas om tjänsten StorSimple Enhetshanteraren äventyras?

**S:** Service configuration data alltid krypteras med den offentliga nyckeln när du visar den i en webbläsare. Eftersom tjänsten inte har åtkomst till den privata nyckeln kan tjänsten inte finns några data. Om tjänsten StorSimple Enhetshanteraren äventyras, påverkas inte, eftersom det inte finns några nycklar som lagras i Enhetshanteraren för StorSimple-tjänsten.

**F:** om någon får tillgång till data krypteringscertifikat kommer Mina data äventyras?

**S:** Microsoft Azure lagrar kundens datakrypteringsnyckeln (.pfx-fil) i krypterat format. Eftersom .pfx-filen är krypterad och StorSimple-tjänsten har inte krypteringsnyckeln för tjänstdata att dekryptera den .pfx-fil kan gränssnittshändelsen bara få åtkomst till PFX-filen inte alla hemligheter skapas.

**F:** vad händer om en statliga entitet begär Microsoft Mina data?

**S:** eftersom alla data krypteras på tjänsten och den privata nyckeln sparas med enheten, statliga entiteten fråga kunden för data.

## <a name="gdpr-compliance"></a>BNPR kompatibilitet

[Allmänna Data Protection förordning (BNPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm) är en Europeiska unionen (EU) data protection och sekretess lag. BNPR innehåller många krav om hur du samlar in, lagra och använda dina personuppgifter. BNPR regler gäller för företag, myndigheter och andra organisationer som fungerar i Europa och samla in och analysera data som är knutna till Europa boende. 

Enhetshanteraren StorSimple för både fysiska och virtuella serien är BNPR som är kompatibla. Det finns två viktiga instanser där personlig information samlas in och visas i tjänsten:

- Varning användarinställningar där e-postadressen för användare konfigureras. Den här informationen kan visas och avmarkerad av administratören. Detta gäller både StorSimple 8000-serien enheter och virtuella StorSimple-matriser.
 * Om du vill visa och ta bort inställningarna för StorSimple 8000-serien, följer du stegen i [visa och hantera aviseringar för StorSimple](storsimple-8000-manage-alerts.md#configure-alert-settings)
 * Om du vill visa och ta bort inställningarna för virtuell StorSimple-matris, följer du stegen i [visa och hantera aviseringar för StorSimple](storsimple-virtual-array-manage-alerts.md#configure-alert-settings)
- Användare som kan komma åt data på resurserna. En lista över användare som har åtkomst till resursdata visas och kan visas. Den här listan tas också bort när resurserna tas bort. Detta gäller endast för virtuell StorSimple-matriser.
 * Visa listan över användare som kan komma åt eller att ta bort en resurs, följer du stegen i [hantera resurser på den virtuella StorSimple-matris](storsimple-virtual-array-manage-shares.md)

Mer information finns i Microsoft Privacy-policy på [Säkerhetscenter](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Nästa steg

[Distribuera din StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).

