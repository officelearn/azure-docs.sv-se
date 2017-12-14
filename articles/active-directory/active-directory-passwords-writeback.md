---
title: "Azure AD SSPR med tillbakaskrivning av lösenord | Microsoft Docs"
description: "Använda Azure AD och Azure AD Connect att tillbakaskrivning av lösenord till en lokal katalog"
services: active-directory
keywords: "Hantering av Active directory-lösenord, lösenordshantering, Azure AD self service för lösenordsåterställning"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 12/06/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 6dfc3246b210b382665eeef2d638945c91d5b62f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="password-writeback-overview"></a>Översikt över tillbakaskrivning av lösenord

Du kan konfigurera Azure Active Directory (Azure AD) för att skriva tillbaka lösenord till din lokala Active Directory med tillbakaskrivning av lösenord. Tillbakaskrivning av lösenord eliminerar behovet av att konfigurera och hantera en komplicerad lokalt lösenordsåterställning självbetjäning (SSPR) återställningslösning och ger ett bekvämt molnbaserade sätt för dina användare att återställa sina lokala lösenord oavsett var de befinner. Tillbakaskrivning av lösenord är en del av [Azure Active Directory Connect](./connect/active-directory-aadconnect.md) som kan aktiveras och används av aktuella Premium-prenumeranter [Azure Active Directory-versioner](active-directory-whatis.md).

Tillbakaskrivning av lösenord innehåller följande funktioner:

