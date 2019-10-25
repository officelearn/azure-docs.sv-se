---
title: Vanliga frågor och svar om Azure Multi-Factor Authentication – Azure Active Directory
description: Vanliga frågor och svar om Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23ecec4d0701f6f55385937f872151a373b2f62e
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72808106"
---
# <a name="frequently-asked-questions-about-azure-multi-factor-authentication"></a>Vanliga frågor och svar om Azure Multi-Factor Authentication

I det här avsnittet besvaras vanliga frågor om Azure Multi-Factor Authentication och tjänsten Multi-Factor Authentication. Den är uppdelad i frågor om tjänsten i allmänhet, fakturerings modeller, användar upplevelser och fel sökning.

## <a name="general"></a>Allmänt

**F: Hur hanterar Azure Multi-Factor Authentication-server användar data?**

Med Multi-Factor Authentication-server lagras användar data endast på lokala servrar. Inga beständiga användardata lagras i molnet. När användaren utför tvåstegsverifiering skickar Multi-Factor Authentication-server data till Azure Multi-Factor Authentication moln tjänsten för autentisering. Kommunikation mellan Multi-Factor Authentication-server och Multi-Factor Authentication moln tjänsten använder Secure Sockets Layer (SSL) eller Transport Layer Security (TLS) över port 443 utgående.

När autentiseringsbegäranden skickas till moln tjänsten samlas data in för autentiserings-och användnings rapporter. Data fält som ingår i två stegs verifierings loggar är följande:

* **Unikt ID** (antingen användar namn eller lokalt Multi-Factor Authentication-Server-ID)
* För- **och efter namn** (valfritt)
* **E-postadress** (valfritt)
* **Telefonnummer** (när du använder ett röst samtal eller SMS-autentisering)
* **Enhets-token** (när du använder autentisering med mobil program)
* **Autentiseringsläge**
* **Autentiserings resultat**
* **Multi-Factor Authentication-server namn**
* **Multi-Factor Authentication-server IP**
* **Klientens IP-adress** (om tillgänglig)

De valfria fälten kan konfigureras i Multi-Factor Authentication-server.

Verifierings resultatet (lyckades eller nekas), och orsaken till att den nekades, lagras med autentiseringsdata. Dessa data är tillgängliga i autentiserings-och användnings rapporter.

**F: vilka SMS-koder används för att skicka SMS-meddelanden till mina användare?**

I USA Microsoft använder följande SMS-kort koder:

   * 97671
   * 69829
   * 51789
   * 99399

I Kanada använder Microsoft följande SMS-kort koder:

   * 759731 
   * 673801

Microsoft garanterar inte konsekvent SMS eller Voice-baserad Multi-Factor Authentication uppleverans av samma nummer. Med hänsyn till våra användare kan Microsoft lägga till eller ta bort korta koder när vi gör väg justeringar för att förbättra SMS-leveranser. Microsoft stöder inte korta koder för länder/regioner utöver USA och Kanada.

## <a name="billing"></a>Fakturering

