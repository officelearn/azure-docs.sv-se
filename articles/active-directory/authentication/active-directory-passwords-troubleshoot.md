---
title: Fel sökning av självbetjäning för lösen ords återställning – Azure Active Directory
description: Fel sökning av återställning av lösen ord för självbetjäning i Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30fd1ebf8a59c80764335be47c986ea77f5d1438
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879178"
---
# <a name="troubleshoot-self-service-password-reset"></a>Felsöka lösen ords återställning via självbetjäning

Har du problem med Azure Active Directory (Azure AD) självbetjäning för återställning av lösen ord (SSPR)? Informationen som följer kan hjälpa dig att få saker att fungera igen.

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-might-see"></a>Felsöka fel vid återställning av lösen ord för självbetjäning som en användare kan se

| Fel | Information | Teknisk information |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Du kan tyvärr inte återställa ditt lösen ord just nu eftersom din administratör har inaktiverat lösen ords återställning för din organisation. Det finns ingen ytterligare åtgärd som du kan vidta för att lösa den här situationen. Kontakta administratören och be dem att aktivera den här funktionen. Mer information finns i [hjälp, jag har glömt mitt Azure AD-lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions). | SSPR_0009: Vi har upptäckt att lösen ords återställning inte har Aktiver ATS av administratören. Kontakta din administratör och be dem att aktivera återställning av lösen ord för din organisation. |
| WritebackNotEnabled = 10 |Du kan tyvärr inte återställa ditt lösen ord just nu eftersom administratören inte har aktiverat en nödvändig tjänst för din organisation. Det finns ingen ytterligare åtgärd som du kan vidta för att lösa den här situationen. Kontakta din administratör och be dem att kontrol lera din organisations konfiguration. Mer information om den här nödvändiga tjänsten finns i [Konfigurera tillbakaskrivning av lösen ord](howto-sspr-writeback.md). | SSPR_0010: Vi har upptäckt att tillbakaskrivning av lösen ord inte har Aktiver ATS. Kontakta din administratör och be dem att aktivera tillbakaskrivning av lösen ord. |
| SsprNotEnabledInUserPolicy = 11 | Du kan tyvärr inte återställa ditt lösen ord just nu eftersom administratören inte har konfigurerat lösen ords återställning för din organisation. Det finns ingen ytterligare åtgärd som du kan vidta för att lösa den här situationen. Kontakta administratören och be dem att konfigurera lösen ords återställning. Mer information om konfiguration av lösen ords återställning finns [i snabb start: Lösen ords återställning](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)via självbetjäning i Azure AD. | SSPR_0011: Din organisation har inte definierat en princip för återställning av lösen ord. Kontakta administratören och be dem att definiera en princip för lösen ords återställning. |
| UserNotLicensed = 12 | Du kan tyvärr inte återställa ditt lösen ord just nu eftersom de licenser som krävs saknas i din organisation. Det finns ingen ytterligare åtgärd som du kan vidta för att lösa den här situationen. Kontakta din administratör och be dem att kontrol lera licens tilldelningen. Läs mer om licensiering i [licens krav för återställning av lösen ord för Azure AD Self-Service](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing). | SSPR_0012: Din organisation har inte de nödvändiga licenser som krävs för att återställa lösen ordet. Kontakta din administratör och be dem att granska licens tilldelningarna. |
| UserNotMemberOfScopedAccessGroup = 13 | Du kan tyvärr inte återställa ditt lösen ord just nu eftersom din administratör inte har konfigurerat ditt konto för att använda lösen ords återställning. Det finns ingen ytterligare åtgärd som du kan vidta för att lösa den här situationen. Kontakta administratören och be dem att konfigurera ditt konto för lösen ords återställning. Mer information om konto konfigurationen för lösen ords återställning finns i [distribuera lösen ords återställning för användare](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices). | SSPR_0013: Du är inte medlem i en grupp som är aktive rad för lösen ords återställning. Kontakta din administratör och din begäran som ska läggas till i gruppen. |
| UserNotProperlyConfigured = 14 | Du kan tyvärr inte återställa ditt lösen ord just nu eftersom nödvändig information saknas i ditt konto. Det finns ingen ytterligare åtgärd som du kan vidta för att lösa den här situationen. Kontakta administratören och be dem att återställa ditt lösen ord åt dig. När du har åtkomst till ditt konto igen måste du registrera den information som krävs. Om du vill registrera information följer du stegen i artikeln [Registrera för självbetjäning för återställning av lösen ord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register) . | SSPR_0014: Ytterligare säkerhets information krävs för att återställa ditt lösen ord. Kontakta administratören och be dem att återställa ditt lösen ord om du vill fortsätta. När du har åtkomst till ditt konto kan du registrera ytterligare säkerhets information på https://aka.ms/ssprsetup. Din administratör kan lägga till ytterligare säkerhets information i ditt konto genom att följa stegen i [Ange och läsa autentiseringsdata för lösen ords återställning](howto-sspr-authenticationdata.md). |
| OnPremisesAdminActionRequired = 29 | Vi kan tyvärr inte återställa ditt lösen ord just nu på grund av ett problem med konfigurationen av lösen ords återställning i organisationen. Det finns ingen ytterligare åtgärd som du kan vidta för att lösa den här situationen. Kontakta din administratör och be dem att undersöka. Mer information om det potentiella problemet finns i [Felsöka tillbakaskrivning av lösen ord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback). | SSPR_0029: Det går inte att återställa lösen ordet på grund av ett fel i din lokala konfiguration. Kontakta din administratör och be dem att undersöka. |
| OnPremisesConnectivityError = 30 | Vi kan tyvärr inte återställa ditt lösen ord just nu på grund av anslutnings problem till din organisation. Det finns ingen åtgärd att ta just nu, men problemet kan lösas om du försöker igen senare. Kontakta administratören och be dem att undersöka om problemet kvarstår. Mer information om anslutnings problem finns i [Felsöka anslutningar för tillbakaskrivning av lösen ord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity). | SSPR_0030: Vi kan inte återställa lösen ordet på grund av en dålig anslutning till din lokala miljö. Kontakta administratören och be dem att undersöka.|

## <a name="troubleshoot-the-password-reset-configuration-in-the-azure-portal"></a>Felsök konfigurationen för lösen ords återställning i Azure Portal

