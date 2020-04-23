---
title: Attribut synkroniserade av Azure AD Connect | Microsoft-dokument
description: Visar en lista över de attribut som synkroniseras till Azure Active Directory.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c2bb36e0-5205-454c-b9b6-f4990bcedf51
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 04/15/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ab51030ad39e1360cabc7d63390af7c1654d2891
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082126"
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Azure AD Connect-synkronisering: Attribut synkroniserade till Azure Active Directory
I det här avsnittet visas de attribut som synkroniseras av Azure AD Connect-synkronisering.  
Attributen grupperas efter den relaterade Azure AD-appen.

## <a name="attributes-to-synchronize"></a>Attribut som ska synkroniseras
En vanlig fråga är *vad som är listan över minsta attribut för att synkronisera*. Standardmetoden och den rekommenderade metoden är att behålla standardattributen så att en fullständig GAL (global adresslista) kan konstrueras i molnet och hämta alla funktioner i Office 365-arbetsbelastningar. I vissa fall finns det vissa attribut som din organisation inte vill synkronisera med molnet eftersom dessa attribut innehåller känsliga data eller PII-data (personligt identifierbar information), till exempel i det här exemplet:  
![felaktiga attribut](./media/reference-connect-sync-attributes-synchronized/badextensionattribute.png)

I det här fallet börjar du med listan över attribut i det här avsnittet och identifierar de attribut som skulle innehålla känsliga data eller PII-data och som inte kan synkroniseras. Avmarkera sedan dessa attribut under installationen med [Azure AD-appen och attributfiltrering](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering).

> [!WARNING]
> När du avmarkerar attribut bör du vara försiktig och bara avmarkera de attribut som absolut inte är möjliga att synkronisera. Om du avmarkerar andra attribut kan det påverka funktionerna negativt.
>
>

## <a name="office-365-proplus"></a>Office 365 ProPlus
| Attributnamn | Användare | Kommentar |
| --- |:---:| --- |
| accountEnabled |X |Definierar om ett konto är aktiverat. |
| Cn |X | |
| displayName |X | |
| objekt-SID |X |mekanisk egendom. AD-användaridentifierare som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| pwdLastSet |X |mekanisk egendom. Används för att veta när redan utfärdade token ska ogiltigförklaras. Används av både lösenord hash-synkronisering, direktautentisering och federation. |
|samAccountName|X| |
| källaAnchor |X |mekanisk egendom. Oföränderlig identifierare för att upprätthålla relationen mellan ADD och Azure AD. |
| användningLokalisering |X |mekanisk egendom. Användarens land/region. Används för licenstilldelning. |
| userPrincipalName |X |UPN är användarens inloggnings-ID. Oftast samma som [mail] värde. |

