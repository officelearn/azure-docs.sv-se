---
title: "Azure Multi-Factor Authentication vanliga frågor och svar | Microsoft Docs"
description: "Vanliga frågor och svar som rör Azure Multi-Factor Authentication. Multi-Factor Authentication är en metod för att verifiera en användares identitet som kräver mer än ett användarnamn och lösenord. Det ger ett ytterligare säkerhetslager användarinloggning och transaktioner."
services: multi-factor-authentication
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: yossib
ms.assetid: 50bb8ac3-5559-4d8b-a96a-799a74978b14
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: joflore
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5b1d48d3ff0d264042f2d282c6b8006e16a251f5
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Vanliga frågor och svar om Azure Multi-Factor Authentication
Det här avsnittet får du svar vanliga frågor om Azure Multi-Factor Authentication och använder multi-Factor Authentication-tjänsten. Den är uppdelad i frågor om tjänsten i allmänhet modeller, användarupplevelser, fakturering och felsökning.

## <a name="general"></a>Allmänt
**F: hur hanterar användardata i Azure Multi-Factor Authentication-servern?**

Användarinformationen är lagrad endast på lokala servrar med Multi-Factor Authentication-servern. Inga beständiga användardata lagras i molnet. När användaren utför tvåstegsverifiering, skickar Multi-Factor Authentication-servern data till Azure Multi-Factor Authentication-Molntjänsten för autentisering. Kommunikation mellan Multi-Factor Authentication-servern och Multi-Factor Authentication-Molntjänsten använder Secure Sockets Layer (SSL) eller Transport Layer Security (TLS) via port 443 utgående.

När autentiseringsbegäranden skickas till Molntjänsten, data samlas in för autentiserings- och rapporter. Datafält som ingår i två steg verifiering loggar är som följer:

* **Unikt ID** (antingen namn eller lokala Multi-Factor Authentication Server Användaridentitet)
* **Första och sista namnet** (valfritt)
* **E-postadress** (valfritt)
* **Telefonnummer** (när du använder en röstsamtal eller SMS-autentisering)
* **Enhetstoken** (när du använder mobilappautentisering)
* **Autentiseringsläge**
* **Resultat för autentisering**
* **Multi-Factor Authentication-servernamn**
* **Multi-Factor Authentication-servern IP**
* **Klienten IP** (om tillgängligt)

De valfria fälten kan konfigureras i Multi-Factor Authentication-servern.

Verifieringen resultatet (lyckades eller nekades) och orsaken om den nekades lagras med autentiseringsdata. Dessa data är tillgängliga i autentisering och användningsrapporter.

## <a name="billing"></a>Fakturering
De flesta faktureringsfrågor kan lösas genom att referera till antingen den [prissättning för multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) eller i dokumentationen om [hur du hämtar Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md).

**F: är min organisation debiteras för telefonsamtal och SMS-meddelanden som används för autentisering?**

Nej, inte debiteras du för enskilda telefonsamtal placeras eller text meddelanden som skickas till användare via Azure Multi-Factor Authentication. Om du använder en MFA-leverantör per autentisering, debiteras du för varje autentisering men inte för den metod som används.

Användarna kan debiteras för telefonsamtal eller textmeddelanden som de får enligt deras personliga telefontjänst.

**F: per användare fakturering modellen debiterar mig för alla aktiverade användare eller bara de som utförs tvåstegsverifiering?**

Fakturering baseras på antalet användare som har konfigurerats för att använda Multifaktorautentisering, oavsett om de utförs tvåstegsverifiering den månaden.

**F: hur fungerar Multi-Factor Authentication fakturering?**

När du skapar en per användare eller per autentisering MFA-leverantören faktureras organisationens Azure-prenumeration månadsvis baserat på användning. Den här fakturering modellen liknar hur Azure fakturerar för användning av virtuella datorer och webbplatser.

