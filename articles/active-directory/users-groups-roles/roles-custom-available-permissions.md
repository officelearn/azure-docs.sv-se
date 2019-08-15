---
title: Anpassad administratörs roll behörigheter för app Registration Management-Azure Active Directory | Microsoft Docs
description: Anpassad administratörs roll behörigheter för att delegera identitets hantering.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 07/31/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99f31c5928273973a9089ae9ef1fd184cdb78bbb
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033310"
---
# <a name="application-registration-subtypes-and-permissions-in-azure-active-directory"></a>Under typer och behörigheter för program registrering i Azure Active Directory

Den här artikeln innehåller tillgängliga registrerings behörigheter för program för anpassade roll definitioner i Azure Active Directory (Azure AD).

## <a name="single-tenant-v-multi-tenant-permissions"></a>Single-Tenant v. behörigheter för flera innehavare

Anpassade roll behörigheter skiljer sig åt för program med en enda klient och flera innehavare. Program med en enda klient är bara tillgängliga för användare i Azure AD-organisationen där programmet är registrerat. Program med flera klienter är tillgängliga för alla Azure AD-organisationer. Program med en enda klient organisation definieras som att de **konto typer som stöds** har angetts till "konton endast i den här organisations katalogen". I Graph API har signInAudience-egenskapen angetts till "AzureADMyOrg" i en enda klient.

## <a name="application-registration-subtypes-and-permissions"></a>Under typer och behörigheter för program registrering

Se [översikten över anpassade roller](roles-custom-overview.md) för att få en förklaring av det allmänna villkorets undertyp, behörighet och egenskaps uppsättnings medelvärde. Följande information är specifik för program registreringar.

### <a name="subtypes"></a>Undertyper

Det finns bara en under typ av program registrering – program. organisationen. Till exempel Microsoft. Directory/Applications. min organisation/Basic/Update. Den här under typen anges på sidan **autentisering** för en speciell app-registrering och motsvarar att ange egenskapen signInAudience till "AzureADMyOrg" med hjälp av Graph API eller PowerShell. Under typen begränsar behörigheten till app-registreringar som är markerade som tillgängliga endast av konton i din organisation (program med en enda klient).

Du kan använda begränsad behörighet för att bevilja Läs-eller hantera behörigheter till interna program utan att bevilja Läs-eller hanterings behörigheter till program som är tillgängliga för konton i andra organisationer.

Det finns program. mina organisations versioner av alla Läs-och uppdaterings behörigheter samt behörigheten Ta bort. Det finns inga program. min organisations version av Create just nu. Standard behörigheter (till exempel Microsoft. Directory/Applications/Basic/Update) bevilja Läs-eller hanterings behörigheter för alla registrerings typer för appar.

![Deklarera ett program med en enda klient organisation eller ett program för flera klienter](./media/roles-custom-available-permissions/supported-account-types.png)

Information om följande behörigheter för för hands versionen av anpassade roller visas i listan [tillgängliga anpassade roll behörigheter i Azure Active Directory](roles-custom-available-permissions.md).

### <a name="create-and-delete"></a>Skapa och ta bort

Det finns två tillgängliga behörigheter för att ge möjlighet att skapa program registreringar, var och en med olika beteenden:

- **Microsoft. Directory/Applications/createAsOwner**: Genom att tilldela det här behörighets resultatet i skaparen som lagts till som första ägare till den skapade app-registreringen, så räknas den skapade program registreringen mot skapare objekt kvoten 250 skapade objekt.
- **Microsoft. Directory/applicationPolicies/skapa**: Om du tilldelar det här behörighets resultatet i skaparen, läggs det inte till som första ägare till den skapade app-registreringen, och den skapade app-registreringen räknas inte mot skapare objekt kvoten 250 skapade objekt. Använd den här behörigheten noggrant, eftersom det inte finns något hinder för att skapa registrerings program förrän kvoten på katalog nivå har nåtts. Om båda behörigheterna tilldelas, prioriteras den här behörigheten.

Om båda behörigheterna tilldelas har/Create-behörigheten företräde. Även om/createAsOwner-behörigheten inte automatiskt lägger till skaparen som den första ägaren, kan ägarna anges under skapandet av appens registrering när de använder Graph API: er eller PowerShell-cmdletar.

Skapa behörigheter bevilja åtkomst till det **nya registrerings** kommandot.

[De här behörigheterna ger åtkomst till den nya registrerings Portal kommandot](./media/roles-create-custom/new-custom-role.png)

Det finns två tillgängliga behörigheter för att ge möjlighet att ta bort registreringar för appar:

#### <a name="microsoftdirectoryapplicationsdelete"></a>Microsoft. Directory/Applications/Delete

