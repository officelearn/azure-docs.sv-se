---
title: Azure Multi-Factor Authentication vanliga frågor och svar | Microsoft Docs
description: Vanliga frågor och svar som rör Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 58992c80344902674b2b21a71b07925c752911a4
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51230959"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Vanliga frågor och svar om Azure Multi-Factor Authentication

HÄR får du svar på vanliga frågor om Azure Multi-Factor Authentication och med Multi-Factor Authentication-tjänsten. Det är uppdelad i frågor om tjänsten i allmänhet faktureringsmodellerna, användarupplevelser, och felsökning.

## <a name="general"></a>Allmänt

**F: hur hanterar användardata i Azure Multi-Factor Authentication-servern?**

Med Multi-Factor Authentication Server lagras användardata endast på lokala servrar. Inga beständiga användardata lagras i molnet. När användaren utför tvåstegsverifiering, skickar data till Azure Multi-Factor Authentication-Molntjänsten för autentisering med hjälp av Multi-Factor Authentication Server. Kommunikation mellan Multi-Factor Authentication-servern och Multi-Factor Authentication-Molntjänsten använder Secure Sockets Layer (SSL) eller Transport Layer Security (TLS) via port 443 för utgående trafik.

När autentiseringsbegäranden skickas till Molntjänsten, data har samlats in för autentisering och användning rapporterar. Datafält som ingår i två steg verifiering loggar är följande:

* **Unikt ID** (antingen användarens eller den lokala Multi-Factor Authentication Server ID)
* **Första- och efternamn** (valfritt)
* **E-postadress** (valfritt)
* **Telefonnummer** (när du använder röstsamtal eller SMS-autentisering)
* **Enhetstoken** (när du använder mobilappautentisering)
* **Autentiseringsläge**
* **Autentiseringsresultat**
* **Multi-Factor Authentication-servernamn**
* **Multi-Factor Authentication Server IP**
* **Klientens IP** (om tillgängligt)

Valfria fält kan konfigureras i Multi-Factor Authentication Server.

Verifikationsresultat (lyckades eller nekades) och orsaken om den nekades lagras med autentiseringsinformationen. Informationen är tillgänglig i autentisering och användningsrapporter.

**F: vilka korta SMS-koder som används för att skicka SMS-meddelanden till Mina användare?**

I USA Microsoft använder du följande koder för SMS-kort:

   * 97671
   * 69829
   * 51789
   * 99399

I Kanada Microsoft använder du följande koder för SMS-kort:

   * 759731 
   * 673801

Microsoft garanterar inte konsekvent SMS eller Voice-baserade Multifaktorautentisering fråga leverans av samma nummer. För våra användare Microsoft Lägg till eller ta bort korta koder när som helst som vi justera väg att förbättra SMS levererbarhet. Microsoft stöder inte korta koder för länder utöver USA och Kanada

## <a name="billing"></a>Fakturering

De flesta faktureringsfrågor besvaras genom att referera till antingen den [prissättning för multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) eller i dokumentationen om [så här hämtar du Azure Multi-Factor Authentication](concept-mfa-licensing.md).

**F: är min organisation debiteras för telefonsamtal och textmeddelanden som används för autentisering?**

Nej, du debiteras inte för enskilda telefonsamtal eller SMS meddelanden som skickas till användare via Azure Multi-Factor Authentication. Om du använder en MFA-provider per autentisering, debiteras du för varje autentisering men inte för den metod som används.

Användarna kan debiteras för telefonsamtal eller SMS som de får enligt deras personliga telefontjänst.

**F: faktureringsmodellen per användare debiterar mig för alla aktiverade användare eller bara de som utförs tvåstegsverifiering?**

Fakturering baseras på antalet användare som har konfigurerats för att använda Multi-Factor Authentication, oavsett om de utförs tvåstegsverifiering den månaden.

**F: hur fungerar fakturering för multi-Factor Authentication?**

När du skapar en per användare eller per autentisering MFA-provider, faktureras din organisations Azure-prenumeration per månad baserat på användning. Detta faktureringsmodellen liknar hur Azure fakturor för användningen av virtuella datorer och webbplatser.