## <a name="exchange-online"></a>exchange online
| Attributnamn | Användare | Kontakt | Grupp | Kommentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definierar om ett konto är aktiverat. |
| Assistent |X |X | | |
| altRecipient (altRecipient) |X | | |Kräver Azure AD Connect build 1.1.552.0 eller efter. |
| authOrig |X |X |X | |
| c |X |X | | |
| Cn |X | |X | |
| Co |X |X | | |
| company |X |X | | |
| countryCode (landsKod) |X |X | | |
| avdelning |X |X | | |
| description | | |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| förnamn |X |X | | |
| homePhone (hemTelefon) |X |X | | |
| information |X |X |X |Det här attributet förbrukas för närvarande inte för grupper. |
| Initialer |X |X | | |
| l |X |X | | |
| legacyExchangeDN |X |X |X | |
| mailNickname (mailNickname) |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| medlem | | |X | |
| mobil |X |X | | |
| msDS-HABSeniorityIndex |X |X |X | |
| msDS-PhoneticDisplayName |X |X |X | |
| msExchArchiveGUID |X | | | |
| msExchArchiveName |X | | | |
| msExchAssistantName |X |X | | |
| msExchAuditAdmin |X | | | |
| msExchAuditDelegate |X | | | |
| msExchAuditDelegateAdmin |X | | | |
| msExchAuditOwner |X | | | |
| msExchBlockedSendersHash |X |X | | |
| msExchBypassAudit |X | | | |
| msExchBypassModerationLink | | |X |Tillgänglig i Azure AD Connect version 1.1.524.0 |
| msExchCoManagedByLink | | |X | |
| msExchDelegateListLink |X | | | |
| msExchELCExpirySuspensionEnd |X | | | |
| msExchELCExpirySuspensionStart |X | | | |
| msExchELCMailboxFlags |X | | | |
| msExchEnableModeration |X | |X | |
| msExchExtensionCustomAttribute1 |X |X |X |Det här attributet förbrukas för närvarande inte av Exchange Online. |
| msExtensionCustomAttribute2 |X |X |X |Det här attributet förbrukas för närvarande inte av Exchange Online. |
| msExtensionCustomAttribute3 |X |X |X |Det här attributet förbrukas för närvarande inte av Exchange Online. |
| msExtensionCustomAttribute4 |X |X |X |Det här attributet förbrukas för närvarande inte av Exchange Online. |
| msExchExtensionCustomAttribute5 |X |X |X |Det här attributet förbrukas för närvarande inte av Exchange Online. |
| msExchHideFromAddressLists |X |X |X | |
| msExchImmutableID |X | | | |
| msExchLitigationHoldDate |X |X |X | |
| msExchLitigationHoldOwner |X |X |X | |
| msExchMailboxAuditEnable |X | | | |
| msExchMailboxAuditLogAgeLimit |X | | | |
| msExchMailboxGuid |X | | | |
| msExchModeratedByLink |X |X |X | |
| msExchModerationFlags |X |X |X | |
| msExchRecipientDisplayTyp |X |X |X | |
| msExchRecipientTypeDetails |X |X |X | |
| msExchRemoteRecipientType |X | | | |
| msExchRequireAuthToSendTo |X |X |X | |
| msExchResourceCapacity |X | | | |
| msExchResourceDisplay |X | | | |
| msExchResourceMetaData |X | | | |
| msExchResourceSearchProperties |X | | | |
| msExchRetentionComment |X |X |X | |
| msExchRetentionURL |X |X |X | |
| msExchSafeRecipientsHash |X |X | | |
| msExchSafeSendersHash |X |X | | |
| msExchSenderHintTranslationer |X |X |X | |
| msExchTeamMailboxUtpiration |X | | | |
| msExchTeamMailboxÄgare |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| msExchUserHoldPolicies |X | | | |
| msOrg-IsOrganizational | | |X | |
| objekt-SID |X | |X |mekanisk egendom. AD-användaridentifierare som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| oOFReplyToOriginator | | |X | |
| andra FaxTelefon |X |X | | |
| otherHomePhone (andraHomePhone) |X |X | | |
| annatTelefon |X |X | | |
| Pager |X |X | | |
| fysisktDeliveryOfficeName |X |X | | |
| Postnummer |X |X | | |
| proxyAddresses |X |X |X | |
| offentligaDelegater |X |X |X | |
| pwdLastSet |X | | |mekanisk egendom. Används för att veta när redan utfärdade token ska ogiltigförklaras. Används av både lösenordssynkronisering och federation. |
| rapportTillOriginator | | |X | |
| rapportTillÄgare | | |X | |
| sn |X |X | | |
| källaAnchor |X |X |X |mekanisk egendom. Oföränderlig identifierare för att upprätthålla relationen mellan ADD och Azure AD. |
| st |X |X | | |
| streetAddress (streetAddress) |X |X | | |
| Targetaddress |X |X | | |
| telefonAssistant |X |X | | |
| telefonAntal |X |X | | |
| thumbnailphoto thumbnailphoto thumbnailphoto thumbnailphoto |X |X | | |
| title |X |X | | |
| oauthOrig |X |X |X | |
| användningLokalisering |X | | |mekanisk egendom. Användarens land/region. Används för licenstilldelning. |
| userCertificate |X |X | | |
| userPrincipalName |X | | |UPN är användarens inloggnings-ID. Oftast samma som [mail] värde. |
| användareSMIMECertificates |X |X | | |
| wWWHomePage (andra) |X |X | | |

