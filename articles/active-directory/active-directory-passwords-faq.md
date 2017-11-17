---
title: "Vanliga frågor och svar: Azure AD SSPR | Microsoft Docs"
description: "Vanliga frågor och svar om Azure AD-lösenordet för självbetjäning återställa"
services: active-directory
keywords: "Hantering av Active directory-lösenord, lösenordshantering, Azure AD self service för lösenordsåterställning"
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
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 112bfeea29eacd0615f1d134567ec52264318b5f
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2017
---
# <a name="password-management-frequently-asked-questions"></a>Vanliga och frågor svar om lösenordshantering

Följande är några vanliga frågor (FAQ) för alla objekt som är relaterade till återställning av lösenord.

Om du har en allmän fråga om Azure Active Directory (Azure AD) och lösenordsåterställning via självbetjäning (SSPR) som inte besvaras här, du kan be gemenskapen för att få hjälp på den [Azure AD-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Medlemmar i gruppen innehåller tekniker, projektledare, MVP och andra IT-proffs.

Dessa vanliga frågor är uppdelat i följande avsnitt:

* [Frågor om lösenord återställer registrering](#password-reset-registration)
* [Frågor om återställning av lösenord](#password-reset)
* [Frågor om ändring av lösenord](#password-change)
* [Frågor om rapporter för lösenord](#password-management-reports)
* [Frågor om tillbakaskrivning av lösenord](#password-writeback)

## <a name="password-reset-registration"></a>Registrering för lösenordsåterställning

* **F: kan användarna registrera sina egna data för återställning av lösenord?**

  > **S:** Ja. Så länge lösenordsåterställning har aktiverats och de licensierade kan användare gå till registreringsportalen för lösenordsåterställning (http://aka.ms/ssprsetup) att registrera sina autentiseringsinformationen. Användarna kan också registrera via åtkomstpanelen (http://myapps.microsoft.com). Om du vill registrera via åtkomstpanelen, de behöver väljer deras profilbild, väljer **profil**, och välj sedan den **registrera dig för lösenordsåterställning** alternativet.
  >
  >
* **F: kan jag definiera data om återställning av lösenord för åt mina användare?**

  > **S:** Ja, kan du göra det med Azure AD Connect PowerShell den [Azure-portalen](https://portal.azure.com), eller Office 365 Admin center. Mer information finns i [återställning av Data som används av Azure AD-lösenordet för självbetjäning](active-directory-passwords-data.md).
  >
  >
* **F: kan jag synkroniserar data om säkerhetsfrågor från lokala?**

  > **S:** Nej, detta inte är möjligt i dag.
  >
  >
* **F: kan användarna registrera data så att andra användare inte kan se dessa data?**

  > **S:** Ja. När användare registrerar data med hjälp av lösenordet återställa portalen för registrering, data sparas i privata autentisering fält som visas bara för globala administratörer och användare.
  >
  >
* **F: Mina användare måste registreras innan de kan använda lösenordsåterställning?**

  > **S:** Nej. Om du definierar tillräckligt med autentiseringsinformation för åt inte användare att registrera. Lösenordsåterställning fungerar så länge som du har korrekt formaterat data som lagras i relevanta fält i katalogen.
  >
  >
* **F: kan jag synkronisera eller ange telefon för autentisering, verifiering e-post eller alternativa autentisering phone fält åt mina användare?**

  > **S:** Nej, detta inte är möjligt i dag.
  >
  >
* **F: hur avgöra vilka alternativ för att visa mina användare i portalen för registrering?**

  > **S:** lösenordsåterställning registrering portal visar bara de alternativ som du har aktiverat för dina användare. Dessa alternativ finns den **princip för lösenordsåterställning för användare** för din katalog **konfigurera** fliken. Till exempel om du inte aktiverar säkerhetsfrågor går sedan användare inte att registrera för det alternativet.
  >
  >
* **F: när en användare anses vara registrerad?**

  > **S:** en användare anses registrerade för SSPR när de har registrerat minst **antal metoder som krävs för att återställa** ett lösenord som du har angett i den [Azure-portalen](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Lösenordsåterställning

* **F: hur lång tid ska gå att få ett e-post, SMS eller telefonsamtal från lösenordsåterställning?**

  > **S:** e-postmeddelanden, SMS-meddelanden och samtal ska tas emot i under en minut. I vanliga fall har 5 till 20 sekunder.
    >Om du inte har fått meddelandet inom den här tiden:
        > * Kontrollera mappen för skräppost.
        > * Kontrollera att det antal eller e-post som kontaktas är den som du förväntar dig.
        > * Kontrollera att autentiseringsdata i katalogen är korrekt formaterad, t.ex, + 1 4255551234 eller  *user@contoso.com* . 
  >
  >
* **F: på vilka språk som stöds av lösenordsåterställning?**

  > **S:** Användargränssnittet för lösenordsåterställning SMS-meddelanden och samtal är lokaliserade till samma språk som stöds i Office 365.
  >
  >
* **F: Vad delar av återställning av lösenord hämta märkta när jag ange organisationens företagsanpassning objekten i min katalog har konfigurera fliken?**

  > **S:** portalen för återställning av lösenord visar organisationens logotyp och kan du konfigurera länken ”kontaktar du administratören” att peka till en anpassad e-postadress eller URL. E-postmeddelandet som skickas av lösenordsåterställning innehåller organisationens logotyp, färger och namnet i brödtexten i e-postmeddelandet och har anpassats från inställningarna för det specifika namnet.
  >
  >
* **F: hur kan jag för att informera användarna om hur du går att återställa sina lösenord?**

  > **S:** prova några av rekommendationerna i vår [SSPR distribution](active-directory-passwords-best-practices.md#email-based-rollout) artikel.
  >
  >
* **F: kan jag använda den här sidan från en mobil enhet?**

  > **S:** Ja, den här sidan fungerar på mobila enheter.
  >
  >
* **F: du stöder upplåsning lokala Active Directory-konton när användarna återställa sina lösenord?**

  > **S:** Ja. När en användare återställer sitt lösenord om tillbakaskrivning av lösenord har distribuerats via Azure AD Connect, låses användarens konto automatiskt när de återställa sina lösenord.
  >
  >
* **F: hur kan jag integrera lösenordsåterställning direkt till min användarens inloggning Skrivbordsmiljö?**

  > **S:** om du är en Azure AD Premium-kund kan du installera Microsoft Identity Manager utan extra kostnad och distribuera lösningen för lokala lösenordsåterställning.
  >
  >
* **F: kan jag ange olika säkerhetsfrågor för olika språk?**

  > **S:** Nej, detta inte är möjligt i dag.
  >
  >
* **F: hur många frågor kan konfigurera för säkerhetsalternativ för autentisering av frågor?**

  > **S:** du kan konfigurera upp till 20 anpassade säkerhetsfrågor i den [Azure-portalen](https://portal.azure.com).
  >
  >
* **F: hur lång tid kan säkerhetsfrågor?**

  > **S:** säkerhetsfrågor kan innehålla 3 till 200 tecken.
  >
  >
* **F: hur lång tid kan svar på säkerhetsfrågor?**

  > **S:** svar kan innehålla 3 till 40 tecken.
  >
  >
* **F: är dubbla svar på säkerhetsfrågor avvisade?**

  > **S:** Ja, vi avvisa dubbla svar på säkerhetsfrågor.
  >
  >
* **F: kan en användare registrera samma säkerhetsfråga mer än en gång?**

  > **S:** Nej. När en användare som registrerar en fråga, kan inte de registrera för att fråga en andra gång.
  >
  >
* **F: är det möjligt att ange minimigräns av säkerhetsfrågor för registrering och återställa?**

  > **S:** Ja, en gräns kan anges för registrering och en annan för återställning. Tre till fem säkerhetsfrågor kan krävas för registrering, och tre till fem frågor kan krävas för återställning.
  >
  >
* **F: jag konfigurerats min principen så att användare måste använda säkerhetsfrågor för återställning, men Azure administratörer verkar konfigureras på olika sätt.**

  > **S:** detta är förväntat beteende. Microsoft använder en starkt lösenord två gate återställning standardprincip för en administratör för Azure-roll. Detta förhindrar att administratörer använder säkerhetsfrågor. Du hittar mer information om den här principen i den [lösenordsprinciper och begränsningar i Azure Active Directory](active-directory-passwords-policy.md#administrator-password-policy-differences) artikel.
  >
  >
* **F: om en användare har registrerats fler än det maximala antalet frågor som krävs för att återställa, hur säkerhetsfrågorna väljs under återställning?**

  > **S:** *N* antal säkerhetsfrågor väljs slumpmässigt utanför det totala antalet frågor en användare har registrerats för var *N* är den som har angetts för den  **Antal frågor som krävs för att återställa** alternativet. Till exempel om en användare har registrerat fem säkerhetsfrågor, men bara tre krävs för att återställa ett lösenord, tre av de fem frågorna väljs slumpmässigt och uppvisas vid återställning. Om du vill förhindra att fråga hammering, om användaren får svar på frågorna fel markeringen processen startar över.
  >
  >
* **F: förhindrar du att användare från flera försök att återställa ett lösenord i en kort tidsperiod?**

  > **S:** Ja, det finns säkerhetsfunktioner som är inbyggda i om du vill skydda från missbruk för återställning av lösenord. Användare kan försöka bara fem Återställ lösenordsförsök inom en timme innan de är utelåst under 24 timmar. Användare kan försöka att validera ett telefonnummer bara fem gånger inom en timme innan de är utelåst under 24 timmar. Användare kan försöka en enda autentiseringsmetod bara fem gånger inom en timme innan de är utelåst under 24 timmar.
  >
  >
* **F: hur lång tid är e-post och SMS enstaka lösenord giltig?**

  > **S:** sessioners livstid för återställning av lösenord är 15 minuter. Användaren har 15 minuter att återställa sina lösenord från början av återställning av lösenord. E-post och SMS engångskod är ogiltiga när den här tiden har löpt ut.
  >
  >
* **F: kan jag blockera användare från att återställa sina lösenord?**

  > **S:** Ja, om du använder en grupp för att aktivera SSPR, du kan ta bort en användare från gruppen som användarna kan återställa sina lösenord.
  >
  >

## <a name="password-change"></a>Ändra lösenordet

* **F: var ska gå Mina användare ändra sina lösenord?**

  > **S:** användare kan ändra sina lösenord överallt där de ser sina profilbild eller ikonen, som i det övre högra hörnet av deras [Office 365](https://portal.office.com) portalen eller [åtkomstpanelen](https://myapps.microsoft.com) inträffar. Användare kan ändra sina lösenord från den [åtkomst panelen profilsida](https://account.activedirectory.windowsazure.com/r#/profile). Användare kan också behöva ändra sina lösenord automatiskt vid Azure AD-inloggningssida om sina lösenord har gått ut. Slutligen användare kan bläddra till den [Azure AD-portalen för ändring av lösenord](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) direkt om de vill ändra sina lösenord.
  >
  >
* **F: kan användarna meddelas i Office-portalen när sina lokala lösenord upphör att gälla?**

  > **S:** Ja, det är möjligt i dag om du använder Active Directory Federation Services (AD FS). Om du använder AD FS, följ instruktionerna i den [skicka lösenord princip anspråk med AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) artikel. Om du använder lösenord hash-synkronisering kan är det inte möjligt i dag. Vi Synkronisera inte lösenordsprinciper från lokala kataloger, så det inte är möjligt för oss att publicera giltighetstid meddelanden till molnet upplevelser. I båda fallen är det också möjligt att [meddela användare vars lösenord ska upphöra att gälla via PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **F: kan jag blockera användare från att ändra sina lösenord?**

  > **S:** för endast molnbaserad användare lösenordsändringar kan inte blockeras. För lokala användare kan du ange den **användaren kan inte ändra lösenordet** alternativet för att du har valt. De valda användarna kan inte ändra sina lösenord.
  >
  >

## <a name="password-management-reports"></a>Rapporter för lösenord

* **F: hur lång tid tar det för att data ska visas i rapporter för lösenord?**

  > **S:** Data ska visas på lösenord management rapporter i 5 till 10 minuter. I vissa fall kan det ta upp till en timme ska visas.
  >
  >
* **F: hur filtrera rapporter för lösenord?**

  > **S:** för att filtrera rapporter för lösenord, Välj små förstoringsglaset till extrema höger om kolumnetiketterna längst upp i rapporten. Om du vill göra bättre filtrering kan du ladda ned rapporten till Excel och skapa en pivottabell.
  >
  >
* **F: Vad är det maximala antalet händelser som lagras i management-rapporter lösenord?**

  > **S:** upp till 75 000 lösenord återställning eller lösenord Återställ registrering händelser lagras i lösenord management rapporter utsträckning tillbaka så långt det är 30 dagar. Vi arbetar för att expandera det här numret för att inkludera fler händelser.
  >
  >
* **F: hur långt tillbaka göra lösenordshantering rapporter gå?**

  > **S:** lösenordshanteringen rapporterar Visa åtgärder som genomförts under de senaste 30 dagarna. Just nu om du vill arkivera informationen, kan du hämta rapporter med jämna mellanrum och spara dem på en annan plats.
  >
  >
* **F: finns det ett maximalt antal rader som kan visas på management-rapporter lösenord?**

  > **S:** Ja. Högst 75 000 rader kan visas på något av lösenord för hantering av rapporter, oavsett om de visas i Användargränssnittet eller hämtas.
  >
  >
* **F: finns det en API för åtkomst till lösenordsåterställning eller registrering rapporterar data?**

  > **S:** Ja. Information om hur du kan komma åt lösenordsåterställning reporting dataströmmen finns [kan få tillgång till lösenordsåterställning rapporteringshändelser programmässigt](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).
  >
  >

## <a name="password-writeback"></a>Tillbakaskrivning av lösenord

* **F: hur fungerar tillbakaskrivning av lösenord i bakgrunden?**

  > **S:** finns i artikeln [hur tillbakaskrivning av lösenord fungerar](active-directory-passwords-writeback.md) förklaringar av vad som händer när du aktiverar tillbakaskrivning av lösenord och hur data flödar genom systemet tillbaka till din lokala miljö.
  >
  >
* **F: hur lång tid tillbakaskrivning av lösenord tar ska fungera? Är det en synkronisering fördröjning som finns med lösenordshashsynkronisering?**

  > **S:** tillbakaskrivning av lösenord är snabbmeddelanden. Det är en synkron pipeline som fungerar helt annorlunda än hash-synkronisering av lösenord. Tillbakaskrivning av lösenord kan användare få realtid feedback om genomförandet av deras återställning av lösenord eller ändra igen. Den genomsnittliga tiden för en lyckad tillbakaskrivning av lösenord är under 500 ms.
  >
  >
* **F: om min lokala kontot är inaktiverat är hur Mina moln konto och komma åt påverkas?**

  > **S:** om ditt lokala ID inaktiveras din moln-ID och åtkomst inaktiveras även vid nästa synkroniseringsintervall via Azure AD Connect. Synkroniseringen är som standard var 30: e minut.
  >
  >
* **F: om mitt konto lokalt är begränsad av en lokal Active Directory-lösenordsprincip, SSPR lyder under den här principen när jag ändrar lösenordet?**

  > **S:** Ja, SSPR förlitar sig på och följer principen lokala Active Directory-lösenord. Den här principen innehåller vanliga lösenordsprincip för Active Directory-domän, samt några definierade, detaljerade lösenordsprinciper, som är riktade till en användare.
  >
  >
* **F: vilka typer av konton fungerar tillbakaskrivning av lösenord för?**

  > **S:** lösenord tillbakaskrivning fungerar för federerad och lösenords-hash synkroniserade användare.
  >
  >
* **F: tillbakaskrivning av lösenord kan genomdriva lösenordsprinciper för min domän?**

  > **S:** Ja. Tillbakaskrivning av lösenord tillämpar ålder för lösenord, historik, komplexitet, filter och andra begränsningar som du kan infördes för lösenord i den lokala domänen.
  >
  >
* **F: är tillbakaskrivning av lösenord säker?  Hur vet jag att jag inte hämta över?**

  > **S:** Ja, tillbakaskrivning av lösenord är säker. Om du vill läsa mer om de fyra säkerhetslagren implementerats av tjänsten för tillbakaskrivning av lösenord, ta en titt på [lösenord tillbakaskrivning säkerhetsmodell](active-directory-passwords-writeback.md#password-writeback-security-model) i avsnittet den [översikt för tillbakaskrivning av lösenord](active-directory-passwords-writeback.md) artikel.
  >
  >

## <a name="next-steps"></a>Nästa steg

* [Hur gör jag för att slutföra en lyckad distribution av SSPR?](active-directory-passwords-best-practices.md)
* [Återställ eller ändra ditt lösenord](active-directory-passwords-update-your-own-password.md)
* [Registrera för återställning av lösenord för självbetjäning](active-directory-passwords-reset-register.md)
* [Har du en fråga med licensiering?](active-directory-passwords-licensing.md)
* [Vilka data används av SSPR och vilka data bör du fylla i för dina användare?](active-directory-passwords-data.md)
* [Vilka autentiseringsmetoder är tillgängliga för användarna?](active-directory-passwords-how-it-works.md#authentication-methods)
* [Vilka principalternativ finns för SSPR?](active-directory-passwords-policy.md)
* [Vad är tillbakaskrivning av lösenord och vad är intresserat med det?](active-directory-passwords-writeback.md)
* [Hur gör jag för att rapportera på aktivitet i SSPR?](active-directory-passwords-reporting.md)
* [Vad är alla alternativ i SSPR och vad betyder de?](active-directory-passwords-how-it-works.md)
* [Jag tror att något har gått sönder. Hur gör jag för att felsöka SSPR?](active-directory-passwords-troubleshoot.md)