När du köper en prenumeration för Azure Multi-Factor Authentication kan betalar organisationen bara årsavgift för licens för varje användare. MFA-licenser och Office 365, Azure AD Premium eller Enterprise Mobility + Security paket debiteras det här sättet. 

Läs mer om alternativen i [så här hämtar du Azure Multi-Factor Authentication](concept-mfa-licensing.md).

**F: finns det en kostnadsfri version av Azure Multi-Factor Authentication?**

I vissa fall Ja.

Multi-Factor Authentication för administratörer för Azure erbjuder en delmängd av Azure MFA-funktioner utan kostnad för åtkomst till Microsoft online services, inklusive portalerna för Azure och Office 365-administratör. Det här erbjudandet gäller endast globala administratörer i Azure Active Directory-instanser som inte har den fullständiga versionen av Azure MFA via en MFA-licens, ett paket eller en fristående förbrukningsbaserad provider. Om dina administratörer använder den kostnadsfria versionen och sedan du köpa en fullständig version av Azure MFA, sedan upphöjs alla globala administratörer till den betalda versionen automatiskt.

Multi-Factor Authentication för Office 365-användare erbjuder en delmängd av Azure MFA-funktioner utan kostnad för åtkomst till Office 365-tjänster, inklusive Exchange Online och SharePoint Online. Det här erbjudandet gäller för användare som har en Office 365-licens som tilldelas när den motsvarande instansen av Azure Active Directory inte har den fullständiga versionen av Azure MFA via en MFA-licens, ett paket eller en fristående förbrukningsbaserad provider.

**F: kan organisationen växlar mellan per användare och per autentisering förbrukning faktureringsmodellerna när som helst?**

Om din organisation köper MFA som en fristående tjänst med konsumtionsbaserad fakturering kan välja du faktureringsmodellen när du skapar en MFA-provider. Du kan inte ändra faktureringsmodellen när en MFA-provider har skapats. Du kan dock ta bort MFA-providern och sedan skapa en med en annan faktureringsmodell.

När en MFA-provider skapas får vara kopplade till en Azure Active Directory (även kallat ”Azure AD-klient”). Om den aktuella MFA-providern kopplas till en Azure AD-klient kan du på ett säkert sätt ta bort MFA-providern och skapa en som är länkad till samma Azure AD-klienten. Alternativt, om du har köpt tillräckligt många MFA-, Azure AD Premium- eller EMS-licenser (Enterprise Mobility + Security) för att täcka alla användare som är aktiverade för MFA, kan du ta bort MFA-providern helt och hållet.

Om MFA-provider är *inte* länkad till en Azure AD-klient, eller om du kopplar den nya MFA-providern till en annan Azure AD-klient, användarinställningar och konfigurationsalternativ inte överförs. Dessutom måste befintliga Azure MFA-servrar återaktiveras med hjälp av aktiveringsautentiseringsuppgifter som skapas via den nya MFA-providern. Återaktiveringen av MFA-servrarna för att koppla dem till den nya MFA-providern påverkar inte autentiseringen via telefonsamtal eller SMS, men mobilappsaviseringarna slutar fungera för alla användare tills de återaktiverar mobilappen.

Mer information om MFA-leverantörer i [komma igång med Azure Multi-Factor Authentication-Provider](concept-mfa-authprovider.md).

**F: kan organisationen växlar mellan konsumtionsbaserad fakturering och prenumerationer (en licens-baserade modellen) när som helst?**

I vissa fall Ja.

Om din katalog har en *per användare* Azure Multi-Factor Authentication-providern, kan du lägga till MFA-licenser. Användare med licenser räknas inte i fakturering förbrukningsbaserad per användare. Användare utan licenser kan fortfarande aktiveras för MFA via MFA-providern. Om du köper och tilldela licenser för alla dina användare som har konfigurerats för att använda Multi-Factor Authentication kan du ta bort den Azure Multi-Factor Authentication-providern. Du kan alltid skapa ett annat MFA-provider per användare om du har fler användare än licenser i framtiden.

