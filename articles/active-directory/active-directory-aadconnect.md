---
title: 'Azure AD Connect: Integrera dina lokala identiteter med Azure Active Directory. | Microsoft Docs'
description: "Azure AD Connect integrerar dina lokala kataloger med Azure Active Directory. På så sätt kan du tillhandahålla en gemensam identitet för Office 365-, Azure- och SaaS-program som är integrerade med Azure AD."
keywords: "introduktion till Azure AD Connect, översikt över Azure AD Connect, vad är Azure AD Connect, installera Active Directory"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/04/2016
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eedb788b2a174d01a2ef661cf4093ff938649bce


---
# <a name="integrating-your-onpremises-identities-with-azure-active-directory"></a>Integrera dina lokala identiteter med Azure Active Directory
Azure AD Connect integrerar dina lokala kataloger med Azure Active Directory. På så sätt kan du erbjuda en gemensam identitet för dina användare för Office 365- och Azure SaaS-program som är integrerade med Azure AD. I det här avsnittet beskrivs planerings-, distributions- och användningsstegen. Det är en samling länkar till avsnitt relaterade till det här området.

> [!IMPORTANT]
> [Azure AD Connect är det bästa sättet att ansluta din lokala katalog till Azure AD och Office 365. Det är ett bra tillfälle för att uppgradera till Azure AD Connect från Windows Azure Active Directory Sync (DirSync) eller Azure AD Sync eftersom dessa verktyg nu är föråldrade och supporten för dem upphör den 13 april 2017.](active-directory-aadconnect-dirsync-deprecated.md)
> 
> 

![Vad är Azure AD Connect?](./media/active-directory-aadconnect/arch.png)

## <a name="why-use-azure-ad-connect"></a>Varför ska jag använda Azure AD Connect?
Om du integrerar dina lokala kataloger med Azure AD kan du hjälpa dina användare att bli mer produktiva genom att tillhandahålla en gemensam identitet för åtkomst både till molnet och lokala resurser. Användare och företag kan dra nytta av följande:

* Användarna kan använda samma identitet för att komma åt lokala program och molntjänster som Office 365.
* Ett enda verktyg är allt som krävs för att tillhandahålla en enkel distributionsupplevelse för synkronisering och inloggning.
* Innehåller de senaste funktionerna för dina scenarier. Azure AD Connect ersätter äldre versioner av identitetsintegrationsverktyg som DirSync och Azure AD Sync. Mer information finns i [Jämförelse av katalogintegreringsverktyg för hybrididentitet](active-directory-hybrid-identity-design-considerations-tools-comparison.md).

### <a name="how-azure-ad-connect-works"></a>Hur Azure AD Connect fungerar
Azure Active Directory Connect består av tre huvudkomponenter: synkroniseringstjänsterna, den valfri Active Directory Federation Services-komponenten och övervakningskomponenten [Azure AD Connect Health](active-directory-aadconnect-health.md).

<center>![Azure AD Connect Stack](./media/active-directory-aadconnect-how-it-works/AADConnectStack2.png)
</center>

* Synkronisering – Den här komponenten är ansvarig för att skapa användare, grupper och andra objekt. Den är också ansvarig för att se till att identitetsinformationen för dina lokala användare och grupper matchar molnet.
* AD FS – Federation är en valfri del av Azure AD Connect och kan användas för att konfigurera en hybridmiljö med hjälp av en lokal AD FS-infrastruktur. Detta kan användas av organisationer i samband med komplexa distributioner, till exempel enkel inloggning för domänanslutning, genomdrivande av AD-inloggningsprinciper och MFA med smartkort eller från en tredje part.
* Hälsoövervakning – Azure AD Connect Health kan tillhandahålla robust övervakning och en central plats på Azure-portalen för att övervaka den här aktiviteten. Mer information finns i [Azure Active Directory Connect Health](active-directory-aadconnect-health.md).

## <a name="install-azure-ad-connect"></a>Installera Azure AD Connect
Du kan ladda ned Azure AD Connect från [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=615771).

