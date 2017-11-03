---
title: "Felsöka: Azure AD SSPR | Microsoft Docs"
description: "Felsöka Azure AD Självbetjäning för lösenordsåterställning"
services: active-directory
keywords: 
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 24b8a9852395c26a40adb406bd706283e1a96d5d
ms.sourcegitcommit: dfd49613fce4ce917e844d205c85359ff093bb9c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/31/2017
---
# <a name="how-to-troubleshoot-self-service-password-reset"></a>Felsökning av lösenordsåterställning via självbetjäning

Om du har problem med lösenordsåterställning via självbetjäning kan objekten som följer hjälpa dig att få saker som arbetar snabbt.

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-may-see"></a>Felsöka självbetjäning lösenordsåterställning som en användare kan se

| Fel | Information | Teknisk information |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Vi beklagar <br> Du kan inte återställa ditt lösenord just nu eftersom din administratör har inaktiverat återställning av lösenord för din organisation. Det finns inga ytterligare åtgärder som du kan vidta för att lösa problemet. Kontakta administratören och be dem att aktivera den här funktionen. Mer information, [hjälp, jag har glömt mitt Azure AD-lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions). | SSPR_0009: Vi har upptäckt att återställning av lösenord inte har aktiverats av administratören. Kontakta administratören och be dem att aktivera återställning av lösenord för din organisation. |
| WritebackNotEnabled = 10 |Vi beklagar <br> Du kan inte återställa ditt lösenord just nu eftersom administratören inte har aktiverat en tjänst som behövs för din organisation. Det finns inga ytterligare åtgärder som du kan vidta för att lösa problemet. Kontakta administratören och be dem att kontrollera konfigurationen för din organisation. Mer information om den här nödvändiga tjänsten [konfigurera tillbakaskrivning av lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-writeback#configuring-password-writeback). | SSPR_0010: Vi har upptäckt att tillbakaskrivning av lösenord inte har aktiverats. Kontakta administratören och be dem att aktivera tillbakaskrivning av lösenord. |
| SsprNotEnabledInUserPolicy = 11 | Vi beklagar  <br> Du kan inte återställa ditt lösenord just nu eftersom din administratör inte har konfigurerat för återställning av lösenord för din organisation. Det finns inga ytterligare åtgärder som du kan vidta för att lösa problemet. Kontakta administratören och be dem att konfigurera återställning av lösenord. Läs mer om lösenord Återställ konfigurationen artikeln [Snabbstart: Azure AD Självbetjäning för lösenordsåterställning](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started). | SSPR_0011: Din organisation har definierat en princip för lösenordsåterställning. Kontakta administratören och be dem att definiera en princip för lösenordsåterställning. |
| UserNotLicensed = 12 | Vi beklagar <br> Du kan inte återställa ditt lösenord just nu eftersom nödvändiga licenser saknas från din organisation. Det finns inga ytterligare åtgärder som du kan vidta för att lösa problemet. Kontakta administratören och be dem att kontrollera licenstilldelning. Läs mer om licensiering artikeln [Licensing krav för Azure AD-lösenordet för självbetjäning återställa](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing). | SSPR_0012: Din organisation har inte licenserna som krävs för att utföra återställning av lösenord. Kontakta administratören och be dem att granska licenstilldelning. |
| UserNotMemberOfScopedAccessGroup = 13 | Vi beklagar <br> Du kan inte återställa ditt lösenord just nu eftersom din administratör inte har konfigurerat ditt konto om du vill använda för lösenordsåterställning. Det finns inga ytterligare åtgärder som du kan vidta för att lösa problemet. Kontakta administratören och be dem att konfigurera ditt konto för återställning av lösenord. Mer information om konfigurationen av kontot för lösenordsåterställning artikeln [lanserar lösenordsåterställning för användare](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices). | SSPR_0012: Du är inte medlem i en grupp som har aktiverats för lösenordsåterställning. Kontakta din administratör och begäran som ska läggas till i gruppen. |
| UserNotProperlyConfigured = 14 | Vi beklagar <br> Du kan inte återställa ditt lösenord just nu eftersom det saknas nödvändig information från ditt konto. Det finns inga ytterligare åtgärder som du kan vidta för att lösa problemet. Kontakta administratören och be dem att återställa lösenordet åt dig. När du har tillgång till ditt konto igen kan du lära dig hur registrera den nödvändiga informationen genom att följa stegen i artikeln [registrera dig för lösenordsåterställning via självbetjäning](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register). | SSPR_0014: Ytterligare säkerhetsinformation behövs för att återställa ditt lösenord. Kontakta administratören och be dem att återställa ditt lösenord för att fortsätta. När du har åtkomst till ditt konto kan du registrera ytterligare säkerhetsinformation på https://aka.ms/ssprsetup. Din administratör kan att lägga till ytterligare säkerhetsinformation till ditt konto genom att följa stegen i [anges, och Läs autentiseringsdata för återställning av lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-data#set-and-read-authentication-data-using-powershell). |
| OnPremisesAdminActionRequired = 29 | Vi beklagar <br> Vi kan inte återställa ditt lösenord just nu på grund av ett problem med din organisations lösenord återställa konfigurationen. Det finns inga ytterligare åtgärder som du kan vidta för att lösa problemet. Kontakta administratören och be dem att undersöka. Mer information om potentiella problem artikeln [felsöka tillbakaskrivning av lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback). | SSPR_0029: Vi kan inte återställa ditt lösenord på grund av ett fel i din lokala konfiguration. Kontakta administratören och be dem att undersöka. |
| OnPremisesConnectivityError = 30 | Vi beklagar <br> Vi kan inte återställa ditt lösenord just nu på grund av problem med nätverksanslutningen för din organisation. Åtgärd ingen att vidta just nu, men problemet kan eventuellt lösas om du försöker igen senare. Om problemet kvarstår, kontakta din administratör och be dem att undersöka. Mer information om problem med nätverksanslutningen läsa [felsöka tillbakaskrivning av lösenord anslutning](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity). | SSPR_0030: Vi kan inte återställa ditt lösenord på grund av en dålig anslutning med din lokala miljö. Kontakta administratören och be dem att undersöka.|


## <a name="troubleshoot-password-reset-configuration-in-the-azure-portal"></a>Felsöka lösenord Återställ konfigurationen i Azure-portalen

| **Fel** | Lösning |
| --- | --- |
| Visas inte i **lösenordsåterställning** avsnitt under Azure AD i Azure-portalen | Detta kan inträffa om du inte har en Azure AD Premium eller Basic licens till administratören som utför åtgärden. <br> Detta kan lösas genom att tilldela en licens till administratörskontot i fråga med hjälp av artikeln [tilldela kontrollerar och lösa problem med licenser](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses) |
| En specifik konfiguration-alternativet visas inte | Flera element i Användargränssnittet är dolda tills behövs. Försök att aktivera alla alternativ som du vill se. |
| Jag kan inte se den **lokal-integrering** fliken | Det här alternativet blir bara visas om du har laddat ned Azure AD Connect och konfigurerat tillbakaskrivning av lösenord. Mer information om det här avsnittet finns i artikeln [komma igång med Azure AD Connect med standardinställningar](./connect/active-directory-aadconnect-get-started-express.md). |

## <a name="troubleshoot-password-reset-reporting"></a>Felsöka lösenord återställa reporting

| **Fel** | Lösning |
| --- | --- |
| Jag kan inte se några lösenord management-aktivitetstyper som visas i den **Self-Service lösenordshantering** audit händelsekategori | Detta kan inträffa om du inte har en Azure AD Premium eller Basic licens till administratören som utför åtgärden. <br> Detta kan lösas genom att tilldela en licens till administratörskontot i fråga med hjälp av artikeln [tilldela kontrollerar och lösa problem med licenser] |
| Användaren registreringar visa flera gånger | För närvarande när en användare som registrerar logga vi just nu varje enskild typ av data som registrerats som en separat händelse. <br> Om du vill att sammanställa data, kan du ladda ned rapporten och öppna data som en pivottabell i excel-om du vill ha mer flexibilitet.

## <a name="troubleshoot-password-reset-registration-portal"></a>Felsöka registreringsportalen för lösenordsåterställning

| **Fel** | Lösning |
| --- | --- |
| Katalogen är inte aktiverad för återställning av lösenord **administratören inte har aktiverat du kan använda den här funktionen** | Växeln den **Self service lösenordsåterställning aktiverat** flaggan till **en grupp** eller **alla** och på **spara** |
| Användaren har inte en Azure AD Premium eller Basic licens **administratören inte har aktiverat du kan använda den här funktionen** | Detta kan inträffa om du inte har en Azure AD Premium eller Basic licens till administratören som utför åtgärden. <br> Detta kan lösas genom att tilldela en licens till administratörskontot i fråga med hjälp av artikeln [tilldela kontrollerar och lösa problem med licenser](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses) |
| Fel vid bearbetning av begäran | Detta kan orsakas av många problem, men vanligtvis felet orsakas av något avbrott eller konfiguration av problem med en tjänst. Om du ser detta fel och den påverkar ditt företag, kan du kontakta Microsoft support för ytterligare hjälp. |

## <a name="troubleshoot-password-reset-portal"></a>Felsökning av lösenordsåterställning, portal

| **Fel** | Lösning |
| --- | --- |
| Katalogen är inte aktiverat för återställning av lösenord. | Växeln den **Self service lösenordsåterställning aktiverat** flaggan till **en grupp** eller **alla** och på **spara** |
| Användaren har inte en Azure AD Premium eller Basic-licens | Detta kan inträffa om du inte har en Azure AD Premium eller Basic licens till administratören som utför åtgärden. <br> Detta kan lösas genom att tilldela en licens till administratörskontot i fråga med hjälp av artikeln [tilldela kontrollerar och lösa problem med licenser](active-directory-licensing-group-assignment-azure-portal.md#step-1-assign-the-required-licenses) |
| Katalogen är aktiverad för återställning av lösenord, men användaren har autentiseringsinformation saknas eller är a-format | Se till att användaren har formaterats korrekt kontaktdata på filen i katalogen innan du fortsätter. Mer information om det här avsnittet finns i artikeln [Data som används av Azure AD Självbetjäning för återställning av lösenord](active-directory-passwords-data.md). |
| Katalogen är aktiverad för återställning av lösenord, men en användare bara har en dataenhet kontakta när policyn är inställd att kräva två verifieringssteg | Kontrollera att användaren har minst två korrekt konfigurerade kontaktmetoder (exempel: mobiltelefon **och** Arbetstelefon) innan du fortsätter. |
| Katalogen är aktiverad för återställning av lösenord, och användaren är korrekt konfigurerad, men användaren kan inte kontaktas | Detta kan vara resultatet av en tillfällig tjänstfel eller felaktig kontaktdata som vi inte kunde identifieras korrekt. <br> <br> Om användaren ska vänta 10 sekunder en försök igen och ”kontaktar du administratören” visas. Klicka på försök igen försöker anropet, genom att klicka på ”kontaktar du administratören” skickar ett formulär i e-postmeddelande till administratörer som begär ett lösenord för att utföras för användarkontot. |
| Användaren får aldrig lösenordsåterställning SMS eller telefonsamtal | Detta kan bero på ett a utformad telefonnummer i katalogen. Kontrollera telefonnumret i formatet ”+ kopia xxxyyyzzzzXeeee”. <br> <br> Återställning av lösenord stöder inte tillägg, även om du anger en i katalogen (de tas bort innan anropet skickas). Försök med ett tal utan ett tillägg, eller att integrera tillägget till telefonnummer i din PBX. |
| Användaren får aldrig lösenordsåterställning av e-post | Den vanligaste orsaken till det här problemet är att meddelandet har avvisats av ett skräppostfilter. Kontrollera din skräppost, mappen för skräppost eller borttagna objekt för e-postmeddelandet. <br> Se också till att du kontrollerar rätt e-post för meddelandet. |
| Jag har angett en princip för lösenordsåterställning, men när ett administratörskontot använder återställning av lösenord, tillämpas inte principen | Microsoft hanterar och kontroller administratörslösenordet Återställ princip för att säkerställa högsta säkerhetsnivå. |
| Användaren som förhindrade försöker utföra för många gånger under en dag för återställning av lösenord | Vi implementera en automatisk bandbreddsbegränsning mekanism att blockera användare från att försöka återställa sina lösenord för många gånger under en kort tidsperiod. Detta inträffar när: <br> 1. Försök att validera ett telefonnummer 5 gånger under en timme. <br> 2. Användaren försöker använda säkerhet frågor gate 5 gånger under en timme. <br> 3. Försök att återställa ett lösenord för samma användarkonto 5 gånger under en timme. <br> Lös problemet genom att be användaren att vänta 24 timmar efter det senaste försöket och användaren kommer sedan att kunna återställa sina lösenord. |
| Användaren ser ett fel vid validering av sitt telefonnummer | Detta fel uppstår när det telefonnummer som angetts inte matchar telefonnumret för filen. Kontrollera att användaren anger fullständiga telefonnummer, inklusive området och land kod, när du försöker använda en telefonbaserad metod för återställning av lösenord. |
| Fel vid bearbetning av begäran | Detta kan orsakas av många problem, men vanligtvis felet orsakas av något avbrott eller konfiguration av problem med en tjänst. Om du ser detta fel och den påverkar ditt företag, kan du kontakta Microsoft support för ytterligare hjälp. |

## <a name="troubleshoot-password-writeback"></a>Felsöka tillbakaskrivning av lösenord

| **Fel** | Lösning |
| --- | --- |
| Lösenord återställa tjänsten startar inte lokalt med fel 6800 i Azure AD Connect-datorn programmets händelselogg. <br> <br> När onboarding, federerad eller lösenords-hash synkroniserade kan inte användare återställa sina lösenord. | När tillbakaskrivning av lösenord är aktiverat, anropar Synkroniseringsmotorn tillbakaskrivning av biblioteket för att utföra konfigurationen (onboarding) genom att tala med onboarding-Molntjänsten. Fel uppstod under onboarding eller när WCF-slutpunkten för tillbakaskrivning av lösenord som resulterar i ett fel i händelseloggen i händelseloggen för din Azure AD Connect-datorn. <br> <br> Under omstart av ADSync-tjänsten om tillbakaskrivning konfigurerades har WCF-slutpunkten startats. Om starten av slutpunkten misslyckas, kommer vi logga en händelse 6800 och låta sync-tjänsten startade. Förekomst av den här händelsen innebär att tillbakaskrivning av lösenord slutpunkten inte har startats. Händelselogginformation för den här händelsen (6800) tillsammans med händelseloggposter generera genom PasswordResetService komponenten anger varför slutpunkten kunde inte startas. Granska felen händelseloggen och försök att starta om Azure AD Connect om tillbakaskrivning av lösenord fortfarande inte fungerar. Om problemet kvarstår försöker du inaktivera och återaktivera tillbakaskrivning av lösenord.
| När en användare försöker att återställa ett lösenord eller låsa upp ett konto med aktiverat tillbakaskrivning av lösenord, misslyckas åtgärden. <br> <br> Dessutom kan du se en händelse i händelseloggen Azure AD Connect innehåller ”: Synkroniseringsmotorn returnerade ett fel hr = 800700CE, message = filnamnet eller tillägget är för lång” när unlock-åtgärden genomförs. | Sök efter Active Directory-konto för Azure AD Connect och återställa lösenordet för att innehålla högst 127 tecken. Öppna sedan synkroniseringstjänsten från Start-menyn. Navigera till kopplingar och hitta Active Directory-kopplingen. Markera den och klicka på Egenskaper. Gå till sidan autentiseringsuppgifter och ange det nya lösenordet. Välj OK om du vill stänga sidan. |
| I det sista steget i Azure AD Connect-installationen visas ett felmeddelande som anger att tillbakaskrivning av lösenord inte att konfigurera. <br> <br> Azure AD Connect programmets händelselogg innehåller fel 32009 med texten ”fel vid hämtning auth token”. | Detta fel uppstår i följande två fall:<br> <br> a. Du har angett ett felaktigt lösenord för det globala administratörskontot som anges i början av Azure AD Connect-installationen.<br> b. Du försökte använda en federerad användare för det globala administratörskontot som anges i början av Azure AD Connect-installationen.<br> <br> Åtgärda felet, se till att du inte använder ett federerat konto för den globala administratören som du angett i början av installationen av Azure AD Connect och att det angivna lösenordet är korrekt. |
| Azure AD Connect-datorn händelseloggen innehåller fel 32002 som genererats av PasswordResetService. <br> <br> Felet läser ”: fel vid anslutning till ServiceBus tokenleverantör kunde inte tillhandahålla en säkerhetstoken...” | Din lokala miljö kan inte ansluta till service bus-slutpunkt i molnet. Det här felet beror normalt på en brandväggsregel blockerar en utgående anslutning till en viss port eller web-adress. Se [anslutning krav](./connect/active-directory-aadconnect-prerequisites.md) för mer information. När du har uppdaterat reglerna, starta om datorn för Azure AD Connect och tillbakaskrivning av lösenord ska börja fungera igen. |
| När arbetar för vissa tid, federerad eller lösenords-hash har synkroniserats kan användare återställa sina lösenord. | I sällsynta fall kan kanske tjänsten tillbakaskrivning av lösenord inte kan starta om när Azure AD Connect har startats om. I dessa fall, kontrollera först om tillbakaskrivning av lösenord verkar vara aktiverade på-plats. Detta kan göras med hjälp av Azure AD Connect-guiden eller powershell (HowTos finns i avsnittet ovan). Om funktionen verkar vara aktiverad, försök att aktivera eller inaktivera funktionen igen antingen via Användargränssnittet eller PowerShell. Om det inte fungerar, försök helt avinstallera och installera om Azure AD Connect. |
| Federerade eller lösenord hash-synkroniserade användare som försöker återställa sina lösenord i ett fel när du skickar det lösenord som du anger det uppstod ett problem med tjänsten. <br ><br> Dessutom visas under lösenord Återställ åtgärder, ett fel om hanteringsagenten nekas åtkomst i händelseloggarna på lokala. | Om du ser felen i händelseloggen kontrollerar du att AD MA-konto (som har angetts i guiden vid tidpunkten för konfiguration) har behörighet för tillbakaskrivning av lösenord. <br> <br> **När behörigheten ges kan det ta upp till 1 timme för behörigheterna att sipprar ned via sdprop bakgrundsaktivitet på domänkontrollanten.** <br> <br> Behörigheten måste användas på säkerhetsbeskrivningen för objektet vars lösenord återställs för lösenordsåterställning för att fungera. Förrän den här behörigheten visas i användarobjektet, fortsätter lösenordsåterställning misslyckas eftersom åtkomst nekades. |
| Federerade eller lösenord hash-synkroniserade användare som försöker återställa sina lösenord i ett fel när du skickar det lösenord som du anger det uppstod ett problem med tjänsten. <br> <br> Förutom detta kan under lösenordsåterställning av åtgärder som visas ett fel i händelseloggarna från Azure AD Connect-tjänsten som anger felmeddelandet ”Det gick inte att hitta objektet”. | Det här felet indikerar normalt att Synkroniseringsmotorn gick inte att hitta antingen användarobjektet i AAD-anslutningsplatsen eller länkade MV- eller AD connector utrymme-objekt. <br> <br> Kontrollera att användaren faktiskt synkroniseras från lokal till AAD via den aktuella instansen av Azure AD Connect och kontrollera tillståndet för objekten i kopplingens utrymmen och MV om du vill felsöka det här. Bekräfta att AD CS-objektet är koppling till MV-objekt via regeln ”Microsoft.InfromADUserAccountEnabled.xxx”.|
| Federerade eller lösenord hash-synkroniserade användare som försöker återställa sina lösenord i ett fel när du skickar det lösenord som du anger det uppstod ett problem med tjänsten. <br> <br> Förutom detta kan under lösenord Återställ åtgärder som visas ett fel i händelseloggarna från Azure AD Connect-tjänsten som anger felet ”flera matchningar”. | Detta anger att Synkroniseringsmotorn har upptäckt att MV-objekt är ansluten till mer än en AD CS-objekt via ”Microsoft.InfromADUserAccountEnabled.xxx”. Det innebär att användaren har ett aktiverat konto i mer än en skog. **Det här scenariot stöds inte för tillbakaskrivning av lösenord.** |
| Lösenordsåtgärder misslyckas med ett konfigurationsfel. Programmets händelselogg innehåller <br> <br> Azure AD Connect fel 6329 med text: 0x8023061f (åtgärden misslyckades eftersom synkronisering av lösenord inte är aktiverat på den här hanteringsagenten.) | Det här inträffar om Azure AD Connect-konfigurationen har ändrats för att lägga till en ny AD-skog (eller om du vill ta bort och readd en befintlig skog) efter funktionen tillbakaskrivning av lösenord har redan aktiverats. Lösenordsåtgärder för användare i ett sådant tillagda nyligen skogar misslyckas. Åtgärda problemet genom att inaktivera och återaktivera funktionen tillbakaskrivning av lösenord efter konfigurationsändringar skogen har slutförts. |

## <a name="password-writeback-event-log-error-codes"></a>Felkoder för lösenord tillbakaskrivning av händelseloggen

Ett bra tips när du felsöker problem med tillbakaskrivning av lösenord är att kontrollera att händelseloggen på Azure AD Connect-datorn. Den här händelseloggen innehåller händelser från två källor av intresse för tillbakaskrivning av lösenord. Källan PasswordResetService beskriver åtgärder och problem relaterade till åtgärden för tillbakaskrivning av lösenord. ADSync källan beskriver åtgärder och problem med att ange lösenord i din AD-miljö.

### <a name="source-of-event-is-adsync"></a>Källan för händelsen är ADSync

| Kod | Namn/meddelande | Beskrivning |
| --- | --- | --- |
| 6329 | BORGEN: MMS(4924) 0x80230619 – ”en begränsning förhindrar att lösenordet ändras i den aktuella som anges”. | Den här händelsen inträffar när tjänsten tillbakaskrivning av lösenord försöker ange ett lösenord för din lokala katalog som inte uppfyller ålder för lösenord, historik, komplexitet eller filtrera kraven i domänen. <br> <br> Om du har en lägsta ålder för lösenord och nyligen har ändrat lösenordet i fönstret tid, går det inte att ändra lösenordet igen tills den angivna ålder i din domän. I testsyfte kan ska minimiålder anges till 0. <br> <br> Om du har tidigare lösenordskrav aktiverad och du måste välja ett lösenord som inte har använts i den senaste tidpunkten för N, där N är inställningen tidigare lösenord. Om du väljer ett lösenord som har använts i den senaste tidpunkten för N måste se du ett fel i det här fallet. I testsyfte kan ska historik anges till 0. <br> <br> Om du har kraven på lösenordskomplexitet tillämpas alla när användaren försöker att ändra eller återställa ett lösenord. <br> <br> Om du har aktiverat lösenordsfilter och en användare väljer ett lösenord som uppfyller inte filtreringskriterier sedan återställa eller ändra åtgärden misslyckas. |
| HR 8023042 | Synkroniseringsmotorn returnerade ett fel hr = 80230402, message = ett försök att hämta ett objekt misslyckades eftersom det finns duplicerade poster med samma fästpunkt | Den här händelsen inträffar när samma användar-id är aktiverat i flera domäner. Det här felet kan till exempel uppstå om du synkroniserar konto/resursskogar och har samma användar-id finns och är aktiverad i varje. <br> <br> Det här felet kan också inträffa om du använder en icke-unikt fästpunktsattributet (till exempel alias eller UPN) och två användare delar samma fästpunktsattributet. <br> <br> Lös problemet genom att se till att du inte har några dubbletter av användare inom din domäner och att du använder en unik fästpunktsattributet för varje användare. |

### <a name="source-of-event-is-passwordresetservice"></a>Källan för händelsen är PasswordResetService

| Kod | Namn/meddelande | Beskrivning |
| --- | --- | --- |
| 31001 | PasswordResetStart | Den här händelsen indikerar att tjänsten lokalt identifierats lösenordsåterställning begäran för en federerad eller lösenord hash-synkroniserade användare från molnet. Den här händelsen är den första händelsen i alla lösenord återställa tillbakaskrivningen. |
| 31002 | PasswordResetSuccess | Den här händelsen indikerar att en användare har markerat ett nytt lösenord under en åtgärd för återställning av lösenord fastställde vi att det här lösenordet uppfyller företagets lösenordsprincip och lösenordet har skrivits tillbaka till den lokala AD-miljön. |
| 31003 | PasswordResetFail | Den här händelsen indikerar att en användare har valt ett lösenord och att lösenordet har kommit till den lokala miljön, men ett fel uppstod när vi försökte ställa in lösenordet i den lokala AD-miljön. Detta kan bero på flera orsaker: <br> <br> Användarens lösenord inte uppfyller ålder, historik och komplexitet eller filtrera krav för domänen. Prova ett helt nytt lösenord för att lösa problemet. <br> <br> MA-tjänstkontot saknar behörighet för att ange det nya lösenordet för användarkontot i fråga. <br> <br> Användarens konto är i en skyddad grupp, till exempel domän- eller enterprise administratörer som inte tillåter lösenord uppsättning åtgärder. |
| 31004 | OnboardingEventStart | Den här händelsen inträffar om du aktiverar tillbakaskrivning av lösenord med Azure AD Connect och anger att vi startas onboarding organisationen till webbtjänsten tillbakaskrivning av lösenord. |
| 31005 | OnboardingEventSuccess | Den här händelsen indikerar onboarding-processen har slutförts och att kapaciteten för tillbakaskrivning av lösenord är redo att användas. |
| 31006 | ChangePasswordStart | Den här händelsen indikerar att tjänsten lokalt identifierats en begäran för en federerad eller lösenord hash-synkroniserade användare från molnet. Den här händelsen är den första händelsen i varje tillbakaskrivning av åtgärd för lösenordsbyte. |
| 31007 | ChangePasswordSuccess | Den här händelsen indikerar att en användare har markerat ett nytt lösenord under en åtgärd för lösenordsbyte fastställde vi att det här lösenordet uppfyller företagets lösenordsprincip och lösenordet har skrivits tillbaka till den lokala AD-miljön. |
| 31008 | ChangePasswordFail | Den här händelsen indikerar att en användare har valt ett lösenord och att lösenordet har kommit till den lokala miljön, men ett fel uppstod när vi försökte ställa in lösenordet i den lokala AD-miljön. Detta kan bero på flera orsaker: <br> <br> Användarens lösenord inte uppfyller ålder, historik och komplexitet eller filtrera krav för domänen. Prova ett helt nytt lösenord för att lösa problemet. <br> <br> MA-tjänstkontot saknar behörighet för att ange det nya lösenordet för användarkontot i fråga. <br> <br> Användarens konto är i en skyddad grupp, till exempel domän- eller enterprise administratörer som inte tillåter lösenord uppsättning åtgärder. |
| 31009 | ResetUserPasswordByAdminStart | Tjänsten lokalt upptäckte en begäran om en federerad för lösenordsåterställning eller lösenords-hash synkroniserade användare från administratören för en användares räkning. Den här händelsen är den första händelsen i varje admin-initierade återställning tillbakaskrivning av lösenord. |
| 31010 | ResetUserPasswordByAdminSuccess | Administratören har valt ett nytt lösenord under en admin-initierade återställning av lösenord, fastställde vi att det här lösenordet uppfyller företagets lösenordsprincip och lösenordet har skrivits tillbaka till den lokala AD-miljön. |
| 31011 | ResetUserPasswordByAdminFail | Administratören har valt ett lösenord för en användares räkning och lösenordet kommit har till den lokala miljön, men ett fel uppstod när vi försökte ställa in lösenordet i den lokala AD-miljön. Detta kan bero på flera orsaker: <br> <br> Användarens lösenord inte uppfyller ålder, historik och komplexitet eller filtrera krav för domänen. Prova ett helt nytt lösenord för att lösa problemet. <br> <br> MA-tjänstkontot saknar behörighet för att ange det nya lösenordet för användarkontot i fråga. <br> <br> Användarens konto är i en skyddad grupp, till exempel domän- eller enterprise administratörer som inte tillåter lösenord uppsättning åtgärder. |
| 31012 | OffboardingEventStart | Den här händelsen inträffar om du inaktiverar tillbakaskrivning av lösenord med Azure AD Connect och anger att vi startas övervakningsinstrumentpanelen organisationen till webbtjänsten tillbakaskrivning av lösenord. |
| 31013| OffboardingEventSuccess| Den här händelsen indikerar övervakningsinstrumentpanelen processen lyckades och att kapaciteten för tillbakaskrivning av lösenord har inaktiverats. |
| 31014| OffboardingEventFail| Den här händelsen indikerar övervakningsinstrumentpanelen processen inte lyckades. Det kan bero på ett behörighetsfel på molnet eller lokalt administratörskonto som har angetts under konfigurationen eller eftersom du försöker att använda en global administratör för federerade moln när du inaktiverar tillbakaskrivning av lösenord. Lös problemet genom att kontrollera dina administrativa behörigheter och att du inte använder någon federerat konto vid konfiguration av kapaciteten för tillbakaskrivning av lösenord.|
| 31015| WriteBackServiceStarted| Den här händelsen indikerar att tjänsten tillbakaskrivning av lösenord har startats och är redo att acceptera lösenord management-begäranden från molnet.|
| 31016| WriteBackServiceStopped| Den här händelsen indikerar att tillbakaskrivning av lösenord-tjänsten har stoppats och att alla begäranden för hantering av lösenord från molnet kommer att misslyckas.|
| 31017| AuthTokenSuccess| Den här händelsen indikerar att vi har hämtat en Autentiseringstoken för den globala administratören som angavs vid installationen av Azure AD Connect att starta övervakningsinstrumentpanelen eller onboarding-processen.|
| 31018| KeyPairCreationSuccess| Den här händelsen indikerar att vi har skapat krypteringsnyckel för lösenord som används för att kryptera lösenord från molnet skickas till din lokala miljö.|
| 32000| UnknownError| Den här händelsen indikerar ett okänt fel under en åtgärd för hantering. Titta på Undantagstext i händelsen för mer information. Om du har problem, försök att inaktivera och aktivera tillbakaskrivning av lösenord. Om det inte hjälper kan innehålla en kopia av händelseloggen tillsammans med spårnings-id som angetts insider till supportpersonalen.|
| 32001| ServiceError| Den här händelsen indikerar att det uppstod ett fel vid anslutning till molnet lösenordet återställa service och sker vanligtvis när den lokala tjänsten kunde inte ansluta till webbtjänsten för återställning av lösenord.|
| 32002| ServiceBusError| Den här händelsen indikerar att det uppstod ett fel vid anslutning till din klient service bus-instans. Detta kan inträffa eftersom du blockerar utgående anslutningar i din lokala miljö. Kontrollera brandväggen för att se till att du tillåter anslutningar via TCP 443 och https://ssprsbprodncu-sb.accesscontrol.windows.net/ och försök igen. Om du fortfarande har problem, försök att inaktivera och aktivera tillbakaskrivning av lösenord.|
| 32003| InPutValidationError| Den här händelsen indikerar att inmatningen som skickades till vår-webbtjänstens API var ogiltigt. Försök utföra åtgärden igen.|
| 32004| DecryptionError| Den här händelsen indikerar att ett fel uppstod Dekrypteringen av lösenordet som anlänt från molnet. Detta kan bero på grund av ett dekrypteringsfel viktiga matchningsfel mellan Molntjänsten och din lokala miljö. För att lösa problemet, inaktivera och återaktivera tillbakaskrivning av lösenord i din lokala miljö.|
| 32005| ConfigurationError| Under onboarding spara vi klient-specifik information i en konfigurationsfil i din lokala miljö. Den här händelsen indikerar det gick inte att spara filen eller att när tjänsten startades det har ett fel läsning av filen. Försök inaktivera och nytt aktivera tillbakaskrivning av lösenord för att tvinga en omarbetning av konfigurationsfilen för att åtgärda problemet.|
| 32007| OnBoardingConfigUpdateError| Under onboarding skicka data från molnet till lokala lösenord återställa tjänsten. Dessa data sedan skrivs till en fil i minnet innan den skickas till sync-tjänsten för att lagra informationen på ett säkert sätt på disk. Den här händelsen tyder på problem med skrivning eller uppdatera data i minnet. Åtgärda problemet försök att inaktivera och nytt aktivera tillbakaskrivning av lösenord för att tvinga en omarbetning av den här konfigurationen.|
| 32008| ValidationError| Den här händelsen indikerar att vi tagit emot ett ogiltigt svar från webbtjänsten för återställning av lösenord. Försök inaktivera och nytt aktivera tillbakaskrivning av lösenord för att åtgärda problemet.|
| 32009| AuthTokenError| Den här händelsen indikerar att det inte gick att hämta tillstånd token för det globala administratörskontot som angavs vid installationen av Azure AD Connect. Det här felet kan orsakas av ett felaktigt användarnamn eller lösenord som angetts för det globala administratörskontot eller eftersom det globala administratörskontot har angetts är federerat. Om du vill åtgärda det här problemet, kör med rätt användarnamn och lösenord och se till administratören ett hanterat konto (endast molnbaserad eller lösenord synkroniseras).|
| 32010| CryptoError| Den här händelsen indikerar ett fel uppstod när lösenordet krypteringsnyckeln eller dekryptera ett lösenord som kommer från Molntjänsten. Det här felet sannolikt indikerar ett problem med din miljö. Titta på detaljer i händelseloggen för mer information och lösa problemet. Du kan också prova att inaktivera och återaktivera tjänsten tillbakaskrivning av lösenord för att lösa problemet.|
| 32011| OnBoardingServiceError| Den här händelsen indikerar att den lokala tjänsten inte kunde korrekt kommunicera med webbtjänsten lösenord Återställ att inleda onboarding-processen. Detta kan bero på en brandväggsregel eller hämta en token för autentisering för din klient. Lös problemet genom att du inte blockerar utgående anslutningar via TCP 443 och TCP 9350 9354 eller https://ssprsbprodncu-sb.accesscontrol.windows.net/ och som inte är federerat AAD-administratörskonto som du använder för att publicera.|
| 32013| OffBoardingError| Den här händelsen indikerar att den lokala tjänsten inte kunde korrekt kommunicera med webbtjänsten lösenord Återställ att påbörja övervakningsinstrumentpanelen-processen. Detta kan bero på en brandväggsregel eller hämta ett Autentiseringstoken för din klient. Lös problemet genom att se till att du inte blockerar utgående anslutningar via 443 eller till https://ssprsbprodncu-sb.accesscontrol.windows.net/ och att AAD admin-konto du använder för att offboard inte är federerat.|
| 32014| ServiceBusWarning| Den här händelsen indikerar att vi var tvungna att försök för att ansluta till din klient service bus-instans. Under normala förhållanden bör detta inte vara ett problem, men om du ser den här händelsen många gånger bör du kontrollera din nätverksanslutning till service bus särskilt om det är en hög fördröjning eller långsam anslutning.|
| 32015| ReportServiceHealthError| För att övervaka hälsotillståndet för tillbakaskrivning av lösenord tjänsten skicka pulsslag data till vår lösenord återställa webbtjänsten var femte minut. Den här händelsen indikerar att ett fel uppstod när du skickar det här hälsoinformation tillbaka till web-Molntjänsten. Den här hälsoinformation innehåller inte en OII eller personligt Identifierbar information och är endast ett pulsslag och grundläggande statistik så att vi kan ge information om status för tjänsten i molnet.|
| 33001| ADUnKnownError| Den här händelsen indikerar att det uppstod ett okänt fel som returneras av AD bör du kontrollera i händelseloggen för Azure AD Connect-servern för händelser från ADSync källan för mer information om felet.|
| 33002| ADUserNotFoundError| Den här händelsen indikerar att den användare som försöker att återställa eller ändra ett lösenord inte hittades i den lokala katalogen. Detta kan inträffa när användaren har tagits bort lokalt men inte i molnet, eller om det finns ett problem med synkronisering. Loggarna synkronisering och den senaste några synkroniseringen kör mer information.|
| 33003| ADMutliMatchError| När en lösenordsåterställning eller ändra begäran kommer från molnet, kan vi använda molnet ankaret anges under installationen av Azure AD Connect för att bestämma hur du länkar begäran tillbaka till en användare i din lokala miljö. Den här händelsen indikerar att påträffades två användare i din lokala katalog med samma fästpunkt attribut för molnet. Loggarna synkronisering och den senaste några synkroniseringen kör mer information.|
| 33004| ADPermissionsError| Den här händelsen indikerar att hanteringsagenten tjänstkontot inte har rätt behörighet för kontot i fråga att ange ett nytt lösenord. Kontrollera att kontot MA i användarens skog har behörigheter för återställning och ändra lösenord för alla objekt i skogen. Mer information om hur göra detta, finns i steg 4: Konfigurera Active Directory-behörigheter.|
| 33005| ADUserAccountDisabled| Den här händelsen indikerar att vi försökte återställa eller ändra ett lösenord för ett konto som har inaktiverats lokalt. Aktivera kontot och försök sedan igen.|
| 33006| ADUserAccountLockedOut| Händelse som anger att vi försökte återställa eller ändra ett lösenord för ett konto som var utelåst lokalt. Utelåsningar kan inträffa när en användare har försökt en ändring eller återställning av lösenord för många gånger under en kort tid. Låsa upp kontot och försök sedan igen.|
| 33007| ADUserIncorrectPassword| Den här händelsen indikerar att användaren angav ett felaktigt aktuella lösenord när du utför ett lösenord ska kunna ändras. Ange det aktuella lösenordet och försök igen.|
| 33008| ADPasswordPolicyError| Den här händelsen inträffar när tjänsten tillbakaskrivning av lösenord försöker ange ett lösenord för din lokala katalog som inte uppfyller ålder för lösenord, historik, komplexitet eller filtrera kraven i domänen. <br> <br> Om du har en lägsta ålder för lösenord och nyligen har ändrat lösenordet i fönstret tid, går det inte att ändra lösenordet igen tills den angivna ålder i din domän. I testsyfte kan ska minimiålder anges till 0. <br> <br> Om du har tidigare lösenordskrav aktiverad och du måste välja ett lösenord som inte har använts i den senaste tidpunkten för N, där N är inställningen tidigare lösenord. Om du väljer ett lösenord som har använts i den senaste tidpunkten för N måste se du ett fel i det här fallet. I testsyfte kan ska historik anges till 0. <br> <br> Om du har kraven på lösenordskomplexitet tillämpas alla när användaren försöker att ändra eller återställa ett lösenord. <br> <br> Om du har aktiverat lösenordsfilter och en användare väljer ett lösenord som uppfyller inte filtreringskriterier sedan återställa eller ändra åtgärden misslyckas.|
| 33009| ADConfigurationError| Den här händelsen indikerar att det uppstod ett problem skriva ett lösenord tillbaka till din lokala katalog på grund av konfigurationsproblem med Active Directory. Kontrollera den Azure AD Connect-datorn programloggen för meddelanden från ADSync-tjänsten för mer information om vilka fel uppstod.|

## <a name="troubleshoot-password-writeback-connectivity"></a>Felsöka anslutningsbarheten för tillbakaskrivning av lösenord

Om det uppstår avbrott i tjänsten med komponenten tillbakaskrivning av lösenord i Azure AD Connect följer här några snabba steg som du kan vidta för att lösa problemet:

* [Kontrollera nätverksanslutningen](#confirm-network-connectivity)
* [Starta om Azure AD Connect-synkroniseringstjänsten](#restart-the-azure-ad-connect-sync-service)
* [Inaktivera och återaktivera funktionen tillbakaskrivning av lösenord](#disable-and-re-enable-the-password-writeback-feature)
* [Installera den senaste versionen av Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [Felsök tillbakaskrivning av lösenord](#troubleshoot-password-writeback)

I allmänhet rekommenderar vi att du kör de här stegen i ordningen som ovan för att återställa din tjänst med snabbaste sättet.

### <a name="confirm-network-connectivity"></a>Kontrollera nätverksanslutningen

De vanligaste felpunkt är brandväggen och eller proxy portar och timeout för inaktivitet är felaktigt konfigurerade. Granska anslutning krav i artikeln [krav för Azure AD Connect](./connect/active-directory-aadconnect-prerequisites.md) för mer information.

### <a name="restart-the-azure-ad-connect-sync-service"></a>Starta om Azure AD Connect-synkroniseringstjänsten

Starta om den Azure AD Connect-synkroniseringstjänsten hjälper dig för att lösa problem med nätverksanslutningen eller andra tillfälliga problem med tjänsten.

1. Som administratör, klickar du på **starta** på servern som kör **Azure AD Connect**.
2. Typen **”services.msc”** i sökrutan och tryck på **RETUR**.
3. Leta efter den **Microsoft Azure AD Sync** post.
4. Högerklicka på tjänstposten, klicka på **starta om**, och vänta tills åtgärden har slutförts.

   ![Starta om tjänsten Azure AD Sync][Service Restart]

De här stegen återupprätta anslutningen med Molntjänsten och lös eventuella avbrott som du kan ha problem. Om Sync-tjänsten startas om inte löser problemet, rekommenderar vi att du försöker att inaktivera och återaktivera funktionen tillbakaskrivning av lösenord som ett nästa steg.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Inaktivera och återaktivera funktionen tillbakaskrivning av lösenord

Inaktivera och återaktivera funktionen tillbakaskrivning av lösenord kan hjälpa till att lösa problem med nätverksanslutningen.

1. Som administratör, öppna den **Azure AD Connect-konfigurationsguiden**.
2. På den **Anslut till Azure AD** dialogrutan, ange din **autentiseringsuppgifter för global administratör för Azure AD**
3. På den **Anslut till AD DS** dialogrutan, ange din **AD DS-administratörsautentiseringsuppgifter**.
4. På den **identifiera användarna unikt** dialogrutan klickar du på den **nästa** knappen.
5. På den **valfria funktioner** dialogrutan avmarkera den **tillbakaskrivning av lösenord** kryssrutan.
6. Klicka på **nästa** via återstående dialogsidorna utan att ändra något tills du kommer till den **redo att konfigurera** sidan.
7. Se till att konfigurera sidan visar den **tillbakaskrivning lösenordsalternativet som inaktiverade** och klicka sedan på gröna **konfigurera** för att genomföra ändringarna.
8. På den **avslutad** dialogrutan avmarkera den **synkronisera nu** alternativ och klickar sedan på **Slutför** att stänga guiden.
9. Öppna den **Azure AD Connect-konfigurationsguiden**.
10. **Upprepa steg 2 – 8**, förutom att se till att du **Kontrollera alternativet tillbakaskrivning av lösenord** på den **valfria funktioner** skärmen för att aktivera tjänsten igen.

De här stegen återupprätta anslutningen med vår tjänst i molnet och Lös avbrott som du kan ha problem.

Inaktivera och återaktivera funktionen tillbakaskrivning av lösenord inte löser problemet, rekommenderar vi att du försöker installera om Azure AD Connect som ett nästa steg.

### <a name="install-the-latest-azure-ad-connect-release"></a>Installera den senaste versionen av Azure AD Connect

Installera Azure AD Connect kan lösa konfiguration och problem med nätverksanslutningen mellan våra molntjänster och din lokala AD-miljö.

Vi rekommenderar att du utför det här steget efter försöker de två första stegen som beskrivs ovan.

> [!WARNING]
> Om du har anpassat out-of-box sync regler **säkerhetskopiera dessa innan du fortsätter med uppgraderingen och distribuera dem manuellt när du är klar**.

1. Hämta den senaste versionen av Azure AD Connect från den [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=615771).
2. Eftersom du redan har installerat Azure AD Connect som du behöver utföra en uppgradering på plats för att uppdatera din Azure AD Connect-installationen till den senaste versionen.
3. Kör det Hämta paketet och följa den på skärmen instruktioner för att uppdatera din Azure AD Connect-datorn.

De här stegen återupprätta anslutningen med vår tjänst i molnet och lös eventuella avbrott som du kan ha problem.

Installera den senaste versionen av Azure AD Connect-servern inte löser problemet, rekommenderar vi att du inaktiverar och aktiverar nytt tillbakaskrivning av lösenord som ett sista steg när du har installerat den senaste versionen.

## <a name="verify-whether-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>Kontrollera om Azure AD Connect har behörigheten som krävs för tillbakaskrivning av lösenord
Azure AD Connect kräver AD **Återställ lösenord** behörighet att utföra tillbakaskrivning av lösenord. Ta reda på om Azure AD Connect har behörighet för en given lokala AD användarkontot, kan du använda funktionen Windows gällande behörigheter:

1. Logga in på Azure AD Connect-servern och starta den **Synchronization Service Manager** (Start → synkroniseringstjänsten).
2. Under den **kopplingar** väljer du lokalt **AD-koppling** och på **egenskaper**.  
![Gällande behörigheter – steg 2](./media/active-directory-passwords-troubleshoot/checkpermission01.png)  
3. I popup-fönstret väljer du den **Anslut till Active Directory-skog** fliken och notera den **användarnamn** egenskapen. Detta är AD DS-konto som används av Azure AD Connect för att utföra katalogsynkronisering. AD DS-konto måste ha Återställ lösenord behörighet för tillbakaskrivning av lösenord för Azure AD Connect.  
![Gällande behörigheter - steg 3](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
4. Logga in på en domänkontrollant för en lokalt och starta den **Active Directory-användare och datorer** program.
5. Klicka på **visa** och se till att **avancerade funktioner** är aktiverat.  
![Gällande behörigheter – steg 5](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
6. Leta efter AD användarkontot som du vill kontrollera. Högerklicka på kontot och välj **egenskaper**.  
![Gällande behörigheter - steg 6](./media/active-directory-passwords-troubleshoot/checkpermission04.png) 
7. I popup-fönstret, går du till den **säkerhet** och klicka på **Avancerat**.  
![Gällande behörigheter - steg 7](./media/active-directory-passwords-troubleshoot/checkpermission05.png) 
8. I popup-dialogrutan Avancerade säkerhetsinställningar går du till den **effektiv åtkomst** fliken.
9. Klicka på **väljer en användare** och välj AD DS-konto som används av Azure AD Connect (se steg 3). Klicka på **visa effektiv åtkomst**.  
![Gällande behörigheter - steg 9](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
10. Bläddra nedåt och leta efter **Återställ lösenord**. Om posten är markerad, innebär det att AD DS-konto har behörighet att återställa lösenordet för det valda kontot för AD-användare.  
![Gällande behörigheter - steg 10](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Azure AD-forum

Om du har en allmän fråga om Azure AD och lösenordsåterställning via självbetjäning, du kan be gemenskapen för att få hjälp på den [Azure AD-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Medlemmar i gruppen innehåller tekniker, projektledare, MVP och andra IT-proffs.

## <a name="contact-microsoft-support"></a>Kontakta Microsoft support

Om du inte kan hitta svaret på ett problem är vårt supportteam alltid tillgängliga som hjälper dig att ytterligare.

Om du vill kunna bistå be du innehåller så mycket information som möjligt när du öppnar ett ärende inklusive:

* **Allmän beskrivning av felet** -vad är fel? Vad är det beteende som upptäcktes? Hur kan vi återskapa felet? Ange så mycket information som möjligt.
* **Sidan** -vilken sida har du på när du har upptäckt fel? Ange URL: en om du ska kunna och en skärmbild.
* **Stöd för koden** -vad har stöd för koden som genererats när användaren såg felet?
    * Om du vill hitta detta återskapa felet, och sedan klicka på länken stöder kod längst ned på skärmen och skicka supportteknikern GUID som är ett resultat.
    ![Hitta stöd koden längst ned på skärmen][Support Code]
    * Om du är på en sida utan en supportkod längst ned, trycka på F12 och Sök efter SID och CID och skicka dessa två resultat till supportteknikern.
* **Datum, tid och tidszon** -inkludera exakt datum och tid **med tidszonen** som felet inträffade.
* **Användar-ID** -som var den användare som såg felet? (user@contoso.com)
    * Är detta en federerad användare?
    * Är detta en lösenord hash-synkroniserade användare?
    * Är detta en enda användaren i molnet?
* **Licensiering** -tillåter användaren har tilldelats en Azure AD Premium eller Azure AD Basic licens?
* **Programhändelseloggen** - om du använder tillbakaskrivning av lösenord och felet finns i du lokal infrastruktur, är en komprimerad kopia av Loggboken program från Azure AD Connect-servern när du kontaktar supporten.



[Service Restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "Starta om tjänsten Azure AD Sync"
[Support Code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "Stöd för koden är placerad längst ned till höger i fönstret"

## <a name="next-steps"></a>Nästa steg

Följande länkar ger ytterligare information om lösenordsåterställning med Azure AD

* [Hur jag för att slutföra en lyckad distribution av SSPR?](active-directory-passwords-best-practices.md)
* [Återställa eller ändra ditt lösenord](active-directory-passwords-update-your-own-password.md).
* [Registrera dig för lösenordsåterställning via självbetjäning](active-directory-passwords-reset-register.md).
* [Har du en fråga med licensiering?](active-directory-passwords-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla för dina användare?](active-directory-passwords-data.md)
* [Vilka autentiseringsmetoder som är tillgängliga för användarna?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Vad är policyalternativen med SSPR?](active-directory-passwords-policy.md)
* [Vad är tillbakaskrivning av lösenord och varför jag är intresserad av den?](active-directory-passwords-writeback.md)
* [Hur rapporterar på aktivitet i SSPR?](active-directory-passwords-reporting.md)
* [Vad är alla alternativ i SSPR och vad de betyder?](active-directory-passwords-how-it-works.md)
* [Jag har en fråga som inte var motsvarar någon annan](active-directory-passwords-faq.md)
