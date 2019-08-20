---
title: 'Azure Active Directory Domain Services: Synkronisering i hanterade domäner | Microsoft Docs'
description: Förstå synkronisering i en Azure Active Directory Domain Services hanterad domän
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 1c52ac967d241f31d96988fa5ead8b4e049f6f4c
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617107"
---
# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Synkronisering i en Azure AD Domain Services hanterad domän
Följande diagram illustrerar hur synkronisering fungerar i Azure AD Domain Services hanterade domäner.

![Synkronisering i Azure AD Domain Services](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>Synkronisering från din lokala katalog till Azure AD-klienten
Azure AD Connect Sync används för att synkronisera användar konton, grupp medlemskap och hash-värden för autentisering till din Azure AD-klient. Attribut för användar konton som UPN och lokalt SID (säkerhets identifierare) är synkroniserade. Om du använder Azure AD Domain Services synkroniseras äldre autentiseringsuppgifter för autentisering som krävs för NTLM och Kerberos-autentisering även till din Azure AD-klient.

Om du konfigurerar tillbakaskrivning synkroniseras ändringar som inträffar i din Azure AD-katalog tillbaka till din lokala Active Directory. Om du till exempel ändrar ditt lösen ord med hjälp av lösen ords hantering i Azure AD, uppdateras det ändrade lösen ordet i den lokala AD-domänen.

> [!NOTE]
> Använd alltid den senaste versionen av Azure AD Connect för att se till att du har korrigeringar för alla kända buggar.
>
>

## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Synkronisering från din Azure AD-klient till din hanterade domän
Användar konton, grupp medlemskap och inloggnings-hashvärden synkroniseras från din Azure AD-klient till din Azure AD Domain Services hanterade domän. Den här synkroniseringsprocessen är automatisk. Du behöver inte konfigurera, övervaka eller hantera den här synkroniseringsprocessen. Den första synkroniseringen kan ta från några timmar till några dagar, beroende på antalet objekt i Azure AD-katalogen. När den första synkroniseringen är klar tar det cirka 20-30 minuter innan ändringar som görs i Azure AD uppdateras i den hanterade domänen. Det här synkroniseringsfrekvensen gäller för lösen ords ändringar eller ändringar i attribut som görs i Azure AD.

Synkroniseringsprocessen är också enkelriktad/enkelriktad. Din hanterade domän är i stort sett skrivskyddad, förutom för anpassade organisationsenheter som du skapar. Därför kan du inte göra ändringar i användarattribut, användar lösen ord eller grupp medlemskap i den hanterade domänen. Därför finns det ingen omvänd synkronisering av ändringar från din hanterade domän tillbaka till din Azure AD-klient.

## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Synkronisering från en lokal miljö med flera skogar
Många organisationer har en ganska komplex lokal identitets infrastruktur som består av flera konto skogar. Azure AD Connect stöder synkronisering av användare, grupper och inloggnings-hashar från miljöer med flera skogar till din Azure AD-klient.

Din Azure AD-klient är däremot ett mycket enklare och ett platt namn område. Om du vill ge användare tillförlitlig åtkomst till program som skyddas av Azure AD löser du UPN-konflikter mellan användar konton i olika skogar. Din Azure AD Domain Services hanterade domän är nära samma som din Azure AD-klient. Du ser en platt ORGANISATIONSENHETs struktur i din hanterade domän. Alla användar konton och grupper lagras i behållaren "AADDC Users", trots att de synkroniseras från olika lokala domäner eller skogar. Du kan ha konfigurerat en hierarkisk OU-struktur lokalt. Din hanterade domän har fortfarande en enkel, platt OU-struktur.

## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>Undantag – vad är inte synkroniserat med din hanterade domän
Följande objekt eller attribut synkroniseras inte till din Azure AD-klient eller till din hanterade domän:

* **Exkluderade attribut:** Du kan välja att undanta vissa attribut från synkronisering till din Azure AD-klient från din lokala domän med hjälp av Azure AD Connect. De uteslutna attributen är inte tillgängliga i den hanterade domänen.
* **Grup principer:** Grup principer som kon figurer ATS i din lokala domän synkroniseras inte med din hanterade domän.
* **SYSVOL-resurs:** På samma sätt synkroniseras inte innehållet i Sysvol-resursen på din lokala domän till din hanterade domän.
* **Dator objekt:** Dator objekt för datorer som är anslutna till din lokala domän synkroniseras inte med din hanterade domän. De här datorerna har ingen förtroende relation med din hanterade domän och tillhör bara din lokala domän. I din hanterade domän kan du bara hitta dator objekt för datorer som du uttryckligen har domänanslutna till den hanterade domänen.
* **SidHistory-attribut för användare och grupper:** Primär användare och primär grupp sid från din lokala domän synkroniseras till din hanterade domän. Befintliga SidHistory-attribut för användare och grupper synkroniseras dock inte från den lokala domänen till din hanterade domän.
* **Organisations enheter (OU):** Organisationsenheter som definieras i din lokala domän synkroniseras inte med din hanterade domän. Det finns två inbyggda organisationsenheter i din hanterade domän. Som standard har din hanterade domän en strukturerad OU-struktur. Du kan dock välja att [skapa en anpassad organisationsenhet i din hanterade domän](create-ou.md).

## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>Hur vissa attribut synkroniseras till din hanterade domän
I följande tabell visas några vanliga attribut och en beskrivning av hur de synkroniseras till din hanterade domän.

| Attribut i din hanterade domän | Source | Anteckningar |
|:--- |:--- |:--- |
| UPN |Användarens UPN-attribut i Azure AD-klienten |UPN-attributet från din Azure AD-klient synkroniseras som det är till din hanterade domän. Det mest pålitliga sättet att logga in på din hanterade domän är därför att använda ditt UPN. |
| SAMAccountName |Användarens smek namn-attribut i din Azure AD-klient eller automatiskt genererad |Attributet SAMAccountName hämtas från attributet smek namn i din Azure AD-klient. Om flera användar konton har samma startalias-attribut genereras SAMAccountName automatiskt. Om användarens smek namn eller UPN-prefix är längre än 20 tecken genereras SAMAccountName automatiskt för att uppfylla gränsen på 20 tecken i SAMAccountName-attribut. |
| Lösenord |Användarens lösen ord från din Azure AD-klient |Hash-värden för autentiseringsuppgifter som krävs för NTLM-eller Kerberos-autentisering (kallas även kompletterande autentiseringsuppgifter) synkroniseras från din Azure AD-klient. Om din Azure AD-klient är en synkroniserad klient, kommer dessa autentiseringsuppgifter att kopieras från din lokala domän. |
| Primär användare/grupp-SID |Automatiskt genererad |Huvud-SID för användare/grupp-konton genereras automatiskt i din hanterade domän. Det här attributet matchar inte det primära användare/grupp-SID för objektet i din lokala AD-domän. Detta matchnings fel beror på att den hanterade domänen har ett annat SID-namnområde än din lokala domän. |
| SID-historik för användare och grupper |Lokal primär användare och grupp-SID |Attributet SidHistory för användare och grupper i din hanterade domän är inställda på att matcha motsvarande primära användare eller grupp-SID i din lokala domän. Den här funktionen gör det enklare att lyfta och byta från lokala program till den hanterade domänen eftersom du inte behöver göra en ny ACL-resurs. |

> [!NOTE]
> **Logga in på den hanterade domänen med UPN-formatet:** Attributet SAMAccountName kan genereras automatiskt för vissa användar konton i din hanterade domän. Om flera användare har samma smek namn-attribut eller om användarna har över långa UPN-prefix, kan SAMAccountName för dessa användare genereras automatiskt. Därför är SAMAccountName-formatet (till exempel "CONTOSO\dee") inte alltid ett tillförlitligt sätt att logga in på domänen. Användarens automatiskt genererade SAMAccountName kan skilja sig från sina UPN-prefix. Använd UPN-formatet (till exempeldee@contoso.com) för att logga in på den hanterade domänen på ett tillförlitligt sätt.

### <a name="attribute-mapping-for-user-accounts"></a>Mappning av attribut för användar konton
I följande tabell visas hur särskilda attribut för användar objekt i din Azure AD-klient synkroniseras med motsvarande attribut i din hanterade domän.

| Användarattribut i din Azure AD-klient | Användarattribut i din hanterade domän |
|:--- |:--- |
| accountEnabled |userAccountControl (anger eller tar bort ACCOUNT_DISABLED-biten) |
| city |L |
| ursprungslandet |CO |
| Avdelning |Avdelning |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| jobTitle |rubrik |
| e-post |e-post |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (kan ibland genereras automatiskt) |
| mobila |mobila |
| objectID |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl (anger eller tar bort DONT_EXPIRE_PASSWORD-biten) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| Postnummer |Postnummer |
| preferredLanguage |preferredLanguage |
| state |St |
| streetAddress |streetAddress |
| Efternamn |SN |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Attributmappning för grupper
I följande tabell visas hur särskilda attribut för grupp objekt i din Azure AD-klient synkroniseras med motsvarande attribut i din hanterade domän.

| Gruppattribut i Azure AD-klienten | Gruppattribut i din hanterade domän |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (kan ibland genereras automatiskt) |
| e-post |e-post |
| mailNickname |msDS-AzureADMailNickname |
| objectID |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| securityEnabled |groupType |

## <a name="password-hash-synchronization-and-security-considerations"></a>Lösenordssynkronisering och säkerhets aspekter
När du aktiverar Azure AD Domain Services genererar Azure AD-katalogen och lagrar lösen ords-hashar i NTLM & Kerberos-kompatibla format. 

För befintliga moln användar konton, eftersom Azure AD aldrig lagrar sina lösen ord i klartext, kan dessa hash-värden inte genereras automatiskt. Därför kräver Microsoft att [Cloud-Users återställer/ändrar sina lösen ord](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) för att de ska kunna genereras och lagras i Azure AD. För alla moln användar konton som skapats i Azure AD när du har aktiverat Azure AD Domain Services genereras lösen ords hasharna och lagras i de NTLM-och Kerberos-kompatibla formaten. 

För användar konton som har synkroniserats från lokala AD med hjälp av Azure AD Connect Sync måste du [konfigurera Azure AD Connect för att synkronisera lösen ords-hashar i de NTLM-och Kerberos-kompatibla formaten](active-directory-ds-getting-started-password-sync-synced-tenant.md).

Hashar för NTLM-och Kerberos-kompatibla lösen ord lagras alltid på ett krypterat sätt i Azure AD. Dessa hashar krypteras så att endast Azure AD Domain Services har åtkomst till krypterings nycklarna. Ingen annan tjänst eller komponent i Azure AD har åtkomst till krypterings nycklarna. Krypterings nycklarna är unika per Azure AD-klient. Azure AD Domain Services synkroniserar lösen ordets hash-värden i domän kontrol Lanterna för din hanterade domän. Dessa lösen ords-hashar lagras och skyddas på dessa domänkontrollanter på liknande sätt som lösen ord lagras och skyddas på Windows Server AD-domänkontrollanter. Diskarna för de här hanterade domän kontrol Lanterna är krypterade i vila.

## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>Objekt som inte synkroniseras med din Azure AD-klient från din hanterade domän
Enligt beskrivningen i föregående avsnitt i den här artikeln finns det ingen synkronisering från din hanterade domän tillbaka till din Azure AD-klient. Du kan välja att [skapa en anpassad organisationsenhet (OU)](create-ou.md) i den hanterade domänen. Dessutom kan du skapa andra organisationsenheter, användare, grupper eller tjänst konton inom dessa anpassade organisationsenheter. Inget av de objekt som har skapats i anpassade organisationsenheter synkroniseras tillbaka till din Azure AD-klient. Dessa objekt är bara tillgängliga för användning i din hanterade domän. Därför visas inte dessa objekt med Azure AD PowerShell-cmdlets, Azure AD Graph API eller med Azure AD Management-ANVÄNDARGRÄNSSNITTET.

## <a name="related-content"></a>Relaterat innehåll
* [Distributions scenarier – Azure AD Domain Services](scenarios.md)
* [Nätverks överväganden för Azure AD Domain Services](network-considerations.md)
* [Kom igång med Azure AD Domain Services](tutorial-create-instance.md)
