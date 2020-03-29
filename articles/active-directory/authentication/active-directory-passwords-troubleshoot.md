---
title: Felsöka återställning av lösenord för självbetjäning – Azure Active Directory
description: Felsöka återställning av lösenord för Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a22c0cc922e021edc37dfbb2d89fdd20c77b2c87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848773"
---
# <a name="troubleshoot-self-service-password-reset"></a>Felsöka självbetjäning av lösenordsåterställning

Har du problem med Azure Active Directory (Azure AD) självbetjäningslösenordsåterställning (SSPR)? Den information som följer kan hjälpa dig att få saker och ting att fungera igen.

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-might-see"></a>Felsöka fel på lösenordsåterställningsfel som en användare kan se

| Fel | Information | Teknisk information |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Vi beklagar att du inte kan återställa lösenordet just nu eftersom administratören har inaktiverat återställning av lösenord för din organisation. Det finns inga ytterligare åtgärder som du kan vidta för att lösa denna situation. Kontakta administratören och be dem aktivera den här funktionen. Mer information finns i [Hjälpen, jag har glömt mitt Azure AD-lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions). | SSPR_0009: Vi har upptäckt att återställning av lösenord inte har aktiverats av administratören. Kontakta administratören och be dem aktivera återställning av lösenord för din organisation. |
| ÅterskrivningAnmälbar = 10 |Vi beklagar att du inte kan återställa lösenordet just nu eftersom administratören inte har aktiverat en nödvändig tjänst för din organisation. Det finns inga ytterligare åtgärder som du kan vidta för att lösa denna situation. Kontakta administratören och be dem kontrollera organisationens konfiguration. Mer information om den här nödvändiga tjänsten finns i [Konfigurera tillbakaskrivning av lösenord](howto-sspr-writeback.md). | SSPR_0010: Vi har upptäckt att tillbakaskrivning av lösenord inte har aktiverats. Kontakta din administratör och be dem att aktivera tillbakaskrivning av lösenord. |
| SsprNotEnabledInUserPolicy = 11 | Vi beklagar att du inte kan återställa lösenordet just nu eftersom administratören inte har konfigurerat återställning av lösenord för din organisation. Det finns inga ytterligare åtgärder som du kan vidta för att lösa denna situation. Kontakta administratören och be dem konfigurera återställning av lösenord. Mer information om konfiguration för återställning av lösenord finns i [Snabbstart: Azure AD-återställning av lösenord med självbetjäning](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started). | SSPR_0011: Din organisation har inte definierat en princip för återställning av lösenord. Kontakta administratören och be dem att definiera en princip för återställning av lösenord. |
| UserNotLicensed = 12 | Vi beklagar att du inte kan återställa lösenordet just nu eftersom nödvändiga licenser saknas i din organisation. Det finns inga ytterligare åtgärder som du kan vidta för att lösa denna situation. Kontakta din administratör och be dem att kontrollera din licenstilldelning. Mer information om licensiering finns i [Licenskrav för azure ad-självbetjäningslösenordsåterställning](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing). | SSPR_0012: Din organisation har inte de licenser som krävs för att utföra återställning av lösenord. Kontakta administratören och be dem granska licenstilldelningarna. |
| AnvändareInteMemberOfScopedAccessGroup = 13 | Vi beklagar att du inte kan återställa lösenordet just nu eftersom administratören inte har konfigurerat ditt konto för att använda återställning av lösenord. Det finns inga ytterligare åtgärder som du kan vidta för att lösa denna situation. Kontakta administratören och be dem konfigurera ditt konto för återställning av lösenord. Mer information om kontokonfiguration för återställning av lösenord finns i [Konfigurera återställning av lösenord för användare](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices). | SSPR_0013: Du är inte medlem i en grupp som är aktiverad för återställning av lösenord. Kontakta administratören och begär att läggas till i gruppen. |
| UserNotProperlyConfigured = 14 | Vi beklagar att du inte kan återställa ditt lösenord just nu eftersom nödvändig information saknas i ditt konto. Det finns inga ytterligare åtgärder som du kan vidta för att lösa denna situation. Kontakta dig admin och be dem att återställa ditt lösenord för dig. När du har tillgång till ditt konto igen måste du registrera nödvändig information. Om du vill registrera information följer du stegen i artikeln [Registrera dig för återställning av lösenord för självbetjäning.](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register) | SSPR_0014: Ytterligare säkerhetsinformation behövs för att återställa ditt lösenord. Om du vill fortsätta kontaktar du administratören och ber dem återställa lösenordet. När du har tillgång till ditt konto kan https://aka.ms/ssprsetupdu registrera ytterligare säkerhetsinformation på . Administratören kan lägga till ytterligare säkerhetsinformation i ditt konto genom att följa stegen i [Ange och läsa autentiseringsdata för återställning av lösenord](howto-sspr-authenticationdata.md). |
| OnPremisesAdminActionRequired = 29 | Vi beklagar att vi inte kan återställa lösenordet just nu på grund av ett problem med organisationens konfiguration för återställning av lösenord. Det finns inga ytterligare åtgärder som du kan vidta för att lösa denna situation. Kontakta din administratör och be dem att undersöka. Mer information om det potentiella problemet finns i [Felsöka tillbakaskrivning av lösenord](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback). | SSPR_0029: Vi kan inte återställa ditt lösenord på grund av ett fel i din lokala konfiguration. Kontakta din administratör och be dem att undersöka. |
| OnPremisesConnectivityError = 30 | Vi beklagar att vi inte kan återställa ditt lösenord just nu på grund av anslutningsproblem till din organisation. Det finns ingen åtgärd att vidta just nu, men problemet kan vara löst om du försöker igen senare. Om problemet kvarstår kontaktar du administratören och ber dem undersöka saken. Mer information om anslutningsproblem finns i [Felsöka återställning av lösenordsåterskrivning](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity). | SSPR_0030: Vi kan inte återställa ditt lösenord på grund av en dålig anslutning till din lokala miljö. Kontakta administratören och be dem undersöka saken.|

## <a name="troubleshoot-the-password-reset-configuration-in-the-azure-portal"></a>Felsöka konfigurationen för återställning av lösenord i Azure-portalen

| Fel | Lösning |
| --- | --- |
| Jag ser inte avsnittet **Återställning av lösenord** under Azure AD i Azure-portalen. | Detta kan inträffa om du inte har tilldelats en Azure AD-licens till administratören som utför åtgärden. <br> <br> Tilldela en licens till administratörskontot i fråga. Du kan följa stegen i artikeln [Tilldela, verifiera och lösa problem med licenser.](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)|
| Jag ser inget visst konfigurationsalternativ. | Många element i användargränssnittet är dolda tills de behövs. Prova att aktivera alla alternativ som du vill se. |
| Jag ser inte fliken **Lokal integration.** | Det här alternativet blir bara synligt om du har hämtat Azure AD Connect och har konfigurerat tillbakaskrivning av lösenord. Mer information finns i [Komma igång med Azure AD Connect med hjälp av expressinställningarna](../hybrid/how-to-connect-install-express.md). |

