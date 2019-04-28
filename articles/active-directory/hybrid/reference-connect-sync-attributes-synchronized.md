---
title: Attribut som synkroniseras av Azure AD Connect | Microsoft Docs
description: Visar en lista över attribut som synkroniseras till Azure Active Directory.
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
ms.date: 01/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb7010ec16592fea2f530329916e00056ca03a70
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62096138"
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Azure AD Connect-synkronisering: Attribut som synkroniseras till Azure Active Directory
Det här avsnittet listas de attribut som synkroniseras av Azure AD Connect-synkronisering.  
Attribut är grupperade efter relaterade Azure AD-app.

## <a name="attributes-to-synchronize"></a>Attribut som ska synkroniseras
En vanlig fråga är *vad är en lista över minsta attribut som ska synkroniseras*. Standardwebbplatsen och den rekommenderade metoden är att hålla standardattributen så att en fullständig GAL (globala adresslistan) kan konstrueras i molnet och för att få alla funktioner i Office 365-arbetsbelastningar. I vissa fall kan det finns vissa attribut som organisationen inte vill synkroniserade till molnet eftersom dessa attribut innehåller känslig eller personligt identifierbar information (personligt identifierbar information) data, som i det här exemplet:  
![felaktigt attribut](./media/reference-connect-sync-attributes-synchronized/badextensionattribute.png)

I det här fallet utgå från listan med attribut i det här avsnittet och identifiera de attribut som innehåller känslig eller identifierbar information data och kan inte synkroniseras. Sedan avmarkera dessa attribut under installationen med hjälp av [Azure AD-app och attributfiltrering](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering).

> [!WARNING]
> När avmarkera attribut, bör du vara försiktig och endast avmarkera dessa attribut som är absolut inte möjligt att synkronisera. Avmarkera andra attribut kan ha en negativ inverkan på funktioner.
>
>

## <a name="office-365-proplus"></a>Office 365 ProPlus
| Attributnamn | Användare | Kommentar |
| --- |:---:| --- |
| accountEnabled |X |Anger om ett konto har aktiverats. |
| CN |X | |
| displayName |X | |
| objectSID |X |mekanisk egenskap. AD användar-ID som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| pwdLastSet |X |mekanisk egenskap. Används för att veta när du upphäver redan utfärdade token. Används av hash-synkronisering av lösenord, direktautentisering och federation. |
|samAccountName|X| |
| sourceAnchor |X |mekanisk egenskap. Oföränderligt ID att upprätthålla relationen mellan ADDS och Azure AD. |
| usageLocation |X |mekanisk egenskap. Användarens land. Används för licensuppgifter. |
| userPrincipalName |X |UPN är inloggnings-ID för användaren. Oftast det samma som [e] värdet. |

## <a name="exchange-online"></a>exchange online
| Attributnamn | Användare | Kontakt | Grupp | Kommentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Anger om ett konto har aktiverats. |
| assistent |X |X | | |
| altRecipient |X | | |Kräver Azure AD Connect version 1.1.552.0 eller efter. |
| authOrig |X |X |X | |
| c |X |X | | |
| CN |X | |X | |
| CO |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
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
| givenName |X |X | | |
| Hemtelefon |X |X | | |
| info |X |X |X |Det här attributet används för närvarande inte för grupper. |
| Initialer |X |X | | |
| L |X |X | | |
| legacyExchangeDN |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| ansvarig |X |X | | |
| medlem | | |X | |
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
| msExchBypassModerationLink | | |X |Tillgängliga i Azure AD Connect version 1.1.524.0 |
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
| msOrg-IsOrganizational | | |X | |
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
| pwdLastSet |X | | |mekanisk egenskap. Används för att veta när du upphäver redan utfärdade token. Används av både Lösenordssynkronisering och federation. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| securityEnabled | | |X |Härleds från groupType |
| SN |X |X | | |
| sourceAnchor |X |X |X |mekanisk egenskap. Oföränderligt ID att upprätthålla relationen mellan ADDS och Azure AD. |
| St |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| title |X |X | | |
| unauthOrig |X |X |X | |
| usageLocation |X | | |mekanisk egenskap. Användarens land. Används för licensuppgifter. |
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
| company |X |X | | |
| countryCode |X |X | | |
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
| givenName |X |X | | |
| hideDLMembership | | |X | |
| Hemtelefon |X |X | | |
| info |X |X |X | |
| Initialer |X |X | | |
| ipPhone |X |X | | |
| L |X |X | | |
| e-post |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| ansvarig |X |X | | |
| medlem | | |X | |
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
| postOfficeBox |X |X | |Det här attributet används för närvarande inte av SharePoint Online. |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mekanisk egenskap. Används för att veta när du upphäver redan utfärdade token. Används av hash-synkronisering av lösenord, direktautentisering och federation. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| securityEnabled | | |X |Härleds från groupType |
| SN |X |X | | |
| sourceAnchor |X |X |X |mekanisk egenskap. Oföränderligt ID att upprätthålla relationen mellan ADDS och Azure AD. |
| St |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| title |X |X | | |
| unauthOrig |X |X |X | |
| url |X |X | | |
| usageLocation |X | | |mekanisk egenskap. Användarens land. Används för licensuppgifter. |
| userPrincipalName |X | | |UPN är inloggnings-ID för användaren. Oftast det samma som [e] värdet. |
| wWWHomePage |X |X | | |