När du köper en prenumeration för Azure Multi-Factor Authentication betalar din organisation bara den årliga licensavgiften för varje användare. MFA licenser och Office 365, Azure AD Premium eller Enterprise Mobility + säkerhetspaket debiteras det här sättet. 

Mer information om alternativen i [hur du hämtar Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md).

**F: finns det en gratisversionen av Azure Multi-Factor Authentication?**

I vissa fall Ja.

Multi-Factor Authentication för administratörer för Azure erbjuder en delmängd av Azure MFA-funktioner utan kostnad för åtkomst till Microsoft online services, inklusive portaler för Azure och Office 365-administratör. Det här erbjudandet gäller endast globala administratörer i Azure Active Directory-instanser som inte har den fullständiga versionen av Azure MFA via en MFA-licens, ett paket eller en fristående förbrukningsbaserad provider. Om dina administratörer använda den kostnadsfria versionen och sedan du köpte en fullständig version av Azure MFA, sedan upphöjs alla globala administratörer till betald version automatiskt.

Multi-Factor Authentication för Office 365-användare tillhandahåller en delmängd av Azure MFA-funktioner utan kostnad för åtkomst till Office 365-tjänster, inklusive Exchange Online och SharePoint Online. Det här erbjudandet gäller för användare som har en Office 365-licens som tilldelats, när den motsvarande instansen av Azure Active Directory inte har den fullständiga versionen av Azure MFA via en MFA-licens, ett paket eller en fristående förbrukningsbaserad provider.

**F: kan min organisation växla mellan per användare och per autentisering förbrukning fakturering när som helst?**

Om organisationen köper MFA som en fristående tjänst med förbrukningsbaserad fakturering kan välja du en faktureringsmodell som tillämpas när du skapar en MFA-leverantören. Du kan inte ändra vilken faktureringsmodell som tillämpas när en MFA-provider har skapats. Dock kan du ta bort MFA-leverantör och sedan skapa en med en annan fakturering modell.

När en MFA-provider har skapats kan den länkas till en Azure Active Directory (även kallat ”Azure AD-klient”). Om den aktuella MFA-providern kopplas till en Azure AD-klient kan du på ett säkert sätt bort MFA-leverantör och skapar en som är kopplad till samma Azure AD-klienten. Alternativt, om du har köpt tillräckligt många MFA-, Azure AD Premium- eller EMS-licenser (Enterprise Mobility + Security) för att täcka alla användare som är aktiverade för MFA, kan du ta bort MFA-providern helt och hållet.

Om MFA-leverantören är *inte* kopplad till en Azure AD-klient eller du länka den nya MFA-providern till en annan Azure AD-klient, användarinställningar och konfigurationsalternativ överförs inte. Dessutom måste befintliga Azure MFA-servrar återaktiveras med hjälp av aktiveringsautentiseringsuppgifter som skapas via den nya MFA-providern. Återaktiveringen av MFA-servrarna för att koppla dem till den nya MFA-providern påverkar inte autentiseringen via telefonsamtal eller SMS, men mobilappsaviseringarna slutar fungera för alla användare tills de återaktiverar mobilappen.

Mer information om MFA-leverantörer i [komma igång med en Azure leverantör av Multifaktorautent](multi-factor-authentication-get-started-auth-provider.md).

**F: kan min organisation växla mellan förbrukningsbaserad fakturering och prenumerationer (en licens-baserade model) när som helst?**

I vissa fall Ja.

Om din katalog har en *per användare* Azure Multi-Factor Authentication-providern, kan du lägga till MFA-licenser. Användare med licenser som räknas inte i per användare förbrukningsbaserad fakturering. Användare utan licenser kan fortfarande vara aktiverat för MFA i MFA-provider. Om du köper och tilldela licenser för dina användare som har konfigurerats för att använda Multifaktorautentisering kan du ta bort Azure Multi-Factor Authentication-providern. Du kan alltid skapa en annan användares MFA-leverantör om du har fler användare än licenser i framtiden.

