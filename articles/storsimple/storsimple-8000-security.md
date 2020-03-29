---
title: StorSimple 8000-serien säkerhet | Microsoft-dokument
description: Beskriver de säkerhets- och sekretessfunktioner som skyddar din StorSimple-tjänst, enhet och data lokalt och i molnet.
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
ms.openlocfilehash: 4598f71f9b611e68f8eb00676138784833c39f32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75891504"
---
# <a name="storsimple-security-and-data-protection"></a>StorSimple säkerhet och dataskydd

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Översikt

Säkerhet är ett stort bekymmer för alla som antar en ny teknik, särskilt när tekniken används med konfidentiella eller proprietära data. När du utvärderar olika tekniker måste du överväga ökade risker och kostnader för dataskydd. Microsoft Azure StorSimple tillhandahåller både en säkerhets- och sekretesslösning för dataskydd, vilket bidrar till att säkerställa:

* **Konfidentialitet** – Endast auktoriserade enheter kan visa dina data.
* **Integritet** – Endast auktoriserade entiteter kan ändra eller ta bort dina data.

Microsoft Azure StorSimple-lösningen består av fyra huvudkomponenter som interagerar med varandra:

* **StorSimple Device Manager-tjänsten finns i Microsoft Azure** – Hanteringstjänsten som du använder för att konfigurera och etablera StorSimple-enheten.
* **StorSimple-enhet** – En fysisk enhet som är installerad i ditt datacenter. Alla värdar och klienter som genererar data ansluter till StorSimple-enheten och enheten hanterar data och flyttar dem till Azure-molnet efter behov.
* **Klienter/värdar som är anslutna till enheten** – Klienterna i infrastrukturen som ansluter till StorSimple-enheten och genererar data som måste skyddas.
* **Molnlagring** – Platsen i Azure-molnet där data lagras.

I följande avsnitt beskrivs säkerhetsfunktionerna i StorSimple som skyddar var och en av dessa komponenter och de data som lagras på dem. Den innehåller också en lista med frågor som du kan ha om Microsoft Azure StorSimple-säkerhet och motsvarande svar.

## <a name="storsimple-device-manager-service-protection"></a>Tjänstskydd för StorSimple-enhetshanteraren

StorSimple Device Manager-tjänsten är en hanteringstjänst som finns i Microsoft Azure och som används för att hantera alla StorSimple-enheter som din organisation har skaffat. Du kan komma åt Tjänsten StorSimple Device Manager genom att använda dina organisationsautentiseringsuppgifter för att logga in på Azure-portalen via en webbläsare.

Åtkomst till Tjänsten StorSimple Device Manager kräver att din organisation har en Azure-prenumeration som innehåller StorSimple. Prenumerationen styr vilka funktioner du kan komma åt på Azure Portal. Om din organisation inte har en Azure-prenumeration och du vill veta mer om dem läser [du Registrera dig för Azure som organisation](../active-directory/fundamentals/sign-up-organization.md).