## <a name="sharepoint-online"></a>sharepoint online
| Attributnamn | Användare | Kontakt | Grupp | Kommentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definierar om ett konto är aktiverat. |
| authOrig |X |X |X | |
| c |X |X | | |
| Cn |X | |X | |
| Co |X |X | | |
| company |X |X | | |
| countryCode (landsKod) |X |X | | |
| avdelning |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| förnamn |X |X | | |
| döljDLMedlemskap | | |X | |
| hemtelefon |X |X | | |
| information |X |X |X | |
| Initialer |X |X | | |
| ipPhone (ipPhone) |X |X | | |
| l |X |X | | |
| e-post |X |X |X | |
| mailnicknamn |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| medlem | | |X | |
| middleName (mittNamn) |X |X | | |
| mobil |X |X | | |
| msExchTeamMailboxUtpiration |X | | | |
| msExchTeamMailboxÄgare |X | | | |
| msExchTeamMailboxSharePointLinkedBy |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| objekt-SID |X | |X |mekanisk egendom. AD-användaridentifierare som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| oOFReplyToOriginator | | |X | |
| andra FaxTelefon |X |X | | |
| otherHomePhone (andraHomePhone) |X |X | | |
| otherIpPhone |X |X | | |
| andraMobil |X |X | | |
| otherPager |X |X | | |
| annatTelefon |X |X | | |
| Pager |X |X | | |
| fysisktDeliveryOfficeName |X |X | | |
| Postnummer |X |X | | |
| postOfficeBox |X |X | |Det här attributet förbrukas för närvarande inte av SharePoint Online. |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mekanisk egendom. Används för att veta när redan utfärdade token ska ogiltigförklaras. Används av både lösenord hash-synkronisering, direktautentisering och federation. |
| rapportTillOriginator | | |X | |
| rapportTillÄgare | | |X | |
| sn |X |X | | |
| källaAnchor |X |X |X |mekanisk egendom. Oföränderlig identifierare för att upprätthålla relationen mellan ADD och Azure AD. |
| st |X |X | | |
| streetAddress (streetAddress) |X |X | | |
| Targetaddress |X |X | | |
| telefonAssistant |X |X | | |
| telefonAntal |X |X | | |
| thumbnailphoto thumbnailphoto thumbnailphoto thumbnailphoto |X |X | | |
| title |X |X | | |
| oauthOrig |X |X |X | |
| url |X |X | | |
| användningLokalisering |X | | |mekanisk egendom. Användarens land/region
. Används för licenstilldelning. |
| userPrincipalName |X | | |UPN är användarens inloggnings-ID. Oftast samma som [mail] värde. |
| wWWHomePage (andra) |X |X | | |

## <a name="teams-and-skype-for-business-online"></a>Team och Skype för företag – Online
| Attributnamn | Användare | Kontakt | Grupp | Kommentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definierar om ett konto är aktiverat. |
| c |X |X | | |
| Cn |X | |X | |
| Co |X |X | | |
| company |X |X | | |
| avdelning |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X |X | |
| förnamn |X |X | | |
| hemtelefon |X |X | | |
| ipPhone (ipPhone) |X |X | | |
| l |X |X | | |
| e-post |X |X |X | |
| mailNickname (mailNickname) |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| medlem | | |X | |
| mobil |X |X | | |
| msExchHideFromAddressLists |X |X |X | |
| msRTCSIP-ApplicationOptions |X | | | |
| msRTCSIP-DeploymentLocator |X |X | | |
| msRTCSIP-line |X |X | | |
| msRTCSIP-OptionFlags |X |X | | |
| msRTCSIP-OwnerUrn |X | | | |
| msRTCSIP-PrimaryUserAddress |X |X | | |
| msRTCSIP-UserEnabled |X |X | | |
| objekt-SID |X | |X |mekanisk egendom. AD-användaridentifierare som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| annatTelefon |X |X | | |
| fysisktDeliveryOfficeName |X |X | | |
| Postnummer |X |X | | |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mekanisk egendom. Används för att veta när redan utfärdade token ska ogiltigförklaras. Används av både lösenord hash-synkronisering, direktautentisering och federation. |
| sn |X |X | | |
| källaAnchor |X |X |X |mekanisk egendom. Oföränderlig identifierare för att upprätthålla relationen mellan ADD och Azure AD. |
| st |X |X | | |
| streetAddress (streetAddress) |X |X | | |
| telefonAntal |X |X | | |
| thumbnailphoto thumbnailphoto thumbnailphoto thumbnailphoto |X |X | | |
| title |X |X | | |
| användningLokalisering |X | | |mekanisk egendom. Användarens land/region. Används för licenstilldelning. |
| userPrincipalName |X | | |UPN är användarens inloggnings-ID. Oftast samma som [mail] värde. |
| wWWHomePage (andra) |X |X | | |