Om din katalog har en *per autentisering* Azure Multi-Factor Authentication provider alltid debiteras du för varje autentisering, så länge MFA-leverantören är länkad till din prenumeration. Du kan tilldela MFA licenser till användare, men du fortfarande faktureras för varje begäran om identitetsverifiering tvåstegsverifiering, om det kommer från någon med en MFA-licens eller inte.

**F: min organisation har att använda och synkronisera identiteter för att använda Azure Multi-Factor Authentication?**

Om organisationen använder en förbrukningsbaserad fakturering modell, är Azure Active Directory valfritt, men krävs inte. Om MFA-leverantören inte är kopplad till en Azure AD-klient, kan du bara distribuera Azure Multi-Factor Authentication-servern eller den Azure Multi-Factor Authentication SDK lokalt.

Azure Active Directory krävs för licens-modellen eftersom licenser läggs till Azure AD-klienten när du köper och tilldela dem till användare i katalogen.

## <a name="manage-and-support-user-accounts"></a>Hantera och stöd för användarkonton

**F: Vad ska jag säga Mina användare kan göra om de inte kan ta emot ett svar på telefonen eller inte har telefonen med dem?**

Alla användare konfigureras förhoppningsvis mer än en verifieringsmetod. Be dem att försöka logga in igen, men välj en annan verifieringsmetod på inloggningssidan.

Du kan ange att användarna ska den [slutanvändarens felsökningsguiden](./end-user/multi-factor-authentication-end-user-troubleshoot.md).


**F: Vad gör jag om en av användarna går inte att få sitt konto?**

Du kan återställa användarens konto genom att göra dem att gå via registreringsprocessen igen. Lär dig mer om [hantera inställningar för användare och enhet med Azure Multi-Factor Authentication i molnet](multi-factor-authentication-manage-users-and-devices.md).

**F: Vad gör jag om en av Mina användare förlorar en telefon som använder applösenord?**

Ta bort alla användares applösenord för att förhindra obehörig åtkomst. När användaren har en motsvarighet enhet, kan de återskapa lösenorden. Lär dig mer om [hantera inställningar för användare och enhet med Azure Multi-Factor Authentication i molnet](multi-factor-authentication-manage-users-and-devices.md).

**F: Vad händer om en användare kan logga in på icke-webbläsarbaserade appar?**

