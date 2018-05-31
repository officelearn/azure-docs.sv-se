---
title: Anslut Active Directory med Azure Active Directory. | Microsoft Docs
description: Azure AD Connect integrerar dina lokala kataloger med Azure Active Directory. På så sätt kan du tillhandahålla en gemensam identitet för Office 365-, Azure- och SaaS-program som är integrerade med Azure AD.
keywords: introduktion till Azure AD Connect, översikt över Azure AD Connect, vad är Azure AD Connect, installera Active Directory
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/19/2018
ms.author: billmath
ms.openlocfilehash: 128a6bffe4e604eb4e02a10f13d561a336fc8c61
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34354426"
---
# <a name="integrate-your-on-premises-directories-with-azure-active-directory"></a>Integrerar dina lokala kataloger med Azure Active Directory
Azure AD Connect integrerar dina lokala kataloger med Azure Active Directory. På så sätt kan du erbjuda en gemensam identitet för dina användare för Office 365- och Azure SaaS-program som är integrerade med Azure AD. I det här avsnittet beskrivs planerings-, distributions- och användningsstegen. Det är en samling länkar till avsnitt relaterade till det här området.

> [!IMPORTANT]
> [Azure AD Connect är det bästa sättet att ansluta din lokala katalog till Azure AD och Office 365. Det är ett bra tillfälle för att uppgradera till Azure AD Connect från Windows Azure Active Directory Sync (DirSync) eller Azure AD Sync eftersom dessa verktyg nu är inaktuella och supporten för dem upphör den 13 april 2017.](active-directory-aadconnect-dirsync-deprecated.md)  Dessutom:



> 
> - Att synkronisera användare med Microsoft Azure Active Directory är en **kostnadsfri funktion** och kräver inte att kunderna har någon betald prenumeration.
>- Synkroniserade användare **beviljas inte automatiskt** *någon* licens. Administratörer har fortfarande fullständig kontroll över licenstilldelningen. 
> - Microsofts rekommendation är att IT-administratörerna synkroniserar alla sina användare. Detta avblockerar inte bara användarnas åtkomst till integrerade Microsoft Azure Active Directory-resurser, utan ger dessutom IT-administratörerna en mycket bredare vy så att de kan se vilka program som användarna använder. 

![Vad är Azure AD Connect?](media/active-directory-aadconnect/arch.png)

## <a name="why-use-azure-ad-connect"></a>Varför ska jag använda Azure AD Connect?
Om du integrerar dina lokala kataloger med Azure AD kan du hjälpa dina användare att bli mer produktiva genom att tillhandahålla en gemensam identitet för åtkomst både till molnet och lokala resurser. Användare och företag kan dra nytta av följande:

* Användarna kan använda samma identitet för att komma åt lokala program och molntjänster som Office 365.
* Ett enda verktyg är allt som krävs för att tillhandahålla en enkel distributionsupplevelse för synkronisering och inloggning.
* Innehåller de senaste funktionerna för dina scenarier. Azure AD Connect ersätter äldre versioner av identitetsintegrationsverktyg som DirSync och Azure AD Sync. Mer information finns i [Jämförelse av katalogintegreringsverktyg för hybrididentitet](../active-directory-hybrid-identity-design-considerations-tools-comparison.md).

### <a name="how-azure-ad-connect-works"></a>Hur Azure AD Connect fungerar
Azure Active Directory Connect består av tre huvudkomponenter: synkroniseringstjänsterna, den valfri Active Directory Federation Services-komponenten och övervakningskomponenten [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md).

<center>![Azure AD Connect Stack](./media/active-directory-aadconnect-how-it-works/AADConnectStack2.png)
</center>

* Synkronisering – Den här komponenten är ansvarig för att skapa användare, grupper och andra objekt. Den är också ansvarig för att se till att identitetsinformationen för dina lokala användare och grupper matchar molnet.
* AD FS – Federation är en valfri del av Azure AD Connect och kan användas för att konfigurera en hybridmiljö med hjälp av en lokal AD FS-infrastruktur. Detta kan användas av organisationer i samband med komplexa distributioner, till exempel enkel inloggning för domänanslutning, genomdrivande av AD-inloggningsprinciper och MFA med smartkort eller från en tredje part.
* Hälsoövervakning – Azure AD Connect Health kan tillhandahålla robust övervakning och en central plats på Azure-portalen för att övervaka den här aktiviteten. Mer information finns i [Azure Active Directory Connect Health](../connect-health/active-directory-aadconnect-health.md).