| Fel | Lösning |
| --- | --- |
| Jag ser inte avsnittet **lösen ords återställning** under Azure AD i Azure Portal. | Detta kan inträffa om du inte har en Azure AD-licens som tilldelats administratören att utföra åtgärden. <br> <br> Tilldela en licens till administratörs kontot i fråga. Du kan följa stegen i artikeln [tilldela, verifiera och lösa problem med licenser](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) .|
| Jag kan inte se ett visst konfigurations alternativ. | Många delar av användar gränssnittet är dolda tills de behövs. Försök att aktivera alla alternativ som du vill se. |
| Jag ser inte fliken **lokal integrering** . | Det här alternativet är bara synligt om du har hämtat Azure AD Connect och har konfigurerat tillbakaskrivning av lösen ord. Mer information finns i [komma igång med Azure AD Connect med hjälp av Express-inställningarna](../hybrid/how-to-connect-install-express.md). |

## <a name="troubleshoot-password-reset-reporting"></a>Felsöka rapportering av lösen ords återställning

| Fel | Lösning |
| --- | --- |
| Jag ser inte några aktivitets typer för lösen ords hantering i händelse kategorin för granskning av **lösen ords hantering** i självbetjäning. | Detta kan inträffa om du inte har en Azure AD-licens som tilldelats administratören att utföra åtgärden. <br> <br> Du kan lösa problemet genom att tilldela en licens till administratörs kontot i fråga. Följ stegen i artikeln [tilldela, verifiera och lösa problem med licenser](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) . |
| Användar registreringar visas flera gånger. | För närvarande loggar vi varje enskild del av de data som är registrerade som en separat händelse när en användare registreras. <br> <br> Om du vill aggregera dessa data och få större flexibilitet i hur du kan visa den, kan du hämta rapporten och öppna data som en pivot-tabell i Excel.

## <a name="troubleshoot-the-password-reset-registration-portal"></a>Felsöka registrerings portalen för lösen ords återställning

| Fel | Lösning |
| --- | --- |
| Katalogen är inte aktive rad för lösen ords återställning. **Administratören har inte aktiverat dig för att använda den här funktionen.** | Aktivera flaggan för **återställning av lösen ord** med självbetjäning till **markerad** eller **alla** och välj sedan **Spara**. |
| Användaren har ingen tilldelad Azure AD-licens. **Administratören har inte aktiverat dig för att använda den här funktionen.** | Detta kan inträffa om du inte har en Azure AD-licens som tilldelats administratören att utföra åtgärden. <br> <br> Du kan lösa problemet genom att tilldela en licens till administratörs kontot i fråga. Följ stegen i artikeln [tilldela, verifiera och lösa problem med licenser](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) .|
| Ett fel uppstod när begäran bearbetades. | Detta kan orsakas av många problem, men vanligt vis orsakas felet av ett avbrott i tjänsten eller ett konfigurations problem. Om du ser det här felet och det påverkar din verksamhet kontaktar du Microsoft Support för ytterligare hjälp. |

## <a name="troubleshoot-the-password-reset-portal"></a>Felsöka portalen för återställning av lösen ord

