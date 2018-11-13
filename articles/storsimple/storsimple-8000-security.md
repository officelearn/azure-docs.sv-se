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
ms.date: 05/18/2018
ms.author: alkohli
ms.openlocfilehash: ded3c89774c39b5edee02b9e3c6807ce75ff16a4
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2018
ms.locfileid: "51566201"
---
# <a name="storsimple-security-and-data-protection"></a>StorSimple säkerhet och dataskydd

## <a name="overview"></a>Översikt

Säkerhet är en stor utmaning för alla som använder en ny teknik, särskilt när tekniken används med konfidentiell eller upphovsrättsskyddad information. Medan du utvärderar olika tekniker kan betrakta du ökade risker och kostnader för dataskydd. Microsoft Azure StorSimple ger både säkerhet och sekretess lösning för dataskydd, hjälper till att se till att:

* **Konfidentialitet** – endast auktoriserade entiteter kan visa dina data.
* **Integritet** – endast auktoriserade entiteter kan ändra eller ta bort dina data.

Microsoft Azure StorSimple-lösningen består av fyra huvudsakliga komponenter som interagerar med varandra:

* **StorSimple Device Manager-tjänsten finns i Microsoft Azure** – management-tjänsten som används för att konfigurera och etablera StorSimple-enheten.
* **StorSimple-enheten** – en fysisk enhet som är installerad i datacentret. Alla värdar och -klienter som genererar data ansluta till StorSimple-enheten och enheten hanterar data och flyttar det till Azure-molnet efter behov.
* **Klienter/värdar som är anslutna till enheten** – klienter i din infrastruktur som ansluter till StorSimple-enheten och genererar data som behöver skyddas.
* **Molnlagring** – Platsen i Azure-molnet där data lagras.

I följande avsnitt beskrivs StorSimple-säkerhetsfunktioner som skyddar var och en av dessa komponenter och de data som lagras på dem. Den innehåller också en lista med frågor som kan uppstå om Microsoft Azure StorSimple-säkerhet och motsvarande svaren.

## <a name="storsimple-device-manager-service-protection"></a>StorSimple Device Manager skydd

StorSimple Device Manager-tjänsten är en tjänst för hantering i Microsoft Azure och används för att hantera alla StorSimple-enheter som din organisation har skaffat. Du kan komma åt StorSimple Device Manager-tjänsten genom att använda din organisations autentiseringsuppgifter för att logga in på Azure-portalen via en webbläsare.

Åtkomst till StorSimple Device Manager-tjänsten kräver att din organisation har en Azure-prenumeration som innehåller StorSimple. Prenumerationen styr vilka funktioner du kan komma åt på Azure Portal. Om din organisation inte har en Azure-prenumeration och du vill veta mer om dem, se [registrera dig för Azure som en organisation](../active-directory/fundamentals/sign-up-organization.md).