## <a name="azure-rms"></a>Azure RMS
| Attributnamn | Användare | Kontakt | Grupp | Kommentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definierar om ett konto är aktiverat. |
| Cn |X | |X |Gemensamt namn eller alias. Oftast prefixet med [mail]-värde. |
| displayName |X |X |X |En sträng som representerar namnet visas ofta som det egna namnet (förnamns efternamn). |
| e-post |X |X |X |fullständig e-postadress. |
| medlem | | |X | |
| objekt-SID |X | |X |mekanisk egendom. AD-användaridentifierare som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| proxyAddresses |X |X |X |mekanisk egendom. Används av Azure AD. Innehåller alla sekundära e-postadresser för användaren. |
| pwdLastSet |X | | |mekanisk egendom. Används för att veta när redan utfärdade token ska ogiltigförklaras. |
| källaAnchor |X |X |X |mekanisk egendom. Oföränderlig identifierare för att upprätthålla relationen mellan ADD och Azure AD. |
| användningLokalisering |X | | |mekanisk egendom. Användarens land/region. Används för licenstilldelning. |
| userPrincipalName |X | | |Detta UPN är inloggnings-ID för användaren. Oftast samma som [mail] värde. |

## <a name="intune"></a>Intune
| Attributnamn | Användare | Kontakt | Grupp | Kommentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definierar om ett konto är aktiverat. |
| c |X |X | | |
| Cn |X | |X | |
| description |X |X |X | |
| displayName |X |X |X | |
| e-post |X |X |X | |
| mailnicknamn |X |X |X | |
| medlem | | |X | |
| objekt-SID |X | |X |mekanisk egendom. AD-användaridentifierare som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mekanisk egendom. Används för att veta när redan utfärdade token ska ogiltigförklaras. Används av både lösenord hash-synkronisering, direktautentisering och federation. |
| källaAnchor |X |X |X |mekanisk egendom. Oföränderlig identifierare för att upprätthålla relationen mellan ADD och Azure AD. |
| användningLokalisering |X | | |mekanisk egendom. Användarens land/region. Används för licenstilldelning. |
| userPrincipalName |X | | |UPN är användarens inloggnings-ID. Oftast samma som [mail] värde. |

## <a name="dynamics-crm"></a>Dynamics CRM
| Attributnamn | Användare | Kontakt | Grupp | Kommentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definierar om ett konto är aktiverat. |
| c |X |X | | |
| Cn |X | |X | |
| Co |X |X | | |
| company |X |X | | |
| countryCode (landsKod) |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| förnamn |X |X | | |
| l |X |X | | |
| managedBy | | |X | |
| manager |X |X | | |
| medlem | | |X | |
| mobil |X |X | | |
| objekt-SID |X | |X |mekanisk egendom. AD-användaridentifierare som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| fysisktDeliveryOfficeName |X |X | | |
| Postnummer |X |X | | |
| preferredLanguage |X | | | |
| pwdLastSet |X | | |mekanisk egendom. Används för att veta när redan utfärdade token ska ogiltigförklaras. Används av både lösenord hash-synkronisering, direktautentisering och federation. |
| sn |X |X | | |
| källaAnchor |X |X |X |mekanisk egendom. Oföränderlig identifierare för att upprätthålla relationen mellan ADD och Azure AD. |
| st |X |X | | |
| streetAddress (streetAddress) |X |X | | |
| telefonAntal |X |X | | |
| title |X |X | | |
| användningLokalisering |X | | |mekanisk egendom. Användarens land/region. Används för licenstilldelning. |
| userPrincipalName |X | | |UPN är användarens inloggnings-ID. Oftast samma som [mail] värde. |

