---
title: Lösenordsåterställning via självbetjäning distributionsplan - Azure Active Directory
description: Återställning av strategin för lyckade implementeringen av lösenord för självbetjäning i Azure AD
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
ms.openlocfilehash: 8b566bfc3f6c49f6cb9fe31f166356f6ae351e38
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440953"
---
# <a name="deploy-azure-ad-self-service-password-reset"></a>Distribuera Azure AD lösenordsåterställning via självbetjäning

Lösenordsåterställning via självbetjäning (SSPR) är en Azure Active Directory-funktion som gör det möjligt att återställa sina lösenord utan att behöva kontakta IT-personal. Anställda måste först registreras för eller vara registrerad för självbetjäning för återställning av lösenord innan du använder tjänsten. Under registreringen väljer medarbetaren en eller flera autentiseringsmetoder som aktiverats av organisationen.

SSPR gör det möjligt att snabbt få blockeringen och fortsätta arbeta oavsett var de är eller tid på dagen. Genom att tillåta användare att avblockera själva, kan din organisation minska icke-produktiva tiden och kostnaderna för hög support för de vanligaste lösenord-relaterade problem.

Hjälpa användare att registrera dig snabbt genom att distribuera SSPR tillsammans med ett annat program eller tjänst i din organisation. Den här åtgärden genererar ett stort antal inloggningar och kommer att öka registrering.

Innan du distribuerar SSPR vill organisationer ta reda på hur många lösenordsåterställning relaterade samtal till supportavdelningen utförs över tid och den genomsnittliga kostnaden för varje anrop. De kan använda den här data efter distribution så visas värdet SSPR att din organisation.  

## <a name="how-sspr-works"></a>Hur SSPR fungerar

