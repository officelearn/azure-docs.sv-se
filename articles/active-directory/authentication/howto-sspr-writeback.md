---
title: Azure AD SSPR med tillbakaskrivning av lösenord | Microsoft Docs
description: Använd Azure AD och Azure AD Connect för tillbakaskrivning av lösenord till en lokal katalog
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: fce92845591f20f7f2e9cff1a856e0904629255b
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054802"
---
# <a name="password-writeback-overview"></a>Översikt över tillbakaskrivning av lösenord

Du kan konfigurera Azure Active Directory (Azure AD) för att tillbakaskrivning av lösenord till din lokala Active Directory med tillbakaskrivning av lösenord. Tillbakaskrivning av lösenord eliminerar behovet av att konfigurera och hantera en komplicerad lokala lösenord för självbetjäning (SSPR) återställningslösning den ger ett enkelt sätt med molnbaserade för dina användare återställa sina lösenord var de än är. Tillbakaskrivning av lösenord är en komponent i [Azure Active Directory Connect](./../connect/active-directory-aadconnect.md) som kan vara aktiverad och används av befintliga prenumeranter av Premium [Azure Active Directory-versioner](../fundamentals/active-directory-whatis.md).

Tillbakaskrivning av lösenord tillhandahåller följande funktioner:

* **Ger återkoppling noll fördröjning**: tillbakaskrivning av lösenord är en synkron åtgärd. Användarna får ett meddelande direkt om sitt lösenord inte uppfyller principen eller inte kunde återställa eller ändras av någon anledning.
* **Har stöd för lösenordsåterställning för användare som använder Active Directory Federation Services (AD FS) eller andra tekniker för federation**: med tillbakaskrivning av lösenord, så länge de federerade användarkontona har synkroniserats till Azure AD-klienten, så är möjligt hantera sina lokala Active Directory-lösenord från molnet.
* **Har stöd för lösenordsåterställning för användare som använder** [lösenordshashsynkronisering](./../connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md): när tjänsten för återställning av lösenord upptäcker att ett synkroniserat användarkonto är aktiverad för synkronisering av lösenordshash kan vi återställa både den här kontots både lokalt och molnet lösenord samtidigt. Tillbakaskrivning av lösenord är inte beroende av synkronisering av lösenordshash.
* **Har stöd för lösenordsåterställning för användare som använder direktautentisering**: med tillbakaskrivning av lösenord, så länge direktautentisering-konton synkroniseras till din Azure AD-klient som ska kunna hantera sina lokala Active Directory-lösenord från molnet.
* **Har stöd för lösenord ändras från åtkomstpanelen och Office 365**: om sammanslaget eller lösenord synkroniseras användare kommer att ändra sina lösenord har upphört att gälla eller icke upphört att gälla, vi skriva dessa lösenord tillbaka till din lokala Active Directory-miljö.
* **Har stöd för tillbakaskrivning av lösenord när en administratör återställer dem från Azure portal**: när en administratör återställer en användares lösenord i den [Azure-portalen](https://portal.azure.com)om användaren är federerat eller lösenord synkroniseras, vi kan ange lösenordet administratören väljer i lokala Active Directory samt. Den här funktionen stöds för närvarande inte i administrationsportalen för Office.
* **Tillämpar din lokala Active Directory-lösenordsprinciper**: när en användare återställs sitt lösenord ska vi se till att det uppfyller din lokala Active Directory-principer innan vi skickar den till katalogen. Den här granskningen inkluderar kontroll historiken, komplexitet, ålder, lösenordsfilter och eventuella andra begränsningar för lösenord som du har definierat i lokala Active Directory.
* **Kräver inte några ingående brandväggsreglerna**: tillbakaskrivning av lösenord använder ett Azure Service Bus-relä som en underliggande kommunikationskanalen. Du behöver öppna några ingående portar i brandväggen för den här funktionen ska fungera.
* **Stöds inte för användarkonton som finns i skyddade grupper i den lokala Active Directory**: Mer information om skyddade grupper finns i [skyddade konton och grupper i Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

## <a name="how-password-writeback-works"></a>Hur fungerar tillbakaskrivning av lösenord

När en federerad eller lösenord hash synkroniserade användaren kommer att återställa eller ändra sina lösenord i molnet, inträffar följande:

1. Vi kontrollerar om du vill se vilken typ av lösenord som användaren har. Om vi ser att lösenordet är hanteras lokalt:
   * Vi kontrollerar om tjänsten för tillbakaskrivning av är igång. Om den är igång kan ge vi användaren gå vidare.
   * Om tjänsten för tillbakaskrivning av inte är igång, vi att meddela användaren att deras lösenord inte kan återställas just nu.
2. Därefter måste användaren skickar Grindarna lämplig autentisering och når den **Återställ lösenord** sidan.
3. Användaren väljer ett nytt lösenord och bekräftar den.
4. När användaren väljer **skicka**, vi kryptera lösenordet i klartext med en symmetrisk nyckel som skapades under installationen tillbakaskrivning.
5. När du krypterar lösenordet inkluderar vi den i en nyttolast som skickas via en HTTPS-kanal till din klientspecifik service bus-reläer (som vi också ställa in för dig under installationsprocessen för tillbakaskrivning av). Den här relay skyddas av ett slumpmässigt genererat lösenord som känner till endast den lokala installationen.
6. När meddelandet når en service bus, aktiveras automatiskt slutpunkten för återställning av lösenord och ser att det finns en väntande begäran återställning.
7. Tjänsten söker sedan efter användaren med hjälp av molnet fästpunktsattributet. För den här sökningen ska lyckas:

    * Användarobjektet måste finnas i Active Directory-anslutarplatsen.
    * Användarobjektet måste kopplas till den motsvarande metaversumobjekt (MV).
    * Användarobjektet måste kopplas till motsvarande Azure Active Directory connector-objektet.
    * Länken från Active Directory connector-objektet till MV måste ha synkroniseringsregeln `Microsoft.InfromADUserAccountEnabled.xxx` på länken. <br> <br>
    När anropet strömmar in från molnet, Synkroniseringsmotorn använder den **Molnakarvärde** attribut för att leta upp anslutarplatsen som Azure Active Directory. Den sedan följer länken till MV-objekt och sedan följer länken till Active Directory-objektet. Eftersom det kan finnas flera Active Directory-objekt (flera skogar) för samma användare, Synkroniseringsmotorn förlitar sig på den `Microsoft.InfromADUserAccountEnabled.xxx` länk för att välja rätt.

    > [!Note]
    > Till följd av den här logiken för lösenord måste tillbakaskrivning ska fungera Azure AD Connect kunna kommunicera med primära domänkontrollantens (PDC) emulator. Om du vill aktivera det manuellt kan ansluta du Azure AD Connect till PDC-emulatorn. Högerklicka på den **egenskaper** för Active Directory-anslutningsappen för synkronisering Välj **konfigurera katalogpartitioner**. Därifrån kan du leta efter den **domänkontrollanten** avsnittet och markerar du kryssrutan som heter **endast använda prioriterade domänkontrollanter**. Även om den prioriterade domänkontrollanten inte är en PDC-emulator, försöker Azure AD Connect ansluta till PDC för tillbakaskrivning av lösenord.

8. När användaren hittas konto, vi försöker återställa lösenordet direkt i rätt Active Directory-skogen.
9. Om uppsättningsåtgärd som lösenord lyckas vi att meddela användaren sitt lösenord har ändrats.
    > [!NOTE]
    > Om användarens lösenord synkroniseras till Azure AD med hjälp av Lösenordssynkronisering, finns det en risk att lokala lösenordsprincip är lägre än lösenordsprincipen som molnet. I det här fallet fortfarande tvingar vi vad som den lokala principen och i stället använda synkronisering av lösenordshash för att synkronisera hash för lösenordet. Den här principen säkerställer att dina lokala principer tillämpas i molnet, oavsett om du använder Lösenordssynkronisering eller federation för att tillhandahålla enkel inloggning.

10. Om lösenordet har angetts för åtgärden misslyckas vi returneras ett fel till användaren och låt dem igen. Åtgärden kan misslyckas eftersom:
    * Tjänsten är inaktiv.
    * Det lösenord som de har valt uppfyller inte organisationens principer.
    * Vi kan inte hitta användaren i lokala Active Directory.

    Vi har ett visst meddelande för många av dessa fall och begära att användaren vad de kan göra för att lösa problemet.

## <a name="configure-password-writeback"></a>Konfigurera tillbakaskrivning av lösenord

Vi rekommenderar att du använder funktionen för automatisk uppdatering av [Azure AD Connect](./../connect/active-directory-aadconnect-get-started-express.md) om du vill använda tillbakaskrivning av lösenord.

DirSync och Azure AD Sync stöds inte längre som ett sätt att aktivera tillbakaskrivning av lösenord. Läs mer om att hjälpa med övergången [uppgradera från DirSync och Azure AD Sync](../connect/active-directory-aadconnect-dirsync-deprecated.md).

Följande steg förutsätter att du redan har konfigurerat Azure AD Connect i din miljö med hjälp av den [Express](./../connect/active-directory-aadconnect-get-started-express.md) eller [anpassade](./../connect/active-directory-aadconnect-get-started-custom.md) inställningar.

1. För att konfigurera och aktivera tillbakaskrivning av lösenord, logga in på din Azure AD Connect-servern och starta den **Azure AD Connect** konfigurationsguiden.
2. På den **Välkommen** väljer **konfigurera**.
3. På den **ytterligare uppgifter** väljer **anpassa synkroniseringsalternativ**, och välj sedan **nästa**.
4. På den **Anslut till Azure AD** , ange autentiseringsuppgift för global administratör och välj sedan **nästa**.
5. På den **Anslut kataloger** och **domän-/ OU** filtrering sidor, Välj **nästa**.
6. På den **valfria funktioner** markerar du rutan bredvid **tillbakaskrivning av lösenord** och välj **nästa**.
   ![Aktivera tillbakaskrivning av lösenord i Azure AD Connect][Writeback]
7. På den **redo att konfigurera** väljer **konfigurera** och vänta tills processen slutförts.
8. När du ser konfigurationen är klar väljer du **avsluta**.

Vanliga uppgifter för felsökning för tillbakaskrivning av lösenord finns i avsnittet [felsöka tillbakaskrivning av lösenord](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) i vår felsökningsartikel.

## <a name="active-directory-permissions"></a>Active Directory-behörigheter

Konton som angetts i Azure AD Connect-verktyget måste ha följande ange om du vill ska vara i omfånget för SSPR:

* **Återställ lösenord** 
* **Ändra lösenord** 
* **Skrivbehörighet** på `lockoutTime`  
* **Skrivbehörighet** på `pwdLastSet`
* **Utökade rättigheter för** på antingen:
   * Rotobjektet av *varje domän* i den skogen
   * Användaren organisationsenheter (OU) som du vill ska vara i omfånget för SSPR

Om du inte vet vilket konto beskrivs kontot refererar öppnar Azure Active Directory Connect Konfigurationsgränssnittet och väljer den **visa aktuell konfiguration** alternativet. Det konto som du behöver lägga till behörigheter till anges under **synkroniserade kataloger**.

Om du anger dessa behörigheter kan MA-tjänstkontot för varje skog hantera lösenord för användarkonton i skogen. 

> [!IMPORTANT]
> Om du inte tilldelar dessa behörigheter, sedan, visas även om tillbakaskrivning av verkar vara korrekt konfigurerade, användare felmeddelanden när de försöker hantera sina lokala lösenord från molnet.
>

> [!NOTE]
> Det kan ta upp till ungefär en timme för behörigheterna att replikeras till alla objekt i din katalog.
>

Om du vill konfigurera lämpliga behörigheter för tillbakaskrivning av lösenord ska ske, gör du följande:

1. Öppna Active Directory-användare och datorer med ett konto som har administratörsbehörighet för lämplig domän.
2. Från den **visa** menyn, se till att **avancerade funktioner** är påslagen.
3. I den vänstra rutan högerklickar du på det objekt som representerar roten för domänen och välj **egenskaper** > **Security** > **Avancerat**.
4. Från den **behörigheter** fliken **Lägg till**.
5. Välj det konto som behörigheter som används för (från Azure AD Connect-konfiguration).
6. I den **gäller** listrutan, väljer **underordnade** objekt.
7. Under **behörigheter**, markerar kryssrutorna för följande:
    * **Återställ lösenord**
    * **Ändra lösenord**
    * **Skriva lockoutTime**
    * **Skriva pwdLastSet**
8. Välj **Använd/OK** att tillämpa ändringarna och stänga alla öppna dialogrutor.

## <a name="licensing-requirements-for-password-writeback"></a>Licensieringskrav för tillbakaskrivning av lösenord

Information om licensiering finns i [licenser som krävs för tillbakaskrivning av lösenord](concept-sspr-licensing.md) eller på följande webbplatser:

* [Priser platsen för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

### <a name="on-premises-authentication-modes-that-are-supported-for-password-writeback"></a>Lokala autentiseringslägen som stöds för tillbakaskrivning av lösenord

Lösenord för tillbakaskrivning av stöd för följande typer av användare lösenord:

* **Molnexklusiva användare**: tillbakaskrivning av lösenord gäller inte i den här situationen eftersom det finns inget lokala lösenord.
* **Lösenordet synkroniseras användare**: tillbakaskrivning av lösenord stöds.
* **Federerade användare**: tillbakaskrivning av lösenord stöds.
* **Direkt-autentiserade användare**: tillbakaskrivning av lösenord stöds.

### <a name="user-and-admin-operations-that-are-supported-for-password-writeback"></a>Användar- och administrativa åtgärder som stöds för tillbakaskrivning av lösenord

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

### <a name="user-and-admin-operations-that-are-not-supported-for-password-writeback"></a>Åtgärder för användare och administratör som inte stöds för tillbakaskrivning av lösenord

Lösenord är *inte* skrivas tillbaka i någon av följande situationer:

* **Stöds inte slutanvändaråtgärder**
  * Slutanvändare återställa sina egna lösenord med hjälp av PowerShell version 1, version 2 eller Azure AD Graph API
* **Åtgärder som inte stöds**
  * Alla administratörsinitierad slutanvändarens lösenord med självbetjäning från den [hanteringsportalen för Office](https://portal.office.com)
  * Alla administratörsinitierad slutanvändarens lösenord med självbetjäning från PowerShell version 1, version 2 eller Azure AD Graph API

Vi arbetar för att ta bort dessa begränsningar, men vi har inte en specifik tidslinje kan vi dela ännu.

## <a name="password-writeback-security-model"></a>Säkerhetsmodell för tillbakaskrivning av lösenord

Tillbakaskrivning av lösenord är en mycket säker tjänst. För att säkerställa att din information är skyddad, aktivera vi en fyra nivåer säkerhetsmodell som beskrivs nedan:

* **Klientspecifik service bus-relä**
  * När du konfigurerar tjänsten ställa vi in en klientspecifik service bus-relä som skyddas av ett slumpmässigt genererat starkt lösenord som Microsoft har aldrig åtkomst till.
* **Låst, kryptografiskt starkt lösenord för krypteringsnyckel**
  * När service bus-relä har skapats kan skapa vi en stark symmetrisk nyckel som vi använder för att kryptera lösenordet när det gäller i rörelse. Den här nyckeln kan bara gäller i ditt företags hemliga arkivet i molnet, vilket är mycket låsta och granskas, precis som alla andra lösenord i katalogen.
* **Branschens standard säkerhet TLS (Transport Layer)**
  1. När ett lösenord återställa eller ändra åtgärden sker i molnet kan vi ta lösenordet i klartext och krypterar dem med din offentliga nyckel.
  2. Vi prioriterar du till en HTTPS-meddelandet som skickas över en krypterad kanal med hjälp av Microsoft SSL-certifikat till service bus-relä.
  3. När meddelandet tas emot i en service bus, vaknar lokala agenten och autentiserar till en service bus med hjälp av starkt lösenord som skapades tidigare.
  4. Den lokala agenten tar upp det krypterade meddelandet och dekrypterar den med hjälp av den privata nyckeln vi genereras.
  5. Den lokala agenten försöker ställa in lösenordet via AD DS SetPassword-API: et. Det här steget är vad gör att vi kan genomdriva din Active Directory lokala lösenordsprincip (till exempel komplexitet, ålder, historik och filter) i molnet.
* **Principer för förfallodatum för meddelande** 
  * Om meddelandet är placerad i service bus eftersom den lokala tjänsten är avstängd, tidsgränsen och tas bort efter ett par minuter. Tidsgräns och borttagning av meddelandet ökar du säkerheten ytterligare.

### <a name="password-writeback-encryption-details"></a>Krypteringsinformation med tillbakaskrivning av lösenord

När användaren har skickat en återställning av lösenord, går återställning av begäran igenom flera krypteringssteg innan den tas emot i din lokala miljö. De här stegen för kryptering säkerställa maximala tjänsternas tillförlitlighet och säkerhet. De beskrivs på följande sätt:

* **Steg 1: Lösenordskryptering 2048-bitars RSA-nyckel**: när användaren har skickat ett lösenord att skrivas tillbaka till den lokala själva skickade lösenordet krypteras med en 2048-bitars RSA-nyckel.
* **Steg 2: Paketet på servernivå kryptering med AES-GCM**: hela paketet, lösenordet och metadata som krävs krypteras med hjälp av AES-GCM. Den här krypteringen förhindrar alla direkt åtkomst till den underliggande ServiceBus-kanalen från att visa eller ändra innehållet.
* **Steg 3: All kommunikation sker över TLS/SSL**: All kommunikation med ServiceBus sker i en kanal för SSL/TLS. Den här krypteringen skyddar innehållet från obehörig från tredje part.
* **Automatisk nyckelförnyelse var sjätte månad**: var sjätte månad eller tillbakaskrivning av lösenord varje gång är inaktiverat och därefter aktiveras i Azure AD Connect. Vi förnyas automatiskt alla för att säkerställa maximal Tjänstsäkerhet och säkerhet.

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

## <a name="next-steps"></a>Nästa steg

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](howto-sspr-deployment.md)
* [Återställ eller ändra ditt lösenord](../user-help/active-directory-passwords-update-your-own-password.md).
* [Registrera för återställning av lösenord för självbetjäning](../user-help/active-directory-passwords-reset-register.md).
* [Har du en fråga om licensiering?](concept-sspr-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](howto-sspr-authenticationdata.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](concept-sspr-howitworks.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](concept-sspr-policy.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](howto-sspr-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](concept-sspr-howitworks.md)
* [Jag tror att något har gått sönder. Hur gör jag för att felsöka SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)

[Writeback]: ./media/howto-sspr-writeback/enablepasswordwriteback.png "Aktivera tillbakaskrivning av lösenord i Azure AD Connect"