## <a name="troubleshoot-password-reset-reporting"></a>Felsöka rapportering om återställning av lösenord

| Fel | Lösning |
| --- | --- |
| Jag ser inga aktivitetstyper för lösenordshantering i kategorin **Självbetjäningsgranskning av lösenordshantering.** | Detta kan inträffa om du inte har tilldelats en Azure AD-licens till administratören som utför åtgärden. <br> <br> Du kan lösa problemet genom att tilldela en licens till administratörskontot i fråga. Följ stegen i artikeln [Tilldela, verifiera och lösa problem med licenser.](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) |
| Användarregistreringar visas flera gånger. | När en användare registrerar loggar vi för närvarande varje enskild data som är registrerad som en separat händelse. <br> <br> Om du vill aggregera dessa data och ha större flexibilitet i hur du kan visa den kan du hämta rapporten och öppna data som en pivottabell i Excel.

## <a name="troubleshoot-the-password-reset-registration-portal"></a>Felsöka registreringsportalen för registrering av lösenord

| Fel | Lösning |
| --- | --- |
| Katalogen är inte aktiverad för återställning av lösenord. **Administratören har inte aktiverat dig för att använda den här funktionen.** | Växla flaggan **Självbetjäningslösenordsåterställning** till **Markerad** eller **Alla** och välj sedan **Spara**. |
| Användaren har inte tilldelats en Azure AD-licens. **Administratören har inte aktiverat dig för att använda den här funktionen.** | Detta kan inträffa om du inte har tilldelats en Azure AD-licens till administratören som utför åtgärden. <br> <br> Du kan lösa problemet genom att tilldela en licens till administratörskontot i fråga. Följ stegen i artikeln [Tilldela, verifiera och lösa problem med licenser.](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)|
| Det går inte att bearbeta begäran. | Detta kan orsakas av många problem, men i allmänhet orsakas det här felet av antingen ett avbrott i tjänsten eller ett konfigurationsproblem. Om det här felet visas och det påverkar ditt företag kontaktar du Microsoft-supporten för ytterligare hjälp. |

## <a name="troubleshoot-the-password-reset-portal"></a>Felsöka portalen för återställning av lösenord