| Fel | Lösning |
| --- | --- |
| Katalogen är inte aktive rad för lösen ords återställning. | Aktivera flaggan för **återställning av lösen ord** med självbetjäning till **markerad** eller **alla** och välj sedan **Spara**. |
| Användaren har ingen tilldelad Azure AD-licens. | Detta kan inträffa om du inte har en Azure AD-licens som tilldelats administratören att utföra åtgärden. <br> <br> Du kan lösa det här problemet om du tilldelar en licens till administratörs kontot i fråga. Följ stegen i artikeln [tilldela, verifiera och lösa problem med licenser](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) . |
| Katalogen är aktive rad för lösen ords återställning, men användaren saknar eller har en felaktig autentiseringsinformation. | Innan du fortsätter bör du se till att användaren har rätt kontakt information på filen i katalogen. Mer information finns i [data som används av lösen ords återställning via](howto-sspr-authenticationdata.md)självbetjäning i Azure AD. |
| Katalogen är aktive rad för lösen ords återställning, men användaren har bara en kontakt persons information på filen när principen är inställd på att kräva två verifierings metoder. | Innan du fortsätter bör du se till att användaren har minst två korrekt konfigurerade kontakt metoder. Ett exempel är att ha både ett mobiltelefon nummer *och* ett telefonnummer till ett kontor. |
| Katalogen är aktive rad för lösen ords återställning och användaren är korrekt konfigurerad, men användaren kan inte kontaktas. | Detta kan bero på ett temporärt tjänst fel eller om det finns felaktiga kontakt data som inte kan identifieras korrekt. <br> <br> Om användaren väntar 10 sekunder, "försök igen" och "kontakta administratören"-länkarna visas. Om användaren väljer "försök igen" försöker den igen. Om användaren väljer "kontakta administratören" skickar den ett formulär-e-postmeddelande till sina administratörer som begär att en lösen ords återställning ska utföras för det användar kontot. |
| Användaren får aldrig lösen ords återställning av SMS eller telefonsamtal. | Detta kan vara resultatet av ett felformaterat telefonnummer i katalogen. Kontrol lera att telefonnumret är i formatet "+ kopia xxxyyyzzzzXeeee". <br> <br> Lösen ords återställning stöder inte tillägg, även om du anger en i katalogen. Tilläggen rensas innan anropet skickas. Använd ett tal utan tillägg eller integrera tillägget i telefonnumret i ditt PBX (Private Branch Exchange). |
| Användaren får aldrig e-postmeddelandet för lösen ords återställning. | Den vanligaste orsaken till det här problemet är att meddelandet avvisas av ett skräp post filter. Kontrol lera mappen skräp post, skräp post eller borttagna objekt för e-postmeddelandet. <br> <br> Kontrol lera också att du kontrollerar rätt e-postkonto för meddelandet. |
| Jag har angett en princip för lösen ords återställning, men när ett administratörs konto använder lösen ords återställning tillämpas inte principen. | Microsoft hanterar och styr principen för återställning av lösen ord för att säkerställa den högsta säkerhets nivån. |
| Användaren nekas ett försök att återställa lösen ordet för många gånger under en dag. | Vi implementerar en funktion för automatisk begränsning för att hindra användare från att försöka återställa sina lösen ord för många gånger under en kort tids period. Begränsning sker när: <br><ul><li>Användaren försöker verifiera ett telefonnummer fem gånger om en timme.</li><li>Användaren försöker använda säkerhets frågornas grind fem gånger om en timme.</li><li>Användaren försöker återställa ett lösen ord för samma användar konto fem gånger om en timme.</li></ul>Du kan åtgärda det här problemet genom att instruera användaren att vänta 24 timmar efter det senaste försöket. Användaren kan sedan återställa sitt lösen ord. |
| Användaren ser ett fel när de verifierar sitt telefonnummer. | Felet uppstår när det angivna telefonnumret inte matchar telefonnumret i filen. Se till att användaren anger hela telefonnumret, inklusive rikt nummer och landskod, när de försöker använda en telefonbaserad metod för lösen ords återställning. |
| Ett fel uppstod när begäran bearbetades. | Detta kan orsakas av många problem, men vanligt vis orsakas felet av ett avbrott i tjänsten eller ett konfigurations problem. Om du ser det här felet och det påverkar din verksamhet kontaktar du Microsoft Support för ytterligare hjälp. |
| Överträdelse av lokal princip | Lösen ordet uppfyller inte den lokala Active Directory lösen ords principen. |
| Lösen ordet överensstämmer inte med en Fuzzy-princip | Det lösen ord som användes visas i [listan över blockerade lösen ord](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad#how-are-passwords-evaluated) och får inte användas. |

## <a name="troubleshoot-password-writeback"></a>Felsöka tillbakaskrivning av lösen ord

| Fel | Lösning |
| --- | --- |
| Tjänsten för återställning av lösen ord startar inte lokalt. Fel 6800 visas i händelse loggen för Azure AD Connect datorns program. <br> <br> Efter onboarding, federerad autentisering, direktautentisering eller lösen ords-hash-synkroniserade användare kan inte återställa sina lösen ord. | När tillbakaskrivning av lösen ord är aktive rad anropar Synkroniseringsmotorn tillbakaskrivning för att utföra konfigurationen (onboarding) genom att kommunicera med Cloud onboarding-tjänsten. Eventuella fel som påträffas vid onboarding eller vid start av Windows Communication Foundation (WCF) för att tillbakaskrivning av lösen ord resulterar i fel i händelse loggen på din Azure AD Connect dator. <br> <br> Under omstarten av Azure AD Sync (ADSync)-tjänsten, om tillbakaskrivning har kon figurer ATS, startar WCF-slutpunkten. Men om det inte går att starta slut punkten loggar vi in händelse 6800 och låter synkroniseringstjänsten starta. Förekomsten av den här händelsen innebär att slut punkten för tillbakaskrivning av lösen ord inte startades. Händelse logg information för den här händelsen 6800, tillsammans med händelse logg poster som genereras av PasswordResetService-komponenten, anger varför du inte kan starta slut punkten. Granska de här händelse logg felen och försök att starta om Azure AD Connect om tillbakaskrivning av lösen ord fortfarande inte fungerar. Om problemet kvarstår kan du försöka inaktivera och sedan återaktivera tillbakaskrivning av lösen ord.
| När en användare försöker återställa ett lösen ord eller låsa upp ett konto med tillbakaskrivning av lösen ord aktiverat, Miss lyckas åtgärden. <br> <br> Dessutom visas en händelse i Azure AD Connect händelse loggen som innehåller: "Synkroniseringsmotorn returnerade ett fel HR = 800700CE, meddelande = fil namn eller fil namns tillägg är för långt" efter upplåsnings åtgärden. | Hitta Active Directory kontot för Azure AD Connect och Återställ lösen ordet så att det inte innehåller fler än 256 tecken. Öppna sedan **synkroniseringstjänsten** från **Start** -menyn. Bläddra till **anslutningar** och hitta **Active Directory-anslutningen**. Markera den och välj sedan **Egenskaper**. Bläddra till sidan **autentiseringsuppgifter** och ange det nya lösen ordet. Välj **OK** för att stänga sidan. |
| I det sista steget i Azure AD Connect installationen visas ett fel som anger att tillbakaskrivning av lösen ord inte kunde konfigureras. <br> <br> Händelse loggen för Azure AD Connect program innehåller fel 32009 med texten "Det gick inte att hämta auth-token". | Det här felet uppstår i följande två fall: <br><ul><li>Du har angett ett felaktigt lösen ord för det globala administratörs kontot som anges i början av installations processen för Azure AD Connect.</li><li>Du försökte använda en federerad användare för det globala administratörs kontot som angavs i början av installations processen för Azure AD Connect.</li></ul> Se till att du inte använder ett federerat konto för den globala administratör som du angav i början av installations processen för att åtgärda problemet. Kontrol lera också att det angivna lösen ordet är korrekt. |
| Händelse loggen för Azure AD Connect datorn innehåller fel 32002 som genereras genom att PasswordResetService körs. <br> <br> Felet läser: "Det gick inte att ansluta till Service Bus. Token-providern kunde inte tillhandahålla en säkerhetstoken. " | Din lokala miljö kan inte ansluta till Azure Service Bus slut punkten i molnet. Det här felet orsakas vanligt vis av en brand Väggs regel som blockerar en utgående anslutning till en viss port eller webb adress. Mer information finns i [krav för anslutning](../hybrid/how-to-connect-install-prerequisites.md) . När du har uppdaterat reglerna ska du starta om den Azure AD Connect datorn och lösen ordet för tillbakaskrivning ska börja fungera igen. |
| Efter en viss tid kan federerade, direktautentisering, direktautentisering eller lösen ords-hash-synkroniserade användare inte återställa sina lösen ord. | I vissa sällsynta fall går det inte att starta om tjänsten för tillbakaskrivning av lösen ord när Azure AD Connect har startats om. I dessa fall ska du först kontrol lera om tillbakaskrivning av lösen ord verkar vara aktive rad lokalt. Du kan kontrol lera genom att använda antingen Azure AD Connect guiden eller PowerShell (se föregående HowTos-avsnitt). Om funktionen verkar vara aktive rad kan du prova att aktivera eller inaktivera funktionen igen, antingen via användar gränssnittet eller PowerShell. Om detta inte fungerar kan du prova att avinstallera och installera om Azure AD Connect. |
| Federerad autentisering, direktautentisering eller lösen ords-hash-synkroniserade användare som försöker återställa sina lösen ord visas ett fel meddelande när du försöker skicka in lösen ordet. Felet indikerar att det uppstod ett tjänst problem. <br ><br> Förutom det här problemet kan du vid lösen ords återställnings åtgärder se ett fel meddelande om att hanterings agenten nekades åtkomst i dina lokala händelse loggar. | Om du ser dessa fel i händelse loggen kontrollerar du att kontot för Active Directory hanterings agenten (ADMA) som angavs i guiden vid konfigurationen har nödvändig behörighet för tillbakaskrivning av lösen ord. <br> <br> När den här behörigheten har angetts kan det ta upp till en timme innan behörigheten att trickle nedåt via `sdprop` bakgrunds aktiviteten på domänkontrollanten (DC). <br> <br> För att lösen ords återställning ska fungera måste behörigheten stämplas på säkerhets beskrivningen för det användar objekt vars lösen ord ska återställas. Tills den här behörigheten visas för objektet användare fortsätter lösen ords återställning att fungera med ett meddelande om nekad åtkomst. |
| Federerad autentisering, direktautentisering eller lösen ords-hash-synkroniserade användare som försöker återställa sina lösen ord, se ett fel när de har skickat sitt lösen ord. Felet indikerar att det uppstod ett tjänst problem. <br> <br> Förutom det här problemet kan du vid lösen ords återställnings åtgärder se ett fel i händelse loggarna från tjänsten Azure AD Connect som indikerar att det inte gick att hitta objektet "Det gick inte att hitta objektet". | Det här felet indikerar vanligt vis att Synkroniseringsmotorn inte kan hitta något User-objekt i Azure AD Connector-utrymmet eller det länkade metaversum (MV) eller Azure AD Connector Space-objektet. <br> <br> Om du vill felsöka det här problemet kontrollerar du att användaren verkligen synkroniseras från den lokala platsen till Azure AD via den aktuella instansen av Azure AD Connect och kontrollerar objektens status i kopplings utrymmen och MV. Bekräfta att objektet Active Directory Certificate Services (AD CS) är anslutet till MV-objektet via regeln "Microsoft.InfromADUserAccountEnabled.xxx".|
| Federerad autentisering, direktautentisering eller lösen ords-hash-synkroniserade användare som försöker återställa sina lösen ord visas ett fel meddelande när de skickar sitt lösen ord. Felet indikerar att det uppstod ett tjänst problem. <br> <br> Förutom det här problemet kan du vid lösen ords återställnings åtgärder se ett fel i händelse loggarna från tjänsten Azure AD Connect som indikerar att det finns ett fel av den identifierade flera matchningar. | Detta anger att Synkroniseringsmotorn har identifierat att MV-objektet är anslutet till fler än ett AD CS-objekt via "Microsoft.InfromADUserAccountEnabled.xxx". Det innebär att användaren har ett aktiverat konto i fler än en skog. Det här scenariot stöds inte för tillbakaskrivning av lösen ord. |
| Lösen ords åtgärder fungerar inte med ett konfigurations fel. Program händelse loggen innehåller Azure AD Connect fel 6329 med texten "0x8023061f (åtgärden misslyckades eftersom Lösenordssynkronisering inte har Aktiver ATS för den här hanterings agenten)". | Det här felet uppstår om Azure AD Connect-konfigurationen ändras för att lägga till en ny Active Directory skog (eller för att ta bort och läsa en befintlig skog) när funktionen för tillbakaskrivning av lösen ord redan har Aktiver ATS. Lösen ords åtgärder för användare i dessa nyligen tillagda skogar fungerar inte. Du löser problemet genom att inaktivera och sedan återaktivera funktionen för tillbakaskrivning av lösen ord när skogens konfigurations ändringar har slutförts. |

## <a name="password-writeback-event-log-error-codes"></a>Händelse i tillbakaskrivning av lösen ord-logg fel koder

Ett bra tips när du felsöker problem med tillbakaskrivning av lösen ord är att granska program händelse loggen på din Azure AD Connect dator. Den här händelse loggen innehåller händelser från två källor av intresse för tillbakaskrivning av lösen ord. PasswordResetService-källan beskriver åtgärder och problem som rör åtgärden för tillbakaskrivning av lösen ord. ADSync-källan beskriver åtgärder och problem som rör inställning av lösen ord i din Active Directorys miljö.

### <a name="if-the-source-of-the-event-is-adsync"></a>Om händelsens källa är ADSync

| Kod | Namn eller meddelande | Beskrivning |
| --- | --- | --- |
| 6329 | BAIL: MMS(4924) 0x80230619: "En begränsning förhindrar att lösen ordet ändras till den aktuella som anges." | Den här händelsen inträffar när tjänsten för tillbakaskrivning av lösen ord försöker att ange ett lösen ord för din lokala katalog som inte uppfyller lösen ordets ålder, historik, komplexitet eller filtrerings krav för domänen. <br> <br> Om du har en lägsta ålder för lösen ord och nyligen har ändrat lösen ordet inom tids perioden kan du inte ändra lösen ordet igen förrän det når den angivna åldern i din domän. För test ändamål ska den lägsta åldern anges till 0. <br> <br> Om du har krav på lösen ords historik aktive rad måste du välja ett lösen ord som inte har använts under de senaste *N* tiderna, där *n* är inställningen för lösen ords historik. Om du väljer ett lösen ord som har använts under de senaste *N* tiderna visas ett problem i det här fallet. I test syfte ska lösen ords historiken anges till 0. <br> <br> Om du har krav på lösen ords komplexitet tillämpas alla dem när användaren försöker ändra eller återställa ett lösen ord. <br> <br> Om du har aktiverat lösen ords filter och en användare väljer ett lösen ord som inte uppfyller filtrerings villkoren, Miss lyckas återställnings-eller ändrings åtgärden. |
| 6329 | MMS (3040): admaexport. cpp (2837): Servern innehåller inte princip kontrollen för LDAP-lösenord. | Det här problemet uppstår om LDAP_SERVER_POLICY_HINTS_OID Control (1.2.840.113556.1.4.2066) inte är aktiverat på domänkontrollanten. Om du vill använda funktionen för tillbakaskrivning av lösen ord måste du aktivera kontrollen. För att göra det måste DCs vara på Windows Server 2008R2 eller senare. |
| HR 8023042 | Synkroniseringsmotorn returnerade felet HR = 80230402, Message = ett försök att hämta ett objekt misslyckades eftersom det finns dubbletter av poster med samma ankare. | Felet uppstår när samma användar-ID är aktiverat i flera domäner. Ett exempel är om du synkroniserar konto-och resurs skogar och har samma användar-ID som finns och har Aktiver ATS i varje skog. <br> <br> Det här felet kan också inträffa om du använder ett icke-unikt Anchor-attribut, t. ex. ett alias eller UPN, och två användare delar samma Anchor-attribut. <br> <br> Lös problemet genom att se till att du inte har några duplicerade användare i dina domäner och att du använder ett unikt Anchor-attribut för varje användare. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Om händelsens källa är PasswordResetService

| Kod | Namn eller meddelande | Beskrivning |
| --- | --- | --- |
| 31001 | PasswordResetStart | Den här händelsen indikerar att den lokala tjänsten har identifierat en begäran om lösen ords återställning för en federerad, direktautentisering eller en lösenordsskyddad användare som kommer från molnet. Den här händelsen är den första händelsen vid varje tillbakaskrivning av lösen ords återställning. |
| 31002 | PasswordResetSuccess | Den här händelsen anger att en användare har valt ett nytt lösen ord under en åtgärd för lösen ords återställning. Vi har fastställt att det här lösen ordet uppfyller företagets lösen ords krav. Lösen ordet har skrivits tillbaka till den lokala Active Directorys miljön. |
| 31003 | PasswordResetFail | Den här händelsen indikerar att en användare har valt ett lösen ord och att lösen ordet har anlänt till den lokala miljön. Men när vi försökte ange lösen ordet i den lokala Active Directorys miljön uppstod ett fel. Felet kan bero på flera orsaker: <br><ul><li>Användarens lösen ord uppfyller inte kraven på ålder, historik, komplexitet eller filter för domänen. Lös problemet genom att skapa ett nytt lösen ord.</li><li>ADMA-tjänstkontot har inte rätt behörighet för att ange det nya lösen ordet för det aktuella användar kontot.</li><li>Användarens konto finns i en skyddad grupp, till exempel domän eller företags administratörs grupp, som inte tillåter lösen ords uppsättnings åtgärder.</li></ul>|
| 31004 | OnboardingEventStart | Den här händelsen inträffar om du aktiverar tillbakaskrivning av lösen ord med Azure AD Connect och vi har börjat publicera din organisation till webb tjänsten för tillbakaskrivning av lösen ord. |
| 31005 | OnboardingEventSuccess | Den här händelsen anger att onboarding-processen lyckades och att funktionen för tillbakaskrivning av lösen ord är klar att använda. |
| 31006 | ChangePasswordStart | Den här händelsen indikerar att den lokala tjänsten har identifierat en begäran om lösen ords ändring för en federerad, direktautentisering eller en Password-hash-synkroniserad användare som kommer från molnet. Den här händelsen är den första händelsen vid varje tillbakaskrivning av lösen ord. |
| 31007 | ChangePasswordSuccess | Den här händelsen indikerar att en användare har valt ett nytt lösen ord under en ändring av lösen ord, vi har fastställt att lösen ordet uppfyller företagets lösen ords krav och att lösen ordet har skrivits tillbaka till den lokala Active Directorys miljön. |
| 31008 | ChangePasswordFail | Den här händelsen indikerar att en användare har valt ett lösen ord och att lösen ordet har anlänt till den lokala miljön, men när vi försökte ange lösen ordet i den lokala Active Directorys miljön har ett fel uppstått. Felet kan bero på flera orsaker: <br><ul><li>Användarens lösen ord uppfyller inte kraven på ålder, historik, komplexitet eller filter för domänen. Lös problemet genom att skapa ett nytt lösen ord.</li><li>ADMA-tjänstkontot har inte rätt behörighet för att ange det nya lösen ordet för det aktuella användar kontot.</li><li>Användarens konto finns i en skyddad grupp, t. ex. domän-eller företags administratörer, som inte tillåter lösen ords uppsättnings åtgärder.</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | Den lokala tjänsten har identifierat en begäran om lösen ords återställning för en federerad, direktautentisering eller lösen ords-hash-synkroniserad användare från administratören för en användares räkning. Den här händelsen är den första händelsen vid varje tillbakaskrivning av lösen ords återställning som initieras av en administratör. |
| 31010 | ResetUserPasswordByAdminSuccess | Administratören har valt ett nytt lösen ord under en administratörs åtgärd som initierats för lösen ords återställning. Vi har fastställt att det här lösen ordet uppfyller företagets lösen ords krav. Lösen ordet har skrivits tillbaka till den lokala Active Directorys miljön. |
| 31011 | ResetUserPasswordByAdminFail | Administratören har valt ett lösen ord för en användares räkning. Lösen ordet har anlänt till den lokala miljön. Men när vi försökte ange lösen ordet i den lokala Active Directorys miljön uppstod ett fel. Felet kan bero på flera orsaker: <br><ul><li>Användarens lösen ord uppfyller inte kraven på ålder, historik, komplexitet eller filter för domänen. Försök med ett nytt lösen ord för att lösa problemet.</li><li>ADMA-tjänstkontot har inte rätt behörighet för att ange det nya lösen ordet för det aktuella användar kontot.</li><li>Användarens konto finns i en skyddad grupp, t. ex. domän-eller företags administratörer, som inte tillåter lösen ords uppsättnings åtgärder.</li></ul>  |
| 31012 | OffboardingEventStart | Den här händelsen inträffar om du inaktiverar tillbakaskrivning av lösen ord med Azure AD Connect och indikerar att vi startade offboarding din organisation till webb tjänsten för tillbakaskrivning av lösen ord. |
| 31013| OffboardingEventSuccess| Den här händelsen anger att offboarding-processen lyckades och att funktionen för tillbakaskrivning av lösen ord har inaktiverats. |
| 31014| OffboardingEventFail| Den här händelsen anger att offboarding-processen inte lyckades. Detta kan bero på ett behörighets fel i molnet eller det lokala administratörs kontot som angavs under konfigurationen. Felet kan också inträffa om du försöker använda en global administratör för federerade moln när du inaktiverar tillbakaskrivning av lösen ord. Du kan åtgärda det här problemet genom att kontrol lera dina administrativa behörigheter och se till att du inte använder ett federerat konto när du konfigurerar funktionen för tillbakaskrivning av lösen ord.|
| 31015| WriteBackServiceStarted| Den här händelsen anger att tjänsten för tillbakaskrivning av lösen ord har startats. Det är dags att ta emot förfrågningar om lösen ords hantering från molnet.|
| 31016| WriteBackServiceStopped| Den här händelsen anger att tjänsten för tillbakaskrivning av lösen ord har stoppats. Eventuella förfrågningar om lösen ords hantering från molnet kommer inte att lyckas.|
| 31017| AuthTokenSuccess| Den här händelsen indikerar att vi har hämtat en autentiseringstoken för den globala administratör som anges under Azure AD Connect-installationen för att starta offboarding eller onboarding-processen.|
| 31018| KeyPairCreationSuccess| Den här händelsen indikerar att vi har skapat lösen ords krypterings nyckeln. Den här nyckeln används för att kryptera lösen ord från molnet som ska skickas till din lokala miljö.|
| 32000| UnknownError| Den här händelsen anger att ett okänt fel inträffade under en lösen ords hanterings åtgärd. Titta på undantags texten i händelsen om du vill ha mer information. Om du har problem kan du prova med att inaktivera och sedan återaktivera tillbakaskrivning av lösen ord. Om detta inte hjälper inkluderar du en kopia av din händelse logg tillsammans med det spårnings-ID som har angetts Insider till support teknikern.|
| 32001| ServiceError| Den här händelsen indikerar ett fel vid anslutning till tjänsten för lösen ords återställning för moln. Det här felet uppstår vanligt vis när den lokala tjänsten inte kunde ansluta till webb tjänsten för lösen ords återställning.|
| 32002| ServiceBusError| Den här händelsen indikerar att ett fel uppstod vid anslutning till klientens Service Bus instans. Detta kan inträffa om du blockerar utgående anslutningar i din lokala miljö. Kontrol lera brand väggen för att se till att du tillåter anslutningar över TCP https://ssprsbprodncu-sb.accesscontrol.windows.net/ 443 och till och försök sedan igen. Om du fortfarande har problem kan du prova med att inaktivera och sedan återaktivera tillbakaskrivning av lösen ord.|
| 32003| InPutValidationError| Den här händelsen anger att inloggen som skickades till API: et för webb tjänsten var ogiltig. Försök utföra åtgärden igen.|
| 32004| DecryptionError| Den här händelsen indikerar att det uppstod ett fel vid dekryptering av lösen ordet som kom från molnet. Detta kan bero på ett matchnings fel mellan moln tjänsten och den lokala miljön. Lös problemet genom att inaktivera och sedan återaktivera tillbakaskrivning av lösen ord i din lokala miljö.|
| 32005| ConfigurationError| Under onboarding sparar vi klient information i en konfigurations fil i din lokala miljö. Den här händelsen indikerar att det uppstod ett fel när filen skulle sparas eller att när tjänsten startades, uppstod ett fel vid läsning av filen. Åtgärda problemet genom att inaktivera och sedan återaktivera tillbakaskrivning av lösen ord för att framtvinga en omskrivning av konfigurations filen.|
| 32007| OnBoardingConfigUpdateError| Under onboarding skickar vi data från molnet till den lokala lösen ords återställnings tjänsten. Dessa data skrivs sedan till en minnes intern fil innan den skickas till Sync-tjänsten för att lagras på ett säkert sätt på disk. Den här händelsen indikerar att det finns ett problem med att skriva eller uppdatera data i minnet. Åtgärda problemet genom att inaktivera och sedan återaktivera tillbakaskrivning av lösen ord för att framtvinga en omskrivning av konfigurations filen.|
| 32008| ValidationError| Den här händelsen indikerar att vi tog emot ett ogiltigt svar från webb tjänsten för lösen ords återställning. Försök med att inaktivera och sedan återaktivera tillbakaskrivning av lösen ord för att åtgärda problemet.|
| 32009| AuthTokenError| Den här händelsen indikerar att det inte gick att hämta en autentiseringstoken för det globala administratörs kontot som angavs under installationen av Azure AD Connect. Felet kan bero på ett felaktigt användar namn eller lösen ord som angetts för det globala administratörs kontot. Det här felet kan också inträffa om det globala administratörs kontot som angetts är federerad. Åtgärda problemet genom att köra konfigurationen igen med rätt användar namn och lösen ord och se till att administratören är ett hanterat (enbart molnbaserad eller lösen ords synkroniserat) konto.|
| 32010| CryptoError| Den här händelsen indikerar att det uppstod ett fel när lösen ords krypterings nyckeln skulle genereras eller dekryptering av ett lösen ord som kommer från moln tjänsten. Det här felet tyder troligt vis på ett problem med din miljö. Titta närmare på informationen om händelse loggen om du vill veta mer om hur du löser det här problemet. Du kan också prova att inaktivera och sedan återaktivera tjänsten för tillbakaskrivning av lösen ord.|
| 32011| OnBoardingServiceError| Den här händelsen indikerar att den lokala tjänsten inte kunde kommunicera med webb tjänsten för lösen ords återställning för att initiera onboarding-processen. Detta kan inträffa till följd av en brand Väggs regel eller om det är problem med att hämta en autentiseringstoken för din klient. Se till att du inte blockerar utgående anslutningar via TCP 443 och TCP 9350-9354 eller till https://ssprsbprodncu-sb.accesscontrol.windows.net/ för att åtgärda det här problemet. Se också till att det Azure AD-administratörskonto som du använder för att publicera inte är federerad.|
| 32013| OffBoardingError| Den här händelsen indikerar att den lokala tjänsten inte kunde kommunicera med webb tjänsten för lösen ords återställning för att initiera offboarding-processen. Detta kan inträffa till följd av en brand Väggs regel eller om det är problem med att hämta en autentiseringstoken för din klient. Du kan åtgärda det här problemet genom att se till att du inte blockerar utgående anslutningar över https://ssprsbprodncu-sb.accesscontrol.windows.net/ 443 eller till, och att det Azure Active Directory administratörs konto som du använder till avpublicera inte är federerad.|
| 32014| ServiceBusWarning| Den här händelsen indikerar att vi måste försöka ansluta till klient organisationens Service Bus instans igen. Under normala förhållanden bör detta inte vara ett problem, men om du ser den här händelsen flera gånger bör du överväga att kontrol lera nätverks anslutningen till Service Bus, särskilt om det är en anslutning med hög latens eller låg bandbredd.|
| 32015| ReportServiceHealthError| För att övervaka hälso tillståndet för tillbakaskrivning av lösen ord skickar vi heartbeat-data till vår webb tjänst för lösen ords återställning var femte minut. Den här händelsen indikerar att det uppstod ett fel när den här hälso informationen skickades tillbaka till moln webb tjänsten. Den här hälso informationen omfattar inte en objekt identifierbar information (OII) eller personligt identifierbar information (PII) och är enbart en pulsslags-och grundläggande tjänst statistik så att vi kan tillhandahålla tjänst status information i molnet.|
| 33001| ADUnKnownError| Den här händelsen indikerar att ett okänt fel returnerades av Active Directory. Mer information finns i händelse loggen för Azure AD Connect Server för händelser från ADSync-källan.|
| 33002| ADUserNotFoundError| Den här händelsen indikerar att användaren som försöker återställa eller ändra ett lösen ord inte hittades i den lokala katalogen. Det här felet kan inträffa när användaren har tagits bort lokalt men inte i molnet. Det här felet kan också inträffa om det är problem med synkroniseringen. Kontrol lera dina synkroniserade loggar och de senaste körnings körnings uppgifterna för mer information.|
| 33003| ADMutliMatchError| När en lösen ords återställning eller ändringsbegäran härstammar från molnet, använder vi moln ankare som angavs under installationen av Azure AD Connect för att fastställa hur du länkar den begär Anden tillbaka till en användare i din lokala miljö. Den här händelsen indikerar att vi hittade två användare i din lokala katalog med samma moln fäst punkt-attribut. Kontrol lera dina synkroniserade loggar och de senaste körnings körnings uppgifterna för mer information.|
| 33004| ADPermissionsError| Den här händelsen anger att kontot för Active Directory hanterings agenten (ADMA) inte har rätt behörighet för kontot i fråga för att ange ett nytt lösen ord. Se till att ADMA-kontot i användarens skog har återställt och ändra lösen ords behörigheter för alla objekt i skogen. Mer information om hur du anger behörigheter finns i steg 4: Konfigurera lämpliga Active Directory behörigheter.|
| 33005| ADUserAccountDisabled| Den här händelsen indikerar att vi försökte återställa eller ändra ett lösen ord för ett konto som har inaktiverats lokalt. Aktivera kontot och försök sedan igen.|
| 33006| ADUserAccountLockedOut| Den här händelsen indikerar att vi försökte återställa eller ändra ett lösen ord för ett konto som låstes lokalt. Utelåsning kan uppstå när en användare har försökt att ändra eller återställa lösen ord för många gånger under en kort period. Lås upp kontot och försök sedan igen.|
| 33007| ADUserIncorrectPassword| Den här händelsen anger att användaren har angett ett felaktigt nuvarande lösen ord när en lösen ords ändring utförs. Ange korrekt nuvarande lösen ord och försök igen.|
| 33008| ADPasswordPolicyError| Den här händelsen inträffar när tjänsten för tillbakaskrivning av lösen ord försöker att ange ett lösen ord för din lokala katalog som inte uppfyller lösen ordets ålder, historik, komplexitet eller filtrerings krav för domänen. <br> <br> Om du har en lägsta ålder för lösen ord och nyligen har ändrat lösen ordet inom tids perioden kan du inte ändra lösen ordet igen förrän det når den angivna åldern i din domän. För test ändamål ska den lägsta åldern anges till 0. <br> <br> Om du har krav på lösen ords historik aktive rad måste du välja ett lösen ord som inte har använts under de senaste *N* tiderna, där *n* är inställningen för lösen ords historik. Om du väljer ett lösen ord som har använts under de senaste *N* tiderna visas ett problem i det här fallet. I test syfte ska lösen ords historiken anges till 0. <br> <br> Om du har krav på lösen ords komplexitet tillämpas alla dem när användaren försöker ändra eller återställa ett lösen ord. <br> <br> Om du har aktiverat lösen ords filter och en användare väljer ett lösen ord som inte uppfyller filtrerings villkoren, Miss lyckas återställnings-eller ändrings åtgärden.|
| 33009| ADConfigurationError| Den här händelsen indikerar ett problem med att skriva tillbaka ett lösen ord till din lokala katalog på grund av ett konfigurations problem med Active Directory. Mer information om vilka fel som har inträffat finns i händelse loggen för Azure AD Connect datorns program händelse logg för meddelanden från ADSync-tjänsten.|

## <a name="troubleshoot-password-writeback-connectivity"></a>Felsöka anslutning av tillbakaskrivning av lösen ord

Om du har avbrott i tjänsten med komponenten för tillbakaskrivning av lösen ord i Azure AD Connect kan du lösa problemet genom att följa dessa steg:

* [Bekräfta nätverks anslutningen](#confirm-network-connectivity)
* [Starta om tjänsten Azure AD Connect Sync](#restart-the-azure-ad-connect-sync-service)
* [Inaktivera och återaktivera funktionen för tillbakaskrivning av lösen ord](#disable-and-re-enable-the-password-writeback-feature)
* [Installera den senaste versionen av Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [Felsöka tillbakaskrivning av lösen ord](#troubleshoot-password-writeback)

I allmänhet rekommenderar vi att du utför de här stegen i den ordning som diskuterats tidigare, för att återställa tjänsten på det snabbaste sättet.

### <a name="confirm-network-connectivity"></a>Bekräfta nätverks anslutningen

Den vanligaste fel punkten är att brand Väggs-och eller proxy-portar och tids gränser för inaktivitet är felaktigt konfigurerade. 

För Azure AD Connect version 1.1.443.0 och senare, behöver du utgående HTTPS-åtkomst till följande:

* \*.passwordreset.microsoftonline.com
* \*.servicebus.windows.net

Mer detaljerad information finns i den uppdaterade listan med [Microsoft Azure Data Center IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653) uppdateras varje onsdag och börjar gälla nästa måndag.

Mer information finns i krav för anslutning i krav [för Azure AD Connect](../hybrid/how-to-connect-install-prerequisites.md) artikel.

### <a name="restart-the-azure-ad-connect-sync-service"></a>Starta om tjänsten Azure AD Connect Sync

Starta om tjänsten Azure AD Connect Sync för att lösa anslutnings problem eller andra tillfälliga problem med tjänsten:

1. Som administratör väljer du **Starta** på den server som kör Azure AD Connect.
1. Ange **Services. msc** i Sök fältet och välj **RETUR**.
1. Sök efter posten för **Microsoft Azure AD-synkronisering** .
1. Högerklicka på tjänst posten, Välj **starta om**och vänta tills åtgärden har slutförts.

   ![Starta om Azure AD Sync tjänsten med hjälp av det grafiska användar gränssnittet][Service restart]

De här stegen återupprättar anslutningen till moln tjänsten och löser eventuella avbrott som kan uppstå. Om du inte kan lösa problemet genom att starta om ADSync-tjänsten rekommenderar vi att du försöker inaktivera och sedan återaktivera funktionen för tillbakaskrivning av lösen ord.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Inaktivera och återaktivera funktionen för tillbakaskrivning av lösen ord

För att lösa anslutnings problem inaktiverar du och aktiverar sedan funktionen för tillbakaskrivning av lösen ord på nytt:

   1. Som administratör öppnar du guiden Azure AD Connect konfiguration.
   1. I **Anslut till Azure AD**anger du dina autentiseringsuppgifter som global administratör för Azure AD.
   1. I **Anslut till AD DS**anger du dina AUTENTISERINGSUPPGIFTER för AD Domain Services-administratören.
   1. Välj **Nästa** -knappen i **identifiera dina användare unikt**.
   1. I **valfria funktioner**avmarkerar du kryss rutan **tillbakaskrivning av lösen ord** .
   1. Välj **Nästa** genom de återstående dialog sidorna utan att ändra något förrän du kommer till sidan **klart att konfigurera** .
   1. Kontrol lera att alternativet för **att konfigurera** lösen ord visar alternativet för **tillbakaskrivning av lösen ord** som inaktiverat och välj sedan knappen för den gröna **konfigurationen** för att spara ändringarna.
   1. I **klart**avmarkerar du alternativet **Synkronisera nu** och väljer sedan **Slutför** för att stänga guiden.
   1. Öppna konfigurations guiden för Azure AD Connect.
   1. Upprepa steg 2-8, förutom att se till att du väljer alternativet för **tillbakaskrivning av lösen ord** på sidan **valfria funktioner** för att återaktivera tjänsten.

Med de här stegen återupprättar du anslutningen till vår moln tjänst och löser eventuella avbrott som du kan råka ut för.

Om du inaktiverar och sedan aktiverar funktionen tillbakaskrivning av lösen ord igen, rekommenderar vi att du installerar om Azure AD Connect.

### <a name="install-the-latest-azure-ad-connect-release"></a>Installera den senaste versionen av Azure AD Connect

Att installera om Azure AD Connect kan lösa konfigurations-och anslutnings problem mellan våra moln tjänster och din lokala Active Directory miljö.

Vi rekommenderar att du bara genomför det här steget efter att du har försökt med de två första stegen som beskrivs ovan.

> [!WARNING]
> Om du har anpassat de färdiga Sync-reglerna kan du *säkerhetskopiera dem innan du fortsätter med uppgraderingen och sedan distribuera dem manuellt när du är klar.*

1. Hämta den senaste versionen av Azure AD Connect från [Microsoft Download Center](https://go.microsoft.com/fwlink/?LinkId=615771).
1. Eftersom du redan har installerat Azure AD Connect måste du utföra en uppgradering på plats för att uppdatera Azure AD Connect-installationen till den senaste versionen.
1. Kör det hämtade paketet och följ anvisningarna på skärmen för att uppdatera din Azure AD Connect dator.

Föregående steg bör återupprätta anslutningen till vår moln tjänst och lösa eventuella avbrott som kan uppstå.

Om du inte kan lösa problemet genom att installera den senaste versionen av Azure AD Connect-servern rekommenderar vi att du försöker inaktivera och sedan återaktivera tillbakaskrivning av lösen ord som ett sista steg efter att du har installerat den senaste versionen.

## <a name="verify-that-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>Kontrol lera att Azure AD Connect har behörighet för tillbakaskrivning av lösen ord

Azure AD Connect måste Active Directory **återställa lösen** ordet för att utföra tillbakaskrivning av lösen ord. Om du vill ta reda på om Azure AD Connect har behörighet för ett angivet lokalt Active Directory användar konto kan du använda funktionen Windows gällande behörighet:

1. Logga in på Azure AD Connect-servern och starta **Synchronization Service Manager** genom att välja **Starta** > **synkroniseringstjänst**.
1. På fliken **anslutningar** väljer du den lokala **Active Directory Domain Services** anslutningen och väljer sedan **Egenskaper**.  
   ![Synchronization Service Manager som visar hur du redigerar egenskaper](./media/active-directory-passwords-troubleshoot/checkpermission01.png)  
  
1. I popup-fönstret väljer du **Anslut till Active Directory skog** och anteckna egenskapen **användar namn** . Den här egenskapen är det AD DS-konto som används av Azure AD Connect för att utföra en katalog synkronisering. För att Azure AD Connect ska kunna utföra tillbakaskrivning av lösen ord måste AD DS-kontot ha behörighet att återställa lösen ord.  

   ![Hitta synkroniseringstjänsten Active Directory-användarkonto](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
  
1. Logga in på en lokal domänkontrollant och starta programmet **Active Directory användare och datorer** .
1. Välj **Visa** och se till att alternativet **avancerade funktioner** är aktiverat.  

   ![Active Directory användare och datorer Visa avancerade funktioner](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
  
1. Sök efter det Active Directory användar konto som du vill verifiera. Högerklicka på konto namnet och välj **Egenskaper**.  
1. Gå till fliken **säkerhet** i popup-fönstret och välj **Avancerat**.  
1. I popup-fönstret **avancerade säkerhets inställningar för administratör** går du till fliken **gällande åtkomst** .
1. Välj **Välj en användare**, Välj det AD DS-konto som används av Azure AD Connect (se steg 3) och välj sedan **Visa gällande åtkomst**.

   ![Fliken gällande åtkomst visar synkroniseringsschemat](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
  
1. Rulla nedåt och leta efter **Återställ lösen ord**. Om posten är markerad har AD DS-kontot behörighet att återställa lösen ordet för det valda Active Directory användar kontot.  

   ![Verifierar att Sync-kontot har behörigheten Återställ lösen ord](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Azure AD-forum

Om du har en allmän fråga om Azure AD och lösen ords återställning via självbetjäning kan du be communityn om hjälp i [Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD)-forumen. Medlemmar i communityn är tekniker, produkt chefer, MVP: er och andra IT-proffs.

## <a name="contact-microsoft-support"></a>Kontakta Microsoft-supporten

Om du inte hittar svaret på ett problem är våra support team alltid tillgängliga för att hjälpa dig.

För att du ska kunna hjälpa dig, ber vi dig att ange så mycket information som möjligt när du öppnar ett ärende. Informationen omfattar:

* **Allmän beskrivning av felet**: Vad är felet? Vad var det beteende som har märkts? Hur kan vi återskapa felet? Ange så mycket information som möjligt.
* **Sida**: Vilken sida var du på när du noterade felet? Ta med URL: en om du kan och en skärm bild av sidan.
* **Support kod**: Vad var support koden som genererades när användaren såg felet?
   * Du hittar den här koden genom att återskapa felet och sedan välja länken **support kod** längst ned på skärmen och skicka support teknikern till det GUID som det resulterar i.

   ![Hitta support koden längst ned på skärmen][Support code]

  * Om du är på en sida utan support kod längst ned väljer du F12 och söker efter SID och CID och skickar dessa två resultat till support teknikern.
* **Datum, tid och**tidszon: Inkludera det exakta datumet och tiden *med tids zonen* då felet inträffade.
* **Användar-ID**: Vem var den användare som såg felet? Ett exempel är *User\@contoso.com*.
   * Är detta en federerad användare?
   * Är detta en direkt autentiserings användare?
   * Är detta en lösen ords-hash-synkroniserad användare?
   * Är det här en endast molnbaserad användare?
* **Licensiering**: Har användaren en Azure AD-licens tilldelad?
* **Program händelse logg**: Om du använder tillbakaskrivning av lösen ord och felet finns i den lokala infrastrukturen inkluderar du en zippad kopia av program händelse loggen från Azure AD Connect-servern.

[Service restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "Starta om tjänsten Azure AD Sync"
[Support code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "Support koden finns längst ned till höger i fönstret"

## <a name="next-steps"></a>Nästa steg

Följande artiklar innehåller ytterligare information om lösen ords återställning via Azure AD:

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](howto-sspr-deployment.md)
* [Återställ eller ändra ditt lösenord](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrera för återställning av lösenord för självbetjäning](../user-help/active-directory-passwords-reset-register.md)
* [Har du en fråga om licensiering?](concept-sspr-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](howto-sspr-authenticationdata.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](concept-sspr-howitworks.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](concept-sspr-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](howto-sspr-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](howto-sspr-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](concept-sspr-howitworks.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)