## <a name="install-azure-ad-connect"></a>Installera Azure AD Connect

> [!IMPORTANT]
> Microsoft stöder inte ändring eller hantering av Azure AD Connect-synkronisering utöver de åtgärder som är formellt dokumenterade. Sådana åtgärder kan göra att Azure AD Connect-synkroniseringen hamnar i ett inkonsekvent tillstånd eller ett tillstånd som inte stöds. Därför kan Microsoft inte tillhandahålla teknisk support för sådana distributioner.

Du kan ladda ned Azure AD Connect från [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=615771).

| Lösning | Scenario |
| --- | --- |
| Innan du börjar – [Maskinvara och krav](active-directory-aadconnect-prerequisites.md) |<li>Steg som du måste utföra innan du börjar installera Azure AD Connect.</li> |
| [Standardinställningar](active-directory-aadconnect-get-started-express.md) |<li>Om du har en AD med enda skog är detta det rekommenderade alternativet.</li> <li>Användaren loggar in med samma lösenord med hjälp av lösenordssynkronisering.</li> |
| [Anpassade inställningar](active-directory-aadconnect-get-started-custom.md) |<li>Används när du har flera skogar. Har stöd för många lokala [topologier](active-directory-aadconnect-topologies.md).</li> <li>Anpassa ditt inloggningsalternativ, till exempel direktautentisering, AD FS för federation eller använd en tredje parts identitetsprovider.</li> <li>Anpassa synkroniseringsfunktioner, t.ex. filtrering och tillbakaskrivning.</li> |
| [Uppgradera från DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md) |<li>Används när du har en befintlig DirSync-server som redan körs.</li> |
| [Uppgradera från Azure AD Sync eller Azure AD Connect](active-directory-aadconnect-upgrade-previous-version.md) |<li>Det finns flera olika metoder beroende på dina preferenser.</li> |

[Efter installationen](active-directory-aadconnect-whats-next.md) bör du kontrollera att allt fungerar som förväntat och tilldela licenser till användarna.

### <a name="next-steps-to-install-azure-ad-connect"></a>Nästa steg för att installera Azure AD Connect
|Avsnitt |Länk|  
| --- | --- |
|Ladda ned Azure AD Connect | [Ladda ned Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)|
|Installera med standardinställningar | [Snabbinstallation av Azure AD Connect](./active-directory-aadconnect-get-started-express.md)|
|Installera med anpassade inställningar | [Anpassad installation av Azure AD Connect](./active-directory-aadconnect-get-started-custom.md)|
|Uppgradera från DirSync | [Uppgradera från Azure AD-synkroniseringsverktyg (DirSync)](./active-directory-aadconnect-dirsync-upgrade-get-started.md)|
|Efter installationen | [Bekräfta installationen och tilldela licenser ](active-directory-aadconnect-whats-next.md)|

