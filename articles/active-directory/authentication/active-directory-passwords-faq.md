---
title: Lösenordsåterställning via självbetjäning vanliga frågor och svar – Azure Active Directory
description: Vanliga frågor och svar om Azure AD-självbetjäning lösenord återställa
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.openlocfilehash: 50edabb395380c8b0185736205834dd65e47317f
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2018
ms.locfileid: "52425275"
---
# <a name="password-management-frequently-asked-questions"></a>Vanliga och frågor svar om lösenordshantering

Här följer några vanliga frågor (och svar FAQ) för allt som är relaterade till återställning av lösenord.

Om du har en allmän fråga om Azure Active Directory (Azure AD) och lösenordsåterställning via självbetjäning (SSPR) som inte besvaras här, kan du be communityn för att få hjälp på den [Azure AD-forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Medlemmar i communityn omfattar tekniker, produktchefer, MVP: er och andra IT-proffs.

Den här vanliga frågor och svar är uppdelat i följande avsnitt:

* [Registrering av återställning av frågor om lösenord](#password-reset-registration)
* [Frågor om lösenordsåterställning](#password-reset)
* [Frågor om lösenordsändring](#password-change)
* [Frågor om lösenordshanteringsrapporter](#password-management-reports)
* [Frågor om tillbakaskrivning av lösenord](#password-writeback)

## <a name="password-reset-registration"></a>Registrering av lösenordsåterställning

* **F: kan Mina användare registrerar sina egna data för återställning av lösenord?**

  > **S:** Ja. Så länge återställning av lösenord är aktiverat och de är licensierade användare kan gå till registreringsportalen för lösenordsåterställning (https://aka.ms/ssprsetup) att registrera sin autentiseringsinformation. Användare kan också registrera via åtkomstpanelen (https://myapps.microsoft.com). Om du vill registrera via åtkomstpanelen, de behöver för att väljer sina profilbild **profil**, och välj sedan den **registrera för återställning av lösenord** alternativet.
  >
  >
* **F: om jag aktiverar lösenord återställa för en grupp och sedan väljer att aktivera det för alla mina användare krävs Omregistrera?**

  > **S:** Nej. Användare som måste fylla i autentiseringsdata krävs inte för att registrera igen.
  >
  >
* **F: kan jag definierar data om återställning av lösenord för Mina användare?**

  > **S:** Ja, kan du göra det med Azure AD Connect, PowerShell, den [Azure-portalen](https://portal.azure.com), eller Office 365 Administrationscenter. Mer information finns i [återställning av Data som används av Azure AD-självbetjäning lösenord](howto-sspr-authenticationdata.md).
  >
  >
* **F: kan jag synkroniserar data om säkerhetsfrågor från en lokal plats?**

  > **S:** Nej, det inte är möjligt i dag.
  >
  >
* **F: kan Mina användare att registrera data så att andra användare inte kan se dessa data?**

  > **S:** Ja. När användare registrerar data med hjälp av lösenordet återställa portalen för registrering, data sparas i privata autentisering fält som endast är synliga för globala administratörer och användaren.
  >
  >
* **F: Mina användare måste registreras innan de kan använda för återställning av lösenord?**

  > **S:** Nej. Om du definierar tillräckligt med autentiseringsinformation för deras räkning kan inte användare att registrera. Fungerar lösenordsåterställning så länge som du har korrekt formaterade data som lagras i lämpliga fält i katalogen.
  >
  >
* **F: kan jag synkronisera eller ange telefon för autentisering, autentisering e-post eller alternativa autentisering phone fälten åt mina användare?**

  > **S:** de fält som kan ställas in av en Global administratör har definierats i artikeln [SSPR-datakrav](howto-sspr-authenticationdata.md).
  >
  >
* **F: hur avgöra vilka alternativ du ska visa mina användare i portalen för registrering av?**

  > **S:** lösenordsåterställning registrering portalen visar endast de alternativ som du har aktiverat för dina användare. Dessa alternativ finns den **princip för lösenordsåterställning för användare** för din katalog **konfigurera** fliken. Till exempel om du inte aktiverar säkerhetsfrågor, kan sedan användare inte registrera för det valda alternativet.
  >
  >
* **F: när en användare anses vara registrerad?**

  > **S:** anses en användare registrerad för SSPR när de har registrerat minst **antal metoder som krävs för att återställa** ett lösenord som du har angett i den [Azure-portalen](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Lösenordsåterställning

* **F: förhindrar du att användare från flera försök att återställa ett lösenord i en kort tidsperiod?**

  > **S:** Ja, det finns säkerhetsfunktioner som är inbyggda i lösenordsåterställning för att skydda dem från missbruk. 
  >
  > Du kan testa endast fem återställning lösenordsförsök inom 24 timmar innan de har låsts ute i 24 timmar. 
  >
  > Användare kan försöka att validera ett telefonnummer, skicka ett SMS eller verifiera säkerhetsfrågor och svar bara fem gånger inom en timme innan de har låsts ute i 24 timmar. 
  >
  > Användarna kan skicka ett e-postmeddelande högst 10 gånger inom 10 minuter innan de har låsts ute i 24 timmar.
  >
  > Räknarna återställs när en användare återställer sitt lösenord.
  >
  >
* **F: hur lång tid ska vänta tar emot ett e-post, SMS eller telefonsamtal från återställning av lösenord?**

  > **S:** e-post, SMS-meddelanden och samtal ska tas emot i mindre än en minut. I normala fall är 5 till 20 sekunder.
    >Om du inte får meddelandet i det här tidsintervallet:
        > * Kontrollera skräpposten.
        > * Kontrollera att talet eller e-post som kontaktas är det du förväntar dig.
        > * Kontrollera att autentiseringsdata i katalogen är korrekt formaterad exempelvis + 1 4255551234 eller *user@contoso.com*. 
  >
  >
* **F: vilka språk som stöds för återställning av lösenord?**

  > **S:** Användargränssnittet för lösenordsåterställning SMS-meddelanden och röstsamtal är lokaliserade på samma språk som stöds i Office 365.
  >
  >
* **F: vilka delar av lösenordsåterställningen hämta märkesprodukter när jag har angett organisationens företagsanpassning objekten i min katalog är konfigurera fliken?**

  > **S:** portalen för återställning av lösenord visar organisationens logotyp och låter dig konfigurera länken ”Kontakta administratören” så att den pekar till en anpassad e-post eller URL. Alla e-postmeddelanden som skickas av lösenordsåterställning innehåller organisationens logotyp, färger och namn i brödtexten i e-postmeddelandet och har anpassats från inställningarna för det specifika namnet.
  >
  >
* **F: hur kan jag för att informera användarna om vart du ska gå att återställa sina lösenord?**

  > **S:** prova några förslag på vår [SSPR distribution](howto-sspr-deployment.md#sample-communication) artikeln.
  >
  >
* **F: kan jag använda den här sidan från en mobil enhet?**

  > **S:** Ja, den här sidan fungerar på mobila enheter.
  >
  >
* **F: det stöd för att låsa upp lokala Active Directory-konton när användare återställa sina lösenord?**

  > **S:** Ja. När en användare återställer sina lösenord, om tillbakaskrivning av lösenord har distribuerats via Azure AD Connect, låses användarens konto automatiskt när de återställer sina lösenord.
  >
  >
* **F: hur kan jag integrera direkt till min användarens inloggning Skrivbordsmiljö för återställning av lösenord?**

  > **S:** om du är en Azure AD Premium-kund kan du installera Microsoft Identity Manager utan extra kostnad och distribuera lokala lösenordsåterställning av lösningen.
  >
  >
* **F: kan jag ställa in olika säkerhetsfrågor för olika språk?**

  > **S:** Nej, det inte är möjligt i dag.
  >
  >
* **F: hur många frågor kan jag konfigurera för säkerhetsalternativ för autentisering av frågor?**

  > **S:** du kan konfigurera upp till 20 anpassade säkerhetsfrågor i den [Azure-portalen](https://portal.azure.com).
  >
  >
* **F: hur lång tid kan säkerhetsfrågor vara?**

  > **S:** säkerhetsfrågor kan innehålla 3 till 200 tecken.
  >
  >
* **F: hur lång tid kan svar på säkerhetsfrågor vara?**

  > **S:** svar får innehålla 3 till 40 tecken.
  >
  >
* **F: är duplicerade svar på säkerhetsfrågor som avvisade?**

  > **S:** Ja, vi avvisa duplicerade svar på säkerhetsfrågor.
  >
  >
* **F: kan en användare registrera samma säkerhetsfråga mer än en gång?**

  > **S:** Nej. När en användare registrerar en viss fråga, kan de registrera sig för frågan en gång.
  >
  >
* **F: är det möjligt att ange en lägsta gräns på säkerhetsfrågor för registrering och återställa?**

  > **S:** Ja, du kan ange en gräns för registrering och en annan för återställning. Tre till fem säkerhetsfrågor kan krävas för registrering, och tre till fem frågor kan krävas för återställning.
  >
  >
* **F: Jag har konfigurerat min principen så att användarna kan använda säkerhetsfrågor för återställning, men Azure-administratörer verkar konfigureras på olika sätt.**

  > **S:** detta är förväntat beteende. Microsoft tillämpar en stark standardprincip med två gates för lösenordsåterställning för alla Azure-administratörsroller. Detta förhindrar att administratörer med säkerhetsfrågor. Du hittar mer information om den här principen i den [lösenordsprinciper och begränsningar i Azure Active Directory](concept-sspr-policy.md) artikeln.
  >
  >
* **F: om en användare har registrerat mer än det maximala antalet frågor som krävs för återställning, hur säkerhetsfrågorna väljs under återställning?**

  > **S:** *N* antalet säkerhetsfrågor väljs slumpmässigt från det totala antalet frågor en användare har registrerat för var *N* är den mängd som har angetts för den **tal frågor som krävs för att återställa** alternativet. Till exempel om en användare har registrerat fem säkerhetsfrågor, men bara tre krävs för att återställa ett lösenord, tre av de fem frågorna väljs slumpmässigt och presenteras vid återställning. För att förhindra att fråga till, om användaren får svar på frågorna fel urvalsprocessen startar.
  >
  >
* **F: hur lång tid är e-post och SMS enstaka lösenord giltig?**

  > **S:** sessioners livstid för återställning av lösenord är 15 minuter. Användaren har 15 minuter att återställa sina lösenord från början av åtgärden för lösenordsåterställning. E-post och SMS engångskod är ogiltiga när den här tidsperioden har gått ut.
  >
  >
* **F: kan jag hindra användare från att återställa sina lösenord?**

  > **S:** Ja, om du använder en grupp för att aktivera SSPR, du kan ta bort en enskild användare från gruppen där användarna kan återställa sina lösenord. Om användaren är en Global administratör de behåller möjligheten att återställa sina lösenord, och det går inte att inaktivera.
  >
  >

## <a name="password-change"></a>Lösenordsändring

* **F: var ska Mina användare gå för att ändra sina lösenord?**

  > **S:** användare kan ändra sina lösenord överallt där de ser sina profilbild eller ikonen, som i det övre högra hörnet av deras [Office 365](https://portal.office.com) portal eller [åtkomstpanelen](https://myapps.microsoft.com) inträffar. Användare kan ändra sina lösenord från den [Åtkomstpanelsprofil sidan](https://account.activedirectory.windowsazure.com/r#/profile). Användare kan också behöva ändra sina lösenord automatiskt på inloggningssidan för Azure AD om deras lösenord har upphört att gälla. Slutligen användare kan bläddra till den [portal Azure AD-lösenordsändring](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) direkt om de vill ändra sina lösenord.
  >
  >
* **F: kan Mina användare meddelas i Office-portalen när deras lokala lösenord upphör att gälla?**

  > **S:** Ja, det är möjligt i dag om du använder Active Directory Federation Services (AD FS). Om du använder AD FS följer du anvisningarna i den [skicka lösenord princip anspråk med AD FS](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) artikeln. Om du använder synkronisering av lösenordshash kan är det inte möjligt i dag. Vi Synkronisera inte lösenordsprinciper från lokala kataloger, så det inte är möjligt för oss upphör att gälla aviseringar till molnet upplevelser. I båda fallen är det också möjligt att [meddela användare vars lösenord ska upphöra att gälla via PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **F: kan jag hindra användare från att ändra sina lösenord?**

  > **S:** för endast molnbaserade användare lösenordsändringar kan inte blockeras. För lokala användare, kan du ange den **användaren kan inte ändra lösenordet** alternativet för att du har valt. De valda användarna kan inte ändra sina lösenord.
  >
  >

## <a name="password-management-reports"></a>Lösenordshanteringsrapporter

* **F: hur lång tid tar det för de data som visas på lösenordshanteringsrapporter?**

  > **S:** Data ska visas på lösenordshanteringsrapporter i 5-10 minuter. I vissa fall kan det ta upp till en timme visas.
  >
  >
* **F: hur kan jag för att filtrera lösenordshanteringsrapporter?**

  > **S:** för att filtrera lösenordshanteringsrapporter, Välj små förstoringsglaset till extrema höger om kolumnetiketterna längst upp i rapporten. Om du vill göra mer omfattande filtrering kan du ladda ned rapporten till Excel och skapa en pivottabell.
  >
  >
* **F: Vad är det maximala antalet händelser som lagras i lösenordshanteringsrapporter?**

  > **S:** upp till 75 000 lösenord återställning eller lösenord återställning registreringshändelser lagras i lösenordshanteringsrapporter, utsträckning tillbaka så långt det är 30 dagar. Vi arbetar för att expandera det här talet med fler händelser.
  >
  >
* **F: hur långt tillbaka går lösenordshanteringsrapporter?**

  > **S:** lösenordshantering rapporterar show-åtgärder som inträffat under de senaste 30 dagarna. För tillfället, om du vill arkivera dessa data, kan du hämta rapporter med jämna mellanrum och spara dem i en annan plats.
  >
  >
* **F: finns det ett maximalt antal rader som kan visas på lösenordshanteringsrapporter?**

  > **S:** Ja. Upp till 75 000 rader kan visas på något av lösenordshanteringsrapporter, oavsett om de visas i Användargränssnittet eller laddas ned.
  >
  >
* **F: finns det en API för att få åtkomst till återställning av lösenord eller registrering som rapporterar data?**

  > **S:** Ja. Läs hur du kan komma åt lösenordsåterställning reporting dataströmmen i [kan få tillgång till återställning av lösenord rapporteringshändelser programmässigt](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent).
  >
  >

## <a name="password-writeback"></a>Tillbakaskrivning av lösenord

* **F: hur fungerar tillbakaskrivning av lösenord i bakgrunden?**

  > **S:** finns i artikeln [hur tillbakaskrivning av lösenord fungerar](howto-sspr-writeback.md) för en förklaring av vad som händer när du aktiverar tillbakaskrivning av lösenord och hur data flödar genom systemet tillbaka till din lokala miljö.
  >
  >
* **F: hur lång tid tillbakaskrivning av lösenord tar ska fungera? Är det en synkronisering fördröjning har uppstått med hash-synkronisering av lösenord?**

  > **S:** tillbakaskrivning av lösenord är direkt. Det är en synkron pipeline som fungerar grunden annorlunda än synkronisering av lösenordshash. Tillbakaskrivning av lösenord kan du få i realtid feedback om deras återställning av lösenord eller ändra åtgärden. Genomsnittlig tid för en lyckad tillbakaskrivning av lösenord är under 500 ms.
  >
  >
* **F: om mitt lokala konto inaktiveras, hur är min molnkonto och åtkomst påverkas?**

  > **S:** om ditt lokala-ID har inaktiverats kan dina moln-ID och åtkomst kommer också att inaktiveras när nästa synkroniseringsintervall via Azure AD Connect. Den här synkroniseringen har som standard var 30: e minut.
  >
  >
* **F: om mitt lokala konto är begränsad av en lokal Active Directory-lösenordsprincip, SSPR lyder under den här principen om jag ändrar mitt lösenord?**

  > **S:** Ja, SSPR förlitar sig på och följer av den lokala Active Directory-lösenordsprincip. Den här principen innehåller lösenordsprinciper för för vanliga Active Directory-domän, samt några definierade, detaljerade lösenordsprinciper som är riktade till en användare.
  >
  >
* **F: vilka typer av konton fungerar tillbakaskrivning av lösenord för?**

  > **S:** lösenord tillbakaskrivning av fungerar för användarkonton som synkroniseras från en lokal Active Directory till Azure AD, inklusive federerad lösenords-hash som synkroniseras och genomströmning Autentication användare.
  >
  >
* **F: kan framtvinga tillbakaskrivning av lösenord min domän lösenordsprinciper?**

  > **S:** Ja. Tillbakaskrivning av lösenord framtvingar ålder för lösenord, historik, komplexitet, filter och andra begränsningar som du kan utsätta på plats för lösenord i den lokala domänen.
  >
  >
* **F: är det säkert att tillbakaskrivning av lösenord?  Hur vet jag att jag inte kommer över ditt konto?**

  > **S:** Ja, tillbakaskrivning av lösenord är säker. Om du vill läsa mer om flera säkerhetslager implementeras av tjänsten för tillbakaskrivning av lösenord, Kolla in den [säkerhet för tillbakaskrivning av lösenord](concept-sspr-writeback.md#password-writeback-security) i avsnittet den [översikt för tillbakaskrivning av lösenord](howto-sspr-writeback.md) artikeln.
  >
  >

## <a name="next-steps"></a>Nästa steg

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
* [Jag tror att något har gått sönder. Hur gör jag för att felsöka SSPR?](active-directory-passwords-troubleshoot.md)