| Lösning | Scenario |
| --- | --- |
| Innan du börjar – [Maskinvara och krav](active-directory-aadconnect-prerequisites.md) |<li>Steg som du måste utföra innan du börjar installera Azure AD Connect.</li> |
| [Standardinställningar](connect/active-directory-aadconnect-get-started-express.md) |<li>Om du har en AD med enda skog är detta det rekommenderade alternativet.</li> <li>Användaren loggar in med samma lösenord med hjälp av lösenordssynkronisering.</li> |
| [Anpassade inställningar](connect/active-directory-aadconnect-get-started-custom.md) |<li>Används när du har flera skogar. Har stöd för många lokala [topologier](active-directory-aadconnect-topologies.md).</li> <li>Anpassa ditt inloggningsalternativ, t.ex. AD FS för federation eller använd en identitetsprovider från en tredje part.</li> <li>Anpassa synkroniseringsfunktioner, t.ex. filtrering och tillbakaskrivning.</li> |
| [Uppgradera från DirSync](connect/active-directory-aadconnect-dirsync-upgrade-get-started.md) |<li>Används när du har en befintlig DirSync-server som redan körs.</li> |
| [Uppgradera från Azure AD Sync eller Azure AD Connect](active-directory-aadconnect-upgrade-previous-version.md) |<li>Det finns flera olika metoder beroende på dina preferenser.</li> |

[Efter installationen](active-directory-aadconnect-whats-next.md) bör du kontrollera att allt fungerar som förväntat och tilldela licenser till användarna.

### <a name="next-steps-to-install-azure-ad-connect"></a>Nästa steg för att installera Azure AD Connect
| Avsnitt |
| --- | --- |
| Ladda ned Azure AD Connect |
| Installera med standardinställningar |
| Installera med anpassade inställningar |
| Uppgradera från DirSync |
| Efter installationen |