## <a name="lync-online-subsequently-known-as-skype-for-business"></a>Lync Online (kallas därefter för Skype för företag)
| Attributnamn | Användare | Kontakt | Grupp | Kommentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Anger om ett konto har aktiverats. |
| c |X |X | | |
| CN |X | |X | |
| CO |X |X | | |
| company |X |X | | |
| avdelning |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X |X | |
| givenName |X |X | | |
| Hemtelefon |X |X | | |
| ipPhone |X |X | | |
| L |X |X | | |
| e-post |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| ansvarig |X |X | | |
| medlem | | |X | |
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
| pwdLastSet |X | | |mekanisk egenskap. Används för att veta när du upphäver redan utfärdade token. Används av hash-synkronisering av lösenord, direktautentisering och federation. |
| securityEnabled | | |X |Härleds från groupType |
| SN |X |X | | |
| sourceAnchor |X |X |X |mekanisk egenskap. Oföränderligt ID att upprätthålla relationen mellan ADDS och Azure AD. |
| St |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| title |X |X | | |
| usageLocation |X | | |mekanisk egenskap. Användarens land. Används för licensuppgifter. |
| userPrincipalName |X | | |UPN är inloggnings-ID för användaren. Oftast det samma som [e] värdet. |
| wWWHomePage |X |X | | |

## <a name="azure-rms"></a>Azure RMS
| Attributnamn | Användare | Kontakt | Grupp | Kommentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Anger om ett konto har aktiverats. |
| CN |X | |X |Namn eller alias. Oftast prefixet för värdet [e]. |
| displayName |X |X |X |En sträng som representerar det namn som ofta visas som eget namn (Förnamn Efternamn). |
| e-post |X |X |X |fullständiga e-postadress. |
| medlem | | |X | |
| objectSID |X | |X |mekanisk egenskap. AD användar-ID som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| proxyAddresses |X |X |X |mekanisk egenskap. Används av Azure AD. Innehåller alla sekundära e-postadresser för användaren. |
| pwdLastSet |X | | |mekanisk egenskap. Används för att veta när du upphäver redan utfärdade token. |
| securityEnabled | | |X |Hämtad från groupType. |
| sourceAnchor |X |X |X |mekanisk egenskap. Oföränderligt ID att upprätthålla relationen mellan ADDS och Azure AD. |
| usageLocation |X | | |mekanisk egenskap. Användarens land. Används för licensuppgifter. |
| userPrincipalName |X | | |Den här UPN är inloggnings-ID för användaren. Oftast det samma som [e] värdet. |

## <a name="intune"></a>Intune
| Attributnamn | Användare | Kontakt | Grupp | Kommentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Anger om ett konto har aktiverats. |
| c |X |X | | |
| CN |X | |X | |
| description |X |X |X | |
| displayName |X |X |X | |
| e-post |X |X |X | |
| mailNickname |X |X |X | |
| medlem | | |X | |
| objectSID |X | |X |mekanisk egenskap. AD användar-ID som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mekanisk egenskap. Används för att veta när du upphäver redan utfärdade token. Används av hash-synkronisering av lösenord, direktautentisering och federation. |
| securityEnabled | | |X |Härleds från groupType |
| sourceAnchor |X |X |X |mekanisk egenskap. Oföränderligt ID att upprätthålla relationen mellan ADDS och Azure AD. |
| usageLocation |X | | |mekanisk egenskap. Användarens land. Används för licensuppgifter. |
| userPrincipalName |X | | |UPN är inloggnings-ID för användaren. Oftast det samma som [e] värdet. |

