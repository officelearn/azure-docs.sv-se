---
title: Vanliga frågor och svar om återställning av lösenord – Azure Active Directory
description: Vanliga frågor och svar om azure ad-återställning av lösenord för självbetjäning
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1246b5b980f60c2f3e65aa5b32a7d79dd6efc7d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407166"
---
# <a name="password-management-frequently-asked-questions"></a>Vanliga frågor och svar om lösenordshantering

Följande är några vanliga frågor (FAQ) för allt som rör återställning av lösenord.

Om du har en allmän fråga om Azure Active Directory (Azure AD) och självbetjäningslösenordsåterställning (SSPR) som inte besvaras här kan du be communityn om hjälp på [Azure AD-forumet](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Medlemmar i samhället inkluderar ingenjörer, produktchefer, MVP:er och andra IT-proffs.

Vanliga frågor och svar är uppdelade i följande avsnitt:

* [Frågor om registrering av återställning av lösenord](#password-reset-registration)
* [Frågor om återställning av lösenord](#password-reset)
* [Frågor om lösenordsändring](#password-change)
* [Frågor om lösenordshanteringsrapporter](#password-management-reports)
* [Frågor om tillbakaskrivning av lösenord](#password-writeback)

## <a name="password-reset-registration"></a>Registrering av lösenordsåterställning

* **F: Kan mina användare registrera sina egna data för återställning av lösenord?**

  > **S:** Ja. Så länge återställning av lösenord är aktiverat och de är licensierade kanhttps://aka.ms/ssprsetup) användare gå till registreringsportalen för lösenordsåterställning (för att registrera sin autentiseringsinformation. Användare kan också registrera sighttps://myapps.microsoft.com)via åtkomstpanelen ( . Om du vill registrera dig via åtkomstpanelen måste de välja sin profilbild, välja **Profil**och sedan välja alternativet **Registrera för återställning av lösenord.**
  >
  >
* **F: Om jag aktiverar återställning av lösenord för en grupp och sedan bestämmer mig för att aktivera det för alla krävs mina användare omregistrera?**

  > **S:** Nej. Användare som har fyllt i autentiseringsdata behöver inte registrera om.
  >
  >
* **F: Kan jag definiera data för återställning av lösenord för mina användares räkning?**

  > **A.** Ja, du kan göra det med Azure AD Connect, PowerShell, [Azure-portalen](https://portal.azure.com)eller [Microsoft 365 admincenter](https://admin.microsoft.com). Mer information finns i [Data som används av Azure AD självbetjäningslösenordsåterställning](howto-sspr-authenticationdata.md).
  >
  >
* **F: Kan jag synkronisera data för säkerhetsfrågor från lokala?**

  > **A.** Nej, det är inte möjligt idag.
  >
  >
* **F: Kan mina användare registrera data på ett sådant sätt att andra användare inte kan se dessa data?**

  > **S:** Ja. När användare registrerar data med hjälp av registreringsportalen för lösenordsåterställning sparas data i privata autentiseringsfält som bara är synliga för globala administratörer och användaren.
  >
  >
* **F: Måste mina användare registreras innan de kan använda återställning av lösenord?**

  > **S:** Nej. Om du definierar tillräckligt med autentiseringsinformation för deras räkning behöver användarna inte registrera sig. Återställning av lösenord fungerar så länge du har formaterat de data som lagras i lämpliga fält i katalogen på rätt sätt.
  >
  >
* **F: Kan jag synkronisera eller ställa in autentiseringstelefonen, e-post för autentisering eller alternativa autentiseringstelefonfält för mina användares räkning?**

  > **A.** De fält som kan ställas in av en global administratör definieras i artikeln [SSPR-datakrav](howto-sspr-authenticationdata.md).
  >
  >
* **F: Hur avgör registreringsportalen vilka alternativ som ska visa mina användare?**

  > **A.** Registreringsportalen för lösenordsåterställning visar bara de alternativ som du har aktiverat för användarna. De här alternativen finns under avsnittet Princip för återställning av **användarlösenord** på fliken **Konfigurera** katalog. Om du till exempel inte aktiverar säkerhetsfrågor kan användarna inte registrera sig för det alternativet.
  >
  >
* **F: När anses en användare vara registrerad?**

  > **A.** En användare anses vara registrerad för SSPR när de har registrerat minst **det antal metoder som krävs för att återställa** ett lösenord som du har angett i [Azure-portalen](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Lösenordsåterställning

* **F: Hindrar du användare från flera försök att återställa ett lösenord på kort tid?**

  > **A.** Ja, det finns säkerhetsfunktioner inbyggda i återställning av lösenord för att skydda den från missbruk. 
  >
  > Användare kan bara prova fem försök att återställa lösenord inom en 24-timmarsperiod innan de är utelåst i 24 timmar. 
  >
  > Användare kan försöka validera ett telefonnummer, skicka ett SMS eller validera säkerhetsfrågor och svar bara fem gånger inom en timme innan de är utelåst i 24 timmar. 
  >
  > Användare kan skicka ett e-postmeddelande högst 10 gånger inom en 10-minutersperiod innan de är utelåst i 24 timmar.
  >
  > Räknarna återställs när en användare återställer sitt lösenord.
  >
  >
* **F: Hur länge ska jag vänta med att få ett e-postmeddelande, SMS eller telefonsamtal från återställning av lösenord?**

  > **A.** E-postmeddelanden, SMS-meddelanden och telefonsamtal bör komma in under en minut. Det normala fallet är 5 till 20 sekunder.
  > Om du inte får meddelandet inom den här tidsramen:
  > * Kontrollera din skräpmapp.
  > * Kontrollera att numret eller e-postmeddelandet som kontaktas är det du förväntar dig.
  > * Kontrollera att autentiseringsdata i katalogen är korrekt formaterade, till exempel +1 4255551234 eller *användar\@contoso.com*. 
* **F: Vilka språk stöds av återställning av lösenord?**

  > **A.** Användargränssnittet för återställning av lösenord, SMS och röstsamtal är lokaliserade på samma språk som stöds i Office 365.
  >
  >
* **F: Vilka delar av lösenordsåterställningen blir märkta när jag ställer in organisationsprofilobjekten på fliken Konfigurera i katalogen?**

  > **A.** Portalen för återställning av lösenord visar organisationens logotyp och låter dig konfigurera länken "Kontakta administratören" så att den pekar på ett anpassat e-postmeddelande eller en anpassad webbadress. Alla e-postmeddelanden som skickas med lösenordsåterställning innehåller organisationens logotyp, färger och namn i e-postmeddelandets brödtext och anpassas från inställningarna för det aktuella namnet.
  >
  >
* **F: Hur kan jag utbilda mina användare om vart de ska gå för att återställa sina lösenord?**

  > **A.** Prova några av förslagen i vår artikel i [SSPR-distributionen.](howto-sspr-deployment.md#plan-communications)
  >
  >
* **F: Kan jag använda den här sidan från en mobil enhet?**

  > **A.** Ja, den här sidan fungerar på mobila enheter.
  >
  >
* **F: Stöder du att låsa upp lokala Active Directory-konton när användare återställer sina lösenord?**

  > **S:** Ja. När en användare återställer sitt lösenord, om tillbakaskrivning av lösenord har distribuerats via Azure AD Connect, låses användarens konto automatiskt upp när de återställer sitt lösenord.
  >
  >
* **F: Hur kan jag integrera återställning av lösenord direkt i användarens inloggningsupplevelse för skrivbordet?**

  > **A.** Om du är Azure AD Premium-kund kan du installera Microsoft Identity Manager utan extra kostnad och distribuera den lokala lösningen för återställning av lösenord.
  >
  >
* **F: Kan jag ställa in olika säkerhetsfrågor för olika språk?**

  > **A.** Nej, det är inte möjligt idag.
  >
  >
* **F: Hur många frågor kan jag konfigurera för autentiseringsalternativet för säkerhetsfrågor?**

  > **A.** Du kan konfigurera upp till 20 anpassade säkerhetsfrågor i [Azure-portalen](https://portal.azure.com).
  >
  >
* **F: Hur länge kan säkerhetsfrågor vara?**

  > **A.** Säkerhetsfrågor kan vara 3 till 200 tecken långa.
  >
  >
* **F: Hur länge kan svaren på säkerhetsfrågor vara?**

  > **A.** Svaren kan vara 3 till 40 tecken långa.
  >
  >
* **F: Avvisas dubblettsvar på säkerhetsfrågor?**

  > **A.** Ja, vi avvisar dubbla svar på säkerhetsfrågor.
  >
  >
* **F: Kan en användare registrera samma säkerhetsfråga mer än en gång?**

  > **S:** Nej. När en användare har registrerat en viss fråga kan de inte registrera sig för den frågan en andra gång.
  >
  >
* **F: Är det möjligt att fastställa en minimigräns för säkerhetsfrågor för registrering och återställning?**

  > **A.** Ja, en gräns kan ställas in för registrering och en annan för återställning. Tre till fem säkerhetsfrågor kan krävas för registrering, och tre till fem frågor kan krävas för återställning.
  >
  >
* **F: Jag har konfigurerat min princip för att kräva att användare använder säkerhetsfrågor för återställning, men Azure-administratörerna verkar vara konfigurerade på ett annat sätt.**

  > **A.** Detta är det förväntade beteendet. Microsoft tillämpar en stark standardprincip med två gates för lösenordsåterställning för alla Azure-administratörsroller. Detta förhindrar administratörer från att använda säkerhetsfrågor. Du hittar mer information om den här principen i [lösenordsprinciperna och begränsningarna i Azure Active Directory-artikeln.](concept-sspr-policy.md)
  >
  >
* **F: Om en användare har registrerat mer än det maximala antalet frågor som krävs för att återställa, hur väljs säkerhetsfrågorna under återställningen?**

  > **S:** *N* antal säkerhetsfrågor väljs slumpmässigt av det totala antalet frågor som en användare har registrerat för, där *N* är det belopp som anges för alternativet Antal frågor som krävs för **att återställa.** Om en användare till exempel har registrerat fem säkerhetsfrågor, men bara tre krävs för att återställa ett lösenord, väljs tre av de fem frågorna slumpmässigt och presenteras vid återställning. För att förhindra fråga hamra, om användaren får svar på de frågor fel urvalsprocessen börjar om.
  >
  >
* **F: Hur länge gäller e-post och SMS engångslösenord?**

  > **A.** Sessionens livstid för återställning av lösenord är 15 minuter. Från början av åtgärden för återställning av lösenord har användaren 15 minuter på sig att återställa sitt lösenord. E-postmeddelandet och SMS-lösenord en gång gäller i 5 minuter under lösenordsåterställningen.
  >
  >
* **F: Kan jag blockera användare från att återställa sitt lösenord?**

  > **A.** Ja, om du använder en grupp för att aktivera SSPR kan du ta bort en enskild användare från gruppen som tillåter användare att återställa sitt lösenord. Om användaren är en global administratör behåller de möjligheten att återställa sitt lösenord och detta kan inte inaktiveras.
  >
  >

## <a name="password-change"></a>Lösenordsändring

* **F: Var ska mina användare gå för att ändra sina lösenord?**

  > **A.** Användare kan ändra sina lösenord var de än ser sin profilbild eller profilikon, till exempel i det övre högra hörnet på deras [Office 365-portal](https://portal.office.com) eller [Åtkomstpanelens](https://myapps.microsoft.com) upplevelser. Användare kan ändra sina lösenord från [sidan Profil på Åtkomstpanelen](https://account.activedirectory.windowsazure.com/r#/profile). Användare kan också uppmanas att ändra sina lösenord automatiskt på inloggningssidan för Azure AD om deras lösenord har upphört att gälla. Slutligen kan användare bläddra till [Azure AD-lösenordsändringsportalen](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) direkt om de vill ändra sina lösenord.
  >
  >
* **F: Kan mina användare meddelas i Office-portalen när deras lokala lösenord upphör att gälla?**

  > **A.** Ja, detta är möjligt idag om du använder Active Directory Federation Services (AD FS). Om du använder AD FS följer du instruktionerna i artikeln [Skicka lösenordspolicy med AD FS.](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) Om du använder synkronisering av lösenord hash-synkronisering är detta inte möjligt idag. Vi synkroniserar inte lösenordsprinciper från lokala kataloger, så det är inte möjligt för oss att publicera meddelanden om förfallodatum till molnupplevelser. I båda fallen är det också möjligt att [meddela användare vars lösenord är på väg att löpa ut via PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **F: Kan jag blockera användare från att ändra sitt lösenord?**

  > **A.** För användare som endast är molnet kan lösenordsändringar inte blockeras. För lokala användare kan du ange alternativet **Användare kan inte ändra lösenord** till markerat. De valda användarna kan inte ändra sitt lösenord.
  >
  >

## <a name="password-management-reports"></a>Rapporter om lösenordshantering

* **F: Hur lång tid tar det för data att visas i lösenordshanteringsrapporterna?**

  > **A.** Data ska visas i rapporterna om lösenordshantering om 5 till 10 minuter. I vissa fall kan det ta upp till en timme att visas.
  >
  >
* **F: Hur kan jag filtrera lösenordshanteringsrapporterna?**

  > **A.** Om du vill filtrera lösenordshanteringsrapporterna väljer du det lilla förstoringsglaset längst till höger i kolumnetiketterna, längst upp i rapporten. Om du vill filtrera rikare kan du hämta rapporten till Excel och skapa en pivottabell.
  >
  >
* **F: Vilket är det maximala antalet händelser som lagras i lösenordshanteringsrapporterna?**

  > **A.** Upp till 75 000 lösenordsåterställnings- eller registreringshändelser för återställning av lösenord lagras i lösenordshanteringsrapporterna, som sträcker sig så långt tillbaka som 30 dagar. Vi arbetar för att utöka detta antal till att omfatta fler evenemang.
  >
  >
* **F: Hur långt tillbaka går lösenordshanteringsrapporterna?**

  > **A.** Lösenordshanteringsrapporterna visar åtgärder som har inträffat under de senaste 30 dagarna. Om du nu behöver arkivera dessa data kan du hämta rapporterna med jämna mellanrum och spara dem på en separat plats.
  >
  >
* **F: Finns det ett maximalt antal rader som kan visas i lösenordshanteringsrapporterna?**

  > **S:** Ja. Högst 75 000 rader kan visas i någon av lösenordshanteringsrapporterna, oavsett om de visas i användargränssnittet eller hämtas.
  >
  >
* **F: Finns det ett API för att komma åt data för återställning av lösenord eller registrering?**

  > **S:** Ja. Mer information om hur du kan komma åt rapporteringsdata för återställning av lösenord finns i [Azure Log Analytics REST API Reference](/rest/api/loganalytics/).
  >
  >

## <a name="password-writeback"></a>Tillbakaskrivning av lösenord

* **F: Hur fungerar tillbakaskrivning av lösenord bakom kulisserna?**

  > **A.** Se artikeln [Hur tillbakaskrivning](howto-sspr-writeback.md) av lösenord fungerar för en förklaring av vad som händer när du aktiverar tillbakaskrivning av lösenord och hur data flödar tillbaka till din lokala miljö.
  >
  >
* **F: Hur lång tid tar tillbaka lösenordsskrivningen till arbetet? Finns det en synkroniseringsfördröjning som det finns med lösenord hash-synkronisering?**

  > **A.** Lösenord tillbakaskrivning är omedelbar. Det är en synkron pipeline som fungerar fundamentalt annorlunda än lösenord hash synkronisering. Lösenord tillbakaskrivning tillåter användare att få feedback i realtid om framgången för deras lösenordsåterställning eller ändring operation. Den genomsnittliga tiden för en lyckad tillbakaskrivning av ett lösenord är under 500 ms.
  >
  >
* **F: Om mitt lokala konto är inaktiverat, hur påverkas mitt molnkonto och åtkomst?**

  > **A.** Om ditt lokala ID är inaktiverat inaktiveras ditt moln-ID och åtkomst vid nästa synkroniseringsintervall via Azure AD Connect. Som standard är den här synkroniseringen var 30:e minut.
  >
  >
* **F: Om mitt lokala konto begränsas av en lokal Active Directory-lösenordsprincip, lyder SSPR den här principen när jag ändrar mitt lösenord?**

  > **A.** Ja, SSPR förlitar sig på och följer den lokala lösenordsprincipen i Active Directory. Den här principen innehåller den typiska lösenordsprincipen för Active Directory-domäner samt alla definierade detaljerade lösenordsprinciper som är riktade till en användare.
  >
  >
* **F: Vilka typer av konton fungerar tillbakaskrivning av lösenord?**

  > **A.** Återställning av lösenord fungerar för användarkonton som synkroniseras från lokala Active Directory till Azure AD, inklusive federerade, synkroniserade lösenordshã¤n och direktutanticationsanvändare.
  >
  >
* **F: Framtvingar tillbakaskrivning av lösenord min domäns lösenordsprinciper?**

  > **S:** Ja. Återställning av lösenord upprätthåller lösenordsålder, historik, komplexitet, filter och andra begränsningar som du kan införa på lösenord i din lokala domän.
  >
  >
* **F: Är tillbakaskrivning av lösenord säker?  Hur kan jag vara säker på att jag inte blir hackad?**

  > **A.** Ja, tillbakaskrivning av lösenord är säkert. Om du vill läsa mer om de flera säkerhetslager som implementerats av tjänsten för tillbakaskrivning av lösenord kan du läsa [avsnittet Om tillbakaskrivning](concept-sspr-writeback.md#password-writeback-security) av lösenord i [översiktsartikeln för tillbakaskrivning](howto-sspr-writeback.md) av lösenord.
  >
  >

## <a name="next-steps"></a>Nästa steg

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
* [Jag tror att något är trasigt. Hur felsöker jag SSPR?](active-directory-passwords-troubleshoot.md)