De flesta fakturerings frågor kan besvaras genom att referera till antingen [sidan Multi-Factor Authentication priser](https://azure.microsoft.com/pricing/details/multi-factor-authentication/) eller dokumentationen om [hur du skaffar Azure Multi-Factor Authentication](concept-mfa-licensing.md).

**F: debiteras min organisation för att skicka telefonsamtal och textmeddelanden som används för autentisering?**

Nej, du debiteras inte för enskilda telefonsamtal eller SMS-meddelanden som skickas till användare via Azure Multi-Factor Authentication. Om du använder en MFA-Provider per autentisering debiteras du för varje autentisering men inte för den metod som används.

Dina användare kan debiteras för telefonsamtal eller textmeddelanden som de får, enligt deras personliga telefon tjänst.

**F: kostar fakturerings modellen per användare för alla aktiverade användare eller bara de som utförde tvåstegsverifiering?**

Faktureringen baseras på antalet användare som kon figurer ATS för att använda Multi-Factor Authentication, oavsett om de utförde tvåstegsverifiering i månaden.

**F: Hur fungerar Multi-Factor Authentication fakturering?**

När du skapar en MFA-Provider per användare eller per autentisering faktureras din organisations Azure-prenumeration varje månad baserat på användning. Den här fakturerings modellen liknar hur Azure-fakturor används för att använda virtuella datorer och webbplatser.

När du köper en prenumeration för Azure Multi-Factor Authentication betalar din organisation endast den årliga licens avgiften för varje användare. MFA-licenser och Office 365-, Azure AD Premium-eller Enterprise Mobility + Security-paket faktureras på det här sättet. 

Läs mer om dina alternativ i [hur du får Azure-Multi-Factor Authentication](concept-mfa-licensing.md).

**F: finns det en kostnads fri version av Azure Multi-Factor Authentication?**

I vissa fall Ja.

Multi-Factor Authentication för Azure-administratörer erbjuder en delmängd av Azure MFA-funktionerna utan kostnad för åtkomst till Microsoft-onlinetjänster, inklusive [Azure Portal](https://portal.azure.com) och [Microsoft 365 administrations Center](https://admin.microsoft.com). Det här erbjudandet gäller endast globala administratörer i Azure Active Directory instanser som inte har den fullständiga versionen av Azure MFA via en MFA-licens, ett paket eller en fristående konsumtions-baserad Provider. Om dina administratörer använder den kostnads fria versionen och sedan köper en fullständig version av Azure MFA, är alla globala administratörer utökade till den betalda versionen automatiskt.

Multi-Factor Authentication för Office 365-användare har en delmängd av Azure MFA-funktioner utan kostnad för åtkomst till Office 365-tjänster, inklusive Exchange Online och SharePoint Online. Det här erbjudandet gäller för användare som har en tilldelad Office 365-licens, om motsvarande instans av Azure Active Directory inte har den fullständiga versionen av Azure MFA via en MFA-licens, ett paket eller en fristående konsumtions-baserad Provider.

**F: kan min organisation växla mellan fakturerings modeller per användare och per autentisering när som helst?**

Om din organisation köper MFA som en fristående tjänst med förbruknings-baserad fakturering väljer du en fakturerings modell när du skapar en MFA-Provider. Du kan inte ändra fakturerings modellen när en MFA-provider har skapats. 

Om MFA-providern *inte* är länkad till en Azure AD-klient, eller om du länkar den nya MFA-providern till en annan Azure AD-klient, överförs inte användar inställningar och konfigurations alternativ. Dessutom måste befintliga Azure MFA-servrar återaktiveras med hjälp av aktiveringsautentiseringsuppgifter som skapas via den nya MFA-providern. Återaktiveringen av MFA-servrarna för att koppla dem till den nya MFA-providern påverkar inte autentiseringen via telefonsamtal eller SMS, men mobilappsaviseringarna slutar fungera för alla användare tills de återaktiverar mobilappen.

Lär dig mer om MFA-leverantörer i [komma igång med en Azure Multi-Factor Auth-provider](concept-mfa-authprovider.md).

**F: kan min organisation växla mellan förbruknings faktureringar och prenumerationer (en licens baserad modell) när som helst?**

I vissa fall Ja.

Om din katalog har en Azure Multi-Factor Authentication *-Provider per användare* kan du lägga till MFA-licenser. Användare med licenser räknas inte in i förbruknings åtgången per användare. Användare utan licenser kan fortfarande aktive ras för MFA via MFA-providern. Om du köper och tilldelar licenser för alla användare som kon figurer ATS att använda Multi-Factor Authentication kan du ta bort Azure Multi-Factor Authentication-providern. Du kan alltid skapa en annan MFA-Provider per användare om du har fler användare än licenser i framtiden.

Om din katalog har en Azure Multi-Factor Authentication *-Provider per autentisering* , faktureras du alltid för varje autentisering, så länge MFA-providern är länkad till din prenumeration. Du kan tilldela MFA-licenser till användare, men du kommer fortfarande att faktureras för varje tvåstegsverifiering, oavsett om det kommer från någon med en MFA-licens som tilldelats eller inte.

**F: måste min organisation använda och synkronisera identiteter för att använda Azure Multi-Factor Authentication?**

Om din organisation använder en förbruknings-baserad fakturerings modell är Azure Active Directory valfritt, men krävs inte. Om MFA-providern inte är länkad till en Azure AD-klient kan du bara Distribuera Azure Multi-Factor Authentication-server lokalt.

Azure Active Directory krävs för licens modellen eftersom licenser läggs till i Azure AD-klienten när du köper och tilldelar dem till användare i katalogen.

## <a name="manage-and-support-user-accounts"></a>Hantera och ge support för användar konton

**F: Vad ska jag berätta för användarna om de inte får något svar på sin telefon?**

Låt dina användare försöka upp till fem gånger om 5 minuter för att få ett telefonsamtal eller SMS för autentisering. Microsoft använder flera leverantörer för att leverera samtal och SMS-meddelanden. Om detta inte fungerar kan du öppna ett support ärende med Microsoft för ytterligare fel sökning.

Om stegen ovan inte fungerar förhoppnings vis har alla användare konfigurerat mer än en verifieringsmetod. Be dem att försöka logga in igen, men välj en annan verifieringsmetod på inloggningssidan.

Du kan peka dina användare till [fel söknings guiden för slutanvändare](../user-help/multi-factor-authentication-end-user-troubleshoot.md).

**F: Vad ska jag göra om en av mina användare inte kan komma åt sitt konto?**

Du kan återställa användarens konto genom att låta dem gå igenom registrerings processen igen. Läs mer om hur du [hanterar användar-och enhets inställningar med Azure Multi-Factor Authentication i molnet](howto-mfa-userdevicesettings.md).

**F: Vad gör jag om en av mina användare förlorar en telefon som använder applösenord?**

Ta bort alla användares applösenord för att förhindra obehörig åtkomst. När användaren har en ersättnings enhet kan de återskapa lösen orden. Läs mer om hur du [hanterar användar-och enhets inställningar med Azure Multi-Factor Authentication i molnet](howto-mfa-userdevicesettings.md).

**F: Vad händer om en användare inte kan logga in på icke-webbläsarbaserade appar?**

Om din organisation fortfarande använder äldre klienter och du har [tillåtit användningen av applösenord](howto-mfa-mfasettings.md#app-passwords), kan användarna inte logga in på dessa äldre klienter med sitt användar namn och lösen ord. De måste i stället [Konfigurera applösenord](../user-help/multi-factor-authentication-end-user-app-passwords.md). Användarna måste ta bort sin inloggnings information, starta om appen och sedan logga in med sitt användar namn och *applösenord* i stället för det vanliga lösen ordet.

Om din organisation inte har äldre klienter bör du inte tillåta att användarna skapar applösenord.

> [!NOTE]
> Modern autentisering för Office 2013-klienter
>
> Applösenord krävs bara för appar som inte stöder modern autentisering. Office 2013-klienter stöder moderna autentiseringsprotokoll, men måste konfigureras. Nu är modern autentisering tillgänglig för alla kunder som kör uppdateringen från mars 2015 eller senare för Office 2013. Mer information finns i blogg inlägget [uppdaterat Office 365 modern Authentication](https://www.microsoft.com/microsoft-365/blog/2015/11/19/updated-office-365-modern-authentication-public-preview/).

**F: mina användare säger att de ibland inte tar emot textmeddelandet, eller att de svarar på dubbelriktade textmeddelanden, men verifierings tiden går ut.**

Leverans av SMS-meddelanden och mottagning av svar i dubbelriktat SMS är inte garanterat eftersom det finns okontrollerade faktorer som kan påverka tjänstens tillförlitlighet. Dessa faktorer inkluderar destinations land/-region, mobil telefon operatören och signal styrkan.

Om användarna ofta har problem med att ta emot textmeddelanden på ett tillförlitligt sätt kan du be dem att använda mobilappen eller Telefonsamtals metoden i stället. Mobilappen kan ta emot meddelanden både via mobil nät och Wi-Fi-anslutningar. Dessutom kan mobilappen generera verifierings koder även om enheten inte har någon signal alls. Microsoft Authenticator-appen är tillgänglig för [Android](https://go.microsoft.com/fwlink/?Linkid=825072), [iOS](https://go.microsoft.com/fwlink/?Linkid=825073)och [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).

Om du måste använda SMS rekommenderar vi att du använder enkelriktat SMS i stället för dubbelriktat SMS när det är möjligt. Enkelriktat SMS är mer tillförlitligt och hindrar användare från att lämna ut globala SMS-avgifter från svar till ett SMS som har skickats från ett annat land/en annan region.

**F: kan jag ändra hur lång tid som mina användare måste ange verifierings koden från ett SMS innan systemet nått tids gränsen?**

I vissa fall Ja. 

För enkelriktat SMS med Azure MFA server v 7.0 eller högre kan du konfigurera timeout-inställningen genom att ange en register nyckel. När MFA Cloud Service skickar textmeddelandet returneras verifierings koden (eller eng ång slö sen ordet) till MFA-servern. MFA-servern lagrar koden i minnet i 300 sekunder som standard. Om användaren inte anger koden innan 300 sekunder har passerat, nekas autentiseringen. Använd de här stegen för att ändra standardinställningen för timeout-inställningen:

1. Gå till HKLM\Software\Wow6432Node\Positive Networks\PhoneFactor.
2. Skapa en DWORD-registernyckeln med namnet **pfsvc_pendingSmsTimeoutSeconds** och ange tiden i sekunder som du vill att Azure MFA-servern ska lagra eng ång slö sen ord.

>[!TIP] 
>Om du har flera MFA-servrar vet bara den som bearbetade den ursprungliga autentiseringsbegäran den verifierings kod som skickades till användaren. När användaren anger koden måste autentiseringsbegäran som verifieras skickas till samma server. Om kod verifieringen skickas till en annan server nekas autentiseringen. 

För dubbelriktat SMS med Azure MFA Server kan du konfigurera timeout-inställningen i MFA-Hanteringsportal. Om användarna inte svarar på SMS inom den angivna tids gränsen, nekas deras autentisering. 

För enkelriktat SMS med Azure MFA i molnet (inklusive AD FS adapter eller nätverks princip Server tillägget) kan du inte konfigurera tids inställningen för timeout. Azure AD lagrar verifierings koden i 180 sekunder. 

**F: kan jag använda maskinvaru-token med Azure Multi-Factor Authentication-server?**

Om du använder Azure Multi-Factor Authentication-server kan du importera tredjeparts-tidsbaserad (ed), eng ång slö sen ord (TOTP mobilapp)-token och sedan använda dem för tvåstegsverifiering.

Du kan använda ActiveIdentity-token som är OATH TOTP mobilapp-tokens om du använder den hemliga nyckeln i en CSV-fil och importera till Azure Multi-Factor Authentication-server. Du kan använda OATH-token med Active Directory Federation Services (AD FS) (ADFS), formulärbaserad autentisering i IIS (Internet Information Server) och Remote Authentication Dial-In User Service (RADIUS) så länge klient systemet kan acceptera användarindata.

Du kan importera TOTP mobilapp-token från tredje part med följande format:  

- Portabel symmetrisk nyckel behållare (PSKC)  
- CSV om filen innehåller ett serie nummer, en hemlig nyckel i Base 32-format och ett tidsintervall  

**F: kan jag använda Azure Multi-Factor Authentication-server för att skydda Terminal Services?**

Ja, men om du använder Windows Server 2012 R2 eller senare kan du bara skydda Terminal Services med hjälp av Fjärrskrivbordsgateway (RD Gateway).

Säkerhets ändringar i Windows Server 2012 R2 har ändrat hur Azure Multi-Factor Authentication-server ansluter till säkerhets paketet Local Security Authority (LSA) i Windows Server 2012 och tidigare versioner. För versioner av Terminal Services i Windows Server 2012 eller tidigare kan du [skydda ett program med Windows-autentisering](howto-mfaserver-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Om du använder Windows Server 2012 R2 behöver du RD Gateway.

**F: jag konfigurerade anroparens ID i MFA Server, men mina användare får fortfarande Multi-Factor Authentication anrop från en anonym anropare.**

När Multi-Factor Authentication anrop görs via det offentliga telefonnätet, kan de ibland dirigeras via en operatör som inte har stöd för uppringarens ID. Därför garanteras inte anropar-ID, trots att Multi-Factor Authentication systemet alltid skickar det.

**F: varför uppmanas mina användare att registrera sin säkerhets information?**
Det finns flera orsaker till att användarna uppmanas att registrera sina säkerhets uppgifter:

- Användaren har Aktiver ATS för MFA av deras administratör i Azure AD, men har inte registrerat någon säkerhets information för sitt konto ännu.
- Användaren har Aktiver ATS för lösen ords återställning via självbetjäning i Azure AD. Säkerhets informationen hjälper dem att återställa sina lösen ord i framtiden om de glömmer bort det.
- Användaren kom åt ett program som har en princip för villkorlig åtkomst för att kräva MFA och som inte tidigare har registrerats för MFA.
- Användaren registrerar en enhet med Azure AD (inklusive Azure AD Join) och din organisation kräver MFA för enhets registrering, men användaren har inte redan registrerats för MFA.
- Användaren skapar Windows Hello för företag i Windows 10 (vilket kräver MFA) och inte tidigare har registrerats för MFA.
- Organisationen har skapat och aktiverat en MFA-registrerings princip som har tillämpats på användaren.
- Användaren har redan registrerats för MFA, men valde en verifierings metod som en administratör har inaktiverat. Användaren måste därför gå igenom MFA-registreringen igen för att välja en ny standard verifierings metod.

## <a name="errors"></a>Fel

**F: Vad ska användarna göra om de ser att det inte finns något fel meddelande om att autentiseringsbegäran inte gäller för ett aktiverat konto när du använder meddelanden i mobilappen?**

Be dem att följa den här proceduren för att ta bort sitt konto från mobilappen och Lägg sedan till det igen:

1. Gå till [din Azure Portal profil](https://account.activedirectory.windowsazure.com/profile/) och logga in med ditt organisations konto.
2. Välj **ytterligare säkerhets verifiering**.
3. Ta bort det befintliga kontot från mobilappen.
4. Klicka på **Konfigurera**och följ sedan anvisningarna för att konfigurera om mobilappen.

**F: Vad ska användarna göra om de ser ett 0x800434D4L fel meddelande när de loggar in till ett program som inte är ett webb läsar program?**

0x800434D4L-felet uppstår när du försöker logga in till ett program som inte är en webbläsare, som är installerat på en lokal dator, som inte fungerar med konton som kräver tvåstegsverifiering.

En lösning för det här felet är att ha separata användar konton för administratörs-och icke-admin-åtgärder. Senare kan du länka post lådor mellan ditt administratörs konto och kontot för icke-administratörer så att du kan logga in i Outlook med kontot som inte är administratör. Om du vill ha mer information om den här lösningen kan du lära dig att [ge en administratör möjlighet att öppna och visa innehållet i en användares post låda](https://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Nästa steg

Om din fråga inte besvaras här, lämnar du den i kommentarerna längst ned på sidan. Eller så finns det ytterligare alternativ för att få hjälp:

* Sök i [Microsoft Support Knowledge Base](https://www.microsoft.com/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport) efter lösningar på vanliga tekniska problem.
* Sök efter och bläddra efter tekniska frågor och svar från communityn eller Ställ din egen fråga i [Azure Active Directory forumen](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).
* Om du är en äldre PhoneFactor-kund och har frågor eller behöver hjälp med att återställa ett lösen ord använder du länken [lösen ords återställning](mailto:phonefactorsupport@microsoft.com) för att öppna ett support ärende.
* Kontakta en support tekniker via [Azure Multi-Factor Authentication-Server-Support (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947). När du kontaktar oss är det bra om du kan inkludera så mycket information om problemet som möjligt. Information som du kan tillhandahålla inkluderar sidan där du såg felet, den speciella felkoden, det ID som identifierades och ID: t för den användare som såg felet.
