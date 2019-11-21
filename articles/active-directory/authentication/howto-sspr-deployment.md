---
title: Distributions plan för återställning av lösen ord för självbetjäning – Azure Active Directory
description: Strategi för lyckad implementering av lösen ords återställning via självbetjäning i Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb79c6dd0358d0360c320cd67a46779b183ef21e
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74208503"
---
# <a name="deploy-azure-ad-self-service-password-reset"></a>Distribuera självbetjäning av lösenordsåterställning för Azure AD

> [!NOTE]
> Den här guiden förklarar självbetjäning för återställning av lösen ord och hur du distribuerar den. Om du letar efter verktyget för återställning av lösen ord för självbetjäning för att komma tillbaka till ditt konto går du till [https://aka.ms/sspr](https://aka.ms/sspr). 

Lösen ords återställning via självbetjäning (SSPR) är en Azure Active Directory funktion som gör att anställda kan återställa sina lösen ord utan att behöva kontakta IT-personalen. Medarbetare måste registreras för eller registreras för lösen ords återställning via självbetjäning innan tjänsten kan användas. Under registreringen väljer den anställda en eller flera autentiseringsmetoder som har Aktiver ATS av organisationen.

SSPR gör det möjligt för de anställda att snabbt få tag i blockering och fortsätta att arbeta oavsett var de befinner sig eller tiden på dagen. Genom att tillåta användare att avblockera sig själva kan din organisation minska den icke produktiva tiden och höga support kostnader för de flesta vanliga problem som rör lösen ord.

Hjälp användarna att komma igång snabbt genom att distribuera SSPR tillsammans med ett annat program eller en tjänst i din organisation. Den här åtgärden genererar en stor volym av inloggningar och kommer att driva registrering.

Innan du distribuerar SSPR kanske organisationer vill fastställa hur många relaterade svar på lösen ords återställning sker över tid och den genomsnittliga kostnaden för varje anrop. De kan använda den här data publicerings distributionen för att visa värdet som SSPR ansluter till din organisation.  

## <a name="how-sspr-works"></a>Så här fungerar SSPR

1. När en användare försöker återställa ett lösen ord måste han eller hon verifiera sin identitet genom att verifiera deras tidigare registrerade autentiseringsmetod eller metoder.
1. Användaren anger sedan ett nytt lösen ord.
   1. För endast molnbaserade användare lagras det nya lösen ordet i Azure Active Directory. Mer information finns i artikeln [hur SSPR fungerar](concept-sspr-howitworks.md#how-does-the-password-reset-portal-work).
   1. För Hybrid användare skrivs lösen ordet tillbaka till den lokala Active Directory via tjänsten Azure AD Connect. Mer information finns i artikeln [Vad är tillbakaskrivning av lösen ord](concept-sspr-writeback.md#how-password-writeback-works).

## <a name="licensing-considerations"></a>Licens överväganden

Azure Active Directory är licensierad per användare, vilket innebär att varje användare måste ha en lämplig licens för de funktioner som de använder.

Mer information om licensiering finns på [sidan Azure Active Directory prissättning](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="enable-combined-registration-for-sspr-and-mfa"></a>Aktivera kombinerad registrering för SSPR och MFA

Microsoft rekommenderar att organisationer aktiverar den kombinerade registrerings upplevelsen för SSPR och Multi-Factor Authentication. När du aktiverar den här kombinerade registrerings upplevelsen behöver användarna bara välja sin registrerings information en gång för att aktivera båda funktionerna.

![Kombinerad säkerhets informations registrering](./media/howto-sspr-deployment/combined-security-info.png)

Den kombinerade registrerings upplevelsen kräver inte att organisationer aktiverar både SSPR och Azure Multi-Factor Authentication att använda. Den kombinerade registrerings upplevelsen ger organisationer en bättre användar upplevelse jämfört med de traditionella enskilda komponenterna. Mer information om kombinerad registrering och hur du aktiverar finns i artikeln [kombinerad säkerhets informations registrering (för hands version)](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-configuration"></a>Planera konfigurationen

Följande inställningar krävs för att aktivera SSPR tillsammans med rekommenderade värden.

| Område | Inställning | Värde |
| --- | --- | --- |
| **Egenskaper för SSPR** | Återställning av lösen ord för självbetjäning har Aktiver ATS | **Vald** grupp för pilot/ **alla** för produktion |
| **Autentiseringsmetoder** | Autentiseringsmetoder som krävs för registrering | Alltid 1 mer än vad som krävs för återställning |
|   | Autentiseringsmetoder som krävs för att återställa | En eller två |
| **Registrering** | Kräv att användare registrerar sig vid inloggning | Ja |
|   | Antal dagar innan användare uppmanas att bekräfta sin autentiseringsinformation | 90 – 180 dagar |
| **Meddelanden** | Meddela användare om lösenordsåterställning | Ja |
|   | Meddela alla administratörer när andra administratörer återställer sina lösenord | Ja |
| **Eventuella** | Anpassa supportavdelningen-länk | Ja |
|   | E-post eller URL för anpassad helpdesk | Support webbplats eller e-postadress |
| **Lokal integration** | Skriv tillbaka lösen ord till lokal AD | Ja |
|   | Tillåt att användare låser upp kontot utan att återställa lösen ordet | Ja |

### <a name="sspr-properties-recommendations"></a>Rekommendationer för SSPR-egenskaper

När du aktiverar lösen ords återställning via självbetjäning väljer du en säkerhets grupp som ska användas under piloten.

När du planerar att starta tjänsten mer brett rekommenderar vi att du använder alternativet all för att genomdriva SSPR för alla i organisationen. Om du inte kan ställa in på alla väljer du den aktuella Azure AD-säkerhetsgruppen eller AD-gruppen som synkroniseras med Azure AD.

### <a name="authentication-methods"></a>Autentiseringsmetoder

Ange de autentiseringsmetoder som krävs för att registrera minst ett av de tal som krävs för att återställa. Om du tillåter flera får användarna flexibilitet när de behöver återställa.

Ange det **antal metoder som krävs för att återställa** till en nivå som är lämplig för din organisation. En kräver minst friktion, medan två kan öka din säkerhets position.

Se [Vad är autentiseringsmetoder](concept-authentication-methods.md) för detaljerad information om vilka autentiseringsmetoder som är tillgängliga för SSPR, fördefinierade säkerhets frågor och hur du skapar anpassade säkerhets frågor.

### <a name="registration-settings"></a>Registrerings inställningar

Ange **Kräv att användare registrerar sig när de loggar in** på **Ja**. Den här inställningen innebär att användarna tvingas att registrera sig när de loggar in, vilket säkerställer att alla användare är skyddade.

Ange **antal dagar innan användare uppmanas att bekräfta sin autentiseringsinformation** till mellan **90** och **180** dagar, om inte din organisation har ett affärs behov för kortare tids ramar.

### <a name="notifications-settings"></a>Aviserings inställningar

Konfigurera både **meddela användare om lösen ords** återställning och **meddela alla administratörer när andra administratörer återställer sina lösen ord** till **Ja**. Om du väljer **Ja** på båda ökar säkerheten genom att se till att användarna är medvetna om deras lösen ord har återställts och att alla administratörer är medvetna om en administratör ändrar ett lösen ord. Om användare eller administratörer får sådant meddelande och de inte har initierat ändringen, kan de omedelbart rapportera en potentiell säkerhets överträdelse.

### <a name="customization"></a>Anpassning

Det är viktigt att anpassa e-postadressen **eller URL: en för supportavdelningen** för att säkerställa att användare som upplever problem snabbt kan få hjälp. Ange det här alternativet till en vanlig e-postadress för supportavdelningen eller en webb sida som användarna är bekanta med.

### <a name="on-premises-integration"></a>Lokal integration

Om du har en hybrid miljö kontrollerar du att **Skriv tillbaka lösen ord till lokal AD** har angetts till **Ja**. Ange också Tillåt att användare låser upp kontot utan att återställa lösen ordet till Ja, eftersom det ger dem större flexibilitet.

### <a name="changingresetting-passwords-of-administrators"></a>Ändra/återställa lösen ord för administratörer

Administratörs konton är särskilda konton med utökade behörigheter. För att skydda dem gäller följande begränsningar för att ändra lösen ord för administratörer:

- Lokala företags administratörer eller domän administratörer kan inte återställa sina lösen ord via SSPR. De kan bara ändra sina lösen ord i sin lokala miljö. Därför rekommenderar vi inte att du synkroniserar lokal AD admin-konton till Azure AD.
- En administratör kan inte använda hemliga frågor & svar som en metod för att återställa lösen ordet.

### <a name="environments-with-multiple-identity-management-systems"></a>Miljöer med flera identitets hanterings system

Om det finns flera identitets hanterings system i en miljö, till exempel lokala identitets hanterare som Oracle AM, SiteMinder eller andra system, kan lösen ord som skrivs till Active Directory behöva synkroniseras till andra system med hjälp av ett verktyg som Tjänsten för meddelanden om lösen ords ändring (PCNS) med Microsoft Identity Manager (MIM). Om du vill ha mer information om det här komplexa scenariot kan du läsa artikeln [distribuera MIM-tjänsten för meddelanden om lösen ords ändring på en](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller)domänkontrollant.

## <a name="plan-deployment-and-support-for-sspr"></a>Planera distribution och stöd för SSPR

### <a name="engage-the-right-stakeholders"></a>Engagera rätt intressenter

När teknik projekt inte fungerar, gör de vanligt vis på grund av felaktiga förväntningar på påverkan, resultat och ansvars områden. För att undvika dessa fall GRO par bör du se till att du engagerar rätt intressenter och att från intressenter-rollerna i projektet är väl förstå genom att dokumentera intressenterna och deras ingångs uppgifter och ansvars uppgifter.

### <a name="communications-plan"></a>Kommunikations plan

Kommunikationen är nödvändig för att en ny tjänst ska lyckas. Kommunicera proaktivt med dina användare om hur du använder tjänsten och vad de kan göra för att få hjälp om något inte fungerar som förväntat. Granska distributions [materialet för lösen ords återställning via självbetjäning på Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56768) för att få tips om hur du planerar din slut användar kommunikations strategi.

### <a name="testing-plan"></a>Test plan

För att säkerställa att distributionen fungerar som förväntat, bör du planera en uppsättning test ärenden som du ska använda för att validera implementeringen. Följande tabell innehåller några användbara test scenarier som du kan använda för att dokumentera dina organisationers förväntade resultat baserat på dina principer.

| Företagscase | Förväntat resultat |
| --- | --- |
| SSPR-portalen är tillgänglig i företags nätverket | Bestäms av din organisation |
| SSPR-portalen kan nås utanför företags nätverket | Bestäms av din organisation |
| Återställ användar lösen ord från webbläsare när användaren inte har Aktiver ATS för lösen ords återställning | Användaren kan inte komma åt flödet för lösen ords återställning |
| Återställ användar lösen ord från webbläsare när användaren inte har registrerats för lösen ords återställning | Användaren kan inte komma åt flödet för lösen ords återställning |
| Användaren loggar in när registreringen av lösen ords återställning tillämpas | Användaren uppmanas att registrera säkerhets information |
| Användaren loggar in när registreringen av lösen ords återställning har slutförts | Användaren uppmanas inte att registrera säkerhets information |
| SSPR-portalen är tillgänglig när användaren inte har någon licens | Är tillgänglig |
| Återställ användar lösen ord från Windows 10 Azure AD-ansluten eller hybrid Azure AD-ansluten enhets Lås skärm när användaren har registrerat sig | Användare kan återställa lösen ord |
| SSPR-registrering och användnings data är tillgängliga för administratörer i nära real tid | Är tillgänglig via gransknings loggar |

### <a name="support-plan"></a>Supportplan

Medan SSPR vanligt vis inte skapar användar problem är det viktigt att ha support personal för beredd för att hantera problem som kan uppstå.

Även om en administratör kan ändra eller återställa lösen ordet för slutanvändare via Azure AD Portal, är det bättre att hjälpa till att lösa problemet via en självbetjänings support process.

I avsnittet operativa rikt linjer i det här dokumentet skapar du en lista över support ärenden och deras sannolika orsaker och skapar en guide för lösning.

### <a name="auditing-and-reporting"></a>Granskning och rapportering

Efter distributionen vill många organisationer veta hur eller om självbetjäning för återställning av lösen ord (SSPR) faktiskt används. Med rapporterings funktionen som Azure Active Directory (Azure AD) kan du besvara frågor genom att använda färdiga rapporter.

Gransknings loggar för registrering och återställning av lösen ord är tillgängliga i 30 dagar. Om säkerhets granskning i ett företag kräver längre kvarhållning måste därför loggarna exporteras och förbrukas i ett SIEM-verktyg som till exempel [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk eller ArcSight.

I en tabell, precis som den nedan, dokumenterar du schemat för säkerhets kopiering, systemet och ansvariga parter. Du kanske inte behöver separata säkerhets kopior för granskning och rapportering, men du bör ha en separat säkerhets kopia som du kan återställa från ett problem.

|   | Hämtnings frekvens | Mål system | Ansvarig part |
| --- | --- | --- | --- |
| Granska säkerhets kopiering |   |   |   |
| Rapportera säkerhets kopia |   |   |   |
| Säkerhets kopiering av haveri beredskap |   |   |   |

## <a name="implementation"></a>Implementering

Implementering sker i tre steg:

- Konfigurera användare och licenser
- Konfigurera Azure AD-SSPR för registrering och självbetjäning
- Konfigurera Azure AD Connect för tillbakaskrivning av lösen ord

### <a name="communicate-the-change"></a>Kommunicera ändringen

Börja implementeringen av kommunikations planen som du utvecklade i planerings fasen.

### <a name="ensure-groups-are-created-and-populated"></a>Se till att grupper skapas och fylls i

Referera till avsnittet Planera autentiseringsmetoder för lösen ord och se till att grupperna för pilot-eller produktions implementeringen är tillgängliga och att alla lämpliga användare läggs till i grupperna.

### <a name="apply-licenses"></a>Tillämpa licenser

De grupper som du ska implementera måste ha tilldelats Azure AD Premium-licensen. Du kan tilldela licenser direkt till gruppen, eller så kan du använda befintliga licens principer, till exempel via PowerShell eller gruppbaserad licensiering.

Information om hur du tilldelar licenser till grupper med användare finns i artikeln [tilldela licenser till användare efter grupp medlemskap i Azure Active Directory](../users-groups-roles/licensing-groups-assign.md).

### <a name="configure-sspr"></a>Konfigurera SSPR

#### <a name="enable-groups-for-sspr"></a>Aktivera grupper för SSPR

1. Få åtkomst till Azure Portal med ett administratörs konto.
1. Välj alla tjänster och skriv Azure Active Directory i rutan Filter och välj sedan Azure Active Directory.
1. På bladet Active Directory väljer du lösen ords återställning.
1. I fönstret Egenskaper väljer du vald. Om du vill att alla användare ska vara aktiverade väljer du alla.
1. Skriv namnet på den första gruppen i bladet standard princip för lösen ords återställning, markera den och klicka sedan på Välj längst ned på skärmen och välj Spara längst upp på skärmen.
1. Upprepa den här processen för varje grupp.

#### <a name="configure-the-authentication-methods"></a>Konfigurera autentiseringsmetoder

Referera till planeringen från avsnittet Planera lösenordsautentisering för lösen ord i det här dokumentet.

1. Välj registrering under Kräv att användare registrerar när du loggar in, Välj Ja och ange sedan antalet dagar före förfallo datum och välj sedan Spara.
1. Välj meddelande och konfigurera per plan och välj sedan Spara.
1. Välj anpassning och konfigurera per plan och välj sedan Spara.
1. Välj lokal integrering och konfigurera per plan och välj sedan Spara.

### <a name="enable-sspr-in-windows"></a>Aktivera SSPR i Windows

Windows 10-enheter som kör version 1803 eller högre som antingen är anslutna till Azure AD eller en hybrid Azure AD-anslutning kan återställa sina lösen ord på Windows inloggnings skärm. Information och steg för att konfigurera den här funktionen finns i artikeln [återställning av lösen ord för Azure AD från inloggnings skärmen](tutorial-sspr-windows.md)

### <a name="configure-password-writeback"></a>Konfigurera tillbakaskrivning av lösen ord

Steg för att konfigurera tillbakaskrivning av lösen ord för din organisation finns i artikeln [instruktion: Konfigurera tillbakaskrivning av lösen ord](howto-sspr-writeback.md).

## <a name="manage-sspr"></a>Hantera SSPR

Nödvändiga roller för att hantera funktioner som är associerade med lösen ords återställning via självbetjäning.

| Affärs roll/persona | Azure AD-roll (om det behövs) |
| :---: | :---: |
| Nivå 1-helpdesk | Lösen ords administratör |
| Nivå 2-supportavdelningen | Användar administratör |
| SSPR-administratör | Global administratör |

### <a name="support-scenarios"></a>Support scenarier

Om du vill göra det möjligt för support teamet att lyckas kan du skapa vanliga frågor och svar baserat på frågor som du får från användarna. Följande tabell innehåller vanliga support scenarier.

| Scenarier | Beskrivning |
| --- | --- |
| Användaren har inga registrerade autentiseringsmetoder tillgängliga | En användare försöker återställa sitt lösen ord men har inte någon av de autentiseringsmetoder som de registrerade är tillgängliga (exempel: de lämnade sin mobil telefon hemma och har inte åtkomst till e-post) |
| Användaren får ingen text eller ett samtal på sitt kontor eller mobil telefon | En användare försöker verifiera sin identitet via text eller anrop, men får inte något text/anrop. |
| Användaren har inte åtkomst till portalen för återställning av lösen ord | En användare vill återställa sitt lösen ord men har inte Aktiver ATS för lösen ords återställning och kan därför inte komma åt sidan för att uppdatera lösen ord. |
| Användaren kan inte ange ett nytt lösen ord | En användare har slutfört verifieringen under flödet för lösen ords återställning men kan inte ange ett nytt lösen ord. |
| Användaren ser ingen länk för återställning av lösen ord på en Windows 10-enhet | En användare försöker återställa lösen ordet från Windows 10 Lås skärmen, men enheten är inte ansluten till Azure AD eller också är inte Intune-hälsoenheten aktive rad |

Du kanske också vill inkludera information som följande för ytterligare fel sökning.

- Vilka grupper som är aktiverade för SSPR.
- Vilka autentiseringsmetoder som har kon figurer ATS.
- Åtkomst principerna för på eller i företags nätverket.
- Fel söknings steg för vanliga scenarier.

Du kan också läsa vår onlinedokumentation om hur du felsöker lösen ords återställning via självbetjäning för att förstå allmänna fel söknings steg för de vanligaste SSPR-scenarierna.

## <a name="next-steps"></a>Nästa steg

- [Överväg att implementera lösen ords skydd i Azure AD](concept-password-ban-bad.md)

- [Överväg att implementera Azure AD Smart utelåsning](howto-password-smart-lockout.md)