Eftersom StorSimple Device Manager-tjänsten finns i Azure skyddas den av Azure-säkerhetsfunktionerna. Läs mer om säkerhetsfunktionerna i Microsoft Azure på [Microsoft Azure Säkerhetscenter](https://azure.microsoft.com/support/trust-center/security/).

## <a name="storsimple-device-protection"></a>Skydd mot StorSimple-enheter

StorSimple-enheten är en lokal hybridlagringsenhet som innehåller SSD-enheter (Solid State Drives) och hårddiskar (HDDs), tillsammans med redundanta styrenheter och automatiska redundansfunktioner. Styrenheterna hanterar lagringsnivåering och placerar för närvarande använda (eller heta) data på lokal lagring (i StorSimple-enheten eller lokala servrar), samtidigt som de flyttar mindre ofta använda data till molnet.

Endast auktoriserade StorSimple-enheter tillåts ansluta till StorSimple Device Manager-tjänsten som du skapade i din Azure-prenumeration. Om du vill auktorisera en enhet måste du registrera den med Tjänsten StorSimple Device Manager genom att tillhandahålla tjänstens registreringsnyckel. Tjänstregistreringsnyckeln är en 128-bitars slumpmässig nyckel som genereras i Azure-portalen.

![Nyckel för registrering av tjänsten](./media/storsimple-security/ServiceRegistrationKey.png)

Om du vill veta hur du hämtar en tjänstregistreringsnyckel går du till [steg 2: Hämta nyckeln](storsimple-8000-deployment-walkthrough-u2.md#step-2-get-the-service-registration-key)för tjänstregistrering .

Tjänstregistreringsnyckeln är en lång nyckel som innehåller över 100 tecken. Du kan kopiera nyckeln och spara den i en textfil på en säker plats så att du kan använda den för att auktorisera ytterligare enheter efter behov. Om tjänstregistreringsnyckeln går förlorad när du har registrerat din första enhet kan du generera en ny nyckel från Tjänsten StorSimple Device Manager. Detta påverkar inte driften av befintliga enheter.

När en enhet har registrerats används token för att kommunicera med Microsoft Azure. Tjänstregistreringsnyckeln används inte efter enhetsregistrering.

> [!NOTE]
> Vi rekommenderar att du återskapar tjänstens registreringsnyckel efter varje användning.


## <a name="protect-your-storsimple-solution-via-passwords"></a>Skydda din StorSimple-lösning via lösenord

Lösenord är en viktig aspekt av datorsäkerhet och används i stor utsträckning i StorSimple-lösningen för att säkerställa att dina data endast är tillgängliga för behöriga användare. Med StorSimple kan du konfigurera följande lösenord:

* Lösenord för StorSimple-enhetsadministratör
* Utmana CHAP-initierare (Handshake Authentication Protocol) och mållösenord
* Lösenordet för StorSimple Snapshot Manager

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Windows PowerShell för StorSimple och storsimple-enhetens administratörslösenord

Windows PowerShell för StorSimple är ett kommandoradsgränssnitt som du kan använda för att hantera StorSimple-enheten. Windows PowerShell för StorSimple har funktioner som gör att du kan registrera enheten, konfigurera nätverksgränssnittet på enheten, installera vissa typer av uppdateringar, felsöka enheten genom att komma åt supportsessionen och ändra enhetens tillstånd. Du kan komma åt Windows PowerShell för StorSimple genom att ansluta till seriekonsolen på enheten eller med hjälp av Windows PowerShell-ommotning.

PowerShell-ommotning kan göras via HTTPS eller HTTP. Om fjärrhantering via HTTPS är aktiverat måste du hämta fjärrhanteringscertifikatet från enheten och installera det på fjärrklienten. Mer information om PowerShell-ommotkoppling finns [på Fjärråtkomst till StorSimple-enheten](storsimple-8000-remote-connect.md).

När du har använt Windows PowerShell för StorSimple för att ansluta till enheten måste du ange lösenordet för enhetsadministratören för att logga in på enheten.

![Enhetens administratörslösenord](./media/storsimple-security/DeviceAdminPW.png)

Tänk på följande:

* Fjärrhantering är inaktiverat som standard. Du kan använda Tjänsten StorSimple Device Manager för att aktivera den. Som säkerhetspraxis bör fjärråtkomst endast aktiveras under den tidsperiod som den faktiskt behövs.
* Om du ändrar lösenordet måste du meddela alla fjärråtkomstanvändare så att de inte drabbas av en oväntad anslutningsförlust.
* Tjänsten StorSimple Device Manager kan inte hämta befintliga lösenord: den kan bara återställa dem. Vi rekommenderar att du lagrar alla lösenord på en säker plats så att du inte behöver återställa ett lösenord om det glöms bort. Om du behöver återställa ett lösenord måste du meddela alla användare innan du återställer det.

Du kan komma åt Windows PowerShell-gränssnittet med hjälp av en seriell anslutning till enheten. Du kan också fjärransluta till den med hjälp av HTTP eller HTTPS, vilket ger ytterligare säkerhet. HTTPS ger en högre säkerhetsnivå än antingen en seriell eller HTTP-anslutning. Om du vill använda HTTPS måste du dock först installera ett certifikat på klientdatorn som kommer åt enheten. Du kan hämta fjärråtkomstcertifikatet från enhetskonfigurationssidan i Tjänsten StorSimple Device Manager. Om certifikatet för fjärråtkomst går förlorat måste du hämta ett nytt certifikat och sprida det till alla klienter som har behörighet att använda fjärrhantering.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Utmana CHAP-initierare (Handshake Authentication Protocol) och mållösenord

CHAP är ett autentiseringsschema som används av StorSimple-enheten för att verifiera fjärrklienters identitet. Verifieringen baseras på ett delat lösenord. CHAP kan vara enkelriktad (enkelriktad) eller ömsesidig (dubbelriktad). Med enkelriktad CHAP autentiserar målet (StorSimple-enheten) en initierare (värd). Ömsesidig eller omvänd CHAP kräver att målet autentiserar initieraren och sedan initieraren autentiserar målet. Din StorSimple kan konfigureras för att använda någon av metoderna.

Tänk på följande när du konfigurerar CHAP:

* CHAP-användarnamnet måste innehålla färre än 233 tecken.
* CHAP-lösenordet måste vara mellan 12 och 16 tecken. Om du försöker använda ett längre användarnamn eller lösenord kommer det att resultera i ett autentiseringsfel på Windows-värden.
* Du kan inte använda samma lösenord för både CHAP-initieraren och CHAP-målet.
* När du har angett lösenordet kan det ändras, men det går inte att hämta det. Om lösenordet ändras måste du meddela alla fjärråtkomstanvändare så att de kan ansluta till StorSimple-enheten.

Mer information om CHAP och hur du konfigurerar den för StorSimple-lösningen finns i [Konfigurera CHAP för din StorSimple-enhet](storsimple-8000-configure-chap.md).

### <a name="storsimple-snapshot-manager-password"></a>Lösenordet för StorSimple Snapshot Manager

StorSimple Snapshot Manager är en MMC-snapin-modul (Microsoft Management Console) som använder volymgrupper och Windows Volume Shadow Copy Service för att generera programkonsekventa säkerhetskopior. Dessutom kan du använda StorSimple Snapshot Manager för att skapa säkerhetskopieringsscheman och klona eller återställa volymer.

När du konfigurerar en enhet så att den använder StorSimple Snapshot Manager måste du ange lösenordet för StorSimple Snapshot Manager. Det här lösenordet anges först i Windows PowerShell för StorSimple under registreringen. Lösenordet kan också ställas in och ändras från Tjänsten StorSimple Device Manager. Det här lösenordet autentiserar enheten med StorSimple Snapshot Manager.

![Lösenordet för StorSimple Snapshot Manager](./media/storsimple-security/SnapshotMgrPassword.png)

StorSimple Snapshot Manager-lösenordet måste vara 14 till 15 tecken och måste innehålla 3 eller fler av en kombination av versaler, gemener, numeriska och specialtecken. När du har angett lösenordet för StorSimple Snapshot Manager kan det ändras, men det går inte att hämta det. Om du ändrar lösenordet måste du meddela alla fjärranvändare.

Mer information om StorSimple Snapshot Manager finns i [Vad är StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Metodtips för lösenord

Vi rekommenderar att du använder följande riktlinjer för att se till att StorSimple-lösenord är starka och väl skyddade:

* Ändra dina lösenord var tredje månad. Att ändra lösenorden tillämpas årligen.
* Använd starka lösenord. Mer information finns i [Skapa starkare lösenord och skydda dem](https://cloudblogs.microsoft.com/microsoftsecure/2014/08/25/create-stronger-passwords-and-protect-them/).
* Använd alltid olika lösenord för olika åtkomstmekanismer. var och en av de lösenord du anger ska vara unika.
* Dela inte lösenord med någon som inte har behörighet att komma åt StorSimple-enheten.
* Tala inte om ett lösenord framför andra eller antyd formatet på ett lösenord.
* Om du misstänker att ett konto eller lösenord har komprometterats rapporterar du händelsen till din informationssäkerhetsavdelning.
* Behandla alla lösenord som känslig, konfidentiell information. 

## <a name="storsimple-data-protection"></a>StorSimple dataskydd

I det här avsnittet beskrivs säkerhetsfunktionerna i StorSimple som skyddar data under överföring och lagrade data.

Som beskrivs i andra avsnitt används lösenord för att auktorisera och autentisera användare innan de kan få åtkomst till din StorSimple-lösning. En annan säkerhetsövervägande är att skydda data från obehöriga användare medan den överförs mellan lagringssystem och medan den lagras. I följande avsnitt beskrivs de dataskyddsfunktioner som medföljer StorSimple.

> [!NOTE]
> Deduplicering ger ytterligare skydd för data som lagras på StorSimple-enheten och i Microsoft Azure-lagring. När data dedupliceras lagras dataobjekten separat från de metadata som används för att mappa och komma åt dem: det finns ingen tillgänglig kontext på lagringsnivå för att rekonstruera data baserat på volymstruktur, filsystem eller filnamn.


## <a name="protect-data-flowing-through-the-service"></a>Skydda data som flödar genom tjänsten

Det primära syftet med Tjänsten StorSimple Device Manager är att hantera och konfigurera StorSimple-enheten. Tjänsten StorSimple Device Manager körs i Microsoft Azure. Du använder Azure-portalen för att ange enhetskonfigurationsdata och sedan använder Microsoft Azure Tjänsten StorSimple Device Manager för att skicka data till enheten. StorSimple använder ett system med asymmetriska nyckelpar för att säkerställa att en kompromettering av Azure-tjänsten inte leder till en kompromettering av lagrad information.

![Datakryptering under flygning](./media/storsimple-security/DataEncryption.png)

Det asymmetriska nyckelsystemet hjälper till att skydda data som flödar genom tjänsten enligt följande:

1. Ett datakrypteringscertifikat som använder ett asymmetriskt offentligt och privat nyckelpar genereras på enheten och används för att skydda data. Nycklarna genereras när den första enheten registreras.
2. Datakrypteringscertifikatnycklarna exporteras till en fil för utbyte av personlig information (.pfx) som skyddas av krypteringsnyckeln för tjänstdata, vilket är en stark 128-bitarsnyckel som genereras slumpmässigt av den första enheten under registreringen.
3. Certifikatets offentliga nyckel är säkert tillgänglig för Tjänsten StorSimple Device Manager och den privata nyckeln finns kvar på enheten.
4. Data som anger tjänsten krypteras med den offentliga nyckeln och dekrypteras med hjälp av den privata nyckeln som lagras på enheten, vilket säkerställer att Azure-tjänsten inte kan dekryptera data som flödar till enheten.

Krypteringsnyckeln för tjänstdata genereras endast på den första enheten som registrerats med tjänsten. Alla efterföljande enheter som är registrerade hos tjänsten måste använda samma krypteringsnyckel för tjänstdata.

> [!IMPORTANT]
> Det är mycket viktigt att göra en kopia av krypteringsnyckeln för tjänstdata och spara den på en säker plats. En kopia av krypteringsnyckeln för tjänstdata bör lagras på ett sådant sätt att den kan nås av en behörig person och enkelt kan meddelas enhetsadministratören.
> 
> Om krypteringsnyckeln för tjänstdata går förlorad kan en Microsoft-supportperson hjälpa dig att hämta den förutsatt att du har minst en enhet i ett onlinetillstånd. Vi rekommenderar att du ändrar krypteringsnyckeln för tjänstdata när den har hämtats.

Om du vill ändra krypteringsnyckeln för tjänstdata och motsvarande datakrypteringscertifikat följer du stegen i [Ändra krypteringsnyckeln för tjänstdata för tjänsten StorSimple Device Manager](storsimple-8000-manage-service.md#change-the-service-data-encryption-key). För att ändra krypteringsnycklarna måste alla enheter uppdateras med den nya nyckeln. Därför rekommenderar vi att du ändrar nyckeln när alla enheter är online. Om enheterna är offline kan deras nycklar ändras vid en annan tidpunkt. De enheter som har inaktuella nycklar kan fortfarande köra säkerhetskopior, men de kan inte återställa data förrän nyckeln har uppdaterats.

Krypteringsnyckeln för tjänstdata och datakrypteringscertifikatet upphör inte att gälla. Vi rekommenderar dock att du ändrar krypteringsnyckeln för tjänstdata årligen för att förhindra att viktiga komprometteringar.

## <a name="protect-data-at-rest"></a>Skydda data i vila

StorSimple-enheten hanterar data genom att lagra den på nivåer lokalt och i molnet, beroende på användningsfrekvens. Alla värddatorer som är anslutna till enheten skickar data till enheten, som sedan flyttar data till molnet, beroende på vad som är lämpligt. Data överförs från enheten till molnet på ett säkert sätt via Internet. Varje enhet har ett iSCSI-mål som visar alla delade volymer på den enheten. Alla data krypteras innan de skickas till molnlagring. 

![Krypteringsnyckel för molnlagring](./media/storsimple-security/CloudStorageEncryption.png)

För att säkerställa säkerheten och integriteten för data som flyttas till molnet kan du med StorSimple definiera krypteringsnycklar för molnlagring enligt följande:

* Du anger molnlagringskrypteringsnyckeln när du skapar en volymbehållare. Nyckeln kan inte ändras eller läggas till senare.
* Alla volymer i en volymbehållare delar samma krypteringsnyckel. Om du vill ha en annan form av kryptering för en viss volym rekommenderar vi att du skapar en ny volymbehållare som är värd för den volymen.
* När du anger molnlagringskrypteringsnyckeln i Tjänsten StorSimple Device Manager krypteras nyckeln med den offentliga delen av krypteringsnyckeln för tjänstdata och skickas sedan till enheten.
* Krypteringsnyckeln för molnlagring lagras inte någonstans i tjänsten och är endast känd för enheten.
* Det är valfritt att ange en molnlagringskrypteringsnyckel. Du kan skicka data som har krypterats hos värden till enheten.

### <a name="additional-security-best-practices"></a>Ytterligare metodtips för säkerhet

* Delad trafik: isolera din iSCSI SAN från användartrafik på ett företags-LAN genom att distribuera ett helt separerat nätverk och använda VLAN där fysisk isolering inte är ett alternativ. Ett dedikerat nätverk för iSCSI-lagring garanterar säkerheten och prestandan för dina affärskritiska data. Det rekommenderas inte att lagring och användartrafik blandas via ett företagsnätverk och kan öka svarstiden och orsaka nätverksfel.
* För nätverkssäkerhet på värdsidan använder du nätverksgränssnitt som stöder TCP/IP Offload Engine (TOE). TOE minskar CPU-belastningen genom att bearbeta TCP på nätverkskortet.

## <a name="protect-data-via-storage-accounts"></a>Skydda data via lagringskonton

Varje Microsoft Azure-prenumeration kan skapa ett eller flera lagringskonton. (Ett lagringskonto ger ett unikt namnområde för att arbeta med data som lagras i Azure-molnet.) Åtkomsten till ett lagringskonto styrs av prenumerations- och åtkomstnycklarna som är associerade med lagringskontot.

När du skapar ett lagringskonto genererar Microsoft Azure två 512-bitars lagringsåtkomstnycklar, varav en används för autentisering när StorSimple-enheten kommer åt lagringskontot. Observera att endast en av dessa nycklar används. Den andra nyckeln hålls i reserv, så att du kan rotera tangenterna med jämna mellanrum. Om du vill rotera tangenterna gör du den sekundära nyckeln aktiv och tar sedan bort primärnyckeln. Du kan sedan skapa en ny nyckel som kan användas under nästa rotation. (Av säkerhetsskäl kräver många datacenter nyckelrotation.)

Vi rekommenderar att du följer dessa metodtips för nyckelrotation:

* Du bör rotera lagringskontonycklar regelbundet för att se till att ditt lagringskonto inte används av obehöriga användare.
* Med jämna mellanrum bör din Azure-administratör ändra eller återskapa den primära eller sekundära nyckeln med hjälp av lagringsavsnittet i Azure-portalen för att direkt komma åt lagringskontot.

## <a name="protect-data-via-encryption"></a>Skydda data via kryptering

StorSimple använder följande krypteringsalgoritmer för att skydda data som lagras i eller färdas mellan komponenterna i Din StorSimple-lösning.

| Algoritm | Nyckellängd | Protokoll/program/kommentarer |
| --- | --- | --- |
| RSA |2048 |RSA PKCS 1 v1.5 används av Azure-portalen för att kryptera konfigurationsdata som skickas till enheten: till exempel lagringskontoautentiseringsuppgifter, StorSimple-enhetskonfiguration och molnlagringskrypteringsnycklar. |
| AES |256 |AES med CBC används för att kryptera den offentliga delen av krypteringsnyckeln för tjänstdata innan den skickas till Azure-portalen från StorSimple-enheten. Den används också av StorSimple-enheten för att kryptera data innan data skickas till molnlagringskontot. |

## <a name="storsimple-cloud-appliance-security"></a>StorSimple Cloud Appliance säkerhet

[!INCLUDE [storsimple Cloud Appliance security](../../includes/storsimple-virtual-device-security.md)]

## <a name="managing-personal-information"></a>Hantera personlig information

StorSimple Device Manager för både fysiska och virtuella serier samlar in personlig information i följande nyckelinstanser:

- Avisera användarinställningar där användarnas e-postadress är konfigurerad. Den här informationen kan visas och rensas av administratören. Detta gäller både StorSimple 8000-seriens enheter och StorSimple Virtual Arrays.
  * Om du vill visa och avmarkera inställningarna för StorSimple 8000-serien följer du stegen i [Visa och hanterar StorSimple-aviseringar](storsimple-8000-manage-alerts.md#configure-alert-settings)
  * Om du vill visa och avmarkera inställningarna för StorSimple Virtual Array följer du stegen i [Visa och hanterar StorSimple-aviseringar](storsimple-virtual-array-manage-alerts.md#configure-alert-settings)
- Användare som kan komma åt data som finns på resurserna. En lista över användare som kan komma åt delningsdata visas och kan visas. Den här listan tas också bort när resurserna tas bort. Detta gäller endast StorSimple Virtual Arrays.
  * Om du vill visa listan över användare som kan komma åt eller ta bort en resurs följer du stegen i [Hantera resurser på Den virtuella storsimple-matrisen](storsimple-virtual-array-manage-shares.md)

Mer information finns i Microsofts sekretesspolicy på [Säkerhetscenter](https://www.microsoft.com/trustcenter).

## <a name="frequently-asked-questions-faq"></a>Vanliga frågor och svar (FAQ)

Här följer några frågor och svar om säkerhet och Microsoft Azure StorSimple.

**F:** Min tjänst är komprometterat. Vad bör vara mina nästa steg?

**A.** Du bör omedelbart ändra krypteringsnyckeln för tjänstdata och lagringskontonycklarna för lagringskontot som används för nivåindelningsdata. Instruktioner finns i:

* [Ändra krypteringsnyckeln för tjänstdata](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Nyckelrotation av lagringskonton](storsimple-8000-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**F:** Jag har en ny StorSimple-enhet som ber om tjänstens registreringsnyckel. Hur hämtar jag den?

**A.** Den här nyckeln skapades när du först skapade Tjänsten StorSimple Device Manager. När du använder Tjänsten StorSimple Device Manager för att ansluta till enheten kan du använda snabbstartssidan för tjänsten för att visa eller återskapa tjänstens registreringsnyckel. Om du skapar en ny tjänstregistreringsnyckel påverkas inte de befintliga registrerade enheterna. Instruktioner finns i:

* [Visa eller återskapa tjänstregistreringsnyckeln](storsimple-8000-manage-service.md#regenerate-the-service-registration-key)

**F:** Jag förlorade min krypteringsnyckel för tjänstdata. Vad gör jag nu?

**A.** Kontakta Microsoft Support. De kan logga in på en supportsession på enheten och hjälpa dig att hämta nyckeln (förutsatt att minst en enhet är online). Omedelbart efter att du har hämtat krypteringsnyckeln för tjänstdata bör du ändra den för att säkerställa att den nya nyckeln bara är känd för dig. Instruktioner finns i:

* [Ändra krypteringsnyckeln för tjänstdata](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)

**F:**  Jag har auktoriserat en enhet för en ändring av tjänstdatakrypteringsnyckeln, men startade inte nyckeländringsprocessen. Vad ska jag göra?

**A.** Om time-out-perioden har gått ut måste du auktorisera om enheten för att ändra krypteringsnyckeln för tjänstdata och starta processen igen.

**F:**  Jag ändrade krypteringsnyckeln för tjänstdata, men jag kunde inte uppdatera de andra enheterna inom 4 timmar. Måste jag börja om?

**A.** 4-timmars tidsperioden är endast för att initiera ändringen. När du har påbörjat uppdateringsprocessen på den auktoriserade StorSimple-enheten är auktoriseringen giltig tills alla enheter uppdateras.

**F:** Vår StorSimple-administratör har lämnat företaget. Vad ska jag göra?

**A.** Ändra och återställ lösenorden som ger åtkomst till StorSimple-enheten och ändra krypteringsnyckeln för tjänstdata för att säkerställa att den nya informationen inte är känd för obehörig personal. Instruktioner finns i:

* [Använda Tjänsten StorSimple Device Manager för att ändra dina storsnatorlösenord](storsimple-8000-change-passwords.md)
* [Ändra krypteringsnyckeln för tjänstdata](storsimple-8000-manage-service.md#change-the-service-data-encryption-key)
* [Konfigurera CHAP för StorSimple-enheten](storsimple-8000-configure-chap.md)

**F:** Jag vill ge StorSimple Snapshot Manager-lösenordet till en värd som ansluter till StorSimple-enheten, men lösenordet är inte tillgängligt. Vad kan jag göra?

**A.** Om du har glömt lösenordet bör du skapa ett nytt. Se sedan till att informera alla befintliga användare om att lösenordet har ändrats och att de bör uppdatera sina klienter för att använda det nya lösenordet. Instruktioner finns i:

* [Ändra lösenordet för StorSimple Snapshot Manager](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password)
* [Autentisera en enhet](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**F:** Certifikatet för fjärråtkomst till Windows PowerShell för StorSimple har ändrats på enheten. Hur uppdaterar jag mina fjärråtkomstklienter?

**A.** Du kan hämta det nya certifikatet från Tjänsten StorSimple Device Manager och sedan ange att det ska installeras i fjärråtkomstklienternas certifikatarkiv. Instruktioner finns i:

* [Cmdlet för importcertifikat](https://docs.microsoft.com/powershell/module/pkiclient/import-certificate)

**F:** Är mina data skyddade om StorSimple Device Manager-tjänsten äventyras?

**A.** Tjänstkonfigurationsdata krypteras alltid med din offentliga nyckel när du visar den i en webbläsare. Eftersom tjänsten inte har åtkomst till den privata nyckeln kan tjänsten inte se några data. Om Tjänsten StorSimple Device Manager har komprometterats påverkas ingen sådan eftersom det inte finns några nycklar lagrade i Tjänsten StorSimple Device Manager.

**F:** Om någon får tillgång till datakrypteringscertifikatet, äventyras mina data?

**A.** Microsoft Azure lagrar kundens datakrypteringsnyckel (.pfx-fil) i ett krypterat format. Eftersom PFX-filen är krypterad och StorSimple-tjänsten inte har krypteringsnyckeln för tjänstdata för att dekryptera PFX-filen, kommer det inte att avslöja några hemligheter genom att helt enkelt få åtkomst till PFX-filen.

**F:** Vad händer om en statlig enhet ber Microsoft om mina data?

**A.** Eftersom alla data krypteras på tjänsten och den privata nyckeln hålls med enheten, måste den statliga enheten be kunden om data.



## <a name="next-steps"></a>Nästa steg

[Distribuera din StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).