Om din katalog har en *per autentisering* Azure Multi-Factor Authentication-providern, alltid debiteras du för varje autentisering, förutsatt att MFA-providern är kopplad till din prenumeration. Du kan tilldela MFA-licenser till användare, men du kommer fortfarande att debiteras för varje förfrågningar om tvåstegsverifiering, om den kommer från någon med en MFA-licens eller inte.

**F: min organisation har du använder och synkronisera identiteter för att använda Azure Multi-Factor Authentication?**

Om din organisation använder en förbrukningsbaserad faktureringsmodellen, är Azure Active Directory valfritt, men krävs inte. Om MFA-providern inte är länkad till en Azure AD-klient kan du bara distribuera Azure Multi-Factor Authentication Server lokalt.

Azure Active Directory krävs för licens-modellen eftersom licenser läggs till Azure AD-klienten när du köper och tilldela dem till användare i katalogen.

## <a name="manage-and-support-user-accounts"></a>Hantera och stöd användarkonton

**F: Vad ska jag säga Mina användare ska jag göra om de inte får ett svar på sin telefon eller inte har telefonen med dem?**

Alla användare konfigurerats förhoppningsvis mer än en verifieringsmetod. Be dem att försöka logga in igen, men välj en annan verifieringsmetod på inloggningssidan.