## <a name="dynamics-crm"></a>Dynamics CRM
| Attributnamn | Användare | Kontakt | Grupp | Kommentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Anger om ett konto har aktiverats. |
| c |X |X | | |
| CN |X | |X | |
| CO |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| L |X |X | | |
| managedBy | | |X | |
| ansvarig |X |X | | |
| medlem | | |X | |
| mobila |X |X | | |
| objectSID |X | |X |mekanisk egenskap. AD användar-ID som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| physicalDeliveryOfficeName |X |X | | |
| Postnummer |X |X | | |
| preferredLanguage |X | | | |
| pwdLastSet |X | | |mekanisk egenskap. Används för att veta när du upphäver redan utfärdade token. Används av hash-synkronisering av lösenord, direktautentisering och federation. |
| securityEnabled | | |X |Härleds från groupType |
| SN |X |X | | |
| sourceAnchor |X |X |X |mekanisk egenskap. Oföränderligt ID att upprätthålla relationen mellan ADDS och Azure AD. |
| St |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| title |X |X | | |
| usageLocation |X | | |mekanisk egenskap. Användarens land. Används för licensuppgifter. |
| userPrincipalName |X | | |UPN är inloggnings-ID för användaren. Oftast det samma som [e] värdet. |

## <a name="3rd-party-applications"></a>3 partsprogram
Den här gruppen är en uppsättning attribut som används som minimal attribut som behövs för en generisk arbetsbelastning eller ett program. Det kan användas för en arbetsbelastning som inte visas i ett annat avsnitt eller för en icke-Microsoft-app. Den används för följande:

* Yammer (endast användare används)
* [Hybrid Business-to-Business (B2B) mellan org samarbetsscenarier erbjuds av resurser som SharePoint](https://go.microsoft.com/fwlink/?LinkId=747036)

Den här gruppen är en uppsättning attribut som kan användas om Azure AD-katalog inte används för att stödja Office 365, Dynamics eller Intune. Den har en liten uppsättning huvudattributen.

| Attributnamn | Användare | Kontakt | Grupp | Kommentar |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |Anger om ett konto har aktiverats. |
| CN |X | |X | |
| displayName |X |X |X | |
| givenName |X |X | | |
| e-post |X | |X | |
| managedBy | | |X | |
| mailNickName |X |X |X | |
| medlem | | |X | |
| objectSID |X | | |mekanisk egenskap. AD användar-ID som används för att upprätthålla synkronisering mellan Azure AD och AD. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |mekanisk egenskap. Används för att veta när du upphäver redan utfärdade token. Används av hash-synkronisering av lösenord, direktautentisering och federation. |
| SN |X |X | | |
| sourceAnchor |X |X |X |mekanisk egenskap. Oföränderligt ID att upprätthålla relationen mellan ADDS och Azure AD. |
| usageLocation |X | | |mekanisk egenskap. Användarens land. Används för licensuppgifter. |
| userPrincipalName |X | | |UPN är inloggnings-ID för användaren. Oftast det samma som [e] värdet. |

## <a name="windows-10"></a>Windows 10
En Windows 10-domänanslutna computer(device) synkroniserar vissa attribut till Azure AD. Mer information om i vilka scenarier finns i [ansluta domänanslutna enheter till Azure AD för Windows 10 inträffar](../active-directory-azureadjoin-devices-group-policy.md). Dessa attribut synkroniseras alltid och Windows 10 visas inte som en app som du kan avmarkera. En domänansluten dator i Windows 10 identifieras genom att använda det attributet userCertificate fylls i.

| Attributnamn | Enhet | Kommentar |
| --- |:---:| --- |
| accountEnabled |X | |
| deviceTrustType |X |Hårdkodad värde för domänanslutna datorer. |
| displayName |X | |
| ms-DS-CreatorSID |X |Kallas även registeredOwnerReference. |
| objectGUID |X |Kallas även deviceID. |
| objectSID |X |Kallas även onPremisesSecurityIdentifier. |
| operatingSystem |X |Kallas även deviceOSType. |
| operatingSystemVersion |X |Kallas även deviceOSVersion. |
| userCertificate |X | |

Dessa attribut för **användaren** är utöver de andra apparna som du har valt.  

| Attributnamn | Användare | Kommentar |
| --- |:---:| --- |
| domainFQDN |X |Kallas även DNS-domännamn. Till exempel contoso.com. |
| domainNetBios |X |Kallas även NetBIOS-namn. Till exempel CONTOSO. |
| msDS-KeyCredentialLink |X |När användaren har registrerats i Windows Hello för företag. | 

## <a name="exchange-hybrid-writeback"></a>Tillbakaskrivning av Exchange-hybrid
Dessa attribut skrivs tillbaka från Azure AD till en lokal Active Directory när du väljer för att aktivera **Exchange-hybrid**. Beroende på din Exchange-version kan färre attribut synkroniseras.

| Attributnamn (Connect Användargränssnittet) |Attributnamn (lokala AD) | Användare | Kontakt | Grupp | Kommentar |
| --- |:---:|:---:|:---:| --- |---|
| msDS-ExternalDirectoryObjectID| ms-DS-External-Directory-Object-Id |X | | |Hämtad från Molnakarvärde i Azure AD. Det här attributet är ny i Exchange 2016 och Windows Server 2016 AD. |
| msExchArchiveStatus| ms-Exch-ArchiveStatus |X | | |Online Arkiv: Ger kunder möjlighet att arkivera e-post. |
| msExchBlockedSendersHash| ms-Exch-BlockedSendersHash |X | | |Filtrering: Skriver tillbaka lokalt filtrering och online säker och blockerade avsändaren data från klienter. |
| msExchSafeRecipientsHash| ms-Exch-SafeRecipientsHash  |X | | |Filtrering: Skriver tillbaka lokalt filtrering och online säker och blockerade avsändaren data från klienter. |
| msExchSafeSendersHash| ms-Exch-SafeSendersHash  |X | | |Filtrering: Skriver tillbaka lokalt filtrering och online säker och blockerade avsändaren data från klienter. |
| msExchUCVoiceMailSettings| ms-Exch-UCVoiceMailSettings |X | | |Aktivera Unified Messaging (UM) – Online röstbrevlåda: Används av Microsoft Lync Server-integrering för att indikera att Lync Server lokala att användaren har röstmeddelanden i online services. |
| msExchUserHoldPolicies| ms-Exc-hUserHoldPolicies |X | | |Tvister skäl: Aktiverar molntjänster för att avgöra vilka användare som är under tvister håll. |
| proxyAddresses| proxyAddresses |X |X |X |Endast x500-adress från Exchange Online har infogats. |
| publicDelegates| ms-Exch-Public-Delegates  |X | | |Gör en Exchange Online-postlåda som ska beviljas SendOnBehalfTo behörighet till användare med lokala Exchange-postlåda. Kräver Azure AD Connect version 1.1.552.0 eller efter. |

## <a name="exchange-mail-public-folder"></a>Offentlig mapp för Exchange-e-post
Dessa attribut synkroniseras från den lokala Active Directory till Azure AD när du väljer för att aktivera **offentlig mapp för Exchange-e-post**.

| Attributnamn | PublicFolder | Kommentar |
| --- | :---:| --- |
| displayName | X |  |
| e-post | X |  |
| msExchRecipientTypeDetails | X |  |
| objectGUID | X |  |
| proxyAddresses | X |  |
| targetAddress | X |  |

## <a name="device-writeback"></a>Tillbakaskrivning av enheter
Enhetsobjekt skapas i Active Directory. De här objekten kan vara enheter som är anslutna till Azure AD eller domänanslutna Windows 10-datorer.

| Attributnamn | Enhet | Kommentar |
| --- |:---:| --- |
| altSecurityIdentities |X | |
| displayName |X | |
| dn |X | |
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
* När du använder ett alternativt ID, är lokala attributet userPrincipalName synkroniserad med Azure AD-attributet onPremisesUserPrincipalName. Alternativt ID-attributet för exempel e-post, synkroniseras med Azure AD-attributet userPrincipalName.
* I listorna över objekttypen **användaren** gäller även för objekttypen **iNetOrgPerson**.

## <a name="next-steps"></a>Nästa steg
Läs mer om den [Azure AD Connect-synkronisering](how-to-connect-sync-whatis.md) konfiguration.

Läs mer om hur du [integrerar dina lokala identiteter med Azure Active Directory](whatis-hybrid-identity.md).