* **Ger feedback noll fördröjning**: tillbakaskrivning av lösenord är en synkron åtgärd. Användarna meddelas omedelbart om sina lösenord inte uppfyllde principen eller inte kunde återställa eller ändras av någon anledning.
* **Har stöd för lösenordsåterställning för användare som använder Active Directory Federation Services (AD FS) eller andra tekniker för federation**: med tillbakaskrivning av lösenord, så länge som de federerade användarkontona synkroniseras till din Azure AD-klient som ska kunna hantera sina lokala Active Directory-lösenord från molnet.
* **Har stöd för lösenordsåterställning för användare som använder** [lösenordshashsynkronisering](./connect/active-directory-aadconnectsync-implement-password-synchronization.md): när tjänsten för återställning av lösenord upptäcker att ett synkroniserade användarkonto är aktiverat för lösenordets hash-synkronisering, återställs både det här kontot Lokal och moln lösenord samtidigt.
* **Har stöd för lösenord ändras från åtkomstpanelen och Office 365**: om sammanslaget eller lösenord synkroniseras användare kommer att ändra sina lösenord har upphört att gälla eller ej upphört att gälla, vi skriva tillbaka lösenord till din lokala Active Directory-miljö.
* **Stöder tillbakaskrivning av lösenord när en administratör återställer dem från Azure portal**: när en administratör återställer ett lösenord i den [Azure-portalen](https://portal.azure.com)om användaren är federerat eller lösenord synkroniseras, vi ställer lösenordet administratören väljer i samt lokala Active Directory. Den här funktionen stöds inte för närvarande i administrationsportalen för Office.
* **Tillämpar din lokala Active Directory-lösenordsprinciper**: när en användare återställer sitt lösenord, vi Kontrollera att den uppfyller din lokala Active Directory-princip innan vi genomför till den katalogen. Den här granska inkluderar kontroll historiken, komplexitet, ålder, lösenordsfilter och eventuella andra begränsningar för lösenord som du har definierat i lokala Active Directory.
* **Kräver inte några inkommande brandväggsregler**: tillbakaskrivning av lösenord använder en Azure Service Bus relay som en underliggande kommunikationskanalen. Du behöver öppna ingående portar i brandväggen för den här funktionen ska fungera.
* **Stöds inte för användarkonton som finns i skyddade grupper i lokala Active Directory**: Mer information om skyddade grupper finns [skyddade konton och grupper i Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

## <a name="how-password-writeback-works"></a>Så här fungerar tillbakaskrivning av lösenord

När en federerad eller lösenord hash synkroniserade användaren kommer att återställa eller ändra sina lösenord i molnet, inträffar följande:

1. Vi kontrollerar du vilken typ av lösenord som användaren har. Om vi se att lösenordet är hanteras lokalt:
   * Vi kontrollerar om tillbakaskrivning av tjänsten är igång. Om den är igång, så vi att användare kan fortsätta.
   * Om tjänsten tillbakaskrivning inte är in, meddela vi användaren att deras lösenord inte kan återställas just nu.
2. Sedan användaren skickar rätt autentiserings-portar och når den **Återställ lösenord** sidan.
3. Användaren väljer ett nytt lösenord och bekräftar den.
4. När användaren väljer **skicka**, vi kryptera lösenordet i klartext med en symmetrisk nyckel som skapades under installationen tillbakaskrivning.
5. Efter kryptering av lösenordet inkludera vi den i en nyttolast som skickas via en HTTPS-kanal till din klient-specifika service bus relay (som vi också konfigurerar du under installationen tillbakaskrivning). Den här relay skyddas av ett slumpmässigt genererat lösenord som känner av endast lokala installationen.
6. När meddelandet når service bus, vaknar slutpunkten för återställning av lösenord automatiskt och ser att det finns en väntande begäran återställning.
7. Tjänsten söker sedan efter användaren med hjälp av fästpunktsattributet moln. För den här sökningen ska lyckas:

    * Användarobjektet måste finnas i Active Directory-anslutningsplatsen.
    * Användarobjektet måste kopplas till den motsvarande metaversumobjekt (MV).
    * Användarobjektet måste länkas till motsvarande Azure Active Directory connector-objekt.
    * Länken från Active Directory connector-objektet till MV måste ha synkroniseringsregeln `Microsoft.InfromADUserAccountEnabled.xxx` på länken. <br> <br>
    När anropet kommer från molnet, Synkroniseringsmotorn använder den **cloudAnchor** attribut för att leta upp Azure Active Directory connector utrymme-objektet. Den sedan efter länken MV-objekt och följer sedan länken till Active Directory-objekt. Eftersom det kan finnas flera Active Directory-objekt (flera skogar) för samma användare, Synkroniseringsmotorn förlitar sig på den `Microsoft.InfromADUserAccountEnabled.xxx` länk för att välja rätt.

    > [!Note]
    > På grund av den här logiken för lösenord måste tillbakaskrivningen ska fungera Azure AD Connect kunna kommunicera med primära domänkontrollantens (PDC) emulator. Om du behöver aktivera det manuellt kan du ansluta Azure AD Connect till PDC-emulatorn. Högerklicka på den **egenskaper** av Active Directory-synkronisering koppling, Välj **konfigurera katalogpartitioner**. Därifrån kan du leta efter den **inställningarna för domänkontrollanter anslutning** och välj rutan **endast använder prioriterade domänkontrollanter**. Även om den primära domänkontrollanten inte är en PDC-emulator, försöker Azure AD Connect att ansluta till den primära domänkontrollanten för tillbakaskrivning av lösenord.

8. När användaren att hitta kontot, vi försöker återställa lösenordet direkt i rätt Active Directory-skogen.
9. Om lösenordet set-åtgärd lyckas meddela vi användaren sitt lösenord har ändrats.
    > [!NOTE]
    > Om användarens lösenord synkroniseras till Azure AD med hjälp av Lösenordssynkronisering, finns det en risk att den lokala lösenordsprincipen är lägre än lösenordsprincipen i molnet. Vi framtvinga i det här fallet fortfarande allt som den lokala principen och i stället använda synkronisering av lösenords-hash för att synkronisera hash för lösenordet. Den här principen ser till att den lokala principen tillämpas i molnet, oavsett om du använder Lösenordssynkronisering eller federation för att tillhandahålla enkel inloggning.

10. Om lösenordet har angetts för åtgärden misslyckas vi ett fel returneras till användaren och låter dem försök igen. Åtgärden kan misslyckas eftersom:
    * Tjänsten är inaktiv.
    * De markerade lösenordet uppfyller inte organisationens principer.
    * Vi kan inte hitta användaren i lokala Active Directory.

    Vi har ett visst meddelande för många av dessa fall och meddela användaren om vad de kan göra för att lösa problemet.

## <a name="configure-password-writeback"></a>Konfigurera tillbakaskrivning av lösenord

Vi rekommenderar att du använder funktionen för automatisk uppdatering av [Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md) om du vill använda tillbakaskrivning av lösenord.

DirSync och Azure AD Sync stöds inte längre som ett sätt att aktivera tillbakaskrivning av lösenord. Mer information att hjälpa med övergången finns [uppgradera från DirSync och Azure AD Sync](connect/active-directory-aadconnect-dirsync-deprecated.md).

Följande steg förutsätter att du redan har konfigurerat Azure AD Connect i din miljö med hjälp av den [Express](./connect/active-directory-aadconnect-get-started-express.md) eller [anpassade](./connect/active-directory-aadconnect-get-started-custom.md) inställningar.

1. Om du vill konfigurera och aktivera tillbakaskrivning av lösenord, logga in till Azure AD Connect-servern och starta den **Azure AD Connect** konfigurationsguiden.
2. På den **Välkommen** väljer **konfigurera**.
3. På den **ytterligare uppgifter** väljer **anpassa synkroniseringsalternativ**, och välj sedan **nästa**.
4. På den **Anslut till Azure AD** sidan Ange autentiseringsuppgifter för en global administratör och välj sedan **nästa**.
5. På den **ansluta kataloger** och **domän/OU** filtrering sidor, Välj **nästa**.
6. På den **valfria funktioner** markerar du kryssrutan bredvid **tillbakaskrivning av lösenord** och välj **nästa**.
   ![Aktivera tillbakaskrivning av lösenord i Azure AD Connect][Writeback]
7. På den **redo att konfigurera** väljer **konfigurera** och vänta tills processen har slutförts.
8. När du ser konfigurationen är klar, Välj **avsluta**.

Vanliga felsökning uppgifter som rör tillbakaskrivning av lösenord, finns i [felsöka tillbakaskrivning av lösenord](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) i vår felsökningsartikel.

## <a name="active-directory-permissions"></a>Active Directory-behörigheter

Kontot som angetts i Azure AD Connect-verktyget måste ha följande ange om du vill ska vara i omfånget för SSPR:

* **Återställ lösenord** 
* **Ändra lösenord** 
* **Skrivbehörighet** på`lockoutTime`  
* **Skrivbehörighet** på`pwdLastSet`
* **Utökade rättigheter** på antingen:
   * Rotobjektet av *varje domän* i den skogen
   * Användaren organisationsenheter (OU) som du vill ska vara i omfånget för SSPR

Om du inte vet vilket konto beskrivs kontot refererar till, öppna Azure Active Directory Connect Konfigurationsgränssnittet och välj den **aktuella konfiguration** alternativet. Det konto som du behöver lägga till behörigheter till anges under **synkroniseras kataloger**.

Om du anger dessa behörigheter kan MA-tjänstkontot för varje skog hantera lösenord för användarkonton i skogen. 

> [!IMPORTANT]
> Om du inte tilldelar dessa behörigheter sedan uppstår trots att tillbakaskrivning verkar vara korrekt konfigurerade, användare fel när de försöker hantera sina lokala lösenord från molnet.
>

> [!NOTE]
> Det kan ta upp till en timme eller mer för behörigheterna att replikeras till alla objekt i katalogen.
>

Utför följande steg för att ställa in lämplig behörighet för tillbakaskrivning av lösenord ska ske:

1. Öppna Active Directory-användare och datorer med ett konto som har administratörsbehörighet för lämplig domän.
2. Från den **visa** menyn Kontrollera **avancerade funktioner** är aktiverat.
3. I den vänstra rutan högerklickar du på det objekt som representerar roten för domänen och välj **egenskaper** > **säkerhet** > **Avancerat**.
4. Från den **behörigheter** väljer **Lägg till**.
5. Välj det konto som behörigheter som används för (från Azure AD Connect-installationen).
6. I den **gäller** listrutan, Välj **underordnade** objekt.
7. Under **behörigheter**, markerar kryssrutorna för följande:
    * **Återställ lösenord**
    * **Ändra lösenord**
    * **Skriva lockoutTime**
    * **Skriva pwdLastSet**
8. Välj **Använd/OK** att tillämpa ändringarna och stänga alla öppna dialogrutor.

## <a name="licensing-requirements-for-password-writeback"></a>Licensieringskrav för tillbakaskrivning av lösenord

Information om licensiering finns [licenser som krävs för tillbakaskrivning av lösenord](active-directory-passwords-licensing.md#licenses-required-for-password-writeback) eller på följande webbplatser:

* [Azure Active Directory priser för platsen](https://azure.microsoft.com/pricing/details/active-directory/)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

### <a name="on-premises-authentication-modes-that-are-supported-for-password-writeback"></a>Lokala autentiseringslägen som stöds för tillbakaskrivning av lösenord

Lösenord tillbakaskrivning stöd för följande typer av användare lösenord:

* **Endast molnbaserad användare**: tillbakaskrivning av lösenord är inte giltigt i den här situationen eftersom det inte finns några lokala lösenord.
* **Lösenord synkroniseras användare**: tillbakaskrivning av lösenord stöds.
* **Federerade användare**: tillbakaskrivning av lösenord stöds.
* **Direkt-autentisering användare**: tillbakaskrivning av lösenord stöds.

### <a name="user-and-admin-operations-that-are-supported-for-password-writeback"></a>Användare och administrativa åtgärder som stöds för tillbakaskrivning av lösenord

Lösenord skrivs tillbaka i följande situationer:

* **Stöds slutanvändaråtgärder**
  * Alla slutanvändare självbetjäning frivillig ändra lösenord
  * Alla slutanvändare självbetjäning kraft ändra lösenord, till exempel lösenordet upphör att gälla
  * Alla slutanvändare Självbetjäning för lösenordsåterställning som härrör från den [portalen för återställning av lösenord](https://passwordreset.microsoftonline.com)
* **Stöds åtgärder**
  * En administratör självbetjäning frivillig ändra lösenord
  * En administratör självbetjäning kraft ändra lösenord, till exempel lösenordet upphör att gälla
  * En administratör Självbetjäning för lösenordsåterställning som härrör från den [portalen för återställning av lösenord](https://passwordreset.microsoftonline.com)
  * Alla administratörsstartade slutanvändarens lösenordsåterställning från den [Azure-portalen](https://portal.azure.com)

### <a name="user-and-admin-operations-that-are-not-supported-for-password-writeback"></a>Användar- och admin-åtgärder som inte stöds för tillbakaskrivning av lösenord

Lösenord är *inte* skrivs tillbaka i någon av följande situationer:

* **Slutanvändaråtgärder stöds inte**
  * Slutanvändare återställa sina egna lösenord med hjälp av PowerShell version 1, version 2 eller Azure AD Graph API
* **Åtgärder som inte stöds**
  * Alla administratörsstartade slutanvändarens lösenordsåterställning från den [hanteringsportalen för Office](https://portal.office.com)
  * Alla administratörsstartade slutanvändarens lösenordsåterställning från PowerShell version 1, version 2 eller Azure AD Graph API

Vi arbetar för att ta bort dessa begränsningar, men vi har inte en specifik tidslinje vi kan dela ännu.

## <a name="password-writeback-security-model"></a>Säkerhetsmodellen med tillbakaskrivning av lösenord

Tillbakaskrivning av lösenord är ett mycket säkert tjänst. För att säkerställa att informationen är skyddad, det för att aktivera en fyra skikt säkerhetsmodell som beskrivs nedan:

* **Klient-specifika service bus relay**
  * När du konfigurerar tjänsten skapa vi en klient-specifika service bus relay som skyddas av ett slumpmässigt genererat starkt lösenord som Microsoft har aldrig åtkomst till.
* **Krypteringsnyckel för låst, kryptografiskt starka lösenord**
  * När service bus relay har skapats kan skapa vi en stark symmetriska nyckel som vi använder för att kryptera lösenordet när det gäller via kabel. Den här nyckeln finns endast i ditt företag hemliga store i molnet, som är kraftigt låst och granskas, precis som andra lösenord i katalogen.
* **Industry standard TLS Transport Layer Security)**
  1. När ett lösenord återställa eller ändra åtgärden sker i molnet, vi ta lösenordet i klartext och kryptera med den offentliga nyckeln.
  2. Det sätt som i en HTTPS-meddelandet som skickas via en krypterad kanal genom att använda Microsoft SSL-certifikat till din service bus relay.
  3. När meddelandet tas emot i service bus, vaknar agenten lokalt och autentiserar till service bus med hjälp av starkt lösenord som skapades tidigare.
  4. Agenten lokalt hämtar det krypterade meddelandet och dekrypterar den med hjälp av den privata nyckeln som vi har skapat.
  5. Lokal agent försöker ställa in lösenordet via AD DS SetPassword API. Det här steget är vad gör att vi kan genomdriva lösenordsprinciper din Active Directory lokalt (till exempel komplexitet, ålder, historik och filter) i molnet.
* **Förfallodatum för meddelandet** 
  * Om meddelandet placeras i service bus eftersom din lokala-tjänsten har stoppats, timeout och tas bort efter flera minuter. Ökar säkerheten ytterligare timeout och borttagning av meddelandet.

### <a name="password-writeback-encryption-details"></a>Krypteringsinformation med tillbakaskrivning av lösenord

När en användare skickar en återställning av lösenord, går Återställ-begäran igenom flera krypteringssteg för innan den tas emot i din lokala miljö. Stegen kryptering säkerställa maximala service tillförlitlighet och säkerhet. De beskrivs på följande sätt:

* **Steg 1: Lösenordskryptering med 2048-bitars RSA-nyckel**: när en användare skickar ett lösenord för att skrivas tillbaka till lokala kan själva skickade lösenordet krypteras med en 2048-bitars RSA-nyckel.
* **Steg 2: Paketet nivå kryptering med AES-GCM**: hela paketet, lösenordet plus metadata som krävs är krypterad med AES-GCM. Krypteringsnycklar förhindrar alla med direkt åtkomst till den underliggande ServiceBus-kanalen visa och manipulera innehållet.
* **Steg 3: All kommunikation som sker över TLS/SSL**: All kommunikation med ServiceBus sker i SSL/TLS-kanal. Denna kryptering skyddar innehållet från obehörig tredje part.
* **Automatisk nyckelförnyelse var sjätte månad**: var sjätte månad eller tillbakaskrivning av lösenord varje gång är inaktiverat och därefter aktiveras på Azure AD Connect. Vi rulla automatiskt över alla nycklar för att säkerställa högsta säkerhet och säkerhet.

### <a name="password-writeback-bandwidth-usage"></a>Bandbreddsanvändning med tillbakaskrivning av lösenord

Tillbakaskrivning av lösenord är en tjänst för låg bandbredd som endast skickar begäranden till den lokala agenten under följande omständigheter:

* Två meddelanden skickas när funktionen är aktiverad eller inaktiverad via Azure AD Connect.
* Ett meddelande skickas en gång var femte minut som ett pulsslag för så länge som tjänsten körs.
* Två meddelanden skickas varje gång som ett nytt lösenord skickas:
    * Det första meddelandet är en begäran att utföra åtgärden.
    * Det andra meddelandet innehåller resultatet av åtgärden och skickas under följande omständigheter:
        * Varje gång ett nytt lösenord skickas när en användare lösenordsåterställning via självbetjäning.
        * Ett nytt lösenord skickas under en användarlösenord ändras igen.
        * Varje gång ett nytt lösenord skickas under en admin-initierad användaren lösenordsåterställning (endast från Azure admin-portaler).

#### <a name="message-size-and-bandwidth-considerations"></a>Överväganden för meddelande storlek och bandbredd

Storleken på varje meddelande som beskrivs ovan är vanligtvis under 1 KB. Även under extrema belastningar förbrukar själva tjänsten för tillbakaskrivning av lösenord några kbit / s bandbredd. Eftersom varje meddelande som skickas i realtid, bara vid behov av en uppdateringsåtgärd för lösenord och eftersom meddelandelagringsstorlek är så liten är bandbreddsanvändningen för tillbakaskrivning kapaciteten för liten för att ha någon märkbar effekt.

## <a name="next-steps"></a>Nästa steg

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](active-directory-passwords-best-practices.md)
* [Återställ eller ändra ditt lösenord](active-directory-passwords-update-your-own-password.md).
* [Registrera för återställning av lösenord för självbetjäning](active-directory-passwords-reset-register.md).
* [Har du en fråga om licensiering?](active-directory-passwords-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](active-directory-passwords-data.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](active-directory-passwords-policy.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](active-directory-passwords-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](active-directory-passwords-how-it-works.md)
* [Jag tror att något har gått sönder. Hur gör jag för att felsöka SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)

[Writeback]: ./media/active-directory-passwords-writeback/enablepasswordwriteback.png "Aktivera tillbakaskrivning av lösenord i Azure AD Connect"