## <a name="3rd-party-applications"></a>Program från tredje part
Den här gruppen är en uppsättning attribut som används som minimala attribut som behövs för en allmän arbetsbelastning eller ett allmänt program. Den kan användas för en arbetsbelastning som inte finns med i ett annat avsnitt eller för en app som inte kommer från Microsoft. Det används uttryckligen för följande:

* Yammer (endast användare förbrukas)
* [Hybrid Business-to-Business (B2B) kors-org samarbete scenarier som erbjuds av resurser som SharePoint](https://go.microsoft.com/fwlink/?LinkId=747036)

Den här gruppen är en uppsättning attribut som kan användas om Azure AD-katalogen inte används för att stödja Office 365, Dynamics eller Intune. Den har en liten uppsättning kärnattribut. Observera att enkel inloggning eller etablering till vissa program från tredje part kräver att synkronisering av attribut konfigureras utöver de attribut som beskrivs här. Programkrav beskrivs i [saas-apphandledningen](../saas-apps/tutorial-list.md) för varje program.

| Attributnamn | Användare | Kontakt | Grupp | Kommentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Definierar om ett konto är aktiverat. |
| Cn |X | |X | |
| displayName |X |X |X | |
| anställnings-ID |X |  |  | |
| förnamn |X |X | | |
| e-post |X | |X | |
| managedBy | | |X | |
| smeknamn för e-post |X |X |X | |
| medlem | | |X | |
| objekt-SID |X | | |mekanisk egendom. AD-användaridentifierare som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mekanisk egendom. Används för att veta när redan utfärdade token ska ogiltigförklaras. Används av både lösenord hash-synkronisering, direktautentisering och federation. |
| sn |X |X | | |
| källaAnchor |X |X |X |mekanisk egendom. Oföränderlig identifierare för att upprätthålla relationen mellan ADD och Azure AD. |
| användningLokalisering |X | | |mekanisk egendom. Användarens land/region. Används för licenstilldelning. |
| userPrincipalName |X | | |UPN är användarens inloggnings-ID. Oftast samma som [mail] värde. |

## <a name="windows-10"></a>Windows 10
En Windows 10-domänansluten dator(enhet) synkroniserar vissa attribut till Azure AD. Mer information om scenarierna finns i [Ansluta domänanslutna enheter till Azure AD för Windows 10-upplevelser](../active-directory-azureadjoin-devices-group-policy.md). Dessa attribut synkroniseras alltid och Windows 10 visas inte som en app som du kan avmarkera. En Windows 10-domänansluten dator identifieras genom att attributet userCertificate fylls i.

| Attributnamn | Enhet | Kommentar |
| --- |:---:| --- |
| accountEnabled |X | |
| deviceTrustType |X |Hårdkodat värde för domänanslutna datorer. |
| displayName |X | |
| ms-DS-CreatorSID |X |Kallas även registeredOwnerReference. |
| objectGUID |X |Kallas även deviceID. |
| objekt-SID |X |Kallas ocksåPremisesSecurityIdentifier. |
| operatingSystem |X |Kallas även deviceOSType. |
| driftSystemVersion |X |Kallas även deviceOSVersion. |
| userCertificate |X | |

Dessa attribut för **användare** är utöver de andra appar som du har valt.  

| Attributnamn | Användare | Kommentar |
| --- |:---:| --- |
| domänFQDN |X |Kallas även dnsDomainName. Till exempel contoso.com. |
| domänNetBios |X |Kallas även netBiosName. Till exempel CONTOSO. |
| msDS-KeyCredentialLink |X |När användaren har registrerat sig i Windows Hello for Business. | 

## <a name="exchange-hybrid-writeback"></a>Tillbakaskrivning av hybrid- utbyte
Dessa attribut skrivs tillbaka från Azure AD till lokal Active Directory när du väljer att aktivera **Exchange hybrid**. Beroende på din Exchange-version kan färre attribut synkroniseras.

| Attributnamn (lokalt AD) | Attributnamn (anslut användargränssnitt) | Användare | Kontakt | Grupp | Kommentar |
| --- |:---:|:---:|:---:| --- |---|
| msds-externtRiktorobjektID| ms-DS-extern-katalog-objekt-Id |X | | |Härleds från cloudAnchor i Azure AD. Det här attributet är nytt i Exchange 2016 och Windows Server 2016 AD. |
| msExchArchiveStatus| ms-Exch-ArkivStatus |X | | |Online arkiv: Gör det möjligt för kunder att arkivera e-post. |
| msExchBlockedSendersHash| ms-Exch-BlockeradeSendersHash |X | | |Filtrering: Skriver tillbaka lokalt filtrering och online säkra och blockerade avsändande data från klienter. |
| msExchSafeRecipientsHash| ms-Exch-SafeRecipientsHash  |X | | |Filtrering: Skriver tillbaka lokalt filtrering och online säkra och blockerade avsändande data från klienter. |
| msExchSafeSendersHash| ms-Exch-SafeSendersHash  |X | | |Filtrering: Skriver tillbaka lokalt filtrering och online säkra och blockerade avsändande data från klienter. |
| msExchUCVoiceMailSettings| ms-Exch-UCVoiceMailSettings |X | | |Aktivera UM (Unified Messaging) – Onlineröstpost: Används av Microsoft Lync Server-integrering för att indikera för Lync Server lokalt att användaren har röstbrevlåda i onlinetjänster. |
| msExchUserHoldPolicies| ms-Exch-UserHoldPolicies |X | | |Bevarande av juridiska skäl: Gör det möjligt för molntjänster att avgöra vilka användare som befinner sig under Bevarande av juridiska skäl. |
| proxyAddresses| proxyAddresses |X |X |X |Endast x500-adressen från Exchange Online infogas. |
| offentligaDelegater| ms-Exch-offentliga-delegater  |X | | |Gör att en Exchange Online-postlåda kan beviljas SendOnBehalfTo-rättigheter till användare med lokal Exchange-postlåda. Kräver Azure AD Connect build 1.1.552.0 eller efter. |

## <a name="exchange-mail-public-folder"></a>Gemensam mapp för Exchange Mail
Dessa attribut synkroniseras från lokal Active Directory till Azure AD när du väljer att aktivera **Exchange Mail Public Folder**.

| Attributnamn | Offentliga Gånger | Kommentar |
| --- | :---:| --- |
| displayName | X |  |
| e-post | X |  |
| msExchRecipientTypeDetails | X |  |
| objectGUID | X |  |
| proxyAddresses | X |  |
| Targetaddress | X |  |

## <a name="device-writeback"></a>Tillbakaskrivning av enheter
Enhetsobjekt skapas i Active Directory. Dessa objekt kan vara enheter som är anslutna till Azure AD eller domänanslutna Windows 10-datorer.

| Attributnamn | Enhet | Kommentar |
| --- |:---:| --- |
| altSecurityId-enheter |X | |
| displayName |X | |
| dn |X | |
| msDS-CloudAnchor |X | |
| msDS-DeviceID |X | |
| msDS-DeviceObjectVersion |X | |
| msDS-DeviceOSType |X | |
| msDS-DeviceOSVersion |X | |
| msDS-DeviceFysiska ID:er |X | |
| msDS-KeyCredentialLink |X |Endast med Windows Server 2016 AD-schema |
| msDS-IsCompliant |X | |
| msDS-ÄrEnabled |X | |
| msDS-IsManaged |X | |
| msds-registrerad ägare |X | |

## <a name="notes"></a>Anteckningar
* När du använder ett alternativt ID synkroniseras det lokala attributet userPrincipalName med Azure AD-attributet onPremisesUserPrincipalName. Attributet Alternate ID, till exempel e-post, synkroniseras med Azure AD-attributet userPrincipalName.
* I listorna ovan gäller objekttypen **Användaren** även för objekttypen **iNetOrgPerson**.

## <a name="next-steps"></a>Nästa steg
Läs mer om synkroniseringskonfigurationen för [Azure AD Connect.](how-to-connect-sync-whatis.md)

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
