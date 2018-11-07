---
title: Lösenordsåterställning via självbetjäning felsökning – Azure Active Directory
description: Felsökning Azure AD lösenordsåterställning via självbetjäning
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 044a3bae75cb385e7a3542b920e0cb3b5bcedcd0
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51233634"
---
# <a name="troubleshoot-self-service-password-reset"></a>Felsöka lösenordsåterställning via självbetjäning

Har du problem med lösenordsåterställning för Azure Active Directory (Azure AD) via självbetjäning (SSPR)? Informationen nedan kan hjälpa dig att allt fungerar igen.

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-might-see"></a>Felsök lösenord för självbetjäning återställning av fel som kan visas för en användare

| Fel | Information | Teknisk information |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Vi beklagar, men du inte kan återställa ditt lösenord just nu eftersom din administratör har inaktiverat återställning av lösenord för din organisation. Det finns inga ytterligare åtgärder du kan vidta för att lösa problemet. Kontakta din administratör och be dem att aktivera den här funktionen. Mer information finns i [hjälp, jag har glömt mitt Azure AD-lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions). | SSPR_0009: Vi har upptäckt att lösenordsåterställning inte har aktiverats av administratören. Kontakta din administratör och be dem att aktivera återställning av lösenord för din organisation. |
| WritebackNotEnabled = 10 |Vi beklagar, men du inte kan återställa ditt lösenord just nu eftersom administratören inte har aktiverat en tjänst som är nödvändig för din organisation. Det finns inga ytterligare åtgärder du kan vidta för att lösa problemet. Kontakta din administratör och be dem att kontrollera organisationens konfiguration. Mer information om denna nödvändiga tjänst finns [konfigurera tillbakaskrivning av lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-writeback#configure-password-writeback). | SSPR_0010: Vi har upptäckt att tillbakaskrivning av lösenord inte har aktiverats. Kontakta din administratör och be dem att aktivera tillbakaskrivning av lösenord. |
| SsprNotEnabledInUserPolicy = 11 | Vi beklagar, men du inte kan återställa ditt lösenord just nu eftersom administratören inte har konfigurerat återställning av lösenord för din organisation. Det finns inga ytterligare åtgärder du kan vidta för att lösa problemet. Kontakta administratören och be dem att konfigurera återställning av lösenord. Läs mer om lösenord återställs i [Snabbstart: återställning av lösenord för självbetjäning i Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started). | SSPR_0011: Din organisation har inte definierat en princip för återställning av lösenord. Kontakta din administratör och be dem att definiera en princip för återställning av lösenord. |
| UserNotLicensed = 12 | Vi beklagar, men du inte kan återställa ditt lösenord just nu eftersom nödvändiga licenser saknas i din organisation. Det finns inga ytterligare åtgärder du kan vidta för att lösa problemet. Kontakta administratören och be dem att kontrollera din licenstilldelning. Mer information om licensiering finns [Licensing krav för lösenord för självbetjäning i Azure AD återställa](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing). | SSPR_0012: Din organisation har inte licenserna som krävs för att utföra återställning av lösenord. Kontakta din administratör och be dem att granska licenstilldelningarna. |
| UserNotMemberOfScopedAccessGroup = 13 | Vi beklagar, men du inte kan återställa ditt lösenord just nu eftersom administratören inte har konfigurerat ditt konto om du vill använda för återställning av lösenord. Det finns inga ytterligare åtgärder du kan vidta för att lösa problemet. Kontakta din administratör och be dem för att konfigurera ditt konto för återställning av lösenord. Mer information om kontokonfigurationen för återställning av lösenord finns [distribuera lösenordsåterställning för användare](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices). | SSPR_0012: Du inte är medlem i en grupp som har aktiverats för återställning av lösenord. Kontakta din administratör och be att läggas till i gruppen. |
| UserNotProperlyConfigured = 14 | Vi beklagar, men du inte kan återställa ditt lösenord just nu eftersom det saknas nödvändig information från ditt konto. Det finns inga ytterligare åtgärder du kan vidta för att lösa problemet. Kontaktar du administratören och be dem att återställa lösenordet åt dig. När du har tillgång till ditt konto igen kan behöva du registrera informationen som krävs. Registrera information genom att följa stegen i den [registrera sig för lösenordsåterställning via självbetjäning](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register) artikeln. | SSPR_0014: Ytterligare säkerhetsinformation behövs för att återställa ditt lösenord. Om du vill fortsätta, kontakta administratören och be dem att återställa ditt lösenord. När du har tillgång till ditt konto kan du registrera ytterligare säkerhetsinformation på https://aka.ms/ssprsetup. Din administratör kan att lägga till ytterligare säkerhetsinformation till ditt konto genom att följa stegen i [uppsättningen och Läs autentiseringsdata för återställning av lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-data#set-and-read-authentication-data-using-powershell). |
| OnPremisesAdminActionRequired = 29 | Vi beklagar, men vi inte kan återställa ditt lösenord just nu på grund av ett problem med din organisations lösenordsåterställning av konfigurationen. Det finns inga ytterligare åtgärder du kan vidta för att lösa problemet. Kontakta din administratör och be dem att undersöka. Läs mer om potentiella problem i [felsöka tillbakaskrivning av lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback). | SSPR_0029: Vi kan inte återställa ditt lösenord på grund av ett fel i din lokala konfiguration. Kontakta din administratör och be dem att undersöka. |
| OnPremisesConnectivityError = 30 | Vi beklagar, men vi inte kan återställa ditt lösenord just nu på grund av anslutningsproblem till din organisation. Det finns ingen åtgärd ska vidtas just nu, men problemet kan lösas om du försöker igen senare. Om problemet kvarstår, kontakta din administratör och be dem att undersöka. Mer information om anslutningsproblem finns [Felsök anslutning för tillbakaskrivning av lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity). | SSPR_0030: Vi kan inte återställa lösenordet eftersom anslutningen till din lokala miljö. Kontakta administratören och be dem att undersöka.|


## <a name="troubleshoot-the-password-reset-configuration-in-the-azure-portal"></a>Felsöka konfigurationen på återställning av lösenord i Azure portal

| Fel | Lösning |
| --- | --- |
| Jag ser inte de **lösenordsåterställning** avsnittet under Azure AD i Azure-portalen. | Detta kan inträffa om du inte har en Azure AD Premium eller Basic-licens tilldelad till den administratör som utför åtgärden. <br> <br> Tilldela en licens till administratörskontot i fråga. Du kan följa stegen i den [tilldela kontrollerar och lösa problem med licenser](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) artikeln.|
| Jag ser ett visst konfigurationsalternativ. | Många element i Användargränssnittet är dolt förrän de behövs. Försök att aktivera alla alternativ som du vill se. |
| Jag ser inte de **lokal integration** fliken. | Det här alternativet endast visas om du har laddat ned Azure AD Connect och har konfigurerat tillbakaskrivning av lösenord. Mer information finns i [komma igång med Azure AD Connect genom att använda standardinställningarna](../hybrid/how-to-connect-install-express.md). |

## <a name="troubleshoot-password-reset-reporting"></a>Felsök lösenordsåterställning reporting

| Fel | Lösning |
| --- | --- |
| Jag ser inte alla lösenord av aktivitetstyper i den **Self-Service lösenordshantering** händelsekategori för granskning. | Detta kan inträffa om du inte har en Azure AD Premium eller Basic-licens tilldelad till den administratör som utför åtgärden. <br> <br> Du kan lösa problemet genom att tilldela en licens till administratörskontot i fråga. Följ stegen i den [tilldela kontrollerar och lösa problem med licenser](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) artikeln. |
| Användaren registreringar visar flera gånger. | För närvarande när en användare registrerar logga vi varje del av data som är registrerad som en separat händelse. <br> <br> Om du vill att sammanställa data och större flexibilitet i hur du kan visa den, kan du ladda ned rapporten och öppna data som en pivottabell i Excel.

## <a name="troubleshoot-the-password-reset-registration-portal"></a>Felsöka registreringsportalen för lösenordsåterställning

| Fel | Lösning |
| --- | --- |
| Katalogen har inte aktiverats för återställning av lösenord. **Administratören har inte aktiverat du kan använda den här funktionen.** | Växla den **lösenordsåterställning via självbetjäning har aktiverats** flaggan till **valda** eller **alla** och välj sedan **spara**. |
| Användaren har inte en Azure AD Premium eller Basic-licens. **Administratören har inte aktiverat du kan använda den här funktionen.** | Detta kan inträffa om du inte har en Azure AD Premium eller Basic-licens tilldelad till den administratör som utför åtgärden. <br> <br> Du kan lösa problemet genom att tilldela en licens till administratörskontot i fråga. Följ stegen i den [tilldela kontrollerar och lösa problem med licenser](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) artikeln.|
| Det finns ett fel vid bearbetning av begäran. | Detta kan orsakas av många problem, men vanligtvis det här felet beror på ett tjänstavbrott eller konfigurationsproblem. Om du ser detta fel och den påverkar din verksamhet, kontakta Microsoft support om hjälp. |

## <a name="troubleshoot-the-password-reset-portal"></a>Felsöka portalen för återställning av lösenord

| Fel | Lösning |
| --- | --- |
| Katalogen har inte aktiverats för återställning av lösenord. | Växla den **lösenordsåterställning via självbetjäning har aktiverats** flaggan till **valda** eller **alla** och välj sedan **spara**. |
| Användaren har inte en Azure AD Premium eller Basic-licens. | Detta kan inträffa om du inte har en Azure AD Premium eller Basic-licens tilldelad till den administratör som utför åtgärden. <br> <br> Du kan lösa problemet om du tilldelar en licens till administratörskontot i fråga. Följ stegen i den [tilldela kontrollerar och lösa problem med licenser](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) artikeln. |
| Katalogen har aktiverats för återställning av lösenord, men användaren har autentiseringsinformation saknas eller är felaktig. | Se till att användaren har formaterats korrekt kontaktdata på filen i katalogen innan du fortsätter. Mer information finns i [återställning av Data som används av Azure AD-självbetjäning lösenord](howto-sspr-authenticationdata.md). |
| Katalogen har aktiverats för återställning av lösenord, men användaren har endast en del av kontaktdata på filen när principen är konfigurerad att kräva två verifieringsmetoder. | Kontrollera att användaren har minst två korrekt konfigurerade kontaktmetoder innan du fortsätter. Ett exempel är att ha både ett mobiltelefonnummer *och* ett telefonnummer. |
| Katalogen är aktiverat för återställning av lösenord och användaren har konfigurerats korrekt, men användaren kan inte kontaktas. | Det kan bero på fel i en tillfällig tjänsten eller om det finns felaktiga kontaktdata som vi kan inte korrekt identifiera. <br> <br> Om användaren ska vänta 10 sekunder, ”försök igen” och ”kontakta administratören” länkar visas. Om användaren väljer ”försök igen”, försöker anropet. Om användaren väljer ”Kontakta administratören”, skickar den ett formulär i e-postmeddelande till sina administratörer som begär en lösenordsåterställning ska utföras för det användarkontot. |
| Användaren får aldrig lösenordsåterställning SMS eller telefonsamtal. | Detta kan bero på ett felaktigt telefonnummer i katalogen. Se till att telefonnumret är i formatet ”+ kopia xxxyyyzzzzXeeee”. <br> <br> Återställning av lösenord stöder inte tillägg, även om du anger en i katalogen. Tillägg som tas bort innan anropet har skickats iväg. Använd ett tal utan tillägg eller integrera tillägget i telefonnumret i din privata gren exchange (PBX). |
| Användaren får aldrig lösenordsåterställning av e-postmeddelandet. | Den vanligaste orsaken till problemet är att meddelandet har avvisats av ett skräppostfilter. Kontrollera din skräppost, mappen för skräppost eller borttagna objekt för e-postmeddelandet. <br> <br> Se också till att du kontrollerar rätt e-postkontot för meddelandet. |
| Jag har angett en princip för återställning av lösenord, men när ett administratörskonto använder återställning av lösenord, principen tillämpas inte. | Microsoft hanterar och kontroller princip för att säkerställa högsta säkerhetsnivån för återställning av lösenord. |
| Användaren förhindras från att försöka lösenordsåterställning för många gånger under en dag. | Vi implementera en automatisk begränsning mekanism att blockera användare från att försöka att återställa sina lösenord för många gånger under en kort tidsperiod. Begränsning inträffar när: <br><ul><li>Användaren försöker verifiera ett telefonnummer fem gånger under en timme.</li><li>Användaren försöker använda security frågor gate fem gånger i timmen.</li><li>Användaren försöker att återställa ett lösenord för samma användarkonto fem gånger i timmen.</li></ul>Be användaren att vänta i 24 timmar efter det senaste försöket för att åtgärda problemet. Användaren kan sedan återställa sitt lösenord. |
| Användaren ser ett fel vid validering av sina telefonnummer. | Det här felet uppstår när det telefonnummer som angetts inte matchar telefonnumret för filen. Kontrollera att användaren anger den fullständiga telefonnummer, inklusive koden område och land när de försöker använda en telefonbaserad metod för återställning av lösenord. |
| Det finns ett fel vid bearbetning av begäran. | Detta kan orsakas av många problem, men vanligtvis det här felet beror på ett tjänstavbrott eller konfigurationsproblem. Om du ser detta fel och den påverkar din verksamhet, kontakta Microsoft support om hjälp. |
| Lokala Policyöverträdelse | Lösenordet uppfyller inte den lokala Active Directory-lösenordsprincip. |
| Lösenordet uppfyller inte fuzzy princip | Lösenordet som användes för visas i listan med förbjudna lösenord och kan inte användas. |

## <a name="troubleshoot-password-writeback"></a>Felsöka tillbakaskrivning av lösenord

| Fel | Lösning |
| --- | --- |
| Tjänsten för återställning av lösenord startar inte på plats. Fel 6800 visas i programhändelseloggen för den Azure AD Connect-datorn. <br> <br> Efter integreringen, federerade, kan inte direktautentisering eller lösenord-hash-synkroniserade användare återställa sina lösenord. | När tillbakaskrivning av lösenord är aktiverad, anropar Synkroniseringsmotorn tillbakaskrivning av biblioteket för att utföra konfigurationen (onboarding) genom att kommunicera till onboarding-Molntjänsten. Eventuella fel som upptäcks under publiceringen eller när de startar Windows Communication Foundation (WCF)-slutpunkten för resultat för tillbakaskrivning av lösenord i fel i händelseloggen på din Azure AD Connect-datorn. <br> <br> Under omstart av tjänsten Azure AD Sync (ADSync) om tillbakaskrivning av har konfigurerats, startas WCF-slutpunkten. Men om det inte går att starta slutpunkten, vi logga händelse 6800 och fjärrhantering av sync-tjänsten startas. Förekomst av den här händelsen innebär att slutpunkten för tillbakaskrivning av lösenord inte startar. Händelselogginformation för den här händelsen 6800, tillsammans med händelseloggen poster generera via komponenten PasswordResetService, ange varför du inte kan starta slutpunkten. Granska de här felen i händelseloggen och försök att starta om Azure AD Connect om tillbakaskrivning av lösenord inte fungerar fortfarande. Om problemet kvarstår försöker du inaktiverar och återaktiverar tillbakaskrivning av lösenord.
| När en användare försöker återställa ett lösenord eller låsa upp ett konto med tillbakaskrivning av lösenord aktiverat, misslyckas åtgärden. <br> <br> Dessutom kan du se att en händelse i händelseloggen för Azure AD Connect som innehåller ”: Synkroniseringsmotorn returnerade ett fel hr = 800700CE, message = filnamnet eller tillägget är för långt” efter upplåsning åtgärden sker. | Hitta Active Directory-konto för Azure AD Connect och återställa lösenordet så att den innehåller högst 127 tecken. Öppna sedan den **synkroniseringstjänsten** från den **starta** menyn. Bläddra till **Anslutningsappar** och hitta den **Active Directory-koppling**. Markera det och sedan välja **egenskaper**. Bläddra till den **autentiseringsuppgifter** sidan och ange det nya lösenordet. Välj **OK** att stänga sidan. |
| I det sista steget i Azure AD Connect-installationen kan du se ett felmeddelande om att det inte gick att konfigurera den tillbakaskrivning av lösenord. <br> <br> Azure AD Connect-programhändelseloggen innehåller fel 32009 med texten ”gick inte att hämta auth-token”. | Det här felet uppstår i följande två fall: <br><ul><li>Du har angett ett felaktigt lösenord för det globala administratörskontot som anges i början av Azure AD Connect-installationen.</li><li>Du har försökt att använda en federerad användare för det globala administratörskontot som anges i början av Azure AD Connect-installationen.</li></ul> Se till att du inte använder ett federerat konto för den globala administratören som du angav i början av installationsprocessen för att åtgärda problemet. Se också till att det angivna lösenordet är korrekt. |
| Händelseloggen för Azure AD Connect-datorn innehåller fel 32002 som genereras genom att köra PasswordResetService. <br> <br> Läser av felet: ”fel vid anslutning till ServiceBus. Tokenleverantören kunde inte tillhandahålla en säkerhetstoken ”. | Din lokala miljö inte kan ansluta till Azure Service Bus-slutpunkt i molnet. Det här felet orsakas normalt av en brandväggsregel som blockerar en utgående anslutning till en viss port eller web-adress. Se [anslutning krav](../hybrid/how-to-connect-install-prerequisites.md) för mer information. När du har uppdaterat reglerna, starta om Azure AD Connect-datorn och tillbakaskrivning av lösenord ska börja fungera igen. |
| Efter arbeta under en period federerad, kan inte direktautentisering eller lösenord-hash-synkroniserade användare återställa sina lösenord. | I vissa sällsynta fall kan tjänsten för tillbakaskrivning av lösenord gick inte att starta om när Azure AD Connect har startats om. I dessa fall kan du först kontrollera om tillbakaskrivning av lösenord verkar vara aktiverade lokala. Du kan kontrollera med hjälp av Azure AD Connect-guiden eller PowerShell (se avsnittet ovan HowTos). Om funktionen verkar vara aktiverat, försök att aktivera eller inaktivera funktionen igen antingen via Användargränssnittet eller PowerShell. Om det inte fungerar försöker en fullständig avinstallation och installera om av Azure AD Connect. |
| Autentisering för federerade, direkt eller lösenord-hash-synkroniserade användare som försöker återställa sina lösenord kan du se ett fel när du försöker skicka sina lösenord. Felet indikerar att det uppstod ett problem med tjänsten. <br ><br> Under åtgärder för återställning av lösenord, förutom det här problemet kan du ser felmeddelandet att hanteringsagenten nekades åtkomst i din lokala händelseloggar. | Om du ser felen i händelseloggen kontrollerar du att Active Directory Management Agent (ADMA)-kontot som angavs i guiden vid tidpunkten för konfiguration har behörigheterna som krävs för tillbakaskrivning av lösenord. <br> <br> Det kan ta upp till en timme för behörigheterna att sipprar ned efter den här behörigheten ges den `sdprop` bakgrundsaktivitet på domänkontrollanten (DC). <br> <br> Behörigheten måste stämplas på säkerhetsbeskrivningen för objektet vars lösenord återställs för lösenordsåterställning för att fungera. Tills den här behörigheten som visas i användarobjektet, fortsätter lösenordsåterställning att misslyckas med ett meddelande om nekad. |
| Autentisering för federerade, direkt eller lösenord-hash-synkroniserade användare som försöker återställa sina lösenord, ser du ett fel när de skickar sina lösenord. Felet indikerar att det uppstod ett problem med tjänsten. <br> <br> Förutom det här problemet visas under åtgärder för återställning av lösenord, ett fel i händelseloggarna från Azure AD Connect-tjänsten som anger felmeddelandet ”Det gick inte att hitta objektet”. | Det här felet indikerar vanligtvis att Synkroniseringsmotorn är det går inte att hitta användarobjektet i Azure AD-anslutarplatsen eller länkade metaversum (MV) eller Azure AD-anslutarplatsen. <br> <br> Se till att du verkligen har synkroniserats från en lokal plats till Azure AD via den aktuella instansen av Azure AD Connect och granska status för objekten i kopplingens utrymmen och MV för att felsöka problemet. Kontrollera att objektet Active Directory Certificate Services (AD CS) är ansluten till MV-objekt via regeln ”Microsoft.InfromADUserAccountEnabled.xxx”.|
| Autentisering för federerade, direkt eller lösenord-hash-synkroniserade användare som försöker återställa sina lösenord ser ett fel när de skickar sina lösenord. Felet indikerar att det uppstod ett problem med tjänsten. <br> <br> Förutom det här problemet visas under åtgärder för återställning av lösenord, ett fel i händelseloggarna från Azure AD Connect-tjänsten som anger att det finns en ”flera matchningar hittades”-fel. | Detta anger att Synkroniseringsmotorn identifierat att MV-objekt är ansluten till mer än en AD CS-objekt via ”Microsoft.InfromADUserAccountEnabled.xxx”. Det innebär att användaren har ett aktiverat konto i mer än en skog. Det här scenariot stöds inte för tillbakaskrivning av lösenord. |
| Lösenordsåtgärder misslyckas med ett konfigurationsfel. Programmets händelselogg innehåller Azure AD Connect fel 6329 med texten ”0x8023061f (åtgärden misslyckades eftersom Lösenordssynkronisering inte är aktiverat på den här hanteringsagenten)”. | Det här felet uppstår om Azure AD Connect-konfigurationen har ändrats för att lägga till en ny Active Directory-skog (eller om du vill ta bort och readd en befintlig skog) efter funktionen för tillbakaskrivning av lösenord har redan aktiverats. Lösenordsåtgärder för användare i dessa nyligen tillagda skogar misslyckas. Om du vill åtgärda problemet, inaktivera och sedan aktivera igen funktionen för tillbakaskrivning av lösenord efter konfigurationsändringar skog har slutförts. |

## <a name="password-writeback-event-log-error-codes"></a>Felkoder för lösenord tillbakaskrivning av händelseloggen

Bästa praxis när du felsöker problem med tillbakaskrivning av lösenord är att granska programmets händelselogg på din Azure AD Connect-datorn. Den här händelseloggen innehåller händelser från två källor av intresse för tillbakaskrivning av lösenord. Källan PasswordResetService beskriver åtgärder och problem som rör driften av tillbakaskrivning av lösenord. ADSync källan beskriver åtgärder och problem som rör ange lösenord i Active Directory-miljön.

### <a name="if-the-source-of-the-event-is-adsync"></a>Om källan för händelsen är ADSync

| Kod | Namn eller meddelande | Beskrivning |
| --- | --- | --- |
| 6329 | BORGEN: MMS(4924) 0x80230619: ”en begränsning förhindrar att lösenordet ändras till den aktuella som angetts”. | Den här händelsen inträffar när tjänsten för tillbakaskrivning av lösenord försöker ange ett lösenord för din lokala katalog som inte uppfyller ålder för lösenord, historik, komplexitet eller filtrering kraven i domänen. <br> <br> Om du har en lägsta ålder för lösenord och nyligen har ändrat lösenordet inom denna tidsperiod kan det inte går att ändra lösenordet igen tills den når den angivna åldern i din domän. I testsyfte sättas minimiålder till 0. <br> <br> Om du har använts på sistone aktiverad så du måste välja ett lösenord som inte har använts under senaste *N* tider var *N* är lösenordsinställningen för historik. Om du väljer ett lösenord som har använts under senaste *N* gånger, kan du se ett fel i det här fallet. I testsyfte ska tidigare lösenord anges till 0. <br> <br> Om du har krav på lösenordskomplexitet, tillämpas alla när användaren försöker att ändra och återställa ett lösenord. <br> <br> Om du har aktiverat lösenordsfilter och en användare väljer ett lösenord som uppfyller inte sökvillkoren, sedan återställa eller ändra åtgärden misslyckas. |
| 6329 | MMS(3040): admaexport.cpp(2837): servern innehåller inte principkontroll för LDAP-lösenord. | Det här problemet uppstår om LDAP_SERVER_POLICY_HINTS_OID kontroll (1.2.840.113556.1.4.2066) inte är aktiverad på domänkontrollanter. Om du vill använda funktionen för tillbakaskrivning av lösenord, måste du aktivera kontrollen. Om du vill göra det finnas domänkontrollanter i Windows Server 2008 (med senaste SP) eller senare. Om dina domänkontrollanter som är på 2008 (pre-R2) så du måste också installera snabbkorrigeringar [KB2386717](https://support.microsoft.com/kb/2386717). |
| HR 8023042 | Synkroniseringsmotorn returnerade ett fel hr = 80230402, message = ett försök att hämta ett objekt som misslyckades eftersom det finns duplicerade poster med samma ankaret. | Det här felet inträffar när samma användar-ID är aktiverat i flera domäner. Ett exempel är om du synkroniserar konto- och skogar och har samma användar-ID finns och är aktiverat i varje skog. <br> <br> Det här felet kan också inträffa om du använder en icke-unikt fästpunktsattributet som ett alias eller UPN och två användare delar den samma fästpunktsattributet. <br> <br> Lös problemet genom att se till att det inte finns några dubbletter av användare i din domäner och att du använder en unik fästpunktsattributet för varje användare. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Om källan för händelsen är PasswordResetService

| Kod | Namn eller meddelande | Beskrivning |
| --- | --- | --- |
| 31001 | PasswordResetStart | Den här händelsen anger att den lokala tjänsten identifierat en lösenordsåterställning begäran för en federerad direkt autentisering eller lösenord-hash-synkroniserade användare som kommer från molnet. Den här händelsen är den första händelsen i varje tillbakaskrivningen för återställning av lösenord. |
| 31002 | PasswordResetSuccess | Den här händelsen anger att en användare har valt ett nytt lösenord under en återställning av lösenord. Vi fastställt att det här lösenordet uppfyller företagets lösenordsprincip. Lösenordet har skrivits tillbaka till den lokala Active Directory-miljön. |
| 31003 | PasswordResetFail | Den här händelsen anger att en användare har valt ett lösenord och lösenordet har kommit fram till den lokala miljön. Men ett fel uppstod när vi försökte ställa in lösenordet i den lokala Active Directory-miljön. Det här felet kan bero på flera orsaker: <br><ul><li>Användarens lösenord inte uppfyller ålder, historik, komplexitet eller filtrera krav för domänen. Lös problemet genom att skapa ett nytt lösenord.</li><li>ADMA-tjänstkontot har inte behörighet att ange det nya lösenordet för användarkontot i fråga.</li><li>Användarens konto är i en skyddad grupp, till exempel domäner eller Företagsadministratörer administratörsgrupp som inte tillåter lösenordsinställning.</li></ul>|
| 31004 | OnboardingEventStart | Den här händelsen inträffar om du aktiverar tillbakaskrivning av lösenord med Azure AD Connect och vi har startat onboarding organisationen till webbtjänsten för tillbakaskrivning av lösenord. |
| 31005 | OnboardingEventSuccess | Den här händelsen indikerar att onboarding-processen lyckades och att funktionen för tillbakaskrivning av lösenord är redo att användas. |
| 31006 | ChangePasswordStart | Den här händelsen anger att den lokala tjänsten har identifierat en begäran om lösenordsändring för en federerad direkt autentisering eller lösenord-hash-synkroniserade användare som kommer från molnet. Den här händelsen är den första händelsen i tillbakaskrivningen varje ändring av lösenord. |
| 31007 | ChangePasswordSuccess | Den här händelsen indikerar att en användare har valt ett nytt lösenord under en åtgärd för lösenordsbyte, har vi fastställt att lösenordet uppfyller företagets lösenordskrav och att lösenordet har skrivits tillbaka till den lokala Active Directory-miljön. |
| 31008 | ChangePasswordFail | Den här händelsen indikerar att en användare har valt ett lösenord och att lösenordet har kommit fram till den lokala miljön, men ett fel uppstod när vi försökte ställa in lösenordet i den lokala Active Directory-miljön. Det här felet kan bero på flera orsaker: <br><ul><li>Användarens lösenord inte uppfyller ålder, historik, komplexitet eller filtrera krav för domänen. Lös problemet genom att skapa ett nytt lösenord.</li><li>ADMA-tjänstkontot har inte behörighet att ange det nya lösenordet för användarkontot i fråga.</li><li>Användarens konto är i en skyddad grupp, till exempel domäner eller Företagsadministratörer administratörer som inte tillåter lösenordsinställning.</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | Lokala-tjänsten har identifierat en lösenordsåterställning begäran för en federerad direkt autentisering eller lösenord-hash-synkroniserade användare som kommer från administratören för en användares räkning. Den här händelsen är den första händelsen i varje tillbakaskrivningen för återställning av lösenord som initieras av en administratör. |
| 31010 | ResetUserPasswordByAdminSuccess | Administratören har valt ett nytt lösenord under en åtgärd som initieras av administratören återställning av lösenord. Vi fastställt att det här lösenordet uppfyller företagets lösenordsprincip. Lösenordet har skrivits tillbaka till den lokala Active Directory-miljön. |
| 31011 | ResetUserPasswordByAdminFail | Administratören har valt ett lösenord för en användares räkning. Lösenordet kommit har till den lokala miljön. Men ett fel uppstod när vi försökte ställa in lösenordet i den lokala Active Directory-miljön. Det här felet kan bero på flera orsaker: <br><ul><li>Användarens lösenord inte uppfyller ålder, historik, komplexitet eller filtrera krav för domänen. Testa ett nytt lösenord för att lösa problemet.</li><li>ADMA-tjänstkontot har inte behörighet att ange det nya lösenordet för användarkontot i fråga.</li><li>Användarens konto är i en skyddad grupp, till exempel domäner eller Företagsadministratörer administratörer som inte tillåter lösenordsinställning.</li></ul>  |
| 31012 | OffboardingEventStart | Den här händelsen inträffar om du inaktiverar tillbakaskrivning av lösenord med Azure AD Connect och anger att vi startas offboarding organisationen till webbtjänsten för tillbakaskrivning av lösenord. |
| 31013| OffboardingEventSuccess| Den här händelsen indikerar att offboarding processen lyckades och att funktionen för tillbakaskrivning av lösenord har inaktiverats. |
| 31014| OffboardingEventFail| Den här händelsen indikerar att offboarding processen inte lyckades. Detta kan bero på ett behörighetsfel på molnet eller lokalt administratörskonto som har angetts under konfigurationen. Felet kan också inträffa om du försöker använda en global administratör för federerad molnet när du inaktiverar tillbakaskrivning av lösenord. Kontrollera din behörighet för att åtgärda problemet och se till att du inte använder ett federerat konto när du konfigurerar funktionen för tillbakaskrivning av lösenord.|
| 31015| WriteBackServiceStarted| Den här händelsen indikerar att tjänsten för tillbakaskrivning av lösenord har startats. Är den redo att acceptera begäranden för hantering av lösenord från molnet.|
| 31016| WriteBackServiceStopped| Den här händelsen anger att tjänsten för tillbakaskrivning av lösenord längre. Alla begäranden för hantering av lösenord från molnet kommer att misslyckas.|
| 31017| AuthTokenSuccess| Den här händelsen indikerar att vi har läst in en Autentiseringstoken för den globala administratören som angavs vid Azure AD Connect-installationen att starta offboarding eller onboarding-processen.|
| 31018| KeyPairCreationSuccess| Den här händelsen indikerar att vi har skapat lösenordskrypteringsnyckeln. Den här nyckeln används för att kryptera lösenord från molnet som ska skickas till din lokala miljö.|
| 32000| UnknownError| Den här händelsen anger ett okänt fel uppstod under en åtgärd för hantering. Titta på undantagstexten i händelsen för mer information. Om du har problem bör du försöka inaktivera och återaktivera tillbakaskrivning av lösenord. Om detta inte hjälper kan innehålla en kopia av händelseloggen tillsammans med spårnings ID angivna insider till en supporttekniker.|
| 32001| ServiceError| Den här händelsen indikerar att det uppstod ett fel vid anslutning till molnet lösenordet-återställning. Det här felet uppstår vanligen när den lokala tjänsten kunde inte ansluta till webbtjänsten för återställning av lösenord.|
| 32002| ServiceBusError| Den här händelsen indikerar att det uppstod ett fel vid anslutning till din klient Service Bus-instans. Detta kan inträffa om du blockerar utgående anslutningar i din lokala miljö. Kontrollera brandväggen för att säkerställa att du tillåter anslutningar via TCP 443 och till https://ssprsbprodncu-sb.accesscontrol.windows.net/, och försök sedan igen. Om du fortfarande har problem, bör du försöka inaktivera och återaktivera tillbakaskrivning av lösenord.|
| 32003| InPutValidationError| Den här händelsen indikerar att skickades till vår webbtjänst-API var ogiltigt. Försök igen.|
| 32004| DecryptionError| Den här händelsen indikerar att det uppstod ett fel Dekrypteringen av lösenordet som anlänt från molnet. Detta kan bero på ett dekryptering viktiga matchningsfel mellan Molntjänsten och din lokala miljö. Lös problemet, inaktivera och återaktivera tillbakaskrivning av lösenord i din lokala miljö.|
| 32005| ConfigurationError| Under onboarding-processen spara vi klientspecifik information i en konfigurationsfil i din lokala miljö. Den här händelsen indikerar att det uppstod ett fel vid sparande av den här filen eller att när tjänsten har startats, det gick inte att läsa filen. Försök för att åtgärda problemet, inaktivera och återaktivera tillbakaskrivning av lösenord att tvinga en omarbetning av konfigurationsfilen.|
| 32007| OnBoardingConfigUpdateError| Under onboarding-processen Skicka vi data från molnet till lokalt tjänsten för återställning av lösenord. Dessa data skrivs sedan till en fil i minnet innan det skickas till synkroniseringstjänsten lagras på ett säkert sätt på disken. Den här händelsen indikerar att det finns ett problem med att skriva eller uppdatera dessa data i minnet. Försöka inaktivera och återaktivera tillbakaskrivning av lösenord att tvinga en omarbetning av konfigurationsfilen för att åtgärda problemet.|
| 32008| ValidationError| Den här händelsen anger vi har tagit emot ett ogiltigt svar från webbtjänsten för återställning av lösenord. Försök för att åtgärda problemet, inaktivera och återaktivera tillbakaskrivning av lösenord.|
| 32009| AuthTokenError| Den här händelsen indikerar inte att vi kunde erhålla en auktorisering token för det globala administratörskontot som angavs vid installationen av Azure AD Connect. Det här felet kan orsakas av ett felaktigt användarnamn eller lösenord som angetts för det globala administratörskontot. Det här felet kan också inträffa om det globala administratörskontot som angetts är federerat. Åtgärda problemet genom att köra konfigurationen med rätt användarnamn och lösenord och se till att administratören är ett hanterat konto (endast molnbaserad eller lösenord synkroniseras).|
| 32010| CryptoError| Den här händelsen indikerar att det uppstod ett fel genereras lösenordskrypteringsnyckeln eller dekryptering av ett lösenord som tas emot från Molntjänsten. Det här felet sannolikt tyder på problem med din miljö. Titta på informationen i händelseloggen för mer information om hur du löser problemet. Du kan även försöka inaktivera och återaktivera tjänsten för tillbakaskrivning av lösenord.|
| 32011| OnBoardingServiceError| Den här händelsen indikerar att den lokala tjänsten korrekt inte kunde kommunicera med webbtjänsten för återställning av lösenord för att påbörja onboardingprocessen. Detta kan inträffa på grund av en brandväggsregel eller om det finns inte att hämta en autentisering token för din klient. Du kan åtgärda det här problemet genom att se till att du inte blockerar utgående anslutningar via TCP 443 och TCP 9350-9354 eller att https://ssprsbprodncu-sb.accesscontrol.windows.net/. Också se till att Azure AD-administratörskonto som du använder för att komma igång är inte federerad.|
| 32013| OffBoardingError| Den här händelsen indikerar att den lokala tjänsten korrekt inte kunde kommunicera med webbtjänst för återställning av lösenord för att starta processen offboarding. Detta kan inträffa på grund av en brandväggsregel eller om det finns inte att hämta en auktorisering token för din klient. Åtgärda problemet genom att se till att du inte blockerar utgående anslutningar över port 443 eller till https://ssprsbprodncu-sb.accesscontrol.windows.net/, och som inte är federerat Azure Active Directory-administratörskonto som du använder för att avregistrera.|
| 32014| ServiceBusWarning| Den här händelsen anger att vi har haft att försöka igen om du vill ansluta till din klient Service Bus-instans. Under normala förhållanden bör detta inte vara ett problem, men om du ser den här händelsen många gånger, Överväg att kontrollera din nätverksanslutning till Service Bus, särskilt om det är en lång svarstid eller långsam anslutning.|
| 32015| ReportServiceHealthError| För att övervaka hälsotillståndet för tjänsten för tillbakaskrivning av lösenord, skicka vi pulsslag data till vår webbtjänst för återställning av lösenord var femte minut. Den här händelsen indikerar att det uppstod ett fel när du skickar den här hälsoinformation tillbaka till molnet-webbtjänsten. Den här hälsoinformation innehåller inte ett objekt identifierbar information (OII) eller personligt identifierbar information (PII)-data och är helt och hållet ett pulsslag och grundläggande statistik så att vi kan ge information om status för tjänsten i molnet.|
| 33001| ADUnKnownError| Den här händelsen anger att det har ett okänt fel som returneras av Active Directory. Kontrollera händelseloggen för Azure AD Connect-servern efter händelser från ADSync källan för mer information.|
| 33002| ADUserNotFoundError| Den här händelsen indikerar att den användare som försöker att återställa eller ändra ett lösenord inte hittades i den lokala katalogen. Det här felet kan inträffa när användaren har tagits bort på plats, men inte i molnet. Det här felet kan också inträffa om det finns ett problem med synkronisering. Kontrollera din synkroniseringsloggarna och senaste få synkronisering kör information för mer information.|
| 33003| ADMutliMatchError| När en lösenordsåterställning eller ändringsbegäran samlas in från molnet, kan vi använda till molnet ankaret som angetts under installationen av Azure AD Connect för att avgöra hur du länkar begäran tillbaka till en användare i din lokala miljö. Den här händelsen anger att vi finns två användare i din lokala katalog med samma fästpunktsattributet för molnet. Kontrollera din synkroniseringsloggarna och senaste få synkronisering kör information för mer information.|
| 33004| ADPermissionsError| Den här händelsen anger att Active Directory Management Agent (ADMA)-tjänstkontot inte har de behörigheter som krävs för kontot i fråga att ställa in ett nytt lösenord. Kontrollera att ADMA-kontot i användarens skog har återställt och ändra lösenord behörigheter för alla objekt i skogen. Mer information om hur du anger behörigheter finns i steg 4: konfigurera lämpliga behörigheter i Active Directory.|
| 33005| ADUserAccountDisabled| Den här händelsen indikerar att vi försökte återställa eller ändra ett lösenord för ett konto som har inaktiveras lokalt. Aktivera kontot och försök sedan igen.|
| 33006| ADUserAccountLockedOut| Den här händelsen indikerar att vi försökte återställa eller ändra ett lösenord för ett konto som har låsts ute på plats. Utelåsningar kan inträffa när en användare har försökt en ändring eller återställning av lösenord för många gånger under en kort period. Låsa upp kontot och försök sedan igen.|
| 33007| ADUserIncorrectPassword| Den här händelsen anger att användaren angett ett felaktigt aktuella lösenord när du utför ett lösenord ska kunna ändras. Ange det aktuella lösenordet och försök igen.|
| 33008| ADPasswordPolicyError| Den här händelsen inträffar när tjänsten för tillbakaskrivning av lösenord försöker ange ett lösenord för din lokala katalog som inte uppfyller ålder för lösenord, historik, komplexitet eller filtrering kraven i domänen. <br> <br> Om du har en lägsta ålder för lösenord och nyligen har ändrat lösenordet inom denna tidsperiod kan det inte går att ändra lösenordet igen tills den når den angivna åldern i din domän. I testsyfte sättas minimiålder till 0. <br> <br> Om du har använts på sistone aktiverad så du måste välja ett lösenord som inte har använts under senaste *N* tider var *N* är lösenordsinställningen för historik. Om du väljer ett lösenord som har använts under senaste *N* gånger, kan du se ett fel i det här fallet. I testsyfte ska tidigare lösenord anges till 0. <br> <br> Om du har krav på lösenordskomplexitet, tillämpas alla när användaren försöker att ändra och återställa ett lösenord. <br> <br> Om du har aktiverat lösenordsfilter och en användare väljer ett lösenord som uppfyller inte sökvillkoren, sedan återställa eller ändra åtgärden misslyckas.|
| 33009| ADConfigurationError| Den här händelsen indikerar att det uppstod ett problem vid skrivning lösenord tillbaka till din lokala katalog på grund av konfigurationsproblem med Active Directory. Kontrollera händelseloggen för den Azure AD Connect-datorn för meddelanden från ADSync-tjänsten för mer information där fel uppstod.|

## <a name="troubleshoot-password-writeback-connectivity"></a>Felsök anslutning för tillbakaskrivning av lösenord

Om du upplever avbrott i tjänsten med komponenten för tillbakaskrivning av lösenord för Azure AD Connect, är här några enkla steg som du kan vidta för att lösa problemet:

* [Bekräfta nätverksanslutning](#confirm-network-connectivity)
* [Starta om tjänsten Azure AD Connect Sync](#restart-the-azure-ad-connect-sync-service)
* [Inaktivera och återaktivera funktionen för tillbakaskrivning av lösenord](#disable-and-re-enable-the-password-writeback-feature)
* [Installera den senaste versionen av Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [Felsöka tillbakaskrivning av lösenord](#troubleshoot-password-writeback)

Om du vill återställa din tjänst på snabbaste sätt, rekommenderar vi i allmänhet när du kör de här stegen i den ordning som beskrevs tidigare.

### <a name="confirm-network-connectivity"></a>Bekräfta nätverksanslutning

De vanligaste tidpunkten för felet är att ingen brandvägg och eller aktiveringsproxyportarna och inaktiv timeout är felaktigt konfigurerade. 

För Azure AD Connect version 1.1.443.0 och senare, du behöver utgående HTTPS åtkomst till följande:

   - passwordreset.microsoftonline.com
   - servicebus.Windows.NET

För mer detaljrikedom, referera till den uppdaterade listan med [IP-intervall i Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653) uppdateras varje onsdag och träder i kraft nästa måndag.

Mer information, gå igenom kraven för anslutningen i den [krav för Azure AD Connect](../hybrid/how-to-connect-install-prerequisites.md) artikeln.

### <a name="restart-the-azure-ad-connect-sync-service"></a>Starta om tjänsten Azure AD Connect Sync

Lös problem med nätverksanslutningen eller andra tillfälliga problem med tjänsten genom att starta om tjänsten Azure AD Connect Sync:

   1. Som administratör, välja **starta** på den server som kör Azure AD Connect.
   1. Ange **services.msc** i sökfältet och välj **RETUR**.
   1. Leta efter den **Microsoft Azure AD Sync** posten.
   1. Högerklicka på tjänstposten, Välj **starta om**, och sedan vänta tills åtgärden slutförs.

   ![Starta om tjänsten Azure AD Sync][Service restart]

De här stegen återupprätta anslutningen med Molntjänsten och lös eventuella avbrott i verksamheten som kan uppstå. Om omstart av ADSync-tjänsten inte löser problemet, rekommenderar vi att du försöker inaktivera och återaktivera funktionen för tillbakaskrivning av lösenord.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Inaktivera och återaktivera funktionen för tillbakaskrivning av lösenord

För att lösa problem med nätverksanslutningen, inaktivera och återaktivera funktionen för tillbakaskrivning av lösenord:

   1. Som administratör öppnar du Azure AD Connect-konfigurationsguiden.
   1. I **Anslut till Azure AD**, ange dina autentiseringsuppgifter för Azure AD-global administratör.
   1. I **Anslut till AD DS**, ange dina autentiseringsuppgifter som administratör AD Domain Services.
   1. I **identifierar användarna**väljer den **nästa** knappen.
   1. I **valfria funktioner**, avmarkera de **tillbakaskrivning av lösenord** markerar du kryssrutan.
   1. Välj **nästa** via återstående dialogsidorna utan att ändra vad som helst tills du kommer till den **redo att konfigurera** sidan.
   1. Se till att den **redo att konfigurera sidan** visar den **tillbakaskrivning av lösenord** beroende på **inaktiverad** och välj sedan gröna **konfigurera** knappen för att genomföra ändringarna.
   1. I **slutfört**, avmarkera de **synkronisera nu** och välj sedan **Slutför** att stänga guiden.
   1. Öppna Azure AD Connect-konfigurationsguiden.
   1. Upprepa steg 2-8, förutom att se till att du väljer den **tillbakaskrivning av lösenord** alternativet på den **valfria funktioner** sidan för att aktivera tjänsten igen.

De här stegen återupprätta anslutningen med vår tjänst i molnet och lös eventuella avbrott i verksamheten som kan uppstå.

Inaktivera och återaktivera funktionen för tillbakaskrivning av lösenord inte löser problemet, rekommenderar vi att du måste installera om Azure AD Connect.

### <a name="install-the-latest-azure-ad-connect-release"></a>Installera den senaste versionen av Azure AD Connect

Installera om Azure AD Connect kan lösa konfiguration och problem med nätverksanslutningen mellan våra molntjänster och din lokala Active Directory-miljö.

Vi rekommenderar att du utför det här steget endast när du försöker de två första stegen som beskrivs ovan.

> [!WARNING]
> Om du har anpassat de out-of the box Synkroniseringsregler *säkerhetskopiera dem innan du fortsätter med uppgraderingen och sedan manuellt distribuera dem igen när du är klar.*
>

1. Hämta den senaste versionen av Azure AD Connect från den [Microsoft Download Center](https://go.microsoft.com/fwlink/?LinkId=615771).
1. Eftersom du redan har installerat Azure AD Connect måste du utföra en uppgradering på plats för att uppdatera din Azure AD Connect-installationen till den senaste versionen.
1. Kör det Hämta paketet och följa den på skärmen instruktionerna för att uppdatera din Azure AD Connect-datorn.

De här stegen bör återupprätta anslutningen med vår tjänst i molnet och Lös avbrott som kan uppstå.

Om du installerar den senaste versionen av Azure AD Connect-servern inte löser problemet, rekommenderar vi att du försöka inaktivera och sedan återaktivera tillbakaskrivning av lösenord som ett sista steg när du har installerat den senaste versionen.

## <a name="verify-that-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>Kontrollera att Azure AD Connect har behörigheten som krävs för tillbakaskrivning av lösenord

Azure AD Connect kräver Active Directory **Återställ lösenord** behörighet att utföra tillbakaskrivning av lösenord. Du kan använda den gällande behörigheten för Windows-funktionen om du vill veta om Azure AD Connect har behörigheten som krävs för en viss lokala Active Directory-användarkonto:

1. Logga in på Azure AD Connect-servern och starta den **hanteraren för synkroniseringstjänsten** genom att välja **starta** > **synkroniseringstjänsten**.
1. Under den **Anslutningsappar** väljer du lokalt **Active Directory Domain Services** anslutningen och välj sedan **egenskaper**.  
   ![Gällande behörigheten – steg 2](./media/active-directory-passwords-troubleshoot/checkpermission01.png)  
  
1. I popup-fönstret, Välj **Anslut till Active Directory-skog** och anteckna den **användarnamn** egenskapen. Den här egenskapen är AD DS-kontot som används av Azure AD Connect för att utföra katalogsynkronisering. Att utföra tillbakaskrivning av lösenord Azure AD Connect har AD DS-kontot återställt lösenordet behörighet.  
   
   ![Gällande behörigheten – steg 3](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
  
1. Logga in till en lokal domänkontrollant och starta den **Active Directory-användare och datorer** program.
1. Välj **visa** och se till att den **avancerade funktioner** är aktiverat.  
   
   ![Gällande behörigheten – steg 5](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
  
1. Leta efter Active Directory-användarkonto som du vill kontrollera. Högerklicka på namnet på kontot och välj **egenskaper**.  
   
   ![Gällande behörigheten - steg 6](./media/active-directory-passwords-troubleshoot/checkpermission04.png) 

1. I popup-fönstret går du till den **Security** fliken och markera **Avancerat**.  
   
   ![Gällande behörigheten – steg 7](./media/active-directory-passwords-troubleshoot/checkpermission05.png) 
   
1. I den **avancerade säkerhetsinställningar för administratören** popup-fönstret, gå till den **gällande åtkomst** fliken.
1. Välj **väljer en användare**, Välj AD DS-kontot som används av Azure AD Connect (se steg 3) och välj sedan **visa gällande åtkomst**.

   ![Gällande behörigheten - steg 9](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
  
1. Rulla nedåt och leta efter **Återställ lösenord**. Om transaktionen har markerat, har AD DS-kontot behörighet att återställa lösenordet för det valda Active Directory-kontot.  
   
   ![Gällande behörigheten - steg 10](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Azure AD-forum

Om du har en allmän fråga om Azure AD och återställning av lösenord, kan du be communityn för att få hjälp på den [Azure AD-forumen](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Medlemmar i communityn omfattar tekniker, produktchefer, MVP: er och andra IT-proffs.

## <a name="contact-microsoft-support"></a>Kontakta Microsoft-supporten

Om du inte hittar svaret på ett problem, är vårt supportteam alltid tillgängliga för att hjälpa dig ytterligare.

För att hjälpa dig korrekt ombeds ange så mycket information som möjligt när du öppnar ett ärende. Uppgifterna omfattar bland annat:

* **Allmän beskrivning av felet**: Vad är felet? Vad var det beteende som upptäcktes? Hur kan vi för att återskapa felet? Ange så mycket information som möjligt.
* **Sidan**: vilken sida har du på när du har märkt felet? Är URL: en om du kan till och en skärmbild av sidan.
* **Supportkod**: vad var den supportkod som genererades när användaren såg felet?
    * För att hitta den här koden, återskapa felet och välj sedan den **supportkod** länken längst ned på skärmen och skicka supportteknikern GUID som är ett resultat.

    ![Hitta supportkod längst ned på skärmen][Support code]

    * Om du är på en sida utan en supportkod längst ned på sidan Välj F12 och Sök efter SID och CID och skicka dessa två resultat till supportteknikern.
* **Datum, tid och tidszon**: ange exakt datum och tid *med tidszonen* som felet inträffade.
* **Användar-ID**: vem som har den användare som såg felet? Ett exempel är *user@contoso.com*.
    * Är detta en federerad användare?
    * Är detta en direktautentisering användare?
    * Är detta en lösenord-hash-synkroniserade användare?
    * Är detta en endast molnbaserad användare?
* **Licensiering**: har du en Azure AD Premium eller Azure AD Basic-licens?
* **Programhändelseloggen**: Om du använder tillbakaskrivning av lösenord och felet finns i din lokala infrastruktur, inkludera en komprimerad kopia av Loggboken program från Azure AD Connect-servern.

[Service restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "Starta om tjänsten Azure AD Sync"
[Support code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "Supportkod som finns längst ned till höger i fönstret"

## <a name="next-steps"></a>Nästa steg

I följande artiklar innehåller ytterligare information om lösenordsåterställning via Azure AD:

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
