---
title: Attribut som synkroniseras med Azure AD Connect | Microsoft Docs
description: Visar en lista över de attribut som synkroniseras till Azure Active Directory.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: c2bb36e0-5205-454c-b9b6-f4990bcedf51
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2018
ms.author: billmath
ms.openlocfilehash: 506206d3e0a28f2ffc0e9e029ec27546bc332754
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Azure AD Connect-synkronisering: attribut synkroniserade till Azure Active Directory
Det här avsnittet listar de attribut som synkroniseras av Azure AD Connect-synkronisering.  
Attribut som är grupperade efter relaterade Azure AD-app.

## <a name="attributes-to-synchronize"></a>Attribut som ska synkroniseras
En vanlig fråga är *vad är en lista över minsta attribut som ska synkroniseras*. Standardwebbplatsen och den rekommenderade metoden är att behålla standardattributen så att en fullständig GAL (globala adresslistan) kan konstrueras i molnet och för att hämta alla funktioner i Office 365-arbetsbelastningar. I vissa fall kan det finns vissa attribut som organisationen inte vill synkroniserade till molnet eftersom dessa attribut innehåller känslig eller personligt identifierbar information (personligt identifierbar information) data, precis som i det här exemplet:  
![felaktigt attribut](./media/active-directory-aadconnectsync-attributes-synchronized/badextensionattribute.png)

