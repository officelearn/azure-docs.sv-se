---
title: Lokala lösenord tillbakaskrivning av integrering med Azure AD SSPR
description: Hämta lösenord i molnet skrivas tillbaka till den lokala AD infratstructure
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 66f33dd0edc44a490c3ccaf989d6d9308e615bb0
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246117"
---
# <a name="what-is-password-writeback"></a>Vad är tillbakaskrivning av lösenord?

Med en molnbaserad lösenordsåterställning verktyget är bra, men de flesta företag fortfarande har en lokal katalog där användarna finns. Så här hanterar Microsoft support att se till att traditionella lokala Active Directory (AD) synkroniserade med ändringar av lösenord i molnet? Tillbakaskrivning av lösenord är en funktion som aktiveras med [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) som tillåter ändringar av lösenord i molnet för att skrivas tillbaka till en befintlig lokal katalog i realtid.

Tillbakaskrivning av lösenord stöds i miljöer där:

* [Active Directory Federation Services](../hybrid/how-to-connect-fed-management.md)
* [Synkronisering av lösenordshash](../hybrid/how-to-connect-password-hash-synchronization.md)
* [Direktautentisering](../hybrid/how-to-connect-pta.md)

> [!WARNING]
> Tillbakaskrivning av lösenord att sluta fungera för kunder som använder Azure AD Connect-versioner 1.0.8641.0 och äldre när den [Azure Access Control service (ACS) är ur bruk 7 November 2018](../develop/active-directory-acs-migration.md). Azure AD Connect-versioner 1.0.8641.0 och äldre inte längre att tillåta tillbakaskrivning av lösenord vid den tidpunkten eftersom de använder ACS för den funktionen.
>
> Om du vill undvika avbrott i tjänsten, uppgradera från en tidigare version av Azure AD Connect till en nyare version finns i artikeln [Azure AD Connect: Uppgradera från en tidigare version till senast](../hybrid/how-to-upgrade-previous-version.md)
>

Tillbakaskrivning av lösenord innehåller:

* **Tillämpning av principer för en lokal Active Directory-lösenord**: När en användare återställer sitt lösenord, kontrolleras det att se till att den uppfyller dina lokala Active Directory-principer innan du genomför den till katalogen. Den här granskningen inkluderar kontroll historiken, komplexitet, ålder, lösenordsfilter och eventuella andra begränsningar för lösenord som du har definierat i lokala Active Directory.
* **Noll fördröjning feedback**: Tillbakaskrivning av lösenord är en synkron åtgärd. Användarna får ett meddelande direkt om sitt lösenord inte uppfyller principen eller inte kunde återställa eller ändras av någon anledning.
* **Har stöd för lösenord ändras från åtkomstpanelen och Office 365**: När federerade eller lösenords-hash synkroniserade användare kommer att ändra sina lösenord har upphört att gälla eller icke upphört att gälla, dessa lösenord skrivs tillbaka till din lokala Active Directory-miljö.
* **Har stöd för tillbakaskrivning av lösenord när en administratör återställer dem från Azure portal**: När en administratör återställer en användares lösenord i den [Azure-portalen](https://portal.azure.com)om användaren är federerat eller lösenords-hash har synkroniserats lösenordet skrivs tillbaka till den lokala. Den här funktionen stöds för närvarande inte i administrationsportalen för Office.
* **Kräver inte några ingående brandväggsreglerna**: Tillbakaskrivning av lösenord använder ett Azure Service Bus-relä som en underliggande kommunikationskanalen. All kommunikation är utgående via port 443.

> [!Note]
> Användarkonton som finns i skyddade grupper i den lokala Active Directory kan inte användas med tillbakaskrivning av lösenord. Mer information om skyddade grupper finns i [skyddade konton och grupper i Active Directory](https://technet.microsoft.com/library/dn535499.aspx).
>

## <a name="licensing-requirements-for-password-writeback"></a>Licensieringskrav för tillbakaskrivning av lösenord

**Självbetjäning lösenord återställning/ändring/upplåsning med lokal tillbakaskrivning är en premiumfunktion i Azure AD**. Mer information om licensiering finns i den [priser platsen för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Om du vill använda tillbakaskrivning av lösenord, måste du ha en av de följande licenser som tilldelats på din klient:

* Azure AD Premium P1
* Azure AD Premium P2
* Enterprise Mobility + Security E3 eller A3
* Enterprise Mobility + Security E5 eller A5
* Microsoft 365 E3 eller A3
* Microsoft 365 E5 eller A5
* Microsoft 365 F1
* Microsoft 365 Business

> [!WARNING]
> Fristående Office 365 licensiering planer *stöder inte ”Self Service lösenord återställning/ändring/upplåsning med lokal tillbakaskrivning”* och kräver att du har en av de föregående prenumerationerna för den här funktionen ska fungera.
>

## <a name="how-password-writeback-works"></a>Hur fungerar tillbakaskrivning av lösenord

När en federerad eller lösenord hash synkroniserade användare försöker återställa eller ändra sina lösenord i molnet, utförs följande åtgärder:

1. En kontroll utförs om du vill se vilken typ av lösenord som användaren har. Om lösenordet är hanteras lokalt:
   * En kontroll utförs för att se om tjänsten för tillbakaskrivning av är igång. Om det är kan användaren fortsätta.
   * Om tjänsten för tillbakaskrivning av är nere kan användaren informeras om att deras lösenord inte kan återställas just nu.
1. Därefter måste användaren skickar Grindarna lämplig autentisering och når den **Återställ lösenord** sidan.
1. Användaren väljer ett nytt lösenord och bekräftar den.
1. När användaren väljer **skicka**, lösenordet i klartext krypteras med en symmetrisk nyckel som skapades under installationen tillbakaskrivning.
1. Det krypterade lösenordet ingår i en nyttolast som skickas via en HTTPS-kanal till din klientspecifik service bus-reläer (som har ställts in för dig under installationsprocessen för tillbakaskrivning av). Den här relay skyddas av ett slumpmässigt genererat lösenord som känner till endast den lokala installationen.
1. När meddelandet når en service bus, aktiveras automatiskt slutpunkten för återställning av lösenord och ser att det finns en väntande begäran återställning.
1. Tjänsten söker sedan efter användaren med hjälp av molnet fästpunktsattributet. För den här sökningen ska lyckas:

   * Användarobjektet måste finnas i Active Directory-anslutarplatsen.
   * Användarobjektet måste kopplas till den motsvarande metaversumobjekt (MV).
   * Användarobjektet måste kopplas till motsvarande Azure Active Directory connector-objektet.
   * Länken från Active Directory connector-objektet till MV måste ha synkroniseringsregeln `Microsoft.InfromADUserAccountEnabled.xxx` på länken.
   
   När anropet strömmar in från molnet, Synkroniseringsmotorn använder den **Molnakarvärde** attribut för att leta upp anslutarplatsen som Azure Active Directory. Den sedan följer länken till MV-objekt och sedan följer länken till Active Directory-objektet. Eftersom det kan finnas flera Active Directory-objekt (flera skogar) för samma användare, Synkroniseringsmotorn förlitar sig på den `Microsoft.InfromADUserAccountEnabled.xxx` länk för att välja rätt.

   > [!Note]
   > Till följd av den här logiken för lösenord måste tillbakaskrivning ska fungera Azure AD Connect kunna kommunicera med primära domänkontrollantens (PDC) emulator. Om du vill aktivera det manuellt kan ansluta du Azure AD Connect till PDC-emulatorn. Högerklicka på den **egenskaper** för Active Directory-anslutningsappen för synkronisering Välj **konfigurera katalogpartitioner**. Därifrån kan du leta efter den **domänkontrollanten** avsnittet och markerar du kryssrutan som heter **endast använda prioriterade domänkontrollanter**. Även om den prioriterade domänkontrollanten inte är en PDC-emulator, försöker Azure AD Connect ansluta till PDC för tillbakaskrivning av lösenord.

1. När användaren hittas konto, görs ett försök att återställa lösenordet direkt i rätt Active Directory-skogen.
1. Om uppsättningsåtgärd som lösenord lyckas användaren är ett meddelande om att deras lösenord har ändrats.
   > [!NOTE]
   > Om användarens lösenords-hash synkroniseras till Azure AD med hjälp av synkronisering av lösenordshash, finns det en risk att lokala lösenordsprincip är lägre än lösenordsprincipen som molnet. I det här fallet tillämpas den lokala principen. Den här principen säkerställer att dina lokala principer tillämpas i molnet, oavsett om du använder hash Lösenordssynkronisering eller federation för att tillhandahålla enkel inloggning.
   >

1. Om lösenordet har angetts för åtgärden misslyckas uppmanar användaren att försöka igen i ett fel. Åtgärden kan misslyckas eftersom:
   * Tjänsten är inaktiv.
   * Det lösenord som de har valt uppfyller inte organisationens principer.
   * Det går inte att hitta användaren i lokala Active Directory.

    Felmeddelanden ge vägledning till användare så att de kan försöka att lösa utan inblandning av administratören.

## <a name="password-writeback-security"></a>Säkerhet för tillbakaskrivning av lösenord

Tillbakaskrivning av lösenord är en mycket säker tjänst. För att säkerställa att din information är skyddad, aktiveras en fyra nivåer säkerhetsmodell som beskrivs nedan:

* **Klientspecifik service bus-relä**
   * När du konfigurerar tjänsten har en klientspecifik service bus-relä ställts in som skyddas av ett slumpmässigt genererat starkt lösenord som Microsoft har aldrig åtkomst till.
* **Låst, kryptografiskt starkt lösenord för krypteringsnyckel**
   * När service bus-relä skapas, skapas en stark symmetriska nyckel som används för att kryptera lösenordet när det gäller i rörelse. Den här nyckeln kan bara gäller i ditt företags hemliga arkivet i molnet, vilket är mycket låsta och granskas, precis som alla andra lösenord i katalogen.
* **Branschens standard säkerhet TLS (Transport Layer)**
   1. När ett lösenord återställa eller ändra åtgärden sker i molnet, lösenordet i klartext är krypterad med din offentliga nyckel.
   1. Det krypterade lösenordet placeras i en HTTPS-meddelandet som skickas över en krypterad kanal med hjälp av Microsoft SSL-certifikat till service bus-relä.
   1. När meddelandet tas emot i en service bus, vaknar lokala agenten och autentiserar till en service bus med hjälp av starkt lösenord som skapades tidigare.
   1. Den lokala agenten tar upp det krypterade meddelandet och dekrypterar den med hjälp av den privata nyckeln.
   1. Den lokala agenten försöker ställa in lösenordet via AD DS SetPassword-API: et. Det här steget är det låter tillämpning av din Active Directory lokala lösenordsprincip (till exempel komplexitet, ålder, historik och filter) i molnet.
* **Principer för förfallodatum för meddelande**
   * Om meddelandet är placerad i service bus eftersom den lokala tjänsten är avstängd, tidsgränsen och tas bort efter ett par minuter. Tidsgräns och borttagning av meddelandet ökar du säkerheten ytterligare.

### <a name="password-writeback-encryption-details"></a>Krypteringsinformation med tillbakaskrivning av lösenord

När användaren har skickat en återställning av lösenord, går återställning av begäran igenom flera krypteringssteg innan den tas emot i din lokala miljö. De här stegen för kryptering säkerställa maximala tjänsternas tillförlitlighet och säkerhet. De beskrivs på följande sätt:

* **Steg 1: Lösenordskryptering 2048-bitars RSA-nyckel**: När användaren har skickat ett lösenord att skrivas tillbaka till den lokala krypteras det skickade lösenordet själva med en 2048-bitars RSA-nyckel.
* **Steg 2: Paketet på servernivå kryptering med AES-GCM**: Hela paketet, lösenordet och metadata som krävs är krypterad med hjälp av AES-GCM. Den här krypteringen förhindrar alla direkt åtkomst till den underliggande ServiceBus-kanalen från att visa eller ändra innehållet.
* **Steg 3: All kommunikation sker över TLS/SSL**: All kommunikation med ServiceBus sker i en kanal för SSL/TLS. Den här krypteringen skyddar innehållet från obehörig från tredje part.
* **Automatisk återställning av nyckel över var sjätte månad**: Alla nycklar rulla över var sjätte månad eller varje gång tillbakaskrivning av lösenord är inaktiverat och därefter aktiveras i Azure AD Connect att säkerställa maximal Tjänstsäkerhet och säkerhet.

### <a name="password-writeback-bandwidth-usage"></a>Användning av nätverksbandbredd i tillbakaskrivning av lösenord

Tillbakaskrivning av lösenord är en långsam-tjänst som bara skickar begäranden till den lokala agenten under följande omständigheter:

* Två meddelanden skickas när funktionen är aktiverad eller inaktiverad via Azure AD Connect.
* Ett meddelande skickas en gång var femte minut som ett pulsslag för så länge som tjänsten körs.
* Två meddelanden skickas varje gång ett nytt lösenord har skickats:
   * Det första meddelandet är en begäran att utföra åtgärden.
   * Det andra meddelandet innehåller resultatet av åtgärden och skickas under följande omständigheter:
      * Varje gång ett nytt lösenord har skickats under en återställning av lösenord för användaren.
      * Varje gång ett nytt lösenord har skickats under en åtgärd för lösenordsbyte användare.
      * Varje gång ett nytt lösenord har skickats under ett admin-initierad användaren lösenordsåterställning (endast från Azure administrationsportalerna).

#### <a name="message-size-and-bandwidth-considerations"></a>Överväganden för meddelande-storlek och bandbredd

Storleken på varje meddelande som beskrivs ovan är vanligtvis under 1 KB. Även under extrem belastning förbrukar några kbit / s bandbredd för själva tjänsten för tillbakaskrivning av lösenord. Eftersom varje meddelande som skickas i realtid, är bara om detta krävs av en åtgärd för uppdatering och eftersom meddelandestorleken är så liten bandbreddsanvändningen av funktionen för tillbakaskrivning av för liten att ha någon märkbar effekt.

## <a name="supported-writeback-operations"></a>Stöds tillbakaskrivningar

Lösenord skrivs tillbaka i följande situationer:

* **Stöds slutanvändaråtgärder**
   * Alla slutanvändare självbetjäning frivillig ändra lösenord
   * Alla slutanvändare självbetjäning force ändra lösenord, exempelvis utgångsdatum för lösenord
   * Alla slutanvändare själva återställa lösenord som kommer från den [portal för lösenordsåterställning](https://passwordreset.microsoftonline.com)
* **Stöds administratör åtgärder**
   * Alla administratör självbetjäning frivillig ändra lösenord
   * Alla administratör självbetjäning force ändra lösenord, exempelvis utgångsdatum för lösenord
   * Alla självbetjäning lösenordsåterställning för administratörer som kommer från den [portal för lösenordsåterställning](https://passwordreset.microsoftonline.com)
   * Alla administratörsinitierad slutanvändarens lösenord med självbetjäning från den [Azure-portalen](https://portal.azure.com)

## <a name="unsupported-writeback-operations"></a>Stöds inte tillbakaskrivningar

Lösenord är *inte* skrivas tillbaka i någon av följande situationer:

* **Stöds inte slutanvändaråtgärder**
   * Slutanvändare återställa sina egna lösenord med hjälp av PowerShell version 1, version 2 eller Azure AD Graph API
* **Åtgärder som inte stöds**
   * Alla administratörsinitierad slutanvändarens lösenord med självbetjäning från den [hanteringsportalen för Office](https://portal.office.com)
   * Alla administratörsinitierad slutanvändarens lösenord med självbetjäning från PowerShell version 1, version 2 eller Azure AD Graph API

## <a name="next-steps"></a>Nästa steg

Aktivera tillbakaskrivning av lösenord med hjälp av självstudierna: [Aktivera tillbakaskrivning av lösenord](tutorial-enable-writeback.md)