### <a name="learn-more-about-install-azure-ad-connect"></a>Mer information om installationen av Azure AD Connect
Det är bra att vara förberedd om det skulle uppstå [driftproblem](active-directory-aadconnectsync-operations.md). Du kanske vill ha en reservserver så att du enkelt kan växla till den i händelse av ett [allvarligt fel](active-directory-aadconnectsync-operations.md#disaster-recovery). Om du ofta kommer att göra konfigurationsändringar bör du planera för ett server i [mellanlagringsläge](active-directory-aadconnectsync-operations.md#staging-mode).

| Avsnitt |
| --- | --- |
| Topologier som stöds |
| Designbegrepp |
| Konton som används för installation |
| Driftplanering |
| Alternativ för användarinloggning |

## <a name="configure-sync-features"></a>Konfigurera synkroniseringsfunktioner
Azure AD Connect har flera funktioner som du kan aktivera om du vill eller som är aktiverade som standard. Vissa funktioner kan ibland kräva ytterligare konfiguration i vissa scenarier och topologier.

[Filtrering](active-directory-aadconnectsync-configure-filtering.md) används när du vill begränsa vilka objekt som synkroniseras till Azure AD. Som standard synkroniseras alla användare, kontakter, grupper och Windows 10-datorer. Du kan ändra filtreringen baserat på domäner, organisationsenheter eller attribut.

[Lösenordssynkronisering](active-directory-aadconnectsync-implement-password-synchronization.md) synkroniserar lösenordshashen i Active Directory till Azure AD. Slutanvändare kan använda samma lösenord lokalt och i molnet men hanterar det endast på en plats. Eftersom din lokala Active Directory används som auktoritet kan du också använda en egen lösenordsprincip.

Med [tillbakaskrivning av lösenord](active-directory-passwords-getting-started.md) kan dina användare ändra och återställa sina lösenord i molnet och tillämpa din lokala lösenordsprincip.

Med [tillbakaskrivning av enheter](active-directory-aadconnect-feature-device-writeback.md) kan en enhet som är registrerad i Azure AD skrivas tillbaka till lokala Active Directory och användas för villkorlig åtkomst.

Funktionen [förhindra oavsiktliga borttagningar](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) är aktiverad som standard och skyddar din molnkatalog mot flera samtidiga borttagningar. Som standard tillåts 500 borttagningar per körning. Du kan ändra den här inställningen beroende på storleken på din organisation.

Funktionen [Automatisk uppgradering](active-directory-aadconnect-feature-automatic-upgrade.md) är aktiverad som standard för installationer med standardinställningar och säkerställer att Azure AD Connect alltid är uppdaterat med den senaste versionen.

### <a name="next-steps-to-configure-sync-features"></a>Nästa steg för att konfigurera synkroniseringsfunktioner
| Avsnitt |
| --- | --- |
| Konfigurera filtrering |
| Lösenordssynkronisering |
| Tillbakaskrivning av lösenord |
| Tillbakaskrivning av enheter |
| Förhindra oavsiktliga borttagningar |
| Automatisk uppgradering |

## <a name="customize-azure-ad-connect-sync"></a>Anpassa synkroniseringen av Azure AD Connect
Azure AD Connect-synkroniseringsverktyget levereras med en standardkonfiguration som är avsedd att fungera för de flesta kunder och topologier. Men det finns alltid situationer då standardkonfigurationen inte fungerar och måste justeras. Du kan göra ändringarna som beskrivs i det här avsnittet och i länkade avsnitt.

Om du inte har arbetat med en synkroniseringstopologi förut är det bäst att börja med att lära sig grunderna och de termer som används. Mer information finns i [Tekniska begrepp](active-directory-aadconnectsync-technical-concepts.md). Azure AD Connect är en utveckling av MIIS2003, ILM2007 och FIM2010. Även om vissa saker är identiska, har mycket ändrats.

[Standardkonfigurationen](active-directory-aadconnectsync-understanding-default-configuration.md) förutsätter att det kan finnas mer än en skog i konfigurationen. I dessa topologier kan ett användarobjekt representeras som en kontakt i en annan skog. Användaren kan också ha en länkad postlåda i en annan resursskog. Standardkonfigurationens funktionssätt beskrivs i [användare och kontakter](active-directory-aadconnectsync-understanding-users-and-contacts.md).

Konfigurationsmodellen i synkroniseringsverktyget kallas för [deklarativ etablering](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md). De avancerade attributflödena använder [funktioner](active-directory-aadconnectsync-functions-reference.md) för att uttrycka attributtransformationer. Du kan se och granska hela konfigurationen med hjälp av verktyg som medföljer Azure AD Connect. Om du behöver göra konfigurationsändringar ser du till att du följer [metodtipsen](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) så att det är lättare att integrera nya versioner.

### <a name="next-steps-to-customize-azure-ad-connect-sync"></a>Nästa steg för att anpassa Azure AD Connect-synkroniseringen
| Avsnitt |
| --- | --- |
| Alla artiklar om Azure AD Connect-synkronisering |
| Tekniska begrepp |
| Förstå standardkonfigurationen |
| Förstå användare och kontakter |
| Deklarativ etablering |
| Ändra standardkonfigurationen |

## <a name="configure-federation-features"></a>Konfigurera federationsfunktioner
AD FS kan konfigureras att ge stöd för [flera domäner](active-directory-aadconnect-multiple-domains.md). Du kan till exempel ha flera toppdomäner som du behöver använda för federation.

Om AD FS-servern inte har konfigurerats att automatiskt uppdatera certifikat från Azure AD eller om du använder en annan lösning än en ADFS-lösning så meddelas du när du behöver [uppdatera certifikaten](active-directory-aadconnect-o365-certs.md).

### <a name="next-steps-to-configure-federation-features"></a>Nästa steg för att konfigurera federationsfunktioner
| Avsnitt |
| --- | --- |
| Alla AD FS-artiklar |
| Konfigurera AD FS med underdomäner |
| Hantera AD FS-servergrupp |
| Uppdatera federationscertifikat manuellt |

## <a name="more-information-and-references"></a>Mer information och referenser
| Avsnitt |
| --- | --- |
| Versionshistorik |
| Jämföra DirSync, Azure ADSync och Azure AD Connect |
| Kompatibilitetslista för Azure AD för andra lösningar än ADFS-lösningar |
| Attribut som synkroniseras |
| Övervaka med hjälp av Azure AD Connect Health |
| Vanliga frågor och svar |

**Ytterligare resurser**

Ignite 2015-presentation om hur du utökar dina lokala kataloger till molnet.

> [!VIDEO https://channel9.msdn.com/Events/Ignite/2015/BRK3862/player]
> 
> 




<!--HONumber=Nov16_HO2-->


