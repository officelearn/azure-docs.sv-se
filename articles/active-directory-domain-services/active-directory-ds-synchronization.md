---
title: 'Azure Active Directory Domain Services: Synkronisering i hanterade domäner | Microsoft Docs'
description: Förstå synkronisering i en Azure Active Directory Domain Services-hanterad domän
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: ergreenl
ms.openlocfilehash: 23d30d3b881fbe5aab4e537073b2582eb01bfe7c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55183432"
---
# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Synkronisering i en Azure AD Domain Services-hanterad domän
Följande diagram illustrerar hur synkroniseringen fungerar i Azure AD Domain Services hanterade domäner.

![Synkronisering i Azure AD Domain Services](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>Synkronisering från din lokala katalog till din Azure AD-klient
Azure AD Connect-synkronisering används för att synkronisera användarkonton, gruppmedlemskap och autentiseringsuppgifter autentiseringshasher till Azure AD-klienten. Attribut för användare, till exempel UPN-konton och lokala SID (security identifier) är synkroniserade. Om du använder Azure AD Domain Services, synkroniseras även äldre autentiseringshasher som krävs för NTLM och Kerberos-autentisering till Azure AD-klienten.

Om du konfigurerar tillbakaskrivning, synkroniseras ändringar som sker i Azure AD-katalogen tillbaka till din lokala Active Directory. Till exempel om du ändrar ditt lösenord med Azure AD-självbetjäning lösenordshantering lösenordet uppdateras i din lokala AD-domän.

> [!NOTE]
> Använd alltid den senaste versionen av Azure AD Connect för att se till att du har korrigeringar för alla kända buggar.
>
>

## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Synkronisering från Azure AD-klienten till din hanterade domän
Användarkonton, gruppmedlemskap och hashvärden för autentiseringsuppgifter synkroniseras från din Azure AD-klient till din hanterade domän i Azure AD Domain Services. Den här synkroniseringsprocessen sker automatiskt. Du behöver inte konfigurera, övervaka eller hantera den här synkroniseringsprocessen. Den första synkroniseringen kan ta från några timmar till några dagar beroende på antalet objekt i Azure AD-katalogen. När den inledande synkroniseringen har slutförts tar det cirka 20 – 30 minuter för ändringar som görs i Azure AD som ska uppdateras i din hanterade domän. Den här synkroniseringsintervall gäller för lösenordsändringar eller ändringar i attribut som görs i Azure AD.

Synkroniseringsprocessen är också en-way/enkelriktade sin natur. Den hanterade domänen är i stort sett skrivskyddad förutom eventuella anpassade organisationsenheter som du skapar. Därför kan du kan inte ändra användarattributen, lösenord eller medlemskap i grupper i den hanterade domänen. Det finns därför ingen omvänd synkronisering av ändringar från en hanterad domän till Azure AD-klienten.

## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Synkronisering sker från en miljö med flera skogar på plats
Många organisationer har en ganska komplex den lokala identitetsinfrastrukturen som består av flera kontoskogar. Azure AD Connect stöder synkronisering av användare, grupper och autentiseringshasher från miljöer med Multi-Forest till Azure AD-klienten.

Azure AD-klienten är däremot ett mycket enklare och fast namnområde. Om du vill aktivera användare på ett tillförlitligt sätt åtkomst till program som skyddas av Azure AD, konfliktlösning UPN för användarkonton i olika skogar. Din Azure AD Domain Services-hanterad domän är försedd Stäng likhet med Azure AD-klienten. Du ser en platt OU-strukturen i din hanterade domän. Alla användarkonton och grupper lagras i behållaren ”AADDC-användare” trots som synkroniseras från olika lokala domäner eller skogar. Du har konfigurerat en hierarkisk Organisationsenhet strukturera lokalt. Den hanterade domänen har fortfarande en enkel fast OU-strukturen.

## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>Undantag – vad är inte synkroniserat till din hanterade domän
Följande objekt och attribut synkroniseras inte till Azure AD-klienten eller till din hanterade domän:

* **Exkluderade attribut:** Du kan välja att exkludera vissa attribut synkroniseras till Azure AD-klienten från din lokala domän med Azure AD Connect. Dessa exkluderade attribut är inte tillgängliga i den hanterade domänen.
* **Grupprinciper:** Grupprinciper som konfigurerats i din lokala domän synkroniseras inte till din hanterade domän.
* **SYSVOL-resursen:** På samma sätt kan synkroniseras innehållet i Sysvol-resursen på din lokala domän inte till din hanterade domän.
* **Datorobjekt:** Datorobjekt för datorer som är anslutna till din lokala domän synkroniseras inte till din hanterade domän. De här datorerna inte har en förtroenderelation med den hanterade domänen och hör till din lokala domän. I den hanterade domänen hitta datorobjekt endast för datorer som du har uttryckligen domänansluten till den hanterade domänen.
* **SidHistory-attribut för användare och grupper:** Primär användare och primära grupp-SID från din lokala domän synkroniseras till din hanterade domän. Dock synkroniseras inte befintliga SidHistory-attribut för användare och grupper från din lokala domän till din hanterade domän.
* **Organisationsstrukturer för enheter (OU):** Organisationsenheter som definierats i din lokala domän synkroniseras inte till din hanterade domän. Det finns två inbyggda organisationsenheter i din hanterade domän. Den hanterade domänen har en fast organisationsenhetsstruktur som standard. Du kan dock välja att [skapa en anpassad Organisationsenhet i den hanterade domänen](active-directory-ds-admin-guide-create-ou.md).

## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>Hur specifika attribut som synkroniseras till din hanterade domän
I följande tabell visas några vanliga attribut och beskriver hur de ska synkroniseras till din hanterade domän.

| Attributet i din hanterade domän | Källa | Anteckningar |
|:--- |:--- |:--- |
| UPN |Användarens UPN-attributet i Azure AD-klienten |UPN-attributet från Azure AD-klienten synkroniseras eftersom är till din hanterade domän. Därför är det säkraste sättet att logga in till din hanterade domän med ditt UPN. |
| SAMAccountName |Användarens mailNickname attribut i Azure AD-klienten eller automatiskt genererade |SAMAccountName-attribut kommer från mailNickname-attributet i Azure AD-klienten. Om flera användarkonton har samma mailNickname-attributet kan genereras SAMAccountName automatiskt. Om användarens mailNickname eller UPN-prefixet är längre än 20 tecken, genereras SAMAccountName automatiskt att uppfylla 20 teckengränsen på SAMAccountName-attribut. |
| Lösenord |Användarens lösenord från Azure AD-klienten |Autentiseringshasher som krävs för NTLM eller Kerberos-autentisering (kallas även kompletterande autentiseringsuppgifter) synkroniseras från din Azure AD-klient. Om din Azure AD-klient är en synkroniserad klient, kommer dessa autentiseringsuppgifter från din lokala domän. |
| Primär användare/grupp-SID |Automatiskt genererade |Primärt SID för användaren/gruppkonton genereras automatiskt i din hanterade domän. Det här attributet inte matchar primära användare/grupp-SID för objekt i din lokala AD-domän. Denna felmatchning beror på den hanterade domänen har ett annat SID-namnområde än din lokala domän. |
| SID-historik för användare och grupper |Lokal primär användare och grupp-SID |Attributet SidHistory för användare och grupper i din hanterade domän har angetts så att de matchar motsvarande primära användare eller grupp-SID i din lokala domän. Den här funktionen hjälper till att underlätta lift and shift av lokala program till den hanterade domänen, eftersom du inte behöver re ACL-resurser. |

> [!NOTE]
> **Logga in på den hanterade domänen med UPN-formatet:** Attributet SAMAccountName kanske autogenererade för vissa användarkonton i din hanterade domän. Om flera användare har samma mailNickname-attributet eller om användarna har alltför långa UPN-prefix, vara SAMAccountName för dessa användare automatiskt genererade. Därför är SAMAccountName-format (till exempel CONTOSO100\joeuser) inte alltid ett tillförlitligt sätt att logga in på domänen. Användarnas automatiskt genererade SAMAccountName kan skilja sig från sina UPN-prefix. Använda UPN-formatet (till exempel ”joeuser@contoso100.com”) att logga in på den hanterade domänen på ett tillförlitligt sätt.
>
>

### <a name="attribute-mapping-for-user-accounts"></a>Attributmappning för användarkonton
I följande tabell visas hur specifika attribut för användare som har objekt i din Azure AD-klient som synkroniseras till motsvarande attribut i den hanterade domänen.

| Användarattribut i Azure AD-klienten | Användarattribut i din hanterade domän |
|:--- |:--- |
| accountEnabled |userAccountControl (anger eller tar bort ACCOUNT_DISABLED-bitars) |
| city |L |
| Land |CO |
| avdelning |avdelning |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| jobTitle |rubrik |
| e-post |e-post |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (kan ibland vara automatiskt genererade) |
| mobila |mobila |
| objekt-ID |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |SID-historik |
| passwordPolicies |userAccountControl (anger eller tar bort DONT_EXPIRE_PASSWORD-bitars) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| Postnummer |Postnummer |
| preferredLanguage |preferredLanguage |
| state |St |
| streetAddress |streetAddress |
| Efternamn |SN |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Attributmappning för grupper
I följande tabell visas hur specifika attribut för en grupp objekt i din Azure AD-klient som synkroniseras till motsvarande attribut i den hanterade domänen.

| Grupp-attributet i Azure AD-klienten | Grupp-attributet i din hanterade domän |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (kan ibland vara automatiskt genererade) |
| e-post |e-post |
| mailNickname |msDS-AzureADMailNickname |
| objekt-ID |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |SID-historik |
| securityEnabled |groupType |

## <a name="password-hash-synchronization-and-security-considerations"></a>Lösenord hash-synkronisering och säkerhetsaspekter
När du aktiverar Azure AD Domain Services, Azure AD-katalogen genererar och lagrar lösenordshasher i NTLM och Kerberos-kompatibelt format. 

För befintliga användarkonton för molnet, eftersom Azure AD lagrar aldrig sina lösenord i klartext, kan inte dessa värden skapas automatiskt. Microsoft kräver därför [molnanvändare till återställning/ändra sina lösenord](active-directory-ds-getting-started-password-sync.md) för sina lösenords-hash till skapas och lagras i Azure AD. För alla moln användarkonton som skapats i Azure AD när du har aktiverat Azure AD Domain Services, lösenords-hash genereras och lagras i de NTLM och Kerberos-kompatibelt format. 

För användarkonton som synkroniserats från lokala AD med hjälp av Azure AD Connect Sync, du behöver [konfigurera Azure AD Connect att synkronisera lösenordshash för NTLM och Kerberos-kompatibelt format](active-directory-ds-getting-started-password-sync-synced-tenant.md).

Hashvärden för NTLM- och Kerberos-kompatibel lösenord lagras alltid ett i Azure AD. Dessa hashvärden krypteras så att endast Azure AD Domain Services har åtkomst till dekrypteringsnycklar. Ingen andra tjänst eller en komponent i Azure AD har du åtkomst till dekrypteringsnycklar. Krypteringsnycklarna är unikt per Azure AD-klient. Azure AD Domain Services synkroniserar lösenords-hash till domänkontrollanterna för den hanterade domänen. Dessa hashvärden för lösenord lagras och skyddas på dessa domänkontrollanter som liknar hur lösenord lagras och skyddas på Windows Server AD-domänkontrollanter. Diskarna för dessa hanterade domänkontrollanter krypteras i vila.

## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>Objekt som inte synkroniseras till Azure AD-klienten från en hanterad domän
Enligt beskrivningen i föregående avsnitt i den här artikeln, finns det ingen synkronisering från en hanterad domän till Azure AD-klienten. Du kan välja att [skapa en anpassad organisationsenhet (OU)](active-directory-ds-admin-guide-create-ou.md) i din hanterade domän. Du kan dessutom skapa andra organisationsenheter, användare, grupper eller service-konton inom dessa anpassade organisationsenheter. Ingen av de objekt som skapats i anpassade organisationsenheter synkroniseras tillbaka till Azure AD-klienten. Dessa objekt är tillgängliga för att endast användas av din hanterade domän. Därför kan visas dessa objekt inte med hjälp av Azure AD PowerShell-cmdletar, Azure AD Graph API eller med hjälp av Användargränssnittet för Azure AD-hantering.

## <a name="related-content"></a>Relaterat innehåll
* [Funktioner – Azure AD Domain Services](active-directory-ds-features.md)
* [-Scenarier för Azure AD Domain Services](active-directory-ds-scenarios.md)
* [Nätverksöverväganden för Azure AD Domain Services](active-directory-ds-networking.md)
* [Kom igång med Azure AD Domain Services](active-directory-ds-getting-started.md)