1. När en användare försöker att återställa ett lösenord, måste de kontrollera sina tidigare registrerad autentiseringsmetod eller metoder för att bekräfta sin identitet.
1. Användaren anger sedan ett nytt lösenord.
   1. Det nya lösenordet lagras i Azure Active Directory för molnexklusiva användare. Mer information finns i artikeln [hur SSPR fungerar](concept-sspr-howitworks.md#how-does-the-password-reset-portal-work).
   1. Lösenordet skrivs tillbaka till den lokala Active Directory via Azure AD Connect-tjänsten för hybridanvändare. Mer information finns i artikeln [vad är tillbakaskrivning av lösenord](concept-sspr-writeback.md#how-password-writeback-works).

## <a name="licensing-considerations"></a>Licensiering överväganden

Azure Active Directory är licens per användare enligt varje användare måste ha en lämplig licens för de funktioner som de använder.

- Självbetjäning för återställning av lösenord för molnexklusiva användare är tillgänglig med Azure AD Basic eller högre.
- Lösenordsåterställning via självbetjäning med lokal tillbakaskrivning för hybridmiljöer kräver Azure AD Premium P1 eller senare.

Mer information om licensiering finns på den [sidan med priser för Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)

## <a name="enable-combined-registration-for-sspr-and-mfa"></a>Aktivera kombinerade registrering för SSPR och MFA

Microsoft rekommenderar att organisationer aktiverar kombinerade registrerings-upplevelsen för SSPR och multifaktorautentisering. När du aktiverar den här kombinerade registrerings-upplevelsen kan behöver användare bara välja registreringsinformationen en gång för att aktivera båda funktioner.

![Kombinerade security information registrering](./media/howto-sspr-deployment/combined-security-info.png)

Kombinerade registrerings-upplevelsen kräver inte organisationer att aktivera både SSPR och Azure Multi-Factor Authentication att använda. Kombinerade registrerings-guiden innehåller organisationer en bättre användarupplevelse jämfört med traditionella enskilda komponenter. Mer information om kombinerade registrering och hur du aktiverar, finns i artikeln [kombineras security information registrering (förhandsversion)](concept-registration-mfa-sspr-combined.md)

## <a name="plan-the-configuration"></a>Planera konfigurationen

Följande inställningar krävs för att aktivera SSPR tillsammans med rekommenderade värden.

| Område | Inställning | Värde |
| --- | --- | --- |
| **SSPR-egenskaper** | Lösenordsåterställning via självbetjäning har aktiverats | **Valt** för pilot / **alla** för produktion |
| **Autentiseringsmetoder** | Autentiseringsmetoder som krävs för registrering | Alltid 1 mer än vad som krävs för återställning |
|   | Autentiseringsmetoder som krävs för återställning | En eller två |
| **Registrering** | Kräv att användare registrerar sig vid inloggning | Ja |
|   | Antal dagar innan användare uppmanas att bekräfta sin autentiseringsinformation igen | 90 – 180 dagar |
| **Meddelanden** | Meddela användare om lösenordsåterställning | Ja |
|   | Meddela alla administratörer när andra administratörer återställer sina lösenord | Ja |
| **Anpassning** | Anpassa helpdesk-länk | Ja |
|   | Anpassade supportavdelningen e-postadress eller URL | Webbplats eller e-postadress för support |
| **Lokal integration** | Skriv tillbaka lösenord till en lokal AD | Ja |
|   | Låt användare låsa upp kontot utan att återställa lösenord | Ja |

### <a name="sspr-properties-recommendations"></a>Rekommendationer för SSPR-egenskaper

När du aktiverar lösenordsåterställning via självbetjäning, väljer du en säkerhetsgrupp som ska användas under pilotprogrammet.

När du planerar att starta tjänsten bredare, bör du använda alternativet All för att upprätthålla SSPR för alla i organisationen. Om du inte ange att alla, väljer du lämplig Azure AD-säkerhetsgrupp eller AD-grupp som är synkroniserad med Azure AD.

### <a name="authentication-methods"></a>Autentiseringsmetoder

Ange autentiseringsmetoder som krävs för registrering hos minst en mer än det antal som krävs för återställning. Så att flera ger användarna flexibilitet när de behöver att återställa.

Ange **antal metoder som krävs för att återställa** till en nivå som är lämpliga för din organisation. En kräver minst friktionen, men två kan öka din säkerhetsposition.

Se [vad är autentiseringsmetoder](concept-authentication-methods.md) för detaljerad information om vilka autentisering metoder är tillgängliga för SSPR, fördefinierade säkerhetsfrågor och hur du skapar anpassade säkerhetsfrågor.

### <a name="registration-settings"></a>Registreringsinställningar

Ange **kräver att användare registrerar vid inloggning** till **Ja**. Den här inställningen innebär att användarna tvingas att registrerar vid inloggning, se till att alla användare är skyddade.

Ange **antal dagar innan användare uppmanas att bekräfta sin autentiseringsinformation igen** till mellan **90** och **180** dagar, såvida inte organisationen har ett företag behöver för ett kortare tidsintervall.

### <a name="notifications-settings"></a>Meddelandeinställningar

Konfigurera både den **meddela användare om lösenordsåterställning** och **meddela alla administratörer när andra administratörer återställer sina lösenord** till **Ja**. Att välja **Ja** på både ökar säkerheten genom att se till att användarna är medvetna om när deras lösenord har återställts och att alla administratörer är medvetna om när en administratör ändrar ett lösenord. Om användare eller administratörer får sådant meddelande och de har inte initierats ändringen, kan de omedelbart rapportera en potentiell säkerhetsrisk.

### <a name="customization"></a>Anpassning

Det är viktigt att anpassa den **supportavdelningen e-postadress eller URL: en** att se till att användare som får problem kan snabbt få hjälp. Ange det här alternativet om du till en gemensam supportavdelningen e-postadress eller en webbsida som dina användare är bekanta med.

### <a name="on-premises-integration"></a>Lokal integration

Om du har en hybridmiljö kan du se till att **Skriv tillbaka lösenord till en lokal AD** är inställd på **Ja**. Också ange Tillåt användare att låsa upp kontot utan att återställa lösenordet på Ja, eftersom det ger mer flexibilitet.

### <a name="changingresetting-passwords-of-administrators"></a>Ändra/Resetting lösenorden för administratörer

Administratörskonton är särskilt med förhöjd behörighet. För att skydda dem, gäller följande begränsningar för ändring av lösenord för administratörer:

- Lokala Företagsadministratörer eller Domänadministratörer kan inte återställa sina lösenord via SSPR. De kan endast ändra sina lösenord i sina lokala miljöer. Vi rekommenderar därför inte synkroniserar lokala AD-administratörskonton till Azure AD.
- En administratör kan inte använda hemliga frågor och svar som en metod för att återställa lösenord.

### <a name="environments-with-multiple-identity-management-systems"></a>I miljöer med flera hanteringssystem för identitet

Om det finns flera identity management system i en miljö till exempel en lokal identitet chefer t.ex. Oracle AM, kan SiteMinder, eller andra system och lösenord som skrivs till Active Directory behöva synkroniseras till andra system med ett verktyg som Lösenordet ändra meddelandetjänsten (PCNS) med Microsoft Identity Manager (MIM). Information om den här mer komplext scenario finns i artikeln [distribuera MIM lösenordet ändra Notification-tjänsten på en domänkontrollant](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller).

## <a name="plan-deployment-and-support-for-sspr"></a>Planera distributionen och stöd för SSPR

### <a name="engage-the-right-stakeholders"></a>Engagera rätt intressenter

Teknik projekt misslyckas, de vanligtvis inte på grund av felaktigt förväntningar på inverkan, resultat och ansvarsområden. Se till att du engagerande rätt intressenter och att intressenter roller i projektet är väl förstått genom att dokumentera intressenterna och deras projekt indata och ansvar för att undvika dessa fallgropar.

### <a name="communications-plan"></a>Kommunikationsplan

Kommunikation är nyckeln till framgång för alla nya tjänster. Proaktivt kommunicera med dina användare hur du använder tjänsten och vad de kan göra för att få hjälp om något inte fungerar som förväntat. Granska den [lösenordsåterställning via självbetjäning distributionen material på Microsoft download center](https://www.microsoft.com/download/details.aspx?id=56768) tips om hur du planerar din strategi för kommunikation av slutanvändaren.

### <a name="testing-plan"></a>Testa plan

För att säkerställa att distributionen fungerar som förväntat, bör du planera en uppsättning testfall som du använder för att verifiera implementeringen. Följande tabell innehåller vissa användbara test-scenarier som du kan använda för att dokumentet organisationens förväntat resultat baserat på dina principer.

| Företagscase | Förväntat resultat |
| --- | --- |
| SSPR-portalen är tillgängliga inifrån företagsnätverket | Bestäms av din organisation |
| SSPR-portalen är åtkomliga från utanför företagets nätverk | Bestäms av din organisation |
| Återställ användarlösenord från webbläsaren när användaren inte har aktiverats för återställning av lösenord | Användaren har inte möjlighet att få åtkomst till lösenordsåterställning flödet |
| Återställ användarlösenord från webbläsaren när användaren inte har registrerats för återställning av lösenord | Användaren har inte möjlighet att få åtkomst till lösenordsåterställning flödet |
| Användaren loggar in när registrering för lösenordsåterställning tillämpas | Användaren uppmanas att registrera säkerhetsinformation |
| Användaren loggar in när registrering för lösenordsåterställning har slutförts | Användaren uppmanas inte att registrera säkerhetsinformation |
| SSPR-portalen är tillgänglig när användaren inte har en licens | Är tillgänglig |
| Återställ användarlösenord från Windows 10 AADJ eller H + AADJ låsskärmen när användaren har registrerats | Användare kan återställa lösenord |
| SSPR-registrering och användning data är tillgängliga för administratörer i nära realtid | Är tillgängligt via granskningsloggar |

### <a name="support-plan"></a>Supportplan

SSPR inte skapar vanligtvis användaren problem, är det viktigt att ha stöd för personal förberedda behöver bry dig om problem som kan uppstå.

En administratör kan ändra eller återställa lösenordet för slutanvändare via Azure AD-portalen, men det är bättre för att lösa problemet via en process för support för självbetjäning.

Skapa en lista över supportärenden och deras troliga orsaker i avsnittet använda guiden i det här dokumentet och skapa en guide för matchning.

### <a name="auditing-and-reporting"></a>Granskning och rapportering

Efter distributionen kan många organisationer vill veta hur eller om lösenordsåterställning via självbetjäning (SSPR) som verkligen används. Rapporteringsfunktionen som ger Azure Active Directory (Azure AD) kan du besvara frågor med hjälp av fördefinierade rapporter.

Granskningsloggar för registrering och lösenordsåterställning är tillgängliga i 30 dagar. Om säkerhetsgranskning inom ett företag kräver längre kvarhållning, loggarna måste därför exporteras och används i ett SIEM-verktyg som [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk eller ArcSight.

Dokumentera schemat för säkerhetskopiering, systemet och ansvariga parter i en tabell som den nedan. Du kanske inte behöver avgränsa gransknings- och reporting säkerhetskopior, men du bör ha en separat säkerhetskopiering från vilken du kan återställa från ett problem.

|   | Frekvensen för nedladdning | Målsystemet | Ansvarig part |
| --- | --- | --- | --- |
| Granskning säkerhetskopiering |   |   |   |
| Rapportering av säkerhetskopiering |   |   |   |
| Disaster recovery-återställning |   |   |   |

## <a name="implementation"></a>Implementering

Genomförandet sker i tre steg:

- Konfigurera användare och licenser
- Konfigurera Azure AD SSPR för registrering och självbetjäning
- Konfigurera Azure AD Connect för tillbakaskrivning av lösenord

### <a name="communicate-the-change"></a>Kommunicera ändringen

Börja implementering av kommunikationsplan som du har utvecklats i planeringsfasen.

### <a name="ensure-groups-are-created-and-populated"></a>Se till att grupper skapas och konfigureras

Referera till avsnittet planera lösenord autentisering metoder och se till att grupperna för pilot- eller produktionsmiljö implementeringen är tillgängliga och alla lämpliga användare läggs till grupper.

### <a name="apply-licenses"></a>Använda licenser

De grupper som du ska implementera måste ha Azure AD premium-licens tilldelad. Du kan tilldela licenser direkt i gruppen eller du kan använda befintliga licens principer som via PowerShell eller gruppbaserad licensiering.

Information om hur du tilldelar licenser till grupper av användare finns i artikeln [tilldela licenser till användare av gruppmedlemskap i Azure Active Directory](../users-groups-roles/licensing-groups-assign.md).

### <a name="configure-sspr"></a>Konfigurera SSPR

#### <a name="enable-groups-for-sspr"></a>Aktivera grupper för SSPR

1. Tillgång till Azure portal med ett administratörskonto.
1. Välj alla tjänster, och i rutan Filter skriver du Azure Active Directory och välj sedan Azure Active Directory.
1. Välj återställning av lösenord på bladet Active Directory.
1. I egenskapsfönstret väljer du valt. Om du vill att alla användare som har aktiverats, markera alla.
1. I standardlösenordet återställa principbladet, skriver du namnet på den första gruppen, markera den och sedan klicka på Välj längst ned på skärmen och välj Spara överst på skärmen.
1. Upprepa proceduren för varje grupp.

#### <a name="configure-the-authentication-methods"></a>Konfigurera autentiseringsmetoderna

Referera till planeringen från avsnittet planera lösenord autentiseringsmetoder i det här dokumentet.

1. Välj registrering, under kräva att användare registrerar vid inloggning, Välj Ja och sedan ange antalet dagar innan förfallotiden och välj sedan spara.
1. Välj meddelande, och konfigurera per avtalet och välj sedan spara.
1. Välj anpassning, och konfigurera per avtalet och välj sedan spara.
1. Välj lokal integrering och konfigurera per avtalet och välj sedan spara.

### <a name="enable-sspr-in-windows"></a>Aktivera SSPR i Windows

Windows 10-enheter som kör version 1803 eller senare och som är antingen Azure AD-ansluten eller hybrid Azure AD-anslutna kan återställa sina lösenord på inloggningsskärmen för Windows. Information och steg för att konfigurera den här funktionen finns i artikeln [Azure AD-lösenordsåterställning från inloggningsskärmen](tutorial-sspr-windows.md)

### <a name="configure-password-writeback"></a>Konfigurera tillbakaskrivning av lösenord

Steg för att konfigurera tillbakaskrivning av lösenord för din organisation finns i artikeln [anvisningar: Konfigurera tillbakaskrivning av lösenord](howto-sspr-writeback.md).

## <a name="manage-sspr"></a>Hantera SSPR

Nödvändiga roller för att hantera funktioner som är associerade med lösenordsåterställning via självbetjäning.

| Företag rollen/person | Azure AD-roll (vid behov) |
| :---: | :---: |
| Supportavdelningen nivå 1 | Lösenordsadministratör |
| Supportavdelningen nivå 2 | Användaradministratör |
| SSPR-administratör | Global administratör |

### <a name="support-scenarios"></a>Stöd för scenarier

Du kan skapa frågor och svar baserat på frågor som du får från dina användare om du vill aktivera din support-teamet framgång. I följande tabell innehåller vanliga supportscenarier för.

| Scenarier | Beskrivning |
| --- | --- |
| Användaren har inte några registrerade autentiseringsmetoder som är tillgängliga | En användare försöker att återställa sina lösenord men har inte någon av autentiseringsmetoderna som de registrerade tillgänglig (exempel: de lämnat sin mobiltelefon i hemmet och kan inte komma åt e-post) |
| Användaren är inte tar emot en text eller anropa i sina office eller mobiltelefon | En användare försöker verifiera sin identitet via SMS eller samtal men tar emot inte ett text/anrop. |
| Användaren kan inte komma åt portalen för återställning av lösenord | En användare vill återställa sina lösenord men har inte aktiverats för återställning av lösenord och därför kan inte komma åt sidan för att uppdatera lösenord. |
| Användaren kan inte ange ett nytt lösenord | En användare har slutförts verifiering vid återställning av flödet lösenord men det går inte att ange ett nytt lösenord. |
| Användaren ser inte en länk för återställning av lösenord på en Windows 10-enhet | En användare försöker att återställa lösenord från låsskärmen för Windows 10, men enheten är antingen inte ansluten till Azure AD eller Intune-enhetsprincip är inte aktiverat |

Du kanske också vill inkludera information, till exempel följande för ytterligare felsökning.

- Vilka grupper som är aktiverade för SSPR.
- Vilka autentiseringsmetoder är konfigurerade.
- Åtkomstprinciper som rör på eller av företagets nätverk.
- Felsökningssteg för vanliga scenarier.

Du kan även gå till vår online-dokumentation om hur du felsöker Självbetjäning för återställning av lösenord för att förstå allmän felsökning av de vanligaste scenarierna för SSPR.

## <a name="next-steps"></a>Nästa steg

- [Överväg att implementera Azure AD-lösenordsskydd](concept-password-ban-bad.md)

- [Överväg att implementera Azure AD Smart kontoutelåsning](howto-password-smart-lockout.md)
