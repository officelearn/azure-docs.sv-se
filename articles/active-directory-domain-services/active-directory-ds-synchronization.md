---
title: 'Azure Active Directory Domain Services: Synkronisering i hanterade domäner | Microsoft Docs'
description: Förstå synkronisering i en Azure Active Directory Domain Services-hanterad domän
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: maheshu
ms.openlocfilehash: 463113731d1c4b4d7dfb5b81d429a8b7ffb74b1b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36218868"
---
# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Synkronisering i en Azure AD Domain Services-hanterad domän
Följande diagram illustrerar hur synkroniseringen fungerar i Azure AD Domain Services hanterade domäner.

![Synkronisering i Azure AD Domain Services](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>Synkronisering från din lokala katalog till Azure AD-klient
Azure AD Connect-synkronisering används för att synkronisera användarkonton, gruppmedlemskap och autentiseringsuppgifter hashar till Azure AD-klienten. Attribut för användare, till exempel UPN-konton och lokala SID (security identifier) är synkroniserade. Om du använder Azure AD Domain Services, synkroniseras även äldre autentiseringshasherna som krävs för NTLM och Kerberos-autentisering till Azure AD-klienten.

Om du konfigurerar återskrivning synkroniseras ändringar som uppstår i Azure AD-katalogen tillbaka till din lokala Active Directory. Till exempel om du ändrar ditt lösenord med hjälp av Azure AD självbetjäning lösenordshantering ändrade lösenordet uppdateras i din lokala AD-domän.

> [!NOTE]
> Använd alltid den senaste versionen av Azure AD Connect så du korrigeringar för alla kända programfel.
>
>

## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Synkronisering från Azure AD-klient till din hanterade domän
Användarkonton, gruppmedlemskap och hashvärdena synkroniseras från din Azure AD-klient till din Azure AD Domain Services-hanterad domän. Den här synkroniseringsprocessen sker automatiskt. Du behöver inte konfigurera, övervaka och hantera den här synkroniseringsprocessen. Den första synkroniseringen kan ta några timmar att ett par dagar beroende på antalet objekt i Azure AD-katalogen. När den första synkroniseringen är klar tar ungefär 20 – 30 minuter för ändringar som görs i Azure AD som ska uppdateras i din hanterade domän. Den här synkroniseringsintervall gäller lösenordsändringar eller ändringar i attribut som gjorts i Azure AD.

Synkroniseringsprocessen är också en-way/enkelriktad till sin natur. Din hanterade domän är i stort sett skrivskyddad förutom eventuella anpassade organisationsenheter som du skapar. Därför kan du ändra användarattribut, lösenord eller gruppmedlemskap inom den hanterade domänen. Det finns därför ingen omvänd synkronisering av ändringar från din hanterade domän tillbaka till din Azure AD-klient.

## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Synkronisering från en miljö med flera skogar lokala
Många organisationer har en ganska komplex lokala identitetsinfrastrukturen som består av flera kontoskogar. Azure AD Connect har stöd för synkronisering av användare, grupper och hashvärden för autentiseringsuppgifter från flera skogar miljöer till Azure AD-klienten.

Azure AD-klienten är däremot ett mycket enklare och flat namnområde. Om du vill aktivera användare på ett tillförlitligt sätt åtkomst till program som skyddas av Azure AD konfliktlösning UPN över användarkonton i olika skogar. Din Azure AD Domain Services-hanterad domän är försedd Stäng likhet med Azure AD-klienten. Du kan se en platt organisationsenhetsstrukturen i din hanterade domän. Alla användarkonton och grupper som lagras i behållaren AADDC-användare, trots att synkroniseras från olika lokala domäner eller skogar. Du har konfigurerat en hierarkisk Organisationsenhet struktur lokalt. Din hanterade domän fortfarande har en enkel flat OU-struktur.

## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>Undantag – vad är inte synkroniserade med din hanterade domän
Följande objekt och attribut är inte synkroniserade till Azure AD-klienten eller till din hanterade domän:

* **Exkluderade attribut:** kan du undanta vissa attribut synkroniseras till Azure AD-klienten från din lokala domän med Azure AD Connect. Dessa exkluderade attribut är inte tillgängliga i din hanterade domän.
* **Grupprinciper:** grupprinciper som konfigurerats i din lokala domän synkroniseras inte med din hanterade domän.
* **SYSVOL-resursen:** på samma sätt kan innehållet i Sysvol-resursen på din lokala domän synkroniseras inte med din hanterade domän.
* **Datorobjekt:** datorobjekt för datorer som är anslutna till din lokala domän inte är synkroniserade med din hanterade domän. Dessa datorer inte har en förtroenderelation med din hanterade domän och hör till din lokala domän. I din hanterade domän finns datorobjekt endast för datorer som du har uttryckligen domänansluten till den hanterade domänen.
* **SidHistory-attribut för användare och grupper:** primär användare och primär grupp-SID från din lokala domän synkroniseras till din hanterade domän. Dock har befintliga SidHistory-attribut för användare och grupper inte synkroniserats från din lokala domän för din hanterade domän.
* **Enheter (OU) organisationsstrukturer:** organisationsenheter som definierats i din lokala domän synkroniseras inte med din hanterade domän. Det finns två inbyggda organisationsenheter i din hanterade domän. Din hanterade domän har som standard en platt organisationsenhetsstrukturen. Du kan dock välja att [skapa en egen Organisationsenhet i din hanterade domän](active-directory-ds-admin-guide-create-ou.md).

## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>Hur specifika attribut som synkroniseras till din hanterade domän
I följande tabell visas några vanliga attribut och beskriver hur de ska synkroniseras till din hanterade domän.

| Attributet i en hanterad domän | Källa | Anteckningar |
|:--- |:--- |:--- |
| UPN |Användarens UPN-attributet i Azure AD-klient |UPN-attributet från Azure AD-klienten synkroniseras som att din hanterade domän. Därför är det säkraste sättet att logga in på din hanterade domän med din unika namn. |
| SAMAccountName |Användarens mailNickname attribut i Azure AD-klienten eller autogenererade |Attributet SAMAccountName tillförs från attributet mailNickname i Azure AD-klienten. Om flera användarkonton har samma attribut för mailNickname, är SAMAccountName genereras automatiskt. Om användarens mailNickname eller UPN-prefixet är längre än 20 tecken, är SAMAccountName autogenererade att uppfylla 20 tecken på SAMAccountName attribut. |
| Lösenord |Användarens lösenord från din Azure AD-klient |Autentiseringshasherna som krävs för NTLM eller Kerberos-autentisering (även kallat kompletterande autentiseringsuppgifter) synkroniseras från din Azure AD-klient. Om din Azure AD-klient är en synkroniserade klient, kommer autentiseringsuppgifterna från din lokala domän. |
| Primär användare/grupp-SID |Genereras automatiskt |Primärt SID för användaren eller gruppkonton genereras automatiskt i din hanterade domän. Det här attributet inte matchar primära användare/grupp-SID för objektet i din lokala AD-domän. Detta beror på den hanterade domänen har ett annat namnområde SID än den lokala domänen. |
| SID-historik för användare och grupper |Lokal primär användare och grupp-SID |Attributet SidHistory för användare och grupper i din hanterade domän har angetts att matcha motsvarande primära användare eller grupp-SID i din lokala domän. Den här funktionen kan underlätta lift och SKIFT lokala program till den hanterade domänen, eftersom du inte behöver re ACL-resurser. |

> [!NOTE]
> **Logga in på den hanterade domänen med UPN-format:** i SAMAccountName attributet kanske automatiskt genererade för vissa användarkonton i din hanterade domän. Om flera användare har samma attribut för mailNickname eller användare som har varit alltför långa UPN-prefix, kanske SAMAccountName för dessa användare automatiskt genererade. Därför är SAMAccountName format (till exempel CONTOSO100\joeuser) inte alltid ett säkert sätt att logga in på domänen. Användarnas autogenererade SAMAccountName kan skilja sig från sina UPN-prefix. Använd UPN-format (till exempel 'joeuser@contoso100.com') att logga in på den hanterade domänen på ett tillförlitligt sätt.
>
>

### <a name="attribute-mapping-for-user-accounts"></a>Attributmappning för användarkonton
I följande tabell visas hur vissa attribut för användare som är synkroniserade objekt i Azure AD-klienten till motsvarande attribut i din hanterade domän.

| Användarattribut i Azure AD-klient | Användarattribut i din hanterade domän |
|:--- |:--- |
| accountEnabled |userAccountControl (anger eller tar bort ACCOUNT_DISABLED-bitars) |
| city |L |
| Land |CO |
| avdelning |avdelning |
| Visningsnamn |Visningsnamn |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| Befattning |rubrik |
| E-post |E-post |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (ibland kanske automatiskt genererade) |
| mobila |mobila |
| objekt-ID |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |SID-historik |
| passwordPolicies |userAccountControl (anger eller tar bort DONT_EXPIRE_PASSWORD-bitars) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| Postnummer |Postnummer |
| preferredLanguage |preferredLanguage |
| state |St |
| StreetAddress |StreetAddress |
| Efternamn |SN |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Attributmappning för grupper
I följande tabell visas hur specifika attribut för en grupp objekt i Azure AD-klient som synkroniseras till motsvarande attribut i din hanterade domän.

| Group-attributet i Azure AD-klient | Group-attributet i din hanterade domän |
|:--- |:--- |
| Visningsnamn |Visningsnamn |
| Visningsnamn |SAMAccountName (ibland kanske automatiskt genererade) |
| E-post |E-post |
| mailNickname |msDS-AzureADMailNickname |
| objekt-ID |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |SID-historik |
| securityEnabled |groupType |

## <a name="password-hash-synchronization-and-security-considerations"></a>Lösenord hash-synkronisering och säkerhetsaspekter
När du aktiverar Azure AD Domain Services, Azure AD-katalogen genererar och lagrar lösenordshashvärden i NTLM och Kerberos-kompatibelt format. 

För befintliga användarkonton för molnet, eftersom Azure AD aldrig lagrar sina lösenord i klartext, kan inte dessa värden skapas automatiskt. Microsoft kräver därför [molnanvändare att återställa/ändra sina lösenord](active-directory-ds-getting-started-password-sync.md) för sina lösenordshashvärden genereras och lagras i Azure AD. För alla moln användarkonton som skapats i Azure AD när du har aktiverat Azure AD Domain Services, lösenordshashvärden genereras och lagras i NTLM och Kerberos-kompatibelt format. 

För användarkonton synkroniseras från lokala AD med hjälp av Azure AD Connect Sync, måste du [konfigurera Azure AD Connect för att synkronisera lösenordshashvärden NTLM och Kerberos-kompatibelt format](active-directory-ds-getting-started-password-sync-synced-tenant.md).

NTLM och Kerberos-kompatibel lösenordshashvärden måste alltid lagras på ett sätt som är krypterade i Azure AD. Dessa hashvärden krypteras så att endast Azure AD Domain Services har åtkomst till dekrypteringsnycklarna. Inga andra tjänst eller en komponent i Azure AD har åtkomst till dekrypteringsnycklarna. Krypteringsnycklarna som är unikt per Azure AD-klient. Azure AD Domain Services synkroniserar lösenordshashvärden till domänkontrollanterna för din hanterade domän. Dessa lösenordshashvärden lagras och skyddas på dessa domänkontrollanter som liknar hur lösenord lagras och skyddas på Windows Server AD-domänkontrollanter. Diskar för dessa hanterade domänkontrollanter krypterat i vila.

## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>Objekt som inte är synkroniserade till Azure AD-klient från en hanterad domän
Enligt beskrivningen i föregående avsnitt i den här artikeln, finns det ingen synkronisering från din hanterade domän tillbaka till din Azure AD-klient. Du kan välja att [skapa en egen organisationsenhet (OU)](active-directory-ds-admin-guide-create-ou.md) i din hanterade domän. Dessutom kan du skapa andra organisationsenheter, användare, grupper eller tjänstkonton inom dessa anpassade organisationsenheter. Inga objekt som skapas i anpassade organisationsenheter synkroniseras till Azure AD-klienten. Dessa objekt är tillgängliga för användning endast i en hanterad domän. Därför kan visas dessa objekt inte med hjälp av Azure AD PowerShell-cmdlets, Azure AD Graph API eller hanteringsgränssnittet för Azure AD.

## <a name="related-content"></a>Relaterat innehåll
* [Funktioner – Azure AD Domain Services](active-directory-ds-features.md)
* [Distributionsscenarier - Azure AD Domain Services](active-directory-ds-scenarios.md)
* [Överväganden för nätverk för Azure AD Domain Services](active-directory-ds-networking.md)
* [Komma igång med Azure AD Domain Services](active-directory-ds-getting-started.md)
