---
title: "Självbetjäning för lösenordsåterställning felsökning – Azure Active Directory"
description: "Felsöka Azure AD Självbetjäning för lösenordsåterställning"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2018
ms.author: joflore
ms.custom: it-pro;seohack1
ms.openlocfilehash: c038a9ec682a5971a5f79b9fe36e667493702cbd
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
---
# <a name="troubleshoot-self-service-password-reset"></a>Felsökning av lösenordsåterställning via självbetjäning

Har du problem med Azure Active Directory (AD Azure) lösenordsåterställning via självbetjäning (SSPR)? Den information som visas nedan kan hjälpa dig att få saker fungerar igen.

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-might-see"></a>Felsöka självbetjäning lösenordsåterställning som en användare kan se

| Fel | Information | Teknisk information |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Vi beklagar, men du inte kan återställa ditt lösenord just nu eftersom din administratör har inaktiverat återställning av lösenord för din organisation. Det finns inga ytterligare åtgärder som du kan vidta för att lösa problemet. Kontakta administratören och be dem att aktivera den här funktionen. Läs mer i [hjälp, jag har glömt mitt Azure AD-lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions). | SSPR_0009: Vi har upptäckt att återställning av lösenord inte har aktiverats av administratören. Kontakta administratören och be dem att aktivera återställning av lösenord för din organisation. |
| WritebackNotEnabled = 10 |Vi beklagar, men du inte kan återställa ditt lösenord just nu eftersom administratören inte har aktiverat en tjänst som behövs för din organisation. Det finns inga ytterligare åtgärder som du kan vidta för att lösa problemet. Kontakta administratören och be dem att kontrollera konfigurationen för din organisation. Mer information om den här tjänsten som behövs finns [konfigurera tillbakaskrivning av lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-writeback#configure-password-writeback). | SSPR_0010: Vi har upptäckt att tillbakaskrivning av lösenord inte har aktiverats. Kontakta administratören och be dem att aktivera tillbakaskrivning av lösenord. |
| SsprNotEnabledInUserPolicy = 11 | Vi beklagar, men du inte kan återställa ditt lösenord just nu eftersom din administratör inte har konfigurerat för återställning av lösenord för din organisation. Det finns inga ytterligare åtgärder som du kan vidta för att lösa problemet. Kontakta administratören och be dem att konfigurera återställning av lösenord. Läs mer om lösenordet återställs i [Snabbstart: Azure AD Självbetjäning för lösenordsåterställning](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started). | SSPR_0011: Din organisation har definierat en princip för lösenordsåterställning. Kontakta administratören och be dem att definiera en princip för lösenordsåterställning. |
| UserNotLicensed = 12 | Vi beklagar, men du inte kan återställa ditt lösenord just nu eftersom nödvändiga licenser saknas från din organisation. Det finns inga ytterligare åtgärder som du kan vidta för att lösa problemet. Kontakta administratören och be dem att kontrollera din licenstilldelning. Mer information om licensiering finns [Licensing krav för Azure AD-lösenordet för självbetjäning återställa](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing). | SSPR_0012: Din organisation har inte licenserna som krävs för att utföra återställning av lösenord. Kontakta administratören och be dem att granska licenstilldelningar. |
| UserNotMemberOfScopedAccessGroup = 13 | Vi beklagar, men du inte kan återställa ditt lösenord just nu eftersom din administratör inte har konfigurerat ditt konto om du vill använda för lösenordsåterställning. Det finns inga ytterligare åtgärder som du kan vidta för att lösa problemet. Kontakta administratören och be dem att konfigurera ditt konto för återställning av lösenord. Läs mer om konfigurationen av kontot för återställning av lösenord i [lanserar lösenordsåterställning för användare](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices). | SSPR_0012: Du är inte medlem i en grupp som har aktiverats för lösenordsåterställning. Kontakta din administratör och begäran som ska läggas till i gruppen. |
| UserNotProperlyConfigured = 14 | Vi beklagar, men du inte kan återställa ditt lösenord just nu eftersom det saknas nödvändig information från ditt konto. Det finns inga ytterligare åtgärder som du kan vidta för att lösa problemet. Kontakta administratören och be dem att återställa lösenordet åt dig. Du måste registrera den nödvändiga informationen när du har åtkomst till ditt konto igen. Registrera information genom att följa stegen i den [registrera dig för lösenordsåterställning via självbetjäning](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register) artikel. | SSPR_0014: Ytterligare säkerhetsinformation behövs för att återställa ditt lösenord. Kontakta administratören och be dem att återställa ditt lösenord för att fortsätta. När du har tillgång till ditt konto kan registrera du ytterligare säkerhetsinformation på https://aka.ms/ssprsetup. Din administratör kan att lägga till ytterligare säkerhetsinformation till ditt konto genom att följa stegen i [anges, och Läs autentiseringsdata för lösenordsåterställning](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-data#set-and-read-authentication-data-using-powershell). |
| OnPremisesAdminActionRequired = 29 | Vi kan tyvärr inte återställa ditt lösenord just nu på grund av ett problem med din organisations lösenord återställa konfigurationen. Det finns inga ytterligare åtgärder som du kan vidta för att lösa problemet. Kontakta administratören och be dem att undersöka. Mer information om potentiella problem finns [felsöka tillbakaskrivning av lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback). | SSPR_0029: Vi kan inte återställa ditt lösenord på grund av ett fel i din lokala konfiguration. Kontakta administratören och be dem att undersöka. |
| OnPremisesConnectivityError = 30 | Vi kan tyvärr inte återställa ditt lösenord just nu på grund av problem med nätverksanslutningen för din organisation. Åtgärd ingen att vidta just nu, men kan problemet lösas om du försöker igen senare. Om problemet kvarstår, kontakta din administratör och be dem att undersöka. Mer information om problem med nätverksanslutningen finns [felsöka anslutningsbarheten för tillbakaskrivning av lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity). | SSPR_0030: Vi kan inte återställa ditt lösenord på grund av en dålig anslutning med din lokala miljö. Kontakta administratören och be dem att undersöka.|


## <a name="troubleshoot-the-password-reset-configuration-in-the-azure-portal"></a>Felsöka konfigurationen på Återställ lösenord i Azure-portalen

| Fel | Lösning |
| --- | --- |
| Visas inte i **lösenordsåterställning** avsnitt under Azure AD i Azure-portalen. | Detta kan inträffa om du inte har en Azure AD Premium eller Basic licens till administratören som utför åtgärden. <br> <br> Tilldela en licens till administratörskontot i fråga. Du kan följa stegen i den [tilldela kontrollerar och lösa problem med licenser](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses) artikel.|
| En specifik konfiguration-alternativet visas inte. | Flera element i Användargränssnittet är dolda tills de behövs. Försök att aktivera alla alternativ som du vill se. |
| Jag kan inte se den **lokal-integrering** fliken. | Det här alternativet bara visas om du har laddat ned Azure AD Connect och har konfigurerat tillbakaskrivning av lösenord. Mer information finns i [komma igång med Azure AD Connect med hjälp av inställningarna för snabb](./connect/active-directory-aadconnect-get-started-express.md). |

## <a name="troubleshoot-password-reset-reporting"></a>Felsöka lösenord återställa reporting

| Fel | Lösning |
| --- | --- |
| Alla lösenord management aktivitetstyper i visas inte i **Self-Service lösenordshantering** audit händelsekategori. | Detta kan inträffa om du inte har en Azure AD Premium eller Basic licens till administratören som utför åtgärden. <br> <br> Du kan lösa problemet genom att tilldela en licens till administratörskontot i fråga. Följ stegen i den [tilldela kontrollerar och lösa problem med licenser](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses) artikel. |
| Användaren registreringar visar flera gånger. | För närvarande när en användare som registrerar logga vi varje enskild typ av data som registrerats som en separat händelse. <br> <br> Om du vill att sammanställa data och har större flexibilitet i hur du kan visa, kan du ladda ned rapporten och öppna data som en pivottabell i Excel.

## <a name="troubleshoot-the-password-reset-registration-portal"></a>Felsöka registreringsportalen för lösenordsåterställning

| Fel | Lösning |
| --- | --- |
| Katalogen är inte aktiverat för återställning av lösenord. **Administratören har inte aktiverat du kan använda den här funktionen.** | Växeln den **lösenordsåterställning via självbetjäning aktiverat** flaggan till **valda** eller **alla** och välj sedan **spara**. |
| Användaren saknar en Azure AD Premium eller Basic-licens. **Administratören har inte aktiverat du kan använda den här funktionen.** | Detta kan inträffa om du inte har en Azure AD Premium eller Basic licens till administratören som utför åtgärden. <br> <br> Du kan lösa problemet genom att tilldela en licens till administratörskontot i fråga. Följ stegen i den [tilldela kontrollerar och lösa problem med licenser](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses) artikel.|
| Det finns ett fel vid bearbetning av begäran. | Detta kan orsakas av många problem, men vanligtvis felet orsakas av ett avbrott eller konfigurationsproblem. Om du ser detta fel och den påverkar ditt företag, kan du kontakta Microsoft support för ytterligare hjälp. |

## <a name="troubleshoot-the-password-reset-portal"></a>Felsökning av portalen för återställning av lösenord

| Fel | Lösning |
| --- | --- |
| Katalogen är inte aktiverat för återställning av lösenord. | Växeln den **lösenordsåterställning via självbetjäning aktiverat** flaggan till **valda** eller **alla** och välj sedan **spara**. |
| Användaren saknar en Azure AD Premium eller Basic-licens. | Detta kan inträffa om du inte har en Azure AD Premium eller Basic licens till administratören som utför åtgärden. <br> <br> Du kan lösa problemet om du tilldelar en licens till administratörskontot i fråga. Följ stegen i den [tilldela kontrollerar och lösa problem med licenser](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses) artikel. |
| Katalogen är aktiverad för återställning av lösenord, men användaren har autentiseringsinformation saknas eller har fel format. | Se till att användaren har formaterats korrekt kontaktdata på filen i katalogen innan du fortsätter. Mer information finns i [återställning av Data som används av Azure AD-lösenordet för självbetjäning](active-directory-passwords-data.md). |
| Katalogen är aktiverad för återställning av lösenord, men användaren har endast en del av kontaktdata när policyn är inställd att kräva två verifieringsmetoderna. | Innan du fortsätter bör du kontrollera att användaren har minst två korrekt konfigurerade kontaktmetoder. Ett exempel är att ha både ett mobiltelefonnummer *och* ett telefonnummer till arbetet. |
| Katalogen är aktiverad för återställning av lösenord och användaren är korrekt konfigurerad, men användaren kan inte kontaktas. | Detta kan bero på fel i en tillfällig tjänst eller om det finns felaktiga kontaktdata som vi kan inte korrekt identifiera. <br> <br> Om användaren ska vänta 10 sekunder, ”försök igen” och ”kontaktar du administratören” visas. Om användaren väljer ”försök igen”, försöker anropet. Om användaren väljer ”kontaktar du administratören”, skickar ett formulär i e-postmeddelande till deras administratörer som begär ett lösenord för att utföras för användarkontot. |
| Användaren får aldrig lösenordsåterställning SMS eller telefonsamtal. | Detta kan bero på ett felaktigt telefonnummer i katalogen. Kontrollera telefonnumret i formatet ”+ kopia xxxyyyzzzzXeeee”. <br> <br> Återställning av lösenord stöder inte tillägg, även om du anger en i katalogen. Tilläggen tas bort innan anropet skickas. Använd ett tal utan ett tillägg eller integrera tillägget i telefonnumret i ditt privata gren exchange (PBX). |
| Användaren får aldrig e-återställning av lösenord. | Den vanligaste orsaken till problemet är att meddelandet har avvisats av ett skräppostfilter. Kontrollera din skräppost, mappen för skräppost eller borttagna objekt för e-postmeddelandet. <br> <br> Se också till att du kontrollerar rätt e-postkontot för meddelandet. |
| Jag har angett en princip för lösenordsåterställning, men principen används inte när ett administratörskontot använder återställning av lösenord. | Microsoft hanterar och kontroller administratörslösenordet Återställ princip för att säkerställa högsta säkerhetsnivå. |
| Användaren inte försöker utföra en lösenordsåterställning för många gånger under en dag. | Vi implementera en automatisk bandbreddsbegränsning mekanism att blockera användare från att försöka återställa sina lösenord för många gånger under en kort tidsperiod. Begränsning inträffar när: <br><ul><li>Användaren försöker verifiera ett telefonnummer 5 gånger under en timme.</li><li>Användaren försöker använda säkerhet frågor gate 5 gånger under en timme.</li><li>Användaren försöker att återställa ett lösenord för samma användarkonto 5 gånger under en timme.</li></ul>Åtgärda problemet genom att be användaren att vänta 24 timmar efter det senaste försöket. Användaren kan sedan återställa sina lösenord. |
| Användaren ser ett fel vid validering av sitt telefonnummer. | Detta fel uppstår när det telefonnummer som angetts inte matchar telefonnumret för filen. Kontrollera att användaren anger fullständiga telefonnummer, inklusive koden området och land när de försöker använda en telefonbaserad metod för återställning av lösenord. |
| Det finns ett fel vid bearbetning av begäran. | Detta kan orsakas av många problem, men vanligtvis felet orsakas av ett avbrott eller konfigurationsproblem. Om du ser detta fel och den påverkar ditt företag, kan du kontakta Microsoft support för ytterligare hjälp. |

## <a name="troubleshoot-password-writeback"></a>Felsöka tillbakaskrivning av lösenord

| Fel | Lösning |
| --- | --- |
| Tjänsten för återställning av lösenord startar inte lokalt. Fel 6800 visas i Azure AD Connect-datorn programmets händelselogg. <br> <br> När onboarding, kan inte federerade eller lösenord-hash-synkroniserade användare återställa sina lösenord. | När tillbakaskrivning av lösenord är aktiverat, anropar Synkroniseringsmotorn tillbakaskrivning av biblioteket för att utföra konfigurationen (onboarding) genom att kommunicera till onboarding-Molntjänsten. Fel uppstod under onboarding eller när du startar Windows Communication Foundation (WCF) slutpunkt för resultat för tillbakaskrivning av lösenord i fel i händelseloggen i Azure AD Connect-datorn. <br> <br> Under omstart av tjänsten Azure AD Sync (ADSync) om tillbakaskrivning konfigurerades startas WCF-slutpunkten. Men om starten av slutpunkten misslyckas, kommer vi logga en händelse 6800 och låta sync-tjänsten startas. Förekomst av den här händelsen innebär att lösenord tillbakaskrivning slutpunkten inte startar. Händelselogginformation för den här händelsen 6800, tillsammans med händelseloggen poster som genereras av komponenten PasswordResetService indikera varför du inte kan starta slutpunkten. Granska felen händelseloggen och försök att starta om Azure AD Connect om tillbakaskrivning av lösenord fortfarande inte fungerar. Om problemet kvarstår försöker du inaktivera och aktivera tillbakaskrivning av lösenord på nytt.
| När en användare försöker att återställa ett lösenord eller låsa upp ett konto med aktiverat tillbakaskrivning av lösenord, misslyckas åtgärden. <br> <br> Dessutom kan du se en händelse i händelseloggen för Azure AD Connect innehåller ”: Synkroniseringsmotorn returnerade ett fel hr = 800700CE, message = filnamnet eller tillägget är för långt” när unlock-åtgärden genomförs. | Sök efter Active Directory-konto för Azure AD Connect och återställa lösenordet så att den innehåller mer än 127 tecken. Öppna den **synkroniseringstjänsten** från den **starta** menyn. Bläddra till **kopplingar** och Sök efter den **Active Directory-kopplingen**. Markera det och sedan välja **egenskaper**. Bläddra till den **autentiseringsuppgifter** sidan och ange det nya lösenordet. Välj **OK** att stänga sidan. |
| Ett felmeddelande som anger att tillbakaskrivning av lösenord inte gick att konfigurera visas på det sista steget i Azure AD Connect-installationen. <br> <br> Azure AD Connect programmets händelselogg innehåller fel 32009 med texten ”fel vid hämtning auth token”. | Detta fel uppstår i följande två fall: <br><ul><li>Du har angett ett felaktigt lösenord för det globala administratörskontot som anges i början av Azure AD Connect-installationen.</li><li>Du försökte använda en federerad användare för det globala administratörskontot som anges i början av Azure AD Connect-installationen.</li></ul> Se till att du inte använder ett federerat konto för den globala administratören som du angav i början av installationen för att åtgärda problemet. Kontrollera också att det angivna lösenordet är korrekt. |
| Händelseloggen för Azure AD Connect-datorn innehåller fel 32002 som genereras genom att köra PasswordResetService. <br> <br> Felet läser: ”fel ansluter till ServiceBus. Tokenleverantören kunde inte tillhandahålla en säkerhetstoken ”. | Din lokala miljö inte kan ansluta till Azure Service Bus-slutpunkten i molnet. Det här felet beror normalt på en brandväggsregel blockerar en utgående anslutning till en viss port eller web-adress. Se [anslutning krav](./connect/active-directory-aadconnect-prerequisites.md) för mer information. När du har uppdaterat reglerna, starta om datorn för Azure AD Connect och tillbakaskrivning av lösenord ska börja fungera igen. |
| Federerade eller lösenord-hash-synkroniserade användare kan inte återställa sina lösenord efter bearbetning under en viss tid. | I sällsynta fall kan tjänsten tillbakaskrivning av lösenord gick inte att starta om när Azure AD Connect har startats om. I dessa fall, kontrollera först om tillbakaskrivning av lösenord verkar vara aktiverat lokalt. Du kan kontrollera med hjälp av Azure AD Connect-guiden eller PowerShell (se avsnittet ovan HowTos). Om funktionen verkar vara aktiverad, försök att aktivera eller inaktivera funktionen igen antingen via Användargränssnittet eller PowerShell. Om det inte fungerar, försök fullständig avinstallation och installera om av Azure AD Connect. |
| Federerade eller lösenord hash synkroniserade användare som försöker återställa sina lösenord finns ett fel när du försökte skicka deras lösenord. Felet indikerar att det uppstod ett problem med tjänsten. <br ><br> Under lösenord Återställ åtgärder, förutom det här problemet kan du ser felet hanteringsagenten nekades åtkomst i din lokala händelseloggar. | Om du ser felen i händelseloggen kontrollerar du att Active Directory Management Agent (ADMA)-konto som har angetts i guiden vid tidpunkten för konfiguration har tillräcklig behörighet för tillbakaskrivning av lösenord. <br> <br> Observera att det kan ta upp till en timme för behörigheterna att sipprar ned när den här behörigheten ges den `sdprop` bakgrundsaktivitet på domänkontrollanten (DC). <br> <br> Behörigheten måste användas på säkerhetsbeskrivningen för objektet vars lösenord återställs för lösenordsåterställning för att fungera. Förrän den här behörigheten visas i användarobjektet fortsätter lösenordsåterställning att misslyckas med ett meddelande om nekad åtkomst. |
| Federerade eller lösenord-hash-synkroniserade användare som försöker återställa sina lösenord, finns ett fel när de skickar sina lösenord. Felet indikerar att det uppstod ett problem med tjänsten. <br> <br> Förutom det här problemet kan under lösenord Återställ åtgärder du se ett fel i händelseloggarna från Azure AD Connect-tjänsten som anger felmeddelandet ”Det gick inte att hitta objektet”. | Det här felet indikerar normalt att Synkroniseringsmotorn gick inte att hitta användarobjektet i Azure AD-anslutningsplatsen eller länkade metaversum (MV) eller Azure AD connector utrymme-objekt. <br> <br> Om du vill felsöka problemet, se till att användaren faktiskt synkroniserad från lokal till Azure AD via den aktuella instansen av Azure AD Connect och kontrollera tillståndet för objekten i kopplingens utrymmen och MV. Bekräfta att objektet Active Directory Certificate Services (AD CS) är anslutet till MV-objekt via regeln ”Microsoft.InfromADUserAccountEnabled.xxx”.|
| Federerade eller lösenord-hash-synkroniserade användare som försöker återställa sina lösenord i ett fel när de skickar sina lösenord. Felet indikerar att det uppstod ett problem med tjänsten. <br> <br> Förutom det här problemet kan under lösenord Återställ åtgärder du se ett fel i händelseloggarna från Azure AD Connect-tjänsten som anger att det finns en ”flera matchningar”-fel. | Detta anger att Synkroniseringsmotorn upptäckte att MV-objekt är ansluten till mer än en AD CS-objekt via ”Microsoft.InfromADUserAccountEnabled.xxx”. Det innebär att användaren har ett aktiverat konto i mer än en skog. Observera att det här scenariot inte stöds för tillbakaskrivning av lösenord. |
| Lösenordsåtgärder misslyckas med ett konfigurationsfel. Programmets händelselogg innehåller Azure AD Connect fel 6329 med texten ”0x8023061f (åtgärden misslyckades eftersom synkronisering av lösenord inte är aktiverat på den här hanteringsagenten)”. | Det här felet uppstår om Azure AD Connect-konfigurationen har ändrats för att lägga till en ny Active Directory-skog (eller om du vill ta bort och lägga till en befintlig skog) efter funktionen tillbakaskrivning av lösenord har redan aktiverats. Lösenordsåtgärder för användare i dessa nyligen lagt till skogar misslyckas. Lös problemet, inaktivera och sedan återaktivera funktionen tillbakaskrivning av lösenord efter konfigurationsändringar skogen har slutförts. |

## <a name="password-writeback-event-log-error-codes"></a>Felkoder för lösenord tillbakaskrivning av händelseloggen

Ett bra tips när du felsöker problem med tillbakaskrivning av lösenord är att kontrollera programhändelseloggen på Azure AD Connect-datorn. Den här händelseloggen innehåller händelser från två källor av intresse för tillbakaskrivning av lösenord. Källan PasswordResetService beskriver åtgärder och problem som rör driften av tillbakaskrivning av lösenord. ADSync källan beskriver åtgärder och problem med att ange lösenord i Active Directory-miljön.

### <a name="if-the-source-of-the-event-is-adsync"></a>Om källan för händelsen är ADSync

| Kod | Namn eller meddelande | Beskrivning |
| --- | --- | --- |
| 6329 | BORGEN: MMS(4924) 0x80230619: ”en begränsning förhindrar att lösenordet ändras i den aktuella som anges”. | Den här händelsen inträffar när tjänsten tillbakaskrivning av lösenord försöker ange ett lösenord för din lokala katalog som inte uppfyller ålder för lösenord, historik, komplexitet eller filtrera kraven i domänen. <br> <br> Om du har en lägsta ålder för lösenord och nyligen har ändrat lösenordet i fönstret tid kan du inte kunna ändra lösenordet igen tills den angivna ålder i din domän. I testsyfte kan ska minimiålder anges till 0. <br> <br> Om du har tidigare lösenordskrav aktiverad och du måste välja ett lösenord som inte har använts under senaste *N* tider var *N* är lösenordet tidigare inställning. Om du väljer ett lösenord som har använts under senaste *N* gånger, och sedan visas ett fel i det här fallet. I testsyfte kan ska tidigare lösenord anges till 0. <br> <br> Om du har kraven på lösenordskomplexitet tillämpas alla när användaren försöker att ändra eller återställa ett lösenord. <br> <br> Om du har aktiverat lösenordsfilter och en användare väljer ett lösenord som uppfyller inte filtreringskriterier sedan återställa eller ändra åtgärden misslyckas. |
| 6329 | MMS(3040): admaexport.cpp(2837): servern inte innehåller principkontroll för LDAP-lösenord. | Det här problemet uppstår om LDAP_SERVER_POLICY_HINTS_OID kontroll (1.2.840.113556.1.4.2066) inte är aktiverad på domänkontrollanter. Om du vill använda funktionen för tillbakaskrivning av lösenord, måste du aktivera kontrollen. Om du vill göra det, måste domänkontrollanter vara på Windows Server 2008 (med den senaste SP) eller senare. Om din domänkontrollanter är 2008 (pre-R2) och sedan måste du också installera snabbkorrigering [KB2386717](http://support.microsoft.com/kb/2386717). |
| HR 8023042 | Synkroniseringsmotorn returnerade ett fel hr = 80230402, message = ett försök att hämta ett objekt misslyckades eftersom det finns duplicerade poster med samma fästpunkt. | Det här felet inträffar när samma användar-ID är aktiverat i flera domäner. Ett exempel är om du synkroniserar konto- och skogar och har samma användar-ID finns och är aktiverad i varje skog. <br> <br> Det här felet kan också inträffa om du använder en icke-unikt fästpunktsattributet som ett alias eller UPN och två användare delar samma fästpunktsattributet. <br> <br> Lös problemet genom att se till att det inte finns några dubbletter av användare inom din domäner och att du använder en unik fästpunktsattributet för varje användare. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Om källan för händelsen är PasswordResetService

| Kod | Namn eller meddelande | Beskrivning |
| --- | --- | --- |
| 31001 | PasswordResetStart | Den här händelsen indikerar att tjänsten lokalt identifierats lösenordsåterställning begäran för en federerad eller lösenord hash synkroniserade användare som kommer från molnet. Den här händelsen är den första händelsen i varje tillbakaskrivningen för återställning av lösenord. |
| 31002 | PasswordResetSuccess | Den här händelsen indikerar att en användare har valt ett nytt lösenord under en åtgärd för återställning av lösenord. Vi har fastställt att det här lösenordet uppfyller företagets lösenordsprincip. Lösenordet har skrivits tillbaka till den lokala Active Directory-miljön. |
| 31003 | PasswordResetFail | Den här händelsen indikerar att en användare har markerat ett lösenord och lösenordet har kommit till den lokala miljön. Men ett fel uppstod när vi försökte ställa in lösenordet i den lokala Active Directory-miljön. Det här felet kan inträffa av olika orsaker: <br><ul><li>Användarens lösenord inte uppfyller ålder, historik och komplexitet eller filtrera krav för domänen. Lös problemet genom att skapa ett nytt lösenord.</li><li>ADMA tjänstkontot saknar behörighet för att ange det nya lösenordet för användarkontot i fråga.</li><li>Användarens konto är i en skyddad grupp, till exempel domän- eller enterprise admin, som inte tillåter lösenord uppsättning åtgärder.</li></ul>|
| 31004 | OnboardingEventStart | Den här händelsen inträffar om du aktiverar tillbakaskrivning av lösenord med Azure AD Connect och vi har startat onboarding organisationen till webbtjänsten för tillbakaskrivning av lösenord. |
| 31005 | OnboardingEventSuccess | Den här händelsen indikerar att onboarding-processen har upprättats och att kapaciteten för tillbakaskrivning av lösenord är redo att användas. |
| 31006 | ChangePasswordStart | Den här händelsen indikerar att tjänsten lokalt identifierats en begäran för en federerad eller lösenord hash synkroniserade användare som kommer från molnet. Den här händelsen är den första händelsen i tillbakaskrivningen varje ändring av lösenord. |
| 31007 | ChangePasswordSuccess | Den här händelsen indikerar att en användare har markerat ett nytt lösenord under en åtgärd för lösenordsbyte fastställde vi att lösenordet uppfyller företagets lösenord och att lösenordet har skrivits tillbaka till den lokala Active Directory-miljön. |
| 31008 | ChangePasswordFail | Den här händelsen indikerar att en användare har valt ett lösenord och att lösenordet har kommit till den lokala miljön, men ett fel uppstod när vi försökte ställa in lösenordet i den lokala Active Directory-miljön. Det här felet kan inträffa av olika orsaker: <br><ul><li>Användarens lösenord inte uppfyller ålder, historik och komplexitet eller filtrera krav för domänen. Lös problemet genom att skapa ett nytt lösenord.</li><li>ADMA tjänstkontot saknar behörighet för att ange det nya lösenordet för användarkontot i fråga.</li><li>Användarens konto är i en skyddad grupp, till exempel domän- eller enterprise administratörer som inte tillåter lösenord uppsättning åtgärder.</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | Tjänsten lokalt upptäckte en lösenordsåterställning begäran för en federerad eller lösenord hash synkroniserade användare från administratören för en användares räkning. Den här händelsen är den första händelsen i varje tillbakaskrivningen för återställning av lösenord som initieras av en administratör. |
| 31010 | ResetUserPasswordByAdminSuccess | Administratören välja ett nytt lösenord under en återställning av lösenord med admin-initierad åtgärd. Vi har fastställt att det här lösenordet uppfyller företagets lösenordsprincip. Lösenordet har skrivits tillbaka till den lokala Active Directory-miljön. |
| 31011 | ResetUserPasswordByAdminFail | Administratören välja ett lösenord för en användares räkning. Lösenordet kommit har till den lokala miljön. Men ett fel uppstod när vi försökte ställa in lösenordet i den lokala Active Directory-miljön. Det här felet kan inträffa av olika orsaker: <br><ul><li>Användarens lösenord inte uppfyller ålder, historik och komplexitet eller filtrera krav för domänen. Försök med ett nytt lösenord för att lösa problemet.</li><li>ADMA tjänstkontot saknar behörighet för att ange det nya lösenordet för användarkontot i fråga.</li><li>Användarens konto är i en skyddad grupp, till exempel domän- eller enterprise administratörer som inte tillåter lösenord uppsättning åtgärder.</li></ul>  |
| 31012 | OffboardingEventStart | Den här händelsen inträffar om du inaktiverar tillbakaskrivning av lösenord med Azure AD Connect och anger att vi startas övervakningsinstrumentpanelen organisationen till webbtjänsten för tillbakaskrivning av lösenord. |
| 31013| OffboardingEventSuccess| Den här händelsen indikerar att övervakningsinstrumentpanelen processen har upprättats och att kapaciteten för tillbakaskrivning av lösenord har inaktiverats. |
| 31014| OffboardingEventFail| Den här händelsen indikerar att övervakningsinstrumentpanelen processen inte lyckades. Detta kan bero på ett behörighetsfel på molnet eller lokalt administratörskonto som har angetts under konfigurationen. Felet kan också inträffa om du försöker använda en global administratör för federerade moln när du inaktiverar tillbakaskrivning av lösenord. Kontrollera din behörighet för att åtgärda problemet och se till att du inte använder ett federerat konto när du konfigurerar kapaciteten för tillbakaskrivning av lösenord.|
| 31015| WriteBackServiceStarted| Den här händelsen indikerar att tjänsten för tillbakaskrivning av lösenord har startats. Det är redo att acceptera lösenord hantering av begäranden från molnet.|
| 31016| WriteBackServiceStopped| Den här händelsen indikerar att tjänsten för tillbakaskrivning av lösenord har stoppats. Lösenord för hantering av förfrågningar från molnet kommer att misslyckas.|
| 31017| AuthTokenSuccess| Den här händelsen indikerar att vi har hämtat en Autentiseringstoken för den globala administratören som angavs vid installationen av Azure AD Connect att starta övervakningsinstrumentpanelen eller onboarding-processen.|
| 31018| KeyPairCreationSuccess| Den här händelsen indikerar att vi har skapat krypteringsnyckel för lösenord. Den här nyckeln används för att kryptera lösenord från molnet skickas till din lokala miljö.|
| 32000| UnknownError| Den här händelsen indikerar ett okänt fel inträffade under en åtgärd för hantering av lösenord. Titta på Undantagstext i händelsen för mer information. Om du får problem, kan du försöka att inaktivera och återaktivera tillbakaskrivning av lösenord. Om det inte hjälper kan innehålla en kopia av händelseloggen tillsammans med spårnings ID angivna insider till supportpersonalen.|
| 32001| ServiceError| Den här händelsen indikerar att det uppstod ett fel vid anslutning till molnet lösenordet återställa tjänsten. Felet uppstår vanligen när den lokala tjänsten kunde inte ansluta till webbtjänsten för återställning av lösenord.|
| 32002| ServiceBusError| Den här händelsen indikerar att det uppstod ett fel vid anslutning till din klient Service Bus-instans. Detta kan inträffa om du blockera utgående anslutningar i din lokala miljö. Kontrollera brandväggen för att se till att tillåta anslutningar via TCP 443 och https://ssprsbprodncu-sb.accesscontrol.windows.net/ och försök sedan igen. Om du fortfarande har problem, kan du försöka att inaktivera och återaktivera tillbakaskrivning av lösenord.|
| 32003| InPutValidationError| Den här händelsen indikerar att inmatningen som skickades till vår-webbtjänstens API var ogiltigt. Försök utföra åtgärden igen.|
| 32004| DecryptionError| Den här händelsen indikerar att ett fel uppstod Dekrypteringen av lösenordet som anlänt från molnet. Detta kan bero på ett dekryptering viktiga matchningsfel mellan Molntjänsten och din lokala miljö. Lös problemet, inaktivera och återaktivera tillbakaskrivning av lösenord i din lokala miljö.|
| 32005| ConfigurationError| Under onboarding spara vi klient-specifik information i en konfigurationsfil i din lokala miljö. Den här händelsen indikerar att det gick inte att spara filen eller när tjänsten startades det uppstod ett fel vid läsning av filen. Försök åtgärda problemet, inaktivera och återaktivera tillbakaskrivning av lösenord att tvinga en omarbetning av konfigurationsfilen.|
| 32007| OnBoardingConfigUpdateError| Under onboarding skicka vi data från molnet till lokalt tjänsten för återställning av lösenord. Dessa data skrivs till en fil i minnet innan det skickas till sync-tjänsten kan lagras på ett säkert sätt på disken. Den här händelsen indikerar att det finns ett problem med skrivning eller uppdatera data i minnet. Försök inaktivera och återaktivera tillbakaskrivning av lösenord att tvinga en omarbetning av konfigurationsfilen för att åtgärda problemet.|
| 32008| ValidationError| Den här händelsen indikerar att vi tagit emot ett ogiltigt svar från webbtjänsten för återställning av lösenord. Försök åtgärda problemet, inaktivera och återaktivera tillbakaskrivning av lösenord.|
| 32009| AuthTokenError| Den här händelsen indikerar inte att det gick att hämta tillstånd token för det globala administratörskontot som angavs vid installationen av Azure AD Connect. Det här felet kan orsakas av ett felaktigt användarnamn eller lösenord som angetts för det globala administratörskontot. Det här felet kan också inträffa om det globala administratörskontot som angetts är federerat. Kör konfigurationen med rätt användarnamn och lösenord för att åtgärda problemet och se till att administratören är ett hanterat konto (endast molnbaserad eller lösenord synkroniseras).|
| 32010| CryptoError| Den här händelsen indikerar att det uppstod ett fel genereras krypteringsnyckel för lösenord eller dekryptera ett lösenord som kommer från Molntjänsten. Det här felet sannolikt tyder på problem med din miljö. Titta på detaljer i händelseloggen för mer information om hur du löser problemet. Du kan också försöka inaktivera och återaktivera tjänsten tillbakaskrivning av lösenord.|
| 32011| OnBoardingServiceError| Den här händelsen indikerar att den lokala tjänsten korrekt inte kunde kommunicera med webbtjänsten för återställning av lösenord för att påbörja onboardingprocessen. Detta kan inträffa på grund av en brandväggsregel eller om det finns ett problem som hämtar en autentisering token för din klient. Se till att du inte blockerar utgående anslutningar via TCP 443 och TCP 9350 9354 eller https://ssprsbprodncu-sb.accesscontrol.windows.net/ för att åtgärda problemet. Också se till att Azure AD-administratörskonto som du använder för att publicera inte federerad.|
| 32013| OffBoardingError| Den här händelsen indikerar att den lokala tjänsten korrekt inte kunde kommunicera med webbtjänsten för återställning av lösenord för att initiera övervakningsinstrumentpanelen-processen. Detta kan inträffa på grund av en brandväggsregel eller om det är problem med komma tillstånd token för din klient. Se till att du inte blockerar utgående anslutningar via 443 eller till https://ssprsbprodncu-sb.accesscontrol.windows.net/ och att Azure Active Directory admin-kontot du använder för att offboard inte federerad för att åtgärda problemet.|
| 32014| ServiceBusWarning| Den här händelsen indikerar att vi var tvungna att försök för att ansluta till din klient Service Bus-instansen. Under normala förhållanden bör detta inte vara ett problem, men om du ser den här händelsen många gånger bör du kontrollera din nätverksanslutning till Service Bus särskilt om det är en tidsfördröjning eller långsam anslutning.|
| 32015| ReportServiceHealthError| För att övervaka hälsotillståndet för din tjänst för tillbakaskrivning av lösenord, skicka vi pulsslag data till vår webbtjänst för återställning av lösenord var femte minut. Den här händelsen indikerar att ett fel uppstod när du skickar det här hälsoinformation tillbaka till web-Molntjänsten. Den här hälsoinformation innehåller inte ett objekt identifierbar information (OII) eller personligt identifierbar information (PII) data och är endast ett pulsslag och grundläggande statistik så att vi kan ge information om status för tjänsten i molnet.|
| 33001| ADUnKnownError| Den här händelsen indikerar att det uppstod ett okänt fel som returneras av Active Directory. Kontrollera i händelseloggen för Azure AD Connect-servern för händelser från ADSync källan för mer information.|
| 33002| ADUserNotFoundError| Den här händelsen indikerar att den användare som försöker att återställa eller ändra ett lösenord inte hittades i den lokala katalogen. Det här felet kan inträffa när användaren har tagits bort lokalt men inte i molnet. Det här felet kan också inträffa om det är problem med synkronisering. Kontrollera din synkroniseringsloggarna och senaste några sync kör detaljer för mer information.|
| 33003| ADMutliMatchError| När en lösenordsåterställning eller ändra begäran kommer från molnet, kan vi använda molnet ankaret anges under installationen av Azure AD Connect för att bestämma hur du länkar begäran tillbaka till en användare i din lokala miljö. Den här händelsen indikerar att påträffades två användare i din lokala katalog med samma fästpunkt attribut för molnet. Kontrollera din synkroniseringsloggarna och senaste några sync kör detaljer för mer information.|
| 33004| ADPermissionsError| Den här händelsen indikerar att tjänstkontot för Active Directory Management Agent (ADMA) inte har rätt behörighet för kontot i fråga att ange ett nytt lösenord. Kontrollera att kontot ADMA i användarens skog har återställt och ändra lösenord behörigheter för alla objekt i skogen. Mer information om hur du anger behörigheter finns i steg 4: Konfigurera Active Directory-behörigheter.|
| 33005| ADUserAccountDisabled| Den här händelsen indikerar att vi försökte återställa eller ändra ett lösenord för ett konto som har inaktiveras lokalt. Aktivera kontot och försök sedan igen.|
| 33006| ADUserAccountLockedOut| Den här händelsen indikerar att vi försökte återställa eller ändra ett lösenord för ett konto som var utelåst lokalt. Utelåsningar kan inträffa när en användare har försökt en ändring eller återställning av lösenord för många gånger under en kort tid. Låsa upp kontot och försök sedan igen.|
| 33007| ADUserIncorrectPassword| Den här händelsen indikerar att användaren angav ett felaktigt aktuella lösenord när du utför ett lösenord ska kunna ändras. Ange det aktuella lösenordet och försök igen.|
| 33008| ADPasswordPolicyError| Den här händelsen inträffar när tjänsten tillbakaskrivning av lösenord försöker ange ett lösenord för din lokala katalog som inte uppfyller ålder för lösenord, historik, komplexitet eller filtrera kraven i domänen. <br> <br> Om du har en lägsta ålder för lösenord och nyligen har ändrat lösenordet i fönstret tid kan du inte kunna ändra lösenordet igen tills den angivna ålder i din domän. I testsyfte kan ska minimiålder anges till 0. <br> <br> Om du har tidigare lösenordskrav aktiverad och du måste välja ett lösenord som inte har använts under senaste *N* tider var *N* är lösenordet tidigare inställning. Om du väljer ett lösenord som har använts under senaste *N* gånger, och sedan visas ett fel i det här fallet. I testsyfte kan ska tidigare lösenord anges till 0. <br> <br> Om du har kraven på lösenordskomplexitet tillämpas alla när användaren försöker att ändra eller återställa ett lösenord. <br> <br> Om du har aktiverat lösenordsfilter och en användare väljer ett lösenord som uppfyller inte filtreringskriterier sedan återställa eller ändra åtgärden misslyckas.|
| 33009| ADConfigurationError| Den här händelsen indikerar att det uppstod ett problem skriva ett lösenord tillbaka till din lokala katalog på grund av konfigurationsproblem med Active Directory. Kontrollera den Azure AD Connect-datorn programloggen för meddelanden från ADSync-tjänsten för mer information där fel uppstod.|

## <a name="troubleshoot-password-writeback-connectivity"></a>Felsöka anslutningsbarheten för tillbakaskrivning av lösenord

Om du upplever störningar i tjänsten med komponenten tillbakaskrivning av lösenord i Azure AD Connect följer här några snabba steg som du kan vidta för att lösa problemet:

* [Kontrollera nätverksanslutningen](#confirm-network-connectivity)
* [Starta om tjänsten Azure AD Connect Sync](#restart-the-azure-ad-connect-sync-service)
* [Inaktivera och återaktivera funktionen tillbakaskrivning av lösenord](#disable-and-re-enable-the-password-writeback-feature)
* [Installera den senaste versionen av Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [Felsöka tillbakaskrivning av lösenord](#troubleshoot-password-writeback)

Om du vill återställa tjänsten på det snabbaste sättet rekommenderar vi i allmänhet kan du utföra stegen i den ordning som beskrivits tidigare.

### <a name="confirm-network-connectivity"></a>Kontrollera nätverksanslutningen

De vanligaste felpunkt är brandväggen och eller proxy portar och timeout för inaktivitet är felaktigt konfigurerade. 

För Azure AD Connect version 1.1.443.0 och senare, du behöver utgående HTTPS åtkomst till följande:

   - passwordreset.microsoftonline.com
   - servicebus.windows.net

För mer detaljerad refererar till den uppdaterade listan med [IP-intervall i Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653) uppdateras varje onsdag och införs följande måndag.

Mer information finns i anslutningen förutsättningar i den [krav för Azure AD Connect](./connect/active-directory-aadconnect-prerequisites.md) artikel.



### <a name="restart-the-azure-ad-connect-sync-service"></a>Starta om tjänsten Azure AD Connect Sync

Starta om tjänsten Azure AD Connect Sync för att lösa problem med nätverksanslutningen eller andra tillfälliga problem med tjänsten:

   1. Som administratör kan du välja **starta** på Azure AD Connect-servern.
   2. Ange **services.msc** i sökfältet och välj **RETUR**.
   3. Leta efter den **Microsoft Azure AD Sync** post.
   4. Högerklicka på tjänstposten, Välj **starta om**, och sedan vänta tills åtgärden är klar.

   ![Starta om tjänsten Azure AD Sync][Service restart]

De här stegen återupprätta anslutningen med Molntjänsten och lös eventuella avbrott som kan uppstå. ADSync-tjänsten startas om inte löser problemet, rekommenderar vi att du försöker att inaktivera och aktivera funktionen för tillbakaskrivning av lösenord på nytt.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Inaktivera och återaktivera funktionen tillbakaskrivning av lösenord

För att lösa problem med nätverksanslutningen, inaktivera och aktivera funktionen för tillbakaskrivning av lösenord på nytt:

   1. Öppna guiden Azure AD Connect-konfiguration som en administratör.
   2. I **Anslut till Azure AD**, ange dina autentiseringsuppgifter för Azure AD-global administratör.
   3. I **Anslut till AD DS**, ange dina autentiseringsuppgifter för AD DS-administratören.
   4. I **identifiera användarna unikt**, Välj den **nästa** knappen.
   5. I **valfria funktioner**avmarkerar den **tillbakaskrivning av lösenord** kryssrutan.
   6. Välj **nästa** via återstående dialogsidorna utan att ändra något tills du kommer till den **redo att konfigurera** sidan.
   7. Se till att den **redo att konfigurera** visar den **tillbakaskrivning av lösenord** alternativet som **inaktiverad** och välj sedan gröna **konfigurera** knappen för att genomföra ändringarna.
   8. I **avslutad**avmarkerar den **synkronisera nu** alternativet och välj sedan **Slutför** att stänga guiden.
   9. Öppna guiden Azure AD Connect-konfiguration.
   10. Upprepa steg 2 – 8, förutom att du väljer den **tillbakaskrivning av lösenord** alternativet på den **valfria funktioner** sidan om du vill aktivera tjänsten igen.

De här stegen återupprätta anslutningen med vår tjänst i molnet och lös eventuella avbrott som kan uppstå.

Inaktivera och återaktivera funktionen tillbakaskrivning av lösenord inte löser problemet, rekommenderar vi att du installerar om Azure AD Connect.

### <a name="install-the-latest-azure-ad-connect-release"></a>Installera den senaste versionen av Azure AD Connect

Installera Azure AD Connect kan lösa konfiguration och problem med nätverksanslutningen mellan våra molntjänster och lokala Active Directory-miljön.

Vi rekommenderar att du utför det här steget endast när du försöker de två första stegen som beskrevs tidigare.

> [!WARNING]
> Om du har anpassat out box sync-regler *säkerhetskopiera dem innan du fortsätter med uppgraderingen och sedan manuellt distribuera dem på nytt när du är klar.*

   1. Hämta den senaste versionen av Azure AD Connect från den [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=615771).
   2. Eftersom du redan har installerat Azure AD Connect måste du utföra en uppgradering på plats för att uppdatera din Azure AD Connect-installationen till den senaste versionen.
   3. Kör det Hämta paketet och följa den på skärmen instruktioner för att uppdatera din Azure AD Connect-datorn.

De här stegen bör återupprätta anslutningen med vår tjänst i molnet och lös eventuella avbrott som kan uppstå.

Installerar den senaste versionen av Azure AD Connect-servern inte löser problemet, rekommenderar vi att du försöker inaktivera och sedan återaktivera tillbakaskrivning av lösenord som ett sista steg när du har installerat den senaste versionen.

## <a name="verify-that-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>Kontrollera att Azure AD Connect har behörigheten som krävs för tillbakaskrivning av lösenord

Azure AD Connect kräver Active Directory **Återställ lösenord** behörighet att utföra tillbakaskrivning av lösenord. Du kan använda funktionen Windows gällande behörigheter för att ta reda på om Azure AD Connect har behörigheten som krävs för ett givet lokala Active Directory-användarkonto:

   1. Logga in på Azure AD Connect-servern och starta den **Synchronization Service Manager** genom att välja **starta** > **synkroniseringstjänsten**.
   2. Under den **kopplingar** väljer du lokalt **Active Directory Domain Services** koppling och välj sedan **egenskaper**.  

   ![Gällande behörigheter – steg 2](./media/active-directory-passwords-troubleshoot/checkpermission01.png)  
  
   3. I popup-fönstret väljer **Anslut till Active Directory-skog** och anteckna den **användarnamn** egenskapen. Den här egenskapen är AD DS-konto som används av Azure AD Connect för att utföra katalogsynkronisering. AD DS-konto måste ha återställa lösenord behörighet för tillbakaskrivning av lösenord för Azure AD Connect.  
   
   ![Gällande behörigheter - steg 3](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
  
   4. Logga in på en domänkontrollant för en lokalt och starta den **Active Directory-användare och datorer** program.
   5. Välj **visa** och kontrollera att den **avancerade funktioner** är aktiverat.  
   
   ![Gällande behörigheter – steg 5](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
  
   6. Sök efter Active Directory-användarkonto som du vill kontrollera. Högerklicka på namnet på kontot och välj **egenskaper**.  
   
   ![Gällande behörigheter - steg 6](./media/active-directory-passwords-troubleshoot/checkpermission04.png) 

   7. I popup-fönstret, gå till den **säkerhet** fliken och markera **Avancerat**.  
   
   ![Gällande behörigheter - steg 7](./media/active-directory-passwords-troubleshoot/checkpermission05.png) 
   
   8. I den **avancerade säkerhetsinställningar för administratören** popup-fönster, gå till den **effektiv åtkomst** fliken.
   9. Välj **väljer en användare**, Välj AD DS-konto som används av Azure AD Connect (se steg 3) och välj sedan **visa effektiv åtkomst**.  
   
   ![Gällande behörigheter - steg 9](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
  
   10. Bläddra nedåt och leta efter **Återställ lösenord**. Om posten är markerat, har AD DS-konto behörighet att återställa lösenordet för det valda Active Directory-användarkontot.  
   
   ![Gällande behörigheter - steg 10](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Azure AD-forum

Om du har en allmän fråga om Azure AD och lösenordsåterställning via självbetjäning, du kan be gemenskapen för att få hjälp på den [Azure AD-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Medlemmar i gruppen innehåller tekniker, projektledare, MVP och andra IT-proffs.

## <a name="contact-microsoft-support"></a>Kontakta Microsoft support

Om du inte kan hitta svaret på ett problem, är vårt supportteam alltid tillgängliga som hjälper dig att ytterligare.

För att hjälpa dig korrekt ber du innehåller så mycket information som möjligt när du öppnar ett ärende. Dessa uppgifter inkluderar:

* **Allmän beskrivning av felet**: Vad är fel? Vad är det beteende som upptäcktes? Hur kan vi återskapa felet? Innehåller så mycket information som möjligt.
* **Sidan**: vilken sida har du på när du har upptäckt fel? Innehåller URL: en om du kunna och en skärmbild av sidan.
* **Stöd för koden**: Vad har stöd för koden som skapades när användaren såg felet?
    * Du hittar den här koden återskapa felet och välj sedan den **stöder kod** länken längst ned på skärmen och skicka supportteknikern GUID som är ett resultat.

    ![Hitta stöd koden längst ned på skärmen][Support code]

    * Om du är på en sida utan en supportkod längst ned, Välj F12 och Sök efter SID och CID och skicka dessa två resultat till supportteknikern.
* **Datum, tid och tidszon**: innehålla exakt datum och tid *med tidszonen* som felet inträffade.
* **Användar-ID**: som var den användare som såg felet? Ett exempel är  *user@contoso.com* .
    * Är detta en federerad användare?
    * Är detta en lösenord-hash-synkroniserade användare?
    * Är detta en endast molnbaserad användare?
* **Licensiering**: tillåter användaren har tilldelats en Azure AD Premium eller Azure AD Basic licens?
* **Programhändelseloggen**: Om du använder tillbakaskrivning av lösenord och felet finns i din lokala infrastruktur, är en komprimerad kopia av Loggboken program från Azure AD Connect-servern.



[Service restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "Starta om tjänsten Azure AD Sync"
[Support code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "Support-koden är placerad längst ned till höger i fönstret"

## <a name="next-steps"></a>Nästa steg

I följande artiklar innehåller ytterligare information om lösenordsåterställning via Azure AD:

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](active-directory-passwords-best-practices.md)
* [Återställ eller ändra ditt lösenord](active-directory-passwords-update-your-own-password.md)
* [Registrera för återställning av lösenord för självbetjäning](active-directory-passwords-reset-register.md)
* [Har du en fråga om licensiering?](active-directory-passwords-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](active-directory-passwords-data.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](active-directory-passwords-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](active-directory-passwords-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](active-directory-passwords-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](active-directory-passwords-how-it-works.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)