I så fall måste börja med en lista med attribut i det här avsnittet och identifiera de attribut som innehåller känslig eller personligt identifierbar information data och kan inte synkroniseras. Sedan avmarkera dessa attribut under installation med hjälp av [Azure AD-app och attributfiltrering](active-directory-aadconnect-get-started-custom.md#azure-ad-app-and-attribute-filtering).

> [!WARNING]
> När avmarkera attribut bör du vara försiktig och bara avmarkera de attribut som inte är möjligt att synkronisera. Unselecting andra attribut kan ha negativ inverkan på funktioner.
>
>

## <a name="office-365-proplus"></a>Office 365 ProPlus
| Attributnamn | Användare | Kommentar |
| --- |:---:| --- |
| accountEnabled |X |Anger om ett konto har aktiverats. |
| CN |X | |
| Visningsnamn |X | |
| objectSID |X |mekanisk egenskap. AD användar-ID som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| pwdLastSet |X |mekanisk egenskap. Används för att veta när ogiltigförklara redan utfärdade token. Används av lösenordshashsynkronisering, direktautentisering och federation. |
| sourceAnchor |X |mekanisk egenskap. Oåterkalleliga identifierare att underhålla relationen mellan ADDS och Azure AD. |
| usageLocation |X |mekanisk egenskap. Användarens land. Används för licenstilldelning. |
| userPrincipalName |X |UPN är inloggnings-ID för användaren. Oftast det samma som [e] värdet. |

## <a name="exchange-online"></a>exchange online
| Attributnamn | Användare | Kontakt | Grupp | Kommentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Anger om ett konto har aktiverats. |
| Installationsassistenten |X |X | | |
| altRecipient |X | | |Kräver Azure AD Connect build 1.1.552.0 eller efter. |
| authOrig |X |X |X | |
| c |X |X | | |
| CN |X | |X | |
| CO |X |X | | |
| Företag |X |X | | |
| CountryCode |X |X | | |
| Avdelning |X |X | | |
| description |X |X |X | |
| Visningsnamn |X |X |X | |
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
| givenName |X |X | | |
| HomePhone |X |X | | |
| Info |X |X |X |Det här attributet används för närvarande inte för grupper. |
| initialer |X |X | | |
| L |X |X | | |
| legacyExchangeDN |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| Manager |X |X | | |
| Medlem | | |X | |
| mobila |X |X | | |
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
| msExchBypassModerationLink | | |X |Tillgängligt i Azure AD Connect version 1.1.524.0 |
| msExchCoManagedByLink | | |X | |
| msExchDelegateListLink |X | | | |
| msExchELCExpirySuspensionEnd |X | | | |
| msExchELCExpirySuspensionStart |X | | | |
| msExchELCMailboxFlags |X | | | |
| msExchEnableModeration |X | |X | |
| msExchExtensionCustomAttribute1 |X |X |X |Det här attributet används för närvarande inte av Exchange Online. |
| msExchExtensionCustomAttribute2 |X |X |X |Det här attributet används för närvarande inte av Exchange Online. |
| msExchExtensionCustomAttribute3 |X |X |X |Det här attributet används för närvarande inte av Exchange Online. |
| msExchExtensionCustomAttribute4 |X |X |X |Det här attributet används för närvarande inte av Exchange Online. |
| msExchExtensionCustomAttribute5 |X |X |X |Det här attributet används för närvarande inte av Exchange Online. |
| msExchHideFromAddressLists |X |X |X | |
| msExchImmutableID |X | | | |
| msExchLitigationHoldDate |X |X |X | |
| msExchLitigationHoldOwner |X |X |X | |
| msExchMailboxAuditEnable |X | | | |
| msExchMailboxAuditLogAgeLimit |X | | | |
| msExchMailboxGuid |X | | | |
| msExchModeratedByLink |X |X |X | |
| msExchModerationFlags |X |X |X | |
| msExchRecipientDisplayType |X |X |X | |
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
| msExchSenderHintTranslations |X |X |X | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| msExchUserHoldPolicies |X | | | |
| msOrg IsOrganizational | | |X | |
| objectSID |X | |X |mekanisk egenskap. AD användar-ID som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherTelephone |X |X | | |
| Personsökare |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| Postnummer |X |X | | |
| proxyAddresses |X |X |X | |
| publicDelegates |X |X |X | |
| pwdLastSet |X | | |mekanisk egenskap. Används för att veta när ogiltigförklara redan utfärdade token. Används av både Lösenordssynkronisering och federation. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| securityEnabled | | |X |Härleds från groupType |
| SN |X |X | | |
| sourceAnchor |X |X |X |mekanisk egenskap. Oåterkalleliga identifierare att underhålla relationen mellan ADDS och Azure AD. |
| St |X |X | | |
| StreetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| rubrik |X |X | | |
| unauthOrig |X |X |X | |
| usageLocation |X | | |mekanisk egenskap. Användarens land. Används för licenstilldelning. |
| userCertificate |X |X | | |
| userPrincipalName |X | | |UPN är inloggnings-ID för användaren. Oftast det samma som [e] värdet. |
| userSMIMECertificates |X |X | | |
| wWWHomePage |X |X | | |

## <a name="sharepoint-online"></a>sharepoint online
| Attributnamn | Användare | Kontakt | Grupp | Kommentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Anger om ett konto har aktiverats. |
| authOrig |X |X |X | |
| c |X |X | | |
| CN |X | |X | |
| CO |X |X | | |
| Företag |X |X | | |
| CountryCode |X |X | | |
| Avdelning |X |X | | |
| description |X |X |X | |
| Visningsnamn |X |X |X | |
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
| givenName |X |X | | |
| hideDLMembership | | |X | |
| homephone |X |X | | |
| Info |X |X |X | |
| initialer |X |X | | |
| ipPhone |X |X | | |
| L |X |X | | |
| E-post |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| Manager |X |X | | |
| Medlem | | |X | |
| middleName |X |X | | |
| mobila |X |X | | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointLinkedBy |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| objectSID |X | |X |mekanisk egenskap. AD användar-ID som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherIpPhone |X |X | | |
| otherMobile |X |X | | |
| otherPager |X |X | | |
| otherTelephone |X |X | | |
| Personsökare |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| Postnummer |X |X | | |
| postOfficeBox |X |X | |Det här attributet är för närvarande inte används av SharePoint Online. |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mekanisk egenskap. Används för att veta när ogiltigförklara redan utfärdade token. Används av lösenordshashsynkronisering, direktautentisering och federation. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| securityEnabled | | |X |Härleds från groupType |
| SN |X |X | | |
| sourceAnchor |X |X |X |mekanisk egenskap. Oåterkalleliga identifierare att underhålla relationen mellan ADDS och Azure AD. |
| St |X |X | | |
| StreetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| rubrik |X |X | | |
| unauthOrig |X |X |X | |
| url |X |X | | |
| usageLocation |X | | |mekanisk egenskap. Användarens land. Används för licenstilldelning. |
| userPrincipalName |X | | |UPN är inloggnings-ID för användaren. Oftast det samma som [e] värdet. |
| wWWHomePage |X |X | | |

## <a name="lync-online-subsequently-known-as-skype-for-business"></a>Lync Online (nu kallat Skype för företag)
| Attributnamn | Användare | Kontakt | Grupp | Kommentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Anger om ett konto har aktiverats. |
| c |X |X | | |
| CN |X | |X | |
| CO |X |X | | |
| Företag |X |X | | |
| Avdelning |X |X | | |
| description |X |X |X | |
| Visningsnamn |X |X |X | |
| facsimiletelephonenumber |X |X |X | |
| givenName |X |X | | |
| homephone |X |X | | |
| ipPhone |X |X | | |
| L |X |X | | |
| E-post |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| Manager |X |X | | |
| Medlem | | |X | |
| mobila |X |X | | |
| msExchHideFromAddressLists |X |X |X | |
| msRTCSIP-ApplicationOptions |X | | | |
| msRTCSIP-DeploymentLocator |X |X | | |
| msRTCSIP-Line |X |X | | |
| msRTCSIP-OptionFlags |X |X | | |
| msRTCSIP-OwnerUrn |X | | | |
| msRTCSIP-PrimaryUserAddress |X |X | | |
| msRTCSIP-UserEnabled |X |X | | |
| objectSID |X | |X |mekanisk egenskap. AD användar-ID som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| otherTelephone |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| Postnummer |X |X | | |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mekanisk egenskap. Används för att veta när ogiltigförklara redan utfärdade token. Används av lösenordshashsynkronisering, direktautentisering och federation. |
| securityEnabled | | |X |Härleds från groupType |
| SN |X |X | | |
| sourceAnchor |X |X |X |mekanisk egenskap. Oåterkalleliga identifierare att underhålla relationen mellan ADDS och Azure AD. |
| St |X |X | | |
| StreetAddress |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| rubrik |X |X | | |
| usageLocation |X | | |mekanisk egenskap. Användarens land. Används för licenstilldelning. |
| userPrincipalName |X | | |UPN är inloggnings-ID för användaren. Oftast det samma som [e] värdet. |
| wWWHomePage |X |X | | |

## <a name="azure-rms"></a>Azure RMS
| Attributnamn | Användare | Kontakt | Grupp | Kommentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Anger om ett konto har aktiverats. |
| CN |X | |X |Namn eller alias. Oftast prefix för [e] värde. |
| Visningsnamn |X |X |X |En sträng som representerar namnet visas ofta som eget namn (Förnamn Efternamn). |
| E-post |X |X |X |fullständig e-postadress. |
| Medlem | | |X | |
| objectSID |X | |X |mekanisk egenskap. AD användar-ID som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| proxyAddresses |X |X |X |mekanisk egenskap. Används av Azure AD. Innehåller alla sekundära e-postadresser för användaren. |
| pwdLastSet |X | | |mekanisk egenskap. Används för att veta när ogiltigförklara redan utfärdade token. |
| securityEnabled | | |X |Härleds från groupType. |
| sourceAnchor |X |X |X |mekanisk egenskap. Oåterkalleliga identifierare att underhålla relationen mellan ADDS och Azure AD. |
| usageLocation |X | | |mekanisk egenskap. Användarens land. Används för licenstilldelning. |
| userPrincipalName |X | | |Den här UPN är inloggnings-ID för användaren. Oftast det samma som [e] värdet. |

## <a name="intune"></a>Intune
| Attributnamn | Användare | Kontakt | Grupp | Kommentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Anger om ett konto har aktiverats. |
| c |X |X | | |
| CN |X | |X | |
| description |X |X |X | |
| Visningsnamn |X |X |X | |
| E-post |X |X |X | |
| mailNickname |X |X |X | |
| Medlem | | |X | |
| objectSID |X | |X |mekanisk egenskap. AD användar-ID som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mekanisk egenskap. Används för att veta när ogiltigförklara redan utfärdade token. Används av lösenordshashsynkronisering, direktautentisering och federation. |
| securityEnabled | | |X |Härleds från groupType |
| sourceAnchor |X |X |X |mekanisk egenskap. Oåterkalleliga identifierare att underhålla relationen mellan ADDS och Azure AD. |
| usageLocation |X | | |mekanisk egenskap. Användarens land. Används för licenstilldelning. |
| userPrincipalName |X | | |UPN är inloggnings-ID för användaren. Oftast det samma som [e] värdet. |

## <a name="dynamics-crm"></a>Dynamics CRM
| Attributnamn | Användare | Kontakt | Grupp | Kommentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Anger om ett konto har aktiverats. |
| c |X |X | | |
| CN |X | |X | |
| CO |X |X | | |
| Företag |X |X | | |
| CountryCode |X |X | | |
| description |X |X |X | |
| Visningsnamn |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| L |X |X | | |
| managedBy | | |X | |
| Manager |X |X | | |
| Medlem | | |X | |
| mobila |X |X | | |
| objectSID |X | |X |mekanisk egenskap. AD användar-ID som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| physicalDeliveryOfficeName |X |X | | |
| Postnummer |X |X | | |
| preferredLanguage |X | | | |
| pwdLastSet |X | | |mekanisk egenskap. Används för att veta när ogiltigförklara redan utfärdade token. Används av lösenordshashsynkronisering, direktautentisering och federation. |
| securityEnabled | | |X |Härleds från groupType |
| SN |X |X | | |
| sourceAnchor |X |X |X |mekanisk egenskap. Oåterkalleliga identifierare att underhålla relationen mellan ADDS och Azure AD. |
| St |X |X | | |
| StreetAddress |X |X | | |
| telephoneNumber |X |X | | |
| rubrik |X |X | | |
| usageLocation |X | | |mekanisk egenskap. Användarens land. Används för licenstilldelning. |
| userPrincipalName |X | | |UPN är inloggnings-ID för användaren. Oftast det samma som [e] värdet. |

## <a name="3rd-party-applications"></a>3 partsprogram
Den här gruppen är en uppsättning attribut som används som minimal attribut som behövs för en allmän arbetsbelastning eller ett program. Det kan användas för en arbetsbelastning som inte finns med i ett annat avsnitt eller för en icke-Microsoft-app. Den används för följande:

* Yammer (endast användare används)
* [Hybrid Business-to-Business (B2B) mellan org samarbete erbjuds av resurser som SharePoint](http://go.microsoft.com/fwlink/?LinkId=747036)

Den här gruppen är en uppsättning attribut som kan användas om Azure AD-katalog inte används som stöd för Office 365, Dynamics eller Intune. Den har en liten uppsättning core attribut.

| Attributnamn | Användare | Kontakt | Grupp | Kommentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Anger om ett konto har aktiverats. |
| CN |X | |X | |
| Visningsnamn |X |X |X | |
| givenName |X |X | | |
| E-post |X | |X | |
| managedBy | | |X | |
| mailNickName |X |X |X | |
| Medlem | | |X | |
| objectSID |X | | |mekanisk egenskap. AD användar-ID som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mekanisk egenskap. Används för att veta när ogiltigförklara redan utfärdade token. Används av lösenordshashsynkronisering, direktautentisering och federation. |
| SN |X |X | | |
| sourceAnchor |X |X |X |mekanisk egenskap. Oåterkalleliga identifierare att underhålla relationen mellan ADDS och Azure AD. |
| usageLocation |X | | |mekanisk egenskap. Användarens land. Används för licenstilldelning. |
| userPrincipalName |X | | |UPN är inloggnings-ID för användaren. Oftast det samma som [e] värdet. |

## <a name="windows-10"></a>Windows 10
En Windows 10-domänanslutna computer(device) synkroniserar vissa attribut till Azure AD. Mer information om scenarier finns [ansluta domänanslutna enheter till Azure AD för Windows 10-upplevelser](../active-directory-azureadjoin-devices-group-policy.md). Dessa attribut synkronisera alltid och Windows 10 visas inte som en app som du kan avmarkera. En domänansluten dator i Windows 10 identifieras genom att låta den attributet userCertificate fylls i.

| Attributnamn | Enhet | Kommentar |
| --- |:---:| --- |
| accountEnabled |X | |
| deviceTrustType |X |Hårdkodad värde för domänanslutna datorer. |
| Visningsnamn |X | |
| MS-DS-CreatorSID |X |Kallas även registeredOwnerReference. |
| objectGUID |X |Kallas även deviceID. |
| objectSID |X |Kallas även onPremisesSecurityIdentifier. |
| Operativsystemet |X |Kallas även deviceOSType. |
| operatingSystemVersion |X |Kallas även deviceOSVersion. |
| userCertificate |X | |

Dessa attribut för **användaren** är utöver de andra apparna som du har valt.  

| Attributnamn | Användare | Kommentar |
| --- |:---:| --- |
| domainFQDN |X |Kallas även DNS-domännamn. Till exempel contoso.com. |
| domainNetBios |X |Kallas även NetBIOS-namn. Till exempel CONTOSO. |

## <a name="exchange-hybrid-writeback"></a>Tillbakaskrivning av Exchange-hybrid
Dessa attribut skrivs tillbaka från Azure AD till lokala Active Directory när du väljer för att aktivera **Exchange hybrid**. Beroende på din Exchange-version kan färre attribut synkroniseras.

| Attributnamn (ansluta UI) |Attributnamn (lokala AD) | Användare | Kontakt | Grupp | Kommentar |
| --- |:---:|:---:|:---:| --- |---|
| msDS-ExternalDirectoryObjectID| ms-DS-External-Directory-Object-Id |X | | |Härleds från cloudAnchor i Azure AD. Det här attributet är ny i Exchange 2016 och Windows Server 2016 AD. |
| msExchArchiveStatus| MS-Exch-ArchiveStatus |X | | |Online Arkiv: Gör det möjligt för kunder att arkivera e-post. |
| msExchBlockedSendersHash| MS-Exch-BlockedSendersHash |X | | |Filtrering: Skrivs tillbaka lokalt filtrering och online säkert och blockerade sändaren data från klienter. |
| msExchSafeRecipientsHash| MS-Exch-SafeRecipientsHash  |X | | |Filtrering: Skrivs tillbaka lokalt filtrering och online säkert och blockerade sändaren data från klienter. |
| msExchSafeSendersHash| MS-Exch-SafeSendersHash  |X | | |Filtrering: Skrivs tillbaka lokalt filtrering och online säkert och blockerade sändaren data från klienter. |
| msExchUCVoiceMailSettings| MS-Exch-UCVoiceMailSettings |X | | |Aktivera Unified Messaging (UM) – Online röstmeddelanden: används av Microsoft Lync Server integrering för att indikera att Lync Server lokalt att användaren har röstmeddelanden i online-tjänster. |
| msExchUserHoldPolicies| MS-undantagna-hUserHoldPolicies |X | | |Tvister Hold: Aktiverar molntjänster att avgöra vilka användare som är under tvister håller. |
| proxyAddresses| proxyAddresses |X |X |X |Endast x500 adressen från Exchange Online infogas. |
| publicDelegates| MS-Exch-offentliga-delegater  |X | | |Gör en Exchange Online-postlådan för att erhålla SendOnBehalfTo behörigheter för användare med lokala Exchange-postlåda. Kräver Azure AD Connect build 1.1.552.0 eller efter. |

## <a name="exchange-mail-public-folder"></a>Offentlig mapp för Exchange-e-post
Dessa attribut synkroniseras från lokala Active Directory till Azure AD när du väljer för att aktivera **offentlig mapp för Exchange-e-post**.

| Attributnamn | PublicFolder | Kommentar |
| --- | :---:| --- |
| Visningsnamn | X |  |
| E-post | X |  |
| msExchRecipientTypeDetails | X |  |
| objectGUID | X |  |
| proxyAddresses | X |  |
| targetAddress | X |  |

## <a name="device-writeback"></a>Tillbakaskrivning av enheter
Enhetsobjekt skapas i Active Directory. De här objekten kan vara enheter som är anslutna till Azure AD eller domänanslutna Windows 10-datorer.

| Attributnamn | Enhet | Kommentar |
| --- |:---:| --- |
| altSecurityIdentities |X | |
| Visningsnamn |X | |
| DN |X | |
| msDS-CloudAnchor |X | |
| msDS-DeviceID |X | |
| msDS-DeviceObjectVersion |X | |
| msDS-DeviceOSType |X | |
| msDS-DeviceOSVersion |X | |
| msDS-DevicePhysicalIDs |X | |
| msDS-KeyCredentialLink |X |Endast med Windows Server 2016 AD-schema |
| msDS-IsCompliant |X | |
| msDS-IsEnabled |X | |
| msDS-IsManaged |X | |
| msDS-RegisteredOwner |X | |

## <a name="notes"></a>Anteckningar
* När du använder ett alternativt ID är lokalt attributet userPrincipalName synkroniserad med Azure AD-attributet onPremisesUserPrincipalName. Alternativt ID-attributet för exempel e-post synkroniseras med Azure AD attributet userPrincipalName.
* I listorna över objekttypen **användare** gäller även för objekttypen **iNetOrgPerson**.

## <a name="next-steps"></a>Nästa steg
Lär dig mer om den [Azure AD Connect-synkronisering](active-directory-aadconnectsync-whatis.md) konfiguration.

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](active-directory-aadconnect.md).