| Fel | Lösning |
| --- | --- |
| Katalogen är inte aktiverad för återställning av lösenord. | Växla flaggan **Självbetjäningslösenordsåterställning** till **Markerad** eller **Alla** och välj sedan **Spara**. |
| Användaren har inte tilldelats en Azure AD-licens. | Detta kan inträffa om du inte har tilldelats en Azure AD-licens till administratören som utför åtgärden. <br> <br> Du kan lösa problemet om du tilldelar en licens till administratörskontot i fråga. Följ stegen i artikeln [Tilldela, verifiera och lösa problem med licenser.](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) |
| Katalogen är aktiverad för återställning av lösenord, men användaren har information om saknad eller felaktig autentisering. | Innan du fortsätter bör du se till att användaren har korrekt format kontaktdata på filen i katalogen. Mer information finns i [Data som används av Azure AD självbetjäningslösenordsåterställning](howto-sspr-authenticationdata.md). |
| Katalogen är aktiverad för återställning av lösenord, men användaren har bara en del av kontaktdata på filen när principen är inställd på att kräva två verifieringsmetoder. | Innan du fortsätter bör du se till att användaren har minst två korrekt konfigurerade kontaktmetoder. Ett exempel är att ha både ett mobiltelefonnummer *och* ett telefonnummer till kontoret. |
| Katalogen är aktiverad för återställning av lösenord och användaren är korrekt konfigurerad, men användaren kan inte kontaktas. | Detta kan bero på ett tillfälligt tjänstfel eller om det finns felaktiga kontaktdata som vi inte kan identifiera korrekt. <br> <br> Om användaren väntar i 10 sekunder visas "försök igen" och "kontakta administratörslänkarna". Om användaren väljer "försök igen" försöker den igen. Om användaren väljer "kontakta administratören" skickas ett e-postmeddelande till administratörer och begär att ett lösenord återställs för det användarkontot. |
| Användaren får aldrig sms:et eller telefonsamtalet för lösenordsåterställning. | Detta kan bero på ett felaktigt telefonnummer i katalogen. Kontrollera att telefonnumret är i formatet "+ccc xxxyyyzzzzXeeee". <br> <br> Återställning av lösenord stöder inte tillägg, även om du anger ett i katalogen. Tilläggen tas bort innan samtalet skickas. Använd ett nummer utan tillägg eller integrera tillägget i telefonnumret i ditt privata filialutbyte (PBX). |
| Användaren får aldrig e-postmeddelandet om återställning av lösenord. | Den vanligaste orsaken till det här problemet är att meddelandet avvisas av ett skräppostfilter. Kontrollera mappen skräppost, skräppost eller borttagna objekt för e-postmeddelandet. <br> <br> Se också till att du kontrollerar rätt e-postkonto för meddelandet. |
| Jag har angett en princip för återställning av lösenord, men när ett administratörskonto använder återställning av lösenord tillämpas inte den principen. | Microsoft hanterar och styr principen för återställning av administratörslösenord för att säkerställa högsta möjliga säkerhet. |
| Användaren hindras från att försöka återställa ett lösenord för många gånger under en dag. | Vi implementerar en automatisk begränsningsmekanism för att blockera användare från att försöka återställa sina lösenord för många gånger på kort tid. Begränsning sker när: <br><ul><li>Användaren försöker validera ett telefonnummer fem gånger på en timme.</li><li>Användaren försöker använda säkerhetsfrågorna gate fem gånger på en timme.</li><li>Användaren försöker återställa ett lösenord för samma användarkonto fem gånger på en timme.</li></ul>Lös problemet genom att instruera användaren att vänta 24 timmar efter det senaste försöket. Användaren kan sedan återställa sitt lösenord. |
| Användaren ser ett fel när han validerar sitt telefonnummer. | Det här felet uppstår när det angivna telefonnumret inte matchar det angivna telefonnumret. Kontrollera att användaren anger hela telefonnumret, inklusive rikt- och landskoden, när de försöker använda en telefonbaserad metod för återställning av lösenord. |
| Det går inte att bearbeta begäran. | Detta kan orsakas av många problem, men i allmänhet orsakas det här felet av antingen ett avbrott i tjänsten eller ett konfigurationsproblem. Om det här felet visas och det påverkar ditt företag kontaktar du Microsoft-supporten för ytterligare hjälp. |
| Lokala policyöverträdelser | Lösenordet uppfyller inte den lokala lösenordsprincipen i Active Directory. |
| Lösenordet följer inte fuzzy policy | Lösenordet som användes visas i [listan över förbjudna lösenord](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad#how-are-passwords-evaluated) och får inte användas. |

## <a name="troubleshoot-password-writeback"></a>Felsöka återställning av lösenord

| Fel | Lösning |
| --- | --- |
| Tjänsten för återställning av lösenord startar inte lokalt. Fel 6800 visas i Azure AD Connect-datorns programhändelselogg. <br> <br> Efter introduktion kan federerade, direktautentisering eller lösenords-hash-synkroniserade användare inte återställa sina lösenord. | När tillbakaskrivning av lösenord är aktiverat anropar synkroniseringsmotorn återskrivningsbiblioteket för att utföra konfigurationen (onboarding) genom att kommunicera med molnanknäckningstjänsten. Eventuella fel som påträffas under introduktionen eller när slutpunkten Windows Communication Foundation (WCF) startas för återställning av lösenord resulterar i fel i händelseloggen på din Azure AD Connect-dator. <br> <br> Under omstart av ADSync-tjänsten (Azure AD Sync) startar WCF-slutpunkten. Men om start av slutpunkten misslyckas loggar vi händelse 6800 och låter synkroniseringstjänsten starta. Förekomsten av den här händelsen innebär att slutpunkten för tillbakaskrivning av lösenord inte startade. Information om händelselogg för den här händelsen 6800, tillsammans med händelseloggposter som genereras av komponenten PasswordResetService, anger varför du inte kan starta slutpunkten. Granska dessa händelseloggfel och försök att starta om Azure AD Connect om tillbakaskrivning av lösenord fortfarande inte fungerar. Om problemet kvarstår försöker du inaktivera och aktiverar sedan tillbaka lösenordet.
| När en användare försöker återställa ett lösenord eller låsa upp ett konto med tillbakaskrivning av lösenord aktiverat misslyckas åtgärden. <br> <br> Dessutom visas en händelse i Azure AD Connect-händelseloggen som innehåller: "Synkroniseringsmotorn returnerade ett fel hr=800700CE, message=Filnamnet eller tillägget är för långt" efter att upplåsningsåtgärden har inträffat. | Hitta Active Directory-kontot för Azure AD Connect och återställ lösenordet så att det inte innehåller fler än 256 tecken. Öppna sedan **synkroniseringstjänsten** på **Start-menyn.** Bläddra till **kopplingar** och hitta **Active Directory Connector**. Markera den och välj sedan **Egenskaper**. Bläddra till sidan **Autentiseringsuppgifter** och ange det nya lösenordet. Välj **OK** för att stänga sidan. |
| I det sista steget i installationen av Azure AD Connect visas ett fel som anger att tillbakaskrivning av lösenord inte kunde konfigureras. <br> <br> Händelseloggen för Azure AD Connect-program innehåller fel 32009 med texten "Fel att hämta auth-token". | Det här felet uppstår i följande två fall: <br><ul><li>Du har angett ett felaktigt lösenord för det globala administratörskonto som angavs i början av installationen av Azure AD Connect.</li><li>Du har försökt använda en federerad användare för det globala administratörskonto som angavs i början av installationen av Azure AD Connect.</li></ul> Lös problemet genom att kontrollera att du inte använder ett federerat konto för den globala administratör som du angav i början av installationsprocessen. Se också till att det angivna lösenordet är korrekt. |
| Händelseloggen för Azure AD Connect-datorn innehåller fel 32002 som genereras genom att köra PasswordResetService. <br> <br> Felet lyder: "Fel anslutning till ServiceBus. Tokenprovidern kunde inte tillhandahålla en säkerhetstoken." | Din lokala miljö kan inte ansluta till Slutpunkten för Azure Service Bus i molnet. Det här felet orsakas normalt av en brandväggsregel som blockerar en utgående anslutning till en viss port eller webbadress. Se [Anslutningsförutsättning](../hybrid/how-to-connect-install-prerequisites.md) för mer information. När du har uppdaterat dessa regler ska du starta om Azure AD Connect-datorn och tillbakaskrivning av lösenord ska börja fungera igen. |
| Efter att ha arbetat en tid kan federerade, direktautentisering eller lösenords-hash-synkroniserade användare inte återställa sina lösenord. | I vissa sällsynta fall kan återställningstjänsten för lösenord misslyckas med att starta om när Azure AD Connect har startats om. I dessa fall kontrollerar du först om tillbakaskrivning av lösenord verkar vara aktiverat lokalt. Du kan kontrollera genom att använda azure AD Connect-guiden eller PowerShell (se föregående HowTos-avsnitt). Om funktionen verkar vara aktiverad kan du försöka aktivera eller inaktivera funktionen igen antingen via användargränssnittet eller PowerShell. Om detta inte fungerar kan du prova en fullständig avinstallation och ominstallation av Azure AD Connect. |
| Federerade, direktautentisering eller lösenords-hash-synkroniserade användare som försöker återställa sina lösenord ser ett fel efter att ha försökt skicka in sitt lösenord. Felet indikerar att det uppstod ett serviceproblem. <br ><br> Utöver det här problemet kan du under åtgärder för återställning av lösenord se ett felmeddelande om att hanteringsagenten nekades åtkomst i dina lokala händelseloggar. | Om du ser dessa fel i händelseloggen kontrollerar du att Adma-kontot (Active Directory Management Agent) som angavs i guiden vid tidpunkten för konfigurationen har de behörigheter som krävs för återställning av lösenord. <br> <br> När den här behörigheten har getts kan det ta upp till `sdprop` en timme innan behörigheterna sipprar ned via bakgrundsuppgiften på domänkontrollanten (DC). <br> <br> För att lösenordsåterställning ska fungera måste behörigheten stämplas på säkerhetsbeskrivningen för det användarobjekt vars lösenord återställs. Tills den här behörigheten visas på användarobjektet fortsätter återställningen av lösenord att misslyckas med ett meddelande om nekad åtkomst. |
| Federerade, direktautentisering eller lösenords-hash-synkroniserade användare som försöker återställa sina lösenord, se ett fel när de har skickat in sitt lösenord. Felet indikerar att det uppstod ett serviceproblem. <br> <br> Utöver det här problemet kan du under åtgärder för återställning av lösenord se ett fel i händelseloggarna från Azure AD Connect-tjänsten som anger felet "Objektet kunde inte hittas". | Det här felet anger vanligtvis att synkroniseringsmotorn inte kan hitta vare sig användarobjektet i Azure AD-anslutningsutrymmet eller det länkade metaversum (MV) eller Azure AD-anslutningsutrymmesobjektet. <br> <br> Om du vill felsöka det här problemet kontrollerar du att användaren verkligen synkroniseras från lokalt till Azure AD via den aktuella instansen av Azure AD Connect och inspekterar tillståndet för objekten i anslutningsutrymmena och MV. Bekräfta att AD CS-objektet (Active Directory Certificate Services) är anslutet till MV-objektet via regeln "Microsoft.InfromADUserAccountEnabled.xxx".|
| Federerade, direktautentisering eller lösenords-hash-synkroniserade användare som försöker återställa sina lösenord ser ett fel när de har skickat in sitt lösenord. Felet indikerar att det uppstod ett serviceproblem. <br> <br> Utöver det här problemet kan du under åtgärder för återställning av lösenord se ett fel i händelseloggarna från Azure AD Connect-tjänsten som anger att det finns ett "Flera matchningar hittades"-fel. | Detta indikerar att synkroniseringsmotorn upptäckte att MV-objektet är anslutet till mer än ett AD CS-objekt via "Microsoft.InfromADUserAccountEnabled.xxx". Det innebär att användaren har ett aktiverat konto i mer än en skog. Det här scenariot stöds inte för tillbakaskrivning av lösenord. |
| Lösenordsåtgärder misslyckas med ett konfigurationsfel. Programhändelseloggen innehåller Azure AD Connect-fel 6329 med texten "0x8023061f (Åtgärden misslyckades eftersom lösenordssynkronisering inte är aktiverad på den här hanteringsagenten)". | Det här felet uppstår om Azure AD Connect-konfigurationen ändras för att lägga till en ny Active Directory-skog (eller för att ta bort och läsa en befintlig skog) när återställningsfunktionen för lösenord redan har aktiverats. Lösenordsåtgärder för användare i dessa nyligen tillagda skogar misslyckas. Åtgärda problemet genom att inaktivera och sedan återaktivera tillbakaskrivningsfunktionen för lösenord när ändringarna av skogskonfigurationen har slutförts. |

## <a name="password-writeback-event-log-error-codes"></a>Felkoder för återställning av lösenordslogg

En bra metod när du felsöker problem med tillbakaskrivning av lösenord är att granska programhändelseloggen på din Azure AD Connect-dator. Den här händelseloggen innehåller händelser från två intressekällor för tillbakaskrivning av lösenord. Källan PasswordResetService beskriver åtgärder och problem relaterade till driften av tillbakaskrivning av lösenord. ADSync-källan beskriver åtgärder och problem relaterade till att ange lösenord i Active Directory-miljön.

### <a name="if-the-source-of-the-event-is-adsync"></a>Om källan till händelsen är ADSync

| Kod | Namn eller meddelande | Beskrivning |
| --- | --- | --- |
| 6329 | BAIL: MMS(4924) 0x80230619: "En begränsning förhindrar att lösenordet ändras till den aktuella som anges." | Den här händelsen inträffar när tjänsten för tillbakaskrivning av lösenord försöker ange ett lösenord på den lokala katalogen som inte uppfyller kraven för lösenordsålder, historik, komplexitet eller filtrering för domänen. <br> <br> Om du har en lägsta ålder för lösenord och nyligen har ändrat lösenordet inom det fönstret kan du inte ändra lösenordet igen förrän det når den angivna åldern i domänen. För testning bör minimiåldern fastställas till 0. <br> <br> Om du har aktiverat krav på lösenordshistorik måste du välja ett lösenord som inte har använts under de senaste *N-tiderna,* där *N* är inställningen för lösenordshistorik. Om du väljer ett lösenord som har använts under de senaste *N-tiderna* visas ett fel i det här fallet. För testning bör lösenordshistoriken anges till 0. <br> <br> Om du har krav på lösenordskomplexitet tillämpas alla när användaren försöker ändra eller återställa ett lösenord. <br> <br> Om du har aktiverat lösenordsfilter och en användare väljer ett lösenord som inte uppfyller filtreringsvillkoren misslyckas återställningen eller ändringen. |
| 6329 | MMS(3040): admaexport.cpp(2837): Servern innehåller inte LDAP-lösenordsprincipkontrollen. | Det hÃ¤r problemet uppstÃ¥r om LDAP_SERVER_POLICY_HINTS_OID -kontroll (1.2.840.113556.1.4.2066) inte är aktiverat pÃ¥ DC.. Om du vill använda funktionen för tillbakaskrivning av lösenord måste du aktivera kontrollen. För att kunna göra detta måste DC:erna finnas på Windows Server 2008R2 eller senare. |
| HR 8023042 | Synkroniseringsmotorn returnerade ett fel hr=80230402, message=Ett försök att hämta ett objekt misslyckades eftersom det finns dubblettposter med samma ankare. | Det här felet uppstår när samma användar-ID är aktiverat i flera domäner. Ett exempel är om du synkroniserar konto- och resursskogar och har samma användar-ID närvarande och aktiverat i varje skog. <br> <br> Det här felet kan också uppstå om du använder ett icke-unikt ankarattribut, till exempel ett alias eller UPN, och två användare delar samma ankarattribut. <br> <br> Lös problemet genom att se till att du inte har några duplicerade användare inom domänerna och att du använder ett unikt ankarattribut för varje användare. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Om källan till händelsen är PasswordResetService

| Kod | Namn eller meddelande | Beskrivning |
| --- | --- | --- |
| 31001 | PasswordResetStart | Den här händelsen anger att den lokala tjänsten har upptäckt en begäran om återställning av lösenord för en federerad, direktautentisering eller lösenords-hash-synkroniserad användare som kommer från molnet. Den här händelsen är den första händelsen i varje tillbakaskrivningsåtgärd för återställning av lösenord. |
| 31002 | PasswordResetSuccess | Den här händelsen anger att en användare har valt ett nytt lösenord under en lösenordsåterställning. Vi bestämde att det här lösenordet uppfyller företagets lösenordskrav. Lösenordet har skrivits tillbaka till den lokala Active Directory-miljön. |
| 31003 | PasswordResetFail | Den här händelsen anger att en användare har valt ett lösenord och att lösenordet har anlänt till den lokala miljön. Men när vi försökte ange lösenordet i den lokala Active Directory-miljön uppstod ett fel. Detta fel kan inträffa av flera skäl: <br><ul><li>Användarens lösenord uppfyller inte kraven för ålder, historik, komplexitet eller filter för domänen. Lös problemet genom att skapa ett nytt lösenord.</li><li>Adma-tjänstkontot har inte rätt behörighet för att ange det nya lösenordet för användarkontot i fråga.</li><li>Användarens konto finns i en skyddad grupp, till exempel domän- eller företagsadministratörsgrupp, som inte tillåter lösenordsuppsättningsåtgärder.</li></ul>|
| 31004 | OnboardingEventStart | Den här händelsen inträffar om du aktiverar tillbakaskrivning av lösenord med Azure AD Connect och vi har börjat introduktion till din organisation till webbtjänsten för tillbakaskrivning av lösenord. |
| 31005 | OnboardingEventSuccess | Den här händelsen anger att introduktionsprocessen lyckades och att återställningsfunktionen för lösenord är klar att användas. |
| 31006 | ÄndraPasswordStart | Den här händelsen anger att den lokala tjänsten har upptäckt en begäran om lösenordsändring för en federerad, direktautentisering eller lösenords-hash-synkroniserad användare som kommer från molnet. Den här händelsen är den första händelsen i varje tillbakaskrivningsåtgärd för lösenordsändring. |
| 31007 | ChangePasswordS | Den här händelsen anger att en användare valde ett nytt lösenord under en lösenordsändring, vi bestämde att lösenordet uppfyller företagets lösenordskrav och att lösenordet har skrivits tillbaka till den lokala Active Directory-miljön. |
| 31008 | ÄndraPasswordFail | Den här händelsen anger att en användare har valt ett lösenord och att lösenordet har kommit fram till den lokala miljön, men när vi försökte ange lösenordet i den lokala Active Directory-miljön uppstod ett fel. Detta fel kan inträffa av flera skäl: <br><ul><li>Användarens lösenord uppfyller inte kraven för ålder, historik, komplexitet eller filter för domänen. Lös problemet genom att skapa ett nytt lösenord.</li><li>Adma-tjänstkontot har inte rätt behörighet för att ange det nya lösenordet för användarkontot i fråga.</li><li>Användarens konto finns i en skyddad grupp, till exempel domän- eller företagsadministratörer, som inte tillåter lösenordsuppsättningsåtgärder.</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | Den lokala tjänsten upptäckte en begäran om återställning av lösenord för en federerad, genomströmningsautentisering eller lösenords-hash-synkroniserad användare som kommer från administratören för en användares räkning. Den här händelsen är den första händelsen i varje tillbakaskrivningsåtgärd för återställning av lösenord som initieras av en administratör. |
| 31010 | ResetUserPasswordByAdminSuccess | Administratören valde ett nytt lösenord under en administratörsinitierad lösenordsåterställning. Vi bestämde att det här lösenordet uppfyller företagets lösenordskrav. Lösenordet har skrivits tillbaka till den lokala Active Directory-miljön. |
| 31011 | ResetUserPasswordByAdminFail | Administratören valde ett lösenord för en användares räkning. Lösenordet kom till den lokala miljön. Men när vi försökte ange lösenordet i den lokala Active Directory-miljön uppstod ett fel. Detta fel kan inträffa av flera skäl: <br><ul><li>Användarens lösenord uppfyller inte kraven för ålder, historik, komplexitet eller filter för domänen. Prova ett nytt lösenord för att lösa problemet.</li><li>Adma-tjänstkontot har inte rätt behörighet för att ange det nya lösenordet för användarkontot i fråga.</li><li>Användarens konto finns i en skyddad grupp, till exempel domän- eller företagsadministratörer, som inte tillåter lösenordsuppsättningsåtgärder.</li></ul>  |
| 31012 | OffboardingEventStart | Den här händelsen inträffar om du inaktiverar tillbakaskrivning av lösenord med Azure AD Connect och anger att vi har börjat ta bort din organisation till webbtjänsten för tillbakaskrivning av lösenord. |
| 31013| OffboardingEventSuccess| Den här händelsen anger att offboarding-processen lyckades och att återställning av lösenord har inaktiverats. |
| 31014| OffboardingEventFail| Den här händelsen anger att offboarding-processen inte lyckades. Detta kan bero på ett behörighetsfel i molnet eller det lokala administratörskontot som anges under konfigurationen. Felet kan också uppstå om du försöker använda en federerad moln global administratör när du inaktiverar tillbakaskrivning av lösenord. Du åtgärdar problemet genom att kontrollera dina administratörsbehörigheter och se till att du inte använder ett federerat konto när du konfigurerar tillbakaskrivningsfunktionen för lösenord.|
| 31015| Skriva tillbakaTjänstStartad| Den här händelsen anger att tjänsten för tillbakaskrivning av lösenord har startats. Den är redo att acceptera begäranden om lösenordshantering från molnet.|
| 31016| WriteBackServiceStopped| Den här händelsen anger att tjänsten för tillbakaskrivning av lösenord har stoppats. Alla begäranden om lösenordshantering från molnet kommer inte att lyckas.|
| 31017| AuthTokenSuccess| Den här händelsen anger att vi har hämtat en auktoriseringstoken för den globala administratör som angavs under Azure AD Connect-installationen för att starta hämtnings- eller introduktionsprocessen.|
| 31018| KeyPairCreationSuccess| Den här händelsen indikerar att vi har skapat krypteringsnyckeln för lösenord. Den här nyckeln används för att kryptera lösenord från molnet som ska skickas till din lokala miljö.|
| 32000| Okändare| Den här händelsen indikerar att ett okänt fel uppstod under en lösenordshantering. Titta på undantagstexten i händelsen för mer information. Om du har problem kan du prova att inaktivera och sedan återaktivera tillbakaskrivning av lösenord. Om detta inte hjälper, inkludera en kopia av din händelselogg tillsammans med det spårnings-ID som angetts för insider till din supporttekniker.|
| 32001| ServiceError (avsändare)| Den här händelsen indikerar att det uppstod ett fel när du anslöt till tjänsten för återställning av lösenord i molnet. Det här felet uppstår vanligtvis när den lokala tjänsten inte kunde ansluta till webbtjänsten för återställning av lösenord.|
| 32002| ServiceBusError| Den här händelsen indikerar att det uppstod ett fel när du anslöt till klientens Service Bus-instans. Detta kan inträffa om du blockerar utgående anslutningar i din lokala miljö. Kontrollera brandväggen för att se till att du tillåter anslutningar https://ssprdedicatedsbprodncu.servicebus.windows.netvia TCP 443 och till och försök sedan igen. Om du fortfarande har problem kan du prova att inaktivera och sedan återaktivera tillbakaskrivning av lösenord.|
| 32003| InPutValidationError| Den här händelsen indikerar att indata som skickades till vårt webbtjänst API var ogiltig. Försök igen.|
| 32004| DekrypteringSerror| Den här händelsen indikerar att det uppstod ett fel i dekryptering av lösenordet som kom från molnet. Detta kan bero på en dekrypteringsnyckel som inte stämmer överens mellan molntjänsten och den lokala miljön. LÃ¶s problemet genom att inaktivera och sedan återaktivera tillbakadejt med lösenord i lokal miljö.|
| 32005| ConfigurationError| Under introduktionen sparar vi klientspecifik information i en konfigurationsfil i din lokala miljö. Den här händelsen anger att det uppstod ett fel när filen sparades eller att det uppstod ett fel när filen startades. Lös problemet genom att inaktivera och sedan återaktivera tillbakaskrivning av lösenord för att tvinga fram en omskrivning av konfigurationsfilen.|
| 32007| OnBoardingConfigUpdateError| Under introduktionen skickar vi data från molnet till den lokala tjänsten för återställning av lösenord. Dessa data skrivs sedan till en minnesfil innan de skickas till synkroniseringstjänsten för att lagras säkert på disken. Den här händelsen indikerar att det är problem med att skriva eller uppdatera dessa data i minnet. Lös problemet genom att inaktivera och sedan återaktivera tillbakaskrivning av lösenord för att tvinga fram en omskrivning av den här konfigurationsfilen.|
| 32008| Valideringserror| Den här händelsen indikerar att vi fick ett ogiltigt svar från webbtjänsten för återställning av lösenord. Lös problemet genom att inaktivera och sedan återaktivera tillbakaskrivning av lösenord.|
| 32009| AuthTokenError| Den här händelsen indikerar att vi inte kunde få en auktoriseringstoken för det globala administratörskonto som angavs under Azure AD Connect-installationen. Det här felet kan orsakas av ett felaktigt användarnamn eller lösenord som angetts för det globala administratörskontot. Det här felet kan också uppstå om det angivna globala administratörskontot är federerat. Lös problemet genom att köra konfigurationen igen med rätt användarnamn och lösenord och se till att administratören är ett hanterat konto (endast molnet eller lösenordssynkronisering).|
| 32010| CryptoError (kryptoror)| Den här händelsen indikerar att det uppstod ett fel när lösenordskrypteringsnyckeln genererades eller dekrypterar ett lösenord som kommer från molntjänsten. Det här felet indikerar troligen ett problem med din miljö. Titta på information om din händelselogg om du vill veta mer om hur du löser problemet. Du kan också prova att inaktivera och sedan återaktivera tjänsten för tillbakaskrivning av lösenord.|
| 32011| OnBoardingServiceError| Den här händelsen indikerar att den lokala tjänsten inte kunde kommunicera korrekt med webbtjänsten för återställning av lösenord för att initiera introduktionsprocessen. Detta kan inträffa som ett resultat av en brandväggsregel eller om det finns ett problem med att hämta en autentiseringstoken för din klient. Lös problemet genom att kontrollera att du inte blockerar utgående anslutningar via TCP 443 och TCP 9350-9354 eller till https://ssprdedicatedsbprodncu.servicebus.windows.net. Se också till att Azure AD-administratörskontot som du använder för att skriva in inte är federerat.|
| 32013| OffBoardingError| Den här händelsen indikerar att den lokala tjänsten inte kunde kommunicera korrekt med webbtjänsten för återställning av lösenord för att initiera hämtningsprocessen. Detta kan inträffa som ett resultat av en brandväggsregel eller om det finns ett problem med att hämta en auktoriseringstoken för din klient. Lös problemet genom att kontrollera att du inte blockerar utgående anslutningar över https://ssprdedicatedsbprodncu.servicebus.windows.net443 eller till och att azure Active Directory-administratörskontot som du använder för att stänga av dig inte är federerat.|
| 32014| ServiceBusWarning| Den här händelsen indikerar att vi var tvungna att försöka ansluta till din klientklientinstans. Under normala förhållanden bör detta inte vara ett problem, men om du ser den här händelsen många gånger bör du överväga att kontrollera nätverksanslutningen till Service Bus, särskilt om det är en anslutning med hög latens eller låg bandbredd.|
| 32015| ReportServiceHealthError| För att övervaka hälsan hos din tillbakaskrivningstjänst för lösenord skickar vi pulsslagsdata till vår webbtjänst för återställning av lösenord var femte minut. Den här händelsen indikerar att det uppstod ett fel när den här hälsoinformationen skickades tillbaka till molnwebbtjänsten. Denna hälsoinformation innehåller inte en objekt identifierbar information (OII) eller personligt identifierbar information (PII) data, och är rent ett hjärtslag och grundläggande servicestatistik så att vi kan tillhandahålla servicestatusinformation i molnet.|
| 33001| ADUnKnownError| Den här händelsen anger att ett okänt fel returnerades av Active Directory. Mer information finns i händelseloggen för Azure AD Connect-servern.|
| 33002| ADUserNotGrundare| Den här händelsen anger att den användare som försöker återställa eller ändra ett lösenord inte hittades i den lokala katalogen. Det här felet kan uppstå när användaren har tagits bort lokalt men inte i molnet. Det här felet kan också uppstå om det finns ett problem med synkronisering. Mer information finns i synkroniseringsloggarna och de senaste synkroniseringskörningsuppgifterna.|
| 33003| ADMutliMatchError| När en begäran om återställning av lösenord eller ändring kommer från molnet använder vi det molnankare som angetts under installationsprocessen för Azure AD Connect för att avgöra hur du länkar begäran tillbaka till en användare i din lokala miljö. Den här händelsen anger att vi hittade två användare i din lokala katalog med samma molnankareattribut. Mer information finns i synkroniseringsloggarna och de senaste synkroniseringskörningsuppgifterna.|
| 33004| ADPermissionsError| Den här händelsen anger att Adma-tjänstkontot (Active Directory Management Agent) inte har rätt behörighet för kontot i fråga för att ange ett nytt lösenord. Kontrollera att ADMA-kontot i användarens skog har återställt och ändrat lösenordsbehörigheter för alla objekt i skogen. Mer information om hur du anger behörigheter finns i Steg 4: Konfigurera lämpliga Active Directory-behörigheter. Det här felet kan också uppstå när användarens attribut AdminCount är inställt på 1.|
| 33005| ADUserAccountDisabled| Den här händelsen anger att vi försökte återställa eller ändra ett lösenord för ett konto som har inaktiverats lokalt. Aktivera kontot och försök igen.|
| 33006| ADUserAccountLockedOut| Den här händelsen anger att vi försökte återställa eller ändra ett lösenord för ett konto som har låsts ute lokalt. Utelåsningar kan uppstå när en användare har försökt ändra eller återställa lösenordsåtgärden för många gånger under en kort period. Lås upp kontot och försök igen.|
| 33007| ADUserIncorrectPassword ADUserIncorrectPassword ADUserIncorrectPassword ADUs| Den här händelsen anger att användaren angav ett felaktigt aktuellt lösenord när han utförde en lösenordsändringsåtgärd. Ange rätt aktuellt lösenord och försök igen.|
| 33008| ADPasswordPolicyError| Den här händelsen inträffar när tjänsten för tillbakaskrivning av lösenord försöker ange ett lösenord på den lokala katalogen som inte uppfyller kraven för lösenordsålder, historik, komplexitet eller filtrering för domänen. <br> <br> Om du har en lägsta ålder för lösenord och nyligen har ändrat lösenordet inom det fönstret kan du inte ändra lösenordet igen förrän det når den angivna åldern i domänen. För testning bör minimiåldern fastställas till 0. <br> <br> Om du har aktiverat krav på lösenordshistorik måste du välja ett lösenord som inte har använts under de senaste *N-tiderna,* där *N* är inställningen för lösenordshistorik. Om du väljer ett lösenord som har använts under de senaste *N-tiderna* visas ett fel i det här fallet. För testning bör lösenordshistoriken anges till 0. <br> <br> Om du har krav på lösenordskomplexitet tillämpas alla när användaren försöker ändra eller återställa ett lösenord. <br> <br> Om du har aktiverat lösenordsfilter och en användare väljer ett lösenord som inte uppfyller filtreringsvillkoren misslyckas återställningen eller ändringen.|
| 33009| ADConfigurationError| Den här händelsen indikerar att det uppstod ett problem med att skriva tillbaka ett lösenord till den lokala katalogen på grund av ett konfigurationsproblem med Active Directory. Mer information om vilken fel som uppstod finns i Azure AD Connect-datorns programhändelselogg.|

## <a name="troubleshoot-password-writeback-connectivity"></a>Felsöka återställning av lösenord

Om du upplever avbrott i tjänsten med komponenten för tillbakaskrivning av lösenord i Azure AD Connect kan du göra några snabba steg för att lösa problemet:

* [Bekräfta nätverksanslutning](#confirm-network-connectivity)
* [Starta om synkroniseringstjänsten för Azure AD Connect](#restart-the-azure-ad-connect-sync-service)
* [Inaktivera och återaktivera funktionen för tillbakaskrivning av lösenord](#disable-and-re-enable-the-password-writeback-feature)
* [Installera den senaste Azure AD Connect-versionen](#install-the-latest-azure-ad-connect-release)
* [Felsöka återställning av lösenord](#troubleshoot-password-writeback)

För att återställa tjänsten på det snabbaste sättet rekommenderar vi i allmänhet att du utför dessa steg i den ordning som diskuterats tidigare.

### <a name="confirm-network-connectivity"></a>Bekräfta nätverksanslutning

Den vanligaste felpunkten är att brandväggs- och eller proxyportar och tidsutgångar för inaktivitet är felaktigt konfigurerade. 

För Azure AD Connect version 1.1.443.0 och senare behöver du utgående HTTPS-åtkomst till följande:

* \*.passwordreset.microsoftonline.com
* \*.servicebus.windows.net

Mer detaljerad information finns i den uppdaterade listan över [Microsoft Azure Datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653) som uppdateras varje onsdag och träder i kraft nästa måndag.

Mer information finns i anslutningskraven i artikeln [Förutsättningar för Azure AD Connect.](../hybrid/how-to-connect-install-prerequisites.md)

> [!NOTE]
> SSPR kan också misslyckas om inställningarna "Lösenordet upphör aldrig att gälla" eller "Användaren kan inte ändra lösenord" är konfigurerade på kontot i AD DS lokalt. 

### <a name="restart-the-azure-ad-connect-sync-service"></a>Starta om synkroniseringstjänsten för Azure AD Connect

Starta om azure AD Connect Sync-tjänsten för att lösa anslutningsproblem eller andra tillfälliga problem med tjänsten:

1. Som administratör väljer du **Start** på servern som kör Azure AD Connect.
1. Ange **services.msc** i sökfältet och välj **Retur**.
1. Leta efter **posten Microsoft Azure AD Sync.**
1. Högerklicka på tjänstposten, välj **Starta om**och vänta sedan tills åtgärden är klar.

   ![Starta om Azure AD Sync-tjänsten med hjälp av guidning][Service restart]

Dessa steg återupprättar din anslutning till molntjänsten och löser eventuella avbrott som du kan uppleva. Om det inte går att lösa problemet genom att starta om ADSync-tjänsten rekommenderar vi att du försöker inaktivera och sedan återaktivera tillbakaskrivningsfunktionen för lösenord.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Inaktivera och återaktivera funktionen för tillbakaskrivning av lösenord

Lös anslutningsproblem genom att inaktivera och sedan återaktivera tillbakaskrivningsfunktionen för lösenord:

   1. Som administratör öppnar du konfigurationsguiden för Azure AD Connect.
   1. Ange dina globala administratörsautentiseringsuppgifter för Azure AD i **Anslut till Azure AD.**
   1. Ange administratörsuppgifter för AD Domain Services i **Anslut till AD DS.**
   1. I **Unikt identifiera användarna**väljer du knappen **Nästa.**
   1. Avmarkera kryssrutan **Tillbakaskrivning** av lösenord i **Valfria funktioner.**
   1. Välj **Nästa** via de återstående dialogsidorna utan att ändra något förrän du kommer till sidan **Klar att konfigurera.**
   1. Kontrollera att **sidan Klar att konfigurera** visar alternativet Återställa **lösenord** som **inaktiverat** och välj sedan den gröna **knappen Konfigurera** för att genomföra ändringarna.
   1. Avmarkera alternativet **Synkronisera nu i** **Färdig**och välj sedan **Slutför** för att stänga guiden.
   1. Öppna konfigurationsguiden för Azure AD Connect igen.
   1. Upprepa steg 2-8, förutom att se till att du väljer alternativet **Tillbakaskrivning** av lösenord på sidan **Valfria funktioner** för att återaktivera tjänsten.

Dessa steg återupprättar din anslutning till vår molntjänst och löser eventuella avbrott som du kan uppleva.

Om det inte går att lösa problemet genom att inaktivera och sedan återaktivera tillbakaskrivningsfunktionen för lösenord rekommenderar vi att du installerar om Azure AD Connect.

### <a name="install-the-latest-azure-ad-connect-release"></a>Installera den senaste Azure AD Connect-versionen

Om du installerar om Azure AD Connect kan du lösa konfigurations- och anslutningsproblem mellan våra molntjänster och din lokala Active Directory-miljö.

Vi rekommenderar att du bara utför det här steget när du har försökt med de två första stegen som tidigare beskrivits.

> [!WARNING]
> Om du har anpassat de färdiga synkroniseringsreglerna *säkerhetskopierar du dem innan du fortsätter med uppgraderingen och distribuerar dem sedan manuellt när du är klar.*

1. Hämta den senaste versionen av Azure AD Connect från [Microsoft Download Center](https://go.microsoft.com/fwlink/?LinkId=615771).
1. Eftersom du redan har installerat Azure AD Connect måste du utföra en uppgradering på plats för att uppdatera din Azure AD Connect-installation till den senaste versionen.
1. Kör det nedladdade paketet och följ anvisningarna på skärmen för att uppdatera din Azure AD Connect-dator.

De tidigare stegen bör återupprätta din anslutning till vår molntjänst och lösa eventuella avbrott som du kan uppleva.

Om det inte går att lösa problemet genom att installera den senaste versionen av Azure AD Connect-servern rekommenderar vi att du försöker inaktivera och sedan återaktivera tillbakaskrivning av lösenord som ett sista steg efter installation av den senaste versionen.

## <a name="verify-that-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>Kontrollera att Azure AD Connect har den behörighet som krävs för återställning av lösenord

Azure AD Connect kräver lösenordsbehörighet för Active Directory **Reset** för att kunna utföra tillbakaskrivning av lösenord. Om du vill ta reda på om Azure AD Connect har den behörighet som krävs för ett visst lokalt Active Directory-användarkonto kan du använda funktionen Effektiv behörighet i Windows:

1. Logga in på Azure AD Connect-servern och starta **Synkroniseringstjänsthanteraren** genom att välja **Starta** > **synkroniseringstjänst**.
1. Markera den lokala **Active Directory Domain Services-kopplingen** under fliken **Kopplingar** och välj sedan **Egenskaper**.  
   ![Synkroniseringstjänsthanteraren som visar hur du redigerar egenskaper](./media/active-directory-passwords-troubleshoot/checkpermission01.png)  
  
1. I popup-fönstret väljer du **Anslut till Active Directory Forest** och noterar egenskapen **Användarnamn.** Den här egenskapen är AD DS-kontot som används av Azure AD Connect för att utföra katalogsynkronisering. För att Azure AD Connect ska kunna utföra tillbakaskrivning av lösenord måste AD DS-kontot ha behörighet att återställa lösenord.  

   ![Söka efter active directory-användarkontot för synkroniseringstjänsten](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
  
1. Logga in på en lokal domänkontrollant och starta Active **Directory-programmet användare och datorer.**
1. Välj **Visa** och kontrollera att alternativet **Avancerade funktioner** är aktiverat.  

   ![Active Directory - användare och datorer visar avancerade funktioner](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
  
1. Leta efter det Active Directory-användarkonto som du vill verifiera. Högerklicka på kontonamnet och välj **Egenskaper**.  
1. Gå till fliken **Säkerhet** i popup-fönstret och välj **Avancerat**.  
1. Gå till fliken **Effektiv åtkomst** i popup-fönstret **Avancerade säkerhetsinställningar för administratör.**
1. Välj **Välj en användare,** välj det AD DS-konto som används av Azure AD Connect (se steg 3) och välj sedan **Visa effektiv åtkomst**.

   ![Fliken Gällande åtkomst som visar synkroniseringskontot](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
  
1. Bläddra nedåt och leta efter **Återställ lösenord**. Om posten är markerad har AD DS-kontot behörighet att återställa lösenordet för det valda Active Directory-användarkontot.  

   ![Verifiera att synkroniseringskontot har behörigheten Återställ lösenord](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Azure AD-forum

Om du har en allmän fråga om Azure AD och återställning av lösenord för självbetjäning kan du be communityn om hjälp på [Azure AD-forumen](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Medlemmar i samhället inkluderar ingenjörer, produktchefer, MVP:er och andra IT-proffs.

## <a name="contact-microsoft-support"></a>Kontakta Microsoft-supporten

Om du inte hittar svaret på ett problem är våra supportteam alltid tillgängliga för att hjälpa dig vidare.

För att hjälpa dig på rätt sätt ber vi dig att ge så detaljerat som möjligt när du öppnar ett ärende. Dessa uppgifter inkluderar:

* **Allmän beskrivning av felet**: Vad är felet? Vad var det beteende som märktes? Hur kan vi återskapa felet? Ge så mycket detaljer som möjligt.
* **Sida**: Vilken sida var du på när du märkte felet? Inkludera webbadressen om du kan och en skärmbild av sidan.
* **Supportkod:** Vad var supportkoden som genererades när användaren såg felet?
   * Om du vill hitta den här koden återskapar du felet och väljer sedan **länken Supportkod** längst ned på skärmen och skickar supportteknikern guid som resulterar.

   ![Hitta supportkoden längst ned på skärmen][Support code]

  * Om du är på en sida utan supportkod längst ned väljer du F12 och söker efter SID och CID och skickar dessa två resultat till supportteknikern.
* **Datum, tid och tidszon**: Inkludera det exakta datumet och tiden *med den tidszon* som felet uppstod.
* **Användar-ID:** Vem var användaren som såg felet? Ett exempel är *contoso.com\@*.
   * Är detta en federerad användare?
   * Är detta en direktautentiseringsanvändare?
   * Är detta en lösenord-hash-synkroniserad användare?
   * Är det här en användare endast för molnet?
* **Licensiering**: Har användaren tilldelats en Azure AD-licens?
* **Programhändelselogg:** Om du använder tillbakaskrivning av lösenord och felet finns i din lokala infrastruktur, inkludera en zippad kopia av din programhändelselogg från Azure AD Connect-servern.

[Service restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "Starta om Azure AD Sync-tjänsten"
[Support code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "Supportkoden finns längst ned till höger i fönstret"

## <a name="next-steps"></a>Nästa steg

Följande artiklar innehåller ytterligare information om återställning av lösenord via Azure AD:

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](howto-sspr-deployment.md)
* [Återställa eller ändra ditt lösenord](../user-help/active-directory-passwords-update-your-own-password.md)
* [Registrera för återställning av lösenord med självbetjäning](../user-help/active-directory-passwords-reset-register.md)
* [Har du en fråga om licensiering?](concept-sspr-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](howto-sspr-authenticationdata.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](concept-sspr-howitworks.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](concept-sspr-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](howto-sspr-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](howto-sspr-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](concept-sspr-howitworks.md)
* [Jag har en fråga som inte besvarades någon annanstans](active-directory-passwords-faq.md)