Ger möjlighet att ta bort registrerings program oavsett undertyp, det vill säga både en-klient och flera klient program.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>Microsoft. Directory/Applications. min organisation/ta bort

Ger möjlighet att ta bort program registreringar som är begränsade till dem som endast är tillgängliga för konton i din organisation eller program med en enskild klient (organisations under typ).

![De här behörigheterna ger åtkomst till kommandot Ta bort app-registrering](./media/roles-custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> När du tilldelar en roll som innehåller behörigheten Skapa, måste roll tilldelningen göras i katalog omfånget. En Create-behörighet som tilldelas i ett resurs omfång ger inte möjlighet att skapa registrerings program.

### <a name="read"></a>Läsa

Alla medlems användare i organisationen kan läsa registrerings information för appar som standard. Gäst användare och program tjänstens huvud namn kan dock inte. Om du planerar att tilldela en roll till en gäst användare eller ett program måste du inkludera lämpliga Läs behörigheter.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>Microsoft. Directory/Applications/allProperties/Read

Möjlighet att läsa alla egenskaper för program med en enda klient och flera innehavare utanför känsliga egenskaper som autentiseringsuppgifter.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>Microsoft. Directory/Applications. min organisation/allProperties/läsa

Ger samma behörigheter som Microsoft. Directory/Applications/allProperties/Read, men endast för program med en enda klient.

#### <a name="microsoftdirectoryapplicationsstandardread-grants-access-to-all-fields-on-the-application-registration-branding-page"></a>Microsoft. Directory/Applications/standard/Read: Ger åtkomst till alla fält på anpassnings sidan för program registrering

![Den här behörigheten ger åtkomst till anpassnings sidan för program registrering](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>Microsoft. Directory/Applications. min organisation/standard/läsa

Ger samma behörigheter som Microsoft. Directory/Applications/standard/Read, men endast för program med en enda klient.

#### <a name="microsoftdirectoryapplicationsownersread"></a>Microsoft. Directory/program/ägare/läsa

Ger möjlighet att läsa egenskapen ägare för en enskild klient och flera klient program. Ger åtkomst till alla fält på sidan program registrerings ägare:

![Den här behörigheten beviljar åtkomst till sidan för program registrerings ägare](./media/roles-custom-available-permissions/app-registration-owners.png)

Ger åtkomst till följande egenskaper för entiteten program:

- AllowActAsForAllClients
- AllowPassthroughUsers
- AppAddress
- AppBrandingElements
- AppCategory
- AppCreatedDateTime
- AppData
- appId
- AppInformationalUrl
- ApplicationTag
- AppLogoUrl
- AppMetadata
- AppOptions
- BinaryExtensionAttribute
- BooleanExtensionAttribute
- CountriesBlockedForMinors
- CreatedOnBehalfOf
- DateTimeExtensionAttribute
- DisplayName
- ExtensionAttributeDefinition
- IntegerExtensionAttribute
- KnownClientApplications
- LargeIntegerExtensionAttribute
- LegalAgeGroupRule
- LocalizedAppBrandingElements
- MainLogo
- MsaAppId
- ResourceApplicationSet
- ServiceDiscoveryEndpoint
- StringExtensionAttribute
- TrustedCertificateSubject
- WebApi
- WebApp
- WwwHomepage

### <a name="update"></a>Uppdatera

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>Microsoft. Directory/Applications/allProperties/Update

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>Microsoft. Directory/Applications. min organisation/allProperties/Update

Ger samma behörigheter som Microsoft. Directory/Applications/allProperties/Update, men endast för program med en klient.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>Microsoft. Directory/program/mål grupp/uppdatering

Ger åtkomst till alla fält på sidan för autentisering av program registrering:

![Den här behörigheten beviljar åtkomst till sidan för registrering av appar](./media/roles-custom-available-permissions/supported-account-types.png)

Ger åtkomst till följande egenskaper för program resursen:

- AvailableToOtherTenants
- SignInAudience

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>Microsoft. Directory/Applications. min organisation/mål grupp/uppdatering

Ger samma behörigheter som Microsoft. Directory/program/mål grupp/uppdatering, men endast för program med en klient.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>Microsoft. Directory/program/autentisering/uppdatering

Möjlighet att uppdatera svars-URL: en, inloggnings-URL, implicit flöde och publicerings domän egenskaper för en enskild klient och flera klient program. Ger åtkomst till alla fält på sidan för autentisering av program registrering, förutom de konto typer som stöds:

![Beviljar åtkomst till app Registration-autentisering men inte konto typer som stöds](./media/roles-custom-available-permissions/supported-account-types.png)

 Ger åtkomst till följande egenskaper för program resursen:

- AcceptMappedClaims
- AccessTokenAcceptedVersion
- Tillägg
- GroupMembershipClaims
- IsDeviceOnlyAuthSupported
- OAuth2LegacyUrlPathMatching
- OauthOidcResponsePolicyBitmap
- OptionalClaims
- OrgRestrictions
- PublicClient
- UseCustomTokenSigningKey

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>Microsoft. Directory/Applications. min organisation/autentisering/uppdatering

Ger samma behörigheter som Microsoft. Directory/program/autentisering/uppdatering, men endast för program med en klient.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>Microsoft. Directory/Applications/Basic/Update

Möjlighet att uppdatera namn, logo typ, URL-adress för webb sidor, användnings villkor för URL-adresser och URL-egenskaper för sekretess policy för en klient och flera klient program. Ger åtkomst till alla fält på anpassnings sidan för program registrering:

![Den här behörigheten ger åtkomst till anpassnings sidan för program registrering](./media/roles-custom-available-permissions/app-registration-branding.png)

Ger åtkomst till följande egenskaper för program resursen:

- AllowActAsForAllClients
- AllowPassthroughUsers
- AppAddress
- AppBrandingElements
- AppCategory
- AppData
- appId
- AppInformationalUrl
- ApplicationTag
- AppLogoUrl
- AppMetadata
- AppOptions
- BinaryExtensionAttribute
- BooleanExtensionAttribute
- CountriesBlockedForMinors
- CreatedOnBehalfOf
- DateTimeExtensionAttribute
- DisplayName
- ExtensionAttributeDefinition
- IntegerExtensionAttribute
- KnownClientApplications
- LargeIntegerExtensionAttribute
- LegalAgeGroupRule
- LocalizedAppBrandingElements
- MainLogo
- MsaAppId
- ResourceApplicationSet
- ServiceDiscoveryEndpoint
- StringExtensionAttribute
- TrustedCertificateSubject
- WebApi
- WebApp
- WwwHomepage

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>Microsoft. Directory/Applications. min organisation/Basic/Update

Ger samma behörigheter som Microsoft. Directory/Applications/Basic/Update, men endast för program med en klient.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directory/applications/credentials/update

Möjlighet att uppdatera egenskaperna för certifikat och klient hemligheter på program med en enda klient och flera innehavare. Ger åtkomst till alla fält på sidan program registrerings certifikat & hemligheter:

![Den här behörigheten ger åtkomst till sidan för app-registrering av certifikat & hemligheter](./media/roles-custom-available-permissions/app-registration-secrets.png)

Ger åtkomst till följande egenskaper för program resursen:
- AsymmetricKey
- EncryptedSecretKey
- Beskrivning av Beskrivning
- SharedKeyReference
- TokenEncryptionKeyId

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>Microsoft. Directory/Applications. min organisation/autentiseringsuppgifter/uppdatera

Ger samma behörigheter som Microsoft. Directory/Applications/credentials/Update, men endast för program med en katalog.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>Microsoft. Directory/program/ägare/uppdatering

Möjlighet att uppdatera egenskapen owner på en enskild klient och flera innehavare. Ger åtkomst till alla fält på sidan program registrerings ägare:

![Den här behörigheten beviljar åtkomst till sidan för program registrerings ägare](./media/roles-custom-available-permissions/app-registration-owners.png)

Ger åtkomst till följande egenskaper för program resursen:
- Ägare

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>Microsoft. Directory/Applications. min organisation/ägare/uppdatera

Ger samma behörigheter som Microsoft. Directory/program/Owners/Update, men endast för program med en enda klient.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>Microsoft. Directory/program/behörigheter/uppdatera

Möjlighet att uppdatera delegerade behörigheter, program behörigheter, auktoriserade klient program, nödvändiga behörigheter och bevilja medgivande egenskaper för en enskild klient och flera klient program. Ger inte möjlighet att genomföra medgivande. Ger åtkomst till alla fält i API-behörigheter för program registrering och visar en API-sida:

![Den här behörigheten ger åtkomst till sidan registrerings-API-behörigheter för appen](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![Den här behörigheten beviljar åtkomst till appens registrering visar en API-sida](./media/roles-custom-available-permissions/app-registration-expose-api.png)

Ger åtkomst till följande egenskaper för program resursen:

- AppIdentifierUri
- Berättigande
- PreAuthorizedApplications
- RecordConsentConditions
- RequiredResourceAccess

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>Microsoft. Directory/Applications. min organisation/behörigheter/uppdatera

Ger samma behörigheter som Microsoft. Directory/program/behörigheter/uppdatera, men endast för program med en klient.

## <a name="required-license-plan"></a>Obligatorisk licens plan

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Nästa steg

- Skapa anpassade roller med hjälp [av Azure Portal, Azure AD PowerShell och Graph API](roles-create-custom.md)
- [Visa tilldelningarna för en anpassad roll](roles-view-assignments.md)