Eftersom StorSimple Device Manager-tjänsten finns i Azure, är den skyddad med säkerhet i Azure-funktioner. Läs mer om säkerhetsfunktionerna i Microsoft Azure på [Microsoft Azure Säkerhetscenter](https://azure.microsoft.com/support/trust-center/security/).

## <a name="storsimple-device-protection"></a>Skydd för StorSimple-enhet

StorSimple-enheten är en lokal hybrid-lagringsenhet som innehåller solid state-hårddiskar (SSD) och hårddiskar (HDD), tillsammans med funktioner för automatisk redundans och redundanta styrenheter. Styrenheterna hantera lagring lagringsnivåer, placera för närvarande används (eller frekvent) data på en lokal lagringsenhet (i StorSimple-enhet eller lokala servrar), vid förflyttning av mindre ofta använda data till molnet.

Endast behörighet StorSimple enheter tillåts att ansluta till StorSimple Device Manager-tjänsten som du skapade i Azure-prenumerationen. För att auktorisera en enhet, måste du registrera den med StorSimple Device Manager-tjänsten genom att ange Registreringsnyckeln för tjänsten. Tjänstregistreringsnyckeln är en 128-bitars slumpmässig nyckel genereras i Azure-portalen.

![nyckel för tjänstregistrering](./media/storsimple-security/ServiceRegistrationKey.png)

Läs hur hämta en tjänstregistreringsnyckel, gå till [steg 2: hämta tjänstregistreringsnyckeln](storsimple-8000-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key).

Tjänstregistreringsnyckeln är en lång nyckel som innehåller 100 tecken. Du kan kopiera nyckeln och spara den i en textfil på en säker plats så att du kan använda den för att auktorisera ytterligare enheter efter behov. Om nyckeln för tjänstregistrering förloras när du har registrerat din första enhet måste skapa du en ny nyckel från StorSimple Device Manager-tjänsten. Detta påverkar inte användningen av befintliga enheter.

När en enhet har registrerats använder token för att kommunicera med Microsoft Azure. Nyckel för tjänstregistrering används inte efter registrering av enheten.

> [!NOTE]
> Vi rekommenderar att du återskapar nyckeln för tjänstregistrering efter varje användning.


## <a name="protect-your-storsimple-solution-via-passwords"></a>Skydda din StorSimple-lösning via lösenord

Lösenord är en viktig aspekt av datorsäkerhet och stor utsträckning används i StorSimple-lösningen för att säkerställa att dina data är tillgängliga för auktoriserade användare. StorSimple gör att du kan konfigurera följande lösenord:

* StorSimple-enhetens administratörslösenord
* Utmana lösenord för CHAP Handshake Authentication Protocol ()-initierare och mål
* Lösenordet för StorSimple Snapshot Manager

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Windows PowerShell för StorSimple och StorSimple-enhetens administratörslösenord

Windows PowerShell för StorSimple är ett kommandoradsgränssnitt som du kan använda för att hantera StorSimple-enheten. Windows PowerShell för StorSimple har funktioner som gör att du kan registrera din enhet, konfigurera nätverksgränssnittet på din enhet, installera vissa typer av uppdateringar, felsöka enheten genom att gå till supportsession och ändra enhetens tillstånd. Du kan komma åt Windows PowerShell för StorSimple genom att ansluta till seriekonsolen på enheten eller genom att använda Windows PowerShell-fjärrkommunikation.

PowerShell-fjärrkommunikation kan göras över HTTPS eller HTTP. Om fjärrhantering över HTTPS är aktiverat, behöver du hämta certifikat för fjärrhantering från enheten och installera det på fjärrklienten. Mer information om PowerShell-fjärrkommunikation går du till [Anslut via en fjärranslutning till din StorSimple-enhet](storsimple-8000-remote-connect.md).

När du använder Windows PowerShell för StorSimple för att ansluta till enheten, måste förse enhetens administratörslösenord att logga in på enheten.

![Enhetens administratörslösenord](./media/storsimple-security/DeviceAdminPW.png)

Tänk på följande metodtips:

* Fjärrhantering är inaktiverat som standard. Du kan använda StorSimple Device Manager-tjänsten för att aktivera den. Som en säkerhetsåtgärd fjärråtkomst ska aktiveras endast under den tidsperiod som faktiskt krävs.
* Om du ändrar lösenordet, måste du meddela alla fjärranslutna användare så att de inte drabbas av ett oväntat anslutningsförlust.
* StorSimple Device Manager-tjänsten kan inte hämta befintliga lösenord: den kan bara återställa dem. Vi rekommenderar att du lagrar alla lösenord på en säker plats så att du inte har att återställa ett lösenord om det glöms bort. Om du behöver att återställa ett lösenord, måste du meddela alla användare innan du återställa den.

Du kan komma åt Windows PowerShell-gränssnittet med hjälp av en seriell anslutning till enheten. Du kan också hämta dem via en fjärranslutning med hjälp av antingen HTTP eller HTTPS, som ger ökad säkerhet. HTTPS ger en högre säkerhetsnivå än en serie- eller HTTP-anslutning. Om du vill använda HTTPS måste du dock först installera ett certifikat på den klientdator som ska få åtkomst till enheten. Du kan hämta fjärråtkomstcertifikatet från konfigurationssidan för enhet i StorSimple Device Manager-tjänsten. Om certifikatet för fjärråtkomst tappas bort, måste du hämta ett nytt certifikat och spridas till alla klienter som har behörighet att använda remote management.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Utmana lösenord för CHAP Handshake Authentication Protocol ()-initierare och mål

CHAP är ett schema för autentiseringsmetoder som används av StorSimple-enheten för att verifiera identiteten för fjärrklienter. Verifieringen är baserad på ett delat lösenord. CHAP kan vara enkelriktade (enkelriktade) eller ömsesidig (dubbelriktad). Med enkelriktad CHAP autentiserar målet (StorSimple-enhet) en initierare (värd). Ömsesidig eller omvänd CHAP kräver att målet autentisera initieraren och sedan initieraren autentisera målet. StorSimple kan konfigureras för att använda någon av metoderna.

Tänk på följande när du konfigurerar CHAP:

* CHAP-användarnamnet måste innehålla färre än 233 tecken.
* CHAP-lösenordet måste vara mellan 12 och 16 tecken. Försök att använda ett längre användarnamn eller lösenord resulterar i ett autentiseringsfel på Windows-värd.
* Du kan inte använda samma lösenord för CHAP-initieraren såväl CHAP-målet.
* När du har angett lösenordet kan ändras, men det går inte att hämta. Om lösenordet har ändrats, måste du meddela alla fjärranslutna användare så att de kan ansluta till StorSimple-enheten.

Mer information om CHAP och hur du konfigurerar den för din StorSimple-lösningen går du till [konfigurera CHAP för din StorSimple-enhet](storsimple-8000-configure-chap.md).

### <a name="storsimple-snapshot-manager-password"></a>Lösenordet för StorSimple Snapshot Manager

StorSimple Snapshot Manager är en snapin-modul i Microsoft Management Console (MMC) som använder volymgrupper och tjänsten Windows Volume Shadow Copy för att skapa programkonsekventa säkerhetskopior. Du kan dessutom använda StorSimple Snapshot Manager för att skapa scheman för säkerhetskopiering och kloning eller återställa volymer.

När du konfigurerar en enhet för att använda StorSimple Snapshot Manager uppmanas du att ange lösenordet för StorSimple Snapshot Manager. Det här lösenordet anges först i Windows PowerShell för StorSimple under registreringen. Lösenordet kan också ställa in och ändras från StorSimple Device Manager-tjänsten. Det här lösenordet autentiserar enheten med StorSimple Snapshot Manager.

![Lösenordet för StorSimple Snapshot Manager](./media/storsimple-security/SnapshotMgrPassword.png)

Lösenordet för StorSimple Snapshot Manager måste vara 14 och 15 tecken och måste innehålla minst 3 av en kombination av versaler, gemener, siffror och specialtecken. När du har angett lösenordet för StorSimple Snapshot Manager kan ändras, men det går inte att hämta. Om du ändrar lösenordet, måste du meddela alla fjärranvändare.

Mer information om StorSimple Snapshot Manager går du till [vad är StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Metodtips för lösenord

Vi rekommenderar att du använder följande riktlinjer för att säkerställa att StorSimple-lösenord är stark och väl skyddade:

* Ändra ditt lösenord var tredje månad. Ändra lösenorden tillämpas per år.
* Använd starka lösenord. Mer information går du till [skapa starkare lösenord och skydda dem](https://blogs.microsoft.com/cybertrust/2014/08/25/create-stronger-passwords-and-protect-them/).
* Alltid använda olika lösenord för olika mekanismer; var och en av de lösenord som du anger måste vara unika.
* Dela inte lösenord med vem som helst som inte har behörighet att få åtkomst till StorSimple-enheten.
* Om ett lösenord framför andra eller inte tipset på formatet för ett lösenord.
* Om du misstänker att ett konto eller lösenord har komprometterats kan du rapportera incidenten till säkerhetsavdelningen information.
* Hantera alla lösenord som känslig och konfidentiell information. 

## <a name="storsimple-data-protection"></a>StorSimple-dataskydd

Det här avsnittet beskriver StorSimple-säkerhetsfunktioner som skyddar data under överföring och lagrade data.

Enligt beskrivningen i andra avsnitt, används lösenord för att auktorisera och autentisera användare innan de kan få åtkomst till din StorSimple-lösning. En annan säkerhetsåtgärd skyddar data från obehöriga användare när den överförs mellan lagringssystem och medan de lagras. I följande avsnitt beskrivs de funktioner för dataskydd med StorSimple.

> [!NOTE]
> Deduplicering ger ytterligare skydd för data som lagras på StorSimple-enheten och i Microsoft Azure storage. När data är deduplicerad dataobjekten lagras separat från de metadata som används för att mappa och komma åt dem: det finns ingen tillgänglig lagringsnivå kontext att rekonstruera data baserat på volymen struktur, filsystemet eller filnamn.


## <a name="protect-data-flowing-through-the-service"></a>Skydda data som flödar in via tjänsten

Det huvudsakliga syftet med StorSimple Device Manager-tjänsten är att hantera och konfigurera StorSimple-enheten. StorSimple Device Manager-tjänsten körs i Microsoft Azure. Du använder Azure-portalen för att ange konfigurationsdata för enheten och sedan använder Microsoft Azure StorSimple Device Manager-tjänsten för att skicka data till enheten. StorSimple använder ett system asymmetriska nyckelpar för att säkerställa att en kompromettering av Azure-tjänsten inte ska resultera i en kompromettering av lagrad information.

![Kryptering av data som rör sig](./media/storsimple-security/DataEncryption.png)

Asymmetrisk nyckel systemet hjälper dig att skydda de data som skickas via tjänsten på följande sätt:

1. Ett certifikat för kryptering som använder en asymmetrisk offentliga och privata nyckel nyckelpar genereras på enheten och används för att skydda data. Nycklarna skapas när den första enheten har registrerats.
2. Certifikat för datakrypteringsnycklar exporteras till en Personal Information Exchange (.pfx)-fil som skyddas av den krypteringsnyckeln för tjänstdata, vilket är en stark 128-bitars nyckel som genereras slumpmässigt av den första enheten under registreringen.
3. Den offentliga nyckeln för certifikatet på ett säkert sätt görs tillgänglig för StorSimple Device Manager-tjänsten och den privata nyckeln sparas med enheten.
4. Att ange tjänsten krypteras data med hjälp av det offentliga nyckel och dekrypterade med hjälp av den privata nyckeln lagras på enheten, att säkerställa att Azure-tjänsten inte kan dekryptera data som flödar till enheten.

Krypteringsnyckeln för tjänstdata genereras på bara den första enheten som registreras med tjänsten. Alla efterföljande enheter som registreras med tjänsten måste använda samma tjänstdatakrypteringsnyckel.

> [!IMPORTANT]
> Det är mycket viktigt att göra en kopia av krypteringsnyckeln för tjänstdata och spara den på en säker plats. En kopia av krypteringsnyckeln för tjänstdata bör lagras på ett sätt att den kan användas av en behörig person och enkelt kan förmedlas till enhetsadministratören.
> 
> Om krypteringsnyckeln för tjänstdata tappas bort, kan en Microsoft-supporten hjälpa dig att hämta det förutsatt att du har minst en enhet i ett onlinetillstånd. Vi rekommenderar att du ändrar krypteringsnyckeln för tjänstdata när den hämtas.

Om du vill ändra krypteringsnyckeln för tjänstdata och motsvarande certifikat för kryptering av data, följer du stegen i [ändra krypteringsnyckeln för tjänstdata för StorSimple Device Manager-tjänsten](storsimple-8000-manage-service.md#change-the-service-data-encryption-key). Ändra krypteringsnycklarna kräver att alla enheter uppdateras med den nya nyckeln. Därför rekommenderar vi att du ändrar nyckeln när alla enheter är online. Om enheter är offline, går det att ändra sina nycklar vid en annan tidpunkt. Enheter med inaktuella nycklar kommer fortfarande att kunna köra säkerhetskopieringar, men de kommer inte att kunna återställa data förrän nyckeln har uppdaterats.

Krypteringsnyckeln för tjänstdata och krypteringscertifikat data inte upphör att gälla. Vi rekommenderar dock att du ändrar tjänstdatakrypteringsnyckel årligen för att förhindra att nyckeln har komprometterats.

## <a name="protect-data-at-rest"></a>Skydda data i vila

StorSimple-enheten hanterar data genom att lagra det nivåerna lokalt och i molnet, beroende på Användningsfrekvens. Alla värddatorer som är anslutna till enheten skicka data till enheten, vilket flyttar data till molnet, efter behov. Data överförs från enheten till molnet på ett säkert sätt via Internet. Varje enhet har en iSCSI-mål som hämtar alla klusterdelade volymer på den enheten. Alla data krypteras innan det skickas till molnlagring. 

![krypteringsnyckel för molnlagring](./media/storsimple-security/CloudStorageEncryption.png)

För att säkerställa säkerheten och integriteten hos data som flyttas till molnet, kan StorSimple du definiera cloud storage krypteringsnycklar enligt följande:

* Du kan ange krypteringsnyckeln för molnlagring när du skapar en volymbehållare. Nyckeln kan inte ändras eller läggs till senare.
* Alla volymer i en volymbehållare delar samma krypteringsnyckel. Om du vill att en annan form av kryptering för en specifik volym, rekommenderar vi att du skapar en ny volymbehållare som värd för den volymen.
* När du anger krypteringsnyckeln för molnlagring i StorSimple Device Manager-tjänsten är nyckeln krypterad med hjälp av den offentliga delen av krypteringsnyckeln för tjänstdata och sedan skickas till enheten.
* Krypteringsnyckeln för molnlagring lagras inte någonstans i tjänsten och bara du känner till enheten.
* Det är valfritt att ange en krypteringsnyckel för molnlagring. Du kan skicka data som har krypterats vid värden till enheten.

### <a name="additional-security-best-practices"></a>Metodtips för ökad säkerhet

* Dela upp trafiken: isolera dina iSCSI SAN-nätverk utifrån användarnas synpunkter på ett Företagsnätverk genom att distribuera ett helt avgränsas nätverk och med hjälp av VLAN där fysisk isolering är inte ett alternativ. Ett dedikerat nätverk för iSCSI-lagring att garantera säkerhet och prestanda för dina verksamhetskritiska data. Blanda lagrings- och trafik via en LAN kan rekommenderas inte och öka svarstiden och orsaka nätverksfel.
* Använd nätverksgränssnitt som har stöd för TCP/IP-avlastning Engine (TOE) för värden sida nätverkssäkerhet. TOE minskar CPU-belastningen genom att bearbeta TCP på nätverkskortet.

## <a name="protect-data-via-storage-accounts"></a>Skydda data via storage-konton

Varje Microsoft Azure-prenumeration kan skapa en eller flera lagringskonton. (Ett lagringskonto innehåller ett unikt namnområde för att arbeta med data som lagras i Azure-molnet.) Åtkomst till ett lagringskonto styrs av de prenumeration och åtkomstnycklar som är associerade med det lagringskontot.

När du skapar ett lagringskonto genererar Microsoft Azure två 512-bitars lagringsåtkomstnycklar, varav används för autentisering när StorSimple-enheten har åtkomst till lagringskontot. Observera att det är bara en av de här nycklarna används. Den andra nyckeln lagras i reserv, så att du kan rotera nycklarna regelbundet. För att rotera nycklar, aktivera den sekundära nyckeln och sedan ta bort den primära nyckeln. Du kan sedan skapa en ny nyckel för användning under nästa rotering. (Av säkerhetsskäl kräver många Datacenter nyckelrotation.)

Vi rekommenderar att du följer dessa Metodtips för rotation av:

* Du bör rotera lagringskontonycklar regelbundet för att säkerställa att ditt lagringskonto inte nås av obehöriga användare.
* Din Azure-administratör bör med jämna mellanrum, ändra eller återskapa den primära eller sekundära nyckeln med hjälp av avsnittet lagring i Azure portal för direkt åtkomst till lagringskontot.

## <a name="protect-data-via-encryption"></a>Skydda data via kryptering

StorSimple använder följande krypteringsalgoritmer för att skydda data som lagras i eller reser mellan komponenter i din StorSimple-lösning.

| Algoritmen | Nyckellängd | Protokoll/program/kommentarer |
| --- | --- | --- |
| RSA |2048 |1 för RSA-PKCS-v1.5 används av Azure-portalen för att kryptera konfigurationsdata som skickas till enheten: till exempel lagring kontoautentiseringsuppgifter, konfiguration för StorSimple-enhet, och krypteringsnycklar för lagring i molnet. |
| AES |256 |AES med CBC används för att kryptera den offentliga delen av krypteringsnyckeln för tjänstdata innan den skickas till Azure-portalen från StorSimple-enhet. Den används också av StorSimple-enheten för att kryptera data innan data skickas till ditt molnlagringskonto. |

## <a name="storsimple-cloud-appliance-security"></a>StorSimple Cloud Appliance-säkerhet

[!INCLUDE [storsimple Cloud Appliance security](../../includes/storsimple-virtual-device-security.md)]

## <a name="managing-personal-information"></a>Hantera personlig information

StorSimple Device Manager för serien för både fysiska och virtuella samlar in personlig information i följande viktiga instanser:

- Avisera användarinställningar där e-postadressen för användare är konfigurerade. Den här informationen kan visas och bort av administratören. Detta gäller både för enheter i StorSimple 8000-serien och StorSimple Virtual Array.
 * Om du vill visa och avmarkerar du inställningarna för StorSimple 8000-serien, följer du stegen i [visa och hantera aviseringar för StorSimple](storsimple-8000-manage-alerts.md#configure-alert-settings)
 * Om du vill visa och avmarkerar du inställningarna för StorSimple Virtual Array, följer du stegen i [visa och hantera aviseringar för StorSimple](storsimple-virtual-array-manage-alerts.md#configure-alert-settings)
- Användare som har åtkomst till data som finns på filresurser. En lista över användare som har åtkomst till resursdata visas och kan visas. Den här listan är också tas bort när filresurser tas bort. Detta gäller endast för StorSimple Virtual Array.
 * Visa listan över användare som kan komma åt eller att ta bort en resurs, följer du stegen i [hantera resurser på StorSimple Virtual Array](storsimple-virtual-array-manage-shares.md)

Mer information finns i Microsofts sekretesspolicy på [Säkerhetscenter](https://www.microsoft.com/trustcenter).

## <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ)

Följande är några frågor och svar om säkerhet och Microsoft Azure StorSimple.

**F:** min tjänst har komprometterats. Vad bör vara nästa steg om jag?

**S:** omedelbart bör du ändra krypteringsnyckeln för tjänstdata och storage-kontonycklar för storage-kontot som används för lagringsnivåer data. Mer information går du till:

* [Ändra krypteringsnyckeln för tjänstdata](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Rotation av storage-konton](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**F:** jag har en ny StorSimple-enhet som frågar efter nyckeln för tjänstregistrering. Hur hämtar jag det?

**S:** den här nyckeln skapades när du först skapade StorSimple Device Manager-tjänsten. När du använder StorSimple Device Manager-tjänsten för att ansluta till enheten kan använda du sidan Snabbstart för att visa eller återskapa tjänstregistreringsnyckeln. Generera en ny nyckel för tjänstregistrering påverkar inte befintliga registrerade enheter. Mer information går du till:

* [Visa eller återskapa tjänstregistreringsnyckeln](storsimple-8000-manage-service.md##regenerate-the-service-registration-key)

**F:** försvann min krypteringsnyckeln för tjänstdata. Vad gör jag nu?

**S:** kontakta Microsoft Support. De kan logga in på en supportsession på enheten och hjälp med att du hämtar nyckeln (förutsatt att minst en enhet är online). Omedelbart efter att du har fått krypteringsnyckeln för tjänstdata, bör du ändra det för att säkerställa att den nya nyckeln är bara du känner till. Mer information går du till:

* [Ändra krypteringsnyckeln för tjänstdata](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)

**F:** jag behörighet en enhet för en service encryption key dataändring, men startade inte viktiga ändringprocessen. Vad ska jag göra?

**S:** om tidsgränsen har gått ut, måste du auktorisera enheten för tjänsten data encryption key ändringen och startar processen igen.

**F:** jag ändrade krypteringsnyckeln för tjänstdata, men jag gick inte att uppdatera andra enheter inom fyra timmar. Måste jag börja om igen?

**S:** 4 timmars tidsperioden är endast för initiering av ändringen. När du startar uppdateringen på behöriga StorSimple-enheten går är auktoriseringen giltig tills alla enheter är uppdaterade.

**F:** våra StorSimple-administratören har lämnat företaget. Vad ska jag göra?

**S:** ändra och återställa lösenord som tillåter åtkomst till StorSimple-enheten och ändra datakryptering service nyckeln så att den nya informationen inte känner till auktoriserad personal. Mer information går du till:

* [Använda StorSimple Device Manager-tjänsten för att ändra din storsimple-lösenord](storsimple-8000-change-passwords.md)
* [Ändra krypteringsnyckeln för tjänstdata](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Konfigurera CHAP för din StorSimple-enhet](storsimple-8000-configure-chap.md)

**F:** jag vill ange lösenordet för StorSimple Snapshot Manager till en värd som ansluter till StorSimple-enheten, men lösenordet är inte tillgänglig. Vad kan jag göra?

**S:** om du har glömt lösenordet, bör du skapa en ny. Sedan måste du informera alla befintliga användare att lösenordet har ändrats och att de ska uppdatera sina klienter att använda det nya lösenordet. Mer information går du till:

* [Ändra lösenordet för StorSimple Snapshot Manager](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)
* [Autentisera en enhet](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**F:** certifikatet för fjärråtkomst till Windows PowerShell för StorSimple har ändrats på enheten. Hur uppdaterar jag mina fjärråtkomstklienter?

**S:** du kan hämta det nya certifikatet från StorSimple Device Manager-tjänsten och sedan tillhandahålla den installeras i certifikatarkivet för fjärråtkomstklienter. Mer information går du till:

* [Import-Certificate-cmdlet](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate)

**F:** är Mina data skyddas om StorSimple Device Manager-tjänsten komprometteras?

**S:** Service configuration data krypteras alltid med din offentliga nyckel när du visar den i en webbläsare. Eftersom tjänsten inte har åtkomst till den privata nyckeln kan tjänsten inte ser några data. Om StorSimple Device Manager-tjänsten komprometteras så påverkas inte, eftersom det inte finns några nycklar som lagras i StorSimple Device Manager-tjänsten.

**F:** om någon får åtkomst till data-krypteringscertifikat kommer Mina data komprometteras?

**S:** Microsoft Azure lagrar kundens datakrypteringsnyckeln (.pfx-fil) i krypterat format. Eftersom .pfx-filen är krypterad och StorSimple-tjänsten har inte tjänstdatakrypteringsnyckel att dekryptera .pfx-filen, utsätter helt enkelt få åtkomst till PFX-filen inte hemligheter.

**F:** vad händer om en myndighetsenhet frågar Microsoft om Mina data?

**S:** eftersom alla data krypteras på tjänsten och den privata nyckeln förvaras med enheten, myndighetsenhet be kunden för data.



## <a name="next-steps"></a>Nästa steg

[Distribuera StorSimple-enheten](storsimple-8000-deployment-walkthrough-u2.md).