### <a name="learn-more-about-install-azure-ad-connect"></a>Mer information om installationen av Azure AD Connect
Det är bra att vara förberedd om det skulle uppstå [driftproblem](active-directory-aadconnectsync-operations.md). Du kanske vill ha en reservserver som du enkelt kan redundansväxla till i händelse av ett [allvarligt fel](active-directory-aadconnectsync-operations.md#disaster-recovery). Om du ofta kommer att göra konfigurationsändringar bör du planera för ett server i [mellanlagringsläge](active-directory-aadconnectsync-operations.md#staging-mode).

|Avsnitt |Länk|  
| --- | --- |
|Topologier som stöds | [Topologier för Azure AD Connect](active-directory-aadconnect-topologies.md)|
|Designbegrepp | [Designbegrepp för Azure AD Connect](active-directory-aadconnect-design-concepts.md)|
|Konton som används för installation | [Mer information om Azure AD Connect-autentiseringsuppgifter och -behörigheter](./active-directory-aadconnect-accounts-permissions.md)|
|Driftplanering | [Azure AD Connect-synkronisering: Driftåtgärder och saker att tänka på](active-directory-aadconnectsync-operations.md)|
|Alternativ för användarinloggning | [Alternativ för användarinloggning i Azure AD Connect](active-directory-aadconnect-user-signin.md)|

## <a name="configure-sync-features"></a>Konfigurera synkroniseringsfunktioner
Azure AD Connect har flera funktioner som du kan aktivera om du vill eller som är aktiverade som standard. Vissa funktioner kan ibland kräva ytterligare konfiguration i vissa scenarier och topologier.

[Filtrering](active-directory-aadconnectsync-configure-filtering.md) används när du vill begränsa vilka objekt som synkroniseras till Azure AD. Som standard synkroniseras alla användare, kontakter, grupper och Windows 10-datorer. Du kan ändra filtreringen baserat på domäner, organisationsenheter eller attribut.

[Synkronisering av lösenordshash](active-directory-aadconnectsync-implement-password-hash-synchronization.md) synkroniserar lösenordshashen i Active Directory med Azure AD. Slutanvändare kan använda samma lösenord lokalt och i molnet men hanterar det endast på en plats. Eftersom din lokala Active Directory används som auktoritet kan du också använda en egen lösenordsprincip.

Med [tillbakaskrivning av lösenord](../authentication/quickstart-sspr.md) kan dina användare ändra och återställa sina lösenord i molnet och tillämpa din lokala lösenordsprincip.

Med [tillbakaskrivning av enheter](active-directory-aadconnect-feature-device-writeback.md) kan en enhet som är registrerad i Azure AD skrivas tillbaka till lokala Active Directory och användas för villkorlig åtkomst.

Funktionen [förhindra oavsiktliga borttagningar](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) är aktiverad som standard och skyddar din molnkatalog mot flera samtidiga borttagningar. Som standard tillåts 500 borttagningar per körning. Du kan ändra den här inställningen beroende på storleken på din organisation.

Funktionen [Automatisk uppgradering](active-directory-aadconnect-feature-automatic-upgrade.md) är aktiverad som standard för installationer med standardinställningar och säkerställer att Azure AD Connect alltid är uppdaterat med den senaste versionen.

### <a name="next-steps-to-configure-sync-features"></a>Nästa steg för att konfigurera synkroniseringsfunktioner
|Avsnitt |Länk|  
| --- | --- |
|Konfigurera filtrering | [Azure AD Connect-synkronisering: Konfigurera filtrering](active-directory-aadconnectsync-configure-filtering.md)|
|Synkronisering av lösenordshash | [Azure AD Connect-synkronisering: Implementera synkronisering av lösenordshash](active-directory-aadconnectsync-implement-password-hash-synchronization.md)|
|Tillbakaskrivning av lösenord | [Komma igång med lösenordshantering](../authentication/quickstart-sspr.md)|
|Tillbakaskrivning av enheter | [Aktivera tillbakaskrivning av enheter i Azure AD Connect](active-directory-aadconnect-feature-device-writeback.md)|
|Förhindra oavsiktliga borttagningar | [Azure AD Connect-synkronisering: Förhindra oavsiktliga borttagningar](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)|
|Automatisk uppgradering | [Azure AD Connect: Automatisk uppgradering](active-directory-aadconnect-feature-automatic-upgrade.md)|

## <a name="customize-azure-ad-connect-sync"></a>Anpassa synkroniseringen av Azure AD Connect
Azure AD Connect-synkroniseringsverktyget levereras med en standardkonfiguration som är avsedd att fungera för de flesta kunder och topologier. Men det finns alltid situationer då standardkonfigurationen inte fungerar och måste justeras. Du kan göra ändringarna som beskrivs i det här avsnittet och i länkade avsnitt.

Om du inte har arbetat med en synkroniseringstopologi förut är det bäst att börja med att lära sig grunderna och de termer som används. Mer information finns i [Tekniska begrepp](active-directory-aadconnectsync-technical-concepts.md). Azure AD Connect är en utveckling av MIIS2003, ILM2007 och FIM2010. Även om vissa saker är identiska, har mycket ändrats.

[Standardkonfigurationen](active-directory-aadconnectsync-understanding-default-configuration.md) förutsätter att det kan finnas mer än en skog i konfigurationen. I dessa topologier kan ett användarobjekt representeras som en kontakt i en annan skog. Användaren kan också ha en länkad postlåda i en annan resursskog. Standardkonfigurationens funktionssätt beskrivs i [användare och kontakter](active-directory-aadconnectsync-understanding-users-and-contacts.md).

Konfigurationsmodellen i synkroniseringsverktyget kallas för [deklarativ etablering](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md). De avancerade attributflödena använder [funktioner](active-directory-aadconnectsync-functions-reference.md) för att uttrycka attributtransformationer. Du kan se och granska hela konfigurationen med hjälp av verktyg som medföljer Azure AD Connect. Om du behöver göra konfigurationsändringar ser du till att du följer [metodtipsen](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) så att det är lättare att integrera nya versioner.

### <a name="next-steps-to-customize-azure-ad-connect-sync"></a>Nästa steg för att anpassa Azure AD Connect-synkroniseringen
|Avsnitt |Länk|  
| --- | --- |
|Alla artiklar om Azure AD Connect-synkronisering | [Azure AD Connect-synkronisering](active-directory-aadconnectsync-whatis.md)|
|Tekniska begrepp | [Azure AD Connect-synkronisering: Tekniska begrepp](active-directory-aadconnectsync-technical-concepts.md)|
|Förstå standardkonfigurationen | [Azure AD Connect-synkronisering: Förstå standardkonfigurationen](active-directory-aadconnectsync-understanding-default-configuration.md)|
|Förstå användare och kontakter | [Azure AD Connect-synkronisering: Förstå användare och kontakter](active-directory-aadconnectsync-understanding-users-and-contacts.md)|
|Deklarativ etablering | [Azure AD Connect-synkronisering: Förstå uttryck för deklarativ etablering](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md)|
|Ändra standardkonfigurationen | [Metodtips för att ändra standardkonfigurationen](active-directory-aadconnectsync-best-practices-changing-default-configuration.md)|

## <a name="configure-federation-features"></a>Konfigurera federationsfunktioner

Azure AD Connect har flera funktioner som förenklar federering med Azure AD med hjälp av AD FS och hantering av federationsförtroendet. Azure AD Connect stöder AD FS på Windows Server 2012R2 eller senare.

[Uppdatera SSL-certifikatet för AD FS-servergruppen](active-directory-aadconnectfed-ssl-update.md) även om du inte använder Azure AD Connect för hantera federationsförtroendet.

[Lägg till en AD FS-server](active-directory-aadconnect-federation-management.md#addadfsserver) i servergruppen för att utöka servergruppen efter behov.

[Reparera förtroendet](active-directory-aadconnect-federation-management.md#repairthetrust) med Azure AD med några enkla klick.

AD FS kan konfigureras att ge stöd för [flera domäner](active-directory-aadconnect-multiple-domains.md). Du kan till exempel ha flera toppdomäner som du behöver använda för federation.

Om ADFS-servern inte har konfigurerats att automatiskt uppdatera certifikat från Azure AD eller om du använder en annan lösning än en ADFS-lösning så meddelas du när du behöver [uppdatera certifikaten](active-directory-aadconnect-o365-certs.md).

### <a name="next-steps-to-configure-federation-features"></a>Nästa steg för att konfigurera federationsfunktioner
|Avsnitt |Länk|  
| --- | --- |
|Alla AD FS-artiklar | [Azure AD Connect och federation](active-directory-aadconnectfed-whatis.md)|
|Konfigurera AD FS med underdomäner | [Stöd för flera domäner för federering med Azure AD](active-directory-aadconnect-multiple-domains.md)|
|Hantera AD FS-servergrupp | [Hantering och anpassning av AD FS med Azure AD Connect](active-directory-aadconnect-federation-management.md)|
|Uppdatera federationscertifikat manuellt | [Förnya federationscertifikat för Office 365 och Azure AD](active-directory-aadconnect-o365-certs.md)|

## <a name="more-information-and-references"></a>Mer information och referenser
|Avsnitt |Länk|  
| --- | --- |
|Versionshistorik | [Versionshistorik](active-directory-aadconnect-version-history.md)|
|Jämföra DirSync, Azure ADSync och Azure AD Connect | [Jämförelse av katalogintegreringsverktyg](../active-directory-hybrid-identity-design-considerations-tools-comparison.md)|
|Kompatibilitetslista för Azure AD för andra lösningar än ADFS-lösningar | [Kompatibilitetslista för Azure AD-federation](active-directory-aadconnect-federation-compatibility.md)|
|Konfigurera en SAML 2.0-identitetsprovider (IdP)|[Använda en SAML 2.0-identitetsprovider (IdP) för enkel inloggning](active-directory-aadconnect-federation-saml-idp.md)|
|Attribut som synkroniseras | [Attribut som synkroniseras](active-directory-aadconnectsync-attributes-synchronized.md)|
|Övervaka med hjälp av Azure AD Connect Health | [Azure AD Connect Health](../connect-health/active-directory-aadconnect-health.md)|
|Vanliga frågor och svar | [Vanliga frågor och svar om Azure AD Connect](active-directory-aadconnect-faq.md)|

**Ytterligare resurser**

Ignite 2015-presentation om hur du utökar dina lokala kataloger till molnet.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3862/player]
> 
> 