Du kan ange dina användare till den [slutanvändarens felsökningsguide för](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

**F: Vad gör jag om en av Mina användare inte kan få deras konto?**

Du kan återställa användarens konto genom att göra dem gå igenom registreringsprocessen igen. Läs mer om [hantera användar- och enhetsinställningar med Azure Multi-Factor Authentication i molnet](howto-mfa-userdevicesettings.md).

**F: Vad gör jag om en av Mina användare förlorar en telefon som använder applösenord?**

Ta bort alla användares lösenord för att förhindra obehörig åtkomst. När användaren har en ersättningsenhet, kan de återskapa lösenorden. Läs mer om [hantera användar- och enhetsinställningar med Azure Multi-Factor Authentication i molnet](howto-mfa-userdevicesettings.md).

**F: Vad händer om en användare kan inte logga in på icke-webbläsarbaserade appar?**

Om din organisation använder fortfarande äldre klienter, och du [tillåtna användningen av applösenord](howto-mfa-mfasettings.md#app-passwords), och användarna inte kan logga in på dessa äldre klienter med sitt användarnamn och lösenord. I stället de behöver för att [ställa in applösenord](../user-help/multi-factor-authentication-end-user-app-passwords.md). Användarna måste rensa (ta bort) sin inloggningsinformation, starta om appen och logga sedan in med sitt användarnamn och *applösenord* i stället för sina vanliga lösenord.

Om din organisation inte har äldre klienter, bör du inte tillåta användarna att skapa applösenord.

> [!NOTE]
> Modern autentisering för Office 2013-klienter
>
> Applösenord krävs endast för appar som inte stöder modern autentisering. Office 2013 klienter stöder moderna autentiseringsprotokoll, men måste konfigureras. Nyare Office-klienter har automatiskt stöd för moderna autentiseringsprotokoll. Mer information finns i den [Office 2013 meddelande om offentlig förhandsversion av modern autentisering](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

**F: Mina användare säger att ibland de inte fått textmeddelandet, eller de svarar på dubbelriktade textmeddelanden men tidsgränsen uppnås för verifiering.**

Leverans av textmeddelanden och mottagande av svar i dubbelriktad SMS garanteras inte eftersom det inte finns fungerar faktorer som kan påverka tillförlitligheten för tjänsten. Dessa faktorer omfattar mål-land, mobiltelefon-operatör och signalstyrka.

Om användarna ofta har problem med att på ett tillförlitligt sätt ta emot textmeddelanden, berätta för dem att använda metoden mobila app eller telefonsamtal i stället. Den mobila appen kan ta emot meddelanden både över mobilnät och Wi-Fi-anslutningar. Dessutom kan den mobila appen generera verfieringskoder även om enheten inte har någon signal alls. Microsoft Authenticator-appen är tillgänglig för [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [IOS](https://go.microsoft.com/fwlink/?Linkid=825073), och [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071).

Om du måste använda textmeddelanden, bör du använda envägs-SMS i stället för dubbelriktad SMS när det är möjligt. Enkelriktad SMS är mer tillförlitlig och det förhindrar att användare ska globala SMS från besvarar ett textmeddelande som skickats från ett annat land.

**F: kan jag ändra Mina användare behöva ange verifieringskoden från ett textmeddelande innan tidsgränsen uppnås för systemet tiden?**

I vissa fall kan Ja. 

Enkelriktad SMS med Azure MFA Server v7.0 eller senare, kan du konfigurera för timeout inställningen av inställningen en registernyckel. När MFA-Molntjänsten skickar textmeddelandet, returneras Verifieringskod (eller engångslösenord) till MFA-servern. MFA-servern lagrar koden i minnet i 300 sekunder som standard. Om användaren inte ange koden innan 300 sekunder, nekas autentiseringen. Använd de här stegen om du vill ändra standardinställningen för tidsgräns:

1. Gå till HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor.
2. Skapa en DWORD-registernyckel med namnet **pfsvc_pendingSmsTimeoutSeconds** och ange tid i sekunder som du vill att Azure MFA-servern att lagra enstaka lösenord.

>[!TIP] 
>Om du har flera MFA-servrar, vet bara det som bearbetas ursprungliga autentiseringsbegäran verifieringskoden som har skickats till användaren. När användaren anger koden, måste autentiseringsbegäran valideras skickas till samma server. Om valideringen koden skickas till en annan server, nekas autentiseringen. 

Du kan konfigurera timeout-inställningen för dubbelriktad SMS med Azure MFA Server i MFA-hanteringsportalen. Om användarna inte svara på SMS inom tidsgränsen för definierade, nekas autentiseringen. 

Du kan inte konfigurera timeout-inställningen för enkelriktad SMS med Azure MFA i molnet (inklusive AD FS-adaptern eller NPS-tillägget). Azure AD lagrar verifieringskoden i 180 sekunder. 

**F: kan jag använda maskinvarutoken med Azure Multi-Factor Authentication-servern?**

Om du använder Azure Multi-Factor Authentication Server kan du importera från tredje part öppen autentisering (OATH) tidsbaserade, enstaka lösenord (TOTP)-token och sedan använda dem för tvåstegsverifiering.

Du kan använda ActiveIdentity tokens som TOTP OATH-token om du placerar den hemliga nyckeln i en CSV-fil och importera till Azure Multi-Factor Authentication-servern. Du kan använda OATH-token med Active Directory Federation Services (ADFS), Internet Information Server (IIS)-formulärbaserad autentisering och Remote Authentication Dial-In User Service (RADIUS) så länge klientsystemet kan acceptera indata från användaren.

Du kan importera från tredje part TOTP OATH-token med följande format:  

- Bärbar symmetriska nyckelbehållare (PSKC)  
- CSV om filen innehåller ett serienummer, en hemlig nyckel i Base-32-format och ett tidsintervall  

**F: kan jag använda Azure Multi-Factor Authentication-servern för att skydda Terminal Services?**

Ja, men om du använder Windows Server 2012 R2 eller senare du bara skydda Terminal Services genom att använda fjärrskrivbordsgateway (RD Gateway).

Säkerhetsändringar i Windows Server 2012 R2 kan du ändra hur Azure Multi-Factor Authentication-servern ansluter till säkerhetspaketet Local Security Authority (LSA) i Windows Server 2012 och tidigare versioner. För versioner av Terminal Services i Windows Server 2012 eller tidigare kan du [skydda ett program med Windows-autentisering](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Om du använder Windows Server 2012 R2, måste RD Gateway.

**F: Jag har konfigurerat Nummerpresentation i MFA-servern, men Mina användare fortfarande ta emot Multi-Factor Authentication-samtal från en anonym anroparen.**

När Multi-Factor Authentication-samtal görs via det offentliga telefon-nätverket, skickas ibland de genom en operatör som inte stöder anroparen-ID. Därför garanteras inte Uppringarens ID, även om Multi-Factor Authentication-systemet skickar alltid.

**F: Varför Mina användare att uppmanas att registrera deras säkerhetsinformation?**
Det finns flera orsaker till att du kan uppmanas användarna att registrera deras säkerhetsinformation:

- Användaren har aktiverats för MFA av administratören i Azure AD, men har inte säkerhetsinformation som har registrerats för sitt konto.
- Användaren har aktiverats för lösenord för självbetjäning i Azure AD. Säkerhetsinformationen kommer hjälpa dem återställa sina lösenord igen om de skulle glömma.
- Användaren åtkomst till ett program som har en villkorlig åtkomst för att kräva MFA och tidigare har inte registrerats för MFA.
- Användaren som registrerar en enhet med Azure AD (inklusive Azure AD Join), och din organisation kräver MFA för registrering av enheten, men användaren har inte tidigare har registrerats för MFA.
- Användaren genererar Windows Hello för företag i Windows 10 (som kräver MFA) och tidigare har inte registrerats för MFA.
- Organisationen har skapat och aktiverat en princip för MFA-registrering som har tillämpats på användaren.
- Användaren kan du tidigare har registrerats för MFA, men valde en verifieringsmetod som en administratör har inaktiverat eftersom. Användaren måste därför gå igenom MFA-registrering igen för att välja en ny standard verifieringsmetod.

## <a name="errors"></a>Fel

**F: Vad ska användare göra om de finns i felmeddelandet ”autentiseringsbegäran är inte för ett aktiverat konto” när du använder aviseringar i mobilappen?**

Be dem att följa den här proceduren för att ta bort kontot från mobilappen, och sedan lägga till den igen:

1. Gå till [din Azure-portalen profil](https://account.activedirectory.windowsazure.com/profile/) och logga in med ditt organisationskonto.
2. Välj **ytterligare säkerhetsverifiering**.
3. Ta bort det befintliga kontot från mobilappen.
4. Klicka på **konfigurera**, och följ sedan anvisningarna för att konfigurera om den mobila appen.

**F: Vad ska användare göra om de finns i ett 0x800434D4L felmeddelande vid inloggning till ett icke-webbläsarbaserade program?**

0x800434D4L felet uppstår vid försök att logga in på ett icke-webbläsarbaserade program som installerats på en lokal dator som inte fungerar med konton som kräver tvåstegsverifiering.

En lösning för det här felet är att ha en separat användare konton för admin-relaterade och åtgärder för icke-administratörer. Senare kan länka du postlådor mellan ditt administratörskonto och icke-administratörskontot så att du kan logga in till Outlook med ditt administratörskonto. För mer information om den här lösningen kan du lära dig hur du [och ge en administratör kunna öppna och visa innehållet i en användares postlåda](http://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Nästa steg

Om din fråga inte besvaras här, lämna den i kommentarer längst ned på sidan. Alternativt kan du här finns ett par ytterligare alternativ för att få hjälp:

* Sök efter den [Microsoft Support Knowledge Base](https://www.microsoft.com/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport) efter lösningar på vanliga tekniska problem.
* Söka efter och bläddra tekniska frågor och svar från diskussionsgruppen eller din egen fråga i den [Azure Active Directory-forum](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).
* Om du är en äldre PhoneFactor-kund och du har frågor eller behöver hjälp med att återställa ett lösenord, Använd den [lösenordsåterställning](mailto:phonefactorsupport@microsoft.com) länk för att öppna ett supportärende.
* Kontakta en supporttekniker via [support för Azure Multi-Factor Authentication Server (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947). När du kontaktar oss, är det bra om du kan inkludera så mycket information om problemet som möjligt. Information som du kan ange innehåller sidan där du såg felet, felkod, specifika sessions-ID och ID för den användare som såg felet.
