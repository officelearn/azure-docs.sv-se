---
title: "Azure AD SSPR med tillbakaskrivning av lösenord | Microsoft Docs"
description: "Med hjälp av Azure AD och Azure AD Connect att tillbakaskrivning av lösenord till lokal katalog"
services: active-directory
keywords: "Hantering av Active directory-lösenord, lösenordshantering, Azure AD self service för lösenordsåterställning"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: gahug
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 8ce4d6d9024dc4ce3956220eb0678a6295b0b7ab
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="password-writeback-overview"></a>Översikt över tillbakaskrivning av lösenord

Tillbakaskrivning av lösenord kan du konfigurera Azure AD att tillbakaskrivning av lösenord till din lokala Active Directory. Det eliminerar behovet av att konfigurera och hantera en komplicerad lokala självbetjäning lösenord Återställ lösningen och det är ett bekvämt molnbaserade sätt för dina användare att återställa sina lokala lösenord oavsett var de befinner. Tillbakaskrivning av lösenord är en del av [Azure Active Directory Connect](./connect/active-directory-aadconnect.md) som kan aktiveras och används av aktuella Premium-prenumeranter [Azure Active Directory-versioner](active-directory-editions.md).

Tillbakaskrivning av lösenord innehåller följande funktioner:

* **Noll fördröjning feedback** -tillbakaskrivning av lösenord är en synkron åtgärd. Användarna meddelas omedelbart om lösenordet inte uppfyller principen eller gick inte att återställa eller ändras av någon anledning.
* **Har stöd för återställa lösenord för användare som använder AD FS eller andra tekniker för federation** -med tillbakaskrivning av lösenord, så länge som de federerade användarkontona synkroniseras till din Azure AD-klient som ska kunna hantera sina lokala AD-lösenord från molnet.
* **Har stöd för återställa lösenord för användare som använder [lösenordshashsynkronisering](./connect/active-directory-aadconnectsync-implement-password-synchronization.md)**  – när tjänsten för återställning av lösenord känner av att ett synkroniserade användarkonto har aktiverats för lösenordshashsynkronisering vi återställa lösenord för molnet och det här kontot på lokalt samtidigt.
* **Har stöd för ändring av lösenord från åtkomstpanelen och Office 365** – när federerade eller lösenord synkroniseras användare kommer att ändra sina lösenord har upphört att gälla eller ej upphört att gälla, vi skriva tillbaka lösenord till din lokala AD-miljö.
* **Har stöd för skrivning tillbaka lösenord när en administratör återställer dem från Azure portal** – när en administratör återställer ett lösenord i den [Azure-portalen](https://portal.azure.com)om användaren är federerat eller lösenord synkroniseras, vi ställer administratören väljer på din lokala AD, samt lösenordet. Detta stöds för närvarande inte i administrationsportalen för Office.
* **Tillämpar dina lokala AD lösenordsprinciper** – när en användare återställer sitt lösenord vi Kontrollera att den uppfyller dina lokala AD-princip innan du genomför den till den katalogen. Detta inkluderar historik, komplexitet, ålder, lösenordsfilter och eventuella andra begränsningar för lösenord du har definierat i din lokala AD.
* **Kräver inte några inkommande brandväggsregler** -tillbakaskrivning av lösenord använder en Azure Service Bus relay som en underliggande kommunikationskanalen, vilket innebär att du inte behöver öppna ingående portar i brandväggen för den här funktionen ska fungera.
* **Stöds inte för användarkonton som finns inom skyddade grupper i din lokala Active Directory** – för mer information om skyddade grupper, se [skyddade konton och grupper i Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

## <a name="how-password-writeback-works"></a>Så här fungerar tillbakaskrivning av lösenord

När en federerad eller lösenord hash synkroniserade användaren kommer att återställa eller ändra sina lösenord i molnet, inträffar följande:

1. Vi kontrollerar du vilken typ av lösenord som användaren har.
    * Om vi se hanteras lösenordet lokalt
        * Vi kontrollerar om tillbakaskrivning av tjänsten är igång och körs, om det är det så att användare kan fortsätta
        * Om tjänsten tillbakaskrivning inte är in, meddela vi användaren att deras lösenord inte kan återställas nu
2. Sedan användaren skickar rätt autentiserings-portar och når skärmen återställning av lösenord.
3. Användaren väljer ett nytt lösenord och bekräftar den.
4. När du klickar på Skicka kryptera vi lösenordet i klartext med en symmetrisk nyckel som skapades under installationen tillbakaskrivning.
5. Efter kryptering av lösenordet inkludera vi den i en nyttolast som skickas via en HTTPS-kanal till din klient-specifika service bus relay (som vi också konfigurerar du under installationen tillbakaskrivning). Den här relay skyddas av ett slumpmässigt genererat lösenord som känner av endast lokala installationen.
6. När meddelandet når service bus, vaknar lösenord Återställ slutpunkten automatiskt och ser att det finns en väntande begäran återställning.
7. Tjänsten sedan söker efter användaren i fråga med hjälp av fästpunktsattributet moln. För den här sökningen ska lyckas:

    * Användarobjektet måste finnas i AD-anslutarplats
    * Objektet måste vara kopplade till motsvarande MV-objekt
    * Användarobjektet måste länkas till motsvarande AAD connector-objekt.
    * Länken från AD connector-objekt till MV måste ha synkroniseringsregeln `Microsoft.InfromADUserAccountEnabled.xxx` på länken. <br> <br>
    När anropet kommer från molnet, Synkroniseringsmotorn använder cloudAnchor-attribut för att leta upp objektet AAD-koppling utrymme sätt länken tillbaka till MV-objekt och följer sedan länken tillbaka till AD-objekt. Eftersom det kan finnas flera AD-objekt (flera skogar) för samma användare, Synkroniseringsmotorn förlitar sig på den `Microsoft.InfromADUserAccountEnabled.xxx` länk för att välja rätt.

    > [!Note]
    > På grund av den här logiken att Azure AD Connect kunna kommunicera med PDC-emulatorn för tillbakaskrivning av lösenord ska fungera. Om du behöver aktivera det manuellt kan du ansluta Azure AD Connect till PDC-emulatorn genom att högerklicka på den **egenskaper** av Active Directory-synkronisering koppling, sedan välja **konfigurera katalogpartitioner**. Därifrån kan du söka efter avsnittet med **anslutningsinställningarna för domänkontrollanten** och markera rutan för att **endast använda prioriterade domänkontrollanter**. Även om den primära domänkontrollanten inte är en PDC-emulator, ska Azure AD Connect försöka ansluta till den primära domänkontrollanten för tillbakaskrivning av lösenord.

8. När användarkontot hittas, försöker vi återställa lösenordet direkt i lämplig AD-skog.
9. Om lösenordet set-åtgärd lyckas meddela vi användaren sitt lösenord har ändrats.
    > [!NOTE]
    > I fallet när användarens lösenord synkroniseras till Azure AD med hjälp av Lösenordssynkronisering, finns det en risk att den lokala lösenordsprincipen är lägre än lösenordsprincipen i molnet. Vi framtvinga i det här fallet fortfarande oavsett som den lokala principen och istället låta synkronisering av lösenords-hash att synkronisera hash för lösenordet. Detta säkerställer att din princip för lokal tillämpas i molnet, oavsett om du använder Lösenordssynkronisering eller federation för att tillhandahålla enkel inloggning.

10. Om lösenordet har angetts för åtgärden misslyckas vi felet returneras till användaren och låter dem försök igen.
    * Åtgärden kan misslyckas på grund av följande
        * Tjänsten är inaktiv
        * De markerade lösenordet inte uppfyller kraven för organisationens principer
        * Det gick inte att hitta användaren i lokala AD

    Vi har ett visst meddelande för många av dessa fall och meddela användaren om vad de kan göra för att lösa problemet.

## <a name="configuring-password-writeback"></a>Konfigurera tillbakaskrivning av lösenord

Vi rekommenderar att du använder funktionen för automatisk uppdatering av [Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md) om du vill använda tillbakaskrivning av lösenord.

DirSync och Azure AD Sync är inte längre stöds sätt för att aktivera tillbakaskrivning av lösenord artikeln [uppgradera från DirSync och Azure AD Sync](connect/active-directory-aadconnect-dirsync-deprecated.md) med mer information om hur med övergången.

Stegen nedan förutsätter att du redan har konfigurerat Azure AD Connect i din miljö med hjälp av den [Express](./connect/active-directory-aadconnect-get-started-express.md) eller [anpassade](./connect/active-directory-aadconnect-get-started-custom.md) inställningar.

1. Att konfigurera och aktivera lösenord tillbakaskrivning logga in till Azure AD Connect-servern och starta den **Azure AD Connect** konfigurationsguiden.
2. På välkomstskärmen klickar du på **konfigurera**.
3. Klicka på skärmen ytterligare uppgifter **anpassa synkroniseringsalternativ** och välj sedan **nästa**.
4. Ange autentiseringsuppgifter för en Global administratör på skärmen Anslut till Azure AD, och välj **nästa**.
5. På sidan Anslut dina kataloger och domän och Organisationsenhet Förfrågningsfiltrering kontrolleras, du kan välja **nästa**.
6. På skärmen för valfria funktioner du kryssrutan bredvid **tillbakaskrivning av lösenord** och på **nästa**.
   ![Aktivera tillbakaskrivning av lösenord i Azure AD Connect][Writeback]
7. Klicka på klart att konfigurera skärmen, **konfigurera** och vänta på att processen skulle slutföras.
8. När du ser konfiguration slutföra, klickar du på **avsluta**

## <a name="active-directory-permissions"></a>Active Directory-behörigheter

Kontot som angavs i Azure AD Connect-verktyget måste ha Återställ lösenord, ändra lösenord, skrivbehörighet på lockoutTime och skrivbehörighet på pwdLastSet, utökade behörigheter på antingen rotobjektet av **varje domän** i skogen **eller** användaren organisationsenheter som du vill att i omfånget för SSPR.

Om du inte är säker på vilket konto som anges ovan refererar till öppna Azure Active Directory Connect Konfigurationsgränssnittet och klicka på alternativet Visa aktuella konfiguration. Det konto som du behöver lägga till behörigheter till anges under ”synkroniseras kataloger”

Dessa behörigheter kan MA-tjänstkontot för varje skog hantera lösenord för användarkonton i skogen. **Om du inte tilldelar dessa behörigheter sedan få trots att tillbakaskrivning verkar vara korrekt konfigurerade, användare problem när du försöker hantera sina lokala lösenord från molnet.**

> [!NOTE]
> Det kan ta upp till en timme eller mer för behörigheterna att replikeras till alla objekt i katalogen.
>

Att ställa in lämplig behörighet för tillbakaskrivning av lösenord ska ske

1. Öppna Active Directory-användare och datorer med ett konto som har administratörsbehörighet för lämplig domän
2. Kontrollera att avancerade funktioner är aktiverat Visa-menyn
3. I den vänstra rutan högerklickar du på objektet som representerar roten för domänen och välj Egenskaper
    * Klicka på fliken Säkerhet
    * Klicka på Avancerat.
4. Klicka på Lägg till från fliken behörigheter
5. Välj kontot som behörigheter som används för (från Azure AD Connect-installation)
6. Välj underordnade objekt i gäller för listruta
7. Markera kryssrutorna för följande under behörigheter
    * Unexpire lösenord
    * Återställ lösenord
    * Ändra lösenord
    * Skriva lockoutTime
    * Skriva pwdLastSet
8. Klicka på Använd/OK genom att använda och stänga alla öppna dialogrutor.

## <a name="licensing-requirements-for-password-writeback"></a>Licensieringskrav för tillbakaskrivning av lösenord

Mer information om licensiering, se [licenser som krävs för tillbakaskrivning av lösenord](active-directory-passwords-licensing.md#licenses-required-for-password-writeback) eller på följande webbplatser

* [Platsen för Azure Active Directory-priser](https://azure.microsoft.com/pricing/details/active-directory/)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Säker produktiva Enterprise](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

### <a name="on-premises-authentication-modes-supported-for-password-writeback"></a>Lokala autentiseringslägen som stöds för tillbakaskrivning av lösenord

Tillbakaskrivning av lösenord fungerar för följande typer av användare lösenord:

* **Endast molnbaserad användare**: tillbakaskrivning av lösenord är inte giltigt i den här situationen eftersom det inte finns några lokala lösenord
* **Lösenord synkroniseras användare**: tillbakaskrivning av lösenord som stöds
* **Federerade användare**: tillbakaskrivning av lösenord som stöds
* **Direkt-autentisering användare**: tillbakaskrivning av lösenord som stöds

### <a name="user-and-admin-operations-supported-for-password-writeback"></a>Användare och administrativa åtgärder som stöds för tillbakaskrivning av lösenord

Lösenord skrivs tillbaka i följande situationer:

* **Slutanvändarens de åtgärder som stöds**
  * Alla slutanvändare självbetjäning frivillig ändra lösenord
  * Alla slutanvändare självbetjäning kraft ändra lösenord för åtgärden (t ex lösenordet upphör att gälla)
  * Alla slutanvändare Självbetjäning för lösenordsåterställning härstammar från den [portalen för återställning av lösenord](https://passwordreset.microsoftonline.com)
* **Administratören de åtgärder som stöds**
  * En administratör självbetjäning frivillig ändra lösenord
  * En administratör självbetjäning kraft ändra lösenord för åtgärden (t ex lösenordet upphör att gälla)
  * En administratör Självbetjäning för lösenordsåterställning härstammar från den [portalen för återställning av lösenord](https://passwordreset.microsoftonline.com)
  * Alla administratörsstartade slutanvändarens lösenordsåterställning från den [klassiska Azure-portalen](https://manage.windowsazure.com)
  * Alla administratörsstartade slutanvändarens lösenordsåterställning från den [Azure-portalen](https://portal.azure.com)

### <a name="user-and-admin-operations-not-supported-for-password-writeback"></a>Användar- och Admin åtgärder stöds inte för tillbakaskrivning av lösenord

Lösenord är **inte** skrivs tillbaka i någon av följande situationer:

* **Stöds inte slutanvändaråtgärder**
  * Slutanvändare återställa sina egna lösenord med hjälp av PowerShell v1, v2 eller Azure AD Graph API
* **Åtgärder som inte stöds**
  * Alla administratörsstartade slutanvändarens lösenordsåterställning från den [hanteringsportalen för Office](https://portal.office.com)
  * Alla administratörsstartade slutanvändarens lösenordsåterställning från PowerShell v1, v2 eller Azure AD Graph API

När vi arbetar för att ta bort dessa begränsningar kan har vi inte en specifik tidslinje vi kan dela ännu.

## <a name="password-writeback-security-model"></a>Säkerhetsmodellen med tillbakaskrivning av lösenord

Tillbakaskrivning av lösenord är ett mycket säkert tjänst.  För att säkerställa att informationen är skyddad, det för att aktivera en 4 nivåer säkerhetsmodell som beskrivs nedan.

* **Klient-specifika service bus relay**
  * När du konfigurerar tjänsten skapa vi en klient-specifika service bus relay som skyddas av ett slumpmässigt genererat starkt lösenord som Microsoft har aldrig åtkomst till.
* **Krypteringsnyckel för låst, kryptografiskt starka lösenord**
  * När service bus relay har skapats kan skapa vi en stark symmetriska nyckel som vi använder för att kryptera lösenordet när det gäller via kabel. Den här nyckeln finns bara i ditt företag hemliga store i molnet, som är kraftigt låst och granskas precis som alla lösenord i katalogen.
* **Industry standard TLS**
  1. När ett lösenord återställa eller ändra åtgärden sker i molnet, vi ta lösenordet i klartext och kryptera med den offentliga nyckeln.
  2. Vi sedan placera som i en HTTPS-meddelandet som skickas via en krypterad kanal med hjälp av Microsofts SSL-certifikat till din service bus relay.
  3. När meddelandet anländer till Service Bus, vaknar agenten lokalt och autentiserar till Service Bus med starka lösenord som skapades tidigare.
  4. Lokal agent hämtar det krypterade meddelandet, dekrypterar den med hjälp av den privata nyckeln som vi har skapat.
  5. Lokal agent försöker ställa in lösenordet via AD DS SetPassword API.
     * Det här steget är vad gör att vi kan genomdriva din AD lokala lösenordsprincip (komplexitet, ålder, historik, filter, etc.) i molnet.
* **Förfallodatum för meddelandet** 
  * Om meddelandet placeras i Service Bus eftersom din lokala-tjänsten har stoppats, avslutas och tas bort efter flera minuter att öka säkerheten ytterligare.

### <a name="password-writeback-encryption-details"></a>Krypteringsinformation med tillbakaskrivning av lösenord

Krypteringssteg som en begäran om lösenordsåterställning går igenom när användaren har skickat det, innan den tas emot i din lokala miljö, så högsta tillåtna tillförlitlighet och säkerhet som beskrivs nedan.

* **Steg 1 – lösenordskryptering med 2048-bitars RSA-nyckel** – när en användare skickar ett lösenord för att skrivas tillbaka till lokalt, först själva skickade lösenordet krypteras med en 2048-bitars RSA-nyckel.
* **Steg 2 – paket-nivå kryptering med AES-GCM** - sedan paketet (lösenord + metadata som krävs) har krypterats med AES-GCM. Krypteringsnycklar förhindrar alla med direkt åtkomst till den underliggande ServiceBus-kanalen visning/manipulation av innehållet.
* **Steg 3 – All kommunikation som sker över TLS / SSL** -All kommunikation med ServiceBus sker i SSL/TLS-kanal. Denna kryptering skyddar innehållet från obehörig tredje part.
* **Automatisk nyckelförnyelse var sjätte månad** – var sjätte månad eller varje gång tillbakaskrivning av lösenord är inaktiverad / aktiveras på Azure AD Connect vi automatiskt förnya alla nycklar för att säkerställa högsta säkerheten.

### <a name="password-writeback-bandwidth-usage"></a>Bandbreddsanvändning med tillbakaskrivning av lösenord

Tillbakaskrivning av lösenord är en långsam-tjänst som skickar begäranden till den lokala agenten endast under följande omständigheter:

1. Två meddelanden när du aktiverar eller inaktiverar funktionen via Azure AD Connect.
2. Ett meddelande skickas en gång var femte minut som ett pulsslag för så länge som tjänsten körs.
3. Två meddelanden skickas varje gång ett nytt lösenord har skickats
    * Första meddelandet som en begäran att utföra åtgärden
    * Andra meddelande som innehåller resultatet av åtgärden och skickas under följande omständigheter:
        * Varje gång ett nytt lösenord skickas när en användare lösenordsåterställning via självbetjäning.
        * Ett nytt lösenord skickas under en användarlösenord ändras igen.
        * Varje gång ett nytt lösenord skickas under en admin-initierad användaren lösenordsåterställning (från Azure admin portalerna endast).

#### <a name="message-size-and-bandwidth-considerations"></a>Överväganden för meddelande storlek och bandbredd

Storleken på varje meddelande som beskrivs ovan är vanligtvis under 1 kb, även under extrema belastning, förbrukar några kbit / s bandbredd för själva tjänsten för tillbakaskrivning av lösenord. Eftersom varje meddelande som skickas i realtid, är endast om krävs för en uppdateringsåtgärd för lösenord, och eftersom meddelandelagringsstorlek är så liten bandbreddsanvändningen för tillbakaskrivning av kapaciteten effektivt för liten för att ha några verkliga märkbar effekt.

## <a name="next-steps"></a>Nästa steg

* [Hur jag för att slutföra en lyckad distribution av SSPR?](active-directory-passwords-best-practices.md)
* [Återställa eller ändra ditt lösenord](active-directory-passwords-update-your-own-password.md).
* [Registrera dig för lösenordsåterställning via självbetjäning](active-directory-passwords-reset-register.md).
* [Har du en fråga med licensiering?](active-directory-passwords-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla för dina användare?](active-directory-passwords-data.md)
* [Vilka autentiseringsmetoder som är tillgängliga för användarna?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Vad är policyalternativen med SSPR?](active-directory-passwords-policy.md)
* [Hur rapporterar på aktivitet i SSPR?](active-directory-passwords-reporting.md)
* [Vad är alla alternativ i SSPR och vad de betyder?](active-directory-passwords-how-it-works.md)
* [Jag tror att något har brutits. Hur felsöker SSPR?](active-directory-passwords-troubleshoot.md)
* [Jag har en fråga som inte var motsvarar någon annan](active-directory-passwords-faq.md)

[Writeback]: ./media/active-directory-passwords-writeback/enablepasswordwriteback.png "Aktivera tillbakaskrivning av lösenord i Azure AD Connect"