Om din organisation använder fortfarande äldre klienter och [tillåtna användningen av applösenord](multi-factor-authentication-whats-next.md#app-passwords), och sedan användarna inte logga in dessa äldre klienter med sitt användarnamn och lösenord. I stället de behöver [ställa in applösenord](./end-user/multi-factor-authentication-end-user-app-passwords.md). Användarna måste rensa (ta bort) sin inloggningsinformation, starta om appen och sedan logga in med sina användarnamn och *applösenord* i stället för vanliga lösenordet.

Om din organisation inte har äldre klienter, bör du inte låta användarna skapa applösenord.

> [!NOTE]
> Modern autentisering för Office 2013-klienter
>
> Applösenord krävs endast för appar som inte stöder modern autentisering. Office 2013-klienter stöder modern autentiseringsprotokoll, men måste konfigureras. Nyare Office-klienterna har automatiskt stöd för modern autentiseringsprotokoll. Mer information finns i [Office 2013 modern autentisering förhandsversion meddelande](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

**F: Mina användare säger att ibland de inte textmeddelandet, eller de svarar på dubbelriktad textmeddelanden men verifieringen timeout.**

Överföringen av textmeddelanden och mottagande av svar i dubbelriktad SMS är inte garanterat eftersom det finns fungerar faktorer som kan påverka tillförlitligheten hos tjänsten. Dessa faktorer är målet land, en mobiltelefon operatör och signalstyrka.

Om användarna ofta har problem med att ta emot textmeddelanden på ett tillförlitligt sätt, be dem att använda metoden mobila app eller telefonsamtal i stället. Mobilappen kan ta emot meddelanden både över mobilnät och Wi-Fi-anslutningar. Mobilappen kan dessutom generera verifieringskoder även när enheten har ingen signal alls. Microsoft Authenticator-appen är tillgänglig för [Android](http://go.microsoft.com/fwlink/?Linkid=825072), [IOS](http://go.microsoft.com/fwlink/?Linkid=825073), och [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

Om du måste använda textmeddelanden, bör du använda enkelriktad SMS i stället för dubbelriktad SMS när det är möjligt. Enkelriktad SMS är mer tillförlitlig och förhindrar du att användarna medför globala SMS avgifter från svara på ett textmeddelande som skickats från ett annat land.

**F: kan jag ändra tidsperiod som användarna måste ange verifieringskoden från meddelandet innan tidsgränsen uppnås för systemet?**

I vissa fall Ja. 

För enkelriktad SMS med Azure MFA-Server v7.0 eller högre, kan du konfigurera timeout inställningen genom att ange en registernyckel. När Molntjänsten MFA skickar textmeddelandet, returneras Verifieringskod (eller engångskod) till MFA-servern. MFA-servern lagrar koden i minnet i 300 sekunder som standard. Om användaren inte ange koden innan 300 sekunder, nekas sin autentisering. Följ dessa steg om du vill ändra standardinställningen för timeout:

1. Gå till HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor.
2. Skapa en DWORD-registernyckel som kallas **pfsvc_pendingSmsTimeoutSeconds** och ange tiden i sekunder som du vill att Azure MFA-Server för att lagra enstaka lösenord.

>[!TIP] 
>Om du har flera servrar för MFA vet bara det som bearbetas ursprungliga autentiseringsbegäran verifieringskoden som skickades till användaren. Om användaren anger koden, skickas autentiseringsbegäran valideras till samma server. Om validering kod som skickas till en annan server, nekas autentiseringen. 

Du kan konfigurera timeout-inställningen i MFA-hanteringsportalen för dubbelriktad SMS med Azure MFA-Server. Om användarna inte svara på SMS inom den angivna tidsgränsen, nekas sin autentisering. 

Du kan konfigurera timeout-inställningen för enkelriktad SMS med Azure MFA i molnet (inklusive AD FS-adaptern eller filnamnstillägget Network Policy Server). Azure AD lagras verifieringskoden 180 sekunder. 

**F: kan jag använda maskinvarutoken med Azure Multi-Factor Authentication-servern?**

Om du använder Azure Multi-Factor Authentication-servern, kan du importera från tredje part öppna autentisering (OATH) utifrån enstaka lösenord (TOTP)-token och använda dem för tvåstegsverifiering.

Du kan använda ActiveIdentity token som TOTP OATH-token om du placerar den hemliga nyckeln i en CSV-fil och importera till Azure Multi-Factor Authentication-servern. Du kan använda OATH-token med Active Directory Federation Services (ADFS), formulärbaserad autentisering för Internet Information Server (IIS) och RADIUS Remote Authentication Dial-In User Service () som klientsystemet kan acceptera indata från användaren.

Du kan importera från tredje part TOTP OATH-token med följande format:  

- Bärbar symmetriska nyckelbehållare (PSKC)  
- CSV-fil om filen innehåller ett serienummer, hemlig nyckel i Base-32-format och ett tidsintervall  

**F: kan jag använda Azure Multi-Factor Authentication-servern för att skydda Terminal Services?**

Ja, men om du använder Windows Server 2012 R2 eller senare du bara skydda Terminal Services genom att använda fjärrskrivbordsgateway (RD Gateway).

Ändringarna i Windows Server 2012 R2 kan du ändra hur Azure Multi-Factor Authentication-servern ansluter till säkerhetspaketet Local Security Authority (LSA) i Windows Server 2012 och tidigare versioner. Versioner av Terminal Services i Windows Server 2012 eller tidigare, kan du [ett program med Windows-autentisering](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Om du använder Windows Server 2012 R2 måste RD Gateway.

**F: jag konfigurerats Uppringarens ID i MFA-Server, men användarna fortfarande ta emot Multi-Factor Authentication-samtal från en anonym anroparen.**

När Multi-Factor Authentication-samtal görs via nätverket offentliga telefon, dirigeras ibland de via en operatör som inte stöder anroparen-ID. Därför kan Uppringarens ID inte garanteras, även om systemets Multi-Factor Authentication skickar alltid.

**F: Varför är min användare uppmanas att registrera sina säkerhetsinformation?**
Det finns flera skäl till att användarna kan också uppmanas att registrera sina säkerhetsinformation:

- Användaren har aktiverats för MFA av en administratör i Azure AD, men saknar säkerhetsinformation ännu registrerats för sitt konto.
- Användaren har aktiverats för självbetjäning för återställning av lösenord i Azure AD. Säkerhetsinformation hjälper dem återställa sina lösenord i framtiden om de skulle glömma.
- Användaren komma åt ett program som har en princip för villkorlig åtkomst att kräva MFA och tidigare har inte registrerats för MFA.
- Användaren som registrerar en enhet med Azure AD (inklusive Azure AD Join), och din organisation kräver MFA för registrering av enheten, men användaren tidigare har registrerats inte för MFA.
- Användaren genererar Windows Hello för företag i Windows 10 (vilket kräver MFA) och tidigare har inte registrerats för MFA.
- Organisationen har skapat och aktiverat en princip för registrering av MFA som har tillämpats på användaren.
- Användaren tidigare registrerad för MFA, men det har valt en verifieringsmetod som en administratör har inaktiverat sedan. Användaren måste därför genomgå MFA-registrering igen för att välja en ny standard verifieringsmetod.


## <a name="errors"></a>Fel
**F: vad användarna gör om visas ett felmeddelande om ”verifieringsbegäran avser inte ett aktiverat konto” när du använder meddelanden för mobila appar?**

Be dem att följa den här proceduren för att ta bort kontot från mobilappen och sedan lägga till den igen:

1. Gå till [Azure portal profilen](https://account.activedirectory.windowsazure.com/profile/) och logga in med ditt organisationskonto.
2. Välj **ytterligare säkerhetsverifiering**.
3. Ta bort det befintliga kontot från mobilappen.
4. Klicka på **konfigurera**, och följ sedan anvisningarna för att konfigurera mobilappen.

**F: vad användarna gör om visas ett felmeddelande om 0x800434D4L när du loggar in till ett icke-webbläsarbaserade program?**

0x800434D4L fel uppstår när du försöker logga in på en icke-webbläsarprogram, installeras på en lokal dator som inte fungerar med konton som kräver tvåstegsverifiering.

En lösning för det här felet är att ha separata användaren konton för admin-relaterade och åtgärder för icke-administratörer. Senare kan du länka postlådor mellan din administratörskonto och icke-administratörskontot så att du kan logga in till Outlook med ditt administratörskonto. Mer information om den här lösningen lär du dig hur du [ger administratörer möjligheten att öppna och visa innehållet i en användares postlåda](http://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Nästa steg
Om din fråga inte besvaras här, lämna den i kommentarer längst ned på sidan. Eller ytterligare alternativ för att få hjälp:

* Sök i [Microsoft Support Knowledge Base](https://www.microsoft.com/en-us/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport) efter lösningar på vanliga tekniska problem.
* Söka efter och bläddra tekniska frågor och svar från gemenskapen eller egna fråga i den [Azure Active Directory-forum](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).
* Om du är en äldre PhoneFactor-kund och du har frågor eller behöver hjälp med att återställa ett lösenord, Använd den [lösenordsåterställning](mailto:phonefactorsupport@microsoft.com) länk för att öppna ett supportärende.
* Kontakta en supporttekniker via [stöd för Azure Multi-Factor Authentication Server (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947). När du kontaktar oss är det bra om du kan ange så mycket information om problemet som möjligt. Information som du kan ange innehåller sidan där du såg felet, felkod, specifika sessions-ID och ID för den användare som såg felet.
